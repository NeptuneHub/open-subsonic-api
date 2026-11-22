---
title: "OpenSubsonicExtension"
linkTitle: "OpenSubsonicExtension [OS]"
description: >
  A supported OpenSubsonic API extension.
---

One entry in the `openSubsonicExtensions` array returned by [`getOpenSubsonicExtensions`](../../endpoints/getopensubsonicextensions). It identifies an extension and its supported versions; endpoint names are defined by that extension's specification.

{{< tabpane persist=false >}}
{{< tab header="**Example**:" disabled=true />}}
{{< tab header="OpenSubsonic" lang="json">}}
{
    "name": "sonicSimilarity",
    "versions": [
        1,
        2
    ]
}
{{< /tab >}}
{{< tab header="Subsonic" >}}
Does not exist.
{{< /tab >}}
{{< /tabpane >}}

| Field |  Type | Req. | OpenS. | Details |
| --- | --- | --- | --- | --- |
| `name` | `string` | **Yes** | **Yes** | The name of the extension, such as `sonicSimilarity`. |
| `versions` | Array of `int` | **Yes** | **Yes** | The supported versions of this extension's specification, rather than the Subsonic API version. |

{{% alert color="warning" title="OpenSubsonic" %}}
This is a new response type.
{{% /alert %}}
