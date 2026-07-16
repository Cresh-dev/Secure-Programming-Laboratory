_Tag:_ #informatica #javascript #linguaggio 

---
È un linguaggio di scripting client-side introdotto nel 1995 nel browser Netscape Navigator per rendere le pagine Web “attive” e dinamiche. Attualmente è l’unico linguaggio di programmazione che i browser possono eseguire nativamente. Di seguito possiamo sottolineare una differenza fondamentale tra classi e prototipi:

- **Java** è basato sulle **classi** (strutture fisse definite a monte).
- **JavaScript** è basato sui **prototipi**: gli oggetti sono istanze che possono ereditare proprietà da altri oggetti e possono essere modificati dinamicamente a runtime (aggiungendo o rimuovendo funzioni e variabili al volo).

```javascript
const animale = {
  respira() { return "sto respirando"; }
};

const cane = {
  abbaia() { return "bau!"; }
};

Object.setPrototypeOf(cane, animale); // cane eredita da animale

cane.abbaia();   // "bau!"       → trovato su cane
cane.respira();  // "sto respirando" → trovato risalendo al prototipo
```

![[Screenshot 2026-05-04 at 10.48.53.png]]

> [!NOTE] Cosa succede se non viene trovata la proprietà nella catena
> `null` viene raggiunto **solo se la proprietà non esiste da nessuna parte** nella catena.

## Integrazione nel Documento HTML

Il codice JS viene inserito tramite il tag `<script>`. Il documento specifica tre modalità di caricamento critiche per le prestazioni (rendering del DOM):

1. **Standard:** ==Il browser interrompe il parsing HTML per scaricare ed eseguire lo script==.
2. **`defer`:** ==Carica lo script in background; l'esecuzione avviene solo al termine del parsing del DOM (ordine di esecuzione preservato)==.
3. **`async`:** ==Carica lo script in background; l'esecuzione avviene non appena lo scaricamento è terminato (ordine di esecuzione "load-first")==.

![[Screenshot 2026-05-04 at 11.05.52.png]]

## Concetti chiave del linguaggio

Di seguito sono presenti i concetti e le strutture fondamentali per comprendere in maniera completa questo linguaggio:

- [[Tipi di dati in javascript]]
- [[Type coercition]]
- [[Variabili e binding]]
- [[Controllo del flusso]]
- [[Funzioni e scope]]
- [[Oggetti, prototipi e classi]]
- [[Tipi di oggetti]]
- [[Un accenno sulla programmazione funzionale]]
- [[Programmazione asincrona]]

