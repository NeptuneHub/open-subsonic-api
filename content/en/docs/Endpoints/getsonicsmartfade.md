---
title: "getSonicSmartFade"
linkTitle: "getSonicSmartFade [OS]"
OpenSubsonic:
- Addition
categories:
- Browsing
description: >
  Orders a queue for Smart Fade playback and suggests per-song cue positions.
---

`http://your-server/rest/getSonicSmartFade`

Returns the supplied songs in an order suitable for Smart Fade playback, based on audio analysis of their openings and endings. Requires [`sonicSimilarity`](../../extensions/sonicsimilarity) version 2.

The response MUST contain every requested song occurrence exactly once, including repeated IDs. The first song with available analysis stays first and songs without analysis are placed last, preserving their relative input order. Missing analysis MUST NOT cause a song to be dropped: return its `entry` and omit both cue fields. Unknown or inaccessible song IDs fail the request using the standard [error handling](../../api-reference#error-handling).

### Parameters

| Parameter | Req. | OpenS. | Default | Comment |
| --- | --- | --- | --- | --- |
| `id` | **Yes** | **Yes** | | The IDs of the songs in queue order. Use one `id` parameter for each occurrence; at least one is required. |

GET uses repeated query parameters. Servers supporting [`formPost`](../../extensions/formpost) also accept POST with `application/x-www-form-urlencoded` and repeated `id` fields.

### Example

{{% alert color="primary" %}} `http://your-server/rest/getSonicSmartFade.view?id=300000060&id=300000055&u=demo&p=demo&v=1.16.1&c=AwesomeClientName&f=json` {{% /alert %}}

### Result

A [`subsonic-response`](../../responses/subsonic-response) element with a top-level `sonicSmartFade` array on success. Each [`sonicSmartFadeEntry`](../../responses/sonicsmartfadeentry) wraps a standard [`Child`](../../responses/child) object and optional cue positions in seconds.

These positions are inexpensive suggestions, not a complete transition plan. `fadeOut` is the suggested end of the outgoing audio, not the start or duration of its fade. Pass adjacent songs' cues to [`getSonicSmartFadeTransition`](../getsonicsmartfadetransition) for precise timing and playback curves. See that endpoint's [fallback behavior](../getsonicsmartfadetransition#fallback) when no usable plan is available.

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
    "sonicSmartFade": [
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
      },
      {
        "entry": {
          "id": "300000055",
          "title": "Red&GreenSmoke",
          "isDir": false,
          "type": "music",
          "album": "Colorsmoke EP",
          "artist": "Synthetic",
          "duration": 400
        },
        "fadeIn": 12.5,
        "fadeOut": 400.0
      }
    ]
  }
}
{{< /tab >}}
{{< tab header="Subsonic" lang="json" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field | Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `sonicSmartFade` | Array of [`sonicSmartFadeEntry`](../../responses/sonicsmartfadeentry) | **Yes** | **Yes** | Songs in playback order, with optional entry and exit positions. |
