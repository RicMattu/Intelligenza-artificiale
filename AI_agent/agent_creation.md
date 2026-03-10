# Guida alla creazione di un agent con ADK
![Completamento](https://img.shields.io/badge/Stato-In%20corso-yellow)
1. installare google-adk (possibilmente in un venv)
2. creare un agent project
   In questa fase viene chiesto di scegliere l'LLM (default: gemini 2.5) e dove farlo girare se Google AI o Vertex AI
   quindi di inserire al Google API key
   Se tutto va bene vengono crate le cartelle
   agente_01/ - .env - **init**.py - agent.py
3. Si può runnare l'agent in due modi `adk run` per usare la command line o `adk web --port 1234` per usare la web UI (vedi [documentazione ufficiale](https://google.github.io/adk-docs/get-started/python/#set-your-api-key]
   per approfondire).



## agent.py
Questo file deve contenere un `root_agent` che è l'unico elemento richiesto.  
IL file di default è:
```python
root_agent = Agent(
    model='gemini-2.5-flash',
    name='root_agent',
    description='A helpful assistant for user questions.',
    instruction='Answer user questions to the best of your knowledge',
)
```
quindi si possono aggiungere dei tool che poi l'agente userà per svolgere i compiti, ad esempio 

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

## Multi - tool agent


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
```python
from google.adk.sessions import DatabaseSessionService

session_service = DatabaseSessionService(
    db_url="sqlite:///sessions.db"   # file locale SQLite
    # db_url="postgresql://user:pass@localhost/mydb"  # oppure PostgreSQL
)
```
immagazzina la conversazione in una tabella SQL (ogni messaggio diventa una riga).
Richiede un db raggiungibile.













