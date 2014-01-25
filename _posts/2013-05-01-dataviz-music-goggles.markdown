---
layout:     post
title:      "DataViz\x3a Music Goggles"
date:       2013-05-01 12:00:00
categories: projects
thumb: "/assets/images/music_goggles/1_thumb.png"
tags: [raspberry pi, python, pygame, aubio]
---

*What*

After many software projects in our Data Visualization class (we scraped
Twitter for [DHS keywords][dhs],
[blended Toy Story]({% post_url 2013-02-15-dataviz-toy-story-blended %}),
and mapped filming locations with Three.js), we decided to try something with
hardware.

We used a Raspberry Pi to play music and to control LEDs set in goggles in
time with the music. The goggles were sandblasted so that the wearer would be
immersed/isolated within the music and lights.

For making the goggles, we started with chemistry/lab goggles and a breadboard
on top, then eventually moved on to welder's goggles which were easier to
mount LEDS in.

![Goggle versions][versions]

After lots of wiring, soldering, and writing python to run on the pi, the
goggles were done! It's definitely not the prettiest headgear, but it worked
on the first try incredibly.

![Goggles done][gogdone]

After building the goggles was complete, we did a lot of code-tweaking to
match the music better and to be more visually pleasing. The below video shows
one of our early efforts, when it was still a bit seizure-ific:

<iframe width="420" height="315" src="//www.youtube.com/embed/xQRHbbmhIqk?rel=0" frameborder="0" allowfullscreen></iframe>

We made it less flashy by lowering our beat detection threshold (so the color
would change less often) and by keeping the LEDs on between beats.
Unfortunately, we didn't get a more up-to-date video or one with synced audio.
Check below for the code and specific tools/libraries that we used.

*Tech*

* Source: <https://gist.github.com/grschafer/01a3c2db59a3f4f2d7eb>
* Python with [pygame][pygame] for audio playback
* [Aubio][aubio] for pitch and onset/beat detection in music
* [smithje/RGB\_LED\_Driver][ledlib] for LED control on a Raspberry Pi
* Raspberry Pi


[dhs]: http://www.dailymail.co.uk/news/article-2150281/REVEALED-Hundreds-words-avoid-using-online-dont-want-government-spying-you.html
[versions]: https://lh4.googleusercontent.com/-CscmRg-GM8s/Ufvw47JSSZI/AAAAAAAAJAc/Hbdbucul5UY/w1127-h845-no/20130424_141258.jpg "Two versions"
[gogdone]: https://lh4.googleusercontent.com/-f0o1leNuS6c/Ufvw44aw-uI/AAAAAAAAJAc/hbYTCwQVe80/w1127-h845-no/20130428_221440.jpg "Finished goggles"
[pygame]: http://pygame.org/
[aubio]: http://aubio.org/
[ledlib]: https://github.com/smithje/RGB_LED_Driver
