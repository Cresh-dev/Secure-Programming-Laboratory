_Tag:_ #informatica #cybersecurity #xss

---
==L'XSS sfrutta la capacità delle applicazioni web di eseguire script sui browser degli utenti, rendendoli vulnerabili a modifiche non autorizzate==. Esistono tre categorie principali basate su come il codice malevolo viene gestito dal server e dal browser.

## Stored XSS (Persistente)

- **Funzionamento:** ==Il codice malevolo viene salvato permanentemente nel database del server prima di essere eseguito==.
- **Pericolosità:** È molto pericoloso perché può colpire un gran numero di utenti (chiunque visualizzi la pagina contenente i dati infetti).
- **Esempio:** Un utente scrive un commento in un forum o un feedback bancario includendo un tag `<script>`. Quando un impiegato della banca visualizza quel commento, lo script viene eseguito nel suo browser, rubando potenzialmente dati sensibili.

![[Screenshot 2026-05-04 at 20.51.09.png]]

### Cross-Origin Resource Sharing (CORS)

Il **Cross-Origin Resource Sharing (CORS)** è un meccanismo basato su header HTTP che permette a un server di indicare al browser quali origini (diverse dalla propria) sono autorizzate a caricare risorse. Normalmente, per motivi di sicurezza, i browser seguono la **Same-Origin Policy (SOP)**: ==un'applicazione web può richiedere risorse (come dati o script) solo dallo stesso dominio, protocollo e porta da cui è stata caricata. Il CORS serve a "sbloccare" questa restrizione in modo controllato==.

![[Screenshot 2026-05-05 at 09.16.35.png]]

Quando un sito (es. `server-a.com`) prova a richiedere dati tramite JavaScript (XMLHttpRequest o Fetch) da un altro sito (es. `server-b.com`), il browser interviene:

- Se la richiesta è **semplice** (es. un GET standard), il server risponde includendo l'header `Access-Control-Allow-Origin` per dire al browser se quel dominio è autorizzato.
- Se la richiesta è **complessa** (modifica dello stato del server come una POST), il browser effettua prima un controllo chiamato **"preflight"**.

Con il preflight il browser invia automaticamente una richiesta preliminare usando il metodo **OPTIONS** prima di inviare la richiesta effettiva. In questa fase:

- Il browser chiede il permesso specificando il metodo (`Access-Control-Request-Method`) e gli header che intende usare.
- Il server risponde confermando quali origini, metodi e header sono accettati (es. `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`).

## Reflected XSS (Non persistente)

- **Funzionamento:** ==Il codice non viene salvato nel database ma viene "riflesso" dal server direttamente verso il client nelle risposte HTTP==.
- **Metodo:** L'attaccante invia un link malevolo alla vittima (via email o pubblicità). Quando l'utente clicca, il payload presente nell'URL viene inserito nella pagina web risultante ed eseguito.
- **Esempio:** Un campo di ricerca che riflette il testo cercato nell'URL (es. `search?query=Content+<script>...</script>`).

![[Screenshot 2026-05-05 at 09.19.52.png]]

## DOM-based XSS

- **Funzionamento:** ==L'attacco avviene interamente sul lato client modificando il Document Object Model (DOM). La risposta HTTP del server non cambia==.
- **Componenti chiave:**
    - **Source:** Un oggetto DOM che contiene dati controllati dall'attaccante (es. `window.location.href`).
    - **Sink:** Un'API DOM capace di eseguire script partendo da testo (es. `document.write()`).
- **Esempio:** Un parametro nell'URL usato per selezionare la lingua che viene iniettato direttamente nel codice della pagina tramite JavaScript.

## Strategie di Difesa

Per proteggere un'applicazione, vengono suggerite diverse strategie:

- **Validazione e Encoding:**
    - Validare l'input degli utenti usando delle **whitelist** (caratteri ammessi).
    - ==Eseguire l'encoding/escaping (trasformazione di caratteri "speciali" o "pericolosi" in rappresentazioni sicure) dei dati prima di iniettarli nella pagina==.
    - ==Utilizzare librerie come DOMPurify per filtrare l'HTML se l'applicazione deve permettere l'inserimento di markup==.
- **Content Security Policy (CSP):**
    - ==Un meccanismo di sicurezza del browser che limita le risorse (script, immagini) che una pagina può caricare tramite l'header Content-Security-Policy.==
    - **Esempio:** ==default-src 'self' permette di caricare risorse solo dallo stesso dominio.==

> [!NOTE] Distinzione tra CORS e CSP
> I **CORS** gestiscono i permessi di accesso dall'esterno: permettono a un server di stabilire quali applicazioni o siti web stranieri abbiano l'autorizzazione per richiedere e ricevere le sue risorse. La **CSP**, al contrario, presidia l'esecuzione interna: impone al browser di un utente regole rigide su quali script, immagini o connessioni la pagina web sia autorizzata a caricare, neutralizzando i tentativi di iniezione di codice maligno.
