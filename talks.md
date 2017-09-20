# [Rethinking Strings](https://www.youtube.com/watch?v=OMbwbXZWtDM) (M. Zeren)

* It makes sense to replace:

    `static const std::string s = "foo";`

  with:

    `static const char[] s = "foo";`

  This gets rid of constructors/destructors. Removes one indirection, generates
  smaller code. This causes temporaries to be created though, for functions
  that take an std::string. Solution: use `std::string_view`.
* Think about using string builders like cat and fmt to concatenate and format
  strings respecitively. The idea is to prevent memory allocations by computing
  the length of the result up front.

# [clang-useful: Building useful tools with LLVM and clang for fun and profit](https://www.youtube.com/watch?v=E6i8jmiy8MY) (P. Goldsborough)

* Have a look at libTooling to implement custom checks:
    - include sorter
    - header guard
    - naming style checker
    - McCabe complexity threshold tool
    - etc.
* Have a look at Chandler's talk on data structures in LLVM (interesting
  reference to the way polymorphism is used in LLVMs datastructures).
* Have a look at clangd!

# [Programming Conversations: Lecture 10](https://www.youtube.com/watch?v=vW14_vP23UU) (A. Stephanov)

* Minimal requirement for an ordering; transitivity: r(x, y) ∧ r(y, z) ⇒ r(x, z)
* Strict ordering ≡ non-reflexive ordering (<)
  Non-strict ordering ≡ reflexive ordering (≥)
* The following operations on relations can be observed:
  ```
     r(a, b) ---converse--- r(b, a)           < ------ >
        |                      |              |        |
        | complement           |              |        |
        |                      |              |        |
     ¬r(a, b) ------------ ¬r(b, a)           ≥ ------ ≤
  ```
* Axioms of equivalence:
    1. Reflexivity: a ~ a
    2. Symmetry: a ~ b iff. b ~ a
    3. Transitivity: a ~ b ∧ b ~ c ⇒ a ~ c
* Weak ordering: transitivity of incomparability holds.
* Total ordering uses equality as the equivalence relation!
