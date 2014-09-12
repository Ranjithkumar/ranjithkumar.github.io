---
author: ranjithru
comments: true
date: 2011-07-21 10:31:47+00:00
excerpt: "I am using rails 3 and ruby 1.9.2 in my application. \nWhile importing/parsing\
  \ the CSV, I get an error \"Incompatible character \nencodings: ASCII-8bit and UTF-8\"\
  . I quickly checked my database encoding, \nit was UTF-8 only and also in application.rb,\
  \ I had 'config.encoding = \"utf-8\"'. \nI had no idea what was going wrong..."
layout: post
slug: incompatible-character-encodings-error-in-ruby-1-9
title: Incompatible character encodings error in ruby 1.9
wordpress_id: 65
keywords: incompatible character encoding, ruby encoding, ASCII-8bit, ruby 1.9
description: "While importing/parsing the CSV, Incompatible character encodings: ASCII-8bit and UTF-8 in ruby 1.9"
categories:
- ruby
tags:
- encoding
- ruby
- UTF-8
---

**Problem:** 
_Incompatible character encodings error while importing csv files in ruby 1.9 which have data in multiple languages._

I am using rails 3 and ruby 1.9.2 in my application.
While importing/parsing the CSV, I get an error "Incompatible character encodings: ASCII-8bit and UTF-8". I quickly checked my database encoding, it was UTF-8 only and also in application.rb, I had<!--more-->
    
    'config.encoding = "utf-8"'.


I had no idea what was going wrong...

After googling a bit, I found that couple of posts mentioned some workarounds for this issue, so I tried:

    
    
    # encoding: utf-8 => in my class
    and
    "hello ümlaut".force_encoding("UTF-8")
    


That output was 
    
    "hello ?mlat" 



With this the Error was fixed (no rails error) but the converted string value is incorrect. It was working correctly in some places but not everywhere.

I searched a bit more and then I found that the sequence of bytes that represent an “ü” is different in different encodings and could not be recognized in UTF-8, so such characters were replaced with a “?”.

**Solution:**
  We have to find out that the original encoding of the string and then convert to UTF-8. To achieve this in ruby 1.9.2, we can't do it directly. 
  so, we need to install the gem 'rchardet19'
  and then add this to the top of your class, require 'iconv'

  now,

    
    
      data = CharDet.detect(value)
      puts "Detected encoding- #{data.encoding}"
    


  and,

    
    
      value = (data.confidence > 0.6 ? Iconv.iconv("UTF-8", data.encoding, value)
               : value)
    


  we are just converting to UTF-8 from the detected encoding.

  This fixes the issue.
