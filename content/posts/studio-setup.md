---
title: "The full setup: how an AI studio runs my two YouTube channels from a Mac in Nepal"
date: 2026-11-01T19:30:00+05:45
description: "Every piece of the self-hosted studio behind my channels: the month file, the renderer, the cloned voice, the scheduler, the phone, and the one rule that keeps me in charge. The write-up I promised for 'comment STUDIO'."
categories: ["tech"]
tags: ["ai", "automation", "youtube", "building in public", "remotion", "fastapi", "claude code"]
slug: "studio-setup"
ShowToc: true
TocOpen: true
draft: false
---

You commented STUDIO. Here is the whole thing, with the parts named, what each one does, and what it cost. Nothing here is a product you can install; it is my own setup, built with Claude Code over a few weeks, and I describe it so you can build your own version of the parts you need.

## What it is in one paragraph

A local web app on my Mac in Panauti. It reads a month plan, writes each reel's script into a JSON file, voices it with a clone of my voice, renders a vertical video, uploads it to YouTube as a scheduled Short, posts it to Facebook and Instagram at the right minute, pulls the comments back every half hour, drafts replies in my voice, and sends my phone a notification whenever something needs me. Nothing goes out without my tick.

## The pieces

| Piece | What it is | What it does |
|---|---|---|
| Studio | Python 3.12, FastAPI, Jinja templates, htmx, SQLite | The dashboard. Month page, jobs page, comments inbox, keys, health. Installed on my phone as a web app. |
| Scheduler | APScheduler inside the same process | One row per job in SQLite. Reels, comments, backups, reminders. Every post is a one-off job with its own cron line. |
| Scripts | One JSON file per reel | Scenes with a headline and the narration, plus sources. The renderer and the caption builder both read it. |
| Voice | ElevenLabs, a clone of my voice | Each scene's narration becomes an MP3. The audio is cached, so re-rendering a reel does not spend credits again. |
| Renderer | Remotion (React) at 1080×1920, 30 fps | Draws the captions, the cards and the stage board in time with the narration, then renders through Chrome. |
| Cover | A frame picked by the script | The thumbnail YouTube and the feeds show. |
| YouTube | YouTube Data API v3 | Uploads the reel as a private video with a publish time, so the Short goes live at 12:02 or 19:30 even if the Mac is asleep. |
| Facebook, Instagram | Meta Graph API | Posts the same file as a reel at the same minute. Instagram needs a public URL to fetch the file, so a temporary one is opened only while posting. |
| TikTok | By hand | TikTok allows scheduling ten days ahead, so that is one sitting every ten days. The studio drops the file and the caption in a folder. |
| Comments | YouTube API and Meta API | Every 30 minutes: new comments into an inbox, an AI draft in my voice, and a notification. Keyword replies (like the one that brought you here) go out by themselves. |
| Phone | Web push notifications and Tailscale | The dashboard is reachable only inside my private network. The phone gets a push when a post lands, fails, or waits for my tick. |
| Backups | A nightly job | Database, keys and tokens, encrypted, to a second disk. |

## The month file

One JSON file holds the month: an entry per post with a date, a time, a channel, the reel id, the pillar, the title, the hook, and a status. The status moves from `planned` to `scripted` to `rendered` to `approved` to `queued` to `posted`. The Month page is just that file drawn as a calendar. When I tick a day and press Push, the studio creates one job per post with a cron line for that minute.

I wrote more about this in the month-plan post, with the template.

## The rule that matters

Nothing posts without my tick. The AI drafts scripts, captions, replies and LinkedIn posts; a human (me) approves each one. That is not a limitation of the tools. It is the design. The day I let it post on its own is the day the channel stops being mine.

## What a day looks like

- 05:30 analytics pulled for both channels.
- 07:30 a LinkedIn draft waits for me.
- 12:02 the Nepali reel goes live on YouTube, Facebook and Instagram.
- 19:30 the English reel.
- Every 30 minutes, comments.
- Every 10 minutes, the inbox folder: a screen recording I drop there becomes a draft Short.
- 02:30 backup.

## What it cost

- The Mac was already on the desk.
- ElevenLabs Starter for the voice clone, on their first-month offer, then the monthly Starter price. The clone is the only thing I pay for every month.
- Gemini for drafts, inside the free tier most days.
- Claude Code to build it, on the plan I already pay for.
- Remotion is free for a solo creator.

Rough count, mine: about twenty hours a week I used to spend editing and scheduling, now spent recording.

## What broke, honestly

- YouTube's daily upload limit stopped a batch of pre-uploads halfway. The studio retries the next day; I learned to pre-upload in smaller batches.
- Instagram cannot fetch a file from a private network. The fix was a temporary public link that opens only while posting.
- A cover job once hung for four days and blocked restarts. Every job now has a maximum runtime.
- The first version of my channels was fully automatic and got 35 views in a month. The studio became useful only after I put myself back in the loop.

## If you build one

Build it in this order and stop when it is enough:

1. One script file format. Scenes, narration, sources.
2. One renderer that turns that file into a video. Remotion if you know React; ffmpeg with image overlays if you do not.
3. A scheduler that uploads with a publish time. YouTube's API does this for you.
4. A month file and a page that shows it.
5. The inbox for comments, last. It is the part that makes the channel feel alive, and the part you want a human on.

Questions in the comments on the reel, or on LinkedIn.
