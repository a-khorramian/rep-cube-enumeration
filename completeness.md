# Completeness of the Search for Cube Solutions and Rep-Cube Solutions of Order 4 (Method 3)

> Supplementary material to: Koohi, M., & Khorramian, A., *Comprehensive Rep-Cube Enumeration: A 3D Polyomino Arranging Approach, Revealing Symmetry, Congruence, and Cuttability* (submitted). Repository overview: [`README.md`](README.md).
> This note proves the statements summarised in Sections 4.4–4.5 of the manuscript.
>
> **Numbering.** The manuscript cites this note by number: Theorems 1–3, Lemmas 1–3, Facts 1–6, checks V0–V6 and Sects. 2.5 and 3.4 are referred to as such. Manuscript sections, definitions and figures are cited with the numbering of the submitted manuscript (Sections 1–10; Definitions 1–15; Figs. 1–30).
>
> **Reading conventions.** "Sect. *n*" always means a section of this note; sections, definitions and figures of the manuscript are written "Manuscript §*n*", "Manuscript Definition *n*", "Manuscript Fig. *n*" (both documents have sections 3 to 6 with different content). "Algorithm *n*" refers to the listings in this repository. **Terminology and numbering are those of the manuscript**: the polyominoes are H0, …, H10 (Manuscript Fig. 1); the 24 squares of the cube are numbered 0–23 and the six surfaces are called Floor, Southern, Eastern, Roof, Northern and Western (Manuscript Figs. 5, 7, 8); a polyomino is *arranged* on the cube in one of 80 *modes* from a *designated square* on an *empty square*; the first polyomino is arranged on a *starting square*; the eleven empty space rep-cubes of Manuscript Fig. 17 are called *rep-cube spaces*; the word *net* is used only in the sense of Manuscript Definition 4 (one of the six faces of a rep-cube space; for *k* = 4 a 2 × 2 block of squares). Appendix C lists the terms.

**SUMMARY** A rep-cube of order *k* is a polyomino that folds into a cube of size √*k* × √*k* × √*k* and is dissected into *k* polyominoes, each of which folds into a cube. In this note we consider order *k* = 4: the polyomino has 24 unit squares, it folds into a cube of size 2 × 2 × 2, and it is dissected into four polyominoes, each of them one of the eleven polyominoes H0, …, H10 that fold into a unit cube. We describe a computer search for these rep-cubes in two stages. The first stage finds all *cube solutions*, i.e. all ways to arrange four polyominoes on the surface of the 2 × 2 × 2 cube so that every square is covered exactly once. The second stage unfolds every cube solution onto each of the eleven rep-cube spaces and obtains the *rep-cube solutions*. We prove three theorems saying that nothing is missed in each stage: the search is complete for the cube solutions (up to the 48 states of a cube solution, Sect. 2.5) and for the unfoldings along the eleven rep-cube spaces. Sect. 3.4 explains why not only the squares of one surface are used as starting squares. The proofs use a few small facts about the cube; each of them can be checked by a short computation that is described in Sect. 6.

*key words:* rep-cube, polyomino, exhaustive search, completeness

---

## 1. Introduction

A *polyomino* is a plane figure formed by unit squares joined edge-to-edge (Manuscript Definition 1). The eleven polyominoes of six squares that fold into a unit cube are H0, …, H10 (Manuscript Fig. 1; Appendix A). A *rep-cube of order k* is a polyomino of the shape of one of H0, …, H10 scaled by √*k*, together with a dissection into *k* polyominoes that are foldable polyominoes and fold into cubes, such that the whole polyomino folds into a cube of size √*k* (Manuscript Definition 3). It is *regular* if the *k* polyominoes have the same area.

In this note we always mean regular rep-cubes of order 4. Then the cube has size 2 × 2 × 2, its surface consists of 24 unit squares (four on each of the six surfaces), and each of the four polyominoes is one of H0, …, H10 (possibly mirrored). Every rep-cube considered here is *nice* in the sense of Manuscript Definition 15: its polyomino sides are parallel to the rep-cube edges. For *k* = 4 this is automatic, because every edge of a net (a 2 × 2 face) of the 2 × 2 × 2 cube has length 2 and joins two grid points, so its direction vector (*a*, *b*) has integer entries with *a*² + *b*² = 4, hence (*a*, *b*) = (2, 0) or (0, 2). Two rep-cube solutions that differ by a rotation or a reflection are regarded as the same (the eight states of Manuscript §5.5); the same outline with a different dissection gives different rep-cube solutions.

**The search.** We search in two stages.

* **Stage 1 (Sect. 3).** We find all *cube solutions* (Manuscript Definition 5), that is, all ways to arrange four polyominoes on the surface of the empty space cube without overlap and without holes. For this we arrange one polyomino at a time on the cube, in all possible ways, until the cube is covered.
* **Stage 2 (Sect. 4).** We unfold each cube solution onto each of the eleven rep-cube spaces (Manuscript Fig. 17) and obtain *rep-cube solutions* (Manuscript Definition 6). A rep-cube solution is *non-cuttable* if it folds without cutting, otherwise *cuttable* (Manuscript Definition 9).

**Results.** We prove the following three theorems. (Algorithm numbers refer to the listings in this repository: [1](alg1_cube_solution.pdf), [2](alg2_state_transition_controller.pdf), [3](alg3_formation_modifier.pdf), [4](alg4_arrange_polyomino.pdf), [5](alg5_hole.pdf), [6](alg6_empty_traverse.pdf), [7](alg7_unfolding_cube.pdf), [8](alg8_parallelization.pdf); all eight together in [`all_algorithms.pdf`](all_algorithms.pdf).)

* **Theorem 1.** The search for cube solutions (Algorithms 1–6, Method 3) finds exactly the cube solutions in which at least one of the four polyominoes is a *standard piece* (Sect. 2.4).
* **Theorem 2.** Every cube solution has one of its 48 states (Sect. 2.5) in which it contains a standard piece. Hence the 48 states of the cube solutions of Theorem 1 are *all* cube solutions.
* **Theorem 3.** For every given set of cube solutions, the unfolding stage (Algorithm 7) generates every unfolding mode on each of the eleven rep-cube spaces and outputs exactly the rep-cube solutions among them (those that pass the three tests T1–T3 of Sect. 4.2). In particular it finds every rep-cube solution that is an unfolding, along one of the eleven rep-cube spaces, of a cube solution of Theorem 1.

**Scope.** The search is therefore complete in two respects: it finds all cube solutions up to their 48 states (Sect. 2.5), and it finds all unfoldings of the cube solutions that it outputs along the eleven rep-cube spaces, including the cuttable ones. We do not claim anything about rep-cubes whose folding onto the cube is not along one of the eleven rep-cube spaces, nor about unfoldings of cube solutions that contain no standard piece (these are the other states of the output; they are not in the input of the unfolding stage, see the remark after Theorem 3).

The proofs use six small facts about the cube (Facts 1–5 in Sect. 2.6 and Fact 6 in Sect. 5). They concern only the 24 squares of the cube and the eleven polyominoes, and each of them can be checked by a short program. We describe the checks in Sect. 6, so that the reader can repeat them.

---

## 2. Preliminaries

**2.1 The cube.** The surface of the cube of size 2 × 2 × 2 consists of 24 squares, four on each surface. They are numbered 0–23 as in Manuscript Fig. 5(c): Floor 0–3, Southern 4–7, Eastern 8–11, Roof 12–15, Northern 16–19, Western 20–23. Two squares are *neighbours* if they share a side; every square has four neighbours, one on each of its four sides. The west, north, east and south neighbour of every square are listed in Table A (Appendix B); this is the neighbourhood relation of Manuscript Fig. 8. Every square contains exactly one vertex of the cube, and the three squares at a vertex are neighbours of each other.

**2.2 Directions.** Every cube square has four sides, called *edge1*, *edge2*, *edge3*, *edge4*; by default they point west, north, east and south (Manuscript Section 3.2, Fig. 7). In this default orientation the cyclic order west → north → east → south is the same on all six surfaces (also on the Roof, whose directions are only *drawn* rotated in Manuscript Fig. 8, see the footnote of Manuscript Table 2); this is checked in V0. A polyomino that is arranged on the cube has four *directions*: Down, Left, Up, Right (Manuscript Fig. 18a). The polyomino drawn as in Manuscript Fig. 1 is in the *Down* direction: its drawn "down" points to the south side of the cube square carrying its first square. The *Left*, *Up* and *Right* directions are obtained by turning the polyomino by 90°, 180° and 270°.

**2.3 The eleven polyominoes and the 80 modes.** The eleven polyominoes H0, …, H10 are shown in Appendix A. Every polyomino has an *Original* form (as drawn in Manuscript Fig. 1) and a *Mirror* form; for H9 (T-shape) and H10 (+-shape) the two forms coincide, for the other nine they differ. A *mode* is a form together with a direction. There are (9 · 2 + 2) · 4 = 80 modes. Square 1 (the *first square*) of a polyomino is its square at the top end in Manuscript Fig. 1 (marked in Appendix A; Method 1 uses only this square, Method 2 uses squares 1 and 6, Method 3 uses all six squares, see Manuscript Fig. 11). The polyominoes H0, H4, H6 and H7 look the same after a half-turn.

**2.4 Arranging a polyomino on the cube.** Take a mode *m*, a square *s* of the polyomino (the *designated square*) and an empty cube square *v*. To *arrange* the polyomino in the mode *m* from *s* on *v* means: *s* is put on *v*, with the direction of *m* given by the default orientation of *v*; the other squares of the polyomino follow by *routing* (Manuscript Definition 7, Section 4.6): each time we step from a square of the polyomino to a neighbouring square in the direction δ ∈ {west, north, east, south} (read in the polyomino), we step to the neighbour of the current cube square whose side is currently labelled δ; in the new cube square the side shared with the old one is relabelled with the opposite of δ, and the other three sides are relabelled in the same cyclic order west → north → east → south. This is the rotation of the edge variables of Manuscript Section 4.6 and Fig. 13. (Example from Manuscript Fig. 12/13: polyomino square 4 is on cube square 4, and we step west to polyomino square 5. This is the west neighbour of cube square 4, the cube square 21, and the side of square 21 that is shared with square 4, its edge4, which was labelled "south", is now labelled "east". A second example, Manuscript Fig. 18(b): H3 in the Left direction arranged from square 20 on the cube square 20 covers the squares 20, 22, 23, 14, 15 and 6; the check V0 reproduces it.)

The six cube squares covered by an arrangement, together with the five pairs of neighbours along which the polyomino was routed, are called an *arranged polyomino*. Different arrangements can give the same arranged polyomino, for example a polyomino that looks the same after a half-turn can be arranged in two ways. If the designated square is the first square of the polyomino, there are 80 · 24 = 1920 arrangements, and they give 1536 different arranged polyominoes (check V1).

A *standard piece* is an arranged polyomino in the *Down* direction, in the *Original* form, whose first square (square 1) is on the *starting square* (Manuscript §4.1, §4.4). There are 11 · 24 arrangements of this kind (one for every polyomino and every starting square).

**2.5 Cube solutions and their states.** A *cube solution* (Manuscript Definition 5) is a set of four arranged polyominoes that cover the 24 squares of the cube exactly once (no overlap, no holes). Two arranged polyominoes are *adjacent* if a square of one is a neighbour of a square of the other.

A cube solution has **48 states**. Take one of the six surfaces as the Floor (6 ways), turn the cube solution about the vertical axis through the centre of the Floor into one of 4 directions (4 ways), and take either the cube solution or its mirror image (2 ways): 6 · 4 · 2 = 48. Equivalently, there are 8 states on a fixed Floor (4 directions of the cube solution and 4 directions of its mirror image), and each of the 6 surfaces can be the Floor, which gives 8 · 6 = 48. This is the count of states of *one cube solution*, and it is the count used in the discussion of why not only one surface is processed (Sect. 3.4). The number 48 is nothing more than this count; V0 only confirms that there are no further neighbourhood-preserving permutations of the 24 squares. A state changes only the positions of the squares and not the neighbourhood relation, so every state of a cube solution is a cube solution again. A state that mirrors the cube exchanges Original and Mirror forms (for H9 and H10 this changes nothing).

*Do not confuse the following.* The 48 states of a cube solution are placements of the arranged polyominoes on the cube in 3D. They are different from (i) the 8 states of a flat rep-cube solution (4 rotations of the polyomino and 4 rotations of its mirror image in the plane), which are used only to remove duplicate rep-cube solutions (Manuscript §5.5, Sect. 5), and (ii) the states (nodes) of the DFS state space of Manuscript Definition 2.

**2.6 Five facts.**

* **Fact 1.** In every arrangement the six squares of the polyomino fall on six different cube squares. (So an arranged polyomino always has six squares.)
* **Fact 2.** If the six squares of any arranged polyomino are removed, the remaining 18 squares are still connected: any two of them are joined by a chain of neighbours.
* **Fact 3.** If six squares of the cube are connected, then at most 8 pairs of them are neighbours. (For the six squares of an arranged polyomino the number of such pairs is 5 or 6; we only use that it is at least 5, which holds for any six connected squares.)
* **Fact 4 (the standard pieces represent all arrangements of a first square).** A *first-square arrangement* is an arrangement of a polyomino from its first square, given by the cube square *c* that carries it, the direction *d* ∈ {Down, Left, Up, Right} and the form (Original or Mirror); for H9 and H10 the two forms coincide. There are 24 · 4 · 2 = 192 of them (for the shapes H0–H8). (a) A state *σ* of the cube maps the first-square arrangement (*c*, *d*, form) to the first-square arrangement (*σ*(*c*), *d*′, form′), where the side that points to the "Up" neighbour of *c* now points to the image of that neighbour, and form′ is the other form exactly if *σ* mirrors the cube; and it maps the arranged polyomino to the arranged polyomino of the image. (b) Under the 48 states the 192 first-square arrangements fall into exactly four classes, and the 24 standard first-square arrangements (*c*, Down, Original), one for each cube square *c*, meet every class, six in each. Hence for every first-square arrangement there is a state that maps it to a standard one.
* **Fact 5.** For each *i* = 0, …, 10 let *R*ᵢ be the polyomino H*i* scaled by 2, i.e. every unit square of H*i* replaced by a 2 × 2 block (this is the rep-cube space of H*i*, Manuscript Fig. 17; its six 2 × 2 blocks are its six nets in the sense of Manuscript Definition 4). Then *R*ᵢ folds without cutting onto the cube of size 2 × 2 × 2: routing *R*ᵢ from a fixed square puts its 24 squares on the 24 cube squares, every cube square exactly once, every two neighbouring squares of *R*ᵢ on neighbouring cube squares, and every net of *R*ᵢ on one surface of the cube. The same holds for its mirror image.

*Proof of Fact 5.* H*i* folds along the sides of its squares onto the unit cube so that its six squares cover the six faces, one square for each face (this is the definition of H*i*). Scaling by 2 scales the polyomino, the cube and all fold lines by 2: the fold lines of *R*ᵢ are the sides between the 2 × 2 blocks, folding along them puts each 2 × 2 block exactly on one surface of the 2 × 2 × 2 cube, with its four squares on the four squares of that surface. This is the construction of Manuscript §5.2. The statement is also checked by routing in V5. ∎

Facts 1–4 are checked in Sect. 6 (checks V1–V4); Fact 4(a) is just the statement that a rotation or reflection of the cube preserves the neighbourhood relation and the cyclic order up to reversal, and the routing rule of Sect. 2.4 only uses these.

---

## 3. Stage 1: the cube solutions

### 3.1 The search

The search arranges the polyominoes on the cube one by one by a depth-first search. Outline:

```
Algorithm A: Outline of the search for cube solutions (Algorithms 1-6)
Input : the empty space cube and the eleven polyominoes
Output: cube solutions
1  for each starting square c and each of the 11 original polyominoes H do
2     arrange H as a standard piece: first square on c, Down direction     // the first arrangement
3  arrange three more polyominoes, one by one, as follows:
4     for each empty square v next to the arranged polyominoes do
5        for each of the 80 modes and each designated square s of the polyomino do   // Method 3: all six squares s
6           arrange the polyomino in this mode from s on v
7           if it overlaps the arranged polyominoes, or Hole finds a hole, skip it
8           otherwise keep it and go on with the next polyomino
9  when four polyominoes are arranged, output the cube solution and go back to try the other choices
```

The *overlap test* (Algorithm 4) checks that the new polyomino covers only empty squares. The *hole test* (Algorithms 5 and 6, Hole and EmptyTraverse) starts at an empty square next to the new polyomino and walks from empty square to neighbouring empty square. When the walk is stuck, it goes back to a visited square that still has an unvisited empty neighbour and goes on. It stops when no such square exists, or when it has visited all empty squares next to the new polyomino and next to the polyominoes adjacent to it. It reports a hole only in the first case.

We assume that the algorithms do what they say: Algorithms 2 and 3 only move on to the next choice, or go back by one polyomino when all choices are used up. So the search is *exhaustive*: at every step it tries every choice of empty square *v*, mode and designated square *s*, and it skips a choice only if the choice fails the overlap test or the hole test, or if it has been tried already (the same arranged polyomino obtained from another designated square is the same choice).

### 3.2 Theorem 1

**Lemma 1.** Let *A* be an arranged polyomino and *v* one of its six squares. Then there is a mode and a designated square *s* such that arranging the polyomino in this mode from *s* on *v* gives *A*, and this combination is tried at *v* by the search.

*Proof.* Look at an arrangement that gives *A*. Let *s* be the square of the polyomino that lies on *v*, and let *m* be the mode of the arrangement. This combination of *m*, *s* and *v* is one of those tried by the search, unless *m* is the Mirror form of H9 or H10, which is not in the 80 modes. In that case we use that the Mirror form of H9 or H10 equals the Original form: the same arranged polyomino is also obtained by an arrangement in the Original form, and then some other square *s*′ of the polyomino lies on *v*. ∎

**Lemma 2 (a good order).** Let *S* be a cube solution and *A* one of its arranged polyominoes. The four arranged polyominoes can be named *A*, *B*, *C*, *D* so that *B* is adjacent to *A*, *C* is adjacent to *D*, and *C* is adjacent to *A* or *B*.

*Proof.* Draw four dots for the arranged polyominoes, and a line between two dots if the arranged polyominoes are adjacent. The graph is connected, because the surface of the cube is connected.

The graph is not a *star*, that is, there are no three arranged polyominoes *L*₁, *L*₂, *L*₃ that are adjacent only to a fourth one *M*. Suppose there were. An arranged polyomino has six squares, hence 24 sides. By Fact 3, at most 8 pairs of neighbours lie inside *L*ᵢ, so at most 16 sides of *L*ᵢ are used inside *L*ᵢ, and at least 8 sides of *L*ᵢ lie against squares outside *L*ᵢ. These squares belong to *M*, because *L*ᵢ is adjacent to nothing else. This holds for *i* = 1, 2, 3, so at least 24 sides of squares of *M* lie against the three arranged polyominoes. But *M* has only 24 sides, and at least 10 of them lie against squares of *M* itself, because *M* is connected and has at least 5 pairs of neighbours. This is a contradiction.

A connected graph with four dots that is not a star has two lines without a common dot (there are five such graphs: path, 4-cycle, triangle with a tail, square with a diagonal, and the complete graph; each is checked at once). Two lines without a common dot contain all four dots. Take two such lines. The line that contains *A* has the dots *A* and *B*, and the other line has the dots *C* and *D*. Because the graph is connected, *C* or *D* is joined to *A* or *B*; call it *C*. ∎

**Lemma 3 (the hole test).** (a) If the empty squares form one connected part, the hole test reports no hole. (b) Let *A*, *B*, *C*, *D* be named as in Lemma 2 and let the search arrange them in this order. Then the hole test reports no hole.

*Proof.* (a) Every step of the walk goes to an empty neighbour of a visited square. So, when the test stops because no unvisited neighbour is left, the visited squares are exactly the connected part of empty squares that contains the start. If all empty squares are connected, they are all visited, and no hole is reported.

(b) The hole test is made after the second and the third polyomino (and perhaps after the first). The algorithm listing does not say whether the new polyomino is already counted as arranged during its own test, so we treat both cases. In every case the empty squares form one connected part:

* After *A* is arranged: the empty squares are all 24 squares, or the 18 squares outside *A*. They are connected by Fact 2.
* After *B* is arranged: the empty squares are the squares outside *A* (connected by Fact 2), or the squares outside *A* and *B*, which are the squares of *C* and *D*. These are connected, because *C* is adjacent to *D*.
* After *C* is arranged: the empty squares are those of *C* and *D*, or those of *D* alone. Both are connected.

By (a) no hole is reported. ∎

**Theorem 1.** The search for cube solutions (Algorithm 1 under Method 3) outputs a cube solution if and only if the cube solution contains a standard piece.

*Proof.* If a cube solution is output, its first arranged polyomino is a standard piece (Algorithm 1, line 3, and Algorithm 3, line 10, which keeps this restriction when the search goes back to the first polyomino). Conversely, let *S* be a cube solution that contains a standard piece *A*. Name the arranged polyominoes *A*, *B*, *C*, *D* as in Lemma 2. We show that the search can arrange them in this order.

The search starts with *A* as the first arrangement: the starting square is the square that carries the first square of *A*, and the polyomino is in the Original form and the Down direction. Suppose that the first *j* arranged polyominoes have been arranged. The next one is adjacent to the arranged part (Lemma 2), so one of its squares *v* is empty and lies next to the arranged part. The search takes *v* at some time. By Lemma 1, the next arranged polyomino is obtained by one of the combinations of mode and designated square tried at *v*. It has six squares (Fact 1), it does not overlap the arranged part because the arranged polyominoes of *S* are disjoint, and no hole is reported (Lemma 3). So the search goes on with *j* + 1 arranged polyominoes. After four polyominoes it outputs *S*. ∎

### 3.3 Theorem 2

**Theorem 2.** Every cube solution has one of its 48 states in which it contains a standard piece. Hence, if we take the 48 states of the cube solutions output by the search of Sect. 3.1, we obtain all cube solutions.

*Proof.* Let *S* be a cube solution and *A* one of its arranged polyominoes. By the definition of an arranged polyomino (Sect. 2.4), *A* is obtained by a first-square arrangement, that is, by an arrangement in some mode with its first square on some cube square *c*. (If a polyomino that looks the same after a half-turn can be arranged in two ways, take either one.) By Fact 4 there is a state *σ* of the cube that maps this first-square arrangement to a standard one: the first square of the polyomino is on the cube square *σ*(*c*), the form is Original and the direction is Down. By Fact 4(a), the image of *A* under *σ* is the arranged polyomino of this standard arrangement, that is, a standard piece. So the state *σ*(*S*) of *S* contains a standard piece.

The last sentence of the theorem follows from Theorem 1: *σ*(*S*) is output by the search, and *S* is one of its 48 states, because *σ*⁻¹ is again one of the 48 states and maps *σ*(*S*) back to *S*. ∎

---

### 3.4 On the number of starting squares (why not only one surface?)

One may ask why the search does not use only the squares of one surface of the cube as starting squares and obtain the cube solutions of the other surfaces from them by symmetry. The search reported here uses all *k* × 6 = 24 starting squares, for two reasons.

*First, the workload moves to the unfolding stage.* A cube solution found from the starting squares of one surface stands for 48 states (Sect. 2.5): 8 states on a fixed Floor, namely its 4 directions and the 4 directions of its mirror image, and this for each of the 6 surfaces, 6 · 8 = 48. All of them would have to be generated and mapped to the remaining five surface positions before unfolding, because the 2D orientation of a polyomino on a rep-cube space depends on which position its surface occupies in the folded cube (Manuscript §5.2, Fig. 18). This increases the work of the unfolding stage substantially compared with the search from all 24 starting squares.

*Second, minimality of a one-surface starting set is not proved.* Theorems 1–3 are proved for the search from all 24 starting squares. A proof for a one-surface starting set would need its own argument, analogous to Sect. 3.2–3.3, and it is not given here. We verified empirically that restricting the starting squares to one surface gives the same unique rep-cube set for *k* = 4, but this is not a proof, and nothing is claimed for arbitrary *k*. We keep it as an open direction (Manuscript §9).

For these reasons all 24 starting squares are used.

---

## 4. Stage 2: unfolding on the eleven rep-cube spaces

### 4.1 Rep-cube spaces and unfolding modes

Take a rep-cube space *R*ᵢ (Fact 5, Manuscript Fig. 17) and fold it onto the cube. Then every cube square *u* has a place in the picture of *R*ᵢ, and the picture gives *u* a direction: the sides of *u* that are drawn as west, north, east and south in the picture (the directions written on the nets in Manuscript Fig. 17; Manuscript §5.2).

Let *A* be an arranged polyomino of a cube solution, and let *u* be a square of *A*. Arranging *A* on the rep-cube space *R*ᵢ *from the designated starting square u* means: *A* is drawn in the plane so that (1) the square of the polyomino that lies on *u* is put on the place of *u* in *R*ᵢ, and (2) its directions are converted from the 3D direction on the cube to the 2D direction of the picture of *R*ᵢ (Manuscript §5.2, Fig. 18). The polyomino may have to be turned or mirrored to do this. This is what Algorithm 7 does in lines 8 and 9. An *unfolding mode* of a cube solution on *R*ᵢ is a choice of one designated starting square in each of its four arranged polyominoes (Manuscript Definition 8, §5.4). The program keeps only one designated starting square for the squares of the same surface and the same direction, because they give the same flat polyomino (Manuscript §5.5); this is the same as trying all six squares of an arranged polyomino.

### 4.2 The unfolding

```
Algorithm B: Outline of the unfolding (Algorithm 7)
Input : cube solutions
Output: rep-cube solutions
1  for each rep-cube space R_i, i = 0..10 do
2     for each cube solution S do
3        for each unfolding mode of S on R_i (one designated starting square in each of the four arranged polyominoes) do
4           arrange the four polyominoes on R_i from their designated starting squares
5           if two of the arranged polyominoes overlap, skip it                              // test T1
6           if the four polyominoes are not one connected polyomino, skip it                // test T2
7           if the polyomino cannot be folded onto the cube as one sheet, skip it           // test T3
8           output the polyomino with its four arranged polyominoes (once for each congruent copy)
```

The three tests are as follows.

* **T1.** The four arranged polyominoes do not overlap on the rep-cube space (Algorithm 7, line 10).
* **T2.** The four arranged polyominoes together form one connected polyomino; in particular none of them is without an adjacent polyomino, as in Manuscript Fig. 20 (CheckIntegrity).
* **T3.** The polyomino can be folded onto the cube as one sheet (FoldToCheckCube). To test this, start at one square of the polyomino and put it on the cube square it came from, with the direction given by *R*ᵢ. Mark the other squares one by one by the routing rule of Sect. 2.4. A square is skipped if its cube square is already used. The test is passed if, for some start square, all 24 squares are marked.

A polyomino that passes the three tests is a *rep-cube solution* (Manuscript Definition 6; in the supplementary text of the manuscript it is called an unfolding, Manuscript Remark 1). If, in addition, every two neighbouring squares of the polyomino are put on neighbouring cube squares by the marking in T3, the rep-cube solution is *non-cuttable*; otherwise it is *cuttable* and has to be cut along at least one edge to be folded (Manuscript Definition 9).

### 4.3 Theorem 3

**Theorem 3.** Let *Σ* be any set of cube solutions. Algorithm 7 applied to *Σ* generates every unfolding mode of every cube solution of *Σ* on each of the eleven rep-cube spaces, and it outputs exactly those that pass T1, T2 and T3, that is, exactly the rep-cube solutions that are unfoldings of cube solutions of *Σ* along one of the eleven rep-cube spaces. In particular, if *Σ* is the set of all cube solutions, which we get from Algorithm 1 by Theorem 2, then Algorithm 7 outputs every rep-cube solution that is an unfolding of a cube solution along one of the eleven rep-cube spaces.

*Proof.* Lines 1–3 of Algorithm B go through every rep-cube space, every cube solution, and every unfolding mode, so every arrangement of the four polyominoes on the rep-cube space is built. Lines 5–7 reject exactly the arrangements that fail T1, T2 or T3, and line 8 outputs the others. The only possible loss is the removal of duplicates, which is harmless by Sect. 5. ∎

**Remark (what the program runs).** Theorem 3 holds for any set Σ of cube solutions. The program applies Algorithm 7 to the output of Algorithm 1 after the removal of duplicates (Sect. 5), that is, by Theorem 1, to the cube solutions that contain a standard piece, and not to the set of all cube solutions. For the program as run, Theorem 3 therefore gives every rep-cube solution that is an unfolding along one of the eleven rep-cube spaces of a cube solution that contains a standard piece. The other states of these cube solutions are the other cube solutions of Theorem 2; Theorem 3 covers their unfoldings only if they are added to Σ.

---

## 5. Removing duplicates and running in parallel

The search finds every cube solution many times. To remove duplicates, the program writes each cube solution as a *key*: the four arranged polyominoes are described one by one, by the number of the polyomino, its form (Original or Mirror), and the list of the cube squares that carry its squares 1 to 6 (for the four polyominoes H0, H4, H6, H7 that look the same after a half-turn, the list is written forwards or backwards, whichever is smaller). The four descriptions are sorted. Two cube solutions with the same key are the same: this is **Fact 6** (check V6). So removing duplicates by key loses nothing.

A rep-cube solution is described by the smallest of the eight lists obtained by rotating and reflecting it (four rotations of the original and four rotations of the mirror image), where each list gives the four polyominoes with their squares, after the rep-cube solution is moved to the corner (Manuscript §5.5). Two rep-cube solutions with the same description are congruent, and congruent rep-cube solutions have the same description. So removing duplicates loses no rep-cube solution.

The program can use several threads. Algorithm 8 cuts the list of the 24 starting squares (and later the list of cube solutions) into blocks that together contain everything, runs the same algorithm on every block, and joins the results. So the result is the same as for one thread.

---

## 6. How to check the facts

All facts concern only the 24 squares of the cube and the eleven polyominoes, so they can be checked by a short program. We describe the program in words, so that the reader can repeat the checks. For this note we wrote an independent program; the numbers below are what it printed.

**Routing by Table A.** Table A in Appendix B is the neighbourhood relation of the cube (Manuscript Fig. 8): for every square it lists the west, north, east and south neighbour. A *direction frame* of a cube square is a choice of which of its four neighbours is currently called "up"; the other three are then called right, down and left in the clockwise order north → east → south → west of the default orientation. To arrange a polyomino from its square *s* on the cube square *v* in a mode, put *s* on *v* with the frame given by the direction of the mode (Down: the default frame), and put the other squares one by one by the routing rule of Sect. 2.4: a step of the polyomino up, right, down or left goes to the neighbour of the current cube square that is called up, right, down or left; in the new square *y*, let *q* be the side shared with the old square. The new frame is: after a step up, *q* is called "down"; after a step down, *q* is called "up"; after a step right, *q* is called "left"; after a step left, *q* is called "right". For a Mirror form, use the mirror image of the polyomino in the same way. (Example: the step from polyomino square 4 on cube square 4 to its west neighbour, the polyomino square 5, goes to cube square 21, and in 21 the side shared with square 4, which is its south side, is now called "east"; this is Manuscript Fig. 13.)

| Check | What to do | Result |
|---|---|---|
| **V0** (Table A) | Check Table A: every square has four different neighbours, the relation is symmetric, there are 48 pairs of neighbours, 8 triangles (the squares at the 8 vertices), and there are exactly 48 neighbourhood-preserving permutations of the 24 squares (these are the 48 states of Sect. 2.5). Give every square the cyclic order north → east → south → west (the default orientation, also on the Roof) and trace the faces of the resulting rotation system. Arrange H3 in the Left direction from square 20 on cube square 20 (Manuscript Fig. 18(b)). | The neighbourhood relation is that of the surface of the 2 × 2 × 2 cube; the rotation system has 26 faces (8 triangles and 18 quadrilaterals), i.e. it is consistent on all six surfaces (a sphere, no handedness change on the Roof). H3 covers 20, 22, 23, 14, 15, 6, as in Manuscript Fig. 18(b). |
| **V1** (Fact 1) | Arrange every polyomino in every mode from its first square on every cube square: 9 polyominoes × 2 forms × 4 directions + 2 polyominoes × 4 directions = 80 modes, 80 × 24 = 1920 arrangements. | Every arrangement covers six different squares. There are 1536 different arranged polyominoes (the same number if two arranged polyominoes are distinguished by their squares and neighbour pairs, or by polyomino, form and list of squares as in the key of Sect. 5). |
| **V2** (Fact 2) | For each of the 1536 arranged polyominoes, remove its six squares and test by breadth-first search whether the other 18 squares are connected. | They are always connected. |
| **V3** (Fact 3) | For each of the 134 596 sets of six squares, test whether it is connected and count the pairs of neighbours inside it. For each of the 1536 arranged polyominoes count the pairs of neighbours inside it. | 2764 sets are connected. The largest number of pairs is 8 (5 pairs: 1608 sets, 6: 952, 7: 192, 8: 12). The arranged polyominoes have 5 pairs (336 of them) or 6 pairs (1200 of them). |
| **V4** (Fact 4) | Take the 192 first-square arrangements (24 squares × 4 directions × 2 forms). Take the 48 states of Sect. 2.5, written as permutations of the 24 squares: for each of the 6 surfaces as Floor, the 4 turns about the vertical axis, each with and without mirroring (6 · 4 · 2 = 48; by V0 there are no others). Compute the orbits of the 192 first-square arrangements, with the action of Fact 4(a), and see which orbits contain a standard arrangement (*c*, Down, Original). In addition, for each of the 1536 arranged polyominoes check that one of the 48 states maps it onto a standard piece. | There are exactly 4 orbits. The 24 standard arrangements meet every orbit, six in each. Every one of the 1536 arranged polyominoes has a state in which it is a standard piece. |
| **V5** (Fact 5) | Double each polyomino H*i* (every unit square becomes 2 × 2 squares) and put its first square on cube square 0, in each of the eight modes (Original or Mirror, four directions); route all 24 squares by the rule above, test that every cube square is marked exactly once, and that every two neighbouring squares are put on neighbouring cube squares with the same frame whichever way they are reached. | For each of the eleven polyominoes exactly two of the eight modes succeed (a polyomino and its mirror image; the other six lead to a contradiction). In these all 24 cube squares are marked, each exactly once, and neighbouring squares are put on neighbouring squares. |
| **V6** (Fact 6) | List all coverings of the cube by four arranged polyominoes. Compute the key of each (Sect. 5), and test whether two coverings with the same key consist of the same four arranged polyominoes. | No two different coverings have the same key (the key of an arranged polyomino determines its polyomino, form and squares, hence its neighbour pairs; there are 1536 different keys, as many as arranged polyominoes). |

Each check takes seconds to about a minute. We also checked Lemma 2 by the same program: for every cube solution and each of its four arranged polyominoes *A* there is an order *A*, *B*, *C*, *D* as in Lemma 2, and no cube solution is a star.

---

## Appendix A. The eleven polyominoes H0, …, H10

Drawn as in Manuscript Fig. 1 (Original form, Down direction). `1` marks the first square (square 1) and `6` the last square (square 6), as in Manuscript Fig. 11(a); `#` marks the other squares (their numbers are not used in the proofs; for H0 see Manuscript Fig. 12). The mirror images are obtained by exchanging left and right; for H9 and H10 they coincide with the originals. H0, H4, H6 and H7 look the same after a half-turn.

```
 H0:  . 1 .    H1:  . 1 .    H2:  . 1 .    H3:  1 . .
      . # #         . # .         . # .         # # .
      # # .         # # #         . # #         . # #
      . 6 .         . . 6         6 # .         . 6 .

 H4:  1 . .    H5:  1 # .    H6:  1 .      H7:  1 # .
      # # .         . # .         # .           . # .
      . # #         . # #         # #           . # .
      . . 6         . . 6         . #           . # 6
                                  . 6

 H8:  1 # .    H9:  # 1 #   H10:  . 1 .
      . # .         . # .         # # #
      . # #         . # .         . # .
      . 6 .         . 6 .         . 6 .
```

## Appendix B. Table A: the neighbourhood relation of the cube of size 2 × 2 × 2

The 24 squares are numbered 0–23 as in Manuscript Figs. 5(c), 7 and 8. For each square the table gives its surface and its west, north, east and south neighbour (edge1, edge2, edge3 and edge4 in the default orientation); it is copied from Manuscript Fig. 8 and is checked in V0. The direction "up" of a polyomino arranged in the Down direction is the north side. The Roof is drawn rotated in Manuscript Fig. 8, but the west, north, east and south neighbours are listed here with the same meaning as on the other surfaces, so that one routing rule applies to all surfaces.

| Square | Surface | West neighbour | North neighbour | East neighbour | South neighbour |
|---|---|---|---|---|---|
| 0 | Floor | 20 | 16 | 1 | 2 |
| 1 | Floor | 0 | 17 | 8 | 3 |
| 2 | Floor | 21 | 0 | 3 | 4 |
| 3 | Floor | 2 | 1 | 9 | 5 |
| 4 | Southern | 21 | 2 | 5 | 6 |
| 5 | Southern | 4 | 3 | 9 | 7 |
| 6 | Southern | 23 | 4 | 7 | 14 |
| 7 | Southern | 6 | 5 | 11 | 15 |
| 8 | Eastern | 1 | 17 | 10 | 9 |
| 9 | Eastern | 3 | 8 | 11 | 5 |
| 10 | Eastern | 8 | 19 | 13 | 11 |
| 11 | Eastern | 9 | 10 | 15 | 7 |
| 12 | Roof | 22 | 14 | 13 | 18 |
| 13 | Roof | 12 | 15 | 10 | 19 |
| 14 | Roof | 23 | 6 | 15 | 12 |
| 15 | Roof | 14 | 7 | 11 | 13 |
| 16 | Northern | 20 | 18 | 17 | 0 |
| 17 | Northern | 16 | 19 | 8 | 1 |
| 18 | Northern | 22 | 12 | 19 | 16 |
| 19 | Northern | 18 | 13 | 10 | 17 |
| 20 | Western | 22 | 16 | 0 | 21 |
| 21 | Western | 23 | 20 | 2 | 4 |
| 22 | Western | 12 | 18 | 20 | 23 |
| 23 | Western | 14 | 22 | 21 | 6 |

## Appendix C. Terminology: this note and the manuscript

The note uses the terminology and the numbering of the manuscript.

| Term | Defined / shown in the manuscript | Remark |
|---|---|---|
| polyomino; H0 … H10 | Definition 1, Fig. 1 | The eleven foldable polyominoes; Original and Mirror form. Square 1 is the first square, square 6 the last square (Fig. 11). |
| mode (80 modes) | §4.2, Fig. 11, Fig. 18a | 11 Original + 9 Mirror forms, each in four directions (Down, Left, Up, Right). |
| arranging a polyomino; designated square; empty square; starting square | §4.1–§4.3 | Routing: Definition 7, §4.6. |
| arranged polyomino | §4.6, Figs. 14–16 | Six cube squares and the neighbour pairs along which the polyomino was routed. |
| standard piece | §4.4 | An arranged polyomino in the Original form and the Down direction with its first square on the starting square. |
| cube squares 0–23; surfaces Floor, Southern, Eastern, Roof, Northern, Western | Figs. 5, 7, 8 | Neighbourhood relation: Table A (Appendix B). |
| cube solution; unique cube solutions | Definition 5, §5.1, Table 3 | Methods 2 and 3: the cube solutions that contain a standard piece (Theorem 1). |
| 48 states of a cube solution | §4.4, §4.5 and Sect. 3.4 of this note (why all starting squares are used) | 6 surfaces as Floor × 4 directions × (cube solution or its mirror image) = 48; equivalently 8 states on a fixed Floor times 6 surfaces. |
| 8 states of a rep-cube solution | §5.5 | 4 rotations × (original or mirror image) in the plane; used only to remove duplicate rep-cube solutions. Not the 48 states above. |
| starting squares (all *k* × 6) | §4.1, §4.3, §9 | Why not one surface only: Sect. 3.4. |
| net; empty space rep-cube; rep-cube space | Definition 4, §5.2, Fig. 17 | A *net* is only one of the six √*k* × √*k* faces (for *k* = 4 a 2 × 2 block). A rep-cube space is the polyomino H*i* scaled by 2 (Fact 5). |
| unfolding mode; designated starting square | Definition 8, §5.4 | One designated starting square for each arranged polyomino; one per surface and direction suffices. |
| T1, T2, T3 | §5.4 (overlap, polyominoes without adjacent squares, folding as a whole) | Algorithm 7. |
| rep-cube solution; non-cuttable; cuttable | Definitions 6 and 9, Remark 1 | The outputs of the unfolding stage that pass T1–T3. |
| Hole; EmptyTraverse | §4.6 | Algorithms 5 and 6. |
