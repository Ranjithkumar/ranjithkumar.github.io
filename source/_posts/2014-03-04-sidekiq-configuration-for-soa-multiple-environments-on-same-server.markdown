---
author: ranjithru
comments: true
date: 2014-03-04 06:07:56+00:00
layout: post
slug: sidekiq-configuration-for-soa-multiple-environments-on-same-server
title: Sidekiq configuration for SOA / multiple environments on same server
wordpress_id: 114
keywords: sidekiq configuration,multitenant,SOA,multiple environment,ruby,redis
description: Sidekiq configuration for SOA / multiple environments on same server
categories:
- Rails
- Ruby
tags:
- multitenant
- rails
- ruby
- sidekiq
- SOA
---

The Sidekiq configuration file by default located at _config/sidekiq.yml_. It is only necessary to create the file if you need to set advanced options, such as concurrency pool size, named queues, PID file location, etc.
<!--more-->Here is an example configuration file:



    :concurrency: 5
    :pidfile: tmp/pids/sidekiq.pid
    staging:
     :concurrency: 10
    production:
     :concurrency: 50
    :queues:
     - default


By default, one Sidekiq process will be started on each app server.

**Setting the Location of your Redis server**

By default, Redis is located at _localhost:6379_.

Following is my development environment,
_SOA + Ruby(2.0) + Rails(4.0) + Unicorn  + Nginx  + SideKiq + MultiTenant_

In your _config/initializers/sidekiq.rb_ file,


    Sidekiq.configure_server do |config|
      config.redis = { url: 'redis://localhost:6379/0', namespace: "sidekiq_app_name_#{Rails.env}" }
    end

    Sidekiq.configure_client do |config|
      config.redis = { url: 'redis://localhost:6379/0', namespace: "sidekiq_app_name_#{Rails.env}" }
    end


Usage:
_The :namespace parameter is recommended if Sidekiq is sharing access to a Redis database._

Finally, start sidekiq from the root directory of your Rails app.


<blockquote>bundle exec sidekiq -e staging -C config/sidekiq.yml</blockquote>
