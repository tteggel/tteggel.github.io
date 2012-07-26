---
layout: note
title: Adding PDF and Pepper Flash to chromium build
description: Adding PDF and Pepper Flash to chromium build
tags: ["desktop linux", "chromium", "google chrome", "google", "chrome"]
---
## The problem
Chromium is easy to build from source to get latest version built specifically for current setup. But it's missing the PDF and PPAPI Flash (Pepper Flash) plugin. PDF support is useful for print preview. Pepper Flash is actively supported by Adobe and Google on Linux since [Adobe discontinued support for the standalone player][1] therefore this is the only way to get latest security patches for Flash on Linux.

## In brief
Download Google Chrome deb from Google repo and unpack `libpdf.so` and `PepperFlash` directory to chromium `out` directory.

## In detail
  1. Download and install Google Chrome and accept EULA first.

  2. Set `$CHROMIUM_BASE` to `chromium/src` directory.

  3. The following script will add the latest PDF and Pepper Flash support to a freshly built chromium.
          #!/bin/bash
          rm -rf /tmp/chromedeb
          mkdir -p /tmp/chromedeb

          pushd /tmp/chromedeb
          wget http://dl.google.com/linux/direct/google-chrome-unstable_current_amd64.deb
          ar -x google-chrome-unstable_current_amd64.deb
          tar -xJf data.tar.lzma
          popd

          rm -f $CHROMIUM_BASE/out/Release/libpdf.so
          mv /tmp/chromedeb/opt/google/chrome/libpdf.so $CHROMIUM_BASE/out/Release
          chmod 644 $CHROMIUM_BASE/out/Release/libpdf.so

          rm -rf $CHROMIUM_BASE/out/Release/PepperFlash
          mv /tmp/chromedeb/opt/google/chrome/PepperFlash $CHROMIUM_BASE/out/Release
          chmod 644 $CHROMIUM_BASE/out/Release/PepperFlash/libpepflashplayer.so

          rm -rf /tmp/chromedeb

[1]: http://blogs.adobe.com/flashplayer/2012/02/adobe-and-google-partnering-for-flash-player-on-linux.html "Adobe and Google Partnering for Flash Player on Linux"
