

Encrypsulation
==============

## Abstract
Encrypsulation a scheme for two mutually distrustful counterparties to cooperatively exchange secrets without the risk of getting stiffed, assuming a trusted third party, or requiring security deposits. The secrets are cryptographically encapsulated in trapdoor puzzles and exchanged. Through an interactive game the difficulty of each puzzle is steadily reduced until both secrets are revealed together. Caveat, this may assume that SNARKs are more magical than they actually are. More literature review necessary.

## Problem Statement
Alice is buying Bob's van. In the not-so-distant future, both the money and the van key will be informational secrets. Alice doesn't want to pay Bob before she receives the key, and Bob doesn't want to give her the title until he has been paid.

### Naive Solution: Trusted Third Parties
The low-hanging fruit for such cooperation scenarios is to introduce a third party who both Alice and Bob trust with the full value of the exchange. In this case the third party must be trusted both to not steal the secrets for himself, and not leak them to the public.

### Security Deposits
Blockchain-based smart contract platforms such as Ethereum or RChain make many TTP protocols practical by eliminating the risk of scamming the participants for personal gain. However blockchain is not the entire answer as the chain itself is a public ledger and thus storing the secrets on the blockchain would leak them not only to the counterparty, but also to the general public. The second half of the solution comes from zkSNARKs.

1. Alice SNARKs that she knows the secret Bob desires
2. Alice sends a security deposit more valuable than the van into a smart contract
3. Alice Observes that Bob has also sent a deposit
4. Alice encrypts her secret to Bob's public key and generates a SNARK that the encryption was correct
5. Alice sends the ciphertext of her secret and the SNARK to the contract which then releases her security deposit
6. If Bob fails to send his encrypted secret and SNARK to the contract he will never receive his security deposit back, thus the economically rational strategy is to send his secret.

This solution requires a constant number of interactions regardless of your trust level with the counterparty, but requires high security deposits and the presence of a smart contracting platform.

## Encrypsulation Protocol Description
This protocol is symmetrical for Alice and Bob and will be described from Alice's perspective. Bob is always doing the same thing at the same time.

1. Alice encodes her secret in a computationally difficult puzzle with a trapdoor. (eg. She encrypts it to a particular public key). The cost to solve the puzzle without the trapdoor must exceed the value of the assets being exchanged. This is the economic security base case. It is always safe to give out such a puzzle because Bob has negative incentive to solve it.

2. Alice generates a zkSNARK arguing that her secret is correctly encoded in the puzzle AND she knows the trapdoor information.

3. Alice sends the puzzle and SNARK to Bob and receives the same from him. She confirms that his SNARK is valid.

The key idea is that they will each dial back their security gradually and in proportion to one another. Thus they reveal both secrets much more quickly by cooperating than is possible by brute forcing the puzzles all the while never taking more than a small risk.

4. Alice and Bob generate shared cryptographic randomness (citation needed) to determine who will act first. Assuming Alice acts first she sends Bob the first bit of her trapdoor along with a SNARK arguing that it is the correct first first bit. (More precisely she is arguing that she knows some trapdoor that starts with the bit she just sent.)

5. Bob replies with the first bit of his trapdoor and a similar SNARK.

6. Since Alice went first in the previous round, Bob will go first in this round. He reveals the next bit of his trapdoor as he did in step 5. In practice steps 5 and 6 will be combined into one.

7. Alice goes next, and the process continues until the entire trapdoors have been exchanged. At any point either party can speed up the process by revealing more than the minimum one bit of the trapdoor.

This technique avoids the necessity of expensive security deposits at the expense of requiring an interactive game.


## Mathematical assessment of security
Isaac I need your help with the wording and style.

Basic idea is that the game runtime is linear in the key length, but the brute force runtime is exponential in it.


## Practical Considerations
We need to make sure the information density is constant throughout the trapdoor information. Otherwise Eve could reveal the first ten useless bits before ever revealing the actual valuable trapdoor.

It would be great if this were non-interactive. I guess the argument then would be that the message size is linear in trapdoor length and the security is exponential.

With real-world network latency introduced, at some point it becomes faster to brute force the puzzle than wait for more trapdoor information to arrive over the network. Common courtesy for an honest participant is to immediately send all remaining secret trapdoor information in case the counterparty is computationally challenged or simply wants to save the energy. Standard implementations should follow this courtesy.

When the counterparties have considerably different computational abilities, the protocol is not secure as stated because there will be some threshold where the stronger party will be able to brute force the puzzle but the weaker won't. As long as the counterparties can agree out-of-protocol about a computational handicap, the deal can go on with only slight modification. The stronger party would simply pre-reveal (preveal #ridonkulous) bits of her trapdoor until the table was level.

When trust is so low that even a factor of two advantage is unacceptible, the granularity can still be increased. Rather than revealing entire bits of the ultimate trapdoor, the trapdoor is chunked up and each chunk encoded in another puzzle as described above. For example my trapdoors is split into 16-bit chunks and each of them is encrypsulated in a 24-bit key




## Things to Reference
* https://eprint.iacr.org/2005/187.pdf Assumption of court to enforce fraud laws later. No guarantee of game termination. Game is totally symmetrical; no one has to go first. 
* https://www.researchgate.net/publication/221498631_How_to_Generate_and_Exchange_Secrets_Extended_Abstract Only seen the abstract, but it sounds like it might do what I attempted here. Possibly better.


## Appendix: Original Wording for later review
They each generate a second snark proving that their secret is locked in a puzzle of medium difficulty. How much should the difficulty be reduced each round? Intuitively, enough that this process doesn't take forever, but not so much that you're screwed if the counterparty stiffs you on this round.
