# Rep-Cube Algorithm Listings

Supplementary material (algorithm listings and a completeness proof) for:

> Koohi, M., & Khorramian, A. **Comprehensive Rep-Cube Enumeration:
> A 3D Polyomino Arranging Approach, Revealing Symmetry, Congruence, and
> Cuttability.** *(submitted)*

This repository contains the complete pseudocode for all eight algorithms described
in the manuscript, as eight standalone PDF files (also combined in
`all_algorithms.pdf`), and a note (`completeness.md`) that proves the completeness of the search for order *k* = 4.
The listings were checked against the authors' implementation: where the manuscript
pseudocode and the implementation diverged, the listings reflect the actual
behaviour. The implementation itself and the enumerated rep-cube data are not part
of this repository.

---

## Background

A *rep-cube* of order *k* is an arrangement of *k* hexominoes (polyominoes of six
squares), connected edge-to-edge, that can be folded into a cube. This work
introduces a three-dimensional, cube-based state space and a DFS enumeration that,
for order *k* = 4, provably generates every valid arrangement of four polyominoes on
the cube surface (a *cube solution*) up to the 48 symmetry states of a cube solution
(see [`completeness.md`](completeness.md)), then unfolds each cube solution into the
corresponding 2D rep-cube solutions along the eleven rep-cube spaces.

For order *k* = 4 the method yields **69,537 rep-cubes** in total (55,219
non-cuttable, 14,318 cuttable), together with **903 uniform rep-cubes** — roughly
7.7× and 5.7× the counts of the most comparable prior enumeration, respectively.

---

## Algorithm Overview

The eight algorithms divide into three functional groups:

### Cube-Solution Search (§3–4)

These algorithms collectively implement the depth-first search (DFS) that traverses
the 3D cube state space and records every valid arrangement of *k* polyominoes on
the cube surface.

| File | Algorithm | Role |
|---|---|---|
| [`alg1_cube_solution.pdf`](alg1_cube_solution.pdf) | `CubeSolution` | Outer DFS driver. Iterates over all *k* × 6 starting squares and all 11 root polyomino modes, calling the subroutines below to advance, backtrack, or record solutions. |
| [`alg2_state_transition_controller.pdf`](alg2_state_transition_controller.pdf) | `StateTransitionController` | Decides, after each arranging step, whether to advance to the next polyomino mode, move to the next adjacent square, or prune the current branch. |
| [`alg3_formation_modifier.pdf`](alg3_formation_modifier.pdf) | `FormationModifier` | Resets the current polyomino mode to its first option and selects the next untried cube square; handles backtracking (pruning the last arrangement and restoring state) when no untried square remains at the current step. |

### Polyomino Placement and Hole Detection (§3–4)

These algorithms are called at each interior node of the search tree to test
whether a candidate polyomino placement is valid.

| File | Algorithm | Role |
|---|---|---|
| [`alg4_arrange_polyomino.pdf`](alg4_arrange_polyomino.pdf) | `ArrangePolyomino` | Places one polyomino on the cube by mapping each of its six squares to a cube square, rotating cube-square orientations dynamically as the polyomino crosses face boundaries. Returns the six occupied squares, or signals overlap. *O*(*k*) per call. |
| [`alg5_hole.pdf`](alg5_hole.pdf) | `Hole` | After a successful intermediate placement, checks whether the newly arranged polyomino has enclosed an empty pocket that no remaining polyomino can fill. Prunes such states early. *O*(*k*³) worst case. |
| [`alg6_empty_traverse.pdf`](alg6_empty_traverse.pdf) | `EmptyTraverse` | Depth-first traversal of empty cube squares adjacent to already-arranged polyominoes, used by `Hole` to determine connectivity of the remaining empty space. |

### Unfolding and Parallelization (§5, §8)

These algorithms convert cube solutions into 2D rep-cube solutions and distribute
the workload across threads.

| File | Algorithm | Role |
|---|---|---|
| [`alg7_unfolding_cube.pdf`](alg7_unfolding_cube.pdf) | `UnfoldingCube` | Iterates over all 11 empty-space rep-cube spaces and all unfolding modes of each cube solution. For each mode, converts 3D polyomino orientations to their 2D counterparts, arranges the polyominoes on the rep-cube space, checks integrity and foldability, and records non-duplicate solutions. |
| [`alg8_parallelization.pdf`](alg8_parallelization.pdf) | `Parallelization` | Partitions the set of assignable items (starting squares for the cube-solution phase; cube solutions for the unfolding phase) into contiguous chunks and spawns one thread per chunk. Results are written to a shared array and joined on completion. |

---

## Relationship Between Algorithms

```
Parallelization (Alg. 8)
└── per thread: CubeSolution (Alg. 1)          ← cube-solution phase
      ├── StateTransitionController (Alg. 2)
      ├── FormationModifier (Alg. 3)
      ├── ArrangePolyomino (Alg. 4)
      └── Hole (Alg. 5)
            └── EmptyTraverse (Alg. 6)

Parallelization (Alg. 8)
└── per thread: UnfoldingCube (Alg. 7)         ← unfolding phase
      └── ArrangePolyomino (Alg. 4)
```

`Parallelization` is invoked twice: once to drive the cube-solution search
(executing `CubeSolution` on a range of starting squares per thread) and once
to drive the unfolding stage (executing `UnfoldingCube` on a range of cube
solutions per thread).

---

## Complexity Summary

| Algorithm | Time | Space |
|---|---|---|
| `ArrangePolyomino` | *O*(*k*) | *O*(*k*) |
| `Hole` | *O*(*k*³) worst case | *O*(*k*²) |
| `CubeSolution` (DFS) | pruned search | *O*(*k*²) |
| `UnfoldingCube` | *O*(*s* · *m* · *k*⁴) | *O*(*k*²) per candidate |
| Duplicate removal (canonical hash) | *O*(*nk*²) expected | *O*(*nk*²) |

Here *s* is the number of unique cube solutions, *m* the average number of
unfolding modes per cube solution (≈ 230 for *k* = 4), and *n* the number of
candidate solutions in a given stage.

---

## Experimental Setup

Results reported in the paper were obtained on:

- **Hardware:** Intel Core Ultra 7 265K, 20 cores / 20 threads @ 3.90 GHz, 64 GB DDR5 RAM
- **Parallelism:** 16 threads (cube-solution and unfolding phases)
- **Wall-clock time (Method 2, 16 threads):** cube solutions ≈ 1,969 s; unfolding ≈ 422 s; rep-cube deduplication ≈ 70–72 s

---

## Files in This Repository

```
README.md                            — this file
completeness.md                      — proof that the search for k = 4 is complete (Theorems 1–3), with the checks V0–V6
all_algorithms.pdf                   — Algorithms 1–8 combined in one PDF
alg1_cube_solution.pdf               — Alg. 1: CubeSolution
alg2_state_transition_controller.pdf — Alg. 2: StateTransitionController
alg3_formation_modifier.pdf          — Alg. 3: FormationModifier
alg4_arrange_polyomino.pdf           — Alg. 4: ArrangePolyomino
alg5_hole.pdf                        — Alg. 5: Hole
alg6_empty_traverse.pdf              — Alg. 6: EmptyTraverse
alg7_unfolding_cube.pdf              — Alg. 7: UnfoldingCube
alg8_parallelization.pdf             — Alg. 8: Parallelization
```

---

## Citation

If you use these algorithms or build on this work, please cite the companion paper:

```bibtex
@unpublished{KoohiKhorramianRepcube,
  author = {Koohi, Mehran and Khorramian, Amanj},
  title  = {Comprehensive Rep-Cube Enumeration: A 3D Polyomino Arranging
            Approach, Revealing Symmetry, Congruence, and Cuttability},
  note   = {Manuscript submitted for publication}
}
```

---

## Contact

- Mehran Koohi — `mehran.koohi@uok.ac.ir` · `mehran.koohi.uok@gmail.com`
- Amanj Khorramian — `a.khorramian@uok.ac.ir`

Department of Computer Engineering, Faculty of Engineering,
University of Kurdistan, Sanandaj, Kurdistan, Iran.
