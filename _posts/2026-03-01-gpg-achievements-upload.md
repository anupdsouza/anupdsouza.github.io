---
layout: post
published: true
title: "Uploading Google Play Games Achievements in Bulk"
date: 2026-03-01 00:10:00 +0530
image: '/images/posts/gpg-achievements-upload/featured.jpg'
description: "Notes on setting up and bulk uploading Google Play Games achievements."
excerpt: "Practical lessons on automating Google Play Games achievements upload, including required files, localization, and common pitfalls."
seo_title: "Google Play Games Achievements Bulk Upload"
seo_description: "Step-by-step notes on bulk uploading Google Play Games achievements with CSV files, localization, and troubleshooting."
categories:
  - Android
tags:
  - Google Play Console
  - Play Games Services
  - Game Development
---

<p align="center" style="font-size: 0.85rem;">
"Are you not entertained?"<br/><br/>
Photo by <a href="https://unsplash.com/@claudiolcastro?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Cláudio Luiz Castro</a> on <a href="https://unsplash.com/photos/super-mario-figurine-on-brown-surface-_R95VMWyn7A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
</p>

## Why Bulk Upload?

Creating a handful of achievements manually in Google Play Console is fine. But once you’re dealing with dozens, plus translations and icons, it quickly becomes a bottleneck. This post walks through the required files, the workflow, and the lessons I learned while experimenting with automation and AI. The goal is to highlight both the pitfalls and the solutions, so you can avoid the same detours.

Official documentation: [Google Play Games Achievements](https://developer.android.com/games/pgs/achievements)

---

## Prerequisites: What Your ZIP File Must Contain

The Play Console achievement upload page specifies that your ZIP must include:

1. **AchievementsMetadata.csv** – Information about the achievements you’re creating  
2. **AchievementsLocalizations.csv** – Translations for achievement names and descriptions  
3. **Icons** – One per achievement, in PNG/JPG/JPEG format, sized **512x512**  
4. **AchievementsIconsMappings.csv** – Mapping between achievements and their icons  

> Note: While the page lists *AchievementsLocalizations.csv* as required, in practice it’s optional. You can upload achievements without translations, but adding them is strongly recommended.

---

## My Journey (and Missteps)

I experimented with AI to see if it could simplify the process. It was useful for quickly generating scripts, but it also introduced pitfalls that I had to work through:

- One suggestion was to upload achievements directly via scripts — a method not supported by Play Console. I even set up OAuth clients in Google Cloud Console before realizing it was a dead end.  
- Another was to include column headers in the bulk CSV upload, which turned out to be unnecessary. Digging into the documentation clarified the correct format.  
- Later, one achievement had **50,000 steps**, exceeding the 10,000 limit. The ZIP uploaded fine, but “Save as draft” always failed with the vague error: *Your changes couldn’t be saved*. Unlike other issues, Play Console didn’t explain why.  
- To debug, I used AI again to generate scripts that created one ZIP per achievement. Uploading them individually revealed the culprit — the 50k steps. I changed it to a binary achievement instead.  
- After deleting all achievements and retrying the bulk upload with corrected steps, everything finally worked.

The takeaway: AI was helpful for scripting, but the official documentation was the real source of truth. Relying on AI alone led to blind spots, and sometimes it misattributed errors to the platform. Human judgment and persistence were what solved the problem.

---

## Registry Setup

Here’s a simplified registry snippet I used:

```dart
enum AchievementProgressType {
  binary,       // One-time unlock
  cumulative,   // e.g. 150 / 1000
  streak,       // resets on failure
  none,
}

import 'package:gemquest/models/achievement.dart';
import 'package:gemquest/models/achievement_progress.dart';

enum AchievementTier { bronze, silver, gold, platinum }

const achievements = [
  Achievement(
    id: 'collect_first_gem',
    progressType: AchievementProgressType.binary,
    points: 10,
    tier: AchievementTier.bronze,
  ),
];
```

This registry acts as the source of truth. Scripts read it to generate CSVs for metadata, localizations, and icon mappings.

> Note: The tier field is part of the registry for human readability and design reference, but the script automatically infers tiers from points when generating icon mappings.
---

## Required Files Explained

### 1. AchievementsMetadata.csv

Defines the basics:

```
Name, Description, Incremental, Steps, State, Points, ListOrder
```

Example:

```
First Gem,Collect your first gem.,False,,Revealed,10,1
```

Rules:
- Total points ≤ 2000  
- Incremental steps ≤ 10,000  
- Names must be unique  

---

### 2. AchievementsLocalizations.csv (Optional but Recommended)

Handles translations:

```
Name, Localized name, Localized description, locale
```

Example:

```
First Gem,Primer Gema,Recoge tu primera gema.,es-ES
First Gem,Pertama Permata,Kumpulkan permata pertama Anda.,id
First Gem,Unang Hiyas,Kunin ang iyong unang hiyas.,fil
```

Important: Locale codes must match Play Console exactly. For Indonesian use `id`, for Tagalog use `fil`. I mistakenly used `id-ID` and `tl`, which failed.

---

### 3. Icons

- Must be **512x512**  
- PNG, JPG, or JPEG format  
- One per achievement  

---

### 4. AchievementsIconsMappings.csv

Maps icons:

```
Name, Icon filename
```

Example:

```
First Gem,bronze_gem.png
```

---

## Debugging Strategy

When bulk uploads failed, the fastest way to isolate issues was:

> Generate one ZIP per achievement and upload individually.

This was a brute force approach that was time consuming but this revealed problems like exceeding step limits or wrong locale codes. In hindsight, it proved quite helpful as it prevented speculations like whether it had anything to do with escaping double quotes or using special characters or excluding optional fields for specific achievements and actually identified the achievement that broke the upload system.

---

## The Script

Here’s the script that I used in order to create a zip file as well as export them individually for inspection if needed. Note that for localization, the achievement strings and values are read directly from the ARB files where the title and description for each achievement are stored as `achievement_<achievement id>_title` & `achievement_<achievement id>_description`.

```python
#!/usr/bin/env python3
"""
Play Games Achievement Generator

Generates:
- AchievementsMetadata.csv
- AchievementsLocalizations.csv
- AchievementsIconsMappings.csv
- Optional ZIP bundle for Play Console import

Supports:
--dry-run   : preview only
--zip       : export achievements_upload.zip
--out DIR   : output directory

Expected Structure:
registry.dart
arb/
icons/
output/
"""

import argparse
import json
import re
import csv
import sys
import zipfile
import shutil
from pathlib import Path
from collections import defaultdict

# ----------------------------
# Locale mapping
# ----------------------------

LOCALE_MAP = {
    "en": "en-US",
    "es": "es-ES",
    "id": "id",
    "tl": "fil",
}

REQUIRED_ICONS = [
    "bronze_medal.png",
    "silver_medal.png",
    "gold_medal.png",
    "platinum_trophy.png",
]

# ----------------------------
# Tier rules (auto based on points)
# ----------------------------


def get_tier(points):
    if points <= 10:
        return "bronze_medal.png"
    elif points <= 20:
        return "silver_medal.png"
    elif points <= 30:
        return "gold_medal.png"
    else:
        return "platinum_trophy.png"


# ----------------------------
# Validation helpers
# ----------------------------


def ensure_exists(path, message):
    if not Path(path).exists():
        sys.exit(f"❌ {message}: {path}")


def validate_registry(path):
    ensure_exists(path, "Registry file not found")


def validate_arb_dir(path):
    ensure_exists(path, "ARB directory not found")
    if not list(Path(path).glob("app_*.arb")):
        sys.exit("❌ No app_*.arb files found in arb directory.")


def validate_icons_dir():
    ensure_exists("icons", "Icons directory not found")
    missing = []
    for icon in REQUIRED_ICONS:
        if not (Path("icons") / icon).exists():
            missing.append(icon)
    if missing:
        print("❌ Missing required icon files in icons/:")
        for m in missing:
            print("   ", m)
        sys.exit(1)


# ----------------------------
# Registry Parser
# ----------------------------


def parse_registry(file_path):
    content = Path(file_path).read_text()

    pattern = re.compile(
        r"Achievement\(\s*id:\s*'([^']+)'.*?progressType:\s*AchievementProgressType\.([a-z]+).*?(target:\s*(\d+),)?\s*points:\s*(\d+)",
        re.DOTALL,
    )

    achievements = []
    for match in pattern.finditer(content):
        id_, progress_type, _, target, points = match.groups()
        achievements.append(
            {
                "id": id_,
                "progress_type": progress_type,
                "target": int(target) if target else None,
                "points": int(points),
            }
        )

    if not achievements:
        sys.exit("❌ No achievements found in registry.")

    return achievements


# ----------------------------
# ARB Loader
# ----------------------------


def load_arb_files(arb_dir):
    arb_data = {}
    for file in Path(arb_dir).glob("app_*.arb"):
        locale_code = file.stem.split("_")[1]
        if locale_code not in LOCALE_MAP:
            continue
        with open(file, "r", encoding="utf-8") as f:
            arb_data[LOCALE_MAP[locale_code]] = json.load(f)
    return arb_data


# ----------------------------
# Validation
# ----------------------------


def validate_points(achievements):
    total = sum(a["points"] for a in achievements)
    if total > 2000:
        sys.exit(f"❌ Total points {total} exceed 2000 limit.")
    return total


def validate_arb_consistency(achievements, arb_data):
    expected_keys = set()

    for ach in achievements:
        expected_keys.add(f"achievement_{ach['id']}_title")
        expected_keys.add(f"achievement_{ach['id']}_description")

    errors = []

    for locale, arb in arb_data.items():
        for key in expected_keys:
            if key not in arb:
                errors.append(f"[{locale}] Missing key: {key}")

    if errors:
        print("\n❌ ARB Validation Errors:")
        for e in errors:
            print("  ", e)
        sys.exit(1)


# ----------------------------
# CSV Generators
# ----------------------------


def generate_metadata(achievements, arb_en):
    rows = []
    for order, ach in enumerate(achievements, start=1):
        title_key = f"achievement_{ach['id']}_title"
        desc_key = f"achievement_{ach['id']}_description"

        rows.append(
            [
                arb_en.get(title_key),
                arb_en.get(desc_key, ""),
                "True" if ach["progress_type"] in ["cumulative", "streak"] else "False",
                ach["target"] if ach["target"] else "",
                "Revealed",
                ach["points"],
                order,
            ]
        )
    return rows


def generate_localizations(achievements, arb_data):
    rows = []
    for locale, arb in arb_data.items():
        if locale == "en-US":
            continue
        for ach in achievements:
            title_key = f"achievement_{ach['id']}_title"
            desc_key = f"achievement_{ach['id']}_description"
            english_name = arb_data["en-US"].get(title_key)
            rows.append(
                [
                    english_name,
                    arb.get(title_key),
                    arb.get(desc_key, ""),
                    locale,
                ]
            )
    return rows


def generate_icons(achievements, arb_en):
    rows = []
    tier_counts = defaultdict(int)

    for ach in achievements:
        icon = get_tier(ach["points"])
        tier_counts[icon] += 1

        title_key = f"achievement_{ach['id']}_title"
        english_name = arb_en.get(title_key)

        rows.append([english_name, icon])

    return rows, tier_counts


# ----------------------------
# Writer
# ----------------------------


def write_csv(path, rows):
    with open(path, "w", newline="", encoding="utf-8") as f:
        csv.writer(f).writerows(rows)


# ----------------------------
# Icon handling
# ----------------------------


def copy_icons_to_output(out_dir):
    for icon in REQUIRED_ICONS:
        shutil.copy(Path("icons") / icon, Path(out_dir) / icon)


# ----------------------------
# Zip
# ----------------------------


def create_zip(out_dir):
    zip_path = Path(out_dir) / "achievements_upload.zip"
    with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as zipf:
        for file in Path(out_dir).glob("*"):
            if file.suffix in [".csv", ".png", ".jpg", ".jpeg"]:
                zipf.write(file, arcname=file.name)
    print(f"\n📦 ZIP created: {zip_path}")


# ----------------------------
# CLI
# ----------------------------


def main():
    parser = argparse.ArgumentParser(
        description="Generate Google Play Games achievement CSV files."
    )

    parser.add_argument("--registry", required=True)
    parser.add_argument("--arb-dir", required=True)
    parser.add_argument("--out", default="output")
    parser.add_argument("--dry-run", action="store_true")
    parser.add_argument("--zip", action="store_true")

    args = parser.parse_args()

    validate_registry(args.registry)
    validate_arb_dir(args.arb_dir)
    validate_icons_dir()

    achievements = parse_registry(args.registry)
    arb_data = load_arb_files(args.arb_dir)

    if "en-US" not in arb_data:
        sys.exit("❌ English ARB (app_en.arb) missing.")

    total_points = validate_points(achievements)
    validate_arb_consistency(achievements, arb_data)

    metadata = generate_metadata(achievements, arb_data["en-US"])
    localizations = generate_localizations(achievements, arb_data)
    icons, tier_counts = generate_icons(achievements, arb_data["en-US"])

    print("\n=== Achievement Build Report ===")
    print(f"Total Achievements: {len(achievements)}")
    print(f"Total Points: {total_points} / 2000")

    print("\nTier Distribution:")
    for tier, count in tier_counts.items():
        print(f"  {tier}: {count}")

    print("\nLocales:")
    for locale in arb_data.keys():
        print(f"  {locale}")

    print("\nARB Validation: PASSED")

    if args.dry_run:
        print("\nDry run complete. No files written.")
        return

    out_dir = Path(args.out)
    out_dir.mkdir(parents=True, exist_ok=True)

    write_csv(out_dir / "AchievementsMetadata.csv", metadata)
    write_csv(out_dir / "AchievementsLocalizations.csv", localizations)
    write_csv(out_dir / "AchievementsIconsMappings.csv", icons)

    copy_icons_to_output(out_dir)

    print("\nIcon Validation: PASSED")
    print(f"\n✅ CSV + Icons written to {out_dir}")

    if args.zip:
        create_zip(out_dir)


if __name__ == "__main__":
    main()
```

---

## Lessons Learned

- **Documentation beats AI guesses.** AI was great at quickly generating scripts, but struggled with platform-specific rules.  
- **Locale codes matter.** Don’t assume based on past knowledge.  
- **Step limits are strict.** Anything above 10,000 must be binary.  
- **Validation scripts save time.** Automate checks for points, locales, and duplicates before uploading.  

---

## Final Thoughts

Bulk uploading achievements is worth the effort once you set up a proper pipeline. AI can be a helpful assistant, but it’s not a substitute for documentation. The official docs remain the most reliable source when things break.

If you’re stuck, try isolating achievements one by one. It’s tedious, but it works.

---
Consider subscribing to my [YouTube channel](https://www.youtube.com/@areaswiftyone?sub_confirmation=1) & follow me on [X(Twitter)](https://x.com/areaswiftyone). Leave a comment if you have any questions. 

Share this article if you found it useful !