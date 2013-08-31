---
layout: post
title: "Node Knockout: Node RPG"
date: 2011-09-01 22:20
comments: true
categories: 
---

Node Knockout
---

Last weekend I participated in [Node Knockout](http://nodeknockout.com/) with [Francis Williams](http://fwilliams.org). It was really bad timing for both of us and we were able to put in about 30 man hours into our project, but I'm proud of what we accomplished and I want us to continue working on it.

The version that we submitted at the end of the 48 hours contained a number of serious bugs which could have been fixed easily if I was there, but Francis was the only one left to fight some of my code which turned out to be pretty confusing without any documentation. [Here](http://win.no.de) it is. Don't look at it.

You can check out the latest version at http://rpg.no.de until we get our free hosting taken away. We'll probably work on this project in private for a while and then start inviting people to participate when we have something interesting.

Here are some of my opinions and experiences with the various technologies we used during the competition.

NKO2
---

This was the second Node Knockout. As soon as I heard about the first one, I knew I had to participate in the next one because I was messing with node.js for a while and I wanted to put together something cool. It was really fun because everyone was using open source software and the people running it were on IRC responding the any questions or problems that contestants are having during the whole competition. It was really cool that you could get on IRC and ask everyone else who is participating if they have experiences the same problems as you and how they solved them. Everyone was very helpful.

NowJS
---

[NowJs](http://nowjs.com/) allows you to use the magical "now" object to share variables between the client and the server. It also allows you to call a function on the other side of the wire as if it's defined locally. You can even pass callbacks! It's really cool.

We used NowJS extensively for this project. I loved the RMI, but I quickly realized that the shared variables don't have much of a use. We did not have anything that can be directly exposed to the client and it was not possible to filter the values allowed to be set in these magical variables. It was basically awkward and useless, so we stuck with using RMI for setting variables and updating game state. Groups were another feature which we tried really hard to use, but it just seemed awkward because we wanted to manage our user list and "room" list ourselves. I did not see the value of using NowJS groups over just sending a message to each user in our own group object.

I was really excited about NowJS when I started, but now I think it's just badly thought out and too immature to be useful for anything serious. I think that there are no real world use cases where you would want to set a variable on the server directly from the client. I think that this would give the client an opportunity to DOS the server by setting a really really big set of data into a variable, but I may be wrong.

In the future, I will replace NowJS with [dnode](https://github.com/substack/dnode) and see how well that goes.

Express, Jade, Stylus
---

[visionmedia(TJ Holowaychuk)](https://github.com/visionmedia) has taken node.js and made something that's really, really easy to use and speeds up development time dramatically. It looks like TJ has made all the best tools that exist for building websites in node.js. [Express](https://github.com/visionmedia/express) gives us the framework to parse requests and serve pages. [Jade](https://github.com/visionmedia/jade) gives us an amazing templating engine which looks much cleaner than any other templating engine including [HAML](http://haml-lang.com/). [Stylus](https://github.com/LearnBoost/stylus) allows us to write CSS without all the unnecessary punctuation and with all the features of [LESS](http://lesscss.org/). These are all really cool tools that I will probably always use every time I use node to make a website. I'm actually also very interested in [Calipso](http://calip.so/?utm_source=nodejs&utm_medium=github&utm_campaign=nodejs) and I am planning to get involved in that.

Heroku vs. Joyent?
---

In the beginning we started off with [Heroku](http://www.heroku.com/) as our deploy target. Late at night on the first day we found out that they don't support WebSockets. Someone on IRC told me to try switching to Joyent. It seemed like a lot of work at the time and we delayed it, but on the next day I decided to give it a try and - lo and behold - I switched in a fraction of the time it took me to set up Heroku and I got my WebSockets and it was awesome. We stayed with them for the rest of the competition. They give you SSH access and it's really cool. I'm not saying there's anything wrong with Heroku - they have really cool features and it looks like they let you scale your app really well - I'm just saying that if you are doing a hackathon, Joyent will save you time.

At one point, after the end of the 48 hours, our [node](https://no.de/) server stopped being accessible from the internet and I went on IRC and asked in #joyent if anyone knows what's going on. Everyone was sleeping, but a bit later one more person popped up reporting the same problem and we determined that we are on the same server. When [Joyent](http://www.joyent.com/) woke up the next day, they fixed it and we were back online. I want to thank the Joyent team for being responsive and awesome and fixing problems quickly and you are totally forgiven :)

Conclusion
---

Node Knockout was a great experience and I did not necessarily learn something new, but did realize how much I can accomplish with the skills that I've obtained over the past few years. I hope to be a part of Node Knockout 3 whenever that happens! Next time we'll be more prepared and have a more complete team. Maybe one day we'll build something worthy of a prize.

Read what Francis has to say about the competition [here](http://fwilliams.org/projects/rpg-node/)!
