![Completamento](https://img.shields.io/badge/Stato-In%20corso-yellow)
# Entropia di una Google API Key
L'entropia misura l'incertezza che ha un'osservatore che non conosce la chiave.  
Matematicamente è la Shannon entropy, definita come:  
$$H = log_2(|KeySpace|)$$  
in questo caso si misura in bit.    
$KeySpace := $ lo spazio delle chiavi possibili


# Esempio 
La Google API Key ha 33 caratteri liberi Base64url cioè ciascun carattere può assumere uno dei
64 valori possibili (A-Z, a-z, 0-9,-,_) quindi la dimensione dello spazio è:
$$|KeySpace| = 64^{33} = 2^{6 \times 33} = 2^198$$
da cui 
> $$ H = 198 $$ 




