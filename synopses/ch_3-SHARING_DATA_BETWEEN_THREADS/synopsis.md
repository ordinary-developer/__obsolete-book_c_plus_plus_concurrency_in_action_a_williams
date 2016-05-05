Sharing data between threads
============================


Problems with sharing data between threads
------------------------------------------

*If all shared data is read-only, there's no problem, because the
data read by one thread is unaffected by whether or not another thread
is reading the same data*.

*Invariants* are statements that are alway true about a particular
data structure.

In concurrency, a *race condition* is anything where the outcome
depends on the relative ordering of execution of operations on two or
more threads. The threads race to perform their respective operations.

The C++ Standard also defines the term *data race* to mean the
specific type of race condition that arises because of concurrent
modification to a single object. Data races cause undefined behavior.

Problematic race conditions typically occur where completing an
operation requires modifiction of two or more distinct pieces of data.
Because the operation must access two separate pieces of data, these
must be modified in separate instructions, and another thread could
potentially access the data structure when only on of them has been
completed.


Avoiding problematic race conditions
------------------------------------

There are several ways to deal with problematic race conditions:
- to wrap you data structure with a protection mechanism, to ensure
  that only the tread actually performing a modification can see the
  intermediate states where the invariants are broken;
- to modify the design of your data structure and its invariants so
  that modifications are done as a series of indivisible changes, each
  of which preserves the invariants; this is generally referred to
  as lock-free programming;
- to handle the updates to the data structure as a transaction, just
  as updates to a database are done within a transaction; the required
  series of data modifications and reads is stored in a transaction
  log and then committed in a single step; if the commit can't 
  proceed because the data structure has been modified by another 
  thread, the transaction is restarted; this is termed
  software transactional memory (STM).


Protecting shared data with mutexes
-----------------------------------

Protecting shared data with *mutexe*s assumes that before accessing
a shared data structure, you *lock* the mutex associated with that 
data, and when you've finished accessing the data structure, you
*unlock* the mutex.

In C++, you can create a mutex by constructing an instance of 
std::mutex, lock it with a call to the member function lock(), and
unlock it with a call to the member function unlock().  
However, it isn't recommended practice to call the member functions
directly, because this means that you have to remember to call 
unlock() every code path out of a function, including those due to
exceptions.  
Instead C++ provides the std::lock_guard class template, which 
implements that RAII idiom for a mutex; it locks the supplied mutex
on construction and unlocks it on destruction, thus ensuring a locked
mutex is always correctly unlocked.

Guideline to follow for correct mutex using:
*Don't pass pointers and references to protected data outside the 
scope of the lock, whether by returning them from a function, storing
them in externally visible memory, or passing them as arguments to
user-supplied functions*.


Deadlocks
---------

Imagine that you have threads arguing over locks on mutexes: 
each of a pair of threads needs to lock both of a pair of mutexes
to perform some operation, and each thread has one mutex 
and is waiting for the other.
Neither thread can proceed, because each is waiting for the other
to release its mutex.
This scenario is called *deadlock*.

The common advice for avoiding deadlock is to always lock the two
mutexes in the same order: if you always lock mutex A before mutex B,
then you'll never deadlock.

It's worth noting that locking either one mutex or another inside 
the call to std::lock can throw an exception.
In this case, the exception is propagated out of std::lock.  
If std::lock has successfully acquired a lock on one mutex and 
exception is thrown when it tries to acquire a lock on the other 
mutex, this first lock is released automatically: std::lock provides
all-or-nothing semantics with regard to locking the supplied mutexes.

Although std::lock can help you avoid deadlock in those cases 
where you need to acquire two or more locks together, it doesn't help
if they're acquired separately.


Guidelines for avoiding deadlock
--------------------------------

A deadlock can occur anywhere that a thread can wait for another
thread to perform some action if the other thread can simultaneously
be waiting for the first thread, and it isn't limited to two threads.

The guidlines for avoiding deadlock all boil down to one idea:  
*don't wait for another thread if there's a chance it's waiting 
for you*.


- Avoid nested locks  
  (don't acquire a lock if you already hold one)
- Void calling user-supplied code while holding a lock
- Acquire locks in a fixed order
- Use a lock hierarchy


Transferring mutex ownership between scopes
--------------------------------------------

std::unique_lock is an example of a type that's *movable*
but not *copyable*. It can be used for transferring mutex ownership
between scopes


You must bear in mind two issues:
- *in general, a lock should be held for only the minimum possible
   time needed to perform the required operations*;
- *if you don't hold the required locks for the entire duration
   of anoperation, you're exposing yourself to race condition*.