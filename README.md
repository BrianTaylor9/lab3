# Hash Hash Hash

This program implements three different hash tables, one with a serial implementation, one with a single mutex, and one with multiple mutexes, that are each safe to use concurrently.

## Building

To build the program, enter the lab3 directory, which should contain all of the source code files and the Makefile, and run the command "make".

## Running

To run the program, build it and execute the command "./hash-table-tester -t THREADS -s ENTRIES", where THREADS is the number of threads that will be used and ENTRIES is the number of entires for each thread to add to the hash table. The output should print the time it takes each implementation to run, as well as the number of entries missing from each hash table.

For example, executing the command "./hash-table-tester -t 8 -s 50000" with a reasonable implementation should produce an output that resembles the following:

"Hash table base: 1,001,527 usec
  - 0 missing
Hash table v1: 1,272,841 usec
  - 0 missing
Hash table v2: 342,846 usec
  - 0 missing"

## First Implementation

Describe your first implementation strategy here (the one with a single mutex).
Argue why your strategy is correct.

For the first implementation, there is a single mutex for the entire hash table. It is initialized when the table is created and destroyed when the table is destroyed. The mutex is locked at the beginning of the hash_table_v1_add_entry function and unlocked at the end of the function. This strategy is correct becuase the lock surrounds the add_entry function, meaning that only one thread can add an entry to the table at a time. Therefore, there is no possibility for multiple threads to modify the same linked list at the same time, thus eliminating race conditions and preventing any entries from being missing.

### Performance

Run the tester such that the base hash table completes in 1-2 seconds.
Report the relative speedup (or slow down) with a low number of threads and a
high number of threads. Note that the amount of work (`-t` times `-s`) should
remain constant. Explain any differences between the two.

Test 1 (low number of threads):
    number of threads: 4
    number of entries added: 100000
    base table time: 1,066,228 usec
    v1 time: 1,844,390 usec
    slow down (v1 time / base table time):

Test 1 (high number of threads):
    number of threads: 10
    number of entries added: 40000
    base table time: 1,170,265 usec
    v1 time: 1,544,354 usec
    slow down (v1 time / base table time):

The test with the low number of threads and high number of entries added has a greater slowdown because each time an entry is added, there is an overhead for locking and unlocking the mutex, so adding more entries leads to more overhead and a greater slowdown.


## Second Implementation

Describe your second implementation strategy here (the one with a multiple
mutexes). Argue why your strategy is correct.

For the second implementation, there is a unique mutex for each hash table entry (each linked list). The mutexes are initialized when the table is created and destroyed when the table is destroyed. A table entry's mutex is locked at the beginning of the hash_table_v2_add_entry function only if that table entry is being modified, and it is unlocked at the end of the function. This strategy is correct because the only chance for race conditions and missing entries occurs when multiple threads are modifying the same table entry, or linked list, at the same time, and these locks ensure that only one thread can modify a given table entry at a time. Multiple threads can concurrently modify different entries at the same time because separate entires are different lists with no shared memory, so there is no possibility for race conditions or unpredictable behavior.

### Performance

Run the tester such that the base hash table completes in 1-2 seconds.
Report the relative speedup with number of threads equal to the number of
physical cores on your machine (at least 4). Note again that the amount of work
(`-t` times `-s`) should remain constant. Report the speedup relative to the
base hash table implementation. Explain the difference between this
implementation and your first, which respect why you get a performance increase.

## Cleaning up

To cleanup, enter the lab3 directory and execute the command "make clean", which will remove all binary files.
