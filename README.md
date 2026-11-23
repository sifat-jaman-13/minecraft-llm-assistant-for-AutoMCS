<div align="center">

# Minecraft LLM Assistant for AutoMCS

**Your AI-powered Minecraft survival companion — right inside your Auto-MCS server.**

Ask gameplay questions, inspect your inventory, find structures and biomes, and recover your last death coordinates.

[![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?style=for-the-badge&logo=windows)](https://www.microsoft.com/windows)
[![Runtime](https://img.shields.io/badge/Runtime-Auto--MCS-5865F2?style=for-the-badge)](https://www.auto-mcs.com/)
[![Python](https://img.shields.io/badge/Python-Script-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![License](https://img.shields.io/badge/License-Proprietary%20Non--Commercial-orange?style=for-the-badge)](LICENSE)

[**Download Latest Release**](https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS/releases) · [**Report an Issue**](https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS/issues)

</div>

---

## ✨ What it does

Minecraft LLM Assistant for AutoMCS is an Auto-MCS server script that adds an AI-assisted survival companion to Minecraft. It combines native Python inventory parsing with optional AI inference, built-in structure and biome lookups, and death-location tracking.

<details open>
<summary><strong>Core features</strong></summary>

| Feature | What it does |
| --- | --- |
| 💬 **AI survival assistant** | Ask gameplay questions with `!llm <question>`. |
| 🎒 **Inventory intelligence** | Parse and aggregate inventory items in Python; request AI analysis for crafting, usage, and recommendations. |
| 🏛️ **Structure finder** | Find the nearest supported structure with `!find <structure>`. |
| 🌍 **Biome finder** | Find the nearest supported biome with `!biome <biome>`. |
| ☠️ **Last-death tracker** | Recall your latest recorded death location with `!lastdeath`. |
| 🧭 **Location details** | View coordinates, distance, and compass direction for locate results. |
| ✍️ **Flexible matching** | Fuzzy matching helps with common structure names and spelling variations. |
| 🔌 **Backend choice** | Use hosted Hugging Face inference or local Ollama inference. |

</details>

## 🧰 Requirements

- Windows
- A Minecraft Java server managed by [Auto-MCS](https://www.auto-mcs.com/)
- Python support provided by Auto-MCS
- Python package `gradio_client`
- One configured AI backend:
  - **Hugging Face:** an access token and a compatible Space.
  - **Ollama:** a local installation with a downloaded model.

The script also uses Python standard-library modules. Windows `winreg` is used to read a user-level `HF_TOKEN`.

## 🚀 Installation

### 1. Set up Auto-MCS

Auto-MCS is the server manager and runtime that hosts this script.

1. Visit the [official Auto-MCS website](https://www.auto-mcs.com/).
2. Download and install the current Windows release.
3. Launch Auto-MCS and create or import a Minecraft Java server.
4. Start the server once so Auto-MCS creates its standard script/AMScript directories.
5. Stop the server before installing this script.

Follow the current Auto-MCS documentation and release instructions if the installer presents version-specific options. This project does not redistribute Auto-MCS.

### 2. Download this project

Choose either method:

<details open>
<summary><strong>Option A — Download a release (recommended)</strong></summary>

1. Open the [Releases page](https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS/releases).
2. Download the latest `minecraft-llm-assistant-for-AutoMCS-v*.zip` asset.
3. Extract the ZIP file.

</details>

<details>
<summary><strong>Option B — Clone the repository</strong></summary>

```powershell
git clone https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS.git
cd minecraft-llm-assistant-for-AutoMCS
python -m pip install -r requirements.txt
```

</details>

### 3. Import the AMScript

Auto-MCS includes an AMScript manager and editor. Use it to install and enable the script for your server. Do not place the file inside the Minecraft world or `mods` directory.

1. Stop the Minecraft server from Auto-MCS.
2. In Auto-MCS, open the target server and its **AMScript** or **Scripts** manager.
3. Choose **Import**, **Add Script**, or create a new script in the built-in editor. The exact label can vary by Auto-MCS version.
4. Select `MineCraft_LLM.txt` from the extracted package. If your Auto-MCS version only accepts `.ams` files, rename a copy to `MineCraft_LLM.ams` or create a new script and paste the complete file contents into the editor.
5. Save the script and enable it for the target server. Auto-MCS manages the shared script library, while activation can be controlled per server.
6. Install the dependency in the Python environment used by Auto-MCS:

```powershell
python -m pip install -r requirements.txt
```

7. Configure an AI backend using the instructions below.
8. Start the Minecraft server and check the Auto-MCS console for the backend status message.

If the server was already running while the script was edited, use **Reload Scripts** in the AMScript manager or run Auto-MCS's `!ams reload` command where supported. Otherwise, stop and restart the server.

For the official workflow and current interface details, see the [Auto-MCS AMScript guide](https://www.auto-mcs.com/guides/amscript).

### 4. Verify in game

Join the server and try:

```text
!lastdeath
!find village
!biome plains
!llm what do I have in my inventory?
```

The first three commands verify script event and command registration. The last command requests AI-powered inventory analysis. To display the inventory without AI, use `!llm what do I have?`.

A successful startup should also show a Hugging Face configuration message or an Ollama connectivity message in the Auto-MCS console.

> [!TIP]
> Scripts are managed centrally by Auto-MCS and may be shared across servers, with activation controlled per server. Use the folder button in the AMScript manager to open the managed scripts directory when you need to back up or inspect files. Keep an external backup because AMScripts may not be included in Auto-MCS's normal server backup set.

## 🧠 Configure the AI backend

Choose **one** backend. The script's server-owner configuration is near the top of `MineCraft_LLM.txt`.

<details open>
<summary><strong>Option A — Ollama (local inference)</strong></summary>

Ollama runs the model locally. Prompts are sent to the configured Ollama endpoint rather than a hosted inference service. The computer running Ollama must have enough memory and disk space for the selected model.

#### 1. Install Ollama

1. Visit the [official Windows download page](https://ollama.com/download/windows).
2. Download and run the installer.
3. Complete installation and allow Ollama to run in the background.
4. Open a new PowerShell window.

Verify:

```powershell
ollama --version
```

If the command is not recognized, restart PowerShell or Windows so the installer can refresh `PATH`.

#### 2. Download a model

The example configuration uses a small Qwen model:

```powershell
ollama pull qwen3.5:0.8b
```

Browse other options in the [Ollama model library](https://ollama.com/library). Larger models generally provide better answers but require more RAM, storage, and processing time.

Test the model:

```powershell
ollama run qwen3.5:0.8b
```

Ask a short question, then enter `/bye` to exit. The local API normally listens at `http://localhost:11434`.

#### 3. Select Ollama in the script

Set these values near the top of `MineCraft_LLM.txt`:

```python
ACTIVE_BACKEND = "ollama"
ACTIVE_MODEL_NAME = "qwen3.5:0.8b"
OLLAMA_BASE_URL = "http://localhost:11434"
```

Keep Ollama running before starting the Minecraft server. The script checks the endpoint at server startup and reports connectivity problems in the Auto-MCS log.

Optional performance settings include `OLLAMA_TIMEOUT`, `OLLAMA_NUM_PREDICT`, `OLLAMA_THINK`, and `OLLAMA_KEEP_ALIVE`.

#### 4. Verify the local API

```powershell
Invoke-RestMethod -Uri "http://localhost:11434/api/version"
```

If the request fails, start the Ollama application or run `ollama serve` in a separate PowerShell window.

</details>

<details>
<summary><strong>Option B — Hugging Face (hosted inference)</strong></summary>

The default configuration uses the Hugging Face Space `yuntian-deng/ChatGPT`.

1. Create or sign in to a [Hugging Face account](https://huggingface.co/).
2. Create an access token with permission to use the selected Space.
3. Save it as a user-level Windows environment variable:

```powershell
[Environment]::SetEnvironmentVariable("HF_TOKEN", "hf_your_token_here", "User")
```

4. Restart Auto-MCS.
5. Keep the script configuration as:

```python
ACTIVE_BACKEND = "hf"
ACTIVE_MODEL_NAME = "yuntian-deng/ChatGPT"
```

Change `ACTIVE_MODEL_NAME` only to a compatible Space exposing the expected `/predict` API.

</details>

## 🎮 Command reference

| Command | Example | Description |
| --- | --- | --- |
| `!llm <question>` | `!llm what can I do with my inventory?` | Ask a survival question. |
| `!find <structure>` | `!find ruined portal` | Locate the nearest supported structure. |
| `!biome <biome>` | `!biome cherry grove` | Locate the nearest supported biome. |
| `!lastdeath` | `!lastdeath` | Display the latest recorded death coordinates. |

### How `!llm` handles requests

- **Direct location prompts:** Requests such as `!llm find a village` are routed to Minecraft's `/locate` command without calling the LLM.
- **Basic inventory prompts:** Python parses and displays the inventory directly.
- **Inventory analysis:** Questions about crafting, building, or what to do with items send the inventory summary to the selected AI backend for analysis.
- **General questions:** Other prompts are sent to the selected AI backend with the player's health and hunger context.

## ⚙️ Configuration reference

| Setting | Purpose |
| --- | --- |
| `ACTIVE_BACKEND` | Select `hf` or `ollama`. |
| `ACTIVE_MODEL_NAME` | Hugging Face Space or Ollama model name. |
| `OLLAMA_BASE_URL` | Ollama API endpoint. |
| `OLLAMA_TIMEOUT` | Ollama request timeout in seconds. |
| `OLLAMA_NUM_PREDICT` | Maximum generated token count. |
| `OLLAMA_THINK` | Enable or disable model thinking mode where supported. |
| `OLLAMA_KEEP_ALIVE` | How long Ollama keeps the model loaded. |

> [!IMPORTANT]
> Never commit access tokens. Use environment variables and keep local configuration changes outside version control.

## 🔒 Privacy and network behavior

- **Hugging Face:** The prompt and included player context (health, hunger, and, for inventory analysis, inventory summary) are sent to the configured Hugging Face Space for inference. The access token is read from `HF_TOKEN` in the process environment or the current Windows user's environment registry entry.
- **Ollama:** Inference requests are sent to the configured Ollama endpoint, which defaults to `http://localhost:11434`.
- **In-game chat:** AI-generated questions and answers are broadcast to all players using the server's chat output. **Do not treat `!llm` conversations as private** and avoid entering personal or sensitive information.

## 🛠️ Troubleshooting

| Problem | What to check |
| --- | --- |
| **Ollama command not found** | Restart PowerShell after installation, then run `ollama --version`. |
| **Ollama connection refused** | Start Ollama or run `ollama serve`; verify `http://localhost:11434/api/version`. |
| **Model not found** | Run `ollama pull <model-name>` and ensure `ACTIVE_MODEL_NAME` matches `ollama list`. |
| **Slow Ollama responses** | Try a smaller model, reduce `OLLAMA_NUM_PREDICT`, or increase `OLLAMA_TIMEOUT`. |
| **Hugging Face token missing** | Confirm `HF_TOKEN` is set for the same Windows user running Auto-MCS, then restart Auto-MCS. |
| **`gradio_client` import error** | Install `requirements.txt` into the Python environment used by Auto-MCS. |
| **No locate result** | Use a supported structure or biome and verify the Minecraft server version supports `/locate`. |
| **Inventory not shown** | Ensure the script can execute `/data get entity` and that the player's name appears in the server output. |

## 🔍 How it works

For inventory requests, the script runs Minecraft's `/data get entity` command, captures the server output, parses the SNBT item list, and aggregates item counts in Python. Basic inventory requests display that result directly; prompts that ask what to craft, build, or do with the inventory send the summary, player health, hunger, and question to the selected AI backend.

Structure and biome searches use Minecraft's `/locate` command, then format the returned coordinates with distance and direction. Death coordinates are recorded through the player-death event and stored in the player's Auto-MCS persistent data.

## 📦 Downloads and script backups

- **Latest release:** [Download from GitHub Releases](https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS/releases).
- **Script backups:** Use the folder button in the AMScript manager to open the managed scripts directory. Keep an external backup because AMScripts may not be included in Auto-MCS's normal server backup set.

## 🙌 Credits

This project is inspired by the Auto-MCS ChatGPT script by [macarooni-man](https://github.com/macarooni-man/auto-mcs/blob/main/amscript-library/ChatGPT/chatgpt.ams).

## 📄 License

This project is available under a [proprietary non-commercial use licence](LICENSE). Individual personal and educational use is permitted; company, organisational, and commercial use requires prior written permission.

Copyright (c) 2026 Sifat Jaman.

---

<div align="center">

**Built for Minecraft survival. Powered by Python + your choice of AI backend.**

[Back to top](#minecraft-llm-assistant-for-automcs)

</div>
