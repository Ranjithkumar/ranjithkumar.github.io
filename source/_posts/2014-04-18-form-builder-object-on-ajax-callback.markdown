---
author: ranjithru
comments: true
date: 2014-04-18 10:53:06+00:00
layout: post
slug: form-builder-object-on-ajax-callback
title: Form builder object on AJAX callback
wordpress_id: 128
categories:
- Rails
tags:
- ajax
- nested_form
- rails
---

**Problem:**

My application has a select box for users to choose a "mapping" for the upload. Based on mapping, user should see the default options selected in that form. When user changes the mapping, an AJAX request gets called and renders a js.erb file. The rendered js should render a partial that has fields_for a nested model. My challenge is, How to pass the form build object to the partial on AJAX callback?

_upload_page.html.erb:_

    
    <%= form_for :upload do |f| %>
      Some divs....
      
      <div>
        <label>Mapping: </label>
        <%= f.select :mapping_id, options_from_collection_for_select(@mappings, "id"
        , "name"), {}, { class: "default_mapping_change" } %>
      </div>
    
      <div id="mapping_option">
            <%= render "mapping_option_form", f: f, default_mapping: @mapping %>
      </div>
    <% end %>
    


On my ajax callback, I replace "mapping_option" div with the update object.

**Solution:**

Create a new form in the js.erb file and passing that one to the partial.

_default_mapping_change.js.erb:_

    
    '<%= form_for :upload do |f| %>'
        $("#mapping_option").html("<%= j(render "mapping_option_form", f: f,
        default_mapping: @mapping) %>");
    '<% end %>'
    


**The single quotes around the form tag are important, or else there will be some javascript escaping issue.**
