---
title: 'Inductive Biases for LLM/LVLM Agent Reasoning'
date: 2026-04-01 15:00:00 +0200
categories: [logbook]
math: true
description: "On different prior knowledge for different environments for agent reasoning"
author: me
# tags: [thesis, software]     # TAG names should always be lowercase
---

When we build AI agents with LLM/LVLMS, we often expect them to *just figure it out.* However, the gap between an LLM playing a 2D game and an embodied agent navigating a 3D house is massive. That gap is bridged by inductive bias—the set of assumptions a model uses to predict outcomes in a new environment. Let's breakdown of how these biases shift depending on the environment the agent inhabits. 



<figure style="text-align: center;">
  <img src="/Users/sungjunekim/Desktop/sungjune/sungjuune.github.io/assets/img/posts/nethack.jpg" alt="Nethack Game" width="180">
  <img src="/Users/sungjunekim/Desktop/sungjune/sungjuune.github.io/assets/img/posts/simworld.jpg" alt="Simulated World" width="260">
  <figcaption style="font-style: italic; color: gray; margin-top: 8px;">
    Figure 1: Comparison between 2D game environments (NetHack) and 3D simulations (SimWorld).
  </figcaption>
</figure>

##  1. The 2D Grid Environment: 

In 2D environments like Atari or NetHack, the world is defined by discrete symbols and flat spatial relationships, making logic and locality important inductive biases. 
- Spatial Invariance: A 2D agent assumes that a "key" has the same function and identity whether it is at coordinates $(0,0)$ or $(50,50)$.

- Relational Bias: The primary bias here is **combinatorial**. We want the agent to understand how objects interact (e.g., Agent + Key = Unlocked Door).

- The "God's Eye" Assumption: Often, 2D agents have access to the full state of the map. The inductive bias assumes that if something isn't on the screen, it doesn't exist or isn't relevant to the immediate step.



## 2. The 3D World Environment

When the LLM agent is operating in the embodied 3D simulator (*e.g,* Habitat and Minecraft), the logic of 2D breaks. Here, instead of relational and combinatorial logics, geometry and physics act as the core inductive biases.

- Egocentric Vision: The agent must learn that the world rotates around its perspective. This requires a bias toward 3D geometry understanding that a shrinking object isn't actually disappearing. It’s just moving further away.

- Object Permanence: Unlike a 2D sprite that might vanish when off-screen, a 3D agent needs a temporal bias. It must assume the kitchen is still behind it even when looking at the living room.

- Affordances: In 3D, we bias the agent toward *actionability*. Instead of seeing a door as a texture, the agent biases its reasoning toward "What can I do with this?" (Open, close, lock).