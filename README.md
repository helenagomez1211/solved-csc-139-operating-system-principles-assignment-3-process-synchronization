Download Link: https://assignmentchef.com/product/solved-csc-139-operating-system-principles-assignment-3-process-synchronization
<br>
This assignment is based on the project in Chapter 7 of the textbook with slight modification. For the cited figures/sections, you need to refer to the corresponding part in the textbook.

<h1>1          Introduction</h1>

In this project, you will design a programming solution to the bounded-buffer problem using the producer and consumer processes shown in Figures 7.1 and 7.2. The solution presented in Section 7.1.1 uses three semaphores: empty and full, which count the number of empty and full slots in the buffer, and mutex, which is a binary (or mutual exclusion) semaphore that protects the actual insertion or removal of items in the buffer. For this project, you will use standard counting semaphores for empty and full and a mutex lock, rather than a binary semaphore, to represent mutex. The producer and consumer — running as separate threads — will move items to and from a buffer that is synchronized with the empty, full, and mutex structures. You are required to use the Pthread package to solve this problem in this project.

<h1>2          The Buffer</h1>

Internally, the buffer will consist of a fixed-size array of type bufferitem (which will be defined using a typedef). The array of bufferitem objects will be manipulated as a circular queue. The definition of bufferitem, along with the size of the buffer, can be stored in a header file such as the following:

/* buffer.h */ typedef int buffer_item;

#define BUFFER_SIZE 5

The buffer will be manipulated with two functions, insertitem() and removeitem(), which are called by the producer and consumer threads, respectively. A skeleton outlining these functions appears as:

#include “buffer.h”

/* the buffer */

buffer item buffer[BUFFER SIZE]; int insert item(buffer item item) { /* insert item into buffer return 0 if successful, otherwise return -1 indicating an error condition */ }

int remove item(buffer item *item) { /* remove an object from buffer placing it in item return 0 if successful, otherwise return -1 indicating an error condition */ }

The insertitem() and removeitem() functions will synchronize the producer and consumer using the algorithms outlined in Figures 7.1 and 7.2. The buffer will also require an initialization function that initializes the mutual-exclusion object mutex along with the empty and full semaphores.

The main() function will initialize the buffer and create the separate producer and consumer threads. Once it has created the producer and consumer threads, the main() function will sleep for a period of time and, upon awakening, will terminate the application. The main() function will be passed three parameters on the command line:

<ol>

 <li>How long to sleep before terminating</li>

 <li>The number of producer threads</li>

 <li>The number of consumer threads</li>

</ol>

A skeleton for this function appears as follows:

#include “buffer.h”

int main(int argc, char *argv[]) {

/* 1. Get command line arguments argv[1],argv[2],argv[3] */

/* 2. Initialize buffer */

/* 3. Create producer thread(s) */

/* 4. Create consumer thread(s) */

/* 5. Sleep */

/* 6. Exit */

}

<h1>3          The Producer and Consumer Threads</h1>

The producer thread will alternate between sleeping for a random period of time and inserting a random integer into the buffer. Random numbers will be produced using the randr(unsigned int *seed) function, which produces random integers between 0 and RANDMAX safely in multithreaded processes. The consumer will also sleep for a random period of time and, upon awakening, will attempt to remove an item from the buffer. An outline of the producer and consumer threads appears as:

#include &lt;stdlib.h&gt; /* required for rand_r(…) */

#include “buffer.h”

void *producer(void *param) { buffer_item item;

while (true) {

/* sleep for a random period of time */ sleep(…);

/* generate a random number */

item = rand_r(…); if (insert_item(item)) fprintf(“report error condition”);

else printf(“producer produced %d
”,item);

}

void *consumer(void *param) { buffer_item item;

while (true) {

/* sleep for a random period of time */ sleep(…); if (remove item(&amp;item)) fprintf(“report error condition”);

else printf(“consumer consumed %d
”, item); }

<h1>4          Thread Creation in the Pthread Package</h1>

Creating threads using the Pthreads API is discussed in Section 4.4.1. Coverage of mutex locks and semaphores using Pthreads is provided in Section 7.3. The following code sample demonstrates the Pthread APIs for creating a new thread:

#include &lt;pthread.h&gt;

void *thread_entry(void *param) { /* the entry point of a new thread */ … }

int main(…) { pthread_t tid; pthread_attr_t attr;

/* get the default attribute */ pthread_attr_init(&amp;attr);

/* create a new thread */

pthread_create(&amp;tid, &amp;attr, thread_entry, NULL); …

}

The Pthread package provides pthreadattrinit(…) function to set the default attributes for the new thread. The function pthreadcreate(…) creates a new thread, which starts the execution from the entry point specified by the third argument.

<h1>5          Mutex Locks in the Pthread Package</h1>

The following code sample illustrates how mutex locks available in the Pthread API can be used to protect a critical section:

#include &lt;pthread.h&gt;

pthread_mutex_t mutex;

/* create the mutex lock */ pthread_mutex_init(&amp;mutex, NULL);

/* acquire the mutex lock */ pthread_mutex_lock(&amp;mutex); /*** critical section ***/

/* release the mutex lock */

pthread_mutex_unlock(&amp;mutex);

The Pthread package uses the pthreadmutext data type for mutex locks. A mutex is created with the pthreadmutexinit(&amp;mutex, NULL) function, with the first parameter being a pointer to the mutex. By passing NULL as a second parameter, we initialize the mutex to its default attributes. The mutex is acquired and released with the pthreadmutexlock(…) and pthreadmutexunlock(…) functions. If the mutex lock is unavailable when pthreadmutexlock(…) is invoked, the calling thread is blocked until the owner invokes pthreadmutexunlock(…). All mutex functions return a value of 0 with correct operation; if an error occurs, these functions return a nonzero value.

<h1>6          Semaphores in the Pthread Package</h1>

The pthread package provides two types of semaphores – named and unnamed. For this project, we use unnamed semaphores. The code below illustrates how a semaphore is created:

#include &lt;semaphore.h&gt; sem_t sem;

/* create the semaphore and initialize it to 5 */ sem_init(&amp;sem, 0, BUFFER_SIZE);

The seminit(…) creates a semaphore and initialize it. This function is passed three parameters:

<ol>

 <li>A pointer to the semaphore</li>

 <li>A flag indicating the level of sharing</li>

 <li>The semaphore’s initial value</li>

</ol>

In this example, by passing the flag 0, we are indicating that this semaphore can only be shared by threads belonging to the same process that created the semaphore. A nonzero value would allow other processes to access the semaphore as well. In this example, we initialize the semaphore to the value 5.

For the semaphore operations wait() and signal() discussed in class, the Pthread package names them semwait() and sempost(), respectively. The code example below creates a binary semaphore mutex with an initial value of 1 and illustrates its use in protecting a critical section: (Note: The code below is only for illustration purposes. Do not use this binary semaphore for protecting critical section. Instead, you are required to use the mutex locks provided by the Pthread package for protecting critical section.)

#include &lt;semaphore.h&gt; sem_t sem_mutex;

/* create the semaphore */ sem_init(&amp;sem_mutex, 0, 1);

/* acquire the semaphore */ sem_wait(&amp;sem_mutex);

/*** critical section ***/

/* release the semaphore */ sem_post(&amp;mutex);

<h1>7          Compilation</h1>

You need to link two special libraries to provide multithreaded and semaphore support using the command

“gcc &lt;files&gt; -lpthread -lrt”.

<h1>8          Testing</h1>

You can start use one producer thread and one consumer thread for testing, and gradually use more producer and consumer threads. For each test case, you need to make sure that the random numbers generated by producer threads should exactly match the random numbers consumed by consumer threads (both their orders and their values). Make sure your code can be compiled and work on Athena server correctly.


