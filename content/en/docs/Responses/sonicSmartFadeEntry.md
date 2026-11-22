---
title: "sonicSmartFadeEntry"
linkTitle: "sonicSmartFadeEntry [OS]"
OpenSubsonic:
- Addition
description: >
  A Child object with optional Smart Fade entry and exit positions.
---

A queue entry returned by [`getSonicSmartFade`](../../endpoints/getsonicsmartfade), with a standard [`Child`](../child) in `entry`. All cue positions are seconds from the beginning of the decoded song and may contain fractional seconds.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
  "entry": {
    "id": "300000060",
    "title": "BrownSmoke",
    "isDir": false,
    "type": "music",
    "album": "Colorsmoke EP",
    "artist": "Synthetic",
    "duration": 304
  },
  "fadeIn": 0.0,
  "fadeOut": 287.5
}
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `entry` | [`Child`](../child) | **Yes** | **Yes** | The song's standard metadata object, including its ID. |
| `fadeIn` | `number` | No | **Yes** | Suggested position where this song starts playing, in seconds; 0 for the first song when analyzed. This is a position, not a fade duration. |
| `fadeOut` | `number` | No | **Yes** | Suggested position where this song finishes handing over to the next, in seconds; the exact song length for the last analyzed song. This is a position, not a fade duration. |

When analysis is available, both cue fields MUST be present, finite, and satisfy `0 <= fadeIn < fadeOut <= song length`. When unavailable, both MUST be omitted, rather than set to `null` or 0. An entry without cues remains playable using normal playback. `Child.duration` provides the standard song duration; cue positions retain fractional-second precision independently of that integer metadata field.
