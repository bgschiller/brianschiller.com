---
title: Claude's Smoking Gun
date: 2026-08-19
description: Why does Claude keep finding "The Smoking Gun" and being wrong about it?
category: blog
tags: [claude, ai]
---

If you've worked with Claude Code or another AI agent to debug something, it's probably pointed out a "smoking gun" or two. In case you haven't, here's an example screenshot I found.

![Screenshot of a terminal. Text says: Now I have the smoking gun. Look at: - t=53.476: render with morphing=false t=53.486: morph-effect fires, ref=true match=false - so the ref was correctly true from the open state. rAF is scheduled at this point.](./smoking-gun-01.png)

You've probably also found that it's frequently _wrong_ about the smoking gun&mdash;the "smoking gun" it identifies ends up not being a problem. Here are a couple more screenshots:

![Screenshot of a slack message. `Now I have the smoking gun.` -> signal you're about to receive the halfest baked idea you've heard in a long time](./smoking-gun-slack-01.png)

![Screenshot of a slack message. `There's the smoking gun - nx.json line 9:` but behold, it was not the smoking gun](./smoking-gun-slack-02.png)

My guess for why this happens is that the LLM has ingested _tons_ of narratives: both blog posts about solving bugs and Agatha Christie novels. Built into the weights of the model, it has a belief about where the reveal ought to occur in a mystery. So when it's time for the reveal, it latches onto the next plausible clue and declares it "the smoking gun!".

Putting this together helps my intuition about how LLMs work. They're extremely useful for debugging, but definitely get "impatient" when the debugging drags on.
