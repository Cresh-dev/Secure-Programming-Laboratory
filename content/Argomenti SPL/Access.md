_Tag:_ #informatica #cybersecurity

---
==Il Controllo degli Accessi (Access Control) è il processo cardine della sicurezza informatica deputato a regolare l'utilizzo delle risorse di un sistema secondo una determinata politica di sicurezza==. Consente l'accesso esclusivamente alle entità autorizzate (che si tratti di utenti, programmi, processi o altri sistemi).

Secondo le definizioni formali dei principali enti di standardizzazione:

- **NIST:** Lo definisce come il processo di concessione o diniego di richieste specifiche per ottenere e utilizzare informazioni, servizi di elaborazione correlati ed accedere a strutture fisiche.
- **IETF:** Lo descrive come il meccanismo con cui l'uso delle risorse di sistema viene regolato in accordo a una politica di sicurezza.

### I Tre Pilastri: Soggetti, Oggetti e Diritti di Accesso

Il funzionamento di qualsiasi modello di controllo degli accessi si basa sulla relazione tra tre elementi:

- **Oggetto (Object):** ==È la risorsa protetta a cui l'accesso è controllato. Esempi tipici includono file, directory, record di database, pagine di memoria, programmi, dispositivi, porte e nodi di rete==.
- **Soggetto (Subject):** ==È l'entità capace di accedere agli oggetti==. Nei sistemi informatici, i soggetti sono generalmente rappresentati dai **processi**: gli utenti non accedono mai direttamente alle risorse, ma lo fanno tramite processi che operano per loro conto, ereditando ed assumendo i medesimi diritti di accesso dell'utente.
- **Diritto di Accesso (Access Right):** ==Rappresenta la modalità specifica con cui un soggetto può interagire con un oggetto==. Sinonimi comunemente usati sono _autorizzazione, permesso_ o _privilegio_. Esempi classici sono le operazioni di _lettura (read), scrittura (write), esecuzione (execute), cancellazione (delete)_ e _creazione (create)_.

## Discretionary Access Control (DAC)

==Il controllo degli accessi discrezionale (DAC) si basa sull'identità del richiedente e su regole di accesso che stabiliscono esplicitamente cosa i richiedenti sono autorizzati o meno a fare==. La caratteristica fondamentale del DAC risiede nel termine "**discrezionale**": un'entità che possiede determinati diritti di accesso su una risorsa può, per propria volontà (discrezione), cedere o abilitare un'altra entità ad accedere a quella stessa risorsa.

### La Matrice di Accesso e le sue Alternative

Il modello teorico generale del DAC è rappresentato dalla **Matrice di Accesso (Access Matrix)**:

- ==Si tratta di una tabella bidimensionale dove i soggetti occupano le righe e gli oggetti occupano le colonne. Ciascuna cella contiene i diritti di accesso specifici di quel soggetto su quel determinato oggetto==.
- Poiché nella realtà la maggior parte delle celle risulta vuota (la matrice è estremamente _sparsa_), non viene quasi mai usata come struttura dati reale nei sistemi operativi, ma rimane un pilastro concettuale.

Per implementare la matrice nella pratica si utilizzano tre alternative:

1. **Access Control List (ACL):** Approccio orientato all'oggetto. Per ogni oggetto, viene mantenuta una lista che specifica i diritti di accesso di tutti i soggetti.
2. **Capability List:** Approccio orientato al soggetto. Per ogni soggetto, viene mantenuto l'elenco dei diritti di accesso che vanta su tutti gli oggetti del sistema.
3. **Authorization Table:** Una tabella piana in cui ogni riga contiene esattamente una relazione singola: un soggetto, un diritto di accesso e una risorsa.

### Regole di Provisioning e Matrice Estesa

Per limitare la totale discrezionalità del modello, si applicano due approcci:

- **Ownership-based (Basato sulla proprietà):** Un soggetto (o un gruppo) viene definito come _proprietario (owner)_ dell'oggetto. Solo il proprietario ha la facoltà di concedere o revocare i permessi (un esempio classico è il file system UNIX).
- **Matrice di Accesso Estesa:** Le colonne della matrice vengono estese per includere anche i soggetti. In questo modo, un soggetto può essere contrassegnato come proprietario o ricevere un diritto con un **copy flag** (che lo autorizza a copiare/trasferire quel diritto ad altri). Inoltre, possono esistere **diritti di controllo (control rights)** di un soggetto su un altro, che permettono al primo di revocare i permessi del secondo.

## Mandatory Access Control (MAC)

==Il controllo degli accessi mandatorio (MAC) adotta una filosofia opposta al DAC: l'accesso viene regolato confrontando le etichette (labels) di sensibilità delle risorse con i permessi (clearances) posseduti dalle entità==.

La natura "**mandatoria**" implica che, anche se un utente ha la clearance per accedere a una risorsa, non può assolutamente (per propria volontà) abilitare un altro utente ad accedervi. Questo modello rigido deriva storicamente dai rigidi requisiti di sicurezza della sicurezza militare.

### Il Modello Bell-LaPadula (BLP)

È il modello formale più celebre del MAC. A ogni soggetto viene assegnato un livello di _security clearance_ e a ogni oggetto una classificazione di sicurezza (es. gerarchia militare: _Top Secret > Secret > Confidential > Restricted > Unclassified_). L'accesso è governato tassativamente da tre proprietà fondamentali:

1. **Simple Security Property (ss-property / "No Read Up"):** ==Un soggetto può leggere un oggetto solo se il livello di quest'ultimo è minore o uguale al proprio livello di clearance==. Non si può leggere materiale più riservato del proprio livello.
2. **Star Property (*-property / "No Write Down"):** ==Un soggetto può scrivere su un oggetto solo se il livello dell'oggetto è uguale o superiore a quello del soggetto==. Questo serve a impedire la fuga di informazioni sensibili verso livelli inferiori di riservatezza.
3. **Discretionary Property (ds-property):** ==Consente una componente discrezionale (i soggetti possono cedere l'accesso a risorse proprie), ma tale concessione è subordinata e deve sempre soddisfare le due regole MAC precedenti==. I soggetti autorizzati non possono in alcun modo aggirare le restrizioni per fornire l'accesso a soggetti non autorizzati.

## Role-Based Access Control (RBAC)

==Il controllo degli accessi basato sui ruoli smette di guardare alle singole identità per concentrarsi sulle funzioni lavorative (ruoli) che gli utenti ricoprono all'interno di un'organizzazione. Le regole stabiliscono quali accessi sono consentiti per ciascun ruolo, e gli utenti ereditano i permessi semplicemente venendo associati a tali ruoli==.

### Caratteristiche di un Sistema RBAC Tipico

- I ruoli riflettono le mansioni aziendali e sono per lo più **statici**.
- I diritti di accesso sono assegnati direttamente ai ruoli (e non agli utenti) e cambiano raramente.
- Gli utenti vengono assegnati ai ruoli secondo una relazione **molti-a-molti** (un utente può avere più ruoli, un ruolo può avere più utenti). L'assegnazione può avvenire in modo statico o dinamico.
- Questo modello è perfetto per implementare il **Principio del Minimo Privilegio (Principle of Least Privilege)**, poiché a ciascun ruolo viene configurato solo il set minimo di permessi strettamente necessario allo svolgimento delle proprie mansioni.

### I Modelli di Riferimento RBAC

La letteratura definisce quattro famiglie standard di modelli di riferimento (da $RBAC_0$ a $RBAC_3$):

1. **$RBAC_0$ (Modello Base):** ==Contiene le funzionalità minime==. Definisce i concetti base: utenti, ruoli, permessi, le relative associazioni (user-role e permission-role) e le sessioni attive.
2. **$RBAC_1$ (Gerarchico):** ==Include tutte le funzionalità di RBAC_0 introducendo le gerarchie di ruoli==, permettendo a un ruolo di livello superiore di ereditare automaticamente i permessi di un ruolo subordinato.
3. **$RBAC_2$ (Vincolato):** ==Include RBAC_0 ma introduce i vincoli (constraints), ovvero restrizioni applicate alla configurazione dei componenti del sistema== (es. impedire che un utente appartenga contemporaneamente a due ruoli in conflitto di interesse).
4. **$RBAC_3$ (Completo):** ==Rappresenta il modello massimale, unendo le funzionalità e i vantaggi di RBAC_0, RBAC_1 e RBAC_2==.

## Attribute-Based Access Control (ABAC)

==L'ABAC rappresenta l'evoluzione più moderna e flessibile: controlla l'accesso valutando in tempo reale gli attributi del soggetto, dell'oggetto (risorsa) e le condizioni ambientali del momento==. Grazie alla sua elevatissima espressività, l'ABAC può essere configurato per emulare e implementare nativamente i modelli MAC, DAC e RBAC. L'unico vero contro è il potenziale **overhead prestazionale**, dovuto alla necessità di calcolare predicati complessi (attributi utente + risorsa) ad ogni singola richiesta di accesso.

### Elementi Chiave e Tipologie di Attributi

Il framework si compone di attributi (predefiniti e assegnati da un'autorità), un modello di policy e un'architettura di enforcement. Gli attributi si dividono in tre categorie macro:

- **Subject attributes (Attributi del Soggetto):** ==Definiscono l'identità e le caratteristiche di chi richiede l'accesso== (es. ID, nome, organizzazione di appartenenza, qualifica lavorativa, ruolo).
- **Object attributes (Attributi dell'Oggetto):** ==Descrivono le proprietà della risorsa== (es. ID del file, titolo, proprietario, data di creazione/modifica, metadati di classificazione). 
- **Environment attributes (Attributi Ambientali):** ==Descrivono il contesto operativo e situazionale in cui avviene la richiesta== (es. data e ora corrente, livello di sicurezza della rete da cui ci si collega).

### Formazione delle Policy e Decisione di Accesso

Le politiche di sicurezza (Policy) definiscono le regole di comportamento aziendale combinando i privilegi dei soggetti e la protezione degli oggetti sotto determinate condizioni ambientali. A livello formale, si utilizza una **notazione funzionale** sia per leggere i valori degli attributi, ad esempio:

$$\text{Role}(s) = \text{"Admin"}, \quad \text{Owner}(o) \leftarrow \text{"Marco"}, \quad \text{Date}(e) = \text{"27/10/2025"}$$

Sia per esprimere le regole di policy vere e proprie, che sono modellate come **funzioni booleane** degli attributi stessi:

$$R_1 : \text{can\_access}(s, o, e) \leftarrow \text{Owner}(o) = s$$

**Il processo decisionale (Access Control Decision):** Quando un soggetto tenta di accedere a una risorsa, il sistema individua e valuta _tutte_ le regole di policy applicabili a quella coppia $(s, o)$. L'accesso viene garantito ed effettivamente concesso **solo ed esclusivamente se tutte le regole applicate restituiscono come valore True**.