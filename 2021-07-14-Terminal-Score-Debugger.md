# Reverse-engineering Lightning Labs Terminal Score

I recently published the Terminal score Debugger on lnrouter.app. It lets you choose a node and dissects the different heuristics used by the famous Terminal Web score to rank nodes.
In this article, I will give a short overview of how the score works and my personal opinion about it.

Disclaimer: This article simplifies the heuristics for better understandability. Head over to the Debugger to get detailed insight about your node.


## Motivation
Several PLEBNET nodes and I were curious about how to improve our Terminal Web score/ranking. We had arguments back and forth, talked about heuristics, and we figured out a lot by just looking at the score itself.
Nonetheless, the problem was that it was hard to introduce new users to this knowledge, and questions kept coming. This led to the decision to create a small dashboard.

## What does the score measure?
```
A list of nodes that Lightning Labs has evaluated as top performing nodes on the network. — Lightning Labs
```

Terminal measures and ranks high performing nodes in the network. It does that by counting how many good channels to stable peers you have.
To be more precise, a stable peer is a node with:
- More than 10 channels
- Big channels (median capacity 500,000sat+)
- High uptime (99.9%+)

The stable node heuristics filter for underfunded or badly managed nodes in my opinion. It also works as a filter for Lightning Labs test infrastructure.
After passing the stable heuristics, Terminal determines how good your channels are. Only channels with stable nodes on both sides are considered.

A good channel:
- Is minimally 1 week old
- Can route at least 1 million satoshi
- Has high uptime

That’s it. Be stable and have good channels. After that, the whole game of improving your Terminal score consists of getting more good channels to a wide range of nodes (and improving your centrality, but that comes with connecting to a wide range of nodes).

## What I like
I like the score. It’s well thought through and I believe it makes a good job in ranking nodes for the purpose of finding a reliable routing partner.
Particularly, I like the requirement for 1+ million routable satoshi, as it separates the wheat from the chaff and sorts out nodes with dead channels.

### The score is hard to game
The score is a good score because it measures honest and hard to fake signals.
Plenty of research has been done on signaling theory over the last 20 years. The score and especially the 1 million routable satoshi heuristic is a good example of a hard-to-fake signal explained by the handicap principle.
```
The handicap principle is a hypothesis to explain how evolution may lead to “honest” or reliable signalling between animals which have an obvious motivation to bluff or deceive each other. — Amotz Zahavi
```

You can not fake having routable money on your node. Putting real funds on it is costly to do. Therefore, I believe the Terminal score is not game-able, and “When a measure becomes a target, it ceases to be a good measure” does not apply in this case. Terminal did a good job here.
Maybe one day, Lightning Labs will consider open sourcing the score.

## What I dislike

Having debugged and talked to a lot of node operators, one pattern emerged:
It is impossible to rank consistently well with a TOR-only node. The TOR network is only partially reliable and doesn’t pass Terminals 99.9% uptime requirement. This results in the weird phenomenon that a node can rank 33 one day and rank 699 the other day due to TOR routing issues that last 2 minutes only.
This happens even to the best of us, for example to Alex Bosworth’s TOR node.
I am torn if this is an issue with the score or a problem with the TOR network. TOR gives nodes a big privacy boost with the caveat of a small reduction in node reliability and latency. A public discussion about the up/downsides of TOR might be needed.

## Outro

If you like the work I do, please consider a donation. I do not get paid for the work I do. I invest my spare time to offer insights to Lightning network router node operators. Twitter -> @SeverinAlexB.
Thanks to the PLEBNET community that supported me in debugging the score. 
Thanks to Lightning Labs for developing such a cool metric to evaluate nodes.
Special thanks to the following individuals that helped me during development:

- kp
- P…
- Mads
- AZ HODL
- xenonfun
- Felix
- smallworlnd
- Oli
- yeah
- GridFlare
- JohnOnChain
- Dirk Krienbühl
- Rajwinder
