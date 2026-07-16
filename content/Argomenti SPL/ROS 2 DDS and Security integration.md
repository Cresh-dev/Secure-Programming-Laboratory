_Tag:_ #informatica #cybersecurity #ros

---
Nella progettazione della seconda generazione di ROS (ROS 2), la comunità si è posta il problema se migliorare il protocollo di trasporto nativo di ROS 1 o adottare un middleware di terze parti. Dopo aver valutato diverse opzioni (tra cui ZeroMQ, Protocol Buffers e strumenti di zeroconf come Bonjour/Avahi), **DDS (Data Distribution Service)** è emerso come la scelta ideale.

L'adozione di un middleware end-to-end comporta specifici **vantaggi e svantaggi**:

- **Vantaggi:** Riduzione drastica del codice da mantenere, comportamento e specifiche ampiamente documentati (use case, API, documentazione di sistema) e possibilità di revisioni, audit e implementazioni da parte di terzi garantendo l'interoperabilità.
- **Svantaggi:** ROS deve adattarsi a un design preesistente; se il middleware si rivela inflessibile, sono necessari dei workaround architetturali. Inoltre, sposare la filosofia e la cultura del middleware rappresenta un impegno significativo.

Ecco un riassunto universitario completo, strutturato e discorsivo basato sul materiale del laboratorio, pensato per lo studio e la memorizzazione dei concetti chiave relativi all'integrazione di DDS e della sicurezza in ROS 2.

## Caratteristiche Tecniche e Astrazione in ROS 2

Il protocollo di trasporto di riferimento su filo è il **DDSI-RTPS** (DDS Interoperability Real-Time Publish-Subscribe), che offre flessibilità sia per l'integrazione di sistemi ad alto livello sia per applicazioni embedded in tempo reale (grazie a implementazioni specifiche a ridotto footprint di memoria).

- **Trasporto:** Di default utilizza **UDP** per garantire portabilità e controllo transazionale, ma dal rilascio della specifica 1.2 supporta anche **TCP**.
- **QoS (Quality of Service):** Introduce parametri per controllare finemente il comportamento delle comunicazioni.

### Sostituzione dei Meccanismi di ROS 1

- **Discovery:** ==DDS elimina la necessità del _ROS Master_ tipico di ROS 1. Il discovery è distribuito e integrato in DDS; ROS 2 interroga le API DDS per ottenere info su nodi e topic, ma nasconde l'interazione diretta all'utente==.
- **Publish-Subscribe:** Il protocollo DDSI-RTPS sostituisce i vecchi TCPROS e UDPROS di ROS 1.
- **Services e Actions:** Poiché non esiste ancora uno standard definitivo ratificato dall'OMG per le RPC (Remote Procedure Call) su DDS, ROS 2 implementa la semantica Richiesta/Risposta (Request/Reply) basandosi sul pattern Publish-Subscribe. In particolare, vengono usate **due coppie di Publisher/Subscriber**: una coppia gestisce la richiesta su un apposito topic DDS (`Fun Request`) e una seconda gestisce la risposta (`Fun Reply`). Questo è reso affidabile grazie ai parametri di QoS del publish-subscribe di DDS. Le _Actions_ sono trattate come cittadini di prima classe nelle API di ROS 2.

## Estensione di Sicurezza: DDS-Security

Un **dominio DDS** costituisce uno spazio di rete virtuale isolato all'interno del quale coesistono molteplici **Domain Participant**. Ciascun Participant opera tipicamente a livello di **processo del sistema operativo** e può ospitare al suo interno uno o più **nodi ROS 2**. Per garantire che lo scambio di dati tra questi processi avvenga in modo protetto, integro e riservato, viene integrata l'estensione **DDS-Security**, che si occupa di autenticare i partecipanti e cifrare il traffico di rete. ==La specifica DDS-Security estende il DDS base introducendo un modello di sicurezza basato su un'architettura a Plugin SPI (Service Provider Interface). Vengono definiti 5 plugin SPI principali==:

1. **Authentication (Autenticazione):** ==Centralizzazione della verifica dell'identità dei partecipanti al dominio (Domain Participants)==.
2. **Access Control (Controllo degli Accessi):** ==Autorizzazione e limitazione delle capacità operative (es. restrizione a specifici domini o topic)==.
3. **Cryptographic (Crittografia):** ==Gestione di cifratura, decifratura, firme digitali e hashing, a supporto anche dei plugin di autenticazione e controllo accessi==.
4. **Logging:** ==Auditing e tracciamento degli eventi legati alla sicurezza==.
5. **Data Tagging:** ==Aggiunta di tag descrittivi ai campioni di dati==.

### Implementazioni Built-in in ROS 2

- **Authentication Plugin (`DDS:Auth:PKI-DH`):** ==Basato su un'infrastruttura a chiave pubblica (PKI). Richiede una coppia di chiavi (pubblica/privata) e un certificato X.509 per ogni partecipante, firmato da una Certificate Authority (CA) comune per stabilire la fiducia reciproca==.
- **Access Control Plugin (`DDS:Access:Permissions`):** ==Anch'esso basato su PKI, richiede che ogni partecipante possieda due file XML firmati: un file di _Governance_ (regole generali del dominio) e un file di _Permissions_ (permessi specifici del nodo)==.
- **Cryptographic Plugin (`DDS:Crypto:AES-GCM-GMAC`):** ==Fornisce cifratura autenticata tramite l'algoritmo AES in modalità GCM (Galois/Counter Mode), che combina crittografia e autenticazione dell'integrità in un'unica operazione==.

## SROS 2: Integrazione in ROS 2

Di default, le funzionalità di sicurezza in ROS 2 non sono abilitate. Quando vengono attivate, prendono collettivamente il nome di **Secure ROS 2 (SROS 2)**. Il supporto a runtime è gestito all'interno della **RCL (ROS Client Library)**, che si occupa di configurare il middleware per ogni partecipante.

### Gestione dei File di Sicurezza (Enclaves)

==Ogni processo ROS 2 richiede un set specifico di file di sicurezza. Per organizzarli, la RCL supporta una struttura ad albero basata sul concetto di Enclave==. I file devono essere posizionati all'interno di una cartella riservata `enclaves` sotto una radice comune chiamata **keystore**.

> [!NOTE] Precisazione su enclave
> In ROS 2, l'enclave si applica a livello di **processo (eseguibile)**, che corrisponde all'**istanza di Domain Participant** a livello DDS. L'enclave non si applica al singolo _Nodo ROS_ in sé, ma al processo OS che lo ospita. A livello di rete, quel processo istanzia un **Domain Participant DDS**, il quale carica i file crittografici dell'enclave per autenticarsi e cifrare i dati.
> 

Esempio di struttura per l'enclave `/front/camera`:

```
<root> (Keystore)
├── enclaves
│   └── front
│       └── camera
│           ├── cert.pem
│           ├── key.pem
│           └── ...
└── public
```

I file fondamentali contenuti in ogni enclave sono:

1. `identity_ca.cert.pem`: Il certificato della CA fidata per il plugin di Autenticazione (Identity CA).
2. `cert.pem`: Il certificato X.509 specifico di quell'enclave, firmato dalla Identity CA.
3. `key.pem`: La chiave privata dell'enclave.
4. `permissions_ca.cert.pem`: Il certificato della CA fidata per il controllo accessi (Permissions CA).
5. `governance.p7s`: Documento XML firmato dalla Permissions CA che specifica come mettere in sicurezza il dominio DDS.
6. `permissions.p7s`: Documento XML firmato dalla Permissions CA che elenca i permessi specifici di quella istanza di enclave.

Per specificare l'enclave a runtime, ci sono due modalità:

- **Tramite Argomenti CLI:** Si imposta la variabile d'ambiente `ROS_SECURITY_KEYSTORE` sulla root del keystore e si passa l'argomento `--enclave` al comando `ros2 run`.
- **Tramite Override d'Ambiente:** Si utilizza la variabile `ROS_SECURITY_ENCLAVE_OVERRIDE`, che ha la precedenza e forza l'enclave bypassando la selezione da CLI.

Di seguito è presente il codice per impostare il keystore e l'enclave con una successiva esecuzione dell'eseguibile di ros2.

``` bash
ROS_SECURITY_ENABLE = true
ROS_SECURITY_STRATEGY = Enforce
ROS_SECURITY_KEYSTORE = /home/bob/.ros/sros2_keystore
ROS_SECURITY_ENCLAVE_OVERRIDE = /front/camera
ros2 run < package_name > < executable_name >
```

### Modalità di Enforcement (Strategie di Sicurezza)

Cosa succede se si avvia un partecipante senza le credenziali appropriate? La RCL offre due opzioni configurabili tramite la variabile `ROS_SECURITY_STRATEGY`:

- **Permissive Mode (Default):** ==Il sistema cerca i file di sicurezza; se non li trova, avvia il nodo normalmente **senza** funzionalità di sicurezza==.
- **Strict Mode (`Enforce`):** ==Il sistema cerca i file di sicurezza; se non sono presenti o validi, il lancio del nodo fallisce immediatamente==.

## Strumenti SROS 2 CLI

Data la complessità della gestione manuale di PKI e file XML di configurazione, la CLI di SROS 2 fornisce il comando `ros2 security` che automatizza le seguenti operazioni:

- Creazione delle CA di Identità e di Permessi.
- Generazione dell'albero delle directory del keystore.
- Creazione di nuove identità per le enclave (generazione chiavi e firma dei certificati).
- Creazione automatica del file di governance per cifrare tutto il traffico di default.
- Traduzione dei permessi scritti in "termini ROS" familiari in file di permessi DDS a basso livello.
- Scoperta automatica dei permessi richiesti analizzando un sistema ROS in esecuzione.