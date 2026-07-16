_Tag:_ #informatica #javascript #linguaggio 

---
Gli oggetti in JavaScript sono collezioni arbitrarie di proprietà, più precisamente array associativi mutabili le cui chiavi rappresentano i nomi delle proprietà. Ad esempio:

```javascript
let description = {
	work: "Went to work",
	"touched tree": "Touched a tree"
};
```

I valori delle proprietà sono accessibili mediante la notazione obj.prop oppure obj\['prop'\]. È possibile creare o rivalorizzare a posteriori una proprietà con l’operatore di assegnazione. Per eliminare una proprietà è sufficiente usare `delete obj.prop`. Gli oggetti possono possedere dei metodi, ovvero delle proprietà contenenti funzioni, a differenza di molti linguaggi orientati agli oggetti, in JavaScript non c’è distinzione fra funzione e metodo, a parte il fatto che la funzione di un oggetto può usare la keyword this per riferirsi all’oggetto stesso.

## Array

L’oggetto Array permette di collezionare una sequenza (non associativa) di valori. Si tratta di oggetti ridimensionabili e che possono contenere mix di differenti tipi di dati. Gli elementi di un array sono indicizzabili a partire da 0. Un array può essere definito ne seguente modo:

```javascript
let fruits = ["banana", "orange", "apple"];
```

Per iterare un array possiamo utilizzare il metodo di ordine superiore `forEach()`. Per gli array abbiamo anche il metodo `map()` è simile a `forEach()` ma anziché effettuare operazioni per ogni elemento dell’array, ci permette di ottenere un nuovo array i cui elementi sono il risultato della mappa applicata su ogni elemento dell’array originale.

## Prototipi

Se provassimo a creare un oggetto vuoto con `let obj = {};`, scopriremmo che esso possiede metodi come ad esempio `toString()`. Ciò accade perché ogni oggetto in JavaScript deriva da un proprio prototipo da cui “eredita” tutte le proprietà e i metodi. In particolare, gli oggetti “semplici” sono realizzati mediante il prototipo di Object. È possibile creare nuovi oggetti usando un altro oggetto come prototipo mediante il metodo `Object.create()` nel seguente modo:

```javascript
let speaker = {
	type: 'generico',
	speak(parola) {
		console.log(`lo speaker di tipo '${this.type}' dice: '${parola}'`);
	}
}

let speakerUmano = Object.create(speaker);
speakerUmano.type = 'umano';
speakerUmano.speak('ciao'); // lo speaker di tipo 'umano' dice: 'ciao'
```

### Prototype chains

La **Prototype Chain** (Catena dei Prototipi) è il meccanismo fondamentale che JavaScript usa per l'ereditarietà. È il "filo invisibile" che collega gli oggetti tra loro, permettendo a un oggetto di accedere a proprietà e metodi definiti altrove. In JavaScript, ogni oggetto ha un legame interno con un altro oggetto, chiamato il suo **prototipo**. Quando si prova ad accedere a una proprietà:

1. Il motore JavaScript la cerca prima nell'oggetto stesso.
2. Se non la trova, non si arrende subito restituendo `undefined`.
3. Sale al livello superiore, ovvero nel **prototipo**, e cerca lì.
4. Questo processo continua risalendo la catena finché non trova la proprietà o raggiunge la fine della catena (`null`).

## Costruttore

Nella pratica, JavaScript fornisce una maniera semplice per la realizzazione di prototipi e oggetti basati su di essi, che fa uso della funzione costruttore. La funzione costruttore è strutturata nel seguente modo:

```javascript
function Speaker(type) {
	this.type = type;
	this.speak = function (parola) {
		console.log(`lo speaker di tipo '${this.type}' dice: '${parola}'`);
	}
}

let speakerUmano = new Speaker('umano');
```

Utilizzando la keyword `new` davanti al nome di una funzione, quest’ultima sarà trattata come costruttore. Tutte le funzioni, infatti, ricevono una proprietà chiamata prototype che si può sovrascrivere o a cui si possono aggiungere proprietà: questa proprietà contiene il prototipo degli oggetti che saranno costruiti mediante tale funzione se usata come costruttore.

## Classi in JS

Dal 2015, JavaScript è stato esteso con le classi ES2015, che si basano sempre sul concetto di prototipo, lo semplificano nella notazione e lo estendono nelle potenzialità (metodi statici, metodi di istanza, ereditarietà, …)

```javascript
class Speaker {
	constructor(type) {
		this.type = type;
	}
	speak(line) {
		console.log(`The ${this.type} speaker says '${line}'`);
	}
}

let humanSpeaker = new Speaker('human');
```

Questa notazione ci permette di specificare un insieme di metodi, compreso il costruttore chiamato sempre `constructor`. 