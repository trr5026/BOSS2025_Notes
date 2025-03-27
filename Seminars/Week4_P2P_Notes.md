# Discussion Questions

# Attacking P2P

1.  ==Given the attack surface in P2P, is running a full node worth it?==
    
    - yes, it's worth it. I would argue it's moral to run a full node as it helps to strengthen and decentralize the network
        - other reasons: verify own tx's
        - adding honest node to network reduces attack surface
        - get on the mission!
    - attack surface:
        - eclipse
        - de-anonymization
        - network partitioning
2.  ==How many honest nodes do you need to be connected to be sure you are connected to the right network/blockchain?==
    
    - just one honest node will allow a node to validate/connect to correct network chain. 2 or more increases the odds but only one is needed
3.  Does it make sense to have separate networks which are more resistant against p2p attacks, and regular nodes will keep at least one connection to them if they choose so?
    
    - general hunch: no, that seems like it flies in the face of what bitcoin is attempting to accomplish. Splitting the network will weaken bitcoin and increase attack surface solely due to increased complexity. Not worth the tradeoff.
    - prove me wrong; what's the flip side?

&nbsp;

# P2P Connections

1.  ==What is the reasoning behind the max inbound and max outbound defaults? For which type of user would they be considered ideal, and when might they be optimized?==
    
    - outbound: 8
        - good balance between redundancy and diminishing returns to reach honest network
    - inbound: 125? 117?
        - resource constraints: 1 in bound peer consumes 1-2MB per block, so 125 connections is around 100 Mbps on the range of an average internet connection speed. Same rationale goes for CPU logic
    - chosen for CPU, bandwidth efficiency
    - ideal for average full node operator, may want to optimize if using full node for business or other specific purpose
        - miners that only care about hashing can change inbound peers to 0
        - merchants that rely on lightning payments may up outbound peers to ensure connecting to honest chain
    - Karliatto answer:
        - outbound: automatic connection for peer discovery, Node initiaites
        - inbound: other nodes initiate this connection
2.  What is the rationale behind the "new"/"tried" table design? Were there any prior inspirations within the field of distributed computing?
    
    - allows a node to identify and categorize its peers based on whether the peer is legit or not. Gives flexibility for node logic to disable connection with bad peers and keep connection with good peers
3.  ==How are anchor connections chosen? In what circumstances would you evict or change them?==
    
    - first, definition: trusted honest nodes
    - how to choose?
        - peers proven to be honest and reliable
    - when to evict/change?
        - when peers are unreliable/dishonest/malicious
4.  What are feeler connections, and when are they used?
    
    - a temporary outbound TCP connection between peers to test the availability and validity of peer nodes
        - help test new connection, prevent eclipse attach, clean up address tables, stale tip recovery
    - every 2 minutes (hard-coded in bitcoin core) a node checks its outbound connections to see if it needs to replace any.
    - how it works: every 2 minutes, node chooses address

&nbsp;

# Eclipse Attacks on Bitcoin’s Peer-to-Peer Network

1.  ==What can an attacker do if they are able to eclipse a mining pool?==
    
    - withhold blocks / selfish mining
        - force pool to mine on wrong chain, attacker can find own block and orphan pool block, wasting resources
    - double-spend attack
        - attacker sends tx to eclipsed pool mining on bad chain. Attacker then sends same tx to their own address on secret good chain. This requires attacker to find block before pool does
    - censor tx's
        - attacker removes certain tx's from mempool, feeds to pool to mine block on
    - de-anonymize users
        - log all messages and tx's sent to pool, attacker can ID users via IP addresses
    - propagate false chain to network so miners waste time mining on false chain (pretty much selfish mining)
        - see selfish mining
2.  What is the difficulty of successfully achieving an eclipse attack? What resources and skills would be required to achieve such an attack?
    
    - easier than 51% attack but still moderately difficult.
    - what's needed to pull off attack?
        - 100's of IP addresses to flood victim's new/tried tables
        - botnet
        - bandwidth
        - HW and SW and knowledge
    - depends what kind of target. merchant may be harder to attack than avg node runner

&nbsp;

# Transaction Trivia

1.  Why must transaction unlocking scripts only push numbers to be relayed?
    
    - safety/security, standardness
2.  What output scripts are 'IsStandard'?
    
    - P2PKH, P2SH, P2WPKH, P2WSH, P2PK, OP_RETURN
3.  Why must transactions be no less than 82 bytes to be relayed?
    
    - This is the minimum amount of information required for a standard tx. Anything less won't work
4.  Why is the blockheight now encoded in the coinbase transaction?
    
    - To ensure that every coinbase tx is unique, used as verifiable anchor

&nbsp;

# [OPTIONAL] Researching P2P privacy attacks

1.  How does "diffusion" message spreading work and why is it ineffective against de-anonymization?
    - messes with timing of relaying blocks/tx's to other nodes to obfuscate 'first sender' pattern, increase privacy and protect nodes from shenanigans
    - not effective against de-anonymization because:
        - delaying doesn't hide where the tx came from. >60% of the time the first relayer of a tx is the source. So that sounds like a fucking stupid way to protect yourself if it's obvious from other nodes who broadcast the tx first
        - it's still too easy to trace tx back to your node
        - better alternatives:
            - VPN or TOR - breaks link between IP address and node's messages?
            - dandelion (BIP156)
                - stem -> node sends tx to one peer secretly, that peer then broadcasts it. This breaks first sender link
            - manual peer selection
