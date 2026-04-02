## Sintesi dell'articolo Turbo Quant
In questo file troverete una sintesi informale del contenuto dell'articolo [originale](https://arxiv.org/abs/2504.19874).

Il titolo originale dell'articolo è _TurboQuant: Online Vector Quantization with Near-optimal
Distortion Rate_, oltre a sintetizzarne il contenuto (senza approfondirne i concetti matematici, cioè senza formule), cercheremo di rispondere alle seguenti domande:
1. Cosa si intende per _vector quantization_ e per _online vector quantization_?
2. Cos'è il _distortion rate_?

## Abstract
(Questa è l'unica parte dell'articolo che , per completezza, è stata tradotta integralmente.)
Il problema della vector quantization ha radici nella teoria dei codifica dei segnali di Shannon [^1], ha l'obiettivo
di quantizzare vettori in spazi euclidei ad alta dimensione minimizzanto la distorsione della loro struttura geometrica.  
Gli autori propongono TurboQuant per affrontare il problema della distorsione sia in termini di MSE che di prodotto scalare, superando i limiti dei metodi esistenti che falliscono nel raggiungere rates di distorsione ottimali.  
Il nostro algoritmo, indipendente dal tipo di dato e adatto ad applicazioni online, raggiunge un quasi ottimale rate di distorsione (con un piccolo fattore costante) per tutte le dimensioni e bit-widths.

TurboQuant consegue questo obiettivo ruotando randomly gli input vectors, inducendo una distribuzione Beta concentrata nelle coordinate e mediando la proprietà di quasi-indipendenza di coordinate distinte in casi di alta dimensionalità per applicare sempliccemente un quantizzatore scalare per ciascuna coordinata.
Riconoscendo che gli MSE-optimal quantizers introducono bias nella stima del prodotto scalare, gli autori propongono un approccio a due stadi:
applicare un quantizzatore MSE seguito da una trasformazione 1-bit QJL sul residuo, restituendo un quantizzatore scalare unbiased.
Hanno anche formito una dimostrazione formale del limite inferiore dal punto di vista della teoria dell'informazione sul miglior errore di distorsione, dimostrando che TurboQuant si avvicina a questo limite, differendo solo di un piccolo fattore costante (≈ 2.7).  
I risultati sperimentali confermano i risultati teorici ottenuti, mostrando che per la quantizzazione KV cache, si raggiunge un _absolute quality neutrality_ con 3.5 bits per canale e una _marginal quality degradation_ di 2.5 bits per canale. Inoltre, nei compiti di tipo _nearest neighbor_, il metodo proposto supera le tecniche di quantizzazione esistenti rducendo l'_indexing time_ praticamente a zero.

Letta l'introduzione, aggiungiamo alle due precedenti le seguenti domande:
3. Cos'è la KV cache quantization?
4. Cosa sono la  _absolute quality neutrality_ e la _marginal quality degradation_?

## 1. Introduzione
Nell'introduzione, viene definito il concetto di quantizzazione vettoriale (_compress high dimensional vectors by quantizing them–converting floating-point coordinate values to low-bitwidth integers–while minimizing distortion_) e vengono ricordati i lavori di Shannon che hanno stabilito che il minimo distrtion rate raggiungibile è determinato dalle proprietà statistiche della sorgente e della metrica di misura.  
Dopodichè viene spiegamo perchè lo studio della VQ in generale è utile per gli LLM, principalmente vengono sostenute tre ragioni:
- in generale, le capacità dei LLM dipendono dalla dimensione del modello e dalla lunghezza del contesto che riescono a mantenere, questo richiede un grande impiego di memoria e causa un tempo di latenza dovuto, sostanzialmente, ai tempi di comunicazione tra la memoria rigida e la RAM. La quantizzazione dei parametri può accorciare questa latenza. Qui gli autori osservano anche che i modelli di quantizzazione devono concentrarsi sulla compressione dei parametri preservando il prodotto scalare (che è quello che viene effettivamente usato dagli LLM).
- i modelli decoder based (es. GPT, LLama) memorizzano key e value nella cosiddetta KV cache. La riduzione di questa cache senza compromettere l'accuratezza è uno dei problemi principali e la VQ lo affronta.
- infine la vector search, fondamentale in ambito RAL, può essere resa più efficiente dalla VQ.  

Se queste sono le motivazioni che giustificano la VQ in generale, TQ si propone in questo contesto come un modello che migliora il trade-off che tutti gli schemi di VQ devono affrontare: slow computation vs optimal distortion rate.
Il TQ è un processo a due stadi, prima viene applicato un VQ con distortion rate ottimale in termini di MSE e dopo viene appicato un 1-bit quantizer al residuo , il risultato è un quantizer con poca distorsione e unbiased.

## 1.1 Definizione del problema 
Fedeli alla linea di non appesantire questa sintesi con formule, posticipiamo la definizione formale del problema.

## 1.2 Lavori collegati
In questa sezione, gli autori hanno lasciato un po' di briciole per approfondire argomenti collegati.
1. La teoria della VQ inizia con Shannon e continua con vari contributi (Zador 1963, Gersho, etc..) soprattutto teorici.
2. Online vs Offline:
3. Online KV cache quantization.



[^1]:Shannon


<!--
Bibliografia
@misc{zandieh2025turboquantonlinevectorquantization,
      title={TurboQuant: Online Vector Quantization with Near-optimal Distortion Rate}, 
      author={Amir Zandieh and Majid Daliri and Majid Hadian and Vahab Mirrokni},
      year={2025},
      eprint={2504.19874},
      archivePrefix={arXiv},
      primaryClass={cs.LG},
      url={https://arxiv.org/abs/2504.19874}, 
}-->
