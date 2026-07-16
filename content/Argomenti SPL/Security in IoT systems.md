_Tag:_ #informatica #cybersecurity

---
Il paradigma tecnologico contemporaneo è caratterizzato da una progressiva interconnessione tra il mondo fisico e quello digitale. Questa evoluzione introduce complessità rilevanti nella protezione delle informazioni, in particolare all'interno dell'Internet of Things (IoT) e nei contesti di convergenza tra Information Technology (IT) ed Operational Technology (OT).

## La Sicurezza nell'Internet of Things: Il Contesto OWASP

L'**OWASP IoT Project** nasce con l'obiettivo di supportare produttori, sviluppatori e utenti finali nella comprensione delle problematiche di sicurezza native del mondo IoT. L'eterogeneità dei dispositivi e la rapidità con cui il mercato evolve rendono complessa l'applicazione delle metodologie di sicurezza tradizionali. I dispositivi IoT sono spesso dislocati in ambienti privi di perimetri di sicurezza fisici, rimanendo esposti ad accessi diretti da parte di potenziali attaccanti (si pensi alle smart home o alle connected cars).

### OWASP IoT Top 10

Per mappare le principali vulnerabilità riscontrate durante la progettazione e il deployment di sistemi IoT, l'OWASP ha redatto una classificazione dei dieci vettori di rischio più critici:

1. **Credenziali deboli o hardcoded:** L'uso di password facilmente vulnerabili tramite brute-force o preimpostate nel firmware che fungono da vere e proprie backdoor.
2. **Servizi di rete insicuri:** Esposizione verso Internet di servizi non necessari sul dispositivo, che compromettono la riservatezza e l'integrità del sistema.
3. **Interfacce di ecosistema insicure:** Vulnerabilità nelle API esterne, interfacce web, servizi cloud o applicazioni mobili collegate al dispositivo, spesso prive di filtri di input/output o di meccanismi solidi di autenticazione.
4. **Mancanza di meccanismi di aggiornamento sicuro:** Assenza di validazione del firmware sul dispositivo, mancanza di crittografia durante il transito dei pacchetti di update e assenza di protezioni anti-rollback.
5. **Componenti obsoleti o insicuri:** Utilizzo di librerie software deprecate o personalizzazioni insicure del sistema operativo, incluse le problematiche derivanti da una supply chain hardware/software compromessa.
6. **Protezione della privacy insufficiente:** Gestione impropria o non autorizzata dei dati personali degli utenti memorizzati nel dispositivo o nell'ecosistema.
7. **Trasferimento e memorizzazione dei dati insicuri:** Mancanza di cifratura o di controlli d'accesso per i dati sensibili a riposo (_at rest_), in transito o durante l'elaborazione.
8. **Mancanza di gestione dei dispositivi:** Assenza di supporto di sicurezza post-deployment, inclusi il monitoraggio dei sistemi, l'asset management e la dismissione sicura (_decommissioning_).
9. **Impostazioni di default insicure:** Sistemi distribuiti con configurazioni di fabbrica permissive e senza la possibilità per gli operatori di modificarle per restringerne i permessi.
10. **Mancanza di hardening fisico:** Assenza di contromisure fisiche sull'hardware, che permette ad attaccanti locali di prelevare informazioni sensibili utilizzabili per attacchi remoti o per assumere il controllo diretto del device.

## OWASP IoT Security Testing Guide (ISTG)

Per strutturare i processi di audit e penetrazione, la **IoT Security Testing Guide (ISTG)** fornisce una metodologia formale e flessibile finalizzata a standardizzare i test d'intrusione garantendo la comparabilità dei risultati. L'ISTG stabilisce una terminologia comune per facilitare il dialogo tra produttori, operatori e penetration tester. ==Il framework si articola su tre pilastri: un modello del dispositivo, un modello dell'attaccante e una metodologia di test strutturata==.

### Il Modello del Dispositivo (IoT Device Model)

L'architettura di riferimento mappa il dispositivo isolandone i confini (_Device Boundaries_) determinati dall'involucro fisico (_enclosure_). Le interazioni tra l'interno e l'esterno avvengono unicamente tramite specifiche interfacce. I componenti si dividono in:

- **Elementi Interni:** Unità di elaborazione (CPU), memorie (EEPROM, Flash), firmware installato e relativi servizi di scambio dati o di aggiornamento.
- **Interfacce:** ==Possono essere _Interne_ (M2M non accessibili dall'esterno, come JTAG, UART, SPI), _Fisiche_ (porte USB, Ethernet)==, _Wireless_ (Wi-Fi, Bluetooth, BLE, ZigBee) o _Utente_ (H2M come display touch, microfoni o app web locali).

![[Pasted image 20260616140338.png]]

### Il Modello dell'Attaccante (Attacker Model) e Livelli di Accesso

Il profilo dell'attaccante viene categorizzato mutuando le metriche di vulnerabilità del sistema **CVSS (Common Vulnerability Scoring System)**, analizzando variabili quali l'Attack Vector (Network, Adjacent, Local, Physical), l'Attack Complexity (Low, High), i Privileges Required (None, Low, High) e la User Interaction (None, Passive, Active).

![[Pasted image 20260616140421.png]]

L'ISTG declina queste metriche definendo due livelli d'accesso d'interazione con il dispositivo:

1. **Livelli di Accesso Fisico (Physical Access Levels):**
    - **PA-1 (Remote):** Accesso a distanza arbitraria tramite reti globali (GAN).
    - **PA-2 (Local):** Accesso entro un raggio limitato tramite reti locali (LAN/WLAN).
    - **PA-3 (Non-invasive):** ==Contatto fisico ravvicinato senza la possibilità di aprire l'involucro==.
    - **PA-4 (Invasive):** ==Accesso diretto ai componenti interni rimuovendo l'enclosure del dispositivo (es. permette l'accesso a CPU, memorie e interfacce interne)==.
2. **Livelli di Autorizzazione Digitale (Authorization Access Levels):**
    - **AA-1 (Unauthorized):** Accesso anonimo, tipico di utenti non registrati.
    - **AA-2 (Low-privileged):** Privilegi standard di un utente registrato.
    - **AA-3 (High-privileged):** Privilegi amministrativi o estesi.
    - **AA-4 (Manufacturer-level):** Accesso a livello di root o debug, riservato agli sviluppatori per l'analisi del codice sorgente.

#### Attack Vector (AV)

Questa metrica riflette il **contesto logico e fisico** attraverso il quale un attaccante può raggiungere e sfruttare la vulnerabilità del sistema. In parole semplici: _da dove deve passare l'attaccante?_

Si divide in quattro livelli di gravità decrescente:

- **Network (N) `[Massima criticità]`**: ==Il sistema vulnerabile è direttamente esposto su internet o su una rete pubblica==. L'attaccante può trovarsi a qualsiasi distanza fisica e non ha bisogno di accedere alla rete interna; l'attacco avviene da remoto.
- **Adjacent (A)**: ==L'attacco è limitato a livello di protocollo a una topologia logicamente o fisicamente adiacente==. L'attaccante deve condividere lo stesso dominio di trasmissione o lo stesso raggio d'azione wireless (es. la stessa sottorete IP locale, una connessione Bluetooth, Wi-Fi locale o NFC).
- **Local (L)**: Il sistema non è esposto sulla rete. L'attaccante deve ottenere l'accesso locale al sistema bersaglio. Questo può avvenire tramite l'interazione diretta con console/tastiera oppure da remoto ma sfruttando una sessione già avviata (es. tramite un emulatore di terminale come SSH).
- **Physical (P) `[Minima criticità]`**: L'attacco richiede tassativamente che l'attaccante **tocchi o manipoli fisicamente** l'hardware del dispositivo vulnerabile. Un esempio classico è la rimozione dell'involucro per leggere i dati direttamente dalle memorie o l'inserimento di una chiavetta USB per attacchi DMA (Direct Memory Access).

#### Attack Complexity (AC)

Questa metrica misura la quantità di sforzo, i fattori ambientali o le **condizioni speciali** che devono coesistere affinché l'attacco vada a buon fine. In breve: _quanto deve essere "bravo" o fortunato l'attaccante, e quante barriere deve aggirare per creare un exploit funzionante?_

Si divide in due livelli:

- **Low (L) `[Massima criticità]`**: Non servono condizioni particolari. L'attaccante può aspettarsi un successo ripetibile ogni volta che lancia l'exploit contro il sistema vulnerabile, poiché non ci sono misure di sicurezza integrate da aggirare attivamente.
- **High (H) `[Minima criticità]`**: L'attacco richiede che l'attaccante esegua passaggi complessi o che si verifichino condizioni altamente specifiche e difficili da controllare (es. raccogliere segreti specifici del bersaglio tramite altri attacchi o indovinare configurazioni precise per eludere i meccanismi di protezione attivi).

#### Privileges Required (PR)

==Descrive il livello di credenziali e autorizzazioni digitali che l'attaccante deve già possedere all'interno del sistema prima di poter tentare l'exploit==. Il modo in che modo l'attaccante abbia ottenuto originariamente tali credenziali non influisce su questa metrica.

I livelli sono tre:

- **None (N) `[Massima criticità]`**: ==L'attaccante è completamente non autenticato (_unauthenticated_). Può colpire il sistema dall'esterno senza possedere account==, file di configurazione o permessi di accesso.    
- **Low (L)**: ==L'attaccante ha bisogno di credenziali base. Dispone dei privilegi tipici di un utente standard non amministrativo==, i quali limitano l'accesso solo alle risorse e alle impostazioni di sua proprietà.
- **High (H) `[Minima criticità]`**: ==L'attacco richiede un controllo significativo sul sistema. L'attaccante deve già possedere privilegi di tipo amministrativo== (es. root o amministratore di sistema) che garantiscono il pieno accesso a tutti i file e a tutte le configurazioni del dispositivo.

#### User Interaction (UI)

Questa metrica analizza se, per il completamento con successo dell'attacco, sia necessaria la **partecipazione attiva o passiva di un essere umano** (diverso dall'attaccante stesso). In sintesi: _l'attacco fa tutto da solo o serve che una vittima compia un'azione?_

Si divide in tre casistiche (come dettagliato nello standard CVSS v4.0):

- **None (N) `[Massima criticità]`**: ==Il sistema può essere interamente compromesso senza alcuna interazione da parte di utenti terzi==. L'attaccante lancia l'attacco e il sistema cede in totale autonomia.
- **Passive (P)**: ==Richiede un'interazione limitata e involontaria da parte della vittima con il payload dell'attaccante==. Un esempio è l'utente che naviga semplicemente su un sito web legittimo che è stato precedentemente modificato o compromesso per mostrare contenuti malevoli.
- **Active (A) `[Minima criticità]`**: ==Richiede che la vittima compia un'azione specifica, conscia e mirata sul sistema per consentire l'esecuzione del codice malevolo==. Un esempio classico è l'importazione manuale di un file infetto all'interno del sistema vulnerabile o il click esplicito su un eseguibile.

### Metodologia e Casi di Test (ISTG Test Cases)

La struttura dei casi di test segue un modello gerarchico ad albero radicato nel nodo principale `ISTG`. Da questo si diramano i nodi figli corrispondenti ai singoli componenti hardware/software (es. _Processing Unit, Memory, Firmware, Wireless Interface_). Ogni sotto-albero scende nel dettaglio delle categorie di test applicabili (es. _Authorization, Information Gathering, Cryptography, Input Validation, Business Logic_), fino ad arrivare alle foglie terminali, identificate da un codice univoco utile alla reportistica.

## La Convergenza IT/OT

Un pilastro fondamentale della sicurezza industriale moderna è la comprensione della differenza e della successiva integrazione tra Information Technology (IT) e Operational Technology (OT).

- **Information Technology (IT):** ==Infrastruttura incentrata sulla gestione, memorizzazione e protezione dei dati digitali e della comunicazione aziendale== (focalizzata su PC, laptop e server).
- **Operational Technology (OT):** ==Sistemi hardware e software progettati per monitorare e controllare eventi, processi industriali e dispositivi fisici sul campo== (es. sistemi SCADA, PLC, pompe, motori).

### Differenze Chiave

| Caratteristica            | Information Technology (IT)                               | Operational Technology (OT)                                                  |
| ------------------------- | --------------------------------------------------------- | ---------------------------------------------------------------------------- |
| **Focus principale**      | Integrità del dato (_Data Integrity_)                     | Controllo dei processi ed evitamento dei tempi di fermo (_Downtime_)         |
| **Priorità di Sicurezza** | ==Sicurezza del dato e Riservatezza (_Confidentiality_)== | ==Disponibilità del sistema (_Availability_) e Sicurezza fisica (_Safety_)== |
| **Ambiente Operativo**    | Standardizzato, condizionato                              | Estremo (temperature elevate, vibrazioni, shock meccanici)                   |
| **Patching di Sicurezza** | ==Frequente e automatizzato==                             | ==Limitato e rigidamente pianificato==                                       |

### Tipologie di Convergenza IT/OT

Il processo di integrazione tra questi due mondi prende il nome di **IT/OT Convergence** e si sviluppa su tre livelli:

1. **Convergenza Fisica:** Collegamento diretto dei dispositivi industriali OT alle reti IT, modernizzando i vecchi apparati affinché possano trasmettere dati direttamente ai sistemi aziendali.
2. **Convergenza Software:** Integrazione dei flussi di dati generati dall'OT all'interno dei software IT analitici per ottimizzare l'efficienza decisionale in tempo reale.
3. **Convergenza Organizzativa:** Unificazione dei flussi di lavoro e fusione delle competenze dei dipartimenti IT e OT per garantire una gestione sinergica e una comunicazione fluida.

## Il Quadro Normativo: La Direttiva NIS2

A livello europeo, l'evoluzione della digitalizzazione e l'inasprimento delle minacce cyber hanno spinto la Commissione Europea a emanare la direttiva **NIS2 (Direttiva UE 2022/2555)**, che sostituisce e aggiorna la precedente normativa del 2016.

### Ambito di Applicazione e Timeline

La direttiva ha imposto agli Stati membri il recepimento nelle legislazioni nazionali entro il **17 ottobre 2024**. Essa si rivolge principalmente agli operatori di servizi essenziali (OES) e ai fornitori di servizi digitali (DSP, come Cloud provider e data center). Le organizzazioni vengono suddivise in due macro-classi:

- **Classe 1 (Essenziali):** Settori critici come energia, trasporti, settore bancario, infrastrutture dei mercati finanziari, sanità, acqua potabile, acque reflue, infrastrutture digitali, gestione dei servizi ICT e pubblica amministrazione.
- **Classe 2 (Importanti):** Gestione dei rifiuti, servizi postali, produzione e distribuzione di alimenti e prodotti chimici, manifattura, provider digitali e organizzazioni di ricerca.

### Requisiti Chiave della NIS2

La conformità alla NIS2 richiede l'adozione di un approccio strutturato basato sulla gestione del rischio aziendale:

- **Misure Organizzative e Gestione del Rischio:** Definizione di una strategia di cybersecurity chiara, identificazione degli asset critici e valutazione continua delle minacce.
- **Misure Tecniche:** Implementazione di controlli di sicurezza stringenti, tra cui tecniche di crittografia dei dati e programmi sistematici di formazione del personale.
- **Segnalazione degli Incidenti:** Obbligo tassativo di notificare gli incidenti informatici significativi alle autorità competenti entro un massimo di **24 ore** dall'evento.
- **Information Sharing:** Promozione della condivisione di informazioni relative ad attacchi e minacce tra le varie organizzazioni per innalzare il livello di prontezza collettivo dell'Unione Europea.