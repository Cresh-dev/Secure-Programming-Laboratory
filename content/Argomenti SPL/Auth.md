_Tag:_ #informatica #cybersecurity

---
Nel contesto delle transazioni digitali, è fondamentale distinguere tra due concetti chiave:

- **Identità Digitale:** La rappresentazione univoca di un soggetto (utente o dispositivo) coinvolto in una transazione informatica.
- **Autenticazione Utente Digitale:** Il processo informatico volto a verificare la validità degli autenticatori utilizzati per reclamare un'identità digitale. Questo meccanismo serve a dimostrare che il soggetto ha effettivamente il controllo di tali autenticatori.

==Una volta completata con successo l'autenticazione, il sistema si affida all'identità verificata per applicare le politiche di autorizzazione, determinando se l'individuo dispone dei privilegi necessari per accedere a specifiche risorse di sistema o eseguire transazioni sul database==.

## I Mezzi di Autenticazione e le loro Vulnerabilità

I fattori di autenticazione si dividono in quattro macro-categorie generali, utilizzabili singolarmente o in combinazione:

1. **Segreti (Qualcosa che si conosce):** Password, codici PIN o risposte a domande di sicurezza.
2. **Token (Qualcosa che si possiede):** Badge elettronici, smart card o chiavi fisiche.
3. **Biometria Statica (Qualcosa che si è):** Caratteristiche fisiche immutabili come impronte digitali, tratti del viso o pattern della retina.
4. **Biometria Dinamica (Qualcosa che si fa):** Comportamenti o azioni, tra cui l'impronta vocale, la grafia o il ritmo di digitazione sulla tastiera.

### Problematiche Comuni e Specifiche

Tutti i fattori condividono una vulnerabilità intrinseca: possono essere estorti con l'inganno tramite tecniche di **ingegneria sociale** o tramite coercizione fisica/psicologica. Presentano inoltre criticità specifiche:

- I **segreti** possono essere indovinati o rubati.
- I **token** possono essere smarriti, sottratti o contraffatti.
- I **sistemi biometrici** devono gestire intrinseci tassi di falsi positivi e falsi negativi, affrontando anche problemi di accettazione da parte dell'utente e la possibilità di repliche artificiali dei tratti.

### Autenticazione a Più Fattori (MFA)

L'MFA incrementa la sicurezza applicando il principio della **separazione dei privilegi**. Richiedendo due o più prove di identità appartenenti a categorie diverse, il sistema diventa molto più resiliente ai compromessi. 

> [!NOTE] Esempio di MFA
> Un utente inserisce username e password (qualcosa che conosce). Se corretti, il sistema richiede un codice OTP generato da un'app o da un token fisico (qualcosa che possiede). L'accesso è garantito solo se entrambi i controlli hanno esito positivo.

## Autenticazione Basata su Password e Gestione degli Hash

Il meccanismo tradizionale prevede il confronto tra la password inserita dall'utente (abbinata a un User ID) e quella memorizzata nel sistema. L'User ID serve a determinare l'autorizzazione, verificare i privilegi associati (es. amministratore o guest) e applicare le regole di controllo degli accessi.

### Il Ruolo delle Funzioni di Hash e del Salt

==Le password non devono mai essere salvate in chiaro. Se il database venisse compromesso, gli attaccanti otterrebbero credenziali sensibili riutilizzabili su altri servizi. La soluzione standard consiste nel salvare l'hash della password tramite funzioni crittografiche volutamente lente, ostacolando i tentativi di brute-force==.

Tuttavia, il solo hashing non basta: se due utenti scelgono la stessa password, il loro hash sarà identico, consentendo agli attaccanti di mappare i duplicati tramite dizionari di hash precomputati. Per risolvere questo problema si introduce il **Salt**:

- Il Salt è un valore casuale a lunghezza fissa generato da un RNG (Random Number Generator) durante la registrazione dell'utente.
- Viene concatenato alla password prima di applicare la funzione di hash.
- Anche se due utenti scelgono la stessa identica password, i loro hash memorizzati saranno completamente diversi grazie ai Salt differenti.
- L'introduzione di un Salt di _b_ bit incrementa la complessità di un attacco da dizionario offline di un fattore pari a $2^b$, rendendo inoltre impossibile tracciare l'utilizzo della stessa password su sistemi diversi.

```
[Registrazione Utente]
User + Password -> [RNG genera Salt] -> Hash(Password + Salt) -> Salvato nel DB (ID, Salt, Hash)

[Verifica Autenticazione]
Password Inserita + Salt (letto dal DB) -> Hash(Password Inserita + Salt) == Hash nel DB? -> Esito (V/X)
```

### Tipologie di Attacco e Contromisure

- **Offline Dictionary Attack:** L'attaccante ruba il file degli hash e lo confronta con liste di password comuni. _Contromisure:_ Protezione rigorosa del file di configurazione, sistemi IDS e implementazione del Salt.
- **Specific Account Attack:** Attacco mirato a un singolo account tramite ripetuti tentativi. _Contromisure:_ Blocco dell'account (lockout) dopo un numero prefissato di fallimenti.
- **Popular Password Attack:** Tentativo di inserire password molto comuni su una vasta gamma di ID utenti diversi. _Contromisure:_ Politiche di complessità che vietano password deboli e tracciamento di richieste ripetute tramite IP o cookie.
- **Workstation Hijacking:** Sfruttamento di una sessione di lavoro lasciata incustodita dall'utente loggato. _Contromisure:_ Log-out automatico dopo un periodo di inattività.
- **Sfruttamento del riutilizzo delle password:** Utilizzo di credenziali violate su un servizio minore per accedere ad account critici. _Contromisure:_ Educazione dell'utente e divieto algoritmico di riutilizzo.
- **Electronic Monitoring (Intercettazione):** Eavesdropping delle password trasmesse in rete. _Contromisure:_ Protocolli crittografati (HTTPS) e meccanismi di Challenge-Response.

_Best Practices per le password:_ Lunghezza minima di 15-20 caratteri, uso di caratteri misti (maiuscole, numeri, simboli), adozione obbligatoria di un Password Manager e attivazione dell'MFA.

## Autenticazione Tramite Token Fisici e Dispositivi Mobili

I token di sicurezza sono dispositivi hardware periferici in possesso dell'utente. Si dividono in due classi:

1. **Memory Cards:** ==Possono memorizzare dati ma non hanno capacità computazionale (es. le tessere magnetiche degli hotel). Fungono da chiavi digitali e la logica di autenticazione risiede interamente nel lettore==. Se abbinate a un PIN diventano un sistema a due fattori, ma sono vulnerabili a clonazione (skimming) e attacchi di tipo replay.
2. **Smart Tokens:** ==Sono veri e propri microcomputer dotati di memoria e capacità di elaborazione interna== (es. smart card con chip, token OTP hardware come l'RSA SecurID).

### Interfacce e Protocolli degli Smart Token

Gli smart token comunicano tramite interfacce fisiche a **contatto** o **contactless** (RF/NFC, dove l'alimentazione del chip avviene spesso per induzione dal segnale radio dello stesso lettore). I protocolli di autenticazione supportati includono:

- _Password Statica:_ Il token trasmette sempre la stessa password memorizzata; vulnerabile a replay attack.
- _Password Dinamica:_ Il dispositivo genera periodicamente una nuova password univoca (OTP) basata su un clock interno che deve rimanere perfettamente sincronizzato con il server.
- _Challenge-Response:_ Il sistema centrale invia una stringa casuale (challenge) e il token la elabora (ad esempio firmandola con una chiave privata) per restituire la risposta.

> [!NOTE] Svantaggi dei Token
> Rischio di smarrimento, vulnerabilità al "shoulder surfing" (sbirciare il codice sullo schermo del token) e attacchi di ingegneria sociale volti a persuadere l'utente a comunicare l'OTP.

### Autenticazione tramite Smartphone

Oggi gli smartphone fungono ampiamente da Smart Token. Offrono il vantaggio di essere dispositivi già in possesso dell'utente, riducendo i costi hardware. Possono ricevere OTP via SMS/Voce o ospitare App di Autenticazione dedicate (es. Google Authenticator). È tassativo che lo smartphone sia protetto da un codice di sblocco o biometria per impedire accessi abusivi in caso di furto.

## Funzionamento dei Sistemi Biometrici

I sistemi biometrici verificano l'identità analizzando pattern di caratteristiche fisiche o comportamentali, richiedendo sensori dedicati e risultando tecnicamente più complessi ed costosi.

Il ciclo di vita di un sistema biometrico si articola in tre fasi:

1. **Enrollment (Registrazione):** ==L'utente interagisce con il sensore biometrico fornendo anche l'identità nominale==. Il sensore acquisisce i dati grezzi, l'estrattore di caratteristiche (_Feature Extractor_) genera un profilo numerico standardizzato chiamato **Template** e il sistema salva l'abbinamento (User, Password, Template) nel database biometrico.
2. **Verifica (Verifica 1:1):** ==L'utente dichiara chi risponde di essere (es. inserendo l'username) e si sottopone alla scansione==. Il sistema estrae il template corrente e il _Feature Matcher_ lo confronta **esclusivamente** con il singolo template salvato in precedenza per quell'utente nel database, confermando o smentendo l'identità.
3. **Identificazione (Identificazione 1:N):** ==L'utente interagisce con il sensore senza fornire alcuna credenziale o nome==. Il sistema estrae il template e lo confronta con **tutti** i template presenti nel database. Se trova una corrispondenza valida, restituisce l'ID dell'utente associato.

## Autenticazione Remota e Protocollo Challenge-Response

Mentre l'autenticazione locale avviene su una macchina fisicamente accessibile, l'**autenticazione remota** si consuma su reti geografiche o collegamenti internet, introducendo minacce aggiuntive come l'intercettazione dei dati (eavesdropping) e il reimpiego della sessione (replay attack).

Per mitigare questi rischi si adotta il **Password Challenge-Response Protocol**:

1. Il client avvia la sessione inviando il proprio identificativo utente $U$ al server.
2. Il server risponde inviando un valore casuale usa-e-getta chiamato **nonce** ($r$), insieme a due funzioni hash concordate, $f()$ e $h()$. Se il server gestisce le password con Salt, invia anche il valore del salt dell'utente in questa fase.
3. Il client riceve i parametri, calcola la funzione hash della password inserita combinata con il salt ($h(P')$) e successivamente applica la funzione di combinazione del nonce $f()$, inviando al server il pacchetto finale: $f(r', h(P'))$.
4. Il server esegue lo stesso calcolo localmente utilizzando la password memorizzata nel database per quell'utente: $f(r, h(P(U)))$.
5. Il server confronta i due risultati: se coincidono ($f(r', h(P')) == f(r, h(P(U)))$), l'accesso viene autorizzato.

Questo protocollo garantisce che la vera password (o il suo hash statico) non transiti mai sulla rete, rendendo vani gli attacchi di intercettazione e di replay, poiché il valore del nonce $r$ cambia ad ogni singola sessione.