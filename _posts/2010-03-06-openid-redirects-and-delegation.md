---
title: 'OpenID: Redirects and Delegation'
date: 2010-03-06 17:59:31 -05:00
tags:
- openid
meta:
  original_post_id: '359'
  _wp_old_slug: '359'
permalink: "/2010/03/06/openid-redirects-and-delegation/"
---
<h2>The Introduction</h2>
<p>I'm a big fan of <a href="http://openid.net/">OpenID</a>. I like the fact that my online (public) identity is associated with a URL that I own. This affords quite a few benefits, such as associating my public profile at various networks with one another. Even better, OpenID supports the delegation of identifiers to OpenID Providers. This allows the owner of a domain to use the domain as an OpenID without operating his own OpenID server. He simply delegates the provider responsibilities to an existing provider by adding some HTML <code>link</code> references at the top of his OpenID URL. But before I get too far ahead of myself, a bit of history.</p>
<h2>The History</h2>
<p>The Global Name Registry was delegated the .name top-level domain by ICANN in 2001. [<a href="http://en.wikipedia.org/wiki/Global_Name_Registry">Wikipedia</a>] The intention was to set aside a specific top-level domain for individuals to register as their own domain. These domains may be registered on the second level (john.name) and the third level (john.doe.name). Generally, the second level domains are shared among the registrants of the third-level. (Aside: I find it rather surprising that the assortment of ancestry sites don't take advantage of this for linking in family trees) In 2007 the GNR spun-off a small start-up formed as a partnership with <a href="http://janrain.com/">JanRain</a>'s OpenID provider, <a href="https://www.myopenid.com/">myOpenID</a>. This partnership created FreeYourID.com. The goal of FreeYourID was to make it dead simple for users to both register their own .name domain and use it as an OpenID. FreeYourID provided a couple great features. The domain registration was transparent to the end user, making it very friendly to non-techies. A .name email address was created (john@doe.name). FreeYourID provided a few URL forwarding options for those with existing domains. They had a decent default landing page that aggregated various social network profiles (YouTube, Flickr, Blogs). They even supported <a href="http://microformats.org/">microformats</a> with <a href="http://microformats.org/wiki/XFN">XFN</a>! And via the partnership with myOpenID, the .name domain was automatically setup as an OpenID.</p>
<h2>The Situation</h2>
<p>So in 2007 I registered <a href="http://jason.karns.name">jason.karns.name</a> from FreeYourID.com. A myOpenID account was created behind the scenes which handled the OpenID login. So as an end user I would attempt OpenID log-in at a relying party, which would navigate to <a href="http://jason.karns.name">jason.karns.name</a> and encounter the OpenID delegation snippets. This would foward over to myOpenID (tied to the shim account) where I would authenticate and be redirected back to the original service having been authenticated as <a href="http://jason.karns.name">jason.karns.name</a>. I used this service for 2 years as OpenID was beginning to gather steam. I used <a href="http://jason.karns.name">jason.karns.name</a> as my primary web address (which was forwarded to other sites). I collected a few social network links on FreeYourID's social network page (which provided a great resource for XFN crawlers). But most importantly, I used my OpenID as my primary (in some cases, <em>only</em>) authentication method at quite a few online services.</p>
<h2>The Problem</h2>
<p>At the beginning of 2009, the .name domain was transferred to VeriSign. This spelled the beginning of the end for FreeYourID. Toward the end of the year, FreeYourID announced it was shutting down and would be transferring all services over to <a href="http://domaindiscount24.net">DomainDiscount24</a>. Prior to the transfer, I purchased <a href="http://jasonkarns.com">jasonkarns.com</a>. Once my .name was transferred, I setup an HTTP 301 redirect from <a href="http://jason.karns.name">jason.karns.name</a> to <a href="http://jasonkarns.com">jasonkarns.com</a>. The landing page of <a href="http://jasonkarns.com">jasonkarns.com</a> contained the same OpenID delegation snippets as <a href="http://jason.karns.name">jason.karns.name</a> so I assumed everything would continue to work. I was wrong. This setup prevented me from logging in at every service that used OpenID.</p>
<h2>The Discovery</h2>
<p>Through some digging and a bit of guidance by <a href="http://willnorris.com/2008/12/challenges-in-changing-my-openid">this post</a> (thanks Will), I <a href="http://openid.net/specs/openid-authentication-2_0.html#normalization">discovered</a> that an OpenID relying part must follow all redirects and the final destination URL is used as the OpenID Identifier rather than the original URL. So in my case, I would attempt to login with <a href="http://jason.karns.name">jason.karns.name</a> which redirected to <a href="http://jasonkarns.com">jasonkarns.com</a>, which was then delegated to myOpenID. I would authenticate normally at myOpenID because the delegation snippets specify which account to use at the provider. However, when redirected back to the relying party, my authentication token reported my ID as <a href="http://jasonkarns.com">jasonkarns.com</a>. As there was no existing account registered for <a href="http://jasonkarns.com">jasonkarns.com</a>, most relying parties would initiate their 'new user' flow. Others just error-ed out.</p>
<h2>The Fix</h2>
<p>So now I realize what the root problem is, but I'm not sure how to fix it. I definitely have to get my OpenID working again so I must serve the OpenID delegation code from <a href="http://jason.karns.name">jason.karns.name</a> directly. However, I also want to continue using <a href="http://jasonkarns.com">jasonkarns.com</a> as the primary online destination for people looking for me.</p>
<ol>
<li>Configure DomainDirect24 to stop the HTTP 301 redirect to <a href="http://jasonkarns.com">jasonkarns.com</a></li>
<li>Create a landing page for <a href="http://jason.karns.name">jason.karns.name</a> which contains the OpenID delegation code</li>
<li>Use the old-school <a href="http://www.w3schools.com/tags/att_meta_http_equiv.asp">meta-refresh</a> to handle the redirect from <a href="http://jason.karns.name">jason.karns.name</a> to <a href="http://jasonkarns.com">jasonkarns.com</a>. OpenID Relying Parties won't follow the meta-refresh because they are only interested in the delegation code.</li>
<li>Setup a frameset to load <a href="http://jasonkarns.com">jasonkarns.com</a> from within jason.karns.name. This is only for user-agents that don't or won't follow the the meta-refresh. This way end users still end up with the same content.</li>
</ol>
<h2>The Result</h2>
<pre class="html"><code>
&lt;!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Frameset//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-frameset.dtd"&gt;
&lt;html xmlns='http://www.w3.org/1999/xhtml' xml:lang='en' lang='en'&gt;
 &lt;head&gt;
 &lt;title&gt;jason.karns.name&lt;/title&gt;
 &lt;meta http-equiv='Content-type' content='text/html; charset=utf-8' /&gt;
 &lt;!-- OpenID server and delegate --&gt;
 &lt;link rel="openid.server openid2.provider" href="http://www.myopenid.com/server" /&gt;
 &lt;link rel="openid.delegate openid2.local_id" href="http://jason.karns.name" /&gt;
 &lt;meta http-equiv="X-XRDS-Location" content="http://www.myopenid.com/xrds?username=jason.karns.name" /&gt;
 &lt;!--Redirect has been requested --&gt;
 &lt;meta http-equiv='refresh' content='0;url=http://jasonkarns.com' /&gt;
 &lt;/head&gt;
 &lt;!-- Frame Redirection for human content readers --&gt;
 &lt;frameset rows='100%,*' style='border:0;'&gt;
 &lt;frame src='http://jasonkarns.com' frameborder='0' /&gt;
 &lt;frame frameborder='0' noresize='noresize' /&gt;
 &lt;/frameset&gt;
&lt;/html&gt;</code></pre>
<h2>The Future</h2>
<p>While this isn't the optimal solution, it works for now. I rather like the idea of potentially having 2 separate OpenIDs (though they are both delegated to the same myOpenID account). However, I don't like the meta-refresh redirect. At one point there was a discussion going on for the OpenID spec around giving 303 (See Other) redirects special behavior—as oppposed to 301 (Moved Permanently), 302 (Found) and 307 (Temporary Redirect). Although at this point, I don't hold much hope. My only other choice of action is to follow <a href="http://willnorris.com/2008/12/challenges-in-changing-my-openid">Will Norris' method</a> and use server-side HTTP Request sniffing and respond accordingly (OpenID delegation for suspected Relying Parties, 301 Redirect for everyone else).</p>
