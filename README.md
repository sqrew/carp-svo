# carp-svo

A modular, high-performance 3D Sparse Voxel Octree (SVO) implementation for the [Carp](https://github.com/carp-lang/Carp) programming language.

This library provides a spatial octree acceleration structure designed for memory-efficient 3D scalar/vector storage, hierarchical queries, and GPU-friendly buffer layouts.

## Features
- **Flat Node Pooling**: All nodes are allocated in a contiguous array, avoiding pointer fragmentation, minimizing memory overhead, and enabling direct GPU buffer streaming.
- **Hierarchical Traversals**: Implements $O(\log N)$ descent for insertion and sampling, enabling fast spatial queries.
- **Early Termination**: Sampling automatically returns parent values for unsubdivided uniform spaces, drastically reducing tree depth traversals.
- **Dynamic Subdivision**: Allocates contiguous 8-node blocks on-demand during point insertions.

## Usage

```carp
(load "carp-svo/svo.carp")
(use Svo)

(defn main []
  (let [origin (Vector3.init 0.0 0.0 0.0)
        size 10.0
        max-depth 4
        default-val 0.0
        ;; 1. Initialize SVO
        tree (Svo.new origin size max-depth default-val)]
    (do
      ;; 2. Insert values at specific 3D positions
      (Svo.insert! &tree (Vector3.init 1.5 2.5 3.5) 100.0)
      (Svo.insert! &tree (Vector3.init 5.0 5.0 5.0) -50.0)
      
      ;; 3. Sample values in continuous space
      (let [val1 (Svo.sample &tree (Vector3.init 1.5 2.5 3.5))
            val2 (Svo.sample &tree (Vector3.init 0.0 0.0 0.0))]
        (do
          (println* "Value at pos 1: " val1)   ;; Outputs: 100.0
          (println* "Value at pos 2: " val2)))))) ;; Outputs: 0.0 (default-val)
```

## Architecture
Each parent node indexes its children as a contiguous block of 8 sibling elements in the pool:
```carp
(deftype SvoNode [
  child-ptr Int       ;; index of the start of the 8 children in the node pool (-1 if leaf)
  value Double        ;; scalar/field value stored at this node
  is-leaf Bool
])
```
This enables extremely fast relative indexing (`child-ptr + octant-index`) during tree descents.

## License
MIT
