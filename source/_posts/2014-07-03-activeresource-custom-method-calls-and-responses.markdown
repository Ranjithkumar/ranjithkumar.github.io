---
author: ranjithru
layout: post
title: "ActiveResource Custom Method Calls &amp; Responses"
date: 2014-07-03 10:52:35 +0530
comments: true
categories: [rails, active-resource]
tags: [activeresource, rubyonrails, REST, ruby]
keywords: active resource custom method calls, custom method call, rubyonrails, rest
description: ActiveResource Custom Method Calls and Responses

---

Active Resource supports defining our own custom REST methods. A custom method call is one that is not one of the default CRUD actions that you get out of the box with RESTful routing.

To invoke them, Active Resource provides the get, post, put and delete methods where you can specify a custom REST method name to invoke.
<!--more-->

**Here one thing to note is that, all custom method calls return the remote service response except 'get'. 'get' returns a hash (or array of hashes).**

    # GET all managers(collection custom method), i.e. GET /people/managers.json
    Person.get(:managers)
    #=> [{:name => "Rans"}, {:name => "Gokul"}]

**If you want to get actual objects from a get call, you can use the find method.**

    # GET all managers(collection custom method), i.e. GET /people/managers.json
    Person.find(:all, from: :managers)
    #=> <#Person...><#Person ...>

Options :from - Sets the path or custom method that resources will be fetched from.
