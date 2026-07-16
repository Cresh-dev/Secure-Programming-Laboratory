_Tag:_ #informatica #cybersecurity #ajax

---
==AJAX non è un linguaggio di programmazione, ma un insieme di tecniche. Il suo scopo è permettere a un client di recuperare informazioni dal server in modo veloce e "trasparente" per l'utente==.

**Modello Standard (Sincrono):** L'utente fa una richiesta, deve attendere che il server la elabori e la pagina viene ricaricata per mostrare il risultato.

![[{561B2929-4413-4B52-A582-5A994D6B5382}.png]]

**Modello AJAX (Asincrono):** Le richieste avvengono in background tramite un "AJAX Engine". L'utente continua a interagire con la pagina mentre i dati vengono trasferiti e l'interfaccia si aggiorna solo dove necessario.

![[{51EA2F56-A944-4440-918F-A41F2EEC3F27}.png]]

> [!NOTE] **Esempio: completamento automatico di ricerca su Google**
> 1. L'utente inizia a digitare nella barra di ricerca di Google.
> 2. Ad ogni pressione di tasto viene effettuata una richiesta AJAX in background.
> 3. Il servizio restituisce una risposta in formato JSON con i suggerimenti disponibili.

Elementi Costitutivi:

- **HTML e CSS:** Per la presentazione.
- **DOM:** Per manipolare dinamicamente il layout durante l'esecuzione.
- **XMLHttpRequest:** ==L'oggetto JavaScript fondamentale per recuperare dati asincronamente==.
- **Formati dati:** XML, testo semplice o, più comunemente, **JSON**.

Tramite l'uso di javascript, possiamo combinare ed utilizzare tutte queste tecnologie.

## L'oggetto XMLHttpRequest (XHR)

È il cuore pulsante di AJAX. Permette di gestire il ciclo di vita di una richiesta HTTP. I metodi principali sono:

- `open(method, url, async)`: Prepara la richiesta specificando il metodo (GET, POST, ecc.) e l'URL.
- `send(data)`: Invia la richiesta al server.
- `setRequestHeader(header, value)`: Imposta gli header HTTP (es. il tipo di contenuto).
- `getAllResponseHeaders`:  Restituisce **tutti** gli header della risposta sotto forma di un'unica stringa. I vari header sono separati tra loro da una sequenza CRLF (ritorno a capo).
- `getResponseHeader`: Questo metodo è più mirato,  permette di ottenere il valore testuale di uno **specifico** header indicandone il nome.

### Proprietà e Stati

==La proprietà readyState indica a che punto è la richiesta (da 0 a 4)==:

- **0 (UNSENT):** ==Oggetto creato==.
- **1 (OPENED):** `open()` ==chiamato==.
- **2 (HEADERS_RECEIVED):** `send()` ==chiamato, header ricevuti==.
- **3 (LOADING):** ==Download dei dati in corso==.
- **4 (DONE):** ==Operazione completata con successo==.

Abbiamo anche altre proprietà tra cui:

- **`responseType`**: È una stringa che si imposta (dopo `open()` ma prima di `send()`) per dichiarare che tipo di dati ti si aspettano. I valori comuni sono `"json"`, `"text"`, `"document"` (per XML/HTML), `"blob"` o `"arraybuffer"` per dati binari.
- **`response`**: Contiene il corpo della risposta filtrato in base a quello che è stato scritto in `responseType`. Se hai impostato `"json"`, questa proprietà conterrà direttamente un oggetto JavaScript già pronto.
- **`responseText`**: Restituisce la risposta come una semplice stringa di testo. È il valore predefinito se non si specifica altro.
- **`responseXML`**: Restituisce un documento XML o HTML. Si usa solo se il server invia dati in quel formato e il browser riesce a farne il parsing.

## Vantaggi e svantaggi a confronto

|**Vantaggi**|**Svantaggi**|
|---|---|
|Compatibilità con i browser principali|Difficoltà nel gestire la cronologia e i segnalibri|
|Migliore interattività e usabilità|Problemi di indicizzazione nei motori di ricerca|
|Risparmio di risorse (si trasferiscono solo i dati necessari)|Rischio di abuso (troppe micro-interazioni)|

## JSON

Il **JSON** (JavaScript Object Notation) è il formato standard per lo scambio di dati tra client e server. Sebbene derivi dalla sintassi di JavaScript, è un formato di testo indipendente dal linguaggio e supportato da quasi tutti i moderni linguaggi di programmazione. Questo formato ha le seguenti caratteristiche:

- **Leggerezza**: È un formato semplice e versatile per lo scambio di dati.
- **MIME Type**: Il tipo ufficiale è `application/json` e l'estensione del file è `.json`.
- **Struttura**: Si basa su due strutture dati universali: collezioni di coppie nome-valore (oggetti) e liste ordinate di valori (array).

### Oggetti ({ })

![[{C5813636-1350-47D0-A7E5-36C6BB5E893A}.png]]

- Rappresentano un insieme non ordinato di coppie **nome-valore**.
- Il "nome" deve sempre essere una stringa racchiusa tra doppi apici.
- Ogni nome è seguito dai due punti (`:`) e le coppie sono separate da virgole.
- Esempio: `{"nome": "Mario", "eta": 30}`.

### Array (\[\])

![[{37305DFD-2D09-4508-8343-9B7FE56920A0}.png]]

- Rappresentano un insieme ordinato di valori.
- I valori sono separati da virgole e racchiusi tra parentesi quadre.
- Esempio: `["mela", "pera", "arancia"]`.

### Valori ammessi

Un valore in JSON può essere:

- Una **stringa** (tra doppi apici).
- Un **numero**.
- Un **oggetto** o un **array** (permettendo strutture annidate complesse).
- I valori booleani **true** o **false**.
- Il valore **null**.

### JSON e JavaScript

Poiché JSON è un sottoinsieme di JavaScript, l'integrazione è nativa e immediata:

- **Deserializzazione (`JSON.parse()`)**: Trasforma una stringa ricevuta dal server in un oggetto JavaScript utilizzabile dal codice.
- **Serializzazione (`JSON.stringify()`)**: Converte un oggetto JavaScript in una stringa JSON per poterlo inviare al server (ad esempio nel corpo di una richiesta POST).