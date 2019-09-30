C++
---

* Stroustroup's diagram on value categories in C++11:


      lvalue, i!m           xvalue, im          prvalue !im
                  *             *             *
                   \           / \           /
                    \         /   \         /
                     \       /     \       /
                      \     /       \     /
                       \   /         \   /
                        \ /           \ /
                         *             *
              glvalue, i                 rvalue, m

* Sean Parent mentions using output functions instead of output iterators for
  functions that produce some form of output: https://twitter.com/SeanParent/status/1010240274369470465

* Regarding the usage-model for exceptions, from: https://isocpp.org/wiki/faq/exceptions#how-exceptions

  "Use throw only to signal an error (which means specifically that the
  function couldn’t do what it advertised, and establish its postconditions)."

C
-

* When writing a signal handler; assume that the handler can be called at any
  moment in time, even while executing the signal handler itself, assuming that
  the same handler is registered for multiple different signals. Therefore,
  ensure proper exclusive access:
  ```
  volatile sig_atomic_t fatal_error_in_progress = 0;

  void
  fatal_error_signal (int sig)
  {
    /* Since this handler is established for more than one kind of signal, 
        it might still get invoked recursively by delivery of some other kind
        of signal.  Use a static variable to keep track of that. */
    if (fatal_error_in_progress)
        raise (sig);
    fatal_error_in_progress = 1;
    ...
  ```

Linux
-----

* Measuring the working set size of an application (http://www.brendangregg.com/blog/2018-01-17/measure-working-set-size.html)

  Resident memory: memory allocated and mapped to pages

* Pin a program on a certain core: taskset -c 0 ./myprogram

Algorithms
----------

* Very interesting post on integer modulo and using the Golden Ratio to evenly
  map numbers from a certain range onto a smaller range (used in the context of
  hash maps).
* A binary relation R is total if for every pair of elements a and b, a R b, or
  b R a, or both.

Style
-----

* Yoda conditions: if (const == variable) (https://en.wikipedia.org/wiki/Yoda_conditions).
