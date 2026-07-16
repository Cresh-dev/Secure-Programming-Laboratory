_Tag:_ #informatica #cybersecurity #ios

---
L'architettura di sicurezza del sistema operativo mobile iOS (sviluppato da Apple Inc. per iPhone, iPod Touch e declinato in varianti come iPadOS, watchOS e tvOS) eredita la struttura di base UNIX-like da macOS. La filosofia difensiva di Apple si articola su più livelli stratificati, partendo da componenti hardware dedicati fino ad arrivare al controllo granulare delle applicazioni, garantendo riservatezza dei dati e integrità del sistema.

## Sicurezza Hardware e Sensori Biometrici

==La base fondamentale (_root of trust_) dell'intero stack di sicurezza di iOS risiede nell'hardware==. Il sistema non si affida esclusivamente al software, ma integra coprocessori e moduli fisici dedicati alla protezione dei dati sensibili:

- **Secure Enclave:** Rappresenta l'implementazione Apple del modulo TPM (_Trusted Platform Module_). Si tratta di un coprocessore completamente isolato dal processore principale (CPU). Gestisce in autonomia le operazioni crittografiche sensibili e i dati biometrici, impedendo che le chiavi di cifratura vengano esposte alla CPU o al kernel, riducendo drasticamente la superficie d'attacco.
- **AES Hardware Engine:** Un motore crittografico dedicato che esegue la cifratura e la decifratura _inline_ (al volo) dei dati scritti o letti dalla memoria di massa. Operando in modo trasparente rispetto ai livelli software superiori, garantisce un'elevata efficienza senza impattare negativamente sulle prestazioni del dispositivo o sull'autonomia della batteria.

> [!NOTE] Flusso di Lavoro Cooperativo
> Quando un'applicazione deve salvare un file sul disco, la cooperazione avviene in questo modo:
> 
> 1. **Generazione:** Il Secure Enclave genera la _File Key_ per quel file.
> 2. **Passaggio sul Canale Dedicato:** Il Secure Enclave passa questa chiave direttamente all'AES Engine attraverso un **canale fisico dedicato**. Questo dettaglio è cruciale: **la chiave non transita mai nella CPU principale e non viene mai vista dal kernel di iOS**.
> 3. **Cifratura al Volo:** L'AES Engine prende i dati che l'app sta scrivendo e li cifra "al volo" usando la chiave ricevuta, per poi salvarli criptati sulla memoria flash.
> 4. **Protezione della Chiave (Wrapping):** Nel frattempo, per fare in modo che la chiave del file possa essere salvata in sicurezza insieme ai metadati del file stesso, il Secure Enclave la "impacchetta" (_wrapping_), ovvero la cifra a sua volta usando una delle _Class Keys_ (legate al passcode dell'utente).
> 
> In questo modo, l'AES Engine permette al dispositivo di rimanere velocissimo e di non consumare troppa batteria (grazie all'efficienza hardware) , mentre il Secure Enclave garantisce che nessuno possa rubare le chiavi, nemmeno se l'intero kernel del sistema operativo venisse compromesso da un malware.

- **Sensori Biometrici:**
    - **Touch ID:** Sensore di impronte digitali che mappa il flusso delle creste subdermiche. Per ragioni di sicurezza, non memorizza l'immagine o le minuzie dell'impronta (evitando che possa essere ricostruita), ma ne genera un formato vettoriale criptato e salvato esclusivamente all'interno del Secure Enclave.
    - **Face ID:** Sensore di autenticazione facciale che proietta e legge migliaia di punti a infrarossi per creare una mappa di profondità del volto, affiancata da un'immagine 2D sempre a infrarossi. I dati vengono tradotti in una rappresentazione matematica e archiviati nel Secure Enclave.
   

> [!NOTE] Comunicazione tra sensori e secure enclave
> La comunicazione tra i sensori biometrici e il Secure Enclave avviene tramite un canale sicuro e isolato, totalmente inaccessibile al kernel del sistema operativo.

## Secure Boot (Avvio Sicuro) e Gestione dei Fallimenti

==Il processo di avvio di iOS è strutturato come una catena di custodia crittografica (_boot chain of trust_), in cui ogni stadio verifica l'integrità del successivo prima di cedergli il controllo, assicurando che il software non sia stato manomesso da terze parti==.

I componenti e le fasi si dividono in base alla memoria di massa in cui risiedono:

1. **Boot ROM (o SecureROM):** ==Memoria a sola lettura (ROM) non modificabile, scritta in fase di fabbricazione e inaccessibile al kernel. Contiene la chiave della Root CA di Apple e rappresenta la vera _root of trust_. Se questo livello venisse compromesso, l'intera struttura di sicurezza crollerebbe==.
2. **Low Level Bootloader (LLB):** ==Presente solo nei dispositivi legacy (fino a iPhone 6s), esegue routine di configurazione iniziali e verifica il firmware successivo==.
3. **iBoot:** ==Bootloader di alto livello (residente nella partizione protetta BOOT della memoria NAND Flash) che ha il compito di verificare l'integrità del kernel XNU, caricarlo in memoria e avviarlo==.
4. **Kernel:** ==Caricato nella partizione di sistema (FSYS), accessibile da kernel e userspace, che avvia il sistema operativo vero e proprio==.

![[Pasted image 20260620091931.png]]

### Gestione del Boot Failure (DFU vs Recovery)

Se la catena di verifica fallisce, il dispositivo entra in modalità di ripristino per prevenire l'esecuzione di codice malevolo:

- **DFU Mode (Device Firmware Update):** ==Si attiva se il fallimento avviene nelle primissime fasi (tra Boot ROM e iBoot). È una modalità di recupero a basso livello che permette il flash di un nuovo firmware senza passare da iBoot (che potrebbe essere corrotto o compromesso)==.
- **Recovery Mode:** ==Si attiva se il fallimento avviene dopo i Boot, tipicamente durante la verifica del kernel. È implementata da iBoot stesso e permette di ripristinare il kernel e i livelli superiori allo stato di fabbrica==.

## Processo di Aggiornamento Software

Gli aggiornamenti software in iOS sono strettamente legati alla _root of trust_ hardware per impedire l'installazione di firmware non autorizzati o attacchi di _downgrade_ (installazione di versioni precedenti con vulnerabilità note). Il processo si articola in tre fasi:

1. **Autorizzazione:** ==Il dispositivo si collega al server di autorizzazione di Apple inviando una lista di misurazioni crittografiche dei componenti da installare (iBoot, immagine OS, ecc.), un valore casuale anti-replay (_nonce_) e l'identificativo univoco del chip del dispositivo (ECID - _Exclusive Chip Identification_). Il server verifica che la versione richiesta sia ancora firmata e valida==.
2. **Personalizzazione:** ==Il server Apple unisce l'ECID del dispositivo alle misurazioni crittografiche e firma digitalmente il pacchetto==. Ciò rende l'aggiornamento utilizzabile **esclusivamente** da quel rilevante dispositivo, impedendo il riutilizzo dei file su altri terminali.
3. **Installazione:** ==Il dispositivo verifica la firma di Apple, controlla la corrispondenza dell'ECID e procede all'installazione assicurando, tramite i meccanismi di Secure Boot, che venga eseguito solo codice autorizzato==.

![[Pasted image 20260620092009.png]]

## Data Protection

Abilitata automaticamente impostando un codice di sblocco (_passcode_) o l'autenticazione biometrica, la _Data Protection_ garantisce che i dati dell'utente siano sempre cifrati quando il dispositivo è a riposo (_at rest_), consentendo comunque al sistema di rispondere a eventi di background (come chiamate o notifiche) anche a schermo bloccato.

Il funzionamento si basa su una **gerarchia di chiavi crittografiche**:

- **UID (Hardware Key):** ==Fusa direttamente nel Secure Enclave in fabbrica, non può essere letta dal resto del sistema==.
- **Passcode Key:** ==Derivata direttamente dal codice inserito dall'utente==.
- **Filesystem Key:** ==Generata alla prima installazione o alla formattazione. Se cancellata (su richiesta dell'utente), rende tutti i dati istantaneamente illeggibili (cancellazione sicura rapida)==.
- **Class Keys (Chiavi di Classe):** ==Cifrate combinando la UID e la Passcode Key, modulano l'accesso ai file in base allo stato del dispositivo==.
- **File Key:** ==Una chiave casuale generata per _ogni singolo file_ all'atto della creazione==.

![[Pasted image 20260620092054.png]]

### Classi di Protezione

Ogni file appartiene a una classe che ne determina l'accessibilità:

1. **Complete protection:** ==La chiave di classe viene decifrata solo a dispositivo sbloccato e rimossa dalla memoria poco dopo il blocco dello schermo==.
2. **Protected unless open:** ==Consente la creazione di file crittografati anche a dispositivo bloccato (usando una coppia di chiavi pubblica/privata), ma la decifratura della chiave privata avviene solo dopo lo sblocco==.
3. **Protected until first user authentication:** ==La chiave viene decifrata al primo sblocco dopo il riavvio del dispositivo e rimane disponibile fino al successivo riavvio==.
4. **No protection:** ==La chiave è sempre disponibile in memoria all'accensione del dispositivo; i file sono protetti solo dall'UID hardware (permettendo la cancellazione rapida tramite rimozione della _filesystem key_)==.

> [!NOTE] Flusso di Cifratura/Decifratura
> Quando un file viene creato, la File Key viene generata dal Secure Enclave, passata all'AES engine per la cifratura al volo sul modulo Flash e poi "impacchettata" (_wrapped_, ovvero cifrata) con la rispettiva chiave di classe all'interno dei metadati del file (i quali sono a loro volta protetti dalla Filesystem Key). Il processo inverso avviene durante la lettura.

## Integrità del Sistema in Runtime

Per contrastare gli exploit e garantire che il codice fidato mantenga la propria integrità durante l'esecuzione su SoC progettati da Apple, sono implementate diverse barriere hardware e software:

- **Kernel Integrity Protection (KIP):** ==All'avvio, iBoot carica il kernel e le sue estensioni in una regione di memoria fisica protetta dal controller di memoria. Una volta completata l'inizializzazione, il controller vieta qualsiasi operazione di scrittura in tale area e blocca l'hardware KIP per prevenire riconfigurazioni malevole==.
- **System Coprocessor Integrity Protection (SCIP):** ==Replica il meccanismo di KIP per proteggere i firmware dei coprocessori critici (Secure Enclave, processore d'immagine ISP, coprocessore di movimento), isolandoli in regioni di memoria dedicate==.
- **Pointer Authentication Codes (PAC):** ==Protezione hardware contro la corruzione della memoria e attacchi ROP (_Return Oriented Programming_). Utilizza chiavi segrete per firmare i puntatori a istruzioni e dati critici (es. indirizzi di ritorno), inserendo la firma nei bit superiori inutilizzati dei puntatori a 64-bit==. Se la verifica hardware della firma fallisce prima del salto di memoria, l'esecuzione viene immediatamente interrotta.
- **Page Protection Layer (PPL) / SPTM:** ==Impedisce la modifica del codice in userspace dopo la sua verifica crittografica. Anche se un attaccante ottenesse privilegi di lettura/scrittura/esecuzione nel kernel, non potrebbe modificare le tabelle delle pagine protette senza invocare un servizio del PPL==. Da iOS 17, il PPL è sostituito dal **Secure Page Table Monitor (SPTM)**, operante a un livello di privilegio ancora superiore al kernel.

## Sicurezza delle Applicazioni

Il modello di sicurezza per il software applicativo si basa sul presupposto che nessuna app debba poter compromettere il sistema o accedere ai dati di altre applicazioni.

- **Code Signing (Firma del Codice):** ==iOS permette l'installazione di app solo tramite App Store (sottoposte a revisione rigorosa). Qualsiasi codice eseguibile deve essere firmato digitalmente con un certificato emesso da Apple (sia esso per app integrate o di terze parti). Questo estende la catena di fiducia e vieta l'uso di codice auto-modificante o risorse non firmate in runtime==.
- **Sandboxing:** ==Ogni applicazione di terze parti viene eseguita in un ambiente confinato (sandbox) con una directory di storage univoca e isolata==. Le interazioni esterne sono mediate rigorosamente dalle API del sistema operativo; la partizione dell'OS è montata in sola lettura e le API bloccano i tentativi di escalation dei privilegi.
- **Entitlements (Autorizzazioni):** ==Coppie chiave-valore firmate digitalmente all'interno dell'app che definiscono granularmente l'accesso a funzionalità sensibili== (fotocamera, microfono, localizzazione) o operazioni privilegiate di sistema, riducendo la necessità per i demoni di girare con privilegi di _root_.
- **ASLR e KASLR:** ==Tecnica che randomizza la disposizione degli indirizzi di memoria del codice eseguibile e delle librerie a ogni avvio dell'app== (e del kernel con il KASLR), rendendo estremamente difficile per gli attaccanti prevedere la posizione dei bersagli di memoria per attacchi come il _return-to-libc_.
- **Execute Never (XN):** Implementazione del principio $W \wedge X$ (Write XOR Execute). ==Una pagina di memoria marcata come modificabile (scrittura) non può essere contemporaneamente marcata come eseguibile==. Eccezioni controllate (tramite specifici _entitlements_ dinamici) sono concesse a software complessi come il compilatore JIT JavaScript di Safari, che mappa la memoria in modo randomizzato e strettamente regolamentato.