---
layout:     post
title:      "Processing\x3a Spinmaze"
date:       2011-01-24 12:00:00
categories: projects
thumb: "/assets/images/spinmaze/3_thumb.png"
tags: processing a* graphs
---

<iframe width="668" height="720" scrolling="no" frameborder="0" src="http://www.openprocessing.org/sketch/46412/embed/?width=640&height=640&border=true"></iframe>

[![Initial state][thumb1]][img1] [![Victory + debug][thumb2]][img2] [![Big maze + enemies][thumb3]][img3]

*How to Play*

Play in the applet above, or [download the source][src] and open it in the
Processing IDE

* Move mouse to rotate
* CTRL to pause (to get to UI elements)
* ` (backtick/tilde) for debug info
* 1/2/3/4 to change ball physics
* R to reset
* More settings and controls accessible in the UI at the top

*What*

Inspired by Jamis Buck's series of [blog posts on maze algorithms][mazes] that
showed up on Hacker News, I added physics to maze-solving and ended up with
this game, which is played by rotating the maze and using gravity to escape the
maze and avoid enemies.

*Tech*

[Processing][proc] with [fisica library][fisica] for physics, [controlP5][cp5]
for UI elements, and [AILibrary][ailib] for A*


[thumb1]: /assets/images/spinmaze/1_thumb.png "Initial state"
[thumb2]: /assets/images/spinmaze/2_thumb.png "Victory + debug"
[thumb3]: /assets/images/spinmaze/3_thumb.png "Big maze + enemies"
[img1]: /assets/images/spinmaze/initial_state.png
[img2]: /assets/images/spinmaze/victory_debug.png
[img3]: /assets/images/spinmaze/bigmaze_enemies.png
[src]: /assets/projects/spinmaze/spinmaze.zip
[mazes]: http://weblog.jamisbuck.org/2011/2/7/maze-generation-algorithm-recap
[proc]: http://processing.org/
[fisica]: http://www.ricardmarxer.com/fisica/
[cp5]: http://www.sojamo.de/libraries/controlP5/
[ailib]: http://www.robotacid.com/PBeta/AILibrary/Pathfinder/index.html
