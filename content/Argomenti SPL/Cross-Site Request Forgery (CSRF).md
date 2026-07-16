_Tag:_ #informatica #cybersecurity #csrf

---
==L'attacco CSRF sfrutta la fiducia che un'applicazione web ripone nel browser di un utente già autenticato. L'obiettivo è forzare l'utente a compiere azioni che cambiano lo stato del server== (state-changing requests), come trasferire fondi, cambiare email o password, o compromettere l'intera applicazione.

## Come funziona l'attacco

L'attacco è possibile perché il protocollo HTTP è **stateless** (senza memoria) e le applicazioni usano i **cookie di sessione** per identificare gli utenti.

![[Screenshot 2026-05-05 at 13.49.18.png]]

1. L'utente effettua il login in un sito legittimo e riceve un cookie di sessione.
2. L'utente, ancora loggato, visita un sito malevolo o clicca su un link creato dall'attaccante.
3. Il browser invia automaticamente il cookie di sessione insieme alla richiesta malevola verso il sito legittimo. 
4. Il server vede un cookie valido e processa l'azione come se fosse stata richiesta volontariamente dall'utente.

## Tipologie di attacco

Ci sono differenti tipologie per eseguire questo attacco, nelle sezioni successive saranno presentate quelle più famose.

### Query Parameter Tampering (GET)

L'attaccante usa link con parametri nella URL (es. `.../transfer?to_user=1&amount=50`). Proprio per la precedente situazione non bisogna usare mai il metodo GET per azioni che modificano lo stato del server. Possiamo schematizzare tutto con un esempio:

Immaginando che il sito della banca, `mia-banca.it`, sia vulnerabile perché permette di trasferire denaro tramite una semplice richiesta GET.

1. **Lo scenario**: Si è loggati nel proprio account bancario per controllare il saldo. Il browser ha salvato il **cookie di sessione**.
2. **L'esca**: Mentre si tiene aperta la scheda della banca, se ne apre un'altra dove è presente un sito di streaming non ufficiale.
3. **L'attacco**: All'interno del sito dell'attaccante, c'è un tag immagine nascosto:
    `<img src="[https://mia-banca.it/transfer?to_user=Hacker&amount=1000](https://mia-banca.it/transfer?to_user=Hacker&amount=1000)" style="display:none;">`
4. **L'esecuzione**: Il browser, cercando di caricare l'immagine, invia la richiesta alla banca. Poiché si è loggati, viene inviato anche il **cookie di sessione**.
5. **Il risultato**: La banca riceve la richiesta, vede che il cookie è valido e trasferisce 1000€ all'hacker senza che la vittima abbia mai cliccato su "Invia" nel sito della banca.

> [!NOTE] Non usare GET per apportare modifiche allo stato del server
> Usando GET, i parametri dell'operazione (`to_user` e `amount`) sono visibili direttamente nell'URL. Questo permette a un utente malintenzionato di camuffare l'attacco in modo semplicissimo. Un'operazione che modifica lo stato del server (come un trasferimento di denaro) **dovrebbe assolutamente essere gestita tramite un metodo POST** e non un GET.

### POST Form Data

L'attaccante crea un modulo HTML nascosto sul proprio sito che, una volta inviato (anche automaticamente), inoltra una richiesta POST al server bersaglio. Possiamo schematizzare tutto con un esempio:

Molte applicazioni moderne usano le richieste POST per le azioni importanti, pensando di essere al sicuro. Ma un attaccante può usare un modulo (form) nascosto.

1. **Lo scenario**: Si è loggati su un social network o su un'applicazione aziendale.
2. **L'esca**: Si visita un sito malevolo creato dall'attaccante.
3. **L'attacco**: Il sito dell'attaccante contiene un codice HTML simile a questo:

``` html
<form action="https://social-network.it/settings/change-password" method="POST" id="csrf-form">
	<input type="hidden" name="new_password" value="PasswordHacker123!">
</form>
<script>
	document.getElementById('csrf-form').submit();
</script>
```

4. **L'esecuzione**: Appena viene caricata la pagina, lo script invia automaticamente il modulo. Anche in questo caso, il browser allega i cookie di sessione alla richiesta POST.
5. **Il risultato**: Il server del social network riceve la richiesta, la valida tramite il cookie e cambia la password con quella scelta dall'hacker.

## Strategie di Difesa

Si controlla la provenienza della richiesta tramite due header:

- **Origin**: Indica da dove proviene la richiesta (usato solo per POST).
- **Referer**: Presente in tutte le richieste, ma può essere omesso o bypassato usando l'attributo `rel="noreferrer"`.

### Cookie SameSite

È una misura di sicurezza del browser che definisce quando i cookie debbano essere inviati in richieste cross-site. Esistono tre livelli:

- **Strict**: ==Il cookie non viene mai inviato in richieste provenienti da altri siti==.
- **Lax**: I==l cookie viene inviato solo per richieste GET derivanti da interazioni dirette dell'utente== (es. clic su un link).
- **None**: ==I cookie vengono inviati sempre==.

![[Screenshot 2026-05-06 at 08.41.26.png]]

==Due URL hanno la stessa origine se e solo se coincidono esattamente tre elementi==:

1. ==Protocollo== (es. `https://`)
2. ==Host / Dominio completo== (es. `app.poliba.it` dev'essere identico, i sottodomini contano come domini diversi)
3. ==Porta== (es. `:443` per HTTPS di default, `:8080`, ecc.)

==Se anche uno solo di questi tre elementi cambia, l'origine è diversa== (Cross-Origin). Il concetto di "Sito" è più flessibile dell'Origine. Guarda il **dominio registrabile** (chiamato anche _eTLD+1_), ovvero il suffisso pubblico (come `.it`, `.com`, `.co.uk`) più la parola immediatamente precedente. Se il dominio registrabile è lo stesso, è **Same-Site** (anche se i sottodomini o le porte cambiano).

### CSRF Tokens

È la protezione più potente. Funziona così:

1. ==Il server genera un token unico per la sessione o la richiesta e lo invia al client==.
2. ==Ogni richiesta successiva (moduli o AJAX) deve includere questo token==.
3. ==Il server valida il token: se manca o è errato, la richiesta viene bloccata==. Poiché un attaccante esterno non può conoscere il token segreto dell'utente, l'attacco fallisce.

