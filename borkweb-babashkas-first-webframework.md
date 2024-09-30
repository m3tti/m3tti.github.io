[<img src="https://github.com/m3tti/borkweb/raw/master/logo/borkweb.svg" alt="Borkweb" width="425px">](https://github.com/m3tti/borkweb)
# Babashka's First Web Framework

## Why Would Anyone Need That?
Good question! You tell me! I guess what drove me was my "new" introduction to Clojure. I was doing Clojure in the past, I guess it was 2015, where I loved it already, but I ditched it due to fewer opportunities in the job market (I was young and needed the money ;)). Why I chose JavaScript to go further, I'll have to start from the beginning in a different post ;).

So, the question was, why [Babashka](Https://Babashka.Org) and why [Borkweb](Https://Github.Com/M3tti/Borkweb)? I guess the thing is, [Babashka](Https://Babashka.Org), in my humble opinion, is a beautiful way to get people into Clojure. Download a binary, throw code at it, DONE. It's much like how I like stuff to work. In Clojure land, it's a little trickier due to the fact that we're running on the beautiful JVM (yes, I still think the JVM is great). But this comes with a downside. We have to figure out some Java stuff first, like what the heck is Maven dependencies?

So, like I said, I guess [Borkweb](Https://Github.Com/M3tti/Borkweb) was my take on getting people into Clojure by doing practical stuff fast. And I assume what's more practical than a real web app? Much like Ruby on Rails, [Borkweb](Https://Github.Com/M3tti/Borkweb) tries to help people get started fast. But the difference is, [Borkweb](Https://Github.Com/M3tti/Borkweb) doesn't want to hide stuff from you. Everything is (I assume :D) at plain sight. So, why do we need [Borkweb](Https://Github.Com/M3tti/Borkweb)? Because people like to get to real work fast.

## NoBuild? What's That?
I guess I first heard about that term from DHH in some of his blog posts. The idea is that you don't need anything else than your runtime and a browser to get your stuff running, no build process involved, and yes, JavaScript nowadays has a ton of build steps. I guess I won't get into too much detail, because you all should already be familiar with that topic.

So, what does [Borkweb](Https://Github.Com/M3tti/Borkweb) do to get to that point? [Borkweb](Https://Github.Com/M3tti/Borkweb) doesn't use Node or anything else, only [Babashka](Https://Babashka.Org). [Borkweb](Https://Github.Com/M3tti/Borkweb) uses import-maps to fetch all your dependencies, and the best part is, you can host all the libs on your own. Grab it from jsDeliver or unpkg, drop it in the static folder, and reference it with the import-map. Now you're ready to go and use whatever lib you need, like (P)React.

But that's not all to it. With that technique, you would only have the library at hand to be used in your JavaScript. So, we need @borkdude's awesome Squint library to compile a subset of ClojureScript code to JavaScript. The best part is that newer versions of it support JSX renderer functions to be given to it. What that means is you are now able to "transpile" ClojureScript JSX code to JavaScript on the fly with no conversion step of JSX to utility functions. Isn't that awesome?

That's why, in [Borkweb](Https://Github.Com/M3tti/Borkweb), you just drop your `cljs` to the `resources/cljs` folder and put the `(view.components/cljs-module "somefile")` code into your Hiccup data structure, and you're ready to go with a Preact component.

For example:
```clojure
;; resources/cljs/counter.cljs
(require '["https://esm.sh/preact@10.19.2" :as react])
(require '["https://esm.sh/preact@10.19.2/hooks" :as hooks])

(defn Counter []
  (let [[counter setCounter] (hooks/useState 0)]
    #jsx [:<>
          [:div "Counter " counter]
          [:div {:role "group" :class "btn-group"}
           [:button {:class "btn btn-primary" :onClick #(setCounter (inc counter))} "+"]
           [:button {:class "btn btn-primary" :onClick #(setCounter (dec counter))} "-"]]]))

(defonce el (js/document.getElementById "cljs"))

(react/render #jsx [Counter] el)
```

```clojure
(require [views.components :as c])
(require [hiccup2.core :as h])

(defn some-html [req]
  (h/html
    [:div#cljs]
    (c/cljs-module "counter")))
```
That's it, a fully working Preact component rendered on the server on the fly without the need for any JavaScript/Node.js build step.

## What Else is Included?
Well, yeah, some quality of life stuff like static file handling, Compojure-esque router functions built on top of Router. This is due to the fact that Ring and Compojure use some stuff that don't work with [Babashka](Https://Babashka.Org). Of course, we had to fork one thing. Ring-anti-forgery doesn't work with [Babashka](Https://Babashka.Org) due to the reliance on Apache Commons stuff. But the fork is really lightweight; it replaces only some base64 encoding thing that was used from Apache Commons and can be easily implemented by Java native functions. One bigger thing that was included is a login and registration functionality, which is based on PostgreSQL (next.jdbc or [Babashka](Https://Babashka.Org) SQL) and some custom hashing code that works on Java native hashing functionalities. It was added because, honestly, who likes to implement user login and registration for every project all the time, and have you ever had the need to not have a login form?

I don't know what to tell more about it :D. That's [Borkweb](Https://Github.Com/M3tti/Borkweb); I hope you like it.
