---
title: "getSonicSmartFadeTransition"
linkTitle: "getSonicSmartFadeTransition [OS]"
OpenSubsonic:
- Addition
categories:
- Browsing
description: >
  Returns the timing and playback curves for a Smart Fade between two songs.
---

`http://your-server/rest/getSonicSmartFadeTransition`

Plans the transition from the currently playing song to the next song. Requires [`sonicSimilarity`](../../extensions/sonicsimilarity) version 2. The result includes playback-rate, volume, and EQ curves with loudness normalization, so the client can execute the plan without analyzing the audio.

Request the plan while the outgoing song plays, early enough to load the next song and schedule the transition. The returned `start` may precede the suggested `fadeOut` by several seconds.

### Parameters

| Parameter | Req. | OpenS. | Default | Comment |
| --- | --- | --- | --- | --- |
| `fromSongId` | **Yes** | **Yes** | | The ID of the currently playing song (A). |
| `toSongId` | **Yes** | **Yes** | | The ID of the next song (B). |
| `fadeOut` | **Yes** | **Yes** | | Suggested end position in A, in seconds from its beginning. Use A's `fadeOut` from [`getSonicSmartFade`](../getsonicsmartfade). |
| `fadeIn` | **Yes** | **Yes** | | Suggested entry position in B, in seconds from its beginning. Use B's `fadeIn` from [`getSonicSmartFade`](../getsonicsmartfade). |

Cue positions MUST be finite, nonnegative numbers within the corresponding songs, with `fadeOut > 0` and `fadeIn` before B's end. They are hints: the server may adjust them to find a suitable transition. Clients MUST use the returned positions when executing a plan.

GET uses query parameters. Servers supporting [`formPost`](../../extensions/formpost) also accept POST with `application/x-www-form-urlencoded`.

### Example

{{% alert color="primary" %}} `http://your-server/rest/getSonicSmartFadeTransition.view?fromSongId=300000060&toSongId=300000055&fadeOut=287.5&fadeIn=12.5&u=demo&p=demo&v=1.16.1&c=AwesomeClientName&f=json` {{% /alert %}}

### Result

A [`subsonic-response`](../../responses/subsonic-response) element with a `sonicSmartFadeTransition` object on success. The [`sonicSmartFadeTransition`](../../responses/sonicsmartfadetransition) response defines how to schedule every curve and configure the filters.

The example uses a short crossfade and five samples per curve for readability. Servers should sample finely enough to reproduce the computed curves with linear interpolation; clients MUST accept any common sample count of at least two. Crossfades can use gentle EQ as well as volume curves.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
  "subsonic-response": {
    "status": "ok",
    "version": "1.16.1",
    "type": "AwesomeServerName",
    "serverVersion": "0.1.3 (tag)",
    "openSubsonic": true,
    "sonicSmartFadeTransition": {
      "start": 283.5,
      "duration": 4.0,
      "filters": {
        "highHz": 4000.0,
        "midHz": 1000.0,
        "midQ": 0.5,
        "bassHz": 180.0
      },
      "from": {
        "gain": 0.4728,
        "speed": [1.0, 1.0, 1.0, 1.0, 1.0],
        "volume": [1.0, 0.8536, 0.5, 0.1464, 0.0],
        "highDb": [0.0, 0.0, 0.0, 0.0, 0.0],
        "midDb": [0.0, 0.0, 0.0, 0.0, 0.0],
        "bassDb": [0.0, 0.0, 0.0, 0.0, 0.0]
      },
      "to": {
        "gain": 0.5445,
        "start": 12.5,
        "speed": [1.0, 1.0, 1.0, 1.0, 1.0],
        "volume": [0.0, 0.1464, 0.5, 0.8536, 1.0],
        "highDb": [0.0, 0.0, 0.0, 0.0, 0.0],
        "midDb": [0.0, 0.0, 0.0, 0.0, 0.0],
        "bassDb": [0.0, 0.0, 0.0, 0.0, 0.0],
        "continueFrom": 16.5
      }
    }
  }
}
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `sonicSmartFadeTransition` | [`sonicSmartFadeTransition`](../../responses/sonicsmartfadetransition) | **Yes** | **Yes** | Timing, normalization gains, and sampled automation for the two requested songs. |

A pair without compatible beats SHOULD still receive a volume crossfade plan, with both `from.speed` and `to.speed` curves equal to 1. If no usable plan can be produced, return a standard failed [`subsonic-response`](../../responses/subsonic-response). Missing parameters use error code 10, unknown or inaccessible songs use code 70, and invalid cue positions or other planning failures use code 0. Application failures follow the standard [error handling](../../api-reference#error-handling), including when HTTP status is 200.

### Fallback

If the plan fails, arrives after its `start`, or requires controls the client cannot execute, the client can use a simple crossfade at the queue's cue positions from [`getSonicSmartFade`](../getsonicsmartfade). For example, fade A over up to the 10 seconds ending at its `fadeOut`, starting B at its `fadeIn`. Shorten the overlap to fit the available audio. For elapsed fraction `x` from 0 to 1, B's volume is `(1 - cos(pi * x)) / 2` and A's is `1 - B`. Use normal playback rates, flat filters, and the client's normal loudness handling. If A has no cues, play it to its end; if B has no cues, start it at 0.
