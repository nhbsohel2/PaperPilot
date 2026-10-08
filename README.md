# PaperPilot

Small local AI helpers for your paper library. Drop a PDF into one folder. PaperPilot does the rest:

1. **Metadata agent** reads the title, authors, year and venue.
2. **Sorter** picks the right topic folder and gives the file a clean name, like
   `LLMs/2024 - Schmidt et al - Better Retrieval for Language Models.pdf`.
3. **Screener** writes a short note: summary, method, results, limits, and a score from 1 to 10 for how useful the paper is for *your* research. It also says **READ**, **SKIM** or **SKIP**, and which sections are worth your time.
4. **Library** adds the paper to a citation file (`library.bib`) and to a local search index. You can then ask questions across all your papers and get answers with citations and page numbers.

**Everything runs on your computer.** The only connection is to Ollama on your own machine (`localhost`). No paper text leaves your computer. You only need the internet once, to install things.

**Cost:** free. Works on Windows, Mac and Linux.

---

## What you get

```
Papers/
├── _Inbox/               ← drop new PDFs here
│   ├── _failed/          ← PDFs that could not be read (with a reason)
│   └── _duplicates/      ← papers you already have
├── LLMs/                 ← your topic folders
├── Computer Vision/
├── ...
├── _Notes/
│   ├── schmidt2024better.md     ← one note per paper
│   ├── _Reading list.md         ← all papers, sorted by READ / SKIM / SKIP
│   └── _Answers/                ← saved answers from "ask"
└── library.bib           ← all citations, always up to date
```

---

## Install (one time, about 15 minutes)

### Step 1: Install Python
- Download Python 3.10 or newer from https://www.python.org/downloads/
- **Windows:** in the installer, tick **"Add python.exe to PATH"**.
- Mac and Linux usually have Python already. Check with `python3 --version`.

### Step 2: Install Ollama (the local AI)
- Download from https://ollama.com and install it.
- Open a terminal (Windows: "PowerShell") and download the two models:

```
ollama pull qwen2.5:7b
ollama pull nomic-embed-text
```

> **Slow laptop or less than 16 GB RAM?** Use `ollama pull qwen2.5:3b` instead, and write
> `chat_model: qwen2.5:3b` in `config.yaml`. Any other Ollama model works too.

### Step 3: Set up PaperPilot
Unzip the folder, then open a terminal **inside** the `paperpilot` folder.

**Windows (PowerShell):**
```
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python run.py init
```

**Mac / Linux:**
```
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python run.py init
```

### Step 4: Change your settings
Open `config.yaml` in any text editor. Change these parts:

- **Folders** — by default everything is in `Papers` in your home folder.
- **`my_research`** — write what you work on, in your own words. The screener uses this to score papers. This is the most important setting.
- **`topics`** — your folder system. Already have folders? Use `folder:` to point to them, for example:
  ```yaml
  - name: LLMs
    description: Large language models, RAG, agents
    folder: 02_Literature/LLMs
  ```

### Step 5: Check that everything works
```
python run.py doctor
```
It should say `All good`.

---

## Daily use

**Start the watcher** (it keeps running and handles new PDFs on its own):
- Windows: double-click `start_watch.bat`
- Mac: double-click `start_watch.command`
- Linux: run `./start_watch.sh`

Then just save or drag PDFs into `Papers/_Inbox`. Each paper takes from a few seconds (good GPU) to about a minute (normal laptop).

**Other commands** (run them in the terminal, with `.venv` active):

| Command | What it does |
|---|---|
| `python run.py process` | Handle everything in the inbox once, then stop |
| `python run.py import` | Add PDFs you **already have** in your library. It does **not** move or rename them |
| `python run.py import "D:/Old papers"` | Same, for another folder |
| `python run.py list` | Show all papers with score and verdict |
| `python run.py list --verdict read` | Show only the must-reads |
| `python run.py ask "How does RAG reduce hallucinations?"` | Get an answer from your papers, with `[citekey, p. N]` citations |
| `python run.py ask "..." --save` | Same, and save the answer as a note |
| `python run.py ask "..." --topic LLMs` | Only use papers from one topic |
| `python run.py search contrastive loss` | Show the passages that match, no AI answer |
| `python run.py cleanup` | You deleted a PDF? This removes it from the index and `library.bib` |
| `python run.py doctor` | Check settings and the local AI |

---

## Using it for writing and citing

**LaTeX / Overleaf:** use `library.bib` as your bibliography and cite with the key from the note, like `\cite{schmidt2024better}`. `ask` also prints a ready `\cite{...}` line. (For Overleaf, upload the `.bib` file again when it changes.)

**Zotero (for Word):** in Zotero, choose **File → Import…** and pick `library.bib`. The PDFs are linked, not copied. Then cite in Word with the Zotero plugin as usual.

**Obsidian:** open your `Papers` folder as a vault. Each note has tags, a score, a link to the PDF, and a **"My notes"** section for your own thoughts. The reading list links to every note. PaperPilot never changes a note after it creates it.

**Pandoc / Markdown writing:** cite with `[@schmidt2024better]` and use `library.bib`.

---

## Good to know

- **The AI can make mistakes.** Always check the summary and the citation before you rely on it, especially numbers. The score is a guide for what to read first, not a final decision.
- **Scanned PDFs** (images, no text) go to `_Inbox/_failed`. Run OCR on them first, for example with OCRmyPDF, then drop them in again.
- **Ollama not running?** Files simply stay in the inbox. PaperPilot tries again later.
- **Don't edit `library.bib` by hand.** It is rebuilt each time. Fix titles in Zotero or in your notes instead.
- **Start on login (optional):**
  - Windows: press `Win+R`, type `shell:startup`, and put a shortcut to `start_watch.bat` there.
  - Mac: System Settings → General → Login Items → add `start_watch.command` (it opens in Terminal).
  - Linux: add `start_watch.sh` to your desktop's autostart apps.
