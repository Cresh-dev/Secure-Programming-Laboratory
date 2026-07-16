_Tag:_ #informatica #javascript #linguaggio 

---
La **programmazione asincrona in JavaScript** è un insieme di tecniche e funzionalità che permettono di eseguire operazioni “in background” senza bloccare il flusso principale del programma (il _main thread_). È fondamentale perché JavaScript è **single-threaded**, quindi senza asincronia tutto resterebbe fermo in attesa di operazioni lente (es. richieste di rete, accesso a file, timer).

## Callback

Il metodo “storico”. Una funzione viene passata come argomento e chiamata quando l’operazione è finita.

`setTimeout(() => {   console.log("Finito!"); }, 1000);`

**Problema:** può portare al _callback hell_, cioè codice annidato e difficile da leggere.

## Il promise pattern

Introdotte per migliorare la gestione delle operazioni asincrone.  
Una _Promise_ rappresenta un valore che **non è ancora disponibile**, ma lo sarà in futuro (fulfilled, rejected).

```javascript
fetch("https://api.example.com/data")
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

**Vantaggi**:

- strutturazione lineare dell’esecuzione
- gestione errori più pulita con `.catch()`

## async/await

Un’evoluzione sintattica delle Promises che permette di scrivere codice asincrono in modo più semplice e “sincrono”.

```javascript
async function caricaDati() {
  try {
    const res = await fetch("https://api.example.com/data");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

**Vantaggi**:

- codice più leggibile
- gestione errori con `try/catch`
- si basa comunque sulle Promises