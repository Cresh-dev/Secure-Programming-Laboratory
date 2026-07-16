_Tag:_ #informatica #javascript #linguaggio 

---
JavaScript non implementa programmazione funzionale pura ma ne mutua alcune idee: funzioni come oggetti di prima classe, chiusure, funzioni anonime, … ==Le funzioni in JavaScript sono oggetti di prima classe: le loro definizioni (in un binding o anonime) rappresentano “valori” che possono essere passati come argomenti ad altre funzioni (callback), possono essere restituiti come valori da altre funzioni e possono essere inseriti in qualunque struttura dati==.

```javascript
function callbackFunc() { console.log("timeout"); }
setTimeout(callbackFunc, 3*1000);

mailbox.sendMessage(message, () => console.log(“Messaggio inviato”));
```

## Il this

In JavaScript, la parola chiave `this` fa **riferimento al contesto di esecuzione corrente**. Il valore di `this` cambia a seconda di **come** e **dove** viene invocata una funzione.  Non è legato alla funzione stessa, ma **al modo in cui viene chiamata**.

### Contesto globale (o fuori da qualsiasi funzione/class)

```javascript
console.log(this); // In un browser: window
```

- In **ambiente browser**, `this` fuori da qualsiasi funzione punta all’oggetto globale `window`.
- In **Node.js**, invece, `this` nel contesto globale è `{}` (un oggetto vuoto, non `global`).

### Dentro una funzione (non in strict mode)

```javascript
function normale() {
  console.log(this);
}

normale(); // window (in browser)
```

Se chiami una funzione da sola (non come metodo), `this` punta di nuovo a `window` (in modalità **non strict**).

### Dentro una funzione in modalità "strict"

```javascript
'use strict';

function test() {
  console.log(this);
}

test(); // undefined
```

In **strict mode**, se chiami una funzione "normale", `this` è `undefined`.

### Dentro un metodo (cioè una funzione dentro un oggetto)

```javascript
const persona = {
  nome: 'Luca',
  saluta: function() {
    console.log(this.nome);
  }
};

persona.saluta(); // Luca
```

Qui `this` fa riferimento all’**oggetto che chiama il metodo** (`persona`).

### Assegnazione del metodo a una variabile

```javascript
const persona = {
  nome: 'Luca',
  saluta: function() {
    console.log(this.nome);
  }
};

const saluto = persona.saluta;
saluto(); // undefined (o errore in strict mode)
```

In questo caso, `saluto` viene eseguita **fuori dal contesto dell’oggetto**, quindi `this` non punta più a `persona`.

### Con `bind`, `call` e `apply`

Questi tre metodi permettono di **forzare** il valore di `this`.

```javascript
function saluta() {
  console.log(this.nome);
}

const persona = { nome: 'Luca' };

saluta.call(persona);  // Luca
saluta.apply(persona); // Luca

const bound = saluta.bind(persona);
bound(); // Luca
```

- `call`: chiama la funzione con `this` settato a un oggetto specifico, passando gli argomenti **uno per uno**.
- `apply`: uguale a `call` ma gli argomenti si passano **in un array**.
- `bind`: non chiama subito la funzione, ma restituisce una **nuova funzione** con `this` legato a un oggetto.

### Arrow Functions (`=>`)

Le arrow function **non hanno un proprio `this`**, ma ereditano quello del **contesto in cui sono state dichiarate**.

```javascript
const persona = {
  nome: 'Luca',
  saluta: function() {
    const inner = () => {
      console.log(this.nome); // eredita this da saluta()
    };
    inner();
  }
};

persona.saluta(); // Luca
```

Se avessimo usato una funzione normale al posto della arrow function, `this` non avrebbe funzionato come ci aspettiamo.

### this nelle classi

```javascript
class Persona {
  constructor(nome) {
    this.nome = nome;
  }

  saluta() {
    console.log(`Ciao, sono ${this.nome}`);
  }
}

const p = new Persona("Luca");
p.saluta(); // Ciao, sono Luca
```

Dentro i **metodi di classe**, `this` punta all’istanza dell’oggetto creato con `new`.

### Attenzione: `this` nei callback

Nei callback, soprattutto in metodi come `setTimeout` o in `forEach`, spesso `this` può non essere ciò che ci aspettiamo.

```javascript
const persona = {
  nome: "Luca",
  saluta: function () {
    setTimeout(function () {
      console.log(this.nome); // undefined (this è window o undefined in strict mode)
    }, 1000);
  }
};

persona.saluta();
```

Soluzione: usare una arrow function:

```javascript
saluta: function () {
  setTimeout(() => {
    console.log(this.nome); // Luca
  }, 1000);
}
```