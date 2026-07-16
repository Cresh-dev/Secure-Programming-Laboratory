_Tag:_ #informatica #cybersecurity #android

---
I dispositivi mobili si definiscono come sistemi computazionali leggeri e portatili, caratterizzati tuttavia da risorse di calcolo ed energia limitate. Dal punto di vista strutturale presentano un hardware eterogeneo e molteplici sistemi di interfaccia (touchscreen, microfono, sensori), pur dovendo garantire un'estrema affidabilità nelle loro funzioni di base, come la connettività telefonica. In questo scenario si colloca **Android**, un sistema operativo open-source basato sul kernel Linux, progettato specificamente per dispositivi mobili touchscreen.

## Funzionalità Chiave ed Evoluzione della Sicurezza

Le caratteristiche fondamentali di Android includono:

- **Base Linux:** Sfrutta il kernel Linux per la gestione ottimale delle risorse hardware.
- **Multitasking:** Supporta l'esecuzione simultanea di più applicazioni tramite una gestione efficiente delle risorse.
- **Interfaccia Utente (UI) Intuitiva:** Adattabile a diversi formati (smartphone, tablet, TV, smartwatches e automobili).
- **Sicurezza e Virtualizzazione:** Isola le applicazioni per garantire l'integrità del sistema.
- **Ottimizzazione Energetica:** Implementa strategie per estendere la durata della batteria.
- **Ecosistema di Sviluppo:** Fornisce un framework per app native e centralizza la pubblicazione sul Google Play Store.

## Lo Stack Software di Android e la Sicurezza del Sistema Operativo

L'architettura software di Android è organizzata in una struttura a strati sovrapposti, ciascuno con compiti specifici:

### Kernel Linux

Il livello più basso è costituito dal kernel Android, basato sulla versione Linux Long Term Supported (LTS) e arricchito da patch specifiche note come _Android Common Kernels_ (ACKs). Il kernel funge da interfaccia diretta tra l'hardware e lo user space dell'OS. Fornisce pilastri di sicurezza nativi quali:

1. **Modello di permessi basato sull'utente:** Controlla rigorosamente l'accesso alle risorse.
2. **Isolamento dei processi:** Garantisce che le applicazioni vengano eseguite in ambienti separati e protetti.
3. **IPC Sicuro:** Meccanismi estensibili per la comunicazione inter-processo.
4. **Modularità:** Possibilità di rimuovere componenti superflue o potenzialmente insicure.

Essendo Android un sistema multi-utente, il kernel attua un rigoroso **isolamento delle risorse**, impedendo a un'applicazione di compromettere o esaurire lo spazio d'archiviazione, la memoria, la CPU o l'accesso ai dispositivi hardware (GPS, Bluetooth, ecc.) di un'altra.

### Security-Enhanced Linux (SELinux)

Integrato a livello di kernel, SELinux applica politiche di controllo degli accessi a livello di singolo processo, confinando (_sandboxing_) persino i processi con privilegi di root. Ogni richiesta di accesso a una risorsa richiede un'autorizzazione basata sul database delle politiche di sicurezza. Può operare in due modalità:

- **Enforcing (da Android 5.0+):** Le azioni non autorizzate vengono attivamente bloccate e registrate nei log.
- **Permissive:** Le azioni non autorizzate vengono eseguite ma comunque loggate a fini di debug.

### Hardware Abstraction Layer (HAL) e Librerie Native C/C++

L'**HAL** fa da intermediario tra il sistema operativo e i driver specifici dell'hardware (fotocamera, sensori). Standardizzando l'interfaccia di comunicazione, consente ai produttori di distribuire patch di sicurezza e aggiornamenti hardware in modo più rapido.

Subito sopra troviamo le **Librerie Native C/C++** (es. _Bionic_ come libreria C core, _OpenGL ES_, _SQLite_, _WebKit_). Per gli aspetti di sicurezza spiccano librerie dedicate come _BoringSSL_ (per comunicazioni TLS), _libhardware_ (accesso sicuro all'hardware) e _libscrypt_ (hashing sicuro delle password).

#### Gestione della Memoria e Vulnerabilità

==I linguaggi C/C++ sono fondamentali per le performance, ma intrinsecamente insicuri per la gestione manuale della memoria, esponendo il sistema a rischi di _stack buffer overflow_== (sovrascrittura degli indirizzi di ritorno nello stack per dirottare il flusso del programma). Per mitigare queste falle, Bionic e il sistema integrano difese native:

- **ASLR (Address Space Layout Randomization):** ==Rende casuali gli indirizzi di memoria usati dai processi, ostacolando l'esecuzione di exploit predittivi==.
- **Canaries (Canarini di stack):** Valori sentinella inseriti in memoria per rilevare i buffer overflow prima del ritorno della funzione.
- **Controlli sui limiti (Boundary Check):** Sostituzione di funzioni unsafe (come `strcpy`) con varianti sicure (come `strncpy`).
- **Sanitizzatori (Memory & Input Sanitizers):** ==Strumenti a runtime per prevenire corruzioni di memoria e iniezioni di payload malevoli==.

### Android Runtime (ART)

Le applicazioni Android vengono distribuite nel formato device-agnostic `.dex` (bytecode Dalvik). L'**ART** compila porzioni di codice _Just-In-Time_ (JIT) durante l'uso caldo e traduce i file `.dex` in binari nativi `.oat` (_Ahead-Of-Time_ o AOT) quando il dispositivo è inattivo per massimizzare le performance. Lato sicurezza, ART esegue la verifica preventiva dell'integrità del bytecode, previene attivamente vulnerabilità di memoria e applica il sandboxing applicativo.

### Java API Framework

Rappresenta l'alto livello esposto agli sviluppatori per costruire app mediante l'uso di SDK in Java o Kotlin. Questo livello beneficia della _Garbage Collection_ (GC) automatica per la gestione sicura della memoria. Il codice del framework viene eseguito all'interno del processo dell'app, ereditandone i permessi.

## Funzionalità di Sicurezza Specifiche del Sistema Operativo

Android adotta un modello di autenticazione protetto da credenziali utente e chiavi crittografiche hardware. La classe `BiometricPrompt` unifica la gestione di PIN, password e dati biometrici a partire da Android 9. Le chiavi crittografiche sono protette all'interno di Keystore hardware isolati (come il _Trusted Execution Environment_ - TEE o il _Secure Element_ - SE), accessibili tramite l'interfaccia **Keymaster HAL** che isola fisicamente le operazioni sensibili dal resto del sistema operativo "non sicuro".

Per la protezione dei dati a riposo (at rest), Android implementa l'**Encryption**:

- **File-Based Encryption (FBE):** ==Standard da Android 7.0, permette di cifrare file diversi con chiavi indipendenti, abilitando il _Direct Boot_== (funzionamento parziale del telefono prima dello sblocco dello schermo).
- **Full-Disk Encryption (FDE):** ==Metodo obsoleto (fino ad Android 10) che proteggeva l'intero disco con un'unica chiave legata alla password dell'utente, bloccando l'avvio completo del sistema fino all'inserimento delle credenziali==.

### Verified Boot (Avvio Verificato)

Il processo di **Verified boot** garantisce che ad ogni stadio dell'avvio venga eseguito solo codice firmato e fidato, impedendo la persistenza di malware legati alla manomissione del sistema operativo. Include una _Rollback Protection_ basata su storage hardware antimanomissione per bloccare il downgrade a versioni precedenti e vulnerabili di Android. Lo stato del Bootloader definisce il livello di verifica: se _LOCKED_ applica i controlli basati sulla Root of Trust (chiave crittografica integrata nel Secure Element); se _UNLOCKED_ consente modifiche software mostrando un avviso all'utente.

## Anatomia di un'Applicazione Android

Un'applicazione Android è un insieme di componenti software che fungono da punti di ingresso per l'utente o per il sistema. Le applicazioni possono essere scritte tramite l'**SDK** (in Java/Kotlin, eseguite all'interno della macchina virtuale) o tramite il **NDK** (in C/C++, destinate a scenari ad alta intensità di calcolo e dipendenti dall'architettura macchina).

Il pacchetto di distribuzione è il file **APK (Android Package)**, che contiene al suo interno:

- Il codice compilato in formato Dalvik (bytecode).
- Le librerie native (compilate per NDK).
- I metadati e le firme crittografiche.
- Le risorse non compilate (immagini, layout XML, audio).
- L'**Android Manifest (`AndroidManifest.xml`)**, un documento vitale che descrive l'app all'OS, elencando componenti, permessi richiesti, funzionalità hardware necessarie e filtri d'intento.

### I Componenti Principali e lo Sviluppo Event-Driven

Lo sviluppo in Android è regolato da eventi (interazioni utente, modifiche di sistema o messaggi personalizzati) gestiti da _Listeners_ ed _Event Handlers_ (es. `onCreate()`, `onClick()`). I mattoni fondamentali di un'app sono:

1. **Activity:** ==Rappresenta una singola schermata con cui l'utente interagisce. La cronologia delle Activity è gestita in uno stack LIFO== (_Last-In-First-Out_). Seguono un ciclo di vita stringente controllato da callback di sistema (dall'inizializzazione con `onCreate()` all'eventuale distruzione tramite `onDestroy()`).
2. **Fragments:** Porzioni riutilizzabili di interfaccia utente inserite all'interno delle Activity, dotate di un ciclo di vita strettamente interconnesso a quest'ultime.
3. **Service:** Componenti per l'esecuzione di operazioni in background a lungo termine, prive di interfaccia grafica (possono essere _Started_ o _Bound_).
4. **Broadcast Receivers:** Componenti deputati a ricevere e rispondere ai messaggi di notifica o di stato inviati a livello globale dal sistema o da altre app.
5. **Content Providers:** Gestiscono l'accesso a set di dati strutturati (es. database SQLite interni), offrendo un'interfaccia CRUD standardizzata ad altre applicazioni, regolata tramite Uniform Resource Identifier (URI) e specifici permessi di lettura/scrittura.
6. **Intents:** ==Messaggi formattati scambiati tra componenti per richiedere un'azione. Si dividono in _Espliciti_ (indirizzati a un componente preciso) ed _Impliciti_ (richiedono un'azione generica delegando all'OS l'individuazione dell'app corretta). Possono trasportare dati aggiuntivi organizzati in coppie chiave-valore==.

## Metodi di Protezione e Sviluppo Sicuro delle Applicazioni

Il primo livello di sicurezza poggia sulla separazione degli ambienti d'esecuzione guidata dal kernel Linux. A ciascuna applicazione installata viene assegnato un **Unique User Identifier (UID)** univoco e un processo dedicato. Di default, le app operano in un regime di visibilità estremamente limitato sul resto delle risorse e del file system. Qualora un'applicazione necessiti di interagire con l'esterno o con dati protetti, deve richiederlo esplicitamente tramite il sistema dei permessi.

### Modelli di Archiviazione (Storage)

I dati dell'applicazione possono essere memorizzati in diversi modi:

- **Database SQLite e Libreria Room:** Per dati strutturati (Room agisce come layer astratto di semplificazione basato su annotazioni DAO/Entity).
- **SharedPreferences:** Per piccole quantità di dati salvate come coppie chiave-valore semplici.
- **Internal Storage:** Spazio privato dell'app, inaccessibile ad altri programmi e cancellato alla rimozione dell'app.
- **External Storage:** Spazio pubblico condiviso che richiede permessi di runtime.
- **Scoped Storage (da Android 10+):** Isola l'accesso ai file riducendo la necessità di ampi permessi di archiviazione globale e confinando le app all'interno delle proprie collezioni multimediali specifiche.

### Gestione dei Permessi (Standard e Custom)

I permessi dichiarati nel manifest possono essere raggruppati in _Permission Groups_ per facilitarne l'approvazione. ==Gli sviluppatori possono definire anche Permessi Personalizzati (Custom Permissions) allegando ai propri componenti un determinato livello di protezione (_Protection Level_)==:

- **Normal:** ==Basso rischio, concessi automaticamente dal sistema==.
- **Dangerous:** ==Alto rischio (es. accesso a fotocamera, contatti), richiedono il consenso esplicito e dinamico dell'utente==.
- **Signature:** ==Il permesso viene concesso solo se l'app richiedente è firmata con il medesimo certificato crittografico dell'app che lo ha emesso==.
- **SignatureOrSystem:** ==Estende i permessi _Signature_ anche alle app preinstallate nell'immagine di sistema==.

### Firma del Codice (Code Signing)

Ogni applicazione deve essere firmata digitalmente con una chiave privata per attestarne l'autenticità e assicurarne l'integrità (garantendo che il codice non sia stato alterato o manomesso dopo il rilascio). Gli sviluppatori possono firmare autonomamente i propri APK utilizzando strumenti a riga di comando come `apksigner` o direttamente tramite l'IDE Android Studio.