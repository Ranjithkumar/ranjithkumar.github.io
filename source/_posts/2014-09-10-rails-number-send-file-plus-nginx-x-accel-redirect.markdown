---
author: ranjithru
layout: post
title: "Rails#send_file + Nginx X-Accel-Redirect"
date: 2014-09-10 16:13:33 +0530
comments: true
categories: [Rails, Nginx]
tags: [send_file, X-Accel-Redirect] 
keywords: Rails#send_file, Nginx X-Accel-Redirect, X-Accel-Redirect, send_file, Best performance systems for file distribution
description: Rails#send_file + Nginx X-Accel-Redirect = Best performance systems for file distribution

---

Sometimes you may need to serve some static files (CSV, PDF, XLS etc) to your users, but only after they have logged in. Obviously you can’t just keep the static file in your public folder as anyone could just use the URL to download files.

One possible solution for protected downloads is to just use the #send_file method provided by Rack to send a non-public file to the user, but serving static files with your app server (Unicorn, Mongrel, Thin etc) is a bad idea as it’s really inefficient. The best approach is to allow the app server to handle the authentication/authorization and then hand the actual downloading to your web server (Nginx, Apache, Lighttpd etc).
<!--more-->

In Lighttpd server it can be done by returning X-Sendfile header from your script. Nginx have its own implementation of such idea using X-Accel-Redirect header.

**The need for X-Accel-Redirect:**

* To deliver large files.
* For those files to not be available to the public
* Would be able to free some resources on server while nginx will handle all slow requests to dynamic content

In this article I will assume that the site is located in _/home/kranjith/sites/projects/blog_ directory and there are some static files (like CSV, PDF, XLS etc) located in _/home/kranjith/sites/projects/blog/uploads_ directory.

First of all, lets take a look at our nginx configuration:
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># Nginx X-Accel-Redirect configuration for Rails and Unicorn </span>
<span style="color: white">upstream unicorn_blog_server {</span>
    <span style="color: white">server unix:/home/kranjith/sites/projects/blog/tmp/sockets/unicorn.sock</span>
    <span style="color: white">fail_timeout=0;</span>
<span style="color: white">}</span>

<span style="color: white">server {</span>
    <span style="color: white">listen       80;</span>
    <span style="color: white">server_name  example.org;</span>
 
    <span style="color: white">root /home/kranjith/sites/projects/blog/public;</span>
    
    <span style="color: white">location /downloads {</span>
      <span style="color: white">internal;</span>
      <span style="color: white">alias /home/kranjith/sites/projects/blog/uploads;</span>
    <span style="color: white">}</span>
    
    <span style="color: white">location / {</span>
       <span style="color: white">try_files $uri @blog;</span>
    <span style="color: white">}</span>

    <span style="color: white">location @blog {</span>
      <span style="color: white">proxy_redirect    off;</span>
      
      <span style="color: white">proxy_set_header  Host              $http_host;</span>
      <span style="color: white">proxy_set_header  X-Real-IP         $remote_addr;</span>
      <span style="color: white">proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;</span>
 
      <span style="color: white">proxy_set_header  X-Sendfile-Type   X-Accel-Redirect;</span>
      <span style="color: white">proxy_set_header  X-Accel-Mapping   /home/kranjith/sites/projects/blog/uploads/=/downloads/;</span>
      
      <span style="color: white">proxy_pass http://unicorn_blog_server;</span>
    <span style="color: white">}</span>
<span style="color: white">}</span>
</pre></div>
    
    
**The internal keyword for the /downloads location prevents the uploads folder from being publicly accessible**.
    
Next you need to ensure that Rails knows what server you are using

In _config/environments/production.rb_ file.
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># Specifies the header that your server uses for sending files.</span>
<span style="color: #f8f8f2">config</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">action_dispatch</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">x_sendfile_header</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&#39;X-Accel-Redirect&#39;</span> <span style="color: #75715e"># for nginx</span>
</pre>
</div>

In _DownloadsController_, just do whatever authorization you need to, then use #send_file to serve the file to the user:
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #66d9ef">class</span> <span style="color: #a6e22e">DownloadsController</span> <span style="color: #f92672">&lt;</span> <span style="color: #66d9ef">ApplicationController</span>
  <span style="color: #f8f8f2">load_and_authorize_resource</span>

  <span style="color: #66d9ef">def</span> <span style="color: #a6e22e">show</span>
    <span style="color: #f8f8f2">send_file(</span> <span style="color: #66d9ef">Rails</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">root</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">to_s</span> <span style="color: #f92672">+</span> <span style="color: #f8f8f2">@uploaded_file</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">file</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">url,</span> <span style="color: #e6db74">type</span><span style="color: #f8f8f2">:</span> <span style="color: #f8f8f2">@uploaded_file</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">content_type,</span> <span style="color: #e6db74">filename</span><span style="color: #f8f8f2">:</span> <span style="color: #f8f8f2">@uploaded_file</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">filename,</span> <span style="color: #e6db74">dispostion</span><span style="color: #f8f8f2">:</span> <span style="color: #e6db74">&quot;inline&quot;</span><span style="color: #f8f8f2">,</span> <span style="color: #e6db74">status</span><span style="color: #f8f8f2">:</span> <span style="color: #ae81ff">200</span><span style="color: #f8f8f2">,</span> <span style="color: #e6db74">stream</span><span style="color: #f8f8f2">:</span> <span style="color: #66d9ef">true</span><span style="color: #f8f8f2">,</span> <span style="color: #f8f8f2">x_sendfile:</span> <span style="color: #66d9ef">true</span> <span style="color: #f8f8f2">)</span>	
  <span style="color: #66d9ef">end</span> 	
<span style="color: #66d9ef">end</span>
</pre></div>

I am using CarrierWave to upload files from Rails applications. In _config/initializers/carrierwave.rb_
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #66d9ef">CarrierWave</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">configure</span> <span style="color: #66d9ef">do</span> <span style="color: #f92672">|</span><span style="color: #f8f8f2">config</span><span style="color: #f92672">|</span>
  <span style="color: #f8f8f2">config</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">storage</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">:file</span>
  <span style="color: #f8f8f2">config</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">root</span> <span style="color: #f92672">=</span> <span style="color: #66d9ef">Rails</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">root</span>
  <span style="color: #f8f8f2">config</span><span style="color: #f92672">.</span><span style="color: #f8f8f2">store_dir</span> <span style="color: #f92672">=</span> <span style="color: #e6db74">&quot;uploads&quot;</span>
<span style="color: #66d9ef">end</span>
</pre></div>

And that’s it! **With described approach we are able to create very flexible and extremely performance systems for file distribution!**

Now I’m going to run through a specific example of downloading a file.

1.Browser makes a request for a file
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># HTTP Headers</span>
<span style="color: white">GET /downloads/SecretSquirrel.zip</span>
</pre></div>

2.Nginx receives this request. It adds on a header with configuration data that will be required by rails.
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># Example HTTP Headers with additional header added by nginx</span>
<span style="color: white">GET /downloads/SecretSquirrel.zip</span>
<span style="color: white">X-Accel-Mapping</span><span style="color: #f8f8f2">:</span>   <span style="color: white">/home/kranjith/sites/projects/blog/uploads/=/downloads/</span>
</pre></div>

3.Nginx passes the request onto Rails and it invokes the relevant controller.

4.The controller makes its authorization checks and calls send_file. Use the absolute path to the file.
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># controller code (e.g. app/controllers/downloads_controller.rb)</span>
<span style="color: white">send_file(&#39;/home/kranjith/sites/projects/blog/uploads/SecretSquirrel.zip&#39;)</span>
</pre></div>

5.Rails (Rack to be precise) then decides what to with the file. Rails knows what server we are using (from _config/environments/production.rb_). Instead of using the file as the body of the request, it will add a header to the response. It uses the X-Accel-Mapping that nginx added earlier to change the file path.
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># HTTP Response</span>
<span style="color: white">HTTP/1.1 200 OK</span>
<span style="color: white">X-Accel-Redirect</span><span style="color: #f8f8f2">:</span> <span style="color: white">/downloads/SecretSquirrel.zip</span>
<span style="color: white">Content-Type</span><span style="color: #f8f8f2">:</span> <span style="color: white">application/octet-stream</span>
<span style="color: white">Content-length</span><span style="color: #f8f8f2">:</span> <span style="color: white">...</span>
<span style="color: white">Content-Disposition</span><span style="color: #f8f8f2">:</span> <span style="color: white">attachment; filename=&quot;SecretSquirrel.zip&quot;</span>
<span style="color: white">&lt;empty body&gt;</span>
</pre></div>

6.Nginx receives this header from rails and interprets it. It finds the location directive and reverses the changes to the path that rails made in step 5.
<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># HTTP Response</span>
<span style="color: white">HTTP/1.1 200 OK</span>
<span style="color: white">Content-Type</span><span style="color: #f8f8f2">:</span> <span style="color: white">application/octet-stream</span>
<span style="color: white">Content-Length</span><span style="color: #f8f8f2">:</span> <span style="color: white">...</span>
<span style="color: white">Content-Disposition</span><span style="color: #f8f8f2">:</span> <span style="color: white">attachment; filename=&quot;SecretSquirrel.zip&quot;</span>
<span style="color: white">&lt;contents of /home/kranjith/sites/projects/blog/uploads/SecretSquirrel.zip&gt;</span>
</pre></div>

7.Browser receives the file as if it was a normal download.







        
