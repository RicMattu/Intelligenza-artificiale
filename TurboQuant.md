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

## Introduzione


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
