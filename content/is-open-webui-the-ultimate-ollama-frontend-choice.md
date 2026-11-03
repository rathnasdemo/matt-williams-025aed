---
schemaId: urn:gala:schema:content-frontmatter:2.0.0
schemaVersion: 2.0.0
id: 01a1a2dd-232c-76b1-9521-f3ad32c30d63
kind: article
title: Is Open Webui The Ultimate Ollama Frontend Choice?
description: 'I walk through Open WebUI, a popular Ollama frontend: install, settings, multiple models, prompts and documents, and what I like and dislike about it.'
language: en-US
authors:
- 01a1a2de-b098-79bd-b2d6-f17752d51779
tags:
- ollama
- open-webui
- local-llm
- docker
- ai-tools
status: published
createdAt: '2024-04-26T12:00:00.000Z'
publishedAt: '2024-04-26T12:00:00.000Z'
slug: is-open-webui-the-ultimate-ollama-frontend-choice
hero:
  path: assets/content-is-open-webui-the-ultimate-ollama-frontend-choice-hero.jpg
  alt: Thumbnail of the video "Is Open Webui The Ultimate Ollama Frontend Choice?"
  role: informative
redirects: []
extensions: {}
---
*Adapted from the video [Is Open Webui The Ultimate Ollama Frontend Choice?](https://www.youtube.com/watch?v=16fWf0VVeIo)*

> **TL;DR:** Open WebUI is the Ollama frontend I see mentioned most, so I started my tool series with it. It installs easily with Docker, but the forced login, global settings and model files section feel odd. Querying several models at once is handy, and the slash command for custom prompts with clipboard and variables is my favorite feature. Pulling in web pages and documents works fairly well. Overall it is a nice tool, though little beyond the prompts really sets it apart from the CLI.

One of the unique aspects of Ollama is its dual nature: it is both an end user tool and a developer tool with an API endpoint. That lets developers program against it, and the GitHub repo homepage lists numerous community integrations, including desktop and web tools. There are over 30 or 40 or even 50 entries, and with so many options it can be overwhelming to choose the right one.

I started a chart and document on my blog to highlight the differences, but as I explored the first few integrations I found that many had significant issues. It felt like I was just highlighting the bad stuff rather than being helpful. So I decided to make a video series on each tool, covering what makes it compelling and what might be missing. Perhaps that helps you find the right tool, or inspires you to build your own app that combines the best features of several.

One reminder: I was a founding member of the Ollama team, but I no longer work for Ollama. These are simply my opinions, not those of anyone on the Ollama team.

## Why start with Open WebUI

*[Watch from 1:38](https://www.youtube.com/watch?v=16fWf0VVeIo&t=98s)*

With so many tools it's hard to know where to start, so I picked the one I think is a favorite for most people working with Ollama. It's the application I see most often mentioned in the Ollama Discord, and it's called Open WebUI. It used to go by something like Ollama web UI, but I think the author wants to be less tied to Ollama specifically.

There's a lot to like. It's visually very attractive, with a few themes, including light and dark mode (I wish dark mode would die because it hurts my eyes). It gives good suggestions of things to try, although I wish they would change occasionally. It also has a very opinionated way of working with prompts. I initially thought that was a little distracting, but I came around to loving it.

## A first look at the interface

*[Watch from 2:46](https://www.youtube.com/watch?v=16fWf0VVeIo&t=166s)*

When you first bring up the tool, you're asked to log in. I hate it when tools do this. If something's running locally, don't make me sign in. If you want to offer that layer for people who host it, great, but make it an option.

In the UI, the chat window is front and center, with a message box at the bottom. At the top you choose a model, which is a pretty critical step, so I wish it wasn't so visually hidden. On the left is a big new chat button, then sections for model files, prompts and documents, followed by your chat list and a search box.

I asked my go-to question, why is the sky blue, using a tiny model that performs beautifully for so many use cases. Pretty quickly we got a pretty amazing response.

## Installing it

*[Watch from 4:00](https://www.youtube.com/watch?v=16fWf0VVeIo&t=240s)*

From ama.com I click the GitHub link, scroll down the readme to the community integrations list, and Open WebUI is at the top of the web and desktop tools. The readme makes it look like an incredible tool: local RAG integration, an intuitive interface, code syntax highlighting, theme customization, markdown and latex support, web browsing capability and much more. The releases page shows releases on a pretty regular basis, and they're not small ones. They add significant new features.

For installation there's a quick start for Docker. Not everyone loves Docker, and some people misunderstood the licensing fuss and think they need to pay, when most individual users don't need to pay anything. If Ollama is running locally, you can use the Docker command. Here is what the flags do:

- **-d** runs it as a daemon process with the UI in the background.
- **-p** publishes port 8080 in the container to port 3,000 on your host. Replace 3,000 with anything else if you prefer a different port.
- **--add-host** tells it to use Ollama on the local machine.
- **-v** creates a volume for open web UI and mounts it into the container's backend data directory.
- **--name** sets the name of the container.
- **--restart always** means that if it stops for any reason, it comes back up.

Finally, you specify the container registry and image to use. If you don't want Docker, there are instructions at a separate link. Once it's running, visit localhost and your port, which for me is 3,000. The first time, you create an account with a name, an email and a password.

## Settings

*[Watch from 7:10](https://www.youtube.com/watch?v=16fWf0VVeIo&t=430s)*

First, set the model from the dropdown. I've downloaded and created so many that I have a lot available, and here I chose llama 3. Then you can ask a question.

Settings are at the top or under your name in the bottom corner. Here is what I found:

- **General.** You can set a theme, language and a system prompt. I'm not sure why anyone would want a global system prompt. Does it get overridden by the system prompt set in the model? There's no way to find out without digging around the docs. The default parameters are something you really want to differ for each model.
- **Connections.** You can set an OpenAI API URL and key, or update the URL for Ollama, and add additional hosts. I'm not sure what happens with multiple hosts, because I can't choose the host when I pick a model.
- **Models.** You can set the host used to manage models, but it's not clear how this differs from the connections screen, and even with a second host set, I only see the default one. There's a button to update all models. Unless you have a small number of models, don't click it. It updates in the most inefficient way possible, pulling every model in your list. You can pull a new model if you know its name, but that's much more awkward than the CLI. You can also delete models. They offer a way to upload GGUF files, but those are just model weights, not the full Ollama model, so they're a bit useless until you make a model from them.
- **Interface.** There are some functionality options and default prompt suggestions. I'm not sure of the value, and default system prompts would be better served here.
- **Audio.** You can set speech to text using Whisper locally or OpenAI, but you can't control which Whisper model or which language you speak. Text to speech is local or OpenAI, and the local one is all but useless.
- **Images.** You can access automatic 1111, which is a bit strange, but the author seems to want to support more than just Ollama.

So far everything feels pretty good. Things work roughly the way you'd expect and it generally performs well.

## Asking several models at once

*[Watch from 9:56](https://www.youtube.com/watch?v=16fWf0VVeIo&t=596s)*

Next to the model dropdown is a plus sign. Click it and you can add a second model, a third, a fourth and so on. When I asked why the sky is blue with two models selected, the bottom showed two of two, and clicking the arrow showed me the output of the other model.

There's no magic here. It asks the same question of each model, so it takes longer, but it becomes easier to find the right model for you.

There are also thumbs up and thumbs down buttons. The readme seems to refer to using them to make something better, but there's no info on where that data goes. I couldn't find any way to get the info out, and I looked at the SQLite DB the app creates and nothing seemed to have been added. You can also edit the answer, maybe to help build good questions and answers for fine tuning later, but the feature doesn't seem complete.

## Prompts and the slash command

*[Watch from 11:16](https://www.youtube.com/watch?v=16fWf0VVeIo&t=676s)*

In prompts, in the top left, click the plus sign to add a new prompt. I entered summarize for the title, which defaults to the command too, and the prompt: write a short summary of the following text. After saving, I started a new chat, typed the slash key and pressed enter to select summarize, then pasted in some text and pressed enter. That gave me a summary, but with a few extra steps.

So I went back and added the word clipboard in double curly braces. Now typing slash summarize and enter automatically takes what's in my clipboard and adds it to the end of the prompt. You can also add variables in square brackets, for example "for someone who is" and then age in square brackets "years old". When you bring up the prompt, the text is pasted in and the age is highlighted, waiting for you to enter a value. You can add as many variables and prompts as you like.

The prompts page has a link to discover prompts, but it just takes you to the Open WebUI page, where you scroll down to find prompts people have shared. It really should link you to the right place. I also found those prompts super generic, and it's better to come up with your own. Still, the slash command for bringing up prompts is amazing, and it may be my favorite feature of the app.

## Web pages and documents

*[Watch from 13:04](https://www.youtube.com/watch?v=16fWf0VVeIo&t=784s)*

In the chat, press the pound key, or hash key, and type a web address. Now you can ask any question about the content on that page. It doesn't seem to follow links, just what's on that page. I could ask about my Patreon, but asking about my employment status gave a very generic answer.

The documents section lets you add one document or a bunch. Unfortunately you get no feedback until the process is done, but once a document shows up you can add tags to it. Back in the chat, press the hash key again and you're prompted to choose a document or a collection, which is the tags associated with your docs. It seems to do a pretty good job, though I wish there were some feedback.

There are some document settings, but they don't seem to have any effect. I change them, and the next time I visit the page they're back to the default. Maybe that's something else to be fixed, and given how often releases come out, maybe there's a fix tomorrow.

## Model files and my verdict

*[Watch from 14:23](https://www.youtube.com/watch?v=16fWf0VVeIo&t=863s)*

The last thing is the model files section. I have no idea why it exists. They should just make a better experience for working with models on the Ollama site, because this feels like the same thing done more awkwardly. I wish it made it easier to take any model, apply new parameters and a system prompt, give it a new name and save it back to the Ollama library. The CLI already does that, but some folks prefer a UI, so it would be a nice addition.

Overall it's a nice tool, but some of the weird aspects are hard to get past. The whole user thing is a pain, though I'm sure they worked hard on it, and I wish you could disable it entirely. The model file section doesn't make sense to me, and a better workflow with models would be great. The button to update models is way too easy to press, so either make it harder to press or have it compare the hashes first and then pull.

The slash command for prompts is pretty genius and I love it, but that's pretty much the only differentiating feature from the CLI. Is that enough to use it regularly? I don't know. A lot of folks are scared of the command line and will be fine with a one-to-one version of the CLI tool, but that's not me. I thought this would be a short video, and I didn't expect to like Open WebUI as much as I did. I hope that's the experience with all the other tools on the community integrations list.

Do you use Open WebUI? Is there anything I missed, or a better tool out there? Share it with me in the comments below.

*[Watch the full video on YouTube](https://www.youtube.com/watch?v=16fWf0VVeIo)*
