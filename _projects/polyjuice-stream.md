---
title: Polyjuice Stream
project: Go / LLM infrastructure
activity_date: "2026-10-28"
thumbnail: /assets/images/projects/polyjuice-stream.svg
thumbnail_alt: Smiling lavender potion bottle, the Polyjuice Stream logo
summary: >-
  Like Polyjuice Potion in [Harry Potter](https://www.harrypotter.com/fact-file/plants-and-potions/polyjuice-potion), your CPU gets to play a GPU.
  Fake LLM replies. Real streams. No accelerator required.
links:
  - label: GitHub
    url: https://github.com/yisususu/polyjuice-stream
  - label: Design notes
    url: https://github.com/yisususu/polyjuice-stream/blob/main/docs/design-v0.1.md
---

![Polyjuice Stream: a smiling potion bottle and the tagline "A little potion for mock LLM output."]({{ '/assets/images/projects/polyjuice-stream-readme.svg' | relative_url }}){: width="900" height="270" .project-logo}

## Why I built it

Testing the systems around an LLM should not require running the LLM itself. Clients, gateways, routers, and dispatchers need responses with useful shapes and timing, even when no accelerator is available.

I built Polyjuice Stream to provide that stand-in. Named after Harry Potter's Polyjuice Potion, it gives simulated output the appearance of an LLM service. The disguise is the feature; the intelligence is strictly pretend.

## What it does

- **Streams or returns complete responses.** Test the same client with SSE streams or a single JSON response.
- **Generates or replays content.** Use seeded random text for repeatable runs, or cycle through recorded responses in a JSONL file.
- **Controls the pace.** Configure the first-response delay, interval between chunks, and output length to exercise different client and routing behaviours.
- **Serves multiple model profiles.** Give each model its own content source, timing, and replay cursor or random sequence behind one endpoint.

## Engineering choices

The implementation separates content generation, response timing, and HTTP delivery. That keeps the same content and timing rules available to both streaming and complete responses.

Configuration and replay data are checked before the server starts. Model profiles keep independent state, so selecting one model does not advance another model's replay cursor.

Content selection uses short critical sections; waiting and network writes happen outside those locks. Slow clients therefore do not hold the content-selection lock, and request cancellation stops the affected response.

The output units are simulated text chunks, not model tokens. This is a tool for testing the surrounding infrastructure; its timings are configured scenarios, not measurements of GPU inference performance.

The [README](https://github.com/yisususu/polyjuice-stream#readme) has the build instructions and examples. The [HTTP contract](https://github.com/yisususu/polyjuice-stream/blob/main/docs/http-contract.md) documents the supported API surface.
