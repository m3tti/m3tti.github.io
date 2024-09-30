# On building a blog in borkweb
Hey new day now post i guess. So lets get it started. First of all you have to copy over all the stuff from borkweb. I assume the esiest way is to use the zipfile provided by github. So you can get that with some small commands.

``` shell
# curl https://codeload.github.com/m3tti/borkweb/zip/refs/heads/master -o borkweb.zip
# unzip borkweb.zip
# mv borkweb-master your-project-name
```

Thats it you are ready to go. 

## Setting the database connection
Borkweb is currently dependend on postgresql you can setup your own or use the `docker-compose.yml` that has everything to start up a database system with postgres and pgadmin. You can / have to adjust your parameters in `database/core.clj`. Here you'll find something like that: 

``` clojure
(defonce db (jdbc/get-connection {:dbtype "postgres"
                                  :dbname "jobstop"
                                  :user "postgres"
                                  :password "test1234"
                                  :port 15432}))
```
Just adjust all the setting to your liking. After that the next natural step would be to adjust the `init.sql` script. After adding all the tables you need you can either directly import your sql script with tool you already know or use the build in `bb -m database.core/initialize-db` function from borkweb to push all the setting to your db. Now is the time to expand the database functionality. In my case i seperate each clojure file in borkweb by the table space. So imagine you have a documents table i would create a file called `database/document.clj` and put all regarding functions into it. Borkweb supports already some helper functions to help you with creating crud functions e.g

``` clojure
(ns database.document
  (:require 
    [database.core :as db]))

(defn insert! [document]
  (db/insert! :documents document))
  
(defn update! [document]
  (db/update! :documents document {:id (:id document)
                                   :deleted false}))

(defn find-by-id [id]
  (first (db/find-by-keys :documents {:id id})))
  
...
```
All these helper functions are optional you can easily access all the jdbc connection params and functions. We'll cover that in the advanced database handling section. Another quite useful thing is that each helper-function comes with the ability to attach a transaction which than can be used to trigger multiple inserts updates deletes in one transaction.

``` clojure

```
tbd
## 
