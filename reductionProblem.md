Problem posed by Isaac DeFrain

> Cool little combinatorics problem:
>
> How many reductions does the following term have?
>
> `x!(*a) | x!(*b) | for(z <- x){y!(*z)} | y!(*c) | y!(*d) | for(z <- y){x!(*z)}`


Rearrange the code for indentation

```java
 1|
 2|new a, b, c, d, x, y in {
 3|  // The x channel stuff
 4|  x!(*a) |
 5|  x!(*b) |
 6|  for(z <- x){
 7|	   y!(*z)  // Send down to the y channel stuff
 8|	 } |
 9|
10|
11|  // The y channel stuff
12|  y!(*c) |
13|  y!(*d) |
14|  for(z <- y){
15|    x!(*z)  // Send up to the x channel stuff
16|  }
17|}
```

Observe there will be exactly two comm events before this process is reduced to quiescence.
The first over the channel `x` and the second over `y`. In both cases several sends are competing
to comm with those delicious receives.

Waiting to comm over `x` (line 8)
* line 6
* line 7
* line 17

Waiting to comm over `y` (line 16)
* line 14
* line 15
* line 9

An initial but incorrect thought is to just choose one of the three sends waiting for
each receive. 3 * 3 = 9 reductions.

But that's incorrect because one pair of sends are mutually exclusive. Specifically lines
9 and 17 cannot both send. Thus there are 8 possibilities.

If an ordered list of reductions is required the number increases. In the 4 cases where no crosstalk happens between the x stuff and the y stuff, the two reductions can happen in either order to lead to the same quiescent state. Thus there are 12 reduction paths.
