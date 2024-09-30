# borkweb
## babashkas first web framework

## Why would anyone need that?
Good question! You tell me! I guess what drove me was my "new" introduction to clojure. I was doing clojure in the past i guess it was 2015 where i loved it already but i ditched it due to to less oportunities in the job marked (i was young and needed the money ;)) why i choosed javascript to go further. But i guess i have to start here from the beginning in a diffrent post ;). 

So the question was why babashka and why borkweb. I guess the thing is babashka imho is a beautifull way to get people into clojure. Download a binary. Throw code at it. DONE. It is much as i like stuff to work. In clojure land its a little trickier due to the fact that we are running on the beautifull jvm (yes i still think jvm is great). But this comes with a downside. We have to figure out some java stuff first. Like what the heck is maven dependencies? 

So like i said i guess borkweb was my take on get people into clojure by doing practical stuff fast. And i assume what is more practical as a real webapp. Much like ruby on rails,  borkweb tries to help people to get started fast. But the diffrence is borkweb does not want to hide stuff from you. Everything is (i assume :D) at plain sight. So why do we need borkweb. Cause people like to get to real work fast.

## NoBuild? Whats that?
I guess i first heard about that term from dhh in some of his blog posts. I'll link some down bellow. The idea is that you don't need anything else than your runtime and a browser to get your stuff running no build process involved and yes javascript nowadays has a ton of build steps. I guess i won't get to much into detail cause you all should already be familiar with that topic. 

So what does borkweb do to get to that point. borkweb doesn't use node or anything else only babashka. 
borkweb uses import-maps to fetch all your dependencies and the best part is you can host all the libs on your own. Grep it from jsdeliver or unpkg drop it in the static folder and reference it with the import-map. Now you are ready to go to use whatever lib you need like (p)react. 
But thats not all to it. With that technique you would only have the library at hand to be used in your javascript. 
So we need @borkdude's awesome squint library to compile a subset of clojurescript code to javascript. The best part newer versions of it support jsx renderer functions to be given to it. What that means is you are now able to "transpile" clojurescript jsx code to javascript on the fly with no conversion step of jsx to utility functions. Isn't that awesome?
Thats why in borkweb you just drop your `cljs` to the `resources/cljs` folder and put the `(view.components/cljs-module "somefile")` code into your hiccup datastructure and you are ready to go with a preact component. 

For example:
``` clojure
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

``` clojure
(require [views.components :as c])
(require [hiccup2.core :as h])

(defn some-html [req]
  (h/html
    [:div#cljs]
    (c/cljs-module "counter")))
```
Thats it a fully working preact component rendered on the server on the fly without the need of any javascript/nodejs build step.

## What else is included?
Well yeah some quality of live stuff like static file handling, compojuresque router functions build on top of ruuter. This is due to the fact that ring and compojure uses some stuff that don't work with babashka. Of course we had to fork one thing. ring-anti-forgery doesn't work with babashka due to the reliance on apache.commons stuff. But the fork is really lightweight it replaces only some base64 encoding thing that was used from apache.commons and can be easily implemented by java native functions. One bigger thing that was included is a login and register functionality which is based on postgresql (next.jdbc or babashka sql). It was added because be honest who likes to implement user login and registration for every project all the time and have you ever had the need to not have a login form.

I don't know what to tell more about it :D. That's borkweb i hope you like it.
