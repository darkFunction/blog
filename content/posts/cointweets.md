---
title: "Identifying cryptrocurrency spam/hype cycles on Twitter"
date: 2018-09-08
tags: [ "python", "cryptocurrency" ]
---

Inspired by a very interesting [Defcon presentation about stock spammers](https://youtu.be/ytDamqTjPwg) from 2011, I thought it would be interesting (and maybe profitable?) to monitor the frequency of low-cap cryptocurrency mentions on Twitter, the theory being that pump groups and spammers are likely to 'hype' coins before/during large buys in order to manipulate more people into buying. 

The free tier on the Twitter streaming API allows you to monitor 400 different terms so I chose to observe the Twitter stock symbol '$' followed by the coin abbreviations, for example $SIA is Siacoin. Ideally we could monitor more intelligently by using smarter rules around which coin a tweet is referring to, but as a starting point this seemed pretty good as the '$' symbol seems to have been fairly widely adopted across Twitter for crypto market discussion.

I collected a few samples across a few days at different points but didn't see any correlation between sudden Tweet frequency spikes and price. This is just a first exploration and I think it would be useful to plot alongside the coin price on the same graph rather than doing manual comparisons.

![Tweets](/images/tweets.png)


