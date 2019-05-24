Crypto-at-the-door
==================

The Crypto-at-the-door policy (or is locker room a better name?) is a design for the rhoVMs registry, which enforces object capability principles by giving each user personal registry. The registry can only be accessed upon initial deployment of code, and only when the deployment is signed by the locker owner's contract. This design allows removing the signature verification schemes from rholang (on the blockchain) at all.

Advantages
----------

### No msg.sender or access control lists
In ethereum, substrate, and many other blockchains, the access control list pattern is ubiquitous and relies on runtime or smart contract code having access to the identity (public key) or the code deployer. RChain coop has begun to implement this design in their new (as of early 2019) wallet (I forget the new preferred vocab, sorry) design. The strengths of strick ocap design are explicated in the [picturebook of secure cooperation](http://erights.org/talks/efun/SecurityPictureBook.pdf and throughout historical rchain writings espicially by Dan Connolly and Mike Stay)

### No ambient authority
Registry lookups should happen on initial deploy, not wherever one feels like throughout the code. This problem has been written about (among many other places) [by Artur](https://gist.github.com/ArturGajowy/d89803a7320cb65567f3b3f2229f908a#b-make-the-deployers-identity-only-available-once-during-a-dpeloy)

### Lookups are naturally commutative
Kyle has explained why registry lookups need to commute if a blockchain-based rho-vm is ever to have significant concurrency. This proposal gives a new structure to lookups which is different from sending a lookup request on a channel and thus the read-only lookups do not appear non-determinacy on a channel.

### Generalize the deploys-from-the-right-key-can-spend-rev solution
We do need public key crypto _somewhere_, but that place should not be specifically in the wallet contract. It should be part of the deploy step.

### Can send around ASTs and avoid re-parsing
Currently rchain, the most mature rhovm implementation, gossips strings of rholang code around the network requiring each node to re-parse the ast. This is so that deployers do not include unforgeable names that they should not have access to. In the crypto-at-the-door system, it is obvious when a user should have access to a particular name (if it's in the user's registry), so there is no hard in passing around ASTs. If a particular name is not in the deployers registry, the deployment should simply be discarded.

Example Code
------------

These examples use the keyword `lookup` rather then `new` to explicitly distinguish registry lookup vs new name generation. Further they use rhoxy syntax which does not have the distinction between names and processes. I'd love to talk more about that too, but it's off topic here.

### Ambient authority
This code will work in crypto-at-the-door
``` scala
lookup stdout(`rho:io:stdout`) in {
  new debugLogger in {

    // debugLogger contract requires explicit delegation of an output stream
    contract debugLogger(outStream, message) {
      outStream!("DEBUG: " + message)
    }
  } |

  debugLogger!("Test message")
}
```

However this version will not work because it performs an "ambient authority" lookup of the URI `rho:io:stdout`
``` scala
new debugLogger in {
  contract debugLogger(message) {

    // Cannot perform arbitrary lookups in the middle of code
    // This line fails, which is the desired behavior
    lookup stdout(`rho:io:stdout`) in {
      outStream!("DEBUG: " + message)
    }
  }
} |

debugLogger!("Test message")
```


### No ACLs
Allowing anyone to "call a contract" and test the identity is an antipattern. This shouldn't work
```scala
new launchRockets, bouncer in {

  let sender = /* Do stuff that gets deployer's public key */ |
  bouncer!(pk) |

  contract bouncer(pk) {
    // This is an access control list (of length one)
    if (pk == /* trusted user */) {
      launchRockets!(Nil)
    }
  }
}
```

A better approach is to let the bouncer _be_ the registry. Put the bouncer in the lockerroom. Once the deployer has the necessary capabilities, no further indentification is ever required.
```scala
// Lookup succeeds iff deploy is signed properly
lookup launchRockets(`my:private:registry:launchRockets`) in {
  launchRockets!(Nil)
}
```

### Multi sig equals rights amplification
No need to address multi"sig" at the cryptographic level (except maybe performance, I have no idea about that) because it can be written in pure rholang and composed with any other superpower like launching rockets, spending rev, voting, whatever. The rev vault need not be a one-off anymore.
```scala
// Setting up the multisig.
// Either party can deploy this part.

let jointChecking = /* Create a new thing that alice and bob share */ |

new alicePropose, bobPropose in {
  contract alicePropose (P, bobCh) {
    new bobApprovalCh in {
      bobCh!(bobApprovalCh) |
      for (true <- bobApprovalCh) {
        // Bob has approved alice's proposal, proceed
        P // in coop syntax this is *P. Run the proposal.
      }
    }
  } |

  contract bobPropose (P, aliceCh) {
    // Similar code to above.
  }
}
```

More elaborate schemes for more parties, different threshold, etc exist. This is a simple example.
