# On building a blog in borkweb
Hey new day now post i guess. So lets get it started. First of all you have to copy over all the stuff from borkweb. I assume the esiest way is to use the zipfile provided by github. So you can get that with some small commands.

``` shell
# curl https://codeload.github.com/m3tti/borkweb/zip/refs/heads/master -o borkweb.zip
# unzip borkweb.zip
# mv borkweb-master your-project-name
```

Thats it you are ready to go. 

## Setting up the database
Borkweb is currently dependend on postgresql you can setup your own or use the `docker-compose.yml` that has everything to start up a database system with postgres and pgadmin. Simply use `docker-compose up -d` in the root folder and there you go. You have to adjust your parameters in `database/core.clj`. Here you'll find something like that: 

``` clojure
(defonce db (jdbc/get-connection {:dbtype "postgres"
                                  :dbname "jobstop"
                                  :user "postgres"
                                  :password "test1234"
                                  :port 15432}))
```
Just adjust all the setting to your liking. And make sure that your database is available. After that the next natural step would be to adjust the `init.sql` script. After adding all the tables you need you can either directly import your sql script with a tool you already know or use the build in `bb -m database.core/initialize-db` function from borkweb to push all the setting to your db. Now is the time to expand the database functionality. In my case i seperate each clojure file in borkweb by the table space. So imagine you have a documents table i would create a file called `database/document.clj` and put all regarding functions into it. Borkweb supports already some helper functions to help you with creating crud functions e.g

``` clojure
(ns database.post
  (:require 
    [database.core :as db]))

(defn all []
  (db/execute! {:select :* :from :posts}))

(defn insert! [post]
  (db/insert! :posts post))
  
(defn update! [post]
  (db/update! :posts post {:id (:id posts)
                                 :deleted false}))

(defn find-by-id [id]
  (first (db/find-by-keys :posts {:id id})))
  
...
```
All these helper functions are optional you can easily access all the jdbc connection params and functions that are present in the `database.core` namespace. Another quite useful thing is that each helper-function comes with the ability to attach a transaction which than can be used to trigger multiple inserts updates deletes in one transaction. Further reading can be found in the honeysql and babashka sql pod documentation.

## Writing a page and registering it in the router
To get something to see on the webpage side of borkweb you have to write pages or views. I tend to create for each base route its own view file and attach all the html rendering functions in there. So for our example i would like to have a route called `/posts` as base route. Therefore we are going to create a `view/posts.clj` file and add every function in there for the rendering. We'll start with the `index` aka. `/posts/` route which displays simply all posts.

``` clojure
(ns view.posts
  (:require 
    [database.posts :as p]
    [view.components :as c])) ;; we are going to use the layout component already in place in borkweb
    
(defn render-row [post]
  [:tr
   [:td (:id post)]
   [:td (:title post)]])
    
(defn index [req]
  (c/layout
   req
   [:h1 "Posts"]
   [:table
    (map render-row (p/all))])
```

This will render all posts for you. 

TBD
