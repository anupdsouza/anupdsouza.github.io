---
layout: post
published: true
title: "Fixing Broken Wi-Fi Connectivity on macOS Tahoe"
date: 2026-02-04 11:55:00 +0530
image: '/images/posts/macos-tahoe-wifi-fix/featured.jpg'
description: "Connected to Wi-Fi but no internet after updating macOS? If you use Charles Proxy or similar tools, here is the fix."
excerpt: "Updated to macOS Tahoe and lost internet connectivity? The culprit might be your developer proxy settings. Here is how to fix it."
seo_title: "Fix macOS Wi-Fi Issues: Disable Proxies after Update"
seo_description: "How to fix Wi-Fi connected but no internet issues on macOS Tahoe caused by Charles Proxy and development tools."
categories:
  - Troubleshooting
tags:
  - macOS Tahoe
  - Wi-Fi
  - Charles Proxy
---
<p align="center" style="font-size: 0.85rem;">
Photo by <a href="https://unsplash.com/@yapics?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Leon Seibert</a> on <a href="https://unsplash.com/photos/internet-led-signage-beside-building-near-buildings-2m71l9fA6mg?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
</p>      

## The Problem

Updating macOS is usually a smooth process, but the recent update to **macOS Tahoe** left my machine in a weird state. I was connected to Wi-Fi with full signal strength, yet I had zero internet connectivity. Browsers timed out, and `ping` requests went nowhere.

I tried the standard troubleshooting steps: restarting the router, forgetting the network, and renewing the DHCP lease. **None of them worked.**

## The Developer Variable

As developers, our machines aren't standard. We often configure network interception tools to debug API calls or mobile traffic. If you have apps like **Charles Proxy**, **Proxyman**, or **Fiddler** installed, they often modify system proxy settings to capture traffic.

It turns out the OS update didn't play nice with these lingering configurations, leaving the system looking for a proxy that wasn't running.

---

## The Fix

If you are stuck in the "Connected but No Internet" limbo, here is what worked for me.

1.  Open **System Settings** and go to **Wi-Fi**.
2.  Find your *connected* network (the one that isn't working) and click **Details**.
3.  Select the **Proxies** tab on the left.
4.  Look for **Web proxy (HTTP)** and **Secure Web proxy (HTTPS)**.
5.  **Turn both of them OFF.**
6.  Click **OK** to save.

![macOS Proxy Settings](/images/posts/macos-tahoe-wifi-fix/macos-wifi-proxy-settings.jpg)

Your internet connection should return immediately, no restart required.
Surprisingly, re-configuring Charles proxy posed no issues thereafter.

---
Consider subscribing to my [YouTube channel](https://www.youtube.com/@areaswiftyone?sub_confirmation=1) & follow me on [X(Twitter)](https://x.com/areaswiftyone). Leave a comment if this helped you out.