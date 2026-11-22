---
title: "Sonic similarity"
linkTitle: "Sonic similarity"
OpenSubsonic:
- Extension
description: >
  Audio similarity, queue ordering, and Smart Fade transitions.
---

**OpenSubsonic version**: [1](../../opensubsonic-versions)

**OpenSubsonic extension name**: `sonicSimilarity` (As returned by [`getOpenSubsonicExtensions`](../../endpoints/getopensubsonicextensions))

## Version 1

This extension adds two new audio-similarity endpoints:

- [`findSonicPath`](../../endpoints/findsonicpath)
- [`getSonicSimilarTracks`](../../endpoints/getsonicsimilartracks)

These endpoints return a list of [`sonicMatch`](../../responses/sonicmatch) entries, each with a `Child` entry and a normalized similarity score.

## Version 2

This version adds two Smart Fade endpoints to version 1:

- [`getSonicSmartFade`](../../endpoints/getsonicsmartfade): Orders an existing queue and suggests entry and exit positions for each song.
- [`getSonicSmartFadeTransition`](../../endpoints/getsonicsmartfadetransition): Plans the transition between two songs, including playback rate, loudness, volume, and EQ automation.

Servers supporting this version MUST implement both endpoints and advertise `sonicSimilarity` versions `[1, 2]` via [`getOpenSubsonicExtensions`](../../endpoints/getopensubsonicextensions). Clients MUST check for version 2 before using these endpoints.

The server supplies a playback plan; the client fetches the audio using [`stream`](../../endpoints/stream) and performs the transition. The analysis and queue-ordering algorithms are implementation-defined.

[`getSonicSmartFade`](../../endpoints/getsonicsmartfade) orders the supplied song list and suggests approximate transition timing based on sonic analysis. [`getSonicSmartFadeTransition`](../../endpoints/getsonicsmartfadetransition) refines this timing for a pair of songs using BPM and voice analysis, and returns more detailed playback controls for the transition.
