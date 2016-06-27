---
layout: post
title:  "Yee-haw.. Now you can comment on posts"
date:   2016-06-26 14:07:36 +0530
categories: jekyll update
comments: true
disqus_identifier: 6adb8054-382e-4de9-ae7d-807b7d02a5e3
tags:
- tutorial
---
> ***Now we can play comments-comments on my posts*** :stuck_out_tongue:

I have read ample of blogs which shows how to integrate Disqus with Jekyll but found myself unable to succeed as Disqus has updated their embeded code which provides feasibility to integrate Disqus comments in your post.

So here I am writing a way which is easy af through which you can integrate Disqus in your Jekyll blog post.

*  Create an account on Disqus, [Register][Register] your forum name.
*  It will land you to an installation page.
*  Choose "Universal Code" from the "Choose your platform option".
  It will provide you embed code which you just have to copy and paste it under "_includes" in new file, say "disqus.html".
*  This is the tricky part, as specified on official doc you have to place the code on whichever post you'd like to load Disqus. And we want something like a boolean variable which allows us to load comments on this post and on that post "nope, just don't do that".
*  Here comes the [Liquid][Liquid] for rescue, just assign
{% highlight javascript %}
{% raw %}
this.page.url = 'http://{{ site.url }}{{ page.url }}';
this.page.identifier = '{{ page.disqus_identifier }}';
{% endraw %}
{% endhighlight %}

*  Head over to "_layouts" edit post.html (I am assuming that you want to add comments in the bottom section of the post and if not, please tweak changes accordingly). Add these line in the end
{% highlight javascript %}
{% raw %}
{% if page.comments == true %}
<section id="comments">
  <h1 class="title">Comments</h1>
  {% include disqus.html %}
</section>
{% endif %}
{% endraw %}
{% endhighlight %}

*  Okay enough bullshit,

<center><img src="/images/bullshit.gif" /></center>
<p/>
 time for some explanation. The value which we had assigned to page.identifier is the value which will uniquely going to identify your posts and will gonna load comments accordingly. We have placed an "If" condition in "post.html" which will work only if we are going to declare a variable named as "comments" and assign a true value to it.

Also don't forget to add "disqus_identifier" in your posts and define unique value to it. Perhaps you can use GUID for this purpose, **Just my two cents** :blush:

[Register]: http://disqus.com/Register
[Liquid]: https://github.com/Shopify/liquid/wiki
