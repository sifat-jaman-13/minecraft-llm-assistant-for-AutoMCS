# Minecraft LLM Assistant

An Auto-MCS server script that adds an AI-powered Minecraft survival companion. It can answer questions about a player's status and inventory, locate structures and biomes, and remember the coordinates of the player's latest death.

## Features

- `!llm <question>` for short survival guidance and inventory-aware questions
- Instant inventory parsing in Python for item lists
- Hugging Face Spaces backend by default
- Optional local Ollama backend
- `!find <structure>` for nearby structure coordinates
- `!biome <biome>` for nearby biome coordinates
- `!lastdeath` to display the last recorded death location
- Fuzzy matching for common structure and biome names
- Direction and distance output for locate results

## Requirements

- A Windows installation of [Auto-MCS](https://github.com/macarooni-man/auto-mcs)
- A Minecraft Java server managed by Auto-MCS
- Python support provided by Auto-MCS
- `gradio_client` for the Hugging Face backend
- Either:
  - a Hugging Face access token and a compatible Space, or
  - a local [Ollama](https://ollama.com/) installation with a downloaded model

The script also imports Python's standard-library modules. `winreg` is used to read `HF_TOKEN` from the Windows user environment.

## Installation

1. Download or clone this repository:

   ```powershell
   git clone https://github.com/sifat-jaman-13/minecraft-llm-assistant.git
   cd minecraft-llm-assistant
   ```

2. Install the Python dependency in the Python environment used by Auto-MCS:

   ```powershell
   python -m pip install gradio_client
   ```

3. Copy `MineCraft_LLM.txt` into the Auto-MCS script/AMScript library location used by your server.

4. Configure a backend in the server-owner configuration section at the top of the script.

### Hugging Face backend (default)

Set a user-level `HF_TOKEN` environment variable containing a Hugging Face token with permission to use the selected Space:

```powershell
[Environment]::SetEnvironmentVariable("HF_TOKEN", "hf_your_token_here", "User")
```

Restart Auto-MCS after setting the variable. The default model is `yuntian-deng/ChatGPT`; change `ACTIVE_MODEL_NAME` if you use a different compatible Space.

### Ollama backend (local alternative)

1. Install Ollama and download a model:

   ```powershell
   ollama pull qwen3.5:0.8b
   ```

2. Start Ollama.

3. Change the configuration in `MineCraft_LLM.txt`:

   ```python
   ACTIVE_BACKEND = "ollama"
   ACTIVE_MODEL_NAME = "qwen3.5:0.8b"
   ```

The default Ollama endpoint is `http://localhost:11434`. Adjust `OLLAMA_BASE_URL` if needed.

## Commands

| Command | Example | Description |
| --- | --- | --- |
| `!llm <question>` | `!llm what can I do with my inventory?` | Ask the assistant a survival question |
| `!find <structure>` | `!find ruined portal` | Locate the nearest supported structure |
| `!biome <biome>` | `!biome cherry grove` | Locate the nearest supported biome |
| `!lastdeath` | `!lastdeath` | Show the latest recorded death coordinates |

The script also recognizes direct location requests through `!llm`, such as `!llm find a village`, and routes those requests to Minecraft's `/locate` command without calling the LLM.

## Configuration

The main settings are near the top of `MineCraft_LLM.txt`:

- `ACTIVE_BACKEND`: `hf` or `ollama`
- `ACTIVE_MODEL_NAME`: Hugging Face Space or Ollama model name
- `OLLAMA_BASE_URL`: local Ollama API URL
- `OLLAMA_TIMEOUT`: Ollama request timeout in seconds
- `OLLAMA_NUM_PREDICT`: maximum number of generated tokens

Never commit access tokens. Use environment variables or a local, untracked configuration change.

## How it works

Inventory questions first request the player's inventory through the Minecraft `/data get entity` command. The script captures the server output, parses the SNBT item list, aggregates matching items, and either displays the result directly or sends the summarized inventory to the selected LLM. Structure and biome searches use Minecraft's `/locate` command and format the returned coordinates with distance and direction.

## Troubleshooting

- **Hugging Face token missing:** confirm `HF_TOKEN` is set for the same Windows user running Auto-MCS, then restart Auto-MCS.
- **Ollama unavailable:** confirm Ollama is running and `OLLAMA_BASE_URL` is reachable.
- **`gradio_client` import error:** install it into the Python environment used by Auto-MCS, not only another system Python installation.
- **No locate result:** use a supported structure or biome name and verify the server version supports the requested `/locate` command.
- **Inventory not shown:** ensure the script has permission to execute `/data get entity` and that the player's name is valid in the server output.

## Credits

This project is inspired by the Auto-MCS ChatGPT script by [macarooni-man](https://github.com/macarooni-man/auto-mcs/blob/main/amscript-library/ChatGPT/chatgpt.ams).

## License

No license has been selected yet. Until a license is added, all rights are reserved by the copyright holder.
