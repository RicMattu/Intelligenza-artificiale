# Guida dettagliata all'uso di MCP toolbox con AI Agent 

Riferimento originale: [link](https://googleapis.github.io/genai-toolbox/getting-started/introduction/).
Per approfondire l'MCP come protocollo [clicca qui] (), in generale per procedere ci basta sapere che l'MCP è un modo standard che glli agenti AI usano per connettersi a tool esterni o a dati.

## tool calling tradizionale vs MCP





### "requirements"

- Python 3.10+ (including pip and your preferred virtual environment tool for managing dependencies e.g. venv).
- PostgreSQL 16+ and the psql client.

## Step 1: set up the db

Avvio il terminale interattivo al localhost con utente (`-U postgres`) cioè con l'utente amministratore predefinito

```bash
psql -h 127.0.0.1 -U postgres
```

1. creo un un nuovo utente (toolbox_user) con la sua password
2. creo il database (toolbox_db)
3. assegno all'utente i privilegi sul db
4. assegno all'utente la proprietà del db

```SQL
CREATE USER toolbox_user WITH PASSWORD 'my-password';
CREATE DATABASE toolbox_db;
GRANT ALL PRIVILEGES ON DATABASE toolbox_db TO toolbox_user;
ALTER DATABASE toolbox_db OWNER TO toolbox_user;
```

Accedo con il nuovo utente

```bash
psql -h 127.0.0.1 -U magazzino_user -d magazzino_db
```

e creo la tabella. Le chiavi sono posizione, stato, etc...  
`nome_chiave  [TIPO] [OPTIONS]`
il tipo può essere VARCHAR (testo), INTEGER, BOOLEAN.  
Posizione è la chiave primaria (quella che assegna l'ID all'oggetto) e infatti non può essere vuota.
pericoloso è una colonna generata, se il codice CER contiene un asterisco è impostat come TRUE

```SQL
CREATE TABLE ubicazioni (
    posizione   VARCHAR NOT NULL PRIMARY KEY,
    stato       INTEGER NOT NULL DEFAULT 0,
    codice      VARCHAR,
    cer         VARCHAR,
    peso        INTEGER,
    pericoloso  BOOLEAN GENERATED ALWAYS AS (cer LIKE '%*%') STORED,
    zona        VARCHAR
);
```

Creo lo script python che importa i json nel db.

## Step 2: configure MCP toolbox

Scarico il toolbox e lo rendo eseguibile (chmod +x)

```bash
export OS="linux/amd64" # one of linux/amd64, darwin/arm64, darwin/amd64, or windows/amd64
curl -O https://storage.googleapis.com/genai-toolbox/v0.28.0/$OS/toolbox
chmod +x toolbox
```

Creo il file tool.yaml, cioè il file di configurazione di MCP toolbox che definisce le azioni
possibili dell'agent sul db.  
Ha tre tipi di blocchi, separati da `---`:

```yaml
kind: sources
---
kind: tools
---
kind: toolsets
```

1. sources: gestisce la connessione al db:

```yaml
kind: sources
name: magazzino-source
type: postgres
host: 127.0.0.1
port: 5432
database: magazzino_db
user: magazzino_user
password: magazzino-password
```

2. tools: espone le query SQL parametrizzandole, il parametro descriptions è fondamentale, perchè è quello che usa il modello LLM per decidere quale tool usare.

```yaml
kind: tools
name: cerca-per-zona
type: postgres-sql
source: magazzino-source
description: Cerca tutte le ubicazioni di una zona del magazzino.
parameters:
  - name: zona
    type: string
    description: La zona del magazzino (es. S1, S2, A1).
statement: SELECT posizione, stato, codice, cer, peso, pericoloso FROM ubicazioni WHERE zona = $1 ORDER BY posizione;
---
kind: tools
name: statistiche-magazzino
type: postgres-sql
source: magazzino-source
description: Mostra statistiche generali del magazzino per zona (totale ubicazioni, piene, vuote, pericolose).
parameters: []
statement: >-
  SELECT zona,
    COUNT(*) as totale,
    SUM(CASE WHEN stato=4 THEN 1 ELSE 0 END) as piene,
    SUM(CASE WHEN stato=0 THEN 1 ELSE 0 END) as vuote,
    SUM(CASE WHEN pericoloso THEN 1 ELSE 0 END) as pericolose
  FROM ubicazioni
  GROUP BY zona ORDER BY zona;
---
```

3. toolsets permette di raggruppare tools per contesto.  
   Costituisce un sottoinsieme nell'insieme dei tools, quando l'LLM viene collegato a quel toolset, vede solo quelli che sono compresi ma non gli altri.

```yaml
kind: toolsets
name: magazzino-toolset
tools:
  - cerca-per-zona
  - cerca-per-posizione
  - cerca-per-codice
  - cerca-per-cer
  - statistiche-magazzino
```

Dopo averlo configurato, si fa girare il toolbox server

```bash
./toolbox --tools-file "tools.yaml"
```

## Step3: connect AI agent to MCP toolbox

Scarico google adk (agent development kit). È una python lib, con le classi:

- Agent (che rappresenta l'agente, a cui viene passato il modello LLM-default: gemini-2.5-flash-, il prompt e i tools);
- App (che contiene il ciclo di vita dell'agent)
- Runner (il motore che eseque il loop:
  - input utente -> chiamata LLM -> scelta tool LLM -> esecuzione tool -> risultato )

```bash
pip install google-adk[toolbox]
```

Uso adk per creare l'agente:

```bash
adk create my_agent
```

in pratica crea una dir. my_agent/ con un file agent.py inizialmente vuoto. Dopo aver scritto suddetto file, bisogna creare un `.env` che contenga la Google API key.

```bash
echo 'GOOGLE_API_KEY="YOUR_API_KEY"' > my_agent/.env
```

dopodichè si può runnare localmente l'agent

```bash
adk run my_agent
```

oppure attraverso una web UI

```bash
adk web --port 8000
```

# Altro

## Analisi file agent.py

L'agente viene importato così:

```python
from google.adk import Agent
```

In realtà la scrittura esplicita è:

```python
from google.adk.agents import LlmAgent
```

Con la prima scrittura, quello che succede è che Python fa questo percorso fisico sul filesystem:

```bash
 venv/site-packages/google/adk/
                            ├── __init__.py        ← Python lo esegue quando importi google.adk
                            └── agents/
                            └── llm_agent.py   ← qui c'è class LlmAgent
```

cioè entra in `google.adk` e in automatico viene eseguito `__init__.py`, qua dentro ci sono le due righe

```python
from .llm_agent import LlmAgent           # importa con il nome originale
from .llm_agent import LlmAgent           # equivalente a quello che fanno
```

La seconda funziona perchè il file llm_agent.py si conclude con

```python
Agent: TypeAlias = LlmAgent
```

## Google API Key

## How to Run Agents

Esistono tre possibilità

1. Web UI:

```bash
adk web --port 8000
```

Nonostante sia un'interfaccia web, è indicata esclusivamente per la fase di deplyment, non in fase di produzione.

2. Command Line

```bash
adk run my_agent
```

C'è l'opzione per salvare la sessione che si sta per aprire:

```bash
adk run --save_session path/to/my_agent
```

In tal caso viene chiesto di inserire un session ID, la sessione viene salvata in:
`path/to/my_agent/<session_id>.session.json.`  
eventualmente si può specificare l'ID prima:

```bash
adk run --save_session --session_id my_session path/to/my_agent
```

Le sessioni salvate possono essere riprese:

```bash
adk run --resume path/to/my_agent/my_session.session.json path/to/my_agent
```

o semplicemente ricaricate con:

```bash
adk run --replay path/to/input.json path/to/my_agent
```

3. API Server

### 1. Web UI

# Domande

- se la web ui non va bene in fase di produzione, in questo caso cosa devo usare?
- non ho capito le storage options [qui](https://google.github.io/adk-docs/runtime/command-line/#replay-sessions)
