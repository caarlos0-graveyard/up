---
layout: post
title: "USDA Nutrient Database Web Service"
category: web
---

## TLD;R
I built a web service that you can use to query the USDA National Nutrient
Database. You can find the docs at
[http://docs.usdanutrientservice.apiary.io/](http://docs.usdanutrientservice.apiary.io/).
It provides querying, full text searching, pagination, caching, and is also kept up to date with the latest data.

## Background
The US Department of Agriculture provides a fairly comprehensive database of
foods and their nutritional information. However, I think they don't really want
anyone to use it, as it's only available as separate US-ASCII text files or,
even worse, an MS-Access database.

Some time ago I found myself needing this
data in a web app, so I built the [USDA Nutrient
Database](https://github.com/mattbeedle/usda-nutrient-database) gem. It's very
simple, and basically just downloads and imports the text files into your
application database.

## The Web Service

Recently [Corey Martin](https://twitter.com/coreyitguy)
wrote a [blog post](http://aspiringwebdev.com/a-smarter-nutrition-label/) about
using this gem to build [smarter nutrition
labels](http://www.cooksmarts.com/weekly-meal-plan-service/nutrition/about-nutrition-label/#.U1QuYeZdWdY)
for [CookSmarts](http://www.cooksmarts.com/). In it he mentions them building
their own server just for serving nutrient information, which eventually
inspired me to build this service. It provides all of the same data, but
additionally adds full text search, pagination, caching, and soon weight
conversions. I'll be keeping the data up to date and will be providing product
support. Feature requests may be sent through UserVoice. Maybe I'll throw an object mapper gem
together in the coming months, to allow people to deal with ruby instead of
JSON. Anyway, you can find the [USDA Nutrient Service
here](http://usda-nutrient-service.mattbeedle.name)... Enjoy
