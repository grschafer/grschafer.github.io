---
layout:     post
title:      "DataViz\x3a Toy Story Blended"
date:       2013-02-15 12:00:00
categories: projects
---

*What*

For a film-related project in my Data Visualization class, my group and I
wanted to compare color palettes of the three Toy Story movies throughout the
duration of the movies. During early experiments, we strayed from strictly
finding color palettes and settled on the idea of taking a moving average of
pixels and creating a movie from these average values.

The final results are shown in the videos below, but also check out the post we
made for the project at <http://toystoryblended.blogspot.com/>, which includes
more detailed process descriptions and plays all three movies simultaneously
and side-by-side.

<iframe width="560" height="315" src="//www.youtube.com/embed/1UAfgj-hLl4?rel=0" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="//www.youtube.com/embed/Be0NYMA8rTY?rel=0" frameborder="0" allowfullscreen></iframe>

<iframe width="560" height="315" src="//www.youtube.com/embed/tKJEBO1s7OQ?rel=0" frameborder="0" allowfullscreen></iframe>

*Tech*

* Source: <https://gist.github.com/grschafer/04f051d959b3c3c2501e>
* Python with [numpy][numpy] for array manipulation
* ffmpeg/avconv for converting between video files and frame images

*Credit*

* The Toy Story movies belong to Pixar and Disney

[numpy]: http://www.numpy.org/
