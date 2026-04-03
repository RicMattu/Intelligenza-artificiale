# TurboQuant, il nuovo algoritmo di Google Research per migliorare l'AI.

Uno dei maggiori problemi che attualmente i ricercatori che si occupano di AI devono affrontare, è quello del peso dei modelli linguistici di ultima generazione. I miliardi di parametri necessari per far generare a ChatGPT, Claude e Gemini le risposte che utilizziamo possono richiedere centinaia di GB di memoria — ad esempio un modello da 70 miliardi di parametri occupa oltre 280 GB. Questo rende il loro utilizzo proibitivo lontano dai grandi data center nonché costoso in termini economici e ambientali.  
In questo contesto, il Team di Google Research ha anticipato il [nuovo algoritmo](https://research.google/blog/turboquant-redefining-ai-efficiency-with-extreme-compression/) di compressione, che presenterà all'importante [conferenza](https://iclr.cc/) internazionale sul machine learning.  

TurboQuant affronta il problema della memoria, principalmente occupandosi delle due fasi critiche del processo di generazione di una risposta da parte di un modello di intelligenza artificiale: la memorizzazione del contesto e la ricerca della risposta in una base documentale.

Quando gli poniamo una domanda, il nostro assistente artificiale la memorizza e genera una risposta. Quando la nostra chat continua, l'assistente tiene conto del contesto in cui si trova memorizzando tutte le domande e le risposte precedenti per generare risultati attinenti e aumentando il "peso" da portare dietro per ogni singola sessione.

D'altra parte, la maggior parte delle aziende adesso utilizza gli LLM (acronimo di Large Language Model) come base da specializzare con una serie di documenti specifici dell'azienda. Questo processo prende il nome di RAG (Retrieval Augmented Generation), i documenti dell'azienda vengono prima convertiti in formato digitale (tecnicamente in vettori numerici) per poter essere accessibili. Il confronto tra tutti questi documenti digitali richiede una memoria importante.

TurboQuant affronta il problema del peso in memoria del formato digitale dei documenti basandosi, come suggerisce il nome, sul concetto di quantizzazione.
L'idea di base è semplice, i numeri nei computer vengono memorizzati in celle di memoria di 32 posti (bit), riducendo la precisione con cui vengono rappresentati, ad esempio arrotondando alcune cifre dopo la virgola, è possibile memorizzarlo con meno spazio a disposizione. Non è diverso da quello che succede con i numeri irrazionali, come il pi greco, che avendo infinite cifre dopo la virgola, viene arrotondato alle prime due permettendoci di ricordare che è quasi $3,14$.

Secondo i ricercatori di Google, TurboQuant riesce a diminuire la memoria richiesta dai modelli senza comprometterne le prestazioni:  passando da 32 a 4 bit per la memorizzazione dei dati, l'algoritmo ottiene un incremento di velocità di calcolo fino a 8 volte, senza inficiare la qualità delle risposte.













