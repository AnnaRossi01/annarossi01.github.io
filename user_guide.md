# Comandi — Portfolio

## Attivare/disattivare l'ambiente virtuale

Da fare **ogni volta** che apri un nuovo terminale per lavorare sul sito:

```bash
cd ~/annarossi01.github.io
source venv/bin/activate
```

Il prompt del terminale mostrerà `(venv)` all'inizio quando è attivo. Per uscire dall'ambiente virtuale:

```bash
deactivate
```

Se in futuro dovessi ricreare l'ambiente da zero (es. su un altro computer):

```bash
python3 -m venv venv
source venv/bin/activate
pip install mkdocs-material
```

---

## Anteprima locale del sito

```bash
mkdocs serve
```

Apre il sito su `http://127.0.0.1:8000`, con aggiornamento automatico ogni volta che salvi un file. `Ctrl+C` per fermarlo.

Se la porta 8000 risulta già occupata (es. un altro `mkdocs serve` rimasto acceso):

```bash
lsof -ti:8000 | xargs kill -9
```

oppure usa una porta diversa senza toccare l'altro processo:

```bash
mkdocs serve -a 127.0.0.1:8001
```

---

## Struttura del progetto (come l'abbiamo creata)

```bash
mkdocs new .
mkdir -p docs/projects
touch docs/skills.md docs/experience.md docs/cv.md
touch docs/projects/ngs-variant-calling.md
touch docs/projects/rnaseq-analysis.md
touch docs/projects/data-pipeline.md
touch docs/projects/ml-genomics.md
touch docs/projects/powerbi-dashboard.md
mkdir -p .github/workflows
```

(Il file `.github/workflows/deploy.yml` e `mkdocs.yml` li abbiamo scritti direttamente in VSCode, non da terminale.)

---

## Ciclo di lavoro standard: modifica → pubblica

Questo è il comando che userai più spesso, ogni volta che aggiorni una pagina:

```bash
git add .
git commit -m "Descrizione breve di cosa hai cambiato"
git push
```

Dopo il push, GitHub Actions builda e pubblica il sito automaticamente in circa 30-60 secondi — nessun altro comando necessario.

---

## Comandi di verifica/diagnosi (usali se qualcosa non torna)

```bash
git status                    # cosa è modificato/da committare
pwd                            # in che cartella sei
ls -la                          # elenco file, incluse cartelle nascoste (.github, .gitignore)
git log --oneline --all -- .github/workflows/deploy.yml   # verifica se un file è mai stato committato
git show --stat HEAD            # file inclusi nell'ultimo commit
```

---

## Autenticazione GitHub (token)

GitHub non accetta più la password normale da terminale — serve un Personal Access Token, generato da [github.com/settings/tokens](https://github.com/settings/tokens) → *Generate new token (classic)*, con scope **`repo`** e **`workflow`** entrambi selezionati.

Per non doverlo reinserire a ogni push:

```bash
git config --global credential.helper store
```

**Importante**: il token va incollato *solo* quando il terminale lo richiede esplicitamente come password — mai altrove, e se mai lo incolli per errore in un posto sbagliato, vai subito su github.com/settings/tokens e revocalo (*Delete*), poi generane uno nuovo.

---

## `.gitignore` (contenuto attuale)

```
# Ambiente virtuale Python
venv/

# File compilati Python
__pycache__/
*.pyc

# Output di build di MkDocs
site/

# File di sistema
.DS_Store

# Impostazioni locali VSCode (opzionale, personale)
.vscode/
```

---

## `.github/workflows/deploy.yml` (contenuto attuale)

```yaml
name: Deploy MkDocs site

on:
  push:
    branches:
      - main

permissions:
  contents: write

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.x"
      - run: pip install mkdocs-material
      - run: mkdocs gh-deploy --force
```

---

## Note per il futuro

- Impostazione GitHub Pages: **Settings → Pages → Source: Deploy from a branch → `gh-pages` / `(root)`**
- Se GitHub Actions fallisce per mancanza di permessi di scrittura: **Settings → Actions → General → Workflow permissions → Read and write permissions**
- Sito live: `https://annarossi01.github.io/`