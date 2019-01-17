A Blockchain-Native Clock
==============

Since the original bitcoin whitepaper the value of blockchain as a time stamping system has been known. However the notion of time native to a blockchain is that of block height and correlating with wall-clock time has been a matter of injecting wall-clock timestamps into blocks.

I've written a [prototype in rholang](https://github.com/JoshOrndorff/librho/blob/master/BlockchainPatterns/clock.rho).

Clocks as tick-counters
-------------------------


Defining Tick
-------------
There are lots of possibilities
* Any message from the clock's "owner"
* Any message that passes a multisig
* A proof of work solution

All of these are likely useful for various applications. But the first few are easy to understand and require no further treatment. Instead I'll focus on a proof of work clock which has the desireable property of being permissionless to advaance.


Calibration
-----------
In many cases we actually want this thing to have a stable relationship with wall-clock time. If we want the ticking to be permissionless, then we need a feedback mechanism. Bitcoin adjusts the difficulty according to the DAA. I wonder if we could generalize that to use a feedback mechanism where users can vote faster or slower.
