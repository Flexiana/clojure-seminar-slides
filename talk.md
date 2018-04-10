title: Clojure Full-stack aplikace
author:
  name: Lukáš Rychtecký
  twitter: @lukasrychtecky
controls: true
style: style.css

--

# Clojure Full-stack aplikace
## Lukáš Rychtecký
## [https://twitter.com/LukasRychtecky](https://twitter.com/LukasRychtecky)

--

### O mě

* [https://twitter.com/LukasRychtecky](https://twitter.com/LukasRychtecky)
* [https://github.com/LukasRychtecky](https://github.com/LukasRychtecky)

--

### Osnova

* Úvod do funkcionálního programování
* Clojure ekosystém
* Architektura, dependency injection, routing
* DB a migrace s Duct
* REST a Compojure
* SPA s Re-frame a Reagent (development stack)
* Funkční ukázka propojení všech technologií na příkladu

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int:
    return a + b
```

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int
    return a + b
```

Snadno

```python
assert 3 add(1, 2)
```

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int:
    return a + b
```

Snadno

```python
assert 3 add(1, 2)
```

Proč?

--

### Motivace

Jak otestujeme?

```python
def add(a: int, b: int) -> int:
    return a + b
```

Snadno

```python
assert 3 add(1, 2)
```

Proč?

Protože se jedná o `pure function` (matematická funkce)

Tzn. funkce, která pracuje pouze s argumenty a vrací výsledek

--

### Motivace

`Pure function` je tedy taková funkce, která:

* pracuje s argumenty
* vrací deterministický výsledek
* nemodifikuje ostatní proměnné -> proměnné jsou immutabilní == konstanty

--

### Motivace - Co jsou to side effects?

* modifikace proměnné z okolí funkce
* IO operace (práce s diskem, DB, HTTP, apod.)

Příklad:
```python
for a in [1, 2, 3, 4]:
    print(a)
```

Jak to otestujeme?

--

### Motivace - Kořeny

* kořeny v roce 1930
* jiný styl přemýšlení nad problémem -> transformace dat
* John McCarthy vymyslel LISP pro AI

![LISP creator](img/John_McCarthy_Stanford.jpg)

--

### Motivace - Současnost

* Clojure (Rich Hickey, 2007, LISP dialect, JVM)
* ClojureScript (Clojure, Javascript machine)

![Clojure creator](img/Rich-Hickey.jpeg)

--

### Motivace - Clojure

* dynamický statický typový
* immutabilní struktury
* pokročilá práce s kolekcemi (vector, linked list, set, hashmap)
* paralelní programování
* lazy sekvence
* praktický jazyk (side efekty)

--

#### Pilíře funkcionálního programování

* first-class and higher-order functions
* pure functions
* lazy evaluation
* deklarativní styl programování

--

### First-class and higher-order functions

* funkci lze předat jako parametr jiné funkci
* funkce může vracet jinou funkci

```python
def foo(func):
    return func(1, 3)

def bar(a, b):
    # do stuff
    # ...

foo(bar)
```

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
--

### Pure functions

* funkce nemá žádné side effects (IO operace apod.)

```clojure
(defn count-items
  [result]
  (count (:items result)))

map(count-items results)  ;; [1, 2, 4, 1]
```

--

### Lazy evaluation

* výrazy a iterace se provedou až ve chvíli, kdy jsou potřeba
* snadno lze pracovat s nekonečnou sekvencí

```clojure

(take 3 infinity-iter) ; [1 4 4]

(take 2 infinity-iter) ; [1 5]
```

--


### Předsudky

#### Když je všechno immutabilní, tak mi musí dojít brzy paměť!

To není pravda. Datové struktury jsou implementované jako Persistent vector. Více třeba na
 [http://blog.higher-order.net/2009/02/01/understanding-clojures-persistentvector-implementation](http://blog.higher-order.net/2009/02/01/understanding-clojures-persistentvector-implementation)


--

### Předsudky

#### Nekonečná sekvence se přeci do paměti nevejde!

To je sice pravda, ale protože je vyhodnocování lazy, načítá se opravdu pouze to, s čím se pracuje.
Tzn. skrze celou aplikaci si mohu předávat iterátor nad milionem položek, ale v paměti je jen uložená pozice iterátoru.

--

### Nekonečná sekvence - příklad

Např.:

```clojure
(filter even? (range)) ; neudělá nic

(next (filter even? (range))) ; vrátí pouze 2 a dál nic nepočítá
```

Více třeba na [http://clojure-doc.org/articles/language/laziness.html](http://clojure-doc.org/articles/language/laziness.html)

--

### Jak na side efekty?

* minimalizovat je, mít co nejvíce pure functions
* pro pure functional jazyky - monády [https://wiki.haskell.org/Monad](https://wiki.haskell.org/Monad)

--

### Clojure ekosystém

* Clojure je postaveno na malých knihovnách
* Nejsou tu frameworky jako Django nebo Rails
* Programátor si musí projekt "poskládat sám"
* Větší volnost a větší zodpovědnost
* [https://github.com/razum2um/awesome-clojure](https://github.com/razum2um/awesome-clojure)

--

### Clojure ekosystém

* JVM - běhové prostředí
* [Leiningen](https://leiningen.org) - build and management tool (de facto standard)
* [Boot](https://github.com/boot-clj/boot) - build and management tool


Oba nástroje mají stejné funkcionality:
* dependency management
* REPL
* plugins
* builds
* šablony aplikací

Více info na [https://www.braveclojure.com/appendix-a/](https://www.braveclojure.com/appendix-a/)

ClojureScript

* [Lumo](https://github.com/anmonteiro/lumo) - NodeJS

--

### Úkol č. 1

Spusťte Leiningen REPL (`lein repl`)

* Vypište všechna sudá čísla od 0 do 10
* Napište funkci, která udělá sumu dané kolekce
* Napište funkci, která udělá sumu druhé mocniny sudých čísel od 0 do 10

Může se hodit - Clojure dokumentace [http://clojuredocs.org](http://clojuredocs.org/)

--

### Úkol č. 1 - Řešení

Spusťte Leiningen REPL (`lein repl`)

* Vypište všechna sudá čísla od 0 do 10
* `(filter even? (range 10))` nebo `(remove odd? (range 10))` `(0 2 4 6 8)`
* Napište funkci, která udělá sumu dané kolekce
* `(defn sum [coll] (reduce + coll))` a `(sum (range 10))` `45`
* Napište funkci, která udělá sumu druhé mocniny sudých čísel od 0 do 10
* `(reduce + (map #(* % %) (filter even? (range 10))))` `120`
* nebo čitelněji `(->> (range 10) (filter even?) (map #(* % %)) (reduce +))`

--

### Architektura a dependency injection s Duct

`Duct` je dependency injection framework

* komponentový systém postavený nad [Integrant](https://github.com/weavejester/integrant)
* deklarativní konfigurace
* life-cycle komponent
* hot-reload kódu

--

### Komponenta

* je to dispatch metoda s konfigurací
* life-cycle události: `prep`, `init`, `halt`, `resume`, `suspend`
* v 99 % si vystačíte s `init` a `halt`
* více informací [https://github.com/weavejester/integrant](https://github.com/weavejester/integrant)

--

### Komponenta - příklad konfigurace

```clojure
{:duct.core/project-ns myapp
 :duct.core/environment :production

 :duct.module/sql
 {:database-url #duct/env ["DB_URL" Str]}

 :myapp.handler/api ;; <-- název musí být totožný s ns, tzn. myapp/handler/api.clj
 {:auth-key #duct/env ["AUTH_KEY" Str :or "dummy-key"]
  :db #ig/ref :duct.database/sql
  :environment #ig/ref :duct.core/environment}

...
```

--


### Komponenta - příklad

```clojure
(ns myapp.handler.api
  (:require
    [clojure.java.io :as io]
    [compojure.core :refer [GET]]
    [integrant.core :as ig]))

(defmethod ig/init-key :myapp.handler/api
  [_ conf] ;; <-- první argument je název komponenty, druhý je konfigurace (hash-map)
  (GET "/example" [:as request]
    (io/resource "myapp/handler/example.html")))

```

--


### Ring

* Ring je implementace servletu - stará se o komunikaci mezi kódem aplikace a HTTP
[https://github.com/ring-clojure/ring](https://github.com/ring-clojure/ring)
* Handler - funkce, která dostane HTTP požadavek jako mapu a vrátí odpověď jako jinou mapu
* Request - mapa, která obsahuje klíče jako: `:headers`, `:uri`, `:body`, ...
* Response - mapa, která definuje odpověď, obsahuje tři klíče: `:status`, `:body` a `:headers`
* Middleware - funkce, vyššího řádu, která vylepší handler (upraví headers apod.)
* Na [clojars.org](https://clojars.org) najdete spoustu užitečných middlewarů
* Dokumentace na
  [https://github.com/ring-clojure/ring/wiki/Concepts](https://github.com/ring-clojure/ring/wiki/Concepts)

--


### Ring middleware - příklad

Middleware nastaví Content-Type

```clojure
(defn wrap-content-type
  [handler content-type]
  (fn [request] ;; <-- vracím nový handler
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

### Ring - util

* Namespace `ring.util` obsahuje spoustu šikovných funkcí
* `ring.util.response/content-type`, `ring.util.response/response`, ...

Příklad:

```clojure
(require '[ring.util.response :as response])

(defn handle-example
  [conf]
  (GET "/example" [:as request]
    (response/response "<h1>Hallo</h1>")))

```
--

### Compojure - routing

* Malá knihovna pro deklarativní routing

```clojure
(require '[compojure.core :refer [GET context routes]])

(GET "/example" [:as request]
  (io/resource "myapp/handler/example.html")))
```

Destructuring URL parametrů
```clojure
(GET "/example/:id" [id :as request]
  (io/resource "myapp/handler/example.html")))
```

Destructuring URL a query parametrů
```clojure
(GET "/example/:id" [id foo :as request] ;; <-- foo není v path, vytáhne se z query parameters
  (io/resource "myapp/handler/example.html")))
```

--

### Compojure - routing ukázky

`context` redukuje duplikovaní parent části path
```clojure
(context "/example"
  (GET "/foo" [:as request]
    "<h1>foo</h1>")

  (GET "/bar" [:as request]
    "<h1>bar</h1>"))
```

Vygeneruje handlery pro `/example/foo` a `/example/bar`

--

### Compojure - routing ukázky

`routes` umožňuje spojit několik handlerů do jednoho
```clojure
(def app
  (routes
    (GET "/foo" [:as request]
      "<h1>foo</h1>")
    (GET "/bar" [:as request]
      "<h1>bar</h1>")))
```

--

### Úkol č. 2

* Vygenerujte projekt `duct` šablony:
```sh
lein new duct todomvc +api +cljs +example +postgres +site
cd todomvc
lein duct setup
```

* Do `project.clj` klíče `:profiles/dev` dejte `{:plugins [[venantius/ultra "0.5.1"]]}` - přidá barvy do REPLu
* `lein repl` - spustí REPL projektu
*
```clojure
(dev) ;; načte dev namespace
(reset) ;; reloadne kód a restartuje server
```
* Zkuste [http://localhost:3000/example](http://localhost:3000/example)
* Vložte do konfigurace `todomvc.handler/api` novou hodnotu `:name` a tu vraťte v odpovědi.
* Vraťte v odpovědi URL a query parametr.

--

### Úkol č. 2 - řešení

resources/todomvc/config.edn

```clojure
...
 :todomvc.handler/api
 {:db #ig/ref :duct.database/sql
  :name "Clojure"}}
```

src/todomvc/handler/example.clj

```clojure
(defn handle-hello
  [conf]
  (GET "/hello" [url-param query-param :as request]
    (response/response (format "<h1>Hello %s, :param %s, :query-param %s</h1>" (:name conf) url-param query-param))))
```
--

;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;

### ClojureScript + ReactJS = Reagent

* [Reagent](https://reagent-project.github.io/) - wrapper nad ReactJS, knihovna pro tvorbu UI
* [Re-frame](https://github.com/Day8/re-frame) - "MVC" framework pro UI web. aplikace

#### Flow

```text
View -> dispatch -> Handler -> Subscriber
|                                        |
     <-                      <-
```

--

### Reagent + Re-frame: views

* definuje UI

Pure view

```clojure
(defn hello-view
  [name]
  [:h1 "Hello " name])
```

Tohle už není pure view

```clojure
(defn hello-view
  []
  (let [name (subscribe [:name])]
    (fn []
      [:h1 "Hello " @name])))
```

--

### Reagent + Re-frame: handlers

Handler + side effect

* "klasický" handler na události

```clojure
(register-handler
  (after write-to-log) ; <-- side effect, zůstává mimo hlavní logiku
  :set-name
  (fn [db [_ name]]
    (assoc db :name name)))
```

--

### Reagent + Re-frame: subscribers

Subscriber

* pohled na data

```clojure
(register-sub
  :name
  (fn [db _]
    (reaction (get @db :name))))
```

--

### Thanks for your attention

* [http://www.braveclojure.com/](http://www.braveclojure.com/)
* [https://clojuredocs.org/](https://clojuredocs.org/)
* [https://github.com/clojure/clojurescript](https://github.com/clojure/clojurescript)
* [http://cljs.info/cheatsheet/](http://cljs.info/cheatsheet/)
* [https://github.com/Day8/re-frame](https://github.com/Day8/re-frame)
* [https://reagent-project.github.io/](https://reagent-project.github.io/)