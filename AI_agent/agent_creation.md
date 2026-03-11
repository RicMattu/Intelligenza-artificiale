# Guida agli ADK AI agent
![Completamento](https://img.shields.io/badge/Stato-In%20corso-yellow)

Creare un AI Agent con l'Agent DEvelopment Kit di google è ababstanza semplice.  
Sintetizzando la procedura descritta [qui](), possiamo individuare tre passaggi fondamentali:
1. Installare google-adk (possibilmente in un venv)
2. Creare un agent project, cioè una cartella opportuna in cui contenere i file.
   In questa fase viene chiesto di scegliere il LLM (default: gemini 2.5 flash) e dove farlo girare se su Google AI o Vertex AI [^1], quindi di inserire al Google API key[^2]. Se tutto va bene vengono crate le cartelle:
   - agente_01/
   - - .env
   - - _ _ init _ _.py
   - - agent.py
4. Si può quindi runnare l'agent. Esistono principalmente due modi standard:
   1.  `adk run` per usare la command line,
   2.  `adk web --port 1234` per usare la web UI (vedi [documentazione ufficiale](https://google.github.io/adk-docs/get-started/python/#set-your-api-key] per approfondire).

## Anali si del file agent.py
IL file che viene creato di default è:
```python
root_agent = Agent(
    model='gemini-2.5-flash',
    name='root_agent',
    description='A helpful assistant for user questions.',
    instruction='Answer user questions to the best of your knowledge',
)
```
Questo file deve contenere almeno un `root_agent`, è l'unico elemento richiesto.  
Per personalizzare l'agent si possono aggiungere dei [tool](#tools) che poi l'agente userà per svolgere i compiti, ad esempio:

```python
#tool implementation
def get_current_time(city: str) -> dict:
    """Returns the current time in a specified city."""
    return {"status": "success", "city": city, "time": "10:30 AM"}
```
e quindi si aggiornano i parametri dell'Agent:
```python
root_agent = Agent(
    model=...
    name=...
    description="Tells the current time in a specified city.",
    instruction="You are a helpful assistant that tells the current time in cities. Use the 'get_current_time' tool for this purpose.",
    tools=[get_current_time],
)
```

## Tools
L'esempio mostrato sopra è di un  functional tool.
ADK supporta 5 categorie di tools: 

### 1. Function Tools
Si possono passare come tools delle semplici funzioni python:
```python
def calcola_media(valori: list[float]) -> dict:
    """
    Calcola la media aritmetica di una lista di numeri.
    
    Args:
        valori: lista di numeri float
    
    Returns:
        dict con 'media' e 'n'
    """
    if not valori:
        return {"error": "lista vuota"}
    return {"media": sum(valori) / len(valori), "n": len(valori)}
```
Il modello si basa sulla docstring per capire quando usare il tool, è obbligatorio merrere i types e che il return type sia un dict.

### 2. Built-in tools 
È possibile utilizzare dei tool predefiniti ad es.
- google_search (per cercare su internet)
- 
La procedura per operare con questi è molto semplice, basta importarlo e aggiungerlo alla lista:
```python
from google.adk.tools import google_search 

root_agent = Agent(
    name="root_agent",
    model="gemini-2.5-flash",
    description="A helpful assistant for user questions.",
    instruction="Answer user questions to the best of your knowledge.",
    tools=[google_search])
```
L'unica limitazione è che un unico agente può avere un solo build-in tool. Per poterne combinare di pìù bisogna adottare una struttura [agent as tools] (#agent-as-tools)

### 3. Agent-as-Tools / Multi Agent hierarchy
È possibile definire una gerarchia di agenti in modo da avere molti agenti specializzati piuttosto che un unico agente con molti tools.  
Questa struttura permette di risolvere il problema del numero di build-in tools oltre che avere altri vantaggi (esportare agenti, localizzazione dei problemi, ecc..).
Dopo aver definito il songolo agente figlio lo si passa all'agent padre semplicemente come un tool:
```python
search_agent = Agent(
    name="search_agent",
    model="gemini-2.5-flash",
    description="Cerca informazioni aggiornate su internet.",  # ← il root legge questo
    instruction="Sei uno specialista in ricerche web. Rispondi solo con fatti trovati online.",
    tools=[google_search],
)

# Agente specialista 2: esecuzione codice
coding_agent = Agent(
    name="coding_agent",
    model="gemini-2.5-flash",
    description="Esegue calcoli e codice Python.",
    instruction="Sei uno specialista in calcoli numerici. Usa sempre il code executor.",
    code_executor=BuiltInCodeExecutor(),
)

# Root agent: orchestra i due specialisti
root_agent = Agent(
    name="root_agent",
    model="gemini-2.5-flash",
    description="Assistente generale.",
    instruction="""
        Sei un assistente generale. 
        Per ricerche web usa search_agent.
        Per calcoli e codice usa coding_agent.
    """,
    tools=[
        AgentTool(search_agent),   # ← wrappato come tool
        AgentTool(coding_agent),   # ← wrappato come tool
    ]
)
```
IL ruolo del root agent è quello di orchestrare gli agenti figli decidendo quale utilizzare e come utilizzare il suo output per formulare la risposta finale.  
C'è una differenza tr apassare i sotto-agenti come sub agents o come Agent as Tools. Nel caso di sopra è l'agente padre a rielaborare l arisposta finale. Nel caso di sub - agents:
```python
booking_agent = Agent(name="Booker",  description="Gestisce prenotazioni voli e hotel.")
info_agent    = Agent(name="Info",    description="Risponde a domande generali.")

coordinator = Agent(
    name="Coordinator",
    model="gemini-2.5-flash",
    instruction="Delega prenotazioni a Booker, domande generali a Info.",
    sub_agents=[booking_agent, info_agent]   # ← AutoFlow decide chi chiamare
)
```
la risposta viene completamente elaborata dal sottoagente.
Anche in termini di numero di chiamate c'è una differenza:
1. con AgentTool ogni invocazione è una run separata e indipendente: il parent fa una chiamata LLM per decidere di chiamare il tool, poi il child fa almeno un'altra chiamata LLM per eseguire il task. Minimo 2 chiamate LLM per task.
2. con sub_agents il parent trasferisce il controllo con transfer_to_agent() — che è una function call già inclusa nella risposta LLM del parent, non una chiamata separata. Il child poi riceve il contesto già costruito. Il routing costa 0 chiamate extra.


### 4. Long Running Function Tools
### 5. third party tools





## Session service
Dove viene salvata la storia della conversazione?

Tecnicamente la storia è una lista di messaggi
```python
[
    Content(role='user',  parts=[Part(text="ciao")]),
    Content(role='model', parts=[Part(text="ciao! come posso aiutarti?")]),
    Content(role='user',  parts=[Part(text="cerca notizie sull'AI")]),
    Content(role='model', parts=[Part(text="ecco cosa ho trovato...")]),
]```
Questa viene utilizzata come contesto da Gemini (il Runner la manda a Gemini).
Esistono tre impostazioni possibili:
1. Default
```python
from google.adk.sessions import InMemorySessionService

session_service = InMemorySessionService()
```
Salva la conversazione nella RAM, quando l'agente viene riavviato o il processo 
termina, si perde tutto.  
È utile per test rapidi o demo.

2. DatabaseSessionService


Lanciando il comando adk run in questo modo: 
```bash
adk run --session_service_uri="sqlite+aiosqlite:///./history_mio_agente.db" agente_01
```
la memoria viene salvata nel db: history_mio_agente.db
Però non viene reinvocata alle successive chiamate dell'agent

```python
import asyncio
from google.adk.runners import Runner
from google.adk.sessions import DatabaseSessionService
from google.genai import types
from agent import root_agent, APP_NAME, USER_ID, SESSION_ID
from dotenv import load_dotenv
load_dotenv()

# per caricare l amemoria o generarla:
async def main():
    session_service = DatabaseSessionService(
        db_url= "sqlite+aiosqlite:///./history_mio_agente.db"
    )

    # se esiste ricarico la sessione esistente
    exixting_session = await session_service.list_sessions(app_name=APP_NAME, user_id=USER_ID)
    if exixting_session.sessions:
        session_id = exixting_session.sessions[0].id
        print(f"Ricaricando sessione esistente: {session_id}")
    else:
        session = await session_service.create_session(app_name=APP_NAME, user_id=USER_ID, session_id=SESSION_ID)
        session_id = session.id
        print(f"Nuov asessione creata: {session_id}")

    runner = Runner(
        agent=root_agent,
        app_name=APP_NAME,
        session_service=session_service,
    )
    while True:
        user_input = input("Tu: ")
        if user_input.lower() in ("exit", "quit"):
            break

        response_text = ""
        async for event in runner.run_async(
            user_id=USER_ID,
            session_id=session_id,
            new_message=types.Content(
                role="user",
                parts=[types.Part(text=user_input)]
            ),
        ):
            if event.is_final_response() and event.content:
                for part in event.content.parts:
                    response_text += part.text or ""
        print(f"Agent: {response_text}")

asyncio.run(main())
```
immagazzina la conversazione in una tabella SQL (ogni messaggio diventa una riga).
Richiede un db raggiungibile.


## Struttura e come vengono invocati
Runner etc..


## Q e A
- nome dell'agente e nome del progetto?



[^1]: Che differenza c'è?
[^2]: A cosa serve la google API key?


