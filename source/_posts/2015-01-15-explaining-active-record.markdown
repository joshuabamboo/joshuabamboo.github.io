---
layout: post
title: "Explaining Active Record"
date: 2015-01-15 14:27:31 -0500
comments: true
categories: 
---

<blockquote class="twitter-tweet" lang="en"><p>ActiveRecord is the shit. <a href="https://twitter.com/hashtag/coding?src=hash">#coding</a> <a href="https://twitter.com/hashtag/justsayin?src=hash">#justsayin</a> <a href="https://twitter.com/hashtag/rails?src=hash">#rails</a></p>&mdash; Logan Hasson (@loganhasson) <a href="https://twitter.com/loganhasson/status/396801652399030273">November 3, 2013</a></blockquote>
<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

Indeed, it is, but why? The Rails Guides -- while normally crystal clear -- can be a bit confusing to understand with regard to AR. Below is a sample excerpt from their answer to "What is Active Record."

> Active Record is the M in MVC - the model - which is the layer of the system responsible for representing business data and logic. Active Record facilitates the creation and use of business objects whose data requires persistent storage to a database. It is an implementation of the Active Record pattern which itself is a description of an Object Relational Mapping system.

While this is all technically precise, I remember this sounding like complete gibberish my first time reading it. In light of this, allow me to try to translate:

* "Active Record is the M in MVC - the model - which is the layer of the system responsible for representing business data and logic." 
> This is obviously assuming some basic knowlege of the MVC paradigm. Essentially the biggest takeaway is that AR deals with the database and the data being stored inside it. AR is responsible for handling all database interaction from the application level.

* "Active Record facilitates the creation and use of business objects whose data requires persistent storage to a database. It is an implementation of the Active Record pattern which itself is a description of an Object Relational Mapping system." 
> What makes AR so special is that it interacts with the database in a simple and intuitive way (Read: not SQL). The way it does this is by creating Ruby objects that come with all the benefits of being objects (like methods). AR then translates between those objects and the database, so you don't have to.

##Why not use SQL?
Let's compare SQL and AR by using a simple use case. Imagine you want to find the last item in one of the tables in your database. In SQL it will look something like this:

''''SQL 
SELECT /* FROM table_name ORDER BY date DESC limit 1
''''

In Ruby, it's one method '.last':

''''Ruby
table_name.last
''''

Even though this is a very simple query, the difference is stark. It only gets worse as the queries become more complex.

To summarize: AR composes SQL, submits the SQL, parses the output, and puts it in a Ruby object--all in one fell swoop. Now you can retweet Logan with confidence and let the world know that Active Record is, indeed, the shit.
