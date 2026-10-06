# SRE micro-feed agent

A tiny agent that replaces doom-scrolling reels with a short, useful SRE feed.
Twice a day (1 PM and 9 PM India time) it picks the best new articles from SRE
blogs and incident write-ups, summarizes each in 3 lines
(**what broke / why / lesson**), and sends them to you on Telegram.

It runs for free on GitHub Actions. There is no server and nothing to host.

```
RSS feeds  ->  rank (incidents first, marketing last)  ->  AI summary  ->  Telegram
```

## Set it up (about 20 minutes, no coding)

### 1. Create your Telegram bot
1. In Telegram, search for **@BotFather** and send `/newbot`.
2. Pick a name and a username ending in `bot`.
3. BotFather replies with a **token** (looks like `123456:ABC...`). Keep it private.
4. Open your new bot in Telegram and press **Start**, then send it any message ("hi").

### 2. Find your chat ID
1. In your browser, open: `https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates`
   (replace `<YOUR_TOKEN>` with the token, keep the word `bot` before it).
2. Find `"chat":{"id":` followed by a number. That number is your **chat ID**.

### 3. (Optional, recommended) Get a free Gemini key for AI summaries
1. Go to <https://aistudio.google.com/apikey> and create an API key.
2. Without a key the agent still works, but sends a plain excerpt instead of a 3-line summary.

### 4. Add your secrets to this GitHub repo
In the repo go to **Settings > Secrets and variables > Actions > New repository secret** and add:

| Name | Value |
|---|---|
| `TELEGRAM_BOT_TOKEN` | the token from BotFather |
| `TELEGRAM_CHAT_ID` | your chat ID |
| `GEMINI_API_KEY` | your Gemini key (optional) |

Secrets are encrypted by GitHub. Never paste them into a file in the repo.

### 5. Run it once
Open the **Actions** tab, choose **SRE micro-feed**, click **Run workflow**.
Within a minute you should get a message on Telegram. After that it runs on its own.

## Make it yours

- **Add or remove sources:** edit `feeds.txt` (one URL per line). Broken feeds are skipped automatically.
- **Change the times:** edit the `cron` line in `.github/workflows/micro-feed.yml`.
  GitHub uses UTC, so subtract 5:30 from your India time (1 PM IST = `07:30` UTC, 9 PM IST = `15:30` UTC).
  Scheduled runs can start a few minutes late.
- **More or fewer items per message:** set `ITEMS_PER_RUN` in the workflow (default 5).
- **Change what counts as interesting:** edit the `BOOST` and `PENALTY` word lists at the top of `feed.py`.

## Troubleshooting

- **No message arrived:** open the failed run in the Actions tab and read the red step.
  Most often the chat ID is wrong, or you forgot to press Start on your bot.
- **Nothing new to send:** the agent never repeats an article (it remembers links in `seen.json`),
  so on a quiet day it sends nothing.
- **GitHub pauses scheduled runs** in repos with no activity for 60 days. Re-enable it in the Actions tab if that happens.

## Safety

- Keep your token and keys in GitHub Secrets only.
- Summaries are AI-generated. Open the link before acting on anything important.

## Try it on your own machine (optional)

```bash
python feed.py        # nothing to install; prints a dry run if no Telegram secrets are set
```
