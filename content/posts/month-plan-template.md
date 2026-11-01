---
title: "How I plan a month of content with one JSON file (template included)"
date: 2026-11-01T19:30:00+05:45
description: "The one file that holds sixty reels across two channels for thirty days, the statuses that move each post from planned to posted, the tick that gates everything, and a template you can start from."
categories: ["tech"]
tags: ["automation", "content", "youtube", "json", "building in public", "scheduling"]
slug: "month-plan-template"
ShowToc: true
TocOpen: false
draft: false
---

You commented PLAN. Here is the file, the way the system reads it, and a template with three real entries from my own November.

## The idea

I plan once a month and decide once a day. The plan is one JSON file. Every post is one entry. The studio reads the file, draws it as a calendar, renders whatever has a script, and creates one scheduled job per post. My job each day is a tick.

## The file

Top level: the month, its start and end, the channels, a one-line niche and strategy, the rhythm (what posts when), and `items`, the list of posts.

Each item:

```json
{
  "id": "2026-11-01-nepal-reel",
  "date": "2026-11-01",
  "time": "12:02",
  "channel": "nepal",
  "kind": "reel",
  "reel": "nepal_decoded_02_salary",
  "pillar": "Nepal Decoded",
  "title": "तलब र भाडा किन मिल्दैन?",
  "hook": "काठमाडौंको औसत तलब र औसत कोठा भाडा जोड्दा हिसाब मिल्दैन।",
  "status": "queued",
  "theme": "saffron"
}
```

- `id` is unique and readable: date, channel, kind.
- `reel` points at the script file (`config/reels/<reel>.json`), which holds the scenes and sources.
- `pillar` is the series; it drives the colour and the caption's footer.
- `status` is the only field that changes after planning.

## The statuses

```
planned → scripted → rendered → approved → queued → posted
                                  ↘ skipped
```

- **planned**: a slot with a title. Nothing exists yet.
- **scripted**: the script JSON is written and the facts are checked against the sources.
- **rendered**: the video and its cover exist on disk.
- **approved**: I watched it and ticked it. This is the gate.
- **queued**: a job exists for that minute (and, for YouTube, the file is already uploaded with a publish time).
- **posted**: the file has a post id for each platform.

The Month page does not store status separately. It reads the files first (is there a script? a render? a YouTube id?) and only then applies anything I set by hand. That way the file never lies about what exists.

## From the file to the schedule

When I press Push, every approved item becomes a one-off job. The job's cron line is just the item's minute:

```python
def cron_for(item):
    y, m, d = item["date"].split("-")
    h, mi = item["time"].split(":")
    return f"{int(mi)} {int(h)} {int(d)} {int(m)} *"

def push(plan):
    for it in plan["items"]:
        if it["status"] != "approved":
            continue
        save_job(name=f"post_{it['reel']}",
                 cron=cron_for(it),
                 command=f"python post_reel.py {it['reel']}")
        it["status"] = "queued"
```

The job posts the file, writes the post ids back, and disables itself. If it fails, it shows as failed on the jobs page and my phone gets a push.

## The template

```json
{
  "month": "2026-11",
  "start": "2026-11-01",
  "end": "2026-11-30",
  "channels": ["dev", "nepal"],
  "niche": "Tech and AI, honestly, from Nepal",
  "strategy": "One reel per channel per day. Nepali at 12:02, English at 19:30. Friday is news. Last day is the recap.",
  "rhythm": {"nepal": "12:02", "dev": "19:30"},
  "items": [
    {"id": "2026-11-01-nepal-reel", "date": "2026-11-01", "time": "12:02", "channel": "nepal", "kind": "reel",
     "reel": "nepal_decoded_02_salary", "pillar": "Nepal Decoded", "title": "तलब र भाडा किन मिल्दैन?",
     "hook": "काठमाडौंको औसत तलब र औसत कोठा भाडा जोड्दा हिसाब मिल्दैन।", "status": "planned", "theme": "saffron"},
    {"id": "2026-11-01-dev-reel", "date": "2026-11-01", "time": "19:30", "channel": "dev", "kind": "reel",
     "reel": "en_ai_runs_my_studio", "pillar": "Build Log", "title": "I let AI run my YouTube studio",
     "hook": "I let Claude run my YouTube studio for a week. Here is what it actually did, and what broke.", "status": "planned", "theme": "saffron"},
    {"id": "2026-11-06-nepal-reel", "date": "2026-11-06", "time": "12:02", "channel": "nepal", "kind": "reel",
     "reel": "tech_week_01", "pillar": "यो हप्ता tech मा", "title": "This week's news", "hook": "", "status": "planned", "theme": "saffron"}
  ]
}
```

Copy it, change the month, delete my items, add yours. If you do not have a renderer or a scheduler yet, the file still works as a plan: a calendar view is one afternoon of code, and the statuses are worth having even if you move them by hand.

## Why one file and not a tool

Because I can read it, diff it, back it up, and ask an AI to fill a week of it from a list of topics. A tool with a database hides all four. When the month is over, the file is the record of what I planned and what actually went out.

Questions in the comments on the reel, or on LinkedIn.
