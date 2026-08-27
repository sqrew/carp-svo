# carp-svo

A modular, high-performance 3D Sparse Voxel Octree (SVO) implementation for the [Carp](https://github.com/carp-lang/Carp) programming language.

This library provides a spatial octree acceleration structure designed for memory-efficient 3D scalar/vector storage, hierarchical queries, and GPU-friendly buffer layouts.

## Features
- **Flat Node Pooling**: All nodes are allocated in a contiguous array, avoiding pointer fragmentation, minimizing memory overhead, and enabling direct GPU buffer streaming.
- **Hierarchical Traversals**: Implements $O(\log N)$ descent for insertion and sampling, enabling fast spatial queries.
- **Early Termination**: Sampling automatically returns parent values for unsubdivided uniform spaces, drastically reducing tree depth traversals.
- **Dynamic Subdivision**: Allocates contiguous 8-node blocks on-demand during point insertions.


## Examples

See [examples.md](examples.md) for usage examples.
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
