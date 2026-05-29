# Release Radar

release-radar monitors software release feeds, summarizes new releases with Gemini, and sends the summaries to Telegram.

## Features

- Fetches release updates from configured RSS/Atom sources
- Supports VS Code release notes via GitHub markdown and standard GitHub Atom feeds
- Summarizes release notes with Gemini (output in Turkish)
- Sends summaries to Telegram with chunking support for long messages
- Stores processed entry IDs in `state.json` to avoid duplicate notifications
- Retries failed network requests and API calls with exponential backoff

## Requirements

- Python 3.10 or newer
- Telegram bot token and chat ID
- Gemini API key

Install dependencies:

```bash
pip install -r requirements.txt
```

## Configuration

Create a `.env` file from the example:

```bash
cp .env.example .env
```

Fill in the required values:

```env
TELEGRAM_BOT_TOKEN="your_bot_token"
TELEGRAM_CHAT_ID="your_chat_id"
GEMINI_API_KEY="your_gemini_api_key"
```

Configure sources in `config.json`:

```json
{
  "VS Code": {
    "rss": "https://code.visualstudio.com/feed.xml",
    "type": "vscode_github"
  },
  "Claude Code": {
    "rss": "https://github.com/anthropics/claude-code/releases.atom",
    "type": "github_releases"
  }
}
```

Supported source types:

- `vscode_github` — fetches full release notes from the VS Code docs GitHub repository
- `github_releases` — works with any GitHub repository's Atom feed (`/releases.atom`)

## Usage

```bash
python main.py
```

On the first run, release-radar syncs existing entries into `state.json` without sending notifications. Subsequent runs process only new entries.

## Automation

The script is designed to run as a scheduled task. On macOS and Linux, use `cron`. On Windows, use Task Scheduler. A GitHub Actions workflow can also be used to run the script periodically without keeping a machine on.

## Runtime Files

- `state.json` — stores processed entry IDs between runs
- `releaseradar.log` — stores application logs

These files are ignored by Git unless you are committing `state.json` updates via a CI/CD pipeline (e.g., GitHub Actions).

## Notes

The Gemini prompt requests summaries in Turkish. Static logs and Telegram message labels are in English.
