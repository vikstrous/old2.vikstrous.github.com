---
layout: page
title: "waterloo co-op map"
#date: 2013-08-30 19:40
comments: false
sharing: true
footer: true
---


Waterloo Co-op Map
---

Waterloo Co-op Map was a Facebook application that allowed co-op students to find each other when they go to a new city during co-op and don't know anyone there. They could use the information in various ways, but it's essentially a database of who's working where for a given co-op term. I wrote this application because there is no simple way to find out where people are working for their co-op term. I wanted co-op students to be able to find each other, especially if they are working somewhere where they've never been before or don't know anyone. Instead of asking all of their friends if anyone is working in <insert obscure city>, they can just check the map. I got the idea for this in 1A (2009) and wrote some of the code at that time, but nothing very presentable or bug-free existed until the end of my first co-op term (April 2010). I had lengthy discussions with [Francis Williams](http://fwilliams.org) about this at the planning stage and I have to give him credit even though at this point of time it's a compete blurr in my head and I have no idea who came up with what. The code was written in PHP with MySQL as the database. I followed the MVC model as much as I could. There is a central index.php page which delivers all of the content based on GET parameters. I tried to make the URLs cleaner at one point using apache mod_rewrite, but I concluded that it's not really worth it since it runs as a Facebook application. At first I had lots of difficulties with the restrictions that Facebook places on applications and I ended up using a combination of FBML and iframes. It ended up running at a reasonable speed with a small number of users. It has never been tested with a large number of users, and I suspect that it will do very poorly with the current architecture. I do have ideas about how to optimize it for large numbers of users, but I don't need to do that yet. I think it's very efficient to add features only as the need arises rather than wasting time for unnecessary things. Edit: I made it as fast as it can get with MySQL. I used lots of caching, etc. Building this application has taught me that 1. Facebook's API is madness and 2. MVC is awesome.

![Screenshot](/images/user/map.png "Screenshot")
