---
layout: post
title: "Hacking RewardsGold.com: Part 1"
date: 2015-01-25 21:50:29 -0500
comments: true
categories: 
---

[Rewards Gold](http://www.rewardsgold.com/) is one of those sites where you earn points, and the points then act as currency for products in the Rewards Gold store. There are several ways to earn points, but the one that caught my attention was the "Invite Friends" option. Simple enough: 250 points for each friend you refer.

At this point I should mention that their store consists almost entirely of magazine and newspaper subscriptions. One of which caught my eye - The Economist. A year of The Economist can set you back anywhere from $350-$120 (i.e. well worth my time).

At a price of 8,600 points, The Economist would've cost me 35 friends' names and emails. Not terrible, but I was looking for something scalable. 

Naturally, I began writing a script. [Here it is on GitHub](https://github.com/joshuabamboo/rewards-gold)

I created a class called `FriendGenerator` where the user could pass in their Rewards Gold username, email, and number of points they needed. Using the Mechanize gem, the script logs the user in and then submits the "Invite Friends" form until the specified number of points are reach. It turns out the emails aren't vetted in any way. As you can see from the `submit_friend_form` method, all of the form data is randomly generated.

```ruby
def submit_friend_form
  page = @mechanize.get('http://www.rewardsgold.com/surv/referfriend.php')
  form = @mechanize.page.form_with(:name => "theform")
  form.FriendName1 = Random.rand(1000000000)
  form.FriendEmail1 = "#{Random.rand(1000000000)}@#{Random.rand(1000000000)}.com"
  form.FriendName2 = Random.rand(1000000000)
  form.FriendEmail2 = "#{Random.rand(1000000000)}@#{Random.rand(1000000000)}.com"
  button = form.button_with(:value => "Submit")
  @mechanize.submit(form, button)
end
```

This works. By simply creating a new instance of the class, passing in the arguments, and calling the `get_points` method, you can get all the points you want.

```ruby
f = FriendGenerator.new("youremail@example.com", "PASSWORD")
f.get_points
```
Bam! Go get yourself The Economist. We could stop here, but there is an even simpler way I discovered. To be continued...