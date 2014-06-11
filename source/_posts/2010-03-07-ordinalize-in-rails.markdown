---
author: ranjithru
comments: true
date: 2010-03-07 10:18:07+00:00
layout: post
slug: ordinalize-in-rails
title: '"ordinalize" in Rails'
wordpress_id: 17
keywords: ordinalize,display a date with suffix
description: "How to display a date with suffix like 'th', 'st', 'nd', or 'rd'"
categories:
- Rails
tags:
- rails
---

How to display a date with suffix like "th", "st", "nd", or "rd"?

for example, I wanna display like this Mon, 7th April

Rails has inbuilt function - "ordinalize"<!--more-->

It turns a number into an ordinal string used to denote the position in an ordered sequence such as 1st, 2nd, 3rd, 4th


    
    date.strftime("%a, #{date.day.ordinalize} %B")
