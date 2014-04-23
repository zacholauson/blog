---
layout: post
title: "Mocking Requests with Reify"
date: 2014-04-18 09:53

---
This week I worked on running my Tic Tac Toe off of my Java Server. While working on testing my request api in Clojure, I needed a way to mock a request and give it some data for the api to be tested with.
I first tried making a MockRequest object with defrecord and deftype but for these I had to implement all of the IRequest methods, which was more than I really needed. I then came across Reify which is similar to a deftype or defrecord, because they both result in classes but Reify is unique because it creates a anonymous class that is dynamically created at runtime.
The thing that made Reify perfect for my MockRequest is that it isnt too strict on defining all of the functions that need to be implemented from the interface.

My IRequest interface has 9 methods that need to be implemented but for my testing I only needed 2 of them to return data, so with Reify I can create a Request object that implements IRequest and returns the data that I specify when `getParams` and `getCookies` is called on the object.

{% codeblock lang:clojure %}
(def mock-request
  (reify main.requests.IRequest
    (getParams [this]
      (java.util.HashMap. {"move" "1"}))
    (getCookies [this]
      (java.util.HashMap. {"board" "---------"}))))

(describe "Request"
  (describe "#get-cookies"
    (it "should return a map of the cookies from the request"
      (should= {"board" "---------"} (get-cookies mock-request))))

  (describe "#get-params"
    (it "should return a map of the params from the request"
      (should= {"move" "1"} (get-params mock-request)))))
{% endcodeblock %}

I also ran into an issue where I could not use a Clojure map in place of the Java Hashmap that is needed by the interface, so I had to create a Java hashmap with a Clojure map: `(java.util.HashMap. {})`.

I am really glad I came across Reify because it really simplified my testing, especially when I started my routes, I needed a route that would build a response with all of the new gamestates cookies stored in it based on the params of the request taken in. Reify worked great in this case because all I needed from the Request was the params.

{% codeblock lang:clojure %}
(def mock-request
  (reify main.requests.IRequest
    (getParams [this]
      (java.util.HashMap. {"board-size" "3" "difficulty" "unbeatable" "gametype" "computer-vs-human"}))))

(describe "NewGameRoute"
  (describe "#build-response"
    (it "should return a response with a 301 redirect to /play with the proper cookies"
      (should= (str "HTTP/1.1 301 Moved Permanently\r\n"
                    "Location: /play\r\n"
                    "Set-Cookie: computer=x\r\n"
                    "Set-Cookie: human=o\r\n"
                    "Set-Cookie: gametype=computer-vs-human\r\n"
                    "Set-Cookie: difficulty=unbeatable\r\n"
                    "Set-Cookie: board=---------\r\n\r\n") (-> (build-response mock-request (new-response)) (get-headers))))))

{% endcodeblock %}
