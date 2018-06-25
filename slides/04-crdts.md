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
