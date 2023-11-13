---
layout: post
title:  "Guide: How to do test automation as inefficiently as possible"
date:   2023-11-13 12:02:41 +0100
categories: jekyll update
---

Simple! 
Write UI tests only.

Let me explain.
There is an anti-pattern which I still see in a lot of projects.

Focusing on automated UI tests on a test environment only.

Whenever there are quality problems in a project, non-QA people tend to search for a solution and often the first thing they come up with is testing on an integrated test environment, either with tests that run against a UI or tests on APIs. 
This typically originates from the classic waterfall approach of testing, where testing is its own phase within the software-development-lifecycle (SDLC), right after the implementation.

This is promising at first glance, but comes with a lot of problems:
- Expensive to implement/maintain
- Slow feedback loops
- Brittle and flaky tests
- Does not scale well

Agile processes rely on frequent feedback, short delivery cycles/releases and frequent changes to requirements. Having UI tests only does not help within this process. While there is some worth of creating automated UI/API tests on an integrated test environment, the majority of your tests should not be on this level, due to the mentioned issues.

Unit- and integration tests can give you a far bigger ‘bang for your buck’ than UI tests can. You might ask why:
- You can run more tests faster
- They are less complicated to write
- Less moving parts, so less flaky and brittle 

My suggestion: 
Aim for a good distribution on different test levels to achieve a good coverage while maintaining high development output at frequent releases.
Look at concepts like the test pyramid, the testing trophy or the testing honeycomb if you need some guidance.

Or… hit me up and let me help you! :)

What are the anti-patterns that you learned around automated testing? Let me know!


<!-- You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/ -->
