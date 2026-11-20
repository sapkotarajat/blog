---
title: "I tested Nepali speech-to-text on my own voice: 11% vs 40% vs 51%"
date: 2026-11-20T09:20:00+05:45
description: "I ran one 68-second Nepali recording through ElevenLabs Scribe and two local Whisper models and counted the wrong characters. Here is the method, the result, the honest limits, and why I keep every transcript."
categories: ["tech"]
tags: ["ai", "speech to text", "nepali", "whisper", "elevenlabs"]
slug: "nepali-speech-to-text-test"
ShowToc: true
TocOpen: false
draft: false
---

Most AI tools say they "support Nepali". I wanted to know what that means for my own voice, in my own room, with the way I really talk: Nepali with English words mixed in, like "team", "client" and "AI".

So I ran a small test. Three engines, one recording, one score. This post is the method, the result, and the limits, because the limits matter as much as the numbers.

## The result first

I recorded 68 seconds of myself reading Nepali lines from a prompter. Then I gave the same audio to three engines and counted how many characters each one got wrong, compared to the text I was reading.

| Engine | Where it runs | Characters wrong |
|---|---|---|
| ElevenLabs Scribe v1 | Cloud | 11.4% |
| Whisper large-v3 | Locally, on my Mac | 40.1% |
| Whisper turbo | Locally, on my Mac | 51.1% |

Lower is better. Scribe was clearly the best on this test. The two Whisper models were far behind.

Speed on my Mac: Whisper large-v3 took 151 seconds to transcribe the 68-second clip. Whisper turbo took 27 seconds. Turbo is fast, but on this clip it got about half of the characters wrong.

## How I tested (simple version)

1. **The text.** I wrote the lines first. They are the opening of my weekly Nepali show: who I am, the question of the episode, and a few points. This file is the "truth".
2. **The audio.** I read the lines from a prompter and recorded them. One file, 68 seconds.
3. **The engines.** I sent the same file to ElevenLabs Scribe (cloud), and ran Whisper large-v3 and Whisper turbo on my own Mac.
4. **The score.** A small Python script compares each transcript with the truth, character by character. Before comparing, it removes spaces and punctuation, and it turns the digits "१२" and "१" into words, so that "१२" and "बाह्र" count as the same. Then it counts the smallest number of character changes needed to turn the transcript into the truth, and divides by the length of the truth (536 characters after cleaning).

This is called **character error rate**. I used characters, not words, because Nepali word boundaries are messy. One engine writes "एकजना", another writes "एक जना". By word, that is a full error. By character, it is almost nothing.

## What the mistakes looked like

The numbers are easier to understand when you see the text.

My first line was:

> १२ जनाको team लाई AI ले १ जना बनायो।

Scribe wrote:

> बाह्र जनाको team लाई AI ले एकजना बनायो,

That is basically right. It even kept "team" and "AI" in English, the way I said them.

Whisper large-v3 wrote:

> बारा जाना को टिमलाई एहाईले एक जाना बनायो,

You can still guess the meaning, but "बाह्र" became "बारा", "जना" became "जाना", and "AI" became "एहाई". Further down, "काठमाडौँ" became "काथमन्डो". The README note I wrote that day says it well: Whisper **drifts into Hindi spellings**. For a Nepali reader, that is not a small problem. It looks wrong at once.

## The honest limits

Please do not read this as "Scribe is 11% and Whisper is 40% for Nepali". It is not that kind of test.

- **One speaker.** Only my voice. My accent, my mic, my room.
- **Short sample.** 68 seconds. One bad sentence can move the score a lot.
- **I did not read word for word.** This is the big one. I was reading from a prompter, but I changed small things as I spoke. For example, the text said "एउटा विषयमा", but both Scribe and Whisper heard "एउटा नयाँ विषयमा". When two different engines hear the same extra word, I most likely said it. So part of every engine's "error" is really my error. Scribe's 11.4% is partly me, not the engine.
- **Clean reading, not real talk.** Reading from a prompter is easier than a normal conversation with laughs, pauses and two people talking at once.
- **One version of each engine, on one day (17 Nov 2026).** Engines change. ElevenLabs now shows newer Scribe versions than the v1 I tested.

So the fair reading is: on my own voice, for clean read Nepali, Scribe was much closer to usable, and plain Whisper was not.

## Why this matters for Nepali creators

If you make videos in Nepali, you need text from your audio all the time: captions, subtitles, a blog post from a video, a script you can search later.

What I learned from this test:

- **Test on your own voice before you pay.** Vendor pages show their own benchmarks. Your audio is different. A 1-minute test with text you already know is small work, and it tells you more than any marketing page.
- **Check the spelling, not only the meaning.** A transcript can be "understandable" and still look wrong to a Nepali reader. Captions in Hindi-style spelling make a Nepali video look careless.
- **Mixed language is normal.** We say "client", "team", "career" in the middle of Nepali sentences. A good engine should keep them as we say them. Scribe did that on my clip.
- **Free and local is not always enough.** Whisper runs on your own computer, with no upload and no bill. But on my clip the result needed so many fixes that it was not really saving time.

## Other Nepali options I found (20 Nov 2026)

I have not tested these yet. I only checked that each one lists Nepali on its own page today:

- **ElevenLabs Scribe**: the one I use now. [Nepali page](https://elevenlabs.io/speech-to-text/nepali).
- **Google Cloud Speech-to-Text**: its language table lists Nepali (`ne-NP`) for its Chirp models. [Supported languages](https://docs.cloud.google.com/speech-to-text/docs/speech-to-text-supported-languages).
- **Microsoft Azure Speech**: lists Nepali (`ne-NP`) with real-time and batch transcription. [Language support](https://learn.microsoft.com/azure/ai-services/speech-service/language-support?tabs=stt).
- **Deepgram**: says Nepali works in its Nova model family with `language=ne`. [Nepali page](https://deepgram.com/product/speech-to-text/nepali).
- **Whisper** (open source): free to run locally, but see my numbers above.

I am not listing prices here. Prices change often, and when I checked today, the numbers I saw did not agree with each other. Check the vendor's own pricing page on the day you sign up.

The next engines I want to run through the same test are Gemini and a model fine-tuned on Nepali. Same 68 seconds, same script, same score. That is the whole point of a fixed test: every new engine gets the same exam.

## What I do with the transcripts

This is the part I care about most.

Every Nepali transcript I pay for, I keep. The audio is cut into short pieces of 3 to 20 seconds, on the gaps between words. Each piece is saved with what the engine heard. Later, I read each line and fix it by hand. Only the lines I have checked count as clean data.

Why? Because one day I want to train **my own Nepali speech-to-text model**, tuned to my voice, on a separate PC, when I have the money for it.

Where I am today:

- 86 pieces so far, about 17 minutes of audio, all from one episode.
- 0 of them checked by hand yet.

That is very small. Published Nepali fine-tunes used around 150 to 165 hours of general Nepali speech. A model tuned to one voice can start from one of those and needs much less. My goal is 20 to 30 hours of checked lines before I even try.

So this will take a long time. But I am making Nepali videos every week anyway. If every paid transcript also becomes training data, the dataset grows without extra work. Only my own voice goes in. No other people's audio, no downloaded clips.

## If you want to run the same test

1. Write 1 minute of Nepali text you would really say.
2. Record yourself reading it. Try to read it exactly.
3. Run it through the engines you are thinking about.
4. Compare by character, after removing spaces and punctuation.
5. Look at the actual text too, not only the score.

Then you know which tool fits your voice, not someone else's.
