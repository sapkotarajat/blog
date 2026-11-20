---
title: "Why I stopped posting reels through the Meta API: 1–4 plays vs 39–247"
date: 2026-11-20T09:00:00+05:45
description: "My Facebook reels posted through the Graph API got 1 to 4 plays each. The same kind of reels scheduled in Meta Business Suite got 39 to 247. This is my own observation, not a proven Meta rule, and here is what I changed."
categories: ["tech"]
tags: ["meta", "facebook", "instagram", "automation", "building in public"]
slug: "meta-api-vs-business-suite-reach"
ShowToc: true
TocOpen: false
draft: false
---

I like automation. I built a system called Studio that posts my videos for me: YouTube, Facebook and Instagram, on a schedule, from one dashboard. For Facebook and Instagram it used Meta's Graph API, the official way for code to post.

On 15 November 2026 I turned that part off. Now every Facebook and Instagram post goes through Meta Business Suite, by hand or with a browser, and never through the API.

This post explains why, and I want to be careful: this is what I saw on my own pages. It is not a rule that Meta has published, and I cannot prove the cause.

## What I saw

My Nepali Facebook Page had two periods that were easy to compare.

| Period | How the reels were posted | Facebook plays per reel |
|---|---|---|
| 3–10 November 2026 | Graph API (from Studio) | 1 to 4 |
| 11–14 November 2026 | Scheduled in Meta Business Suite | 39 to 247 |

The worst Business Suite reel did about ten times better than the best API reel.

My English page showed the same direction, but smaller. Its reels went from a range of 1 to 23 plays to a range of 3 to 48 after the switch.

Two pages, same direction. That was enough for me to stop using the API for these posts.

## What this does not prove

I want to list the problems with my own comparison before anyone else does.

- **Different reels.** The API reels and the Suite reels were not the same videos. Some topics are just better than others.
- **Different weeks.** The Suite reels came after the API reels. A page can grow on its own over time, so later posts may do better anyway.
- **Small pages.** Both pages were very new and very small. On a small page, one share can change everything.
- **Small numbers.** A handful of reels per period, not hundreds.
- **No A/B test.** I did not post the same video both ways at the same time. I could not, without posting it twice.
- **Facebook plays only.** The numbers above are Facebook plays. I did not have the same clean split for Instagram.

So the honest version is: **on my pages, in those two weeks, reels posted through the API got almost no plays, and reels scheduled in Business Suite got real plays.** I don't know why.

## Some guesses (only guesses)

I can't check the real reason. Here are two things in my own setup that could matter:

- **The flow is different.** In Business Suite, I post to the Page's Facebook and its Instagram together, upload a custom cover, and wait for the copyright check to say it is safe to publish before scheduling. I have not checked whether my API posts went through all of the same steps.
- **It's the path Meta's own tools use.** Maybe posts made in Meta's own app look more "normal" than posts from a small third-party app. That is a guess, not something I have seen confirmed.

If you know of a clear, official reason, I would like to read it.

## What I changed

1. **YouTube stays automated.** Studio still uploads and schedules YouTube Shorts through the YouTube API. Those jobs now post to YouTube only. I switched 21 scheduled jobs (15–29 November) to YouTube-only on the same day.
2. **Facebook and Instagram go through Business Suite.** Studio keeps a small queue: the video, the cover, the caption and the time. Then each reel is scheduled in Business Suite, one at a time, on both the Facebook Page and its Instagram account.
3. **A written checklist.** I wrote the steps down so they are the same every time: open the composer, pick the page, tick Facebook and Instagram, upload the video and cover, check the caption word for word (Nepali text can get garbled), wait for the copyright check, schedule, record it in the queue.
4. **Schedule only a few days ahead.** I queue two to three days at a time, so I can react to what worked.

## The cost of doing it this way

This choice is not free.

The API was "post and forget". Business Suite is clicking. I first tried letting an AI agent click through Business Suite in a browser for me. It worked, but it was expensive: six reels used about 368,000 tokens. So for a while I scheduled the posts by hand once a day. Now the AI posts single-account reels in the browser again, and I am asked first before any big run across many accounts, because those burn a lot of usage.

So I traded convenience and some AI budget for reach. On my numbers, it is worth it. For you, it depends on how much reach your API posts are getting.

## What happened after

After the switch, my Nepali Instagram reach for the last 7 days went from 568 to 5,868 between the 13 and 20 November snapshots. My Nepali Facebook views for the last 7 days went from 123 to 1,666.

I want to be careful here too. In the same week I also started posting short talking clips of myself, and those did much better than my older explainer videos. So I cannot give all of that growth to Business Suite. Two things changed at once.

## If you post with the Meta API

I am not telling you to stop. I am telling you to check.

1. Look at plays per reel for your API posts, over at least a week.
2. Schedule a few similar reels in Business Suite.
3. Compare. Write down the dates, so you can't fool yourself later.

If your API posts are doing fine, keep them. Mine were not, so I moved. It is my observation from my own pages, and I will update this post if the numbers change.
