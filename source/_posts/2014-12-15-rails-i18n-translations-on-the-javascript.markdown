---
author: ranjithru
layout: post
title: "Rails I18n translations on the JavaScript"
date: 2014-12-12 11:49:21 +0530
comments: true
categories: [rails, javascript]
tags: [translations]
keywords: Rails I18n translations on the JavaScript, I18n translations in JavaScript, translations in js
description: Rails I18n translations on the JavaScript

---

Recently, my user story demanded me to translate some text in raw JavaScript file. I know that I can easily use them in my _.js.erb_ templates. But what about the Javascript files(_.js_)?

[**I18n.js**](https://github.com/fnando/i18n-js) is a small library to provide the Rails I18n translations on the JavaScript.
<!--more-->

**Steps for setting up i18n-js:**

1) Add i18n-js gem to your Gemfile<br/>
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #cccccc">gem</span> <span style="color: #cd0000">&quot;i18n-js&quot;</span><span style="color: #cccccc">,</span> <span style="color: #cd0000">&quot;&gt;= 3.0.0.rc8&quot;</span>
<span style="color: #cccccc">bundle</span> <span style="color: #cccccc">install</span>
</pre></div>

2) If you're using the asset pipeline, add the following lines in _app/assets/javascripts/application.js_
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #cd0000">//</span><span style="color: #3399cc">=</span> <span style="color: #cd00cd">require</span> <span style="color: #cccccc">i18n</span>
<span style="color: #cd0000">//</span><span style="color: #3399cc">=</span> <span style="color: #cd00cd">require</span> <span style="color: #cccccc">i18n</span><span style="color: #3399cc">/</span><span style="color: #cccccc">translations</span>
</pre></div>

If you're not using the asset pipeline, add the following lines in your _application.html.erb_ (layout file)
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #3399cc">&lt;%=</span> <span style="color: #cccccc">javascript_include_tag</span> <span style="color: #cd0000">&quot;i18n&quot;</span> <span style="color: #3399cc">%&gt;</span>
<span style="color: #3399cc">&lt;%=</span> <span style="color: #cccccc">javascript_include_tag</span> <span style="color: #cd0000">&quot;translations&quot;</span> <span style="color: #3399cc">%&gt;</span>
</pre></div>

3) To export all translation files, run the following commands
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #cccccc">rake</span> <span style="color: #cd0000">i18n</span><span style="color: #cccccc">:</span><span style="color: #cd0000">js</span><span style="color: #cccccc">:export</span>
</pre></div>

4) Add the following lines in your _application.html.erb_ (layout file)
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #cccccc">&lt;script type=</span><span style="color: #cd0000">&quot;text/javascript&quot;</span><span style="color: #cccccc">&gt;</span>
     <span style="color: #cccccc">I18n.defaultLocale</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;</span><span style="color: #3399cc">&lt;%=</span> <span style="color: #cccccc">I18n</span><span style="color: #3399cc">.</span><span style="color: #cccccc">default_locale</span> <span style="color: #3399cc">%&gt;</span><span style="color: #cd0000">&quot;</span><span style="color: #cccccc">;</span>
     <span style="color: #cccccc">I18n.locale</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;</span><span style="color: #3399cc">&lt;%=</span> <span style="color: #cccccc">I18n</span><span style="color: #3399cc">.</span><span style="color: #cccccc">locale</span> <span style="color: #3399cc">%&gt;</span><span style="color: #cd0000">&quot;</span><span style="color: #cccccc">;</span>
<span style="color: #cccccc">&lt;/script&gt;</span>
</pre></div>

5) Finally, you can use translate your text in _js_ file.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #cccccc">I18n</span><span style="color: #3399cc">.</span><span style="color: #cccccc">t(</span><span style="color: #cd0000">&quot;some.scoped.translation&quot;</span><span style="color: #cccccc">);</span>
</pre></div>

**Note:** Run the following command every time you have new translation in the _.yml_ file
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #cccccc">rake</span> <span style="color: #cd0000">i18n</span><span style="color: #cccccc">:</span><span style="color: #cd0000">js</span><span style="color: #cccccc">:export</span>
</pre></div>





