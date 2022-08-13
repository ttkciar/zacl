## zacl

**STATUS: Work-in-progress**

ZACL is no longer a language effort.  Instead it is a framework for the [D programming language](https://dlang.org/).

D is a better language than I would have made by myself, and it has already absorbed more than twenty years of development and debugging.  The "special" features I wanted to implement in ZACL can instead be implemented as D libraries, so why not?  Also, D is a really fun language to learn and use.  It's like C without the pain of C.  It makes me happy.

### ZACL for Concurrency

ZACL will support "Job" classes which instantiate to remote entities with a transparent RPC interface.

A Job class represents an entity which may run in a thread, subprocess or remote process (the "location").

Data stored in a Job instance is stored in its associated location.  Methods called via Job instances are run in the associated location.

Accessing a Job's data and methods look just like accessing a local object's data and methods, but actually perform RPC calls behind the scenes.

This makes for an abstracted way to distribute data and/or processing across multiple cores or hosts.  To run foo() across a dozen threads, just instantiate a dozen thread Job instances and call the foo() method for each of them.  To shard an associative array across a thousand-host data cluter, just instantiate a thousand remote Job objects with associative array attributes in them and whatever you put in those attributes will be stored on the remote hosts.

A trivial example:

``class Example : RemoteJob {
    int i = 0;
    int j = 0;
    int get_sum() { return i + j; }
}

auto remote = new Example;  // instance of Example resides on a remote host

remote.i++;  // Actually an RPC call telling remote instance to increment i

int sum = remote.get_sum();  // Actually an RPC call telling remote instance to run get_sum

auto sump = remote.async.get_sum();  // returns a Promise instance immediately while get_sum runs remotely
``

### ZACL for Rapid Development

D is already highly expressive for a static strongly-typed language, and ZACL aims to close the expressiveness gap between D and dynamic languages (Python, Perl, Ruby, Javascript).

It will accomplish this by implementing an autocoercing dynamic type system which can be used alongside D's static type system, and by creating aliases and wrappers for often-used modules in the D standard library, to give them short names and interfaces with dynamic types.

### ZACL for Performance

Well, sort of.  It's actually D which is performant (about the same as C).  The idea is that programmers can use idiomatic ZACL for parts of the code which are not performance-critical, and use D for parts of the code which need C-like performance and memory footprint.

This is akin to the common practice of developing software in Python or Perl and re-writing the critical path in C when more performance is needed (or using an existing library which is implemented in C).

Since ZACL and D are actually the same language, though, the fast-running code and the fast-developed code can live side-by-side in the same source file, rather than having Python in one file and C in another file.

### So Where is ZACL Now?

It's still in the early stages of development.  I spun my wheels for about two years trying to figure out how to get the semantics I wanted from the dynamic type system via D's compile-time metaprogramming, and only recently concluded it couldn't work that way, that I would have to make it run-time dynamic.

So I only recently (in 2022) started implementing the dynamic type system.  I wish I'd started it sooner.  It's coming together nicely, but isn't usable yet.

The "Job" class implementation will come later, in part because the language itself is evolving a little quickly for my comfort.  If I implemented the "Job" class, and then the dynamic type system, then by the time I was done with the type system the "Job" class might need to be re-implemented due to language churn.

I'm hoping D will settle down in the time it takes me to implement the type system.  Also, implementing the "Job" class will be a lot easier if I can use the dynamic type system to implement it.

