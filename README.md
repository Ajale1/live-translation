# How to install LiveTranslate (step by step, for everyone)

No technical experience needed. Find your situation below and follow the
numbered steps exactly.

**Which version do I need?**

| You have | Install this | Where it appears in Resolve |
|---|---|---|
| DaVinci Resolve (FREE) | The one-file version (`LiveTranslate.py`) | Workspace ▸ Scripts ▸ LiveTranslate |
| DaVinci Resolve STUDIO (paid) | The full plugin (`install-plugin.py`) | Workspace ▸ Workflow Integrations ▸ LiveTranslate |

Not sure which Resolve you have? Open Resolve and look at the title bar —
Studio says "DaVinci Resolve Studio".

---

## A. FREE Resolve — Windows

### Step 1: Install Python (one time)

1. Go to https://www.python.org/downloads/ and click the yellow
   "Download Python 3.x" button.
2. Open the downloaded file.
3. IMPORTANT: on the first screen, tick the box **"Add Python to PATH"**
   (bottom of the window). Then click "Install Now".
4. Wait for "Setup was successful" and close it.

### Step 2: Run LiveTranslate

1. Download `LiveTranslate.py` (from the Releases page) into your
   Downloads folder.
2. Press the Windows key, type `powershell`, press Enter. A blue window
   opens.
3. Copy-paste these two lines into it, pressing Enter after each:

   ```powershell
   cd $HOME\Downloads
   python LiveTranslate.py
   ```

   (If it says "python is not recognized", try `py LiveTranslate.py`.)
4. A setup window appears and installs everything by itself — it downloads
   the speech and translation models. This takes several minutes ONE time.
   Do not close it. Wait for **"Setup complete"** — the caption window then
   opens by itself.

### Step 3: Use it inside Resolve

1. Close Resolve completely and open it again.
2. Top menu: **Workspace ▸ Scripts ▸ LiveTranslate**.
3. Press ▶ Start and speak. Done.

---

## B. FREE Resolve — Mac

### Step 1: Run LiveTranslate

1. Download `LiveTranslate.py` into your Downloads folder.
2. Open Terminal: press Cmd+Space, type `terminal`, press Enter.
3. Copy-paste these two lines, pressing Enter after each:

   ```bash
   cd ~/Downloads
   python3 LiveTranslate.py
   ```

   (If your Mac offers to install "command line developer tools", click
   Install, wait, then run the second line again.)
4. A setup window appears and installs everything by itself (several
   minutes, one time). Wait for **"Setup complete"**.

### Step 2: Use it inside Resolve

1. Quit Resolve fully (Cmd+Q) and open it again.
2. Top menu: **Workspace ▸ Scripts ▸ LiveTranslate**.
3. Press ▶ Start. The first time, your Mac asks about the microphone —
   click **Allow**.

---

## C. Resolve STUDIO — Mac (full plugin with panel)

### Step 1: Install Node.js (one time)

1. Go to https://nodejs.org and download the LTS version.
2. Open the installer, click Continue/Agree/Install all the way through.

### Step 2: Install the plugin

1. Download the source ZIP (from the Releases page) into Downloads and
   double-click it — you get a folder called `resolve-live-translate`
   (or similar).
2. Open Terminal (Cmd+Space, type `terminal`, Enter).
3. Copy-paste these two lines, pressing Enter after each:

   ```bash
   cd ~/Downloads/resolve-live-translate
   sudo python3 install-plugin.py
   ```

4. When it says `Password:`, type your Mac login password and press Enter.
   **You won't see anything while typing — that's normal.**
5. Wait. You'll see progress messages (models download — several minutes,
   one time). Do not close Terminal until you see **==> Done!**

### Step 3: Open it

1. Quit Resolve fully (Cmd+Q), open it again.
2. Top menu: **Workspace ▸ Workflow Integrations ▸ LiveTranslate**.
3. Press ▶ Start, click **Allow** when asked about the microphone, speak.

---

## D. Resolve STUDIO — Windows (full plugin with panel)

### Step 1: Install Python and Node.js (one time)

1. Python: https://www.python.org/downloads/ — during install, TICK
   **"Add Python to PATH"**, then Install Now.
2. Node.js: https://nodejs.org — download LTS, install with default
   options.

### Step 2: Install the plugin

1. Download the source ZIP into Downloads. Right-click it ▸ "Extract All"
   ▸ Extract. You get a folder called `resolve-live-translate`.
2. Press the Windows key, type `powershell`, then RIGHT-CLICK
   "Windows PowerShell" and choose **"Run as administrator"** ▸ Yes.
3. Copy-paste these two lines, pressing Enter after each:

   ```powershell
   cd $HOME\Downloads\resolve-live-translate
   python install-plugin.py
   ```

4. Wait for the progress messages (models download — several minutes, one
   time) until you see **==> Done!**

### Step 3: Open it

1. Close Resolve completely, open it again.
2. Top menu: **Workspace ▸ Workflow Integrations ▸ LiveTranslate**.
3. Press ▶ Start and speak.

---

## Using it (30 seconds)

- **Speak in** = the language being spoken. **Translate to** = add as many
  languages as you want (type to search, e.g. "Yoruba"). Click **Apply**.
- **▶ Start** = live captions while you talk.
- **⏲ Transcribe Timeline** = subtitles for the video already in your
  timeline (exact timing).
- **✎ Review / Edit** = fix any wrong word, then ↻ Re-translate that line.
- **Export SRT** = saves subtitle files (one per language) to the
  `LiveTranslate` folder in your home folder.
- **SRT → Timeline (XX)** = puts that language's subtitles straight onto
  your open timeline.

The FIRST time you use an African language (Hausa, Yoruba, Igbo, Swahili…),
the plugin downloads an extra translation model (~1.2 GB, one time). Let it
finish — after that everything works offline.

---

## If something goes wrong

| You see | Do this |
|---|---|
| `python is not recognized` (Windows) | Reinstall Python and TICK "Add Python to PATH". Or use `py` instead of `python`. |
| `No such file or directory` after `cd` | The folder isn't where the command expects. Type `cd `, then drag the folder from Finder/Explorer onto the window, press Enter. |
| `Permission denied` installing the plugin | Mac: put `sudo ` in front of the command. Windows: open PowerShell "as administrator". |
| Can't find it in Resolve | FREE version → Workspace ▸ Scripts. STUDIO plugin → Workspace ▸ Workflow Integrations. Restart Resolve completely first. |
| `npm not found` | Install Node.js from nodejs.org, close and reopen the terminal, try again. |
| No captions appear when speaking | Check the green level bar moves when you talk. Mac: System Settings ▸ Privacy & Security ▸ Microphone ▸ allow. |
| Setup failed halfway (internet dropped) | Just run the same command again — it continues where it stopped. |

Still stuck? Open an issue on the GitHub page with a screenshot of the
error — I read them.

— Williams Ameh · Regent Films Studio
