# Examples for carp-svo

## 1. Basic Set & Sample

```carp
(load "carp-svo/svo.carp")
(use Svo)

(defn main []
  (let [origin (Vector3.init -5.0 -5.0 -5.0)
        size 10.0
        max-depth 3
        tree (Svo.new origin size max-depth 0.0)]
    (do
      ;; Set temperature of a particle in space
      (Svo.insert! &tree (Vector3.init 0.0 0.0 0.0) 25.5)
      
      ;; Sample at that location
      (let [temp (Svo.sample &tree (Vector3.init 0.0 0.0 0.0))]
        (println* "Temperature at center: " temp)))))
```

## 2. Sparse Space & Early Sample Termination

```carp
(load "carp-svo/svo.carp")
(use Svo)

(defn main []
  (let [origin (Vector3.init 0.0 0.0 0.0)
        size 100.0
        max-depth 6
        tree (Svo.new origin size max-depth -9.9)]
    (do
      ;; Even though max-depth is 6, we have not inserted anything yet.
      ;; The root is a leaf node, so sample terminates instantly (O(1) complexity).
      (let [v1 (Svo.sample &tree (Vector3.init 50.0 50.0 50.0))
            v2 (Svo.sample &tree (Vector3.init 2.0 80.0 15.0))]
        (do
          (println* "V1: " v1) ;; -9.9
          (println* "V2: " v2)))))) ;; -9.9
```

## 3. Clearing and Re-using the Octree Pool

```carp
(load "carp-svo/svo.carp")
(use Svo)

(defn main []
  (let [origin (Vector3.init 0.0 0.0 0.0)
        size 10.0
        max-depth 4
        tree (Svo.new origin size max-depth 0.0)]
    (do
      ;; Splat values
      (Svo.insert! &tree (Vector3.init 1.0 1.0 1.0) 42.0)
      (println* "Value before clear: " (Svo.sample &tree (Vector3.init 1.0 1.0 1.0))) ;; 42.0
      
      ;; Reset the octree pool to default state
      (Svo.clear! &tree)
      (println* "Value after clear: " (Svo.sample &tree (Vector3.init 1.0 1.0 1.0)))))) ;; 0.0
```
