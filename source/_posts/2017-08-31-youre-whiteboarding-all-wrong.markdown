---
layout: post
title: "You're Whiteboarding All Wrong"
date: 2017-08-31 00:32:11 -0400
comments: true
categories: 
---

I’ve done a number of white boarding challenges, and they all terrify me. Something about them. 
The best way I can describe it is that feeling you get when you’re taking a test you didn't study for. You’re given a problem - you've never seen it before - but you *need* to have the answer.
It's a miserable feeling. Some people shut down and leave the page blank. Some ramble in the hopes that they might stumble upon the answer. I’ve been guilty of both. The good news: This is not a test.

![](https://media.giphy.com/media/10gtBUqL2PNHBS/giphy.gif)

YOU AIN'T GOT THE ANSWERS, SWAY! Luckily, you don’t need them.


####White boarding is a discussion.


With a test, you're given a problem. There are two possible outcomes: pass or fail. If you know the answer you pass; if not, you fail. 

With a discussion, you are given a problem. You don’t need to know the solution. In fact, if you did it would ruin the whole thing. There would be nothing to discuss. 
Your job is to do your best to make the problem approachable. They want to walk through the process with you. Watch your brain work. It’s okay to be Sway. 


With a test, the process might look something like this:

#####Problem -> Solution -> Harder Problem -> Revised Solution -> Add complexity -> Next solution…


With a discussion, it's a very different story.

#####Problem -> Questions -> Clarify problem -> Restate the problem -> Discuss scoped problem -> Exploration…

Notice, the major difference between these two: The discussion has no solution in sight. That does not mean that you will not arrive at a solution. It's just that there is much to consider before a solution begins to reveal itself. Let's take a look at what these differing approaches may look like in practice.

##Examples

Here is a recent example of a whiteboard challenge. Let’s take a look at how it went, and then compare how it might’ve gone using this more structured discussion approach. 

The Problem: Determine whether the following code will compile/How you would determine if there was a missing curly bracket: `var greeting = {`

###Test Approach

I asked a few clarifying questions. Could I assume it was a a string? If so, I could split a string and then go through an array to find the opening and closing brackets. When in the test mindset, the pressure of producing some result leaves you leaping toward a solution.

Boom. Naive solution. Split the string. Iterate through the collection. If you find the an open bracket, continue iterating and look for its partner. 

We discussed. In an attempt to clarify, I jotted down a basic implementation.


He added complexity. What happens if we have nested brackets: `{ greetingCount: [[1],2]}`


I tried reworking the initial solution for the new case. When that hit a dead end, I attempted another path: regex. After all, regex is made for pattern matching. This led to us going down rabbit holes because I committed early on to this solution. It was hard to switch gears.

![](https://media.giphy.com/media/26uflOGFZAKurnp7O/giphy.gif)



###Discussion Approach


Now, let's compare that experience with the back and forth of the discussion approach. Again, notice how there is no solution in Problem -> Questions -> Clarify problem -> Restate the problem -> Discuss scoped problem -> Exploration. 

**Question**

Can we assume it's a string? Are these the only two characters we want to check for? Should it be a method? If so, should it return a boolean value? 

**Clarify**

These starter questions give you a chance to get more info about the problem. In my example, the interviewer gave me a key detail that I missed. “You don’t need to write code.” I should’ve used this opportunity to ask even more questions. If no code, should we explore different algorithms? Data structures? Pseudocode? This would give me the chance to scope the problem even further. 

**Restate**

Depending on the conversation, the new restated problem might be something like: *What data structure could you use to find connected components in a string?*

**Discuss**

Notice we have the same problem, but a whole new direction for the conversation. Now we can talk about data structures.


1. Array - This is where I could explore the initial string solution from above. It could be useful because we have the indexed position of the character.

2. Hash - A hash could potentially be used to keep track of a character’s count and position. It seems complex. Probably too complex, but we could always revisit.

3. Linked list - Probably not ideal for this one.

4. Stack - Could we add the characters to a stack and keep track of each one using LIFO?

5. Queue - Or maybe we could use FIFO to queue up the characters?

**Explore**

Again, this continues the discussion. “A stack seems to make the most sense, let's explore it...” 

Rather than a teacher/student dynamic, this approach creates a sort of partnership between you and the interviewer where you are working together. This is, after all, what you will be doing with this person if you get the job. 

![](https://media.giphy.com/media/Jylb9PZHvJZSg/giphy.gif)

##Final Thoughts
Hindsight’s 20/20, so the example is admittedly a bit contrived. But the important takeaway for me is the back and forth with the interviewer. Notice there is no code yet (just like the interviewer suggested). The discussion allows for you to better understand the problem. It allows the interviewer to learn a little bit about your thought process and what you know. In fact, it's a great way to show off your breadth in real time.


Treat it like you’re making a web app. You don’t go immediately from idea to production. There are a ton of steps in between, and most don’t involve code. You have domain model sketches, wireframes, Trello boards with user stories, tests, bugs, conversations with friends, colleagues and rubber ducks. The whiteboard is a microcosm of this. Creating a back and forth with your interviewer is just a way of showcasing some of those skills.