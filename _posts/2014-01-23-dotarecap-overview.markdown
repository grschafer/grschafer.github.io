---
layout:     post
title:      DotaRecap Overview
date:       2014-01-23 15:00:00
categories: projects
---


Deployed URL: <http://www.dotarecap.com>

### Screenshots

[![Initial state][thumb1]][img1] [![Victory + debug][thumb2]][img2] [![Big maze + enemies][thumb3]][img3]

### Project Motivation

After graduating university and traveling for a while, I opted to wait a few months before diving into the job search and start of my career. This "gap year"-esque period has been a great opportunity to spend time with friends and pursue personal interests/projects such as this one.

I started this project primarily to learn Rails and its ecosystem, as well as various other tools (the <a href="#techlist">full list is under the Project Description</a>). I centered the project around the video game [Dota 2] to help me sustain interest/focus/motivation and because this area of development (analyzing game replays) is relatively new -- much of the data in game replays is undocumented and must be discovered and understood through exploration. For example, [here are the protobufs] present in replay files and, although all the fields are labeled, it is difficult in many cases to tell what they correspond to in the game, especially when fields are re-used for different events and messages.


### Project Description

[DotaRecap.com] is a website for viewing lossy replays (recaps) of [Dota 2] games (matches). It extracts specific data (such as player health, kills, deaths, gold, experience, positions, etc.) from game replay files and displays those on a web page with a time slider, allowing users to examine these variables while scrubbing through the duration of the game. DotaRecap lets you use a browser (mobile, if you like) to quickly see how a game played out, without needing to use or download a game client, large replay files, or video casts. The downside, of course, is the loss of fidelity: you won't be able to see the art, 3d animation, player nuances/tactics, or hear commentators, as you would when spectating in-game.

DotaRecap's functionality is contained within 3 repos:

* [dota2rails]: Contains the rails app. Notably, instead of using ActiveRecord, I have pulled and persisted hashes to/from [mongodb] thus far. I think the flexibility and simplicity of this approach has been beneficial overall (given that it's a 1-person project), because my data and idea for the site have both changed frequently. This repo also contains lots of [d3.js] for displaying replay data.
* [alacrity]: Contains replay parsers and [celery] tasks in python. The parsers are built on onethirtyfive's [skadi] and bluepeppers's [tarrasque] libraries, which do the hard work of exposing the data within Dota 2's binary replay files. The celery tasks are responsible for updating league and match lists in mongodb, fetching data from the [steam webapi], downloading matches, running the parsers on matches, etc.
* [matchurls]: Contains a node server for fetching Dota 2 replay download urls. This is a barely-modified fork of [the original, written by RJackson].

<a name="techlist"></a>These repos use and are supported by a variety of tools that I enjoyed learning, including: [coffeescript], [sass], [d3.js], [celery] backed by [rabbitmq], [mongodb], [aws s3], [nginx], [unicorn], [vagrant], and [ansible]). Regarding the last two (vagrant and ansible), there is a 4th repo ([dota2rails-deploy]) that includes the other 3 as submodules (so I can keep their compatible versions in sync) and that I use to deploy the whole site with minimal manual work.


### Project Architecture

Horizontal scalability has been a primary goal of mine in the design of this project, and I believe I've built a good foundation toward achieving that goal, though I still need to do the load-testing to actually _prove_ it. Beyond keeping scalability in mind, my design was guided by whatever tools and services I wanted to gain exposure to at the time.  =)

With that in mind, the following slides show the current architecture (where all the different services and software are running on 2 vps machines, labeled web and mq), the target architecture (which includes changes still needed to facilitate horizontal scaling), and how all the different parts interact under various usecases.

<script async class="speakerdeck-embed" data-id="3843bd1065ca0131a8e94ae38a915c91" data-ratio="1.77777777777778" src="//speakerdeck.com/assets/embed.js"></script>

I would love any questions, discussion, and critiques of the design -- though I would like to throw out there that some aspects of the design are a result of how it evolved and changed, so they don't make much sense anymore and are on my list of things to fix (e.g. - celery currently has a cronjob to check mongodb for user-requested/uploaded replays, where ideally, the rails app would communicate those requests and uploads directly to rabbitmq, cutting out the polling and the wait between cron fires).

Some more aspects of the design that contribute to its scalability and distributability:

* Using Session::CookieStore saves on database calls and negates the need for sticky sessions
* Almost all of a page's payload (js, css, images, parsed match data) can be served by a CDN
* Celery is inherently distributable: just add more machines/workers and point them at the same rabbitmq (cluster)
* Celery has rate-limiting and retry policy control built in
* Rabbitmq pushes messages to consumers (celery workers), rather than consumers using a polling mechanism

### Future Improvements

Despite how far this project has come, my todo list for it only seems to keep growing. Here are some of the changes I would like to tackle:

* Reach the target architecture (load-balance among multiple [unicorn] servers that share a [memcached] cache, use a CDN for all assets, put mongodb and rabbitmq on separate boxes).
* Port replay parsers from [skadi]/[tarrasque], which were recently deprecated, to [smoke].
* Consider switching from [mongodb] to an rdbms (with ActiveRecord?), because the data model is stabilizing and the data is inherently relational (leagues have many matches, which have many players).
* Connect the rails app to rabbitmq (instead of via mongodb and polling/cron as it currently is). This could also enable task status and ETA displays on the website.
* Testing! This is something I've overlooked in general, due to it being a rapidly-changing and one-person project.
* Server metrics (via something like [nagios]?).
* Add rate-limiting on the rails app.
* UX improvements (tooltips, better error-handling and error messages, faster and more responsive javascript, etc.).
* Investigate streaming parse results? Instead of user's requesting/uploading a replay then getting a link to their parsed replay later, could the frontend load the parsing results while the parsing is still ongoing? Would need auto-scaling number of celery workers.


### Acknowledgements

* Thanks to [onethirtyfive], [bluepeppers], [RJacksonm1], and everyone on the #dota2replay QuakeNet IRC for their software and assistance with understanding and parsing Dota 2 replays.
* Ansible playbooks by [leucos] and [owainlewis] that I built upon and learned from.
* Everyone who builds and writes documentation for all the software used in this project!


[Dota 2]: http://dota2.com
[here are the protobufs]: https://github.com/skadistats/skadi/tree/master/protobuf
[dota2rails]: https://github.com/grschafer/dota2rails
[alacrity]: https://github.com/grschafer/alacrity
[skadi]: https://github.com/skadistats/skadi
[tarrasque]: https://github.com/skadistats/Tarrasque
[steam webapi]: http://wiki.teamfortress.com/wiki/WebAPI#Dota_2
[matchurls]: https://github.com/grschafer/matchurls
[the original, written by RJackson]: https://github.com/RJacksonm1/matchurls
[mongodb]: http://www.mongodb.org/
[d3.js]: http://d3js.org/
[celery]: http://www.celeryproject.org/
[coffeescript]: http://coffeescript.org/
[sass]: http://sass-lang.com/
[rabbitmq]: http://www.rabbitmq.com/
[aws s3]: http://aws.amazon.com/s3/
[nginx]: http://wiki.nginx.org/Main
[unicorn]: http://unicorn.bogomips.org/
[vagrant]: http://www.vagrantup.com/
[ansible]: http://www.ansibleworks.com/
[dota2rails-deploy]: https://github.com/grschafer/dota2rails-deploy
[haproxy]: http://haproxy.1wt.eu/
[memcached]: http://memcached.org/
[smoke]: https://github.com/skadistats/smoke
[nagios]: http://www.nagios.org/
[onethirtyfive]: https://github.com/onethirtyfive
[bluepeppers]: https://github.com/bluepeppers
[RJacksonm1]: https://github.com/RJacksonm1
[leucos]: https://github.com/leucos/ansible-rbenv-playbook
[owainlewis]: https://github.com/owainlewis/vagrant-ansible-rails
[DotaRecap.com]: http://www.dotarecap.com
[thumb1]: /assets/images/dotarecap/1_thumb.png "Index page"
[thumb2]: /assets/images/dotarecap/2_thumb.png "Match page"
[thumb3]: /assets/images/dotarecap/3_thumb.png "Uploading a replay"
[img1]: /assets/images/dotarecap/index.png
[img2]: /assets/images/dotarecap/show.png
[img3]: /assets/images/dotarecap/upload.png
