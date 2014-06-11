---
author: ranjithru
comments: true
date: 2010-02-07 08:47:54+00:00
layout: post
slug: thinking-sphinx-with-multiple-instance
title: Thinking sphinx with multiple instance
wordpress_id: 1
keywords: thinking sphinx,multiple instance
description: How to run multiple thinking sphinx instance in the single app
categories:
- Rails
tags:
- rails
- thinking_sphinx
---

**Problem:** How to run multiple thinking_sphinx instance in the single app itself?

**Solution:** this can usually be done by adding some settings to a file named sphinx.yml in your config directory.<!--more-->

**Example:**


    
    
    
    development:
        port: 3312
    
    test:
        port: 3313
    
    production:
        port: 3315
    
    


**References**: http://freelancing-god.github.com/ts/en/advanced_config.html


