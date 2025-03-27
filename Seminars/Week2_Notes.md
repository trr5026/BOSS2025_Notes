## What is SegWit?

* * *

1.  Is a non-segwit node considered a full node?  
    techically yes, practically no (can't validate non-segwit tx's, which comprise big % of tx's)
    
2.  What is the difference between a hard and soft fork?
    
    - forks are protocol updates that change aspects of the protocol.
        
    - A hard fork is a protocol change that is NOT backwards-compatible. It causes the network to split into two distinct networks. One that follows the old consensus rules, one that follows the new ones
        
    - A soft-fork is a protocol change that IS backwards-compatible.
        

## SegWit in Mastering Bitcoin / SegWit video w/ Jimmy Song

* * *

1.  How should one understand ANYONE_CAN_SPEND outputs? Do nodes with and without the SegWit upgrade behave differently while validating or enforcing?
    
2.  [Optional] What is the difference between wrapped SegWit and native SegWit addresses? (SegWit Wallet Development)
    

Jimmy song video notes:

- What is segwit?
    
    - fix transaction malleability (big time for lightning network - why?)
        - before segwit, transactions were malleable in that they could be slightly altered but still remain valid. However the altering would change the txid (TXID = hash(inputs + outputs + signatures)
            - padding leading zeros to r or s of DER encoded signatures (0x1F -> 0x001F same value, different bytes)
            - flipping s-value low vs. high (proposed in [BIP62](https://github.com/bitcoin/bips/blob/master/bip-0062.mediawiki) which was later withdrawn. PR6769 merged this update into bitcoin core in October 2015 - SCRIPT_VERIFY_LOW_S)
            - script sig modification by adding harmless/bening op_codes i.e. OP_NOP or OP_DROP after pushing data
        - after segwit, txid = hash(inputs + outputs). Signature moved to witness so none of the above fuckery would affect the txid
        - how did this impact lightning?
            - commitment tx's are pre-signed to spend funding tx's output using txid. If the txid is changed before it's mined, it will fail to broadcast, breaking the payment channel
    - reduce network transmission
        - no more scriptsig field to broadcast, less data used
    - increase transaction throughput
        - block weight limit change to 4MB (4 million weight units)
    - enable future upgrades
- P2WPKH (pay to witness pubkey hash)
    
    - like P2PKH except segregates scriptsig data (moves to witness)
    - new script
    - different data sent to pre vs. post segwit nodes
        - 0x00 in scriptsig for pre-segwit nodes
        - segwit marker/flag, witness program (sighash/commitment hash aka condition required to satisfy script and spend coin)
    - OP_0 + 20 bytes = 22 bytes TOTAL
- ScriptPubKey
    
    - P2PKH: [OP_DUP + OP_HASH160 + 20-byte pubkey hash + OP_EQUALVERIFY + OP_CHECKSIG]
    - P2WPKH: [OP_0 <20-byte pubkey hash>
    - P2WSH: [OP_0 <32-byte pubkey hash>
    - P2SH: [OP_HASH160 <20-byte script> OP_EQUAL]
- ScriptSig
    
    - P2PKH: <signature> <pubkey>
    - P2WPKH: empty! (signature/pubkey moved to witness script)
    - P2WSH: empty! (moved to witness script)
    - P2SH: <redeemscript> args

&nbsp;

&nbsp;

## Transaction Malleability

* * *

1.  How is ECDSA malleable?
    - See notes below:
    - TL:DR - 2 solutions for elliptic curve equation (r,s), (r,-s). If you know s, you know -s and can create different txid via hash

Notes

* * *

- padding DER-encoded signatures with leading zeros
    
- low vs. high s-value (r,s) = (r,-s) 2 solutions for the same
    
- ### What Are r and s in ECDSA?
    
    - **Context**: In an ECDSA signature (used in Bitcoin txs like P2PKH), the signature is a pair (r,s) (r, s) (r,s).
    - **Not x, y Coordinates**:
        - **r**: The x-coordinate of a random point R=k⋅G R = k \cdot G R=k⋅G (where G G G is the generator point, k k k is a nonce).
        - **s**: A scalar computed as s=k−1(h+r⋅d)mod  n s = k^{-1} (h + r \cdot d) \mod n s=k−1(h+r⋅d)modn (where h h h is the tx hash, d d d is the private key, n n n is the curve order: ~2256−4.3×1077 2^{256} - 4.3 \times 10^{77} 2256−4.3×1077).
    - **Misconception**: (r,s) (r, s) (r,s) aren’t directly the (x,y) (x, y) (x,y) of a curve point from y2=x3+7 y^2 = x^3 + 7 y2=x3+7. r r r is an x-coordinate, but s s s is a derived value, not y.
    - s incorporates the tx hash and private key, not curve geometry
    
    #### How It Works
    
    1.  Pick a random k k k (nonce).
    2.  Compute R=k⋅G R = k \cdot G R=k⋅G, get Rx R_x Rx​ (x-coordinate of R).
    3.  r=Rxmod  n r = R_x \mod n r=Rx​modn.
    4.  s=k−1(h+r⋅d)mod  n s = k^{-1} (h + r \cdot d) \mod n s=k−1(h+r⋅d)modn.
    5.  Signature = (r,s) (r, s) (r,s).

TL:DR: knowing only the signature (not needing private key), one can choose the opposite s-value to change the signature and hence transaction. Segwit fixes this.

&nbsp;

&nbsp;

## SegWit Benefits

* * *

1.  Why do second layer protocols (like Lightning) require transaction malleability to be fixed?
    - commitment txid can't be changing or else it breaks payment channel. If someone opens a channel with a given txid, and that txid is changed then broadcast, if someone with original txid attempted to close the channel, they wouldn't be able to. Risk of force-close, loss of funds

&nbsp;

benefits

- malleability fixes
    - wallets tracking bitcoins
    - users spending unconfirmed tx's
    - lightning network
- linear scaling of sighash operations
    - helps people sending larger/more complex tx's due to saving in both fees + tx size
- increased security
    - jbioreajb
- script versioning
    - script version byte - can add op_codes without requiring hard fork
- reduce utxo growth
    - signatures cost 75% less than data that impactds utxo set size

&nbsp;

## SegWit's Impact on Scalability

* * *

1.  What was the quadratic sighash problem prior to Segwit? How does [BIP 143](https://github.com/bitcoin/bips/blob/master/bip-0143.mediawiki) solve this?
    
    - Problem: prior to segwit, each signature verification required hashing entire tx including all input scriptsigs
    - O(n) x O(n) = O(n^2) issue.
    - algorithm to check a specific tx input:
        - strip other inputs from tx
        - replace input script we're checking w/ script of output (utxo) it's trying to spend
        - hash resultant tx twice w/ SHA256
        - check that signature correctly signed hash result from #3
    - BIP143 solution:
        - pre-computes key data ONLY ONCE rather than hashing entire tx for each input
            - removes one of the O(n) terms such that hashing scales linearly w/ # of inputs, not quadratically
        - hashPrevouts (hash of all input outpoints) computed ONCE
        - hashSequence (hash of all input sequences) computed ONCE
        - hashOutputs (hash of all outputs) computed ONCE
        - now hashing each input requires only a small fixed set of values
2.  What rationale was used to decide on the 4 MB SegWit block weight (3 x old_tx_bytes + segwit_tx_bytes), instead of say a 2 MB block weight (old_tx_bytes + segwit_tx_bytes)?
    
    - compromise between scalability, backward compatibility, and network security
    - maintain effective throughput w/o exceeding 1MB base limit
        - legacy nodes don't need to process blocks larger than 1MB, only see non-witness data
    - witness data 'discounted' to incentivize segwit adoption
        - [3 x base_size + witness size] means witness data discounted by 75% in block
    - 4MB weight limit allows actual worst-case size of 2-2.3 MB tx data
    - keep block propagation and validation efficient

## Advanced SegWit

* * *

1.  How do blocks commit to witness data?
    
    1.  segwit introduces a new commitment structure in bitcoin block header to ensure blocks commit to witness data properly. This is done via witness merkle root included in coinbase tx
        1.  witness merkle tree constructed with both txid's and witness data
        2.  witness root hash (aka witness commitment) computed from witness merkle tree
        3.  witness commitment is inserted as LAST output in coinbase tx
        4.  witness commitment output contains OP_RETURN script w/ witness merkle root
            - this ensures that witness data is included in block validation
    2.  witness merkle root construction:
        - txid: traditional txid legacy format
        - wtxid: witness txid (includes witness data)
        - new witness merkle tree built using wtxids instead of txids
        - witness merkle root combined w/ fixed 'commitment nonce' (provided by miners in coinbase tx)
        - final witness commitment hash inserted into coinbase tx
    3.  block header still uses traditional merkle root, but witness data commit indirectly enforced via last output in coinbase tx
    4.  Now old nodes can still validate blocks using only txid's (traditional merkle root)
        - witness data now committed via coinbase tx, cannot be modified after mining
        - segwit full nodes can/must verify witness commitment in last ouptut of coinbase tx matches witness merkle root (wtxid)
    5.  Summary:
        - - **Witness data is committed via a separate witness Merkle root.**  
                - **The witness commitment appears in the coinbase transaction as an OP_RETURN output.**  
                - **The block header remains unchanged, keeping compatibility with old nodes.**  
                - **Full nodes verify that the witness commitment in the coinbase matches the computed witness Merkle root.**
2.  What is weight versus virtual bytes? How do they differ? How does weight change the relative costs of inputs and outputs?
    
    - weight: new way to measure tx size in segwit
    - accounts for both base tx data and witness data
    - formula: weight = (base_size x 3) + (witness_size x 1)
        - base size = transaction size without witness data (bytes)
        - witness size = size of segwit-specific data (signatures, scripts, etc)
        - witness data 25% weight vs. base data, meaning signatures are 'cheaper' weight-wise
    - max block weight limit = 4,000,000 WU (weight units)
        - segwit blocks can theoretically be 4MB in weight terms but typically contain only ~2MB on tx data
        - legacy blocks strict 1MB (~1,000,000 WU) limit
    - vBytes (virtual bytes) = weight / 4
        - used to calculate fees: fee rate = fee (satoshi) / vBytes
        - ex: tx with 1,000 WU = 1,000 / 4 = 250 vBytes
        - fee rate 10 sat / vByte for tx with 1,000 WU: 250 vBytes * 10 sat/vByte = 2,500 sats
    - weight is a different way to define the size of a tx and takes the witness discount into account.
    - How does weight change relative costs of inputs/outputs?
        - tx's with large # of inputs are cheaper due to witness discount
        - outputs same cost as no witness data in outputs
3.  How could BIP 9 be considered controversial within the community? How was BIP 148 received when first proposed?
    
    - 95% threshold required to activate via miners gave too much power to miners in terms of accepting or blocking proposal
        - upgrade could fail if miners didn't cooperate
    - BIP148: user-activated soft fork.
        - required all nodes enforcing bip148 to reject blocks not signalling for segwit starting 8/1/2017
            - goal is to force miners to signal for segwit by making non-segwit blocks invalid
        - reception: mixed/controversial
            - supporters: full node operators, devs, businesses. View that miners shouldn't control upgrades
            - against: fear of a chain split if large % of network did not support it
                - BCH emerged from conflict (we see how that went for them.. POORly)
    - timeline:
        - Segwit introduced via bip141 using bip9's signaling mechanism
            - bip9 is more about how an upgrade is deployed rather than the upgrade itself
            - required that 95% of miners were running upgrade within 2-week period to activate upgrade
            - IT FAILED!
        - BIP9 failed, so BIP148 was proposed. This was a user-activated soft fork that would cause all nodes who opted to run BIP148 upgrade to reject non-segwit blocks. This was to take effect 8/1/2017
        - Miners pre-empted BIP148 with BIP91 on 7/20/2017 (<2 weeks before UASF!!)
            - BIP91: miner-coordinated activation. Lowered activation threshold % from 95% to 80% signaling for segwit. Enforced by MINERS, not USERS
            - backward-compatible with BIP148, meaning miners adopting BIP91 are automatically compatible with UASF (BIP148) nodes
            - BIP91 required 80% (672/806 blocks in 1,008 block period) to signal readiness.
                - started mid-July, reached 80% by 7/20/17, becoming LOCKED-IN!
        - Segwit locked in on 8/8/17 after sufficient BIP91 miner signaling
        - Segwit fully activated on 8/24/17
            - after lock-in, there's a grace period to allow nodes/wallets to upgrade. Once grace period ends, segwit rules fully enforced (all nodes enforcing segwit would reject non-segwit tx's)

&nbsp;

## SegWit Costs

* * *

1.  How does SegWit affect initial block download (IBD)?
    - overall segwit makes IBD slower as it creates larger block weights, requires more tx validation and more tx's now fit into blocks.
        - checking witness merkle root
        - P2SH-encoded witness tx's
    - some strategies to reduce validation times
        - libsecp256k1 speeds up signature checks
        - compact blocks bip 152

Segwit costs (about 1% per tx):

- witness commitment hash in coinbase tx (38-47 bytes)
- tx serialization: 2 bytes for marker/flag, 1 byte per input for count of witness items for each input
- block validation: slight increase in validation required for additional SHA256 hashes per tx

technical debt:

- easy fix now vs. long term problem, OR upfront pain/difficulty for long term smooth sailing
    - ugly/complicated code
    - poor design decisions/tradeoffs

mitigate risk but also keep backward compatibility

handling soft forks:

- old vs. upgraded nodes must achieve consensus on tx/block validation and propagation
- BIP9 deployment
- anyone can spend?
- risk of double spending tx's in mempool w/ combo of non vs. segwit output. Once confirmed, can't double spend

## Gregory Maxwell’s Inhibition Proposal

* * *

1.  What is ASIC BOOST and what did it have to do with SegWit's deployment?
    - an optimization technique that allows miners to reduce their energy consumption by 20-30% by exploiting a design oversight in SHA-256 POW function enabling miners to re-use certain hash calculations
        - how this works: miners has 80-byte block header (version, prev block hash, merkle root, timestamp, difficulty target, nonce), but SHA256 works on 64-byte chunks, so 80-byte header requires 2 rounds of SHA256 hashing
        - SHA256 works in 64-byte chunks, so requires 2 steps to process 80 bytes. SHA256 output is 32-bytes. So second SHA256 is hash of 32-byte output of first SHA256.
        - ASICBOOST looks for merkle roots where the last 4 bytes of merkle root are the same . This allows miner to re-use SHA256 output without having to perform the hash, saving 30%
        - basically re-use first 64-byte hash of block header and only vary 16-byte second-part of block header, as this saves computing power not needing to re-calculate 64-byte part each time...?
    - 2 ways to implement: overt and covert:
        - overt
            - block version field (first 4 bytes of header) used to generate different hash candidates, incrementing version # but keeping merkle root unchanged. allows re-use of first SHA256 round reducing power consumption
            - easy for nodes to spot manipulating version field
            - can be blocked by enforcing version field limits
        - covert
            - instead of version field, modify merkle tree structure to find multiple valid merkle roots that share same last 4 bytes, which tricks SHA256 into re-using first 64-byte computation which saves on hashing/energy
            - remains hidden as nodes don't inspect merkle root structure
    - tying back to segwit:
        - adding a second merkle tree using witness data (signatures) into coinbase tx prevents miners from manipulating original merkle root as transaction order now mattered; miners couldn't randomly re-order tx's to generate new merkle roots while saving energy on re-using existing hash outputs
        - **SegWit broke covert ASICBOOST by introducing a new witness Merkle tree and requiring its root to be included in the coinbase transaction.** Covert ASICBOOST relied on **manipulating the Merkle Root** to find multiple valid versions that shared the same last 4 bytes, allowing miners to **reuse the first SHA-256 round** and save energy. **With SegWit, the witness commitment in the coinbase transaction also depends on transaction order**, meaning any attempt to modify transactions to game the Merkle Root **would also change the witness commitment**, making the attack infeasible.

&nbsp;

## The Long Road to SegWit

* * *

1.  How did users know whether miners support SegWit prior to activation?
    - miners used BIP9 via a signal bit in the block header to indicate whether or not they supported segwit activation
    - to track, nodes running segwit-compatible software would count how many blocks in a 2016-block period (2 weeks) signaled for segwit. if 1916 or 95% of blocks supported, segwit would 'lock in' and activate next period

&nbsp;

## Bech32 (optional)

* * *

1.  1.  What are some address format design goals and requirements discussed in the bech32 talk? How problematic is the bech32 mutability issue for v0 segwit addresses versus for taproot?

&nbsp;

- replace bip142 (base58) with base32-based format
- 128-bit vs. 80-bit encrpytion

Base32 vs. Base58:

- advantages:
    - case-insensitive
    - simpler encoding (map directly to bits avoiding complex conversions)
    - more compact QR codes
    - better error detection
- disadvantage:
    - ~17% larger than base58
- checksum ensures invalid addresses not mistakenly accepted
- BCH (bose-chaudhuri-hocquenghem) codes chosen for strong error detection
- 6-character checksum reduces error acceptance probability to < 1 in a billion
- simple to implement, 10 lines of checksum logic code
- not backward-compatible

&nbsp;

mutability issue:

- certain typos can still be accepted as valid bech32 segwit v0 address
- segwit v0 uses fixed-length witness programs, so typos can conform to expected lengths and remain valid
- taproot (segwit v1) introduces bech32m
    - modifies checksum calc by using different final constant (0x2bc830a3 vs. 1)
    - ensures segwit v0 address w/ bech32 can't be mistaken for bech32m address
- future wallets should use bech32m

&nbsp;

&nbsp;

- questions for group:
    - how exactly does ASICBOOST work?
