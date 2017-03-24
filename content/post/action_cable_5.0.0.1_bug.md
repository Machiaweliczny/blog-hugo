+++
tags = ["Rails","ActionCable"]
categories = [""]
draft = false
description = ""
date = "2017-03-24T20:48:41+01:00"
title = "action_cable_5.0.0.1_bug"

+++

There is quite nasty bug in ActionCable 5.0.0.1 which I had to deal with recently:

As we can read in [changelog for 5.1.0](https://github.com/rails/rails/blob/5-1-stable/actioncable/CHANGELOG.md) there was a bug:

Protect against concurrent writes to a WebSocket connection from multiple threads; the underlying OS write is not always threadsafe.

Tracking this was hard because my application simply didn't receive some messages when sidekiq jobs were trying to send messages simultanously. Message not delivered and no error raised => fun debugging experience :)

Leaving this as short notice in case some of you are wondering why you might be losing messages with ActionCable 5.0.0.1.
