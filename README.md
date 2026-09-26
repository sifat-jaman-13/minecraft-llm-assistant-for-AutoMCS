# Minecraft LLM Assistant for AutoMCS

Minecraft LLM Assistant for AutoMCS is an Auto-MCS server script that provides an AI-assisted survival companion directly in Minecraft. It can answer concise gameplay questions, inspect a player's inventory, locate structures and biomes, and record the coordinates of the player's latest death.

The project supports two AI backends:

- **Hugging Face Spaces** for hosted inference.
- **Ollama** for local inference on the server-owner's computer.

## Features

- `!llm <question>` for survival guidance and inventory-aware questions
- Native Python inventory parsing and item aggregation
- `!find <structure>` for nearby structure coordinates
- `!biome <biome>` for nearby biome coordinates
- `!lastdeath` for the most recently recorded death location
- Fuzzy matching for common structure names and spelling variations
- Distance and compass-direction output for locate results
- Optional hosted or local AI inference

## Requirements

- Windows
- A Minecraft Java server managed by [Auto-MCS](https://www.auto-mcs.com/)
- Python support provided by Auto-MCS
- Python package `gradio_client`
- One configured AI backend:
  - a Hugging Face access token and a compatible Space, or
  - Ollama installed locally with a downloaded model

The script uses Python standard-library modules in addition to `gradio_client`. Windows `winreg` is used to read a user-level `HF_TOKEN`.

## Install Auto-MCS

Auto-MCS is the server manager and runtime that hosts this script.

1. Open the official website: [https://www.auto-mcs.com/](https://www.auto-mcs.com/).
2. Download the current Windows installer or release provided by Auto-MCS.
3. Run the installer and follow its setup prompts.
4. Launch Auto-MCS and create or import a Minecraft Java server.
5. Start the server once so Auto-MCS creates its standard script/AMScript directories.
6. Stop the server before installing this script.

Follow the current Auto-MCS documentation and release instructions if the installer presents version-specific options. This project does not redistribute Auto-MCS.

## Install this project

### Option 1: Download a release package

1. Open the repository's [Releases page](https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS/releases).
2. Download the latest `minecraft-llm-assistant-for-AutoMCS-v*.zip` asset.
3. Extract the ZIP file.
4. Copy `MineCraft_LLM.txt` into the Auto-MCS script/AMScript library location used by your server.
5. Install the Python dependency in the Python environment used by Auto-MCS:

   ```powershell
   python -m pip install -r requirements.txt
   ```

6. Configure one of the AI backends below.
7. Restart Auto-MCS and start the server.

### Option 2: Clone the repository

```powershell
git clone https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS.git
cd minecraft-llm-assistant-for-AutoMCS
python -m pip install -r requirements.txt
```

Then copy `MineCraft_LLM.txt` into the Auto-MCS script/AMScript library location and configure a backend.

## Configure the AI backend

### Option A: Ollama local inference

Ollama runs the model locally. This avoids sending prompts to a hosted inference service and does not require a Hugging Face token. The computer running Ollama must have enough memory and disk space for the selected model.

#### 1. Install Ollama on Windows

1. Visit the official download page: [https://ollama.com/download/windows](https://ollama.com/download/windows).
2. Download and run the Windows installer.
3. Complete the installation and allow Ollama to run in the background.
4. Open a new PowerShell window.

Verify the installation:

```powershell
ollama --version
```

If the command is not recognized, restart PowerShell or Windows so the installer can refresh `PATH`.

#### 2. Download a model

The script is configured for a small Qwen model by default in the example below:

```powershell
ollama pull qwen3.5:0.8b
```

You can browse available models at [https://ollama.com/library](https://ollama.com/library). Larger models generally provide better answers but require more RAM, storage, and processing time.

Test the model before connecting it to Minecraft:

```powershell
ollama run qwen3.5:0.8b
```

Ask a short question, then enter `/bye` to exit. The local API normally listens at `http://localhost:11434`.

#### 3. Select Ollama in the script

At the top of `MineCraft_LLM.txt`, set:

```python
ACTIVE_BACKEND = "ollama"
ACTIVE_MODEL_NAME = "qwen3.5:0.8b"
OLLAMA_BASE_URL = "http://localhost:11434"
```

Keep Ollama running before starting the Minecraft server. The script checks the Ollama endpoint at server startup and reports connectivity problems in the Auto-MCS log. Optional performance settings include `OLLAMA_TIMEOUT`, `OLLAMA_NUM_PREDICT`, `OLLAMA_THINK`, and `OLLAMA_KEEP_ALIVE`.

#### 4. Verify Ollama from PowerShell

```powershell
Invoke-RestMethod -Uri "http://localhost:11434/api/version"
```

If this request fails, start Ollama from the Start menu or run `ollama serve` in a separate PowerShell window.

### Option B: Hugging Face hosted inference

The default configuration uses the Hugging Face Space `yuntian-deng/ChatGPT`.

1. Create or sign in to a Hugging Face account.
2. Create an access token with permission to use the selected Space.
3. Save it as a user-level Windows environment variable:

   ```powershell
   [Environment]::SetEnvironmentVariable("HF_TOKEN", "hf_your_token_here", "User")
   ```

4. Restart Auto-MCS.
5. Leave the script configuration as:

   ```python
   ACTIVE_BACKEND = "hf"
   ACTIVE_MODEL_NAME = "yuntian-deng/ChatGPT"
   ```

Change `ACTIVE_MODEL_NAME` only to a compatible Space exposing the expected `/predict` API.

## Commands

| Command | Example | Description |
| --- | --- | --- |
| `!llm <question>` | `!llm what can I do with my inventory?` | Ask the assistant a survival question |
| `!find <structure>` | `!find ruined portal` | Locate the nearest supported structure |
| `!biome <biome>` | `!biome cherry grove` | Locate the nearest supported biome |
| `!lastdeath` | `!lastdeath` | Display the latest recorded death coordinates |

The assistant also routes direct location requests such as `!llm find a village` to Minecraft's `/locate` command without using the LLM.

## Configuration reference

The server-owner configuration section is at the top of `MineCraft_LLM.txt`:

| Setting | Purpose |
| --- | --- |
| `ACTIVE_BACKEND` | Select `hf` or `ollama` |
| `ACTIVE_MODEL_NAME` | Hugging Face Space or Ollama model name |
| `OLLAMA_BASE_URL` | Ollama API endpoint |
| `OLLAMA_TIMEOUT` | Ollama request timeout in seconds |
| `OLLAMA_NUM_PREDICT` | Maximum generated token count |
| `OLLAMA_THINK` | Enable or disable model thinking mode where supported |
| `OLLAMA_KEEP_ALIVE` | How long Ollama keeps the model loaded |

Never commit access tokens. Use environment variables and keep local configuration changes outside version control.

## Troubleshooting

- **Ollama command not found:** restart PowerShell after installation, then run `ollama --version`.
- **Ollama connection refused:** start the Ollama application or run `ollama serve`; verify `http://localhost:11434/api/version`.
- **Model not found:** run `ollama pull <model-name>` and make sure `ACTIVE_MODEL_NAME` exactly matches `ollama list`.
- **Slow Ollama responses:** use a smaller model, reduce `OLLAMA_NUM_PREDICT`, or increase `OLLAMA_TIMEOUT`.
- **Hugging Face token missing:** confirm `HF_TOKEN` is set for the same Windows user running Auto-MCS, then restart Auto-MCS.
- **`gradio_client` import error:** install `requirements.txt` into the Python environment used by Auto-MCS.
- **No locate result:** use a supported structure or biome and verify the Minecraft server version supports `/locate`.
- **Inventory not shown:** ensure the script can execute `/data get entity` and that the player's name appears in the server output.

## How it works

For inventory questions, the script requests the player's inventory through Minecraft's `/data get entity` command, captures the server output, parses the SNBT item list, and aggregates matching items. It either displays the result directly or sends the summary to the selected AI backend. Structure and biome searches use `/locate`, then format the returned coordinates with distance and direction.

## Download

Download the latest ready-to-install package from the [GitHub Releases page](https://github.com/sifat-jaman-13/minecraft-llm-assistant-for-AutoMCS/releases).

## Credits

This project is inspired by the Auto-MCS ChatGPT script by [macarooni-man](https://github.com/macarooni-man/auto-mcs/blob/main/amscript-library/ChatGPT/chatgpt.ams).

## License

This project is licensed under the [MIT License](LICENSE).

Copyright (c) 2026 Sifat Jaman.
