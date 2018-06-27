# Thinking Distributedly

# Guidelines
* Visualization
* Idempotent
* Time
* Partial Orders
* Monotonic

# Fiddly Bits
## State, Meta Data, Combinatorial Orders, Duplication

# Visualization
## Merging Data Structures
All information and meta data that the CRDT is composed of

# Simple Set

```clojure
(let [car #{:car}
      hose #{:hose}
      coat #{:coat}])
(= (conj (conj car hose) coat)
   (conj car (conj (conj car hose) coat))
   (conj (conj hose coat) car))
```

# Fruit Counter

```clojure
(let [alice {:apple 5 :orange 6}
      bob {:orange 4 :pear 3}
      charlie {:pear 5 :apple 2}])
  (= (fruit-count alice bob charlie) ;; 16
     (fruit-count alice bob bob charlie)
     (fruit-count bob alice charlie))
```

# Benefits
* Ephemeral Details are made concrete
* Can play around with it locally
* Easy to change "network"

# Idempotent
## Multiple actions same effect
* Painting a fence the same color
* Opening a door
* Running one lap

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

# Time
## Before, After & During

# Wall Clock Time
```clojure
{ :created-at "1980-08-03 11:34:55"
  :cart {:pear 1}}
```

# Physical Shortcomings
* Each server has different time skew
* Human Standards (e.g. Daylight Savings Time)

# Logical Shortcomings{.hidden}
## Logical Shortcomings
![arrival times](./images/wall-clock-arrival-times.png)

# Before and After
`{}` - 1980-08-03 11:34:55 - `{:pear 1}`

# Logical Time
## Before & After Causality
* Events (e.g. created `{:pear 1}`)
* Messages (e.g. received call from Alice)

# Lamport Timestamps
![Tracks Causality what has happened before and after](./images/lamport-timestamps.png)

# Lamport Events
```clojure
(def lamport {:a 2 :b 2 :c 3})

(event-time :c lamport)
;;{:a 2 :b 2 :c 4}

(event-time :a lamport)
;;{:a 3 :b 2 :c 4}

(event-time :b
  (event-time :b lamport))
;;{:a 2 :b 4 :c 3}
```

# Lamport Messages
```clojure
(def lamport {:a 2 :b 2 :c 3})

(message lamport
  {:a 3 :b 1 :c 2})
;;{:a 3 :b 2 :c 3}

(message lamport
  {:c 4 :d 2})
;;{:a 2 :b 2 :c 4 :d 2}

(message lamport
  {:a 3 :b 5 :c 6})
;;{:a 3 :b 5 :c 6}
```

# Neither Before or After
* `{:a 1 :b 0}` < `{:a 1 :b 1}`
* `{:a 0 :b 1}` < `{:a 1 :b 1}`
* `{:a 1 :b 0}` ? `{:a 0 :b 1}`

# Before, After and Concurrent

# Total Order{.hidden}
## Total Order
![Everything is either smaller or larger than you](./images/total-order.png)

# Single Time Line
![single history total order](./images/one-history.png)

# Partial Order{.hidden}
### Partial Order
![Elements can be smaller, larger,or indeterminate](./images/partial-order.png)

# Multiple Time Lines
![first partial order history](./images/partial-order-history.png)

# Multiple Time Lines Next{.hidden}
![next partial order history](./images/partial-order-history-next.png)

# Multiple Time Lines Final{.hidden}
![final partial order history](./images/partial-order-history-final.png)

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

# Monotonic
## Never go back to a previous state

# Removing Items
Deletion data set, tombstones, meta data

# Deletion data set
Two sets one for additions and one for deletions

```clojure
(let [additions #{:apple :pear}
      deletions #{:apple}]
      (difference additions deletions))
```

# Tombstones
Add meta data to element marking as deleted

```clojure
(let [fruits #{:apple :pear}]
  (tombstone :pear fruits))
#{:apple [:deleted :pear]}

(let [fruits #{[{:add #{1}} :apple] [{:add #{2}}] :pear}]
  (add :pear
    (tombstone :pear fruits)))
#{[{:add #{1}} :apple] [{:add #{2 4} :delete #{3}} :pear]}
```

# Meta Data
## Lamport Timestamps

```clojure
(let [my-fruits {:timestamp {:a 2 :b 1}
                   :data #{[:a 1 :apple]}}
      your-fruits {:timestamp {:a 2 :b 2}
                   :data #{[:a 1 :apple]
                           [:b 1 :pear]
                           [:b 2 :kiwi]}}]
  (merge my-fruits your-fruits))
{:timestamp {:a 2 :b 2}
 :data #{[:a 1 :apple] [:b 2 :kiwi]}}
```
