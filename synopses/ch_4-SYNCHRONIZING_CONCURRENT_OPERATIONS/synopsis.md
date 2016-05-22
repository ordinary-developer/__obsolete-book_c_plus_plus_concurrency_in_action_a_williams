SYNCHRONIZING CONCURRENT OPERATIONS
===================================

Common
------

The C++ Standard Library provides facilities to synchronize operations
between threads in the form of *condition variables* and *futures*


Waiting for an event or other condition
---------------------------------------

Conceptually, a condition variable is associated with some event or
other condition, and one or more threads can wait for that condition
to be satisfied. When some thread has determined that the condition
is satisfied, it can then notify one or more of the threads waiting
on the condition variable.

The Standard C++ Library provides two implementations of a condition
variable:
- std::condition_variable;
- std::condition_variable_any.
Both of these are declared in the <condition_variable> library header.
In both cases, they need to work with a mutex in order to provide
appropriate synchronization; the former is limited to working with
std::mutex, whereas the latter can work with anything that meets some
minimal criteria for being mutex-like.

Because std::condition_variable_any is more general, there is the 
potential for additional costs in terms of size, performance, or
operating system resources, so std::condition_variable should be 
preferred unless the additional flexibility is required.


