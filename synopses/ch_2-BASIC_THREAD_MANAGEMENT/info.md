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
