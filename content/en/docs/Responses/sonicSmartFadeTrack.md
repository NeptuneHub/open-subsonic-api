---
title: "sonicSmartFadeTrack"
linkTitle: "sonicSmartFadeTrack [OS]"
OpenSubsonic:
- Addition
description: >
  Normalization gain and playback curves for one song in a Smart Fade.
---

The `from` object inside [`sonicSmartFadeTransition`](../sonicsmartfadetransition), controlling the outgoing song. For example, `gain` below is accessed as `from.gain`. The incoming [`sonicSmartFadeIncomingTrack`](../sonicsmartfadeincomingtrack) includes the same fields under `to`, such as `to.gain`, plus entry and continuation positions.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
  "gain": 0.4728,
  "speed": [1.0, 1.0, 1.0, 1.0, 1.0],
  "volume": [1.0, 0.8536, 0.5, 0.1464, 0.0],
  "highDb": [0.0, 0.0, 0.0, 0.0, 0.0],
  "midDb": [0.0, 0.0, 0.0, 0.0, 0.0],
  "bassDb": [0.0, 0.0, 0.0, 0.0, 0.0]
}
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `gain` | `number` | **Yes** | **Yes** | Nonnegative linear normalization multiplier for the whole song, relative to decoded PCM audio. 1 means unchanged; 0.5 halves sample amplitudes. |
| `speed` | Array of `number` | **Yes** | **Yes** | Positive playback-rate multipliers. 1 is normal speed; 1.02 is 2% faster. Pitch changes with speed; do not apply pitch-preserving time stretch. |
| `volume` | Array of `number` | **Yes** | **Yes** | Nonnegative linear fader multipliers, applied on top of this song's `gain`. May exceed 1 for level compensation; MUST NOT be clamped to 1. |
| `highDb` | Array of `number` | **Yes** | **Yes** | High-shelf gain in dB at each point; 0 is flat, negative values attenuate, positive values boost. |
| `midDb` | Array of `number` | **Yes** | **Yes** | Peaking filter gain in dB at each point. |
| `bassDb` | Array of `number` | **Yes** | **Yes** | Low-shelf gain in dB at each point. For the incoming song, ignore `to.bassDb` when `to.highpassHz` is present. |

All curves are arrays of finite numbers with the same sample count. Their timing and the gain's relationship to ReplayGain are defined in [`sonicSmartFadeTransition`](../sonicsmartfadetransition#scheduling-and-loudness).
