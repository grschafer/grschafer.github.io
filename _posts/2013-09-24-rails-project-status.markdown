---
layout:     post
title:      Rails Project Status Update
date:       2013-09-24 15:00:00
categories: projects
---

This post will document and follow overall progress on my current (Autumn 2013) Rails project.

### Project Motivation

After graduating university and traveling for a while, I've opted to wait a few months before diving into the job search and start of my career. This "gap year"-esque time has been a great opportunity to hang out with friends and pursue personal interests/projects such as this one.

I started this project primarily to learn Rails and its surrounding ecosystem of tools (I'm hoping to get comfortable with haml, sass, coffeescript, rspec, and more). I'm centering the project around the video game [Dota 2] to help me sustain interest/focus/motivation and because this area of development (analyzing game replays) is relatively new -- much of the data in game replays is undocumented and must be discovered and understood through exploration. For example, [here are the protobufs][pbufs] present in replay files and, although all the fields are labeled, it is difficult in many cases to tell what they correspond to in the game, especially when fields are re-used for different events and messages.

### Project Description

Using the replay parsing library [Skadi] and its wrapper [Tarrasque], I will be making an analysis/stats website with Rails. It will parse insightful (I hope!) and new stats from competitive/tournament game replays in the Python backend and display them in a Rails website. Longer term, I'm hoping to automate replay fetching (so the site will keep up with the most recent tournaments), reveal multi-game or global trends and correlations, and allow users to upload their own replays for analysis (to make use of [Steam OpenID]({% post_url 2013-09-07-steam-openid-and-webapi-with-rails %}) and so the website has more interactivity than just the javascript kind).

### Project Schedule

My current schedule, which I can change at will to make it look like I'm hitting every deadline.  =)

A dump of the textfile where I'm tracking/updating my schedule:

```
schedule
	every sunday: shell/vim/tooling improvement

by Sept 21
	completed list of individual-game components
	make python stuff plugin-ish (module directory and all classes follow superclass/interface, get called every tick...pre/post hooks?)
	learn: skadi/tarrasque, d3.js?
	log:
		16 - ward map, schedule, project structure
		17 - runes, roshan, gold/xp, buyback
		18 - kill list
		19 - escapes, hero positions, farming routes
		20/21/22 - farming routes, yet unfinished  ='(

by Sept 28
	prepare website for deployment
	learn: testing, analytics (google or other?), styling (SASS), coffeescript, asset pipeline, d3.js, ember/backbone/angular?
	log:
		23 - collected all data from TI3 grandfinals, started partials
		24 - partials
		25 - 
		26 - 
		27 - 
		28 - 

by Oct 5
	deployed website demonstrating components for all TI3 games
	analysis blogpost (on reddit?)
	learn: heroku/rackspace/aws deployment

by Oct 12
	make new component and deploy (voronoi diagrams -- animated and possession number)
	new analysis blogpost
	learn: deployment maintenance, capistrano?, minimize downtime
	
by Oct 19
	make new multi-game component and deploy
	new analysis blogpost
	learn: database, performance

	... continue weekly posts/feature-adds?

by Oct 26, Nov 2, Nov 9, Nov 16, Nov 23
	allow user-uploaded replays, which are analyzed by individual-game components
	also keep auto-replay fetching (via steam login) but don't share the url (make sure it's throttled?)
	future weeks: analyze multi-game? (ie - if user uploads multiple of their games, detect that and analyze across them)
	learn: permissions, background jobs, queues, async/websockets/webworkers?
```

[Dota 2]: http://dota2.com
[pbufs]: https://github.com/skadistats/skadi/tree/master/protobuf
[Skadi]: https://github.com/skadistats/skadi
[Tarrasque]: https://github.com/skadistats/Tarrasque
