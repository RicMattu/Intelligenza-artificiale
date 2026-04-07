Uno dei problemi che ha spinto i ricercatori di Google a proporre una nuova soluzione per i Transformer è 
quello della **KV Cache**, in questo articolo cerchiamo di capire di cosa si tratta.

Quando un Transformer-based LLM genera del testo, usa tutti i token precedenti per generare il successivo.

---------------------
Fonti:

https://research.google/blog/turboquant-redefining-ai-efficiency-with-extreme-compression/
https://medium.com/@comeback01/turboquant-and-the-kv-cache-revolution-toward-memory-boundless-llm-inference-906af7e69370
https://huggingface.co/blog/not-lain/kv-caching
