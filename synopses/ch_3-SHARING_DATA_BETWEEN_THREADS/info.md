sharing data between threads
============================


problems with sharing data between threads
------------------------------------------

*if all shared data is read-only, there's no problem, because the
data read by one thread is unaffected by whether or not another thread
is reading the same data*

*invariants* are statements that are alway true about a particular
data structure

in concurrency, a *race condition* is anything where the outcome
depends on the relative ordering of execution of operations on two or
more threads; the threads race to perform their respective operations

the C++ Standard also defines the term *data race* to mean the
specific type of race condition that arises because of concurrent
modification to a single object; data races cause undefined behavior

problematic race conditions typically occur where completing an
operation requires modifiction of two or more distinct pieces of data;
because the operation must access two separate pieces of data, these
must be modified in separate instructions, and another thread could
potentially access the data structure when only on of them has been
completed


avoiding problematic race conditions
------------------------------------

there are several ways to deal with problematic race conditions:
- to wrap you data structure with a protection mechanism, to ensure
  that only the tread actually performing a modification can see the
  intermediate states where the invariants are broken
- to modify the design of your data structure and its invariants so
  that modifications are done as a series of indivisible changes, each
  of which preserves the invariants; this is generally referred to
  as lock-free programming
- to handle the updates to the data structure as a transaction, just
  as updates to a database are done within a transaction; the required
  series of data modifications and reads is stored in a transaction
  log and then committed in a single step; if the commit can't 
  proceed because the data structure has been modified by another 
  thread, the transaction is restarted; this is termed
  software transactional memory (STM)


protecting shared data with mutexes
-----------------------------------

protecting shared data with *mutexe*s assumes that before accessing
a shared data structure, you *lock* the mutex associated with that 
data, and when you've finished accessing the data structure, you
*unlock* the mutex

guideline to follow for correct mutex using:
*don't pass pointers and references to protected data outside the 
scope of the lock, whether by returning them from a function, storing
them in externally visible memory, or passing them as arguments to
user-supplied functions*


deadlocks
---------

imagine that you have threads arguing over locks on mutexes: 
each of a pair of threads needs to lock both of a pair of mutexes
to perform some operation, and each thread has one mutex 
and is waiting for the other;  
neither thread can proceed, because each is waiting for the other
to release its mutex;  
this scenario is called *deadlock*

the common advice for avoiding deadlock is to always lock the two
mutexes in the same order: if you always lock mutex A before mutex B,
then you'll never deadlock

it's worth noting that locking either one mutex or another inside 
the call to std::lock can throw an exception; 
in this case, the exception is propagated out of std::lock;  
if std::lock has successfully acquired a lock on one mutex and 
exception is thrown when it tries to acquire a lock on the other 
mutex, this first lock is released automatically: std::lock provides
all-or-nothing semantics with regard to locking the supplied mutexes

although std::lock can help you avoid deadlock in those cases 
where you need to acquire two or more locks together, it doesn't help
if they're acquired separately


guidelines for avoiding deadlock
--------------------------------

a deadlock can occur anywhere that a thread can wait for another
thread to perform some action if the other thread can simultaneously
be waiting for the first thread, and it isn't limited to two threads

the guidlines for avoiding deadlock all boil down to one idea:  
*don't wait for another thread if there's a chance it's waiting 
for you*


- avoid nested locks  
  (don't acquire a lock if you already hold one)
- void calling user-supplied code while holding a lock
- acquire locks in a fixed order
- use a lock hierarchy


