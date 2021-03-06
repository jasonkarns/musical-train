---
title: Roy.G.Biv alpha
date: 2009-11-17 21:45:50 -05:00
tags:
- css
- rgba
meta:
  original_post_id: '359'
  _wp_old_slug: '359'
permalink: "/2009/11/17/roy-g-biv-alpha/"
---
<p>I was recently on a project that called for a translucent background color over a an image similar to this:</p>
<p>
<img src="{{ site.baseurl }}/assets/2009/11/rgba_thumb.jpg" alt="rgba_thumb" width="491" height="198" class="alignnone size-full wp-image-531" /> <a rel="cc:attributionURL" href="http://www.flickr.com/photos/yeowatzup/">http://www.flickr.com/photos/yeowatzup/</a> / <a rel="license" href="http://creativecommons.org/licenses/by/2.0/">CC BY 2.0</a></p>
<p>Of course, being the conscientious web developers we are, we want to be as semantic as possible with our markup. This means that text should be marked up as text and not flattened into the image, forever to remain hidden from the world of web spiders, search engines, assistive technologies, and mash-up artists. We give the text a background color to keep the text readable over the background image but we still want the background image to be slightly visible through the text area. Before RGBa, we would resort to a 1 x 1px translucent PNG but this adds additional overhead (both with the extra HTTP request, the maintenance of the image should the color change, and a PNG fix for IE6). Another option would be the CSS opacity property. Unfortunately, the opacity property applies to an element and all of its descendants. This means the text itself would become translucent as well, something we would like to avoid if possible. So, let’s use some RGBa!</p>
<p>First, add the standard RGB background color so the text block will still be legible in browsers that don’t support RGBa:</p>
<pre class="css">  div {
    background: rgb(100, 100, 183);
  }</pre>
<p>Now we can enhance this for conforming browsers:</p>
<pre class="css">  div {
    background: rgba(100, 100, 183, .75);
  }</pre>
<p>We now have support in Firefox 3+, Webkit (Safari 3+, Chrome 1+). What about that other browser? To add support for IE6-IE7, we need to use IE's proprietary filter property. As this is a proprietary property, it should be included via an IE-only stylesheet referenced using <a href="http://www.quirksmode.org/css/condcom.html">Conditional Comments</a>.</p>
<pre class="css">  div {
    background:transparent;
    filter:progid:DXImageTransform.Microsoft.gradient(startColorStr=#BF6464B7,endColorStr=#BF6464B7);
    zoom: 1;
  }</pre>
<p>A bit of an explanation is in order. First we set the background to transparent which overrides the solid color rgb declaration. Next we apply IE’s proprietary filter. Notice we set the startColorStr and the endColorStr to the same values. These values are not your standard HEX values. Instead of 0xRRGGBB, the first 2 digits are the alpha transparency. Converting our 75% into HEX (.75 * 255 –&gt; 191.25 –&gt; 0xBF). Lastly, we apply the zoom property to <a href="http://www.satzansatz.de/cssd/onhavinglayout.html">trigger hasLayout</a> on the element. This is required for the filter to take effect.</p>
<p>Keen observers will note that the filter property is not supported in IE8 standards mode. As IE8 now properly follows the CSS grammar, we must add the vendor prefix and quote the value. The hasLayout trigger is no longer needed.</p>
<pre class="css">  div {
    background:transparent;
    -ms-filter:"progid:DXImageTransform.Microsoft.gradient(startColorstr=#BF6464B7,endColorstr=#BF6464B7)";
  }</pre>
<p>Combined, we have our main CSS:</p>
<pre class="css">  div {
    background: rgb(100, 100, 183);
    background: rgba(100, 100, 183, .75);
  }</pre>
<p>and IE's CSS:</p>
<pre class="css">  div {
    background:transparent;
    filter:progid:DXImageTransform.Microsoft.gradient(startColorStr=#BF6464B7,endColorStr=#BF6464B7);
    -ms-filter:"progid:DXImageTransform.Microsoft.gradient(startColorstr=#BF6464B7,endColorstr=#BF6464B7)";
    zoom: 1;
  }</pre>
<p>We have now achieved cross-browser, CSS-only (no PNGs needed), alpha transparency!</p>
