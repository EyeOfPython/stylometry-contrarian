Assume Alice already has money in a LN wallet with a channel open. 

Bob downloads a new LN wallet and opens it up. Alice cannot pay Bob because he has no open channels and no capacity. 

Either:

1) Bob must connect to a hub who will open a channel with Bob, pay a tx fee to open the channel (which they might be able to recoup later by charging bob), and deposit 1 BTC of their own money into Bob's channel. Effectively locking it up. 

Or 

2) Bob must buy some bitcoin at an exchange. Open a payment channel with somebody using that Bitcoin, pay a transaction fee, then make an outgoing to LN payment to *somebody*. That somebody could possibly be sending the coins back to the exchange to sell them.  Now Bob's counterparty has enough funds in his side of the channel to facilitate Alice's payment to Bob. 

[This all assumes there exists a route between Alice and Bob where every hop along the route has 1 BTC in the outgoing side of the channel. If this is not the case then the payment cannot be made.]

It's very awkward. We get lot's of people coming into the OpenBazaar subreddit demanding we implement LN. I explain how awful of a UX this is for vendors to even be able to receive just one payment from a buyer and they tell me I'm lying and don't know how LN works. That's the state of the discourse surrounding LN today. 
This is basically exactly the problem the BU gigabock testnet identified. At sizes > 100mb the mempools were so out of sync that blocks were basically transmitted as full blocks.   


BSV had ONE 128mb block and it caused a six block reorg. On the BU testnet sustained 128mb blocks caused a total breakdown of the chain where there were so many reorgs that every node had a different view of the state of the blockchain.   


And would you believe barely a day goes by where the BSV supporters don't mock me pointing this out as if it's sooooo obvious how wrong I was.
I would prefer Bitcoin, I never wanted a split, but the project is monopolized by people who don't share my values. Quite a few assholes in that community as well.
To be more specific:

1) Vendors need to remain online 24/7 to receive orders.

One of primary pieces of feedback from OpenBazaar 1.0 was that vendors did not like having to run a server on their home computer 24/7 to make sales. That requirement was a remant of the original Dark Market design and it was one of the primary things we wanted to change for OpenBazaar 2.0. For 2.0 we spent an enormous amount of time re-architecting OpenBazaar (and moved to IPFS) specifically to allow vendors to receive orders while they are offline. With the LN vendors need to remain online to provide a hash to all would-be purchasers to kick off the lightning payment. If the vendor is not online, the payment cannot be made. This would represent a significant regression in the user experience and would bring back the primary user complaint from OpenBazaar 1.0.

2) Vendors cannot receive LN payments without a third party liquidity provider. 

If a vendor opens OpenBazaar, opens a payment channel, lists some items, then receives an order, the buyer cannot pay for the order over LN since the vendor's counterparty does not have any funds in his side of the channel to send to the vendor. The only way for the vendor to receive an incoming payment is to have buyers open a direct channel (innefficient and unacceptably expensive) or open a channel with an intitutional liquidity provider who agrees (probably for a fee) to deposit money in the vendor's channel to facilitate incoming payments. This is not a great UX and introduces some significant friction into the app (not to mention no such insitutional liquidity providers currently exist). Moreover it's difficult to calculate exactly how much money the liquidity provider should deposit in the channel. Is $1,000 enough? Hard to say. If buyers try to pay more than $1,000 worth of orders before the vendor can spend the coins out of the channel (presumably to an exchange) then the those additional payments cannot be made. This creates a weird UX where the vendor has to continually try to juggle the amount of funds available in the incoming side of the channel to ensure that there is enough liquidity to facilitate payments.

3) Vendors will need third party "watchers".

Since OpenBazaar users have expressed distain for a requirement to a full node to use the software, they would be left with the rather ugly solution to having to hire a third party "watcher" (which currently do not exist) to protect them from fraud.

4) Lightning currently does not do multisig. 

Escrowed payments are a necessary prerequisit for any decentralized marketplace to function. In theory lightning payments could use 2 out of 3 hashes in the HTLC, but no software currently supports this functionality and doing so introduces dramatically more complexity on top of an already dramatically complex protocol. And it would require the moderators to remain online at all times else escrowed payments could not be made.

5) It's not clear that LN payments will be 100% reliable. 

Whether a payment can find a route depends on how many people use LN and how they use it. If the routing paths simply do not exist or if they exist but lack the needed capacity than payments can not be made. For an app like OpenBazaar to gain any kind of sizable user base, the app (including the payment layer) needs to be 100% reliable. If this is not the case it will make the app feel broken and discourage many people from using it. At this point in time we do not know if 100% reliability in payment routing is likely or not. 

In my opinion at present time using just about any coin other than Bitcoin removes all of the above frictions and provides a much better user experience. Unless the benefits of lightning (relative to the alternatives) outweigh these costs outlined above, or they find a way to remedy the issues defined above, it doesn't make much sense to implement LN in OpenBazaar.
> I didn't like the burden that increased blocks would put on nodes. I believed this would push bitcoin towards centralization, that nodes would be forced to drop out from the increased storage burden.

Look none of us want that to happen. We should always be able to run the software on normal consumer grade computers (maybe not 5 year old computers, but current hardware). 

But the main point of contention that has existed between this group and Core is we know that 2x, 4x, 8x, will not prevent that from being possible. If it remains feasible to run a node on a standard laptop you can get at bust buy, with a normal home internet connection, how much centralization pressure can there really be?

That will buy us at least several years, if not longer, at which point other optimizations will be mature enough to scale beyond that.

Personally I find this to be a very reasonable scaling strategy. 
> you don't understand how open source works

This notion that "anyone can contribute" is total bullshit. Literally the way github works is that the owners of a github repo are the total 100% dictators of what gets into that repo. And in the case of Bitcoin Core, you can submit PRs until you are blue in the face and they wont make it into the repo if it goes against their plans. Even if 99% of the community want the change. 

Sure you can fork the repo and put out another version with your changes but we've seen what they do to people who try that... they declare war on anyone who tries and that person or group is subjected to extreme amounts of verbal abuse and slander. And anyone who dares to run this alternative implementation is subjected to illegal hacking attacks. 

They will use every dirty trick in the book to ensure they remain in a dictatorial position. 
None of them in there care about creating digital money or changing the world. They are all-in on bitcoin as a get-rich-quick speculative trading asset, nothing else. And there is massive cognitive dissonance if you point this out.  
> attempted coups by large miners

Oh please. Segwit would not be activate right now if the people you claim are orchestrating a coup didn't come together and compromise. 
> ACK. We definitely need to coerce Coinbase into switching back to Bitcoin Core. If we do not take any action, we're setting a dangerous precedent where other wallets and services are allowed to break apart from the consensus.

This is the most dangerous part of Bitcoin Core right now. Not only do they have an effective monopoly over the protocol, but they take active steps to coerce and censor everyone who threatens that monopoly. 

> It is about sending a strong message. I think we may need to start an accreditation scheme for Bitcoin-consensus compliant wallets and services. Through code signing and use of multisig, we can even distinguish transactions made by compliant wallets and non-compliant wallets, and have pools not mine them (or wallets refuse to send to known-non-compliant wallets).

Think about that for a minute.
This is pretty much the guiding vision of Bitcoin core. And this is what the fanbois fight so hard for.
He comes across as a crony capitalist. 

At the Senate hearings it was pretty clear he was advocating regulation as a way to keep out competitors. 

He basically said, "You should have to spend millions of dollars if you want to enter this space".

Cronyism pure and simple.
Conversation went something like this:

> Him: Blockstream's Liquid is almost ready and is going to scale Bitcoin

> Me: Liquid is an enterprise product which they charge like $5,000 to use or something like that.

> Him: You are a liar Chris. How can you possibly claim they are going to charge fees for Lightning? Everyone needs to know how much of a dishonest liar you are.

> Me: Umm, dude we were talking about Liquid, which is entirely different from lightning. Here's a screenshot from 2 minutes ago.

> Me: posts screenshot.

> Him: silence

[half hour passes and I get a twitter notification]

> Hey everyone @chrispacia is on facebook telling lies about Lightning saying that Blockstream is going to charge $5,000 to use it. 

Seriously this is the quality of the intellect we are dealing with. 
I don't consider money laundering a crime at all. It's nothing but transferring money from A to B. That in and of itself does not harm anyone. 

If someone robs a bank and proceeds to launder the funds, the crime was the bank robbery not the subsequent money transfer. Law enforcement should focus on stopping the robbery, not demanding everyone be inconvienced and innovation crushed because it makes their investigation easier.

The NSA also claims that spying on everyone makes it easier to stop terrorism. Whether that is true or not still doesn't justify violating everyone's rights. 

If they want to stop crime, they need to get better at old fashioned police work and stop inventing crimes like "money laundering" that harm everyone just to make their jobs easier. 
So I'm friends with both Deadalnix and Shammah so I'm not trying to pick sides here, but I do just want to point out to people in this thread that deadalinix is in a verrrry unenviable position of being the lead maintainer of as software which is currently used as the primary mining software on a billion dollar network. One little oversight and the network forks or crashes. This would even be the case if ABC had a much lower market share than it does. I don't know how someone in that position sleeps at night.

Just to give an example, we had four bchd-beta nodes running on the network and the other day all four of the nodes rejected a valid block and fell out of consensus with the rest of the network. There was a one block reorg and the utxocache (which I wrote) didn't properly handle moving a utxo from unspent to spent and then back to unspent. So the network moved on and bchd nodes were all stuck at the same block.

And it wasn't just a screw up on my part. Three other people reviewed this code. We have multiple reorg tests that exercise all different reorg scenarios ― except this one apparently.

My point being this shit is hard and very stressful. You need to keep that in mind when interacting with people.

&#x200B;
Fwiw, I'm with Mike and Gavin on increasing the limit. 

Reasons:

* Decentralization is often quoted as a reason against raising the limit and something like the lightening network an alternative. Yet there will likely be far fewer payment hubs in the lightening network as they take a large amount of capital to startup. Much more than running a node at the 20 mb block size. Hardly an improvement in dectralization. 

* The number of nodes is likely to be much more a function of bitcoin adoption than the cost to run a node. If at some point in the future merchants are not using payment processors and instead are keeping/spending the coins it's not hard to imagine all medium to large size businesses running their own nodes. That would put the node count in the millions making these arguments irrelevant. Maybe that future never comes to be, but it's hard to see a large increase in bitcoin use that doesn't come with a large increase in nodes. The recent declines are more likely due to the fact that in the early days you had very few choices in wallets and most people just ran bitcoin core. 

* There is concern that the falling block reward will harm seruity if not sumplimented by higher fees. But how much can fees go up without causing people to stop using Bitcoin in favor of alternatives? To completely replace the block reward with fees in a 1 mb block limit the fee would need to be $1.36 per transaction. Is that competitive? I doubt it. On the other hand, at today's fees a 30mb block would completely replace the block reward. 
Honest to God I did that to Ripple (or in reverse). I was like "I have $1,000 laying around. Let me put it into Ripple. It's a win-win, either I make $500-1000 on that trade, or I crash Ripple. Either way I'm OK with the outcome." 

Turns out I crashed Ripple. You're welcome.
He wont be allowed back into the community. Neither will any nChain employees. 
"Show of hands. How many people in this room have had comments deleted on r/Bitcoin". Half the room raises their hands.
To answer the question, there's no technical reason not to have a modest blocksize increase at this point. It will relieve fee pressure and buy time for other optimizations to mature. 

Bitcoin Core has refused a modest block size increase at every step along the way and at this point it's very hard to assume good faith. We do know that many of them work for a company (Blockstream) whose business model apparently entails creating a proprietary, centralized clearing system for hosted wallets where they take a cut of every transaction. If genuine scaling solutions are implemented, that business model goes out the window. It reeks of "create the problem, sell the solution". 

Not surprisingly they find it incredibly hard to build support for this "vision" of bitcoin so they resort to massive censorship and narrative manipulation. It's late stage desperation at this point. 

Segwit2x was a compromise proposal to activate segwit (which Core developed but a sizable percentage of the community did not want) in exchange for a 2mb blocksize increase. Since segwit activated first, Core is now saying "hahaha we got what we want fuck you and fuck your 2x". And are willing to split the chain to keep their 1mb. 

Bitcoin Cash was created by people fed up with the bullshit. It forked away from Core earlier this month and implemented the modest blocksize increase Core refused to do. Without the baggage of a company trying to profit from choking off bitcoin, it's a real possibility Bitcoin Cash could overtake legacy Bitcoin as some point. 
Checkpoints can potentially cause nodes to fall out of consensus if there is a deep reorg. That's why Core removed them. 

But a deep reorg is such a catastrophic failure for a monetary system that maintaining consensus through it is the least of your worries.
This was still the best comment by Mike Hearn:

> BtcDrak is a Bitcoin Core development list moderator. Who is he? Who pays him? What's his real name? He hired Peter Todd as consultant for ViaCoin, where did he get the money to do that? If it's OK to demand I answer these sorts of questions, Drak should go first. Real name, profession, and sources of income please. After all, he has more influence on Core than me.

> Most importantly, the point of Bitcoin is that how people get paid or who they are shouldn't matter. This principle has broken down due to Core's insistence that There Be Only One™, telling people that competing developer teams are "trying to do a coup", the bitcoin.org admins forbidding linking to anyone who supports XT (let alone XT itself) and so on. In such a world who those developers are and who pays them matters a great deal because they're effectively dictators: it's extremely hard to get rid of them even if you want to. 
I really had to laugh at this one. I'm watching Roger's speech and this dude in the orange shirt was so unbelievably triggered. He was randomly shouting out BCASH and was visibly shaking. His friend was patting him on the shoulder consoling him.

Then when Roger said he expects Moore's law to continue for at least a decade, this guy was shouting BULLSHIT. 

Then Roger offered a $10k bet that Moore's Law will hold for the next decade to anyone who would come up on stage and take him up on the offer.

This pic was taken a few seconds later with this guy obviously not going up on stage.
I like how "Fake Satoshi" is the most upvoted comment on /r/bitcoin. How could someone read that complaint and come to that conclusion?
I feel like some of them at Core have always been playing dirty. This isn't the first time Maxwell and the others have said things that are either half true, misleading, or false in order to advance their agenda. 

This should be a lesson for people in the community. Always be honest. You have nothing to gain by being manipulative. 
Yeah it's a lot of cognitive dissonance imo. That plus LN was over sold to kill the blocksize increase. 

In theory if everything works perfectly it could work even if it is a bit clunky. But I tend to think this might be one where theory and practice diverge. Whether routing paths exists depends entirely on how people use the network. Which is not in the control of the developers. 
This is pretty unreal. Ripple has been pitching XRP to banks for like five years now and to my knowledge none have taken the bait. I think they might be selling other enterprise solutions to banks, but none are using XRP in any serious capacity. I think there might be one Mexican bank "piloting" XRP and that's pretty much the extent of the usage. 

So in any normal industry we would call this a failed product with no product-market fit. 

But in this case people decide that failed product is worth $140 billion and make the CEO richer than Zuckerberg.
I really admire how Roger never stops speaking about liberty and never stops trying create more freedom in the world. He also donated one million dollars to the Foundation for Economic Education which is pretty amazing if you ask me. 
\> go

I haven't announced this yet but I've fully ported btcd to bch. It's fully compatible and Nov hardfork is in an open PR.  [https://github.com/gcash/bchd](https://github.com/gcash/bchd)
Listing to this again is so cringe worthy to watch them just repeat brain dead CSW talking points. "But if there's no replay protection doesn't that guarantee that only one chain will survive?" 

Come on man, you don't have to be a PhD in comp sci to understand why CSW's argument was false. 
I don't think they would be forking if Craig weren't involved. Sane people can see that the difference between protocols is not worth forking over. This is about power, not about protocol rules. 
> I assume you very well know that Bcash (or Bitcoin Cash) altcoin is RV's project

It's funny because if you know your history Roger didn't even support Bitcoin Cash when it came out. It was only after segwit2x failed did he change his support. 


It's really amazing. And to imagine that there are people running around calling this group "true cypherpunks". I will never understand. 
My problem is most of these people could have just said "I like BSV better" and that would have been that. OK, not my taste but hope it works for you.

Instead, like 99% of them supported CSW's attempt to 51% attack the chain and cause people to lose millions of dollars. They zealously were rooting for him to bankrupt people, double spend exchanges, and destroy. And when you call them out for this incredibly shitty behavior they just smirked and laughed and said "welcome to capitalism bitch".

These people should not be treated with kid gloves. They are scumbags.

&#x200B;
It's not clear yet that the lightning network is viable. We're not really going to commit resources to it unless/until it proves itself and there is substantial demand for it. 

It's also not clear to me that even if it works well our users would want to pay $20 just to get money into the app. 
No they will pull some line from the whitepaper and claim it says the opposite of what it says. 
The recipient needs to be online in order to provide the invoice needed to kick off the transaction. So that means just printing out a QR code like people used to do wont work. Or at least you would need to have a sever running somewhere to make it work. 

Besides that, there is the issue of your counter-parties maliciously closing the channel and broadcasting an old channel state to try to rip you off. You can foil this attack if you catch them before the timeout but there are several ways to do this and none are really great solutions:

1) Run a full node so you can continually scan for malicious transactions. Core assumes that everyone will do this, but in reality it will be more like 1% of bitcoin users. 

2) Only use Lightning for outgoing payments and never take incoming over lighting then you can't be ripped off this way.

3) Remember to open your wallet once per timeout period to let it sync the blockchain. If the time out period is 24 hours then you need to remember to do this once per day. Lite clients are even more vulnerable here as peers can lie by omission and you can still get ripped off even if you remember to open your wallet.

4) Outsource the watching of the blockchain to a trusted third party. 

It's a pretty shitty UX no matter how you slice it.
Maybe Bitfury's intelligence community handlers can help them out with that.
> I'm actually 100% ok with ceding 100% of the micropayment market to BTC/Lightning

You wont have to. Payment channels can work for Bitcoin Cash as well. 
OK. It would be one thing if they competed fair and square in the market. But fair and square doesn't include massive censorship/social manipulation campaigns, lying and character assassinations, hacking attacks, tricking miners into signing loyalty pledges etc. 

When I say they don't share my values, ^ those are not my values. I don't want anything to do with people like that. This is why I've taken my business elsewhere. I have that right. 
thanks :)
One of the most pathetic things I've seen in a while. These people really do have no life. 
His point is they plan to list it and start trading without telling people how many coins are available. If people assume 21 million and pay $300, only to later find out that there are 10x the coins and they should have only paid $30, then that's fraudulent misrepresentation. 
Sadly history is replete with this type of rent seeking and it's arguably the number one problem we face as a society. The typical story goes like this.

Company X cannot compete openly in the free market so they conceive of some government regulation that will benefit them at the expense of their competitors. But you can't just go around saying "pass this regulation so that we can increase our profits at the expense of other people". So what do they do? They find some way of spinning it such that the regulation is really for the "public good' or for "consumer protection" or "consumer safety".

The brilliance of this strategy is they are able to convert extremely gullible but otherwise well intentioned people to their cause of fattening their profits. Average people start calling their congressman demanding this regulation. Rallies are held. Activists flood the communication channels with support ... All for the private benefit of special interest. 

This is how we got the Fed in the first place. The public was told this was a consumer protection measure meanwhile the President of the First National Bank of Chicago, James Forgan, was holding meetings where he tells the bankers, "The purpose of this meeting is to discuss winning the banking community over to government control, directed by the bankers, for their own ends" (actual quote).

We've seen the same thing play out in the Bitcoin community sadly, but I'd actually be surprised if it worked long term in this case because, unlike regulation, the blocksize isn't mandatory. People are free to use something else. And ultimately we'd expect competition to prevail.
The reason we don't have consensus is there are different visions of what the blocksize limit should do. This proposal uses it purely as an anti-spam mechanism (which was the original intent) whereas others want to use it as a policy tool to set fees. 

Unless those two views can be reconciled it's going to be more gridlock. 
People who want regulation are out of their minds.
At minimum they are smaller than the current signatures, so there's some space savings. And txs would relay slightly faster. 

But it opens the door for signature aggregation in the future which can also reduce the amount of data in a block. 

I don't really see any problems with the idea. It's just a challenge to figure out the correct way to do it. Overload the existing signature, new pubkey prefixes, new opcodes, etc. 
Nearly all Core developers supported a blocksize increase in the past. If you could rollback the clock to like 2012 you'd only find maybe two or three against it (Luke obviously being one). And I doubt you'd get many to openly say that in the year 2017 the network couldn't handle over 1mb. 

What happened? is a very good question indeed. 
How about a little work/life balance lol. 80 hours is ridic. 
No idea what he's talking about.
They have been so completely exposed the last few days they are rapidly becoming a total laughing stock (they already were to people who knew this but now just about everyone knows). 
We will be adding the ability to use other coins in short order. There's no way the default coin will be one that cost $10 to make a transaction. I'm not even sure it's worth even supporting such a coin at all.
I'm probably guilty of using the term single threaded, but it's easier to convey the concept to a non-technical person than it is to explain a mutex to them. But yes the problems are mostly due to locking rather than a single thread.
Remember segwit utxos can use P2SH
We are making solid progress. It's hard to give an exact date but I would hope sometime early next year. 

You are welcome to play around with what we have so far. There's a working network with products and you can make purchases with testnet bitcoins. 

If you stop by the slack channel we can help you install it.

> Will this have a beta release?

There will probably be a slow rollout to more and more people rather than a major public release.
My god the US really needs loser pays. 
I think the protocol will be in pretty good shape to scale in five years time. Whether we have been able to convince enough people to use it remains to be seen. I don't know about absolute price, but I do think we have room to make inroads in our price relative to BTC as I genuinely think BCH can be better tech. Another bull run that blows up the BTC mempool and drives fees back up to $100 would really help us make that case. 
> In short; MalFix minimizes the impact on the base layer, whereas SegWit minimizes the complications of the update.

Like the proposal but I don't think I'd agree with this statement. Segwit is a massive change that touches nearly every part of the code base. It's a huge endeavor to make a full node library segwit compatible. Probably 100x more work than the malleability fix defined by this bip. 
spec https://github.com/gcash/bchd/blob/avalanche/avalanche/spec.md
It's taken us forever to get this multicurrency version of OpenBazaar out the door but it's almost ready. Brian is currently building testnet binaries of a release candidate which can be found here https://github.com/OpenBazaar/openbazaar-desktop/releases. 

When that's out you'll be able to use BTC, BCH, LTC, and ZEC with ETH following not too long after. It should also make adding other currencies relatively easy so maybe next year we'll be able to add more. 

Other than that we've also been doing work on a mobile version which should be out around the same time as this new desktop version. And we've started some work on a browser version that can connect to openbazaar p2p network. 
Probably some libertarian forum. I actually heard about it in 2011 and downloaded the software and ran the GUI and was like "OK now what?", then I deleted it. But it was only like a year later before I spent more time to learn how it worked and got excited about it. Bitcoin was like $7 when I got in. 
Ryan too
So segwit really should have been a non-controversial upgrade. At best it's a small tweak to the transaction format. It became political when they decided to roll it out as a softfork because to do so it was no longer a small tweak but rather a fairly large change and one that is a bit of a rube goldberg machine. 

I have genuine concerns about activating hacks of this magnitude in a $40 billion system. Imo bitcoin clearly would have been better served with a cleaner hf segwit implementation. But, of course no such implementation exists so we would have to chose between doing it right and waiting another year or activating the softfork hack and getting it now. It's really terrible that this is the choice we are confronted with. 

On lightning... there's nothing wrong with having a lightning network per se, but imo it's unlikely to be a viable generalized payment solution for bitcoin. 

In order for random user Alice to pay random user Bob you not only need to find a *path*, through channels, from Alice to Bob, but each hop along the way needs to have the correct value else the payment cannot be made. Intuition tells me that Alice's payment will not find a route 100% of the time (as well as some simulations I wrote).

This is largely confirmed by [this](https://medium.com/@rusty_lightning/miners-and-bitcoin-lightning-a133cd550310) post by LN dev Rusty, where he says that they are capping LN tranactions at .042 btc (valued at $42 at the time of his blog post) since, in his words:

> Large lightning payments are less reliable: it’s harder to find a route with the capacity you need. In practice, the 0.042 limit is an upper bound.

And it's not like under $42 LN payments all of a sudden become reliable. It's a spectrum. Micropayments will likely work well. But it's doubtful everyday payments will. 

I would love to be proved wrong, but I don't think I am. And given that it's not likely to be a viable scaling solution, I don't see how it can be pitched to the community as some kind of silver bullet and used to justify taking other scaling approaches off the table. 
The point of pointing out bugs wasn't to say, "someone should fix that". I suspect Andrew was trying to show that Core isn't God's gift to software engineering as many people want to make them out to be.
If you're running a bchd node please update as soon as you can because it contains a bug fix needed by the neutrino wallets. 
>supposed bottleneck at 22 MB was not a problem.

This is why people who don't understand the technicals of the debate should refrain from advocating one side or another. The bottleneck was at *sustained* 22 mb. Nobody ever claimed that > 32 mb worth of transactions couldn't fit in the mempool. Just look at the BTC network to see how large the mempool can get. The issue was always sustained volume.
Looks like a different approach to updating the channel state in the lightning network that has some small advantages over the current approach. 
The feature freeze was Feb 15th to give industry enough time to plan for the hardfork. I don't think there was enough time to debate, analyze, plan something like this by Feb 15th and it probably shouldn't be something that should be rushed in after the deadline. 

I think it's a nice feature, but maybe one we can improve on in the months leading up to the Nov 15th fork. 

Remember once this is part of the code, there's no taking it out. It really needs to be perfect if we're going to do it. 
> this would be a protocol level change

It's not a protocol change. It's a change to the address serialization. 
They must have forgot what happened the last time colonists arrived at a new land and imposed communism https://mises.org/library/fall-communism-virginia
Privacy. Right now all the existing privacy tech makes scalability much worse and would cause infinite inflation if the cryptography is ever broken (for example quantum computers). 
For one. There is no metric by which you could plausibly claim Bitcoin Cash is "centralized" that doesn't also apply to Bitcoin. I know it's easy to just regurgitate propaganda without thinking but that doesn't make the claim true. 

Second, I did try it and it didn't work. And even if it did work there's a major difference between being able to make test purchases in a laboratory setting between nodes only one or zero hops apart and having any random user on the network be able to find a path to any other random node for any value. I'll take note of it when the later happens.  
I think it's important to point out that at $20, $50, $100 transaction fees very few people will be willing to pay a multiple of that amount to open lightning channels. 

This leaves us to ask, what other off chain solutions exist that can function at those fee levels? The only real answer is hosted wallet where you turn over control of your bitcoins and have all the the other problems with centralization (national security letters, etc). 

And it seems like the product he's talking about is designed to facilitate clearing between hosted wallets (the enterprises that are mentioned). 
I've been saying that for a while. We have a bitcoin FOMC with basically the same amount of control as real FOMC. 

At this point I'd be open to supporting a fork that changes the pow algorithm. Same blockchain up to height 500000 or so then new rules kick in. The market will decide which coin they value more. No Chinese miner support needed. 

It would be a chance to get some items checked off the hardfork wishlist as well. 
They can't follow BCH's approach because it will require hardforks which their community will never accept. 
It's about the same amount of data if you have a small number of keys, like a 2 of 3 multisig. But if you're using like 8 or more keys then it's less data. If you had 50 keys the savings would be pretty dramatic. I said potentially because the savings is dependent on the number of keys. 
We're going have a soft release in September. If you would like to play around with it and help uncover bugs, send us an email. 

Also, /u/alonmuroch and I have been slaving over this project without $50k in crowdfunding like some. By the time were done with it, I'm pretty confident it will be the most private and secure software wallet available. If you'd like to send us some coin to support the project this is our address:

14vd62t6whJJtcwD2hKjxTCXUnheE6CAyo
Would be nice to have a BCH conference this year
Haha thanks but that was a different Go implementation. I think that might be related to the wormhole project. 
I haven't looked at how they do it but I believe the original opcodes were disabled because they had bugs in them. So obviously you'd need to implement them differently if you're going to re-enable them. 
You could say this by nearly all of them. Very few people on that side would I consider a quality human being. 
I wouldn't say he has done any good. Guy is literally one of the worst people in the space. I don't know how he sleeps at night.
I think it's probably implied that that, "When confronted with two valid chains, the longest one is valid". 

The issue is what constitutes a valid chain. That's what's determined by community consensus.
1) Not much more than a few months out. From what I can see it looks like it's working reasonably well right now and they are just polishing it and ironing out some bugs. 

2) Well I think their narrative needs to be decisively refuted. To a large extent they seem to be winning the PR battle or at least remaining on even par with BCH in that area and it's all based on BS. That needs to be pointed out so people who don't know any better don't fall for it. I also spent a lot of time refuting the bogus narratives put out by Core. Though they were more effective than us in that area. 

3) I think we just need to continue building and continue innovating. That will sufficiently differentiate us from BTC. 

4) I spend a few evenings a week working on BCH stuff. I don't have any plans to change that atm.

5) Without new features, probably. But not without improving the underlying tech. Our goal has always been to scale while remaining decentralized. That's not going to be possible without continuous improvement. 

6) This is a tough one because we have a lot of things to deal with (like volatility) that other apps/currencies/payment systems don't have to deal with. And it's always 100x more difficult to do things decentralized than it is centralized so it feels like we're always fighting an uphill battle. I don't have any real solutions other than to remain laser focused on UX and try to close the gap with centralized systems and then sell our advantages. 

7) I don't think we ever have enough devs for my liking. But if BCH gets popular and the price goes up I think we'd attract more. Part of the reason bchd exists is to provide an implementation that is easier for new devs to get up to speed and work on.

8) I think it's going to take a lot of time with a lot of ups and downs along the way. A big time economic crash might make more people take a look at cryptocurrency, but that wont do it by itself. 

9) Here's a decent UTXO commitment overview https://www.yours.org/content/first-utxo-commitment-on-testnet-db7bf45bf83d

I think people are always free to use the technology however they want. If people wanted to keep the old rule set alive, they were free to do so. The ABC software would have automatically activated replay protection for them so it would have been easy if anybody wanted. 

But for people who made the conscious to use BCH with the new rule set, they wanted to make sure they weren't going to be attacked and lose lots of money. Adding reorg protection in the face of active threats of reorgs and attacks seems like a reasonable thing to do. 

It seems to me like the people who are upset with that decision are people who actually wanted BCH to be attacked and lament the fact that the checkpoint and reorg protection prevented that from happening. 
Another point. Even to the extent we may introduce breaking changes. Is it not better to get them out of the way while the ecosystem is small and only a few companies need to refactor their software then to wait until hundreds of millions of people are using it and then say "oh shit we've hit a bottleneck and need to do a breaking change"?

This talking point is coming from CSW where he tells his followers (all of whom are not technical) that the software and protocol are just fine and can scale to to billions of people with no changes. And since they don't know any better they get mesmerized by the technobabble and believe him. 

Then they say "isn't it better to lock down the protocol for statbility?". It's anti-science bs.
Hopefully I'm not going too far out on a limb and someone figures out that it doesn't work. 
It's funny because there are like one or two libertarians still left in the Core community and the swear up and down that these guys are really libertarians and cypherpunks. If you ask for evidence they say things like "well how could they not be?". 

It's getting really sad. 
The UASF was very transparent. The whole reason Core/Blockstream didn't propose it outright is because of optics. The needed it to appear like it was some kind of grassroots effort independent of Blockstream. But they did a lousy job hiding it. 
    OP_RETURN <flag> <IPFS hash> <description>

An app could then read every tx that has the flag an build a searchable index. Could maybe even use transactions to "like" files which have an accurate description and are of high quality. Likes could even leave a review that is viewable by others. 
lol 
It was rushed because the UASF idiots would have forked the network if the code was not done by August 1st.
They haven't won until the results are in. There's a non-zero chance that what they end up producing after this hostile take over, censorship, and community split.. sucks balls and doesn't satisfy the needs of the market. 
> We can't keep following up with ABC changes if what they are doing keeps on breaking our work. 

What changes required you to change your software? Unless you're writing a full node I don't know why you would need to rewrite your software. 

As far as I know the only change that may have required *some* developers to refactor their code is the change to the DAA. And even there the only wallets that I know of that were affected was my wallet and Electron Cash. 
Nobody ever cared about about SV existing. We only care about our chain existing and not being attacked by them. 
CSW tried to buy out the BCH devs but they told him to go stuff it.
My recommendation is to do what memo is doing and put the command in a separate pushdata from the payload. This way people can use bloom filtering to filter out all transactions except the ones related to blockpress rather than downloading the whole chain.
I have no idea how a coin that does nothing original but merge changes from Core can have any value. 
This guy is delusional:

> I'm very excited about grin as a totally new way of safely scaling a Bitcoin-like system.

"Safely scaling". WTF is he talking about? He think grin.. with larger txs and more CPU requirements can "safely" scale while BTC with smaller txs and less CPU requirements can't? The only advance of Grin in the scaling department is the historical chain can be pruned. But we can mostly do that already with BTC, albeit maybe with slightly less security for a new bootstrap than Grin but that hardly accounts to "safely scaling". This just shows he knows he's full of shit when it comes to scaling BTC.

> The grin currency is a poor store of value

LOL. It's no worse than BTC or any other cryptocurrency which are incredibly poor stores of value right now. 


I don't know if he did or didn't do that personally. I also don't know what his terms of service was. If he did that and if the TOS allowed it then I don't see any problem. 

I'd also point out though that Bitcoin.com only has something like 300ph on BTC before the fork and after the fork the Bitcoin.com hash rate went up to 4000ph. So the vast majority of that obviously wasn't his BTC hashrate. That was other people joining the bitcoin.com pool to help defend BCH. 
>exact opposite of decentralization

Not sure how this is the "exact opposite of decentralization". It has always been possible for people to configure their node with any checkpoint hey want.

`--addcheckpoint= Add a custom checkpoint. Format:'<height>:<hash>'`

This is not unlike users having the ability to set the blocksize. Because the software ships with a default `excessiveblocksize` does that mean it's centralized? If not, why does shipping with a default list of checkpoints make it that? People are always free to run whatever software they want and configure it however they want.
So this is going to be a bit of an MVP. It's only going to support using one currency at a time. You'll be able to choose the currency on startup. If you want to use another one you'll have to tinker with the settings and it will create a new node with a different data directory for the other coin. 
Also this was consumer grade hardware they were running on. Basically equivalent to a laptop you could get at best buy. 

The one caveat is they still need to repeat the test with larger utxo set sizes so the numbers may come down some, but I don't think it will change the underlying thesis that consumer grade hardware can handle very large block sizes. 
I'm aware of at least five different algorithms that were proposed. All of which were probably fine. At some point you just need to pick one and get it over with. 
Pretty sure this dude is an outright fascist.
If a majority of miners are not acting honestly then *bitcoin* itself isn't secure. It's just not SPV nodes. 

I never got the reasoning behind criticizing SPV for not handling a 51% attack well when it's been known from day one that full nodes can't handle it either. 

A honest or rational-profit-seeking majority is a requirement for bitcoin to function at all.
For one, most users have loads of excess bandwith. A pruned node uses like 220 kbps or something like that. Basically nothing when you have 100 mbps+ 

Second, 97.5% of full node bandwidth is used to upload blocks to other nodes. If you improve blockchain sync, you radically reduce bandwidth usage. 
And just as an observation, if you have to put "cypherpunk" in your twitter profile it almost certainly means that you are not one. 
There's a good deal of refactoring needed in the user interface to handle other coins. That's not really my area though. 
Well if you build a business around the idea that the limit will not be raised... 

Btw, this is the reason why I don't expect any genuine scaling solutions to come from Blockstream/Core.  If a solution were implemented that provided for large scale then they wouldn't have a business model.
Yes! It makes bitcoin unusable for purchases at brick-and-mortar stores. 

Contrary to Peter's assertions, the probability of a merchant who accepts zero-confirm transactions getting defrauded is currently very low. Lower than credit card charge back rates and merchants are more than capable of calculating that risk (even more so given the public nature of the blockchain) and pricing it in to their products. 

This patch would dramatically increase the rate of double spends (the goals is basically a 100% success rate) and force merchants to require at least one confirmation. Which, of course, most brick-and-mortar stores cannot do. 

The only way to try to salvage it would be to use the scorched earth tactic which requires all buyers to pay extra for the product and get a refund after for the difference after it confirms. 

I contend the UX for that is so poor it would seriously harm bitcoin adoption.
Nothing to see here. Bitcoin is serving it's intended purpose... sitting in a paper wallet under your mattress. Don't you know actually using it for things was soooo 2014.
I'd highly encourage people to start small. 
That's because I rently sold 20% of my coins. There's no better predictor.
> We're you a miner?

> Uh, uh, uh. ..... a long time ago.


He's like a little child
When I lived in Pittsburgh they had (and probably still do) a flat tax levied on the first paycheck of the new year. Many part time workers actually received a paycheck of $0 because it all went to pay the tax. 

Nothing like working for two weeks and taking home zero. 
"Bcash has 3 developers, Bitcoin Core has hundreds". 

That's a propaganda line if I ever heard one. Anyone who went to SV this past weekend can attest there are more than 3 devs working on Bitcoin Cash. 

And hundreds for Core? Maybe if you count all the typo fixers who submit trivial commits just to get their name in the repo. In practice it's about a dozen or so regular contributors. 
Lol you saying I'm lying and then make a statement like that which anyone can check the validity of in 2 seconds. 
For me bitcoin will fall far short of it's potential it all it ever does is service illegal markets. Central banks fear competition, a coin that never services anything but 0.01% of the economy is no threat. 

Edit: and notice it's usage in illegal markets is on the decline as most darknet vendors prefer monero now. 
Consensus is whatever they say it is.
Will they relinquish control of this sub when XT becomes the majority chain? Since, well, they will be supporting an alt coin at that point. 
You can always build another layer on top of BCH that has ethereum like functionality. At the base later you can't store and manipulate state in a contract like ethereum can.
Yeah there's a lot of things that could possibly be built with the technology. We've thought about spin off apps that would basically work the same under the hood but would be targeted at a specific market niche. 

While social could be one of them, most of the OB-specific plumbing is really related to just making orders. Any social app would likely make use of just the IPFS parts and not the OB layer. 
This is exactly right. They're crying foul because they want to be able to attack the chain and cause BCH users and exchanges to lose millions of dollars. 

Real classy people. 
Yes. The keys and output scripts are still the same.The only change is how those output scripts are presented to the user. 

To be clear... funds are send to a script which looks like:

    76a91408e84b3be82339c8ea85993968cb3514930a13f088ac

But an address just takes that script and serializes it into a user friendly format:

    12cuWcn5xQ3hgVU6FgyGWuWWeprKSTExi4

And this is just a change to the user friendly format:

    bitcoincash:qpzry9x8gf2tvdw0s3jn5grmq650p

Correct. Obviously you only want to use a wallet that uses the new address type if other wallets understand it and are capable of paying to it. It's kind of one of those things where everyone should upgrade around the same time to avoid segmentation. 
I personally don't view that attack as realistic. You have to remember that segwit activates on nodes as well. If miners reverted to pre-segwit rules then their blocks would be rejected by upgraded nodes. They would need to attempt such an attack before most of the network had a chance to upgrade. And given that I think 80% have already upgraded, I don't think that's likely to work. 
Btw if you didn't see the panel. Stephen Pair basically said "Bitcoin is not working for us as a company. At this point we have three options 1) Fork Bitcoin 2) Fork Bitcoin and 3) Fork Bitcoin."
These relentless dos attacks by Core supporters tells you all you need to know about the quality of people you're dealing with.
This article sums up everything I've been saying for a while now:

* People wont adopt bitcoin if they can't use it. Since the stores on campus didn't accept it, people lost interest. The pipe dream of a hispster coin which people only invest in and not use is delusional. 

* Since they couldn't use it, they just sold it. Again, assets don't have utility because people save it (as the small blockers believe) instead people save because the asset has utility. If you take away that utility, people simply have no reason to save it. 
That's one problem. The other is that the keys are generated while you are online. 

If your system is compromised, your keys could easily be stolen.

I would consider it less secure than cold storage when the keys are generated offline 
Good points. Whenever someone tells me that it makes no sense to spend bitcoins at subway because they could be worth a lot more. I tell them that it must also make no sense to spend dollars at subway since they could have bought bitcoin with those dollars.

People just don't think things through.
> Minute 36: He proposes entities (companies) that should take over control of transaction validation from users, and do it in exchange for money. So you want to transact BSV, you pay that entity.

Shadders alluded to miners playing this role in his presentation. He talked about miners selling services to people and one of those services would be an API to see if your transaction is included in the blockchain.

And if you consider their "vision" for Bitcoin entails basically facebook scale infrastructure to run a node then the only people who will be in a position to tell you if your transaction is valid or not is these handful of miners. 
Yeah, you can badger merchants to accept BCH or cryptocurrency in general. A couple of friends of mine Derrick and Steven have nearly single handedly gotten many restaurants and shops in portsmouth NH to accept cryptocurrency (including BCH). It's really pretty amazing to see so many businesses in such a small area accepting crypto. 

http://freestatebitcoin.com/tour/
Listen to your mom. 
I think we'd need to create a subscription protocol. 

- The service provider creates a QR code (or URI) that you scan with your wallet. 
- The wallet asks you if you want to approve the subscription.
- While the subscription is active the wallet connects to the server and receives BIP70 push notifications from the service provider. 
- Wallet tells you a payment is due and ask you if you want to pay it. 
- You could probably have the wallet auto-approve the payment if you really wanted it though that would require keeping your wallet unencrypted. 
You need to reevaluate your life
Lol the lawsuit alleges that Craig is Satoshi and him and Klienman are the owners of Satoshi's 1.1m btc. I wouldn't exactly call that a scam unraveling. 
Frankly I know this is 100% bullshit because nobody, given the choice between $30 or a 2MB blocksize (which is widely recognized in the academic community to have zero negative impact on decentralization) nobody would chose $30 fees except through brainwashing. 

But regardless I frankly don't care if few dozen brainwashed people want to keep the Bitcoin blocksize at 1MB. Have at it. It has zero effect on me. I'm just not going to invest and time, energy, or money into a project run by incredibly shitty people and which is supported by a shitty community. 

> The hacking attacks against BU were fair game from my point of view, if big blockers didn't want hacks they shouldn't have coded such crappy bug-ridden software. 

Showing your true colors. And the bugs are not what I was referring to as a "hacking attack". Illegal DNS amplification DDOS attacks, unrelated to bugs in the Bitcoin Unlimited software bugs, were launched against every Bitcoin Unlimited node. 

If someone did the same thing to Core it would take every node not behind Cloudflare offline. 

Glad you support that. Classy guy. 
That's because the BCU drops to zero value if the hardfork doesn't happen by the end of the year. Of course that's going to trade at a discount.
Luke does not cite where he gets the 17.7% bandwidth growth figure from. I assume it came from Rusty's blog post where he tried to estimate it. 

Then later Rusty wrote another [blog post](https://rusty.ozlabs.org/?p=551)
revising his previous estimate upward to 30%,

>  instead of 17% per annum this suggests 30% per annum as a reasonable growth rate

Of course, Luke has decided to sick with the lower number. 
This is the road you have to go down when you refuse to allow higher transaction volumes. 
Matt Corallo runs a high speed block relay network on BTC. Which is frankly not needed at all since they have 1mb4eva and the peer network works just fine at that block size. 

But bloXroute is working on a souped up high speed relay network for coins that are actually trying to scale beyond 1 MB. And now Corallo is shitting in it because nothing could possibly be better than what he created. 
Maybe partially. Multiple clients increase the risk of a chain split due to bugs in one or more of the implementations. One client can be following the spec where another one can have some really tiny bug in some obscure file and when the edge case is hit it causes a chain split. If everyone runs the same implementation than that bug just becomes a consensus rule. Kind of like the bug in OP_CHECKMULTISIG.

But the benefit of multiple implementations is you reduce the risk of systemic failure due to an incompetent/hostile dev team like we saw with Core. 

Imo the benefits outweigh the risks. 
This is awesome. One thing they might want to look into is what are the standard and max transaction sizes allowed on the network today. Not sure off the top of my head but Lighthouse needs BIG transactions to be useful. 
SIGHASH_NOINPUT was one of the possible options considered for fixing malleability on BCH. 
Anyone know how many tickets were sold?
It's really nice to just be able to make stuff up. That miner you're claiming has all these patents and does the "majority" of mining... has 12% of the hash rate. https://cash.coin.dance/blocks/thisweek

Also that same miner has 18% of BTC hashrate. https://coin.dance/blocks/thisweek

I know critical thinking is hard and all but you shouldn't give up. Keep at it. You can do it!
This is a new low for them. The requirements of the MIT license were fully satisfied there is literally zero to complain about. Unbelievable that he filed this issue.  
full out panic
It really boggles my mind how Core can go around saying "only Google will be able to run a node". 

Yet I look at my home laptop and it could process 100-200x more transactions and still have plenty of room left over to stream Netflix. And by the time volume increases to the point where my computer can't handle it (which has to be over a decade out) I will have a much faster computer and connection by that time. 

Satoshi was right that unless there's a very unlikely explosion in volume, it's hard to see ordinary computers ever not being able to fully validate the chain.
That doesn't make any thing he said wrong. For example, he found Craig forging emails under penalty of perjury  and Craig was forced to withdrawal the emails.
Yes all BSV people. 
Wonder if these are all their fake UASF nodes they forgot to take down.
I have no hate for LN. I think it has its uses. If I looked at the proposal and thought, "hey this is really good. It will preserve all the useful properties of Bitcoin while keeping blocks easy to validate" then I would have supported it and might still be a BTC supporter.

But it was always obvious to me that the claims being made about LN were an extreme long shot to actually be true and that LN was being radically oversold in an attempt to kill the blocksize increase by convincing people who aren't technical enough to evaluate it for themselves.

Yes LN is currently in beta, but in beta it's performing even worse than I thought it would. I do suspect the route failure rate will come down. I don't expect it to come down to a rate where a decentralized network topology is viable. 

And while centralized hubs may work, we'll have to wait and see if it will even be desirable and viable to run a hub at scale.
Pretty sure he didn't research it. Someone made up the line for propaganda purposes and he just repeated it without checking. This is how groupthink and confirmation bias work. 
> If Gavin and Mike didn't want their character assassinated, they shouldn't have behaved in such immoral ways.

And this shit. There is not a single example of them behaving immorally. This is the precise type of bullshit that I want to have nothing to do with. Both of them are very upstanding people that never did anything unethical or immoral. 

Go play with your store of value coin and stop spreading lies and shitting up reddit. 
I don't do this for your benefit. It's so other people reading your comments know you're a lying piece of shit. 
> many would argue it was the UASF that forced miners to finally do what they were supposed to.

And those many would be wrong. UASF had <1% support from all economically irrelevant users. Miners were 100% prepared to just ignore them. 
Thanks. Seems to working for well for me. Haven't made a ton of transactions with it but all the ones I have made have gone through with no problem and it hasn't had any problems syncing the chain. 
The UASF isn't what broke the stalemate.... it was the NY agreement. 

The only reason btc1 rushed the code out was the August first deadline. If the UASF didn't exist the same thing would have happened only a few weeks later and with more code review and testing. 
Having a better discovery UX is a high priority. 
This shouldn't be a surprise after all the hard work he's put in to break zeroconf.
I agree with the hard fork comment. It seems like the core devs have basically convinced themselves that hard forks should never been done, even when there is consensus. 

I don't see the danger of setting the activation threshold very high, like 90-95%. It seems like that is no more disruption than a soft fork at a lower threshold. 
>For instance getting 1 transaction out of a 1GB block on most clients actually parses the full block (taking approx 3GB of mem). On Flowee it just reads the transaction itself. Typically a couple hundred bytes.

&#x200B;

bchd does this as well
Well said
It's just the beginning of a pre-consensus implementation https://github.com/gcash/bchd/compare/avalanche

We'll probably have it running on mainnet in a few weeks and logging data. 
Also keep in mind, if you go to 1m block time you increase SPV overhead by 10x. 
You might as well announce to the world you have no idea how software works. 
If they had any honor they would have quit when he started talking about attacks. Especially the developers who were asked to write attack code.
https://youtu.be/tPImTXFb_U8?t=2526

> Adding DSV adds quite a lot of complexity to the code. It's a big change. 

Having recently implemented it I can tell you the change added almost no complexity. It fit nicely within the existing patterns in the code and was one of the easier consensus changes to implement. 

Here's the commit https://github.com/gcash/bchd/commit/f137f0c05703a2eeae773103409d6a062af38811
He's a sociopath so I wouldn't expect less.
    func validate(sig, key5, Y1, X4, K6) {
    	const root = <rootHash>
    	K5 = sha256(key5)
    	X3 = sha256(cat(K5, K6))
    	Y2 = sha256(cat(X3, X4))
    	R = sha256(cat(Y1, Y2))
    	if R != root {
    		return false
    	}
    	if !checksig(sig, key5) {
    		return false
    	}
    	return true
    }
1) I'd personally prefer IPFS rather than torrents at this point. It's more robust and has more functionality. 

2) If you're going to flag OP_RETURN transaction, the flag should be in a separate pushdata so that lite clients can filter on it. 
Not sure I'm sold on the name but I would argue that once we get the double spend relaying up and running there probably does need to be a way to distinguish between a new unconfirmed tx and one where enough time has passed without detecting a double spend that a double spend is less likely. 
See I was always very skeptical. Prior to Bitcoin I spent a lot of time reading monetary history. And imo it was the L2 that destroyed the gold standard. Gold worked reasonably well as money when people traded the coins directly with each other. But it was the beginning of the end once people started depositing their coins in banks and trading bank notes. Not that it couldn't work in theory, but in practice it was centralized enough that the government found L2 very easy to regulate and the crony capitalists were all too eager to use regulation to profit. 

Unless the L2 for Bitcoin can have exactly the same proprieties as L1, it will be hard pressed to avoid the same fate. So far I haven't seen any proposals for an L2 that come close. 
Isn't he supposed to be an economist or something? I would think he would know better. 
Another demonstration of your class.
I have not taken a deep look at it but from the presentation it seems like a pretty novel combination of a bloom filter and iblt. There's not much to it so off the top of my head I don't see any weaknesses. 
It puts you on the one agreed to by the majority hash power. Yes it can't handle a 51% attack but full nodes would be in trouble in such a situation as well.

And fraud proofs are on the cash roadmap.
It's a lite wallet that connects to the p2p network. It's not a full node but full nodes do give it cryptographic proof that your transactions made it in the blockchain.
> 56 companies located in 21 countries

> 83.28% of hashing power

> 5.1 billion USD monthly on chain transaction volume

> 20.5 million bitcoin wallets

I'm pretty sure they don't need luck. 
If you polled the "technical community" in 2011, maybe only one or two would have said the block size should be 1MB in 2017.  
I think it's safe to say that's why people like /u/bitusher think they have a huge super majority.

"Why just look 99% of comments support the roadmap".
My biggest fear is that they made the decision to go "all in" on this technology while 1) having zero experience with it in practice 2) without knowing if it's even capable of being a genuine replacement for on chain transactions. 

Let's just say hypothetically, 10% of your transactions fail to find a route (who knows what the actual number will be, it may be more may be less). Can this even be considered viable at all?

Imagine walking into a store, picking up some merch off the shelf and heading to the register. You pull out your phone to pay, and "ERROR can't find a route". Now what? You could make an on-chain transaction, but we had a developer single-handedly blow up zero conf transactions. So making an on-chain transaction is out of the question. And even if the merchant is willing to trust you and accept the risk, you might have to pay a $2 transaction fee (maybe more if you had to close a channel to free up funds). 

Can such a payment system exist at all in a competitive market? Does it make any sense to bet the house on this working 100% perfectly when there are so many unknowns? This is a multi-billion dollar system and they are behaving like it's start-up amateur hour. 
> No. Soft forks used to introduce unpopular features are essentially malware. If you don't like a feature in 0.12, don't run the software. I sure as @#$@ am not running it.

If you don't run the up-to-date software after a softfork activates then you essentially have a non-fully-validating node (unless you back port the patch). That's part of the reason why some people (like [me](https://chrispacia.wordpress.com/2016/01/11/against-softforks/)) oppose them as a way to upgrade the protocol. They get a handful of mining pool operators (basically everyone on the stage in hong kong) to upgrade and then, boom, everyone is forced to accept the protocol change else run what's basically an SPV node. 

It's not a very democratic way to make protocol changes. 

(Also RBF has nothing to do with soft/hard forks. It's a change to the default mining configuration.)
It's probably too premature to pass judgement. It's unlikely we would use an altcoin though unless it overtook Bitcoin.
The webpage does say communists are not allowed to apply for citizenship. 


>Money laundering helps criminals because once money is laundered, they can spend it without fear of it being traced to their crimes.

Being able to speak on the phone without it being recorded helps terrorists because they can speak without fear of being traced to their crimes.

And being able to send an email without it being intercepted and read likewise allows them to communicate without fear.

>Anti-money laundering laws help to prosecute crimes and make it more difficult for criminals to get away with crimes.

Mass surveillance helps prosecute crimes and makes it more difficult for terrorists to get away with crimes.

Needless to say that isn't a valid justification for violating people's rights.
Prior to the last hardfork once you met the requirements to release funds from an address (such as a signature, etc), you could send the funds to any address you wanted.   


A covenant is a restriction of which address you are allowed to send funds to. The OP\_CHECKDATASIG opcode that the BSV people fought so hard against is what enables this.   


So the LastWill contract uses a covenant like this. It says, "If you sign with the hot wallet key you can only send the funds back into this address". Which refreshes the timer. So if the hot wallet key gets stolen the worst a the thief could do is just reset the timer.
Would like to see someone build a client in Go. 
> BU would surely fund the roll out of double-spend proofs (I would personally drive this initiative forward) if it was likely that it would be adopted by the majority of nodes and miners.  

I think it's likely this would be. Not sure why you feel the opposite. I would implement it in bchd, you guys in BU, probably XT, and while I don't know if anyone from ABC would allocate the time to implement it, I'm pretty sure if a PR was ported over from BU they would merge it. 

> The trouble now is that Avalanche partly competes with this proposal

I personally don't think the two are mutually exclusive. For me, I doubt any SPV wallets are ever going to use avalanche and will *need* double spend proofs if they are going to be able to detect double spends. This is regardless of what happens with avalanche. Not all full nodes might run avalanche (and it's not clear it's desirable for all nodes to run it anyway) so double spend proofs are going to be necessary here too. 

And like you said, double spend proofs are something that is easy and tangible to do right now. Avalanche couldn't make it into the protocol in less than a year from now even if we wanted it to. And in practice it's probably going to be well more than a year as we've only begun even thinking about it, let alone collecting data, doing research, and forming concrete proposals to be evaluated. 

I'd really like to see double spend proofs implemented. And as fast as possible IMO, but it needs an owner. Someone to write a spec and shepherd it through from spec to implementation. 

I would like to do it but I don't know if I have any time. Between working a full time job and working on like a hundred side projects I've got very little free time. 
Well I think CSW was clearly the big loser. He made a lot of hard predictions and not one of them came true. He embarrassed himself, embarrassed Calvin, further ruined what little reputation they had left, and now they are stuck working on an altcoin that isn't likely to remain in the top 10 for very long. 
I wouldn't have changed anything. I would have preferred to not lose some of our user base to BSV, but at the end of the day the absolute number we lost was pretty small. If we do our jobs and increase BCH usage, we'll more than make up for them. 
I have no idea what you mean by that. It's not possible to mine compatible blocks. There's a flag day hardfork. 
> What does '2 years' mean?

He has floated this idea that, you know, since he's Satoshi, he will dump his BTC and buy BCH in 2020. Maybe that's what he's alluding to. 
It's not likely more scalable than ABC right now.  It will need a good deal of work. Contributions from others would be welcome but for me I will probably only work on it as needed. The focus is more about being a solid blockchain server for applications. 
They're talking about sharding the work between CPU cores to improve performance and scalability. Not sharding the blockchain like ethereum is tryign to do. 
akshully....  


Since the data is readily available and the probabilities are calculated offline, you can change the numbers however you want and see what kind of results you get. For example, if you increase the capacity of every channel by 10x you still get a 60&#37; failure rate for a $100 payment. 
It's nice to see this finally being worked on. Arguably the goal of this protocol is the same as the goal of Luke's getblocktemplate in 2012 just getblocktemplate was horrifically unoptimized to the point where it was unusable. Sadly it has taken six years to get a proposal for something better and in the interim mining has become much more centralized. 

Should this be implemented it would go a long way to making mining more decentralized. 
The regulations are mostly crony capitalist measures and were a contributing factor in the crisis. 
Regardless of whether that is true or not, the point of this post is to show that even with the requirement to run a node on a home computer, it still scales. 
I say go for it. You have my support.
They list the male number but don't say the male and female number combined. 
Ugh, the whole point of the UASF (and I'm assuming those are UASF nodes), is to goad the miners into changing the consensus rules. The more it appears like *most* of the community is going to orphan their blocks, the more likely miners are to consider changing the consensus rules. In this context astroturfing of this type can have a real effect on the miner's decision. 
I think Adam was more reasonable before blockstream (and through the early days there). I think 2-4-8 was probably a genuine proposal but it was shot down the the 1mb4ever people. Then, of course, group think sunk in the same as it has with many others who used to support increasing the blocksize. 
This what you get if a handful of devs have their way.
Looks like:   

    nActivateSizeForkMajority = 750; // 75% of hashpower to activate fork
    nSizeForkGracePeriod = 60*60*24*14; // two week grace period after activation
Coinbase sucks as a wallet. Only use them for buy and sell and that's it.
Had a payment processor tell me they are considering removing BTC as an option for that reason. That plus a terrible UX that they don't want their customers to have to deal with.
The economics are actually the exact opposite of what you say. If people started doing fiat -> BCH -> Fiat there would be genuine demand for the asset capable of supporting a price. 

Store of value is nothing more than a euphemism for a speculative trading asset. If the only demand for the asset comes from speculation then it's basically a Ponzi that is going to zero eventually.
Everything he says is designed to justify centralizing the network. Since the whitepaper was released everyone has been on guard for a 51% attack and the view has always been that mining needs to be as decentralized as possible to ensure that 51% attacks are difficult if not impossible. 

So how do you convince people to accept radical mining centralization? Just tell the plebs that miners don't collude and call it day. There, now you don't have to worry about 51% attacks due to centralization. 
All you technical illiterates are going to wake up to cold hard reality one day and wonder why your chain stopped working. 
> is it silly to worry about centralization due to block size limit increase at this scale?

Yes. Your typical home computer can process blocks much larger than 32 mb. 

> Will we see an increase in orphaned blocks due to the increased block propagation time that would result from larger blocks?

There are techniques to improve block transmission that have yet to be implemented. When implemented a 32 mb block will transmit faster than a 1mb block today. So we should actually see orphans go down not up. 
It's not that we're experts it's that people like Pierre are totally clueless. The more an asset is used as a medium of exchange, the more demand goes down really? 

This is up there with the time Alex Bergeron claimed utility is a made up concept.
He makes a living as a contrarian. 
/u/jaqk you asked why people don't doing rejoin the bitcoin project ... it's because it's infested with people like this ^
not until they are spent
This is amazing almost every paragraph contains a false statement:

> Some personal thoughts on accelerating Bitcoin adoption as we get close to the end of 2017, an interesting and dramatic year full of user adoption and events for Bitcoin, to say nothing of the market growth!

If you don't count speculative trading (which I think it's fair to say you shouldn't), Bitcoin had negative user adoption in 2017.

> This time of year is sometimes a time when people pause to reflect on collaborative spirit, and Bitcoin is a friendly industry and ecosystem where almost all of the competition is from outside - fiat, banks etc.

There's nothing friendly about it. It's incredibly hostile to anyone who disagrees with their dictatorial position over development and the direction they are taking Bitcoin. 2017 also saw them hire an uber troll for the "Chief Strategy Officer". A big middle finger to the notion of a friendly environment. 

> Bitcoin market demand is high, and resources are straining which may slow Bitcoin’s adoption and I think we all want to see the Bitcoin experience on usability, transaction speed, transaction cost, security all improved, and reasonably soon.

No resources are straining. None at all. Computers have more excess capacity than ever. 

> None of these side-effects of high demand are designed - they arise from run away market success bumping into technology limits.

They we're all designed... by HIS COMPANY!

war is peace freedom is slavery ignorance is strength
> I suspect that people don't want to roll out two updates, and wait for the reference implementation to come out so they can roll out all of it in one update

Fwiw in OpenBazaar we used the native P2WSH for multisig because our own nodes understand that. But it does make it difficult for people to send funds to to these address from outside the wallet. So what they end up doing is sending funds into the native wallet and paying from there... which costs an extra fee.

We also haven't yet implemented segwit for P2PKH addresses because I'd prefer to use P2WPKH but again, nobody else support paying to such address types. And there's no way I would implement the nested P2SH segwit addresses because it's such an invasive refactor for my code and it's not as efficient as P2WPKH and I'd have to do another invasive refactor just to switch back. It also blows up restoring coins from a seed.

But all this is moot now as fees have basically destroyed the OB use case for Bitcoin and we've had to switch to BCH. I suspect BTC will probably be deprecated if the fees remain where they are and all my BTC and segwit work would have gone to waste. 
You do realize that "store of value" is a secondary function. An asset must have primary utility of some other form. What other utility does bitcoin have if it's not permitted to function as a medium of exchange due to arbitrary restrictions?
I think monero might have more severe scalability problems than bitcoin (cash). I also don't think monero allows for SPV nodes (someone could correct me if I'm wrong) which ends up requiring people to run full nodes and pretty dramatically reduces the potential user base. 

Also the lack of m of n multisig is probably holding it back as well (though I think they have a plan to fix that?).
Sharding. Should be done well before 32 mb.
Whatever helps you sleep at night dude.
Yes
Surprising coming from Heritage. 
I think it's possible. We'd need to all stakeholders to agree on a spec which is the difficult part.   


Also, while it would work the fact that payments are push rather than pull means the UX isn't quite on par with credit cards. You could get a notification that you have a bill due and could click a button to pay it, but it would still likely require manual user action.   


Automating the payment has a lot of challenges because the user's device might be turned off or not online. And even if it wasn't, the user might have a pin/password set on the wallet which would require manual unlocking of the wallet.   


So it's possible but less than ideal UX.
The person looking to spend the funds locked in that address gets the oracles signature from the external source and then uses it as an input to the function call in the script.  


This is actually how Bitcoin should have been designed from the beginning. Currently it works like this:  


`<signature> <pubkey> OP_CHECKSIG`  


The OP\_CHECKSIG operation hashes the transaction and then validates the signature against that hash using the public key. But this prevents signing arbitrary data as it literally only allow you to sign the hash of the transaction. It should have been something like this:  


`<signature> OP_SIGHASH <pubkey> OP_CHECKSIG`  


Where OP\_CHECKSIG accepts a message digest and then something like an OP\_SIGHASH could have been used to push the transaction hash onto the stack for validation. But alas this was a big oversight by Satoshi.  


So the current protocol change calls for leaving OP\_CHECKSIG the same but creating a new OP\_CHECKDATASIG opcode that functions the way we would expect where you can pass in a custom message digest:  


`<signature> <message_digest> <pubkey> OP_CHECKDATASIG`   

It's just how the opcode was originally designed. 
    
    OP_2 <pubkey2> <pubkey2> <pubkey3> OP_3 OP_CHECKMULTISIG
> TL;DR: Bitcoin.com wallet had 2M downloads in the last 6 months, LN will max out before 200,000 active wallets and the solution is still purely theoretical.

If they keep going the "you need to run a full node to use this software" route they will never get to 200,000. Pretty sure there aren't that many people on the planet who want to run a full node. 
A couple of the Wu-Tang guys came down to anarchopulco last month because they were apparently interested in Bitcoin and crypto anarchy. They played a show too.
Watch video and comment with your opinion. 
Any guesses how much the Core troll spent doing this? 50k? more?
Bitcoin Cash and ZCash are going to be released soon. XRP is centralized and not a good fit for our project. 
This question would probably need to be revisited after Ethereum implements PoS, sharding and plasma. Right now the system can barely handle current use cases. Adding large volumes of day-to-day payments on top would really bog it down. 

But if their scaling solutions work well then it might be a serious contender for payments as well. 
Some of you may be around long enough to remember a similar phenomenon with dark wallet. 

Every week or so there was a /r/bitcoin post like "Guiz, Let's do another fund raising round for for Dark Wallet! Let's do this!"

What was amazing was those posts continued even after it was clear dark wallet was dead and all the previously donated money had gone to waste. 
> they currently seem to prefer the car made by core

The users are not a homogeneous blob. There are quite a few who disapprove. It's hard to gauge the exact numbers, but certainly this sub makes it seems like everyone is a Core supporter because most non-Core supporters have their comments deleted and are banned. 
It would not surprise me to see Bitcoin Cash pass legacy Bitcoin at some point. 

The buttcoiners in the other sub seem to think a crippled blockchain that is too expensive for anyone to use will magically have value because "digital gold". 

Those who have even a minimal understand of economics know that value is tied to utility and to the extent an asset with no/little utility has a high valuation basically means it's a bubble waiting to pop. 

As someone who actually builds bitcoin applications it's a no brainer to build on a blockchain that's actually usable compared to one that isn't. 

I strongly suspect the more usable chain will out compete the less usable one over time. 
> if sw2x gets adopted, Jeff Garzik will become lead maintainer of Bitcoin

I'm not sure that's what Jeff wants. 

Jeff is currently leading a working group with a very narrow charter to make a few small changes to the Core software. Jeff has stated that should the signers of the NY agreement come to consensus on any other changes to bitcoin then another working group could be created to implement those changes. 

I don't think the purpose of btc1 is to be a formal bitcoin implementation that has continual development. 
I personally prefer a spec like this than a free floating variable like in BU. 
> The primary function of SegWit is to increase capacity.

Judging by the comments of some of the devs I'd say they find the fact that it increases capacity (only slightly) to be lamentable. 
I was (and still am) willing to bet someone who's making these claims that segwit wont translate into any signification increase in capacity any time soon. 

I offered Greg a bet something like the average block size (measured over 1000 blocks) wont be above like 1.5 or 1.7 within a year of segwit activation. 

But he didn't take the bet. 

Still available if anyone wants to take the other side. 
I would work on getting a proper (reviewed and agreed upon) spec for flexible transactions. 

I would also do more research on bitcoin-ng/byzcoin
> holders inherently accept a soft fork

Orwellian is the correct term here. Miners can basically do anything they want in a softfork... including creating a new consensus rules that makes your coins unspendable. Or even make it so they own the only spendable coins, basically confiscating everyone's wealth in a mega bitcoin heist. 

But according to him this is all part of the bitcoin social contract that people agreed to when they started using bitcoin. 
Who cares. It's fanatical hobbyists running nodes. If bitcoin ever gained mainstream adoption you'd see fewer people running nodes at home than run email servers at home. 
Having your txs confirm in  185.822377ms is pretty nice though :p
More work has been done on the logo since the split then the codebase https://github.com/bitcoin-sv/bitcoin-sv/commits/master
Anyone want to write the Schnorr signature code in Go?
A coin mined by a single person, controlled and owned by a single person and which strives to be "government friendly" is the real deal? What planet are you from again?
I like this quite a bit. Could be a more permanent solution than what ABC implemented.
And for comparison I'll quote Ryan X Charles:

> The only engineering challenge is removing DoS vectors. 

It's obvious who knows what they are talking about and who doesn't. Follow non-technical people at your own risk. 
Do any SV supporters still want to argue that 128mb is safe?

I'll quote Uri Klarman of bloXroute

> x10 larger blocks cause x10 more forks & re-orgs

> At x100 the blockchain “unravel” to more and more forks & doesn’t converge back to 1 blockchain
> I’m aware of no evidence that Craig could code at all, let alone had excellent C++ skills

Remember he plagiarized hello world when asked to prove he could code.
Yes we don't have assholes like you relentlessly censoring pushing whatever propaganda line your ministry of truth decided the plebs should be regurgitating. 
He's saying *nobody* can validate sustained 128MB blocks because the software cannot handle it. There is no software out there that can. ABC is trying to build software that can handle it and you guys bizarrely think they're attacking Bitcoin Cash. 
It's more conservative and fits the same pattern of the existing OP\_CHECKSIG.
Very unlikely to need millions of txs per second any time soon. Visa is only about 2k-10k.
lol thank you. With a little bit of luck and the right emotional support, I might just come out of this OK. :p
That rant sounded like a typical leftist to me. Claim some company is "greedy"  and you need to use regulations to deal with it. "For the people". Meanwhile those very same regulations just so happen to help your own bottom line. 

Free markets no where in sight.
I don't think he's going to be getting any more articles in Forbes
Notice how he assumes no blocksize increase. Sure you have a big problem if the blocksize stays at 1mb and the subsidy becomes very tiny. The only way to finance mining is by astronomical fee levels. To replace the current subsidy exclusively with fees you'd need about $68 per transaction. 

So instead he proposes lifting the 21m cap. 

Notice that you could also replace the block subsidy entirely with only nickle tx fees if you have gigabyte blocks (which will likely be feasible well before the subsidy runs out). 

The only concern on our end is making sure that tx volume exists when the subsidy runs out. 
They are needed in some capacity to disincentive miners from trying some funny business. I agree not everyone needs to run a node but at least some percentage of economic activity needs to be fully validated. 

In the future I'm hopefully we'll get to sharded nodes, which are like partial SPV nodes, but where collectively everyone validates everything. That would be sufficient to have the right incentives. 
The required changes are not that large. Take an existing library and fork it and you're all set. 
> In the last couple months people associated with Bitcoin "unlimited" have been arguing that mallability is a non-issue, a fake concern (with unspecified motivations) and opposing segwit on those grounds

I like to think I'm pretty well read as bitcoin forums go and I've never seen this argument made even once.
Imo Flexible transactions would be much better for bitcoin long term.
Well there aren't any production ready implemtations if that's what they mean by working. 
I like this gem:
> SegWit is a softfork, it does not need consensus.
Shhh Eric, you're not supposed to talk about managing risk. You're just supposed to accept the party line that zeroconf are NEVER safe and make all your customers wait an hour. 
I agree that the equation:

> Bigger blocks -> Harder to run a node -> Less nodes -> More centralization

is misleading outright but even more so when you consider what is driving bigger blocks... adoption. With adoption you also have a phenomenon that looks like this...

> More adoption -> More people who need to rely on fully validating nodes -> More nodes -> More decentralization.

The question is which effect outweighs the other? Right now most merchants use a payment processor so increased adoption isn't likely to drive much of an increase in full nodes. But will it always be like that?

If merchants start using Bitcoin outright, without a payment processor, surely we would see the number of nodes go up with adoption. 

Again we can debate which effect is the primary driver of node count, but it should be clear the first equation is far too simplistic.
The last commit was feb 6th and the wallet has more or less been in the same unfinished state for 6 months or longer.
Wow this is some shady ass marketing: 

> “When you transfer $50 worth of bitcoin to your dollar card, we sell your bitcoin for $50 on a bitcoin exchange, credit your dollar card $50, and hold that money in our **full** **reserve** **of** **assets**, thereby eliminating bitcoin (and its volatility) from the equation,” said Caitlin Looney, vice president of marketing at Bitreserve.

By throwing around the term "full reserve" they make it seem like your funds will be held in 100% reserve, but read further and that isn't the case.

> “The value in our full reserve and its ability to be **invested in a portfolio of low-risk securities**, however, will be our primary revenue generator,” Looney explained.

Why use the term "full reserve" when in fact it's fractional reserve? Is that not fraudulent misrepresentation?


I've got plans for a java implementation using the orchid library. Im going to automatically change Tor identities between each step. 

But I'm still a couple months from beginning work on it. I have a lot to do in the mean time.
For those interested in learning what the other side believes and positively engaging with us, I highly recommend starting with Huemer's book. It's the best exposition of libertarian anarchism out there.

>My political philosophy is a form of anarchism. In my experience, most people appear to be convinced that anarchism is obvious nonsense, an idea that can be refuted within 30 seconds with minimal reflection. This was roughly my attitude, before I knew anything about the theory. It is also my experience that those who harbor this attitude have no idea what anarchists actually think–how anarchists think society should function or how they respond to the 30-second objections. Anarchists face a catch-22: most people will not give anarchism a serious hearing, because they are convinced that the position is crazy; they are convinced that the position is crazy, because they do not understand it; and they do not understand it, because they will not give it a serious hearing. I therefore ask the reader not to give up reading this book merely because of its conclusion. The author is neither stupid, nor crazy, nor evil; he has a reasoned account of how a stateless society might function. Whether or not you ultimately accept the account, it is very likely that you will find it to have been worth considering.
I make shit with more than 2 users.
Say the guy which such a severe reading comprehension problem that he is STILL stating I said this despite numerous people linking to my actual words for months. 

Allow me link to myself https://www.reddit.com/r/btc/comments/bemxay/the_bsv_chain_has_just_experienced_a_6block_reorg/el71fd4?utm_source=share&utm_medium=web2x
You would think, but we've seen some incredibly dumb people in their community. Almost all are non-technical and shout down technical people and insist they are wrong. It's the flat earth club of crypto.
Why do you make the leap from businesses wanting to run full nodes to average people trying to use bitcoin? There's zero chance normal people would run a full node even if you had a 10kb blocksize. 
If everybody "uses" bitcoin in this manner it will require a perpetually increasing number of people "using" it in order to maintain its current value.
Checkpoints where used in Bitcoin all the way through 2016 (and as another comment says, they were put in by Satoshi). They were only removed by the core developers in recent years. Some implementations still have them. Here's my codebase where the checkpoints were added way back in 2013. https://github.com/gcash/bchd/blob/master/chaincfg/params.go#L268
biased much
I will never use it. It's not a decentralized currency. Minded 100% by one organization and has a lunatic acting as protocol dictator. And he's going to unilaterally implement ridiculous protocol changes like allowing miners to steal people's old coins. Wouldn't touch it with a 10 foot pool. 

And I've got a ton of really cool BCH software about to come out too. It would be a shame to see it all go to waste but I will just shut it down before porting it to BSV. 
Man you changed my mind. I've been strongly in the Bitcoin Cash camp since the fork but your post was just so enlightening that I feel compelled to switch my allegiance now.
The answer coincides with the Hal Finney post from earlier. People took their gold coins to banks for convenience and those banks started issuing (mostly fractionally backed) notes and deposits on those coins.

Governments found it very easy to regulate and control such a system and the bankers actively sought out regulation for rent seeking purposes. 

Eventually the regulations created so much instability that people clamored to completely replace what was left of the free banking system with fiat money and central banks. 

This is why it's ridiculous for them to suggest "we can just use bitcoin as the base layer and build a banking system on top". People who argue for that have no idea what the history of money and banking looks like nor why Bitcoin was created. 
This my only tweet with him. Instantly blocked after I saw that. (maybe my response to his Liquid/sidechains comment was a little more snarky than I remembered it to be but it's still clear I was not talking about lightning). 

https://twitter.com/ChrisPacia/status/870156166130749440
Jacob if you made that page using a previous release candidate can you run this command to update it. https://gist.github.com/cpacia/751589a9c70399713774a0ac5c954c15

There is a key table migration that needs to be run. 
You cannot receive a payment unless 1) your counterparty deposits their own money into their side of the channel or 2) you make an outgoing payment first. 

So unless there is some well financed hub that is will to commit funds to channels up request, then you have a weird situation where you can't receive payments into your LN wallet unless you spend them out first. 
It's best not to rely on it for larger payments. 

Right now you can possibly double spend just by pushing two conflicting transactions to the network. You'll have a 50% probability that your double spend will get mined. 

We can maybe eliminate that risk by adding double spend proof relaying (which people are working on) but that only helps so long as every miner is using the first seen policy. If a 10% miner is running replace by fee, then you have a 10% probability your double spend will get mined. 

Ultimately other solutions are going to be needed to improve the security of fast payments. 
Well LTCs whole roadmap is to just merge in commits from Core. Which means it will run into the same problems with fees etc. If you want to scale you need optimize the shit out of everything, which the BCH developers seem committed to doing. 

As for LN. I don't particularly have high hopes. Maybe I'll be surprised, but even if it works as expected (which seems like a long shot) I just can't see $40-$100 fees to open channels producing a viable consumer app. 




He says he has "a lot of money at stake". Didn't he have a post a number of months back saying he cashed out to pay student loan debt or something like that? Maybe his definition of "a lot" is different than mine.
Spoken like someone who criticizes without reading first. 
Yeah I've been in since $9 and haven't sold any. But at this point I don't know what to do with the coins. If bitcoin is allowed to scale then it still has huge upside. But if things stay the way they are then there's really no future unless people don't mind making transactions through centralized institutions and Blockstream taking a cut of every transaction via Liquid. 
What would your opinion of bitcoin's decentralization be if the only way to transact at a reasonable cost was to use an exchange?
This guy also praised the Venezuelan economy before it collapsed. 
As pointed out in the OP, that doesn't require Full RBF. FSS would do the same thing. 

It's hard to see any use case for full RBF except fraud.
This is probably why they flipped out when the issue of Baltimore was brought up. And then the judge did a 180 and refused to allow any more questions along those lines.
There is something to be said about different management philosophies producing superior results for investors. Mackey has been pretty successful at Whole Foods. But I'm largely skeptical that the strategy that works for some firms will work for all firms. If that is the case then 90%+ of the corporate world could increase stock performance by adopting a different strategy. Are we really going to suggest that greater profits are out there to be had but these companies doggedly cling to inferior strategies for no good reason? Again I'm skeptical. 

But the worst part of the piece was trying to link this concept to macro performance. For the Yglesiases of the world, it's all about "spending". Just garden variety "spending" and dammit businesses just aren't doing it enough. Spare me. 


Reorgs create uncertainty around whether your transaction should be considered final. Frequent reorgs means users have to wait substantially longer for the risk of losing the transaction to a double spend to drop to a low enough level.   


This is a reduced level of service and seriously harms the cash use case.  


If you persist in ramming through blocks of that size you'll have so many reorgs that the chain cannot converge and you've lost consensus.   


I probably shouldn't be telling you this and just let you guys figure it out when it happens to you.
>centralized coin and "community"

And join the community that strives to make blocks so big that the only way to make the network work is to have all nodes run in a single data center. And one where one guy decides the entire protocol and everybody just has to do exactly what he says.
> also known as small-world network as Satoshi explained it)

Craig Wright is not Satoshi Nakamoto. Satoshi never called Bitcoin a small-world network. He never advocated only a handful of centralized mining nodes. 
Well CommunistAndy, we're trying to build a platform for uncensorable free trade of private property on the internet. OpenBazaar is an ecommerce marketplace like eBay or Etsy, but works like bittorrent under the hood. So users trade directly with each other in a p2p network rather than going through servers. They pay each other in cryptocurrency which works the same way. 
I'm really at a loss how people are so quick to put out material that embarrasses themselves like this. 

Every comment I make or article I write I ask myself "How likely is it that I'm wrong and will make an ass of myself if I click publish?"

Apparently these guys have no shame.
>we'll

Thanks
Stress tests should be done on testnet and not mainnet. 
> They use ABC, so they can't handle the additional load.

Anyone notice how these people always accuse the other side of things that apply directly to them?

The BSV "stress test" reorged the chain, had half hour propagation times, and mined a poisen block that didn't show up until 4 blocks later and they accuse the only people who are doing active work to fix these issues as "not being able to handle it".

BSV supporters are seriously the flat earthers of crypto. No amount of empirical evidence placed right in front of their eyes will convince them that they are wrong.
CTOR and CDS are trival changes. CTOR by itself reduces the amount of data that needs to be transmitted with each block by like 70%. Only CSW cultists who can't think for themselves say otherwise. 

> You can ask yourself what the Internet would be today if a small set of software developers had changed TCP/IP protocol every time they felt a little bit low on attention?

You know this is a good example. Because TCP is "locked in" you can't improve on it easily. So while there are superior transport protocols out there like QUIC, your "locking in" means people are stuck with inferior technology. 

In fact because of this QUIC operates in user space and is currently on version 47 and represents 7% of all internet traffic. Yes that is 47 different versions of QUIC have been deployed in an attempt to improve the protocol and optimize things. 
I don't know what to say except all of the Unwriter points seem delusional to me.
They both listen on 8333 by default so you'd want to have bchd listen on a different port and then connect it to your other node

    bchd --listen=0.0.0.0:8555 --connect=127.0.0.1:8333

After sync you'd want to restart without connect so that it will connect to other peers. 
The majority doesn't decide what blocks my node, or bitpay's nodes coinbase's nodes, etc accept as valid. 
Sure it is. BSV supporters are claiming that 51% hashrate defines the protocol rules. Satoshi explicitly says 51% hashrate does not "throw open the door to arbitrary changes" since nodes validate the protocol rules. 
The reason it's 1CounterpartyXXXXXXXXXXXXXXXUWLpVr  and not 1CounterpartyXXXXXXXXXXXXXXXXXXXXX is because valid addresses must contain a checksum.
I don't know what makes him think that "anonymity will be trival to implement at the bank layer". In the next sentence he acknowledges that banks don't operate like this right now but then says "we'll see this more and more with Bitcoin". 

Why? Are regulations going to magically disappear because ... bitcoin? 

I've actually talked to bitcoiners who think this will actually be the case. That people will run illegal underground banks just like silk road to facilitate people's payments. This seems delusional to me. 
This sounds like people who want to go to jail to me. Even if the app is truly decentralized (which it sounds like it isn't), the government will claim that building something for the explicit purpose of flouting the law doe not entitled you to legal protection. 
First part of that is extremely unlikely to happen.
There was a patch merged into Bitcoin Core that would have relayed the first double spend attempt around the network such that merchants could detect the double spend attempt. The process would be, receive a payment, wait a few seconds to see if a double spend comes over the wire, if no then accept the payment. 

But, a wikipedia style edit war took place and they unmerged the patch from Bitcoin Core under the belief that it's better to let merchants get scammed so that they "learn their lesson" then to give them tools to manage risk. 

I suspect we will see a (likely improved) double spend relaying in Bitcoin Cash at some point. 
What matters is the fee at which you can get mined. Not the average fee. If people want to pay more to incentivize miners let's let them. 
Amir is super ideological. Which I think is a good thing. But I think that makes him more inclined to read into things rather that see the big picture. 

When he was out there calling for the 1mb cap to remain in place and saying that anyone who advocates increasing it is a threat to our freedoms... it was completely clear that keeping the 1mb cap would result in exactly the situation that we have today. Again, failure to see the big picture. 

(fwiw always felt like Peter Todd knew this would happen and just didn't give a shit)
That's false. The Grephene they are referring to here is a block propagation algorithm. It's able to compress blocks about 10x more than the current algorithm. 

It could be implemented without any other changes, but it does require a canonical ordering of txs in a block to work so it might make sense to couple it with other changes related to canonical ordering. 
> You've conned a bunch of investors with rainbows and unicorns to get a $76mil funding

They literally told prospective investors "if you invest in us you are buying access to the people who control Bitcoin"

But cypherpunks
    privkey1 = hash(seed +1)
    privkey2 = hash(seed +2) 
    privkey3 = hash(seed +3)

Slightly more involved than that but that gives you the idea.
I'm curious of the plans for yours. If it's intended to stay as a hosted website I'm not sure I see a need to make onchain transactions. It's already managing your wallet and just swapping balances would save on transaction fees. 

Now if it was to be fully p2p then I could see. 
Nobody will be using LN if fees blow out to $50-$100. It would cost hundreds in fees just to open channels to begin using a LN. 

The far more likely scaling solution is the one Blockstream has invested heavily in... everyone uses fully trusted hosted wallets and pays Blockstream a transaction fee to clear their transactions with other hosted wallet providers using liquid. That's a more realistic future in a 1mb world. 
> ONE study goes against the vast majority. 

Again you're in denial. Here's the quote from the last paragraph of the article from Neumark and Wascher.

> the oft-stated assertion that recent research fails to support the traditional view that the minimum wage reduces the employment of low-wage workers is clearly incorrect. A sizable majority of the studies surveyed in this monograph give a relatively consistent (although not always statistically significant) indication of negative employment effects of minimum wages. In addition, among the papers we view as providing the most credible evidence, almost all point to negative employment effects, both for the United States as well as for many other countries.
Yeah, it seems delusional to me. He says he will need to wait for segwit to make his bitcoin spendable but then what? You get 6 months to a year of being able to spend bitcoin and that's it forever. You know damn well they don't want any blocksize increase after segwit. 

And another thing... All this talk about needing to keep the cost of running a node down so that people can run a node on their home computer. Where we are headed is the only way to make a transaction without paying $50 fees is to use coinbase and similar institutions as your wallet. In that world *nobody*, except maybe coinbase, will be running a full node. Why would you if you can't use it to transact?

I don't know how this isn't obvious. 
Who the hell uses ripple?
The entire article centers around how we don't know the topology and the topology will affect the level of privacy. Again, I understand this is cognitive dissonance for you but the argument isn't "it's not ready". 
Are you really trying to pull this? You can (quite obviously) post here yet most people from here cannot post at all in your hallowed places.
The main issue for OB seems to be UX not on chain txs per se. The Dark Market protocol design was such that every node has to remain online 24/7.  Many of our users have rightly been frustrated with this requirement (as are we as we were looking for alternatives even before 1.0 was released).

Now it's possible to distribute the data more and make orders asynchronous and our 2.0 version which does that is under heavy development.

But to make it work you need to be able to make escrow payments asynchronously. But the way LN works, you can't really do escrow payments without all three parties being online at the same time. So it's basically back to the "every node must remain online" model which we know people don't want. 

And it's ironic because some of the most ardent LN proponents in the other sub also criticize OB 1.0 for requiring nodes to remain online 24/7. It's like they don't even understand what they are advocating for. 

And look at the criticism I got over there for raising UX concerns (the very concerns **they** raise about OB 1.0). Someone class act even made an entire post mocking us essentially for trying to address these concerns and, of course, it's one of the top posts. 

At the end of the day, we don't have much influence in Bitcoin and we'll make OpenBazaar work one way or another regardless of what the community decides. 


Despite Zander having writing some code, FT is mostly just in the concept phase. It's ridiculous to suggest that it's "deeply flawed" when all the details aren't even fleshed out. 

There's nothing that couldn't be fined tuned if the proposal had more than 1 person collaborating on it. At the end of the day, as a concept, it's vastly superior and it makes extending bitcoin in the future very easy without having to try to cram a square peg in a round hole like you have to do with softforks. 
https://ipfs.pics/Qmf2EtXt6VypHY2rniLFV79JSdCFHtFKJp1afdsLekFZH3
Sadly this is where we're heading. 
1000x better than SegWit imo. 
This is one of the many problems with the federal reserve. It used to be the case that Americans could just simply save cash and it would appreciate by 3-6% per year. 

After the Fed came along, only an idiot would save cash as you would lose 3-6% per year. So it forces people to go out and invest in risky(er) assets and purchase services from stock brokers just to get a positive return on their retirement savings. 

It creates a natural demand for those services, which when couple with massive government restrictions on who can sell such services, creates massive profits for Wall St. 

Then the very people who support these policies campaign around the country criticizing income inequality. 
First off this is hardly an attack where Reddit can just revoke the gold. 

Second. That's bullshit. While double spending has always been easy, it's always been trivial to detect and hence decline the payment. 

It has never been trivial to send a payment to someone, then minutes later send the double spend and have it get in the blockchain (which is what to you need to do to successfully steal anything). It's RBF that changes that. Claiming that it has always been trivial to do that without RBF is either misinformed or dishonest.
> Then why did Adam Back himself advocate "insurance" for LN hubs, admitting that LN hubs could "steal" your Bitcoins?

The scenario described was one where you have less bitcoins in the channel with a hub than the cost of a bitcoin transaction fee. I think the counter party might be able block you from getting your coins out of the channel in that case. Or something like that. I think that's a genuine concern which will require some trust in the hub operators. But the hub cannot normally run away with your coins like Coinbase can. 

Also to note, the way they designed the protocol, if a counter party tries to broadcast a previous channel state (to steal back a payment) you will have 1 week to open up your lightning wallet and scan the blockchain to catch him in the act. If you go longer than a week without opening your wallet, the counter party can steal your coins. 

This is a rather ugly wart on the protocol imo. There's even talk about having third parties scan the chain for you while you're offline for a fee. 

A significant downgrade from how we use bitcoin today. 
The point is that up to this point almost all miners have used the first-seen policy. It doesn't matter that nodes may see txs in a different order. All that is (was) required for a merchant to take zero conf txs was to have the pos terminal flag a tx when two conflicting txs were detected. Order doesn't matter. 

It's only miners adopting RBF in place of first-seen that poses a threat to merchants because detecting a double spend after the customer walks out the store doesn't help you.

Peter Todd's argument (and yours as well) has been that rational miners will choose RBF over first-seen because it maximizes profits. Yet a number of people have questioned the validity of this logic because:

1) It focuses only on profits in the short run (one-time game) rather than on what policy maximizes profits over the long run (repeating game).

2) Miners have not voluntarily adopted RBF even though they have had six years to do so. When the empirical evidence does not match the theory, the theory is probably wrong. And given #1 it almost certainly is.

So we are left with this push to implement a policy that will clearly reduce the utility of Bitcoin based on a (very likely) bogus economic analysis.
By the end of the year is a goal. There's still a lot to do in that time frame. I would refrain from giving a hard date at this point. 
This same concept could be applied everywhere. Why have food stamps, housing vouchers, medicaid, etc? It's all vastly less efficient than a simple cash transfer. 
Except if you bought when he first recommended it you would be up 330%. What an ass. 
> . It's hard to imagine a currency with 100 mil active daily users to be unusable for weeks like in the ABC/SV fork.

It was never inactive for one. BCH never experienced any downtime. Exchanges did halt trading, however, because a psychotic mad man was threatening to attack the chain and double spend exchanges. 

But that had nothing to do with the BCH protocol upgrade and had everything to do with the child-like behavior of CSW. 
The kind that don't exist. 
Signatures are not the only source of malleability. We will be activating additional script verify flags in May, however which will hopefully eliminate the remaining sources of third party malleability. 
> Have you ever considered that some people are building stuff on top of unwriter APIs or related software like Flowee?

I can't speak for Tom but I can't imagine he's exposing an API that would be affected by CTOR or CHECKDATASIG.

I maintain a full node. Yes, I have to put in work (a couple days or writing code, and several rounds of review) to update the consensus rules to be compatible. 

But none of the changes affected the bchd API. Anyone who was building anything on bchd would not have noticed a difference. 
1. Zeroconf priority. We had a developer retreat (on the ABC side) of 20 devs across six different implementations to improve zeroconf. No nchain devs we're present. And as far as I can tell they think nothing is wrong with zeroconf and doesn't need fixing despite the data showing it is trivial to double spend zeroconf right now. So the only one who will actually improve it is the ABC side. And we left the conference with a pretty solid plan to do so. I'm not sure nchain devs even know what the plan is. 

2. stress test. I don't see this as a selling point. People should be spamming testnet for testing not mainnet. But in any case the ABC codebase is what mined the 32mb blocks. 

3. Blocksize 2020. Again, nchain devs think the software can already handle 2GB. This is laughably wrong. I seriously doubt they are competent enough to know what needs to be changed to handle that level volume. On the other side we have a solid roadmap to get there that goes beyond just raising the limit to a level that would surely crash the network without software improvements. 

4. I don't know what "blocksize config by miners" even means. All implementations allow for a configurable blocksize. It's just not recommended to configure it beyond 32 until it's proven safe. 

5. 2009 codebase... this is not a selling point. Not anywhere close. Further it isn't even true as CSW has announced plans to radically change the protocol to allow unspent coins to be claimed by miners if they haven't moved in a period of time. That's way more radical than anything ABC has proposed. 

6. "honest government" ... oxymoron. 
Nakamoto consensus does not resolve incompatible forks.
Also no way the lightning network will work with $1000 fees. You'd need to make tens of thousands of transactions while the channel is open for the fees to be competitive with anything else. 

Also shows how dishonest people like Tuur were who hard sold people on LN to kill the blocksize increase while secretly knowing LN has no future if they get their way. 
I know you try to censor the shit out of people and try to control what they believe at least as hard as any government does. 
Well it's missing features. No utxo cache, no prune mode. So it's very slow to sync the chain. Also I haven't worked on porting the wallet code at all.
I should have noted that was the last number I heard of and it was from last year. If it's gone down it's only because fewer people are using /r/bitcoin not because the moderators have relented. 
25&#37; ... only if everyone upgrades. You've seen how that goes with segwit. In practice you're probably looking at something more in the range of 10&#37; for the next few years. 
Let's not forget that it is his poorly designed stratum protocol that is responsible for much of the mining centralization today. It really takes some nerve to call Bitcoin cash centralized.
I don't think that scenario will happen. Very unlikely that people will be routing payments on mobile devices since they wont be online 24/7. 

Only way that would happen is if they have a heavyweight server grinding away at home and they link their mobile app to it. Which I think is mostly how they expect people to use it but which seems like a pipe dream to me.
Happy Easter to you too man
Have you seen the nonsense coming from Hogg and his friends?
Kinsella has been acting like a douche lately. Especially after drinking the Core Koolaid now he feels like he's knows everything about the blocksize debate. 

With that said I still expect him to win this debate as it doesn't seem like Craig Wright has a coherent position on this issue. 
This was verge
In many jurisdictions in the US garbage collection is a paid service provided by competing private companies. Imagine that.  

Also not hard to imagine private roads functioning substantially better than socialized roads. 
Seriously read the LN spec yourself independently without allowing yourself to be influenced by others. Then ask yourself how likely it is that such a system will become a viable general payments layer (and not just a micropayment system). 
So you were lucky enough to slip in during a lull in the $15 fees. Do you expect your lucky streak to continue is that why you feel like Bitcoin isn't broken? How do you feel about the core devs wanting fees to go higher?
So you can't backport commits without it being "stealing" now. This is a new low (and yes the requirements of the MIT license were fully satisfied). 
I completely completely agree with all of this. My impression is most of them only care about making money through speculation and don't give a shit about any use cases or about changing the world. 

Don't get me wrong, I've made a LOT of money in bitcoin, but I would gladly trade it all to see bitcoin achieve it's potential to disrupt banks, central banks, and governments (though if it does that I suspect it would be worth a lot more). Improving my financial standing is not as important to me as changing the world for the better.

> Did a bunch of non-libertarians just join the Bitcoin community?

That's probably part of it. But I never got the impression many in that crowd were libertarians to begin with. One of them was trying to tell me the main value proposition of bitcoin is because central banks are going to use it. No joke. 

> Where did the entrepreneurs, the "misfits" run off to?

I think a lot of people got fed up and left. Or were driven out. 


> uasf was gaining momentum

yes it went from 1% to 2% of bitcoin economy. Some momentum.
Diane, nice work. I attempted something similar but less formal. If all the assumptions hold, the data is pretty optimistic but you stop your analysis at $22 for the "big" payment. 

If lightning is to be a generalized payment system we'd like to make payments much larger than $22 (very few of my everyday payments are less than $22 for example). 

Can you also repeat at values $50, $75, $100, $200, etc and report the results. 
It's a good thing bitcoin is only a store of value /s

I feel like this is their last stand so I wouldn't be surprised if they did. 

Also, I'm 100% sure if segwit2x activates they will start up a similar astroturfing campaign to get people to not run the hardfork portion of the code in a similar attempt to scare the miners into not going through with it. 

That astroturfing campaign will probably be bigger than the UASF one. 
> They continue to block progress on protocol development, fail to follow through, and keep on upping their demands, never satisfied with anything we deliver.

This is very rich. The only olive branch offered by Eric has been "You give us everything we want, and we'll give you nothing you want" and he has the audacity to act surprised that the other side isn't satisfied. What planet is he living on?
Literally just got off a call with our UI guys who need to implement the wallet UI and our UX designer spent the entire call shaking his head at how terrible the UX is surrounding stuck transactions and bumping fees. 
That's not really it. This is a side channel attack on a VPS. The VPS itself could be secure and the keys can still be recovered. Although no one should be reusing keys like that anyway, but if you use a service that uses a VPS and reuses keys, it's insecure. 
If this becomes the default type of address, it's hard to see how it wouldn't provide significant anonymity. 

If *every* bitcoin user is generating new addresses for each transaction, how do you link transactions to identities? There would be no need to send everything through a mixer to a single address because the stealth address would *be* your single address. The wallet would just handle everything else behind the scenes. 

32MB is enormous. It's likely more than all cryptocurrency volume combined. More than a payments company with a $127B market cap.   


We are a long way from filling that capacity. Likely years if it's going to happen at all.

By the time we do fill that capacity we will have made it technically possible to raise the limit. Also remember all BCH nodes are designed to be able to lift the cap without the help of developers as it's just a command line option. So we are really of no risk the developers doing what the BTC devs did.
If you think this account is a Greg sock puppet you're an idiot.
So when CSW is forced to withdrawal evidence he submitted to a court under penalty of perjury because Greg/Contrarian proved he forged the evidence, I guess that was a fake "proof".
> Those miners will eventually be forced out of the game by biggers blocks, and that's by design. When all miners are in data centers, gigabyte blocks won't be an issue anymore.

You might as well just build a centralized digital currency at this point. Why keep up the pretense of decentralization if you're just going to run everything out of one or two datacenters?
So like I can make a transaction saving certain data into a contract and then another transaction can read from that data. Or even write to it as well. 

You can't do that with BCH. But you are correct that it makes ethereum less scalable. 
I haven't seen any evidence to the contrary but their behavior was certainly fishy. 
In May the remaining scriptverifyflags will be made into consensus rules. If there are other sources of malleability it would basically be a zero day as I don't think anyone knows of any others. 
Nobody thinks it's insanity. As far as I can tell the position of every developer has consistently been 1) Bitcoin can easily handle way more than 1mb today so restricting the blocksize to 1mb is ridiculous and 2) With optimization, research, new technology, and a lot of hard work we can make it scale to very large sizes on chain. 

Nobody ever said we could just "lock down" the protocol as is and the system will just magically scale to terabyte block sizes. 

This was CSW preying on the ignorance of non-technical people in this space. 
The reason we are in this position is because BCH isn't popular enough. Hashrate follows value. Not the other way around. We need to face the fact that we haven't done what it takes to get BCH past 10% of BTC in value. If we had CSW wouldn't be in the position to attack the coin. 


This whole thing is brain dead. 

1) He claims burning is "an attack on the monetary system". How? No explanation. It's like he doesn't understand that coins are effectively infinitely divisible (with a small protocol change). Or he somehow thinks "deflation bad" without realizing the entire system is designed to promote deflation. Or does an extra 0.1% deflation (or whatever burning amounts to) put it over some magic threshold where it becomes bad?

2) Returning lost/burned coins into circulation effectively is taking value away from holders and transferring it to miners. This would only make sense to do if you thought the original design of Bitcoin failed to subsidize miners enough to provide a high enough hashrate to secure the network. But I thought version 0.1 of Bitcoin was perfect? Apparently not, he's now claiming. Not to mention he doesn't even attempt to provide any evidence for the claim that miners need more subsidy. Or does he not even realize this is a transfer of value from users to miners?

3) I'm not even sure OP_FALSE is the most used way of burning coins. Does anyone even use OP_FALSE? There are near infinite ways to burn Bitcoin and he focuses in OP_FALSE for some reason (with a bizarre example of the "legitimate" use case for OP_FALSE where it's being used to as a place holder for a bug in OP_CHECKMULTISIG). 

4) This could only be done in a world where the miners are the dictators of the protocol. In the real world merchants/exchanges/some users run full nodes and fully validate blocks. If the miners were to unilaterally make this change the rest of the ecosystem would not accept their blocks (Assuming these stakeholders disapprove of increasing the miner subsidy at their expense. Which is a rather good assumption).

5) That he writes this article to promote BSV shows how out of touch with the community he is! He thinks this protocol change is a selling point!
Shapeshift had to give in eventually.
Look in a mirror buddy. 
> by fake Bitcoin cash supporters and devs.

You do realize the people you're calling "fake" are the same people who were fighting Core since like 2014 and the very people who brought Bitcoin Cash into existence. Wright had nothing to do with it and still has contributed zero. 
He gets his information from Tone Vays so it's not surprising. Misinformation spreads very quickly when the receivers of it are not technical enough to evaluate claims on their merits. 
> Monero is the only coin that solves this.

Personally I think this sentiment is dangerous and will likely get people busted while using Monero thinking they are safe.

From what we know about this case the dude got busted because, once they linked his handle to his real world identity, they used writing style analysis to confirm that it is him. Monero likely would not have stopped this.

With Monero to deanonymize him they would just have had to make a "donation" themselves. Then once their output was spent in the chain they would just follow where it was sent to. 

Given how monero works, there would likely be a bunch of dummy spends of that output but at least one of them would have linked to local bitcoins. 

And since local bitcoins is a regulated institution they would be able to get their receiving addresses. All they had to do was follow the path of each spend (including the dummy spends) and eventually one of them would end up a local bitcoins. Perform the same writing style analysis on the account and you've got your perp. 

To my knowledge zcash is the only one that could have prevented it. Not Monero.
Buy a plan with a larger (or no) limit. Not like they don't exist. 
I agree. Though once the value transfer nut is cracked you could implement any montary policy you want. Bitcoin happened to get it right because Satoshi knew what he was doing. You could contrast that with the couple Core devs that created Freicoin with a crankish left-wing inspired monetary policy (calling interest usary, etc)
Litecoin literally has no purpose. The Litecoin roadmap *is* the Core roadmap. The whole plan is to just merge in commits from Core and that's it. 

If you like the Core roadmap, then buy Bitcoin. If you don't like the Core roadmap, then you wont like Litecoin. 

There literally is no point to Litecoin. I can only conclude that its $12B market cap comes from people who have no clue what they hell they are investing in.
It will take quite a while to reach that level. If tx volume *doubles* every year you wont get there until 2031. 
Litecoin's whole roadmap is to just merge in commits from Core. That mean's its roadmap is identical to bitcoin and will suffer from all the same issues. If you like the idea of a coin with transaction fees that are so high that you need to use centralized custodial wallets to transact, then by all means support litecoin.

Bitcoin Cash is taking a different path and the developers are committed to doing the hard work needed to optimize the software and protocol for on chain scaling. 
Whoever built that can you make it translate testnet addresses as well?
I asked Jimmy Song this exact question. Namely doesn't an asset that's only a store of value and has no underlying utility require an perpetually increasing number of investors to be sustainable? 

I forget his exact words but it was something like, "I don't believe current economists are correct and we're going to see new economic theory come out of this". 

Doesn't really inspire confidence that armchair economists are directing bitcoin based on some "new" economic theory. 
I'd like to see this too. It's dirt cheap to validate and adds some interesting functionality.
I'm not even implying a conspiracy theory. Nearly every dev on that side is out there telling people 1mb is more than enough space once lightning comes out and everyone is using it. 

But that obviously isn't true if lightning is nothing more than a micropayment system. 

I think a micropayment system is a great idea. And lightning might very well work well for that use case. But I've been saying from day one that it seems like it's a very low probability that it will be viable for everyday payments (which aren't micropayments). But this hasn't stopped anyone from misleading the community overselling the ability of lightning to scale bitcoin. 
I think it's primarily due to them having built a business around selling offchain solutions like Liquid, which will have very little actual demand if Bitcoin manages to scale. 

Adam is literally fighting for his job. If Bitcoin scales he will almost certainly get canned as CEO for not being able to deliver any profit. 
Wouldn't be surprised if scumbag /u/bashco was the one frantically switching between 30 sock pockets. 
Fwiw this was an alpha version he is reviewing. And a large part of the reason some listings and purchases didn't work is because, being alpha, we are still making breaking changes. 

And the caching on the network works too good so listings which used an old format or old version continue to persist.

Beta version should be out this week and we expect there wont be any more breaking changes. 
> When trading off between risk A and risk B take the middle road.

Vitalik reads my mind.
**based on my perception. not anything official. 
No he was referring to only lite clients which don't do validation. And even there he forgot about the new difficulty adjustment rule which would prevent bitcoin lite clients from following the bitcoin cash chain. 
What a scam
That "research" paper you linked to is an embarrassment and it's frankly shocking it even passed peer review. In addition to using unreliable phone interviews (which produced so many anomalous results the data was obviously useless), they didn't even ask the relevant question... how many *labor hours* were worked. Instead they asked how many employees do they have, effectively rendering the study useless. 

When the actual payroll data for was evaluated later, guess what... job losses. 

Most of this "new minimum wage" research, if not an outright embarrassment like Card and Krueger, has to add controversial additional controls to already controlled experiments to get the reported results. 

In other words, when you use standard models, you get the expected results. So they scrap the standard models and make additional corrections and guess what... no job losses!

Nothing to see there!
Going back to when segwit was announced it was clear there wasn't anywhere close to consensus for it as a softfork. At the time they controlled like 99% of the hashrate so they thought they could just ram it home over the opposition. Which is a terrible mentality btw. 

But I got the impression that they felt that segwit was going to be the only game in town (which it mostly is) and that pressure would build to simply "do something". 

I think that prediction has mostly come true as we see people switching sides and saying segwit should be activated just so "something" happens. It doesn't say much about the quality of the tech, but it does show that their strategy worked. 

That plus the extreme manipulation of the narrative on r/bitcoin and elsewhere I think is what has contributed to the increasing support. 
Satoshi envisioned storing the block's merkle tree on disk. Transactions with spent outputs could be deleted and the relevant branch of the tree deleted as well. To bootstrap a new node you only need to send them the txs with unspent outputs and the merkle paths. 

This was never implemented. 
Right now... if I got to a faucet and send coins to my testnet wallet, I get three separate transactions coming into the wallet. The testnet is really messed up. 
"A peer-to-peer speculative investment asset" or so /r/bitcoin tells us
Cognitive dissonance != fud
Lol he recommends eliminating what little competition exists between national governments on the tax and regulatory front and replacing it with top down rules. Yeah that will help. 
Also what the hell do you do with multisig?

In OpenBazaar we send a partially signed tx from the seller to the buyer. Days later when the product is received the buyer signs and  broadcasts. But what if there is a massive "fee event" in the meantime? Then the damn tx might never confirm. And it's a massive PIA to try to recollect the signatures because the parties may never come back online. 

So we end up overpaying on all transactions to guard against the damn "fee event" but it doesn't actually guard against it as fees could easily spike above what we are paying.

Worst UX imaginable.
> It's a centralized marketplace just like ebay

Well that's not exactly true, ebay runs servers owned by one company. The content you see in OB is pulled from many nodes across the network who are free to join and leave at their discretion. Preliminary work has already been started on making store data more distributed such that listings, images, profiles, etc will be stored on and downloaded from multiple nodes. 

> the company specifically coded it to not allow TOR use

That's not true either. The network design decisions were made with NAT traversal in mind not preventing Tor usage. And Tor can still be integrated in the current design using onioncat. There's also been progress on integrating i2p as well. 

Not surprising when lobbyist for the banking industry wrote it. It shows how easily people are duped into believing legislation like this is for the "public good". 
>Only 21 million units can ever be issued, and a fixed money supply is incompatible with a growing economy. In a bitcoin-dominated economy, workers would have to accept pay cuts every year, and prices for goods would gradually fall. 

This is absolutely false. In the face of a fixed supply of money, only those goods and services that increase in supply will fall in price. 

Since the supply of labor tends to be fairly stable, so will be nominal wages.

*If* you want to argue that nominal wages will fall due to population growth, this will tend to only apply to starting wages. Most people tend to get raises as they become more productive and advance in their careers. At best population growth would slow the rate of raises for these people but it's doubtful it would put downward pressure on nominal wages.

So all around false claim.
The economics section is just terrible. Just four paragraphs and every one is negative. 

Instead of saying, "Rothbard believed X about the economy. Some view this as a great advance while other disagree."

They just say: "Rothbard believed X about the economy. So and so says people who think this way are part of a 'moronic cult'."
The plan was to have Craig claim he's Satoshi, patent everything and anything and then patent troll the shit out of everyone. 
Node operators would decide, not him. 
There's a chat room in the ABC slack but it's not that active. 
Thanks. Mobile should be out relatively soon. Like first quarter of next year at the latest. Ethereum might land around the same time. It's already fairly close to be functional. 
> However not even the multi-spender knows which transaction variant will be confirmed. That's Nakamoto Consensus in action. Pretty good.

There are two problems with this...

1) The zeroconf problem. Even to the extent we solve fast respends you still have the possibility that miners can accept bribes to include double spends. For the system to be secure you'd nearly 100% of miners refusing to accept bribes. That's not realistic. And if even a small percentage accept bribes (like 5%) then zeroconf doesn't work for anybody.

2) The network, as is, does not scale due to block propagation issues. Graphene, compact blocks, xthinner, and every other compression scheme work by relying on the mempools of network nodes to be in sync. If everyone has all the transactions in the block then your don't need to re-propagate those transactions. However, at scale mempools are not in sync. The network is being slammed by so many transactions that mempools become wildly out of sync. This is ultimately what the BU's gigablock testnet observed. When the average blocksize approached 100mb, the xthin protocol they were using completely broke down because mempools were so far out of sync. They literally were transmitting full 100mb blocks around the network. At a full 100mb block it takes longer than 10 minutes to propagate so the result was the blockchain forked into many forks and failed to converge. That's a total consensus failure. And the problem can be traced to lack of mempool synchronization. 

So preconsensus (particularly avalanche) solves both of these problems.

You get completely secure zeroconf and you can scale past 100mb blocks because it synchronizes the mempools. 

> ... which is definitely not in the whitepaper.

Who cares. Stop treating the whitepaper like a bible.

> Bottom line: In both cases (with or without pre-consensus protocol) there is no difference for the end user and there is no difference for miners except in edge cases.

Again this is false, see above. Without it zeroconf is insecure and the network can't scale past about 100mb. 
I'm happy to do one.
I've never liked the idea of being forced to use the money button wallet. We got a first hand experience why that is a terrible idea when Ryan unilaterally turned off BCH on his services and prevented people for withdrawing their money. Is that really how you'd want ALL internet payments to work?
I wrote out a bunch of paragraphs and then reddit screwed up and deleted it.

The bottom line is his proposal to query the mempool of miners is not that different from our proposal to relay double spend proofs. It has the same outcome except his is relying on massive mining centralization. In a healthy, decentralized mining network, it should not be possible to query the mempool of all the miners.

Moreover, he ignores the issue of miner bribes which Peter Rizen showed actually does work on the BCH network today. After the merchant has checked the miner mempool (in Wright's example) and approved the payment, and after the buyer has walked out of the store, then he sends the miners a double spend paying a high fee.

If he's buying a $4,000 television, he can send a $2,000 miner fee and get himself $2,000 discount on the television. One would hope miners would be honest enough to not accept the bribe, but even if only a small percentage of them do, say 10%, then it blows up zeroconf for everyone as double spends would work 10% of the time.

So you need a system where miners can orphan blocks that contain double spends. Wright doesn't mention this but in the past he's suggested just having individual miners deciding by themselves to orphan various blocks. That's a surefire way to cause chain splits and consensus failure.

What's actually need it a way for miners to come to a consensus (a *pre-consensus*) on what transactions are and are not double spends, so they can orphan blocks without causing chain splits and consensus failure. Hence why we are doing active research into avalanche.

&#x200B;
It's also not clear at this point that when failures and retransmissions are accounted for graphene will be more efficient than Toomim's xthinner. Real world data comparing the two would be nice. 
I would drop SV if it were me. And yes it would be a huge PIA to maintain compatibility as the rules diverge more. 
> busy at work behind the scenes

So the centralized coin controlled by a single entity does closed development. Why am I not surprised?
You'll probably see this in bchd by the end of the year. Half hour to full sync.
Checkpoints really don't introduce any additional trust in the system. When syncing a new node if you really want to be sure you're going to sync on the correct chain you need to either:

1) Validate the hardcoded genesis block against a known good copy or
2) Trust the devs hardcoded the correct genesis. 

All a checkpoint does is roll the genesis forward. So you need to either.

1) Validated the checkpoint against a known good copy of the header chain or
2) Trust the devs hardcoded the correct checkpoint. 

It's exactly the same security model. The only reason we don't do it typically is because a checkpoint creates a risk of consensus failure between non-upgraded and upgraded nodes if there's a deep reorg. But in this case that risk is tiny as eveyone will upgade since nobody wants to let craig wipe out their blocks. 
This comment deserves it's own post.
We have beer
Great post!
> deceitful

Of course there is nothing deceitful about it. No I didn't use AMP but if you think that's going to turn a 99% failure rate into a 99% success rate then I've got an ICO to pitch you. 
We need it because we don't want a currency that is usable one month and then unusable the next. 

It also really shows a lot about your intellect that you think that there are people out there who would "spam" the chain and pay hundreds of millions in transaction fees just to make a point. 
The question should be phrased the other way... will tx volume rise so fast that it will outstrip what is feasible for the network to process?

Only then would you get a rise in fees. By my calculations even if transaction volume doubles every year we're not likely to hit any bottlenecks for close to a decade. By then new scaling solutions will be available.
The biggest problem they have is they oversold it/lied to keep the blocksize at 1mb and now, surprise, the community now has unrealistic expectations about the technology. This post being a prime example. 
😂
In fairness there are quite a few edge cases where zeroconf could be double spent even without rbf. Bitcoin Cash still has these issues. It will take other innovations to strengthen zeroconf or make confirmations faster.

And also Core's version is technically opt-in meaning merchants could require confirmations if the transaction opts into rbf. The problem is there was a certain Core developer pushing non-opt-in rbf which seems to only have one purpose of maliciously facilitating double spends because he was insistent that miners will do this anyway so might as well write the code and push them to do it. 

There probably are miners running full (non-opt-in) rbf on the network today. 
Even the fee you posted prices out many use cases. Not the least of which is people using it in oppressive regimes like Venezuela. 

But beyond that the trend has been shooting upwards. Fees started the year at like 5 cents. In April they were *only* 50 cents. And just last week $5.30.
Yes. I wrote a blog post on this. This is absolutely correct. Store of value is always secondary. If there is no other value *except* as a store of value... by definition that's a ponzi scheme. 

https://chrispacia.wordpress.com/2016/12/27/can-bitcoin-exist-only-as-a-store-of-value/
that's the talking point but it's not true.
> lack evidence of an actual LN failure.

Normal intuition should tell you you're unlikely to find routes where ever hop has the needed value. Why there has been zero research done on this before deciding to go all in on it is beyond me. 

Either way, I suspect LN will be built eventually and when it's running we'll get to see how it works. 

If it works well, great! I'll make a blog post praising it and admitting that I underestimated it. 

If it doesn't work well then some people are going to have some serious explaining to do. 
Layering on the FUD
He has not said this many times. He has gone around telling people that an increase in the blocksize will make it so that only Google can run a node (which had always been a transparent attempt to manipulate public opinion).

That quote seems to be an admission that the decentralization arguments have always been shaky so now it's on fees. 
Why do you keep doing fundraising drives without seeing any kind of progress? Do you think just throwing money at them will make them get up off their asses?
I'm not a citizen. I'm just a person.   
Do they have any plans for multisig escrow or are they going to stick it out with the failed model?
At that level of demand surely we would have enough money to employ more developers and expedite scaling.
The signatures are about 11% smaller. Validation speed is almost the same. The big win will be down the road if we can introduce aggregate signatures so that we only need one signature per transactions rather than one per input. 
I think most of them are doing what they think is right. I do find the behavior of some of them to be fishy but I don't have any evidence of malice. 

I think the big problem I have with them is they seem to be repeating the mistakes of the past and saying "this time will be different". 

If you look at the gold standard you had a two layer system where the base layer (gold coins) was extremely decentralized but difficult/cumbersome/expensive to transact with. So a "layer 2" banking system was layered on top to facilitate transfers. This system proved to lack the property of resilience as the crony banks and government regulators found it very easy to control layer 2. Ultimately this lack of resilience is what paved the way for today's fiat dominated system. 

So the Core developers look at that history (or maybe they don't. who knows they might just be ignorant) and say "Yeah let's do that. Let's create a two layer system just like the gold standard". 

I see know reason to expect a different outcome. 

Bitcoin was always a peer to peer system from day one. And it held the promise of being resilient in the fact of adversaries trying to control it. That's the system I signed up for and what I hope to keep developing towards. 
For usability I think a stable price (or preferably a stable slow growing price) would be best. I'm not sure the media attention from wild price swings is worth making the tech less usable.
Six months is certainly aggressive. I'm not opposed to it but I would also be OK if it was less frequently. I also don't have any good ideas on the best way to introduce changes as it seems anything that is proposed will be opposed by at least someone or some small group. 

At the end of the day I think the only governance process that has a chance to work is everyone follows rule sets they like and don't follow ones they don't like. 


I'm a big fan of Go. In past years I've worked on projects in Java as well as Python and neither of them hold a candle to Go imo. 

At this point, unless I was going to work on an operating system or driver of some kind, I would pretty much use Go exclusively for any new project. 
> How do you envision the current universe of independent implementation development for BCH proceeding in the future (e.g. more or fewer teams a year from now, larger or smaller numbers of devs per team) and why?

It seems like there's probably a natural tendency to keep the number of implementations relatively low as it increases the number of contributors per implementation which is a good thing. More eyes on the code. 

So I don't know if you'd ever see more than 6-10 implementations for that reason. But that's just my guess. Hundreds don't seem likely to me. Of course if we end up blessed with a surplus of devs maybe more than 6-10 would be viable. 

But the counter argument is it's very, very difficult for multiple implementations to remain in consensus. There are literally thousands of little tiny bugs that could cause a chain split. So from an end user standpoint there's an incentive to use what everyone else is using so you are less likely to be forked off in the event of a chain split. 

So because of that you'll probably always see just a couple implementations with most of the market share. Unless all these other ones can demonstrate 100% compatibility and earn people's trust. 
The serialization format is here https://github.com/tomasvdw/bips/blob/master/ecmh-utxo-commitment-0.mediawiki
Yes. PLEASE go to /r/Bitcoincashsv. We don't want totalitarians who force centralized shitcoins onto people in this sub. 
> Without following the longest chain you have a dictatorship

You should fucking leave the community. This is about free association. Always had been. You and your merry band of statists are trying to centralize BCH into the hands of a psychopathic authoritarian and you have the fucking audacity to come in here and claim we're the ones trying to dictate the rules. 

Just fucking get out of here and go twittle around with some shitcon.

You are not welcome here.
This is a made up talking point by SV supports. Repeat the lie often enough and you muddy the waters. The people on that side are really despicable. Adopting blockstream tactics.
There is no reasonable objections to CDS other than Craig can't profit from licensing his patents. 

And the biggest objection to CTOR wasn't even technical. It was more about process and timing.
Yes. Though I don't know if he ever got it fully working. I've fully rebranded, ripped out segwit, and implemented all the forks. 
This explains why he is not concerned about centralizing BCH mining so much that it becomes trivial for the state to control it. Apparently he must think that's a legitimate state function.

Fwiw he also blocked me on Twitter though I never interacted with him there.
This is really as bad as Blockstream with the intentional lies and smears. 
> Didn't the Gigablock Initiative show that it's possible to process gigabyte blocks on the current hardware?

No it didn't. The software shit itself around 22mb blocks. With optimization (that hasn't been deployed in production) they were able to get up to about 100mb blocks before it shit itself again due to another bottleneck.
"I never lie" - Luke Jr.
So at the time I thought I remember the max block size was the issue. We've increased the max block size obviously but I'm pretty sure the max tx size is still 1MB. 

This would probably be a hardfork to increase it though I'm not sure I see any downsides given that quadratic hashing has been fixed. 
And here's the point by point reply. https://www.yours.org/content/reply-to-giacomo-2-d67e78a632ff

Seriously in the very first tweet Giacomo demonstrates that he's suffering from extreme irrationality. The inability to reason that people who take an opposing view from you are anything but malicious or stupid is a classic sign of irrationality.

So on tweet one anyone with with half a brain could have stopped reading knowing that nothing that follows is going to be of any quality. And somehow you guys eat it right up. 

Really says a lot about your intellect. 
Give this a read to see about exactly how limited scaling on chain is https://www.yours.org/content/can-bitcoin-cash-scale-on-chain--4c977e7218cb

As for lightning.. among many issues with it, the routing problem is probably the biggest. It's extremely unlikely it will ever work without a few centralized payment hubs (and even there it might not work since value cannot be multiplexed across channels). 

Currently on the LN on mainnet, your $20 payment will fail to find a route 75% of the time. Your $100 payment will fail to find a route 99% of the time. 

This is not only NOT a long term solution, it's not a solution at all. It fundamentally does not work as a payment solution. The only way to make it work is to make an onchain transaction opening a direct channel to every merchant you want to pay and treating it like a pre-paid giftcard system. 
I still can't get over one of the Core drones telling me that merchant adoption was "sooo 2013". 

2013 was awesome!
Big burn for the hodl mokeys
Of course he is. This was just a shitpost on his part which he tried to dress up and make intellectual sounding. 

And you're right, the hashing algorithm doesn't mean anything. The fact that nothing of consequence is using litecoin's hashing algorithm doesn't mean it wouldn't be screwed if enough GPU miners turned their hash power on litecoin. 
+/- 2% == "soaring"
I really doubt Naomi wants anything other than to see cryptocurrency advance liberty. She's has interviewed me regarding segwit2x fwiw and she's super genuine. 
His name is Drew Janiszyn on facebook btw. It looks like he changed his twitter profile to look like some anonymous hacker from turkey.
> (You mentioned that if the wallet is not familiar with the special address format then the payment goes >>/dev/null? That sounds bad!)

If the sender's wallet doesn't understand the address then it will just give the user an error when they try to send. The funds wouldn't move.
Agreed. Technical advancements are needed to make fast transactions secure. It would be great if lightning worked as advertised but right now it seems unlikely. 
And it has CORS turned on. smh
It will likely be in the next release. 
No I'm referring to the people who don't care that bitcoin isn't/can't be used as money and only care about bitcoin as a "store of value", which is basically a euphemism for a speculative trading asset. 

If you point out that bitcoin is rapidly become unusable as money they just turn their noses in the air and say "oh well I'm making money". 
Except users with nearly no economic activity don't matter for consensus. 
It's utility is decreasing by the day hence why that dominance is unlikely to remain. 
I kind of see three types of /r/bitcoiners

1) Made a lot of money holding bitcoin are are now comfortable with it being nothing more than a peer-to-peer speculative trading asset. This group has basically viewed this as a winning formula up to this point and view anything else as a threat to their holdings. 

2) Groupthinkers who lack any kind critical thinking ability and just regurgitate the propaganda. 

3) People trying the ole "create the problem, sell the solution" racket. 

In all three cases bitcoin only exists in a bastardized form where it falls far short of its revolutionary potential. 
The argument not mentioned is that if there are no non-mining full nodes and *if* miners form a 51% cartel, that cartel can change the rules of bitcoin, such as increasing the inflation rate.

*Economic* non-mining nodes serve as a check on a 51% cartel since they will reject invalid blocks. Since miners would have nowhere to spend their coins, presumably they would not try such an attack. 

This does not require *all* nodes be full nodes, but certainly economically important nodes should be. 
> who are the members of this "small group" who have "kick started the effort"? 

Engineers from the parties who signed the agreement.

> And who is "Justin" that is mentioned in the document?

Justin Langston from bitpay.

Alp showed up and took a big fat shit on the thread.
I was commenting on the first reddit post saying I think it's a mistake to do it as a softfork. Greg said "we'll just clean it up later". Which I took as more evidence that it was a mistake not to do it right from the start. 
> The opponents' argument is that it should be unneccessary, and that the standard bitcoin network should be able to handle these issues.

That's not really it. The argument, which OP and the top post make no mention of, is that we don't know how well it will work in practice. 

Will routing have a more centralized or decentralized topology? Will it reliably find routes? Will any node be able to pay any other random node 100% of the time? 90% of the time? 50% of the time? Will you be able to find routes when sending anything over pocket change, say $100? Given that the vast majority of users don't run (and will not be running) their own full nodes, is it an acceptable UX to require third parties (the "watchers") just to make their wallets work? 2 of 3 escrow cannot be done without all parties online 24/7, is this an acceptable UX for consumer applications?

None of these questions have been answered and probably wont be until LN is deployed. The opposition you speak of isn't about LN per se, but being forced to used LN as basically the only way to make payments. It seems irresponsible to force people on to a system with so many unknowns. 
OK first off there isn't anything centralized about OpenBazaar as is. It's basically Dark Market with a DHT added. And we all know how people act like dark market was some great achievement and OpenBazaar is a betrayal. 

Secondly, the 1.0 series served the purpose to get us the funding we need to build the app we always wanted. 

The version is the works is a hard break from the dark market design and is an order of magnitude better. Data is distributed across the network, stores can go offline and don't need to host anything. Nodes can listen on .onion addresses. Hell you can even use Bitcoin Core as your wallet if you want. 

So please, unless you've got some great software written by pb1x that justifies your holier than thou attitude then spare us.
Core will tell you that in the future nobody will be using SPV. Pretty big difference in visions. 
I don't understand the part about "waiting for Core to respond". They've already responded. They have no intention of increasing the blocksize and their focus 100% on the lightning network. That's why they're pushing SW and CSV. 
It's likely that people that never signed will move and partially offset those who don't.

You don't need to have signed to move to a state with more liberties and massive number of libertarians.
> It makes total sense if you consider their only objective to be: government proof wealth storage.

It still doesn't. Investors have bid the price up to where it is today speculating that bitcoin will be demanded in trade in the future. If that demand never materializes because the developers want a "store of wealth first and foremost" then ironically the value will plunge. 
Great rebuttal. Your points were very compelling.
> as an actual seldom used product.

They've blown up zeroconf txs and are in the process of jacking up fees. We're going to be forced to use this regardless of whether it sucks or is awesome. 
That's bullshit if you ask me. You can't tell me two masters and two PhDs aren't an impressive achievement. How many people on earth can make that claim?

> Doesn't mean squat

Seriously? 
> If we could reliably prevent double spending of unconfirmed transactions, we wouldn't need the block chain.

This is a straw man. Nobody has ever said Bitcoin reliably prevents double spends on zero conf. We have said the fruad rate remains well below that of credit cards and merchants are more than capable of evaluating risk.

As Voorhees put it:

> We accept thousands of zero-conf transactions. We don't need it to be impossible to double-spend, we just need to mitigate the risk sufficiently to where it can still make business sense. An occasional double-spend against us is worth the cost for the improved user experience.

Full RBF destroys a competitive advantage for Bitcoin. It's akin to slashing someone's tires because there's a tiny probability they will get in an accident if they drive. 

It's paternalistic and incredibly wrong headed.
Sam we could probably hard code a single invalid guid that can be used to browse while generating the real guid, but do nothing else. 
The pool sends the miner the block header, the merkle root of which represents the transactions the pool included in the block. So the pool sets which address receives the reward, not the miner.

At least that's how it usually works. Other less used protocols do things a little differently. 
My only objection to this is that getting involved in politics - especially in the United States - is mostly useless. Finding ways to opt out, like using bitcoin, is far more effective.
Why are they using the gay looking B that nobody wants?
Of course
Buttercoin has to be the worst brand name ever. 
> In capitalism, people are rewarded for their talents and efforts.

Not quite. People are rewarded for creating value for others. One can have all the talent in the world and fail if the talent is expended towards providing something people don't want. 

> Within capitalism, education would cost money

Again not necessarily. All the information you need to know is publicly available for free. Now you might say there are costs associated with teachers/school buildings etc, but that's just the cost for the delivery of that information not the information itself. We would expect a market for education to continually find more and more efficient ways to deliver information, presumably the price would get pretty close to the cost of the information - zero. 

> Would someone who is born into a poor family with no realty or capital, that has no natural talents, or exceptional intelligence, or means of affording education just be trapped into low-paying manual labor purely because of their genetics and their families lack of success?

It's what you make of it. History is replete with men who rose up out of abject poverty to accomplish great things. The more you serve other people, the better off you will be. I don't think anyone would be trapped. Consider just in January the Journal of Economic Growth published a study by economists who concluded that if not for the increase in government regulation over the past 60 years, median household income would be $330,000/yr. 

I think that is a low estimate since it only considers the impact of regulation. Factor in taxes and the trillions in wealth squandered by governments on services of little to no value (or negative value) and I think you can conclude the median income would be far higher than $330,000. Would people be condemned to impoverishment? I don't think so. 

edit: link http://www4.ncsu.edu/~jjseater/regulationandgrowth.pdf


>while unwriter and friends don't care and build the metanet

Good luck trying to replace the internet.   


And good luck trying to do so while tying your anchor to CSW.
Thanks for the feedback
This article was about Bayesian reasoning which requires you continually update the probability of something being true as new information comes in. Since this article was written quite of bit of new information that weighs heavily against CSW being Satoshi has come in while no information in his favor has. 

Thus, at the time I wrote the there was around a ~50% chance that he is Satoshi (and a 50% chance he was full of shit) but today that probability would need to be adjusted way down to reflect the new information. 

It's probably still around a 50% chance that he was involved in some limited capacity but <1% chance he is the inventor of Nakamoto consensus, author of the white paper, writer of the code, or the persona of Satoshi Nakamoto. 

In either case he lying and trying to take credit for something he didn't create. 

I will update the article accordingly.
> Is this avalanche explorer a node that participates in avalanche polling per u/Chris_Pacia's spec?

Yes

> If this node isn't determining pre-consensus but merely observing it, would it similarly send out 134+ queries (for each mempool transaction?) to a random network sample? 

The transactions are batched so it's at most one query per 10ms. It's not 134 x n transactions. If there are no transactions to work it sits idle. 


Who could've guessed
Actually neutrino has been running on BCH for months now. https://github.com/gcash/neutrino

I've also impoved it as well since it can detect an unconfirmed transaction rather than waiting until it confirms to detect the payment.
> the /r/bitcoin side became very pro-government/blue pill.

With the exception of a couple die hards I don't think most people in the BTC community are libertarians. I'm under the impression most of the Core devs are silicon valley-type leftists.  Similar to the type of people at the EFF but not remotely close to most libertarians. 
> From my interactions with Chris Pacia, the important aspect of Avalanche preconsensus isn't that it prevents the attacker from getting his double spend mined

I think that's backwards. That is the main part. The merchant should wait a few seconds after receiving a payment to scan for double spends. If the the attacker broadcasts a double spend while the merchant is still scanning the merchant should be able to detect it and decline the payment. If the attacker broadcasts the double spend after the merchant stops scanning then avalanche should prevent the double spend from being mined. Or at least that would be the goal. 
They weren't. He kept the private keys on his website and only encouraged people to back it up. I personally didn't back up my keys because the amount I had in the wallet wasn't worth my time. 

I actually copied down the seed when Ryan started going crazy because I suspected something like that might happen. And it did. 

Other people were not so lucky. My friend Naomi could not get her coins out of the site. 

Again basing the primary system for all online payments on this model is a horrible idea. 

Why people would trust his company at all after that is beyond me. 
>The leading austrian thinkers have been government friendly. They've been pro small government.

This is mostly false. Every austrian economist today (those with a PhD in economics) is an anarchist. I can't name one PhD that identifies as an Austrian that isn't also an anarchist.

It's true that Mises and Hayek (who are both dead) were not anarchists but Mises lived and died before the theory was even fully formed. And when Hayek was asked about the growing popularity of anarchism he said something like "If the idea were around when I was younger I'd probably be an anarchist today". 
It was over the second BSV failed to take over BCH. BSV is a centralized nchain product controlled by CSW. BCH remains a decentralized digital currency. 
There's a lot of development going on across a number of implementations. It never stopped. The price does what the price does. All we can do is write code. 
> Utter bullshit because he ran it on "simulated nodes" and nothing in real life.

Try again. Those we're real transactions on mainnet. 

> If someone is defrauding a merchant, then they go to jail.

And how are you going to do that? The double spender is in his car driving away by the time the double spend is sent. Are merchants expected to hunt down every person who defrauds them? More likely they just wont use Bitcoin(SV). 
Nodes that don't upgrade to the last version of the software end up forking themselves off the network. While this is necessarily true any time there is a hardfork, even if no hardfork takes place and nodes do not upgrade by the given date, they will still fork themselves off the network. 

As far as I know ABC is the only implementation that does this. 

It essentially requires ABC users to upgrade every 6 months regardless of whether there is a planned hardfork or not. 

It also means that replay protection is activated automatically if there is a "no fork" movement and the original chain stays alive. 
> He's a pragmatic person and will side with whatever side wins.

He's a dogmatic person who refuses to trust market participants and insists that miners must dictate to users what chain to use by attacking other ones. 
the only one that matters
6 is very unlikely to happen. If you were an exchange and the asshole just caused you to lose a lot of money would you reward him with the ticker symbol he covets?
CSW is trying to take over BCH and install himself as a dictator. 
It has never been "hash rate decides". Miners mine whatever is profitable. That means "users decide" not miners. 
Tom acts like the code he wrote all by himself and which nobody has reviewed is already capable being run in production and supporting a $10B currency. 
> It's not the case that non-mining nodes serve literally-zero-purpose-in-any-circumstance. 

This is true if you believe there is zero possibility of a 51% mining cartel ever forming an trying to change the consensus rules (such as by inflating supply). 

However, if this is a genuinely possibility then having economic actors fully validate their transactions can create a disincentive for miners to try this. The reason is because at the end of the day the miners need to sell their coins into the market. But if people reject their coins because they don't follow the rules then they'd just lose money by trying. 

We don't need everyone running nodes to have this effect, but certainly we would like most businesses to do so. 
A whopping $50/month gets you an unlimited plan. Apologies to the bernie bros who can't afford that. 
I'm fully prepared to publicly say I was wrong about the LN if the route failure rate falls down to a reasonable level and the other UX issues prove to be nbd. Again I'm not a dogmatist. 
If this is in a redeem script and you put junk in the other keys the script will fail due to it not matching the p2sh hash.
I think you're right. I was thinking of ANYOUTPUT. Names are too similar :p
>  BCH proponents are confusing medium of exchange which is when trading partners agree what money to denominate a transaction in, with method of payment which is how the liability (if any) resulting from a transaction is settled (if at all). 

No we aren't. You can have as sound of a base money as you want but if you don't have an incorruptible means of transferring it then it's worthless. Gold is literally as good of a base money as you can hope to have, yet it was a total disaster because the "layer 2" payment system for gold was easily corrupted by crony bankers and governments. 

The innovation behind bitcoin is that it can be transferred P2P without needing they very type of "layer 2" that you advocate. The innovation is NOT sound base money. The world had sound base money for thousands of years. The problem has always been the method of transfer. 

You will ruin Bitcoin if you focus on "sound base money" without having an incorruptible and censorship resistant method of transfer.  
Merchant adoption. One /r/bitcoiner told me last year that merchant adoption was "sooooo 2014". We need to pick up where we left off.
It's not on the same par given the history of slavery and discrimination. But people using the word still have malicious intent.
Yeah. I was mostly trying to highlight a fun situation. We definitely shouldn't bully people. Let them do what they want. It doesn't affect us. 
Yes, this was the public name used when talking to me on facebook. I don't know if frogo is the same person. Probably is unless he just picked up this lie from Drew. 
Maybe someone here can explain it to me but my understanding is that xCurrent works like the lightning network (only maybe better because every bank customer can send a payment over the "channel" not just a single user).

So if Alice at Bank A wants to pay Dad at Bank C, the payment can use a hash timelock contract to send the payment from Alice -> Bob -> Charlie -> Dan (or maybe more specifically in reverse just like the lightning network). 

The result is instant, cheap, atomic fiat transfers. How could adding XRP possibly improve this situation? Now it's Alice -> Bob -> XRP -> Charlie -> Dan. Both Bob and Charlie must now hold an incredibly volatile digital asset and expend the cost to hedge it. This hedging cost will have to built into transaction fees, making it more expensive then just using xCurrent and staying in fiat. 

So I don't see any way using XRP is an improvement. It doesn't increase the speed of the transfer, but it does increase the cost (risk). 

Is the entire strategy to just sell XRP to people who have no idea what's going on? 
None of the prices of any cryptocurrencies make any sense.
Have you looked at the technical community? It's 99% male. There's no way a company could put in a 50/50 policy.

If you think there aren't enough women in the technical community then propose some ways to get more women involved. But you can't make companies hire people who don't exist.  
So UTXO set size is *mostly* a function of the number of users, not transactions per se. 

Obviously if it gets too large then the memory cache wont be efficient enough and you'll need to read from an SSD. It's not clear if this will be an actual bottleneck or not. I know Peter R and the unlimited team plans to research this. 

But looking longer term, the UTXO set is one of the easier parts of the system to shard. Which means that nodes potentially wont need the full UTXO set in the future. 
I don't know how you can say Bitcoin is on the verge of failing in one tweet and then in the next one say you support the people who put it in that state.
They don't. They assume they are valid. Basically old nodes are transformed into non-fully validating nodes with reduced security anytime there is a softfork. 
That's a bullshit argument. Nobody goes around calling Ethereum Classic "eclassic" because it's easier. The fact is that the Ethereum community isn't made up of a bunch of whiny bitches who can't handle competition. They are confident in their vision for their coin and welcome the competition. The fact that there are these organized "bcash" trolling campaigns just demonstrates that you know deep down btc is an overpriced shitcoin and are scared to death of competition. 
I don't think we want to go that route. If we have to scale that large we will need to shard.
> what is wrong with your current money? 

Every year the central bank siphons off a portion of my income and distributes it to wealthy bankers via inflation. I really hate to quote Keynes but this quote from before he lost his mind is spot on.

> By a continuing process of inflation, governments can confiscate, secretly and unobserved, an important part of the wealth of their citizens.  By this method, they not only confiscate, but they confiscate arbitrarily; and, while the process impoverishes many, it actually enriches some.  The sight of this arbitrary rearrangement of riches strikes not only at security, but at confidence in the equity of the existing distribution of wealth.  Those to whom the system brings windfalls . . . become 'profiteers', who are the object of the hatred of the bourgeoisie, whom the inflationism has impoverished not less than the proletariat.  As the inflation proceeds . . . all permanent relations between debtors and creditors, which form the ultimate foundation of capitalism, become so utterly disordered as to be almost meaningless.

And not only do central banks enrich the bankers and wall street class at the expense of average people but it systematically causes asset bubbles which have the potential to take down the entire economy (see 2008 housing bubble).   

>  bitcoin is for international transfers, a tiny minority of fanatical users buying starbucks, and maybe an inflation hedge (for which store of value is a euphemism). 

If that's all bitcoin is ever used for then why even bother with it. I got into bitcoin to undermine the monetary system, not to fiddle around with large value remittances or create a new speculative trading asset for wall street. 
Correct. And if you looked at the order of transactions in Jihan's blocks it was no different than most other miners suggesting that he either 1) wasn't using ASICBOOST at all or 2) using it only very sparingly. 

Second. The fact that he twice agreed to run Segwit (and is currently doing so) also suggests that he was not profiting from ASICBOOST as you would have expected him to oppose Segwit at every turn if that was the case. 

/u/ArchReaper/ the fact is they decided to push Segwit before consulting with the rest of the community and without trying to build consensus around it. Since they were able to just push their previous softforks on the community they thought they would just do the same with Segwit. But it turned out that a sizable percentage of the community rejected Segwit (maybe as high as 30% overall but a much higher percentage of chinese rejected it). 

So they needed to conjure up boogyman to explain their failure and bad process and decided it was going to be Jihan. The (very likely) false narrative that Segwit was floundering because Jihan is profiting from ASCIBOOST was invented for malicious political purposes. 
Completely agree. Nearly zero research was done before deciding to go all in on it. 
At this point the topology of the lightning network is pure speculation. Intuition would probably tell you that a purely decentralized LN is unlikely to reliably route payments.
> Open Bazaar has a much larger problem with not securely supporting dark market traffic.

We don't explicitly support any type of commerce, but the new version has been working fine over Tor for months. I wrote the transport that not only enables OB over Tor but IPFS as well. https://github.com/OpenBazaar/go-onion-transport

> If they want fees resolved it is best they do everything they can to support segwit activation so LN channels can facilitate extremely low fees.

The number one piece of feedback that we got from version 1.0, which was build on the darkmarket codebase, was that users did not want to keep the app running 24/7 to sell things. This was my number one complaint as well and I was planning on rearchitecting it to move away from the darkmarket codebase since I joined the project. With the new version vendors can indeed receive sales while they are offline. But in this context LN would be a regression since, again, users have overwhelmingly said they don't want to keep their computer running all the time. 

> constant complaints about high fees hurting them doesn't add up

LN does not support multisig escrow, which is the only way to build a decentralized market that isn't riddled with scams. So right now it's $3+ to put bitcoin in the escrow and another $3 to release the funds. We're looking at $6-7 just to make a purchase. That's worse than eBay's take rate and I would argue if fees stay at this level or god forbid increase, then we have to move off bitcoin. 

But you guys just don't seem to care that real world use cases are dropping like flies due to your intransigence. 



There was a comment by peter todd a while back that I agree with. 

> I would much rather have the tradeoff be that the Bitcoin blockchain layer--the very lowest level--stays highly decentralized. It may get more expensive to use directly, but you counterbalance that by putting the **inevitable centralization at a higher level**.

Essentially, given the current state of the tech (and I would include LN in this) you have to pick your poison. It's either centralization at the blockchain layer or at the payment layer.

While *i* *guess* it would be better to have the centralization at the payment layer, both would be unacceptable imo. I know none of us, including the small blockers, got into this to send all our payments through large intermediaries. 

But personally I don't think we need to accept one or the other, there are advanced scaling techniques ― sharding for example, that allow us to have our cake and eat it too.. We just need more research, more testing, and more experimentation. 
However many people you think you have and however many coins you think they have, there is probably 100x more on the other side. 

You're free to believe what you want, but it doesn't make it the case. 

> while we reinvest in the UASF chain.

This is like a best case scenario for bitcoin. 

Also there is a difference between a service bit and a version bit. I would expect you to know that. 

That was more re-writing of history. I can't believe he tries to make it seem like he listened to the industry and users who all said they wanted a blocksize increase and lower fees and so they engineered segwit to make it happen.

The reality is segwit was designed from the beginning to enable lightning and nothing else. And they basically thrust it on the community. I still remember that conference where they announced it. It was basically like "this is what we're giving you, be happy". The first anyone even heard of it was when they announced they were going all in on it.
His re-writing of the history of the failure of the HK agreement is genuinely bizarre. It's like he doesn't think anyone was paying attention when it happened.
Code here: https://github.com/cpacia/ens-chrome-extension
This is why the number of nodes signaling UASF is a meaningless number. Anyone can spin up a node but it doesn't mean any economic activity is flowing through that node. There is no way to measure what percentage of bitcoin's economic activity is flowing through what nodes... which would be the only statistic that would matter for UASF activation. 

Just using raw node count is meaningless at best and the equivalent of a sybil attack at worst. 
I can't believe anyone would fall for this after what happened last time. 
He's up there as one of the most toxic people in the community. And that says a lot because there's quite a long list. 
As funny as this is I suspect there is at least some truth. The amount of brigading on here during the antbleed was unlike anything we've seen before or since. The amount of pro-core upvotes were like 50x-80x the norm then dropped right back down after the antbleed story was over. 
P2SH is working on bitcoin as well. That doesn't mean it isn't an ugly hack. Segwit is probably an order of magnitude uglier. 
If ethereum overtakes bitcoin in market cap I think that will be my cue to sell all my bitcoins. 

The bitcoin protocol is has none of the modern bells and whistles of other coins and the developers can't get over themselves to do anything other than tinker around the edges. Literally the only thing going for bitcoin is network effect. And if it looses that then there's no reason to hold the coin. 
I didn't get into bitcoin for a get rich quick scheme. I got into it because of its potential to undermine central banks (and commercial banks to a lesser extent). 

You guys seem hell bent on making sure it's only ever used as a speculative investment and never used as money. 

If I wanted that I'd go piddle in the stock market. 
> The fee on a gold trade is easily over $50, often times over $100 compared to the spot equity price.

This is the fee paid to the dealer... which you still have to pay to bitcoin exchanges. Bitcoin fees are on top of that. 
If the deal is you "I will sign a message to you under the condition you don't make a copy" are you going to copy it anyway and publically release it demonstrate to everyone you aren't a man of your word?


I believe the exact quote is "Satoshi was clueless about a lot of things."
A bearer asset that has no use value has a price of exactly $0.
There are plenty of people who pull off attacks just because they can. They don't get anything from it, they just do it to be an asshole. 

You can bet that will happen here.

They only way to stop it is to require the buyer to pay extra over and above the amount of the goods being purchased and refund it to him after confirmation. 

Which is an extremely ugly user experience and a good way to prevent mainstream adoption.
What you are referring to is merge avoidance. http://www.coindesk.com/merge-avoidance-privacy-bitcoin/
Think about what you're asking. For the government to use it's coercive powers to pick winners and losers. How do you think that will turn out?
Figured out what the author's problem is: 

He says bitcoin doesn't offer any benefits to merchants because people can already use "FaceCash" (which nobody has ever heard of). 

At the bottom it says the author is the founder of FaceCash. 

This shit shouldn't be taken seriously. 
Author is an ignoramus. 
> All that's needed is sufficient levels of decentralisation to provide censorship resistance and protocol stability.

That certain requires more than just a couple facebook scale firms.
But not for senators right?
> It just has a different governing model to BCH and Core.

Lol I'd say. Single company run but a narcissistic manic dictates the protocol.
He's been saying he will release things for years and nothing has come out. He is a known and proven liar.
> Does it allow everyone to participate in Avalanche preconsensus or only a set of recent block producers?

The primary purpose right now is to just collect data. So anyone will be able to participate but it wont offer any sybil resistance or affect the consensus rules. 

> Is it your intention that Avalanche messaging will be transmitted across the existing BCH gossip network?

Avalanche messages are transmitted between peers just like all other messages. They aren't relayed though if that's what you're asking. 
The concern is that miners would willfully choose not to mine the first seen transaction. While most miners might not do so, all it takes is a small percentage doing so and then it ruins zeroconf for everyone. 

There are two ways avalanche could be done:

- Only use avalanche when there's a double spend. This is the easiest to implement and would use minimal network resources, but it leaves open the possibility that a *majority* or miners could still choose to use RBF and then everyone has to use it. 

- Use avalanche on all transactions. This is more complicated and uses more bandwidth but as long as a merchant waits long enough so that the transaction has propagated to the miners and they have finished avalanche before accepting the transaction, then no subsequent double spends can be mined. 
Thanks. I said in another comment that The Machinery of Freedom was probably the one book that converted me to anarchism. I also like The Problem of Political Authority quite a bit. 
I think the push for shorter block times is coming from the Chinese community. From what I heard all the people in the Chinese community that were opposed to reducing the block times left for BSV and the remainder are all in favor of it. 
This is interesting. Looks like better compression at the expense of longer decoding time?
I picked up on that too. Very bizarre as he sounds like he's never heard of BIP70 and is pretending like this is an original idea of his. 
Please do stress tests on the test network and not mainnet. That's what it's there for.
:(
As someone else said previously.. a stable protocol does not necessarily mean a stable currency. We want a stable currency. You saw what happened to BTC when the protocol remained stable.. the currency shit itself. 

The point is to anticipate future bottlenecks and fix them now while the ecosystem is small and it's easy to do that. If  you wait until you're at visa scale or larger to fix bottlenecks you'll guarantee you wont have a stable currency.
The max blocksize right now is 32 and we're not even coming close to filling that. 

By the time we see blocks in the hundreds of mb mobile networks will likely have more bandwidth.
You really think I care about his personality? I care that he's attempting a hostile takeover of our network and trying to turn it into a centralized shitcoin under his control. 
That's exactly what it is. The doers who created BCH and continue driving it forward are on the ABC side. CSW has done nothing but talk and delivered absolutely zero. Not a single one of the alleged technologies he's said he's working on have been brought to market. Not one. 
> Why would miners care if SV wins and becomes the main chain?

Because it's 100% controlled by a psychopathic authoritarian hell bent being the coin's dictator. That is not good for the long term prospects for the value of BCH.
My bchd node didn't crash :)
This is so obvious to everyone except SV supporters. 
It's an erroneous comment. Nodes don't accept any longest chain, they only accept the longest chain that is valid according to their rule set. Hence you can't use nakamoto consensus to resolve disagreements over the rule set. 
>  I don't think SPV nodes should be affected.

They might be. Some code relies on scanning blocks in topological order for transactions.
While I agree that the Core devs are wrong, I'm not sure they're going to be ready to wave the white flag after we processed about 3MB average blocks.
Over how long of a time period did you run it to earn 31 cents?
Actually I did do it.. https://github.com/cpacia/spec/blob/gbt/getblocktemplate2.md

Seems like I was working on that around the same time he was. And note I'm not a full time bitcoin dev like he is. I can only work on this stuff in my spare time. 

I did share my (rough draft) with a couple miners who thought that it was promising. 
You are correct. And you don't even need to send it to different vendors. The second transaction could just send the funds back to your own wallet. 

We are currently working on enabling double spend relaying so that a merchant can detect if you do this and decline your payment. 

Once double spend relaying is in place the only other way to get your double spend to confirm would be to bribe a miner. 
/u/thezerg1 you mentioned you'd like to have better support for lite clients. Would you consider adding bip157-158 (client side filtering) into unlimited (even as a runtime option)? 

I've been trying to figure out how best to maximize privacy in lite clients for a while now and at this point I'm not sure we can do much better than those bips. The downside is it's more bandwidth than we'd like lite clients to use but it should still be feasible to run on a mobile phone even if the blocksizes goes up to 50-100mb. 

If we had full nodes that offered that, I'd build the mobile wallet. 
That post was very unfortunate imo and one of the main drivers behind the Core vision. 

I say unfortunate because the system Hal described existed in the form of the gold standard, at least for a very brief period of time, but it was so centralized that the government found it trivial to regulate and did indeed regulate it out of existence. 

Not only did they regulate it out of existence, but their regulations caused so much instability that they "convinced" the vast majority that a free market banking system is too unstable to exist and government run money and banking is the only way to fix the problem. 

There's no reason to think that recreating the banking system on top of Bitcoin will have any different outcome. 

The main benefit of Bitcoin is that eliminates the need to use the type of intermediary institutions that Hal described and which undermined the gold standard. 
To the extent Moore's Law will end for integrated circuits there will always be new paradigms. It's like that we every technology. Once we exhaust what we can do with current tech it incentivizes people to create new tech to keep marching forward. Progress will not stop. At best the switch from one paradigm to another slows things down slightly but that's it. 
Your intent in using the word BCASH is to insult, mock, and denigrate. Nobody believes you say it "because it's shorter". 
This is my comment on his video:

> You can say not FUD but this doesn't really make it so. On Bitcoin (BTC) you have 3 pool operators that can band together and do the exact same thing that you're suggesting 1 pool can do with BCH. Does the requirement to have three people colluding rather than one make Bitcoin THAT much more secure? Of course not. 

> There has been this concern with Bitcoin really going all the way back to the start of pooled mining. And in fact at times in Bitcoin's history a single pool has controlled a majority of the hash rate. So why wasn't this attack done on BTC? For the most part the pools don't control the hash power and they are free to switch to other pools if they turn malicious. Sure such an attack has a slightly larger chance of success with BCH rather than BTC but the difference isn't worth making a video about and claiming some grand insight.﻿
It uses elliptic curve diffie helman combined with bip32 to derive the key. As far as how the recipient knows it's for him but nobody else does, he does the same ecdh operation that the sender does and if he gets the same address it's for him. 

For the purpose of flagging the tx for download by the lite client it uses a form of prefix filtering. 
I was just about to ask this. I wonder if there is a measure of the probability of getting different size payments through from one random node to another. I'd be very interested in that data. I suppose the data exists but I'm not sure how to get at it without writing a bunch of custom code. 
Often if you quit a company without making to a certain point you don't get to keep your shares.
It's not a consensus parameter. Miners can always patch their code to run it regardless of what everyone else wants. 

This was the whole point that people like Peter Todd made .. they believed that miners *would* patch their code since they would earn higher fees from doing so. 

Many people objected saying that was very short sighted and unlikely. But the fact remains that a single mining pool doing so basically blows up zeroconf. 

We need something better. Can't rely on every single miner to do the right thing. 
So it turns out that the primary scaling bottleneck is the Core software itself. For the most part it's a single threaded application. So the primary plan is to optimize the software which will go a long way to getting where we want to be.

With optimizations a standard laptop on a home internet connection can process about 100mb blocks. If not more. 

By the time we actually exhaust the resources on a standard laptop (which will probably be years from now), we expect more advanced scaling solutions, like sharding, to be maturing. 
> reducing the barrier to entry to run your own node 

You don't need to run your own node. Nobody has ever been defrauded by using an spv wallet. Full nodes really only serve one purpose ― to disincentive any miner collusion. That's it. But average users don't need to run them to get the desired effect. Large transactions creators (think businesses) are probably sufficient for the purpose. 

> and in-so-doing making the network more robust

The utility of the network is falling day by day. Bitcoin has even lost its original use case of darknet purchases. They all prefer monero now. The more fees rise and the more unpredictable confirmation times become, the more people switch to alternatives. 

As I write this I'm waiting over 3 hours now for a purchase I made on OpenBazaar to confirm. I paid $1.62 fee. This is a horrible UX and almost certainly you'll be adding OpenBazaar to the list of apps that have dropped bitcoin if there aren't fundamental changes. 

We have reached a point in bitcoin where literally the only thing it's used for is speculative trading. And most people in /r/bitcoin mock the idea of using it for anything but that. 

It's a really sad state of affairs. I still own a ton of bitcoin, mostly to try to profit from people's irrationality, but I've written it off in terms of it ever being useful money. 




All licenses are government licenses. You need the state to enforce it. 
In fairness they do have a vague notion of using a DHT style overlay after the gossip protocol hits a scaling bottleneck. But it does show just how far off they are from having a working product. It sounds like they haven't even solved all the issues with the basic gossip protocol and haven't even started working on something more scalable. 

And I'm concerned that even with perfect information routes simply might not be available to make payments. With only a partial view of the network topology that might be even worse. 
You know the best way to prove the lightning network works? Build it and let people judge for themselves. 
Every video I see of Greg he seems like a really nice guy. It's completely different from his online persona. 
Keep in mind this is still probably alpha quality
Why was there non-zero fees in each block for the years before the 1mb limit was hit? 

For that matter why is it that every other altcoin has positive fees? I just looked at litecoin and the last block has 0.524 LTC in fees despite only being only 34869 bytes. 
Nodes by themselves aren't what prevents this, it's economic nodes. If the miners revert the chain, steal the coins, then take those coins or the coins mined in those blocks to an exchange to cash out, the exchange will reject the transaction because it violates the new network rules. 
I'm pretty sure they know that's unworkable. That's why they're investing so much in liquid. 

The only way to transact when fees are $100 is to use a hosted wallet (and undermine almost all of the useful properties of Bitcoin).

And hosted wallet service A isn't going to want to pay $100 for you to send a payment to a customer of hosted wallet service B so they will send the payment over liquid where blockstream will take a cut of the transaction.

This is the business model.
Maybe I'm naive but I wouldn't expect sharing a comment you find interesting to count as brigading. It's also not clear to me, judging by the comments, that people followed the link and actually "brigaded" the post. 
It's clear you support something without fundamentally understanding how bitcoin works.
I would like to think this, but in practice when UASF doesn't go through, they will all just revert the patch in their code. We wont' actually get rid of them.
Just like a typical political organization. Since most businesses tend to assume that every one piece of user feedback represents 1000 users or more, astroturfing of this type is often effective at getting businesses to change their policy. Its a tried and true political tactic. Not surprising they're deploying it. 
Sure I'd be happy to collaborate. I don't think I have the time to work on a javascript implementation though unless there's a lite client library that's as easy to use as Go. 

Also, we'd have to use localstorage for the block headers, which I guess is ok, not sure. 
Yup, it's storing it as a packed []dns.RR. Though currently it's only parsing typeA records, but that could be easily extended to other record types. 
> has no products

I've heard their "liquid" side chain charges $5000 per user and they take a cut of every single transaction passing through that chain. Very strong incentive to make sure txs happen there and not on bitcoin.

Also, I'm not sure how many people know this but since they either can't or don't want to make sidechains work trustlessly, their plan is to put the private keys in boxs with thermite which will then explode if they are opened. That sounds like a bad joke, but I've heard it's very real. 
LOL
My opinion fwiw...

One piece of evidence in Craig's favor that very few seem to know about is that when the story broke in Wired and Gizmodo, Gizmodo actually went an interviewed Dave Kleinman's brother. His brother said that two years prior, when Kleinman died, Craig Wright told him that he and Klienman created Bitcoin.

Should you be inclined to say that the brother lied to Gizmodo, there is a random article in something like the nyt (I forget which one you have to google it) about Bitcoin where Klienman's dad says in the comments something like "I'm looking for information about my son creating bitcoin". The comment is timestampped like two years ago.

So if you insist that Craig is just an evil scammer... he must have started planting the seeds of his scam over two years ago. I know diehards will insist "Of course he did! He's that depraved!", but I would like to suggest the simpler answer is that he really is satoshi.

"If he was Satoshi then he would provide his signature". Well he allegedly did to Matonis and Gavin. To suggest that was all staged is to suggest that the hoax is even more elaborate and extreme.

"He HAS to provide a public signature". Why? He seems content the way he is. I'm not sure he owes it to anyone. 

Anyway. Occam's razor. Maybe not 100% probability he's Satoshi, but certainly the most likely explanation. 
That is what happens when you get viscerally attacked for having an opinion...  people tend to change their views so they aren't exiled. Same thing happens in politics. Branding your opponents as "backwards", "anti-science", "luddites", etc is a very effective strategy.
"They" in the OP are long time core supporters.
One of them told me buying this with bitcoin is "soooo 2014".
I'm skeptical that many upvotes wasn't a bot.
Lol project much?

> But I've heard the actual plans for the future version. More centralization

Go on, please tell us of what centralization you're referring to. 

As I'm sure most people reading this already know, that was just a made up line. 

As is the suggestion that I spread out of context quotes. 
I don't think it's possible. You're looking at over 80 bits that would need to be brute forced. Distributed.net has been working on brute forcing a 72 bit key for like 13 years now (with 122,236 participants) and are only through 4.2% of the keyspace (and they aren't checking the blockchain for spendable coins). 

http://stats.distributed.net/projects.php?project_id=8
Can you elaborate on this:

> After their tests, ACINQ discovered that the proposed Flare algorithm was able to find a payment route in about .5 seconds with a probability of 80 percent.

Was that 80% within .5 seconds (with the rest taking longer)? Or only an 80% success rate of finding routes?
The tldr; of this is by moving the transaction selection to after the block is mined you eliminate the advantage that larger blocks give to larger mining pools ―helping to make mining less centralized and eliminating the main barrier to on-chain scaling.

And as a byproduct you basically get instant confirmations. 
It needs to be a testnet address
They wouldn't make any more fees over the fss variety yet they would earn less reward due to reduced utility=lower price.
Read the gizmodo aritcle. They make a more compelling case. 
This exactly. Pure propaganda. The fact is the mem pool difference that have allowed double spending in the past could have been (and mostly are) accounted for with carefully written code since miners were still using first-seen as the base policy. 

Only by changing the base policy to RBF does zero conf txs become 100% insecure. 
I invited junesth into slack to discuss openbazaar and he decided all he was going to do when he got in there was troll. He remains the only person we blocked. He's just butthurt.
I'm not looking for donations, but I hope to have a wallet with all that and more by the end of the year. 

It's slow progress because it's basically just me working in my spare time and I needed to write a p2p [messaging protocol](http://www.bitcoinauthenticator.org/subspace/) (which isn't finished) first so that stealth transactions can be sent straight to the recipient (much more private) and coinjoin/shuffle can be done completely p2p. Plus I need to plug all (or at least most) of the [privacy leaks](https://github.com/cpacia/Bitcoinj-bloom-filter-patch) in the bitcoinj bloom filter implementation. 
^ Doing it right
Percentage of disposal income spent on food:
1958 - 15.1%
2011 - 5.7%

Source: http://www.ers.usda.gov/datafiles/Food_Expenditures/Food_Expenditures/table7.xls
This isn't nearly as bad as coinvalidation which is basically trying to make it illegal to use bitcoin unless you are "registered".

The hearn proposal is a slippery slope though.
Lol for a system nobody uses and basically has zero chance of ever being used.
There is a serious reading comprehension problem here. Which is why I have a hard time taking any of these people seriously. 

I was clearly referencing the gigablock testnet which showed there was an accept-to-mempool bottleneck at around 22mb. And another bottleneck due to block propagation at around 100mb. 

These bottlenecks are mostly software problems and not hardware problems. At the time of that tweet nobody had any software fixes to the mempool problem (BU had some unreleased code but that was it). 

I don't know why I need to repeat this over and over again. It is obvious what I was talking about and where I got my data from. Hence the extreme reading comprehension problem.

As far as whether BCH can handle 32mb blocks... ABC has not yet address the mempool issue to my knowledge. That would imply at miners running ABC would not be able to sustain more than 22mb into the mempool if there was >22mb of transactions broadcasted every 10 minutes. Which would suggest they wouldn't produce >22mb blocks sustained. They should still be able to validate a steady stream of 32mb blocks if some other client, like BU, produced them.

Now I don't know for sure that the ABC client can't handle more than 22mb into the mempool. The gigablock test was done with BU not ABC but I'm assuming the code is similar enough that it's probably the case.
For one, you can have so many orphan that the chain never converges. This stops the entire system from functioning.

You can't just say "well miners will decide how large of blocks to accept". How? If each miner is making its own decision which *valid* blocks to reject this will result in the chain splitting into many chains as all miners are rejecting a different set of blocks. 

You need a coordination mechanism if miners are going to reject blocks above a certain size. But what is this going to be? Picking up the phone and calling each other? How would not that imply a completely centralized network if that were even possible?

And any mechanism to have miners all decide on which sizes blocks to reject is ...gasp... a blocksize limit. 

> Once your transaction is in the mempool of all miners it's a done deal.... and it does not affect users.

Again. This should be bitcoin 101. The whole reason we have a blockchain is to coordinate between nodes which transaction came first. Different nodes see different transactions first. If someone broadcasts conflicting transactions to different miners... and if there's a reorg due to these giant blocks.. there's like a 50% probability the double spends will get mined in the reorg.
Think about it... imagine you get to facebook scale in terms of the media being put on chain. Could you even plausibly suggest the network is "decentralized" if you need facebook scale infrastructure to run?

They aren't building a decentralized system. They are building a centralized system of high powered servers which they want to pretend is decentralized. 
It's also the case that >99% of the population is convinced in the necessity of the state and will attempt to reconstitute the state at first opportunity. I don't expect this to always be the case. Just like there was a time where >99% believed in the necessity of a king but today nobody does. 
Very Nice!
Some circumstantial evidence this is correct.. I've blocked only about a dozen BSV people on twitter and my twitter notifications have mostly gone silent. So at least from my perspective the absolute number of CSW fan boys seems very low. 
> How is that reconciled with the original bch sentiment that diverting from the whitepaper is bad?

When I got into Bitcoin way back in the day one of the main questions that was asked repeatedly is "What happens if someone invents better algorithms or cryptocurrency designs? Wont that just make Bitcoin the Myspace of cryptocurrencies?"

And the answer was always, "We can always upgrade Bitcoin with state of the art technology if it proves to be useful. We aren't locked in to 2009 tech".

The answer was never "The whitepaper defines what must be Bitcoin for all time so Bitcoin will neveeeeer change. Also don't worry about it ever becoming the Myspace of cryptocurrencies because this one off paper from 2009 was so perfect nobody will ever invent anything better."

If the general sentiment among users was the latter, I would guess most people would have taken a pass on Bitcoin back in the day because that's an absurd statement. 
> What do you mean by "getting attacked"? Once you have a hash war, the rules are simple: the chain with the biggest hashrate wins. It's called Nakamoto consensus[1].

This is not how Nakamto Consensus works. This is how CSW wanted people to believe it works because he thought he was going to have majority hashrate, but it's not how it works. 
While nothing may be 100% decentralized BSV is 100% centralized.
In general I'm not optimistic it will be reliable enough to be a general payment system. And it has a very squirrely UX. But if it were to work out I'd think it would have a better chance on the BCH blockchain where fees are less than a penny to open a channel than on the BTC blockchain where the thought leaders say they can't wait for $1000 fees. 
The goal is to scale while remaining as decentralized as possible. One step at a time. 1gb blocks are within the realm of possibility given the optimizations we have available to us. Going beyond that and still keeping it decentralized requires more research and advanced technologies like sharding or zk-snarks to mature. 
The BSV talking point is "how it's unfair that the ABC would retain the BCH ticker after only putting a lot of hash on for a short period of time". 

But maybe someone on that side can explain why it would have been "fair" to give the ticker to a coin that obviously has an unsustainable of amount of hashrate? Craig and Calvin are doing the equivalent of lighting money on fire right now. It's obviously not sustainable and it's obvious BSV hashrate will have to go way day at some point. So the fact that they are willing to irrationally mine at a huge loss is what make ticker symbol distribution "fair"? 

 
You make it sound like everyone screwed over poor nchain. No mention of Craig trying to be dictator and run the network as if it was his property. THAT is why nobody will work with nchain.
Thank the BTC devs as well because they wrote 98% of the code. 
Apparently it was the same miner who mined both blocks. This suggests either a deliberate attack to wipe out their own transactions (which you can do when you control 100% of the hashrate) or just plain old incompetence. 
He's been saying this the entire time. Even today on twitter he's saying that if ABC side really wanted to win they should attack BSV. 

He cannot comprehend users actually having a choice. It's a shame but that's an incredibly authoritarian mindset and it's not surprising he's now bedfellows with the supreme authoritarian in this space ― Craig Wright. 
Lol they invited him on to have a chat. He immediately launches into this canned corporate political statement. Then just leaves. wft lol
Here's another dragons den account. 
Looks great!
> What is he talking about?

Presumably 51% attacking the network. In their world mining is so centralized that one or two miners can implement whatever policy they want. 
It's primarily the lack of a UTXO cache. Creating one is a high priority. There is an open PR but it has bugs in it.
I think they are saying XRP is misleadingly marketed not BCH
> and very little value in networks that are misleadingly marketed and not even required
for use within their own network (such as XRP)
BTW if you're using it with BCH you should upgrade as this release has a wallet change that's not backwards compatible. 
It has a lot of similarities. Namely, without actual usage, the only place demand can come from is speculators. And speculators will only invest if they think the price will go up, but again without users, the only thing that can make the price go up is a *perpetual influx of new speculators*. 

So at some point the later investors are left hodling the bag. Very similar to a ponzi. 
It's a custodial wallet for custodial wallets. 

The coinbases and bitpays of the world would be expected deposit a portion of their funds into a multisig address controled by (presumably) blockstream and others. 

The amount they deposited is tracked on the liquid ledger. 

Then whenever coinbase (or one of its customers) wants to pay bitpay (or anyone else) they make a liquid transaction which transfers funds from coinbase to the other party on the liquid ledger. 

If they ever want to get their money out of the multisig they need approval of the key holders. 
Not sure if it was created by core trolls or not, but they definitely promoted it in an attempt to say "see, these type of forks happen all the time. there's nothing special about bitcoin cash. just another fork". 

Of course the fact is that nobody was asking for Bitcoin Gold. There was no community split over changing the pow algo nor was there a four year long debate. For all practical purposes, Bitcoin could be said to have split in two when Bitcoin Cash forked. You can't really say the same about Bitcoin Gold as it was just a money grab and propaganda tool. Should have been valued at zero from the start IMO. 
Pretty sure the two in the front are Reina and Marianne. See no reason why the others wouldn't also be conference attendees. 
There was a chain split and community split in August. This is due 100% to the intransigence of the Core developers and their supporters. We tried everything possible to compromise to prevent a chain split and keep the community together. In the end their version of a compromise was "you give us everything we want, and we'll give you nothing you want". 

So there was no choice but to take the nuclear option and split the chain and community. There isn't just a single bitcoin now because of this. There are two (technically more than two but only two that matter). 

The Core side wants to whitewash this history and pretend like none of this happened. They want people to think that there wasn't a chain/community split and there aren't two versions of Bitcoin as a result. They want people to think Bitcoin Cash is just some random unrelated altcoin that is trying to leach off the Bitcoin brand. 

Hence they go around telling everyone this story and use the term Bcash because they don't want people to know it has any association with Bitcoin. This is all part of a massive social manipulation campaign on their part that began some three to four years ago and is still continuing to this day. 

It's all part of their strategy to try to get their way 100% and never have to compromise or admit they have behaved like shitlords. 
> If you receive a transaction your node downloads the entire dependency graph and runs the contract code (scripts) for every transaction

What do you do if people are using it for something like HFT and the dependency graph becomes enormous (like on the order of gigabytes)?
Wasn't BCH also added to Abra? Is this a fake news tweet?
::facepalm::
I a little confused as to how the wallet is currently functioning without the DAA.
I think I spent $2k with flight, hotel, ticket... also lost $500 in a poker tournament. 
Pretty awesome $10 u/tipper
must be an /r/bitcoiner
Because the total number of transactions made around the world is a finite number and to the extent CPU speeds and bandwidth could even be said to have a limit it's astronomically larger than what it would take to process all the world's transactions. 

Sure you can't process all the world's txs on a blockchain today. But in 100 years it will be trivial. Thus the claim it will never be possible is false. 
Yeah though it's a lot easier to implement. This took me a half hour. If I had to implement payment codes it would have taken much longer. As far as which uses more data, that would depend on how people use it. Hard to say without seeing them both deployed and measuring.
> In what way is it inefficient and expensive?

Uh if you have to do that every time you're in a situation we're describing you're making a lot of on chain payments (inefficient). Also not sure if you've been paying attention to fees over the past year (expensive). 

> It's amazing to me that you can have such strong opinions on Lightning without understanding extremely basic aspects of it.

If you think combining transactions (is this even implemented yet?) makes up for the poor UX and high cost of having to close a channel to pay someone you're pretty delusional. 
This is mostly correct. Miner support was at like 85%. Industry support was around the same percentage. Why didn't it happen? Because miners feared they would fork the chain and some people wouldn't accept their coins because they were running full nodes which fully validated the consensus rules. 

Same with businesses. Coinbases didn't want to be left holding the bag if their customers validated the blocksize. 
> When will OpenBazaar integrate the Lightening Network?

If it achieves a reasonable amount of adoption and if it's clear that the benefits (relative to just using other coins) exceed the costs of using it (which are pretty high) then it will be implemented. We definitely don't have resources to commit to something speculative at this time however. 
> exposes

Oh please. That link was nothing but diarrhea of the mouth. There's nothing of substance to even refute. I'm happy to have people read it to see what kind of low IQ drivel you guys are producing. 
The count is at 786 txs per block for the past 3hr. That's pretty close to an ath. 
The search engines don't really work on testnet so they were serving up mainnet listings that were not viewable on testnet. That's why there were stuck loading. 
Bip70 should be used as it has a number of advantages over straight payments. Not the least of which is your wallet provides a refund address as part of the transaction which is a UX improvement over having the user manually enter a refund address. 
Yes. We have plans to really beef up the discovery experience. There's going to be a channel feature which will bring more relevant content to the front and allow us to have the opportunity to feature vendors and products. 

And anyone will be able to curate a channel so the hope is that people will be able to create tailored browsing experiences. 
I'm not sure I'm following but I'm pretty sure if an htlc expires they don't have to close a channel. 
> If people do not run full nodes, only the most rich / powerful / die hard will run them, which leaves both transactions, code, and overall authority up to them. This breaks the entire model.

Had to stop reading here. Running a node just for some vague notion of "helping" the network, and not actually using it to receive payments, does zero to help the network. 

Even if you do use it to receive incoming payments but your incoming payments are tiny compared to those "rich, powerful corporations", then your node has a negligible impact on the network. 

It's obvious you fail to grasp this. 

Second, it will always be possible for the foreseeable future to run a Bitcoin cash node on a laptop on a home network connection. The only reason you think this isn't the case is because you've outsourced your critical thinking.
We've thought about it. Wolf even made some designs. 

I think ideally it would use atomic swaps but that kind of requires both sides to the trade have access to both blockchains. It's something that's on our radar. Maybe when we get more resources we'll take a deeper look. 
Yes it will run into the same problem with fees if and only if it gets popular. But I'd rather not have to deal with the same issues with btc all over again. If you want to scale you need a different roadmap from btc, which ltc does not have. Bch does tho.
> important not to increase the block size faster than average non-first-world computer speed and internet acces grow, or nodes would start dropping out of the network.

I don't see why that is important. To the extent that nodes matter at all it's because by having *some* economic actors full validate their transactions it discourages miners from trying to cartelize and change the consensus rules. But you don't need everyone, or even people in a giving geographical area, to run nodes to get that effect. 

People in the third world can just use SPV. Which to the extent they use Bitcoin at all they are surely already doing (or maybe just a trusted API). I seriously doubt anyone in the third world is running a full node today and bitcoin isn't harmed because of it.

As to malleability I believe third party malleability was fixed with the last hardforks. But even if I'm wrong, the need for monitoring is one of the uglier aspects of LN and another one of the reasons why I think LN will be hard pressed to see any substantial consumer adoption.
Bitcoin Cash will be added shortly. So will be verified mods. 

As far as the few bucks, I don't know what to tell you other than cross your fingers and hope the bitcoin fees go down. You have the keys to the money. We don't store any of that. 
There is a pretty strong consensus across the different development teams. They just had a meeting recently. Here's abc's roadmap for next year https://www.bitcoinabc.org/bitcoin-abc-medium-term-development
100x capacity isn't that far off. With software optimizations my home laptop could probably handle it. 
testnet
This is economic nonsense. The reason precious metals were used to store value is because 1) the were demand for their underlying use cases (jewelry etc) and 2) they were demanded as media of exchange.

Bitcoin has neither 1 nor 2. 
Tried it out and it was very underwhelming. Some of which is UX issues that I assume could be improved, but some of which is just technical issues that cannot be fixed. Here's my experience.

1) Download the LND app and fund it with testnet (not receiving initial acknowledgement of an unconfirmed payment due to the use of the committed bloom filters makes the app feel broken, but ok). 

2) I asked my coworker Sam to download and install as well. Let me try sending him a payment. He downloads and clicks `Generate Payment Request` and gives it to me. I paste it in, click `Send Payment` and get `Payment route failure`.

3) Ok maybe payment requests don't work like that. Maybe we need to open a channel first. So I go to `Create Channel` and it's asking for Sam's pubkey AND IP address. For my tastes having to deal with raw IPs is a really bad UX. I'm not sure why the app can't just use the pubkey to look up the IP in a DHT. This is how we handle it in OpenBazaar. So anyway, now Sam needs to look up his IP, but he's behind a VPN so now he needs to hassle with turning that off just to get me the information I need to open a channel. Finally he gets me the pubkey and IP and I put it into the app and click `Create Channel`. Nothing happens then after about 15 seconds or say it says `Deadline Exceeded`. So that didn't work. Maybe NAT traversal issues, but it didn't say anything else. But if it is NAT traversal, having to deal with that on top of all the other UX issues with the lightning network is going to create a huge hurdle for adoption.

4) So I go to yalls.org and try to pay for an article. I grab the payment request and paste it in and once again get `Payment route failure`. Again maybe because I don't have any channels open. And note that even if I had successfully managed to set up a channel with Sam and pay him. I would still get `Payment route failure` because Sam doesn't have any channels connecting to the person whose article I was trying to buy. In that case I would have to pay a big fee to close out my channel with Sam (and maybe wait the timeout period if he wasn't online when I tried to close the channel). And then pay a second fee to open a channel with the person on yalls.org. That's more on-chain transactions and more fees than if I just paid the bitcoin directly. 

5) So the payment request for this article didn't work, let me try the pubkey and IP. I click `Create Channel` and maybe about 10-15 seconds later I get a message suggesting that it worked. Something like `Channel Created` though I don't remember the exact text. But then I go to the `Channels` tab and there are no channels there. Is this because it's waiting for confirmation? No indication. I wait 10 more minutes for the next testnet block. It comes. The LND logs show it received a new block. Still no channel listed in the `Channels` tab. Oh well, let's try the payment request again. Copy and paste the payment request.... `Payment route failure`. 

So after about an hour of playing around I wasn't able to get anything working and was unsuccessful in paying the two people I tried to pay. 

Again some of this is just UX design/bugs that can be improved. But some isn't. Whether routing paths exist between the payer and payee depends entirely on how people use the network. In my case, even if I was able to open a channel with and pay Sam, I still would not have been able to pay for the article because of how *Sam* used the network.

For any technology to gain consumer acceptance and adoption it needs to work flawlessly and have a seamless UX. It's not clear at this point that it will ever be sufficient for generalized payments. 
There was always a cloud of rent seeking surrounding them. Their product is designed for "secure zeroconf" and it's a bit of a coincidence that it came to market around the same time Peter Todd started campaigning for RBF and doing things like stealing from Reddit to prove that zeroconf is insecure. 

They also got (paid?) Peter Todd to push checklocktimeverify into the protocol to improve their product. 
Deadalnix wrote about some simulations he did here. https://lists.linuxfoundation.org/pipermail/bitcoin-ml/2017-August/000136.html
You don't need to fork it. Just need to implement the wallet interface for bitcoin cash. https://github.com/OpenBazaar/openbazaar-go/blob/master/docs/altcoins.md

There is already a version of the spvwallet that works for bitcoin cash. It's really just a matter of putting the pieces together. 

The more difficult part of integrating an altcoin is in the client because they hardcoded the word "bitcoin" all over the place. But given that bitcoin cash is more or less bitcoin, you probably don't need to change it. 
The only thing it forced was hasty code to avoid any possibility of a chain split. Other than that, if the agreement didn't happen you guys would have just uninstalled your uasf software and gone back to core. It would have been a non-event. 
No it's precisely you who don't know how it works. There is a team. With meetings, emails, planning and collaboration. It's not just random people submitting random PRs... it shows a lot about your understanding of open source software that you think it is. 

If the existing team members refuse to collaborate with someone then that person will be frozen out of the meetings, planning, and emails. Not only will that person not be in any kind of position to collaborate but they will have no incentive to. Hence why so many people left bitcoin core after the regime change. 
That's not how development works. All projects have a team of people that collaborate together. The existing members of that team choose whether to collaborate with a new person or not. 

Perfect example is how they chose not to collaborate with people like Jeff and Gavin and they had to go off and do other stuff. 
There is a mobile app in the works.
What do you make of a well respected person like Ian Grigg saying he has first hand knowledge of a group of people, the leader of which was Craig, creating bitcoin?
I don't know if there are any charts, but I used the median fee reported by 21 https://bitcoinfees.21.co/ and multiply by the price. 

It's found under *Which fee should I use?*
Who holds 1mm in litecoin? ffs
/r/bitcoiners will tell you it doesn't matter because if you want to buy stuff you should be using dollars anyway.
I wrote a how-to guide to implement an altcoin https://github.com/OpenBazaar/openbazaar-go/blob/master/docs/altcoins.md
Yeah. Most of those test store's visible right now are not online. 
>  But there is no reason why banks couldn't run fractional reserve and create many times more BTC. 

I've spent a lot of time arguing with free bankers on this point and I tend to hold a fairly contrarian view. 

I deposit money (whatever is the current medium of exchange, say gold) in a bank and get back a fractional-reserve banknote.. I would argue that the banknote is *not* money but rather a form of a risk-bearing financial instrument. It's subject to a credit risk, because the people the bank lent the money to may default. It's also subject to a liquidity risk, obviously, because the bank can't pay out all claims at once. 

And I would also argue such banknotes would not be *fungible* either.  The risk to the investments of Bank A might be different from those of Bank B so it's unclear why the banknotes issued by both institutions should be fungible. 

So why would any merchant accept a non-fungible, risk-bearing financial instrument, denominated in gold, in place of gold proper? I don't think anyone would intentionally do so. 

Remember money is defined as the *most* liquid asset in an economy. But how could a risk-bearing financial instrument be *more* liquid than the underlying asset it's denominated in? It seems to be a logical impossibility. 

The economists I've argued with about this basically told me to stop asking these questions and just accept that, empirically, businesses did accept fractional-reserve banknotes as payment. 

But I find that to be an unacceptable answer. I think more plausibly is that early banks hawked their fractional reserve notes off as if they were backed 100%. People accepted them not knowing any better and the legal system let the banks do it and later codified the system into law. 

So I see bitcoin as a useful economic experiment in this regard. My suspicion is that, were someone to create fractional-reserve bitcoin note (or the electronic equivalent), that people would not accept it as payment in lieu of bitcoin proper. The fact that, thus far my prediction has been holding true, seems to suggest I'm right that fractional reserve money is more of a historical accident than anything that would normally emerge in a free market setting. 

Wrote more on this here... https://chrispacia.wordpress.com/2013/09/10/is-fractional-reserve-banking-compatible-with-bitcoin/

Segwit doesn't solve quadratic hashing btw since you can still make old style txs. 

I don't think flextrans explicitly addresses quadratic hashing but you could easily swap out the checksig op codes in a hardfork.
Flexible transactions would remove some technical debt. Segwit adds to it.
Except the original idea was that no cartel would be able to control 51% of the hashing power or effectively coordinate their actions to change the protocol rules. That premise has obviously proven to be incorrect and it has opened the bitcoin protocol up to be controlled by a tiny few. 

It's not a feature, it's a bug. And it's a bug that is being exploited for further centralized control over the system. 
Another soft-fork-at-all-costs bastardization of the protocol.
This is a pretty transparent attempt to spread fear of lightweight wallets to make people who don't know better say "OMG my bitcoins are insecure!? I better run a full node on my laptop then. And since my 2011 windows 7 laptop can barely handle it, I better make sure they never increase the blocksize". 
Could you elaborate on "extreme troubleshooting"? We were just at consensus in NY and every person we talked to (including many non-tech savvy people) said they had no problem installing it. 
> If you have a deflationary currency, people will just hoard money and banks won't loan anything because its just better for them to hoard it.

Needless to say not everyone (or even every economist) agree with this statement. I wrote an article specifically addressing it here: https://chrispacia.wordpress.com/2013/10/22/bitcoin-and-the-deflationary-spiral/

> There's a lot of people who say bring back the gold standard and lets make our currency deflationary, but from an economic standpoint it will do more harm than good.

Again, that's a contentious economic view. I give a rather non-biased macro counter-argument here: https://chrispacia.wordpress.com/2014/02/08/bitcoin-explained-like-youre-five-part-5-macroeconomics/
It's unbelievable how much Bernie supporters just stick their fingers in their ears and think about sunshine and lollipops when it comes to economics.  
So I was trying to remember where I heard that author's name before then I remembered he was in this debate on immigration: https://www.youtube.com/watch?v=EwRVNVT6xLY

Amazingly he agreed to debate on the pro-immigration side of the debate, then once the debate started he realized he never meant to take that position and changed it in mid-debate. 

Anyway someone who can't figure out the difference between a pro-immigration and anti-immigration position probably isn't in a position to give an opinion on bitcoin.
Obviously he's not breaking zeroconf by defrauding Reddit of $10. It's all his other actions. He has single handedly pushed RBF on the community. Has written patches and encouraged everyone to run them. Hard selling mining polls on these patches behind the scenes. And then finally getting them merged into Bitcoin Core over mass community opposition (Note it stops being "opt in" when blocks are full, which is why they were so happy to accept the "opt in" version).

That isn't increasing network resilience. It's reducing it's utility. 


> It would have been better for OpenBazaar to aim to have something basic and workable out as soon as possible with a clear roadmap for upgrading with new features over time than it is to wait for the whole thing to be finished.

That has been our goal from the beginning, I just think people assume we have more man power than we do. It's basically like 3 people writing code. We're close to having a workable release. Hopefully it wont be much longer. 
That should be stickied to r/bitcoin
The notion seems to imply that if we just keep the block size at 1 mb indefinitely that we can at least preserve the value of our coins. But our coins only have value because people are speculating on high future demand from people using it. If the block size stays stuck and 1 mb (and LN doesn't work as well as expected) that demand simply cannot materialize and will certainly cause the price to crash.
>  doesn't mean that some evil people are conspiring against you.

That's putting words in my mouth to say the least. Look I like the concept of the lightning network. I think Joseph and Thad are very smart and I like them personally. It's something that should definitely be built and I'm all for making changes to bitcoin to get the most efficient form, SW & CSV. I'm sure, at minimum, it will find use cases around the bitcoin economy. 

What I'm not for is prematurely deprecating core features of bitcoin, that have stood from it's inception to today, when we have not seen how the lightning network will work in practice. 

Will organic decentralized routing be feasible, or will there be pressure to use large centralized payment hubs? This isn't some crazy far fetched concern, it was basically the topic of Joseph's talk at scaling bitcoin which he admits would be pretty bad if it looks like that in practice. 

My comment above was not directed at LN but at the decisions of the core devs to go "all in" on LN without even seeing how it works in practice. 
Here we go again. This comes up almost every other day now and we've repeatedly said that using onename will be optional. It's only there to map the 20 byte hash to something more memorable. 

If you don't use onename, you're not not going to be foregoing a human readable store name for 20 random bytes. The store name will still be a string entered by you and can say whatever you want regardless of whether you use onename or not. 

Why onename? It isn't perfect, but at present there isn't anything else that lets you do lightweight queries. Downloading/verifying the full namecoin blockchain to do name resolution is not an option for openbazaar. Neither is purchasing a domain name as some other schemes require.

If you want to see us use namecoin submit some pull requests to namecoin to add committed utxo to the protocol so we don't need to download the full chain. Or go create a sidechain to do it. 

I see a lot of complaints about onename (who are actively working on improving the space) without offering any usable solutions. 
Yeah the fee market is the idea behind it. With full RBF it's more about the security of unconfirmed txs.
> I've spoken to two ATM operators with loses among other things... one of them the losses were in the thousands of dollars and the police had gotten involved.

There are situations where it doesn't make sense to accept zeroconf. That may be one of them. Why can't it just be left up to the merchant to decide what level of risk is right for them?

As it stands, this will make all brick and mortar transaction unfeasible and prevent merchants for deciding for themselves based on the known risks.
> Previous to the settlement of the new world, the elite had an effective monopoly over land ownership, forcing the lower classes lease lands from them under ridiculous circumstances.

That was feudalism which is far removed from lockean homesteading and ownership that most libertarians subscribe to. 
Thanks Yuri. I don't mind people playing around with it, just keep in mind that it's alpha software and you may lose coins. It shouldn't be considered stable atm. We will let you know when there's a more stable beta. 

https://www.bitcoinauthenticator.org/version-0-1-0-alpha-release/
https://github.com/cpacia/BitcoinAuthenticator
https://github.com/negedzuregal/BitcoinAuthWallet
Also a reminder that 2FA is not a be all end all for blockchain.info. The way that service works is it encrypts your wallet with your password only. 

2FA only stops the attacker from downloading the encrypted wallet from blockchain's servers. There are other ways an attacker could get your encrypted wallet. 
Here's a problem it solves ... If you want to pay me, rather than telling you my address is 1JxTsJtcf3pwGRJptkdui5BPnxKJWp84eq, I just tell you it's +chrispacia.

Much more human friendly. And you're verifying that is my real account because I'm telling it to you. Combine that with stealth addresses in the future and it'll be perfect.
You don't have to convince us that we are a long way from convincing most people to give up statism. 
Seem to me the news is worse than people seem to think

* All financial institutions are prohibited from having anything to do with bitcoin. Including creating bitcoin services and owning it in their portfolios. That cuts out an enormous part of potential future demand.

* It's pretty clear the government views bitcoin as a threat to the RMB. It seems they only want it traded as a commodity and that's it. They said explicitly that goods and services shouldn't be priced in BTC and it shouldn't be used to buy goods and services (it's unclear to me if this only applies to financial institutions but it's clear they intend to protect the RMB at all costs).

That was a very aggressive play. By relegating it to nothing by a commodity to be traded on an exchange then what good is it?

I'm really not a fan of the constant changing of the business models. 

Correct me if I'm wrong but wasn't this initially supposed to be a p2p exchange platform? Then a client-server open source exchange platform designed to help improve bitcoin exchanges. Now it's about remittances? An area where there are already a ton of people working. Are they next going to try to develop video games to mine bitcoin? 


That I never agreed with. There is a minimum relay fee (it's very low but it's still there) so transactions are not "free" thus there can't be unlimited demand.
At large utxos set sizes it can't fit in memory so you need to read and write to an SSD...which is a lot slower. Like all operations if this takes too long you can't keep up with the chain. 


Hey Andrew, this isn't a formal proposal and any final proposal may end up looking entirely different, but this is at least a start if you want to give it a look https://github.com/gcash/bchd/blob/avalanche/avalanche/spec.md
This was on localhost. So you'd need to add round trip time to that number. I wouldn't expect it to be more than 1 to 2 seconds. 
> They believe taxes are necessary and morally justifiable.

> they are.

LOL
Just for context, Hernzzzz owns a handful of BTC and he was promised he would be rich if he just supported crippling the mainnet and HODL. Since that hasn't happened he's incredible frustrated and he's using attacking BCH as a coping mechanism. 
> Because you can't know what is spent and what isn't, by design, you always end up with some ever growing datastructure somewhere.

To explain this.. in Bitcoin we have a database of all the unspent transaction outputs (UTXOs). This is the current set of spendable "coins". Whenever a transaction is processed the outputs of that transaction are added to the UTXO database and the inputs of the transaction are *removed*. Thus if we assume each user tends to only use n UTXOs, the database will tend to grow with the number of users. 

In monero, they don't know which UTXOs are spent given the way the system tries to obfuscate transactions. Thus you can't *remove* UTXOs from the UTXO database in monero and it grows with every transaction. That's really bad scalability.  

A couple years ago I did watch a presentation a PhD student gave where she said she was working on a solution to this design problem but I don't know if anything came of it.  
Imagine arguing that miners decide the consensus rules of Bitcoin then when miners activate segwit and enforce a 1mb limit arguing that's not Bitcoin. 

CSW's definition of Bitcoin is that it's whatever he says it is.
Maybe though you'd likely need to download the full utxo set. Not sure if that would be less data or not. 
The *state* of the system is the UTXO set, not the historical transactions. The historical transactions is only needed for calculating the current state, but once you have the current state you no longer need access to the history. 
Nakamoto Consensus is how nodes come to a consensus on which chain to select when confronted with two or more valid chains. It's not a voting system to determine the rule set. 
Yeah I do consider myself to be an anarchist. David Friedman's The Machinery of Freedom was the book that converted me. My influences are most of the main libertarian thinkers, Rothbard et all. I'm also influenced by Michael Huemer's views on philosophy. 
Thanks. Not too concerned with illegal products at the moment as that doesn't seem to be what people are using it for at this point in time. 

> Second, what do you think is the best not-yet-done idea that OB could service? 

This is kind of an area where we struggle because OB would work best for use cases that aren't legal. Finding legal ones or even a grey area with sufficient demand is difficult. 
By definition Bitcoin is a decentralized currency. BSV is 100% centralized and run exclusively for the profit of CSW. It has precisely zero claim to being Bitcoin. 
That works
>  Bitcoin can already scale much larger than that with existing hardware for a fraction of the cost.

15 million purchases is about 52mb blocks. So yes I do think Bitcoin can scale larger than that today. I don't know if I would say *much* larger but I guess it depends on how you define much. With some optimizations we can maybe get several times that amount without serious issue. 
> Pools run nodes. Individual miners do not.

We're going to be fixing this. Luke Jr botched the getblocktemplate spec years ago by producing something that was so unoptimized that it was unusable and slush's centralized mining pool algorithm ended up out competing it. 

We can and will make it so that individual miners can run their own nodes and the pool's only responsibility will be to distribute the reward. 
I'm a little torn on what their plan was. On the one hand, what you said makes the most sense. On the other CSW doesn't strike me as very smart and he may have thought he could just force people to follow his chain just by mining the longest chain. They also didn't attempt to mine a compatible chain as their first block contained OP_MUL (or at least they didn't publicly). 

In any case this is reason we dumped a bunch of hash on the network at the beginning was to prevent the very scenario you described and force them to fork off. 
> the miners of the last 100 blocks

I just picked the number 100 out of thin air without thinking about it. In retrospect I should have said something like 2048 or a much larger number.
Your block would get orphaned, yes. Anyone mining would need to be querying the set of 100 miners to figure out which txs are valid and invalid. Just only the set of 100 miners would be the ones voting. 
They we're literally wrong about every last prediction they make.
- There will be no split
- You can't have two chains without replay protection
- Only one chain will survive.
- ABC side will capitulate to raw hash power.
- Nakamoto Consensus applies across incompatible chains.
- There will be three coins.. abc, bsv, and "unified coin".

It was frankly embarrassing to watch. 
Sadly, if we can't muster a defense against the psychotic authoritarian asshole it might be the last time you can do that. 
> the only alternative 

That's not the only alternative. You're thinking like an authoritarian. It's not ABC or SV. You can use SV and I'll use ABC. 

I'm happy with that outcome. SV supporters are not. You want to attack me and take away my choice. I think you all are disgusting human beings. 
Another non-technical person embarrassing himself trying to make technical arguments. Hey dummy. The bottleneck (which only BU has fixed so far), is getting more than a **sustained** 22MB into the mempool. I've repeated this over and over and over and over and over again. It's sustained volume not a single block. You could probably mine a single 128MB block, but you'll never get sustained 128MB volume.   


The most sustained volume we've ever seen on the network is 13MB.   


So please don't make technical arguments when you haven't done any research and don't know what you're talking about. 
> It feels like you are trying to imply he was just sitting in the room in the corner, dick in hand, while others were creating it.

Given his technical incompetence and economic incompetence I'd bet that's what he was doing. 
They had it for a very short period of time and there were also a pool. They didn't own the hashrate like CSW does.
Because the BSV followers hold satoshis words as gospel but routinely take his words out of context to justify their economically and technically illiterate views. 
Full nodes only have one hardcoded set of consensus rules. The will not accept a longer chain if it is not valid according to their hardcoded rules. 

Users, merchants, exchanges, payment processors, all decide what node software to run. That may mean some run ABC and follow that rule set and some follow BSV. But nobody should force the other side to follow a rule set they don't want. 
This. Amazing how blind BSV trolls are.
*It will be the main implementation of BSV after the fork. Very few of us will be using it. 
And BSV mines BSV blocks. 
There was no disagreement on DSV. Only disapproval came from nchain because they have patents on an alternative. 
Take all the devs for all the non-C/C++ languages and they will all be able to pick up Go much faster than C/C++
Keep in mind Peter Todd and Greg Maxwell also pushed the same idea in the past. This isn't unique to just Luke Jr.
This is another one of the nonsense arguments that Craig has been repeating that has been picked up and blindly repeated by his followers. As you say the only way the hash rate could prevent a split is to 51% attack the other chain and mine empty blocks. 

Since Craig has been going around saying he will use his has rate to prevent a split one can only assume he plans to maliciously attack BCH.
If you know how Core's "highly effective QA process & team review" works it wouldn't give you anywhere near the amount of confidence that Andreas has there. Basically your average PR gets ACKed by only like three or four people. Most of them respond utACK (untested). It's actually a surprise to me that more bugs haven't got through. 

Again, not saying anyone else is better but to me it would not be lavishing the praise that Andreas does with such high confidence. 
This community needs to stop repeating this fallacy. It's 100% false. If it were true the BTC chain would have increased the blocksize since the vast majority of miners wanted it. 
This is a non-issue at 32mb. You don't need the entire utxo set in memory to process blocks. Even if you did miners almost certainly have enough memory to do so. 
He's making a poor comparison between tcp and http, which is just stuffed inside IP packets, to layer 2 bitcoin, which moves all transactions off onto a separate network with different (and inferior properties). 

In the case of tcp and http, they still use the underlying IP protocol. In the case of layer 2, they don't use the underlying bitcoin protocol. 

For a closer analogy tcp and http are more like putting data in OP_RETURN.

Also, IP scales just by sending more packets, again not by moving packets off onto a separate and inferior network. 
I used a library I wrote for signing plus some utilities from btcd. 

Here's the gist: https://gist.github.com/cpacia/69d3ad71508c63631a5b81afe530ba3a
> works

I downloaded the channel graph and wrote a bunch of code to calculate the probability of sending a payment to every other user (with an open channel) on the network. Simulating a random user sending a payment to another random user.

The results:

$5 payments: 52% route failure rate

$20 payments: 75% route failure rate

$100 payments: 99% route failure rate

I know the cognitive dissonance will prevent this from sinking in but it's still worth pointing out. 
Garbage wallet with garbage devs. Wouldn't care what they have to say. 

Also note that they claim Mike Hearn was "championing" black lists while his actual words were:

> I don't have any particular opinion on what we should talk about. I'm aware of the arguments for and against such a scheme. I'm interested in new insights or thoughts.
So just my first reading on this I think I like it more than than weakblocks. 

It has properties that are similar to bitcoin-ng in that the transactions can be signed and propagated as fast as latency allows. So a merchant will get the signal very quick compared to weakblocks where the merchant might need to wait 10-15 seconds. 

He doesn't mention it in the paper but I would imagine this could be used for high speed block relay by having a miner just say, "my block contains all the transactions signed by the leader (maybe plus a few extra)". This would probably require transactions to be chained or numbered so that you can know the exact set.
The subway system there is pretty ridiculous. One day I spent about a half hour trying to find an exit. Then when I finally found one it was some service exit in a back ally like a 15 minute walk from where I was trying to get to. 
What he's failing to realize is we had sound money (Gold) with a shit payment system ("layer 2" run by banks) and it was a complete flop. The banks used their position to lobby for more and more crony regulations and the government was more than happy to oblige them as long as it got a cut of the loot. 

The reason why Bitcoin is different is because we don't need a corruptible "layer 2" to transact with it. 

I honestly don't see the point of Bitcoin if we can't transfer it peer to peer. You're just setting the entire system up to fail just like the gold standard. 
I almost fell off my bed at that one. 
Here's another one that was discussed at the conference. Seems to be a consensus to do it. Just need to get a consensus around a spec. https://github.com/cpacia/spec/blob/master/double-spend-notifications.md
You don't currently trust your hardware? Is anyone reverse engineering their chips to make sure there's nothing funny in there?
The Boston Bitcoin Cash meetup is holding on in Portsmouth on Saturday. I'll be there. 
> Brian Hoffman went to the dark side a while ago.

The darkside is more fun. And we get donuts!
It's rather the opposite. Fees explode you guys pop the champagne. Fees come down because everyone stops using it and you guys celebrate. 
https://www.yours.org/content/colored-coins-in-bitcoin-cash-b26804e05964
I could tell you were not part of the meetings by your comment. If you were then your comment was just trolling (which I assume it is regardless). 
They are not behind closed doors. The links to the working groups are public on github.
OpenBazaar has an spv wallet in it
Yeah I think it's probably more likely to grow slower than that. Faster is a nice problem to have though. 
Personally I like paying 1 penny instead of $10 to transact on OpenBazaar. You call it a "fake" coin but it's working just fine. 
I'm pretty convinced that the reason people believe Bitcoin Cash is a "scam" or "can't scale" or is "centralized" is because the Bitcoin community engages in commie-sytle censorship and manipulation to keep a tiny handful of people in a monopoly dictatorial position over development and apparently most people don't have the critical thinking capability to combat it. 

I wrote this post the other day regarding the ability to scale on chain and how Bitcoin/Bitcoin Cash can do so while still remaining just as decentralized as it is today.

https://www.yours.org/content/can-bitcoin-cash-scale-on-chain--4c977e7218cb

Not surprisingly I was subjected to a lot of name calling ("You sound like an SJW!", etc) but not a single one even attempted to refute the arguments laid out. 

The community is full of massive cognitive dissonance. 
> Also, doesn't bigger block size mean more centralization?

This is really the big lie that has been told. It's true above a certain size but not at the blocksizes we're talking about. The primary reason you would have centralization is if it starts to become too expensive for people to run a node at a reasonable cost... for example on their home computer. 

But this is really a long way from happening. With some software optimizations, the laptop I'm typing this on can process upwards of 100mb blocks on my home internet connection. 

So ask yourself, if nobody has to rush out and buy a more expensive computer, how is this going to cause centralization? It wont. Most computers today have lots of excess capacity. 

And given Moore's law and Neilsen's law, capacity grows every year with technological progress. If you assume transaction volume will double every year, your still looking at like 7-10 years from now before any actual hardware bottlenecks are hit (for a home computer) and by then VISA scale will be within reach. 
It's hard to know what miners are actually doing. But there was an accidental fork a couple years ago that happened because miners were not validating the blocks they mined. 

The reason they were skipping validation is they figured they could get started mining on the new block faster if they skipped it and that could translate into slightly more blocks found and more money.

In practice it doesn't make sense to entirely skip validation. It's OK to start mining on a block without validating it as long as you validate it in the background and revert to the previous block if it turns out to be invalid. Skipping validation entirely does not maximize profits. 

But in general, yes having some % of the bitcoin economy validate blocks encourages miners to validate their blocks and follow the rules. 
> And if the answer is yes, which I so hope, what will the prefixes be?

The prefix starts with `bitcoincash:`

Example -> bitcoincash:qrjxh9kxp38mcvtslpdzn7rjrcmw2krxg5ku7rrk6h
Don't try to call it something it isn't either. Ethereum Classic is called ... Ethereum Classic not eclassic. 
Adam Back
It's probably a bit premature given that there's still a lot of research and optimization going on with them. 

There are also considerations of security and scalability. Security because if the crypto is broken (which we know a quantum computer can do) it will ruin the chain. And scalability because the transaction sizes are very large and slow to validate. 

I tend to like deadalnix's idea to create privacy extension blocks which can segregate these transactions from the main chain such that if the crypto blows up only the privacy utxo holders are left holding the bag and it doesn't destroy the main chain. You could also have a capacity limit on these transactions to limit the harm to scalability. It would just mean people would have to pay higher fees to use that transaction type. 
They came around. Earlier this year they were promoting UASF. 
We will want to wait and see how well LN works in the real world before committing development resources to it. 

LN also has some UX issues for us as you need to keep the app open 24/7 to receive payments and need to select a third party watcher. 
> Bcash is centralized and has a "CEO" 

You demonstrate your extreme lack of reading comprehension with this comment.
Another one that wants to pretend like fees weren't just $15 a few days ago. Waits till there's a lull to make this post.
Hopefully sometime around the new year bitcoin cash will be ready. Monero differs quite a bit from bitcoin so we're having discussions on what would need to change to integrate it.
Pretty sure LN hubs aren't actually part of their plan. It's to force everyone to use hosted wallets which use Liquid as the clearing system. 
Why file all these patents anyway? Do you support 'intellectual property'?
The 1 hour wait fee is current 98 cents bro. Not 5.
OpenBazaar uses SPV (I wrote it myself). 

And a also have a bitcoin cash fork of the same wallet. 
> subject to the following conditions:

>The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

I don't know how you read that any other way. 
I need to create a Mac developer account (and probably get a Mac). I can probably get someone to do it for me.
For the amount that you troll people who disagree with I really hope you're not as dense as you come across. 

None of the posts you link to show how a lightning payment can be made without either the recipient being online or without lite clients outsourcing the watching. 

Are you just trying to spread propaganda here and make it look like you know what you're talking about or is there some actual proposal to extend lightning in the way you suggested?
educate us bitusher.
Transaction selection is ultimately up to miners, but there was recently a patch merged into ABC that will allocate 5% of the block to high priority (free or low fee) transactions by default. 
I expect the value of BTC to decline once investors realize the utility is going up in smoke. Not sure how long the irrationality can continue, but it wont continue forever.... ROI must be delivered else people will sell. 
five minutes
That called cognitive dissonance. 
You got downvoted because you can't tell the difference between a fact and opinion. But given your username we didn't really expect more. Sorry for your handicap. 
OB1 :)
yes
"reject" is more appropriate than orphan.
You're going to need a bank to transact once fees hit $50-$100. 
Also see this excellent comment, must read: https://www.reddit.com/r/Bitcoin/comments/6f0ze9/the_majority_that_is_pushing_for_uasf_is_the/diewzl0/
How long until alice can pay bob from her mobile phone and not have to spend 20 minutes typing into a terminal?
Imo 100% consensus is not possible. The real question is if a large enough percentage of economic actors in the space support the change to make it work. Currently there are:
> 58 companies located in 22 countries

> 83.28% of hashing power

> 5.1 billion USD monthly on chain transaction volume

> 20.5 million bitcoin wallets

That's *most* of the economic actors. What's left seems to be a couple hundred disgruntled individuals at best. Given that you can't make everyone happy, those couple hundred are going to get the short end of the stick. 
> It is impossible to technically force a HF on users as they can always simply use nVersion=4 segwit2x code to use segwit and ignore the HF at a later date.

True. But you're forgetting that you guys are a tiny part of the community and account for a negligible amount of bitcoin's economic activity. 

You guys are free to ignore the hardfork, but it will only be a couple hundred of you who do so at best. 

> Keep in mind that BIP 141 is already mostly active(except activation) in over 96%( ~60k nodes) out their and thus not using BIP 91 for this proposal will simply cause nodes to unexpected-witness DOS ban as shown here -

The code does exactly what the UASF does and defines a new service bit to avoid DOS bans.
> Indeed, they apparently only invited people they expected to agree!

Luke stop.

Quote from Barry Silbert:

> I've sent invites to every Core dev I can contact and nobody will engage. 

Proof : http://i.imgur.com/sfaTEqv.png
I think they thought 1.7 MB by removing the witness was the compromise. Remember Luke (and probably the others) wants 300 kB so 1.7 seems enormous by their standards. 
New all time high median fee... .73 usd
Regardless if it's only one rogue hacker or not, the behavior is consistent with the rest of the shitty behavior exhibited by the majority of their community. 
It doesn't have to be (and shouldn't) mutually exclusive but like 95% of the shills in r/bitcoin are pitching it as the alternative to raising the blocksize. 
Do you not trust intel with your CPU?
You should have made an altcoin rather than trying to turn bitcoin into one. 
If we assume the goal is to maximize revenue to miners (which I don't think that should be the only consideration) then miners would want to set the block size at whatever size will maximize revenue. 

Frankly, we don't know what that size is because there's no market. Reducing the block size might increase the fee per transaction but reduce total fees. Correspondingly, increasing the block size might lower the fee per transaction but increase total fees. It all depends on the shape of the demand curve. 

Needless to say, there has been zero economic analysis or estimations of demand to see if the block size should go up or down. Instead we get top down central planning from people who are just guessing. 

I'm not sure the bitcoin unlimited approach is the correct way to handle it (maybe). But I'm pretty sure just keeping the block size at one megabyte until some central planning committee has a gut feeling that it's the correct time to raise the limit isn't the optimal approach. 
Lol I'm pretty proud of that one. He was trolling me incredibly hard and that got him to stop.
There is a definite sense among some small blockers that Bitcoin should just be a speculative investment asset that people just sit on and make one transfer every ten years. 

From this perspective it doesn't matter if Bitcoin can't be used as money or there isn't any good way to use it as a payment system.
Unconfirmed transactions have always been somewhat risky to accept (though must less so at lower values). 

My biggest problem with RBF was that it was deprecating somewhat useful functionality without any viable replacement on the horizon. 

I've always felt the answer to zeroconf would be technological, and in the past year we've seen bitcoin-ng and byzcoin, both of which seem to offer that technological solution (though neither are on the table). 
At some point someone is going to fork the miners off the network with a different PoW. My vote is for bitcoin-ng. Sirer is going to be talking about it tomorrow. 
Connections are encrypted, yes. But that only hides the content of communications not the ip addresses. You can use a VPN however. And in the future it will likely support Tor.
The main issue here is lightweight wallets don't have any good way of estimating fees. They can set the fees on a per byte basis but how do they know what fee to pay? Trust a third party api not to lie to you?Have users look at the 21 fee page every time before each transaction? Force users to run full nodes (which is to say force them off Bitcoin as that will never happen)?

All options are terrible. 


> I don't understand why new DNM's can pop up every day with a user interface that is so much better.

They are centralized services. It's 1000x more difficult to build when your requirement is to make it decentralized. 

We have plans to improve the UX that I think people will be very happy with when they come out, but that's probably not until later this year. 
> He described a situation where a miner or mining pool who is against the hard fork may broadcast their support for the change and then remove their support after the 28-day countdown to activation begins. 

That can happen with softforks too. In fact you can also get a permanent chain split if enough people do this in a softfork. 
Storage isn't really the bottleneck. The blockchain can mostly be pruned from disk and there are optimizations that can be made so that historical data isn't really needed. 
What part don't you like? https://www.youtube.com/watch?v=keGJgjsU5NQ
1) I never made it personal.

2) Aaron's argument illustrates the point exactly -- hardforks should never happen. Then 10 years down the road the protocol looks like swiss cheese. 
> While he was alive, Kleiman kept an aluminum-encased USB drive on his person at nearly all times. If there really is a cache of Kleiman’s bitcoins or anything else linking him to Satoshi, Paige said, “I guarantee that drive has some shit in it.” According to Paige, when Kleiman died, his brother, Ira Kleiman, took possession of it.

> Ira Kleiman declined to speak on the record about whether he is in possession of his brother’s hard drives. Described by acquaintances as guarded and private, Ira Kleiman also refused to meet with a reporter in person or speak over the phone, opting instead to send dozens of cagey and cryptic emails and SMS messages in an exchange that lasted several days. He claimed that after his brother’s death, Wright contacted him and told him that he and Dave Kleiman were involved in creating Bitcoin, and also alleged to possess documents provided to him by several sources that might corroborate the information provided to Gizmodo by Wright’s apparent hacker. 
It's not the instant part. It's the low fraud rate part. Chargebacks are a serious issue for brick and mortar merchants. The (historically) negligible fraud rate on zero conf txs represents an improvement over credit cards. 

That goes away with RBF unless this whole lightening thing pans out. Even then, it's a radically different Bitcoin than we have today.
> The first step to scaling by more than an order of magnitude is the Lightning network.

Serious question. Do you see any possible way the LN will have more payment hubs than we currently have nodes (or even more hubs than nodes at a large block size)?

If you're worried that $50/month to run a node will cause a dangerous degree of centralization than what does that say about payment hubs that will likely cost $100k to $1M a month to run?

There's this assumption going around that LN will be more decentralized at scale than the bitcoin network. But that's an unproven assumption (and one that there's good reason to be skeptical about). 

> It is apparent that one side of the issue foresees the future where we have a useful trustless currency while the other sees that 100MB blocks means 300x the users ASAP and hear "cha-ching".

It isn't even about the money. For me it's about undermining the power of central banks (and governments by extension). Bitcoin is no threat to existing political regimes if only 50k people use it or if it's nothing more than a black market currency that never sees more than $1M in trade. 

To realize it's potential for disruptive political change we need as many people using it as possible. 
> How to Steal My Money?

> There are two ways to steal my funds:

>    (1) guess my password - easier

I hope he used a 256 bit random key instead of a password.
> I anticipate there will never be more than 100K nodes, probably less.  It will reach an equilibrium where it's not worth it for more nodes to join in.  The rest will be lightweight clients, which could be millions.

> At equilibrium size, many nodes will be server farms with one or two network nodes that feed the rest of the farm over a LAN. ~ Satoshi Nakamoto
Bits is the best name IMO. I'll consider using it as the default in my app. 
Some dude literally said no one will want to use money that isn't backed by a military.
I thought he did a great job giving relatively non controversial answers without giving up ground.

His answer on deflation was great.
How about you read Huemer's work first then criticise. Criticizing from ignorance doesn't garner any respect.
>By sending bitcoins to Bob she [Eve] can link addresses together when Bob spends the bitcoins.

This is a little misleading. The whole idea is to get everyone to stop reusing addresses. If *everyone* only uses addresses only once how can Eve determine who Bob paid or who paid him? The addresses that link to her's in the block chain were all generated at the moment of the transaction and were never reused. How does she know who they belong to?

There are small exceptions like where Alice receives a payment from Eve, sends the same inputs to Bob and Bob sends the same inputs back to Eve. Then yes, Eve can make the connection.

But those situations are low probability events. 

This was the best summary of Bitcoin I've ever read.
Bitcoin has two (actually more) functions - a digital currency and a payment system.

Should bitcoin be widely adopted as a payment system (ie. Dollars-->bitcoin, send bitcoin, bitcoin-->dollars) it will naturally become less volatile. At some point one would expect people to say, "why am I encurring the cost to trade in and out of dollars when I can just spend the bitcoin directly?" When that happens bitcoin becomes a currency.
> As automation++, value of money--

This is the opposite of what happens. Automation increases the production of real goods and services. It's goods and services which are traded in exchange for money. Hence "more goods and services chasing the same amount of money" means the value of money must increase. 
> E - Avalanche pre-consensus is not uniform and fragmented into N groups with N possible "valid transaction sets".

The way it's implemented currently an attacker with enough hashpower/stake can only cause the network to reject a transaction. He can't prevent nodes from coming to consensus about whether a transaction is valid or invalid.
Fraud proofs would only protect against a 51% attack. But if that happens we've got bigger problems and full nodes aren't secure either (for slightly different reasons). 
La fin du monde
I agree. Though this talk was describing the events of what happened to people who mostly didn't know about it.
> In order for Avalanche to be effective, more than 51% of miners will need to form a cartel and agree to orphan blocks

You realize this is exactly what CSW and Calvin proposed right? 

Avalanche is just a decentralized (not cartel) way of doing the same thing. 
Presenting the guy's own words is not slander. If you guys weren't retarded you realize low much of a low life CSW is just be reading what he says. 
> Is there much of a point with double spend proofs if BCH integrates Avalanche?

You either need to query avalanche or listen for double spends during the expected time it would take avalanche to complete. The later is less of a load on the avalanche nodes. 
> open bazaar is dead no one uses it.

It has more users than the lightning network ;)
> How does the Neutrino system work with in-mempool transactions? How does a wallet 'subscribe' to a list of addresses to get notified on new unconfirmed txns?

Currently it listens for all unconfirmed transactions. You can turn it off in a config setting then you'd operate in a blocks only mode.

 > What are the privacy disadvantages of simply querying ElectrumX servers over Tor -- one server per address?

I guess that could work though it would be very slow and cumbersome. 
> the property of immutability is compromised in favor of expedience.

Reiterating what tyler said, a pure POW is not immutable at all. It always can be overriden. Avalanche strives to bring immutability and finality, albeit with some other tradeoffs. 
I don't have any problem with it if people want to add it. 
1. I like BCH because it's closest to the tech that I personally want to use and we're in a position to improve it and bring it closer to my personal ideal. Does that mean other people will want to use? Lol I'd like to think that's the case, but I could be wrong. 

2. BTC still has a chance but I think they would need to stop letting the small block dogmatists from dictating the direction of BTC.

As far as LN goes, if fees remain low, they could maybe make something that at least partially works. I really doubt that it will ever be 100% and as decentralized at actually BTC network is. And if some of the loudest voices in the Core community have their way and fees blow out to $100-$1000, then nobody will be opening channels and using LN. 

3. POS could be good if it could be made to work. Maybe from a pure security standpoint a hybrid system where you need >51% of the mining power AND >51% of the stake to attack the system might be the best of both worlds. Avalanche combined with POW could possibly work like that. 

4. I like how the ETH devs are not afraid to experiment and make changes. I think they have solid researches and a good attitude towards improving the chain. 
I'm terrible at picking investments. Back in like 2013 I lost 17 btc due to day trading so I gave it up. 
Without.
I think we as a community need to try to update Bip70 in a collaborative way. Bitpay has decided to unilaterally work on a JSON version but the authentication scheme they created is completely broken IMO (among other issues with it) https://github.com/bitpay/jsonPaymentProtocol

I'm all for a JSON version (ideally servers should support both proto3 and JSON as expressed by the request header) but we need to get the spec right. 

Just requiring that the payment request be served over https and that the certificate used on the connection be the desired merchant identity is probably the easiest way to authenticate and 1000x better than the GPG key madness Bitpay is trying to switch us to. 
Yeah sharding, zk-snarks, etc. 
The buyer sends the signed transaction directly to the merchant with BIP70, yes.
I grabbed my seed from there as soon as he announced he was going all in on BSV. Had a couple hundred dollars in tips in there. 
Good thing the longest chain regardless of consensus rules has never defined bitcoin.
And possibly the worst part of it all is he didn't realize that op_sha256 also enables the very same type of illegal gambling and it's more efficient and the fees are cheaper than using op_checkdatasig.
Nchain will have to build everything the community around BCH has built by themselves. Nobody is going to be building this stuff for a scam coin.
> If proponents of one chain are trying to 51% attack a different chain to promote their own (and not just to double-spend), I would guess their chain has shortcomings that they are trying to compensate for by reducing free market competition.

exactly
This is clearly the Core troll army trying to help BSV.
Another Core turned temporary SV troll. 
Jihan minds his own business. All these conspiracy theories about Jihan from people who don't even know how he operates. 

Jihan has invested in OpenBazaar. We've had one meeting with him where he bought lunch and said nothing. That's the amount of control he exerts over the projects he invests in.
lack of reply protection doesn't force you to pick a side. Further the bch/bsv split has opt-in two way replay protection.
Having hash power doesn't make the coin profitable. It's actually the opposite. If you have an excess of hash power it's not profitable to mine. 

And judging by community sentiment my guess is BSV is valued at < 20% of BCH. So moving all that hash power over from BCH to BSV means Calvin and Craig are going to be mining BSV at a big loss. While making BCH much more profitable. 
Keep in mind that when Craig an his cronies withdrawal their hash to mine BSV that will make BCH dramatically more profitable to mine and will draw hash rate over from BTC filling most of what was lost.  
>  If they do the wrong thing they will lose money and go out of buisness. 

And all of us with them. Fuck that. 
>  has forced changes that none of the development teams behind other Bitcoin Cash implementations supported (CTOR)

This isn't true that none of the other implementations supported it. I supported it for one. So did Bitprim, bcash, bitcrust(?).

And the vibe I got from BU wasn't that they were fully opposed to it but wanted more time to research 
alternatives. Which is fair imo. They also said they don't think it's a significant enough change to split the chain and community over. 

I think this is the main point of theirs that I'm not sure you were fully understanding. They want to validate utxos in the block using the way that they proposed. Doing so removes any need to validate dependencies within the block and removes the need for topological ordering.

If you validate that way AND keep the topological ordering requirement, then you have to do extra work to validate the ordering is indeed topological.

Given that, it make sense to remove the ordering requirement to improve performance. And as TomTom said, you don't per se need to introduce another lexicographical ordering requirement, but there are some nice things it gets you like better graphene and exclusion proofs.

You are correct to ask if this is the best way to validate a block. I don't know if I have the answer to that, but if it *is* the best way, then it would seem like introducing lexicographical ordering would be a plus.
>The gold-backed dollar worked fine.

You should study the history of the gold standard if you think this is the case. Because of the inconveniences of transacting directly in gold, most transactions took place on a "layer 2"  banking system, which was undermining the soundness of the monetary system as far back as the 18th century.

>The Bank of North America, which quickly received a federal charter and opened its doors at the beginning of 1782, received the privilege from the government of its notes being receivable in all duties and taxes to all governments, at par with specie. In addition, no other banks were to be permitted to operate in the country. In return for its monopoly license to issue paper, the bank would graciously lend most of its newly created money to the federal government to purchase public debt and be reimbursed by the hapless taxpayer. - A History of Money and Banking in the United States

The interventions and cronyism only worsened in the 19th century and ultimately lead to calls to create a central bank and end the gold standard. Which happened in 1933, not 1971 like you claim. The system which existed from 1945 - 1971 was a bastardized international gold standard in which only foreign central banks, not the general public, could put in claims to redeem notes for gold. And note, they *did* print tons of money while on this international gold standard. It didn't stop them one bit. This is the reason why gold exchange was ended, because it printed so much money it couldn't redeem all outstanding paper.
Not completely accurate. The reward drops to < 1 BCH in just 14 years. If the price has not risen dramatically by then then fees will need to replace almost all of it. 

To answer OP, it would take 1.2 GB blocks at an average fee of 5 cents to replace the current BTC reward... which is twice VISA's average daily volume. 
And here you are doing the exact same thing.

> The inability to reason that people who take an opposing view from you are anything but malicious or stupid is a classic sign of irrationality.
It's a peer-to-peer network where the miner who mined the block n-100 can sign and relay the transactions that he thinks the *next* miner should include.

Like weakblocks, there's a small incentive for miners to use this network because it can make their blocks propagate faster and it obviously benefits merchants to have a little more certainty that a zeroconf tx will be mined. 
I'm not terribly hot on the idea but I think it works like this... Miner Mike records the time he saw the payment (tx1) and double spend (tx2). If tx2 was received more than 30 seconds, say, after tx1 then it's highly unlikely that tx2 was actually broadcast first and is not a double spend. In that scenario if Mike sees a block containing tx2 he can just put if off to the side and say "I'll only mine on top of this block if someone else does". So he's not permanently rejecting it, just refusing to mine on top of it himself. 

Could that work? Maybe. But my concern is the the time between transactions (30 seconds in the example) needs to be set high enough where we can say with near certainty that tx2 is a double spend. However, let's say 30 seconds is the correct number, that is still too long for merchants to wait to see if a double spend comes off the wire. 

So it would seem like while it could maybe work, it still doesn't get the time low enough to justify risking short term chain forks. 
> Gold is an order of magnitude worse as a base money than Bitcoin

That's a pretty bold statement. Gold does not depend on social consensus which can and has changed. Nor does it depend on bulletproof coding from a handful of people to avoid blowing up. 

In terms of systemic risk Bitcoin is probably several orders of magnitude more risky than gold. The only real reason to prefer it over Gold is that the benefits (p2p transfer) outweigh those extreme risks. 
Correct. So roasbeef has implemented this into his fork of btcd https://github.com/Roasbeef/btcutil/tree/master/gcs

I wouldn't mind using his code but it would be a good deal of effect to port btcd to bch. More time to do that and maintain it than I have on my hands. Justus did tell me he's looking into maybe porting it as part of Stash. 

But it would be cool if it was also implemented into unlimited. 

As for what I would write the client in, probably Go since I have a lot of SPV wallet code in go already and the the GCS filtering library already exists. 
It's a very tiny fraction of users that run linux and an even tinier fraction run a distro that a deb package doesn't work with. 

I think we tend to assume people who are running those alternate distros likely have the technical ability to compile from source.
> Why is malleability an issue in your opinion?

In LN they get the counterparty to sign the refund transaction before they commit funds to the channel. If the transaction that commits funds to the channel is malleated then the funds are still locked but now the refund transaction is invalid. 
> Though I will argue in this post for how LN can work, nobody can guarantee that it actually will work as hoped.

That sums up LN fairly well. It certainly *can* work. Though there is no guarantee that at the end of the day it will.
By itself running a node does very little. It will consume as many resources as it contributes. It will, however, act as a server for lite clients so that is a sense in which it does "benefit the network". But doing so it optional and is not what most people think of when talking about benefiting the network. 

But having at least *some* economic actors fully validating transactions does provide a benefit to the network in that it helps ensure the miner's incentives are aligned properly. 

If nobody but miners fully validate the consensus rules we'd have to cross our fingers and hope they miners never form a 51% cartel and change the consensus rules. 

But the fact is miners must sell the coins they mine (to exchanges, merchants, etc) in order to profit from their mining activity. If enough economic actors are fully validating their transactions then there would be a strong disincentive for miners to try forming a 51% cartel and changing the consensus rules since nobody would accept the coins they mine. 

We don't need everyone fully validating transactions, but we'd like to at minimum have large economic actors doing so. Which is mostly what we have today. 
You can use BCH in OpenBazaar currently. Works pretty well. 

As for tracking number. The parties to the dispute would need to provide it to the moderator. He can see the shipping address as part of the contract. 
There are telegram working groups that are open to the public and open conference calls are held as part of the working group. The summaries of the calls are posted publicly. 
I've bought it to. The to the moon hot sauce was one of the hottest I've ever had. 
Well I'm not sure. The low s and nullfail was implemented last hardfork. Are there any other forms of third party malleability? If no then segwit is only needed for dual funding.
That doesn't seem to be what you we're implying there but in either case the network doesn't need to process all the world's transactions today, it just needs to process the amount of transactions that people are actually making. 
> It sounds like they are saying on-chain scaling can't work, not that it can't work "today".

That would be an obviously false argument. 
How much would that piss them off if it was implemented in bitcoin cash before bitcoin ;P
I want fees to remain one penny as tx volume increases. For that to happen you need a competent developer team who is committed to optimizing the shit out of the software. Out of the ones mentioned here, the only one that qualifies is BCH. 

LTC's whole roadmap is to just merge commits from Core. Which is to say it will shit the bed if volume increases just like Core did. 

XMR devs seem to be smaller blockers as well. Even if they weren't their tech has more severe scalability challenges than btc/bch. 

So the only one that has a chance of keeping fees low as it scales is BCH. 

The xpub doesn't work for this as you don't have a great way to avoid reusing the same child number.

Also payment codes are similar but slightly different in that they require a setup transaction before any payments can be made. Probably an OK way of doing doing it though I'm not a fan of the setup requirement.
We'll we're working on one but we have limited resources. 
You can watch Brian's presentation on it if you like https://youtu.be/BPNs9EVxWrA?t=2h56m10s
To my knowledge all the developers are in agreement to increase it to 32mb this year. I don't think the article is inaccurate. 
Well 1GB is visa scale (slightly more).

Given Moore's law, it would probably be viable to process that volume on a home computer in about 8-9 years. Possibly less.
Personally, I think it's a long shot to be viable for everyday payments. But should that long shot become reality it could easily be repurposed for bitcoin cash. In either case you'd rather pay 1 penny to open a channel than $20. 
This post should be titled "Daily reminder that you don't need segwit for the Lightning Network". 

Bi-directional payment channels work fine without segwit. Segwit brings a minor benefit in that both parties can simultaneously fund the channel at open. But given how LN is being designed and used people aren't even doing that anyway. 

If for some reason you needed dual-funding you could just open another channel. 
"Why do you buy food? Don't you guys realize you could convert those dollars into BCH and it could be worth a lot of money in the near future?"
> So no, XRP can not be frozen

This is provably false. The XRP consensus mechanism is flat out broken and does not work in a decentralized context. To make it work they have to ship it with a default list of validators and if you don't "trust" their default list you'll likely fork yourself off the network. 

So at the end of the day those validators (Ripple) are in a position to censor transactions since they ultimately decide which transactions make it into the ledger. 
https://www.deadalnix.me/2016/09/29/using-merklix-tree-to-checkpoint-an-utxo-set/
Lol. Makes me like Sztorc a little more. 
I think Peter Rizun is planning on testing the ssd assumption. 
No because poor people running nodes has no impact on the network. The people who do are economically significant actors. Keeping the blocksize at 1mb doesn't change that fundamental dynamic. 
The Bitcoin Core developers made a conscious decision to jack fees way up and offered poor, if not empirically false, reasons for this change. 

Bitcoin Cash is continuing the Bitcoin chain as it always worked, with low fees and is placing a priority on keeping it that way. 
Adam Back must pay well for you to take time away from Christmas dinner to post this.
Notice how dishonest his answer about the LN is when his own website says lightning is a "micropayment system for *tiny* payments". What about all those payments that aren't "tiny" but still small enough that the fees are uneconomical (which is probably like 90% of payments)?

Also note Aaron's solutions... 1) Use a centralized service which undermines all of bitcoin's value proposition. or 2) Don't use bitcoin. 
>  If you hadnt noticed, we already have multiple working LN clients just a few months after segwit activated

If that's true doesn't that suggest that nobody wants it? Why aren't people flocking to the already-working clients you talk about?
They will do one better than all of that... quit using Bitcoin and move onto bitcoin cash.
"Data-center only validation". What a lie. You'd have to go upward of gigabyte blocks before that happens. 
You're missing the point. It's not just dollar savings that is siphoned away but *income*. New money does not spread out evenly throughout the economy. The first spenders (wall st) spend the new money at today's prices. Everyone else sees prices rise before the inflation hits their wages. That reduces your real income. See https://chrispacia.wordpress.com/2013/09/20/the-fed-orchestrates-the-largest-redistribution-of-wealth-from-poor-to-rich-the-left-blames-the-free-market/

As for this:
> Those are not solutions for everyday purchases.

Bitcoin could easily have enough capacity to meet the everyday purchase of the current user base if it were allowed. Of course, it can't do that for everyone presently but given the pace of innovation in the space it's a 100% certainty that it will be possible by the time that need arises. 

But that's not going to happen in bitcoin as long as there are people like yourself who are convinced there's nothing wrong with fiat and bitcoin is really just about international transfers or speculative investments. 
> If this is correct, then does this mean 2nd-layer options can only work if ppl use Segwit wallets?

Well the lightning network requires segwit so yes. But more importantly, it's not just 18 months away... it's 18 months away from seeing if it works well at all. The lightning network is a purely speculative technology that could very well be a flop (there are technical and UX issues that remain unresolved). So we wont know until it's released and/if people are using it. Like any new technology, the odds are not very high that it will be a commercial success. 

> are the 2nd/3rd-layer solutions being designed in a decentralized way to free them from potential government crackdown?

Again we simply don't know. If the routing topology of the lightning network ends up centralizing in a few large hubs then the answer is no. If payments are routed through a large mesh of small nodes then yes, but this is much harder to do in practice. 

You're touching upon why people are pretty outraged. They are actively deprecating the core value proposition of bitcoin in favor of a purely speculative technology that might not work out. And if it doesn't work out then what?
A good history of Bitcoin here https://medium.com/@arthricia/is-bitcoin-unlimited-an-attack-on-bitcoin-9444e8d53a56
> safe and reliable on BCH, especially with Replace By Fee gone

Replace by fee was not a consensus rule. Miners were and still are free to include any transactions they want, including double spends. 

In practice it *might* not be in the miner's long run interest to mine double spends, but we should have stronger guarantees in the protocol than just hoping miners behave the right way.
So did you just make up the whole ZKCP part to make it sound like you have an impressive knowledge of the technology?

Also, again you're wrong. Step one to sending a lightning payment is to get a hash from the recipient. He can't provide that hash if he's offline can he?
Did they make you stop doing it automatically?
I would point out that the original goal of bitcoin was to create a self-contained ecosystem where people could just spend bitcoin for the things they wanted. This would eliminate the need to have to pay exchange fees on both sides of a transaction. 

Sadly bitcoin seems to have abandoned that vision a long time ago in pursuit of a "peer-to-peer speculative trading asset". So the middle graphic should really be  $5 + 3%. 
> instantaneously at a fraction of the cost of current methods

Uh it's already more expensive than other options. And I wouldn't describe a several hour wait as "instantaneous".

Not to mention.... yawn. Nobody got into bitcoin because they thought it would be a 5% improvement over western union. 
Sharding. Ethereum will hopefully be experimenting with it first. And deadalnix has written several blog posts on it and has it on the bitcoin cash roadmap.
My company never confirmed support. I don't think we were contacted either. 
I've been working on this https://github.com/OpenBazaar/spvwallet

Going to use it in openbazaar
I don't disagree with you per se and so my criticism wasn't really directed at you. Glad you're not one who holds that position.

In the case of your specific vision for Bitcoin the question is how successful can Bitcoin be when it needs third party institutions to transfer it?

I agree that it *could* and I would argue *should* work well that way, but we've seen what the government did to gold when it used a similar "layer 2" system.

Bitcoin being even more invasive than gold I don't expect such a layer 2 to be given a pass by governments.
> This soft fork is "user" activated because "users" are sick of paying high fees and want bigger blocks.

I'm not sure where you got that idea but most UASF supporters support 1 mb non-witness data basically forever. Once 1.8 mb total capacity is reached via segwit then that's pretty much all you're going get. If fees blow out to $50-$100, most of them will say so be it. 
tbh, I assumed the actual reason for the ban was the comment since I couldn't imagine just sharing a post (which I have done in the past without bans) would be grounds for banning. Either way that sub is shit.
This is just terrible. All his points on ICOs are correct but people are adults and can and should bear the consequences of their investment decisions. 

Calling for SEC regulation is beyond the pale.
The people who agreed to this is most of the bitcoin economy and 80% of the hash power. If Core refuse to support they will fork themselves off onto a tiny minority chain.
isn't even socialist™
His comments have been the most upvoted comments on /r/Bitcoin as of late and dozens of people respond saying they completely agree.
So basically you think bitcoin is a p2p speculative investment asset, great! But that's not what anyone signed up for. Investment assets don't undermine corrupt financial and political institutions... money does. And for something to be money it needs to be exchanged for goods and services, not hoarded in an investment account. 
This notion that anyone who is against segwit is only doing so because it was invented by Core is absurd. 

I was on the very first segwit thread, before it was even designed and before the bips were written, saying changes this big should be done as a hardfork and not a softfork. 

At the time I was worried that such a big change would end up being very hacky when done as a softwork... once the bips came out segwit ending up being even worse in this regard than I was expecting. 
Well 47% of the United States is uninhabited. And I suspect more like 70% or 80% would be considered "lightly inhabited". Problem is much of that land is off limits to build on. And places like NYC or SF have ridiculous regulations that massively increase the cost of building if not outright prevent it. 


In theory I don't have any problem with that vision. In practice, the government will never allow it. The same could have been done with gold decades ago and it hasn't happened because the government doesn't want any monetary competition. 

If you want to have an insurgent money that competes directly with fiat it needs to be censorship-resistant. And a bank-based system has been proven to not fit the bill. 

If government didn't exist (or at least if wasn't so hell bent on maintaining its monopoly) then such a system would probably be practical. But that isn't the world we live in today. 
If you look at the some of the research being done over the last year or so it's pretty clear to me that most of the major problems in bitcoin are either solved or very close to being solved. 

* Mining fairness and nearly instant confirmation times:
http://hackingdistributed.com/2015/10/14/bitcoin-ng/

* Very fast irreversible transactions
http://hackingdistributed.com/2016/08/04/byzcoin/

* Sharding the UTXO set
http://www.deadalnix.me/2016/09/29/using-merklix-tree-to-checkpoint-an-utxo-set/

* And the link above, sharding block validation

I'm not saying all these definitely going to work, but I think it's definitely worth seeing how they perform in production. And if they work as advertised, then most of the hard problems would have been solved. 

The last one remaining being mining centralization with proof of work. 
> OpenBazaar is going to solve its decentralization problems by pushing data into the "decentralized grid". Except the incentive model for getting others to host this? Nowhere to be found. 

By default nodes seed all data they download. So if you view a product listing you become a seeder. I suppose you can argue that won't work because there's no compensation but then bittorrent shouldn't work either. 

Nodes can also push data to other nodes but only if the recipient explicitly enables this functionality. We might play around with payments for this service in the future but I think it's doubtful it will be necessary.
> Costa Rica, like their neighbor Nicaragua, has low quality internet services, poverty, inflation, and they will need bitcoin the most for remittances and securing wealth outside of banking systems.

There's no way they can afford the current transaction fees. 
Does the opposite hold true? Should all the other pools give their miners the option to mine Classic?
> I wonder what else that USB stick contained.

I took that read that he used his own "clean" USB stick.
Is the current reward (subsidy +fees) enough to secure the network? I ask because that level of reward can easily be replaced by fees at larger block sizes.
Transaction volume likely needs to be doubled as multisig takes two txs per order. 
As bad as hillary is on economics at least she realizes that we don't live in a fantasy world where you can just dictate any wage rate you want without negative economic consequences.
Yeah. A soft fork is a Core developer coordinated with 51% attack on the network.
Hey bro. If you read the article he uses data from the actual peer reviewed paper from which the 97% number comes from. 

Or if you like you can take a look at the peer reviewed data yourself http://iopscience.iop.org/1748-9326/8/2/024024/media/erl460291datafile.txt
That was an optimistic goal we set back in May. We got about 80% of the way there. Hopefully we will be close by the end of the year. 
> So even though at 1MB blocks we're having issues getting miners to actually validate

That comes across as just laziness/incompetence on the part of those miners. Even if they want to spv mine, validating the block while you're are mining on the header will earn more profit than never validating at all.
Are there any plans to do committed utxo with elements? That plus the asset issuance would make for some pretty dope lightweight clients. 
> This is a myth based on... hopes and dreams? I'm not really sure.

This isn't about some theoretically laboratory experiment. What percentage of brick-and-mortar merchants have actually been defrauded by accepting zero-confirm? I've never heard of a single one.

When deciding whether to accept bitcoin in their stores merchants are going look at the historical rate of double spends (which is very low) and try to forecast going forward. The reason brick and mortar stores accept bitcon is because this rate is so low. It's not hopes and dreams, it's reality. If it wasn't, they wouldn't accept it.

This patch guarantees it will be much higher and threatens to relegate bitcoin only to online shopping where merchants can wait for the confirms.
> Anyway, the top section of the paper is the most important regarding that objection: if even the most popular wallets for "end-users" don't detect double-spends at all let alone invalid transactions, and can be double-spent trivially with ~50% probability.

That is a false claim. Schildbach's Bitcoin Wallet (as do other bitcoinj wallets) only accept an unconfirmed tx if it relayed by a high threshold of its peers. Even with uneven propagation the probability of that threshold being met for this race example is pretty close to zero.

So that simple race attack will not succeed with a 50% probability as claimed.
You are mostly right but you need to consider that the present value of any asset is equal to it's discounted future value. Investors almost certainly have factored the halving into the current market price. 

Now given that the time discount is a discount for ... time... then we would expect the price to gradually rise as we get closer to the halving (assuming demand stays constant). 
Keep in mind the fees that larger blocks would generate. People are concerned that a falling block subsidy will harm the network, yet with only 30 mb blocks the fee would complete replace the subsidy. 

At visa level the fees would be like $60k a block. 10x more than today's subsidy.
Stellar seems like it's just a clone of Ripple created by a guy who got pissed off and left Ripple. The whole thing is an attempt to use web of trust to do what Bitcoin does. 

We see how that turned out for the Internet. There are only 4 trusted parties with 90% market share.

Why do they think this will be different?
> Freezing of accounts is a very important feature in a 
well functioning state

Tells you all you need to know.
Fuck ripple anyway. It was always about faux decentralization. Everything about them was shady from the beginning
I don't know if "owning themselves" is a philosophically (or legally) correct way to think about it. 

There is a sense in which all resources which others are prohibited from using must be owned by someone. If I had to re-phrase it I would likely say the vehicle started out as the private property of the creator who more or less donated it to the public at large. 

So essentially everyone would be part owner in some sense rather than it being owned by itself.
Coursera Stanford Cryto class
ECDSA is public key crypto and based on discrete logarithms, but yes, diffie hellman isn't used.
We're trying to solve this problem using onename. You store your (stealth) address in the namecoin blockchain. When someone goes to pay you they enter your onename... chrispacia for example. Then the wallet makes a query to get the address.

The difficult part is making it trustless, but we're working on it. 

Demo here: http://youtu.be/T-CX-S8fq5A
Your (encrypted) wallet is kept on blockchain's server. When your log in your wallet, the browser gives the wallet ID to the sever and the server returns the encrypted wallet...which is then decrypted with your PW.

2FA prevents someone else from downloading your encrypted wallet from their server (assuming no MITM like the Tor attack), but it does not make the wallet any more difficult to brute force for someone who is able to get their hands on the encrypted wallet. 

If you want better security you should use a multisig wallet. Greenaddress, bitgo, or you can play around with an [alpha version](https://www.bitcoinauthenticator.org) of the wallet I'm working on.
MIT but it's really just public domain.
Currencies used to be backed by by gold (and IOUs) but are no longer. They are backed by nothing, not unlike bitcoin. 

The difference is in the rules of the game which are much more favorable to the average person in bitcoin.
You're a fallacy factory. We are not the not the government. Don't conflate government with society. 
That does nothing to address either the argument or the integrity of the economist who wrote it.
Serious question. Unless there are risk that you will *never* get your funds from Mt.Gox isn't it still a better deal than elsewhere? 

The difference between Mt.Gox and other exchanges is about 12%. That means by using another exchange you are paying 12% to get your funds 3 months quicker. That's an annualized interest rate of 48%. Is it really worth it?

The difference for the amount of $ I want to withdrawal is about $2,000. If I don't *need* the money right now, why should I use another exchange?
That's only if you assume the problems w/ unemployment aren't structural. 
It's breathtaking to me how many people refuse to acknowledge that money printing redistributes wealth upwards. This has been known since at least the 18th century but so many people today like to pretend it isn't the case.
Then you don't understand how it works. 
yeah it could happen afaik
Craig defrauded the Australian tax office by getting millions of dollars in innovation grants for fake companies that were set up just to scam the government. 

He got caught and Calvin and company agreed to pay off his debt to the Australian government in exchange for the rights to all the patents CSW would file after announcing he is Satoshi.

Craig has to peddle these patents to up hold his end of the deal. If none of them pan out he's going to owe tens of millions of dollars to Calvin and other people.
yeah I can't use an electron cash plugin in my app
This honestly sounds like you're afraid of people hearing CSW's own words. The fact that you brigaded this thread would seem to confirm that.

He's your dear leader. You knew who he was before you made the conscious decision to promote BSV... a coin which he owns and controls.

Why are you trying to disavow him?
That's really has nothing to do with it. Like ethereum the inflation rate trends to zero. 
Time and resources
I haven't looked into that wallet there but last time I used the eclair one I looked into it and noticed that the node that it has you open a channel with (the one run by their company) had opened a bunch of channels with various LN merchants like the Blockstream store, Bitrefill, etc and deposited around $1000 in each channel. 

So essentially payments to these stores would go through 100% of the time since the channels were in a sense pre-arranged. That struck me as a bit of smoke an mirrors as that setup couldn't possibly work at a large scale. Or even a small scale for that matter. 
He also said he sees a future where merchants want to run full nodes and possibly up to 100k full nodes on the network (far more than there are today). That is the opposite of what CSW wants. 

> "Each transaction has to be broadcast twice".

Means the tx is broadcasted once to the mempool and again when it's included in the block. Merchant to miner to rest of the miners doesn't even make sense as that is more than two broadcasts. 
It has been conclusively demonstrated that gambling oracles have been possible on the Bitcoin blockchain since day one using OP_SHA256. It has also been demonstrated that OP_SHA256 produces smaller transactions and thus would cost users less in fees than DSV.

Not a single person has been able to state why it should be considered illegal when the alleged illegal functionality was there the entire time using a different opcode and while the new use cases for the opcode that have been proposed ... zeroconf forfeit transactions, covenants, and vault transactions are no where close to illegal. 

This was and always will be an absurd argument. 
Keep in mind they could always not mine any txs they don't want to. But there could be some txs that they do want to that they can't because of avalanche. 
>  unless Avalanche is made as part of consensus rules

That would ultimately be the idea. 
The constitution of the consensus group is rather arbitrary and can include blocks, utxos, or coin age going back an arbitrary amount of time. So even in the event of a split one can learn of most of the participants in the group by just syncing to the point of the split. A split would need to continue for weeks, months, or even years, before a new node would face ambiguity.
No just instead of sending the full block to the wallet it would construct a partial block using the requested ranges. 
You can look at this list of malleability vectors created By Pieter Wuille https://github.com/bitcoin/bips/blob/master/bip-0062.mediawiki

Most of them have already been fixed except for #3 and #7. So the proposal is to make them consensus rules in the next hardfork. That would likely close the remaining third party malleability vectors unless there are others we don't know about. 

Something like malfix would be needed to prevent first party malleability but that's not being proposed for May. 
1. I think we probably will enable it next year, yes. 

2. Which do you have in mind? It's possible bchd could be used as a backend for the BCH wallet in OB but that might create more work for us because right now we just use the same backend for everything. 

3. We should have testnet binaries available to be test today. https://github.com/OpenBazaar/openbazaar-desktop/releases

So mainnet shouldn't be that much longer. 

4. That would be nice. Maybe next year we can take a look at something like that. We still have a massive TODO list for OB. 
>  The network can refuse invalid blocks therefore it could also penalize misbehaving nodes with a time delay (or other mechanisms).

This is a good way to fork the network. The point of preconsensus is to *safely* orphan blocks. Not recklessly do it.

> Also can you give a real example where a miner accepted bribe on BTC or BCH networks?

This is encouraged on the BTC network. As for BCH ... https://youtu.be/TIt96gFh4vw?t=963
You do realize there is zero difference between this system and the gold standard right? And the gold standard was an objective failure because this system lacked the property of resilience. 
Probably any time next week
This is the only real challenge here is that the spec will need to keep up to date with mobile OSs. Google and Apple keep changing how they handle push notifications. I have written any mobile apps in a while so I'm not sure the best approach. Someone with a lot of mobile experience would need to be involved in writing the spec. 
A lot of people couldn't access their seed after he turned it off. 
I think the problem you are describing is only an issue with dual funded channels which this implementation does not have. 

Alice signs but doesn't broadcast a transaction (tx0) funding the 2of2 from one of her inputs. She asks Bob to sign a refund (tx1) which spends from tx0 back to alice. Alice now broadcasts tx0. 

Assuming we have fixed third party malleability, the only one who can malleate tx0 is Alice. 

But Bob isn't going to accept any payments on the channel until it confirms so even if Alice tries to malleate the tx0, her funds are locked and Bob loses nothing. 
I don't agree that it's not longer a concern as finalization introduces other problems that have not yet been resolved.
> Accelerate the deposit and withdrawal of exchanges, Increase the number of arbitrage users;

This seems doubtful. No exchange accepts only one confirmation and if the block time went down then they would just increase the number of required confirmations. 
They say it all the time. Bitcoin was perfect when it was released. The only goal of development is to strip out the stuff Core added and return it to the 0.1 state. 
You might be right. Though I think there's likely to be two different groups of opinions on this:  
\- A network with instant transactions is more valuable than one without. Thus the profit maximizing strategy for miners is to preserve the first seen rule.

\- Zeroconf isn't what makes the network valuable and/or gains from taking bribes outweigh the gains from preserving instant transactions. Thus the profit maximizing strategy is to take bribes.   


Ethics may come into play as well. I personally would not accept bribes to help defraud people even if it thought it was a profit maximizing strategy. I have to imagine at least some miners would feel the same way.  


So if those holding the former opinion are in a majority then avalanche works with only miners in the consensus group. If the later group are in a majority then you need to use utxos or coinage for the anti-sybil mechanism. 
1) Toomin is actively working on an implementation using it.

2) There are a lot of things to work on. One person, team, shouldn't be expected to do everything.

3) What he was saying is if you want to see it done faster than what they can do with limited resources then pick up a compiler and stop demanding other people work twice as hard. 
Lightning is always going to have a tradeoff between reliability and centralization. In a more pure p2p network topology payments are very unreliable. Censorship-resistant, yes, but only if you can find a route with the correct capacity. Tests I did like six months showed a 97% failure rate for a $100 payment. Obviously we'd expect that to improve some if it ever sees adoption but it will never be low enough for it to be a viable payment system due to the design of the system.   


On the other end you can increase reliability by introducing centralization around large payment hubs and reducing the censorship resistance. It remains to be seen if that is even a viable model as the capital needed to run such hubs at scale is ridiculously large.   


Finally, nobody will be opening payment channels or using LN if fees blow out to $100 or $1000 like the Core minimalists want to see. 
