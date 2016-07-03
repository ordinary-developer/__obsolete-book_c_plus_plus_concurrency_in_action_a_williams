C++ MEMORY MODEL AND OPERATIONS ON ATOMIC TYPES
===============================================

Atomic operations and types in C++
----------------------------------

An *atomic operation* is an idivisible operation.

The standard atomic types can be found in the <atomic> header.

The std::atomic<> class template has the next operations:
- load();
- store();
- exchange();                |   assignment from and convertion to the
- compare_exchange_weak();   |-> user-defined type
- compare_exchange_strong(). |


Each of the operations on the atomic types has an optional 
memory-ordering argument that can be used to specify the required
memory-ordering semantics.

Operations are divided into three categories:
- *store* operations, which chan have the next types of ordering:
  - memory_order_relaxed;
  - memory_order_release;
  - memory_order_seq_cst;
- *load* operations, which can have the next types of ordering:
  - memory_order_relaxed;
  - memory_order_consume;
  - memory_order-acquire;
  - memory_order_seq_cst;
- *read-modify-write* operations, which can have the next types
  of ordering:
  - memory_order_relaxed;
  - memory_order_consume;
  - memory_order_acquire;
  - memory_order_release;
  - memory_order_acq_rel;
  - memory_order_seq_cst.
The default ordering for all operations is "memory_order_seq_cst".



Operations on std::atomic_flag
------------------------------

*std::atomic_flag* is the simplest standard atomic type, which 
represents a Boolean flag. Objects of this type can be in one of 
two states: set or clear.  
It is the only type guaranteed to be lock-free.

Objects of type std::atomic_flag must be initialized with
ATOMIC_FLAG_INIT. This initializes the flag to a clear state.

If the std::atomic_flag object has static storage duration, it will
always be initialized by the time of the first operation on the flag.

After your flag object initialization, you can
- destroy it  
  (calling desctructor);
- clear it (store operation)  
  (calling the "clear()" member function);
- set it and query the previous value (read-modify-write operation)   
  (calling the "test_and_set()" member function).

You can't copy-construct another std::atomic_flag object from the 
first, and you can't assign one std::atomic_flag to another.
All operations on a atomic type are defined as atomic, and 
assignment and copy-construction cannot be atomic because involve
two objects.


Operations on std::atomic<bool>
-------------------------------

std::atomic<bool> is still not copy-constructible or copy-assignable,
you can construct it from a nonatomic bool.

Assignment operators that atomic types support return values
(of the corresponding nonatomic type) rather than references.
