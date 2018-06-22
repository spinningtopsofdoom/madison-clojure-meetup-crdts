# Visualization
## Merging Data Structures

```clojure
(let [car #{:car}
      hose #{:hose}
      coat #{:coat}])
(= (conj (conj car hose) coat)
   (conj (conj hose coat) car))
```

# Fruit Counter

```clojure
(let [a {:apple 5 :orange 6}
      b {:orange 4 :pear 3}
      c {:pear 5 :apple 2}])
  (= (fruit-count a b c) ;; 16  
     (fruit-count b a c))
```

# Benefits
* All information is in one place
* Can play around with it locally
* Deal with values instead of history

# Idempotent
## Multiple actions same effect
* Painting a fence the same color
* Opening a door
b - a => r
b - a - a - a - a => r

# Enabler
* Multiple retries
* Replace a slow / crashed server

# Naturally Idempotent
* Adding items to a set
* Taking the maximum number

# Idempotent Operations
## Unique identifier
```clojure
;; Non Idempotent
(conj [2 3 3] 2) ;; => 10
;; Idempotent
(conj {:a 2 :b 3 :c 3} {:a 2}) ;; => 8
```

# Before, After & During
# Events vs messages
## Wall Clock Time
```clojure
{:cart {:pear 1}} ;;; 1980-08-03 11:34:55
```

# Physical Shortcomings
* Each server has different time skew
* Human Standards (e.g. Daylight Savings Time)

# Logical Shortcomings
* Created `{:pear 1}` at 1980-08-03 11:34:55
* Message `{:pear 1} arrived at Alice at 1980-08-03 11:32:30
* Message `{:pear 1} arrived at Bob at 1980-08-03 11:35:22

# Before and After
* Before 1980-08-03 11:34:55 `{}`
* After 1980-08-03 11:34:55 `{:pear 1}`

# No Absolute Time
## Before & After Causality
* Events (e.g. created `{:pear 1}`)
* Messages (e.g. received call from Alice)

# Lamport Timestamps
{:a 0}---{:a 1}--------{:a 2}-----------------{:a 2 :b 1}
                \                             /
-------{:b  0}---{:a 1 :b 0}----{:a 1 :b :1}-----------

Tracks Causality what has happened before and after

# Lamport Rules
* Each Client Unique ID
* Logical (Integer) Clock
* Increment after every event
* Send Clock with every message
* Merge by taking maximum time for each client

# Neither Before or After
* `{:a 1 :b 0}` < `{:a 1 :b 1}`
* `{:a 0 :b 1}` < `{:a 1 :b 1}`
* `{:a 1 :b 0}` ? `{:a 0 :b 1}`

# Before, After and Concurrent

# Total Order
Everything is either smaller or larger than you
2---------------5---------------------9

# Single Time Line
11:10 -> 11:11 -> 11:12 -> 11:13

# Partial Order
Elements can be smaller, larger,or indeterminate

# Multiple Time Lines
* `{:a 0}` -> `{:a 0 :b 0}` -> `{:a 1 :b 0}` -> `{:a 1 :b 1}`
* `{:a 0}` -> `{:a 0 :b 1}` -> `{:a 1 :b 1}`
* `{:a 0}` -> `{:a 1}` -> `{:a 1 :b 0}` -> `{:a 1 :b 1}`

# Monotonic
Always add information, either data or meta-data

# Information never removed
```clojure
(disj (conj #{:b :c} :a) :a) ;; #{:b :c :a}
(conj (disj #{:b :c} :a) :a) ;; #{:b :c}
```

# Size can decrease

```clojure
(let [big-george #{:a :b :c}
      medium-george #{:a :c}
      little-george #{:b}]
  (= (intersection big-george medium-george little-george)
     (intersection little-george big-george medium-george)))
```

# Removing Items
Deletion data set, tombstones, meta data

# Deletion data set
Two sets one for additions and one for deletions

```clojure
(let [additions #{:apple :pear}
      deletions #{:apple}]
      (difference additions deletions))
```
