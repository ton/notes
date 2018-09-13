# [Rethinking Strings](https://www.youtube.com/watch?v=OMbwbXZWtDM) (M. Zeren)

* It makes sense to replace:

    `static const std::string s = "foo";`

  with:

    `static const char[] s = "foo";`

  This gets rid of constructors/destructors. Removes one indirection, generates
  smaller code. This causes temporaries to be created though, for functions
  that take an std::string. Solution: use `std::string_view`.
* Think about using string builders like cat and fmt to concatenate and format
  strings respectively. The idea is to prevent memory allocations by computing
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

# [When a Microsecond Is an Eternity: High Performance Trading Systems in C++](https://www.youtube.com/watch?v=NH1Tta7purM) (C. Cook)

* Static local variables incur a slight performance overhead, since the
  compiler needs to guarantee that the variable is initialized once and only
  once, considering multi-threading, exceptions, etc.

# [What Has My Compiler Done For Me Lately? Unbolting the Compiler's Lid.](https://www.youtube.com/watch?v=bSkpMdDe4g4) (M. Godbolt)

* Register on x86:

  - 16 64-bit registers: rax, rbx, rcx, rdx, rsp, rbp, rsi, rdi, r8-r15
  - Floating point registers: xmm0-xmm15
  - Argument passing using rdi, rsi, rdx, rcx, ..., integer return value in rax
* Intel ASM syntax: op, dest, src1, src2
* Memory reference form: base + optional register + optional register * (1, 2, 4, or 8)
* Examples:

     sub eax, [r14 + 4*rbx] <=> eax -= r14[rbx]
     lea rax, [r14 + 4*rbx] <=> int* rax = &r14[rbx]

# [The Design of abs::StrCat](https://www.youtube.com/watch?v=tuWnw3sWZ4w) (J. Brown)

* Interesting approach to efficient concatenation of alphanumeric strings to
  form an std::string.

# [Going Nowhere Faster](https://www.youtube.com/watch?v=2EWejmkKlxs) (C. Carruth)

* Goes into detail on how to use Google's benchmark framework together with the
  perf tool to identify cache locality issues. Demos a case where a level 1
  cache miss rate increase from 0.01% to 0.04% results in a >20% performance
  drop...!

# [LLVM: A Modern, Open C++ Toolchain](https://www.youtube.com/watch?v=uZI_Qla4pNA) (C. Carruth)

* Nice application of link-time optimization (LTO), using LLVM's thin LTO (~60
  minutes in).
* lld is about two times faster than GNU Gold, which in turn is about two times
  faster than ld.

# [Curiously Recurring C++ Bugs at Facebook](https://www.youtube.com/watch?v=lkgszkPnV8g) (L. Brandy)

* Interesting enumeration of (beginner) C++ bugs. All these bugs would make
  great interview questions to check someone's C++ knowledge. For example, what
  does the following print?

    std::map<std::string, std::string> m;
    m["hello"] = "world";
    std::cout << m["world"];

  Or, comment on the following code:

    void initialize(std::map<std::string, int> settings)
    {
      tolerance_ = settings["tolerance"];
      ...
    }

* This is a declaration of a string variable named 's':

    std::string(s);

  So the following is a bug (in ~100% of the cases):

    std::lock_guard<std::mutex>(m_mutex);

  This will shadow a mutex living in a higher scope, or cause a redefinition error.
* -Wshadow-compatible-local is a useful shadow warning flag.
* ASAN is great.

# [Programming Conversations: Lecture 10](https://www.youtube.com/watch?v=vW14_vP23UU) (A. Stephanov)

* Minimal requirement for an ordering; transitivity: r(x, y) ∧ r(y, z) ⇒ r(x, z)
* Strict ordering ≡ non-reflexive ordering; ¬r(a, a)
  Non-strict ordering ≡ reflexive ordering; r(a, a)
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
* Issue with std::max() in the standard, std::max(a, b), with a ~ b, should
  return b instead of a.

# [Programming Conversations: Lecture 11](https://www.youtube.com/watch?v=LOYOxxiF_hA) (A. Stephanov)

* Fundamental properties of type in C/C++, sizeof and alignof. Why is alignment
  important? Some processors do not support arbitrary alignment.
* Types that support the following four concepts...:

    1. Default constructible
    2. Copy constructible
    3. Assignable
    4. Destructable

   ...are called semi-regular types. A regular type also knows the concept of
   equivalence.
* Equivalence (roughly) as defined by Leibniz:

    x = y <=> ∀p : p(x) = p(y)

# [Programming Conversations: Lecture 12](https://www.youtube.com/watch?v=1FE6C9kfGBI) (A. Stephanov)

* Defining equality is sometimes hard; for example, two priority queues are
  equal in case they will pop items in the same order, but that may be hard to
  compute up front.
* Representational equality of a type consisting of N parts; each part needs to
  be equal to the corresponding part. Memory pointed to by pointers is also
  considered to be part of a type in this context.
* [Axiom of Trichotomy](https://en.wikipedia.org/wiki/Trichotomy_(mathematics))
  is useful when implementing less-than relations.

# [Pragmatic Type Erasure: Solving Classic OOP Problems With An Elegant Design Pattern](https://www.youtube.com/watch?v=0I0FD3N5cgM) (Z. Laine)

* Boost.TypeErasure seemingly offers good generic type erasure functionality.

# [The Performance Addict's Toolbox](https://www.youtube.com/watch?v=DxP--1yEgKQ) (P. Steinbach)

* Interesting tools to measure hardware performance; dd, ior, memhog, stream
* Alternative profilers; ocperf, likwid, hotspot, quick-bench.com, google/benchmark
* 'False sharing'; occurs in a parallel application in case one thread reads
  from a cache line, and another thread writes to the same cache line. The
  cache coherency protocol needs to ensure that memory always has a comparable
  state; therefore these cache lines need to be reloaded over and over, causing
  effective sequentialization of parallel code.
* When benchmarking, also take the standard deviation of measurements into
  account!
* R Markdown; interesting tool for presenting analysis results.

# [So, You Inherited a Large Codebase...](https://www.youtube.com/watch?v=B2XtqVZcSdM) (D. Sankel)

* kythe is an interesting clang-based refactor tool.
* Look into levelization.

# [Effective CMake]() (D. Pfeifer)

* The library interface may change during installation. Use the
  `BUILD_INTERFACE` and `INSTALL_INTERFACE` generator expressions as filters:

    target_include_directories(Foo PUBLIC
      $<BUILD_INTERFACE:${Foo_BINARY_DIR}/include>
      $<BUILD_INTERFACE:${Foo_SOURCE_DIR}/include>
      $<INSTALL_INTERFACE:include>
      )

# [Back to the Basics! Essentials of Modern C++ Style](https://www.youtube.com/watch?v=xnqTKD8uD64) (H. Sutter)

* Argument for returning `unique_ptr` for factory functions; in case the return
  value is ignored, it does not cause a memory leak.
* Transferring ownership should use a value `unique_ptr` parameter.
* Pass pointers/references to functions that do not need to manage lifetime.
  Passing a const reference of a `unique_ptr` is senseless, just pass in either
  a pointer (in case the pointer might be null), or otherwise a const reference.
* Do not dereference a non-local, possibly aliased, shared pointer. Instead,
  pin the shared pointer using an unalias local copy:

    auto x = *some_shared_ptr;  // increase reference count
    f(*x);                      // f() takes a reference

  The same goes for calling a member function on the shared_ptr, where the
  member function possibly changes the parent object.
* Continue: ~1:17:00

# [Efficient Programming with Components, Lecture 2](https://www.youtube.com/watch?v=FUMPsmKnKv8) (A. Stephanov)

* Continue: Part 2, 6:00
* "There are ladies present, so I can not tell you everything I think about Scott Meyers."
* Thee fundamental laws of thought:
   - identity: P == P
     "Whatever is, is."
   - non-contradiction: ¬(P ∧ ¬P)
     "Nothing can both be and not be."
   - excluded middle: P ∨ ¬P
     "Everything must either be or not be."
* Why were implicit type conversions introduced to C? They did not have
  function overload support.
* Implicit type conversions; one step allowed for user-defined types, another
  step allowed for built-in types.
* std::cin << 42; // :o)

# [Efficient Programming with Components, Lecture 3](https://www.youtube.com/watch?v=sp_IBYVqMeQ) (A. Stephanov)

* Watched.

# [Efficient Programming with Components, Lecture 4](https://www.youtube.com/watch?v=4pSqzrbjq4Q) (A. Stephanov)

* The result of xor on a signed type is undefined, since the xor operation on
  the sign bit is undefined...!

  Part 2 (~9m)

# [Papers I Have Loved](...) (C. Moraturi)

* Evolving Virtual Creatures, Karl Sims, 1996
* PWL CONF (?)
* Quaternions, Shoemake, 1999

# [You Can Do Better Than std::unordered_map: ...](https://www.youtube.com/watch?v=M2fKMP47slQ) (Malte Skarupke)

* Very interesting talk on hash maps in general. Also, his blog is full of
  nicely written articles: https://probablydance.com.

# [C++ Mixins: Customization Through Compile Time Composition]() (Odin Holmes)

* Mixin initialization pseudo-code:

  ```
  template<typename... Ts>
  class composition : public call<detail::make_base<composition<Ts...>>, Ts...>
  {
  public:
    composition(std::tuple<Ts...>&& d) : data_(std::move(d))
    {
      ... // Some form of initialization (Odin uses abilities here to query
          // mixins to see whether they required initialization).
    }
  };
  ```

  Key here is to provide already constructed mixins to the composition!

# [Moving Faster: Everyday Efficiency in Modern C++](https://www.youtube.com/watch?v=J9yVA341zrw) (Alan Talbot)

* Watched.

# [The Principles of Clean Architecture](https://www.youtube.com/watch?v=o_TH-Y78tt4) (Bob Martin)

* Mitochondria are little bacteria like creates taking in glucose and oxygen,
  and producing water, carbondioxide, and energy. They have their own DNA and
  reproduction cycle. In a sense, we are in symbiosis with them. They come
  exclusively down from the mother. Mitochondria provide a great tool to
  determine common ancestors, since their DNA only changes due to random flips
  once in a while, the man does not influence their DNA. Using this approach,
  it was determined that our common 'mother' lived somewhere in east Africa
  some 120.000 years ago, at the start of the last great ice age. Very
  interesting.
