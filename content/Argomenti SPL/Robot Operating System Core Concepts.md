_Tag:_ #informatica #cybersecurity #ros

---
==Il Robot Operating System (ROS) non è un vero e proprio sistema operativo, ma un middleware modulare e adattabile che facilita la costruzione di sistemi robotici complessi==. Negli anni, si è evoluto in un ecosistema ricchissimo di algoritmi, utility e applicazioni.

## L'Evoluzione da ROS 1 a ROS 2

**ROS 1** ha rivoluzionato il settore grazie a librerie per la navigazione autonoma, la simulazione e il controllo. Tuttavia, presentava limiti critici:

- **Affidabilità**: Difficoltà nella trasmissione dati su reti instabili (come WiFi o satelliti).
- **Centralizzazione**: La presenza del **ROS Master** rappresentava un "single point of failure" (punto di fallimento singolo); se il Master cadeva, l'intero sistema smetteva di funzionare.
- **Sicurezza**: Mancanza di meccanismi di sicurezza integrati.

==ROS 2 nasce per superare questi limiti, adottando lo standard industriale DDS (Data Distribution Service) per le comunicazioni, rendendolo adatto a casi d'uso critici e professionali==.

## Architettura e Differenze Chiave

Il passaggio alla versione 2 ha introdotto cambiamenti strutturali profondi:

| **Categoria**               | **ROS 1**                                      | **ROS 2**                                                            |
| --------------------------- | ---------------------------------------------- | -------------------------------------------------------------------- |
| **Architettura di Rete**    | Basata su un server centrale (**Master**).     | **Peer-to-peer** con scoperta automatica dei nodi.                   |
| **Protocollo di Trasporto** | Protocollo personalizzato su TCP/UDP.          | Standard **DDS** con strati di astrazione.                           |
| **Supporto OS**             | Principalmente **Linux**.                      | Multi-piattaforma: **Linux, Windows, macOS**.                        |
| **Librerie Client**         | Scritte indipendentemente per ogni linguaggio. | Condividono una libreria core in **C (rcl)** per garantire coerenza. |

## I Concetti Core: Nodi e Comunicazione

Il mattone fondamentale di un sistema ROS è il **Nodo**. Ogni nodo ha un ruolo modulare e specifico (es. gestire una telecamera, controllare i motori o processare dati sensoriali).

![[Screenshot 2026-05-13 at 15.52.52.png]]

### Modalità di Comunicazione

1. **Topic (Argomenti)**: ==Modello di comunicazione asincrona di tipo "chat room". Molti nodi possono pubblicare (Publishers) o ricevere (Subscribers) dati sullo stesso topic contemporaneamente==. È ideale per flussi continui di dati come sensori laser o posizioni.

![[Screenshot 2026-05-13 at 16.28.01.png]]

2. **Service (Servizi)**: ==Modello sincrono "chiamata e risposta" (call-and-response), simile a una procedura remota==. Viene usato per operazioni rapide e deterministiche, come lo spawn di un robot in una simulazione. Esiste un solo server per ogni servizio, ma possono esserci più client.

![[Screenshot 2026-05-13 at 16.29.26.png]]

3. **Action (Azioni)**: ==Pensate per task a lunga durata che richiedono feedback costante o la possibilità di essere interrotti (preemption)==. Un esempio tipico è la pianificazione di un percorso in navigazione. Le azioni combinano internamente servizi (per obiettivi e risultati) e topic (per il feedback).

![[Screenshot 2026-05-13 at 16.28.48.png]]

> [!NOTE] Esempio di uso delle action rispetto ai service
> Uno scenario che si può osservare è la navigazione autonoma di un robot verso un punto dello spazio: l’azione permette di monitorare i metri rimanenti e annullare il movimento se compare un ostacolo imprevisto, laddove un servizio bloccherebbe il nodo client senza controllo in itinere. 

### Segregazione della Rete: ROS_DOMAIN_ID

Attraverso la variabile d'ambiente `ROS_DOMAIN_ID`, ROS 2 permette di isolare logicamente diverse macchine sulla stessa rete. Nodi con ID diversi non comunicheranno tra loro, evitando interferenze in ambienti con più robot.

![[Screenshot 2026-05-13 at 15.56.12.png]]

## API Unificate

A differenza di ROS 1, dove le librerie per i diversi linguaggi venivano scritte da zero in modo indipendente, ROS 2 utilizza un'architettura a strati:

- **RCL (ROS Client Library)**: È il "cuore" del sistema, scritto interamente in **C**. Essendo il C un linguaggio universale, funge da base comune.
- **Wrapper specifici**: Sopra questo cuore in C, vengono costruiti dei "gusci" (wrapper) per i linguaggi di alto livello:
    - **rclcpp**: La libreria per chi programma in C++.
    - **rclpy**: La libreria per chi programma in Python.

ROS 2 introduce la possibilità di scegliere come distribuire i nodi nei processi del sistema operativo, offrendo due approcci principali che sono illustrati di seguito.

### Nodi in processi differenti

È l'approccio standard in cui ogni nodo (o gruppo di nodi) gira come un processo separato.

- **Isolamento dei guasti (Fault Isolation)**: Se un nodo crasha, non trascina con sé gli altri processi, aumentando la robustezza del sistema.
- **Debugging facilitato**: È più semplice monitorare e risolvere problemi su singoli processi indipendenti.

### Nodi nello stesso processo

ROS 2 permette di aggregare più nodi all'interno di un **singolo processo**.

- **Basso overhead**: Si risparmiano risorse di sistema non dovendo gestire molteplici processi.
- **Comunicazione efficiente**: I nodi possono scambiarsi dati molto più velocemente (spesso tramite puntatori alla memoria condivisa) evitando il passaggio dei dati attraverso la rete di sistema (DDS).

## Scrivere un Nodo come Componente

In ROS 2, la best practice è scrivere i nodi come classi che ereditano dalla classe base `Node`. Questo approccio trasforma il codice in un modulo riutilizzabile (un componente).

- **Ereditarietà**: Il componente eredita tutte le funzionalità fondamentali di ROS dalla classe principale.
- **Modularità**: Essendo una classe, può essere importata e utilizzata facilmente da altri script.
- **Funzionalità Interne**: All'interno della classe del componente è possibile creare publisher, subscriber, server e client.

## Deployment del Componente

Una volta scritto il componente, bisogna decidere come caricarlo ed eseguirlo nel sistema. Esistono diverse modalità:

**Tramite API di Servizio di ROS 2**: 

Si possono caricare i componenti dinamicamente all'interno di un processo contenitore già esistente utilizzando:

- **Chiamate ai servizi**: Inviare una richiesta per caricare il componente.
- **Strumenti da riga di comando (CLI)**: Usare comandi specifici nel terminale per il caricamento.
- **File di Launch**: Automatizzare il caricamento dei componenti tramite script di avvio.

**Metodi Alternativi**:

- **Composizione Manuale**: Scrivere un programma "contenitore" personalizzato che istanzia i nodi direttamente nel codice.
- **Esecuzione Standalone**: Far girare il componente come un normale eseguibile indipendente (il metodo classico di ROS 1).

## Parametri e File di Launch

- **Parametri**: Consentono di configurare i nodi a runtime senza dover ricompilare il codice. ROS 2 utilizza un sistema di parametri decentralizzato. Per gestire configurazioni complesse, si usano file **YAML**, più leggibili e strutturati.
- **File di Launch**: In sistemi complessi con decine di nodi, sarebbe impossibile avviarli manualmente uno per uno. I file di launch (scritti in **Python** in ROS 2) automatizzano l'esecuzione, definendo quali programmi avviare, con quali argomenti e come mappare i topic.

