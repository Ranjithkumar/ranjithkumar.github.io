---
author: ranjithru
comments: true
date: 2010-03-07 11:31:42+00:00
layout: post
slug: cycle-helper-in-rails
title: '"cycle" helper in Rails'
wordpress_id: 20
categories:
- Rails
tags:
- rails
---

If you wanna display the list of records with different(alternate) classes for table rows, then you can use this helper(you no need to check odd-even records). Rails has so many awesome feature like this.

for ex: if you want to apply 'odd' and 'even' class for alternate record


    
    
     -season_hash.each do |k, v|
       %tr{:class => "#{cycle('odd', 'even')}"}
         %td= v["games"]
         %td= v["goals"]
         %td= v["assists"]
         %td= v["practices"]
    
    
