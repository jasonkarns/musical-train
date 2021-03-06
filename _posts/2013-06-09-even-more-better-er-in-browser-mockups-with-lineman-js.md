---
title: Even More Better-er In-Browser Mockups with Lineman.js
date: 2013-06-09 20:35:49 -04:00
tags:
- frontend
- lineman
- node
meta:
permalink: "/2013/06/09/even-more-better-er-in-browser-mockups-with-lineman-js/"
---
<p><a href="http://alistapart.com/issue/374">Issue #374</a> of <a href="http://alistapart.com/">A List Apart</a> by <a href="http://alistapart.com/author/garann">Garann Means</a> is a great two-part piece on using Node.js for in-browser mockups and prototypes. In "<a href="http://alistapart.com/article/even-better-in-browser-mockups-with-node.js">Even Better In-Browser Mockups with Node.js</a>" she gives a great overview for why using Node for mockups/prototypes is such a great idea. In part two—"<a href="http://alistapart.com/article/node-at-work-a-walkthrough">Node At Work: A Walkthrough</a>"—she dispenses with the theory and shows hands on how to build a mockup that you can use, using Node.js. I strongly recommend you read both articles.</p>
<p>Using Node.js for mockups provides quite a few key benefits, not the least of which being:</p>
<ol>
<li>Mockup built using web technologies, not Photoshop comps</li>
<li>It works! (users/stakeholders can get their hands on it)</li>
<li>Rapid development</li>
</ol>
<p>But there are a few drawbacks to vanilla Node/express.js:</p>
<ol>
<li>No CSS pre-processors: SASS, LESS or Stylus</li>
<li>No JavaScript pre-compilation languages: CoffeeScript, TypeScript</li>
<li>Hand-wiring templating engines</li>
<li>What about client-side frameworks: Backbone, Angular, or Ember?</li>
<li>Node's HTTP server (express) can be a pain</li>
<li>Restarting the server on every file change? Blech!</li>
</ol>
<p>Lineman.js can help!</p>
<h2>Lineman.js</h2>
<p>Lineman is a tool to help you build fat-client webapp projects. Mockups and Prototypes certainly fit that bill! In fact, when working on mockups and prototypes, generally speed of deployment and rapid development is a key factor. Lineman can help you scaffold out a mockup and get you up and running fast!</p>
<p>In addition to all the benefits that Garann discussed for why using Node is a good idea for mockups, Lineman also provides the ability to use CSS preprocessors, compile-to-JavaScript languages, and choose your own templating tool. (Lineman ships with support for LESS and SASS CSS pre-processors, Underscore and Handlebars template engines, and CoffeeScript compilation. And it's easy to add support for your tooling of choice e.g. Stylus, TypeScript, Mustache)</p>
<p>Lineman eases the pain of integrating with a server-side API. You can simply <a href="https://github.com/testdouble/lineman#stubbing-server-side-endpoints">stub out the API endpoints</a> in Lineman's <code>config/server.js</code> configuration file (which makes a great deliverable to the server-side team when it comes time to build out the real server endpoint). Or, if the server-side API already exists, you can tell Lineman to just <a href="https://github.com/testdouble/lineman#proxying-requests-to-another-server">proxy requests to the real API</a>.</p>
<p>Further, to increase the pace of development and feedback loop, let Lineman handle recompiling your assets and restarting the server for you! And lastly, if you plan on using one of the many great client-side frameworks out there (like Backbone, Angular or Ember), Lineman has <a href="https://github.com/testdouble/lineman#starting-from-a-template">project templates</a> ready to get you going.</p>
<h2>"Cool Art Store" Demo, ported to Lineman</h2>
<p>I <a href="https://github.com/jasonkarns/coolartstore">created a fork</a> of Garann's <a href="https://github.com/garann/coolartstore">original "Cool Art Store" demo project</a> to show how easy it is to use Lineman for simple mockups. I isolated the Lineman port to a <a href="https://github.com/jasonkarns/coolartstore/tree/lineman">branch named 'lineman'</a>.</p>
<p>What was necessary to get Garann's Cool Art Store demo running on Lineman?</p>
<ol>
<li>Ran <code>$ lineman new coolartstore</code> to get the base Lineman archetype project. (much easier than starting with a blank canvas)</li>
<li>Moved the css, js, image, and template assets from <code>public/</code> to <code>app/</code>. (Not strictly necessary – Lineman can be configured to look for assets anywhere you wish by using <code>config/files.js</code> or <code>config/files.coffee</code>)</li>
<li>Converted the templates from <a href="http://olado.github.io/doT/index.html">doT.js</a> to <a href="http://handlebarsjs.com/">Handlebars</a> (Also, not technically necessary – there are a <a href="https://npmjs.org/search?q=grunt+dot">number of doT-precompile grunt tasks</a>, so configuring Lineman to use doT is also an option; I just hapen to prefer Handlebars.)</li>
<li>Stripped out the XHR requests for the <code>doT</code> templates; Lineman pre-compiles your templates into a single JS object so you need only execute the template at runtime.</li>
<li>Ported <code>server.js</code> to use Lineman's <code>config/server.js</code> for defining the API endpoint stubs. This mostly boiled down to removing all the express.js setup code. (Lineman makes it easy to just define the endpoints and not deal with boilerplate.)</li>
<li><strong>Bonus</strong>: since Lineman runs the JavaScript through JSHint, it caught a couple of lint-warnings that were promptly fixed.</li>
<li><strong>Bonus</strong>: Lineman concatenates and minifies the final CSS/JS assets. If this weren't just a mockup, we'd be one step closer to shipping!</li>
<li><strong>Bonus</strong>: using <code>lineman run</code> meant that I didn't have to keep restarting the node server after each file modification!</li>
</ol>
<h2>Not just for Mockups</h2>
<p>Of course, Lineman is really tailored for developing fat-client webapps for <a href="https://github.com/testdouble/lineman#deployment">production</a>, not just mockups in particular. So TDD your client-side application using your JavaScript test framework of choice and let Lineman <a href="https://github.com/testdouble/lineman#specs">run the specs/tests for you</a>! Or <a href="https://github.com/testdouble/lineman#continuous-integration-specs">integrate your test suite into your Continuous Integration build</a>. And let Lineman help with <a href="https://github.com/testdouble/lineman#heroku">deployment to Heroku</a> as well!</p>
