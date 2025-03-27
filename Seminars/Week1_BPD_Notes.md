Discussion Questions

&nbsp;

## Bitcoin Whitepaper

* * *

1.  Why did Satoshi put so much stress on the irreversibility of transactions?
    
    - To solve the double spending problem
2.  What proposed role do SPV nodes play in the bitcoin ecosystem?
    
3.  What do you think might happen if Satoshi returned to Bitcoin development?
    
    - I think bitcoin is bigger than any one person at this point, even if it is satoshi. I would actually prefer that Satoshi never comes back, but what would happen if they did? I think it would be good for bitcoin. then again, I think everything is good for bitcoin.

&nbsp;

## Incomplete History of Bitcoin Development

* * *

1.  How have show-stopping bugs or disruptions to the network been handled in the past?

&nbsp;

## Bitcoin's Academic Pedigree

* * *

1.  In your opinion, what did Satoshi "invent" that was truly innovative?
    
    - It wasn't that he invented any one thing, but that he was able to put already existing technology puzzle pieces together in a way that simultaneously solves a few very important problems with money:
        - digital scarcity
        - difficulty adjustment
        - supply cap
        - true decentralization
    - I think if anything the societal impact and transformation bitcoin is having at a fundamental moral and social level is perhaps the most profound aspect of bitcoin, and hence invented by satoshi
2.  [What is a Sybil Attack](joplin://x-callback-url/openNote?id=1ba079fd21d045359c97bd033c0bd141), and how has it been solved in the past? How does proof of work enable Sybil resistance for new nodes joining the Bitcoin network?
    
    - a sybil attack is an attack on a network where an adversary creates many sybils, or nodes, to gain disproportionate influence over a network.
    - In the case of the bitcoin network, the nature of its design helps to prevent sybil attacks by adding a prohibitive cost to adding nodes to the network. It's not free; requires internet access and computer HW capable of storing/running blockchain.
        - for POW specifically, there is no faking work, so if someone wanted to sybil attach the bitcoin network, they'd have to actually produce all of the work required. Cheating won't work, kicked off network by honest nodes. coercion won't work; it's too decentralized. POW incentivizes someone with ASIC/energy resources to compete honestly rather than dishonestly, as the network is the most powerful computer network in the world, and attempting to use resources to reorg/double spend in the pure physical sense would likely result in wasting all of those resources
    - I'd think a political attack would be more effective than a sybil attack at this point, but even that window is mostly closed.
    - in the case of mining, sybil attacks are practically unfeasable as to the nature of the HW required to carry out any significant attack on the basis of hash rate
        - attacker would need coordinated resources, ASIC's, energy infrastructure, and they'd need to be able to hide their presence physically, else be eff'd up by psychopathic bitcoiners. These people put all their life savings in bitcoin and brush off +80% drawdowns like nothing. Don't get on the wrong end of these psychopaths!
3.  What is the "fair exchange" problem, and how does it apply to the blockchain?
    
    - a problem that arises in cryptography and distributed systems where two parties want to exchange digital goods or assets in a way that guarantees that both parties get what they agree to, or that neither do to prevent one party from cheating
    - Regarding blockchain, this problem gets more complex when including trading assets that do not belong on the blockchain. In bitcoin's case, if only transferring bitcoin, there is no worry. But what about using bitcoin to pay for something in the real world? Thus more complex strategies are required
        - some examples:
            - HTLC's
            - multi-party multi-sig
            - others?
4.  Why do the authors of Bitcoin's Academic Pedigree believe that bitcoin was ignored by academia for a long time? What reputation/relationship does bitcoin have with academia today?
    
    - It's a profound monetary and technological innovation and disrupts how existing societies store and transfer value, so of course it was ignored. TO be a bit less tongue-in-cheek, the fact that it had bottom-up origins outside of the orthodox consensus and peer review ensured that it would not be adopted top-down, but rather bottom-up, and by those who actually need/use it first. Academics are likely to be the last cohort that adopts bitcoin for that reason.
    - even today it's still a mixed bag as the saying "one will willingly not accept or understand something if their paycheck depends on them not understanding it" comes to mind

&nbsp;

## If I'd Known What We Were Starting

* * *

1.  How do you judge the merits or value of a good project/experiment?
    
    - I'm actually not really sure, so follow-up questions:
    - what is its purpose? What does it aim to achieve/accomplish?
    - what people/groups/entities does it benefit? Does it harm anything or anyone?
2.  Why do you think Bitcoin's creator remained anonymous and (as far as we know) chose not to profit from Bitcoin's success?
    
    - I think this was the only way that bitcoin would be capable to succeed. Had there been a pre-mine or a known identity attached to its founding, I have a strong believe that bitcoin would not have stayed decentralized.
    - I also think that bitcoin has evolved past the control of any one person or group, but rather is its own meta organism that has a distinct distributed intelligence about it. Thinking of an article I read about bitcoin being a brain and how humans are the neurons/synapses of the brain. So many metaphors!

&nbsp;

### Bitcoin's Security Model: A Deep Dive (MISSING!)

* * *

1.  What do you think will happen when the block subsidy runs out?
    - I think that the answer to this question has a path dependence to it. If bitcoin's network grows large enough that it does become a global neutral reserve asset, then anyone who uses the network to store and transfer value will be incentivized to protect it.
        - currently bitcoin mining is incentivized by profit, so it's essentially using human nature (greed) as a security protocol. When the block subsidy goes away, the most common argument I hear is that transaction fees will be high enough that miners will still mine for those profits. How do those numbers work out?
            - what factors must we consider?
                - cost of energy
                - cost of mining hardware
                - expected tx fees
                - network strength

&nbsp;

## Security Models with John Newbery (MISSING!)

* * *

1.  Define the differences between a full node, pruned node, and an SPV node?
    
    - full node: stores entire history of blockchain on node. Able to verify every transaction ever made on bitcoin network
    - pruned node: a node with a limited history of the blockchain stored. Can only verify transactions that it has stored, cannot index/verify entire blockchain
    - SPV node: lightweight client that must TRUST full nodes in order to verify transactions
2.  What are the incentives to run a full node? If I do run a full node, why accept incoming connections given that they come at a cost? What might those costs be?
    
    - strengthen decentralization of the network, verify own transactions (avoid trusting third parties)
    - cost is information and energy, but worth the cost as each node is holding up the network. The network requires volunteers to power, enforce, maintain. Running a node is a subset of maintaining the network, so if it provides value, surely the effort of running a full node are worth the value
3.  What is the difference between the block propagation network and the transaction relay network? How does participating in one or the other impact your definition of a full node?
    
    - Block propagation network deals with relaying new blocks to network
    - TX relay network deals with relaying tx's to all nodes in network
    - what a node does depends on how it's configured... not all nodes will mine or propagate blocks or tx's
    - this question confuses me a bit as blocck propagation and transaction propagation occur on the same network, but done using different functions of a node. Nodes have the ability to do both, only one, or neither, so making a distinction between block propagation and transaction propagation being different networks is semantics to me
        - we can discuss the mechanics of how block propagation is different than tx propagation, but both occur on the same network
    - compact block filters > bloom filters
4.  What are checkpoints and do we need them?
    
    - they are hard-coded (src/chainparams.cpp?) checks built into core that serve as reference points to the blockchain's validity. Meant to prevent dishonest actors from performing deep re-writes of main chain (51% attack)
        - also help with syncing blockchain,
    - we don't really need them any more as the network has matured and there are tens of thousands of existing full nodes with full history, and deep re-orgs/re-writes are increasingly unfeasible as the chain size continues to grow

&nbsp;

## Scaling Bitcoin: A trip to the moon requires a rocket with multiple stages

* * *

1.  Do you believe that bitcoin needs to be competitive with visa/mastercard to succeed?
    
    - No. Visa/mastercard are payment processors dealing in credit. Bitcoin is sound/hard money. Store of value is enough for me, anything else is icing on the cake.
    - how does bitcoin reshape society if central banking is no longer needed?
        - decentralizing vs. centralizing forces
            - if something happens supposedly organically but
2.  How do you think the bitcoin community might react to a centralized payment processor that does as many transactions per second as visa/mastercard?
    
    - Some will freak out, others won't, but on net would likely be net positive for both the network and its users
        - network effect, competition on tx fees, development of technology/innovation
