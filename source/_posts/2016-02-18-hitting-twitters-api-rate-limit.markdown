---
layout: post
title: "Hitting Twitter's API Rate Limit"
date: 2016-02-18 22:43:58 -0500
comments: true
categories: 
---

## "p" All Over the Code

```ruby
class TwitterDirt
  attr_accessor :twitter_client, :timeline_size, :handle
  
  def initialize(handle)
    @twitter_client = initialize_twitter_client; p "Hit the initialize"
    @handle = handle
    ...
  end
  
  def twitter_client
    p "! Request to Twitter API"
    @twitter_client
  end

  def get_user_timeline
    tweets = twitter_client.user_timeline(handle, :count => tweets_per_page); p 'Hit TwitterDirt#get_user_timeline'
    (number_of_pages - 1).times do
      puts 'Hit #get_user_timeline/loop'
      ...
    end
    ...
  end

  def obscene_tweets
    p 'TwitterDirt#obscene_tweets'
    arr_of_tweets = []
    loop_count = 0
    get_user_timeline.each do |tweet|
      if Obscenity.profane?(tweet.text)
        arr_of_tweets << tweet
      end
      loop_count += 1
    end
    p "Hit #obscene_tweets loop: #{loop_count} times"
    arr_of_tweets
  end
  
  ...
  
  private
    def initialize_twitter_client
      p '!!! TwitterDirt#initialize_twitter_client'
      client = Twitter::REST::Client.new do |config|
        config.consumer_key        = ENV['CONSUMER_KEY']
        config.consumer_secret     = ENV['CONSUMER_SECRET']
        config.access_token        = ENV['ACCESS_TOKEN']
        config.access_token_secret = ENV['ACCESS_SECRET']
      end 
    end
end
```