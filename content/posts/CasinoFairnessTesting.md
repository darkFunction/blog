---
title: "Casino fairness testing"
date: 2014-12-13
tags: [ "python" ]
---

Been playing around with [Sikuli](http://www.sikuli.org) for automating a slots game and outputting the results to a simple [Google Charts](https://developers.google.com/chart/) page on a local webserver. Got it running under Crunchbang and threw some machines up. Sikuli is a really good framework (maybe the best for multi-purpose Linux automation?), and while the fuzzy image search is a real time saver, it sucks at differentiating between colours. If two images are the same in all aspects except colour, it's unreliable, and you need to start comparing pixels manually with `java.awt.Robot`.

![Casino](https://pbs.twimg.com/media/B4wlbnhIAAEti1z.jpg:large)
