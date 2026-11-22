---
title: "sonicSmartFadeFilters"
linkTitle: "sonicSmartFadeFilters [OS]"
OpenSubsonic:
- Addition
description: >
  Shared filter configuration for a Smart Fade transition.
---

The `filters` object inside [`sonicSmartFadeTransition`](../sonicsmartfadetransition). Fields below belong to that object: for example, `highHz` is accessed as `filters.highHz`.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
  "highHz": 4000.0,
  "midHz": 1000.0,
  "midQ": 0.5,
  "bassHz": 180.0
}
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `highHz` | `number` | **Yes** | **Yes** | High-shelf corner frequency in Hz. Positive. |
| `midHz` | `number` | **Yes** | **Yes** | Peaking filter centre frequency in Hz. Positive. |
| `midQ` | `number` | **Yes** | **Yes** | Peaking filter quality factor Q (dimensionless). Positive. |
| `bassHz` | `number` | **Yes** | **Yes** | Low-shelf corner frequency in Hz. Positive. |

Use second-order biquad filters with the standard [audio EQ cookbook coefficients](https://www.w3.org/TR/audio-eq-cookbook/) (as used by Web Audio `BiquadFilterNode`): high and low shelves have slope S = 1, and the peaking filter uses `filters.midQ`. When `to.highpassHz` is present, replace the incoming song's low shelf with a second-order high-pass with Q = `10^(1/20)` ([Web Audio high-pass `Q`](https://www.w3.org/TR/webaudio/#dom-biquadfilternode-q) = 1 dB). This fixes the filter shapes without requiring additional fields. Clamp frequencies below the playback sample rate's Nyquist frequency if necessary.
