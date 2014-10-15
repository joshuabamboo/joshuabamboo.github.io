---
layout: post
title: "Communicating With Other Classes"
date: 2014-10-14 22:15:57 -0400
comments: true
categories: Flatiron School
---
When creating multiple classes, I noticed I was having a hard time wrapping my head around calling methods that weren't defined in my class. If you're like me, you had a hard time understanding how you could access methods from other classes. Here's an example of what I mean:

```ruby
		class Artist
			@@artists = []
			attr_accessor :name, :songs, :genres

			def initialize
				@songs = []
		    @genres = []
		    @@artists << self
			end

			def add_song(song)						### passing in a parameter that's an instance of the Song class
				@songs << song
		    @genres << song.genre       ### calling genre method from the Song class
		    if song.genre                  
		      song.genre.add_artist(self)
		    end
			end
		end


		class Song
			attr_accessor :name, :artist, :genre

			def genre=(genre)							### passing in a parameter that's an instance of the Genre class
				@genre = genre
				genre.add_song(self)        ### calling add_song method from the Genre class
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
```

Here we have three classes (Artist, Song, Genre). Notice the comments (###). These classes are calling methods that do not exist in their class. This can be confusing, especially if you're thinking about the concept of [scope](http://www.techotopia.com/index.php/Ruby_Variable_Scope). The method isn't defined. How could I possibly use it, right?! Wrong.


A simple step back helped me finally wrap my head around it. Think about a class that already exists that we use all the time, like arrays. 

#####Array is a class (Just like the ones we created above)

```ruby
		class Array
			...
		end
```

#####A new array is an instance within the class Array

`rappers = []`

is the same as 
     
`rappers = Array.new`

#####Now we can pass our rappers array as an argument into another class' method

```ruby
		class Artist
			def count_rappers(rappers)
				rappers.size 					### calling size method on rappers array
			end             
		end
```
Note that we are calling size on rappers, but we have not defined any method called size in our Artist class. The reason we have access to the size method is because it is one of the many methods within the class Array. 

```ruby	
		class Array
			def size
				...
			end
		end
```

Rappers is an instance of the class Array; therefore, methods within that class can be called on the rappers array. This is really no different than the first example of the Artist, Genre and Song classes. Within the Artist class, the add_song method is being passed a parameter that is an instance of the Song class. This allows us to then call methods from the Song class on that instance. Similarly, in the Song class we can access the Genre class method by passing in an instance of Genre. As long as the method's parameter is an instance of a particular class, we will always be able to reach into that class.

