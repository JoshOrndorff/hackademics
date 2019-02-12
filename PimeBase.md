Prime Base
==========

Representing Natural numbers as their Prime Factorizations
------

Humans represent integers daily as decimal numbers. Computer-scientists know it is routine to convert numbers between decimal and other bases especially binary and hexadecimal. Algebra students know rationals. Here I explore an alternative representation of the natural and perhaps rational numbers (not the reals). I'm curious whether calculations such as gcd, lcm, etc would run faster with reasonable tradeoffs (adding becomes hard).

Encoding: First Observation
----------------------
Begin with an example. To encode the number 12, we first calculate it's prime factorization. 12 = {2, 2, 3}.

The first several natural numbers are summarized in the following table.
<pre>
  | 2 | 3 | 5 | 7 | 11|
--+--------------------
1 |   |   |   |   |   |
2 | 1 |   |   |   |   |
3 |   | 1 |   |   |   |
4 | 2 |   |   |   |   |
5 |   |   | 1 |   |   |
6 | 1 | 1 |   |   |   |
7 |   |   |   | 1 |   |
8 | 3 |   |   |   |   |
9 |   | 2 |   |   |   |
10| 1 |   | 1 |   |   |
11|   |   |   |   | 1 |
12| 2 | 1 |   |   |   |
</pre>

Once we know the factorization of a number we can represent in as a tuple.
<pre>
# | tuple
--+----
1 | ()
2 | (1)
3 | (0, 1)
4 | (2)
5 | (0,0,1)
</pre>

So instead of writing a number as it's decimal (or any other) base form. We'll represent it directly as this tuple. We already know there is a 1:1 correspondence. We can write down made-up numbers like (1, 4, 0, 0, 5) and not know their "value". It feels weird at first, but it is no weirder than writing down a number like 7834278159 and not knowing its factorization.

Encoding: Recursion
--------------------
Now that we know how to write values as prime bases instead of decimals, we may as well do it for the numbers inside the tuples.
<pre>
1 = ()
2 = (())
3 = (0,())
4 = ((()))
5 = (0,0,())
6 = ((),())
7 = (0,0,0,())
8 = ((0,()))
</pre>
This does leave a little wrinkle because we now have to write 0 somehow.



Calculations
------------
**Easy to calculate**
* equlity ==, !=
* lcm
* gcd

**Hard to calculate**
* ordering <, >, <=, >=
* value

Research questions
-------------
* Would it be useful to draw numbers as mountain ranges?
* Does the depth mean anything?
* How quickly (value-wise) does the series (), (()), ((())), (((()))) grow?
* How quickly do (()), ((),()), ((),(),()) grow?


Rough example code
-------------------
I experimented very briefly with some java code around this idea in 2016. https://github.com/JoshOrndorff/PrimeBase

Extension to Rationals
------------------
How do you write negatives? Color the factors?
