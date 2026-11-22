---
title: "getOpenSubsonicExtensions"
linkTitle: "getOpenSubsonicExtensions [OS]"
categories:
- System
OpenSubsonic:
- Addition
description: >
  List the OpenSubsonic extensions supported by this server.
---

`http://your-server/rest/getOpenSubsonicExtensions` OpenSubsonic version [1](../../opensubsonic-versions)

List the OpenSubsonic extensions supported by this server.

Each entry identifies an extension and its supported versions, rather than an individual API endpoint. A server returns an entry for each extension it implements.

### Parameters

Takes no extra parameters.

**Note**: Unlike all other APIs `getOpenSubsonicExtensions` **must** be publicly accessible.

### Example

{{% alert color="primary" %}} `http://your-server/rest/getOpenSubsonicExtensions.view?v=1.16.1&c=AwesomeClientName&f=json` {{% /alert %}}

### Result

A [`subsonic-response`](../../responses/subsonic-response) element with an `openSubsonicExtensions` array on success. Each entry lists an extension name and its supported versions.

This example shows a server advertising only [`Sonic similarity`](../../extensions/sonicsimilarity). The single `sonicSimilarity` entry with versions `[1, 2]` covers all four endpoints: [`findSonicPath`](../findsonicpath), [`getSonicSimilarTracks`](../getsonicsimilartracks), [`getSonicSmartFade`](../getsonicsmartfade), and [`getSonicSmartFadeTransition`](../getsonicsmartfadetransition). Servers supporting only the original two endpoints advertise `[1]`; servers supporting other extensions include additional entries for those extensions.

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
        "openSubsonicExtensions": [
            {
                "name": "sonicSimilarity",
                "versions": [
                    1,
                    2
                ]
            }
        ]
    }
}
{{< /tab >}}
{{< tab header="Subsonic" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field |  Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `openSubsonicExtensions` | Array of [`OpenSubsonicExtension`](../../responses/opensubsonicextension) | **Yes** | **Yes** | The extensions supported by the server, with their supported version numbers. |

{{% alert color="warning" title="OpenSubsonic" %}}
This is a new endpoint.
{{% /alert %}}
