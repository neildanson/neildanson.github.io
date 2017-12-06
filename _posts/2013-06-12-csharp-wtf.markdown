---
layout: post
title:  "C# WTF"
date:   2013-06-21 13:04:58 +0000
categories: blog csharp wtf
---

If you like laughing at language failings then you have to have seen [WTFJS](https://wtfjs.com/) or the famous [WAT](http://www.youtube.com/watch?v=kXEgk1Hdze0) video. Of course we can all laugh at Javascript and Ruby because we use C# we dont have to work with such a bad language design………..

# Oh Wait

So no cheating now.

What do you expect the output of this code to be?

{% highlight c# %}
var n = new Nullable<int>();
if (n == null)
    Console.WriteLine("n is null");
else
    Console.WriteLine("n is not null");
{% endhighlight %}

If you guessed “n is null” you’re right! Or you cheated.

Despite having just created an instance of a Nullable struct it is deemed to be null. Thanks to a silent Equals overload we have code that, at face value makes no sense.

# There’s more..

{% highlight c# %}
Nullable<int> n = null;
if (n.HasValue) ....
{% endhighlight %}

If you expected a NullReferenceException here, I’m afraid you failed. In this case C# has actually done the unthinkable and *prevented* a NullReferenceException. But it did it by obscuring the code so that you can only really assess what’s going on by either looking at the IL or by running it. Looking at the code won’t help you here. Thanks to [implicit operators](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/implicit) you can have code that cannot be reasoned easily about without running it.

# Even more?

What happen’s here then?

{% highlight cf# %}
object i = 0;
decimal l = (decimal)i;
{% endhighlight %}

So “i” is an int and an int can be converted to a decimal, therefore this works – right? Alas, no C# will allow the code to compile, looks valid but fail massively at runtime. So you may look at this code and think, that it’s obvious what’s going on here and you wouldn’t write code like this.

The problem is that [certain frameworks](https://en.wikipedia.org/wiki/Windows_Presentation_Foundation) pass objects around all over the place and then expect you to know how to decipher it. Which is fine in a small code-base, but is hugely fragile.

# Even Even more?

Tomas Petricek writes so much more elequently than I can so I’ll refer you to him on a whole host of async/await WTFs – http://tomasp.net/blog/csharp-async-gotchas.aspx

# Wrapping up

So next time you find yourself in a C# ivory tower, looking down on other less fortunate languages, just remember that someone, somewhere is looking down on you and C#, laughing at the simple mistakes that could be easily avoided if only you’d used their language.