_Tag:_ #informatica #cybersecurity #xxe

---
XML sta per eXtensible Markup Language. ==È un linguaggio di markup progettato per memorizzare e trasportare dati. L'XXE è una vulnerabilità che permette a un attaccante di interferire con l'elaborazione dei dati XML di un'applicazione==. Sfrutta un **parser XML configurato in modo errato** che permette il caricamento di =="entità esterne", ovvero riferimenti a dati situati al di fuori del documento XML stesso==. Un attacco XXE può consentire di:

- **Visualizzare file locali** sul server (es. `/etc/passwd`).
- **Interagire con sistemi esterni o backend** accessibili dal server.
- Eseguire attacchi **SSRF** (Server-Side Request Forgery) per colpire infrastrutture interne.
- Causare un **Denial of Service (DoS)** tramite l'esaurimento delle risorse del server (es. "Billion Laughs Attack").

## Come Funziona l'Attacco

L'attacco si basa sulla definizione di un'**entità personalizzata** all'interno del DTD (Document Type Definition) del file XML.

> [!NOTE] Cosa è un DTD
> Un **DTD** (_Document Type Definition_) è un sistema usato nei file **XML** per definire la **struttura valida** del documento. In pratica, è una specie di “schema” che dice come deve essere fatto un XML corretto.

1. **Definizione dell'entità**: L'attaccante usa la parola chiave `SYSTEM` e specifica un URL o un file locale.
    - _Esempio per leggere un file_: `<!ENTITY xxe SYSTEM "file:///etc/passwd">`.
2. **Utilizzo dell'entità**: L'entità viene richiamata nel corpo del messaggio XML.
3. **Elaborazione del server**: Il parser XML legge l'entità, recupera il contenuto del file richiesto e, se l'applicazione lo prevede, lo restituisce nella risposta HTTP.

![[Screenshot 2026-05-05 at 10.42.34.png]]

> [!NOTE] Cosa sono le entità in XML
> Le entità agiscono come delle "scorciatoie" o segnaposto. Quando il parser XML incontra un riferimento a un'entità, lo sostituisce con il valore corrispondente definito in precedenza. Si hanno differenti tipologie di entità:
> - **Entità Predefinite:** Sono integrate nelle specifiche del linguaggio XML per rappresentare caratteri speciali che altrimenti verrebbero interpretati come codice.
> - **Entità Personalizzate (Custom):** Possono essere definite dall'utente all'interno della **DTD** (Document Type Definition) usando questa sintassi: `<!DOCTYPE test [ <!ENTITY miaentita "valore dell'entità" > ]>` In questo caso, ogni volta che nel documento compare `&miaentita;`, il parser lo sostituirà con "valore dell'entità".
> - **Entità Esterne (External Entities):** Sono entità il cui valore si trova al di fuori della DTD dove sono dichiarate. Per definirle si usa la parola chiave `SYSTEM` seguita da un URL o dal percorso di un file.

## Tipologie di XXE

| **Tipo**            | **Descrizione**                                                                                                                                                             |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Direct XXE**      | Il contenuto del file richiesto viene restituito direttamente nella risposta dell'applicazione.                                                                             |
| **SSRF**            | L'entità punta a un URL interno per indurre il server a fare richieste HTTP verso altri sistemi (es. endpoint di metadati EC2).                                             |
| **Blind XXE (OOB)** | L'applicazione non restituisce dati nella risposta. L'attaccante deve "esfiltrare" i dati inviandoli a un server sotto il suo controllo tramite protocolli come FTP o HTTP. |
| **Error-based XXE** | Si forza un errore di parsing che include i dati sensibili nel messaggio d'errore stesso.                                                                                   |
| **Billion laughs**  | ==Si definiscono entità annidate che si espandono esponenzialmente. Il parser tenterà di espandere tutte le entità, esaurendo la memoria del server==.                      |
## Prevenzione e Best Practices

Si posso adoperare diverse strategie per difendersi:

- **Disabilitare l'elaborazione delle entità esterne**: Questa è la misura più efficace, configurando correttamente le librerie di parsing XML.
- **Validazione dell'input**: Sanificare i dati XML in ingresso.
- **Aggiornamento dei parser**: Utilizzare librerie XML moderne e aggiornate con le ultime patch di sicurezza.
- **Monitoraggio**: Loggare attività sospette come eccessivo consumo di risorse o chiamate a entità esterne insolite.