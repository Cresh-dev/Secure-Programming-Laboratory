_Tag:_ #informatica #javascript #linguaggio 

---
Quando si scrive uno script per una pagina HTML, si hanno a disposizione tre categorie di oggetti:

- **Native (Nativi):** Sono gli oggetti propri del linguaggio JavaScript, come `Object`, `Array` e `Function`.
- **BOM (Browser Object Model):** Sono forniti dal browser e servono per interagire con l'ambiente esterno alla pagina (es. `navigator` per info sul browser, `history` per la cronologia o `location` per l'URL).
- **DOM (Document Object Model):** Servono per manipolare gli elementi interni alla pagina visualizzata (l'oggetto principale è `document`).

L'oggetto **`window`** funge da contenitore globale per tutti questi elementi.

![[Screenshot 2026-05-04 at 12.20.10.png]]

## Il DOM (Document Object Model)

Il DOM trasforma un documento HTML in una **struttura a albero** composta da "nodi".

- Ogni tag HTML (come `<body>`, `<h1>`, `<p>`) diventa un nodo che puoi **creare, modificare o eliminare** dinamicamente tramite codice.
- L'oggetto **`document`** è la radice di questo albero e rappresenta l'intera pagina.
- **Proprietà utili di `document`**:
    - `forms`: Tutte le maschere di inserimento dati.
    - `images`: Tutte le immagini presenti.
    - `links`: Tutti i collegamenti ipertestuali.
    - `body`: L'intero corpo del documento.

## Gestione degli Eventi

Javascript permette alla pagina di "reagire" alle azioni dell'utente utilizzando gli **attributi di evento**.

|**Evento**|**Attributo HTML**|**Quando si attiva**|
|---|---|---|
|**Click**|`onClick`|Quando si clicca su un elemento.|
|**Mouse over**|`onMouseOver`|Quando il mouse entra nell'area di un elemento.|
|**Focus**|`onFocus`|Quando un elemento di un form viene selezionato/evidenziato.|
|**Submit**|`onSubmit`|Quando viene inviato un modulo (form).|
|**Load**|`onLoad`|Quando la pagina ha finito di caricarsi correttamente.|