---
layout:     post
title:      "Learn Go Project\x3a IRC Log Search"
date:       2013-10-25 12:00:00
categories: projects
---

*How to Use*

Visit the repo at <https://github.com/grschafer/quassel-search-go> and follow the README instructions.


*What*

I've recently gotten back into sitting on IRC channels related to software I'm using/making as another avenue for learning. I set up a [Quassel Core](http://quassel-irc.org/) which has been great for not missing any messages and connecting from multiple computers, but, as a friend pointed out, searching the backlog of messages seems pretty inefficient. (You have to continuously scroll up in the correct channel to fetch past messages from the core, then ctrl+f within your window to find text.) My friend's observation, plus my attendance of a recent Go talk, inspired me to take a break from my ongoing [Rails/Dota2 project]({% post_url 2013-09-24-rails-project-status %}) to learn Go and make a tool for searching Quassel logs more quickly.

![Results page][screenie]

Go has been a lot of fun to work with, simply for the variation (e.g. - it's been a while since I've regularly worked in a compiled language) and the chance to learn something new. It took a while to get used to Go's simplicity after coming from the flexibility of Python/Ruby and I certainly missed having some features (list comprehensions, ternary operator/inline if, exceptions). But, not having those features made me slow down and strive for more idiomatic code. Perhaps the most notable change for me was how easy it was to catch edge cases and errors, because of all the explicit errors that you *must* do something with if you receive them.


*Tech*

* Source: <https://github.com/grschafer/quassel-search-go>
* Go with sqlite3 and postgres drivers:
    * <https://github.com/mattn/go-sqlite3>
    * <https://github.com/lib/pq>
* Config file parser: <https://code.google.com/p/gcfg/>


[screenie]: /assets/images/quassel_search/results_page.png "Results page"
