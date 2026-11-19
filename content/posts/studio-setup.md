---
title: "The full setup: how an AI studio runs my two YouTube channels from a Mac in Nepal"
date: 2026-11-01T19:30:00+05:45
lastmod: 2026-11-19T22:30:00+05:45
description: "Every piece of the studio behind my channels: the month file, the renderer, the cloned voice, the scheduler, the 3 tools from the reel (vidIQ, Claude in Chrome, ElevenLabs), and the one rule that keeps me in charge. The write-up I promised for 'comment STUDIO'."
categories: ["tech"]
tags: ["ai", "automation", "youtube", "building in public", "remotion", "fastapi", "claude code", "mcp", "vidiq", "elevenlabs", "claude in chrome"]
slug: "studio-setup"
ShowToc: true
TocOpen: true
draft: false
# Comment-keyword resource: live at its URL, kept off the home page, lists, tags, RSS, search and sitemap.
resource: true
_build:
  list: never
sitemap:
  disable: true
searchHidden: true
robotsNoIndex: true
---

You commented STUDIO. Here is the whole thing, with the parts named, what each one does, and what it costs. Nothing here is a product you can install. It is my own setup, built with Claude Code, and I describe it so you can build your own version of the parts you need.

*Updated 19 November 2026: Facebook and Instagram now go through Meta Business Suite, comments that ask for this page are answered by DM, and there is a new section on the 3 tools from the reel.*

## What it is in one paragraph

A local web app on my Mac in Panauti, plus Claude Code. It reads a month plan, writes each reel's script into a JSON file, voices it with a clone of my voice, renders a vertical video, and uploads it to YouTube as a scheduled Short. Claude then schedules the same reel on Facebook and Instagram in Meta Business Suite, through my browser. Every half hour the studio pulls comments back, drafts replies in my voice, and sends my phone a notification when something needs me. Nothing goes out without my yes.

## The 3 tools from the reel

The reel says "3 MCP servers". To be exact: only one of them is an MCP connector. The other two are a browser extension and my own script. Here is each one, and what it really does for me.

### 1. vidIQ (MCP connector)

- **What it is:** vidIQ's MCP connector. I add it once in claude.ai under Settings → Connectors, and then Claude Code can call its tools.
- **What I use it for:** Claude studies a YouTube channel for me: top videos, growth, and what people search for. Two tools I use: `vidiq_channel_stats` (any channel, by handle or ID) and `vidiq_keyword_research`.
- **Good to know:** the calls use vidIQ credits from my account. When the credits run out, the calls fail until you top up.

### 2. Claude in Chrome (browser extension)

- **What it is:** a Chrome extension, not an MCP server. Claude works inside my real Chrome, where I am already signed in.
- **What I use it for:** scheduling reels on Facebook and Instagram. Claude opens Meta Business Suite in a new tab, picks the right Page, uploads the video and the cover, pastes the caption, checks the Nepali text, and sets the date and time. Then it records what it scheduled.
- **Why a browser and not the API:** in early November I posted reels to my Nepali Facebook Page through the Graph API. Each one got 1 to 4 Facebook plays. The reels I scheduled in Business Suite a few days later got 39 to 247. So since 15 November, Facebook and Instagram go only through Business Suite.
- **The rule:** Claude only schedules reels I have already approved. One post at a time.

### 3. ElevenLabs (through my own script)

- **What it is:** the ElevenLabs API, called by my own Python scripts. I don't use the ElevenLabs MCP connector; my scripts do the job and cost no extra tokens. I'm on the Starter plan.
- **Voice-over:** every voice-over reel, Nepali and English, is my own cloned voice. Each line of the script goes to the clone and comes back as audio. The audio is cached, so a re-render does not spend credits again. My API key sits on the studio's Keys page, never in the code.
- **Nepali speech to text (Scribe):** when I record myself talking, ElevenLabs Scribe turns the audio into text, with a start and end time for every word. That gives me the timeline for the edit and the captions.
- **Why ElevenLabs for this:** other models were not accurate in Nepali. I tested them on 68 seconds of lines I read from my prompter, and counted how much of the text came out different:
  - ElevenLabs Scribe: 11.4% different
  - Whisper large-v3, on my Mac: 40.1% different (it drifts into Hindi spellings)
  - Whisper turbo, on my Mac: 51.1% different

  Part of Scribe's gap is me not reading word for word. It is a small test, but the difference was clear.
- **What I keep:** every transcript is cut into short clips of my own voice and saved with its text. I check and fix the text by hand. One day, when I can afford the hardware, I want to train my own Nepali speech-to-text model on it.

### A tip: keep connectors off

Every connector you turn on loads its list of tools into every Claude session, even when you don't use it. That costs tokens. I keep only the few I use every day turned on, and switch the others on only for the task that needs them.

## The pieces

| Piece | What it is | What it does |
|---|---|---|
| Studio | Python 3.12, FastAPI, Jinja templates, htmx, SQLite | The dashboard. Month page, jobs page, comments inbox, keys, health. Installed on my phone as a web app. |
| Claude Code | Runs in the terminal on the same Mac | Writes and checks scripts, renders, and does the clicks in the browser. I review and approve. |
| Scheduler | APScheduler inside the same process | One row per job in SQLite. Reels, comments, backups, reminders. Every job has a maximum runtime. |
| Scripts | One JSON file per reel | Scenes with a headline and the narration, plus sources. The renderer and the caption builder both read it. |
| Voice | ElevenLabs API, a clone of my voice | Each line becomes audio, cached. See tool 3 above. |
| Transcripts | ElevenLabs Scribe | My recorded Nepali audio becomes text with word timings, for the timeline and captions. |
| Renderer | Remotion (React) at 1080×1920, 30 fps | Draws the captions, the cards and the logos in time with the narration, then renders through Chrome. |
| Cover | A frame picked by the script | The thumbnail YouTube and the feeds show. |
| YouTube | YouTube Data API v3 | Uploads the reel as a private video with a publish time, so the Short goes live at 12:02 or 19:30 even if the Mac is asleep. |
| Facebook, Instagram | Meta Business Suite, through Claude in Chrome | Schedules the same reel on both. See tool 2 above. |
| TikTok | By hand | I schedule it in TikTok itself. The studio puts the file and the caption ready for me. |
| Comments | YouTube API, plus reading Instagram and Facebook comments | Every 30 minutes: new comments into an inbox, an AI draft in my voice, and a notification. |
| Phone | Web push notifications and Tailscale | The dashboard is reachable only inside my private network. The phone gets a push when a post lands, fails, or waits for me. |
| Backups | A nightly job | Database, keys and tokens, encrypted, to a second disk. |

## How you got this page

When you comment STUDIO:

- **YouTube:** the studio replies with the link by itself.
- **Instagram and Facebook:** the studio does not post the link in public. The comment goes to a "DM to send" list in the studio, with the message ready to copy, and my phone gets a ping. I send you the DM in the app and tap "DM sent". Then the studio posts a short "check your inbox" reply under your comment.
- **TikTok:** there is no comment-to-DM there, so on TikTok I ask you to DM me STUDIO, and TikTok's own keyword auto-reply answers.

## The month file

One JSON file holds the month: an entry per post with a date, a time, a channel, the reel id, the pillar, the title, the hook, and a status. The status moves from `planned` to `scripted` to `rendered` to `approved` to `queued` to `posted`. The Month page is just that file drawn as a calendar. When I tick a day and press Push, the studio creates one job per post with a cron line for that minute.

I wrote more about this in the month-plan post, with the template.

## The rule that matters

Nothing posts without my yes. The AI drafts scripts, captions, replies and LinkedIn posts, and Claude can do the clicks. A human (me) approves each one. That is not a limit of the tools. It is the design. The day I let it post on its own is the day the channel stops being mine.

## What a day looks like

- 05:30 analytics pulled for the channels.
- 07:30 (weekdays) a LinkedIn draft waits for me.
- 12:02 the Nepali reel goes live.
- 19:30 the English reel.
- Every 30 minutes, comments.
- Every 10 minutes, the inbox folder: a screen recording I drop there becomes a draft Short.
- 02:30 backup.

## What it costs

- The Mac was already on the desk.
- ElevenLabs Starter plan, for the voice clone and Scribe transcripts.
- vidIQ credits for the MCP calls.
- Gemini for drafts, inside the free tier most days.
- Claude Code and Claude in Chrome, on the Claude plan I already pay for.
- Remotion is free for a solo creator.

Rough count, mine: about twenty hours a week I used to spend editing and scheduling, now spent recording.

## What broke, honestly

- YouTube's daily upload limit stopped a batch of pre-uploads halfway. The studio retries the next day; I learned to pre-upload in smaller batches.
- Reels I posted to Facebook through the API got almost no plays (1 to 4 each). The same kind of reels scheduled in Business Suite got 39 to 247. I moved Facebook and Instagram to Business Suite.
- In the old API setup, Instagram could not fetch a file from my private network. The fix then was a temporary public link that opened only while posting.
- Meta removed the permission my own app needed to send Facebook messages. So the studio can't send DMs itself; it gets the message ready and I send it.
- A cover job once hung for four days and blocked restarts. Every job now has a maximum runtime.
- The first version of my channels was fully automatic and got 35 views in a month. The studio became useful only after I put myself back in the loop.

## If you build one

Build it in this order and stop when it is enough:

1. One script file format. Scenes, narration, sources.
2. One renderer that turns that file into a video. Remotion if you know React; ffmpeg with image overlays if you do not.
3. A scheduler that uploads with a publish time. YouTube's API does this for you.
4. A month file and a page that shows it.
5. One research tool for Claude, like the vidIQ connector. Add more only when you need them.
6. The inbox for comments, last. It is the part that makes the channel feel alive, and the part you want a human on.

Questions in the comments on the reel, or on LinkedIn.
