---
name: youtube-fintuber-digest
description: Summarize new videos in Sahan's YouTube fintuber playlist (stock market / investing channels) by pulling each video's transcript and writing a digest. Use when he asks to check his playlist, get video summaries, or a fintuber digest.
---

# YouTube fintuber digest

Sahan follows a growing list of stock-market/investing YouTubers ("fintubers"). Instead of watching every video, he adds new uploads to a YouTube playlist and wants a text digest of the key points instead.

Default playlist: `https://www.youtube.com/playlist?list=PLS5pnaglAyzc` (named "summerize_investing", public). If he names a different playlist or link in his request, use that instead.

This is an **on-demand** workflow, not a scheduled one — only run it when he actually asks ("check my playlist", "summarize my fintubers", "what's new in my playlist", etc.). Do not set up a recurring/scheduled task for this unless he explicitly asks for automatic daily runs.

## Method: browser, not APIs or scripts

Do NOT try to fetch the playlist or transcripts via bash/curl/yt-dlp/youtube-transcript-api or WebFetch — YouTube blocks or rate-limits those paths from this environment. The reliable path is browser automation running on Sahan's own device:

- Prefer the Claude in Chrome tools (`mcp__claude-in-chrome__*`) if connected (check with `tabs_context_mcp`).
- Otherwise use the built-in browser pane (`mcp__remote-devices__Claude_Browser__*`). If it says access to youtube.com isn't allowed yet, call `request_access` with scope "site" (this only needs approval once per computer — it was already granted once).

## Steps

1. Open the playlist URL. Use `get_page_text` (or `find`) to list every video currently in it: title, channel, and its `/watch?v=...` link.
2. For each video:
   - Navigate to its URL.
   - **Immediately silence the player** — YouTube autoplays with sound, and the transcript doesn't need playback. Do this right after navigation, before anything else:
     - Preferred: run JavaScript on the page (e.g. Claude in Chrome's `javascript_tool`): `document.querySelectorAll('video').forEach(v => { v.muted = true; v.pause(); });` The transcript panel works fine with the video paused.
     - If JavaScript isn't available: click the video player once to focus it, then press `k` (pause). Only press `m` if a screenshot shows the speaker icon is *not* already muted — `m` toggles, so pressing it blindly can unmute.
     - If playback resumes later (e.g. after clicking in the page), pause it again the same way.
   - Click the `...more` link under the description to expand it (needed before the transcript button is reachable — it's often present in the accessibility tree but not clickable until expanded).
   - Scroll down and click "Show transcript" (a screenshot is the most reliable way to find its on-screen position if `find`/ref-based clicks report the element as outside the viewport — this happens because YouTube keeps a hidden duplicate node).
   - Call `get_page_text` again to extract the transcript (it comes out timestamped, often broken into chapters if the creator added them).
3. Read each transcript in full yourself and write a per-video summary. **Be thorough: extract every substantive point the creator makes**, not just the headline thesis. The goal is that Sahan doesn't need to watch the video. Capture:
   - **Stocks/tickers** discussed and the main thesis or call on each.
   - **News and updates**: earnings, guidance, deals, partnerships, product launches, regulatory/legal news, management changes, analyst upgrades/downgrades.
   - **Market moves**: price action, notable gains/drops, sector rotation, index/macro moves (rates, Fed, inflation data, etc.) and the reasons the creator gives for them.
   - **Predictions and price targets**: the creator's own and any they cite, with timeframes and conditions where given.
   - **Supporting evidence**: financial figures, valuation metrics, catalysts, risks.
   - **Overall sentiment** (bullish/bearish/neutral) and any trades or positions the creator says they made.
   - Flag when something is presented as opinion or speculation rather than fact.

   **Skip filler and repetition**: sponsor reads, channel/membership/Patreon plugs, "like and subscribe", intros/outros, generic disclaimers, and the recurring talking points or personal backstory a creator repeats in most videos (e.g. their long-term philosophy or a thesis they restate without anything new). Only mention a recurring point if something about it changed or new evidence was given.
4. Present the digest as a chat reply (this is the agreed delivery method) — one section per video, most recent upload first. No file or artifact needed unless he asks for one.
5. Close any browser tabs you opened when done, unless he asked to see the page.

## Notes from past runs

- Some videos show "Subtitles/closed captions unavailable" right after the page loads even though captions exist — this can be stale; recheck after the page/player settles, or just proceed to the transcript panel directly since it doesn't depend on that button.
- The video title on screen doesn't always match its actual content (e.g. a video titled about one stock turned out to be a full analysis of a different one) — always summarize what's actually said, not just the title.
