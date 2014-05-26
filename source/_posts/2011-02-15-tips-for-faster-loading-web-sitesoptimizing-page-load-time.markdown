---
author: ranjithru
comments: true
date: 2011-02-15 05:31:44+00:00
layout: post
slug: tips-for-faster-loading-web-sitesoptimizing-page-load-time
title: Tips for faster loading web sites(Optimizing page load time)
wordpress_id: 37
categories:
- Rails
tags:
- optimization
- rails
---

**1) Make fewer HTTP request(Js, CSS & image)**
Most of the end-user response time is spent on the front-end and tied up in downloading all the components in the page like images, stylesheets, scripts, etc. Reducing the number of components in turn reduces the number of HTTP requests.
_a) Combined files_ => its a way to reduce the number of HTTP requests by combining all files into a single file. ex: js & css
In our rails app, we used bundle_fu([https://github.com/timcharper/bundle-fu](https://github.com/timcharper/bundle-fu)). Its used to bundle all your assets very easy. It can speed your load time up around 50%.

Example put the following around your stylesheets/javascripts:

    
             -bundle :name => "default_bundle" do
               = javascript_include_tag "http://w.sharethis.com/button/buttons.js"
               = stylesheet_link_tag 'jquery-ui', 'auto_complete/token-input.css'
               = javascript_include_tag 'jquery-1.4.2.js', 'jquery-ui.js', 'auto_complete/jquery.tokeninput.js', 'auto_complete/setup.js', 'underscore.js', 'date.js', 'cal.js', 'application.js', 'time_picker/jquery.timePicker.js', 'ajax_pagination.js'
               = stylesheet_link_tag 'compiled/certification.css','compiled/error.css', 'compiled/elements.css', 'compiled/messages.css', 'compiled/calendar.css', 'compiled/common.css', 'time_picker/timePicker.css', :media => 'screen, projection'
               = stylesheet_link_tag 'compiled/print.css', :media => 'print'
               = javascript_include_tag "markerCluster/jsapi", "markerCluster/map.js", "markerCluster/markerclusterer.js", "jquery-jtemplates"
    


_b) CSS sprites_     => Its used to reducing the number of image requests. Combine your background images into a single image and use the CSS background-image and background-position properties to display the desired image segment.

**2) Avoid empty src or href**
You may expect a browser to do nothing. But most browsers makes a request to server(sending a large amount of unexpected traffic).

**3) Compress components with gzip**
This is used to reduce their file size over the wire by approximately 70%. This can be set up using your Apache(needs Apache 2, mod_deflate, mod_headers and access to server config) or Nginx config.

example for apache(in the server config file):

    
    
    # Compress some text file types
    AddOutputFilterByType DEFLATE text/html text/css text/xml application/x-javascript
    
    # Deactivate compression for buggy browsers
    BrowserMatch ^Mozilla/4 gzip-only-text/html
    BrowserMatch ^Mozilla/4\.0[678] no-gzip
    BrowserMatch \bMSIE !no-gzip !gzip-only-text/html
    
    # Set header information for proxies
    Header append Vary User-Agent



**4) Add expires headers for JS & CSS**
There are two aspects to this rule:
_a) For static components :_ implement "Never expire" policy by setting far future Expires header
_b) For dynamic components:_ use an appropriate Cache-Control header to help the browser with conditional requests

This means that A first-time visitor to your page may have to make several HTTP requests, but by using the Expires header you make those components cacheable. The next request, Browser use a cache to reduce the number and size of HTTP requests, making web pages load faster. A web server uses the Expires header in the HTTP response to tell the client how long a component can be cached. However, it creates an additional problem too. The problem is what happens if you change these files? The browser will be stuck with the old files. The solution is to send a last modified timestamp with your requests (Ex: "<img src='/images/rails.png?84392578943' />"). Now your browser will know to ask for the file again. The "timestamp" is the default behavior of rails.

**5) Put CSS at top**
Yahoo discovered that moving stylesheets to the document HEAD makes pages appear to be loading faster. This is because putting stylesheets in the HEAD allows the page to render progressively.

**6) Minify JS & CSS**
Minification is the practice of removing unnecessary characters from code like comments and unneeded white space characters (space, newline, tab and etc). This improves response time performance & load times. You can use JSMin and YUI Compressor for minifying your JS code. Also some plugins are there, please check it here: [https://github.com/sinefunc/sinatra-minify](https://github.com/sinefunc/sinatra-minify) and [https://github.com/ericbarnes/ci-minify](https://github.com/ericbarnes/ci-minify).

please check the screenshot. Right now, as you can see, I have made it Yslow grade from "F" to "B" very easily. I am sure, we can easily get grade "A" too... We need some support from the server side regarding "Add expires header" and "Use Cookie-free Domains for Components". I have requested engineyard(hosting server) for the same. Waiting for the reply from them. By next week, it will turn into grade "A".

and also found one good link from rubyquicktips. Benchmark.ms is very nice. its used to track how long some bit of code takes to process. please check it here: [http://rubyquicktips.tumblr.com/post/2838217166/benchmark-ms-rails-you-sneaky-devil](http://rubyquicktips.tumblr.com/post/2838217166/benchmark-ms-rails-you-sneaky-devil)

Before optimization:
[![](http://ranjithonrails.files.wordpress.com/2011/02/screenshot.png?w=300)](http://ranjithonrails.files.wordpress.com/2011/02/screenshot.png)

After optimization:
[![](http://ranjithonrails.files.wordpress.com/2011/02/picture-4.png?w=300)](http://ranjithonrails.files.wordpress.com/2011/02/picture-4.png)
