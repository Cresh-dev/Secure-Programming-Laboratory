_Tag:_ #informatica #cybersecurity

---
Il **DevOps** è un insieme di pratiche e filosofie culturali nate per accorciare il ciclo di vita dello sviluppo software, garantendo rilasci frequenti e affidabili in linea con gli obiettivi di business. Si basa sulle cosiddette **"Sette C"**:

- **Continuous Development, Continuous Integration, Continuous Testing, Continuous Deployment, Continuous Feedback, Continuous Monitoring e Continuous Operations.**

![[Pasted image 20260616160809.png]]

Il **DevSecOps** estende questo modello integrando la **sicurezza in ogni fase** del ciclo di vita. L'obiettivo è abbattere le barriere tra team di sviluppo, operazioni e sicurezza per garantire consegne veloci ma sicure.

![[Pasted image 20260616160832.png]]

### Componenti Core del DevSecOps

- **Automated Security Testing:** Test regolari e automatici per il rilevamento precoce delle vulnerabilità.
- **Security as Code:** Integrazione delle misure di sicurezza direttamente nelle pipeline CI/CD.
- **Shift-left:** Anticipare la sicurezza alle fasi di pianificazione e codifica.
- **Shift-right:** Estendere la sicurezza al post-deployment (monitoraggio e incident response).

## Integrazioni di Sicurezza per Fasi

| **Fase**    | **Obiettivo**                    | **Integrazioni di Sicurezza (Esempi)**                                                                          |
| ----------- | -------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| **Plan**    | Definire scopi e obiettivi.      | Threat modeling e analisi dei rischi.                                                                           |
| **Code**    | Sviluppo iterativo veloce.       | Secure coding standards e pre-commit hooks per scansione segreti.                                               |
| **Build**   | Creazione dell'eseguibile.       | **SAST** (Static Analysis) e **SCA** (Software Composition Analysis) per dipendenze vulnerabili.                |
| **Test**    | Validazione qualità/performance. | **DAST** (==analisi di sicurezza dinamica effettuata su un applicazione in esecuzione==) e Penetration Testing. |
| **Deploy**  | Consegna in produzione.          | Release gates basati su compliance; blue/green o canary deployments.                                            |
| **Monitor** | Feedback e manutenzione.         | Monitoraggio real-time e analisi post-mortem degli incidenti.                                                   |

## Automazione Locale: Git Hooks e Framework pre-commit

==I git hooks sono script automatici che si attivano al verificarsi di specifici eventi in un repository Git, utilissimi per l'automazione dei task==. Si dividono in:

- **Client-side hooks:** ==Eseguiti sul computer locale dello sviluppatore== (es. `pre-commit` prima di finalizzare un commit, `pre-push` prima dell'invio al server, `commit-msg` per controllare la formattazione del messaggio).
- **Server-side hooks:** ==Eseguiti sul server remoto che ospita il codice== (es. `pre-receive` per analizzare e rifiutare i commit non conformi prima di accettarli, `post-receive` per far partire la build o il deploy dopo l'accettazione).

Se un hook esce con uno stato non-zero (errore), il flusso di Git si interrompe (es. il commit viene rifiutato). Di solito sono scritti in Bash. In un ecosistema DevSecOps maturo, i controlli eseguiti localmente (_client-side_) vengono **re-applicati e forzati anche lato server** (es. nel `pre-receive` per bloccare commit contenenti _secrets_ o nel `post-receive` per avviare i linters prima del deploy) per evitare che gli sviluppatori li saltino.

### Il Framework pre-commit

Configurare manualmente i Git hooks nella cartella `.git/hooks` tramite script può essere complesso e difficile da scalare. Per questo si usa il framework multi-linguaggio **`pre-commit`**. I suoi vantaggi principali includono la gestione centralizzata tramite un unico file `pre-commit-config.yaml`, la riusabilità dei controlli tra vari progetti, il controllo di versione degli hook stessi e la facilità di aggiornamento (`pre-commit autoupdate`). Nel file di configurazione YAML si definiscono i repository ufficiali da cui scaricare i plugin di controllo. Il documento mostra esempi pratici di hook cruciali per la sicurezza:

- `check-added-large-files` e `check-yaml` (controlli sintattici e di struttura).
- `ruff-check` e `ruff-format` (analisi statica e formattazione per Python).
- `gitleaks`: Fondamentale in DevSecOps per rilevare la fuga (_leakage_) di password, chiavi SSH o chiavi API direttamente nel codice prima che vengano committate.

### GitHub Actions

Rappresenta la piattaforma cloud di CI/CD integrata in GitHub per automatizzare i flussi di lavoro (_workflows_). I concetti chiave sono:

- **Workflow:** Procedure automatizzate composte da uno o più **Job** (che girano in parallelo o sequenza). Ogni Job contiene dei **Step** (passi sequenziali che eseguono script o "Actions" predefinite).
- **Event:** L'evento scatenante (push, pull request, un trigger temporale pianificato o manuale).
- **Runner:** Il server (es. macchine Ubuntu, Windows o macOS) su cui gira effettivamente il flusso.
- **Funzionalità avanzate:** Gestione dei segreti (_Secrets management_) per iniettare variabili d'ambiente protette, _Matrix builds_ (per testare l'applicazione contemporaneamente su più OS e versioni di linguaggi) e sistemi di caching per velocizzare le esecuzioni successive.