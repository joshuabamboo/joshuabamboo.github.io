---
layout: post
title: "Post Mortem: Whiteboard Challenge"
date: 2016-05-09 02:10:45 -0400
comments: true
categories:
---

####What I learned and how I would face the problem today
--

During an interview with a large tech company, I was asked to solve the following problem:

Imagine you have a 2X2 grid and three items (for example, an apple, a bottle, and a cd -- referred to below as "a", "b", and "c", respectively). If you place these items in a specific pattern on the grid, then you will unlock a prize (money, let's say).

The items must be placed in this exact pattern, and any other permutation will result in no prize.

![](../images/whiteboard4.JPG =600x)


##Solution 1
###The naive solution

My initial approach was to index each individual square in order to know the position of each item.

![](../images/whiteboard1.JPG =600x)

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

![](../images/whiteboard5.JPG =600x)

Addressing problem 1: We could continue to build out the hash from the first solution, but that would quickly become complex and would not scale well. The pattern was unique when the grid was just 2X2; however, with a 3X3 grid, the pattern would match in 4 locations:

![](../images/whiteboard2.JPG =600x)

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


##Post interview

I don't like the idea of needing to keep track of the size of the grid, which seems to be the source of our scaling problem. If we make the grid size irrelevant, scaling is no longer an issue.

###Coordinates
Switching from integer addresses to coordinates was a real breakthrough. This went from assigning arbitrary integers to a location to communicating valuable location information.

![](../images/whiteboard3.JPG =600x)


###"A grid within the grid" Solution
The other idea was this: if we have the coordinates of the items' locations, then we know the boundaries of that location. Consider the following grid:

![](../images/whiteboard6.JPG =600x)

We can now draw a boundary box around the max and min `x` and `y` axis points, creating a more explicit box within the grid. Everything outside of that smaller box is empty space, and not relevant to solving the problem.

Now that we have the smaller box, we can check it against a list of winning patterns more efficiently.

During the interview, I kept coming back to the notion that this problem was solvable with a tree. We even discussed the possibility of using regular expressions. Now with the smaller box boundary, we can make use of either. In fact, we can revisit the initial naive solution. Modified slightly, this set up now works with our new solution:

####Hash

```ruby
pattern = ["a", "b", nil, "c"]
patterns = {pattern => "money"}
```

Now that we have the pattern stored as a key in our `patterns` hash, we can simply pass in an array of the items from our "grid within a grid." If the array of items matches one of the patterns stored in our hash, it will return the prize that is stored as the value. If not, it will return `nil`.

![](../images/patterns-hash.png)
![](../images/patterns-hash-nil.png)


####DB
If we stuck to the original database concept, the `patterns` table would simply be three columns, and would no longer grow horizontally depending on the size of the grid.

| id   | prize | pattern |
| ---- |:-----|---|
| 1 | "money" | ["a", "b", nil, "c"] |
