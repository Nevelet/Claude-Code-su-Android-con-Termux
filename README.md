# Claude Code su Android con Termux

Guida per installare e utilizzare **Claude Code su Android** attraverso **Termux** e un ambiente **Ubuntu**. Puoi guardare il mio tutorial qui.

L'idea è semplice:

```text
Android
   │
   └── Termux
         │
         └── Ubuntu (proot-distro)
                │
                └── Claude Code
```

> **Nota:** Claude Code non viene installato direttamente nel sistema Android. Viene eseguito all'interno di un ambiente Ubuntu avviato tramite Termux.

---

## Prerequisiti

Prima di iniziare, assicurati di avere:

- uno smartphone o tablet Android con versione Android 8+;
- **Termux** installato (da scaricare unicamente su [F-Droid](https://f-droid.org/it/packages/com.termux/) ; 
- una connessione Internet;
- un account Anthropic/Claude per effettuare l'autenticazione a Claude Code.

È consigliato utilizzare una versione aggiornata di Termux.

---

# Installazione

## Step 1 — Aggiornare Termux

Per prima cosa aggiorniamo i pacchetti disponibili e quelli già installati:

```bash
apt update && apt upgrade -y
```

### Cosa fa?

- `apt update` aggiorna l'elenco dei pacchetti disponibili;
- `apt upgrade` installa gli aggiornamenti disponibili;
- `-y` conferma automaticamente le richieste.

È consigliabile partire da un ambiente aggiornato prima di procedere con l'installazione.

---

## Step 2 — Installare proot-distro

Installiamo `proot-distro`:

```bash
pkg install proot-distro -y
```

### A cosa serve?

`proot-distro` permette di installare e utilizzare distribuzioni Linux all'interno di Termux.

Nel nostro caso lo utilizzeremo per creare un ambiente Ubuntu sul dispositivo Android.

> Non è necessario effettuare il root dello smartphone.

---

## Step 3 — Installare Ubuntu

Installiamo Ubuntu:

```bash
proot-distro install ubuntu
```

Il comando scaricherà e configurerà l'ambiente Ubuntu all'interno di Termux.

La prima installazione potrebbe richiedere alcuni minuti, a seconda della velocità della connessione.

---

## Step 4 — Entrare in Ubuntu

Una volta completata l'installazione, avviamo Ubuntu:

```bash
proot-distro login ubuntu
```

A questo punto ci troviamo all'interno dell'ambiente Ubuntu.

Da qui in avanti, i comandi relativi a Claude Code verranno eseguiti nell'ambiente Linux appena creato.

---

## Step 5 — Installare Claude Code

Ora possiamo installare Claude Code utilizzando lo script ufficiale:

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

### Cosa fa questo comando?

`curl` scarica lo script di installazione dall'indirizzo indicato e lo passa a `bash` per essere eseguito.

> ⚠️ **Attenzione:** eseguire uno script remoto direttamente con `bash` significa affidarsi al contenuto dello script. Utilizza questo comando solo se proviene dalla fonte ufficiale di Claude/Anthropic e verifica sempre la provenienza dei comandi prima di eseguirli.

---

## Step 6 — Aggiungere Claude Code al PATH

Aggiungiamo la directory di installazione al `PATH`:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

### Perché è necessario?

Il `PATH` indica a Linux in quali cartelle cercare i programmi quando digitiamo un comando nel terminale.

Il comando precedente:

1. aggiunge `~/.local/bin` al `PATH`;
2. salva la modifica nel file `.bashrc`;
3. ricarica immediatamente la configurazione di Bash tramite `source`.

In questo modo il comando `claude` sarà disponibile direttamente dal terminale.

---

## Step 7 — Avviare Claude Code

Finalmente possiamo avviare Claude Code:

```bash
claude
```

Se è il primo avvio, Claude Code potrebbe richiedere di effettuare l'autenticazione con il proprio account.

Una volta completata l'autenticazione, sarà possibile utilizzare Claude Code direttamente dal terminale Android.

---

# Riepilogo

Se vuoi semplicemente copiare tutti i comandi in ordine:

```bash
apt update && apt upgrade -y
```

```bash
pkg install proot-distro -y
```

```bash
proot-distro install ubuntu
```

```bash
proot-distro login ubuntu
```

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

```bash
claude
```

---

# Come funziona

La procedura crea una piccola catena di ambienti:

**Android → Termux → Ubuntu → Claude Code**

Termux fornisce il terminale e l'ambiente necessario per eseguire `proot-distro`.

`proot-distro` permette di creare e avviare Ubuntu senza installare un secondo sistema operativo sul telefono e senza richiedere il root.

Claude Code viene quindi installato ed eseguito all'interno di Ubuntu.

---

# Risoluzione dei problemi

### Il comando `claude` non viene trovato

Se dopo l'installazione compare un errore simile a:

```text
claude: command not found
```

assicurati di aver eseguito:

```bash
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
```

e poi riprova:

```bash
claude
```

---

### Sono uscito da Ubuntu. Come rientro?

Da Termux puoi eseguire nuovamente:

```bash
proot-distro login ubuntu
```

e successivamente:

```bash
claude
```

---

### Devo fare il root del telefono?

No. Questa procedura utilizza `proot-distro` e non richiede il root del dispositivo.

---

## ⚠️ Note importanti

Questa guida utilizza software di terze parti e comandi eseguiti direttamente dal terminale.

Prima di eseguire comandi trovati online, verifica sempre la loro provenienza e assicurati di comprendere cosa fanno.

Le prestazioni di Claude Code su Android possono variare in base al dispositivo e all'ambiente utilizzato.

---

# Avvio rapido tramite script e widget

Se utilizzi spesso Claude Code nello stesso progetto, puoi creare uno script che:

1. avvia Ubuntu;
2. imposta il `PATH` di Claude Code;
3. entra automaticamente nella cartella del progetto;
4. avvia Claude Code.

Lo script verrà inserito direttamente nella cartella `~/.shortcuts`, utilizzata da **Termux:Widget**. In questo modo potrai avviare Claude Code direttamente dalla schermata Home di Android, senza dover aprire manualmente Termux e digitare i comandi.

## 1. Creare la cartella degli shortcut

Da Termux, crea la cartella `~/.shortcuts`:

```bash
mkdir -p ~/.shortcuts
```

## 2. Creare lo script

Crea direttamente lo script all'interno della cartella degli shortcut:

```bash
nano ~/.shortcuts/Claude
```

Inserisci il seguente contenuto:

```bash
#!/data/data/com.termux/files/usr/bin/bash

# Cartella del progetto su Android
PROJECT_PATH="/storage/emulated/0/Download/Syncthing/Obsidian/Second Brain"

# Converti il percorso Android nel percorso visibile da Ubuntu
UBUNTU_PATH="/data/data/com.termux/files/home/storage/shared${PROJECT_PATH#/storage/emulated/0}"

# Entra in Ubuntu, imposta il PATH, raggiunge il progetto e avvia Claude Code
proot-distro login ubuntu -- bash -c 'export PATH="$HOME/.local/bin:$PATH"; cd "'"$UBUNTU_PATH"'" && claude'
```

Salva il file e rendilo eseguibile:

```bash
chmod +x ~/.shortcuts/Claude
```

## 3. Testare lo script

Prima di configurare il widget, puoi verificare che lo script funzioni correttamente direttamente da Termux:

```bash
~/.shortcuts/Claude
```

Se tutto è configurato correttamente, verrà avviato Ubuntu, verrà raggiunta automaticamente la cartella del progetto e partirà Claude Code.

## 4. Installare Termux:Widget

Installa **Termux:Widget** sul dispositivo Android da [qui](https://f-droid.org/it/packages/com.termux.widget/)

Termux:Widget permette di eseguire gli script presenti nella cartella `~/.shortcuts` direttamente dalla schermata Home.

> Assicurati di utilizzare una versione di Termux:Widget compatibile con la tua installazione di Termux.

## 5. Aggiungere il widget alla schermata Home

Dopo aver installato Termux:Widget:

1. tieni premuto su uno spazio vuoto della schermata Home;
2. apri la sezione **Widget**;
3. cerca **Termux:Widget**;
4. aggiungilo alla schermata Home;
5. seleziona lo script **Claude**.

A questo punto verrà visualizzato il collegamento allo script.

Toccandolo, Termux:Widget eseguirà automaticamente lo script.

## Risultato

Con un solo tocco verrà eseguita questa sequenza:

```text
Schermata Home Android
        ↓
Termux:Widget
        ↓
~/.shortcuts/Claude
        ↓
Avvio di Ubuntu
        ↓
Cartella "Second Brain"
        ↓
Claude Code
```

Non sarà quindi necessario aprire manualmente Termux, entrare in Ubuntu, raggiungere la cartella del progetto e digitare `claude`.

## Cambiare progetto

Se vuoi utilizzare lo script con un'altra cartella, modifica semplicemente questa riga:

```bash
PROJECT_PATH="/storage/emulated/0/Download/Syncthing/Obsidian/Second Brain"
```

inserendo il percorso della nuova cartella.

Il resto dello script non deve essere modificato.

> **Nota:** il percorso utilizzato deve essere accessibile da Termux. Se non hai ancora autorizzato Termux ad accedere alla memoria condivisa di Android, esegui una volta:
>
> ```bash
> termux-setup-storage
> ```
>
> e concedi l'autorizzazione richiesta da Android.

---

# Riferimenti

Ho preso qualche info su quest'altro progetto: https://github.com/ferrumclaudepilgrim/claude-code-android che vi consiglio di visitare per maggiori informazioni. 

---

# Licenza

Questa guida è distribuita a scopo informativo.

Puoi adattarla e modificarla liberamente per il tuo progetto, indicando la fonte originale quando opportuno.