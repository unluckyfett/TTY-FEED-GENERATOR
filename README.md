# TTY AFSK Feed Generator

A single‑page, browser‑only tool that polls one or more feeds (RSS/Atom, JSON Feed, or plain text), formats items for teleprinter output (ITA2/“Baudot”), and plays **continuous‑phase AFSK** audio at 45.45 baud to drive a TU or software decoder. It de‑duplicates items, can follow links for full‑text extraction, and wraps/normalizes text to fit classic TTY column widths.

---

## Highlights

- **Multi‑feed manager** with enable/disable, nicknames, and reordering.
- **CORS handling** with a configurable proxy prefix and robust fallbacks.
- **De‑duplication** by title/link, optional _Updates only_ seeding on start.
- **Preview pipeline:** sanitize → coerce to ITA2 → wrap to `N` columns → (optional) strip links for cleaner copy.
- **Bell support:** keyword‑triggered, ITA2 `FIGS‑BELL` with configurable repeats.
- **AFSK engine (WebAudio):** continuous‑phase FSK, 45.45 baud (configurable), 1.5 stop bits (configurable), default mark/space 2125/2295 Hz, output level control.
- **Full‑text (optional):** follows article links, extracts main content, and strips boilerplate/ads; HTML is sanitized and **images/embeds are removed** during extraction for TTY‑friendly text.
- **JSON/GeoJSON aware:** understands NWS active alerts and USGS earthquakes feeds directly.

---

## Quick Start

1. Open (https://unluckyfett.github.io/TTY-FEED-GENERATOR/)
2. In **Feed Source → Feed Manager**, click **Add** to enter one or more feed URLs (RSS/Atom/JSON/text). Give each a **Nickname** so batches can be tagged in the output.
3. (Optional) Toggle:
   - **Full text (follow links)** – fetch article pages and append extracted body text.
   - **Updates only (seed on start)** – mark current items as “seen,” then only send new ones.
   - **Strip links in Preview** – remove URLs from the text that’s sent to your TTY.
4. Set **Poll Interval** (default 60s) and click **Start**. New items appear in *Preview* and, if **Auto‑send** is on, AFSK audio plays automatically.
5. Use **Stop Audio** to halt playback; **Reset Seen** to clear de‑dup state.

> **CORS tips:** If a feed fails due to cross‑origin policies, set a trusted **CORS Proxy Prefix** (defaults to `https://corsproxy.io/?`).

### Sample Feeds

- BBC News: `https://feeds.bbci.co.uk/news/rss.xml`  
- NPR Top Stories: `https://feeds.npr.org/1001/rss.xml`  
- Hackaday: `https://hackaday.com/blog/feed/`  
- Ars Technica: `https://feeds.arstechnica.com/arstechnica/index`  
- TechCrunch: `https://techcrunch.com/feed/`  
- NASA Breaking News: `https://www.nasa.gov/rss/dyn/breaking_news.rss`  
- USGS Earthquakes (ATOM): `https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_hour.atom`  
- NWS Active Alerts (JSON): `https://api.weather.gov/alerts/active`

---

## Encoder & Text Controls

- **Line Length (cols):** Wrap to 20–132 columns (default **72**), typical of Model 28/32 workflows.
- **Map Unsupported → Nearest:** Coerce characters outside ITA2 into close stand‑ins (e.g., smart quotes → `'`, em‑dash → `-`, “@” → `&`, etc.).
- **Bell Trigger Keywords:** Comma‑separated words (e.g., `ALERT, WARNING, URGENT`). If present in a batch, the app injects **FIGS‑BELL** before the text; **Bell Repeat** plays it 1–5 times.
- **Sync & Shifts:** The app sends CR/LF in your chosen order and ensures **LTRS** state at the end of transmissions for safe recovery.
- **Strip links in Preview:** Removes `http(s)://…`, `www.…`, and bare domains from the outgoing text to reduce clutter on paper.

---

## AFSK Audio (WebAudio)

- **Baud:** 45.45 (configurable)  
- **Mark/Space:** 2125/2295 Hz (configurable)  
- **Stop Bits:** 1.0–2.0 (default 1.5)  
- **Level:** 0.05–1.0 (default 0.5)  
- **Signaling:** continuous‑phase, SPACE start bit, 5 data bits LSB‑first, MARK stop bit(s).  
- **Lead‑in / Tail‑out:** Includes a MARK lead‑in and brief tail‑out for TU stability.

Connect your computer’s audio output to your **terminal unit (TU)** or software TTY modem. Start low on level and increase until printing is reliable without slurring or double‑strikes.

---

## How It Works (Pipeline)

1. **Fetch** feed(s) with CORS‑friendly fallbacks.  
2. **Parse** RSS/Atom/JSON/HTML/plain‑text into a unified list of `{title, link, desc}`.  
3. **Post‑process**: sanitize HTML → **strip images/embeds** → trim boilerplate (site‑aware helpers) → optional full‑text fetch.  
4. **Format for TTY**: de‑duplicate, optional bell injection, coerce to ITA2, wrap lines to `N` columns, CR/LF sync.  
5. **Encode**: BAUDOT (ITA2) → bitstream (start, 5 data, stop bits).  
6. **Synthesize**: continuous‑phase AFSK and play via WebAudio.

---

## Hosting (GitHub Pages)

1. Create a public repo and add `index.html` (and any assets like `logo.png`).  
2. Push to `main` (or `gh-pages`).  
3. Enable **Settings → Pages** and choose the branch/root.  
4. Visit the Pages URL; the app runs entirely client‑side.

---

## Known Limitations

- **CORS**: Some sites block proxy fetches; try another trusted proxy or mirror.  
- **Full‑text extraction**: Heuristics can miss content or include odd bits on complex sites.  
- **Browser support**: Tested primarily in Chromium‑based browsers. Mobile browsers may suspend audio contexts.  
- **Audio path**: Hardware TUs vary; you may need attenuation or isolation to prevent hum/overdrive.

---

## Credits & License

- UI/logic by **Frankie Caswell**.  
- **License:** [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). See `index.html` footer for attribution details.


