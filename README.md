# Hash Time Locked Contract for Ethereum

The Hash Time Locked Contract (HTLC) arose from a suggested *atomic swap* implementation [due to Tier Nolan][atomic-swap-tiernolan].  An atomic swap is a way to make two transfers, on possibly different blockchains, such that either both succeed or both fail.  The two transfers are not necessarily simultaneous in time.

This initial implemenation was for Bitcoin-like blockchains and required each party creating a transaction with the output requiring not only the other's signature but the pre-image of a hash.  The maker of the first such transaction creates the hash.  Then the other party creates another such transaction with the same hash.  When the first party unlocks his funds using the hash pre-image and signature, the second party learns the pre-image and can then use it to unlock his funds.  

However, there is an issue here with either party backing out at different stages of this protocol.  For example, if both transactions get mined but the first party backs-out and never finishes the swap, the second party's funds remain locked (so is the first party's but presumably such a malicious actor doesn't care).  As another example, after the first party's transaction gets mined, if the second party never makes his transaction, the first party's funds remain locked.  So both parties need a way to get their locked coins refunded.  

In Bitcoin-like blockchains, there's no way to invalidate a transaction, only a way to specify a time in the future when the transaction becomes valid.  Thus, in order to allow a party to backout and get refunded if the other party doesn't follow through, there's a bit of finagling with secondarty transactions.  In fact, at that time, there was no way to even lock an output to be spendable in the future.  Instead, one locked the output with a 2-of-2 multisig and then created another transaction spending that output with the `nlocktime` field set in the future.  Before initiating the swap proper, each party would sign the other's secondary transaction, enabling a timelocked refund.  Later, the `OP_CTLV` (check-timelock-verify) op-code was proposed and accepted, which simplified matters and led to the HTLC.



[atomic-swap-tiernolan]: https://bitcointalk.org/index.php?topic=193281.msg2224949#msg2224949
