# RValue references

rvalue references
-----------------

There are two types of references in C++:
- lvalue references (references to lvalues)  
  the term lvalue refers to things that can be o the left side of an
  assignment expression - names objects, objects allocated on the
  statck or heap, or members of other objects (thinsg with a defined
  storage location)
- rvalue references (references to rvalues)  
  the term rvalue refers to things that can occur only on the right
  side of an assignment expression - literals and temporaries


Move semantics
--------------

Rather that copying the contents of an rvalue parameter, you can just
move the contents.

This can be beneficial when you wish to move the parameter value into 
a local or member variable without copying, because although an rvalue
reference parameter can bind to rvalues, within the function itself
it is treated as an lvalue
