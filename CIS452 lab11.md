1. First build main-race.c. Examine the code so you can see the (hopefully obvious) data race in the code. Now run helgrind (by typing valgrind \--tool=helgrind ./main-race) to see how it reports the race.
• Does it point to the right lines of code?  
• What other information does it give to you?

It does print the right lines  
It also tells you when threads are created

2. What happens when you remove (e.g., comment out) one of the offending lines of code?  
   

It doesn’t show any errors

3. Add a lock around one of the updates to the shared variable. What does helgrind report?  
   

It reports there are 2 errors

4. Now add locks around both. What does helgrind report?  
   

No errors

5. Next examine main-deadlock.c. This code has a problem known as deadlock (which we discuss in much more depth in a forthcoming chapter). Based on this code,
•Describe what a deadlock is.  
•Why specifically does this code have a deadlock?

It’s when there is a chain of threads waiting for each other to release locks to continue, but are unable to release any locks because they are waiting.

This code has a deadlock because thread1 could grab lock1, and thread2 could grab lock2, and then thread1 would wait on lock2 to be available, and thread2 would wait on lock1 to be available

6. Run helgrind on this code. What does it report?  
   

It reports an error that a lock order principle is violated.

7. Examine main-deadlock-global.c.
•Does it have the same problem that main-deadlock.c has?  
•Why or why not?  
•Should helgrind be reporting the same error?  
•What does this tell you about tools like helgrind?

It doesn’t have the same problem because there is a lock before going into the section where the deadlock could happen. The deadlock would only happen if both threads enter that section at the same time, so the “g” lock prevents that. 

Valgrind shouldn’t report the same error, but it does anyway.

8. Look at main-signal.c. This code uses a variable (done) to signal that the child is done and that the parent can now continue. Why is this code inefficient? (what does the parent end up spending its time doing, particularly if the child thread takes a long time to complete?)  
   

The parent just spins and takes up CPU time while waiting for the child. 

9. Run helgrind on this program.
•What does it report?  
•Is the code correct?

It reports an error where there is a conflict between the main thread accessing “done” and the child thread updating “done”  
The code is correct. 

10. Now look at the slightly modified version of the code found in main-signal-cv.c. This version uses a condition variable to do the signaling (and associated lock).  
* Why is this code preferred to the previous version?  
* Is it correctness, or performance, or both?

Pthread\_cond\_wai  
It is preferred, because the instead of taking up CPU time, the parent thread just sleeps until the child signals it to wakeup  
It has better performance when the time to context switch twice is less than the time the parent would wait for the child to signal it.

11. Once again run helgrind on main-signal-cv. Does it report any errors?  
    

No
