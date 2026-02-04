---
order: 1
layout: post
title: Sainfeld - AI Generated Seinfeld Episodes
description: An automated AI system that writes brand new Seinfeld-style episodes, generates illustrated scenes, and publishes them weekly to a live website.
image: /images/projects/sainfeld/featured.jpg
github: #
featured: true
---

## Sainfeld

**Sainfeld** is a fully automated AI storytelling project that generates brand new Seinfeld style sitcom episodes; complete with scripts, illustrated scenes, and a published webpage - without any manual intervention.

Every run:    
• writes an original episode using Gemini  
• generates matching scene artwork with Imagen  
• builds the HTML  
• updates metadata  
• deploys to Firebase  

The site updates itself like a tiny production pipeline.

🔗 <a href="https://sainfeld.anupdsouza.com/" target="_blank" rel="noopener noreferrer"><strong>Live demo</strong></a>

---

## Why I built it

I’m a huge Seinfeld fan and have always thought the sitcom is pure comedy gold - *"Gold, Jerry! Gold!"*. I wanted to go beyond simple AI demos and build something that felt like a real product: not just generating text, but actually **shipping content automatically**. Sainfeld became a playground for combining AI + automation + deployment into a system that runs hands-off.

---

## Tech Stack

Python • Vertex AI (Gemini + Imagen) • GitHub Actions • Firebase Hosting • Static site generation

---

## What it does

- Generates structured episode scripts as valid JSON  
- Creates consistent cartoon scenes for each scene  
- Optimizes and saves images automatically  
- Builds formatted HTML pages  
- Maintains episode metadata  
- Deploys to production via CI/CD  

Everything runs through a single workflow.

---

## Highlights

- Prompt engineering for structured AI output  
- Automated image generation with rate limiting + compression  
- Fully reproducible builds  
- Zero manual publishing  
- Designed with recovery/failure safeguards  

---