---
schemaId: urn:gala:schema:content-frontmatter:2.0.0
schemaVersion: 2.0.0
id: 01a1a2dd-1fa3-7a61-bba1-615ae95162e3
kind: article
title: Optimize Your AI - Quantization Explained
description: I explain what Q2, Q4 and Q8 mean in Ollama, how K-quants work, and how context quantization can save gigabytes of RAM when you run big models on basic hardware.
language: en-US
authors:
- 01a1a2de-b098-79bd-b2d6-f17752d51779
tags:
- quantization
- ollama
- local-ai
- flash-attention
- kv-cache
status: published
createdAt: '2024-12-28T12:00:00.000Z'
publishedAt: '2024-12-28T12:00:00.000Z'
slug: optimize-your-ai-quantization-explained
hero:
  path: assets/content-optimize-your-ai-quantization-explained-hero.jpg
  alt: Thumbnail of the video "Optimize Your AI - Quantization Explained"
  role: informative
redirects: []
extensions: {}
---
*Adapted from the video [Optimize Your AI - Quantization Explained](https://www.youtube.com/watch?v=K75j8MkwgJ0)*

> **TL;DR:** Quantization lets you run big AI models on modest hardware by storing the model's numbers with less precision. I explain Q2, Q4 and Q8 with a ruler analogy, show why the K variants are smarter, and demo context quantization in Ollama. Turning on flash attention and a Q8 KV cache cut memory use for a maxed-out context from about 15 GB to about 5 GB. My advice is to start with a Q4 K M model, then move to Q8 or down to Q2 depending on quality.

This tiny laptop is running a 70 billion parameter AI model right now. Shouldn't that model be too big to fit? The math doesn't add up unless you know about quantization.

In the next 10 minutes or so you'll learn how to run massive AI models on sort of basic hardware, what Q2, Q4 and Q8 actually mean (it's simpler than you think), and which quantization is right for your projects. I'll also show you a brand new trick with context quantization that could save you gigabytes of RAM.

## Why models need so much memory

*[Watch from 1:03](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=63s)*

Here's what nobody tells you about AI models: they're just giant collections of huge numbers, billions of them, and each one needs to be stored with incredible precision. Normally these numbers are stored with 32-bit precision. It's like having a ruler that can measure down to microscopic levels. Sounds great, but all that precision comes at a pretty massive cost.

Quick math: take a 7 billion parameter model and multiply each parameter by four bytes, and you need 28 GB just to store it. That's more RAM than a lot of gaming PCs have. You're looking at $2,000 to $3,000 for a GPU just to run one model, and this model won't even fit in one of those.

## Quantization as different rulers

*[Watch from 2:02](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=122s)*

Think of quantization like choosing different rulers. The full model uses millimeters. Q8 measures in centimeters: less precise, but still pretty accurate. With Q4 we've got a marker every 5 cm or so, but look how much extra space we have. Q2 is the extreme option, like measuring with a random stick from your yard, but sometimes that's all you need.

My favorite way to explain this is a mail room, where instead of postcards and packages you're storing numbers. In the original unquantized model every number gets its own custom-size mailbox. It's perfect organization, but imagine how much space that takes up. Full 32-bit precision is like an infinite mail room where every tiny value gets its own special spot.

Regular Q4 quantization is like having just 16 mailboxes for all your numbers. Every number has to pick the closest available slot. It works, but it's not very smart, especially when you have one bunch of really tiny numbers and another bunch of really huge ones.

## K-quants

*[Watch from 3:17](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=197s)*

It gets better. See those models tagged with K in Ollama? They use something called K-quants, and they are brilliant. Instead of one rigid system, K-quants create multiple specialized mail rooms. Small numbers get their own precise area and big numbers get their own space. It's like having a smart assistant organizing everything perfectly.

The different sizes you see, KS, KM and KL, for small, medium and large, are different levels of detail in the notes kept in the mail room:

- **Small** keeps brief notes.
- **Medium** keeps more detailed records.
- **Large** maintains very precise information about how everything was arranged.

This means areas with mostly small numbers get more precise small slots, while areas with bigger numbers get appropriately sized spaces. It's a mail room that adapts to what you're storing rather than forcing everything into the same size box.

## Context quantization

*[Watch from 4:38](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=278s)*

Ollama just dropped a game-changing feature: context quantization. We've been talking about making the models smaller, but there's another memory hog, your conversation history. A year ago this wasn't a big deal, because models could only remember 2,000 to 8,000 tokens of conversation. Now we're talking about models that can remember entire books worth of conversation, 128k or 128,000 or so tokens or more. All that history eats up RAM, and context quantization fixes this.

It's super simple to enable. First turn on flash attention by setting ollama flash attention to true, then set the cache type with ollama KV cache type.

## Demo: how much memory it saves

*[Watch from 5:37](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=337s)*

For this demo I'm using Qwen 2.5, a 7 billion parameter model. Out of the box it uses Q4 KM quantization and takes up 4.7 GB on disk. First let's max out the context size. By default Ollama gives us 2K tokens, but Qwen can handle 32k, or 32,768.

To set that up, go to ollama.com, find the model, and run the command shown there. Then type **/set parameter num_ctx 32768** and hit enter, type **save** followed by whatever name you want, such as Qwen 2.5 Max, and hit enter. Finally quit Ollama. If you run **ollama ls** you should see the new model in your list.

I watched the output from asitop, a similar tool to nvtop on Linux but for macOS. Before running the model, memory usage hangs around 25 gigs. The Mac uses unified memory, so that's the total used by the system and the GPU. Once I run it, usage climbs to around 40.9 GB, so the model with a 32k context uses about 15 GB.

Next I quit ollama serve and ran it again with flash attention enabled. I'm doing this at the command line to easily switch back and forth between settings rather than setting up the environment variables the correct way. With the same question, memory tops out at 33.7 GB. That's a massive drop from 40.9 GB, a savings of about 7 gigs.

Then I set the KV cache quantization to Q8. Now it only climbs to 30.6 GB, which means the maxed-out context is taking closer to 5 gigs.

For comparison, I went back to the original model with the default context size of 2K and no flash attention. Memory goes from 28.5 to 30.3, so a normal context size needs less than 2 GB. The maxed-out context needs 15 gigs, and context quantization set to Q8 saves us 10 gigs, bringing it down to only 5 gigs.

Not every model benefits in the same way, though. I saw one model, a brand new one by IBM, take less memory with flash attention on but more memory with KV cache quantization set to Q8 than without flash attention at all. So flash attention and context quantization aren't always guaranteed to help.

## Picking the right quantization

*[Watch from 9:03](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=543s)*

In general it's best to start with one of the Q4 models, either Q4 or even Q4 KM, which is what Ollama seems to be defaulting to these days. If you notice issues with generation quality, you can move up to Q8 or even fp16. If it seems to work well, you can try dropping further down to Q2. It's amazing how often a Q2 model works just as well for most tasks for most people, and the memory usage is so much lower. Combine that with a quantized context and you're getting a lot of power on a machine with even the most limited memory.

## The game plan

*[Watch from 9:46](https://www.youtube.com/watch?v=K75j8MkwgJ0&t=586s)*

Whether you're running AI on a Raspberry Pi or a beast of a workstation, here's the plan:

1. **Start simple.** Begin with a Q4 KM model and enable flash attention.
2. **Test your specific use case.**
3. **Optimize.** If it's running smooth, try Q2. If it's having issues, move to Q8.
4. **Need more context?** Try playing with Q8 KV cache quantization.

These tricks can turn a large model into something that runs on a mostly normal laptop. That's not just theory; you saw it happen. Download an Ollama Q4 model, enable flash attention, test with your use case, and experiment with lower quantization. The perfect setup isn't about using the highest settings, it's about finding what works for your specific needs.

*[Watch the full video on YouTube](https://www.youtube.com/watch?v=K75j8MkwgJ0)*
