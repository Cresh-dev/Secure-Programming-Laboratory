_Tag:_ #informatica #javascript #linguaggio 

---
In JavaScript, per conservare valori e tenere traccia dello stato di un programma (che cambia nel tempo), si utilizzano le **variabili**, chiamate anche **binding** (legami). Un esempio classico di dichiarazione e inizializzazione è:

``` JavaScript
let a = 5 * 5; // La variabile 'a' conterrà il valore numerico 25
```

## Regole di Naming (Nomi delle variabili)

I nomi scelti per le variabili devono seguire regole precise:

- Possono contenere caratteri alfanumerici (lettere e numeri).
- Gli unici simboli speciali esplicitamente ammessi sono il simbolo del dollaro (`$`) e il trattino basso o underscore (`_`).
- **Convenzione:** In JavaScript la comunità adotta quasi universalmente la convenzione del **camelCase** (es. `nomeUtente`, `calcoloTotaleSpese`), dove la prima parola è minuscola e le successive iniziano con la maiuscola.

## Confronto tra le parole chiave: var, let e const

La gestione e il comportamento delle variabili cambiano drasticamente a seconda della parola chiave utilizzata per dichiararle (`var`, `let`, o `const`). Le differenze principali si articolano su tre pilastri: lo **Scope**, la **Riassegnazione (Reassignment)** e la **Ridichiarazione (Redeclaration)**.

### Scope (Ambito di visibilità)

Lo _scope_ determina in quali parti del codice una variabile è accessibile.

- **`var`:** ==Ha uno scope global (globale) e local (funzionale). Non riconosce i blocchi di codice (come i cicli for o le condizioni if)==.
- **`let` e `const`:** ==Oltre allo scope global e local, introducono lo block scope (scope di blocco)==. Significa che se vengono dichiarate all'interno di una coppia di parentesi graffe `{ }` (ad esempio dentro un `if` o un `while`), rimangono confinate lì dentro e non sono visibili all'esterno.

### Reassignment (Riassegnazione del valore)

Consiste nel cambiare il valore precedentemente salvato dentro una variabile già esistente (es. `a = 10;`).

- **`var` e `let`:** ==Consentono la riassegnazione. Il valore può essere sovrascritto liberamente nel corso del programma==.
- **`const`:** ==NON consente la riassegnazione . Una volta assegnato un valore iniziale, questo non può più essere cambiato (è una costante)==.

### Redeclaration (Ridichiarazione)

Consiste nel dichiarare nuovamente una variabile usando la stessa parola chiave e lo stesso nome nello stesso identico scope (es. riscrivere `let a = 5;` e subito dopo `let a = 10;`).

- **`var`:** ==Consente la ridichiarazione==. Questo comportamento, sebbene tollerato da `var`, è spesso fonte di bug e comportamenti imprevisti.
- **`let` e `const`:** ==NON consentono in alcun modo la ridichiarazione==. Se si prova a ridichiarare una variabile nello stesso ambito, JavaScript bloccherà l'esecuzione del codice restituendo un errore di sintassi (`SyntaxError`).

## Tabella Riassuntiva

|**KEYWORD**|**SCOPE**|**REASSIGNMENT**|**REDECLARATION**|
|---|---|---|---|
|**`var`**|global, local|**YES**|**YES**|
|**`let`**|global, local, block|**YES**|**NO**|
|**`const`**|global, local, block|**NO**|**NO**|

Nello sviluppo JavaScript moderno, l'uso di `var` è fortemente sconsigliato a causa dei suoi comportamenti permissivi e imprevedibili (come la ridichiarazione e la mancanza di block scope). La buona pratica prevede l'uso di **`const` di default** per tutti i valori che non devono cambiare, e l'uso di **`let` solo quando si sa con certezza** che la variabile dovrà essere riassegnata.