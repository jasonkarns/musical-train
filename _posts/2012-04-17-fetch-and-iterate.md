---
title: Fetch and Iterate
date: 2012-04-17 23:33:34 -04:00
tags:
- javascript
- php
- ruby
- while-loop
meta:
permalink: "/2012/04/17/fetch-and-iterate/"
---
<p>Tonight I stumbled across a <a href="http://qfox.nl/weblog/248">blog post</a> <em>from the future</em>! Or something like that. On 4/17 at 11:30PM I read a blog post that was published on 4/18 at 1:54. Time zones = time travel. <em>Any</em>way…</p>
<p>It was about finding a cleaner, more succinct, syntax for a standard pattern:    <br />fetch_data, process_data, rinse and repeat until no more data!</p>
<p>Quick, <a href="http://qfox.nl/weblog/248">go read the original post</a>. Don’t worry, it’s short.</p>
<p>{elevator music}</p>
<p>Okay, so how do we improve this? Well, in some languages (like JavaScript, Ruby, PHP, et. al), an assignment expression actually returns a value. And since the expression returns a value, the expression itself can be used as the looping condition, like so:</p>
<pre><code>while(data = fetch()){
  process(data);
}​</code></pre>
<p>Notice the single equals sign? We are doing assignment here, not an equality check. We invoke <var>fetch()</var> and the result is returned and assigned to <var>data</var>. Now the value of <var>data</var> itself is used as the <kbd>while</kbd> condition. So the data is continually fetched and processed until <var>fetch()</var> returns a falsy value. (In JavaScript, that would be one of <kbd>false</kbd>, <kbd>undefined</kbd>, <kbd>null</kbd>, <kbd>0</kbd>, <kbd>""</kbd>, or <kbd>NaN</kbd>)</p>
<p>This is a very common pattern in PHP, especially when fetching/querying data from a database. It’s a much less common pattern in JavaScript, but still quite valid. As is with most useful things, this approach can be misused. It can easily be confused for a typo (missing an equals sign?) so it should be used with care. For instance, ensuring that your <var>fetch()</var> function and <var>data</var> variable are named clearly should make it less likely to be misunderstood. When used appropriately, it can certainly increase the clarity of your code.</p>
