# JARVIS for Windows

**Just A Rather Very Intelligent System.**

A voice-first AI assistant that runs on your **Windows** PC. Talk to it, and it talks back — with a British accent, dry wit, and an audio-reactive particle orb straight out of the MCU.

JARVIS listens through your microphone, thinks with Claude, speaks with Fish Audio, and can control your PC — open websites, open a terminal, look at your screen, and spawn Claude Code sessions to build entire projects — all through natural voice conversation.

> "Will do, sir."

---

## What It Does (on Windows)

✅ **Works on Windows:**
- **Voice conversation** — speak naturally, get spoken responses in the JARVIS voice
- **Builds software** — "build me a landing page" and watch Claude Code do the work in a new console window
- **Works on existing projects** — "jump into my-app and summarize what's left"
- **Opens websites** — "pull up YouTube"
- **Opens a terminal** — "open a terminal"
- **Sees your screen** — "what's on my screen?" (screen capture → Claude vision)
- **Manages tasks** — "remind me to call the client tomorrow"
- **Takes notes** — "save that as a note"
- **Remembers things** — "I prefer React over Vue" (it remembers next time)
- **Weather** — "what's the weather like?"
- **Audio-reactive orb** — a Three.js particle visualization that pulses with JARVIS's voice

🚫 **macOS-only (disabled on Windows):**
- Apple **Calendar**, **Mail**, and **Notes** integrations (these use AppleScript, which doesn't exist on Windows). They're safely no-ops on Windows — they won't crash anything.

## Requirements

- **Windows 11** (also works on Windows 10)
- **Python 3.11+**
- **Node.js 18+**
- **Git for Windows** (its bundled OpenSSL is used to make the SSL certificate)
- **Google Chrome** (required — JARVIS uses Chrome's Web Speech API for voice recognition)
- **Anthropic API key** — powers the AI brain ([get one](https://console.anthropic.com/))
- **Fish Audio API key** — powers the voice ([get one](https://fish.audio/)); requires a small balance
- **Claude Code CLI** — for the build/project features ([install](https://docs.anthropic.com/en/docs/claude-code))

## Setup (Windows / PowerShell)

Run these from the project folder in PowerShell:

```powershell
# 1. Create a Python virtual environment
python -m venv venv

# 2. Install backend dependencies into it
venv\Scripts\python.exe -m pip install -r requirements.txt

# 3. Install the Chromium browser used for web automation
venv\Scripts\python.exe -m playwright install chromium

# 4. Install frontend dependencies
cd frontend
npm install
cd ..

# 5. Set up your API keys
copy .env.example .env
#   then open .env and paste your ANTHROPIC_API_KEY and FISH_API_KEY

# 6. Generate a local SSL certificate (uses Git's bundled OpenSSL)
& "C:\Program Files\Git\usr\bin\openssl.exe" req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes -subj "/CN=localhost"
```

### Running it

Open **two** PowerShell windows in the project folder:

```powershell
# Window 1 — backend
venv\Scripts\python.exe server.py
```
```powershell
# Window 2 — frontend
cd frontend
npm run dev
```

Then open **Chrome** to **http://localhost:5173**, click the page once to enable the microphone, and start talking. Stop either server with `Ctrl+C`.

> ⚠️ **Do not start the backend with `--reload` on Windows.** Plain `python server.py` uses the ProactorEventLoop, which the build/project features need. The `--reload` flag switches to a loop that breaks them.

## Configuration

Edit your `.env` file:

```env
# Required
ANTHROPIC_API_KEY=your-anthropic-api-key-here
FISH_API_KEY=your-fish-audio-api-key-here

# Optional — your name (JARVIS will address you personally)
USER_NAME=Ash
```

## Architecture

```
Microphone -> Chrome Web Speech API -> WebSocket -> FastAPI -> Claude (Haiku) -> Fish Audio TTS -> Speaker
                                                       |
                                                       v
                                               Claude Code (builds real projects)
                                                       |
                                                       v
                                       Windows actions: Chrome, console, screen capture
```

| Layer | Technology |
|-------|-----------|
| Backend | FastAPI + Python (`server.py`) |
| Frontend | Vite + TypeScript + Three.js |
| Communication | WebSocket (JSON + binary audio) |
| AI (fast) | Claude Haiku — low-latency voice responses |
| TTS | Fish Audio with the JARVIS voice model |
| System (Windows) | `subprocess` + Win32 (`ctypes`) + `mss` screen capture |

## Notes on the Windows port

This version adds Windows support on top of the original macOS project:
- `actions.py` — opens Chrome / a console window directly (instead of AppleScript)
- `screen.py` — screen capture via `mss`, window listing via the Win32 API
- `calendar_access.py` / `mail_access.py` / `notes_access.py` — guarded so the Apple-only
  features no-op cleanly on Windows
- `frontend/vite.config.ts` — proxies to `127.0.0.1` to avoid IPv6 resolution issues

## License

Free for personal, non-commercial use. Commercial use requires a license — see
[LICENSE](LICENSE) for the full terms.

## Credits

Original JARVIS project created by **Ethan Rogers** ([ethanplus.ai](https://ethanplus.ai)).
Windows port and setup by Aakash Sethi.

Powered by [Anthropic Claude](https://anthropic.com) and [Fish Audio](https://fish.audio).

> **Disclaimer:** This is an independent fan project and is not affiliated with, endorsed by, or connected to Marvel Entertainment, The Walt Disney Company, or any related entities. The JARVIS name and character are property of Marvel Entertainment.
