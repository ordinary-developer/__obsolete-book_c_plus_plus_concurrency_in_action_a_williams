managing threads
================

basic thread management
-----------------------

to launch a thread you must include the *<thread>* header

to compile add the *-lpthread* option:
```sh
$ g++ --std=c++14 -lpthread --output temp *.cpp *.hpp
```

*std::thread* works with any callable type (so you can use an 
instance of a class with a function call operator)

every c++ program has at least one thread, which is started by 
the C++ runtime: the thread running main()

once you've started your thread, you need to explicitly decide 
- whether to wait for it to finish  
  (by joining with it - by calling *join()* on the associated 
   std::thread instance)  
  (*join()* can be called only once for a give thread of execution)
- or leave it to run on itw own  
 (by detaching it - by calling *detach()*)


joining
-------

one common way to handle the scenario when the tread function holds
pointers or references to local variables and the thread hasn't 
finished when the function exits, is to make the tread function
self-contained and copy the data into the thread rather than 
sharing the data  
alternatively, you can ensure that the thread has completed execution
before the function exists by joining the tread

if you don't need to wait for a thread to finish, you can avoid this
exception-safety issue by detaching it; 
this breaks the association of the thread with the std::thread object
and ensures that std::terminate() won't be called when the
std::thread object is destroyed, even the thread is still running
in the brackground


detaching
---------

you can only call *t.detach()* for a std::thread object t when
*t.joinable()* return true

after the call completes, the std::thread object is no longer 
associated with the actual thread of execution and is therefore
no longer joinable


passing arguments to a thread function
--------------------------------------

passing arguments to a thread function is as simple as passing 
additional arguments to the std::thread constructor;  
but by default the arguments are *copied* into an internal storage;
even if the corresponding parameter in the function is expecting
a reference

references are also copied, so when you pass a reference, a thread
will create a copy of object referenced by reference and then create
a reference to this *new just copied* object, if you want to pass
really a reference use std::ref(ref_name), but you must remember
of scoping of refs (the initial ref must exist in the memory before
thread will finish its work)

if you want to move an object (e.g., std::unique_ptr) "to a thread",
you must use std::move

threads are *movable* but not *copyable*
