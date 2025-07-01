---
title: "Setting up Claude MCP for TI with Kaspersky TIP as a case study"
layout: single
date: 2025-06-27
tags: [posts]
excerpt: "A blog on how to set up a Threat Intelligence server for Claude, using Kaspersky TIP as a case study"
---

Okay, let's skip the introduction and get straight to the guide.

First of all, we need an API key to access Kaspersky TIP. Create an account or open existing one.

<img src="{{ site.url }}{{ site.baseurl }}/images/kasperskymcp1.gif" alt="">

Request a token and copy it. We need to create a new user environment variable called `OPENTIP_API_KEY` with the value of our API key.

Now we need to download Claude for Windows from the official website and install it.

[https://claude.ai/download](https://claude.ai/download)

When launched from a standart user account, it is installed to `C:\Users\User1\AppData\Roaming\Claude`.

Further we need to install Kaspersky MCP.

Let's clone their repo.

`git clone https://github.com/KasperskyLab/threat-intelligence.git`

They recommend to install `uv` package manager.

`powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"`

Go to the repo and run its MCP process.

`Path\threat-intelligence\opentip-mcp>uv run opentip.py`

Now copy the json snippet required to register the MCP in Claude (see `README.md`)

<img src="{{ site.url }}{{ site.baseurl }}/images/kasperskymcp2.gif" alt="">

Run Claude and go to `File -> Settings -> Developer`. Press `Edit Config`. The app creates an empty file `claude_desktop_config.json`. Go to our Claude installation folder and paste the copied json into that file. Don't forget to replace `YOUR_API_KEY` macro with your API key and change the path to the repo.

<img src="{{ site.url }}{{ site.baseurl }}/images/kasperskymcp3.png" alt="">

Restart Claude. If everything went well, you will see the icon of Kaspersky TIP in the `Search and tools` box next to the prompt input field.

<img src="{{ site.url }}{{ site.baseurl }}/images/kasperskymcp4.gif" alt="">

Examples of requests.

```
Provide me with information about file with the hash: 580C9454B743739528466FC0604F6E62

Get full analysis result for file with the hash: 580C9454B743739528466FC0604F6E62

Get information about the following domain from Kaspersky TIP: www.google.com

I want Kaspersky TIP to analyze a file from my computer, the path: C:\Windows\bfsvc.exe
```

<img src="{{ site.url }}{{ site.baseurl }}/images/kasperskymcp5.gif" alt="">

Video guide

{% include video id="DCbWHR1th2Y" provider="youtube" %}




 