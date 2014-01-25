---
layout:     post
title:      Machine Learning Bejeweled
date:       2012-12-10 12:00:00
categories: projects
thumb: "/assets/images/bejeweled/1_thumb.png"
tags: python pybrain ml
---

*Source*

<https://github.com/grschafer/BejeweledBot>

*What*

The final project of my university Machine Learning course was very open-ended,
allowing me to take a different stab at Bejeweled from [what I made for
Swapples]({% post_url 2012-12-10-machine-learning-bejeweled %}).

After a semester spent covering decision trees, neural networks, naive bayes,
and a few other supervised-learning algorithms, I opted to explore a new topic
for my final project: [reinforcement learning][rl]. This approach to learning
interested me because it didn't need labeled data, just an interactive
environment for it to explore. My implementation used [PyBrain][pybrain] and
was coded to only know that it can swap pieces (it didn't know that matching 3
of the same color would yield a positive reward).

The video below shows how the reinforcement learner played Bejeweled when it
was just starting out (no state-action pairs have any associated value):

<iframe width="420" height="315" src="//www.youtube.com/embed/ew_O0HdWd5Q?rel=0" frameborder="0" allowfullscreen></iframe>

Sometimes it gets lucky by chance and makes a match, which gives a positive
reward and helps the program learn that configuration of pieces for the future.
After playing at top speed for a few hours, the bot plays much better, as shown
in the video below:

<iframe width="420" height="315" src="//www.youtube.com/embed/7wey-S6H38E?rel=0" frameborder="0" allowfullscreen></iframe>

It was really cool to see a piece of code go from oblivious to expert-level in
a game without any guidance. For the specific case of Bejeweled, though, it
would have been much easier to just write a program that knew how to play it
-- I'll have to make my next application of reinforcement learning do or solve
something I don't understand.  =)

*Tech*

* Python with [PyBrain][pybrain] for reinforcement learning base classes
* [Pygame][pygame] for demoing

*Thanks*

* Al Sweigart for his [Bejeweled implementation in pygame][bejeweled] that
allowed me to demo the program graphically and helped me understand what the
learner was doing!
* Osmic for the [icons][gems] used in Al's game


[rl]: http://en.wikipedia.org/wiki/Reinforcement_learning
[pybrain]: http://www.pybrain.org/
[pygame]: http://pygame.org/
[bejeweled]: http://inventwithpython.com/blog/2011/06/24/new-game-source-code-gemgem-a-bejeweled-clone/
[gems]: http://opengameart.org/content/gem-jewel-diamond-glass
