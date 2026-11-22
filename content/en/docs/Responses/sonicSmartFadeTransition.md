---
title: "sonicSmartFadeTransition"
linkTitle: "sonicSmartFadeTransition [OS]"
OpenSubsonic:
- Addition
description: >
  Timing and sampled audio controls for a Smart Fade between two songs.
---

Returned by [`getSonicSmartFadeTransition`](../../endpoints/getsonicsmartfadetransition). `from` controls the requested `fromSongId` (A), and `to` controls `toSongId` (B). `filters`, `from`, and `to` are nested objects, each with its own linked response type below. For example, the outgoing gain is `from.gain`, the incoming gain is `to.gain`, and the high-shelf frequency is `filters.highHz`.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
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
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `start` | `number` | **Yes** | **Yes** | Position in A, in seconds from its beginning, where the transition starts. Nonnegative. |
| `duration` | `number` | **Yes** | **Yes** | Elapsed playback seconds during which both songs overlap. Positive; independent of their changing playback rates. |
| `filters` | [`sonicSmartFadeFilters`](../sonicsmartfadefilters) | **Yes** | **Yes** | Filter configuration shared by both songs, such as `filters.highHz` and `filters.midQ`. |
| `from` | [`sonicSmartFadeTrack`](../sonicsmartfadetrack) | **Yes** | **Yes** | A's normalization gain and automation curves, such as `from.gain` and `from.volume`. |
| `to` | [`sonicSmartFadeIncomingTrack`](../sonicsmartfadeincomingtrack) | **Yes** | **Yes** | B's normalization gain and automation curves, plus `to.start`, `to.continueFrom`, and optional `to.highpassHz`. |

### Scheduling and loudness

All curves in both songs MUST contain the same number `N >= 2` of samples, including `to.highpassHz` when present. Sample `i` occurs at elapsed time `i * duration / (N - 1)` from the transition's start, for `i` from 0 to `N - 1`. Join values with straight lines in their given units: interpolate linear multipliers for `from.volume` and `to.volume`, playback-rate multipliers for `from.speed` and `to.speed`, dB for EQ gains, and Hz for a high-pass sweep. The last sample is at exactly `duration`. All curve clocks advance in elapsed playback time, regardless of the song playback rates.

1. Fetch the plan and both audio sources before the transition. Apply each song's `gain` throughout its playback, including outside the overlap. For the first song, obtain its outgoing plan before playback when possible; if its gain becomes available during playback, adjust smoothly. Subsequent songs inherit their gain from the preceding plan. The server MUST provide a consistent gain for a song across adjacent plans within the same playback session. This gain replaces client ReplayGain or other automatic loudness normalization; applying both would normalize twice. A common user volume may still be applied after mixing.
2. When A reaches `start`, start B from `to.start` and schedule all curves together. Each source plays its own audio at its `speed` curve through a low shelf (or B's high-pass), peaking filter, high shelf, fader `volume`, and normalization `gain`. In Web Audio, schedule curves with `AudioParam.setValueCurveAtTime(values, when, duration)`.
3. After `duration` elapsed seconds, stop A. Continue B alone from `to.continueFrom`, at normal speed, flat filters (bypass the high-pass), volume 1, and its existing `gain`. Preserve continuity when switching sources or resetting filters, for example with a short overlap of identical audio. The server MUST calculate `continueFrom` consistently with the integral of B's `speed` curve, allowing for numeric rounding.

Servers MUST fit the plan within the available audio of both songs, accounting for playback-rate changes. The volume and EQ curves already include the server's level compensation and peak control; clients MUST apply the supplied values together. Outside transitions, use normal speed, flat filters, and volume 1. A simple crossfade has constant `speed` curves of 1; its EQ curves may still compensate for the source material.

See the endpoint's [complete example](../../endpoints/getsonicsmartfadetransition#result) and [fallback behavior](../../endpoints/getsonicsmartfadetransition#fallback).
