---
layout:     post
title:      Rails Project Status Update
date:       2013-09-24 15:00:00
categories: projects
---

This post will document and follow overall progress on my current (Autumn 2013) Rails project.

Backend (Python) Repo: <https://github.com/grschafer/alacrity>

Frontend (Rails) Repo: <https://github.com/grschafer/dota2rails>

Deployed URL: <http://grschafer.dyndns.org/> (temporary)

### Project Motivation

After graduating university and traveling for a while, I've opted to wait a few months before diving into the job search and start of my career. This "gap year"-esque time has been a great opportunity to hang out with friends and pursue personal interests/projects such as this one.

I started this project primarily to learn Rails and its surrounding ecosystem of tools (I'm hoping to get comfortable with haml, sass, coffeescript, rspec, and more). I'm centering the project around the video game [Dota 2] to help me sustain interest/focus/motivation and because this area of development (analyzing game replays) is relatively new -- much of the data in game replays is undocumented and must be discovered and understood through exploration. For example, [here are the protobufs][pbufs] present in replay files and, although all the fields are labeled, it is difficult in many cases to tell what they correspond to in the game, especially when fields are re-used for different events and messages.

### Project Description

Using the replay parsing library [Skadi] and its wrapper [Tarrasque], I will be making an analysis/stats website with Rails. It will parse insightful (I hope!) and new stats from competitive/tournament game replays in the Python backend and display them in a Rails website. Longer term, I'm hoping to automate replay fetching (so the site will keep up with the most recent tournaments), reveal multi-game or global trends and correlations, and allow users to upload their own replays for analysis (to make use of [Steam OpenID]({% post_url 2013-09-07-steam-openid-and-webapi-with-rails %}) and so the website has more interactivity than just the javascript kind).

### Progress Log

Coming soon...

[Dota 2]: http://dota2.com
[pbufs]: https://github.com/skadistats/skadi/tree/master/protobuf
[Skadi]: https://github.com/skadistats/skadi
[Tarrasque]: https://github.com/skadistats/Tarrasque
