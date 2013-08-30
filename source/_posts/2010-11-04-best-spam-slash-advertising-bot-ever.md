---
layout: post
title: "Best spam/advertising bot ever"
date: 2010-11-04 12:00
comments: true
categories: 
---


I wouldn't be surprised if someone has already done this, but I came up with this after seeing so many bots trying to troll me unsuccessfully.

1. Make a chat bot
2. Take a whole bunch of chat histories (parse them with regex magic and put them in a db?)
3. Use the first line of a random conversation as a conversation starter for your bot
4. Every time the user says anything, find the closest match in the conversation histories and use the response given there; if there is nothing even close, use a random line
5. Eventually provide the user with a link to your site
6. Check if the user clicked on the link and optionally how long he stayed on your site
7. Save each successful conversation as a new conversation and discard each failure to persuade the user to click on the link; optionally increase the weight of the lines used in proportion to how long the user stayed on the site
8. The better responses will start being used more often
9. ???
10. Profit!
