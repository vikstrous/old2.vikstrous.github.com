---
layout: post
title: "Simplest PHP Hit Counter"
date: 2013-09-08 15:14
comments: true
categories: 
---

I often find myself wanting to add a little hit counter to a strategic place in a php script without having to deal with setting up any kind of fancy third party metrics system. So I have written the same few lines of code way too many times. Here they are so they will save me and you time next time we need to do this one silly thing.

```
$count_my_page = ("hitcounter.txt");
$hits = file($count_my_page);
$hits[0] ++;
$fp = fopen($count_my_page , "w");
fputs($fp , "$hits[0]");
fclose($fp);
```

P.S. I don't know why I used file and fputs instead of just file_get_cntents and file_put_contents. I need to revise this later.
