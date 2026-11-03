---
schemaId: urn:gala:schema:content-frontmatter:2.0.0
schemaVersion: 2.0.0
id: 01a1a2dd-26ba-74bb-ac03-40137042f865
kind: article
title: Fine Tune a model with MLX for Ollama
description: 'Fine tuning is easier than the code-heavy tutorials make it look. I walk through three steps: build a dataset, run MLX fine tuning on Apple Silicon, and use the adapter in Ollama.'
language: en-US
authors:
- 01a1a2de-b098-79bd-b2d6-f17752d51779
tags:
- fine-tuning
- mlx
- ollama
- mistral
- apple-silicon
status: published
createdAt: '2024-08-30T12:00:00.000Z'
publishedAt: '2024-08-30T12:00:00.000Z'
slug: fine-tune-a-model-with-mlx-for-ollama
hero:
  path: assets/content-fine-tune-a-model-with-mlx-for-ollama-hero.jpg
  alt: Thumbnail of the video "Fine Tune a model with MLX for Ollama"
  role: informative
redirects: []
extensions: {}
---
*Adapted from the video [Fine Tune a model with MLX for Ollama](https://www.youtube.com/watch?v=3UQ7GY9hNwk)*

> **TL;DR:** Fine tuning changes how a model phrases its answers, not what it knows, and it is easier than most tutorials suggest. There are three steps: build a dataset of questions and answers in the model's template, saved as JSONL; run mlx\_lm.lora to train an adapter on Apple Silicon; then point an Ollama Modelfile at that adapter and create the new model. I used my own video scripts to teach Mistral my writing style. The result is not perfect, but it is far less scary than I expected.

AI models are amazing. What they know and how they respond to any question is nothing short of miraculous. But we are all still looking for ways to make them better: to know more, to sound like us, to do our work for us. The model you download from Hugging Face or the Ollama library is a general model, designed to be really good for everyone.

There are two main approaches to modifying what a model spits out. We can add new information in the prompt that the model can use to know things it didn't. Or we can tweak the model weights to change how the model responds, the style it uses. That tweaking is called fine tuning.

## What fine tuning is and isn't

*[Watch from 0:43](https://www.youtube.com/watch?v=3UQ7GY9hNwk&t=43s)*

Fine tuning isn't about teaching the model new information, although it is possible to do. Instead it adjusts how the model phrases what it says, the syntax it uses, and the format of the output.

When you look online for tutorials, you get buried in code. It looks really hard, like it needs an expert. But it's not. It's actually pretty easy, though you definitely get better with practice. I think one of the biggest problems is that most folks demonstrate fine tuning using Python notebooks, which has to be the worst format for teaching.

I am using MLX this time because it supports Apple Silicon. In future videos I will look at unsloth and maybe axolotl as well. You might be shocked at how easy this is.

1. Come up with a dataset of questions and answers.
2. Run the fine tuning process.
3. Use the new adapter with a model.

Step 1 is definitely the hardest part, and the one most wrapped up in the dark arts that are hard to explain. I thought 2 and 3 were going to be the tough ones, and there is almost nothing to them.

## Step 1: build the dataset

*[Watch from 2:09](https://www.youtube.com/watch?v=3UQ7GY9hNwk&t=129s)*

The goal is to get the model to understand how you want it to answer certain questions. So you provide a question and an answer in a format the model will understand, and that format is specific to the model.

I am fine tuning Mistral. Go to ollama.com, find the mistral model, and click on the template. What I am looking for is how the model expects to see a question and then the answer. This model accepts the new tool calling method with Ollama, but I don't care about that for now. There is an **[INST]**, then the system prompt if there is one, then the user prompt, then the closing **[/INST]**, and then the answer. We need to replicate this a bunch of times and save the results in a JSONL file.

JSONL is kind of like a regular JSON file, but not really. Each line is a new object, rather than an array of objects separated by commas. I have no idea why. It would be a whole lot easier for everyone if it was a simpler array. Each object has a single key and value: the key is 'text' and the value is the string with the INST block.

You need to come up with at least 50 to 100 of these objects, and more can be better. Again, we aren't teaching the model new facts, but the style to respond with.

Often folks want a model that will write emails or documents as themselves, or at least a first draft. That's what I tried. I have hundreds of videos, and I have the scripts for most of those from the last few years on my Mac. The way I talk in those videos is the way I actually talk and write. So I:

- read in the markdown files with those scripts
- split each file by newlines
- got Ollama to summarize each paragraph
- had Ollama convert that summary into an instruction for someone to write about, which is my question, with the original paragraph as the answer

This probably isn't an ideal example, but it seems to work well for this case. Then I saved 60% of the answers to the train.jsonl file and 20% to each of two other files, valid.jsonl and test.jsonl. The fine tuning process will need these.

## Step 2: run the fine tuning

*[Watch from 4:58](https://www.youtube.com/watch?v=3UQ7GY9hNwk&t=298s)*

Since I am using MLX, we need to install mlx-lm with **pip install mlx-lm**. Next we need access to the Mistral model on Hugging Face. MLX needs the original safe tensor files. Apparently you can also use quantized files, but they need to be in MLX's format, and I haven't found a way to convert the standard GGUF files that Ollama uses to that format.

The Mistral model requires you to log in to Hugging Face to download it, so install the Hugging Face CLI, set it up from the page on Huggingface.co, and run **huggingface-cli login**.

Now for the fine tuning command. Start with **mlx_lm.lora**, then:

- **The train flag** tells it we want to fine tune the model.
- **The model flag** says which model to use, as org slash model name. You can find it by going to the repo and clicking the copy button next to the name.
- **The data flag** points to the path where your jsonl files are.
- **The batch size flag** tells mlx_lm how many examples are used in each iteration. Larger goes through everything faster, but needs more memory, so it takes some experimentation.

Run that and it's time to listen to the fans spin. I have an M1 Max with 64GB Unified RAM, and it takes several minutes, pretty much hands off. When it's done, you will have a new directory called adapters with the safetensors files and a config file.

## Step 3: use the adapter in Ollama

*[Watch from 6:56](https://www.youtube.com/watch?v=3UQ7GY9hNwk&t=416s)*

Now we define a new model using our adapter. Since we used mistral, we create a Modelfile with FROM set to mistral and ADAPTER set to the directory with our new adapters in it. Then run **ollama create mmmistral -f Modelfile**, which takes a second or so, and then **ollama run mmmistral** to start using our fine tune of mistral.

If you are on a much older version of Ollama, this may not work. Support for these adapters was added just in the last couple of days. You also need to create the adapter with the mlx_lm command. If you use the lora script in the mlx examples repo, it creates an npz file that needs to be fused to the original model, which is just a bit more hassle. mlx_lm creates an adapter that is used by Ollama.

## How it turned out

*[Watch from 7:56](https://www.youtube.com/watch?v=3UQ7GY9hNwk&t=476s)*

Is this a perfect fine tune? No, not really. I have some work to do to make it better, but at least now I know it's not as scary as I thought it was. Have you done any fine tuning of models yourself? Any specific fine tunes you think are really compelling? Do you have any ideas for a fine tune you might like to do? Let me know in the comments.

*[Watch the full video on YouTube](https://www.youtube.com/watch?v=3UQ7GY9hNwk)*
