---
order: 3
layout: post
title: Poker Bingo
description: A unique casino card game that blends Texas Hold'em poker with Bingo, featuring three game modes, progressive jackpots, and a complete SwiftUI implementation.
image: /images/projects/poker-bingo/featured.jpg
appstore: https://apps.apple.com/us/app/poker-bingo-game/id6757565931
featured: true
---

<p align="center" style="font-size: 0.85rem;">
Photo by <a href="https://unsplash.com/@mparzuchowski?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Michał Parzuchowski</a> on <a href="https://unsplash.com/photos/white-and-black-dice-on-green-table-U8n_O7rEq7o?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
</p>

## Poker Bingo

**Poker Bingo** is a casino-style card game that combines **Texas Hold'em** with the excitement of **Bingo**. Instead of simply trying to build the best poker hand, every round also gives you the opportunity to match your own cards and complete a Bingo, unlocking progressive jackpots along the way.

The entire app was built from the ground up in **SwiftUI**, from the animated game table to the custom betting interface and game logic. Alongside the gameplay itself, I integrated **StoreKit** to support in-app purchases for coin packs and **iCloud** to persist player information such as their chosen name, avatar and coin balance across devices.

🔗 <a href="https://apps.apple.com/us/app/poker-bingo-game/id6757565931" target="_blank" rel="noopener noreferrer"><strong>App Store Link</strong></a>

---

## Tech Stack

Swift • SwiftUI • StoreKit • Combine • iCloud

---

## Highlights

- **Three Unique Game Modes:** Play Poker Bingo, Joker Poker or Bingo Only, each offering a different way to play.
- **Complete SwiftUI Implementation:** Every screen, animation and interaction was built using SwiftUI.
- **Progressive Jackpots:** Gold, Silver and Bronze jackpots continue to grow as games are played.
- **Poker Hand Evaluation:** Every street is evaluated independently with detailed results for each player.
- **Multiple Seats:** Players can participate across multiple seats and even back AI opponents.
- **Smooth Animations:** Card dealing, reveals and payouts are animated to create a polished casino experience.
- **StoreKit Integration:** In-app purchases allow players to purchase additional coin packs without interrupting gameplay.
- **iCloud Sync:** Persisted player preferences, avatar selection, display name and coin balance across devices using iCloud.
- **Reusable Game Engine:** Designed the game using reusable SwiftUI components and shared game logic, making it easier to support multiple game modes while keeping the codebase maintainable.

---

## How Poker Bingo Works

Every player starts with **five private cards**.

The game then reveals **15 community cards** across **three streets**, with five cards appearing on each street. For every street, your poker hand is compared against the board's hand. Beat the board to win that street.

At the same time, you're also trying to match every one of your original five cards as they're revealed throughout the game. If all five are matched, you call **BINGO** and collect bonus payouts from the progressive jackpot.

The result is a game where you're constantly paying attention to both your poker hand and your Bingo progress, making every card reveal exciting.

---

## Gameplay

A complete playthrough of the Poker Bingo game mode can be viewed here.

<iframe title="PokerBingo" width="640" height="360" src="https://www.youtube-nocookie.com/embed/4Y52co_xtBc?controls=0" frameborder="0" allowfullscreen></iframe>
<br>

---

## What I Learned

Poker Bingo became a great exercise in building a complex state-driven application with SwiftUI.

The game has a surprising number of moving parts happening simultaneously: card dealing, poker evaluation, Bingo matching, animations, betting, jackpots, AI players and payouts; all of which need to stay perfectly synchronised throughout a round.

Working through those challenges reinforced just how capable SwiftUI has become for building games and highly interactive applications when paired with a clean architecture and careful state management.
