---
layout: post
title: "Communicating With Other Classes"
date: 2014-10-14 22:15:57 -0400
comments: true
categories: Flatiron School
---
When creating multiple classes, I noticed I was having a hard time wrapping my head around calling methods that weren't defined in my class. If you're like me, you had a hard time understanding how you could access methods from other classes. Here's an example of what I mean:

		class Artist
			@@artists = []
			attr_accessor :name, :songs, :genres

			def initialize
				@songs = []
		    @genres = []
		    @@artists << self
			end

			def add_song(song)
				@songs << song
		    @genres << song.genre          ### calling genre method
		    if song.genre                  
		      song.genre.add_artist(self)  ### calling add_artist method
		    end
			end
		end


		class Song
			attr_accessor :name, :artist, :genre

			def genre=(genre)
				@genre = genre
				genre.add_song(self)        ### calling add_song method
			end
		end


		class Genre
			attr_accessor :name, :songs, :artists
			@@genres = []

			def initialize
				@songs = [] 
				@artists = []
				@@genres << self
			end


			def add_song(song)
				@songs << song
			end


			def add_artist(artist)
				@artists << artist
				@artists.uniq!
			end
		end

Here we have three classes (Artist, Song, Genre). Notice the comments (###). These classes are calling methods that do not exist in their class. This can be confusing, especially if you're thinking about the concept of [scope](http://www.techotopia.com/index.php/Ruby_Variable_Scope). The method isn't defined. How could I possibly use it, right?! Wrong.


A simple step back helped me finally wrap my head around it. Think about a class that already exists that we use all the time, like arrays. 

#####Array is a class 
######Just like the ones we created above
		class Array
			...
		end

#####A new array is an instance within the class Array
		rappers = []
is the same as 
     
     rappers = Array.new

#####Now we can pass our rappers array as an argument into another class' method
		class Artist
			def count_rappers(rappers)
				rappers.size 					### calling size method on rappers array
			end             
		end

Note that we are calling size on rappers, but we have not defined anything method called size in our Artist class. The reason we have access to the size method is because it is one of the many methods within the class Array.
		
		class Array
			def size
				...
			end
		end

This is really no different than the first example of the Artist, Genre and Song classes. As long as the method's parameter is an instance of a particular class, we will always be able to reach into that class.

