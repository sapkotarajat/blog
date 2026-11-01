---
title: "The clipping system the gurus sell, built for free: the write-up with the code"
date: 2026-11-01T19:30:00+05:45
description: "How five Shorts came out of one nine-minute film with a Python edit list, ffmpeg and a scheduled YouTube upload. The real code, the real limits, and what it cost (a week of evenings, zero dollars)."
categories: ["tech"]
tags: ["ffmpeg", "python", "youtube shorts", "video", "automation", "claude code", "building in public"]
slug: "clipping-system"
ShowToc: true
TocOpen: true
draft: false
---
You commented CLIP. This is the system, with the code that matters. It is not a product. It cuts Shorts out of my own films in my own format, and that narrow scope is exactly why it works.

## What goes in, what comes out

In: one film already cut from phone clips (the Dashain film, nine minutes, sixty-five iPhone clips shot in HLG), plus the catalogue of those clips.

Out: five vertical Shorts with a title stamp, natural sound, an optional music track, loudness-normalised, uploaded to YouTube as scheduled videos in one command.

## Part 1: the edit list is Python

Every cut is a line. `clip()` takes the clip id, the in and out seconds, and a few options: a 9:16 window position for a landscape source (`"crop", x=0.62`), a slow-down (`s=0.5`), a dissolve into the next piece (`xf=0.5`), a fade, and which audio to use.

```python
# edl_dashain.py  (an edit list module for vlog_build3.py)
from vlog_build3 import clip, photo

REEL_TIKA = [
    clip(6120, 0.0, 5.0, "crop", x=0.5, title="tika_s"),
    clip(6011, 0.0, 10.0, "crop", x=0.62, xf=0.5),
    clip(6029, 0.0, 8.0, "crop", x=0.55),
    clip(6122, 6.0, 14.0, "crop", x=0.5),
    clip(6111, 2.0, 8.0),
    clip(6364, 88.0, 93.0, fade_out=1.0),
]

REEL_HIMAL = [
    clip(6389, 0.0, 4.6, title="himal"),
    clip(6390, 0.0, 14.0, xf=0.5),
    clip(6391, 6.0, 14.0, xf=0.5),
    clip(6385, 0.0, 3.0, fade_out=0.8),
]

def proj(out, items, **kw):
    d = dict(out=out, src=SRC, photos=PH, titles=TI, amb=AMB, bed=BED, items=items)
    d.update(kw)
    return d

PROJECTS = {
    "reel_tika":  proj("tall", REEL_TIKA,  bitrate="40M", music=MUSIC_REELS["reel_tika"],  lra=20, tp=-1.0, end_fade=0.3),
    "reel_himal": proj("tall", REEL_HIMAL, bitrate="40M", music=MUSIC_REELS["reel_himal"], lra=20, tp=-1.0, end_fade=0.3),
}
```

The signature behind it:

```python
def clip(c, i, o, mode="fit", s=1.0, y=0.5, x=0.5, title=None, audio=("own",),
         xf=0.0, fade_in=0, fade_out=0, lvl=None, trans="fade"):
    ...
```

- `mode="fit"` puts the source inside the frame with a blurred, darkened copy of itself behind it when the aspect differs.
- `mode="crop"` takes a 9:16 window out of a landscape clip at horizontal position `x` (0 is the left edge, 1 the right).
- `audio` is `("own",)` for the clip's sound, `("hp",)` for a high-passed version, `("mute",)`, or `("sub", other_clip, start)` to borrow sound from a quieter clip.
- `title="tika_s"` overlays a PNG title stamp by name.

Why Python and not a timeline: I can read it, diff it, copy a Short and change two numbers, and ask Claude to draft the first version from the catalogue.

## Part 2: ffmpeg cuts, grades and mixes

The builder renders one piece per line, then joins them.

```text
vlog_build3.py <edl.py> <project> [--dry] [--out DIR]
```

Per piece, in order:

1. Decode with VideoToolbox, then take the frames into software so iPhone portrait rotation is applied (hardware-only decode skips it).
2. `hwupload` and `scale_vt`: HLG BT.2020 to Rec.709, resized to 2160×3840.
3. Speed change and 30 fps.
4. The grade, then the optional PNG overlay.
5. Encode with `hevc_videotoolbox`, 10-bit, 40 Mbps for Shorts.

Dissolves are rendered as separate `xfade` pieces between a tail and a head, then everything is concatenated losslessly.

Sound is a numpy timeline, not a filter graph: each piece contributes its own, substituted, high-passed or muted audio; a room-tone bed at −44 dBFS runs under everything so cuts never go to digital silence; a music track can be RMS-levelled, ducked under speech and aligned so its natural ending lands on the last frame. Then a two-pass `loudnorm` to −16 LUFS with a −1 dBTP ceiling, and the mux.

`--remix` rebuilds only the audio from the saved edit list and re-muxes it onto the finished video in a minute or two, which is how a music change costs no re-render.

Two gotchas that cost me evenings: this ffmpeg has no `drawtext`, so all text is PNG overlays; and the `crop` filter evaluates its expressions once, so zooms use `scale=eval=frame` instead.

## Part 3: titles, covers and the upload

Title stamps and end cards are PNGs rendered with Pillow (Devanagari through raqm), one script per project, so the stamps match the film's look. The cover is a frame plus the same stamp. My scripted reels use Remotion for their captions and cards; for cut-from-footage Shorts the PNG route is simpler and good enough.

The upload is one call per Short, private with a publish time, so YouTube releases it at the slot:

```python
body = {
    "snippet": {"title": title, "description": caption, "categoryId": "22"},
    "status": {"privacyStatus": "private", "publishAt": publish_at,
               "selfDeclaredMadeForKids": False, "embeddable": True},
}
resp = yt.videos().insert(part="snippet,status", body=body,
                          media_body=MediaFileUpload(path, chunksize=-1, resumable=True)).execute()
```

Five Shorts, five publish times, one command. The id of each goes into a small JSON register so the studio knows what is already scheduled and never uploads twice.

## Honest cost and honest limit

- Cost: a week of evenings, zero dollars. ffmpeg, Python, Pillow and the YouTube API are free; the Mac was on the desk.
- Limit: it only works for my own format, phone clips I shot and catalogued, with a film I already cut. It will not find "viral moments" in a podcast. The expensive part was the film; the clipping is cheap because the film is good.

## If you build one

1. Catalogue your clips first (duration, orientation, HLG or not, whether anyone is speaking). You cannot write an edit list for footage you cannot see.
2. Make the edit list a file in your language. Resist the timeline.
3. Render pieces, then join. Re-render only what changed.
4. Normalise loudness last, always.

Questions in the comments on the reel, or on LinkedIn.
