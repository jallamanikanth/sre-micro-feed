# SRE micro-feed agent

A tiny agent that replaces doom-scrolling reels with a short, useful SRE feed.
Twice a day (1 PM and 9 PM India time) it reads SRE blogs and incident write-ups,
picks the best new articles, has **Gemini** summarize each in 3 lines
(**what broke / why / lesson**), and updates a web page you can open on your phone.

It runs for free on GitHub Actions and GitHub Pages. There is no server and nothing to install.

```
SRE blogs  ->  rank (incidents first, marketing last)  ->  read full article  ->  Gemini summary  ->  web page
```

## Set it up (about 10 minutes, no coding)

### 1. Get a free Gemini API key
1. Go to <https://aistudio.google.com/apikey> and create an API key.
2. Copy it. Treat it like a password.

### 2. Add the key to this repo as a secret
In the repo open **Settings > Secrets and variables > Actions > New repository secret**:

| Name | Value |
|---|---|
| `GEMINI_API_KEY` | your Gemini key |

Secrets are encrypted by GitHub. Never paste the key into a file in the repo.
Without the key the page still works, but shows plain excerpts instead of AI summaries.

### 3. Turn on the web page (GitHub Pages)
1. Open **Settings > Pages**.
2. Under **Build and deployment**, set **Source** to **Deploy from a branch**.
3. Choose branch **main** and folder **/docs**, then **Save**.
4. After a minute your page is live at `https://<your-github-username>.github.io/sre-micro-feed/`.

On your phone, open that link and use **Add to Home Screen**, so it sits on your home
screen where the Instagram icon used to be.

### 4. Run it once
Open the **Actions** tab, choose **SRE micro-feed**, click **Run workflow**.
After about a minute, refresh your page. From then on it updates itself twice a day.

## Make it yours

- **Add or remove blogs:** edit `feeds.txt` (one feed URL per line). Broken feeds are skipped automatically.
  To find a feed, search "<blog name> RSS feed".
- **Change the times:** edit the `cron` line in `.github/workflows/micro-feed.yml`.
  GitHub uses UTC, so subtract 5:30 from your India time (1 PM IST = `07:30` UTC, 9 PM IST = `15:30` UTC).
  Scheduled runs can start a few minutes late.
- **More or fewer items per run:** set `ITEMS_PER_RUN` in the workflow (default 5).
- **Change what counts as interesting:** edit the `BOOST` and `PENALTY` word lists at the top of `feed.py`.
- **Change the Gemini model:** set `GEMINI_MODEL` in the workflow. By default it uses Google's
  `gemini-flash-latest` alias and falls back to `gemini-2.5-flash`.

## Troubleshooting

- **Page says "excerpt" on every card:** the Gemini key is missing or wrong. Re-check the secret name is exactly `GEMINI_API_KEY`.
- **Page is empty or 404:** finish step 3 and run the workflow once (step 4).
- **Nothing new today:** the agent never repeats an article (it remembers links in `seen.json`),
  so on a quiet day the page simply stays as it was.
- **A run failed:** open it in the Actions tab and read the red step.
- **GitHub pauses scheduled runs** in repos with no activity for 60 days. Re-enable it in the Actions tab if that happens.

## Safety

- Keep your API key in GitHub Secrets only.
- Summaries are AI-generated and can be wrong. Open the link before acting on anything important.
- Pages from a public repo are public. The page only lists public blog posts and links.

## Try it on your own machine (optional)

```bash
python feed.py        # nothing to install; set GEMINI_API_KEY first for AI summaries
```
It writes `docs/index.html`; open that file in a browser.
