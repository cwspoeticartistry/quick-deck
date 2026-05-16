# web-research — Firecrawl Research Skill for Claude

A portable skill that turns any URL or topic into clean Markdown content,
ready to feed into quick-deck or any other Claude workflow.

---

## SKILL IDENTITY

- **Name:** web-research
- **Trigger phrase:** `use web-research`
- **Compatible with:** Claude Code (primary), Claude Desktop / Claude.ai (with file attachment)
- **Requires:** Firecrawl API key in `FIRECRAWL_API_KEY` environment variable
- **Pairs with:** quick-deck — use web-research to gather content, then quick-deck to build the deck

---

## ACTIVATION

When the user types `use web-research` (Claude Code) or attaches this file and sends
"Please follow the instructions in the attached file.", start the research flow below.

---

## RESEARCH FLOW

### Step 1 — Source

Ask exactly this question and wait for the answer before proceeding:

> "What would you like me to research? You can give me:
> - A URL to scrape (e.g. https://example.com/article)
> - A search query (e.g. 'AI trends 2026')
> - A list of URLs separated by commas"

### Step 2 — Depth

Ask:

> "How deep should I go?
> 1. **Single page** — just the URL you gave me
> 2. **Crawl** — the page plus up to 5 linked pages on the same domain
> 3. **Search** — find the top 5 web results for your query then scrape each one"

Wait for the answer.

### Step 3 — Output format

Ask:

> "How would you like the output?
> - **Summary** — 3–5 bullet points per source, key facts only
> - **Full content** — complete Markdown of each page
> - **Deck-ready** — structured as quick-deck input (topic + key points per slide)"

Wait for the answer.

---

## EXECUTION RULES

### API call — single URL (scrape)

```bash
curl -X POST https://api.firecrawl.dev/v1/scrape \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "<URL>",
    "formats": ["markdown"],
    "onlyMainContent": true
  }'
```

### API call — crawl (page + linked pages)

```bash
curl -X POST https://api.firecrawl.dev/v1/crawl \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "<URL>",
    "limit": 5,
    "scrapeOptions": { "formats": ["markdown"], "onlyMainContent": true }
  }'
```

Poll `/v1/crawl/{id}` every 3 seconds until `status: "completed"`.

### API call — search

```bash
curl -X POST https://api.firecrawl.dev/v1/search \
  -H "Authorization: Bearer $FIRECRAWL_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "query": "<QUERY>",
    "limit": 5,
    "scrapeOptions": { "formats": ["markdown"], "onlyMainContent": true }
  }'
```

---

## OUTPUT RULES

- Strip navigation bars, cookie banners, footers, and ads from scraped content.
- Always cite the source URL above each piece of content.
- If the user chose **Deck-ready**, format output as:

```
TOPIC: <inferred topic>
AUDIENCE: <inferred audience>
STYLE: (leave blank — user will choose)

SLIDE 1 — <heading>
- <bullet>
- <bullet>

SLIDE 2 — <heading>
...
```

This format feeds directly into quick-deck's three-question flow.

- If any URL returns an error or paywall, note it and skip to the next source.
- After output, always offer: "Would you like me to use quick-deck to turn this into a presentation?"

---

## CREDENTIAL RULE

NEVER hardcode or print the `FIRECRAWL_API_KEY` value. Read it only from the environment.
If `FIRECRAWL_API_KEY` is not set, respond:

> "I need a Firecrawl API key to scrape the web. Please set FIRECRAWL_API_KEY in your
> environment (Claude Code: ~/.claude/settings.json → env → FIRECRAWL_API_KEY) and restart."

---

## INSTALL (Claude Code)

1. Download this file and rename it to `SKILL.md`
2. Place it at: `~/.claude/skills/web-research/SKILL.md`
3. Add your Firecrawl key to `~/.claude/settings.json`:
   ```json
   "env": { "FIRECRAWL_API_KEY": "fc-your-key-here" }
   ```
4. In any Claude Code conversation: `use web-research`

## INSTALL (Claude Desktop / Claude.ai)

1. Download this file, attach it to a new Claude conversation
2. Send: "Please follow the instructions in the attached file."
3. When prompted for a URL or query, Claude will ask you to provide the API key inline.

---

*web-research · Part of the quick-deck skill suite · Free forever · MIT*
