---
layout: post
title: "Making A Profile Template With Box Selections"
date: 2014-11-21 09:59:51 -0700
categories: front-end making-forms code
---

{% highlight erb %}
<%= form_for @mentor, :html => {multipart: true, class: "form-horizontal"} do |f| %>
  <div>
    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.text_field :name, :class => "form-control", :placeholder => "Name" %>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.text_field :email, :class => "form-control", :placeholder => "email@example.com" %>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.text_field :company, :class => "form-control", :placeholder => "employer" %>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.text_area :bio, :class => "form-control", :placeholder => "Write a short bio" %>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.label "Profile Photo (optional)", class: "form-label" %>
        <%= f.file_field :photo, :class => 'form-control'%>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.label "Best At (select all that apply) ", class: "form-label" %>
        &nbsp&nbsp
        <%= f.collection_check_boxes(:skills, Skill.all, :id, :name) do |b| %>
        <%= b.check_box %>
        <%= b.label(class: "check-box")  %>
        <% end %>
      </div>
    </div>

    <div class="form-group">
      <div class="col-md-6 col-md-offset-3">
        <%= f.submit "Submit", :class => 'btn btn-lg btn-primary' %>
      </div>
    </div>
  </div>
<% end %>
{% endhighlight %}

## The code above will produce the view below: ##

![view]({{ site.url }}/assets/images/form_selectors.png)
