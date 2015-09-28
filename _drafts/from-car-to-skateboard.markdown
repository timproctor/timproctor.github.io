---
layout: post
title: "from car to skateboard"
date: 2014-10-17 12:36:49 -0600
comments: true
categories: ruby, junior programmer, projects
---

# Reflecting on failure and how to turn it into progress.

This blog reflects on how I reacted to project explosions, and ultimately made
strides on the path to becoming and expert programmer.

### Sort of related...

Sean Griffin is a Rails contributor and Senior Developer at Thoughtbot. He is
also a mentor at Turing School of Software and Design. Today, Sean was our Friday
guest speaker. He was asked basically what steps he took to become an expert programmer.
His response was that he "broke a lot of things" and he was curious why code worked
how it worked. When asked what students should spend more time doing, his answer
was, "more testing".

### Individual Project Requirements

We were tasked with choosing from a list of datasets (like: Data.gov, Sunlight Foundation,
ProPublica, NASA, OpenColorado, etc...) to build an app that made this data relevant
to the consumer.

Here is a list of what my "customer" said I must deliver:

  - to use an external OAuth provider to authenticate consumers
  - to filter data through the web and API, based on authorization rules
  - to deploy our app through a droplet, provided by Digital Ocean (thanks DO!)
  - to make use of background workers
  - to send notifications to users over email, SMS, Twitter, etc...

Here is a list of what my "customer" said they would like:

  - to automate deployment with a single command
  - to allow uploads of images
  - to manipulate those images to suit the layout/style of the app itself
  - to receive content from consumers via email

### Know your dataset - no, really... look at the data.

I chose my dataset on Monday (of the three week project), which was from OpenColorado.
My data was in the form of a CSV file. At first glance I thought it contained
columns of latitude and longitude coordinates for parking lots in Denver. On Saturday, while
trying to parse the CSV data into objects that I could save to my database,
I discovered that there were no adresses nor coordinates for the parking lots.

### Apples into Oranges

I quickly researched other datasets from OpenColorado. I found a CSV dataset
that had the addresses for 18 electrical vehicle charging stations in and around
the Denver area. My ParkIt app pivoted, becoming my ChargeIt app.

### Re-prioritizing customer expectations

At this point I knew that I would not be able to deliver what the customer "must"
have by the target date. So, instead of crying about it, which I did consider as
an option, I thought about how to downsize what I could build in the given time.

### Iteration backwards from car to motorcycle to bicycle to skateboard

I realized that I would not be able to deliver a car (metaphorically speaking), but
maybe I could deliver a skateboard. It wouldn't get you from point A to B as fast,
but it would work. I have learned that it's better to deliver a product with features
that work than a product with lots of features that almost work.

### Time to show off some models, views, and controllers

I used the geocoder gem to turn my adresses into latitude and longitude coordinates that could
then be turned into map points via the Google API. The contollers stay pretty light
with this gem and I recommend it.

Here is an example of the model using the geocoder library:

```ruby

class ChargingStation < ActiveRecord::Base
  geocoded_by :full_street_address
  after_validation :geocode

  def full_street_address
    "#{address_line1},#{city}, #{state} #{zip}"
  end
end

```
And the controller stayed light:

```ruby

class ChargingStationsController < ApplicationController
  respond_to :json

  def index
    @address = params[:q]
    stations = ChargingStation.near(@address, 5)
    @charging_stations = ChargingStationDecorator.decorate_collection(stations)
  end

  def create
    @charging_station = ChargingStation.create(charging_params)
  end

  private

  def charging_params
    params.require(:charging_station).permit(:facility_name, :address_line1,
                                             :city, :state, :zip)
  end
end

```

### Keep your javascript out of your html.erb views

The app asked consumers to enter an address and then geocoder would return
charging station locations within 5 miles of that address. I wish that I had kept
the javascript out of my html.erb view partial, but I didn't.

```ruby
    <style type="text/css">
      html, body, #map-canvas { height: 500px; margin: 0; padding: 0; }
    </style>
    <script type="text/javascript"
      src="https://maps.googleapis.com/maps/api/js?key=AIzaSyCSM97LEyh3OM3GhejPbaZDWqTlGLpZ54I">
    </script>


<div id="map-canvas"></div>
<p>
  Address: <%= address %>
</p>
<script type="text/javascript">
var geocoder, map;

function geocode_n_map(address) {
    geocoder = new google.maps.Geocoder();
    geocoder.geocode({
        'address': address
    }, function(results, status) {
        if (status == google.maps.GeocoderStatus.OK) {
            var myOptions = {
                zoom: 14,
                center: results[0].geometry.location,
                mapTypeId: google.maps.MapTypeId.ROADMAP
            }
            map = new google.maps.Map(document.getElementById("map-canvas"), myOptions);

            var current_marker = new google.maps.Marker({
                icon: 'http://maps.google.com/mapfiles/ms/icons/green-dot.png',
                map: map,
                position: results[0].geometry.location,
                title: "Location you searched for"
            });

            $(".listings li").each(function(){
              var marker = new google.maps.Marker({
                  icon: 'http://maps.google.com/mapfiles/ms/icons/yellow-dot.png',
                  map: map,
                  position: new google.maps.LatLng($(this).data("lat"), $(this).data("lon")),
                  title: $(this).data("name")
              });

            })


        }else{
          alert("Couldn't Find Map");
        }
    });
}

$( document ).ready(function(){
  geocode_n_map("<%= address %>");
});
</script>

```

### OAuth through Twitter

I was able to figure out how to set-up OAuth through the omniauth-twitter gem.
I recognized that for my app, this feature was unnecessary. However,
it was a feature requested by the "customer". Here is a peak at the contoller.

```ruby

class SessionsController<ApplicationController
  def create
    auth = request.env["omniauth.auth"]
    user = User.find_by_provider_and_uid(auth["provider"], auth["uid"]) || User.create(provider:auth["provider"], uid:auth["uid"], name:auth["info"]["nickname"])
    session[:user_id] = user.id
    redirect_to root_url
  end

  def destroy
    session[:user_id] = nil
    redirect_to root_url
  end
end

```

### If you can only a deliver a skateboard, make it look as good as possible

I'll be the first to admit that I wish my front-end was better. It's a work in
progress for sure. The first window shows the root page. Consumer enter an address
and click "search":

![welcome_url](./images/welcome_index.png)

The results of the search are displayed in the example below:

![search_index](./images/search_index.png)

### Postgres hell and deploying to Digital Ocean

I spend two days trying to deploy to Digital Ocean. I learned how to read log files
from nginx and how to create roles in the Postgres terminal (psql). I also learned
how to make sure my assets made it to Digital Ocean. For anyone who has ever deployed
before, you know exactly the pain points I'm speaking of. I could detail the pain,
but that is an entire blog for another day.

### Did you notice what I'm missing?

I didn't want to mention this. I think TDD is super important but I made a typical
rookie mistake when it came to TDD. Can I just leave it at that? I think I will.
Send me an email if you really want to know about the testing. I don't want to talk
about it. Like all good skeletons - let's keep this one in the closet.

MAH
