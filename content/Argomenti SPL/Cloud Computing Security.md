_Tag:_ #informatica #cybersecurity

---
==Il cloud è un modello che permette l'accesso on-demand a un pool condiviso di risorse configurabili (server, storage, applicazioni) con uno sforzo minimo di gestione==. Caratteristiche Essenziali

- **On-demand self-service**: Accesso alle risorse in autonomia.
- **Broad network access**: Disponibilità su un'ampia rete.
- **Resource pooling**: Utilizzo efficiente di risorse condivise tra più utenti.
- **Rapid elasticity**: Capacità di scalare rapidamente in base alla domanda.
- **Measured service**: Modello di pagamento basato sull'effettivo consumo (Pay-per-use).

## Modelli di implementazione del cloud

Ci sono diversi modelli di **distribuzione (deployment) del Cloud Computing**:

- **Private Cloud:** Un'infrastruttura dedicata a **una sola azienda** (massima sicurezza e controllo).
- **Public Cloud:** Servizi offerti da fornitori esterni (come Google o Amazon) a **molteplici aziende** contemporaneamente.
- **Hybrid Cloud:** L'unione di **Cloud Privato + Cloud Pubblico** (per bilanciare sicurezza e flessibilità).
- **Multi-cloud:** Quando un'azienda usa **più fornitori di Cloud Pubblico** diversi per non dipendere da uno solo.

## Modelli di Servizio e Responsabilità

Il concetto di sicurezza nel cloud ruota attorno al **Shared Responsibility Model** (Modello di Responsabilità Condivisa):

- **IaaS (Infrastructure as a Service)**: ==Il provider gestisce l'hardware fisico; il cliente è responsabile di OS, applicazioni e dati (es. Amazon EC2)==.
- **PaaS (Platform as a Service)**: ==Il provider gestisce anche l'OS; il cliente si occupa solo di applicazioni e dati (es. Azure Web Apps)==.
- **SaaS (Software as a Service)**: ==Il provider gestisce quasi tutto lo stack; il cliente è responsabile solo dei propri dati (es. Microsoft Office 365)==.

![[Screenshot 2026-05-07 at 09.23.48.png]]

### L'esempio di AWS 

AWS è una piattaforma **Cloud**, Invece di comprare server fisici e tenerli in ufficio, venogno affittati da Amazon. Il Modello di Prezzo

- **Pay-As-You-Go:** Paghi solo per i minuti o le ore in cui il servizio è attivo. Se spegni tutto, smetti di pagare.
- **Save When You Reserve:** Se sai che userai un server per 1 o 3 anni, puoi "prenotarlo" in anticipo e ottenere sconti enormi (fino al 70%).
- **Free Tier:** AWS offre un "piano gratuito" per i nuovi utenti per testare i servizi senza spendere nulla (entro certi limiti).

Per gestire questi servizi si hanno tre modi:

1. **Console:** Un sito web con bottoni e menu.
2. **CLI (Interfaccia a riga di comando):** Per dare ordini tramite stringhe di testo.
3. **SDK:** Strumenti per permettere ai programmatori di integrare i servizi AWS direttamente dentro il codice delle loro app.

#### I Servizi Principali

AWS offre oltre 200 servizi, ma questi sono i "pilastri" fondamentali:

- **EC2:** Sono veri e propri computer virtuali. Si sceglie il sistema operativo (Windows/Linux) e la potenza. È **IaaS** (Infrastructure as a Service) perché viene gestito quasi tutto dall'utente.
- **Lambda:** Qui non si gestisce il computer. Si carica solo il pezzetto di codice e AWS lo esegue solo quando serve. È **PaaS/Serverless**.
- **S3:** Serve per salvare file, foto, video e backup. È estremamente sicuro e scalabile.
- **EBS:** È come un hard disk esterno che si "attacca" ai server EC2. Serve per far girare il sistema operativo e i programmi.
- **RDS:** Database relazionali classici (come SQL). AWS si occupa della manutenzione, l'utente mette solo i dati.
- **DynamoDB:** Un database NoSQL velocissimo, ideale per applicazioni che devono gestire milioni di utenti contemporaneamente senza rallentamenti.
- **VPC:** È la "recinzione" privata nel cloud. Decide l'utente chi può entrare e uscire dalla sua rete virtuale.
- **Route 53:** Il vigile urbano del web. Gestisce il DNS, ovvero traduce i nomi dei siti (come `www.tuosito.it`) in indirizzi IP che i computer capiscono.

## Sicurezza dell'Infrastruttura e Virtualizzazione

Le **Virtual Machines (VM)** sono il cuore del cloud, simulando computer fisici su un unico hardware tramite un hypervisor.

Best Practice per le VM:

- Aggiornare e patchare regolarmente sistema operativo e app.
- Disabilitare l'accesso tramite password a favore dell'**autenticazione SSH tramite chiavi**.
- Ridurre la superficie di attacco installando solo i pacchetti necessari da fonti fidate.

![[Screenshot 2026-05-07 at 10.37.51.png]]

### Secure Shell (SSH) e Autenticazione

==Il protocollo SSH (porta 22) è fondamentale per l'accesso remoto sicuro. È più sicura delle password perché utilizza una coppia di chiavi crittografiche (pubblica e privata)==.

1. Il server invia una sfida crittografata con la **chiave pubblica** del cliente.
2. Solo la **chiave privata** corrispondente può risolvere la sfida per stabilire la sessione.

![[Screenshot 2026-05-07 at 11.09.32.png]]

### Sicurezza di Rete in AWS

Il controllo del traffico avviene attraverso diversi livelli di isolamento:

| **Strumento**          | **Livello di Operatività** | **Caratteristica**                                                                        |
| ---------------------- | -------------------------- | ----------------------------------------------------------------------------------------- |
| **Security Groups**    | Istanza (EC2)              | **Stateful**: permettono automaticamente il traffico di ritorno.                          |
| **NACL (Network ACL)** | Subnet                     | **Stateless**: ogni pacchetto deve essere autorizzato esplicitamente in entrata e uscita. |
| **VPC**                | Rete Logica                | Segmento isolato dove definire range IP e tabelle di routing.                             |

![[Screenshot 2026-05-07 at 11.25.49.png]]

> [!NOTE] Esempio pratico
> Se si vuole ospitare un sito web su AWS, si crea una **singola istanza EC2**, si installa un server web (come Apache o Nginx) e poi bisogna configurare un **Security Group** per permettere il traffico sulla porta 80 (HTTP). Quell'ambiente isolato e configurato è l'istanza.

#### Security Groups

==I Security Groups sono Firewall virtuali a livello di istanza. Sono stateful (il traffico di ritorno è permesso automaticamente)==.

#### Network Access Control List

==I NACL sono dei Firewall a livello di subnet. Sono stateless (bisogna autorizzare esplicitamente sia l'entrata che l'uscita) e seguono un ordine di priorità basato su numeri==.

#### Virtual Private Cloud e Public IP

==Le VPC sono un segmento isolato dove configurare subnet pubbliche (con Internet Gateway) e private==.

#### Sicurezza dello Storage e dei Dati

La messa in sicurezza delle risorse di storage non è un'operazione statica, ma un insieme di controlli logici e crittografici atti a garantire la [[Obiettivi della sicurezza#Modelli di Sicurezza Dalla Triade CIA all'Esade di Parker|Triade CIA]] (Riservatezza, Integrità e Disponibilità).

- **Crittografia (Encryption):** Implementazione di standard elevati per la protezione dei dati _at rest_ (archiviati) e _in transit_.
- **Controlli di Accesso e Audit Logging:** Monitoraggio sistematico di ogni operazione di lettura/scrittura per garantire la tracciabilità delle azioni.
- **Data Loss Prevention (DLP):** Utilizzo di strumenti automatizzati per l'identificazione e la protezione di informazioni sensibili da fuoriuscite non autorizzate.

#### Data Redundancy

La ridondanza è la misura tecnica necessaria per contrastare i guasti hardware e i disastri naturali, garantendo la **durability** del dato.

- **Ridondanza Gestita (AWS Managed):** Servizi come EFS (Elastic File System) replicano nativamente i dati su più **Availability Zones (AZ)** all'interno della stessa Regione. Ciò garantisce che il guasto di un intero data center non comporti la perdita di informazioni.
- **Ridondanza Gestita dall'Utente:** Per scenari di _Disaster Recovery_ critici, è responsabilità dell'architetto implementare repliche **Cross-Region** (es. snapshot di EC2 o RDS spostati in una regione geografica differente), proteggendo il sistema da eventi catastrofici su scala regionale.

![[Screenshot 2026-05-11 at 09.24.22.png]]

#### Identity and Access Management (IAM)

==Il framework IAM costituisce il piano di controllo della sicurezza cloud. Si basa sull'assegnazione di identità e permessi secondo criteri granulari==.

- **Principio del Minimo Privilegio (POLP):** Ogni entità (utente o servizio) deve disporre esclusivamente delle autorizzazioni minime necessarie per assolvere alle proprie funzioni, limitando il "raggio d'azione" (blast radius) in caso di compromissione.
- **Account Root:** Rappresenta l'identità con privilegi illimitati. La best practice accademica e professionale ne impone l'uso limitato alla sola configurazione iniziale, prediligendo l'uso di utenti IAM standard protetti da **Multi-Factor Authentication (MFA)**.
- **Astrazioni Logiche:**
    - **User:** ==Identità fisica o applicativa==.
    - **Group:** ==Insieme di utenti con permessi comuni==.
    - **Role:** ==Identità temporanea per l'assunzione di privilegi specifici, fondamentale per la sicurezza dei servizi== (es. un'istanza EC2 che accede a un bucket S3).

Le autorizzazioni sono codificate in documenti **JSON (JavaScript Object Notation)** che definiscono logicamente il perimetro di azione.

$$\text{Policy} = \{ \text{Effect, Action, Resource, Condition} \}$$

In una policy standard, l'elemento **Effect** determina l'esito (Allow/Deny), l'**Action** specifica l'operazione API (es. `runInstances`), la **Resource** indica l'oggetto target e la **Condition** introduce variabili di contesto.