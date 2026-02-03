---
order: 2
layout: post
title: PG Transit - Real-Time Bus Tracking
description: A complete modernization of a city transit app featuring live bus tracking, a "moving" trip timeline, custom iconography, and an automated serverless data pipeline.
image: /images/projects/pg-transit/featured.jpg
github: #
featured: true
---

## PG Transit

**PG Transit** is a modern iOS companion for commuters in Prince George, BC. It transforms a static schedule viewer into a live, breathing travel assistant.

I rebuilt the legacy Objective-C app entirely in **SwiftUI**, redesigning the interface from scratch, including the app icon and custom iconography to create a polished, native iOS experience. The app merges static schedules with real-time data streams to answer the commuter's most important question: *"Where is my bus right now?"*

🔗 <a href="https://apps.apple.com/us/app/prince-george-transit/id558331296" target="_blank" rel="noopener noreferrer"><strong>App Store Link</strong></a>

---

## Why I built it

My client needed to modernize their existing transit app to support real-time features that users expect today. I wanted to engineer a system that felt "alive"; where buses move on the map and arrival times update instantly while ensuring the app remained fully functional offline. I also took the opportunity to refresh the visual identity by creating the app iconography as well as navigation structure in a way that fits perfectly on modern iOS devices. I also built a promotions system for my client to integrate local businesses and improve visibility amongst commuters.

---

## Tech Stack

Swift • SwiftUI • MapKit • WeatherKit • Core Data • Combine • • SwiftProtobuf • Firebase Storage • GitHub Actions

---

## Highlights

- **Live Trip Timeline:** A standout UX feature where the bus icon physically moves down the list of stops in real-time, allowing users to track their journey's progress at a glance.
- **High-Performance Decoding:** Utilized the SwiftProtobuf library to parse binary GTFS-Realtime feeds (Vehicle Positions and Trip Updates). This ensures efficient, low-latency processing of live data without draining user battery or bandwidth.
- **Smart Stop Details:** Stop schedules display live status indicators (e.g., "5 min delay", "On Time") color-coded for readability.
- **Contextual Maps:** Maps are context-aware, showing bus stops & live bus icons along with local weather conditions powered by **WeatherKit**.
- **Dark Mode:** A fully adaptive UI that looks great in both light and dark environments.
- **Custom Design:** Designed the main App Icon that reflects the identity of Prince George and a complete set of custom in-app iconography to ensure a cohesive visual language.

---

## The Data Pipeline

To keep schedules accurate without constant App Store updates, I engineered a serverless data pipeline:

1.  **Automated Ingestion:** A **GitHub Actions** workflow runs daily to download the latest static schedule (GTFS) directly from BC Transit.
2.  **Version Control:** The script checks the data version against what is currently live. If a change is detected, it uploads the new dataset to **Firebase Storage**.
3.  **OTA Updates:** The app checks for these updates daily. Instead of blocking the user with a forced update, it unobtrusively badges the Settings tab, allowing the user to initiate the schedule update when it is convenient for them.

---