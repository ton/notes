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

# [Programming Conversations: Lecture 10](https://www.youtube.com/watch?v=vW14_vP23UU) (Alexander Stephanov)

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
* Total ordering uses equality as the equivalence relation, and has to adhere
  to the following requirements:
    1. Anti-symmetry; a ≤ b ∧ b ≤ a, then a = b
    2. Transitivity
    3. Trichotomy; a < b ∨ a > b ∨ a = b; equivalent, the connex property: a ≤ b ∨ b ≤ a
* Weak ordering, the third requirement reads:
    3. Trichotomy; a < b ∨ a > b ∨ a is equivalent to b
  So, an equivalence relation other than equality is used. Think for example
  about an employee struct, that defines a less than operator based on the
  surname only.
* Issue with std::max() in the standard, std::max(a, b), with a ~ b, should
  return b instead of a.

# [Programming Conversations: Lecture 11](https://www.youtube.com/watch?v=LOYOxxiF_hA) (Alexander Stephanov)

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

# [Programming Conversations: Lecture 12](https://www.youtube.com/watch?v=1FE6C9kfGBI) (Alexander Stephanov)

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

# [Efficient Programming with Components, Lecture 2](https://www.youtube.com/watch?v=FUMPsmKnKv8) (Alexander Stephanov)

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

# [Efficient Programming with Components, Lecture 3](https://www.youtube.com/watch?v=sp_IBYVqMeQ) (Alexander Stephanov)

* Watched.

# [Efficient Programming with Components, Lecture 4](https://www.youtube.com/watch?v=4pSqzrbjq4Q) (Alexander Stephanov)

* The result of xor on a signed type is undefined, since the xor operation on
  the sign bit is undefined...!
* The requirement on the type of a template function min() is that it is
  totally ordered. A partial ordering will not work, since then not every
  element is comparable.
* Equivalence does not mean equality. Equivalence that implies a certain
  equivalence relation holds, which might be defined based on other criteria
  than complete equality.
* Some notes integrated into the notes for 'Programming Conversations, Lecture
  10'.

# [Efficient Programming with Components, Lecture 5](https://www.youtube.com/watch?v=8bWx2WTYvB8) (Alexander Stephanov)

* When introducing a version of `min()`, that has a default comparison
  operator, introduce a wrapper function around the more generic function
  taking the comparison function. This allows taking the address of the version
  of `min()` that has a default comparison operator.
* Issue with `std::max` in the standard, `std::max(a, b)`, with `a ~ b`, should
  return `b` instead of `a`, for reasons of stability of the input arguments.
  Suppose you would want to use `std::max` to implement a sort function, it is
  hard to use it for a stable sort algorithm, since it will swap equivalent
  values.
* When writing the equivalent of `std::min_element` that takes an input range,
  you want to return an iterator, because:

    1. You might want to update the value afterwards (or delete it).
    2. You may be interested in its position in the input range.
    3. The input range might be empty, so you would otherwise need to find a
       bottom value for the value type of the range.
* Alex prefers not to use prefix/postfix operators inside expressions for
  maintainability reasons. "You don't have to write everything in a single
  line."
* Difference between ForwardIterator and InputIterator; an InputIterator deals
  with streams, where the pointed to values are ephemeral, and can not be hold
  on to. For a ForwardIterator, this is not the case.
* Nice min/max algorithm that only takes 3(N / 2) comparisons vs. the naive 2(N
  - 1) number of comparisons. Every iteration, take two candidate elements and
  compare those to figure out a candidate min, and candidate max element.

# [Efficient Programming with Components, Lecture 6](https://www.youtube.com/watch?v=lWSYE-hRw0s) (Alexander Stephanov)

* Rewatch.

# [Efficient Programming with Components, Lecture 7](https://www.youtube.com/watch?v=yUZ3y5w3f0o) (Alexander Stephanov)

* You are allowed to reuse the name of a member variable for the constructor
  parameter, since the constructor parameter is in an outer scope.

# [Efficient Programming with Components, Lecture 8](https://www.youtube.com/watch?v=RdQM3c-0eiA) (Alexander Stephanov)

* Chose to implement functionality for a class with a stand-alone function in
  case the function can be implemented using the existing public interface of a
  class (~34m, part 2).

# [Efficient Programming with Components, Lecture 9](https://www.youtube.com/watch?v=5gPpZLUvvEE) (Alexander Stephanov)

* When implementing complex template functions, start with the body, fill in
  the template parameters later (~23m, part 2).

# [Efficient Programming with Components, Lecture 10](https://www.youtube.com/watch?v=rFgiMPZnaiw) (Alexander Stephanov)

* iterator_traits exists as a type function to map a certain type to another
  type. Some types (notably pointers) do not allow the definition of member
  types for this purpose.

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
          // mixins to see whether they require initialization).
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

# [Embind and Emscripten: Blending C++11, JavaScript, and the Web Browser](https://www.youtube.com/watch?v=Dsgws5zJiwk) (Chad Austin)

* Interesting example on using Web Audio with C++, will be fun to experiment
  with a bit.
* #DEFINE EMSCRIPTEN_HAS_UNBOUND_TYPE_NAMES 0
* Calling JavaScript from C++ is a concern in terms of performance, research
  this a bit.

# [C++ on the Web: Let's Have Some Serious Fun](https://www.youtube.com/watch?v=jXMtQ2fTl4c) (Dan Gohman)

* The only way for WASM modules to communicate with the outside world is
  through imports and exports (~30m).
* No plans for direct communication with native shared object code (~53m).

# [Docker Based C++ Dependency and Build Management](https://www.youtube.com/watch?v=lmIc0MgWBEI) (Jason Rice)

* Would it be possible to use a Dockerfile to manage the fractal-thirdparty
  build process...? Multi-stage builds might work in this context.

# [Let's Make a Web Match-3 Game in C++14](https://www.youtube.com/watch?v=GHA1HVOILqQ) (Kris Jusiak)

# [GNU Guix: The Functional GNU/Linux Distro That’s a Scheme Library](https://www.youtube.com/watch?v=WOolAnW9a6Y) (Ludovic Courtès)

* initrd: initial ramdisk, responsible for mounting the root filesystem for
  example.

# [Patterns and Techniques Used in the Houdini 3D Graphics Application](https://www.youtube.com/watch?v=2YXwg0n9e7E) (Mark Elendt)

* Interesting tidbits about paged COW arrays (~47m).
* Houdini uses OpenCL exclusively for GPU programming.
* Use jemalloc as the underlying memory allocator (probably because of the
  heavy use of threading in Houdini...?).
* USD: Universal Scene Description (Pixar).

# [C++ in Elvenland](https://www.youtube.com/watch?v=CVg7CYVV3KI) (Serge Guelton)

* `inline` implies weak symbols, the only guaranteed effect of the `inline` keyword.

# [Debugging Linux C++](https://www.youtube.com/watch?v=V1t6faOKjuQ) (Greg Law)

* Using richer debug information with: '-ggdb3'...
* Good ~/.gdbinit basics:

    set history save on
    set print pretty on
    set pagination off
    set confirm off

* Watchpoints are interesting:

    watch foo                   # stop when foo is modified
    watch foor if foo > 10      # watch foo when it gets larger than 10
    rwatch foo                  # watch foo when it is read
    watch foo thread 3          # watch foo when thread 3 modifies it
    watch -l foo                # watch location

* Perform an action across threads:

    thread apply all backtrace
    thread apply 1-4 print $p
    thread apply all backtrace full     # backtrace including local variables

* Dynamic printf (wtf):

    dprintf fn,"This print does not have to be compiled: %s", my_string

  Settings:

    set dprintf-style gdb|agent|call
    set dprintf-function fprintf

* Catchpoints:

    catch catch                # catch when an exception is caugt
    catch syscall nanosleep    # stop at the nanosleep syscall
    catch syscall 100          # stop at systemcall number 100

* GCC address sanitizer:

    gcc -fsanitize=address -static-libasan ...

* Poor man's profiler:

    ltrace -t cmd              # prefix every library call with a custom
                               # command (wall clock time measurement for example)

# [Generic Programming](https://www.youtube.com/watch?v=wJpxWHuZQY) (Sean Parent)

* Monoid: data set that has an operation that is associative on it.
* Mathematics is about discovery, about what is, not about discovery, about
  what you wish it were. Based on the ideas of Euler. Goes against the ideas
  from the 'Nicolas Bourbaki'-group.
* Software is associated with algebraic structures.
* Concept: set of axioms satisfied by a data type and a set of operations on
  it.

# [RVO: Harder Than It Looks](https://www.youtube.com/watch?v=hA1WNtNyNbo) (Arthur O'Dwyer)

* Returning big objects is done through a return slot address, which is passed
  into the function using %rdi. It basically acts as a pointer. Note that the
  pointer points into the stack.
* Copy elision cannot happen when:

    - The compiler does not yet know which copy to elide (there is a
      conditional in the return statement).
    - The physical location of the to-be-elided object is outside of our
      control, for example, `return x` where `x` is a global variable, or a
      function parameter for example. A copy needs to be made in these cases.
    - Slicing of the return type happens. In case we need to return a type T,
      and allocate and return a type U, where U is-a T, but of bigger size, we
      cannot allocate the variable of type U in the return slot since that is
      smaller and only fits variables of type T.

* Never write `return std::move(x)` in C++11. It might prevent copy-elision,
  and in C++11 implicit move when returning values happens anyway, there is no
  need to cast to an rvalue.
* Implicit move is not performed in case the first parameter of the move
  constructor of the type that is returned by the function specification is not an rvalue reference of the
  object's type that iret.

# [OOP Is Dead, Long Live Data-oriented Design](https://www.youtube.com/watch?v=yy8jQgmhbAU) (Stoyan Nikolov)

* When using templates for type erasure (instead of an abstract base class), do
  not worry about the fact that you can no longer have a single vector with
  base class pointers anymore, just create a vector for each known concrete
  type.

# [Expect the Expected](https://www.youtube.com/watch?v=PH4WBuE1BHI) (Andrei Alexandrescu)

# [The Networking TS in Practice: Testable, Composable Asynchronous I/O in C++](https://www.youtube.com/watch?v=hdRpCo94_C4) (Robert Leahy)

* Asynchronous completion handler that moves itself into the completion handler
  instead of relying on shared_ptr:

     template<typename Handler>
     void async_action(..., Handler&& handler)
     {
       ...
       auto fn = [..., h = std::forward<Handler>(handler)](std::error_code ec) mutable
       {
         if (ec) h(ec, ...);
         else asio::async_action(..., std::move(h));
       };
       io_object.async_action(std::move(fn));
     }

# [Design for Performance](https://www.youtube.com/watch?v=m25p3EtBua4) (Fedor Pikus)

* When there is nothing to optimize, there is hot data. Sometimes a single
  access function stands out, but not its callers, calls are scattered all over
  the place. Two options; access less data, or organize data differently, both
  are design issues.

# [Goals for Better Types - Implement Complete Types](https://www.youtube.com/watch?v=mYrbivnruYw) (Sean Parent)

* Transactional assignment can be implemented using a copy and move:

  T& operator=(const T& b) {
    T tmp = b; *this = std::move(tmp); return *this;
  }

  The advantage is that in case the copy throws, the assigned to object is no
  left in an invalid state. This is not the most optimal implementation of
  copy, but the performance gains for the optimal one are neglible.
* Continuing on this; comment from the audience, why not use a sink argument
  here. Response from Sean is that this messes with the rules of auto generated
  assignment functions; using a sink argument causes the type effectively to
  have no move assignment operator in case that type is used within the context
  of another type U. Thus, U does not get an auto-generated move assignment
  operator (verify this..., find defect number).
* The functional programming guys say; we have no pointers! But they still have
  indexes into list; i.e. they still have to deal with relations. Indexes into
  lists have the same problems as pointers.

# [More Modern CMake](https://www.youtube.com/watch?v=y7ndUhdQuU8) (Deniz Bahadir)

* target_link_libraries should always be used with the PRIVATE, PUBLIC, and
  INTERFACE keywords, otherwise it triggers some deprecated behavior.
  Also, target_link_libraries can not be used to set build requirements
  (PRIVATE) of a target from a different directory!
* add_library() can be called without sources, then target_sources() can be
  used to add sources to the target, specifying again PRIVATE, PUBLIC, and
  INTERFACE.
* An IMPORTED target is a target that can be referred to, but which is not
  build .
* x PRIVATE y: usage requirements of y becomes build requirements of x
  x INTERFACE y: usage requirements of y becomes usages requirements of x
  x PUBLIC y: usage requirements of y becomes usage/build requirements of x
* For OBJECT libraries its requirements are only ever propagated to direct
  descendants, and only if that direct descendant is not an OBJECT library
  itself. This means that usage requirements of an OBJECT library can only
  become build requirements for its direct descendants! (3.12 restriction
  only).

# [Handmade Hero: Day 19](https://www.youtube.com/watch?v=qFl62ka51Mc) (Casey Moraturi)

* The theory of units and how they cancel out when multiplying ratio's;
  dimensional analysis (~17m).

# [Type Deduction and Why You Care](https://www.youtube.com/watch?v=wQxj20X-tIU) (Scott Meyers)

* By value lambda captures preserve cv-qualifiers. The only place in C++ where
  cv-qualifiers are preserved in a type deduction context (~41m).
* By default, the function call operator for a lambda is const qualified
  (~41m).
* auto is never a reference type!
* Interesting observations on decltype(auto), to be able to influence auto
  function return type deduction (~58m).

# [SICP, Lecture 2A](https://www.youtube.com/watch?v=eJeMOEiHv8) (G. J. Sussman)

* Higher order functions are functions taking functions as their arguments that
  produce functions as values.

# [Category Theory 1.1: Motivation and Philosophy](https://www.youtube.com/watch?v=I8LbkfSSR58) (B. Milewski)

* Object Oriented paradigm does not mix well with concurrency; object oriented
  programming abstracts exactly the wrong things:

    1) mutation of state
    2) sharing of state

  Thus, Object Oriented programming is abstracting over data races by design.

# [Category Theory 1.2: What is a category?](https://www.youtube.com/watch?v=p54Hd7AmVFU) (B. Milewski)

* The major tools in our arsenal are:
    - Abstraction
    - Composition
    - Identity
* Equality (identical), and 'identical for all intents and purposes';
  isomorphism.
* Composition and identity define category theory.
* A category consists of:
    - a class of objects
    - with some arrows (morphisms) between them
* Objects have no properties. Morphisms have no properties except that they
  have a beginning and an end:

    a  * --------> * b

* Composition: g ∘ f. g after f, in a picture:

    a     f      b     g      c
    * ---------> * ---------> *

* Morphisms f and g are composible in case f ends where g starts.
* Identity: every object needs to have an identity morphism (an arrow pointing to itself), such that:

    g ∘ id(a) = g, and id(a) ∘ f = f

* Axiom: (h ∘ g) ∘ f = h ∘ (g ∘ f), i.e. associativity
* Example of a category from the real world, type category, with as objects
  types, and as the morphisms functions.

# [Performance Matters](https://www.youtube.com/watch?v=r-TLSBdHe1A) (Emery Berger)

* Link order and environment variable size (moves the program stack) can have a
  larger impact on performance than using -O3 over -O0.

# [Speed is found in the minds of people](https://www.youtube.com/watch?v=FJJTYQYB1JQ) (Andrei Alexandrescu)

* Nice examples of using Boolean arithmetic to prevent branches.

# [Better Code: Datastructures](https://www.youtube.com/watch?v=sWgDk-o-6ZE) (Sean Parent)

* `stable_partition_position`; `stable_partition` that projects the predicate
  from a position in the input range to some other range.

# [C++ Seasoning](https://www.youtube.com/watch?v=qH6sSOr-yk8) (Sean Parent)

* Demonstrates how to avoid having to put pointers to some polymorphic type in
  a container, by means of type erasure. Starts around the 1h mark.
