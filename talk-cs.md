title: Clojure Full-stack aplikace
author:
  name: Lukáš Rychtecký
  twitter: lukasrychtecky
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
* Absolvent ČVUT FEL
* Programuji cca 10 let
* Java, CoffeeScript, Python
* Poslední 2 roky Clojure/ClojureScript
* Clean code

--

### Osnova

* Úvod do funkcionálního programování
* Clojure ekosystém
* Architektura, dependency injection, routing
* DB a migrace s Duct
* REST a Compojure
* Testování
* SPA s Re-frame a Reagent (development stack)

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

* dynamický silně typový
* immutabilní struktury
* pokročilá práce s kolekcemi (vector, linked list, set, hashmap)
* paralelní programování
* lazy sekvence
* praktický jazyk (side efekty)

--

### Pilíře funkcionálního programování

* first-class and higher-order functions
* pure functions
* lazy evaluation
* deklarativní styl programování

--

### First-class and higher-order functions

* funkci lze předat jako parametr jiné funkci
* funkce může vracet jinou funkci

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

* funkce nemá žádné side effects (IO operace apod.)

```clojure
(defn count-items
  [result]
  (count (:items result)))

(map count-items results)  ;; [1, 2, 4, 1]
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

--

### Clojure ekosystém

Leiningen i Boot mají stejné funkcionality:

* dependency management
* REPL
* plugins
* builds
* šablony aplikací

Více info na [https://www.braveclojure.com/appendix-a/](https://www.braveclojure.com/appendix-a/)

* ClojureScript [Lumo](https://github.com/anmonteiro/lumo) - NodeJS

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
--

### Úkol č. 1 - Řešení

* nebo čitelněji

```clojure
(->> (range 10)
      (filter even?)
      (map #(* % %))
      (reduce +))
```

--

### Architektura a dependency injection s Duct

`Duct` je dependency injection framework

* komponentový systém postavený nad [Integrant](https://github.com/weavejester/integrant)
* deklarativní konfigurace
* life-cycle komponent
* hot-reload kódu
* The Twelve-Factor App [https://12factor.net](https://12factor.net)

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

--

### Ring

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
--

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
(GET "/example/:id" [id foo :as request]
  ;; <-- foo není v path, vytáhne se z query parameters
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

* Pro celý seminář je připraven vzorový projekt
  [https://github.com/Flexiana/clojure-seminar-todomvc](https://github.com/Flexiana/clojure-seminar-todomvc), ve kterém
se můžete přepínat pomocí tagů
* Vygenerujte projekt `duct` šablony:
```sh
lein new duct todomvc +api +example +postgres
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

--

### Úkol č. 2

* Zkuste [http://localhost:3000/example](http://localhost:3000/example)
* Vložte do konfigurace `todomvc.handler/example` novou hodnotu `:name` a tu vraťte v odpovědi.
* Vraťte v odpovědi URL a query parametr.

--

### Úkol č. 2 - řešení

`resources/todomvc/config.edn`

```clojure
...
 :todomvc.handler/example
 {:db #ig/ref :duct.database/sql
  :name "Clojure"}}
```

`src/todomvc/handler/example.clj`

```clojure
(defn handle-hello
  [conf]
  (GET "/hello/:url-param" [url-param query-param :as request]
    (response/response (format "<h1>Hello %s, :param %s, :query-param %s</h1>"
                               (:name conf)
                               url-param
                               query-param))))
```
--

### DB a migrace s Duct - boundary

* Boundary je protokol, pomocí kterého se komunikuje s okolním světem (RDBS, Redis, HTTP calls, ...). Boundary explicitně
 definuje hranice mezi systémem a okolím.
* Umožňuje "vytlačit" side efekty na kraj systému
* Usnadňuje testování

--

### DB a migrace s Duct - boundary příklad

```clojure
(ns todomvc.tasks.boundary
  (:require
    [duct.database.sql] ;; <-- Duct modul, který se stará o připojení k DB
    [todomvc.tasks.db :as db])) ;; <-- ns s DB dotazy


(defprotocol TaskService ;; <-- "Interface" našeho boundary
  (get-all [db-spec]))


(extend-protocol TaskService ;; <-- A jeho implementace
  duct.database.sql.Boundary
  (get-all [db-spec]
    (db/get-all db-spec))) ;; <-- Tady se už volá konkrétní DB dotaz
```

Ten tajuplný `Boundary` je vlastně jenom record:

```clojure
(defrecord Boundary [spec])
```
--

### DB a migrace s Duct - boundary 2. příklad

Reálné volání DB:
```clojure
(get-all (->Boundary db))
;;({:description "uklidit" :done false :id 1 :title "Prvni ukol"}))
```

Nadefinujeme nový record

```clojure
(defrecord FakeBoundary [spec])

(extend-protocol TaskService
  FakeBoundary
  (get-all [db-spec]
    ["Nic nedostanes"]))
```
--

### DB a migrace s Duct - boundary 2. příklad

A řekneme si o všechny úkoly
```clojure
(get-all (->FakeBoundary db))
;;["Nic nedostanes"]
```
--

### DB a migrace s Duct - Ragtime

* Ragtime je knihovna na DB migrace
* Definice migrací přímo v konfiguraci nebo v SQL souborech
* [https://github.com/duct-framework/migrator.ragtime](https://github.com/duct-framework/migrator.ragtime)

--

### DB a migrace s Duct - Ragtime

Výhody:

* Žádné složité DSL
* Čisté SQL, syntax highlighting

Nevýhody:

* Trošku ukecaná konfigurace

--

### DB a migrace s Duct - Ragtime konfigurace

`resources/todomvc/config.edn`:

```clojure
{:duct.migrator/ragtime
 {:migrations [#ig/ref :todomvc.migration/add-tasks]}
 ;; <-- určuje pořadí migrací

 [:duct.migrator.ragtime/sql :todomvc.migration/add-tasks]
 ;; <-- pojmenování migrace
 {:up [#duct/resource "todomvc/migrations/001-add-tasks.up.sql"]
  ;; <-- dopředná migrace
  :down [#duct/resource "todomvc/migrations/001-add-tasks.down.sql"]}}
  ;; <-- zpětná migrace
```

--

### DB a migrace s Duct - Ragtime konfigurace

`resources/todomvc/migrations/001-add-tasks.up.sql`:
```sql
create table tasks (
    id serial primary key,
    title text not null,
    description text,
    done boolean default false not null)
```
* Migrace se automaticky aplikují když spustíte server přes REPL

--

### DB a migrace s Duct - HugSQL

* HugSQL je knihovna pro práci s RDBS
* [https://www.hugsql.org](https://www.hugsql.org/)
* Žádná abstrakce, čisté SQL

Alternativy:

* Clojure JDBC [http://funcool.github.io/clojure.jdbc/latest](http://funcool.github.io/clojure.jdbc/latest/) je základní
  knihovna, jednoduché dotazy nebo SQL ve stringu
* Korma [http://sqlkorma.com](http://sqlkorma.com/) dotazy pomocí DSL

--

### DB a migrace s Duct - HugSQL příklad

HugSQL nabinduje SQL dotazy do Clojure funkcí (pomocí spec. anotací)

`src/todomvc/tasks/db.sql`:

```sql
\-- :name get-all :? :*
select * from tasks
```
Poznámka: Ten \ tam být nemá, ale Markdown to nechce jinak zobrazit

--

### DB a migrace s Duct - HugSQL příklad

`src/todomvc/tasks/db.clj`:
```clojure
(ns todomvc.tasks.db
  (:require
    [hugsql.core :as hugsql]))

(hugsql/def-db-fns "todomvc/tasks/db.sql") ;; <-- naimportuje SQL dotazy ze souboru
(hugsql/def-sqlvec-fns "todomvc/tasks/db.sql")
;; <-- šikovné během vývoje, ukáze celý dotaz (vyzkoušíme za chvilku)
```
--

### DB a migrace s Duct - REPL

* Vytvořte si DB (PostgreSQL) (např. `createdb todomvc -O <váš-uživatel>`)
* Přepněte se na tag `priprava-ukol3` (`git reset --hard priprava-ukol3`)

--

### DB a migrace s Duct - REPL

* Nastavte si připojení do DB `export DB_URL="jdbc:postgresql://localhost/todomvc?user=root&password=toor"` nebo si
  přidejte do `dev/resources/local.edn` tento řádek `:duct.module/sql {:database-url #duct/env ["DB_URL" Str :or "jdbc:postgresql://localhost/todomvc?user=root&password=toor"]}`
* Nastartujte REPL `lein repl`
* Přepněte se do dev profilu `(dev)`
* Spusťte aplikaci `(reset)`

--

### DB a migrace s Duct - REPL

* V konzoli by se mělo vypsat něco jako `:duct.migrator.ragtime/applying :todomvc.migration/add-tasks#491d1e44`
* Teď můžete ovládat celou aplikaci přímo z konzole
--

### DB a migrace s Duct - REPL a DB dotazy 1.

* V proměnné `config` máte aktuální konfiguraci
* Změny v kódu se hot-swapnout když zavoláte znovu `(reset)`
* Uložte si připojení k DB `(def db-spec (-> config :duct.module/sql :database-url))`
* Naimportujte DB dotazy `(require '[todomvc.tasks.db :as db])`

--

### DB a migrace s Duct - REPL a DB dotazy  2.

* Získejte všechny úkoly z DB `(db/get-all db-spec)`
* POZOR DB dotazy ze SQL se neaktualizují, po `(reset)` je nutné i reload namespacu `(require '[todomvc.tasks.db :as db] :reload)`
* Zobrazení SQL dotazu `(db/get-all-sqlvec)`

--

### Úkol č. 3

* Přidejte funkci `create-task`, která vezme mapu s klíči `:title` a `:description`
* Přijdete funkci `finish-task`, která označí úkol jako vyřízený

--

### Úkol č. 3 - Řešení

* Přidejte funkci `create-task`, která vezme mapu s klíči `:title` a `:description`

```sql
\-- :name create-task :<! :1
insert into tasks (title, description) values (:title, :description) returning *
```

* V REPLu musím resetnout systém `(reset)` a reloadnout namespace `(require '[todomvc.tasks.db :as db] :reload)`

```clojure
(db/create-task-sqlvec {:title "Umyt nadobi", :description "HNED!"})
["insert into tasks (title, description) values ( ? , ? ) returning *" "Umyt nadobi" "HNED!"]

(db/create-task db-spec {:title "Umyt nadobi", :description "HNED!"})
```
--

### Úkol č. 3 - Řešení

* Přijdete funkci `finish-task`, která označí úkol jako vyřízený

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


### REST a Compojure

Definice routy pro /api/tasks:

`src/todomvc/handler/api.clj`

```clojure
(ns todomvc.handler.api
  (:require
    [compojure.core :refer [context GET]]
    [integrant.core :as ig]
    [ring.middleware.json :refer [wrap-json-response]]
    [todomvc.tasks.api :as tasks]))


(defmethod ig/init-key :todomvc.handler/api ;; <-- nová komponenta pro API
  [_ conf]
  (wrap-json-response ;; <-- chceme vracet odpověď jako JSON
    (context "/api" []
      (context "/tasks" []
        (GET "/" [:as request]
          (tasks/get-tasks conf request)))))) ;; <-- samotný handler
```
--

### REST a Compojure

Middleware musíme přidat do závislostí projektu, upravím `project.clj` a přidáme do `:dependencies`
`[ring/ring-json "0.4.0"]`.


--

### REST a Compojure - Vytvoření úkolu

Komponentu musíme zaregistrovat v `resources/todomvc/config.edn`:

```clojure
...

 :todomvc.handler/api
 {:db #ig/ref :duct.database/sql}

 :duct.router/cascading
 [#ig/ref :todomvc.handler/api]
;; <-- tento řádek už tam je, jenom nahraďte původní handler
```
--


### REST a Compojure

Ještě implementace našeho handleru `src/todomvc/tasks/api.clj`:

```clojure
(ns todomvc.tasks.api
  (:require
    [todomvc.tasks.boundary :as db]))

(defn get-tasks
  [conf request]
  {:body (db/get-all (:db conf)), :status 200})
```
--


### REST a Compojure

* Spusťte REPL a restartujte server
* Zavolejte `curl http://localhost:3000/api/tasks` a měli byste vidět seznam úkolů

--

### REST a Compojure - Vytvoření úkolu

Přidáme handler na vytvoření úkolu `src/todomvc/handler/api.clj`:

```clojure
(ns todomvc.handler.api
  (:require
    [compojure.core :refer [context GET POST]] ;; <-- naimportujeme POST
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
        (POST "/" [:as request] ;; <-- handler na POST
          (tasks/create-task conf request))))))
```

--

### REST a Compojure - Vytvoření úkolu

Naimplementujeme handler `src/todomvc/tasks/api.clj`:

```clojure
(defn create-task
  [conf request]
  (let [new-task (db/create-task (:db conf) (:body-params request))]
    {:body new-task, :status 201}))
```

Zkontrolujeme protokol TaskService, měl by vypadat nějak tato:

```clojure
(defprotocol TaskService
  (get-all [db-spec])
  (create-task [db-spec task]))
```

--

### REST a Compojure - Vytvoření úkolu

Teď by mělo stačit restartovat server `(reset)` a udělat POST request na adresu `http://localhost:3000/api/tasks`

```json
{"title": "Uklit byt", "description": "Vynést koď, vyluxovat, ..."}
```

--

### REST a Compojure - Validace

* Vstup dat je potřeba validovat nejen z bezpečnostních důvodů a s tím nám pomůžu knihovna Struct
[http://funcool.github.io/struct/latest/](http://funcool.github.io/struct/latest/)
* Validační model - čisté datové struktury, žádná makra
* Přidejme `[funcool/struct "1.2.0"]` do závislostí a restartujme REPL

--

### REST a Compojure - Validace

Ukázka použití:

```clojure
(require '[struct.core :as st])

(st/validate {:name nil} ;; <-- vstupní data pro validaci
             {:name [st/required]}) ;; <-- validační model
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

### REST a Compojure - Validace

Knihovna podporuje i coercing:

```clojure
(st/validate {:age "20"} ;; <-- věk je tu jako string
             {:age [st/required st/integer-str]}) ;; <-- ale my to chceme jako integer
[nil {:age 20}] ;; <-- voilà máme zde integer
```

Můžeme zahazovat i klíče, které neznáme:
```clojure
(st/validate {:age "20", :foo "bar"}
             {:age [st/required st/integer-str]}
             {:strip true})
[nil {:age 20}] ;; <-- dostaneme pouze ty klíče, o které vyžadujeme v modelu
```

--

### Úkol č. 4

* Upravte funkci `create-task` tak, aby validovata vstup: `:title` - povinné pole, `:description` - nepovinné pole (nemusí být v requestu)

--

### Úkol č. 4 - Řešení

* Upravte funkci `create-task` tak, aby validovata vstup: `:title` - povinné pole, `:description` - nepovinné pole (nemusí být v requestu)

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
                             ;; <-- HugSQL vyžaduje všechny atributy, i ty nilové
       :status 201}
      {:body {:errors errors}, :status 400})))
```

--

### Úkol č. 5

* Přidejte handler, který označí úkol jako dokončený

--

### Úkol č. 5 - Řešení

* Přidejte handler, který označí úkol jako dokončený

`src/todomvc/tasks/api.clj`

```clojure
(PATCH "/:id" [id :as request]
  (tasks/finish-task conf request))
```

--

### Úkol č. 5 - Řešení

* Přidejte handler, který označí úkol jako dokončený

`src/todomvc/tasks/api.clj`

```clojure
(defn finish-task
  [conf request]
  (let [[errors input] (st/validate (:params request) {:id [st/required st/integer-str]}) ;; <-- id musí být integer
        task (db/finish-task (:db conf) (:id input))]
    (if (nil? task)
      {:status 404}
      {:status 200, :body task})))
```

--


### Testování

K testování nám bude stačit standardní modul `clojure.test`

```clojure
(ns todomvc.foo-test
  (:require
    [clojure.test :refer [deftest is testing]]))

(deftest foo-test
  (testing "should add two numbers"
    (is (= 2 (+ 1 1)))))
```

--

### Testování - API

Otestujeme `get-tasks` handler skrze HTTP `test/todomvc/tasks/api_test.clj`:

```clojure
(ns todomvc.tasks.api-test
  (:require
    [clojure.test :refer [deftest is]]
    [cheshire.core :as cheshire] ;; <-- budeme parsovat JSON response
    [integrant.core :as ig]
    [ring.mock.request :as mock] ;; <-- budeme mockovat Ring request
    [todomvc.tasks.api]
    [todomvc.tasks.boundary :refer [TaskService]]))
```
--

### Testování - API

Otestujeme `get-tasks` handler skrze HTTP `test/todomvc/tasks/api_test.clj`:

```clojure
(defn json-response ;; <-- helper funkce na aplikaci requestu a parsování JSON response
  ([conf method path]
   (json-response conf method path nil))
  ([conf method path body] ;; <-- dvě arity, příprava na další úkol
   (let [handler (ig/init-key :todomvc.handler/api conf)
         request (mock/request method path)]
     (-> (if (nil? body)
           request
           (assoc request :body-params body))
         handler
         (update :body #(some-> % (cheshire/decode true)))))))

(deftest get-tasks-test
  (let [conf {:db (reify TaskService ;; <-- "mock" databáze
                    (get-all [db-spec]
                      []))}
        request (mock/request :get "/api/tasks")
        response (json-response conf request)]
    (is (= 200 (:status response)))
    (is (= [] (:body response)))))
```

Restartujeme server `(reset)` a pustíme testy `(test)`

--

### Úkol č. 6

* Napište test na vytvoření úkolu

--

### Úkol č. 6 - Řešení

* Napište test na vytvoření úkolu

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
(reg-db-event
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
(reg-sub
  :name
  (fn [db _]
    (:name db))))
```

--

### SPA s Re-frame a Reagent - Konfigurace

Upravíme `resources/todomvc/config.edn`:

```clojure
 :duct.middleware.web/defaults {:static {:resources "todomvc/public"}}
 ;; <-- přidáme, servírování static. souborů

  :duct.router/cascading
 [#ig/ref :todomvc.handler/api
  #ig/ref :todomvc.handler/site] ;; <-- přidáme další routy

 :todomvc.handler/site {} ;; <-- přidáme
```

--

### SPA s Re-frame a Reagent - Konfigurace

Vytvoříme index stránku `resources/todomvc/pages/index.html`:

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

### SPA s Re-frame a Reagent - Konfigurace

Přidáme vstupný bod pro vývoj `dev/src/client.cljs`:

```clojure
(ns dev.client
  (:require
    [figwheel.client :as fw]
    [re-frisk.core :refer [enable-re-frisk!]]
    [todomvc.client.main]))

(enable-console-print!)
(enable-re-frisk!)


(fw/start {:on-jsload todomvc.client.main/init
           :websocket-url "ws://localhost:3449/figwheel-ws"}) ;; <-- nastaví hot reload
```

--

### SPA s Re-frame a Reagent - Konfigurace

Přidáme vstupní bod pro CLJS `src/todomvc/client/main.cljs`:

```clojure
(ns todomvc.client.main)

(defn ^:export init
  []
  (let [app-element (.getElementById js/document "app")]
    (js/console.log "CLJS!" app-element)))
```
--

### SPA s Re-frame a Reagent - Konfigurace

Přidáme nové routy `src/todomvc/handler/site.clj`:

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
      ;; <-- vrátíme statickou stránku
      "text/html")))
```

--

### SPA s Re-frame a Reagent - Konfigurace

* Konfigurace v `project.clj` je trochu větší, přepněte se na další krok `git reset --hard cljs-konfigurace`
* Restartujeme REPL a server `(dev)`, `(reset)` a spustíme CLJS build `(figwheel-repl/start-figwheel!)`
* V konzoli by se mělo objevit něco jako `Successfully compiled build :dev to "resources/todomvc/public/js/main.js" in 2.999 seconds.`

--

### SPA s Re-frame a Reagent - Konfigurace

* Otevřeme prohlížeč [http://localhost:3000](http://localhost:3000/)
* Produkční build `lein min-app`
--

### SPA s Re-frame a Reagent - Výpis úkolů

Upravíme `src/todomvc/client/main.cljs`:

```clojure
(ns todomvc.client.main
  (:require
    [reagent.core :as reagent]
    [re-frame.core :as re-frame]
    [todomvc.client.events]
    [todomvc.client.subs] ;; <-- musíme někde naimportovat, jinak se nezaregistrují
    [todomvc.client.tasks.components :as components]
    [todomvc.client.tasks.events]))
    ;; <-- musíme někde naimportovat, jinak se nezaregistrují


(def init-db ;; <-- výchozí stav DB
  {:tasks {:list nil
           :new-task nil}})

```
--

### SPA s Re-frame a Reagent - Výpis úkolů

Upravíme `src/todomvc/client/main.cljs`:

```clojure
(defn- main
  []
  (reagent/create-class
    {:display-name
     "main"

     :component-will-mount ;; <-- klasický React event handler
     #(re-frame/dispatch [:tasks/get-tasks])

     :reagent-render
     (fn []
       (let [app (re-frame/subscribe [:app])] ;; <-- náš jediný subscriber na celou DB
         (fn []
           (components/todomvc-wrapper @app))))}))
           ;; <-- musí se dereferencovat! nechceme používat atom
```
--

### SPA s Re-frame a Reagent - Výpis úkolů

Upravíme `src/todomvc/client/main.cljs`:

```clojure
(defn ^:export init
  []
  (let [app-element (.getElementById js/document "app")]
    (re-frame/dispatch-sync [:init-db init-db]) ;; <-- nastavení výchozí DB při startu
    (re-frame/clear-subscription-cache!)
    (reagent/render [main] app-element)))
```

--

### SPA s Re-frame a Reagent - Výpis úkolů

Vytvoříme subscriber na DB `src/todomvc/client/subs.cljs`:

```clojure
(ns todomvc.client.subs
  (:require
    [re-frame.core :as re-frame]))


(re-frame/reg-sub
  :app
  identity)
```
--

### SPA s Re-frame a Reagent - Výpis úkolů

Vytvoříme handler na nastavení DB `src/todomvc/client/events.cljs`:

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

### SPA s Re-frame a Reagent - Výpis úkolů

Vytvoříme handler na získání úkolů `src/todomvc/client/tasks/events.cljs`:

```clojure
(ns todomvc.client.tasks.events
  (:require
    [re-frame.core :as re-frame]
    [todomvc.client.tasks.middlewares :as m]))


(re-frame/reg-event-db
  :tasks/get-tasks
  [m/get-tasks] ;; <-- side effect je mimo logiku
  (fn [db _]
    db)) ;; <-- neměníme stav aplikace


(re-frame/reg-event-db
  :tasks/handle-get-tasks
  (fn [db [_ tasks]]
    (assoc-in db [:tasks :list] tasks))) ;; <-- nastavíme úkoly do DB
```

--

### SPA s Re-frame a Reagent - Výpis úkolů

Vytvoříme handler na získání úkolů `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/handle-error-get-tasks
  (fn [db [_ response]]
    db)) ;; <-- zde bychom mohli nastavit třeba nějakou zprávu, kterou pak zobrazíme
```

--

### SPA s Re-frame a Reagent - Výpis úkolů

Vytvoříme middleware na získání úkolů `src/todomvc/client/tasks/middlewares.cljs`:

```clojure
(ns todomvc.client.tasks.middlewares
  (:require
    [ajax.core :refer [GET]]
    [re-frame.core :as re-frame]))


(def get-tasks
  (re-frame/after ;; <-- side effect zabalíme do `after` middlewaru
    (fn [db _]
      (GET "/api/tasks"
           :format :json
           :response-format :json
           :keywords? true
           :handler #(re-frame/dispatch [:tasks/handle-get-tasks %])
           ;; <-- odešleme událost se seznamem úkolů
           :error-handler #(re-frame/dispatch [:tasks/handle-error-get-tasks %])))))
```
--

### SPA s Re-frame a Reagent - Výpis úkolů

A nakonec upravíme výpis úkolů `src/todomvc/client/tasks/components.cljs`:

```clojure
(ns todomvc.client.tasks.components)

(defn todo-list
  [tasks]
  [:ul.todo-list
   (for [task tasks] ;; <-- renderujeme sekvenci
     ^{:key (:id task)}
     ;; <-- každý element v sekvenci musí mít unikátní ID (vyžaduje React)
     [:li
      [:div.view
       [:input {:type "checkbox", :class "toggle"}]
       [:label (:title task)]
       [:button.destroy]]
      [:input {:type "text", :class "edit", :id (str "task-" (:id tasks))}]])])
```
--

### SPA s Re-frame a Reagent - Výpis úkolů

A nakonec upravíme výpis úkolů `src/todomvc/client/tasks/components.cljs`:

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

### SPA s Re-frame a Reagent - Výpis úkolů

A nakonec upravíme výpis úkolů `src/todomvc/client/tasks/components.cljs`:

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

### SPA s Re-frame a Reagent - Výpis úkolů

* Teď stačí aktualizovat stránku a měli byste vidět seznam úkolů z vaší DB
* Pokud nechcete kód opisovat, stačí se přepnout na tag `task-list`

--

### SPA s Re-frame a Reagent - Příprava na 7. úkol

Handler na bindování hodnoty z inputu do DB `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/update-new-task
  (fn [db [_ title]]
    (assoc-in db [:tasks :new-task] title)))
```
--

### SPA s Re-frame a Reagent - Příprava na 7. úkol

Ještě musíme vyhodit událost při změně inputu `src/todomvc/client/tasks/components.cljs`:

```clojure
(ns todomvc.client.tasks.components
  (:require
    [goog.events.KeyCodes :as KeyCodes]
    [re-frame.core :as re-frame]))
```
--

### SPA s Re-frame a Reagent - Příprava na 7. úkol

Ještě musíme vyhodit událost při změně inputu `src/todomvc/client/tasks/components.cljs`:

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
                ;; <-- vyhodíme událost při zmáčknutí enteru
                :on-change #(re-frame/dispatch [:tasks/update-new-task
                                                (-> % .-target .-value)])}]]
                ;; <-- vyhodíme událost na on-change
      (when (seq tasks) [main tasks])]]))
```

--

### SPA s Re-frame a Reagent - Úkol č. 7

Vytvořte nový úkol a aktualizujte seznam úkolů

Nápověda: Odeslání POST požadavku

```clojure
(POST "/api/tasks"
  :params request-body
  ...
```
--

### SPA s Re-frame a Reagent - Úkol č. 7 - Řešení

Vytvořte nový úkol a aktualizujte seznam úkolů

Vytvoříme handlery `src/todomvc/client/tasks/events.cljs`:

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
    (assoc-in db [:tasks :new-task] nil))) ;; <-- vymaže input
```
--

### SPA s Re-frame a Reagent - Úkol č. 7 - Řešení

Vytvořte nový úkol a aktualizujte seznam úkolů

Vytvoříme handlery `src/todomvc/client/tasks/events.cljs`:

```clojure
(re-frame/reg-event-db
  :tasks/handle-error-create-task
  (fn [db [_ response]]
    db))
```

--

### SPA s Re-frame a Reagent - Úkol č. 7 - Řešení

Vytvořte nový úkol a aktualizujte seznam úkolů

Vytvoříme middleware na odeslání nového úkolu `src/todomvc/client/tasks/middlewares.cljs`:

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


### SPA s Re-frame a Reagent - Úkol č. 7 - Řešení

A nakonec upravíme komponentu `src/todomvc/client/tasks/components.cljs`:

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
                :value new-task ;; <-- nastavíme hodnotu z DB
                :on-key-up #(when (= KeyCodes/ENTER (.-keyCode %))
                              (re-frame/dispatch [:tasks/create-task]))
                              ;; <-- odešleme úkol na enter
                :on-change #(re-frame/dispatch [:tasks/update-new-task
                                                (-> % .-target .-value)])}]]
      (when (seq tasks)
        [main tasks])]]))
```
--

### SPA s Re-frame a Reagent - Validace

Využijeme kód na validaci z backendu a přesuneme schema do `cljc` souboru `src/todomvc/tasks/schemas.cljc`:

```clojure
(ns todomvc.tasks.schemas
  (:require
    [struct.core :as st]))

(def Task
  {:title [st/required]
   :description [st/string]})
```

--

### SPA s Re-frame a Reagent - Validace

Upravíme handler `src/todomvc/client/tasks/events.cljs`:

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
      (assoc-in db [:tasks :errors] errors)))) ;; <-- nastavíme chyby do DB
```
--

### SPA s Re-frame a Reagent - Validace

Middleware na odesílání zabalíme do podmínky `src/todomvc/client/tasks/middlewares.cljs`:

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

### SPA s Re-frame a Reagent - Validace

A drobně upravíme komponentu `src/todomvc/client/tasks/components.cljs`:

```clojure
(defn todomvc-wrapper
  [db]
  (let [tasks (-> db :tasks :list)
        new-task (-> db :tasks :new-task)
        error (-> db :tasks :errors :title)] ;; <-- vytáhneme si chybu
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
       (when error ;; <-- a zobrazíme ji
         [:div {:style {:color "red", :text-align "center"}} error])]
      (when (seq tasks) [main tasks])]]))
```

--


### Reference

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

### Další úkoly

* Dodělejte patičku podle [http://todomvc.com](http://todomvc.com)
* Naimplementujte filtery v patičce
* Naimplementujte dokončení úkolu
* Naimplementujte odstranění úkolu
* Naimplementujte editaci názvu úkolu
