---
layout: note
title: Easy switching of HTTP proxy on Linux
description: Easy switching of HTTP proxy on Linux
tags: ["desktop linux", "http", "proxy", "privoxy", "ubuntu", "command line", "sh"]
---
## The problem
HTTP proxy setup on desktop Linux is a disaster. Switching from home to office locations regularly is a pain. The GUI is flaky with no command line support. It doesn't know about subversion, maven or any number of other more obscure tools that require individual proxy set up.

## In brief
  1. Install a local non-caching proxy that can forward onwards to an upstream proxy.
  2. Point all tools at this proxy. This step is once only and is not covered by this note.
  3. Script the configuration of this proxy to turn forwarding on or off as required.

## In detail
  1. Install [Privoxy][1].
  2. Configure privoxy for forwarding. In `/etc/privoxy/config`:
          forward   /            upstream.http.proxy.org:8080

  3. Put `proxy-off` script on path:
          #!/bin/sh
          prog=`basename $0`
          test "X$SUPERCMD" = "X$prog" ||
                              exec /usr/bin/super -r $0 $prog ${1+"$@"}

          sed -i "s/^\(\s*\)forward\([\/\sA-Za-z0-9\.:]*\)/\#\1forward\2/" /etc/privoxy/config

  4. Put `proxy-on` script on path:
          #!/bin/sh
          prog=`basename $0`
          test "X$SUPERCMD" = "X$prog" ||
                              exec /usr/bin/super -r $0 $prog ${1+"$@"}

          sed -i "s/^\#\(\s*\)forward\([\/\sA-Za-z0-9\.:]*\)/\1forward\2/" /etc/privoxy/config

  5. Add `/etc/super.tab` entires for `proxy-off` and `proxy-on`:
          proxy-off           /path/to/proxy-off        username
          proxy-on            /path/to/proxy-on         username

## Limitations
  * `proxy-off` and `proxy-on` scripts will enable / disable all forward rules in privoxy config. Maybe make `sed`s more specific.
  * No FTP support.

## Side-effects
  * Privoxy filtering features are really handy.

[1]: http://www.privoxy.org/ "Privoxy is a non-caching web proxy..."
