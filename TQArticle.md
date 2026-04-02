# TurboQuant, il nuovo algoritmo di Google Research per migliorare l'AI.
Uno dei problemi più importanti che attualmente i ricercatori che si occupano di AI si trovano ad affrontare è la memoria.
I modelli linguistici di ultima generazione richiedono una potentissima capacità di calcolo 


Il Team di Google Research ha anticipato il [nuovo algoritmo](https://research.google/blog/turboquant-redefining-ai-efficiency-with-extreme-compression/) che presenterà all'importante [conferenza](https://iclr.cc/) del settore.  
Con questo i ricercatori della Silicon Valley, si propongono di affrontare uno dei più importanti problemi dell'intelligenza artifciale: i costi della memorizzazione dei dati e dei processi che utilizzano memoria.  

Si tratta di un nuovo modello di compressione di dati, che verrà utilizzato per ridurre il peso dei miliardi di parametri che i modelli linguistici di grande dimensione come ChatGPT e Claude utilizzano.
Questo approccio promette di migliorare l'efficienza sia nell'addestramento di tali modelli, cioè nella fase in cui vwngono "programmati" per rispondere alle domande, sia nel migliorare le loro capacità in fase di ricerca documentale (tecnicamente RAG, Retrivial augmented generation), che ormai è diventato uno dei settori di ricerca più importanti, in quanto permette di utilizzare i potentissimi algoritmi delle big tech specializzandoli ai propri dati aziendali o ospedalieri.
Il principio matematico su cui si basa TurboQuant, come suggerisce il nome, è quello della quantizzazione vettoriale, cioè il processo di riduzione della dimensione e quindi della precisione di un valore.
Il concetto di base è semplice, i numeri nei computer vengono memorizzati in celle di memoria di 32 posti (bit), riducendo la precisione con cui vengono rappresentati, ad esempio arrotondando alcune cifre dopo la virgola, è possibile memorizzarlo con meno spazio a disposizione. Non è diverso da quello che succede con i numeri irrazionali, come il pi greco, che avendo infinite cifre dopo la virgola, viene arrotondato alle prime due permettendoci di ricordare che è quasi $3,14$.
Il problema è trovare il compromesso tra memoria occupata e la precisione che diventa sempre meno più sono le cifre che vengono trascurate.

Secondo i ricercatori di Google, TurboQuant riesce a diminuire la memoria richiesta dai modelli senza comprometterne le prestazioni, i dati trasmessi mostrano che questo algoritmo migliora di 8 volte le prestazioni riducendo il formati di memorizzazione da 32 a 4 bit e che è ideale per la ricerca vettoriale (che è alla base della RAG, citata sopra).

La necessità di questo algoritmo nasce proprio dall'esigenza di integrare i modelli pre-esistenti con grandi molti di dati perssonalizzati.











