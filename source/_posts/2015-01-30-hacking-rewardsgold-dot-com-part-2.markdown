---
layout: post
title: "Hacking RewardsGold.com: Part 2"
date: 2015-01-30 23:22:05 -0500
comments: true
categories: 
---
If you haven't read part one, you can find it [here](http://joshuabamboo.github.io/blog/2015/01/25/hacking-rewardsgold-dot-com/). If you have, you'll recall that I wrote a script to submit the invite form for points on rewardsgold.com. This works perfectly fine; however, while tinkering with their form I discovered a flaw.

Beware of form data. This one line of code left Rewards Gold vulnerable:

```html
<input type="hidden" name="points" value="500">
```

They actually keep their point value as a hidden input in their form. By simply inspecting the element in Chrome, you can alter that "500" to any number you like. And just like that, when you submit the form your point count will increase by whatever number you choose. Pro tip: The Economist is 8600 points. :)