---
layout: post
title: "IE7 Margin Bug"
date: 2011-06-16 16:21
comments: true
categories: 
---


I found a bug in Internet Explorer 9 working in IE7 mode today which has to do with the inheritance of horizontal margins.

Update: It turns out that this is a problem only in quirks mode and in IE7 mode!

The bug is that an input inside a floated div inherits the horizontal margin from the div.

So if we have

``` html
<div style="float:left; margin:20px;">
    <input/>
</div>
```

then the margin-left and margin-right on the input are 20px.

I found something similar [here](http://blog.netscraps.com/bugs/ie6-ie7-margin-inheritance-bug.html) but it doesn't seem to happen in IE9. This one does. I haven't tested in in other versions of IE, but I did make a test.

Try this test:

``` html
<style>
.clear { border:none; clear:both;}
.blue { background-color:#00AAFF; }
.red { background-color:red; }
</style>

<p>Broken in IE9:</p>
<div class="blue">
    <div style="float:left; margin:20px;" class="red">
        <input/>
    </div>
    <div class="clear"></div>
</div>

<p>Not broken in IE9:</p>
<div class="blue">
    <div style="float:left; margin:20px;" class="red">
        <span style="width:0px"></span>
        <input/>
    </div>
    <div class="clear"></div>
</div>

```

I wonder if it breaks other versions of IE too. One fix is to make sure the input isn't the first element inside the div. There are other ways to get around it too.

P.S. Why is the first blue box extending to include the paragraph after it??? Looks like a topic for another blog post *sigh*.
