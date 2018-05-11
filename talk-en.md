title: Clojure Full-stack application
author:
  name: Lukáš Rychtecký
  twitter: lukasrychtecky
controls: true
style: style.css

--

# Clojure Full-stack aplication
## Lukáš Rychtecký
## [https://twitter.com/LukasRychtecky](https://twitter.com/LukasRychtecky)

--

### About me

* [https://twitter.com/LukasRychtecky](https://twitter.com/LukasRychtecky)
* [https://github.com/LukasRychtecky](https://github.com/LukasRychtecky)

--

### Outline

* Introduction into a functional prgramming
* Clojure ecosystem
* Architecture, dependency injection, routing
* DB and migrations with Duct
* REST and Compojure
* Testing
* SPA with Re-frame and Reagent (development stack)

--

### Motivation

How could we test this?

```python
def add(a: int, b: int) -> int:
    return a + b
```

--

### Motivation

How could we test this?

```python
def add(a: int, b: int) -> int
    return a + b
```

Simple

```python
assert 3 add(1, 2)
```

--

### Motivation

How could we test this?

```python
def add(a: int, b: int) -> int:
    return a + b
```

Simple

```python
assert 3 add(1, 2)
```

Why?

--

### Motivation

How could we test this?

```python
def add(a: int, b: int) -> int:
    return a + b
```

Simple

```python
assert 3 add(1, 2)
```

Why?

Because it's a `pure function` (mathematical funkce)

It means that a function uses just given arguments and returns a result

--

### Motivation

`Pure function` is a function that:

* works with given arguments
* returns a deterministic result
* doesn't mutate variables -> variables are immutable == constants

--

### Motivation - What are side effects?

* a modification of outside wolrd variables
* IO operations (a file disc, DB, HTTP, etc.)

Example:
```python
for a in [1, 2, 3, 4]:
    print(a)
```

How could we test this?

--

### Motivation - Roots

* it started in 1930
* it's a different style of thinking about problems -> data transformation
* John McCarthy invented LISP for AI

![LISP creator](img/John_McCarthy_Stanford.jpg)

--

### Motivation - Present

* Clojure (Rich Hickey, 2007, LISP dialect, JVM)
* ClojureScript (Clojure, Javascript machine)

![Clojure creator](img/Rich-Hickey.jpeg)

--

### Motivation - Clojure

* dynamically and statically typed
* immutabilní struktury
* advanced collections (vector, linked list, set, hashmap)
* parallel programming
* lazy sequences
* a practical language (side effects)

--

### Pillars of functional programming

* first-class and higher-order functions
* pure functions
* lazy evaluation
* declaratice style of programming

--

### First-class and higher-order functions

* a function can be passed as an argument
* a function can return another function

```clojure
(defn foo
  [func]
  (func 1 3))

(defn bar
  [a b]
  ;; do stuff
  ;; ...
  )


(foo bar)
```

--

### Pure functions

* a function without side effects (IO operations etc.)

```clojure
(defn count-items
  [result]
  (count (:items result)))

(map count-items results)  ;; [1, 2, 4, 1]
```

--

### Lazy evaluation

* iteration and computation are done when thay are actually needed
* easy work with an infinity sequence

```clojure

(take 3 infinity-iter) ; [1 4 4]

(take 2 infinity-iter) ; [1 5]
```

--


### Preconceptions

#### You run you of memory, because everything is immutable

That's not truth. All data structures are implementes as Persistent vector. See more
 [http://blog.higher-order.net/2009/02/01/understanding-clojures-persistentvector-implementation](http://blog.higher-order.net/2009/02/01/understanding-clojures-persistentvector-implementation)


--

### Preconceptions

#### An infinity sequence is too big to a memory

That's truth, but becase an evalutation is lazy, not all items of a collection are computed in the memory.
It means that you can pass through an application an iterator of millions items, but only a position of the iterator is
saved in the memory.

--

### An infinity sequence - example

```clojure
(filter even? (range)) ; does nothing

(next (filter even? (range))) ; returns just 2 and it doesn't compute anything else
```

See more [http://clojure-doc.org/articles/language/laziness.html](http://clojure-doc.org/articles/language/laziness.html)

--

### How to deal with side effects?

* use less side effects as possible, have pure functions as you can
* for pure functional languages - monad [https://wiki.haskell.org/Monad](https://wiki.haskell.org/Monad)

--

### Clojure ecosystem

* Clojure s based on a lot of small libraries
* You will not find anything like Django or Rails
* You should compose a project you-self
* A bigger freedom and bigger responsibility
* [https://github.com/razum2um/awesome-clojure](https://github.com/razum2um/awesome-clojure)

--

### Clojure ecosystem

* JVM - a running environment
* [Leiningen](https://leiningen.org) - build and management tool (de facto standard)
* [Boot](https://github.com/boot-clj/boot) - build and management tool

--

### Clojure ecosystem

Leiningen and Boot have same functionalities:

* dependency management
* REPL
* plugins
* builds
* templates for applications

See more [https://www.braveclojure.com/appendix-a/](https://www.braveclojure.com/appendix-a/)

* ClojureScript [Lumo](https://github.com/anmonteiro/lumo) - NodeJS

--

### Task No. 1

Run Leiningen REPL (`lein repl`)

* List all even numbers from 0 to 10
* Create a function that makes a sum of a collection
* Create a function that make a sum of powers of even numbers from 0 to 10

Clojure documentation [http://clojuredocs.org](http://clojuredocs.org/)

--

### Task No. 1 - Solution

Run Leiningen REPL (`lein repl`)

* List all even numbers from 0 to 10
* `(filter even? (range 10))` nebo `(remove odd? (range 10))` `(0 2 4 6 8)`
* Create a function that makes a sum of a collection
* `(defn sum [coll] (reduce + coll))` a `(sum (range 10))` `45`
* Create a function that make a sum of powers of even numbers from 0 to 10
* `(reduce + (map #(* % %) (filter even? (range 10))))` `120`
--

### Task No. 1 - Solution

* or more readable

```clojure
(->> (range 10)
      (filter even?)
      (map #(* % %))
      (reduce +))
```

--

### Architecture and dependency injection with Duct

`Duct` if a dependency injection framework

* a component system based on [Integrant](https://github.com/weavejester/integrant)
* a declarative configuration
* life-cycle of a component
* hot-reload of a code
* The Twelve-Factor App [https://12factor.net](https://12factor.net)

--

### A component

* it's a dispatch method with a configuration
* life-cycle events: `prep`, `init`, `halt`, `resume`, `suspend`
* in 99 % it's enough to use `init` and `halt`
* see more [https://github.com/weavejester/integrant](https://github.com/weavejester/integrant)

--

### A component - example configuration

```clojure
{:duct.core/project-ns myapp
 :duct.core/environment :production

 :duct.module/sql
 {:database-url #duct/env ["DB_URL" Str]}

 :myapp.handler/api ;; <-- same name as namespace, myapp/handler/api.clj
 {:auth-key #duct/env ["AUTH_KEY" Str :or "dummy-key"]
  :db #ig/ref :duct.database/sql
  :environment #ig/ref :duct.core/environment}

...
```

--

### Component - example

```clojure
(ns myapp.handler.api
  (:require
    [clojure.java.io :as io]
    [compojure.core :refer [GET]]
    [integrant.core :as ig]))

(defmethod ig/init-key :myapp.handler/api
  [_ conf] ;; <-- first argument is a name, second a configuration (hash-map)
  (GET "/example" [:as request]
    (io/resource "myapp/handler/example.html")))

```

--

### Ring

* Ring is an implementation of a servletu - it communicates between a code and HTTP
[https://github.com/ring-clojure/ring](https://github.com/ring-clojure/ring)
* Handler - a function that takes HTTP request as hasp-map and returns another hash-map as response
* Request - a hash-map with keys like `:headers`, `:uri`, `:body`, ...
* Response - a hash-map with three keys `:status`, `:body` a `:headers`
* Middleware - a high-order function that could modify a request or a response

--

### Ring

* On [clojars.org](https://clojars.org) you can find a lot of useful middlewares
* Documentation
  [https://github.com/ring-clojure/ring/wiki/Concepts](https://github.com/ring-clojure/ring/wiki/Concepts)

--

### Ring middleware - example

A middleware sets Content-Type

```clojure
(defn wrap-content-type
  [handler content-type]
  (fn [request] ;; <-- returns new handler
    (-> request
        handler
        (assoc-in [:headers "Content-Type"] content-type))))

(defn handle-example
  [conf]
  (GET "/example" [:as request]
    (io/resource "myapp/handler/example.html")))


(defmethod ig/init-key :myapp.handler/api
  [_ conf]
  (-> conf
      handle-example
      (wrap-content-type "text/html")))
```
--

### Ring - util

* A namespace `ring.util` contains a lot of useful functions
* `ring.util.response/content-type`, `ring.util.response/response`, ...

example:

```clojure
(require '[ring.util.response :as response])

(defn handle-example
  [conf]
  (GET "/example" [:as request]
    (response/response "<h1>Hallo</h1>")))

```
--

### Compojure - routing

* A small library for a declarative routing

```clojure
(require '[compojure.core :refer [GET context routes]])

(GET "/example" [:as request]
  (io/resource "myapp/handler/example.html")))
```

Destructuring URL parameters

```clojure
(GET "/example/:id" [id :as request]
  (io/resource "myapp/handler/example.html")))
```

Destructuring URL and query parameters

```clojure
(GET "/example/:id" [id foo :as request]
  ;; <-- foo is not in a path, it will be taken from query parameters
  (io/resource "myapp/handler/example.html")))
```

--

### Compojure - routing examples

`context` reduces a parent part of a path

```clojure
(context "/example"
  (GET "/foo" [:as request]
    "<h1>foo</h1>")

  (GET "/bar" [:as request]
    "<h1>bar</h1>"))
```

Generates handlers for `/example/foo` and `/example/bar`

--

### Compojure - routing examples

`routes` concatenates more routes

```clojure
(def app
  (routes
    (GET "/foo" [:as request]
      "<h1>foo</h1>")
    (GET "/bar" [:as request]
      "<h1>bar</h1>")))
```

--

### Task No. 2

* Generate a projects from a template `duct`:
```sh
lein new duct todomvc +api +example +postgres
cd todomvc
lein duct setup
```

* Put into `project.clj` under key `:profiles/dev` a value `{:plugins [[venantius/ultra "0.5.1"]]}` - it adds colours into a REPL
* `lein repl` - it runs a REPL of a project

```clojure
(dev) ;; loads dev namespace
(reset) ;; reloads a code and restarts a server
```

--

### Task No. 2

* Try [http://localhost:3000/example](http://localhost:3000/example)
* Put into a configuration `todomvc.handler/api` new value `:name` and return it in a response
* Return in a response a URL parameter and a query parameter

--

### Task No. 2 - solution

`resources/todomvc/config.edn`

```clojure
...
 :todomvc.handler/api
 {:db #ig/ref :duct.database/sql
  :name "Clojure"}}
```

`src/todomvc/handler/example.clj`

```clojure
(defn handle-hello
  [conf]
  (GET "/hello" [url-param query-param :as request]
    (response/response (format "<h1>Hello %s, :param %s, :query-param %s</h1>"
                               (:name conf)
                               url-param
                               query-param))))
```
--

### DB and migration with Duct - boundary

* A boundary is a protocol that separates a logic and outside world (RDBS, Redis, HTTP calls, ...)
* It allows to push side effects into "a border" of a system
* Simplifies a testing

--

### DB and migration with Duct - boundary example

```clojure
(ns todomvc.tasks.boundary
  (:require
    [duct.database.sql] ;; <-- A Duct module that handles a connection to a DB
    [todomvc.tasks.db :as db])) ;; <-- a namespace withing DB queries


(defprotocol TaskService ;; <-- "Interface" of a boundary
  (get-all [db-spec]))


(extend-protocol TaskService ;; <-- And its implementation
  duct.database.sql.Boundary
  (get-all [db-spec]
    (db/get-all db-spec))) ;; <-- Here we call a real DB query
```

In fact a `Boundary` is just a record:

```clojure
(defrecord Boundary [spec])
```
--

### DB and migration with Duct - boundary 2. example

A real calling DB:
```clojure
(get-all (->Boundary db))
;;({:description "uklidit" :done false :id 1 :title "Prvni ukol"}))
```

Let's define new record

```clojure
(defrecord FakeBoundary [spec])

(extend-protocol TaskService
  FakeBoundary
  (get-all [db-spec]
    ["Nothing"]))
```
--

### DB and migration with Duct - boundary 2. example

Let's get all tasks
```clojure
(get-all (->FakeBoundary db))
;;["Nothing"]
```
--

### DB and migration with Duct - Ragtime

* Ragtime is a library for DB migrations
* Migrations can be defined in a configuration of SQL files
* [https://github.com/duct-framework/migrator.ragtime](https://github.com/duct-framework/migrator.ragtime)

--

### DB and migration with Duct - Ragtime

Advantages:

* No complicated DSL
* Pure SQL, syntax highlighting

Disadvantages:

* A little verbose configuration

--

### DB and migration with Duct - Ragtime configuration

`resources/todomvc/config.edn`:

```clojure
{:duct.migrator/ragtime
 {:migrations [#ig/ref :todomvc.migration/add-tasks]}
 ;; <-- an order of migrations

 [:duct.migrator.ragtime/sql :todomvc.migration/add-tasks]
 ;; <-- naming of a migration
 {:up [#duct/resource "todomvc/migrations/001-add-tasks.up.sql"]
  ;; <-- up migration
  :down [#duct/resource "todomvc/migrations/001-add-tasks.down.sql"]}}
  ;; <-- down migration
```

--

### DB and migration with Duct - Ragtime configuration

`resources/todomvc/migrations/001-add-tasks.up.sql`:
```sql
create table tasks (
    id serial primary key,
    title text not null,
    description text,
    done boolean default false not null)
```
* All migrations are applied automatically when a server is started via REPL

--

### DB and migration with Duct - HugSQL

* HugSQL is a library for DB queries
* [https://www.hugsql.org](https://www.hugsql.org/)
* No abstraction, just pure SQL

Alternatives:

* Clojure JDBC [http://funcool.github.io/clojure.jdbc/latest](http://funcool.github.io/clojure.jdbc/latest/) is a base
  library for simple quering, or complicated queires can be composed from strings
* Korma [http://sqlkorma.com](http://sqlkorma.com/) DSL for queries

--

### DB and migration with Duct - HugSQL example

HugSQL binds SQL queries into Clojure functions (via annotations)

`src/todomvc/tasks/db.sql`:

```sql
\-- :name get-all :? :*
select * from tasks
```
Note: Backslash \ must not be in a real file, it's here just because of Markdown

--

### DB and migration with Duct - HugSQL example

`src/todomvc/tasks/db.clj`:
```clojure
(ns todomvc.tasks.db
  (:require
    [hugsql.core :as hugsql]))

(hugsql/def-db-fns "todomvc/tasks/db.sql") ;; <-- imports SQL queries from file
(hugsql/def-sqlvec-fns "todomvc/tasks/db.sql")
;; <-- it's helpful during development, it displays a whole query
```
--

### DB and migration with Duct - REPL

* Create DB (PostgreSQL) (e.g. `createdb todomvc -O <user>`)
* Switch to tag `priprava-ukol3` (`git reset --hard priprava-ukol3`)

--

### DB and migration with Duct - REPL

* Set up a connection to DB `export DB_URL="jdbc:postgresql://localhost/todomvc?user=root&password=toor"` or add it into
  `dev/resources/local.edn` a line `:duct.module/sql {:database-url #duct/env ["DB_URL" Str :or "jdbc:postgresql://localhost/todomvc?user=root&password=toor"]}`
* Run REPL `lein repl`
* Switch to dev profile `(dev)`
* Run application `(reset)`

--

### DB and migration with Duct - REPL

* In a console you suppose to see `:duct.migrator.ragtime/applying :todomvc.migration/add-tasks#491d1e44`
* Now you can manage a whole application from the console
--

### DB and migration with Duct - REPL and DB queries 1.

* In variable `config` you can see the configuration
* All change in a code will be hot-swapped when you run `(reset)`
* Save a DB connection `(def db-spec (-> config :duct.module/sql :database-url))`
* Import DB queries `(require '[todomvc.tasks.db :as db])`

--

### DB and migration with Duct - REPL and DB queries 2.

* Get all tasks from the DB `(db/get-all db-spec)`
* WARNING DB queries aren't refreshed after calling `(reset)` it's necessary to reload a namespace by hand `(require '[todomvc.tasks.db :as db] :reload)`
* See a DB query `(db/get-all-sqlvec)`

--

### Task No. 3

* Add function `create-task` that will accepts a hash-map within `:title` and `:description` keys
* Add function `finish-task` that will mark a task as done

--

### Task No. 3 - Solution

* Add function `create-task` that will accepts a hash-map within `:title` and `:description` keys

```sql
\-- :name create-task :<! :1
insert into tasks (title, description) values (:title, :description)
```

* You have to reset the system `(reset)` and reload the namespace `(require '[todomvc.tasks.db :as db] :reload)`

```clojure
(db/create-task-sqlvec {:title "Umyt nadobi", :description "HNED!"})
["insert into tasks (title, description) values ( ? , ? )" "Umyt nadobi" "HNED!"]

(db/create-task db-spec {:title "Umyt nadobi", :description "HNED!"})
```
--

### Task No. 3 - Solution

* Add function `finish-task` that will mark a task as done

```sql
\-- :name finish-task :<! :1
update tasks set done = true where id = :id returning *
```

```clojure
(db/finish-task-sqlvec {:id 2})
["update tasks set done = true where id = ? returning *" 2]

(db/finish-task db-spec {:id 2})
{:description "HNED!" :done true :id 2 :title "Umyt nadobi"}
```

--


### REST and Compojure

Defines routes for /api/tasks:

`src/todomvc/handler/api.clj`

```clojure
(ns todomvc.handler.api
  (:require
    [compojure.core :refer [context GET]]
    [integrant.core :as ig]
    [ring.middleware.json :refer [wrap-json-response]]
    [todomvc.tasks.api :as tasks]))


(defmethod ig/init-key :todomvc.handler/api ;; <-- new component for API
  [_ conf]
  (wrap-json-response ;; <-- we want response as a JSON
    (context "/api" []
      (context "/tasks" []
        (GET "/" [:as request]
          (tasks/get-tasks conf request)))))) ;; <-- a handler itself
```
--

### REST and Compojure

A middleware must be in requirements, thus let's edit `project.clj` by adding `[ring/ring-json "0.4.0"]` into `:dependencies`

--

### REST and Compojure - A task creation

A component has to be registered in `resources/todomvc/config.edn`:

```clojure
...

 :todomvc.handler/api
 {:db #ig/ref :duct.database/sql}

 :duct.router/cascading
 [#ig/ref :todomvc.handler/api]
;; <-- this line is already there, just replace the origin one
```
--


### REST and Compojure

An implementation `src/todomvc/tasks/api.clj`:

```clojure
(ns todomvc.tasks.api
  (:require
    [todomvc.tasks.boundary :as db]))

(defn get-tasks
  [conf request]
  {:body (db/get-all (:db conf)), :status 200})
```
--


### REST and Compojure

* Run REPL and restart the server
* Call `curl http://localhost:3000/api/tasks` and you suppose to get a list of tasks

--

### REST and Compojure - A task creation

Let's add a handler for a task creation `src/todomvc/handler/api.clj`:

```clojure
(ns todomvc.handler.api
  (:require
    [compojure.core :refer [context GET POST]] ;; <-- import POST
    [integrant.core :as ig]
    [ring.middleware.json :refer [wrap-json-response]]
    [todomvc.tasks.api :as tasks]))


(defmethod ig/init-key :todomvc.handler/api
  [_ conf]
  (wrap-json-response
    (context "/api" []
      (context "/tasks" []
        (GET "/" [:as request]
          (tasks/get-tasks conf request))
        (POST "/" [:as request] ;; <-- a handler for POST
          (tasks/create-task conf request))))))
```
--

### REST and Compojure - A task creation

An implementation of the handler `src/todomvc/tasks/api.clj`:

```clojure
(defn create-task
  [conf request]
  (let [new-task (db/create-task (:db conf) (:body-params request))]
    {:body new-task, :status 201}))
```

A protocol for TaskService should be like:

```clojure
(defprotocol TaskService
  (get-all [db-spec])
  (create-task [db-spec task]))
```

--

### REST and Compojure - A task creation

Restart a server `(reset)` a make a POST request to `http://localhost:3000/api/tasks`

```json
{"title": "Uklit byt", "description": "Vynést koď, vyluxovat, ..."}
```

--

### REST and Compojure - Validation

* Data suppose to be validated not only for security reasons, let's use Struct library
[http://funcool.github.io/struct/latest/](http://funcool.github.io/struct/latest/)
* A validation model - pure data, no macros
* Add `[funcool/struct "1.2.0"]` into dependencies and restart REPL

--

### REST and Compojure - Validation

Example of usage:

```clojure
(require '[struct.core :as st])

(st/validate {:name nil} ;; <-- inpute data
             {:name [st/required]}) ;; <-- a validation model
[{:name "this field is mandatory"} {}]
```

```clojure
(let [[errors input] (st/validate {:name "foo"} {:name [st/required]})]
  (if (nil? errors)
    :valid
    :invalid))
:valid
```
--

### REST and Compojure - Validation

The library supports also coercing:

```clojure
(st/validate {:age "20"} ;; <-- věk je tu jako string
             {:age [st/required st/integer-str]}) ;; <-- an input is string, but we want it as an integer
[nil {:age 20}] ;; <-- voilà an integer is here
```

It can throw away unknown keys:
```clojure
(st/validate {:age "20", :foo "bar"}
             {:age [st/required st/integer-str]}
             {:strip true})
[nil {:age 20}] ;; <-- see we only have known keys
```
--

### Task No. 4

* Edit function `create-task` for input validation: `:title` - required, `:description` - non required (no neede to be in a request)

--

### Task No. 4 - Solution

* Edit function `create-task` for input validation: `:title` - required, `:description` - non required (no neede to be in a request)

```clojure
(def Task
  {:title [st/required]
   :description [st/string]})

(defn create-task
  [conf request]
  (let [[errors input] (st/validate (:body-params request) Task {:strip true})]
    (if (nil? errors)
      {:body (db/create-task (:db conf)
                             (merge (into {} (map vector (keys Task) (repeat nil)))
                                    input))
                             ;; <-- HugSQL needs all attributes defined in a query
       :status 201}
      {:body {:errors errors}, :status 400})))
```

--

### Task No. 5

* Add a handler that marks a task as done

--

### Task No. 5 - Solution

* Add a handler that marks a task as done

`src/todomvc/tasks/api.clj`

```clojure
(PATCH "/:id" [id :as request]
  (tasks/finish-task conf request))
```

--

### Task No. 5 - Solution

* Add a handler that marks a task as done

`src/todomvc/tasks/api.clj`

```clojure
(defn finish-task
  [conf request]
  (let [[errors input] (st/validate (:params request) {:id [st/required st/integer-str]}) ;; <-- id must be an integer
        task (db/finish-task (:db conf) (:id input))]
    (if (nil? task)
      {:status 404}
      {:status 200, :body task})))
```

--


### Testing

We will use a standard module `clojure.test`

```clojure
(ns todomvc.foo-test
  (:require
    [clojure.test :refer [deftest is testing]]))

(deftest foo-test
  (testing "should add two numbers"
    (is (= 2 (+ 1 1)))))
```

--

### Testing - API

Let's test `get-tasks` handler via HTTP `test/todomvc/tasks/api_test.clj`:

```clojure
(ns todomvc.tasks.api-test
  (:require
    [clojure.test :refer [deftest is]]
    [cheshire.core :as cheshire] ;; <-- for parsing a JSON response
    [integrant.core :as ig]
    [ring.mock.request :as mock] ;; <-- for mocking Ring request
    [todomvc.tasks.api]
    [todomvc.tasks.boundary :refer [TaskService]]))
```
--

### Testing - API

Let's test `get-tasks` handler via HTTP `test/todomvc/tasks/api_test.clj`:

```clojure
(defn json-response ;; <-- a helper function for a request application and parsong JSON response
  ([conf method path]
   (json-response conf method path nil))
  ([conf method path body] ;; <-- two arities, see below
   (let [handler (ig/init-key :todomvc.handler/api conf)
         request (mock/request method path)]
     (-> (if (nil? body)
           request
           (assoc request :body-params body))
         handler
         (update :body #(some-> % (cheshire/decode true)))))))

(deftest get-tasks-test
  (let [conf {:db (reify TaskService ;; <-- "mock" DB
                    (get-all [db-spec]
                      []))}
        request (mock/request :get "/api/tasks")
        response (json-response conf request)]
    (is (= 200 (:status response)))
    (is (= [] (:body response)))))
```

Restart the server `(reset)` and run tests `(test)`

--

### Task No. 6

* Write a test for a task creation

--

### Task No. 6 - Solution

* Write a test for a task creation

`src/todomvc/tasks/api.clj`

```clojure
(deftest create-task-test
  (testing "should create a task"
    (let [conf {:db (reify TaskService
                      (create-task [db-spec params]
                        (assoc params :id 1)))}
          response (json-response conf :post "/api/tasks" {:title "Task 1"})]
      (is (-> response :body :id some?))
      (is (= 201 (:status response)))))

  (testing "should return bad request"
    (let [response (json-response {:db nil} :post "/api/tasks" {})]
      (is (= {:errors {:title "this field is mandatory"}} (:body response)))
      (is (= 400 (:status response))))))
```

--


### ClojureScript + ReactJS = Reagent

* [Reagent](https://reagent-project.github.io/) - a wrapper around ReactJS, a library for UI creation
* [Re-frame](https://github.com/Day8/re-frame) - "MVC" framework for UI

#### Flow

```text
View -> dispatch -> Handler -> Subscriber
|                                        |
     <-                      <-
```

--

### Reagent + Re-frame: views

* Let's define UI

Pure view

```clojure
(defn hello-view
  [name]
  [:h1 "Hello " name])
```

This is not pure view

```clojure
(defn hello-view
  []
  (let [name (subscribe [:name])]
    (fn []
      [:h1 "Hello " @name])))
```

--

### Reagent + Re-frame: handlers

A handler + side effect

* a "classic" handler for events

```clojure
(register-handler
  (after write-to-log) ; <-- side effect, out of a logic
  :set-name
  (fn [db [_ name]]
    (assoc db :name name)))
```

--

### Reagent + Re-frame: subscribers

Subscriber

* a cursor/view on data

```clojure
(register-sub
  :name
  (fn [db _]
    (reaction (get @db :name))))
```

--

### SPA with Re-frame and Reagent - configuration

Edit `resources/todomvc/config.edn`:

```clojure
 :duct.middleware.web/defaults {:static {:resources "todomvc/public"}}
 ;; <-- for static files

  :duct.router/cascading
 [#ig/ref :todomvc.handler/api
  #ig/ref :todomvc.handler/site] ;; <-- add new routes

 :todomvc.handler/site {} ;; <-- add new handler
```

--

### SPA with Re-frame and Reagent - configuration

Create index page `resources/todomvc/pages/index.html`:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Example Handler</title>
  </head>
  <body>
    <div id="app"><h1>Loading...</h1></div>
    <script src="/js/main.js"></script>
    <script>todomvc.client.main.init()</script>
  </body>
</html>
```

--

### SPA with Re-frame and Reagent - configuration

Add an netry point for CLJS development `dev/src/client.cljs`:

```clojure
(ns dev.client
  (:require
    [figwheel.client :as fw]
    [re-frisk.core :refer [enable-re-frisk!]]
    [todomvc.client.main]))

(enable-console-print!)
(enable-re-frisk!)


(fw/start {:on-jsload todomvc.client.main/init
           :websocket-url "ws://localhost:3449/figwheel-ws"}) ;; <-- set up hot reload
```

--

### SPA with Re-frame and Reagent - configuration

Add an entry point for UI application `src/todomvc/client/main.cljs`:

```clojure
(ns todomvc.client.main)

(defn ^:export init
  []
  (let [app-element (.getElementById js/document "app")]
    (js/console.log "CLJS!" app-element)))
```
--

### SPA with Re-frame and Reagent - configuration

Add new routes `src/todomvc/handler/site.clj`:

```clojure
(ns todomvc.handler.site
  (:require
    [compojure.core :refer [GET]]
    [integrant.core :as ig]
    [ring.util.response :as response]))

(defmethod ig/init-key :todomvc.handler/site
  [_ conf]
  (GET "/" [:as request]
    (response/content-type
      (response/resource-response "todomvc/pages/index.html")
      ;; <-- returns a static page
      "text/html")))
```
--

### SPA with Re-frame and Reagent - configuration

* a configuration in `project.clj` is a little bit verbose, let's switch into next step `git reset --hard cljs-configuration`
* Restart the REPL and the server `(dev)`, `(reset)` and run CLJS build `(figwheel-repl/start-figwheel!)`
* In console you suppose to see `Successfully compiled build :dev to "resources/todomvc/public/js/main.js" in 2.999 seconds.`

--

### SPA with Re-frame and Reagent - configuration

* Open a browser [http://localhost:3000](http://localhost:3000/)
* A production build `lein min-app`
--

### SPA with Re-frame and Reagent - List all tasks

Edit `src/todomvc/client/main.cljs`:

```clojure
(ns todomvc.client.main
  (:require
    [reagent.core :as reagent]
    [re-frame.core :as re-frame]
    [todomvc.client.events]
    [todomvc.client.subs] ;; <-- it needs to be imported anywhere, otherwise it will not be registered
    [todomvc.client.tasks.components :as components]
    [todomvc.client.tasks.events]))
    ;; <-- it needs to be imported anywhere, otherwise it will not be registered


(def init-db ;; <-- initial DB
  {:tasks {:list nil
           :new-task nil}})

```
--

### SPA with Re-frame and Reagent - List all tasks

Edit `src/todomvc/client/main.cljs`:

```clojure
(defn- main
  []
  (reagent/create-class
    {:display-name
     "main"

     :component-will-mount ;; <-- classic React event handler
     #(re-frame/dispatch [:tasks/get-tasks])

     :reagent-render
     (fn []
       (let [app (re-frame/subscribe [:app])] ;; <-- the only one subscriber we have in the application
         (fn []
           (components/todomvc-wrapper @app))))}))
           ;; <-- it need to be dereferenced! we don't want to use an atom
```
--

### SPA with Re-frame and Reagent - List all tasks

Edit `src/todomvc/client/main.cljs`:

```clojure
(defn ^:export init
  []
  (let [app-element (.getElementById js/document "app")]
    (re-frame/dispatch-sync [:init-db init-db]) ;; <-- set up an initial state of a DB
    (re-frame/clear-subscription-cache!)
    (reagent/render [main] app-element)))
```

--

### SPA with Re-frame and Reagent - List all tasks

Create a subscriber for a DB `src/todomvc/client/subs.cljs`:

```clojure
(ns todomvc.client.subs
  (:require
    [re-frame.core :as re-frame]))


(re-frame/reg-sub
  :app
  identity)
```
--

### SPA with Re-frame and Reagent - List all tasks

Create a handler for setting an initial state of a DB `src/todomvc/client/events.cljs`:

```clojure
(ns todomvc.client.events
  (:require
    [re-frame.core :as re-frame]))


(re-frame/reg-event-db
  :init-db
  (fn [_ [_ init-db]]
    init-db))
```

--

### SPA with Re-frame and Reagent - List all tasks

Create a handler to get all tasks `src/todomvc/client/tasks/events.cljs`:

```clojure
(ns todomvc.client.tasks.events
  (:require
    [re-frame.core :as re-frame]
    [todomvc.client.tasks.middlewares :as m]))


(re-frame/reg-event-db
  :tasks/get-tasks
  [m/get-tasks] ;; <-- side effect ouf side of logic
  (fn [db _]
    db)) ;; <-- we don't want to change a state of the DB


(re-frame/reg-event-db
  :tasks/handle-get-tasks
  (fn [db [_ tasks]]
    (assoc-in db [:tasks :list] tasks))) ;; <-- set up tasks into the DB
```

--

### SPA with Re-frame and Reagent - List all tasks

Create a handler to get all tasks `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/handle-error-get-tasks
  (fn [db [_ response]]
    db)) ;; <-- here we can set up e.g. a message
```

--

### SPA with Re-frame and Reagent - List all tasks

Create a middleware to get all tasks `src/todomvc/client/tasks/middlewares.cljs`:

```clojure
(ns todomvc.client.tasks.middlewares
  (:require
    [ajax.core :refer [GET]]
    [re-frame.core :as re-frame]))


(def get-tasks
  (re-frame/after ;; <-- side effect wrapped in `after` function
    (fn [db _]
      (GET "/api/tasks"
           :format :json
           :response-format :json
           :keywords? true
           :handler #(re-frame/dispatch [:tasks/handle-get-tasks %])
           ;; <-- dispatch an evetn to handle a response
           :error-handler #(re-frame/dispatch [:tasks/handle-error-get-tasks %])))))
```
--

### SPA with Re-frame and Reagent - List all tasks

At the and edit a list of tasks `src/todomvc/client/tasks/components.cljs`:

```clojure
(ns todomvc.client.tasks.components)

(defn todo-list
  [tasks]
  [:ul.todo-list
   (for [task tasks] ;; <-- render sequence
     ^{:key (:id task)}
     ;; <-- every element in a sequence must has a unique ID (needed by React)
     [:li
      [:div.view
       [:input {:type "checkbox", :class "toggle"}]
       [:label (:title task)]
       [:button.destroy]]
      [:input {:type "text", :class "edit", :id (str "task-" (:id tasks))}]])])
```
--

### SPA with Re-frame and Reagent - List all tasks

At the and edit a list of tasks `src/todomvc/client/tasks/components.cljs`:

```clojure
(defn main
  [tasks]
  [:section.main
   [:input {:type "checkbox"
            :name "toggle"
            :class "toggle-all"
            :id "toggle-all"}]
   [:label {:for "toggle-all"} "Mark all as complete"]
   [todo-list tasks]])
```
--

### SPA with Re-frame and Reagent - List all tasks

At the and edit a list of tasks `src/todomvc/client/tasks/components.cljs`:

```clojure
(defn todomvc-wrapper
  [db]
  (let [tasks (-> db :tasks :list)]
    [:div.todomvc-wrapper
     [:section.todoapp
      [:header.header
       [:h1 "todos"]
       [:input {:class "new-todo"
                :placeholder "What needs to be done?"
                :auto-focus true}]]
      (when (seq tasks)
        [main tasks])]]))
```

--

### SPA with Re-frame and Reagent - List all tasks

* Just refresh the page and you suppose to see a list of tasks
* You can checkout to tag `task-list` and see the result in the example project

--

### SPA with Re-frame and Reagent - Preparation for 7. task

A handler for binding a value from an input into the DB `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/update-new-task
  (fn [db [_ title]]
    (assoc-in db [:tasks :new-task] title)))
```
--

### SPA with Re-frame and Reagent - Preparation for 7. task

We need to dispatch an event when the input is changed `src/todomvc/client/tasks/components.cljs`:

```clojure
(ns todomvc.client.tasks.components
  (:require
    [goog.events.KeyCodes :as KeyCodes]
    [re-frame.core :as re-frame]))
```
--

### SPA with Re-frame and Reagent - Preparation for 7. task

We need to dispatch an event when the input is changed `src/todomvc/client/tasks/components.cljs`:

```clojure
(defn todomvc-wrapper
  [db]
  (let [tasks (-> db :tasks :list)]
    [:div.todomvc-wrapper
     [:section.todoapp
      [:header.header
       [:h1 "todos"]
       [:input {:class "new-todo"
                :placeholder "What needs to be done?"
                :auto-focus true
                :on-key-up #(when (= KeyCodes/ENTER (.-keyCode %))
                              (js/console.log % "Enter"))
                ;; <-- dispatch an event when Enter key is pressed
                :on-change #(re-frame/dispatch [:tasks/update-new-task
                                                (-> % .-target .-value)])}]]
                ;; <-- dispatch an event on-change
      (when (seq tasks) [main tasks])]]))
```

--

### SPA with Re-frame and Reagent - Task No. 7

Create a new task and update a list of tasks

Hint: Sending POST request

```clojure
(POST "/api/tasks"
  :params request-body
  ...
```
--

### SPA with Re-frame and Reagent - Task No. 7 - Solution

Create a new task and update a list of tasks

Let's create handlers `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/create-task
  [m/create-task]
  (fn [db _]
    db))

(re-frame/reg-event-db
  :tasks/handle-create-task
  [m/get-tasks]
  (fn [db _]
    (assoc-in db [:tasks :new-task] nil))) ;; <-- erase the input's value
```
--

### SPA with Re-frame and Reagent - Task No. 7 - Solution

Create a new task and update a list of tasks

Let's create handlers `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/handle-error-create-task
  (fn [db [_ response]]
    db))
```

--

### SPA with Re-frame and Reagent - Task No. 7 - Solution

Create a new task and update a list of tasks

Let's create a middleware for sending new task `src/todomvc/client/tasks/middlewares.cljs`:

```clojure
(def create-task
  (re-frame/after
    (fn [db _]
      (POST "/api/tasks"
            :params {:title (-> db :tasks :new-task)}
            :format :json
            :response-format :json
            :keywords? true
            :handler #(re-frame/dispatch [:tasks/handle-create-task %])
            :error-handler #(re-frame/dispatch [:tasks/handle-error-create-task %])))))
```

--


### SPA with Re-frame and Reagent - Task No. 7 - Solution

At the and alter the component `src/todomvc/client/tasks/components.cljs`:

```clojure
(defn todomvc-wrapper
  [db]
  (let [tasks (-> db :tasks :list)
        new-task (-> db :tasks :new-task)]
    [:div.todomvc-wrapper
     [:section.todoapp
      [:header.header
       [:h1 "todos"]
       [:input {:class "new-todo"
                :placeholder "What needs to be done?"
                :auto-focus true
                :value new-task ;; <-- set up a value from the DB
                :on-key-up #(when (= KeyCodes/ENTER (.-keyCode %))
                              (re-frame/dispatch [:tasks/create-task]))
                              ;; <-- create a task when Enter is pressed
                :on-change #(re-frame/dispatch [:tasks/update-new-task
                                                (-> % .-target .-value)])}]]
      (when (seq tasks)
        [main tasks])]]))
```
--

### SPA with Re-frame and Reagent - Validation

Let's reuse a code for validation from backend, move schema to `cljc` file `src/todomvc/tasks/schemas.cljc`:

```clojure
(ns todomvc.tasks.schemas
  (:require
    [struct.core :as st]))

(def Task
  {:title [st/required]
   :description [st/string]})
```

--

### SPA with Re-frame and Reagent - Validation

Edit handler `src/todomvc/client/tasks/events.cljs`:

```clojure
(ns todomvc.client.tasks.events
  (:require
    [struct.core :as st]
    [todomvc.client.tasks.middlewares :as m]
    [todomvc.tasks.schemas :refer [Task]]))


 (re-frame/reg-event-db
   :tasks/create-task
   [m/create-task]
   (fn [db _]
    (let [[errors model] (st/validate {:title (-> db :tasks :new-task)} Task)]
      (assoc-in db [:tasks :errors] errors)))) ;; <-- set up errors into the DB
```
--

### SPA with Re-frame and Reagent - Validation

Wrap a middleware into a condition `src/todomvc/client/tasks/middlewares.cljs`:

```clojure
(def create-task
   (re-frame/after
     (fn [db _]
      (when (-> db :tasks :errors nil?)
        (POST "/api/tasks"
              :params {:title (-> db :tasks :new-task)}
              :format :json
              ...
```
--

### SPA with Re-frame and Reagent - Validation

Alter the component `src/todomvc/client/tasks/components.cljs`:

```clojure
(defn todomvc-wrapper
  [db]
  (let [tasks (-> db :tasks :list)
        new-task (-> db :tasks :new-task)
        error (-> db :tasks :errors :title)] ;; <-- get an error
    [:div.todomvc-wrapper
     [:section.todoapp
      [:header.header
       [:h1 "todos"]
       [:input {:class "new-todo"
                :placeholder "What needs to be done?"
                :auto-focus true
                :value new-task
                :on-key-up #(when (= KeyCodes/ENTER (.-keyCode %))
                              (re-frame/dispatch [:tasks/create-task]))
                :on-change #(re-frame/dispatch [:tasks/update-new-task
                                                (-> % .-target .-value)])}]
       (when error ;; <-- and display the error
         [:div {:style {:color "red", :text-align "center"}} error])]
      (when (seq tasks) [main tasks])]]))
```
--


### References

* [http://www.braveclojure.com/](http://www.braveclojure.com/)
* [https://clojuredocs.org/](https://clojuredocs.org/)
* [https://github.com/clojure/clojurescript](https://github.com/clojure/clojurescript)
* [http://cljs.info/cheatsheet/](http://cljs.info/cheatsheet/)
* [https://github.com/duct-framework/duct](https://github.com/duct-framework/duct)
* [https://github.com/ring-clojure/ring](https://github.com/ring-clojure/ring)
* [https://github.com/weavejester/compojure](https://github.com/weavejester/compojure)
* [https://github.com/Day8/re-frame](https://github.com/Day8/re-frame)
* [https://reagent-project.github.io/](https://reagent-project.github.io/)
* [https://github.com/razum2um/awesome-clojure](https://github.com/razum2um/awesome-clojure)

--

### Next tasks

* Create a footer by [http://todomvc.com](http://todomvc.com)
* Implement filters into the footer
* Implement finishing a task
* Implement removing a task
* Implement editing a task's title
