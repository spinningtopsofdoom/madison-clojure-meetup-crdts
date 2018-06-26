# CRDTS
## Unordered Sets, Unordered Maps, Ordered Text

# Sets
## Simplest first CRDT's
```clojure
(union #{:a :b} #{:b :c} #{:d})
#{:a :b :c :d}
```
# Deletion
## Semantics of the operation
```clojure
(let [alice {:add #{:a} :delete #{:a}}
      bob {:add #{:a}}
      carol {:delete #{:a}}]
  (= (merge alice bob carol)
     (merge carol bob alice)
     (merge bob alice carol)))
```

# Observe Remove
An item can only be removed after it's been observed. Each item is uniquely identified by the client name and time stamp
```clojure
(let [alice {:add #{[:a 0 :a]} :delete #{[:a 0 :a]}}
      bob {:add #{[:b 0 :a]}}
      carol {}]
  (= (merge alice bob carol)
     (merge carol bob alice)
     (merge bob alice carol)))
#{:add #{[:b 0 :a]}}
```
# Maps
## Set of Key Value Pairs :)

# Map Updates
## More than one client updates a key at the same time

# Arbitrary Tiebreaker
## Client ID and timestamp
```clojure
(let [alice #{[:a 1 [:foo :bar]] [:a 3 [:foo :baz]]}
      bob #{[:b 0 [:foo :foo]]}]
  (sort-updates alice bob))
[[:b 0] [:a 1] [:a 3]] ;; min client
[[:a 1] [:a 3] [:b 0]] ;; max client
```

# Ordered Text
## Easier than you would think

# Unique Identifiers
## "Hello"
## to
## [['H' 0] ['e' 1] ['l' 2] ['l' 3] ['o' 4]]

# Adding text
#### Add after identifier with new characters and identifiers

```clojure
(let [word [['b' 0] ['a' 1] ['r' 2]]
      added-text [0 [['e' 3]]]]
    (new-word added-text word))
[['b' 0] ['e' 3] ['a' 1] ['r' 2]]
```

# Concurrent Adds
## Same as Concurrent Map Updates

# Linear ID Growth
## ID size equal to the number of additions
### [['h' 8936634546] ['i' 8936634547]]

# Trees to the rescue
[][0 'h'] [0.0 'e'] [0.1 'l'] [1 'l'] [1.0 'o ']]
  [0 'h'] ---------[1 'l']
  /     \             /
[0 'e']  [1 'l']   [0 'o']
