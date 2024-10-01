Here is the corrected text:

# Building a Blog in Borkweb

Hey, new day, now post, I guess. So, let's get it started. First of all, you have to copy over all the stuff from Borkweb. I assume the easiest way is to use the zipfile provided by GitHub. So, you can get that with some small commands.

```shell
# curl https://codeload.github.com/m3tti/borkweb/zip/refs/heads/master -o borkweb.zip
# unzip borkweb.zip
# mv borkweb-master your-project-name
```

That's it, you are ready to go.

## On Setting Up the Database

Borkweb is currently dependent on PostgreSQL. You can set up your own or use the `docker-compose.yml` that has everything to start up a database system with PostgreSQL and pgAdmin. Simply use `docker-compose up -d` in the root folder, and there you go. You have to adjust your parameters in `database/core.clj`. Here, you'll find something like that:

```clojure
(defonce db (jdbc/get-connection {:dbtype "postgres"
                                  :dbname "jobstop"
                                  :user "postgres"
                                  :password "test1234"
                                  :port 15432}))
```

Just adjust all the settings to your liking, and make sure that your database is available. After that, the next natural step would be to adjust the `init.sql` script. After adding all the tables you need, you can either directly import your SQL script with a tool you already know or use the built-in `bb -m database.core/initialize-db` function from Borkweb to push all the settings to your database. Now is the time to expand the database functionality. In my case, I separate each Clojure file in Borkweb by the table space. So, imagine you have a documents table; I would create a file called `database/document.clj` and put all regarding functions into it. Borkweb supports already some helper functions to help you with creating CRUD functions, e.g.

```clojure
(ns database.post
  (:require 
    [database.core :as db]))

(defn all []
  (db/execute! {:select :* :from :posts}))

(defn insert! [post]
  (db/insert! :posts post))
  
(defn update! [post]
  (db/update! :posts post {:id (:id post)
                                 :deleted false}))

(defn find-by-id [id]
  (first (db/find-by-keys :posts {:id id})))
  
;; ...
```

All these helper functions are optional; you can easily access all the JDBC connection params and functions that are present in the `database.core` namespace. Another quite useful thing is that each helper function comes with the ability to attach a transaction, which can then be used to trigger multiple inserts, updates, and deletes in one transaction. Further reading can be found in the HoneySQL and Babashka SQL pod documentation.

### Init.sql

That's how a `init.sql` might look like.

```sql
create database blog;
use database blog;

-- create initial database
create table users(
       id serial primary key,
       email varchar(255) not null,
       password varchar(512) not null
);

-- posts
create table posts(
       id serial primary key,
       title varchar(255) not null,
       content text not null
);
```
## On Writing a Page and Registering it in the Router

To display content on the webpage side of Borkweb, you need to write pages or views. A common approach is to create a separate view file for each base route and attach all the HTML rendering functions to it. For example, to create a route called `/posts`, you would create a `view/posts.clj` file and add all the necessary functions for rendering.

```clojure
(ns view.posts
  (:require 
    [utils.response :as r]
    [database.posts :as p]
    [view.components :as c]))

(defn save [req]
  (p/insert! (:form-params req))
  (r/redirect "/posts"))

(defn new [req]
  (c/layout
   req
   [:h1 "New Post"]
   [:form {:method "post" :action "/posts"}
    [:label "Title"]
    [:input {:name "title" :type "text"}]
    [:label "Content"]
    [:textarea {:name "content"}]
    [:input {:type "submit" :value "save"}]))

(defn render-row [post]
  [:tr
   [:td (:id post)]
   [:td (:title post)]])

(defn index [req]
  (c/layout
   req
   [:h1 "Posts"]
   [:table
    (map render-row (p/all))]))
```

This code will render all posts. To attach this code to the router, you need to extend the code in `/routes.cljs` with the newly created page.

```clojure
(ns routes
  (:require
   [ruuter.core :as ruuter]
   [static :as static]
   [view.index :as index]
   [view.login :as login]
   [view.profile :as profile]
   [view.posts :as posts]
   [view.register :as register]))

;; ... some helper stuff 

(def routes
  #(ruuter/route 
    [(get "/static/:filename" static/serve-static)
     (get "/" index/page)
     (get "/register" register/index)
     (post "/register" register/save)
     (get "/login" login/index)
     (post "/login" login/login)
     (get "/logout" login/logout)
     (get "/profile" profile/index)
     ;; our new page
     (get "/posts" posts/index)
     (get "/posts/new" posts/new)
     (post "/posts" posts/save)]
    %))
```

After starting the app with `bb -m core` and navigating to `http://localhost:8080/posts` in your browser, you should see the table you just created.

## On Adding CLJS Components

ClojureScript is a key feature in Borkweb. It allows you to easily add interactive components to your pages without requiring a full-featured SPA framework. Borkweb uses Preact components, which can be added to any Hiccup code using the `(view.components/cljs-module filename)` function.

To create a Preact component, simply drop your ClojureScript code into the `resources/cljs` folder. For example, to create a counter component, you can create the following file:

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

You can then load this component in your post code like this:

```clojure
(defn index [req]
  (c/layout
   req
   [:h1 "Posts"]
   (c/cljs-module "counter")
   [:table
    (map render-row (p/all))])
```

This will render a JavaScript counter component. Borkweb also supports Preact web components, which can be used like any normal HTML tag in your page. An example of this can be found in `resources/cljs/custom-element.cljs`.

```clojure
(require '["https://cdn.jsdelivr.net/npm/preact-custom-element@4.3.0/+esm$default" :as register])

(defn Greeting [{:keys [name]}]
  #jsx [:p "Hello, " name])

(register Greeting "x-greeting", ["name"], {:shadow false})
```

This code can be loaded in the `view.component/layout` function to support each view that uses this layout with the custom-created component.

I guess that was everything I've got to say about Borkweb. I hope you found this information helpful and informative.

