---
title: "sonicSmartFadeIncomingTrack"
linkTitle: "sonicSmartFadeIncomingTrack [OS]"
OpenSubsonic:
- Addition
description: >
  Playback controls and cue positions for the incoming song in a Smart Fade.
---

The `to` object inside [`sonicSmartFadeTransition`](../sonicsmartfadetransition), controlling the incoming song. It includes all fields of [`sonicSmartFadeTrack`](../sonicsmartfadetrack): `to.gain`, `to.speed`, `to.volume`, `to.highDb`, `to.midDb`, and `to.bassDb`, plus the fields below.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
  "gain": 0.5445,
  "start": 12.5,
  "speed": [1.0, 1.0, 1.0, 1.0, 1.0],
  "volume": [0.0, 0.1464, 0.5, 0.8536, 1.0],
  "highDb": [0.0, 0.0, 0.0, 0.0, 0.0],
  "midDb": [0.0, 0.0, 0.0, 0.0, 0.0],
  "bassDb": [0.0, 0.0, 0.0, 0.0, 0.0],
  "continueFrom": 16.5
}
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `start` | `number` | **Yes** | **Yes** | Nonnegative position in the incoming song, in seconds from its beginning. `to.start` is where it starts at the same instant as the outgoing song reaches the transition's top-level `start`. |
| `continueFrom` | `number` | **Yes** | **Yes** | Position in the incoming song, in seconds, from which normal playback continues after the overlap. Accounts for `to.speed`; need not equal `to.start + duration`. |
| `highpassHz` | Array of `number` | No | **Yes** | Positive high-pass corner frequencies in Hz at each point, replacing the incoming low shelf during a rising transition. Omit when unused; do not send `null`. |

When `to.highpassHz` is present, ignore `to.bassDb`. The high-pass shape is defined in [`sonicSmartFadeFilters`](../sonicsmartfadefilters), and its sample count MUST match every other curve in the transition.
