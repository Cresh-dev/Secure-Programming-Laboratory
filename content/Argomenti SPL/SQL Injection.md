_Tag:_ #informatica #cybersecurity #sql

---
==L'Injection è definita come l'atto di indurre un'applicazione a includere comandi non previsti all'interno dei dati inviati a un interprete. Nello specifico, la SQL Injection (SQLi) manipola l'input del "data-plane" per alterare l'esecuzione di comandi SQL predefiniti==. L'attacco avviene tipicamente a causa di una **mancata separazione tra codice e dati**: l'applicazione concatena l'input dell'utente direttamente nelle stringhe di query, permettendo all'attaccante di "rompere" la struttura logica della query stessa.

> [!NOTE] Esempio di SQL Injection
> Un esempio di SQL Injection si verifica quando un utente inserisce `' OR '1'='1' --` nel campo password, rendendo la condizione sempre vera e permettendo l’accesso senza credenziali valide.

## Impatto sulla Sicurezza (Triade RID)

Si possono delineare le conseguenze devastanti di un exploit SQLi riuscito:

- **Riservatezza (Confidentiality):** Accesso non autorizzato a dati sensibili e lettura di file dal file system del DBMS.
- **Integrità (Integrity):** Modifica o eliminazione non autorizzata dei record (INSERT, UPDATE, DELETE).
- **Disponibilità (Availability):** Possibilità di arrestare il DBMS o compromettere il sistema operativo sottostante.
- **Autenticazione/Autorizzazione:** Bypass delle procedure di login e scalata dei privilegi.

## Classificazione delle Tecniche di Attacco

Esistono diverse varianti di SQLi che possono essere osservate attraverso esempi pratici:

- **In-band SQLi (Union-based):** Utilizzo dell'operatore `UNION` per unire i risultati di una query legittima con quelli di una query malevola, permettendo l'esfiltrazione di intere tabelle (es. `information_schema`).
- **Inferential SQLi (Blind SQLi):** ==Quando l'applicazione non restituisce dati direttamente, l'attaccante interroga il database con domande booleane (Vero/Falso) o basate sul tempo (WAITFOR DELAY) per dedurre informazioni.==
- **Logic SQLi:** Manipolazione della logica booleana (es. `' OR '1'='1'`) per rendere la clausola `WHERE` sempre vera e bypassare l'autenticazione.

> [!NOTE] Differenza tra Blind e Standard SQLi
> Nella **SQLi Standard** (In-band), i dati estratti o gli errori del database sono visibili direttamente nella pagina web o nei log della risposta. Nella **Blind SQLi**, nessun dato viene mostrato direttamente a schermo.

## Strategie di Mitigazione e Prevenzione

Il "Root Cause" viene individuato nell'uso di istanze `Statement` dinamiche. La soluzione principale proposta è l'adozione di **Prepared Statements con Bind Variables**:

- **Parametrizzazione:** L'uso di `setString()` garantisce che l'input dell'utente sia trattato esclusivamente come dato e mai come codice eseguibile.
- **Principio del Minimo Privilegio:** Limitare i permessi dell'utente database dell'applicazione.
- **Input Validation:** Implementazione di "Allow List" per validare rigorosamente i dati in ingresso.


> [!NOTE] Esempio dell'uso di setString()
> Quando viene usato il `setString`, il driver del database (ad esempio quello di Java per MySQL o PostgreSQL) prende l'input e lo "impacchetta" in modo che sia trattato **esclusivamente come una stringa letterale**.
>- Se l'utente inserisce `' OR '1'='1' --`, il `setString` aggiunge automaticamente i caratteri di escape (come il backslash `\`) o raddoppia gli apici.
>- Il database riceverà l'istruzione di cercare un libro il cui titolo sia **esattamente** la stringa `' OR '1'='1' --`. Non vedrà mai quegli apici come "comandi" per chiudere la query, perché la struttura della query è già stata decisa e "congelata" nella fase 1.
