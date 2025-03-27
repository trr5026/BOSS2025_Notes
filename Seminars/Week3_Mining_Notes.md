## Mining and Consensus

* * *

1.  Why would some miners use parts of the version field as a nonce? What effect did this have on nodes?
    
    - if standard nonce field was exhausted: 4 bytes = 32 bits = 2^32 possible combos = 4 billion. Miners can exhaust this range in seconds nowadays
    - effect on nodes: once segwit began using version for signaling i.e. BIP9, miners couldn't use version field for risk of screwing up version signaling. Not as big a deal in earlier years when mining was less standardized
2.  How do P2Pools work? Advantages and disadvantages? Why aren't there any mainstream P2Pools today in the market?
    
    - P2Pool is a decentralized mining system which operates as a P2P network of miner nodes
    - uses 'sharechain' parallel version of blockchain w/ lower difficulty to prove miners' work
    - each miner runs P2Pool node alongside full node
    - does not need trusted third-party operator
    - advantages:
        - decentralized, trustless, transparent, resilient to DDOS attacks
    - disadvantages:
        - higher payout variance, more technical complexity, higher network dependency (poor connectivity = stale/orphaned blocks/chains), scalability issues (30-s target difficult for less powerful  miners)
3.  ==How far back into the past can the timestamp in blocks be? Is this validated? How can this be attacked?==
    
    - it must be greater than the median timestamp of the previous 11 blocks, aka Median Time Past (MTP)
        - must also not exceed node-adjusted time 2 hours forward
    - this is validated when nodes validate a new block added to the chain.
    - ways to attack:
        - bias blocks to the future, manipulating difficulty. requires majority hash rate
        - seems most attacks are limited due to timestamp window
        - fix on testnet 4: make difficulty adjustment be first block of current epoch and first block of next epoch (space between two consecutive blocks cannot be gamed)
            - didn't work, make testnet5. Attempting to fix logic for main chain
        - time warp attack - requires majority hashrate
4.  Have we seen the difficulty re-target algorithm be manipulated in other projects? Is this a threat to Bitcoin?
    
    - yes, with BCH when it was forked on 2017. emergency difficulty adjustment since so few people mining on chain led to unpredictability and hash rate instability and eventually replaced with better algorithm
    - timestamp manipulation in Verge (XVG) in 2018
    - ETC (ethereum classic)
    - threat to bitcoin?
        - possibly in future: quantum threat, mining centralization, forks/competition
    - bitcoin tradeoff: responsiveness for stability. Two weeks is adequate for this
5.  In your opinion, is it a good or bad thing that specialized hardware is now needed to mine bitcoin rather than letting individual users participate in that part of the ecosystem?
    
    - this is a tradeoff question;
    - good
        - more mining power = better network security
        - increased economic efficiency
        - drives efficient energy usage and usage of uneconomic sources of power
        - evolution of mining industry into something serious at scale and global
    - bad
        - centralization risk
        - hardware/supplier dependency (bitmain and whatsminer both Chinese)
        - locks average Joe out of industry / high barrier to entry economic, skill/knowledge wise
    - overall net positive, but this is a tradeoff scenario due to incentives from industry. No perfect answer
        - i.e. more and more powerful ASIC hashing = better network security but also centralization risk
6.  Consider the following scenario: Segwit2x didn’t happen (or miners didn’t throw in support for it) and UASF (BIP148) ended up causing a chain split on August 1st, 2017, with minority mining support (e.g. ~10-20%). What would likely happen as a result?
    
    - I think this could have killed bitcoin. Fracture community, which chain would provide decentralization, security and censorship resistance? Likely a good thing miners pre-empted BIP148
7.  Can you ensure the transaction will be processed even if you send it with low fees? Which mechanisms do you have to ensure a stuck transaction (due to low fees) gets processed?
    
    - not guaranteed, but ways to help your odds
        - RBF, CPFP, accelerators, going directly to miner to mine tx
        - wait for low fee environment
    - tx priority: part of equation is # days in mempool, so older tx is, more likely to be mined
8.  Some mempool divergence is natural in a decentralized system. What negative side effects might we incur if the level of mempool divergence between nodes becomes "too high"? Consider other stack layers (P2P, wallet, validation etc.). What level of divergence do you think the system can safely tolerate?
    
    - orphaned tx's, stale blocks, higher risk of DoS attacks
    - validation delays
    - fee market distortion
    - unreliable fee estimation
    - confirmation uncertainty
    - safely tolerate?
        - tx's should reach 90% of nodes within 10-20s
        - 20-30% divergence max
        - strategies in place to mitigate divergence
            - FIBRE/fast block propagation

## Advances in Block Propagation

* * *

1.  How can changes to Bitcoins p2p protocol affect mining?
    
    - block propagation speed
    - tx relay / mempool sync
    - peer discovery /  connectivity
    - security and attack resistance
    - pool-miner interaction
2.  How is a high stale block rate the best indicator that block propagation times are too high?
    
    - shows that many miners are mining the wrong chain due to lack of or delay in block propagation. Wasted effort/resources. The longer time between propagating block -> higher chance of mining on wrong chain
3.  Alternatively, while a low stale block rate can be an indicator that block propagation times are good, it could also be an indicator of something more problematic. What could be a bad reason for there to be very few stale blocks?
    
4.  ==Greg Maxwell mentioned that miners are hesitant to connect with one another directly, even though it would speed up block propagation. Why?==
    
    - basically wanting to maximize profit against competition, for security reasons, selfish mining
5.  ==A low block-orphaning rate can be an indicator that block propagation times are good. It could also be an indicator of something more problematic. What could be a bad reason for there to be very few orphans?==
    
    - mining centralization
    - selfish mining/coordinated behavior
    - network structure
    - censorship or preferential relay
    - karliatto
        - can be two options
            - things working very well, infrastructure good
            - mining pools are colluding and high centralization risk

&nbsp;

## Compact Blocks

* * *

1.  How could we speed up block propagation in the network and why is that important for decentralization?
    
    - optimizing block relay protocol like BIP152/153 compact blocks
    - reduce validation overhead
    - mitigates network attacks
    - dedicated relay networks like FIBRE (reducing lag to only physical effects)
    - the longer a delay between a found block and its propagation to the network the more room it leaves for fuckery
    - slow propagation favors big miners over small miners, widening the gap between success and failure, which in a centralization risk
2.  Compact block relay is a good way to reduce the amount of bandwidth to propagate new blocks to full nodes, but is it the fastest method for a synced node given peers may need to request missing transactions?
    
    - when new block found, miner sends cmpctblock message which is only 10-20kb vs. 1Mb for entire block
        - block header 80 bytes, short txid's 6 bytes each, a couple full tx's
    - compact block relay is fast but not always fastest due to sometimes missing tx's which other nodes would have to query
        - compact: 1-2s
        - FIBRE: 100-200ms
        - graphene: 0.5-1s
    - not fastest but likely best tradeoff due to better decentralization incentives than FIBRE (special network which requires active participation/maintenance by third party - requires trust!)

&nbsp;

## Mining: No Good, The Bad, and The Ugly

* * *

1.  ==What is a BGP Hijack attack? What prevents it?==
    
    - Border Gateway Protocol (BGP) hijack attack occurs on a network where a malicious actor manipulates the network's routing system to redirect traffic between autonomous systems (ASes) for nefarious purposes i.e. man in the middle attacks
    - the re-routing itself isn't the attack, but what the rerouter does with the info it's rerouting
        - delay block propagation
        - double-spend funds
        - leak or steal private information
    - how to prevent?
        - route filtering: only use pre-agreed routing path/info
        - Resource Public Key Infrastructure (RPKI)
            - use cryptography to verify AS's
        - BGP monitoring, path validation
        - encryption
    - EverythingSatoshi
        - redirect mining information to harm miner, attack in some way
            - ways to attack
                - double spend
                - delay block propagation
                - MIIT attack (stratum v1 unencrypted)
        - in 2018, mining pool re-routed through Russian network?
        - how to prevent?
            - encrypted communication
            - decentralized pools
            - redundancy in communication (starlink + broadband)
            - datum server by Ocean (stratum v1 w/ modifications)
            - build own templates (Stratum V2)
2.  Why not encrypt all communications with pool operators?
    
    - performance overhead - costs too much!
    - information not sensitive (block templates, nonces, share submissions
    - stratum V1 unable to encrypt messages, requires miners to adopt V2 or other protocols which do encrpyt
    - speed trumps security

&nbsp;

## Majority is not Enough

* * *

1.  What is the 'Selfish Mining' attack? How does it work? Is it a real threat?
    
2.  Is it possible to identify and ignore a Selfish Miner's chain? Must we follow the most-work chain even if we identify that it came from a selfish miner?
    

&nbsp;

## Mining pools

* * *

1.  How do pools give work assignments to client miners? Do all miners search the same block candidates?
    
    - pool handles block template creation
    - pool uses protocol like stratum v1/v2 to communicate with miners
        - pool sends miner a job: block header data, nonce range, coinbase extra nonce (to search beyond 4 billion hashes if needed)
        - miners take job, hash block header w/ SHA256. If hash beats pool share difficulty, miner submits share to pool as proof of work (used to determine payout)
    - all miners don't use same block candidate, but rather split up the work via nonce ranges
        - miner 1 does nonce 1-10, miner 2 does nonce 11-20 etc.
        - this improves search efficiency and ability to find blocks. Think grid search for avy rescue. Don't send entire party to same starting area, divide and conquer!
2.  ==What are 'shares' and how do they help facilitate pooled mining? (my question)==
    
    - a share represents proof that a miner submitted a hash to the pool that meets a pool-specific difficulty target (easier than network difficulty target for full block), proving its work toward finding the block
    - a mining pool combines hash power from many miners to increase the chance of finding a block. Shares allow for fair distribution of rewards based on how much work was performed by miners within a pool (a miner providing more shares to the pool is doing more and gets a larger % of reward
    - a pool finds a block when a miner within the pool submits a valid hash that is below the current network difficulty target
    - types of reward systems:
        - pay per share: fixed payout per share defined by the pool. Pool
        - proportional: rewards are split amongst all pool participants each time the pool finds a block (Ocean)
        - score-based: shares receive a score based on age/time, balancing fairness and luck
    - if doing pooled mining and you find the block, why can't you just broadcast and capture all the rewards for yourself vs sending to pool and sharing rewards with pool?
        - coinbase tx set up by block template. If you use pool's template, it will pay to pool. If you try to change template, will need to re-do work to broadcast

&nbsp;

## Overview of the covert AsicBoost allegation

* * *

1.  What is the difference between overt and covert ASIC Boost?
    - overt ASIC boost: re-hashing the version bytes and re-using proof-of work. It's very obvious to see in mempool
    - covert: re-using first 64 bytes of hash only hashing the last 16 bytes of block header. re-orders tx's, very difficult to observe behavior in mempool. segwit adding wtxid merkle root in (last?) coinbase output breaks covert ASIC boost (why some miners were opposed?)
