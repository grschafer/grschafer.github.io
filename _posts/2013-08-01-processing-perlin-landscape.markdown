---
layout:     post
title:      "Processing\x3a Perlin Landscape"
date:       2013-08-01 13:00:00
categories: projects
---

<iframe width="668" height="560" scrolling="no" frameborder="0" src="http://www.openprocessing.org/sketch/46408/embed/?width=640&height=480&border=true"></iframe>

[![Default settings][thumb1]][img1] [![Edges + boxy + water][thumb2]][img2] [![Noisy][thumb3]][img3]

*How to Play*

Play in the applet above, or [download the source][src] and open it in the
Processing IDE

* Move the mouse to "move" the landscape
* Hold CTRL to move the mouse without moving the landscape (to get to UI
elements)
* Click and drag to move the camera
* More settings and controls accessible in the UI at the top

*What*

Inspired by [Notch's hints at terrain generation in Minecraft][minecraft], I
wanted to see if I could replicate the landscape. Please note that it looks a
lot better in OpenGL mode, which you can see by downloading the source and
running it in the Processing IDE.

*Tech*

[Processing][proc] with [controlP5][cp5] for UI elements

*When*

Fall 2010


[thumb1]: /assets/images/perlin/1_thumb.png "Default settings"
[thumb2]: /assets/images/perlin/2_thumb.png "Edges + boxy + water"
[thumb3]: /assets/images/perlin/3_thumb.png "Noisy"
[img1]: /assets/images/perlin/default_settings.png
[img2]: /assets/images/perlin/edges_boxy_water.png
[img3]: /assets/images/perlin/noisy.png
[src]: /assets/projects/perlin/explore_perlin.pde
[minecraft]: http://notch.tumblr.com/post/3746989361/terrain-generation-part-1
[proc]: http://processing.org/
[cp5]: http://www.sojamo.de/libraries/controlP5/
