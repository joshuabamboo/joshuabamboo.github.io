---
layout: post
title: "Postmortem: Whiteboard Challenge"
date: 2016-05-09 02:10:45 -0400
comments: true
categories:
---

#####What I learned and how I would face the problem today


During an interview with a large tech company, I was asked to solve the following problem:

Imagine you have a 2X2 grid and three items (for example, an apple, a bottle, and a cd -- referred to below as "a", "b", and "c", respectively). If you place these items in a specific pattern on the grid, then you will unlock a prize (money, let's say).

The items must be placed in this exact pattern, and any other permutation will result in no prize.

![](http://joshuabamboo.github.io/images/whiteboard4.JPG =600x)


##Solution 1
###The naive solution

My initial approach was to index each individual square in order to know the position of each item.

![](http://joshuabamboo.github.io/images/whiteboard1.JPG =600x)

Then use a hash to store the indexed location as the key and the item as the value:


```ruby
solution = {1=>"a", 2=>"b", 3=>nil 4=>"c"}
```

After storing the solution, we could then store that as the key of another hash where the value is the prize:

```ruby
solutions = {solution=>"money"}
```

This way we could check the locations of items on the board against the keys in our `solutions` hash. If the key exists, then it would return the value. If not, it would return `nil`.

```ruby
solutions[{1=>"a", 2=>"b", 3=>nil 4=>"c"}]
	#=> "money"

solutions[{1=>"a", 2=>"b", 3=>"a" 4=>"c"}]
	#=> nil

solutions[{1=>"b", 2=>"a", 3=>nil 4=>"b"}]
	#=> nil
```


###What did I learn
This approach only works for a fixed grid size. As soon as the grid size changes, this solution does not work.

###What should change
Grid size must be accounted for in some way, so that is what must be tackled in the next solution.

-


##Solution 2
###Accounting for variable grid sizes

There are actually two problems when the grid size increases:

1. Scale - Now there is more than one potential set of locations for the pattern to fit on the board.
2. Absolute locations - The 2X2 solutions hash now has the wrong indexed locations.

![](http://joshuabamboo.github.io/images/whiteboard5.JPG =600x)

Addressing problem 1: We could continue to build out the hash from the first solution, but that would quickly become complex and would not scale well. The pattern was unique when the grid was just 2X2; however, with a 3X3 grid, the pattern would match in 4 locations:

![](http://joshuabamboo.github.io/images/whiteboard2.JPG =600x)

The bigger problem here is problem 2: relying on the absolute location.

I was running into a wall trying to approach these two problems at once, so I eventually asked if I could approach the problem as if I were setting up a relational database for a web app where the grid was a form being submitted by the user. The interviewer obliged. The following table was the result of that exchange:

###Winning patterns

| id   | prize | dimensions | 1 | 2 | 3 | 4 | 5 | 6 | ... |
| ---- |:-----|------:| --:| --:| --:| --:| --:| --:| --:|
| 1 | "money" | "2x2" | "a" | "b" | nil | "c" |
| 2 | "money" | "3x3" | "a" | "b" | nil | nil | "c" |
| 3 | "money" | "4x4" | "a" | "b" | nil | nil | nil | "c" |

You'll notice that a pattern starts to emerge. If the user submits a pattern of "a b nil nil c" on a 3x3 grid, we can check the 3x3 winning patterns to see if it matches a pattern from our `winning patterns` table.

This is obviously a bad way to design a table. Each block in the grid is represented by a column. As a consequence, as the grid grows, the table would need to grow horizontally. Nevertheless, it helped me wrap my head around the problem. A pattern begins to emerge. This pattern can be represented on three different dimensioned grids. As long as we know the dimensions of the board, we can check for the pattern by counting the number of empty spaces (`nils`) between the items.

At this point we ran out of time in the interview.


###What did I learn
1. As the grid grows, it is important to consider whether the solution will scale.
2. The absolute position of the items is less important than their relative position to one another. Relative position should be the focus.
3. Thinking about the problem in a new way can be clarifying. Approaching it from the perspective of creating a web app added a layer of familiarity that allowed me to be more comfortable and confident in problem-solving in uncharted territory.

###What should change
Obviously the idea needs some finessing. A solution started to emerge, but I left the interview with the algorithm on my mind. A pattern was identified, but scaling was still an issue.


-----

#Post interview

###Clarify the problem
One of the big takeaways from this interview and all technical interviews is the importance of asking clarifying questions. Take the grid for example:

* Are we responsible for constructing the grid?
	* If not, how is the grid represented?
		* Is it a form?
		* Is it an array of arrays?
		* Is it just represented by a string?
* Is the grid being passed in somehow (EG HTTP request) or are we writing a script?

This is an important start. It helps you become familiar with the domain. And more importantly, asking questions will help you scope the problem.

###Breaking down the problem

We all know how important it is as an engineer to take one big problem and break it into several smaller problems. As I approach the problem today, I would actually break it down into at least three problems:

1. Determining the grid layout
2. Isolating the user's pattern
3. Matching the user's pattern with the winning patterns


###Problem 1: Determing the grid layout

I don't have the luxury of asking clarifying questions at this phase, so I had to make some assumptions. Since I was allowed in the interview to think about it as a form being submitted, I will stick with that. I am assuming that I, the programmer, am responsible for constructing the grid. I am choosing to have the grid designed as a form where each input is a different square in the grid. When the form is submitted, we could then access the value and name of each input via the params being sent back to our server.

With this setup in mind, I began thinking about the best way to design a grid.
I didn't like the idea of needing to keep track of the size of the grid, which seemed to be the source of our scaling problem. If we made the grid size irrelevant, scaling was no longer an issue.

#####Coordinates!
Switching from integer addresses to coordinates was a real breakthrough. This went from assigning arbitrary integers to a location to communicating valuable location information.

![](http://joshuabamboo.github.io/images/whiteboard3.JPG =600x)

###Problem 2: Isolating the user's pattern

If we have the coordinates of the items' locations, then we know the boundaries of that location. Consider the following grid:
The other idea was this: if we have the coordinates of the items' locations, then we know the boundaries of that location. Consider the following grid:

![](http://joshuabamboo.github.io/images/whiteboard6.JPG =600x)
#####"A grid within the grid" Solution

We can now draw a boundary box around the max and min `x` and `y` axis points, creating a more explicit box within the grid. Everything outside of that smaller box is empty space, and not relevant to solving the problem.

programmatically, we could represent this by (a) isolating the dimensions then (b) isolating the pattern within those dimensions.

#####Isolating the dimensions
When we receive params, it will contain information for every form input. For example, a 3x3 grid might return something like:

```ruby
params[:grid] #=> {"0-0"=>"a", "1-0"=>"b", "2-0"=>"",
                   "0-1"=>"", "1-1"=>"c", "2-1"=>"",
                   "0-2"=>"", "1-2"=>"", "2-2"=>"",
                  }
```
The task now is to pull out the key/value pairs that contain non-empty strings as values.

```ruby
coordinates = []
params[:grid].each {|location, item| coordinates<<location if item != ""}
```

Now, we have a `coordinates` array that returns `["0-0", "1-0", "1-1"]`. We have to separate the strings on the hyphen.
```ruby
separated_coords=[]
coordinates.each {|cord| separated_coords<<cord.split("-")}

separated_coords #=> [["0", "0"], ["1", "0"], ["1", "1"]]
```

Next, we want to iterate through each array and store the x and y values in separate arrays.

```ruby
x=[]
y=[]

separated_coords.each {|cord| x<<cord[0]; y<<cord[1]}

x #=> ["0", "1", "1"]  
y #=> ["0", "0", "1"]  
```

Now we can easily do the calculations to find the smallest possible grid that contains this patterns.

```ruby
max_x = x.max.to_i
min_x = x.min.to_i

min_y = y.min.to_i
max_y = y.max.to_i

smallest_grid_dimensions = "#{(max_x - min_x)+1}*#{(max_y - min_y)+1}"
  #=> "2*2"
```

#####Isolating the pattern

We know from our first operation while calculating the dimensions that three items added to the grid. We can calculate this by call `#size` on `separated_coords`.

```ruby
separated_coords.size #=> 3
```

We also know that there are four elements that make up this pattern, given that we now have the dimensions.

```ruby
eval(smallest_grid_dimensions) #=> 4
```

This tells us that there is just one `nil` missing from our pattern.

Meanwhile, we can get a list of all the items by iterating through params.

```ruby
items=[]
params[:grid].each {|location, item| items<<item}
items #=> ["a", "b", "", "", "c", "", "", "", ""]
```

The starting point of our pattern will be at the minimum values for x (`min_x`) and y (`min_y`).
```ruby
starting_item = params[:grid]["#{min_x}-#{min_y}"]
```

Likewise, the same can be done with the max values to get the ending_item
```ruby
ending_item = params[:grid]["#{max_x}-#{max_y}"]
```

```ruby
while x <=
```

This leaves us with an array of all the user's items.

`user_pattern #=> ["a", "b", nil, "c"]`

Now that we have the pattern for the smallest grid dimensions, we can check it against a list of winning patterns more efficiently.

###Problem 3: Matching the user's pattern with the winning patterns

During the interview, I kept coming back to the notion that this problem was solvable with a tree. We even discussed the possibility of using regular expressions. Now with the smaller box boundary, we can make use of either. In fact, we can revisit the initial naive solution. Modified slightly, this set up now works with our new solution:

#####Hash

```ruby
pattern = ["2*2", "a", "b", nil, "c"]
patterns = {pattern => "money"}
```

Now that we have the pattern stored as a key in our `patterns` hash, we can simply pass in an array of the items from our "grid within a grid." If the array of items matches one of the patterns stored in our hash, it will return the prize that is stored as the value. If not, it will return `nil`.

![](http://joshuabamboo.github.io/images/patterns-hash.png)
![](http://joshuabamboo.github.io/images/patterns-hash-nil.png)


#####DB
If we stuck to the original database concept, the `patterns` table would simply be four columns, and would no longer grow horizontally depending on the size of the grid.

| id   | prize   | pattern              | dimension |
| ---- |---------|----------------------|-----------|
| 1    | "money" | ["a", "b", nil, "c"] | "2*2"     |

With the DB solution, you could structure the winning pattern as an array or string as a column in the `winning_patterns` table (I will use the array example since that's what I used above).

At this point, we just need to query the database. In Rails, the ActiveRecord query would look like this:

```ruby
user_pattern = ["a", "b", nil, "c"]
size = "2x2"
win = WinningPattern.find_by(pattern: user_pattern, dimension: size)
win.prize if win
```

If the query returns an object, then the return value of `win.prize` will be the corresponding prize, in this case `"money"`. If no match is found in the db `WinningPattern.find_by(pattern: user_pattern)` will return `nil`.


##What Did I Learn

* Clarifying questions are important. They shed light on ambiguity and help break the problem down into its component parts.
* Find the problems within the problem. Breaking down the original problem into bite-sized chucks not only helps you solve the problem, but helps you relax. If you are focusing on one little problem and tabling everything else, it alleviates the stress of needing to solve everything at once.
* I find this type of on-the-fly problem solving combined with normal interview pressure tough in the moment. Try to find comfort in knowing that the questions are designed to be tough. No one is immediately "John Nashing" the answer on the whiteboard. Furthermore, that's not even what the interviewer is looking for you to do. The interviewer is on your side. They are hoping you do well (so they can stop doing 5 interviews a day :) ). They just want to see how you work.
