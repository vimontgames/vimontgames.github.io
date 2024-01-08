---
title: "Goatcounter"
date: 2024-01-08T0:0:0-00:04
categories:
  - Blog
tags:
  - GoatCounter
  - Analytics
---

I hope you don't mind but I was curious to see if anyone but me would read this blog, 
so I added website traffic analytics using [GoatCounter](https://www.goatcounter.com/).

It was surprisingly easy to setup, as I was already using [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes)'s 
[remote theme starter](https://github.com/mmistakes/mm-github-pages-starter/) I only add to :

#### Step 1

- Create a free [GoatCounter](https://www.goatcounter.com/signup) account

#### Step 2
- Copy the '_layout' folder from the [Minimal Misktakes](https://github.com/mmistakes/minimal-mistakes/tree/master/_layouts)
theme's repo to my own fork of [remote theme starter](https://github.com/mmistakes/mm-github-pages-starter/)

#### Step 3

- Edit *_layouts/default.html* to add the last line juste before the closing **head** tag

```
    [...]
    <script data-goatcounter="https://vimontgamesgithubio.goatcounter.com/count" async src="//gc.zgo.at/count.js"></script>
</head>
```

You can check the counters at [vimontgamesgithubio.goatcounter.com](https://vimontgamesgithubio.goatcounter.com)


