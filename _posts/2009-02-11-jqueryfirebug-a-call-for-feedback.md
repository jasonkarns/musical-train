---
title: 'jQuery.Firebug: A call for feedback.'
date: 2009-02-11 15:17:49 -05:00
tags:
- debugging
- firebug
- javascript
- jquery
meta:
  original_post_id: '28'
  _wp_old_slug: '28'
permalink: "/2009/02/11/jqueryfirebug-a-call-for-feedback/"
---
<p>As a result of some of the discussion following from my post on my new jQuery plugin, <a href="http://jasonkarns.com/blog/2009/01/06/announcing-jqueryfirebug/">jQuery.Firebug</a> I'm soliciting feedback for its desired behavior. Example:</p>
<pre class="javascript">
$('.setA').log();
$('.setB').log("some", "information");
$('.setC').log("title attribute is: ", ".attr('title')");
</pre>
<p>Some explanation. The log method follows the same rules as the <a href="http://getfirebug.com/console.html">Firebug console.log</a> method. It can take 0 or more arguments that are concatenated into a space-separated string when finally printed to the console. For some jQuery-specific behavior, I have added a little wrinkle as shown with the log statement following SetC. If an argument to the log method:</p>
<ol>
<li>is a string</li>
<li>begins with a period (dot)</li>
<li>is a valid jQuery method</li>
</ol>
<p>then the jQuery method specified is executed on the jQuery selection and the <em>result</em> is printed to the console. In the example above, if the title attribute on the first element of <var>SetC</var> is <code>'example title'</code> then the final log message would be <code>"title attribute is: example title"</code>.</p>
<p>Further, my the plugin will feature an additional option (off by default) that will explicitly print each element in the jQuery selection wrapped in a <code>console.group</code>. In the example above, say <var>SetC</var> contains 2 <code><span /></code> elements. If the option were turned on, the output would be similar to the output of the following:</p>
<pre class="javascript">
console.log("title attribute is: example title");
console.group($(".setC"));
console.log($(".setC").get(0));
console.log($(".setC").get(1));
console.groupEnd();
</pre>
<p>So, back to the problem at hand. My issue, is when and where to print the jQuery selection itself. The different options are:</p>
<ol>
<li>only print the jQuery selection when there are no arguments to the log method</li>
<li>only print the jQuery selection when there are no arguments to the log method but also print the jQuery selection in place of any string argument  equalling <code>"this"</code> (similar to my jQuery method replacement demonstrated above with <code>.attr("title")</code>)</li>
<li>always prepend the jQuery selection to the arguments (so the jQuery selection is printed before the rest of the arguments)</li>
<li>always append the jQuery selection to the arguments (so the jQuery selection is printed after the rest of the arguments)</li>
</ol>
<p>I'm leaning towards either #3 or #4 but am open to feedback. Please comment with your suggestions. Keep in mind that all four above choices will still result in just one log message per <code>log()</code> call. Turning on the '<code>explicit</code>' option is the only thing that will result in more console messages than <code>log()</code> calls. Also, keep in mind that printing the jQuery selection itself to the console will allow deep inspection. For instance, clicking on the jQuery selection in Firebug shows what elements are selected, etc.</p>
