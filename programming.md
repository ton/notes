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
