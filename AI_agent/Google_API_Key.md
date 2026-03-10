
## Cos'è?
In generale una API Key è un identificatore che client passa a un server per identificarsi.

La chiave API Google, è un codice alfanumerico legato a un indirizzo mail google (per transitività, in realtà a un Google Cloud Project) che identifica chi sta chiamando le API di Google.  
Ogni richiesta HTTP verso le API di google include questa chiave nell'header.  
ha il prefisso AIzaSy seguito da 35 caratteri alfanumerici^[aja] 
A differenza delle Service Account key (che hanno una scadenza e un ciclo di rotazione raccomandato),  
le API key di Google non scadono a meno che non vengano revocate esplicitamente. (ciò le rende comode ma più rischiose se esposte accidentalmente).

## A cosa serve? 

Google la usa per tre cose 
1. Identificazione
2. Autorizzazione
3. Quota tracking

## Nella creazione di un AI agent con ADK ?
## Osservazioni
1. La API key non va mai pubblicata.  
Per questo ADK la mette nel file .env che è nel .gitignore.  
Chiunque abbia la tua chiave può consumare la tua quota o, se hai abilitato la fatturazione, generare costi.

2. La API key può essere cambiata in qualsiasi momento

[aja] AJOajOJA

