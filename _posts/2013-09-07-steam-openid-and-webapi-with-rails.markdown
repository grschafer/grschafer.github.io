---
layout:     post
title:      Steam OpenID and WebAPI with Rails
date:       2013-09-07 16:12:01
categories: guides
---


I recently became interested in stats and analysis of Dota 2, partially fueled by the stats overlays presented during streamed broadcasts of [The International 3][ti3], a Dota 2 tournament with a prize pool of nearly $2.9 million USD. To get started with making a stats website (such as [datdota][datdota] or [dotabuff][dotabuff]), you need to use the Steam WebAPI and (optionally) allow users to login to your website using Steam OpenID. This guide covers the process I followed in getting started using Steam OpenID and the Steam WebAPI with Ruby on Rails, followed with deployment to Heroku.

You can find this walkthrough's resulting repo on GitHub: <https://github.com/grschafer/dota2rails>


## Getting an API Key

To use Steam OpenID or the Steam WebAPI, you'll need to get an API Key.

1. Read <http://dev.dota2.com/showthread.php?t=58317>.

2. As mentioned in above forum post, get an API key from <http://steamcommunity.com/dev/apikey>.


## Making the Rails Site

For an introduction to making a first Rails app, check out [this tutorial][railsgs]. I have included all of the necessary steps below, but in less detail than the afore-linked tutorial.

1. Start a new Rails site (I called mine dota2rails). The '$' in code snippets indicates that that line is entered at the command prompt.

    ```bash
    $ rails new dota2rails
    ```

2. Let's grab a gem to handle the OpenID authentication for us.

    1. To use the [omniauth-steam gem][steamauthgem], add the following to the bottom of the Gemfile:
    
        ```ruby
        # Gemfile
        …
        # use Steam OpenID Omniauth gem for authenticating Steam users
        gem 'omniauth-steam'
        ```
    
    2. Install omniauth-steam and all of its dependencies (multi\_json, omniauth-openid, omniauth, hashie, rack-openid, ruby-openid) by running the following:
    
        ```bash
        $ bundle install
        ```

3. As instructed in the documentation for [omniauth][oa_instr1] and [omniauth-steam][oa_instr2], we need to configure the Rails middleware to use OmniAuth::Builder, so let's create a new file `config/initializers/omniauth.rb` and add the following lines to it:

    ```ruby
    # config/initializers/omniauth.rb
    Rails.application.config.middleware.use OmniAuth::Builder do
      provider :steam, ENV["STEAM_WEB_API_KEY"]
    end
    ```

4. We need to provide that API\_KEY to the environment somewhere. There are [a few different options for managing Rails environment variables][railsenv] -- I'll be using the [figaro gem][figaro]:

    1. Add the following to the bottom of the Gemfile:
    
        ```ruby
        # Gemfile
        …
        # gem for managing environment variables (steam webapi key)
        gem 'figaro'
        ```
    
    2. Run `bundle install` to install figaro. Next, generate the figaro config file and edit `.gitignore` (to avoid committing the config file to the repo) by running the following command:
    
        ```bash
        $ rails generate figaro:install
        ```
    
    3. Open the generated config file (located at `config/application.yml`) and add your API key:
    
        ```yaml
        # config/application.yml
        STEAM_WEB_API_KEY: '234F789DC78E6A88B987AD87F00F'
        ```
    
    4. If you're planning on uploading your repo (i.e. to GitHub), you should also hide your application's secret token. To do so, move the secret token in `config/initializers/secret_token.rb` to the `config/application.yml` file.
    
        ```yaml
        # config/application.yml
        STEAM_WEB_API_KEY: '234F789DC78E6A88B987AD87F00F'
        SECRET_TOKEN: '904150a195ef13012705d0c15751b333b2b79cb1678ffe4191d29635d0c57175ea7354b8f4c3290b1085363b7eb546b7d49ca7e40bebee3dced5dc9524f4cbe7'
        ```
        
        ```ruby
        # config/initializers/secret_token.rb
        Dota2rails::Application.config.secret_key_base = ENV['SECRET_TOKEN']
        ```

5. Now we have the OmniAuth middleware, which handles the OpenID login process when we hit the `/auth/:provider` and `/auth/:provider/callback` paths for our application (in this case `:provider` is `steam`). Let's add a controller and views so we can show a login button to the user and handle the OpenID callback. Run the following command to generate a welcome controller with an index action and the associated view:

    ```bash
    $ rails generate controller welcome index
    ```

6. Edit `app/views/welcome/index.html.erb` to include a login button that links to the `auth/steam` path:

    ```erb
    <h1>Welcome#index</h1>
    <%= link_to image_tag("http://cdn.steamcommunity.com/public/images/signinthroughsteam/sits_large_noborder.png"), '/auth/steam' %>
    ```

7. Mark the index action of the welcome controller as the root route of the application so that visiting `localhost:3000` will show the template we just edited (instead of having to visit `localhost:3000/welcome/index`). Make this edit in `config/routes.rb`:

    ```ruby
    # config/routes.rb
    Dota2rails::Application.routes.draw do
      get "welcome/index"
      root 'welcome#index'
      …
    end
    ```

8. If you run `rails server` now and navigate to `localhost:3000`, you should see the login button. Clicking it takes you to `https://steamcommunity.com/openid/login?...` where you can login with your Steam account. Doing so will then redirect you back to your application (to `localhost:3000/auth/steam/callback?...`), where you should see an error stating that "No route matches [POST] ...". We need to add a controller action and a route to handle the callback. I manually added a callback action to the welcome controller in `app/controllers/welcome_controller.rb` so that it looks as follows:

    ```ruby
    # app/controllers/welcome_controller.rb
    class WelcomeController < ApplicationController
      # auth callback POST comes from Steam so we can't attach CSRF token
      skip_before_filter :verify_authenticity_token, :only => :auth_callback

      def index
      end

      def auth_callback
        auth = request.env['omniauth.auth']
        session[:current_user] = { :nickname => auth.info['nickname'],
                                              :image => auth.info['image'],
                                              :uid => auth.uid }
        redirect_to root_url
      end
    end
    ```

    Some extra explanation is in order. The WelcomeController class inherits from ApplicationController, which you can find in `app/controllers/application_controller.rb`. The ApplicationController includes a line `protect_from_forgery with: :exception`, which enables CSRF protection by inserting a CSRF token in a hidden field in forms (which must match the CSRF token in the head section of the page in order for the submitted form to be accepted). Unfortunately, [OmniAuth OpenID seems to be hard-coded to request a POST callback instead of a GET callback][openidpost] so, when Steam POSTs the user's login info back to our application's auth\_callback action, Rails will expect a valid CSRF token. We have no control over what gets POSTed, so we add the `skip_before_filter...` line to turn off CSRF protection for the auth_callback action. If you have a better solution, please let me know!

    Looking at the `auth_callback` action now, Steam will send the logged-in user's info to `/auth/steam/callback`; the omniauth middleware intercepts this callback and sets `request.env['omniauth.auth']` as shown [here][setomniauthauth] to a hash ([here's an example of the hash returned for my account][pbauthhash]). I only care about a few items (username, profile image, and steam_id), so I put those items in the current user's session (a [CookieStore by default][cookiestore]) and then redirect to the front page.

    Now, we just need to add the route to `config/routes.rb` for the callback so Rails knows which controller and action should receive the callback:

    ```ruby
    # config/routes.rb
    Dota2rails::Application.routes.draw do
      get "welcome/index"
      root 'welcome#index'
      post 'auth/steam/callback' => 'welcome#auth_callback'
      …
    end
    ```

9. Now you should be able to experience the entire authentication flow! When you visit the main page of your website, you can click on the login button which redirects you to Steam, where you can login and then be redirected back to your main page. This is kind of boring though, because it doesn't show that you are logged in or that it knows who you are. Let's add that to the view in `app/views/welcome/index.html.erb`:

    ```erb
    <h1>Welcome#index</h1>
    <%= link_to image_tag("http://cdn.steamcommunity.com/public/images/signinthroughsteam/sits_large_noborder.png"), '/auth/steam' %>

    <% if session.key? :current_user %>
    <h3>Current user:</h3>
    <%= image_tag session[:current_user][:image] %>
    <p><%= session[:current_user][:nickname] %></p>
    <p><%= session[:current_user][:uid] %></p>
    <% end %>
    ```

10. Great! Now if you are logged in, it will display your Steam username, profile image, and SteamID. That's the groundwork for using Steam OpenID, but I'm going to continue a bit further and show fetching and displaying a list of the logged-in user's recent Dota 2 matches from the [Steam WebAPI][steamwebapi]. The data provided by the Steam WebAPI for Dota 2 match history looks like [this bit of json][jsonmh]. To fetch match history, we'll edit the welcome#index action to call the Steam WebAPI:

    ```ruby
    class WelcomeController < ApplicationController                                 
      # auth callback POST comes from Steam so we can't attach CSRF token                                 
      skip_before_filter :verify_authenticity_token, :only => :auth_callback        
                                                                                    
      def index                                                                                           
        @matchlist = []                                                                                   
        if session.key? :current_user                                                                     
          url = URI.parse("https://api.steampowered.com/IDOTA2Match_570/GetMatchHistory/v001/?key=#{ENV['STEAM_WEB_API_KEY']}&account_id=#{session[:current_user][:uid]}")
          res = Net::HTTP::get(url)                                                                       
          @matchlist = JSON.load(res)['result']['matches'] || []                                          
        end                                                                                               
      end                                                                                                 
                                                                                                          
      def auth_callback                                                                                   
        auth = request.env['omniauth.auth']                                                               
        session[:current_user] = { :nickname => auth.info['nickname'],                                    
                                              :image => auth.info['image'],                                         
                                              :uid => auth.uid }                                                    
        redirect_to root_url                                                                              
      end                                                                                                 
    end
    ```

11. The `@matchlist` instance variable from welcome#index is available in the corresponding view, where we need to list the matches:

    ```erb
    # app/views/welcome/index.html.erb
    <h1>Welcome#index</h1>
    <%= link_to image_tag("http://cdn.steamcommunity.com/public/images/signinthroughsteam/sits_large_noborder.png"), '/auth/steam' %>

    <% if session.key? :current_user %>
    <h3>Current user:</h3>
    <%= image_tag session[:current_user][:image] %>
    <p><%= session[:current_user][:nickname] %></p>
    <p><%= session[:current_user][:uid] %></p>
    <% end %>

    <ul>
    <% @matchlist.each do |match| %>
    <li><%= Date.strptime(match['start_time'].to_s, '%s').to_formatted_s(:rfc822) %> - <%= match['match_id'] %></li>
    <% end %>
    </ul>
    ```

Pardon the mess for converting from UNIX timestamp to date. That's the basics of getting data from the Steam WebAPI, though, and there are many directions this example could be taken from here (match detail pages, stats across multiple matches, and many of the other features provided by existing stats websites such as [dotabuff][dotabuff]). But now, on to deployment!


## Deploying to Heroku

These instructions mostly follow [Heroku's Getting Started with Rails4 article][herokugs], but are more abbreviated to hopefully get up and running even faster!

1. Signup for a [Heroku account][heroku] if you don't have one

2. Download the [Heroku toolbelt][toolbelt] for your platform

3. Log in using the heroku command from the toolbelt you just installed:

    ```bash
    $ heroku login
    Enter your Heroku credentials.
    Email: your_email@example.com
    Password:
    Could not find an existing public key.
    Would you like to generate one? [Yn]
    Generating new SSH public key.
    Uploading ssh public key /home/your_username/.ssh/id_rsa.pub
    ```

4. Add the Heroku gem and specify your Ruby version in your Gemfile. Also, comment out the sqlite3 gem, which Heroku doesn't support and will complain about if it's enabled:

    ```ruby
    # Gemfile
    …
    # Use sqlite3 as the database for Active Record
    #gem 'sqlite3'
    …
    ruby "2.0.0"

    # Heroku integration gem
    gem 'rails_12factor', group: :production
    ```

5. Heroku expects deployed apps to use a database, which requires a bunch of extra configuration, so let's just disable all of that by removing ActiveRecord from our app. Go to `config/application.rb` and change the code from `require 'rails/all'` to the following:

    ```ruby
    # config/application.rb
    …
    #require 'rails/all'
    require "action_controller/railtie"
    require "rails/test_unit/railtie"
    require "sprockets/railtie"
    …
    ```

6. Update your dependencies by running:

    ```bash
    $ bundle install
    ```

7. Oh no! If you try running it locally now, you probably will get an error about an undefined method for action\_mailer. ActionMailer is a part of the `rails/all` that we just removed (because we don't need it), but it turns out there are still configuration settings that reference action\_mailer and active\_record. Go to `config/environments/development.rb` and comment out the offending lines:

    ```ruby
    # config/environments/development.rb
    …
    # Don't care if the mailer can't send.                                        
    #config.action_mailer.raise_delivery_errors = false                           
                                                                                  
    # Print deprecation notices to the Rails logger.                              
    config.active_support.deprecation = :log                                      
                                                                                  
    # Raise an error on page load if there are pending migrations                 
    #config.active_record.migration_error = :page_load 
    …
    ```

7. Make a git repo at the root of your Rails site (for me it's at `/home/greg/repos/dota2rails`) if you haven't already and commit all of your changes:

    ```bash
    $ git init # if you haven't created a repo already
    $ git add .
    $ git commit -m "steam openid and webapi"
    ```

8. From the same directory (the root of your Rails site), register your repo with Heroku by running the following:

    ```bash
    $ heroku create
    Creating salty-ravine-7463... done, stack is cedar
    http://salty-ravine-7463.herokuapp.com/ | git@heroku.com:salty-ravine-7463.git
    Git remote heroku added
    ```

9. Now we need to set Heroku environment variables that we set locally with figaro. Figaro is supposed to be able to do this by running `rake figaro:heroku`, but I encountered an error about active\_record being missing so I ended up using the following approach instead:

    ```bash
    $ heroku config:add STEAM_WEB_API_KEY='234F789DC78E6A88B987AD87F00F'
    Setting config vars and restarting salty-ravine-7463... done, v3
    STEAM_WEB_API_KEY: 234F789DC78E6A88B987AD87F00F

    $ heroku config:add SECRET_TOKEN='904150a195ef13012705d0c15751b333b2b79cb1678ffe4191d29635d0c57175ea7354b8f4c3290b1085363b7eb546b7d49ca7e40bebee3dced5dc9524f4cbe7'
    Setting config vars and restarting salty-ravine-7463... done, v4
    SECRET_TOKEN: 904150a195ef13012705d0c15751b333b2b79cb1678ffe4191d29635d0c57175ea7354b8f4c3290b1085363b7eb546b7d49ca7e40bebee3dced5dc9524f4cbe7
    ```

10. Deploy your app to Heroku!

    ```bash
    $ git push heroku master
    …

    -----> Ruby/Rails app detected
    -----> Using Ruby version: ruby-2.0.0
    -----> Installing dependencies using Bundler version 1.3.2
             Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin --deployment
             Fetching gem metadata from https://rubygems.org/.........
             Fetching gem metadata from https://rubygems.org/..
             Installing rake (10.1.0)
             …
    ```

11. Visit your new website:

    ```bash
    $ heroku open
    ```

If you've gotten this far successfully, congratulations! You've made a simple Rails app with authentication via Steam OpenID and data from the Steam WebAPI that is deployed on Heroku!

If you encountered issues following this guide or have other thoughts/suggestions/corrections, please let me know on twitter, email, or github (links at left)!

[steam]: http://store.steampowered.com/
[ti3]: http://en.wikipedia.org/wiki/The_International_(video_gaming)
[railsgs]: http://guides.rubyonrails.org/getting_started.html
[steamauthgem]: https://github.com/reu/omniauth-steam
[oa_instr1]: https://github.com/intridea/omniauth/wiki#getting-started
[oa_instr2]: https://github.com/reu/omniauth-steam#usage
[railsenv]: http://railsapps.github.io/rails-environment-variables.html
[figaro]: https://github.com/laserlemon/figaro
[openidpost]: https://github.com/intridea/omniauth-openid/blob/master/lib/omniauth/strategies/open_id.rb#L37
[setomniauthauth]: https://github.com/intridea/omniauth/blob/master/lib/omniauth/strategy.rb#L362
[pbauthhash]: http://pastebin.com/rzJ84Ueb
[cookiestore]: http://guides.rubyonrails.org/action_controller_overview.html#session
[steamwebapi]: http://wiki.teamfortress.com/wiki/WebAPI#Dota_2
[jsonmh]: http://pastebin.com/2efHAxr8
[datdota]: http://www.datdota.com/
[dotabuff]: http://dotabuff.com/
[herokugs]: https://devcenter.heroku.com/articles/getting-started-with-rails4
[heroku]: https://www.heroku.com/
[toolbelt]: https://toolbelt.heroku.com/
