---
author: ranjithru
layout: post
title: "Metaprogramming Ruby"
date: 2014-12-01 12:16:06 +0530
comments: true
categories: [ruby, metaprogramming]
tags: [Metaprogramming]
keywords: Metaprogramming, Dynamic Dispatch, Pattern Dispatch, Dynamic Method, Ghost Method, Dynamic Proxy, Blank Slate, Kernel Method, Scope Gate, Flattening the scope, Eigenclass, Context Probe, Clean Room, class_eval, Class Macro, Around Alias, Hook Methods, Class Extension Mixin
description: Metaprogramming Ruby

---

I Love the book "Metaprogramming Ruby" by Paolo Perrotta and found it very informative. The idioms defined in the book are so helpful. Here I have created a reference based on them for my own use. Hopefully it will help others too.

**1) Dynamic Dispatch**<br/>
Ruby allows us to dynamically call unknown methods(even private methods) on objects.

<!-- HTML generated using hilite.me -->
<div style="background: #272822; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: none; border: 0;"><span style="color: #75715e"># object.send(message, *arguments)</span>
<span style="color: white">2.send(:+, 3) # => 5</span>
</pre></div>

<!--more-->

**2) Pattern Dispatch**<br/>
Similar to Dynamic Dispatch, but uses a convention or pattern to identify which methods to call.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">User</span>
  <span style="color: #cdcd00">attr_accessor</span> <span style="color: #cd0000">:first_name</span>
  <span style="color: #cdcd00">attr_accessor</span> <span style="color: #cd0000">:last_name</span>

  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">full_name</span>
    <span style="color: #cd0000">&quot;#{</span><span style="color: #cccccc">first_name</span><span style="color: #cd0000">} #{</span><span style="color: #cccccc">last_name</span><span style="color: #cd0000">}&quot;</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">user</span> <span style="color: #3399cc">=</span> <span style="color: #cccccc">User</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span>
<span style="color: #cccccc">user</span><span style="color: #3399cc">.</span><span style="color: #cccccc">first_name</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;Ranjithkumar&quot;</span>
<span style="color: #cccccc">user</span><span style="color: #3399cc">.</span><span style="color: #cccccc">last_name</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;Ravi&quot;</span>

<span style="color: #75715e"># use pattern dispatch to invoke all &#39;name&#39; methods</span>
<span style="color: #cccccc">user</span><span style="color: #3399cc">.</span><span style="color: #cccccc">public_methods</span><span style="color: #3399cc">.</span><span style="color: #cccccc">each</span> <span style="color: #cdcd00">do</span> <span style="color: #3399cc">|</span><span style="color: #cccccc">method_name</span><span style="color: #3399cc">|</span>
  <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;#{</span><span style="color: #cccccc">method_name</span><span style="color: #cd0000">} = #{</span><span style="color: #cccccc">user</span><span style="color: #3399cc">.</span><span style="color: #cccccc">send(method_name)</span><span style="color: #cd0000">}&quot;</span> <span style="color: #cdcd00">if</span> <span style="color: #cccccc">method_name</span> <span style="color: #3399cc">=~</span> <span style="color: #cd0000">/_name$/</span>
<span style="color: #cdcd00">end</span>

<span style="color: #75715e"># -- output --</span>
<span style="color: #75715e"># first_name = Ranjithkumar</span>
<span style="color: #75715e"># last_name = Ravi</span>
<span style="color: #75715e"># full_name = Ranjithkumar Ravi</span>
</pre></div>

**3) Dynamic Method**<br/>
Ruby allows us to dynamically create methods at runtime
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">Bar</span>
  <span style="color: #cdcd00">def</span> <span style="color: #00cdcd">self</span><span style="color: #3399cc">.</span><span style="color: #cccccc">create_method(</span><span style="color: #cd00cd">method</span><span style="color: #cccccc">)</span>
    <span style="color: #cccccc">define_method</span> <span style="color: #cd0000">&quot;my_#{</span><span style="color: #cd00cd">method</span><span style="color: #cd0000">}&quot;</span> <span style="color: #cdcd00">do</span>
      <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;Dynamic method called &#39;my_#{</span><span style="color: #cd00cd">method</span><span style="color: #cd0000">}&#39;&quot;</span>
    <span style="color: #cdcd00">end</span>
  <span style="color: #cdcd00">end</span>

  <span style="color: #75715e"># these methods are executed within the definition of the Bar class</span>
  <span style="color: #cccccc">create_method</span> <span style="color: #cd0000">:foo</span>
  <span style="color: #cccccc">create_method</span> <span style="color: #cd0000">:bar</span>
<span style="color: #cdcd00">end</span>

<span style="color: #75715e"># Test out our dynamic methods</span>
<span style="color: #cccccc">Bar</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">respond_to?</span> <span style="color: #cd0000">:my_foo</span> <span style="color: #75715e"># =&gt; true</span>
<span style="color: #cccccc">Bar</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">my_foo</span> <span style="color: #75715e"># =&gt; &quot;Dynamic method called &#39;my_foo&#39;&quot;</span>
<span style="color: #cccccc">Bar</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">my_bar</span> <span style="color: #75715e"># =&gt; &quot;Dynamic method called &#39;my_bar&#39;&quot;</span>
</pre></div>

**4) Ghost Method**<br/>
When a method is not found, Ruby will send this method as a symbol to method_missing.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">Example</span>
  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">method_missing(method_name,</span> <span style="color: #3399cc">*</span><span style="color: #cccccc">args)</span>
    <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;You called: #{</span><span style="color: #cccccc">method_name</span><span style="color: #cd0000">}(#{</span><span style="color: #cccccc">args</span><span style="color: #3399cc">.</span><span style="color: #cccccc">join(</span><span style="color: #cd0000">&#39;, &#39;</span><span style="color: #cccccc">)</span><span style="color: #cd0000">})&quot;</span>
    <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;You also passed a block&quot;</span> <span style="color: #cdcd00">if</span> <span style="color: #cd00cd">block_given?</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">Example</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">this_is_cool(</span><span style="color: #cd00cd">1</span><span style="color: #cccccc">,</span> <span style="color: #cd00cd">2</span><span style="color: #cccccc">,</span> <span style="color: #cd00cd">3</span><span style="color: #cccccc">)</span> <span style="color: #75715e"># =&gt; You called: this_is_cool(1, 2, 3)   </span>
<span style="color: #cccccc">Example</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">this_is_cool(</span><span style="color: #cd0000">:a</span><span style="color: #cccccc">,</span> <span style="color: #cd0000">:b</span><span style="color: #cccccc">,</span> <span style="color: #cd0000">:c</span><span style="color: #cccccc">)</span> <span style="color: #cccccc">{</span> <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;a block&quot;</span> <span style="color: #cccccc">}</span> <span style="color: #75715e"># =&gt; You called: this_is_cool(a, b, c)</span>
                                                        <span style="color: #75715e"># =&gt; You also passed a block </span>
</pre></div>

**5) Dynamic Proxy**<br/>
Wrapping an object or service and then forwarding method calls to the wrapped item is known as dynamic proxying.
In other word, Catching `Ghost Method` and forwarding them onto another method/service.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">def</span> <span style="color: #cccccc">method_missing(method_name,</span> <span style="color: #3399cc">*</span><span style="color: #cccccc">args,</span> <span style="color: #3399cc">&amp;</span><span style="color: #cccccc">block)</span>
  <span style="color: #cdcd00">return</span> <span style="color: #cccccc">get(</span><span style="color: #00cdcd">$1</span><span style="color: #3399cc">.</span><span style="color: #cccccc">to_sym,</span> <span style="color: #3399cc">*</span><span style="color: #cccccc">args,</span> <span style="color: #3399cc">&amp;</span><span style="color: #cccccc">block)</span> <span style="color: #cdcd00">if</span> <span style="color: #cccccc">method_name</span><span style="color: #3399cc">.</span><span style="color: #cccccc">to_s</span> <span style="color: #3399cc">=~</span> <span style="color: #cd0000">/^get_(.*)/</span>
  <span style="color: #cdcd00">super</span> <span style="color: #75715e"># if we don&#39;t find a match then we&#39;ll call the top level `BasicObject#method_missing`</span>
<span style="color: #cdcd00">end</span>
</pre></div>
If we find a match for get_#{name} then we will delegate to another method such as get(:data_type) where :data_type is :name or :age(e.g. get_name, get_age etc) else send to super and raise error.

**6) Blank Slate**<br/>
Ruby allows us to remove functionality from a class. This technique can be useful to ensure that your class doesn’t expose unwanted or unexpected features.<br/>
e.g. Prevents issues when using "Dynamic Proxy". User calls a method that exists higher up the inheritance chain so your `method_missing` doesn't fire because the method does exist. To work around this issue, make sure your class starts with a "Blank Slate".<br/>
To remove method, use `Module#undef_method` (removes all the methods), or `Module#remove_method` (remove receiver's method, keep inherited methods). Ghost methods are slower than normal methods. Do not remove methods start with __, method_missing or respond_to?, and leave some other methods.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #75715e"># create a blank slate class</span>
<span style="color: #cdcd00">class</span> <span style="color: #00cdcd">ImBlank</span>
  <span style="color: #cd00cd">public_instance_methods</span><span style="color: #3399cc">.</span><span style="color: #cccccc">each</span> <span style="color: #cdcd00">do</span> <span style="color: #3399cc">|</span><span style="color: #cccccc">method_name</span><span style="color: #3399cc">|</span>
    <span style="color: #cccccc">undef_method(method_name)</span> <span style="color: #cdcd00">unless</span> <span style="color: #cccccc">method_name</span> <span style="color: #3399cc">=~</span> <span style="color: #cd0000">/^__|^(public_methods|method_missing|respond_to\?)$/</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #75715e"># see what methods are now available</span>
<span style="color: #cccccc">ImBlank</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">public_methods</span> <span style="color: #75715e"># =&gt; [&quot;public_methods&quot;, &quot;__send__&quot;, &quot;respond_to?&quot;, &quot;__id__&quot;]</span>
</pre></div>

**7) Kernel Method**<br/>
Defining methods in the Kernel module will make those methods available to all objects.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">module</span> <span style="color: #cccccc">Kernel</span>
  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">say_hello</span>
    <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;hello from #{</span><span style="color: #cd00cd">self</span><span style="color: #3399cc">.</span><span style="color: #cccccc">class</span><span style="color: #3399cc">.</span><span style="color: #cccccc">name</span><span style="color: #cd0000">}&quot;</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">Class</span><span style="color: #3399cc">.</span><span style="color: #cccccc">say_hello</span> <span style="color: #75715e"># =&gt; hello from Class</span>
<span style="color: #cccccc">Object</span><span style="color: #3399cc">.</span><span style="color: #cccccc">say_hello</span> <span style="color: #75715e"># =&gt; hello from Class</span>
<span style="color: #cccccc">Object</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">say_hello</span> <span style="color: #75715e"># =&gt; hello from Object</span>
<span style="color: #cd00cd">1</span><span style="color: #3399cc">.</span><span style="color: #cccccc">say_hello</span> <span style="color: #75715e"># =&gt; hello from Fixnum</span>
<span style="color: #cd0000">&quot;&quot;</span><span style="color: #3399cc">.</span><span style="color: #cccccc">say_hello</span> <span style="color: #75715e"># =&gt; hello from String</span>
</pre></div>

**8) Scope**<br/>
Class.new is an alternative to class<br/>
**Scope Gate:**<br/>
There are 3 ways to define a new scope in Ruby:

* starting new class definition, `class`
* starting new module definition, `module`
* start new method, `def`

Global variable can access any scope. Be aware that scoping in Ruby is different than some other languages. Ruby does not chain scopes when performing lookups, so don’t expect it to find variables defined in an outer scope.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cccccc">scope</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;main scope&quot;</span>
<span style="color: #cd00cd">puts</span><span style="color: #cccccc">(scope)</span> <span style="color: #75715e"># =&gt; main scope</span>

<span style="color: #cdcd00">class</span> <span style="color: #00cdcd">ExampleClass</span>
  <span style="color: #75715e"># the main scoped variable isn&#39;t defined in the classes&#39; scope</span>
  <span style="color: #cccccc">defined?(scope)</span> <span style="color: #75715e"># =&gt; nil</span>
  <span style="color: #cccccc">scope</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;class scope&quot;</span>
  <span style="color: #cd00cd">puts</span><span style="color: #cccccc">(scope)</span> <span style="color: #75715e"># =&gt; class scope</span>
<span style="color: #cdcd00">end</span>
</pre></div>

**Flattening the scope:**<br/>
Where you change the code in such a way that it's easier for you to pass variables through "Scope Gates".
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cccccc">my_var</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;abc&quot;</span>
<span style="color: #cccccc">MyClass</span> <span style="color: #3399cc">=</span> <span style="color: #cccccc">Class</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span> <span style="color: #cdcd00">do</span>
  <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;#{</span><span style="color: #cccccc">my_var</span><span style="color: #cd0000">} in class&quot;</span>

  <span style="color: #cccccc">define_method</span> <span style="color: #cd0000">:my_method</span> <span style="color: #cdcd00">do</span>
    <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;#{</span><span style="color: #cccccc">my_var</span><span style="color: #cd0000">} in method&quot;</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span> <span style="color: #75715e"># =&gt; abc in class</span>

<span style="color: #cccccc">MyClass</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">my_method</span> <span style="color: #75715e"># =&gt; abc in method</span>
</pre></div>

**9) Eigenclass**<br/>
A hidden class on the ancestors chain. Eigenclass is a singleton class object. It stores the singleton method of an object.<br/>
**Class Extension:**
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">MyClass</span>
  <span style="color: #cdcd00">class</span> <span style="color: #3399cc">&lt;&lt;</span> <span style="color: #cd00cd">self</span>
    <span style="color: #cdcd00">def</span> <span style="color: #cccccc">my_method;</span> <span style="color: #cd0000">&#39;hello&#39;</span><span style="color: #cccccc">;</span> <span style="color: #cdcd00">end</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">MyClass</span><span style="color: #3399cc">.</span><span style="color: #cccccc">my_method</span> <span style="color: #75715e"># =&gt; &quot;hello&quot;</span>
</pre></div>
**Object Extension:**
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">module</span> <span style="color: #cccccc">MyModule</span>
  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">my_method;</span> <span style="color: #cd0000">&#39;hello&#39;</span><span style="color: #cccccc">;</span> <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">obj</span> <span style="color: #3399cc">=</span> <span style="color: #cccccc">Object</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span>
<span style="color: #cdcd00">class</span> <span style="color: #3399cc">&lt;&lt;</span> <span style="color: #cccccc">obj</span> <span style="color: #75715e"># extends obj</span>
  <span style="color: #cdcd00">include</span> <span style="color: #cccccc">MyModule</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">obj</span><span style="color: #3399cc">.</span><span style="color: #cccccc">my_method</span> <span style="color: #75715e"># =&gt; &quot;hello&quot;</span>
<span style="color: #cccccc">obj</span><span style="color: #3399cc">.</span><span style="color: #cccccc">singleton_methods</span> <span style="color: #75715e"># =&gt; [:my_method]</span>

<span style="color: #75715e"># Another way to extend object</span>

<span style="color: #cccccc">obj</span> <span style="color: #3399cc">=</span> <span style="color: #cccccc">Object</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span>
<span style="color: #cccccc">obj</span><span style="color: #3399cc">.</span><span style="color: #cccccc">extend</span> <span style="color: #cccccc">MyModule</span>
<span style="color: #cccccc">obj</span><span style="color: #3399cc">.</span><span style="color: #cccccc">my_method</span> <span style="color: #75715e"># =&gt; &quot;hello&quot;</span>
</pre></div>

**10) Context Probe**<br/>
Execute a code block in the context of another object using `instance_eval`
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">Foo</span>
  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">initialize</span>
    <span style="color: #00cdcd">@z</span> <span style="color: #3399cc">=</span> <span style="color: #cd00cd">1</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>
<span style="color: #cccccc">foo</span> <span style="color: #3399cc">=</span> <span style="color: #cccccc">Foo</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span>
<span style="color: #cccccc">foo</span><span style="color: #3399cc">.</span><span style="color: #cccccc">instance_eval</span> <span style="color: #cdcd00">do</span>
  <span style="color: #00cdcd">@z</span> <span style="color: #3399cc">=</span> <span style="color: #cd00cd">2</span>
  <span style="color: #cd00cd">puts</span> <span style="color: #00cdcd">@z</span> <span style="color: #75715e"># =&gt; 2</span>
<span style="color: #cdcd00">end</span>

<span style="color: #75715e"># There is also `instance_exec` which works the same way but allows passing arguments to the block</span>
<span style="color: #cccccc">Foo</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">instance_exec(</span><span style="color: #cd00cd">3</span><span style="color: #cccccc">)</span> <span style="color: #cccccc">{</span> <span style="color: #3399cc">|</span><span style="color: #cccccc">arg</span><span style="color: #3399cc">|</span> <span style="color: #00cdcd">@z</span> <span style="color: #3399cc">*</span> <span style="color: #cccccc">arg</span> <span style="color: #cccccc">}</span> <span style="color: #75715e"># =&gt; 3</span>
</pre></div>

**11) Clean Room**<br/>
Clean rooms are used to change the current context to something expected or clean (does not affect to current environment).
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">def</span> <span style="color: #cccccc">do_stuff</span>
  <span style="color: #00cdcd">@scope</span>
<span style="color: #cdcd00">end</span>

<span style="color: #00cdcd">@scope</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;outer scope&quot;</span>
<span style="color: #cd00cd">puts</span> <span style="color: #cccccc">do_stuff</span> <span style="color: #75715e"># =&gt; outer scope</span>

<span style="color: #cccccc">Object</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">instance_eval</span> <span style="color: #cdcd00">do</span>
  <span style="color: #00cdcd">@scope</span> <span style="color: #3399cc">=</span> <span style="color: #cd0000">&quot;clean room scope&quot;</span>
  <span style="color: #cd00cd">puts</span> <span style="color: #cccccc">do_stuff</span> <span style="color: #75715e"># =&gt; clean room scope</span>
<span style="color: #cdcd00">end</span>
</pre></div>

**12) class_eval**<br/>
Evaluate a block in the context of a class. Similar to re-opening a class but more flexible in that it works on any variable that references a class, where as re-opening a class requires defining a constant.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">def</span> <span style="color: #cccccc">add_method_to(a_class)</span>
  <span style="color: #cccccc">a_class</span><span style="color: #3399cc">.</span><span style="color: #cccccc">class_eval</span> <span style="color: #cdcd00">do</span>
    <span style="color: #cdcd00">def</span> <span style="color: #cccccc">m;</span> <span style="color: #cd0000">&#39;Hello!&#39;</span><span style="color: #cccccc">;</span> <span style="color: #cdcd00">end</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">add_method_to</span> <span style="color: #cd00cd">String</span>
<span style="color: #cd0000">&#39;abc&#39;</span><span style="color: #3399cc">.</span><span style="color: #cccccc">m</span> <span style="color: #75715e"># =&gt; &quot;Hello!&quot;</span>
</pre></div>

**13) Class Macro**<br/>
Class Macros are just regular class methods that are only used in a class definition.<br/>
e.g. `attr_accessor`, `attr_reader`. These are class macros.

<br/>Write your own class macro. Here is an example of deprecate old methods, print warning message when being called.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">Book</span>
  <span style="color: #cdcd00">def</span> <span style="color: #00cdcd">self</span><span style="color: #3399cc">.</span><span style="color: #cccccc">title;</span> <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;I&#39;m an A&quot;</span> <span style="color: #cdcd00">end</span>

  <span style="color: #cdcd00">def</span> <span style="color: #00cdcd">self</span><span style="color: #3399cc">.</span><span style="color: #cccccc">deprecate(old_method,</span> <span style="color: #cccccc">new_method)</span>
    <span style="color: #cd00cd">warn</span> <span style="color: #cd0000">&quot;Warning: #{</span><span style="color: #cccccc">old_method</span><span style="color: #cd0000">}() is deprecated. Use #{</span><span style="color: #cccccc">new_method</span><span style="color: #cd0000">}()&quot;</span>
    <span style="color: #cd00cd">send</span><span style="color: #cccccc">(new_method)</span>
  <span style="color: #cdcd00">end</span>

  <span style="color: #cccccc">deprecate</span> <span style="color: #cd0000">:GetTitle</span><span style="color: #cccccc">,</span> <span style="color: #cd0000">:title</span>
<span style="color: #cdcd00">end</span>
</pre></div>

**14) Around Alias**<br/>
Around Alias uses the `alias` keyword to store a copy of the original method under a new name, allowing you to redefine the original method name and to delegate off to the previous method implementation.
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">String</span>
  <span style="color: #cdcd00">alias</span> <span style="color: #cd0000">:orig_length</span> <span style="color: #cd0000">:length</span> <span style="color: #75715e"># make alias of old method</span>

  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">length</span> <span style="color: #75715e"># define new method, override</span>
    <span style="color: #cd0000">&quot;Length of string &#39;#{</span><span style="color: #cd00cd">self</span><span style="color: #cd0000">}&#39; is: #{</span><span style="color: #cccccc">orig_length</span><span style="color: #cd0000">}&quot;</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cd0000">&quot;abc&quot;</span><span style="color: #3399cc">.</span><span style="color: #cccccc">length</span> <span style="color: #75715e">#=&gt; &quot;Length of string &#39;abc&#39; is: 3&quot;</span>
</pre></div>

**15) Hook Methods**<br/>
The method being called when event triggered, like `Module#included`, `Class#inherited`
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">class</span> <span style="color: #00cdcd">String</span>
  <span style="color: #cdcd00">def</span> <span style="color: #00cdcd">self</span><span style="color: #3399cc">.</span><span style="color: #cccccc">inherited(subclass)</span>
    <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;#{</span><span style="color: #cd00cd">self</span><span style="color: #cd0000">} was inherited by #{</span><span style="color: #cccccc">subclass</span><span style="color: #cd0000">}&quot;</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">class</span> <span style="color: #00cdcd">MyString</span> <span style="color: #3399cc">&lt;</span> <span style="color: #cd00cd">String</span><span style="color: #cccccc">;</span> <span style="color: #cdcd00">end</span> <span style="color: #75715e"># =&gt; String was inherited by MyString</span>
</pre></div>

**Method-related hooks:**

* method_missing
* method_added
* method_removed
* method_undefined
* singleton_method_added
* singleton_method_removed
* singleton_method_undefined

**Class & Module hooks:**

* inherited
* included
* extended
* extend_object
* const_missing
* append_features
* initialize_copy

**Marshalling hooks:**

* marshal_dump
* marshal_load

**16) Class Extension Mixin**<br/>
Class Extension Mixin allows you to both `include` and `extend` a class
<!-- HTML generated using hilite.me -->
<div style="background: #000000; overflow:auto;width:auto;border:solid gray;border-width:.1em .1em .1em .8em;font-size: 0.8em;">
<pre style="margin: 0; line-height: 125%; background: #000000; border: 0;"><span style="color: #cdcd00">module</span> <span style="color: #cccccc">MyMixin</span>
  <span style="color: #cdcd00">def</span> <span style="color: #00cdcd">self</span><span style="color: #3399cc">.</span><span style="color: #cccccc">included(base)</span> <span style="color: #75715e"># Hook Method</span>
    <span style="color: #cccccc">base</span><span style="color: #3399cc">.</span><span style="color: #cccccc">extend</span> <span style="color: #cccccc">ClassMethods</span>
  <span style="color: #cdcd00">end</span>

  <span style="color: #75715e"># Class Methods</span>
  <span style="color: #cdcd00">module</span> <span style="color: #cccccc">ClassMethods</span>
    <span style="color: #cdcd00">def</span> <span style="color: #cccccc">x</span>
      <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;I&#39;m X (a class method)&quot;</span>
    <span style="color: #cdcd00">end</span>
  <span style="color: #cdcd00">end</span>

  <span style="color: #75715e"># Instance Methods</span>
  <span style="color: #cdcd00">def</span> <span style="color: #cccccc">a</span>
    <span style="color: #cd00cd">puts</span> <span style="color: #cd0000">&quot;I&#39;m A (an instance method)&quot;</span>
  <span style="color: #cdcd00">end</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cdcd00">class</span> <span style="color: #00cdcd">Foo</span>
  <span style="color: #cdcd00">include</span> <span style="color: #cccccc">MyMixin</span>
<span style="color: #cdcd00">end</span>

<span style="color: #cccccc">Foo</span><span style="color: #3399cc">.</span><span style="color: #cccccc">x</span> <span style="color: #75715e"># =&gt; I&#39;m X (a class method)</span>
<span style="color: #cccccc">Foo</span><span style="color: #3399cc">.</span><span style="color: #cccccc">new</span><span style="color: #3399cc">.</span><span style="color: #cccccc">a</span> <span style="color: #75715e"># =&gt; I&#39;m A (an instance method)</span>
</pre></div>














