# Coding challenge for d4l 

## Task desc
Please write a program in your prefered language that will send out emails to recipients from a huge list (1 Mio entries) in a performant way. You do not need to send real emails but just fake the email sending by waiting for half a second.

## Approach

To solve this problem i'm going to use Python.  Because python is single threadedm, I decided to use multiprocessing library to parallelize the (mock) computations. I've defined a process-safe queue for the tasks which are then processes asynchronously by the consumers. For the optimal utilisation of resources it's good to spawn at least two workers for every core available.

This approach allows me to distribute task among adjustable number of workers and gain a massive speed up compared to a naive approch in which every message is sent out one by one in the main thread. 


### Testing:

I've tested the program on my 2018 Macbook Pro with 2.9 GHz 6-Core Intel Core i9 and 32GB RAM. 
In practice ``` multiprocessing.cpu_count() ``` showed 12 available CPU cores thus the program has spawned 24 workers. 

```bash

$ python main.py --jobs 10000 --workers 16
$ python main.py --jobs 100 --workers 4

```


#### 24 Consumers (2 per core)

- 10 emails : ca. 0.5 second
- 100 emails : ca. 2.53 seconds
- 1000 emails : ca. 21.1 seconds
- 10000 emails : ca. 209 seconds
- 100000 emails : ca. 2000 seconds ~ 1h 6 min 
- 1000000 emails : ca. 20 000 seconds ~ 5h 30 minutes

#### 196 Consumers (16 per core )
- 10 emails : 0.5 second
- 100 emails : 0.5 seconds
- 1000 emails : 3 seconds
- 10 000 emails : 27 seconds
- 100 000 emails : 274 seconds
