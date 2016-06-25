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

