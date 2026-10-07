# HW.3_EECE.4811

Q1. 
First, briefly explain how the reader-writer lock works. In particular, explain the purpose of:
- readers -> counts how many reader threads are inside the critical section currently
- lock -> protects the readers counter so that two readers cannot update it simultaneously
- writelock -> controls access to the actual shared resource, before writing, a writer must hold this lock
- why the first reader acquires writelock -> because once at least one reader is active, writers need to be blocked
- why the last reader releases writelock -> because once the reader count is no longer at least 1 (zero), no readers are using the shared resource anymore and writers can now safely enter

Q2. Now suppose a developer changes rwlock_acquire_readlock() to the following.....The developer argues that this should be equivalent because readers is still updated while holding lock. Is the modified implementation correct?

No it is not correct due to this line if (rw->readers == 1), this happens after the lock has been released, therefore another reader can change rw->readers before the first reader checks it. To implement this correctly, you would have to do the following:

sem_wait(&rw->lock);
rw->readers++;

if (rw->readers == 1)
    sem_wait(&rw->writelock);

sem_post(&rw->lock);

Here, the transition from 0 readers to 1 cannot be interrupted and the first reader always blocks writers.

Q3. Finally, the original implementation in Figure 31.13 can cause writer starvation. Briefly explain how this can happen. Is writer starvation the same kind of problem as the bug above? Why or why not?

This is not the same problem as the code bug, the modified version has a race condition that can break mutual exclusion and allow a writer and readers to access the resource simultaneously. Writer starvation does not violate mutual exclusion and our lock is working as intended, however it is unfair because a writer may never get a turn.




