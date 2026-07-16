_Tag:_ #informatica #cybersecurity

---
Il percorso verso la comprensione della sicurezza inizia con la distinzione tra concetti chiave:

- **Minaccia (Threat):** Qualsiasi evento o situazione potenzialmente dannosa per i beni o le operazioni di un'organizzazione.    
- **Rischio (Risk):** La misura dell'esposizione a una minaccia, calcolata in base all'impatto e alla probabilità che l'evento si verifichi.
- **Threat agent**: Individuo, gruppo, organizzazione o governo con l'intenzione di condurre attività dannose.
- **Attacco**: Una attività malevola che ha lo scopo di collezionare, interrompere, negare, degradando o distruggendo risorse o dati del sistema informativo.
- **Vulnerabilità:** Una debolezza intrinseca nel sistema o nelle procedure che può essere sfruttata da un avversario.
- **Contromisura:** Metodi o dispositivi volti a ridurre l'efficacia degli attacchi o a prevenire accessi non autorizzati.

## Modelli di Sicurezza: Dalla Triade CIA all'Esade di Parker

Il pilastro della sicurezza è la **Triade CIA**, che definisce tre obiettivi essenziali:

1. **Riservatezza (Confidentiality):** ==Protezione delle informazioni sensibili da accessi non autorizzati==.
2. **Integrità (Integrity):** ==Garanzia che i dati rimangano inalterati se non da utenti autorizzati==.
3. **Disponibilità (Availability):** ==Certezza che le risorse siano accessibili agli utenti autorizzati quando necessario==.

![[{1E03FC2D-9CED-4A90-82C2-83A90E3CBD50}.png]]

### Parkerian Hexad

Per una visione più completa, il modello evolve nel cosiddetto **Parkerian Hexad**, che aggiunge tre ulteriori dimensioni:

- **Possesso o Controllo:** ==La gestione fisica o logica del sistema, distinta dalla riservatezza==.
- **Autenticità:** ==La proprietà di un dato o di un'entità di essere genuina e verificabile==.
- **Utilità:** ==La capacità dei dati di essere effettivamente usati== (es. un dato criptato senza chiave perde utilità).

### CIA$^3$

Passiamo ora dalla classica Triade CIA al concetto di **CIA³**, aggiungendo due pilastri fondamentali per una protezione completa: la **Responsabilità** (Accountability) e l'**Assicurazione** (Assurance).

- **Responsabilità (Accountability)**: ==Questo principio stabilisce che le azioni compiute all'interno di un sistema devono essere riconducibili in modo univoco all'entità (utente o processo) che le ha eseguite. Poiché nessun sistema può essere considerato sicuro al 100%, è indispensabile poter risalire alla parte responsabile in caso di violazione della sicurezza. Si ottiene solitamente attraverso sistemi di log e audit che registrano chi ha fatto cosa e quando==.
- **Assicurazione (Assurance)**: ==L'assicurazione rappresenta il grado di fiducia nel fatto che le misure di sicurezza implementate funzionino correttamente. Non basta implementare riservatezza, integrità e disponibilità; bisogna avere la certezza che tali controlli siano stati configurati bene e operino come previsto. Fornisce la garanzia che i meccanismi di difesa siano in grado di prevenire le violazioni e di operare correttamente sia in condizioni normali che in presenza di eventi avversi==.

> [!NOTE] Esempio dell'Automated Teller Machine (ATM)
> - **Riservatezza (Confidentiality):** L'obiettivo è proteggere i dati sensibili, come i dettagli del conto bancario e i codici PIN, impedendo che soggetti non autorizzati possano visualizzarli o accedervi.
> - **Integrità (Integrity):** Garantisce che i dati delle transazioni (ad esempio, l'importo prelevato) non vengano alterati o modificati durante il processo di elaborazione tra l'ATM e la banca.
> - **Disponibilità (Availability):** Assicura che il servizio ATM sia operativo e accessibile agli utenti in qualsiasi momento, idealmente 24 ore su 24, 7 giorni su 7.
> - **Autenticità (Authenticity):** Serve a verificare che l'utente sia effettivamente chi dichiara di essere; questo avviene solitamente attraverso l'uso combinato della carta fisica e del PIN.
> - **Responsabilità (Accountability):** Consiste nel registrare (logging) tutte le transazioni eseguite. Questo permette di tracciare ogni attività risalendo al singolo utente, facilitando audit di sicurezza e la risoluzione di eventuali controversie.


## Analisi di Incidenti Storici

|**Incidente**|**Impatto Principale**|**Analisi CIA**|
|---|---|---|
|**WannaCry (2017)**|Ransomware globale su sistemi Windows.|Colpisce duramente **Integrità** e **Disponibilità**; Riservatezza preservata.|
|**Ashley Madison (2015)**|Data breach di un sito di incontri.|Violazione critica della **Riservatezza** (esposizione dati personali).|
|**Stuxnet (2010)**|Cyber-arma contro infrastrutture nucleari iraniane.|Impatto massimo sull'**Integrità** (distruzione fisica di macchinari).|
|**Equifax (2017)**|Furto di dati finanziari di 147 milioni di persone.|Grave compromissione della **Riservatezza**.|
|**XZ Utils (2023)**|Backdoor inserita nella catena di fornitura software.|Una **minaccia** totale per tutti i pilastri, sebbene l'impatto reale sia stato limitato.|
|**Mirai Botnet (2016)**|Attacco DDoS tramite dispositivi IoT.|Compromissione della **Disponibilità** di servizi online globali.|