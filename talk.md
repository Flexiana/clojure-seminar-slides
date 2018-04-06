title: Úvod do jazyka Clojure/ClojureScript
author:
  name: Lukáš Rychtecký
  twitter: lukasrychtecky
controls: true
style: style.css

--

# Úvod do jazyka Clojure a ClojureScript
## Lukáš Rychtecký
## [https://twitter.com/LukasRychtecky](https://twitter.com/LukasRychtecky)

--

### Osnova

* Motivace
* Trocha teorie
* Syntaxe
* Základy jazyka a příklady
* ClojureScript - SPA

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

foo(bar)
```

--

### Pure functions

* funkce nemá žádné side effects (IO operace apod.)

```python
def count_items(result):
    return len(result.items)

map(count_items, results)  # [1, 2, 4, 1]
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
