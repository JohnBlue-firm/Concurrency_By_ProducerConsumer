
# Side project object
- A demonstartion of concurrency via pthread.
- Comparsion among design with and without concurrency based on proccess time.

# Comparison result
||thread number|CPU time used|Real-time elapsed|
| :---: | :---: | :---: | :---: |
|without concurrency|only main|0.001935 seconds|0.016363 seconds|
|with concurrency|1+1+1+1|0.004293 seconds|0.006486 seconds|
||1+2+2+1|0.004239 seconds|0.004039 seconds|
||1+3+3+1|0.004596 seconds|0.003946 seconds|

hint:
- **CPU time used** is the total CPU bound period.
- **Real-time elapsed** is the period the whole proccess have been through.
- **The sleep function** in C suspends thread, but will not consume CPU time.
- The clock function un C often used to measures CPU time used by the program. It counts the number of clock ticks used by the CPU to execute the program.
- Other functions like gettimeofday, clock_gettime, or time can provide real time stamp including the span in sleep and I/O bound.

explain of the result:
- In term of **CPU time used**, the concurrency design doesn't beat the original design. The poossible explaination is because it will cost CPU **additional time to manage threads**. However, I also have embeded sleep function (for mocking the CPU bound or I/O bound) in the program that didn't count on the CPU time.
- In term of **Real-time elapsed**, the concurrency design ourperforms the original design. Because the concurrency design is aiming to break down a task into severval parts (either the part is CPU bound or I/O bound) and solve the parts in a parallel manner, this result is quite coherent with the expectation.

# Conculsion
- It is nice to consider concurrency into design. A concurrency design could make the proccessing of tasks ***become more interactive***. To put it simple, it can overlapping the proccessing period when tasks can work in parallel.
- However, it is not a better in all cases. It is cruicial to examine:
-- if the problem or tasks could be break down and perform in parallel manner?
-- do we have sufficient resources such as CPU cores, memory, or I/O bandwidth to support the concurrentcy design?
-- the possibility to have sufficient performance gains after applying.
Because concurrentcy design is more complex and could cost extra overhead such as context switch or difficulty to debug or fix, it is better to ***think before leap***.

-----
# Programs description
### Task description
- Simple design (only one thread)
- Concurrency design
![...](/ConcurrencyDesign.png)
### Program description
- Simple design
  * sequential proccessing x >> x += 1 >> x *= 2 >> prinf x
  * only main function calling producer_consumer function
- Concurrency design
-- ***Buffer struct*** for synchronization of data
  * this struct will not only provide a space for produser and consumer, but make sure the data flow work in order (x >> x += 1 >> x *= 2 >> prinf x).
  * ***produce function*** to add item to the buffer, wait if the buffer is full, and decrease empty count.
  * ***consume function*** to remove item from the buffer, wait if the buffer is empty, and decrease full count.

  -- ***The functions*** for independent sub-tasks
  * ***producer and consumer***: create list of x / printf list of result(x)
  * ***add_one and multiple_two***: perform x + 1 / perform x * 2
  * ***main function***: initializes the buffers and locks, creates the threads, and use signal to implement timeout mechanism.

