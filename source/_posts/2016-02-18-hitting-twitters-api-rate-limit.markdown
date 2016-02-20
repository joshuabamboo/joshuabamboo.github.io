---
layout: post
title: "How To Debug Hitting API Rate Limits"
date: 2016-02-18 22:43:58 -0500
comments: true
categories: 
---

## Step 1: `p` All Over the Code
The ruby method `p` (`puts` works fine too. The only difference between the two is their return values. We don't care about return values in this case.) allows you to put strings to the console. We will use this as a tool to track the flow of our program by putting them in key places and describing where we are/what is happening in the code. Keep in mind that the purpose of these `p` statements is to read them later in the terminal to assess what code is being run and when. These will go everywhere: `p "<<where we are/what is happening in the code>>"`

>### `p` in the Controller
>Put `p "Controller#action line number"` *on each line* of the triggered `Controller#Action`.

><img src="{{ root_url }}/images/twitter-api/controller.png" />

-
>### `p` in the Model
>Put `p "Model#method_name"` at the top of each method in the model where the API is called.

><img src="{{ root_url }}/images/twitter-api/model.png" />

-
>### `p` in the Loops
>Loops are the primary suspect for too many API calls, so we'll want to pay special attention to them. `p "Model#method_name loop"`

><img src="{{ root_url }}/images/twitter-api/model-loop.png" />

-
>### `p` in the Client Connection
>Last but not least, the most important place to monitor is the API calls themselves. I like to use `!`s, so the calls stand out in the terminal output.  

>* Put a `p "!!! initialize client"` where the connection is first established to the API client.
>* Put a `p "! Hit the API"` in the method you're using the make calls to the API. This is likely an `attr_accessor`, so this step will require you to explicitly write out this method as well.

><img src="{{ root_url }}/images/twitter-api/client.png" />

## Step 2: Run the Code
Start your server and go through the actions in the browser that will trigger your API calls.

## Step 3: Look at the Terminal Output
Here's the moment of truth. Go to the terminal where your server is running. Immediately following the request, you should see all of the `p` strings being put to the console as each line is run. 

<video controls loop width="800" autoplay>
  <source src="{{ root_url }}/images/twitter-api/api-hell.mp4" type="video/mp4">
</video>

## Step 4: Detective Work
Let's do some detective work. If you placed `p` statements in the right places, this part should be relatively easy. Find the the place(s) where a bunch of API calls are being fired, and look at the lines just before it to see where to debug.

In this particular case, notice that `TwitterDirt#get_user_timeline loop` is where it hits the rate limit. 

<img src="{{ root_url }}/images/twitter-api/terminal-output.png" />

Let's take a closer look at that loop. 

<img src="{{ root_url }}/images/twitter-api/detective-work.png" />

The number of times we iterate is determined by the `TwitterDirt#number_of_pages` method. Let's put a binding in there and see what's happening.

<img src="{{ root_url }}/images/twitter-api/pry.png" />

It looks like we found our problem (one of them, at least)! This particular user has 155 pages of tweets. We have to send a request to Twitter for each page of tweets we want to access. Twitter only allows [180 API calls every 15 minutes](https://dev.twitter.com/rest/public/rate-limiting). **This one request uses ~90% of the allowed calls.** There's nothing we can do about Twitter's decision to only allow 180 calls, so we have to limit the number of pages we search.

##Step 5: Solve It

Luckily (I guess) for us. [Twitter only allows access to a user's 3200 most recent tweets](https://dev.twitter.com/rest/reference/get/statuses/user_timeline). This is true even if the user has 31,000 tweets (155 pages * 200 tweets per page) like in our current example.

`3200 tweets/200 tweets per page = 16 pages`

What this means is that 16 should be the max number of API calls to get a user's timeline. After the 16th call, Twitter stops sending back the intended response. Consequently, `TwitterDirt#number_of_pages` should return an integer no greater than 16.

<img src="{{ root_url }}/images/twitter-api/number-of-pages.png" />

**This single line of code just saved us 139 API calls!**

<iframe src="//giphy.com/embed/eoxomXXVL2S0E?hideSocial=true" width="480" height="360" frameborder="0" class="giphy-embed" allowfullscreen=""></iframe>