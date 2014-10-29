---
layout: post
title: "TIL Konstantin Haase Rebuilt Sinatra... in 6 lines!"
date: 2014-10-27 20:44:47 -0400
comments: true
categories: Flatiron School, TIL
---
That's right - 6 Lines. 

Sinatra, a stripped-down alternative to Ruby frameworks like Rails, is known for being lightweight and flexible. It's not a framework and doesn't require using the Model-View-Controller architecture pattern. Even still, a simple DSL like Sinatra has a lot going on behind the scenes. Sinatra is just under 2,000 lines of code (LOC). Rails, by comparison, is ??,???. 

Konstantin Haase managed to create a Sinatra clone called [Almost Sinatra](https://github.com/rkh/almost-sinatra) using just 6 lines of code. Here's it is:

```ruby
    %w.rack tilt date INT TERM..map{|l|trap(l){$r.stop}rescue require l};$u=Date;$z=($u.new.year + 145).abs;puts "== Almost Sinatra/No Version has taken the stage on #$z for development with backup from Webrick"
    $n=Module.new{extend Rack;a,D,S,q=Rack::Builder.new,Object.method(:define_method),/@@ *([^\n]+)\n(((?!@@)[^\n]*\n)*)/m
    %w[get post put delete].map{|m|D.(m){|u,&b|a.map(u){run->(e){[200,{"Content-Type"=>"text/html"},[a.instance_eval(&b)]]}}}}
    Tilt.mappings.map{|k,v|D.(k){|n,*o|$t||=(h=$u._jisx0301("hash, please");File.read(caller[0][/^[^:]+/]).scan(S){|a,b|h[a]=b};h);v[0].new(*o){n=="#{n}"?n:$t[n.to_s]}.render(a,o[0].try(:[],:locals)||{})}}
    %w[set enable disable configure helpers use register].map{|m|D.(m){|*_,&b|b.try :[]}};END{Rack::Handler.get("webrick").run(a,Port:$z){|s|$r=s}}
    %w[params session].map{|m|D.(m){q.send m}};a.use Rack::Session::Cookie;a.use Rack::Lock;D.(:before){|&b|a.use Rack::Config,&b};before{|e|q=Rack::Request.new e;q.params.dup.map{|k,v|params[k.to_sym]=v}}}
```

![Mind Blown](https://p.gr-assets.com/540x540/fit/hostedimages/1383924783/6705751.gif)

This is obviously just a fun exercize. As he explains in [this video on obfuscation](http://vimeo.com/61087285), this was just "an interesting project I did on the weekend." [#NoBigDeal](http://oi39.tinypic.com/14b6nmu.jpg)

Give the video a look and check him out of twitter [@konstantinhaase](https://twitter.com/konstantinhaase). Not only is the content interesting, he's a really funny guy. 