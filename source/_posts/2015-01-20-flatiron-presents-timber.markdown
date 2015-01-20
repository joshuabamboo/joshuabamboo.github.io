---
layout: post
title: "Flatiron Students Present - Timber"
date: 2015-01-20 15:00:28 -0500
comments: true
categories: 
---

![Timber homepage](../images/timber-home.png)

Timber is an application my partner and I built and presented at Flatiron Students Present Meetup. We wanted to create a fun application that we'd enjoy making and presenting. We wanted to dive pretty heavily into relational databases. And we wanted a good way to really get a grasp of Active Record. Behold, Timber was born.

###What is Timber?
Timber is a web app clone of Tinder built in Rails. The twist being that it is specifically targeted at users that recently ended a relationship and were still living with their significant other. In essence, Timber is the Tinder for cohabitation breakups.

###How does it work?
For our demo, we used two obvious test users, Pitbull and Ke$ha ([You know the song](http://open.spotify.com/track/3cHyrEgdyYRjgJKSOiOtcS)).

Pitbull recently broke up with his girlfriend, and he was looking for a place to move. Ke$ha recently broke up with her boyfriend, and she was looking for someone to help defray the cost of her now much more expensive apartment.

From here, the UX resembles Tinder. Pitbull will see heterosexual females with apartments. Ke$ha will see heterosexual males looking for a place to stay. If they like each other, they will match and can send messages back and forth.

###The tech
This project relies heavily on database interaction. In a way, our database queries were a little more complex than a Tinder-style checking for reciprical likes. Our models were more similar to an Airbnb model, in that each user could be a guest or a host. In order to filter the users properly, we check gender, orientation, and the sex they're looking for. Once we have our `@user` variable set, we then must check to see if the user is a guest or a host. We do all this with smaller helper methods such as `not_have_apts` vs `have_apts` and `women_seeking_men` vs `men_seeking_women`. The goal is that ultimately the methods start to read like conversational English. 

```ruby
def filtered_users
  if self.gender == "male" && self.orientation == "straight" && self.looking_for == "women"
    @users = User.women_seeking_men
  elsif self.gender == "female" && self.orientation == "straight" && self.looking_for == "men"
    @users = User.men_seeking_women
  else
    @users = "error"
  end
  self.host? ? @users.not_have_apts : @users.have_apts
end
```

Another important component of database interaction was making sure that users were only viewing users they hadn't seen before. To do this, we will go through the `Like` and `Dislike` tables to look for users where neither a like nor dislike is persisted. 

```ruby
def unviewed_users
  users = self.filtered_users
  if self.has_liked?
    Like.where(:liker_id => self.id).find_each do |like|
      users = users.where.not(:id => like.likee_id)
    end
  end
  if self.has_disliked?
    Dislike.where(:disliker_id => self.id).find_each do |dislike|
      users = users.where.not(:id => dislike.dislikee_id)
    end
  end
  users
end
```

Lastly, it's worthwhile to take a look into the Message model to examine the query for displaying user messages. Here, we grab messages based on a `to_id` and `from_id`, grab the reciprocal messages, concatenate them, and sort by date and time.

```ruby
class Message < ActiveRecord::Base
  belongs_to :to, :class_name => :user
  belongs_to :from, :class_name => :user

  def self.messages(to_id, from_id)
    Message.where("to_id = ? AND from_id = ?", to_id, from_id)
  end

  def conversation_with(recipient_id)
    current_user_messages = Message.messages(self.id, recipient_id)
    recipient_messages = Message.messages(recipient_id, self.id)
    conversation = (current_user_messages + recipient_messages).sort_by(&:created_at)
  end
end
```
