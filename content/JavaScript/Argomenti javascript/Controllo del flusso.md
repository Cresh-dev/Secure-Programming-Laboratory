_Tag:_ #informatica #javascript #linguaggio 

---
![[Screenshot 2025-05-06 at 16.20.54.png]]
## Strutture Condizionali (Selezione)

Questi costrutti permettono di deviare il flusso del programma eseguendo blocchi di codice differenti in base al verificarsi di determinate condizioni.

- **Esecuzione condizionale (`if / else if / else`):** Rappresenta la struttura di controllo fondamentale. Il programma valuta un'espressione booleana; se questa risulta vera, viene eseguito il primo blocco di codice. In caso contrario, è possibile verificare ulteriori condizioni sequenziali tramite `else if` o catturare qualsiasi altra casistica residua mediante la clausola finale `else`.
- **Selezione multipla (`switch`):** Viene utilizzata come alternativa più leggibile e ordinata rispetto a una serie prolissa di `if` nidificati. Il costrutto valuta il valore di una singola variabile o espressione (`switch (value)`) e indirizza l'esecuzione verso il blocco corrispondente alla costante specifica (`case a`, `case b`). È prevista inoltre una sezione `default` per gestire i casi non esplicitamente contemplati.

## Strutture Iterative (Cicli)

I costrutti iterativi consentono di ripetere l'esecuzione di un blocco di istruzioni finché una determinata condizione rimane soddisfatta. L'immagine ne evidenzia tre tipologie:

- **Iterazione per vero a condizione iniziale (`while`):** In questo ciclo, la condizione di controllo viene valutata _prima_ di eseguire il corpo del ciclo. Se l'espressione è inizialmente falsa, le istruzioni interne non verranno eseguite nemmeno una volta.
- **Iterazione per vero a condizione finale (`do while`):** A differenza del precedente, questo costrutto esegue il blocco di istruzioni _almeno una volta_, poiché la verifica dell'espressione condizionale avviene solo al termine dell'iterazione.
- **Ciclo conteggiato (`for`):** È una struttura ottimizzata per le iterazioni di cui si conosce a priori (o si può calcolare) il numero di ripetizioni. Integra in un'unica riga l'inizializzazione dell'indice (`let i=0`), la condizione di permanenza nel ciclo (`i < N`) e l'espressione di incremento o aggiornamento (`i=i+1`).

> [!NOTE] Controllo del flusso
> In calce alla panoramica sui cicli, viene ricordato che è sempre possibile alterare il comportamento standard di qualsiasi iterazione utilizzando due parole chiave fondamentali: **`continue`**, che interrompe l'iterazione corrente per passare immediatamente alla successiva, e **`break`**, che interrompe bruscamente e definitivamente l'intero ciclo.