---
layout: post
title: "Refactoring Tic Tac Toe"
date: 2015-02-16 23:43:50 -0500
comments: true
categories: 
---

Oh, how far I've come since I began this coding journey. So much of our journey as code newbies is focused on how much we still have to learn, but it's a good exercise to reflect on how far you've come. For me, this came in the form of refactoring a game of tic tac toe.

As much as it makes me cringe, I'm sharing my first ever attempt at coding something in Ruby:

```ruby
puts "Welcome to beginner Tic Tac Toe.\nYou are X. The computer is O. You go first." 
puts "Enter the number of the box you would like to occupy:\n "
currentBoard = "1|2|3\n4|5|6\n7|8|9"
puts currentBoard + "\n "

remainingSpaces = [1, 2, 3, 4, 5, 6, 7, 8, 9]  #available positions to choose   
location = 0  # set to 0, so the default is outside remainingSpaces range
locationsX = []  # array of player's selected locations
locationsO = []  # array of computer's selected locations
winningCombinations = [[1, 2, 3], [4, 5, 6], [7, 8, 9], [1, 4, 7], 
  [2, 5, 8], [3, 6, 9], [1, 5, 9], [3, 5, 7]]  # all possible winning sequences  


until remainingSpaces[0] == nil # loop until we've run out of tic tac toe spaces
  #user makes a move
  location = gets.chomp().to_i  # user input converted to integer
  if remainingSpaces.include?(location)  # is the input a valid choice from remainingSpaces
    currentBoard[location.to_s] = ('X')  # updating the board to show 'X' instead of num
    remainingSpaces.delete(location)  #removes the chosen number from the array  
    puts "\n" + currentBoard 
    locationsX.push(location)  # adds selection to array containing all player X selections
    
    #check for winner
    for combination in winningCombinations # need to look at each array in the parent array
      if locationsX.sort & combination == combination #does array of previous moves match winner
      abort("Tic Tac Toe! You win!") # if yes, game over
      end
    end
        
  else #  in case the user selects any char != 1-9
    puts 'Available positions are: '
      remainingSpaces.each do |i|
          puts i 
      end   
    puts "Select a number..."
    redo
  end

   #computer makes a move 

   #for combination in winningCombinations
   ## Tie game locator
    if remainingSpaces[0] == nil  # breaks loop on the last iteration to avoid unnecessary final turn for O
      abort("Cat game! Game over.") # if we made it here, it's a tie
   ## offensive move -> if there's 2/3 combination, select the third
    #elsif (combination & locationsO).length == 2 #do both arrays have 2 locations in common?
    # space = combination - locationsO # if yes, what's the the third space that's missing
    # if remainingSpaces.include? space # has that space been selected already by player O?
    #   location = space # if no, pick that space
    # end 
   ## defensive move -> if opponent has 2/3 combination, select the third
    #elsif (combination & locationsX).length == 2 #same as offensive
    # space = combination - locationsX # same as offensive
    # if remainingSpaces.include? space # has that space been selected already by player X?
    #   location = space # same as offensive
    # end
   ## work-around
    elsif remainingSpaces.include? 5  # included this as a rudimentary work-around to the commented code above
      location = 5                  # taking the middle square gives player O a better statistical chance of a tie
   ## pick a random location  
    else
      location = remainingSpaces.sample
    end


    currentBoard[location.to_s] = ('O')  # updating board to show 'O' instead of the chosen number
    remainingSpaces.delete(location)  #removes the chosen number from the array
    puts "\nThe computer is making its move..."
    sleep(1.2) # added delay for a more natural flow
    puts "\n" + currentBoard 
    locationsO.push(location)
    
    #check for winner
    for combination in winningCombinations
      if locationsO.sort & combination == combination  
      abort("Tic Tac Toe! The computer wins!")
      end
    end
  
    puts "\nThe computer chose %d. Your turn..." % location
end
```

Now, compare this with [my recently refactored version](https://github.com/joshuabamboo/tic-tac-toe-cli). It's good to glance back at where you've come from while moving full speed ahead.