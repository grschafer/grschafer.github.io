---
layout:     post
title:      Omgpop Game Automation
date:       2012-01-16 12:00:00
categories: projects
thumb: "/assets/images/omgpop/1_thumb.png"
tags: python scikit-learn numpy autohotkey ocr
---

*What*

After being introduced to some of the games on Omgpop, the clear next step was
to automate them for fun. This first video shows Swapples, a Bejeweled-like
game. The board is screengrabbed then the tiles are visually recognized by an
SVM. The first half of the video shows the program running when the board is
allowed to "settle" (differencing subsequent frames gives <1% pixels changed).
The second half of the video shows the program running without waiting for the
board to settle, illustrating how much time is spent waiting and how fast it
could (ideally) make moves. I'm hoping to return to this project at some point
to make it smarter about choosing moves (prioritize 4-/5-in-a-row, hourglasses,
multipliers) and to make moves more quickly (make multiple moves per analyzed
screengrab by tracking how a move will affect the board).

<iframe width="560" height="315" src="//www.youtube.com/embed/BDNRC0w8K28?rel=0" frameborder="0" allowfullscreen></iframe>

This second video shows Letterblox, which I partially-automated by traversing a
trie of English words based on user-input letters. The trie was a nice exercise
in recursion, but it turned out that asking a plain dictionary/hashtable for
all permutations of the letters was faster.

<iframe width="560" height="315" src="//www.youtube.com/embed/sAAE5W9UKj4?rel=0" frameborder="0" allowfullscreen></iframe>

This third video shows Typow, which I automated by screengrabbing, OCRing,
then playing back the text.

<iframe width="560" height="315" src="//www.youtube.com/embed/C9K743W2EQQ?rel=0" frameborder="0" allowfullscreen></iframe>

*Tech*

* Swapples - Python with [scikits-learn SVMs][svm] for detecting tiles,
[PIL][pil] for screen grabbing, [numpy][numpy], and [SendKeys][sendkeys] for
sending clicks
* Letterblox - Python with [SendKeys][sendkeys] for sending keystrokes
* Typow - [AutoHotkey][ahk] script that uses [ImageMagick][imgmagick] for
screen grabbing and preprocessing, and [Tesseract][tess] to OCR the image


[svm]: http://scikit-learn.org/stable/modules/svm.html
[pil]: http://www.pythonware.com/products/pil/
[numpy]: http://www.numpy.org/
[sendkeys]: http://www.rutherfurd.net/python/sendkeys/
[ahk]: http://www.autohotkey.com/
[imgmagick]: http://www.imagemagick.org/script/index.php
[tess]: https://code.google.com/p/tesseract-ocr/
