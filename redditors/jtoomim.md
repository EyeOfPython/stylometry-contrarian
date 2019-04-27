Actually, no, it's pot growing in Haarlem, The Netherlands.

http://www.nydailynews.com/news/crime/dutch-police-bust-marijuana-grow-house-snow-melts-article-1.2110709
I'm a 0.25% miner. I run a 750 kW, 1 PH bitcoin mining hosting company in central Washington. http://toom.im for more information on us.

We spend about 1/80th as much on internet connectivity as we do on power. That 1/80th gives us two 100 Mbps symmetrical fiber lines, of which one is just a backup. On our single active fiber line, we're running two full nodes, two p2pool nodes, about 1 PH/s of miners, and a few other things. With 400 kB average blocks, that uses about 1% of our available bandwidth. 

If we scaled up to 8 MB average blocks (20x higher transaction volume than today), that would use about 20% of our bandwidth, while still only costing 1.25% as much as we pay in electricity. 

If we spent as much on bandwidth as we did on electricity, we could probably afford a pair of 10 Gbps or 40 Gbps connections. (80x 100Mbps = 8 Gbps, but networking tends to get big economy of scale benefits.) That would be enough bandwidth to download and upload an 8192 MB block in about 10 seconds. BIP101 doesn't have 8192 MB blocks scheduled to be allowed until 2036. We could afford them today, at least for the bandwidth.

Currently, transaction fees are about 0.1 BTC per block. If transaction volume were 20x higher, that would be about 2 BTC per block. This increase would more than compensate for the increased bandwidth and storage usage, which would have nearly zero marginal cost in our case. If blocks were 4000 MB each (10,000x larger), we could afford to reduce transaction fees 10x while still getting 100 BTC per block in fees. Fees scale faster than bandwidth/processing costs for miners with more than about 0.01% of the network hashrate.
If you look at these blocks, you'll notice they don't actually have very many transactions. For example, the 3.7 MB SegWit block #**[894090](https://testnet.smartbit.com.au/block/00000000000016a805a7c5d27c3cc0ecb6d51372e15919dfb49d24bd56ae0a8b)** only has 468 transactions, with 467 inputs and 481 outputs. 

In comparison, the 975 kB block #**[440819](https://www.smartbit.com.au/block/0000000000000000018c4197cf4a7e09cd32ce13459651b84b90f0e77497e0a8)** on mainnet has 2,408 transactions, 4,515 inputs, and 5,176 outputs. 

A block made with a flat non-segwit 3.7 MB blocksize cap would be able to handle around 9,139 transactions, 17,133 inputs, and 19,642 outputs.

Despite being 379% as large, this testnet segwit block only got 9.8% as much work done. This is not an accident or a coincidence. The only way to make a very large block like this with SegWit is to make a small number of transactions with a small number of inputs and outputs but an artificially huge amount of signature/witness data. SegWit's 4x discount for witness data incentivizes transactions that are larger than normal due to complicated scripts and signatures.

The only known use for transactions with bloated witness data like this is spam. The 3.7 MB blocks were made to test the network's resilience to spam, not to test SegWit's functionality or transaction throughput.
There was a length-2 orphan race on Bitcoin SV:

    bch@feather:~$ ssh sv@maker bin/bitcoin-cli getchaintips
    [
      {
        "height": 557319,
        "hash": "000000000000000001ae1a2eeda841d099e85b00b29a8f84bdd4f58e380338de",
        "branchlen": 0,
        "status": "active"
      },
      {
        "height": 557301,
        "hash": "000000000000000001a11bf6da0ce31699ac4cbd312fb573886f85b979252f70",
        "branchlen": 2,
        "status": "valid-fork"
      },
    ....

Block 557299 had two children. Both of those children had children. One of the grandchildren of 557299 was sterile, generating the orphaned block 557301 with hash ....2f70 shown here. The other branch of the fork had more children, and eventually won the orphan race.

This is far from the only orphan race that BSV has had, but this is the first one that took 2 blocks to resolve. Since the Nov 17th stress tests started, SVPool alone has had [5 orphan blocks](https://pool.svpool.com/). They represent about 25% of the BSV hashrate, so if their orphan rate is representative of the network, it would predict about 20 orphans overall, or an orphan rate of 4.6%. An orphan rate of 4.6% is consistent with an average block propagation latency of 28 seconds: `1-e^(-28/600.) = 0.04559`. The 354 blocks produced on SV in the last 3 days have had an average blocksize of 5.018 MB. An average of 28 seconds for 5 MB indicates an average block propagation speed of 0.18 MB/s. This number is quite poor; in comparison, we noticed about 1 MB/s during the Sep 1 stress test. The low performance is likely due to mempool desynchrony issues with Bitcoin SV due to CPU saturation from the single-threading of net_processing.cpp code and the INV_BROADCAST_MAX bug.

It is not my belief that this chainsplit was due to malicious miner activity. I believe this happened on their own due to poor management of the stress test transactions.
The main targets for saving the world from overheating are the big arrows in this chart:

http://imgur.com/a/6kG96 ([source](http://www.skepticalscience.com/how-much-meat-contribute-to-gw.html))

The main sources of greenhouse gases are:

1. Electricity and heating (24.6%)

2. Deforestation (18.3%)

3. Agriculture (13.5%)

4. Transportation (13.5%)

For each of those sources, there's a different set of possible solutions:

1. (a) Electricity generation: Coal is more than just a present that Santa gives to naughty kids. It is also the worst source of energy for heating and electricity that we have. Natural gas power plants emit about 50% as much CO2 per kilowatt-hour as coal power plants. Coal pollution also kills about 800,000 people every year, or 1136 times as many as died in the first 5 seasons of [Game of Thrones](https://www.washingtonpost.com/graphics/entertainment/game-of-thrones/), so getting rid of coal power is a good idea no matter what. (Unless you just really hate people.) Nuclear, hydro, wind, solar, and geothermal emissions are close to zero. We should use more of those. http://imgur.com/a/DIJfM ([source](https://en.wikipedia.org/wiki/Life-cycle_greenhouse-gas_emissions_of_energy_sources)) (b) Heating: switching to [district heating](https://en.wikipedia.org/wiki/District_heating) is the most efficient option. [Heat pumps](https://en.wikipedia.org/wiki/Heat_pump) and improved building insulation are also good options. 

2. Deforestation: This is largely [driven by agriculture](https://en.wikipedia.org/wiki/Deforestation#Causes). Government policies can be put in place to protect forests either by controlling land rights or charging forest clearers money which can be used to plant trees elsewhere. We can also reduce the amount of land needed by agriculture by producing the same amount of food on less land. This can be achieved by making (and eating) less meat, by reusing land more, or by using better agricultural technology.

3. Agriculture: About 66% of all agricultural emissions come from meat production. Beef production is responsible for most of this. Making a pound of beef produces 2x to 5x as much greenhouse gas as making a pound of pork or chicken, and 20x as much as making a pound of beans. The problem with beef is cow bellies. Specifically, the stuff that comes out of cow bellies. There are two different things that come out of cow bellies: burps and poop. Cow burps contain a lot of methane, and methane is about 30x more powerful as a greenhouse gas than carbon dioxide. As for cow poop (manure), when it decomposes it produces a lot of nitrous oxide, or laughing gas. That might sound funny, but nitrous oxide is about 10x more potent than methane, or 300x more potent than carbon dioxide. The solution? Don't eat beef at all, and when your parents tell you to eat your vegetables, do it.

4. Transportation: When you want to go somewhere, use the bus, walk, ride a bicycle, carpool, or tell your parents to buy an electric car.
I urge people to be on the lookout for subtle bugs injected intentionally. Be very careful about submissions to Github or to consider.it from people with questionable agendas.

Try to be welcoming to newbies, and be friendly, but don't let yourself get duped.

Note that these are not Core devs, just Core fanatics. Don't punish the innocent just because it happened in the Bitcoin Core slack.

**[Cross post](https://www.reddit.com/r/Bitcoin_Classic/comments/41slq4/it_should_not_be_hard_to_inject_bugs_into_bitcoin/)** on /r/bitcoin_classic: 
Correct. SegWit does help normal transactions, but it helps spammers more.
Jaxx stores private keys unencrypted on the device. The files aren't even encrypted with the PIN. Jaxx trusts that nothing and nobody using that device will look at that file. This is a very dangerous assumption.

https://steemit.com/bitcoin/@angelgarz/security-problem-of-jaxx-wallet-anyone-can-extract-your-seed

A reasonable wallet program will encrypt all private keys with the user's password to prevent exactly this kind of attack. Jaxx is not reasonable.
Because it got leaked yesterday. Someone noticed that they had added API support yesterday and posted that on the other forum. This announcement from Coinbase came before Coinbase was ready, and happened as a response to that leak.
SegNet forked because someone was running an old (and known-incompatible) version of SegWit, not because SegWit itself was buggy. The unexpected fork does not indicate buggy code. It just means that people tend not to be too careful with testnets.

I'm not a fan of SegWit as a scaling solution, but I think it's important to not knock SegWit based on a misunderstanding.
Magnesium deficiency is quite common. Roughly half of the USA does not meet the RDA for Mg intake. https://www.ncbi.nlm.nih.gov/pubmed/22364157

Anxiety is a common effect of Mg deficiency. Supplementation with Mg appears to reduce anxiety. https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5452159/
Samson Mow invited both me and (IIRC) Jeff Garzik. He invited me about 48 hours before I would have had to step on a plane to get to Hong Kong in time for the meeting, and I was not able to arrange for my datacenter to be taken care of in my absence, so I didn't go. Jeff was given a similar amount of advance notice.
> It appears that the new wallet was rejecting nVersion <= 2 blocks (which are allowed under the old wallet) without checking if there was a majority upgraded, causing two chains to form. This made an intended soft fork into an unintended hard fork.

[Source](https://np.reddit.com/r/vertcoin/comments/5tgx79/official_vertcoin_has_hard_forked_new_wallets/ddmgdyd/)

That's a pretty basic error, and has nothing at all to do with SegWit specifically.
Oops, that was me. I forgot to upgrade one of my backup p2pool nodes, and it seems that we still had 27 TH/s pointed there.
That's what Matt Corallo's relay network is for. When you find a block, you just publish the header plus the hashes of the transactions, so you don't need to send the full block. You have to have the same amount of average bandwidth, because you have to have the transactions already for this to work. Most large miners already use it.

The above notwithstanding, if blocks took longer to propagate, then we'd make them smaller. Every miner has control over the size of the blocks he creates. If a miner gets high orphan rates, then he has a financial incentive to reduce the size of the blocks he creates. Upstream bandwidth is entirely decoupled from the protocol-level blocksize cap.

The purpose of the protocol-level blocksize cap is to prevent large miners from DoSing non-mining full nodes and small miners by publishing large blocks which everyone else is "forced" to download. It's not the only mechanism possible to prevent this attack, it's just the one that we've been using since 2010. 

What would probably be a better anti-DoS mechanism is if miners followed a rule in which they would not mine *directly* on top of very large blocks. If, say, someone published a 10 MB block, my node might choose to ignore it at first. If a 500 kB block was mined on top of that, my node might then choose to recognize it. If the big block was 100 MB instead of 10 MB, my node might require 4 blocks on top of it before accepting it. Such a feature could be used to proportionally and adaptively disincentivize excessively large blocks without hard consensus rules requiring a hard fork to add or remove.
The pump happened because over the last couple days two things happened:

1. Bitpay announced Bitcoin Cash support
2. Someone noticed that Coinbase's API already showed support for Bitcoin Cash.

Both of these pieces of information were readily available on the internet. However, they were not available on this subreddit.

If you're blaming Coinbase for missing out on this trading opportunity, that's not really fair. You should be blaming yourself for using a restricted source of information.
No, not laptops. Mostly octacore VPSs, with a few dedicated servers as well. The median server rental cost was $600/month.

https://www.dropbox.com/s/o9n7d03vbb1syia/Experiment_1.pdf?dl=0
I traveled around in China for a couple weeks after Hong Kong to visit with miners and confer on the blocksize increase and block propagation issues. I performed an informal survey of a few of the blocksize increase proposals that I thought would be likely to have widespread support. The results of the version 1.0 census are below. 

http://imgur.com/3fceWVb

My brother is working on a website for a version 2.0 census. You can view the beta version of it and participate in it at https://bitcoin.consider.it. If you have any requests for changes to the format, please PM /u/toomim. Note that he has been banned from this subreddit due to an administrative error, so you should not expect him to reply if you simply tag him here.

https://docs.google.com/spreadsheets/d/1Cg9Qo9Vl5PdJYD4EiHnIGMV3G48pWmcWI3NFoKKfIzU/edit#gid=0

Or a snapshot for those behind the GFW without a VPN:

http://toom.im/files/consensus_census.pdf

This post is a cross-post. The original post is on /r/btc in https://np.reddit.com/r/btc/comments/3ygo96/blocksize_consensus_census/. I may respond to comments in this thread, but I would prefer to have conversations in a forum without censorship.

**Edit: The doc and PDF have been updated with more accuracy and nuance on Bitfury's position. The imgur link this post connects to has not been updated. Chinese translation is in progress on the Google doc.**
Vitalik has always been friendly towards everybody in BCH except CSW.

Peter R has always been friendly towards everybody in BCH except CSW.

I have always been friendly towards everybody in BCH except CSW.

Roger Ver has always been friendly towards everybody in BCH except CSW.

Jonald Fyookball has always been friendly towards everybody in BCH except CSW.

CSW has been unfriendly to most of these people and more.

But somehow you think that Vitalik should be unwelcome because he might upset CSW? Should we also not invite Peter Rizun, Roger Ver, and JFyk?

Maybe next time we should instead just not invite CSW, as he seems to be the common denominator in all the drama, and he didn't seem to want to be at the conference anyway given how little time he spent in the meeting.
SegWit allows the signature data to be pruned off of the blockchain with nearly no loss of information. It allows efficient fraud proofs. It fixes the malleability issue. These are things which would make SegWit worthwhile regardless of how the fork is deployed.

The ability to use Schnorr signatures, the O(n ^ 2) hashing "fix", and the effective blocksize increase are things that are being rolled into SegWit because SegWit allows for otherwise HF changes to be done as a soft fork, and are being rolled out in an imperfect way in order to satisfy the softness requirement.

There are enough good things in the first paragraph that SegWit is still likely worthwhile, even if hard forks are on the table in order to more elegantly implement the things in the second paragraph. Fraud proofs in particular are really cool for the benefits they could have for SPV wallets.

That said, I agree that the main motivation from Core for SegWit appears to be avoiding a hard fork, and I agree that this motivation is lame.
They have also not solved the AcceptToMemoryPool (ATMP) bottleneck that effectively limits the code to about [100 tx/sec](https://youtu.be/LDF8bOEqXt4?t=4401) (~25 MB).

Given that changing the default limit to 128 MB is a one-line change that they have not yet made, whereas fixing the ATMP bottleneck is an [over](https://github.com/gandrewstone/BitcoinUnlimited/commit/898ba85d4a5076a0a384456ce5c631af74674c9c)-[2000](https://github.com/gandrewstone/BitcoinUnlimited/commit/032782a868a02a5408a8004d296b9be0a946511f)-[line](https://github.com/gandrewstone/BitcoinUnlimited/commit/2088796df6a8d8e477bbec460348278a440d2235) change that requires actual programming skill, I suspect they plan to just ignore the issue. After all, it's more important to be able to *market* your product as supporting 128 MB blocks than it is to actually support 128 MB blocks.
He meant that he is 99% confident that your funds are 100% gone.
While I agree that /u/deadalnix and co should have been providing benchmarks in support of their proposals, I've been working on doing that in their stead.

Yesterday we observed that on average, [37 of the 43 kB](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/e58k9wu/) per block in Graphene messages is order informataion that would be eliminated by CTOR. Now, 37 kB is not a lot at all, but it's still 86%, and as we scale it  eventually might grow to the point where it matters. I think this is the strongest reason for CTOR. Whether that CTOR is lexical or topological is a separate question.

Concerns have been raised that lexical orders would make block validation more difficult, most notably by Tom Zander and Awemany. I implemented a version of the outputs-then-inputs algorithm for topological block orders, and so far have found the [serial version is only 0.5% slower](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244#issuecomment-417841637) than the standard topoological algorithm. My code has a much greater chance for parallelization, and I'm working on getting that done soon. Once parallelized, it's plausible that the parallel version may be 350% faster on a quad core machine than the standard algorithm, but this depends on what [Amdahl](https://en.wikipedia.org/wiki/Amdahl%27s_law) has to say on the matter. I think this shows the fear-mongering about the lexical ordering to be unjustified, and suggests that there will be some tangible benefits soon.
This has nothing to do with Ethereum vs BCH, and everything to do with the way the CryptoKitties team decided to program their smart contract.
I hate to say it, but it looks like these blocks might have had a bunch of spam. There's a suspicious group of 64.3 kB SegWit transactions in both of these blocks:

https://www.smartbit.com.au/block/484399/transactions?sort=size&dir=desc

https://www.smartbit.com.au/block/484398/transactions?sort=size&dir=desc

Block #484398 has 8 of these transactions, and #484399 has 10 of them. All told, that's about 1155 kB of space used by one entity in two blocks.

Each of these transactions has 200 inputs and 1 output. At 64.3 kB per tx, that amounts to roughly 321 bytes per input. That sounds like a multisig tx, which is a well-known way to pack more bytes into the same weight with Segwit.

It's also possible that these transactions belong to an exchange or some other large entity that uses multisig. Still, it's weird, seemingly artificial, and clearly one entity that's doing this. Does anyone know of any exchanges that use P2SH or P2WSH deposit addresses?

Edit: more data [here](https://www.reddit.com/r/Bitcoin/comments/6z3rz7/13mb_segwit_block/dmsh999/) thanks to /u/dooglus.

Edit 2: I haven't checked every single transaction, but at least one of the transactions contains a mix of P2SH (non-Segwit) and Segwit transactions, and at least one of the other ones is pure Segwit. ~~I don't see a pattern in the age of the inputs. This makes me think that it's less likely to be spam and more likely to be something like an exchange consolidating their UTXOs for cold storage.~~

Edit 3: It looks like most of the UTXOs spent in each transaction were created at the same time. For example, the inputs for https://blockchain.info/tx/3cd63f3d3a1fb702f9065cec9581b02afc2ec65ad9d98d7b7ddc0c0d63c91342 were all created around 2017-09-08 10:04:27 or a few hours before. This might be due to the agent keeping a list of UTXOs sorted by creation date, and then iterating through 200 at a time to consolidate them.
The flood did not wipe out 20% of the hashrate. There are a few problems with this assertion:

1. Using a [504 block averaging window](https://bitcoinwisdom.com/bitcoin/difficulty), the hashrate fell from a peak of about 40 EH/s to about 36 EH/s, a decrease of only 10%, not 20%.

2. This 10% drop was reversed after about 10 days, and the estimated hashrate is now once again at an all-time high when using the 504 block window. If hardware was destroyed by a flood, one would not expect the hashrate to bounce back so quickly.

3. Fluctuations of 10% in a 504 block window happen pretty frequently due to statistical variance. For example, there was an 11% drop (from 38 EH/s to 34 EH/s between Jun 5 and Jun 12.

All told, I don't think there's clear evidence that the flood's effect on the hashrate was even statistically significant. 
The total SV hash has been consistently about 1.5 EH/s or less until 1 week before the fork. They increased their hashrate by 110% (1.8 EH/s) in one week.

That's about 200 MW of datacenter capacity which all came online at once. Given that most Bitcoin datacenters are in the 2 to 20 MW range, and that building something of that size requires a year or longer, I find it quite surprising that all of that hashrate would come online in one week. It was like they flicked a single switch exactly 1 week before the fork. Hashrate rentals are a far more parsimonious explanation for this data than actual hardware.

Stacked chart version: https://imgur.com/ygDmqse.png

Line graph version: https://imgur.com/OeWlXKr.png

The stacked chart indicates that SVPool's early hashrate (Nov 1-8) came from CoinGeek directly. This is not a surprise, since CoinGeek owns and operates SVPool. They probably decided that it looked better to have their 2.7 EH/s of hashrate split between two pools instead of all in one place.

Mempool did not exist until 11-10. I suspect that CoinGeek wanted to split its hashrate up further because they knew that a blockchain with only three pools (two of whom were owned and operated by one person) is not very reputable.

Data source: https://cash.coin.dance/blocks/minerhashratesv (xls download)

Spreadsheet I made from that with graphs: https://toom.im/files/sv_hashrates.xlsx

Edit: Some people are claiming that they could have just moved hashrate over from BTC. However, CoinGeek & Co repeatedly claimed that they were all-in on BCH and did not have any BTC hashrate, and also claimed that they did not rent hashrate. Either way, they appear to have lied. 

Besides, renting hashrate IS moving hashrate over from BTC. The only difference is whether you're paying people to make that happen.
Methodology:

SV: 

    bitcoin-cli getblock `bitcoin-cli getbestblockhash` | grep chainwork
      "chainwork": "000000000000000000000000000000000000000000d3468369ee1512bddd9812",

ABC:

    bitcoin-cli getblock `bitcoin-cli getbestblockhash` | grep chainwork
      "chainwork": "000000000000000000000000000000000000000000d350e3fa6943c314849936",

Fork block:

    bitcoin-cli getblock `bitcoin-cli getblockhash 556766` | grep chainwork
      "chainwork": "000000000000000000000000000000000000000000d3367b433e911be0f8dbb9",

Run python. Take those numbers and prepend 0x to make them interpreted as integers.

    >>> forkwork = 0x000000000000000000000000000000000000000000d3367b433e911be0f8dbb9  
    >>> abcw = 0x000000000000000000000000000000000000000000d350e3fa6943c314849936
    >>> svw = 0x000000000000000000000000000000000000000000d3468369ee1512bddd9812
    >>> float(abcw - forkwork) / (svw - forkwork)
    1.647287174558812

Thus, ABC now has 64.7% more work than ~~ABC~~ SV as of this comment.

Edit: Now 70%.

Edit: Now 90%, as of ABC height 556842.

Edit: Now 91%, as of ABC height 556851.

Edit: Now 96.3%, as of ABC height 556860.
Upgraded to Classic 0.12.
Oh, thanks for that. I actually have 90% written a medium post about how P2pool (yes, p2pool!) is prior art against almost all of "Agent-based Turing Complete Transactions Integrating Feedback Within A Blockchain System", and Ethereum and Counterparty are prior art against everything else. Most of that patent is patenting a "turing complete system" that reads and writes data to a blockchain. In other words, a computer program that uses a blockchain. It's pretty absurd, really.
> It is strange that all pools signaling BU are newly emerged pools.

This may be because there was an unmet demand in the mining ecosystem for BU-based pools. Perhaps a lot of small- and medium-scale miners wanted to support BU, but couldn't do so with the existing pools, so that when new pools were formed, a substantial chunk of the network hashrate switched to them. Old pools may be especially reluctant to switch to BU because they think it might be viewed as a betrayal, and cost them a lot of hashrate from the Core supporters amongst their customers, but new pools are more concerned with attracting new customers than they are worried about pissing off non-customers.
75% support means 3:1 approval.

95% support means 19:1 approval. 

The 95% threshold is a 6.3x higher bar to meet than a 75% threshold. That is a *massive* difference. Using a 95% threshold as the decision criteria strongly biases the system towards inaction. 

You probably like that. I don't. I want Bitcoin to be able to adapt, grow, and progress in a reasonable fashion. I don't want to see Bitcoin become surpassed by some altcoin like Litecoin or Doge simply because we chose a threshold that makes progress unfeasible.

Furthermore, if we used the 95% threshold for a contentious and divisive issue, it might encourage the supermajority to engage in vote manipulation. If, for example, 10% of the hashpower opposed the fork, it would be quite easy for the majority faction to intentionally orphan and 51% attack all of the blocks mined by that 10% minority. I find that scenario distasteful, and prefer to avoid it. The higher we set that threshold, the stronger the incentive to perform this attack becomes. At 95%, it seems pretty strong to me. At 75%, it's weak enough that I doubt it will be an issue. 

Using a high threshold like 95% gives excessive power to minorities. BW.com, for example, controls more than 5% of the hashrate, so they alone could block such a fork. Most people here don't even know who runs BW.com, and have no idea what they stand for. Do we really want to rest the fate of Bitcoin in the decision of such a small entity? (Note: BW.com actually supports the hard fork. It's just an example.)

Using 95% for a non-controversial fork is a good idea. When there's no doubt about whether the fork *will* be activated, and the only question is *when* it will be activated, then the optimal activation threshold is relatively high: the costs (in terms of old nodes failing to fully validate) of a fork are minimized by activating later, and the opportunity cost (in terms of the risk of never activating, or of delaying a useful activation) is small. For a controversial fork, the optimal activation threshold is much lower: the opportunity cost (the risk of making an incorrect democratic decision) is much greater. 

In order to mitigate the cost of early activation (while full node support may still be low), we have a few tools available. First, there's the grace period. We can give everyone some time after the fork has been triggered (via 75% vote) and before the new rules are active. In the current version of Classic, that time is 1 month. Second, we can use the alert system and media channels to warn everybody of the coming fork and encourage them to upgrade. Third, we can coordinate with miners to not switch enough mining power on to cross the 75% threshold if it appears that full node adoption is lagging behind.

With these things in mind, I think that the 75% threshold is about right for this kind of thing, and so I vote to not change it.
Well, they have to write a feature that the developers think is good and worth being merged, **and** intentionally sabotage it in a hard-to-detect fashion.

So it's a lot more work than just being an honest contributor. If you do it once and get caught, people will be pretty skeptical of you for all time, so you might want to not do it with an anonymous github account. Something like cointracker, maybe.

We've had a lot of pull requests get opened on our Github recently. I detected a lot of bugs in them pretty quickly over the weekend and closed them abruptly. 

**[Github Classic closed PRs](https://github.com/bitcoinclassic/bitcoinclassic/pulls?utf8=%E2%9C%93&q=is%3Apr)**

Some had **[incomplete merges](https://github.com/bitcoinclassic/bitcoinclassic/pull/7)**. That could have been a mistake, though. Pretty easy to detect, to be honest.

Some were subtle manipulations to reopen known vulnerabilities, like **[in UPNP](https://github.com/bitcoinclassic/bitcoinclassic/pull/15)**.

Some would have resulted in **[performance regressions](https://github.com/bitcoinclassic/bitcoinclassic/pull/20#issuecomment-172432798)**. I may have gotten angry at PeterTodd a bit. I got the impression that he knew it was a bad idea, and was voting in favor regardless. I suspect Peter Todd's hat may not be entirely white.

Others were things that we already knew to be flawed ideas, like the **[relaying of double-spends](https://github.com/bitcoinclassic/bitcoinclassic/pull/17)** (which worked fine before mempool evictions).

There were a few that got close to getting support, like the **[removal of the alert key](https://github.com/bitcoinclassic/bitcoinclassic/pull/9)**, which got support on consider.it under a distorted pretense. We were able to **[identify the problems with that approach pretty well](https://github.com/bitcoinclassic/bitcoinclassic/issues/27#issuecomment-172995064)**, but not before a **[huge vote in favor of it](https://bitcoinclassic.consider.it/remove-theymos-alert-keys-access)** had been passed. 

Democracy can be dangerous. I will not follow it blindly, I will just let it be my guide.
Bitcoin should not be turned into a file-sharing protocol. 

If you want to timestamp a file's hash and commit that to the blockchain, I think that's a reasonable secondary use of Bitcoin, and it will not significantly bloat the blockchain or add many legal complications. However, directly embedding files into the blockchain does do those things.

Increasing the OP_RETURN size makes it easier to do things with Bitcoin that I think are a distraction from what Bitcoin should be, and will make it harder for Bitcoin to achieve its true goal as universal digital cash.

Most of the people I've seen asking for bigger OP_RETURNs or lower dust limits are trying to do things like embed controversial data (like DeCSS or [Mein Kampf](https://bookchain.cash/)) into the blockchain in order take advantage of Bitcoin's censorship-resistant properties. But there's a big problem with that: Bitcoin is censorship-resistant, but it's not censorship-proof. AML/KYC laws allow governments to choke the on/off ramps to Bitcoin, and while they might not be able to block Bitcoin entirely, they can make it very inconvenient to use. If we make it easy to piggyback illegal data onto the Bitcoin blockchain, that will increase the incentive for governments to try to crack down on Bitcoin. While they won't be able to do much to stop the spreading of that illegal data, they will be able to make Bitcoin hard to use as a currency.

Unless someone can present a desired use-case for large OP_RETURNs that is consistent with Bitcoin's role as universal (programmable) digital cash, and is not simply a scheme for piggybacking controversial data on a censorship-resistant system, and which also cannot be done more efficiently by using hash commitments (possibly merkleized, like with [Factom](https://www.factom.com/assets/docs/Factom_Whitepaper_v1.2.pdf)) I will not support an increase in the OP_RETURN limit.
I've been making pretty decent progress on Xthinner. Xthinner is a new block propagation protocol that [uses LTOR and some other tricks](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0) to improve block compression by about 4x. The implementation is about 60% done. Current status on a block with 250k transactions (i.e. about 100 MB):

    jtoomim@light-star:~/dev/bitcoins/xthinner-newabc/src$ test/test_bitcoin --run_test=xthinner_tests
    Running 1 test case...
    Testing Xthinner on a block with 250003 transactions with mempool size 250002
    Encoding is 310649 pushBytes, 621296 commands, 20763 checksum bytes
    total  409074 bytes, 13.0903 bits/tx
    Single-threaded encoding took 647787 usec, 2591 ns/tx (mempool)
    Serialization/deserialization took 46425 usec, 185 ns/tx (mempool)
    Single-threaded decoding took 394528 usec, 1578 ns/tx (mempool)

    *** No errors detected

The code can correctly encode and decode a vector of transactions. I still need to write the glue code (e.g. dealing with headers and coinbase, handling missing transactions, connecting to the net code) and tests.
Most people in the cryptocurrency space already own Bitcoin. If the community were to switch to a different coin, then we would have to sell Bitcoin and buy Newcoin. A mass migration like this would cause all sorts of chaos in the exchange rate between the two coins and would effectively be a redistribution of wealth based on when people switched. With a fork, on the other hand, each person's wealth is preserved: the amount of Bitcoin held before the fork equals the amount of Bigcoin held after the fork.
I think it would be better to just be paranoid about new submissions. It will give us practice going over everything with a fine-toothed comb, which is a good thing no matter what.
Ooh, this is confidence-inspiring:

"[This is a quick fix because we've run out of time. This must be
implemented properly in the future.](https://github.com/bitcoin-sv/bitcoin-sv/commit/55c993841725690256fd4b7093142ddd8084312a)"
He has a proprietary mining client that runs on clean, renewable hot air. /s
Your numbers are off. The opportunity cost of 4 EH/s in mining BTC is about [$617k per day](https://www.cryptocompare.com/mining/calculator/btc?HashingPower=4000000&HashingUnit=TH%2Fs&PowerConsumption=0&CostPerkWh=0&MiningPoolFee=1) right now, not $1 million.

Assuming your reward numbers are right, BCH miners are losing about $213k/day. BSV miners are losing about $527k/day.
JP Morgan's analysis was based on direct costs, not socialized or externalized costs. JP Morgan's analysis was based on the assumption that the average Bitcoin miner pays the USA average industrial rate. This assumption is false.
Maybe top three or four. The overflow bug was clearly more serious than this, and I think the BDB/LevelDB lock fork from 0.7 to 0.8 was probably more serious too. Crashing on errors is generally safer than giving incorrect results on errors.
Both attackers (segwit and non-segwit) need to include a fee in their transaction that is greater than the transactions they wish to displace. With SegWit, the attacker can make a 3.8 kB SegWit transaction that will get included with the same absolute fee as a 1.0 kB non-SegWit transaction would need to have, or as much as a 1.8 kB "natural" SegWit transaction. The 3.8 kB spam transaction would only prevent 1.0 kB of non-SegWit transactions from being included in the block, but it would still use 3.8 kB of bandwidth and (until signature pruning is implemented) storage. Basically, SegWit gives the spammer around 3.8x as much hardware resource consumption bang for his spam buck.

Without SegWit, a spammer has to pay a fee for 1 kB of transaction space if he wants to use 1 kB of block space. He doesn't get way to make his transactions cost 50% to 75% less than a real transaction of the same size.

Your point is valid in that the fees that everyone would need to pay for blockspace are likely to be higher with SegWit than with a straight blocksize increase, but it remains to be seen whether that effect will be greater or smaller than the 4x discount that intentional spam gets. It's anyone's guess whether 1 kB of spam would be cheaper to send with SegWit or with a straight 2 MB increase. It's pretty clear that fees for everyone else would be cheaper with a straight 2 MB increase, though, and it's also clear that fees with SegWit are relatively cheaper for spammers than for non-spammers.
Actual miners are lazy. They don't like to change pool information on their hardware very often, because (unless you are good at shell scripting or have paid for someone to develop a batched management interface for you) that would require logging into each of your machines and copy-pasting in new data to a web form and clicking submit. A typical mining farm will have hundreds to tens of thousands of these machines.

The approach that Slush is taking is different. Rather than requiring users to reconfigure each machine, Slush is giving users a way to switch all of their hardware between Classic and Core by clicking on one button on Slush's website. This should result in much faster changes.
The current fees of 0.1 mBTC/kB are small and reasonable to most users. I think a scenario in which fees remain about the same is a reasonable one. Fees could go up or down. If they go up too high, people won't want to use Bitcoin, and transaction volumes will go down.

I think that increasing transaction volume is easier and better than increasing fees per kB, so the scenarios I draw for the future reflect that.

Letting the hashrate fall by more than 50% due to economic factors is dangerous for bitcoin. See https://www.reddit.com/r/Bitcoin/comments/3i2hke/august_23_2015_block_size_and_hard_fork_dialog/cuctxvg for an explanation of why. Currently, it costs about $4,000/block to maintain our 300 MW mining network. At current block sizes, that translates to about $4/transaction. Eventually, we'll have the choice of either letting Bitcoin become easy to 51% attack, increasing fees to $4/transaction, or dramatically increasing the on-chain transaction volume. I think the third option is the best one.
83 to 73 sounds like it could just be variance. Do we have any evidence showing otherwise?

My guess is that people are watching http://litecoinblockhalf.com/segwit.php, which shows a 24-hour average (576 blocks). The amount of variance over a 576 block interval is around ± 5%. A swing from 83% to 73% is about the size of the confidence interval for a 24-hour estimate of the SegWit support.

Edit: Support is back up to 76% now. Either Jihan turned some of his miners back off, or perhaps 73% was just a low point caused by variance.
I like to think ahead. One of the things I'm worried about with Bitcoin is what happens if the reductions in block subsidy results in > 50% of the hashpower going offline. In that case, it will be very cheap for a malicious entity to rent or purchase enough hashrate just long enough to perform a 51% attack. 

Off-chain transactions like Lightning Network reduce miner fees, which makes the eventual problem of paying for the mining network bigger. However, if we scale up the on-chain capacity by about 100x to 1000x, we can pay enough fees to support the current mining infrastructure indefinitely. This is one of the main reasons why I support BIP101.

Mostly, though, I just wanted to illustrate that a 0.25% miner like me can afford the bandwidth of BIP101's blocks even without any technological advancement. In 2016, 8 GB blocks would be expensive but economically feasible. In 2036, 8 GB blocks probably won't even be expensive.
It's okay. We won't trust the response; we'll verify.
I'm a professional miner. I spent about $3 million last year on mining hardware. 

It is my opinion that 128 MB blocks are currently not at all safe.

I can't buy hardware that will make it possible to mine blocks larger than 32 MB without unacceptable orphan rates because the hardware isn't the limitation. The software is.

Once we fix the inefficiencies and serialization issues in the software, we can scale past 32 MB. Not before.
However, unless you edit the p2pool source code, it will not generate version 0x30000000 blocks, and consequently will not vote for the fork. I'll make a branch of p2pool soon that resolves this issue.

Edit: https://github.com/jtoomim/p2pool/commits/jtoomim_performance

    git clone https://github.com/jtoomim/p2pool.git
    cd p2pool
    git checkout jtoomim_performance

This branch also has better performance when handling high transaction volumes. Expect about 50% lower CPU usage than the main p2pool branch.
I generally prefer not to accept donations for my work. I don't need the money, and think it's better for people to give donations to other people who actually do need it.
Math check: Alipay handles about 40% of China's total payments. On Singles Day, they saw a peak of [1 billion transactions](https://www.techinasia.com/alibaba-alipay-1-billion-transactions) in a day. That translates to 11574 tx/sec average, or about 2.7 GB per block average for 24 hours. That's for 40% of a 1.4 billion person country, or roughly 560 million people. If you needed terabyte blocks for a single city, that city would need to have 1000 GB/2.7 GB*0.560 billion = 202 billion people in it. 

I don't know of any single cities with populations of 202 billion. The comic is off by a factor of 10,000.
I do not like this us-vs-them attitude. 

Most of the Core devs are good smart people who happen to be making some bad decisions. They are trying to do what they think is best for Bitcoin. They're just wrong about what that is.
Why not a SegWit soft fork instead of a blocksize increase hard fork? Here are my opinions. ([Cross post](https://np.reddit.com/r/Bitcoin/comments/3ygu5d/blocksize_consensus_census/cydpcob) from /r/btc)

1. SegWit is a lot more complicated than a simple blocksize increase, and has been under discussion and investigation for a much shorter period of time. I am not comfortable with it being deployed on a time scale that I think a capacity increase should be deployed on. 

2. SegWit would require *all* bitcoin software (including SPV wallets) to be partially rewritten in order to have the same level of security they currently have, whereas a blocksize increase only requires full nodes to be updated (and with pretty minor changes).

3. SegWit only increases the capacity for typical transaction loads (mostly P2PKH, some multisig) to a maximum effective size of 1.75 MB. Achieving this increase requires 100% of new transactions and wallets to use SegWit. With 50% adoption, the capacity increase may only be 37.5%. (Previous rollouts of new transaction formats have taken about 1 year for widespread adoption.) On the other hand, SegWit increases the capacity for blocks full of specially crafted multisig transactions to nearly 4 MB. This means that it eats up a lot of our safety headroom (e.g. for adversarial conditions) while only providing a very modest increase in typical capacity. This seems like it might actually be intentional, as it makes the types of transactions that will be needed for Lightning and Blockstream products like sidechains relatively cheaper, and will not benefit on-chain transactions much. The reduction in headroom will also make any subsequent blocksize increases harder to gain political support for, which also may be intentional. This concern can be addressed by limiting the total size of SegWit block+witness to around 1.75 MB.

4. SegWit makes more technical sense as a hard fork. As a soft fork, it would be deployed by putting the merkle root of the witness data tree into the coinbase message or into an OP_RETURN transaction somewhere. The coinbase message field is already quite cramped with block height, extranonce1 and extranonce2, merged mining information, blocksize vote information, and other miner/pool-specific data, and I believe that the remaining space should be reserved for miner use, not for developer use. An OP_RETURN transaction sounds technically preferable, but still pretty crufty and nasty. Both options would have the result that all witness proofs would require including the merkle path to the transaction in question plus the transaction in question, and would increase the size of such proofs by about 50%. Putting the witness data in a sibling tree to the transaction data makes way more technical sense, makes the whole system easier to code for in all wallet and Bitcoin software that will ever be written, and reduces the size and complexity of verifying the witness proofs. However, doing so would require a hard fork, which is what the Core developers are trying so desperately to avoid. Doing SegWit initially as a soft fork then moving the SegWit merkle root later with a hard fork is an option, but that would permanently commit SegWit data to both places in different blocks in the blockchain, and consequently would require all Bitcoin software ever to be written to be able to read SegWit data in both locations in order to be able to complete initial block download.

5. SegWit makes more security sense as a hard fork. With SegWit, all Bitcoin wallets would no longer be able to accurately verify new transactions, and all Bitcoin software would need to be updated to be able to verify the new transaction+witness data format. With a soft fork, old wallets will see all new transactions as being valid regardless of whether they are actually valid or not. Pieter Wuille (sipa) makes the case that this will not result in any actual dangers to most users because miners will be selfishly honest and will only create blocks that are valid under the new rules. With a hard fork, old nodes would still fully validate transactions according to the rules that they were written for, and would not see new transactions or would mark them as invalid. They would remain on a separate and fully validating blockchain that lacked economic and had nearly zero mining. I think it is preferable to indicate the consensus rules have changed by moving mining and economic activity off of the old rules rather than to write the new rules in such a way that old nodes are relying on their trust in miners instead of verifying the rules themselves. I think that with a soft fork, nodes and software will not get upgraded as fast because the old code will still be dangerously mostly compatible, whereas with a hard fork, people will notice that they're not getting new blocks and upgrade because that's the only way they can maintain functionality.

Note that the FAQ linked to by OP addresses some of these objections. I present the objections here so that people can evaluate for themselves how well the FAQ addresses each one.
This was not intended to be an accurate performance test. It was just the results I got after getting the code to work for the first time ever. Apparently, Bitcoin.com and Coinspice thought that was newsworthy. The full description is here:

https://old.reddit.com/r/btc/comments/bas60b/by_the_power_of_ctor_xthinner_is_now_working_with/

The test setup for this was one node with only 2 peer connections (one to a regular mainnet node, one to the test recipient), and another node with only 1 peer connection. I expect that the low connection count, combined with the ~5 sec/hop random trickle delay over two hops, meant that transactions were unusually delayed and the mempool for the recipient was unusually sparse. The 99% figure is including the transmission of those transactions that were missing from mempool, so it's likely that that metric will go up considerably in a more realistic test scenario. I'm expecting around 99.5% to 99.7% for blocks larger than 1 MB, as Xthinner generally encodes the average TXID using 12 to 16 bits (depending on block and mempool size), rather than 48 bits for Compact Blocks. Note that on a medium-sized block with a properly synced mempool, Xthinner was able to get 99.54%.

> something like half a percent of additional savings compared to what Bitcoin does

If Xthinner takes compression from 98.61% to 99.60%, that's a 71% reduction in bytes transmitted, or performance that's 3.47x as good. 

Adding and subtracting percentage figures is quite misleading in this context. There's an enormous difference between 99% and 99.999999%. That's not a 0.999999% change, it's a million-fold improvement.

> It also claims that a wopping 25% of blocks are requiring a round trips.

Actually, it was worse than that. Only 25% did *not* require round trips in this test setup. It turns out that setting up chains of nodes with only 1 or 2 connections is bad for performance. What a surprise.

Xthinner's protocol is pretty similar to Compact Blocks except for how it encodes short IDs and checksums. If a normal BTC node is able to get 94.5% reconstruction success in 0.5 RTTs, Xthinner should be able to do the same.

> at the cost of being easily pessimised by txid partial collision attacks?

Xthinner is not as susceptible to attacks as you might think. Xthinner checks mempool for partial collisions at the time the block is encoded, and includes enough bytes adaptively to ensure that no collisions occur if mempools are synced. In order to cause collision risk, you'd have to broadcast transactions such that the encoder doesn't receive the colliding tx until after they've sent the message, but before the receiver has received and attempted to decode the block. Moreover, there are checksums embedded in Xthinner that are computed on a byte index of the TXIDs that is randomly chosen for each connection and/or block, which can be used to disambiguate between colliding transactions  (though the implementation of that logic is not yet written). To aid this reconstruction logic, we could try the first-seen tx in cases where a collision occurs, and check the checksums to verify that it was correct. Even if these defenses are never implemented and the current implementation is all we'll ever have, the worst that an attacker can do with a forced collision is to force a round trip that downloads the individual TX that was collided against. In summary, collision attacks are hard to pull off, easy to defend against, and cause little damage, so I do not think that adversarial collision risk is a significant problem with Xthinner.

If there's a collision vulnerability that you had in mind that is more serious than this one, I'd be interested in hearing it.

/u/throwawayo12345 thanks for the tag.
JPMorgan's analysis is wrong. I mine Bitcoin in the USA for less than half of their estimated cost.
>what is alpha software

It's what you release to the world when you've run out of time, apparently.
No block size BIPs have won. 

- BIP100 has a lot of expressed support among miners (via coinbase message voting), but it has not been implemented.

- BIP101 was implemented in BitcoinXT and in the BigBlocksOnly patch to Core, but it has not yet gained much support among miners. It is not clear to what extent the lack of support for BIP101 is a vote for the Bitcoin Core development team vs a vote against the technical aspects of BIP101 (e.g. the 20-year block growth pattern).

- Consensus among miners clearly supports an immediate increase to at least 8 MB blocks, which both BIP100 and BIP101 provide. The Bitcoin Core project has several developers who strongly oppose 8 MB blocks, making it currently impossible for any changes to support 8 MB blocks to get merged due to the consensus rules followed by Bitcoin Core. Most of the developers who support >= 8 MB blocks have left Bitcoin Core in favor of BitcoinXT; for the most part, only Jeff Garzik remains.


The results of miner voting on blocksize changes can be seen here:

http://data.bitcoinity.org/bitcoin/block_size_votes/7d?c=block_size_votes&r=hour&t=bar

> but suddenly it seems like the blocksize-discussion on this board came to an end. What happend?

As for this, when this developer exodus happened, the moderators of this board (primarily theymos) decided that BitcoinXT was an altcoin, and banned discussion of it. A temporary ban was also placed on all discussion of blocksize-related issues. That ban was subsequently partially lifted. Currently, blocksize issues can only be discussed in this weekly thread on this forum.


It would on BTC because of FIBRE.
[AcceptToMemoryPool bottleneck](https://www.reddit.com/r/btc/comments/9c8tv2/either_atmp_or_scalecash_is_bottlenecking_the/), 

This issue [was known](https://www.reddit.com/r/btc/comments/9bbvwe/bitcoin_sv_alpha_code_published_on_github/e51ymw7/) in advance. I expected [some problems](https://www.reddit.com/r/btc/comments/9b3827/the_gigablock_testnet_showed_that_the_software/e52yhcm/) like this, so the mediocre performance was not a surprise.
> why is all this fighting going on?

Because SegWit is not a good short- or medium-term solution to the scaling problem. SegWit has a lot of good stuff in it, but the element that allows for immediate increases in capacity -- the 0.25x discounting function for witness data -- is not a good thing.

SegWit provides 1.375 to 1.75 MB of capacity in the near future (for 50% to 100% wallet adoption). It also comes with a 4 MB adversarial condition. This means that miners and network engineers have to design their systems to be able to handle 4 MB blocks without bogging down, but we only get to actually use about 40% of that capacity.

This 4x adversarial case will make it very difficult to increase the blocksize limit in the future. With a 1 MB base block size, it's 4 MB, but with a 2 MB base block size, the adversarial case would be 8 MB. 

On the other hand, with a simple block size increase hard fork, we get 2 MB of capacity with a 2 MB adversarial case.

The 4x adversarial case is a direct result of the 0.25x discounting function that the current version of SegWit uses. The way this works is that SegWit says that one byte of data in the base block (what we currently think of as *the* block) is equal to one byte, but that one byte of data in the witness block is 0.25 bytes. This is an accounting trick, not compression: each byte in the witness block still has to be sent over the network as one byte, and it still takes up one byte of disk space; the only place where we are counting it as 0.25 bytes is in the accounting for the block size limit.

I don't like this accounting trick. I think it has harmful effects on the network's ability to scale. I also see it as a sort of tax subsidy for Lightning Network and sidechains. I don't like that subsidy. I would rather see those technologies compete on their own merits, not because we changed the fee rules to be biased towards them.
Writing blocks to disk is sequential. Checking transaction validity is not.

You need to use SSDs for the UTXO set. This is `~/.bitcoin/chainstate/*`. The UTXO set is a LevelDB map (i.e. prefix tree) of all (TXID, index) -> (output script, value) mappings for the currently unspent transaction outputs. Reading and writing to the UTXO set is the main bottleneck on syncing a full node from scratch unless you cache the whole thing in RAM. The UTXO set size increases with larger blocks and increases over time. Right now, with 1 MB blocks on BTC, we have a UTXO set size of about 3.1 GB on disk, or about 8 GB in RAM. With 1 GB blocks, after a few years we would use about 3 TB of SSD space or 10 TB of RAM.

Processing these UTXOs will require handling an average of about 30k database reads and 15k writes per second *without* the 13x factor for spikes that you use elsewhere. Each read and write can require multiple SSD accesses (trees are O(n log n), so total throughput requirements for the disk array might be 450k random access IOPS without any spike capacity. This is well beyond HDD speeds, and beyond even mid-range SSDs. [That kind of performance](https://www.storagereview.com/liqid_element_aic_review_768tb) isn't cheap to get. This requirement can be reduced if you use a ton of RAM as a UTXO cache (i.e. using the `-dbcache=xxxxxx` command-line option), but this would probably require hundreds of GB or terabytes of RAM to be effective.

Your bandwidth usage numbers are also off by a substantial margin. Most of the node's bandwidth isn't used by sending and receiving transactions; it's used by *announcing* those transactions or uploading historical blocks. If a node has 100 peers, it only has to send and receive a (e.g.) 400-byte transaction once on average, but it has to announce that 32+8 byte TXID (with the lowest possible overhead) 100 times. This means the node will use 4 kB for announcing the transaction, but only 0.8 kB sending and receiving it. For historical blocks, if we assume that the average full node operates for about one year, then your node will have each historical block requested from it on average once per year.  This becomes more burdensome as each year passes. The first year, you would upload historical blocks at 0.5 times the bandwidth used for transactions in current blocks. During the second year, it would be 1.5x. During the tenth year, it would be 9.5x.
Yep. BCH is still recovering from the November '18 split between BCH and BSV, where the price fell from $450 to $120. The markets are only now beginning to recover from that.

Bitcoin trading is heavily influenced by bots that trade on the margin and effectively suppress price changes. These bots have limited capital liquidity, so while they're able to keep the price nearly constant in the face of low-volume bear/bull markets, once the volume saturates their available liquidity they are ineffectual and the price changes dramatically, releasing the pent-up pressure that was building in the weeks or months prior. In this instance, the bots were saturated by BTC's volume, and once that happened all currencies' values were able to freely float, and BCH just happened to have more pent-up growth pressure than the others.
I don't usually accept donations or outside funding for my work. I prefer to be accountable only to myself.
No serious miners pay $0.10/kWh. At my 2 MW mine, for example, I pay $0.0285/kWh. 

Just because residential rates are around $0.10/kWh doesn't mean that miners, who seek out industrial rates in the cheapest regions that exist, pay that much.

If you're calculating the cost as $0.10/kWh, then BTC mining would also be unprofitable. BTC mining was about $0.092/kWh until a few days ago; now it's $0.07/kWh.

The most correct number to use in this context is the opportunity cost of mining BTC, as I used in my calculation. If you don't use that, then you should use a number that better reflects industrial miner rates, like $0.05/kWh all-in.
Nice find, BitcoinXio. Do you have the resources you need to watch them closely for vote manipulation, and temp/permaban them if found guilty?

I support freedom of speech and open discussion and being tolerant of minority opinions, but I draw the line at using bots to manipulate the discussion. 

"One user, one vote" should not be violated.

That said, proposing publicly the creation of bots falls short of a permaban-worthy offense in my view.
My reasons for not liking him:

1. He's a poor communicator of technical concepts. While he has occasional valid insights, he is so bad at communicating them that it's extremely difficult to tell them apart from him just being wrong, which happens with approximately equal frequency.

2. He's a poor communicator with respect to social cues. In other words, to be blunt, he's a bit of an asshole.

3. His position is so extremely pro-huge-block that it seems to be divorced from reasonable engineering realities. There are a half dozen technical hurdles left in implementations before 128 MB blocks are safe and feasible, and yet he wants to do them by November. This seems really foolish and childish. I want it now! No.

4. He divides the community with points 1-3 above. This makes it difficult for the BCH community to resolve issues harmoniously and stay essentially unified. If you are a believer in [Metcalfe's law](https://en.wikipedia.org/wiki/Metcalfe%27s_law) as applied to cryptocurrencies, splitting the community in half without good technical reason would result in each of the resulting networks having a quarter of their former value. (This interpretation of Metcalfe's law appears contrary to data from the ETH/ETC and BTC/BCH hardforks, in which the sum of the resulting currencies had greater value than the pre-fork currency did; however, that might just be the result of anxiety about the forking process itself dissipating after the fork went smoothly.)

It's possible for someone to have the best intentions and still do harm. I think CSW is an example of this for BCH.
I am not of the opinion that Lightning will solve our problems.

My opinion is that **misunderstanding and/or misrepresenting Luke-Jr's argument and then demonizing him based on that misrepresentation is wrong**. It lowers the standard of discourse and causes each side to hate the other side for the wrong reasons.

The position that Lightning Network adoption will reduce both block sizes and fees is not absurd. It's possible that it is an incorrect position (as I believe it to be), but it's not absurd.
Note also that all of the performance bugs SDL found in this article are implementation issues, not protocol issues. All of them can be fixed by a simple patch to bitcoind with no additional forks required.
I think Peter R was trying to make a distinction between a central property (in the sense of an essential, sine qua non property) and a property that people care about. 

The 21 million coin limit is not a technically unchangeable feature of Bitcoin. It's just a couple of constants in the code that keeps it what it is. As such, I would not consider the fixed money supply to be a central property of the Bitcoin technology. The PoW system is much more of a central property of Bitcoin. Even the Script processing language is more central to Bitcoin than the 21 million coin limit. 

On the other hand, the 21 million coin limit can be construed as a central property of the social contract that Bitcoin investors and users bought into. People made their decisions to buy or mine Bitcoin based on the fraction of the total currency which they expected to receive. Changing the limit but retaining the current issuance system changes that fraction post-hoc. This would be a violation of trust, and would piss everyone off. Therefore, it won't let it happen.

Most users of Bitcoin don't really care about the Script processing language. They just use it to send money from address A to address B, and aren't concerned with things like multisig, time-locked transactions, and Coinjoins. Even though Script is a central property to how Bitcoin works, it doesn't matter to most people. On the other hand, the 21M limit is a peripheral property of Bitcoin--it's just a couple code constants based on arbitrary decisions that Satoshi made--but it matters a lot to every Bitcoin user.

The fixed coin supply in Bitcoin has always been a valid topic of debate, and it has always been something for which around 90% of people have the same opinion. Because sentiment is overwhelmingly in favor of keeping it fixed, it will remain fixed.
Thanks to /u/chris_pacia for repeating /u/deadlnix's recognition of my citation of [the data](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/), but I didn't collect it, /u/jonathansilverblood did. I just [pointed out](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/e58k9wu/) those numbers from his dataset.
Bitcoin Cash full nodes will reject a transaction if it can be detected as a double spend. After five minutes, all full nodes will have the original transaction, so they will know the second one is a fraud. This will make it so that all miners should agree on which transaction is valid and which one is not, so the 5-min transaction will not get mined.

If the two transactions are sent within about 3 seconds of each other from different locations, then there will not be universal agreement on which one came first. That's the scenario this alarm is designed for.

If someone rents 400 PH/s in order to mine a block themselves containing their transaction, they should be able to pull off the double spend about 10% of the time. And it would only cost them about 1.25 BCH in miner rentals for each of those attempts. Of course, the customer would need to hide their face and identity from security cameras in order to avoid legal retribution.

tl;dr: If someone comes into your store wearing a balaclava and asks to buy an item valued at more than 1.25 BCH using a 0-conf transaction, you should politely ask them to wait for one confirmation before allowing them to leave.
No, the difficulty does not make 51% attacks more viable. In order to do a 51% attack you need 51% of the currently active hashrate, regardless of the difficulty. It doesn't matter if you're mining one block an hour or one block a second as long as you are mining blocks faster than everyone else.

Lower difficulty will mean that other miners (like me) who have so far been holding off of Bitcoin Cash will join the mining network, thereby increasing the network hashrate. This will make 51% attacks harder.
Clearly, my ego is so large it warps spacetime.
I'm not doing it for XT. I'm doing it for Bitcoin.
I've presented another anti-DoS mechanism in https://www.reddit.com/r/Bitcoin/comments/3i2hke/august_23_2015_block_size_and_hard_fork_dialog/cucvg2f. A hard protocol-level blocksize cap is not the only way to protect against DoS attacks using large blocks. It's just the oldest way.

Selfish miners generally prefer small blocks over large blocks. In order for selfish mining to work, a selfish miner has to be able to publish several blocks in a short time period, and they have to be able to do it in response to the competition getting close. With large blocks, you lose control over the publication time. You have to start broadcasting the large block early enough that it's finished before the rest of the network catches up. Without the ability to control the publication time for large blocks, I think the potential gains to be made by excluding low-bandwidth miners would be balanced or outweighed by the higher orphan rates. I'm not sure about that, though. Do you know how to do the math to compare those effects?

Large blocks have higher orphan rates because they propagate across the network more slowly than small blocks. In a worst-case scenario, a large-block miner might completely clog up the network, limiting the block rate to the rate at which they could be downloaded by 51% of the mining network. In this case, a miner that creates 8 kB blocks will have a lot more blocks incorporated into the chain than a miner that creates 8 GB blocks. The solution to dealing with large-block spam is for miners to simply not download those blocks, and at the extreme point, that's just the natural result of dealing with congested pipes. A selfish large-block miner cannot DoS more than 51% of the hashrate at once.

If the high-bandwidth pools are in the hashrate majority, then they can beat bandwidth-constrained miners. If 10% of pools do not have enough bandwidth to keep up, they have a few options:

1. The 10% of miners using those pools with poor connectivity can switch to pools with better connectivity.
2. The pools can switch to SPV mining, only downloading and verifying the headers before beginning to mine on top of it. If the pools notice a significant portion of large blocks with errors in them, they can blacklist nodes that repeatedly publish invalid blocks. Nodes can set a flag to indicate whether they have verified the block they are forwarding. Mining nodes can give priority to blocks that are verified over blocks that aren't. This would further slow down the propagation of large blocks and increase their orphan rates further.
3. The pools can stop being such cheapskates about their bandwidth bills, and upgrade their systems to be able to handle the BIP101 capacities. This should not be very expensive for medium-sized miners (0.01% to 1.0%), as I have argued elsewhere.
4. The pools can give priority to downloading small blocks. Large blocks will tend to lose orphan races. Selfish miners with unlimited bandwidth will have to balance the higher orphan rates against the higher fees and potentially the selfish mining benefits.
5. The pools can choose to only download (or build a chain on top of) large blocks if they are the parent of another block. This would be like selfish mining, but against the large-block publisher. It would be an attempt to intentionally orphan large blocks in order to disincentivize their creation.

Ultimately, these 10% of pools may fail. If that happens, the remainder of the Bitcoin mining network will have higher performance. I am not certain this is a bad thing.
No, previous versions will also still sync the chain. No blocks have been mined in which a single transaction spends the same inputs twice. It's only if (a) the new version allowed that, (b) old versions disallowed that, and (c) if it actually happened that we would have a problem.
Yes, CTOR should not be a priority right now. Graphene by itself should be enough to take us from 32 MB to around 256 MB, at least for the block propagation bottleneck. After 256 MB, Graphene may need to be supplemented with some canonical order rule to improve efficiency.

Note: we observed today that on average, [37 of the 43 kB](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/e58k9wu/) per block in Graphene messages is order informataion that would be eliminated by CTOR. Now, 37 kB is not a lot at all, but it's still 86%, and as we scale it  eventually might grow to the point where it matters.
The bigger concern is that Mark Karpeles may have had the key, which means the Japanese government may now have the key.

https://github.com/bitcoin/bitcoin/pull/7692#issuecomment-197967429

I don't think most devs on Core care about Gavin having the key.
Is there a specific reason to use logical shifts in the new opcodes instead of arithmetic shifts? Satoshi Nakamoto's version used arithmetic shifts, but you're using logical shifts. Why was Satoshi's version wrong?
> ...When multiplied by the thousands of concurrent users? It would be significant.

No, it wouldn't. A quad-core CPU, running finely-tuned ASM code, can do about 20 MH/s. A current-generation ASIC (Antminer S9) can do 13,500,000 MH/s, and earns about $0.60/hr. So with 1 million users, assuming your javascript code is just as fast as ASM, and assuming all of your users are on high-end desktops, you might get around $1/hr. 

On the other hand, if you just showed ads to that 1 million concurrent (assuming one page view per user hour, or 1m views/hr, and 1 ad per page), you'd get [around $2,800/hr](https://monetizepros.com/display-advertising/average-cpm-rates/).
I think this proposal is intellectually interesting, but crufty and hackish and should never actually be deployed. Writing code for Bitcoin in a future in which we have deployed a few generalized softforks this way sounds absolutely terrifying.

Instead of this:

    CTransaction GetTransaction(CBlock block, unsigned int index) { 
        return block->vtx[index];
    }
    
We might have this:

    CTransaction GetTransaction(CBlock* block, unsigned int index) { 
        if (!IsBIP102sBlock(block)) {
            return block->vtx[index];
        } else { 
            if (!IsOtherGeneralizedSoftforkBlock(block)) { 
                // hooray! only one generalized softfork level to deal with!
                return LookupBlock(GetGSHashFromCoinbase(block->vtx[0].vin[0].scriptSig))->vtx[index]);
           } else { 
               throw NotImplementedError; // I'm too lazy to write pseudocode this complicated for reddit.
        }
    }
    
    bool IsBIP102sBlock(CBlock* block) {
    // ...
    }

    bool IsOtherGeneralizedSoftforkBlock(CBlock* block) {
    // ...
    }

    CBlock* LookupBlock(uint256 hash) {
    // ...
    }
    
    uint256 GetGSHashFromCoinbase(CBlock* block) {
    // ...
    }

It might be possible to make that a bit simpler with recursion, or by doing subsequent generalized softforks in a way that doesn't have multi-levels-deep block-within-a-block-within-a-block stuff. Still: ugh.

        
Now we just need to finish collecting and analyzing the data.
Compression shorts, maybe.
Can'tstopthenople?
More to the point, Antpool easily has the power to veto SegWit. Antpool has pledged not to run SegWit until the Core team (mostly Luke-jr) releases HF code, as per the HK consensus meeting. It does not sound like Core has made progress in that respect.
... they tagged me in a message a while later by mistake. I read my notifications.

Also, I've been visiting the Core slack on occasion to talk with their developers. Because I am in favor of collaboration, and keeping Bitcoin from crashing and burning due to dishonest and adversarial behavior. Like this.
We have **/r/bitcoin_classic**. 

Mind your step. Don't trip on the underscore.
Smart contracts? What about 21's device enables smart contracts per se? It seems to be an attempt at a micropayment enabler, not a smart contract enabler. Smart contracts require that you have a programmer write a bitcoin transaction that will only be valid when the conditions of the contract are completed. The 21 computer does not do that as far as I have read.

I'm working on Xthinner with CTOR for ABC. I suspended work on that a few weeks ago because of the fork, though. I figured it was probably better to spend my time preventing BCH from being taken over than to spend my time adding performance that we don't yet need.

Currently, I have the encoding algorithm running fairly well for TXIDs based on the mempool. I do not yet have decoding or serialization/deserialization working yet, nor have I written the code for handling missing transactions and checksums. I'd say it's about 25% done.

If anyone wants to help, let me know and I can upload the WIP to my Github and help you get started.

Edit: Made some good progress over the last few days. Xthinner can now encode and decode transaction lists. Encoding a 1-million tx block with a 2-million tx mempool takes about 1.2 seconds. Still need to implement serialization, checksums, deal with the coinbase transaction and block header, write tests and do security review. Maybe 33% done so far.

Compression ratio looks like it will be about 99.5%. That's pretty competitive with Graphene+CTOR, actually.
That's just statistical noise. You need a 14-day averaging window to get an estimate of hashrate that even remotely decent. Even then, there's still going to be a few percent variance in the estimated hashrate.

Those estimates are probably daily estimates; 10% fluctuation in daily estimates are commonplace.
Decent desktop machines actually outperform high-end servers in single-threaded performance. A good desktop CPU will typically have boost frequencies of around 4.4 to 4.8 GHz for one core, but only have four to eight cores total, whereas most Xeon E5 chips can do around 2.4 to 3.4 GHz on a single core, but often have 16 cores in a single chip.
It's not ideology or malice; it's economics. A block 100% filled with 5 sat/byte transactions only provides 0.05 BTC of revenue. That adds about 0.4% to the total revenue of a miner. If the miner has a slow internet connection, 1 MB of data in their blocks can significantly slow block propagation and increase their expected block orphan rate by more than 0.4%.

In the past, I've set a fee floor of 5 sat/byte for my mining hardware (via p2pool) when the CPU cost of processing those transactions was causing significant delays in switching blocks. The performance of bitciond has been improved since then, so a fee floor of 1 sat/byte might make sense for me now, but I have faster internet connectivity than many of my competitors.
Fee estimation is based on the recent past. Transaction confirmation times depend on the near future. When the future is unlike the past, the fee estimation algorithms make mistakes.

If you send a transaction in the early morning (European time) on a Monday, your wallet will look at the network traffic Sunday night, see it was low, and decide that a low fee is necessary. Then, as the Monday daytime traffic ramps up, the requisite fee will increase, and the fee recommended to you by your wallet may turn out to be insufficient.

If you send a transaction late on Friday night, then your wallet will see the high Friday daytime traffic, and will assume that a high fee is needed. As traffic slows down for the evening and the weekend, the requisite fee will fall, and you will end up overpaying.

Ultimately, when blocks are congested with e.g. 10% more transactions than there is block space, you need to pick a fee that is higher than e.g. 10% of other transactions. The hard part is that everybody else, including that 10%, wants to do the same thing. Everybody and their wallet is trying to guess what everybody else is trying to guess that everybody else is trying to guess (etc) that the optimal fee will be. Economists call this scenario a [Keynesian Beauty Contest](https://en.wikipedia.org/wiki/Keynesian_beauty_contest). There is no optimal strategy for this problem, because as soon as other people start to use one strategy, the best strategy to use becomes one that takes their strategy into account.

Most wallet developers consider unconfirmed transactions to be a worse user experience than high fees, so they use default fee settings that make the unconfirmed scenario unlikely. In any case, it's a trade-off. Unless you can predict the future behavior of other wallets better than the other walelts can, the only way to achieve confirmation certainty is to overpay. 

Ultimately, it's not a solvable problem. It's a competitive guessing game. No matter what, fee estimation algorithms will result in some people's transactions being left unconfirmed, and other people's transactions paying too high of a fee.
> Where they sold zcash hashrate at astronomic prices

We **[held](https://forum.z.cash/t/cloud-hashing-auction-toomim-bros/2320)** an **[auction](https://forum.z.cash/t/cloud-hashing-auction-1b-200-h-s-available-for-3-months-bids-only/2457)**. Our customers set the prices. We thought the prices were too high, so we tripled their hashrates for free a couple days later (and doubled it twice more later on, for a total of 12x).

> didn't even have any of it working for the first two weeks of Zcash

This is factually incorrect. It was two days, actually. After four days, we were delivering about 6x as much hashrate as our auction customers had paid for (or 2x as much for non-auction customers).
> Zcash-cloud-mining-scammer

I suppose I should reply to this, as it's going to come up again.

Around October 8th-10th, we **[held](https://forum.z.cash/t/cloud-hashing-auction-1b-200-h-s-available-for-3-months-bids-only/2457)** an **[auction](https://forum.z.cash/t/cloud-hashing-auction-toomim-bros/2320/51)** for a total of 400 H/s of 3 month Zcash cloud hashing contracts. The final price set by the auction ended up being 180 mBTC/(H/s), which in retrospect was absurdly high. A few days after the auction, we multiplied their hashrates by 3x.

We advertised our contracts as non-refundable, since buying cloud hashing is effectively a bet about the future network hashrate and exchange rate, but the way I phrased that clause wasn't very clear for the auction orders and a few customers misunderstood it.

About 10 days later, we started making web sales of contracts at around 1/3 the auction price, with adjustments to our price made based on how quickly we sold out. These contracts were clearly advertised as non-refundable.

Around Oct 26th, some of our customers started getting cold feet as it looked like the hashrate was going to be much higher than they had guessed, so people started asking for refunds. We refused those requests, as they were motivated by the desire to avoid a risk that they willingly accepted earlier.

Both sets of contracts were specified to begin on Oct 28th, Zcash's launch day. When launch day came, we had some bugs that delayed the start of our contracts by about 2 days. We offered to compensate our customers with 20x the amount of ZEC that they would have earned on the first 2 days. Most of our customers accepted this compensation, and we have delivered ~~most~~all of those compensation payments.

Shortly afterwards, we doubled our hashrate delivery to 2x (web orders) or 6x (auction orders). Around Nov 14th, we doubled it again, and we are currently delivering 4x (web) or 12x (auction) as much hashrate as our customers paid for. Currently, we have delivered 3.26x as many hashes since Oct 28th as we were supposed to have by now.

Even with the compensation and overdelivery, the cloud hashing contracts will likely not be profitable due to our customers' misprediction of the network hashrate during the auction. Many have asked for refunds, citing the 2 day delay as a breach of contract. I have refused to give refunds, because a 2 day delay would likely be classified legally as a minor breach of contract (for which compensation for damages are due) rather than a material breach of contract (for which the contract is terminated and compensation for damages are due), and because the main reason they are asking for a refund is the change in market conditions (which is a risk they accepted) not the delay in contract starts.
But still defaults to enabled.
We had help with this one. Actually, Mike Toomim and I didn't even initiate this project; I was approached for it by two fellows who should be unveiling themselves soon, and dragged my brother in for help making it more democratic.
The average cost of electricity isn't relevant. The cost to the average miner is what matters. Miners, as a class, pay lower-than-average rates.

The JP Morgan analysis assumes that miners pay about $0.07/kWh, because that's the average industrial electricity rate in the USA. But that's silly. Miners are like aluminum smelters. They don't say, "My home is New York City, so I'm going to build an aluminum smelting plant here regardless of what the power costs are." Instead, they say "My home is New York City, but I want to build an aluminum smelting plant, so I'm going to move to upstate NY near Niagara Falls to take advantage of the dams and excess hydropower there."

Electricity is the primary cost for smelting aluminum, so aluminum smelters have sought out the cheapest reliable power they can find. They pay around [$0.029/kWh on average](https://agmetalminer.com/2015/11/24/power-costs-the-production-primary-aluminum/). The average Bitcoin miner pays something similar.
Two weeks seems likely. Calvin is already [starting to bargain for peace](https://coingeek.com/plan-end-worlds-first-bitcoin-hash-election/).
I have carefully weighed CoinGeek's most courteous and generous offer, and with much careful consideration, I have reached the conclusion that I think is in the best interests of me as a miner and the users of the blockchain that I defend on whether to or not to accept it. My answer, which I submit to them in the most respectful manner possible:

**No.**
Satoshi wrote many things other than his whitepaper. Satoshi clearly had a vision for how to scale Bitcoin, and it wasn't Lightning Network or Sidechains. It was to scale on-chain, with SPV wallets to make lightweight access to the blockchain available to users. What we now call full nodes was not something that Satoshi thought the network needed many of.

> It would be nice to keep the blk*.dat files small as long as we can.

> The eventual solution will be to not care how big it gets.

> But for now, while it's still small, it's nice to keep it small so new users can get going faster.  When I eventually implement client-only mode, that won't matter much anymore.

[Aug 12, 2010](https://bitcointalk.org/index.php?topic=287.msg8810#msg8810). 

> The design outlines a lightweight client that does not need the full block chain.  In the design PDF it's called Simplified Payment Verification.  The lightweight client can send and receive transactions, it just can't generate blocks.  It does not need to trust a node to verify payments, it can still verify them itself. 

> The lightweight client is not implemented yet, but the plan is to implement it when it's needed.  For now, everyone just runs a full network node. 

> I anticipate there will never be more than 100K nodes, probably less.  It will reach an equilibrium where it's not worth it for more nodes to join in.  The rest will be lightweight clients, which could be millions.

> At equilibrium size, many nodes will be server farms with one or two network nodes that feed the rest of the farm over a LAN.

[July 14, 2010](https://bitcointalk.org/index.php?topic=286.msg2947#msg2947)


> The current system where every user is a network node is not the intended configuration for large scale.  That would be like every Usenet user runs their own NNTP server.  The design supports letting users just be users.  The more burden it is to run a node, the fewer nodes there will be.  Those few nodes will be big server farms.  The rest will be client nodes that only do transactions and don't generate.

[July 29, 2010](https://bitcointalk.org/index.php?topic=532.msg6306#msg6306)

> The existing Visa credit card network processes about 15 million Internet purchases per day worldwide.  Bitcoin can already scale much larger than that with existing hardware for a fraction of the cost.  It never really hits a scale ceiling.  If you're interested, I can go over the ways it would cope with extreme size.

[Apr 12, 2009](https://pastebin.com/Na5FwkQ4)

> A higher \[block size\] limit can be phased in once we have actual use closer to the limit and make sure it's working OK.

[Dec 29, 2010](https://pastebin.com/wA9Jn100)

You can find a selection of quotes from Satoshi here:

https://www.bitcoin.com/satoshi-quotes/

The early emails between Mike Hearn and Satoshi are also quite illuminating:

https://pastebin.com/Na5FwkQ4

https://pastebin.com/cKZPC1rF

https://pastebin.com/wA9Jn100

https://pastebin.com/JF3USKFT

https://pastebin.com/syrmi3ET
Suhas Daftuar ~~found~~ (or patched?) the bug. Matt was just the one to create the PR based on Suhas's commits.

That said, I recognize your pith. Have an upvote.

Edit: Okay, so Matt created the bug too. This isn't actually surprising; Matt is the most performance-oriented of the BTC Core devs. The same thing that made him want to skip pre-validation in FIBRE and Relay Netowrk is also what caused him to want to skip this check in Bitcoin Core. 
Mostly software, but it will affect nodes that have low-GHz CPUs before it affects high-GHz CPUs. The typical variation we see in different machines in single-threaded performance is not that large, maybe 3x, and sometimes the most expensive CPUs are 16-core monstrosities that only get 2.4 GHz per core, which will perform a lot worse than a 2-core 3.8 GHz Core i3.

The main issue with ATMP is that it can only run in one thread at a time. This means it can only use one core of a CPU. There may be some other issues in ATMP that make it take more time in that thread for a single transaction than is necessary, but the lack of parallel processing is the biggie.
I believe you mean "chain split," not "hard fork". 

The terms are a little counterintuitive in the Bitcoin community to newbies. A fork happens whenever the consensus rules change, even if all miners follow the same branch.

A hard fork happens whenever the consensus rules become more permissive, so that a previously disallowed block would be allowed. Hard forks may also make other rules more strict at the same time. For example, a hard fork could allow a block header to be 112 bytes instead of just 80 bytes, but with the requirement that the extra 32 bytes represent a UTXO snapshot commitment.

A soft fork happens whenever the consensus rules become strictly less permissive, such that some previously valid blocks become invalid, but all blocks that are now valid were also valid before. For example, a soft fork could require that the coinbase transaction have an OP_RETURN output containing 32 bytes of data representing a UTXO snapshot commitment. Doing that right now isn't against the rules at all, but nobody does it because it's also not required. A soft fork could change that.

Forks are so-called because they have the *potential* for a chain split. That is, **they're like a fork in the road. Just because both paths exist does not mean that both are taken.** If some miners go down one branch of the fork and other miners go down another branch, that is both a fork and a chain split. If all miners go down the same branch, that is a fork but not a chain split.
The minimum fee in your scenario is $0.01, not $2.25.

The total fee must be a whole number of satoshis, but the fee-per-byte does not need to be. A 225 byte transaction can have a fee of 1 satoshi, or 0.00444 sat/byte. If 1 BCH = $1e6, then 1 satoshi = 1e-8 BCH = $1e-2 = $0.01. 
I'll give you a downvote instead.
I visited them and confronted them afterwards.

http://imgur.com/6npqlHz
I wish he hadn't included the "start submitting feature requests" bit at the end. We have too much to do as it is. It also gives the wrong impression: If you submit a feature request, that does not mean it will get done. We are not your slaves. 

What we need is some sort of developer market. If you want a feature, you should offer to pay to get it done, and if you want to work for pay, you should offer to implement a feature for pay.

Consider.it is not a platform where devs can be hired, unfortunately. Such a platform would be nice, but that is not what we have. Consider.it is a platform for choosing which of the implemented features can be merged, and for deciding how the project as a whole should be run.
Why not a SegWit soft fork instead of a blocksize increase hard fork? Here are my opinions. 

1. SegWit is a lot more complicated than a simple blocksize increase, and has been under discussion and investigation for a much shorter period of time. I am not comfortable with it being deployed on a time scale that I think a capacity increase should be deployed on. 

2. SegWit would require *all* bitcoin software (including SPV wallets) to be partially rewritten in order to have the same level of security they currently have, whereas a blocksize increase only requires full nodes to be updated (and with pretty minor changes).

3. SegWit increases the capacity for typical transaction loads (mostly P2PKH) to a maximum effective size of 1.75 MB. SegWit increases the capacity for blocks full of specially crafted transactions or multisig transactions to 4 MB. This means that it eats up a lot of our safety headroom (e.g. for adversarial conditions) while only providing a very modest increase in typical capacity. This seems like it might actually be intentional, as it makes the types of transactions that will be needed for Blockstream products like sidechains and Lightning relatively cheaper, and will not benefit on-chain transactions much. The reduction in headroom will also make any subsequent blocksize increases harder to gain political support for, which also may be intentional. This concern can be addressed by limiting the total size of SegWit block+witness to around 2 MB.

4. SegWit makes more technical sense as a hard fork. As a soft fork, it would be deployed by putting the merkle root of the witness data tree into the coinbase message or into an OP_RETURN transaction somewhere. The coinbase message field is already quite cramped with block height, extranonce1 and extranonce2, merged mining information, blocksize vote information, and other miner/pool-specific data, and I believe that the remaining space should be reserved for miner use, not for developer use. An OP_RETURN transaction sounds technically preferable, but still pretty crufty and nasty. Both options would have the result that all witness proofs would require including the merkle path to the transaction in question plus the transaction in question, and would increase the size of such proofs by about 50%. Putting the witness data in a sibling tree to the transaction data makes way more technical sense, makes the whole system easier to code for in all wallet and Bitcoin software that will ever be written, and reduces the size and complexity of verifying the witness proofs. However, doing so would require a hard fork, which is what the Core developers are trying so desperately to avoid.

5. SegWit makes more security sense as a hard fork. With SegWit, all Bitcoin wallets would no longer be able to accurately verify new transactions, and all Bitcoin software would need to be updated to be able to verify the new transaction+witness data format. With a soft fork, old wallets will see all new transactions as being valid regardless of whether they are actually valid or not. Pieter Wuille (sipa) makes the case that this will not result in any actual dangers to most users because miners will be selfishly honest and will only create blocks that are valid under the new rules. With a hard fork, old nodes would still fully validate transactions according to the rules that they were written for, and would not see new transactions or would mark them as invalid. They would remain on a separate and fully validating blockchain that lacked economic and had nearly zero mining. I think it is preferable to indicate the consensus rules have changed by moving mining and economic activity off of the old rules rather than to write the new rules in such a way that old nodes are relying on their trust in miners instead of verifying the rules themselves. I think that with a soft fork, nodes and software will not get upgraded as fast because the old code will still be dangerously mostly compatible, whereas with a hard fork, people will notice that they're not getting new blocks and upgrade because that's the only way they can maintain functionality.
How do you assess support from users without a vote? BitcoinXT is a call for a referendum. It's asking the users for a vote of no confidence in the Bitcoin Core leadership. If the users do not support BIP101, they will vote against it by continuing to run Core. If there isn't a 3:1 supermajority favoring BIP101, then asking for a vote does no harm. The only ones who have anything to fear from this democratic "hostile takeover" are the ones who would be in the <= 25% minority.

The main flaw of BIP101 in my eyes is that it uses hashpower as a proxy for voting power. This is not ideal, as it means that people who use bitcoin but who don't mine don't get a vote directly. However, I think it's better than ideal, because the ideal solution would be too complicated. Ultimately, I think the 75% threshold, coupled with the fact that miners tend to be more conservative than normal users and that informal polls indicate that most users support >= 8 MB blocks, will ensure that at least an economic majority will support BIP101 should a 3:1 supermajority of miners support it. 

It's easy to implement a vote by hashpower. Hashpower votes also reflect something at the core of how bitcoin works: it's a distributed ledger where consensus is determined by proof of work. If you want to have a vote in that consensus, you should mine. 

> After posting this the upvotes steadily rose to about 8, now they've suddenly dropped to -1. Did my post get linked to a brigade somewhere?

No, it just got read by a bunch of people, and it seems that the majority disagree with you. This is how Reddit works. Incidentally, this is why the moderators of this forum have been moderating so heavily: the majority of redditors here hold opinions which the moderators disagree with.
/u/markblundeberg and I have an idea for how to use OP_CHECKDATASIGVERIFY to allow you to send money directly to someone's email address or phone number for new users. That should give a user experience very similar to Venmo or PayPal in terms of the onboarding process for new users. An existing user would be able to just send BCH to someone's phone number with some secret information delivered by text message, and in order to claim the funds, they need to register a public key with a semi-trusted third party (who is never privy to the funds or the transaction itself). Should the recipient fail to claim the funds withing e.g. 2 weeks, the sender gets the ability to clawback the funds via OP_CHECKLOCKTIMEVERIFY.

You can read about the proposal here:

https://gist.github.com/markblundeberg/bd28871548108fc66d958018b1bde085

The thread in which we had our initial discovery of the possibility is here: https://www.reddit.com/r/btc/comments/9bpp8z/using_pgp_signatures_with_bitcoin_script_op/e54woga/
I can't tell if you're being sarcastic or not, but I'm going to answer you as if you weren't.

It currently doesn't matter if your server costs $20,000 or $1000 because the full node software is mostly single-threaded, and the fastest CPU for single-threaded tasks is a [$425 Core i7 8086K](https://www.cpubenchmark.net/singleThread.html). If you spend more money, you get more cores, but lower max clockspeeds.
This block was not mined by p2pool.org, but by some miner who uses the p2pool software. Blocktrail incorrectly reports it as coming from p2pool.org.
> Zcash-cloud-mining-scammer 

I suppose I should reply to this, as it's going to come up again.

Around October 8th-10th, we **[held](https://forum.z.cash/t/cloud-hashing-auction-1b-200-h-s-available-for-3-months-bids-only/2457)** an **[auction](https://forum.z.cash/t/cloud-hashing-auction-toomim-bros/2320/51)** for a total of 400 H/s of 3 month Zcash cloud hashing contracts. The final price set by the auction ended up being 180 mBTC/(H/s), which in retrospect was absurdly high. A few days after the auction, we multiplied their hashrates by 3x.
We advertised our contracts as non-refundable, since buying cloud hashing is effectively a bet about the future network hashrate and exchange rate, but the way I phrased that clause wasn't very clear for the auction orders and a few customers misunderstood it.

About 10 days later, we started making web sales of contracts at around 1/3 the auction price, with adjustments to our price made based on how quickly we sold out. These contracts were clearly advertised as non-refundable.
Around Oct 26th, some of our customers started getting cold feet as it looked like the hashrate was going to be much higher than they had guessed, so people started asking for refunds. We refused those requests, as they were motivated by the desire to avoid a risk that they willingly accepted earlier.

Both sets of contracts were specified to begin on Oct 28th, Zcash's launch day. When launch day came, we had some bugs that delayed the start of our contracts by about 2 days. We offered to compensate our customers with 20x the amount of ZEC that they would have earned on the first 2 days. Most of our customers accepted this compensation, and we have delivered most of those compensation payments.

Shortly afterwards, we doubled our hashrate delivery to 2x (web orders) or 6x (auction orders). Around Nov 14th, we doubled it again, and we are currently delivering 4x (web) or 12x (auction) as much hashrate as our customers paid for. Currently, we have delivered 3.26x as many hashes since Oct 28th as we were supposed to have by now.

Even with the compensation and overdelivery, the cloud hashing contracts will likely not be profitable due to our customers' misprediction of the network hashrate during the auction. Many have asked for refunds, citing the 2 day delay as a breach of contract. I have refused to give refunds, because a 2 day delay would likely be classified legally as a minor breach of contract (for which compensation for damages are due) rather than a material breach of contract (for which the contract is terminated and compensation for damages are due), and because the main reason they are asking for a refund is the change in market conditions (which is a risk they accepted) not the delay in contract starts.

(This comment was originally posted **[in the other thread](https://www.reddit.com/r/btc/comments/5feao7/wondering_why_the_4to1_discount_on_witness_data/dajygv9/)**, but I'm copying it here.)
Do you have any code in place to detect attempted 51% attacks (intentional orphaning) against you or other BU miners/pools?
It starts at 1 MB. It is extremely slow. It starts on Jan 1st, 2017 with 1.177 MB, and wouldn't exceed 2 MB until Jan 1st, 2022. 

Most of the miners do not like it.

https://docs.google.com/spreadsheets/d/1Cg9Qo9Vl5PdJYD4EiHnIGMV3G48pWmcWI3NFoKKfIzU/edit#gid=0

https://gist.github.com/sipa/c65665fc360ca7a176a6

It was not very popular among users, either:

https://bitcoin.consider.it/bip103-1-mb-then-177-yearly-growth
Only if they were planning on reorging the chain and orphaning otherwise valid and honest blocks.

It's not a 51% attack because it has 51% of the hashrate. It's a 51% attack because they're planning on using that hashrate to orphan everyone else's blocks and to destroy ABC:

https://twitter.com/ProfFaustus/status/1061972492309401601

If Bitmain and ViaBTC took the same action against ABC, then it would absolutely be a 51% attack against ABC. If they took the same action against SV, then it would be a 51% attack against SV.
i said *might*. And I backed it up with data. And I presented alternate hypotheses.

I often criticize people when they say things like "The mempool is full -- must be spam!" without any specific data. Most recently, people were crying about alleged spam when the real culprit was slow block rates.

However, in this instance, we have specific data, and the specific data look a lot like what would happen if someone was trying to use spam to make big Segwit blocks. It's not proof, and I didn't claim that it was. It's just very suspicious.
> Begging people to move to other blockchains

I had to sell some coins today to cover operating expenses for our mine. We had enough bitcoin and enough ether to cover our needs. I decided to sell ether because I didn't want to deal with bitcoin's delays right now. I guess that means I'm letting bitcoin go to the moon because of its congestion. Hmm.
To make a 3.7 MB block with SegWit, you need to the  construct transactions that are at least 92.5% witness data, and then fill the block with nothing but those transactions. Typical transactions are about 60% witness data and 40% non-witness data. These transactions were atypical. You only get 75 kB of non-witness data (i.e. source and destination addresses) in that 3.7 MB block. This was only enough for **[467 transactions](https://testnet.smartbit.com.au/block/00000000000000f0a72f3f16eefee91d9f3e4938278872a25936bae5cbc53d66)**.

The only known use case for blocks like these is DoS attacks. This means you're basically making 12 kB transactions comprising of one input and one output, when a normal one-in one-out transaction would usually only take around 225 bytes.


> It's true for softfork version of SegWit too.

You may notice that my opinions on these two topics **[are consistent](https://www.reddit.com/r/Bitcoin/comments/3yqe7c/segregated_witness_still_sounds_complicated_why/cyg2w0y)**.
If anyone has questions on the bounty, feel free to message me.
They would need to release an 20 block alt chain to reorg 10 blocks, as there's a 2x penalty on mid-depth reorgs that fall short of the (default) 10 block limit.

Should that attack get pulled off, manual intervention may be needed to resolve it, such as with invalidateblock on the attacker's blocks. Given that this chainsplit attempt should be clearly and unambiguously an attack, I don't think there will be much difficulty in getting the community to come to consensus on punishing the attacker with `bitcoin-cli invalidateblock <hash>`.

This is an opt-in soft forking change. If you don't like it, you can do `bitcoind -maxreorgdepth=-1` to disable the feature, and you will maintain consensus with the rest of the network unless a >10 block reorg attack is successfully performed. Even if you do not run this feature yourself, you will benefit from this feature, since the existence of this feature and its use by exchanges and miners will discourage any attackers from even attempting a reorg attack.

I understand how this change would make people feel uncomfortable, but if you spend some time thinking about it, I think you'll see that this change improves security a lot more than it harms it. Finalization means that history cannot be rewritten in the blockchain by a majority attack no matter what, and censorship by a simple majority is significantly harder. The cost is that a supermajority of 66% can fork the network, and such forks would require manual intervention to resolve. I think that's a decent tradeoff, and it's worth not rejecting it out-of-hand because it's novel.
The two were independently derived ideas that happen to do basically the same thing. This isn't very surprising, as the idea of verifying signatures, pubkeys, and messages all off the stack is relatively obvious to anyone with more than a passing understanding of Bitcoin script.

The version in Elements Alpha is open-sourced, so even if it were a copy, this would not be illegal. It does not give Blockstream any control whatsoever to use their open-source code. If it did, all C++ Bitcoin Cash clients would belong to Blockstream, since Bitcoin Core uses Blockstream code.

There are some technical differences between the Elements Alpha version and the ABC version. ~~The EA version accepts a message as one of the arguments, whereas the ABC version originally accept a message as the argument.~~ (Nullc informs me: EA's CSFSV uses a Schnorr sig, but ABC's CDSV uses ECDSA. EA uses the full message, and ABC originally used a message hash but changed that to a full message after nullc notified ABC of a vulnerability from using the message hash.) This is not a major difference, as both current approaches are valid and each has different benefits, but it does suggest that ABC (Mengerian?) came up with the idea without knowledge of Elements Alpha's prior art. This means that it's unlikely to be plagiarism.
We saw 1 block orphan during the stress test.

Orphan rates are very easy to predict given block propagation delays. The formula is 1 - e^(-t/600), where t is the average delay in seconds in propagation to miners.

I wish we had been able to measure block propagation delay directly from miners' nodes, but I was the only miner to submit data. I hope to get better data next time.
In fairness, deadlnix's comment was about CTOR, not LTOR. Whatever the order is, it ought to be canon. Whether or not it's lexical is a separate question, as you rightly pointed out.

(Yes, I'm aware that deadlnix does not usually distinguish between CTOR and LTOR, and I don't blame you for your interpretation.)
I partially agree with this. If the devs want to make a hard fork change, they need to tell the users what change they want to make and attempt to convince them of the merits of the fork. If the users cannot be convinced of the value of the fork, then the fork should not happen. It is the job (and cryptoeconomic obligation) of miners to assess public opinion regarding the fork in order to decide whether or not to support the fork.

It appears to me that there is too much disagreement about the content of the November hardfork to go ahead with it at this time. While I personally like the idea of a canonical block order, I think there is enough dissent for it to not be worth pushing through. Graphene can be implemented without a canonical block order with [reasonable efficiency](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/973#issuecomment-408223520). Algorithms for validating transactions within a block in parallel that exist for a canonically sorted block can also work with a topologically sorted block with only minor adjustments. As such, I consider a canonical block ordering to not be worth disenfranchising, discouraging, or disrupting the developers, users, and businesses in the Bitcoin Cash ecosystem that foresee problems with a canonical block order in their implementations.

On the other hand, if we had a sample implementation of transaction-parallel validation of canonically ordered blocks showing a substantial performance benefit without undue implementation complexity, I think that would alleviate a lot of the concerns I have seen about canonical ordering and may tip the balance in favor of the hard fork.

I would say that the same is true for the preconsensus concept, but since to the best of my knowledge there has not yet been a coherent and detailed explanation of what Amaury's preconsensus concept actually is, I can't say much about it.
Almost all miner revenue comes from the block reward/miner subsidy. Transaction fees make up a small portion of total revenue. On BTC right now, it's about 1.2 BTC per block from transaction fees and 12.5 BTC per block from the miner subsidy. On BCH, fees are 0.01 BCH and the subsidy is 12.5 BCH. So we have about 13.7 BTC per block versus 12.51 BTC per block as the basic revenue.

1 BTC is worth 6.9x as much as 1 BCH, so the advantage in revenue per block is even bigger. With both fees and the exchange rate differences, 1 BTC block is worth 7.55x as much as 1 BCH block.

The diifficulty of mining one block on BTC is 1,364 billion, but on BCH it's only 150 billion. That's 9.11x harder for BTC than for BCH.

7.55 / 9.11 = 0.829. It is currently 0.829x as profitable to mine BTC as it is to mine BCH.
The orphan and uncle rates are a function of the gas processed divided by the interval between blocks. If the block interval increases 80%, then the amount of gas that can be processed for the same uncle rate also increases 80%.

The gas limit should clearly be raised. I will configure my mining node to have a larger gas limit, and I encourage all other ETH miners to do the same.
Welcome to /r/btc. While your opinion may be less common in this sub, we are all happy to have it here.
It's because miners in China are behind the Great Firewall of China. It's a performance issue first and a government trust issue second. 

It is not an issue of thinking that the Chinese miners will cooperate to perform a 51% attack. The concern is that the performance issue of the GFW might cause an accidental 51% attack by cutting Chinese miners off from the rest of the world for a short period of time.
BTC jumped a few percent in price relative to BCH over the last 24 hours. Most of BTC's hashrate is set up without automatic switching between BCH for profitability. Manual switching comes with a delay of a day or so.

Never attribute to malice that which can equally well be explained by laziness.
I'm talking about a reduction in bytes transmitted in the latency-critical code path. I made it very clear in my [original post](https://old.reddit.com/r/btc/comments/bas60b/by_the_power_of_ctor_xthinner_is_now_working_with/) that this is only an improvement for the block propagation code path, and does little for overall bandwidth use (much less than 50%, since you omitted INV broadcasts) or other resources used by a full node.

> This is an assumption that txids are not so close as to have collisions with only a 12 bit hash. This is an unrealistic assumption, esp because anyone who wants to make it abritarily untrue can do so.

That is not how Xthinner works. When several transactions have the same prefix, that prefix is encoded only one time. The total portion of the TXID that Xthinner encodes is much larger than 12 bits; it just uses about 12-16 bits per tx on average to encode that after eliminating redundancies. This 12-16 bit range applies for all block sizes, as long as the TXIDs/hashes are uniformly distributed. To get a better idea of how Xthinner works, I recommend reading [my primer](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0).

Yes, it's possible to do hash grinding to generate a bunch of transactions that are not uniformly distributed and have pairs with very similar TXIDs, but to do that you have to expend 256^n computation in order to add n bytes to the encoded size of each tx. In order to make Xthinner's performance equal to Compact Blocks, you would need to hash-grind through 256^4 = 4,294,967,296 transactions for every transaction that ends up in the block. I do not find this scenario alarming.

> The easiest way to generate transactions with conflicting txids one that naturally produces conflicting transactions. 

This is a reasonable concern. It's worth noting that Compact Blocks and Xthinner would perform nearly the same way in this scenario -- both would need to fetch the missing transaction(s) with a round trip. In the worst case, Xthinner would detect the conflict via checksums rather than the short TXID directly, so Xthinner would need to download the 7 or 15 block-adjacent transactions as well (depending on the checksum configuration). Both Xthinner and Compact Blocks include a PrefilledTransaction mechanism by which nodes can include likely-novel transactions in the (compact/xtr)block announcement, but neither has yet implemented any heuristics except including the coinbase tx. The net effect of this exploit would be to slightly (~200 ms) punish a miner who includes a transaction that conflicts with transactions already in most other nodes' mempools. In some ways, that seems like a desirable incentive. I hope that Avalanche or another pre-consensus method fixes this scenario by establishing consensus about which of two conflicting transactions to include in mempool and blocks.

> Why do you care about schemes that require more than zero bits per transaction in a block when schemes are available that don't?

Because miners are permitted to make blocks that deviate from what my node expects them to make. Zero bits is only possible by completely eliminating miner freedom. If we're going to take that route, why even have mining at all?
Agree. I'm not going to abandon my BU membership for exactly this reason.

That said, I'm kinda glad Amaury left. He never took membership in BU seriously anyway, and I think BU is better without him. (Amaury is a big net positive influence on BCH, but he has rough edges, and they showed in his BU membership.)
[I don't accept donations](http://www.jwalkonline.org/docs/Grad%20Classes/Fall%2007/Org%20Psy/Cases/motivation%20articles/PERUSED/effects%20of%20surveillance.pdf). I prefer to work for fun.
> At best CTOR improves graphene 98% efficiency up to 99.5% ... 1.5% marginal improvement.

A 1 GB block that is encoded with 98% compression takes 20 MB. A 1 GB block that is encoded with 99.5% compression takes 5 MB. That would be a 300% improvement in block propagation speed, not a 1.5% improvement.

> I tried to warn everyone

And people ignored you because your math sucks.
The increase in the per-script opcode limit to 500 makes [a known O(n^2) sighash vulnerability](https://gist.github.com/markblundeberg/c2c88d25d5f34213830e48d459cbfb44) about 2.5x worse. Their plan to lift the opcode limit even higher in the future and to raise the per-input length maximum fro 10 kB at some point in the future [makes this problem worse yet](https://www.reddit.com/r/btc/comments/9qu189/to_prevent_dos_blocks_script_size_limits_should/).
Not usually. The CTOR+LTOR consensus rule change affects a lot of possible optimizations (mostly by making UTXO inserts sequential and predictable within a block). Block propagation is obviously a p2p protocol layer issue, but changes there can be done without breaking clients that don't support the new protocol extensions. Other than that, it's pretty much all implementation details.

The Bitcoin ABC team originally thought that CTOR/LTOR was required for a big parallelization optimization that they wanted to do on block validation, but I [found a way](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244) to use that optimization even with the current ordering. Consequently, you will often see that argument for CTOR in the literature, but it is an invalid argument. Other arguments exist for CTOR/LTOR which are both more compelling and more valid, so it doesn't matter.
I made a [post](https://np.reddit.com/r/ethereum/comments/9csqbi/craig_wright_tried_to_patent_ethereum_after/) in /r/ethereum linking to this page. A lot of his patents appear to be targeted right at Ethereum, so I think it behooves them to get involved in fighting against this patent troll.
What I see is cryptorebel alleging without evidence that there are downvote bots attacking him, and suggesting fighting back with upvote bots. 

I am interested in seeing evidence of downvoting bots trailing cryptorebel. If that evidence is compelling, action should be taken in his defense. 

However, I doubt that his downvotes are coming from bots. I know I have been personally responsible for downvoting him many times in the past because I have found his comments to often be detrimental to the quality of discussion in this subreddit, and I expect that plenty of other humans are doing the same. I suspect that cryptorebel is mistaking being downvoted by humans for being downvoted by bots, and is suggesting using upvote bots to counter these human downvotes. That. Is. Not. Okay.

If you suspect vote manipulation, the answer is not more vote manipulation.. The correct response is to document the vote manipulation and get the mods or admins involved. 
James Hillard [7:11 PM] 
@alp: Peter R is IMO is a sophisticated troll with those charts, they are just enough to convince those that doesn't know any better
4 

anduck [7:12 PM] 
i don't get that why people who want to improve bitcoin can't work for the same codebase

aknix [7:12 PM] 
so i take it governance is all that anyway cares about.. Grow up.. lol

alp [7:12 PM] 
@dts: that's an oldish picture

anduck [7:12 PM] 
maintainers don't choose after all; peer review, which is open to everyone, chooses

dts [7:12 PM] 
@anduck: what kind of user privacy things do you think Classic will decrease?

[7:12] 
@anduck: maybe they will use a stupider form of RBF that requires you to taint your utxo together(edited)

aknix [7:12 PM] 
What does privacy matter if the codebase doesnt improve?

brg444 [7:13 PM] 
yes, I forgot to highlight Xapo

[7:13] 
and Bitnet

dts [7:14 PM] 
Confidential transactions and Coinjoin need to improve a lot but there are few "business cases" that they enable specifically

anduck [7:14 PM] 
@dts: https://github.com/bitcoinclassic/bitcoinclassic/pull/16  https://github.com/bitcoinclassic/bitcoinclassic/pull/15

GitHub
Improve 0-conf tracking by cointracker · Pull Request #16 · bitcoinclassic/bitcoinclassic · GitHub
This change in 0.12 would make our 0-conf tracking much less reliable if deployed.

GitHub
re-enable UPNP by cointracker · Pull Request #15 · bitcoinclassic/bitcoinclassic · GitHub
UPNP makes our 0-conf tracking and compliance tracking more reliable by allowing us to make incoming connections to nodes behind firewalls, we should re-enable it by default.

[7:15] 
@dts: yes also they seem to be very much against opt-in RBF.

**alp [7:15 PM] 
It should not be hard to inject bugs into their code**

anduck [7:15 PM] 
and would prefer the FSS-RBF if some RBF...

[7:15] 
@dts: well, if lightning rolls out, coinjoin isn't needed IMO
1 

dts [7:15 PM] 
@bram: One of the main Classic ideas for improving Bitcoin is incorporating Bittorrent actually

bram [7:16 PM] 
@dts AUGH
1 

anduck [7:16 PM] 
@dts: but it could be done for bitcoin too

Aleph 0 [7:16 PM] 
blocktorrents, yes.

**alp [7:16 PM] 
bram would be a perfect mole**

Gregory "instagibbs" Sanders [7:16 PM] 
Wait what

dts [7:16 PM] 
Some Classic supporters claim that bittorrent will solve the propagation time issues for big blocks

alp [7:16 PM] 
they want to do somoething like bittorrent

Gregory "instagibbs" Sanders [7:16 PM] 
What.

aknix [7:16 PM] 
LMAO

alp [7:16 PM] 
its not exactly bittorrent IIRC

aknix [7:16 PM] 
im dying

anduck [7:17 PM] 
don't die.

Gregory "instagibbs" Sanders [7:17 PM] 
@bram go be their CTO of torrents

aknix [7:17 PM] 
wait torrent like DHT to improve speed lol

James Hillard [7:17 PM] 
The UDP part is the most interesting...since it might be able to punch through the GFW better

**Aleph 0 [7:17 PM] 
we need some people who are good at underhanded C.**

Most miners prefer 2-4-8 (except Bitfury, but they're okay with it). I prefer 2-4-8. I think we should do 2-4-8 instead.
Hi,

I'm the one who started this. I did the mining to activate it.

**This is not a performance test.** This is intended as a quick test to ensure that the forking mechanism works, and to check for any unanticipated behavior in chaotic conditions. Currently, I'm guessing BIP101 nodes comprise less than 10% of testnet's network, which means they should have a lot of trouble propagating blocks to each other. It will be interesting to see what happens when BIP101 comprises a hashrate majority but a full node minority.

Performance of this network will be terrible. There's no relay network, and most of the servers that people are going to be poorly configured, medium-low powered, and not actively maintained. We'll probably do some block propagation tests (because it's one of the most important questions with big blocks), but we are not expecting the results of this test to be any way representative of actual network performance on mainnet.

Performance tests are better done with a separate testnet with > 90% BIP101 nodes, or in regtest mode, rather than on testnet.
You provide escrow, but you never touch their wallet... Does this mean there's some OP_CDSV magic going on, or does "never touch their wallet" not mean "never touch their funds"?
I don't mind that they rented hashpower. I'm happy to be part of a coalition that's willing to rent even more than they do in order to defend our blockchain against attack.

I'm just pointing it out because they claimed they weren't renting, and that's implausible given the data. I dislike dishonesty.
There's a reason for that. BSV's positions do not make technical sense. People who can do their own quantitative analyses generally come to the conclusion that BCH is the better option.
Yes.
Delete your chaindata and resync. Doing this will result in more unnecessary data being pruned off or not downloaded, and will use far less space. My Parity installation is currently using 97 GiB, for example.
Most mining pools have manually set a ceiling on how high the adjustable gas limit will go. The mechanism for adjustment is actually just that each block votes on whether to increase or decrease the limit, and the default behavior for a mining node is to vote based on whether the block was full or not. However, that default behavior can easily be overriden with command-line switches. During the spam attacks of last year, miners were encouraged to set those command-line options to manually decrease the limit as a mitigation technique, and most miners have not removed them.
Not me. I will greet anybody who violates their promise to support Segwit2x with distrust and disdain. In my eyes, they are liars. They are people who promised 2x as a compromise in order to get Segwit, and reneged on 2x as soon as they got the part they wanted out of the compromise.
According to https://www.viabtc.com/tools/txaccelerator/, they accept donations for their accelerator at 16ENAMhrY9nwRH63isFKjQgTf1UD173jdq.
No, it's a solid lagging indicator. 

Miners follow profitability. Profitability follows price. Price follows popularity.
This mechanism would result in slower block propagation. Because the transactions haven't been published to the p2p network previously, techniques for fast block propagation like xthin and compact blocks and the fast relay network won't work as intended. Consequently, the full transactions will need to be sent along in addition to the block header and the list of transaction hashes. With xthin and compact blocks, this would require a separate TCP/IP round trip per hop.

This will add at least a few hundred milliseconds and often a couple of seconds to block propagation, which means that this will be expensive for miners to support. If a single transaction adds half a second of delay, that would be expected to cost the miner 1/1200th of a block reward in orphan risk, or roughly $9.40. Additional transactions after the first will not cost as much, but will still be much more expensive than normal for miners to include. 

If you're trying to envision this Preferred Miner system as a way to support your miner of choice, you'll need to include a transaction fee that is several times larger than normal just to overcome the increased orphan risk from including your transaction. Ultimately, it would be simpler and more efficient to just give your preferred miner money directly to subsidize their operation.
jtoomim, not jtoomin.
BCH p2pool's hashrate doubled recently as [another industrial miner in Quincy, WA](https://forum.bitcoin.com/pools/p2pool-decentralized-dos-resistant-trustless-censorship-resistant-pool-t69932-270.html?sid=827e3e9106332f2d80dfefc787d6231a&sid=827e3e9106332f2d80dfefc787d6231a) (apparently a neighbor of mine) added about 7 PH/s. Also, the total BCH hashrate dropped recently. Consequently, p2pool is a larger portion of the total blocks.

I have [an outstanding $1500 bounty](https://github.com/jtoomim/p2pool/issues/17) for adding AsicBoost support to p2pool, and we have a guy (Full Cycle Mining) working on implementing it now. If things work out, AsicBoost support should be in soon.

Unfortunately, the current p2pool codebase cannot generate large BCH blocks efficiently, and is limited to creating blocks of roughly 2 MB sizes. Fixing this is possible, but will require quite a bit of work. I have a [$5,000 bounty](https://github.com/jtoomim/p2pool/issues/19) posted (but not fully described) for the work needed to resolve this performance issue.
I think your priorities are backward. I think the history rewriting is the worst attack, and the DoS is the second-worst.
> deep re-orgs aren't fatal ... no transactions are lost they just become unconfirmed.

Not when the deep re-orgs are intentional and caused by an attacker who is attempting to double-spend exchanges, as CSW has repeatedly threatened.
Renting is far cheaper than buying if you're only planning on using the hashrate for a few weeks or months. Capital expenditures for building a datacenter are typically around 10 months' electricity costs, and an S9's capital cost is another 10 months. Total capital costs are equal to around 15 months of operating costs.

These numbers vary by jurisdiction. In China, for example, capital costs are lower and operating costs are higher. But you get the idea. Renting for short periods is way cheaper, not to mention faster, which is why CG did it.
1.8 EH/s is about 130,000 Antminer S9s. A worker can set up about 50 S9s per day if all of the infrastructure (including hot air containment) is in place already. Setting up that many machines should take about 2571 man-hours, or 13 days for a staff of 25.

Building the datacenters takes a lot longer than plugging in the machines, though. Either they had a single 200 MW datacenter which came online that week (just in time!), or they had multiple 10 MW facilities that all came online at about the same time. Huge 200-MW facilities are pretty rare and generally newsworthy, so I doubt that to be the case. (Bitmain's latest facility in central WA state was 8 MW, and that made the news.) Having twenty 10-MW facilities all come online in the same week also sounds implausible to me.

The only way I think it's reasonable that they could have added 1.8 EH/s in the exact week preceding the hard fork is if they rented it.
> The main argument that Craig brings up is that implementation of Wormhole is made easier on Bitcoin ABC 18.0+.

Counterargument: According to [devs of Wormhole](https://www.reddit.com/r/btc/comments/9awqff/clarification_omni_and_wormhole_do_not_benefit/) and [markblundeberg](https://www.reddit.com/r/btc/comments/9gmi4z/logical_proof_that_wormhole_omni_is_not_secure/e65c9ei/), CTOR makes Wormhole unequivocally *worse*.
Thanks for your kind words. It's really encouraging to see that the rift and animosity between the BTC and BCH crowds is finally starting to heal.

If I may ask, what are some properties that you would like to see in BCH but don't, and which make you prefer BTC? I hope to win people like you over to the BCH side in a few years with technical improvements, and I'm always looking for good data on which improvements to prioritize.
What has Bitcoin SV done to address the near-universality of the cs_main lock, which causes most of Bitcoin SV's code to be single-threaded? If SV has not done anything about this, how well does Bitcoin SV expect their code to scale on a single CPU core?
CTOR should help with Blocktorrent too (similar encoding scheme to Xthinner), though I didn't mention that in the article because Blocktorrent is still a bit pie-in-the-sky at this point in time.
> CSW wants to move BCH out of the hands of amateurs 

CSW *is* an amateur. He doesn't actually code himself, and when he tried to prove his programming prowess by posting an assembly code version of "hello world", he got caught as having [copied it from Stack Overflow](https://bitcoinexchangeguide.com/craig-wright-hello-world-coding-test-gets-ridiculed-for-copy-paste-job-by-crypto-community/).

Their software is only a week old. Its most recent commit message says "[This is a quick fix because we've run out of time. This must be implemented properly in the future.](https://github.com/bitcoin-sv/bitcoin-sv/commit/55c993841725690256fd4b7093142ddd8084312a)" That doesn't exactly sound very professional to me. 
Fortunately the patent has not yet been granted in any jurisdictions. However, the patent clerk relied on the fact that Ethereum was prior art for claim 1 and 18, and did not go so far as to say that *any* blockchain application was prior art. This suggests that CSW's obfuscation using "Turing compete system" was partially effective.
Most of the BU devs I've talked to are saying something along the lines of "this is too fast, we need more time to digest." I also am of that opinion. I really like the lexical CTOR proposal, but I think that November is too early for a change of that nature. The community needs time to think about it and see the data.
Bitcoin Core and ABC do get up to 350% during IBD. Script and ECDSA verification is efficiently parallelized, so whenever that's the bottleneck, all cores get saturated pretty well. This is basically the same scenario as your test import 357% figure.

I'm sure Flowee is faster for synchronization on IBD, but I think that's mostly because of the (very impressive!) work you've done with memmapped files and on reducing memory allocations and copying.
Due to the daily cycle of bitcoin (and all human) activity, we can expect transaction delays to be common during the daytime in Europe and North America for a while. For now, the backlog will generally get cleared sometime after the sun sets on the east coast of the USA. 

Eventually, the daily average transaction generation rate will exceed the block capacity. Once that happens, the backlog will start to grow without bound, and the lowest-fee transactions will likely remain unconfirmed forever.
Agreed. 

There is a reason why the #1 principle of Bitcoin Classic is to **[make decisions democratically](https://www.reddit.com/r/btc/comments/4089aj/im_working_on_a_project_called_bitcoin_classic_to)** with user and miner input. Core has been ignoring user and miner wishes for ages in favor of their own pet projects. The proper response of users to that behavior is to ignore Core, and use something else.
I traveled around in China for a couple weeks after Hong Kong to visit with miners and confer on the blocksize increase and block propagation issues. I performed an informal survey of a few of the blocksize increase proposals that I thought would be likely to have widespread support. The results of the version 1.0 census are below. 

http://imgur.com/3fceWVb

My brother is working on a website for a version 2.0 census. You can view the beta version of it and participate in it at https://bitcoin.consider.it. If you have any requests for changes to the format, please CC /u/toomim.

https://docs.google.com/spreadsheets/d/1Cg9Qo9Vl5PdJYD4EiHnIGMV3G48pWmcWI3NFoKKfIzU/edit#gid=0

Or a snapshot for those behind the GFW without a VPN:

http://toom.im/files/consensus_census.pdf

**Edit: The doc and PDF have been updated with more accuracy and nuance on Bitfury's position. The imgur link this post connects to has not been updated. Chinese translation is in progress on the Google doc.**
It's more disappointing if the extensive funding that BU currently has is stolen by packing the BU membership with people who support BSV, thereby coopting the BCH funds for BSV development. You know, like [BUIP107](https://bitco.in/forum/threads/buip107-closed-sell-the-bch-portion-of-bus-funds-for-bsv.23184/) but worse. Without /u/todu, /u/deadalnix, and /u/mengerian, this scenario is starting to look more likely.
Corrections: BTC does 3.5 TPS with 1 MB blocks. To get 30,000 TPS, BCH would need roughly 10 GB blocks. If each transaction is 400 bytes, 30k TPS would be 12 MB/s (that's bytes, not bits), or roughly 100 Mbit/s.

30,000 tps is about as many transactions as the whole world is doing. It's a pretty big number. How can we scale from there? Well, by adding more hardware.

If BCH had 10 GB blocks, the price of each BCH would probably be about 30,000 times higher than it is right now. That would make BCH's market cap about $60 trillion, which is comparable to the total world's M1+M2. 

Scaling past that point can be done with better hardware. BCH would no longer be a hobbyist project by that point. We would not need to worry about keeping it affordable for hobbyists to run their own full node. Running a full node would require a 10 Gbps or faster internet connection and moderately fast server hardware -- say, 32 cores, 128 GB RAM, 50 TB SSD, 5 PB of HDD space. Today, running such a node would cost around $200 per month. 

Chances are, by the time we actually need hardware like this, it will be cheap. It will likely take 10 years or more for BCH usage to grow that much. By that time, bandwidth will be about 6x to 58x more plentiful (1.2x per year or 1.5x per year). Processors, RAM, and SSDs will have 32x as many transistors, and consequently 32x as much capability for the same price. What costs $200/month to run now will be available as a $20 basic VPS. 

If we want more capacity than 30k TPS in 10 years, we can get that by spending more than $20/month per full node. With 1 BCH having a value of around $3 million, the total mining reward would be about $4.7 million per block, or $675 million per day, just for the subsidy. At $0.01/tx, transaction fees would add another $26 million/day. In this scenario, I think there would be enough interest in BCH for a few hundred thousand people or businesses to be able to afford to spend $20 or $200 per month in order to run their own full node and to get unfiltered access to the network.
My Xthinner WIP is actually written on the ABC codebase, but I'm trying to make it easy to copy-paste into BU and XT. It's possible that BU will merge the code before ABC due to BU being less conservative about changes than ABC.
Coingeek and BMG are buying BSV and selling BCH. So far, imaginary_uname and I have found 28k BCH that they moved on 12/6 at around 2:45pm, presumably to exchanges to sell. There's probably a lot more than that that they moved and are trading; I only spent about 20 minutes looking manually on blockdozer. Check the thread:

https://twitter.com/im_uname/status/1070857656305238018
Please, this is a pet peeve of mine: Checkpoints are where a developer types the hash of a specific block into the code. That's very different from what 0.18.5 added. The feature that was added was finalization, not checkpoints.

But yes, there are issues with 0.18.5's approach. I'm pretty sure the ABC team was aware of those issues, but thought that even with them that code was more beneficial than harmful. Whether they were right about that assessment is an open question.
How certain is Bitcoin SV that there are no remaining O(n^(2)) bugs or vulnerabilities left in Bitcoin script execution? Has Bitcoin SV commissioned any reviews of the script system to ensure that there are no O(n^(2)) vulnerabilities that could get exploited if the script opcode limit is raised or lifted?
After by bitcoind. Before by FIBRE or Relay Network.
On the contrary, I'm saying that this is exactly the kind of thing that professionals do as a standard industry practice when their bosses give them unreasonable deadlines.

... healthcare.gov ...

... No Man's Sky ...

... Windows Me ...


There are no major mainland Chinese chip manufacturers. 

Companies like Bitmain do not make their own chips. They design them, and then ship their designs off to foundries like TSMC to be etched into silicon. The foundries then return the etched silicon wafers back to their client, and the client will then slice the wafers into die, package them, mount the packaged chips on PCBs, and sell the completed device.

If you're alleging that the Chinese miner manufacturers will get their 16 nm chips fabricated in e.g. Taiwan, then dump the finished product at cost or at a loss, that is possible. However, it sounds like you're alleging that the chips are actually etched in mainland China, and that is simply not true. The only chip foundries in China right now are using very old process nodes like [40 nm](https://www.taiwannews.com.tw/en/news/3268315) or 28 nm. Not only that, but these mainland Chinese fabs are owned by Taiwanese companies like UMC and TSMC.

Edit: TSMC has a fab in Nanjing which [added 16 nm capability in May 2018](https://news.8btc.com/bitmain-order-manufactured-by-tsmc-nanjing-fab-volume-production-in-may), and Bitmain is now using this fab. However, the fact that TSMC owns this fab means that they're not going to dump wafers from it if that would cut into their 7 nm wafer sales.
Of the major contract manufacturers, only TSMC, Samsung, and GlobalFoundries [had 7 nm chips on their roadmaps](https://www.extremetech.com/computing/249075-foundry-futures-tsmc-samsung-globalfoundries-intel-gear-7nm-beyond). TSMC is the first to market with a 7 nm process. GlobalFoundries was planning to be second to the market, but just cancelled that program entirely. Samsung expects to get their 7 nm process in production for Samsung-developed chips around the end of this year, with availability to third parties from Samsung [starting in 2019](https://www.anandtech.com/show/12795/samsung-updates-foundry-roadmap-euvbased-7lpp-ready-for-2018-3-nm-incoming). Until 2019, TSMC will have a monopoly on the 7 nm process and will be able to charge whatever rates they want. This will likely drive up prices for 7 nm mining hardware until 2019. 

Once 2019 arrives, manufacturing costs for 7 nm chips should fall, but as there will only be about 1 year left before the next halving, most miners will be reluctant to invest in new hardware that might never ROI. Consequently, it's likely that the 7 nm generation might not achieve a dominant market share of all Bitcoin mining, and the current 16 nm ASICs may continue to be in use for quite a while longer.
The compression referred to is using already-transmitted data (the transactions, which are generated and propagated continuously in a non-latency-critical manner) in order to transmit blocks (which are latency-critical) using far fewer bytes.

Edit: This may not be the most intuitive use of the word "compression", but the word has been used this way by Bitcoin developers for Bitcoin blocks for years.
No, it's not wallets. It's block rate:

https://data.bitcoinity.org/bitcoin/block_time/7d?f=m10&r=hour&t=l

We're getting about one block every 8 minutes right now. That increases capacity, which makes transactions confirm way faster and cheaper.
This was done in BU.
Come on, get with the program. People use **[Zip disks](https://en.wikipedia.org/wiki/Zip_drive)** nowadays.
> I’m thinking that with coins stuck in LN channels, they probably CANNOT hard fork anymore

A hard fork on BTC to increase the blocksize limit will not invalidate any existing LN channels. A blocksize-increase HF will not change the validity of individual transactions (which could make LN channel-close transactions invalid unless modified). A blocksize-increase HF only affects the validity of blocks, not transactions.
It's a matter of history. In 2015-2016, when the blocksize debate was heating up, the moderator of /r/bitcoin decided to censor and ban any users who advocated for a hard fork to increase the blocksize limit. In response, Roger Ver created this subreddit as a forum for uncensored and lightly moderated discussion of all Bitcoin-related topics. By allowing for uncensored discussion of both sides, this subreddit was quickly dominated by users who could not post their viewpoints on /r/bitcoin, so the Big Block viewpoint became overrepresented here.

A year or two later, as the Bitcoin Cash movement was beginning to gain traction, this subreddit had a lot of users in support of it. As a planned hard fork of Bitcoin, discussion of Bitcoin Cash was permitted in this forum, and the Big Block majority in this forum looked kindly upon it.

Things just continued from there.
There were quite a few OP_CDSV transactions in the first block after the fork. They were mostly used for splitting coins.
Rough numbers: At peak, about 7-8 EH/s. Sustained, about 3 EH/s. There's about 1 EH/s that never left BCH during CG's run-up, so 4 EH/s sustained total.

Keep in mind: whenever CG increased his hashrate, he made mining unprofitable for other BCH miners, and squeezed them out and onto BTC. These miners knew what CG was doing, and knew that until Nov 15th it didn't matter, so they left willingly and came back as soon as they were needed.
That's the estimate. Most of the orphaned blocks have not propagated the network, and we don't know of a comprehensive orphan metric for CG, Mempool, or BMG, so the actual number could be much higher or lower. IIRC, we've directly seen 5 orphans so far on the network, and we also know that SVPool has had 5 orphans (none of which we've actually seen!), so there have been at least 10 orphans. The 20 orphan guess is speculating that there are another 10 orphans NOT on SVPool which we have not seen on the network.
Their difficulty is currently at 518 billion. Fork difficulty was 520 billion. Their difficulty got down to about 497 billion before it started climbing again.

So, no, the difficulty does not affect those numbers very much. Only about 2%, which is still well within the margin for statistical noise.

I like the way you're thinking, though. Have an upvote.
About 2x as much hashrate as Coingeek+BMG+SVPool have together, and about 33% more than BCH has had over the last day.
Ethereum can handle around [1.2 million transactions](https://etherscan.io/chart/tx) per day, and fees spiked up to [$4/tx](https://bitinfocharts.com/comparison/ethereum-transactionfees.html) when that happened. 

BTC can handle around [450,000 transactions](https://www.blockchain.com/charts/n-transactions#) per day, and had fees of [$50/tx](https://bitinfocharts.com/comparison/bitcoin-transactionfees.html) when that peak was reached.

BCH is currently operating at a rate of around [2.5 million transactions](https://cash.coin.dance/blocks) per day (17k per block over the last 17 blocks) with an average transaction fee of [$0.002/tx](https://bitinfocharts.com/comparison/bitcoin%20cash-transactionfees.html).
When mining, you don't hash the whole block. Instead, you just hash the block's 80-byte header. Every hash is of the same amount of data regardless of how big the block is.

The block's header contains a hash of the merkle root for the transaction tree. This gets hashed along with the rest of the block header, and serves as a fingereprint for which transactions were being included in that block at the time it was being hashed.
> You can't just recommend everyone prune their nodes, somebody has to store the chain.

Nope. Ethereum has a commitment of the state root to every block, which means that nodes can trustlessly sync to the current state of the system without replaying the whole blockchain. This commitment is the Ethereum equivalent of a UTXO set commitment, something that has been frequently discussed but never implemented for Bitcoin.

Another consequence of this state root hash commitment is that light clients have much better security in Ethereum than in Bitcoin. In Ethereum, full nodes cannot lie to light clients about the presence or absence of transactions/UTXOs like they can in Bitcoin. Everything that a full node tells a light client in Ethereum is provable.

> No concern about centralization in respect to those that can put together the disk space to hold everything?

Correct, because there is no need to hold everything. The only reason to hold the full archival transaction history with Ethereum is curiosity.
> How does Ethereum solve that with it's state root?

The Ethereum state trie was designed from the ground up to allow for this feature. Specifically, the trie has **[these properties](https://github.com/ethereum/wiki/wiki/Design-Rationale)**:

1. Every unique set of key/value pairs maps uniquely to a root hash, and it is not possible to spoof membership of a key/value pair in a trie (unless an attacker has ~2^128 computing power)
2. It is possible to change, add or delete key/value pairs in logarithmic time

The trie is a consensus-critical data structure in Ethereum's design, akin to Merkle trees in Bitcoin, whereas the UTXO database is not a consensus-critical data structure in Bitcoin's design. Bitcoin implementations are free to implement their UTXO databases however they want, and if UTXO commitments were to be added to blocks, that would likely confer a performance advantage to one implementation over another due to potential differences between the consensus UTXO DB encoding and each implementation's UTXO DB encoding.

Furthermore, UTXO commitments have a performance penalty. For a naive implementation of #1 above with a Merkle tree for UTXOs, you have to hash the full database for an insertion or removal -- O(n) -- and only modifications are O(log(n)). Ethereum's Patricia-Merkle trie fixes that. Still, getting a trie with the property #1 above generally requires compromising the ability to parallelize updates to the trie. This makes Ethereum more I/O dependent than Bitcoin for full-node performance.
They're pushing Dash now.
Also by keeping the new lines of code for any contribution to a minimum, such as by avoiding needlessly complicated softforks.
I intend to submit pull requests for the major changes to Bitcoin Core as well. They may get rejected. So be it.

My goal is to build a project that implements features that users and miners say they want. It currently appears that Core does not share that goal. If that is the case, and if I am successful in my goal, then Bitcoin Classic will likely go somewhere.
Soft forks are developer-sanctioned systematic 51% attacks (or 95% attacks) against miners who do not upgrade. This is the reason why they're "safe" -- you can rely on miners to exclude any transactions that do not follow the new rules. Any miners who do not upgrade are censored from the Bitcoin network.

https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-December/012176.html
The activation rules for BIP101 on testnet are:

    starting time: 1 Aug 2015 (timestamp 1438387200)
    activation condition: 75 of 100 blocks
    grace period: 24 hours

I've mined about 90 of the last 100 blocks on testnet with BitcoinXT. The activation of BIP101 should be coming soon. Things I can use help with:

- Adding more nodes. Right now, there aren't very many XT nodes on testnet to propagate blocks to. Once the hard fork occurs, it's quite possible that XT nodes might be only connected to a bunch of Core nodes. Core is effectively performing a Sybil attack on XT. If you add a node to the net, posting its IP here can help too, because we can use the addnode option to make sure that they're all chained together. (Don't add every single node! We want to simulate multi-hop transit.) Keep in mind that bandwidth use might get fairly high during these tests, and there's not really a point yet in testing failure modes, so please only use nodes with reasonably fast performance.

- Spam spam spam spam spam spam spam. Send me a testnet bitcoin address if you want some coins to help spam with. I've got a few. Also, if you have a good spam script, please share that. It would be nice to have some transactions of different sizes in the spam mix. One of my goals with this project is to do some benchmarking and testing on getblocktemplate and block validation performance, and having a good mix of transaction sizes would help with getting more valid results.

I am running a few XT testnet nodes which you all may connect to:

    79.143.179.215:18333  # contabo germany
    106.184.6.233:18333   # linode tokyo
    139.162.2.241:18333   # linode singapore
    139.162.205.183:18333 # linode london
    45.79.162.82:18333    # linode newark


Edit: A clarification to everyone: This is *not* a performance test. This is just a test to see if there are any problems getting the fork to work and persist in chaotic and semi-adversarial conditions. It's a robustness test. Performance tests will take a lot more time and effort to perform, and probably will not be feasible in a mixed BIP101/Core testnet. We will probably collect some performance data while we're at it, but we expect the results from that to be horrible for reasons having to do with our use of testnet, not the limits of what Bitcoin can handle.

I will be on **#bitcoinxt on IRC freenode** most of the time. Coordination on this project can be done either there (for real-time stuff) or here.

**Edit2: The first fork has happened at block height 584265.**

    "hash" : "0000000000000169b59ac09e7d1bb1db5287a4a0567f80a7e0b1477fc67eb9c5",

    "confirmations" : 1,

    "size" : 1109130,

    "height" : 584265,

    "version" : 536870919,

    "merkleroot" : "f6005da73a7ab6bc6c6bb03e70dad1fa432c8305cd96c32b555d00c0558c3288",
    "tx" : ...

It successfully propagated to my 5 VPSs, plus to the user chmod755 on IRC. It is not showing up on any block explorers that I've checked so far. So far so good. Next step: switch back to mining with  Core and make the forks rejoin.

**Edit3: Adding more nodes to testnet is desirable. Details of your node is useful too.**

If you add a node, please make sure it's connected to at least two other IP addresses listed in this thread via an addnode= command in ~/.bitcoin/bitcoin.conf. Please post the IP address for nodes that you set up, along with a note on its location and performance specs (bandwidth, memory, CPU, and the hosting company if it's a VPS or dedicated server).
I'm not saying that big blocks will kill LN. I'm saying that small blocks will kill Bitcoin. 

At 300 MW (roughly our current mining network), each block uses 50 MWh, or about $4,000. At 800 transactions per block (current average with 0.4 MB blocks), that's about $5/tx needed to supply that hashrate. At 80,000 tx/block (40 MB blocks), that would only be $0.05/tx. If we can scale Bitcoin volume up 100x while having a minimum (or average) fee around $0.05/tx, we should be able to handle 300 MW indefinitely.

If we don't provide at least $2,000/block in fees+subsidy, it will become quite cheap to perform 51% attacks. Lightning Network is really cool in a lot of ways, but it makes this problem worse.

I don't know where your 13k/sec bandwidth figure comes from. Do you mean 130 kB/s? That's about what our nodes are currently using with 400 kB blocks. At 4 MB blocks, that would be 1.3 MB/s. Yes, I think that some people are making the argument that miners won't be able to afford 1.3 MB/s. I'm making the argument that we can afford it and that miners would get more in revenue than they would spend on bandwidth.
And I said:

>1.25% is the floor on what we can pay for internet connectivity. It's just two business-class internet connections from a local ISP in our neighborhood. We pay about $65 per connection per month. We spend more money on water than we do on internet. Even with 8 MB blocks, we'd spend more on water than on internet.

>Keep in mind that running a full node is optional. Small miners can use pools, where the pool runs the full node. The bandwidth requirement for pooled mining is about 0.1 KiB/s per miner, and is not related to block size at all.

>They can also run their full node on a VPS in a datacenter with much lower bandwidth costs than we have. You don't need your miners and your fullnode to be in the same place. 100 ms latency does not make a significant difference in stale and orphan rates when new blocks are found once every 600 seconds.

>Sharing a 40 Gbps line is much cheaper per Mbps than having a dedicated 100 Mbps line.

You can do a million satoshi if you feel like it.
No, I've been refusing donations for years. I prefer people to give donations or tips to relative newcomers to crypto. $10 means nearly nothing to me, but it means a week of freedom from hunger for a family in Venezuela.

/u/EatBCH can you reply here so I can tippr you?
Sample output from running the sim follows. The output first shows the blocks that were mined in a sample run. Next, it goes through the calculation for the penalties, PoW, and score. (In this sample, the defender accumulated a penalty of 2.27 because its later blocks came after the attacker's, but the attacker accumulated a penalty of 8.91 because its early blocks were came long after the defender's.) Third, it tells you who won that sample round. Finally, it goes through 10,000 rounds and compiles some statistics for the contests with the chosen parameters.

Note: Most of the contests that the attacker wins with these parameters are only 2-block reorgs. Longer reorgs are progressively rarer. Most of the contests that the defender wins are either by finalization or by exhausting the attacker. The attacker loses a lot more blocks per contest than the defender does, even though the defender has 40% of the total hashrate.

    Seed = 2566777562
    Defender                                                                 Attacker
    [     0: parent=  None, time=     0, height=  0, diff=1.00, PoW=  1.0] [     0: parent=  None, time=     0, height=  0, diff=1.00, PoW=  1.0]
    [   1-D: parent=     0, time=   413, height=  1, diff=1.00, PoW=  2.0] [   1-A: parent=     0, time=  1200, height=  1, diff=1.00, PoW=  2.0]
    [   2-D: parent=   1-D, time=   731, height=  2, diff=1.00, PoW=  3.0] [   2-A: parent=   1-A, time=  1200, height=  2, diff=1.00, PoW=  3.0]
    [   3-D: parent=   2-D, time=   790, height=  3, diff=1.00, PoW=  4.0] [   3-A: parent=   2-A, time=  1200, height=  3, diff=1.00, PoW=  4.0]
    [   4-D: parent=   3-D, time=  1618, height=  4, diff=1.00, PoW=  5.0] [   4-A: parent=   3-A, time=  1200, height=  4, diff=1.00, PoW=  5.0]
    [   5-D: parent=   4-D, time=  2780, height=  5, diff=1.00, PoW=  6.0] [   5-A: parent=   4-A, time=  1762, height=  5, diff=1.00, PoW=  6.0]
    [   6-D: parent=   5-D, time=  3627, height=  6, diff=1.00, PoW=  7.0] [   6-A: parent=   5-A, time=  2023, height=  6, diff=1.00, PoW=  7.0]
    [   7-D: parent=   6-D, time=  3707, height=  7, diff=1.00, PoW=  8.0] [   7-A: parent=   6-A, time=  2835, height=  7, diff=1.00, PoW=  8.0]
    [   8-D: parent=   7-D, time=  3710, height=  8, diff=1.00, PoW=  9.0] [   8-A: parent=   7-A, time=  3402, height=  8, diff=1.00, PoW=  9.0]
    [   9-D: parent=   8-D, time=  3777, height=  9, diff=1.00, PoW= 10.0] [   9-A: parent=   8-A, time=  3568, height=  9, diff=1.00, PoW= 10.0]
    [  10-D: parent=   9-D, time=  3827, height= 10, diff=1.00, PoW= 11.0] [  10-A: parent=   9-A, time=  3600, height= 10, diff=1.00, PoW= 11.0]
    [  11-D: parent=  10-D, time=  5100, height= 11, diff=1.00, PoW= 12.0] [  11-A: parent=  10-A, time=  3970, height= 11, diff=1.00, PoW= 12.0]
                                                                           [  12-A: parent=  11-A, time=  4142, height= 12, diff=1.00, PoW= 13.0]
                                                                           [  13-A: parent=  12-A, time=  4403, height= 13, diff=1.00, PoW= 14.0]
                                                                           [  14-A: parent=  13-A, time=  4461, height= 14, diff=1.00, PoW= 15.0]
                                                                           [  15-A: parent=  14-A, time=  4474, height= 15, diff=1.00, PoW= 16.0]
                                                                           [  16-A: parent=  15-A, time=  4524, height= 16, diff=1.00, PoW= 17.0]
        Block    1-D increased chain penalty by 0.00 to  1.00 from    0 sec delay
        Block    2-D increased chain penalty by 0.00 to  1.00 from    0 sec delay
        Block    3-D increased chain penalty by 0.00 to  1.00 from    0 sec delay
        Block    4-D increased chain penalty by 0.22 to  1.22 from  418 sec delay
        Block    5-D increased chain penalty by 0.34 to  1.56 from 1018 sec delay
        Block    6-D increased chain penalty by 0.37 to  1.93 from 1604 sec delay
        Block    7-D increased chain penalty by 0.15 to  2.08 from  872 sec delay
        Block    8-D increased chain penalty by 0.04 to  2.12 from  309 sec delay
        Block    9-D increased chain penalty by 0.02 to  2.14 from  209 sec delay
        Block   10-D increased chain penalty by 0.02 to  2.16 from  227 sec delay
        Block   11-D increased chain penalty by 0.08 to  2.24 from 1130 sec delay
        Block 12-TBD increased chain penalty by 0.02 to  2.26 from  382 sec delay
        Block 13-TBD increased chain penalty by 0.01 to  2.26 from  121 sec delay
        Block 14-TBD increased chain penalty by 0.00 to  2.27 from   62 sec delay
        Block 15-TBD increased chain penalty by 0.00 to  2.27 from   50 sec delay
        Block 16-TBD increased chain penalty by 0.00 to  2.27 from    0 sec delay
    Overall for 11-D: score=4.850913, pow=11.000000, penalty=2.267614
    
        Block    1-A increased chain penalty by 6.56 to  7.56 from  787 sec delay
        Block    2-A increased chain penalty by 0.98 to  8.54 from  469 sec delay
        Block    3-A increased chain penalty by 0.38 to  8.92 from  410 sec delay
        Block    4-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block    5-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block    6-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block    7-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block    8-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block    9-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   10-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   11-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   12-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   13-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   14-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   15-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
        Block   16-A increased chain penalty by 0.00 to  8.92 from    0 sec delay
    Overall for 16-A: score=1.794551, pow=16.000000, penalty=8.915877
    
    Defender won the sample round above
    
    Parameters: att_hashrate = 1.50, def_hashrate = 1.00, attacker_delay = 1200, attack_endurance = 60000, maxreorgdepth = 10
    Defender won 4457 of 10000 rounds
    Defender orphan rate:   37.6%
    Attacker orphan rate:   47.6%
    Defender finalizations: 43.9%
    Attacker finalizations: 0.0%
    236075 139623 96452 36244 66507 30573
    
    Reorg_size	Probability
    0       	0.0%
    1       	8.4%
    2       	16.2%
    3       	9.3%
    4       	4.2%
    5       	2.7%
    6       	2.2%
    7       	1.6%
    8       	1.1%
    9       	1.0%
    10       	0.9%
    11       	0.7%
    12       	0.7%
    13       	0.7%
    14       	0.6%
    15       	0.3%
    16       	0.3%
    17       	0.4%
    18       	0.3%
    19       	0.2%
    Chance of >=10 block reorg: 5.1%

I mean, they're including people's transactions. ABC's mempool is empty. They're including OP_CDSV. They've got reasonable amounts of bitcoin days destroyed. There don't appear to be any orphans except for the one Waterhole block early on. If this is an attack, what is being attacked?
Why did Bitcoin SV wait until ~~early August~~ late July before privately publishing the desired spec for their hard fork to other devs? Why did Bitcoin SV not publicly publish the hard fork spec until after the feature freeze date of August 15th? Is it Bitcoin SV's belief that public input on hard fork proposals is irrelevant until after the feature set for a fork has been frozen?
Yes, BTC should only be used by people who really know what they're doing. BCH, on the other hand, is digital cash for everybody.
This was a BU vote on a Bitcoin Unlimited Improvement Proposal (BUIP).

Deadalnix is a member of BU. He is also a developer for Bitcoin ABC, but that has nothing to do why he was allowed to vote here. He is not a developer of BU.

Norway is just a member of BU. I've only seen him in the bitco.in forum. He doesn't code for BU.

Digitsu knows how to code, but he doesn't contribute much to BU's codebase.

Reina is just a member of BU. She's active on twitter as well. She doesn't code.

AdrianX is just a member of BU. Doesn't code.

Peter_R doesn't do much C++ work. He's sort of a dev. More of a scientist, though.

Basically, the BU coders are: Peter_Tchipper, sickpig, thezerg, sorta peter_r, sorta kyuupichan, and sorta freetrader. There are also dgenr8 (XT), Mengerian (ABC), and deadalnix (ABC) who are not active coders for BU.

There may also be a few others whose names I don't recognize, but most of the rest are non-devs.
I agree with you. I think the November fork timeline is too aggressive for a change as big as the lexical CTOR. I think May 2019 is a better schedule. While I personally have reviewed the proposal in detail and think it's a good idea, I do not think we've given the rest of the community enough time to review it. Regardless of whether or not it's technically safe to fork in, it is not politically or socially safe to fork in yet. Social factors matter just as much as technical ones for a project like this.

/u/deadalnix 
Jihan? Huh?

> I think \[BU is\] next in the line of succession.

I don't like the "line of succession" argument. Bitcoin Cash is a community. We all make the decision together. Bitcoin ABC is a respected part of that community, and has shown a level of competence that warrants us giving them the benefit of the doubt a lot of the time, but they can be wrong. We should always be double-checking their work and their proposals, and we should also always encourage contributions from other teams. If BU comes forward with a proposal (e.g. Graphene), we should give it equal standing as a proposal that came from ABC, albeit with a bit more code review to look for bugs given the BU team's history of zero-days.

But yes, I have been spending a lot of time talking with the BU folks recently. Far more than ABC. I like the BU guys and think they're doing good work. I would have preferred to my OTI block validation in BU instead, but when I looked at the code, their version of ConnectBlock was messy and complicated by their parallel block validation code, so making the changes in Bitcoin ABC was far easier.
Hmm, that does look pretty suspicious. As if they have 20 bot accounts to auto-upvote.
A year ago, miners were making $1.30/kWh on typical expenses of $0.06/kWh. Now that the price has fallen and the hashrate has increased, we're only making about $0.10/kWh on expenses of $0.06/kWh.

It has nothing to do with speculation. It has nothing to do with squeezing other miners out of the game. The only reason that hashrate is increasing is because it's still profitable to do so.

Changes in hashrate are limited by the production capacity for new miners and the development speed of new datacenter projects. Hashrate increases lag behind price increases by 3 to 24 months. The hashrate increase that we're getting right now is still being driven by the ramp up in BTC price from $700 in Jan 2017. The price has increased 10x since then. It takes quite a while to engineer and build out facilities to provide a 10x increase in mining.

I am an industrial miner. In Jan 2017, our mining activity was 0.7 MW. At that time, we had been working on an expansion project for about 12 months which we were working on as hard as we could. That came online shortly afterwards. By the end of 2017, we had 2.0 MW of miners running. We immediately started development of another project to add another 8 MW of capacity, but that project has been stalled as our utility company is unable to keep up with the load growth. Consequently, we probably will be unable to increase our capacity any more for at least 18 more months. This is how it goes in industry: changes in market conditions result in delayed responses in deployments.
Yes, it does cost miners something to include transactions in blocks when the blocks aren't full. The larger a block is, the greater its chance of being orphaned. Given an empirical block propagation speed of about 1.6 MB/s and a block subsidy of 12.5 BCH, the cost to a miner for including a transaction is [about 0.625 sat/byte](https://www.reddit.com/r/btc/comments/91284i/where_can_i_view_orphaned_block_for_bch/e2vml2u/). If a transaction pays less than this amount in fees, then the miner would have a negative marginal expected revenue for including that transaction.
I wasn't aware that the definition of "shitcoin" was precise enough to allow a calculation with two significant figures. 

I also wasn't aware that the definition of "shitcoin" encompassed all but the top 9 coins. But reverse-engineering your claim seems to indicate that, so I guess it must be true, right?

But seriously: dismissing cryptocurrencies like ZCash, Dash, Monero, Tether, and Siacoin as "shitcoins" and therefore of no significant value is not reasonable. Each of those cryptocurrencies has substantial technical innovations and trading volumes between $7m per day (for Sia, the smallest) to $2040m per day (for Tether).

But even if you were right and only the top 9 cryptocurrencies mattered, the combined market cap statistic would still be basically the same. The top 9 cryptos are and have always been the vast majority of the total market cap. 
The argument that the Core side gives is this:

1. The cost of running a high-performing full node is proportional to (or directly affected by) the block size limit.
2. If the cost of running a good full node is high, then small miners will be unable to run their own high-performance full node.
3. Running a low-performance full node increases orphan rates, which harms small miners and encourages centralization.
4. If small miners cannot afford a good full node, then small miners will choose to mine on large pools.
5. If all mining is done on a small number of large pools, then Bitcoin will be vulnerable to censorship or attack.

This argument sounds reasonable, but it is quantitatively absurd. I'll give a few numbers to illustrate why.

1. I run a medium-small mining operation. I currently have about 0.03% of the Bitcoin hashrate, plus some hashrate for altcoins. Let's call that 0.1% total. I run a few high-performance full nodes. These nodes have enough capacity to handle around 20 MB blocks with acceptable performance (i.e. with an acceptable orphan rate). This costs me about $80/month, including hardware, electricity, and internet connectivity.
2. I currently pay about $30,000 per month on electricity for that 0.1% of the Bitcoin hashrate. $80/month is nowhere near significant to my bottom line.
3. Block relay takes about 140 ms for a 1 MB block. Most of that is just speed-of-light delay. The formula is roughly [130 ms + 10 ms/MB](https://www.reddit.com/r/btc/comments/7jf0eu/cobra_its_easy_to_understand_why_bitcoin_cash/dr6dqij/) or less when using Bitcoin FIBRE (the best relay network). That means that a 100 MB block would take about 1.1 seconds, which would give a marginal orphan rate of around 0.18%. 0.18% is far less than the fees that most mining pools charge, so it would not be a significant contributor to the economics of mining.
4. Small miners choose to mine on large pools because pools reduce payout variance. I actually (mostly) solo mine, but the variance hurts. For example, we had zero Bitcoin mining revenue between September 25th and December 15th because we just got really unlucky. That's maybe a few hundred thousand dollars worth of revenue that we didn't get because we were unlucky. We were able to survive that dry spell because we don't have any investors to keep happy and we had Bitcoin in the metaphorical bank, but most miners cannot tolerate that kind of risk. This variance effect is several orders of magnitude stronger than the $80/month full node cost or the 0.18% orphan rate cost.
I hate to say it, but it looks like these blocks might have had a bunch of spam. There's a suspicious group of 64.3 kB SegWit transactions in both of these blocks:

https://www.smartbit.com.au/block/484399/transactions?sort=size&dir=desc

https://www.smartbit.com.au/block/484398/transactions?sort=size&dir=desc

Block #484398 has 8 of these transactions, and #484399 has 10 of them. All told, that's about 1155 kB of space used by one entity in two blocks.

Each of these transactions has 200 inputs and 1 output. At 64.3 kB per tx, that amounts to roughly 321 bytes per input. That sounds like a multisig tx, which is a well-known way to pack more bytes into the same weight with Segwit.

It's also possible that these transactions belong to an exchange or some other large entity that uses multisig. Still, it's weird, seemingly artificial, and clearly one entity that's doing this. Does anyone know of any exchanges that use P2SH or P2WSH deposit addresses?

Edit: more data [here](https://www.reddit.com/r/Bitcoin/comments/6z3rz7/13mb_segwit_block/dmsh999/) thanks to /u/dooglus.
I hate to say it, but it looks like these blocks might have had a bunch of spam. There's a suspicious group of 64.3 kB SegWit transactions in both of these blocks:

https://www.smartbit.com.au/block/484399/transactions?sort=size&dir=desc

https://www.smartbit.com.au/block/484398/transactions?sort=size&dir=desc

Block #484398 has 8 of these transactions, and #484399 has 10 of them. All told, that's about 1155 kB of space used by one entity in two blocks.

Each of these transactions has 200 inputs and 1 output. At 64.3 kB per tx, that amounts to roughly 321 bytes per input. That sounds like a multisig tx, which is a well-known way to pack more bytes into the same weight with Segwit.

It's also possible that these transactions belong to an exchange or some other large entity that uses multisig. Still, it's weird, seemingly artificial, and clearly one entity that's doing this. Does anyone know of any exchanges that use P2SH or P2WSH deposit addresses?

Edit: more data [here](https://www.reddit.com/r/Bitcoin/comments/6z3rz7/13mb_segwit_block/dmsh999/) thanks to /u/dooglus.
The chain split happened because a lot of people didn't like SegWit and wanted a blockchain without it. Bitcoin Cash took some of BTC's hashrate because of SegWit. 
> You know that the Lightning Network is developed under the assumption that SegWit eventually activates, yes? 

This conflict between the LN developers (including Jeffrey and Poon) and the Core developers (including Corallo and Maxwell) began in part because the LN developers proposed a method of solving transaction malleability and guaranteeing space in blocks for LN channel close transactions via an extension block instead of SegWit.

https://github.com/tothemoon-org/extension-blocks

https://www.cryptocoinsnews.com/schism-developing-lightning-network-bitcoin-core-developers/

https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-April/013981.html


> It's an opensecret that Jihan is using the smaller puppets pools to signal BU

That's an interesting rumor. I'll make sure to keep it a secret from the Illuminati. It's an open secret that the Illuminati want to make sure that they don't have any competition when it comes to monopolies on financial systems, and would crush Bitcoin if they knew how powerful Jihan's pet project was becoming.

But seriously, there's another explanation for why miners are voting for Unlimited. Maybe they're doing it *of their own accord*. Maybe *some people other than Jihan* actually prefer Unlimited, and are willing to put up their own hashrate in favor of it.

*Non sunt multiplicanda entia sine necessitate.* Entities must not be multiplied beyond necessity. And the corollary: don't posit the existence of a conspiracy to explain data that can just as easily be explained without the existence of a conspiracy. If you do, it reveals your desire for there to be an evil conspiracy more than it says anything about the data itself.
Antminer S7s (which make up the bulk of the current network hashrate) are still earning around $0.11/kWh. Even the 18-month-old Antminer S5 is still earning $0.06/kWh. The brand-new Antminer S9 earns about $0.31/kWh. Most miners pay around $0.04-0.05/kWh for electricity.

Miners (like me) are turning off some obsolete equipment and replacing it with S9s. Our power consumption isn't changing much, but since the new hardware is about 5x to 10x more efficient than what we're shutting off, the hashrate increases.
They were submitting code with known issues, claiming that it was "good for 0-conf!" It was pretty transparent, but it was hard to justify shutting down all of the threads without seeming like an asshole. (I knew most of them were changes that were associated with problems, but I couldn't remember the exact issue for all of them.)

But yeah, circumstantial evidence.
> It's not actually your miner though, right? 

https://en.wikipedia.org/wiki/Glomar_response
Hashpower rental and purchase of obsolete equipment is the justification for 51% attacks at lower than $2000/block. Bitcoin is secured against 51% attacks by keeping the capital costs of acquiring enough equipment to perform an attack high. If the profitability of running miners decreases to the point that the least efficient 50% of miners are rendered obsolete and taken off the network, then the cost of acquiring miners or renting them for a short period of time plummets. In order to prevent that from happening, you have to keep those miners on. At 300 MW (our current network hashrate), that turns into about $4000/block. At $2000/block, about half of them would be turned off, which makes these rental attacks somewhat feasible.

It takes a lot more bandwidth than (blocksize / 600 seconds) to run a bitcoin full node, by the way.

LN is not pertinent unless you rely on it to scale Bitcoin instead of enlarging blocks.

> Should there be a hard fork taken when there is legitimate and wide spread disagreement over the validity of the hard fork?

There will be no fork with XT unless 75% of miners vote for it. I would call 25% "narrowly spread disagreement".

> Should there be dictatorial leadership from Mike Hearn on the consensus code? (that's one of the stated goals)

I think Mike Hearn should have dictatorial leadership of his Bitcoin client. That's no problem, in my eyes. The problem is if Mike Hearn has dictatorial (or consensus-based) control of the only accepted Bitcoin client. I think that there should be several different options for clients. I think that no single development team should have complete control over the Bitcoin network. That's what we have right now. Consensus should be de-facto, determined by which clients the users and miners of bitcoin decide is the best client or clients. Consensus of support should be determined on the network by the use of feature support flags. If BitcoinXT plus BitcoinXL plus BitcoinJGarzik plus everything else comprise 75% of hashpower and all of them announce support for BIP101 blocksizes, then we should allow BIP101 blocksizes on the network.

One of the core principles of Bitcoin is that it's supposed to be a decentralized network. Right now, Bitcoin development is about as centralized as it can get, with only one Officially Sanctioned Client. Any attempts to make a change that isn't sanctioned by the Council of Five, the five devs with git commit access, is blocked. Using BitcoinXT is a vote that all clients are equally valid. Thus, when it comes to the core principle of decentralization, Bitcoin XT is more core than Core.
Some people are arguing for lifting the 21M coin cap, yes. There are two classes of arguments that can be made against raising the 21M cap:

1. Raising the 21M cap would require a hard fork, and hard forks are dangerous.
2. Raising the 21M cap would be really stupid, as even a hint of doing this would devalue the currency.

You're making a classic slippery slope argument. If we get rid of reason #1 why not to do it, there's still the other reason not to do it, and #2 is the only reason that's needed.

Just because gay people are allowed to marry in the United States doesn't mean people should be allowed to marry horses or goats, if for no other reason that non-human animals are not capable of legally providing consent.
> Optimizing for latency is the difference between dozens of centralization favoring orphans a day and one or two.

and

> Orphaning (and esp selfish mining / centralization pressure) are a lot more complicated than just evaluating a CDF, as results in bitcoin have shown.

Please show me how an extra 200 ms of latency can result in dozens of orphans per day. This claim seems incorrect to me, and seems to be the center of our disagreement for all of the rest of your comment. 

My understanding is that 200 ms of additional latency would add approximately (1-e^(-0.2/600)) * 144 = 0.048 orphans per day. This is consistent with BTC's current performance, which has end-to-end block propagation latency of around 150 ms and an orphan rate of [6 blocks per year](https://www.blockchain.com/btc/orphaned-blocks) or 0.016 orphans per day. That's not a statistically significant deviation from the expectation based on the CDF.

Furthermore, my belief is that 0.05 additional orphans per day is not worth arguing about. If your belief is that 0.05 orphans is a showstopper, then I suppose we have nothing more to discuss on this topic. If your belief is that 200 ms would cause more than 0.05 orphans per day, I would like to see some evidence and/or math for that claim.

> Presumably your 1GB block would be processed on computers more like "PHI cluster" than a "rpi" (or at least thats what the scam visionary says

No, my target is to be able to handle 1 GB blocks on an enthusiast-grade desktop machine. If it can run modern games, it should be able to run BCH with 1 GB blocks. I think CSW is nuts, and I'm glad that he left BCH to found his own centralized coin.
> 10kb vs 1kb serialization delay is a rounding error compared to a 200ms round trip time. ... unconditional roundtripless transmission has been a solved problem ...

Xthinner isn't made for 1 MB blocks. It's made for 10-1000 MB blocks, with the intent on scaling to 1-1000 GB once Blocktorrent is added. At 1 GB, we're talking about 15 MB vs 4 MB transmission delay per hop. That's about a 1 second per peer per hop difference on 100 Mbps. A 5% chance of a 200 ms RTT is small potatoes in the intended use case. Even a 100% chance of a 200 ms delay is acceptable in my book -- for a pool with 30% of the hashrate, that would only incur a 0.3•(1-e^-0.2/600) = 0.01% orphan rate/revenue advantage. I don't think that's a significant enough centralization force to panic over. Sure, it's nice to remove that delay if we can, but fixating on it is penny-wise and pound-foolish.

Trying to optimize for that last 100 ms makes sense for BTC, since the block size is constrained there and there's nothing else to optimize for. But in BCH, we have bigger ambitions.

> Certainly isn't clear in the thread here, nor in your reply, nor in the figure's you've provided.

I don't have the time to repeat every obvious caveat in every conversation I have with someone who is already aware of them. I also don't have time to correct every news article written that omits those caveats, though I make an effort to do so when I notice.

> In compact blocks the attacker cannot generate the collision in the first place. The only collisions that can happen there are chance ones, and the IDs are intentionally someone oversized to make their probability negligible.

CB's probability of collisions is not negligible in BCH's intended scaling roadmap. The probability of random collisions is about 1% at 1 GB, and close to 50% at 12 GB. So once again, you and I are discussing different scenarios.

> In compact blocks the attacker cannot generate the collision in the first place. The only collisions that can happen there are chance ones, and the IDs are intentionally someone oversized to make their probability negligible.

Assuming that code is written to deal optimally with collision scenarios as I mentioned in my earlier comment (time-of-arrival resolution, checksum resolution), the only scenarios in which Xthinner would need to do a round trip is a scenario in which Compact Blocks would *also* need to do a round trip. And it doesn't matter, because a 200 ms delay is tolerable. Limiting blocks to 1 MB or 32 MB forever is not.

> (and no, adapting the ID lengths to the minimum required to not collide locally is not a new idea -- and the same arguments against it apply.)

That isn't the only idea in Xthinner. The new idea is using CTOR and the Xthinner stack state machine to do lossless compression of the prefix bytes. This is why Xthinner can code e.g. 40 bits of TXID data in only 12 to 16 bits. Consequently, random collisions are improbable with Xthinner for any block size, unlike CB or Rusty's proposed variable-length scheme.

> You misunderstand. Zero bits per tx in the block, N bits per transaction in the symmetric difference.

There are already other projects like Graphene which are trying that approach for BCH. Xthinner is designed for robustness, for gracefully degrading performance, for low CPU usage, and for future segmented use in Blocktorrent. Xthinner is not intended to be the best thing possible; it's just intended to be better than what we have without any significant new drawbacks.
I'm much more worried about whether Blocktorrent will ever get finished for Merkle trees than about whether it can be updated for Merklix. I think time would be better spent coding than talking about coding at the moment.
If you start with -dbcache=8192 as a command-line option, it will use up to 8 GB of RAM and will sync about 10x faster.

The bottleneck is not downloading. The bottleneck is reading and writing transaction outputs to the UTXO database.
Nice find! To fix:

https://www.reddit.com/prefs/

Uncheck "allow subreddits to show me custom themes"

Or use RES to do this only for /r/bitcoinsv by clicking on the RES icon after loading /r/bitcoinsv.
The Xthinner WIP currently gets a 2.5 million TX block down to 3.6 MB. At 400 bytes/tx, 2.5 million TX would be a 1 GB block.

     jtoomim@light-star:~/dev/bitcoins/xthinner-newabc/src$ test/test_bitcoin --run_test=xthinner_tests
    Running 1 test case...
    Testing Xthinner on a block with 3 transactions with mempool size 2500002
    Tx/Block creation took 122301304 usec, 48920 ns/tx (mempool)
    Sorting took 5344361 usec, 2137 ns/tx (mempool)
    Encoding is 2735833 pushBytes, 5471664 commands, 207530 checksum bytes
    total  3627321 bytes, 11.6074 bits/tx
    Single-threaded encoding took 5559180 usec, 2223 ns/tx (mempool)
    Serialization/deserialization took 777090 usec, 310 ns/tx (mempool)
    Single-threaded decoding took 3053430 usec, 1221 ns/tx (mempool)
    
    *** No errors detected

It will take a while to get enough demand for 1 GB blocks, though.
> Rewriting history is already solved by the incentive structure of Bitcoin, read chapter 6 of bitcoin.pdf.

Section 6 is weak, and does not apply in multi-chain antagonistic scenarios, nor in hashrate rental scenarios. Satoshi's argument in section 6 is this:

> He ought to find it more profitable to play by the rules, such rules that favour him with more new coins than everyone else combined, than to undermine the system and the validity of his own wealth.

But what if attacking a chain *adds* to the validity of his own wealth, because that person either has a short position on the chain (and is renting hashrate, and otherwise not invested in its survival), or if the attacker holds a long position in a competing chain like BTC or BSV? 

If you're relying on Satoshi's 10-year old ideas to defend against every possible scenario, you're going to be disappointed. Satoshi was definitely ahead of his time, but he wasn't a god. The economics of mining have changed a lot since then, and so have the economics of the attacks.
If there's an exchange that requires 20 confirmations for a 1 million deposit, then the attacker just reorgs after 21 blocks. As long as the attacker has 51% of the hashrate and we don't have something in place that specifically prevents secret mining attacks, they can reorg as many blocks as they want given enough time.

The attacker can also do 1,000 attacks in parallel of $1k each.
This dataset is the same format and analysis as described in my [September article](https://medium.com/@j_73307/block-propagation-data-from-bitcoin-cashs-stress-test-5b1d7d39a234). The middle columns show the elapsed time (in seconds) between the first node in the dataset having received and validated a block and the node for that column having received the block. 120 means 2 minutes to reach that node, for example, and is very bad. There's a lot of stuff in there that's far worse than that. A few instances of 1200 seconds or more.

I'll do a more complete analysis and writeup later, and include more data (like the 63 MB block), but I thought some of you might want to look at the raw data as soon as I had it, so here you go.

If you have debug.log files from servers with proper clock settings either from the SV chain (BUSV or BSV) or from ABC, I will be interested in getting copies of those files from you in a bit.
I will never support any blockchain that is ruled by miners, developers, and users who condone 51% attacks. If it is not possible to maintain a viable ABC+BU+XT+BitPrim+bcash etc. blockchain, then I will sell all of my BSV holdings, either for other currencies like ETH and ZEC, or for fiat. I will not sell my ABC-BCH holdings; instead, I will continue to try to recover the ABC blockchain as long as it is viable to do so, using whatever methods I have at my disposal (e.g. bitcoin-cli invalidateblock).
LTOR *cannot* be used without a consensus rule change. Gavin's order can be used without a consensus rule change, but Gavin's order is [probably slow enough to be problematic](https://www.reddit.com/r/btc/comments/9cq62n/during_the_bch_stress_test_graphene_block_were_on/e5zweh2/). And in order for Gavin's order to protect us from the Graphene outcast attack, it would need to be a consensus rule anyway.
Here's the rough draft.

https://medium.com/@j_73307/p2pool-as-prior-art-for-nchains-turing-complete-transactions-patent-or-how-to-patent-all-40f3d429eaa4
The Bitcoin code has multiple threads for doing work. However, most of the code can only be running in one thread at a time, because otherwise corruption of shared data structures could result. As a result, most tasks are only done by one thread at a time, and so usually only one core of your CPU will be active no matter how much load there is.
> the miners decide not you

I **am** an industrial-scale miner, so yes, **I** decide.

> let's stick to what the tweet actualy says not what you imagine

I'm not responding to the tweet at all. I'm responding to a question that someone asked me, based on the knowledge I have about industrial miners from having been one for four years, and from having also been a Bitcoin developer at the same time.

> Who are you again that has such a deep knowledge of Haipo's expertise in this subject?

I don't know Haipo personally, but I do know Wang Chun (F2pool), Micree Zhan and Kevin Pan (Bitmain), Alex Petrov (Bitfury), Sam Cole (defunct KNCMiner), Guy Corem (defunct Spondoolies), as well as quite a few other people in the mining community. In 2015-2016, I performed [a survey](https://www.reddit.com/r/btc/comments/3ygo96/blocksize_consensus_census/) of attitudes held by the world's major miners and pools toward various hard-fork blocksize increase proposals, and worked with Gavin Andresen, Marshall Long, and Olivier Janssens to get them all on board with a single fork option. Although our attempt at establishing consensus support for a fork ultimately failed once the Core trolls started fighting back, I think I'm fairly well qualified to speak about how miners and pool operators think and act when it comes to major protocol changes in the coins they mine.
Okay, there's more to it than just the free-rider problem. Sorry, I let the opportunity for snark get the best of me.

> Could you speak to why none of the big pools/miners has a public protocol dev presence? 

Part of the issue is also that Bitcoin protocol design and implementation is not ~~his~~their core competency; mining is. ~~He has~~They have a very direct and strong incentive to spend most of ~~his~~their time optimizing ~~his~~their own little fiefdoms, and that's what ~~he~~they have gotten to be the most knowledgeable about. ~~He~~They only have a weak incentive to work on the protocol itself.

I suffer from the same effect. I try a lot harder than most miners do to write code that benefits the community as a whole, but so far my biggest contributions have just been to p2pool, a mining platform of which I am the largest user. My attention is just naturally drawn to my own little fiefdom, and I don't make much progress in helping projects that aren't related to mining. I thought I might be able to combat that effect by paying other people to work on the stuff that I don't work on, so I donate money into a Bitcoin Cash development fund via some miners which I have set up to hash into that fund's address. We started doing this donation 9 weeks ago, and so far exactly none of it has been spent by the donation fund. Basically, it seems that you can't just throw money around to make things happen; you have to also invest time in managing the people you pay, and I haven't managed to do that.
It sounds like you're experiencing mania. This is not related to your vitamin D intake. You should read about bipolar disorder, as it's possible that you have it. If you are taking any stimulants, those may also be responsible for the effects.
[5,305 lines added, 571 removed](https://github.com/bitcoin/bitcoin/pull/8149/files) with SegWit.
He's referring to block size (through the use of Lightning or other efficiency improvements) **not the block size limit**.
LN is not some magic fix. It won't increase capacity instantly by infinity.

With LN, you go from one on-chain transaction per financial transaction to two larger on-chain transactions per account per billing interval. A billing interval might be on the order of a month. If a typical Bitcoin user does 15 transactions per month, and if LN channel open/close transactions are 1.5x as large as regular transactions, then LN would only increase capacity by about 5x.

At 1 MB and 500 bytes per tx, with LN channels lasting an average of one month each, Bitcoin could only support around 4.3 million users, assuming Bitcoin was used for nothing except LN. In 2012, there were **[883 million](https://www.statista.com/statistics/279257/number-of-credit-cards-in-circulation-worldwide/)** VISA users worldwide. To get to that level, Bitcoin blocks would need to average 204.4 MB in size. To get to VISA-level transaction volumes (~2000 tps, 400 bytes/tx) without LN, blocks would need to be around 480 MB in size.

LN will make scaling easier, but it's not a fix. We will still need to increase the blocksize a lot. Most of the value of LN will be in enabling new use cases for Bitcoin, such as micropayments (e.g. the distribution of ad revenue). However, for the semi-infrequent large-value transfers that Bitcoin has traditionally excelled at, Lightning will be at best a moderate improvement, and at worst an awkward system with a complicated UX.
Bitmain probably supplies 80% of the miners for all BU-powered pools. Bitmain also supplies about 80% of the miners for all SegWit-supporting pools.

(Note: Although Bitfury is not Bitmain-supplied and supports SegWit, Bitfury is not a pool. They are a large solo miner.)

If you go to https://shop.bitmain.com/main.htm?lang=en, you'll notice there is no checkbox for "I am planning on running this Antminer S9 on a BU pool" that magically reduces the price. If you buy one or a dozen S9s, you get to choose for yourself what pool you want to run it on. Jihan does not make that decision for you, nor does he have the ability to influence you in any way.
Ethereum was able to add 20 million accounts in a matter of days without failing, although it inconvenienced many users. I'd say that bodes well for Ethereum's scalability.
It means that the signature isn't valid. This is a forgery, and not even a particularly good one.
Ethereum doesn't have a ~~sky~~ block size limit.
> There are no good arguments against 2mb blocks.

There are some mediocre arguments against 2 MB blocks, though. Those arguments sound like good arguments to people who have different value systems than yours or mine.
> users delaying block relay to vote

I don't think this can work. It's very inexpensive to run a half-node, a node that doesn't fully validate but relays data. If a miner wants to relay large blocks, setting up a network of half-nodes around the world is much cheaper than mining is.

Also, miners can just connect directly to each other.

I had some experience with the BIP101 hardfork in operating a fork in a scenario in which the hashrate majority is a full node minority. It was annoying. That's all. We ended up manually specifying addnode=.... lines for every node in our testnet network. More elegant solutions are possible (e.g. dynamic peer blocksize limit discovery, and selective connections to peers with similar limits). 

In general, I see a lot of people hold the opinion that full nodes matter. I do not share this opinion. Full nodes are nearly indistinguishable from halfnodes, and essentially identical to full-node-backed-halfnodes. They are not a robust voting mechanism against someone who wants to manipulate the vote.
> Hearsay testimony from a biased and personally invested guy reporting the supposed words of non-CEOs.

http://www.swissapp.mobi/2015/12/12/btcc-coo-samson-mow-without-consensus-on-block-size-limit-stakeholders-could-carry-out-an-increase/

Hearsay testimony it is not. The non-CEO criticism is accurate.

Isn't everybody personally invested?
The cheapest power in the world is renewable. We, for example, use hydropower to get our 2.5¢/kWh rates. I want to outcompete miners who are running on coal, because coal emissions kill about 300,000 people per year worldwide.

Weeding out the miners who have very low bandwidth will cause the rest of the bitcoin network to work faster and at higher capacity, which I also consider a good thing. People in Africa will have to use SPV wallets, which I consider a bad thing, but I think that is a minor problem and outweighed by the benefits.

Non-mining operations like Coinbase can move to where the bandwidth is more easliy than mining operations, actually. Bandwidth is far cheaper in major hub cities like San Jose, Tokyo, Hong Kong, and Frankfurt. https://en.wikipedia.org/wiki/List_of_Internet_exchange_points_by_size . These internet hub cities tend to be in large population centers, and the electricity prices there tend to be pretty high. Electricity prices are usually lowest near hydroelectric dams in remote areas, where supply is high and demand is low. Because of their location, bandwidth tends to be limited.
It's a reverse merger. They bought Squire Mining (a publicly traded company), and are now transferring privately-owned assets to the public company so that they can sell stock publicly without going through the slow and expensive IPO process.

https://en.wikipedia.org/wiki/Reverse_takeover
Xthinner is basically working in my dev branch. I still need to fix a few bugs, add some features (e.g. merkle root checking before relay, command-line option to enable/disable), finish up the test suite, run network benchmarks, and get it through code review.

Currently, the code is capable of propagated blocks as long as both nodes have all of the transactions in the block (i.e. synced mempools). It should also be capable of propagating blocks with missing transactions, but I haven't finished the tests for this code, so I don't know if it works or not.

I probably should have finished Xthinner by now, but I lost motivation around January and got distracted by other projects. 
Sometimes the relative profitability of mining BCH is higher than BTC. Sometimes it's lower. When the profitability is higher, even if it's only 0.001% higher, the rational thing for every SHA256 miner in the world to do is to mine BCH until the difficulty increases again and the profitability drops below BTC, at which time the rational thing to do is to switch from BCH to BTC.

Effectively, this is a difficulty and hashrate oscillation caused by a subset of miners or pools rationally seeking profit. The DAA is not perfect at keeping BCH's difficulty proportional to true instantaneous hashrate, and the correction in difficulty is delayed relative to the actual hashing, so a burst in high hashrate that ended 12 hours ago can cause the difficulty right now to be low.

There isn't an easy way to fix this issue, unfortunately. When there are pools out there who are willing to swing the hashrate by +/- 80% just to gain 2% more revenue, preventing large hashrate fluctuations is tough. If we reduce or increase the averaging period for the DAA, for example, that would change the frequency of the oscillation but not the amplitude. It may be possible to reduce the issue by changing the weighting envelope from (effectively) a simple moving average to a more complicated FIR filter, but we would need to be careful that our intended solution won't make things worse.
BCH is now both CTOR and LTOR. The LTOR term refers to the specific form of CTOR that BCH decided to implement. The second paragraph of my [introductory article on Xthinner](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0) explains those two terms.
Not really. Nodes will use whichever protocols they support in whatever order of preference they're configured for.

Graphene will probably be more efficient when it works, but Graphene doesn't work all of the time. Xthinner ought to work all the time. Xthinner can be a fallback for situations in which Graphene fails for whatever reason.

Xthinner also lends itself very well to being used in Blocktorrent. I expect that Xthinner+Blocktorrent+UDP will perform far better than Graphene on TCP will.
I see, so he lied in public and admitted it privately. What's to say that they aren't still lying, and just making a strategic choice that it's better to not be all-in on BCH mining than to be a hashrate renter?
Rented hashrate would have been on BTC beforehand. Statistical noise makes it hard to detect a 1.8 EH/s change on BTC, but eyeballing the green line on the 2 month graph on [Bitcoinwisdom](https://bitcoinwisdom.com/bitcoin/difficulty), it does look like BTC's hashrate dropped by about 1.5-2 EH/s between Nov 8th and Nov 15th, and dropped even more after that (probably from Bitcoin.com's hashrate). It's worth keeping in mind that the BTC hashrate was also absorbing BCH miners at that time who were taking temporary refuge from CoinGeek's unprofitable BCH mining before the fork.

The difficulty on BTC dropped -7.39% on Nov 16th (from 51.4 EH/s to 47.6 EH/s estimated hashrate), which is pretty unusual. That might actually be the biggest difficulty drop in BTC's post-ASIC existence.
They stated they were all-in on BCH, and criticized their opponents for not being the same. If they had BTC hash, that would be equally dishonest and hypocritical of them.
Bitcoin SV is currently mining on their chain with about 1.8-2.5 EH/s, judging from the number and difficulty of blocks that they have mined.

Before the fork, the SV crew had about 2.3 EH/s. I suppose it's possible that SV held some hashrate in reserve, or has suddenly acquired some hashrate since then, but I don't think it's likely.

It's also not likely that they would have more than 4 or 5 EH/s in reserve, which is what they would need to overtake ABC.
Because they're mining with the same amount of hashrate on the SV chain as they had before the fork. All of their hashrate is accounted for.
1. Copying another person's *writing* without attribution is plagiarism.

2. Copying another person's *ideas* without attribution is also plagiarism.

It's trivial to show that CSW did #1 in this instance. You are only arguing (weakly) against #2.
During the ETH/ETC hard fork, some businesses like Coinbase lost funds due to ETC draining attacks. The attacker would send ETH to Coinbase from an account that had no ETC, then withdraw ETH from Coinbase and replay the transactions to also withdraw ETC from Coinbase. This happened because the ETH/ETC fork lacked replay protection. Has Bitcoin SV taken any steps to prevent this type of attack from happening to exchanges with the Bitcoin SV/Bitcoin ABC+BU+XT fork? Or is this type of replay attack a design goal of the Bitcoin SV team? If so, why?
Yes, they still have the 7-14 tx per sec limit in INV broadcasts. Oops!
Why are people calling this a hard fork? I'm pretty sure it's a soft fork. They're taking something that was previously semi-allowed (including one input twice in a transaction) and making it disallowed. That purely restricts the set of acceptable blocks, and would be a soft fork.
Me too. LN is awesome. 

Just not quite as awesome as the Bitcoin base layer.
minPOW will not be necessary.

Let's say that BCH currently has 100 PH/s of hashpower, and the BCH price is $1000. CSW and Coingeek fork off and create a new chain. We now have two different contenders for the name BCH. Let's call CSW's version BCHSV (Satoshi's VisionTM) and the non-CSW version BCHUL (Unlimited, XT, and maybe ABC). Coingeek and some unknown entity bring 55 PH/s of hashpower to BCHSV, leaving 45 PH/s on BCHUL. 

Meanwhile, the markets aren't taking CSW seriously for whatever reason, so coins on the BCHSV chain are trading for $250, whereas coins on the BCHUL chain are still trading for $800. Revenue per PH/s on BCH originally was around 12.5\*$1000/100PH/s = $125 per 10 minutes per PH/s, but now on BCHSV it's only 12.5\*250/55 = $56.8. On the other hand, BCHUL now has revenue of 12.5\*$800/45 =$222. This means that profitability for mining on BCHUL is now higher than it was for BCH, and is also higher than for mining on BTC (which was in equilibrium with BCH). Consequently, a bunch of hashrate from BTC comes to BCHUL to take advantage of the profits to be had, and BCHUL ends up with 80 PH/s compared to BCHSV's 55 PH/s. At the same time, BCHSV's miners are not making enough to pay their power bills, so they give up after a week and return to BCHUL.

Hashrate follows price. The markets will determine the winner, not the hashrate.
> 128MB is a soft cap on the blocks accepted, not the hard cap they mine (32MB).

That is not what the released alpha code does. The code so far still includes a [32 MB default block size limit](https://github.com/bitcoin-sv/bitcoin-sv/blob/5ef02bedfa01bfa7408c055449b2a68771892260/src/consensus/consensus.h#L20) on blocks accepted. We are aware that the Bitcoin SV team intends to implement acceptance for 128 MB blocks in the future. We are just noting that they have not yet done that.
Almonds aren't that great of a source of magnesium. In order to get 100% of your RDA for magnesium from [these almonds](http://nutritiondata.self.com/facts/nut-and-seed-products/3086/2) you would need to eat 879 calories of almonds (42% of your RDA) every day. If you're aiming for much more than 100% of RDA, you might have to eat nothing but almonds all day every day.

Almonds are also fairly expensive. The dose above is about 145 grams, which costs about $2.56 for 400 mg of Mg assuming $8/lb of almonds. You can get 400 mg of Mg in a supplement for [$0.13](https://www.vitaminshoppe.com/p/magnesium-citrate-200-mg-300-tablets/vs-1977#!).
He's referring to block size (through the use of Lightning or other efficiency improvements) **not the block size limit**.
The thing I dislike about your assertion is that it is not disprovable. 

It seems that you're asserting that there is a special channel by which miners can get substantial volume discounts (beyond the 1.5% listed on their website) that only exists for miners who support Bitcoin Unlimited. 

Without access to Bitmain's full financial or production records, it is impossible to prove the absence of such a channel -- we'd need to document every sale that Bitmain made, and prove that it went through the existing non-politicized channels. However, if it actually existed, it should be straightforward to prove the existence of such a channel -- we would just need one instance of someone who bought Antminers from Bitmain and who got a discount or access to greater availability because of their politics. Can you provide that?

I also find it interesting that you're alleging that Bitmain is favoring Bitcoin Unlimited buyers when both of Bitmain's own pools, Antpool and BTC.com, are mining with Bitcoin Core.
This ends either when people like you stop using this peer-to-peer currency, or when we fork to increase block capacity.
>  You mean to tell me ... that doesn't represent real world usage?

I didn't say that these blocks won't reflect real-world usage. We've seen spam attacks on Bitcoin before. We'll probably see them again. It's likely that we'll see 3.7 MB spam attack attempts on mainnet with SegWit.
For BIP101, the rule is actually to start at 8 MB, doubling every 2 years, *with linear interpolation* between the doubling points.
Correct, I decided not to ask about SegWit, because I did not think that the pools and miners had had enough time to digest it. I knew that at the time I was conducting the survey, I had not had enough time to digest it either.

I also did not present Bitcoin Unlimited.
Fee was about $0.12 or 0.0026 BTC on a 2.5 kB transaction. 

https://blockchain.info/tx/728833ea5c23a44b030a04d3990eea1a93cd09ea43d04fe4828a4206bb2ec6c9
It should be a hard fork, not a soft fork. It would make a really ugly and hackish soft fork, and the coinbase message is the miners' space, not the developers' space.
Agree, downvotes are important. This isn't Facebook.
I'm continuing a conversation with /u/adam3us that was started in the [8/23/2015 discussion](https://www.reddit.com/r/Bitcoin/comments/3i2hke/august_23_2015_block_size_and_hard_fork_dialog/cucrqi5) thread.

Quoth adam:

>>I agree. It would be best if you and the rest of the core developers joined the existing consensus among miners and users in support of large (>= 8 MB) blocks.

>There are better designs that support bursting.

>You're a miner right? (toomim mining I recognise from bitcointalk) so I think it's fairly clear that for supply and demand reasons if blocks are currently < 1/2 full average and about 1/2 of those transactions are < $1, then if the blocksize is jumped to 8MB the network will be overcapacity by 16-32x. Now if there is an excess of supply, price falls, ergo fees will drop to zero basically. If you're a miner this is maybe not to your advantage.

>Hosted wallet companies may want free fees. I'm not sure if you do.

>Gavin has said some keynsian subsidy things like he wants to subsidise fees by increasing the blocksize.
Maybe come to the http://scalingbitcoin.org[1] conference or watch the live-stream they're trying to setup and participate via IRC.

>I think it should be uncontroversial that multiple BIPs be reviewed and tested, simulated, the best one selected and deployed. That's what people are working on.

My response:

> if the blocksize is jumped to 8MB the network will be overcapacity by 16-32x. Now if there is an excess of supply, price falls, ergo fees will drop to zero basically.

I do not think that fees will drop if supply increases with demand holding constant. I've made that argument at [this link](https://www.reddit.com/r/Bitcoin/comments/3i2hke/august_23_2015_block_size_and_hard_fork_dialog/cudq76o), but I will make it again below.

I think we're currently at or close to the floor of the demand curve in terms of fees -- that is, I think that fees per kB are currently low enough that people don't care about them and don't pay attention to them. The main thing that determines fee/tx is the default setting in the bitcoin clients that people use.

Fees reached their peak in 2013 when the average blocksize was about 150 kB. In December, total fees reached US$20k/day, or about 25¢/tx. Shortly after that, version 0.8.2 of bitcoin-qt was released, which dropped the default fees from 0.5 mBTC/kB to 0.1 mBTC/kB. Fees have never reached the same level since. 

During the stress tests, total fees reached about $10k-14k/day, or about 7.7¢/tx. Now that the stress test is over and blocks are no longer congested, fees are about $5.6k/day or 4.9¢/tx. Thus, when blocks were actually full, the average fee size increased by about 57%. Block transaction volume was about 58% higher. Unconfirmed transaction volume (and congestion) was higher than that.

https://www.quandl.com/data/BCHAIN/TRFUS-Bitcoin-Total-Transaction-Fees-USD

https://www.quandl.com/data/BCHAIN/TRFEE-Bitcoin-Total-Transaction-Fees

https://www.quandl.com/data/BCHAIN/NTRBL-Bitcoin-Number-of-Transaction-per-Block

https://blockchain.info/charts/n-transactions?show_header=true&daysAverageString=7&timespan=all

These data suggest to me that the fees per tx do not change much with moderate block congestion. (This might be improved somewhat with better client support for changing or automatically determining fees based on congestion.) Furthermore, the degree of overcapacity (2.5x vs 6x) seems to be completely irrelevant. Fees were higher when we had 6x overcapacity than they are now. Fees appear to only change based on on limited supply if the blocks are actually full. If we wish to increase fees by limiting block sizes, we would have to ensure that most or all blocks were full. In order to achieve the $2/tx to $20/tx needed to pay for the current 300 MW, 400 PH/s mining network with 1 MB blocks, that congestion would need to be extreme.

I think that artificially limiting blocksize to drive up fees is a terrible idea, since:

1. It ensures that some or most demand will not be met in a timely fashion.
2. The amount of demand that is met is fixed vs. time and does not scale to meet spikes in demand.
3. It does not inspire confidence in the capacity of the Bitcoin system.
4. It worsens the user experience considerably (long and unpredictable delays in transaction verification--block delay projections depend on how many competing transactions are added with higher fees and many other factors).
5. It promotes non-revenue-generating off-chain transactions as a method of fee-dodging and worsens the problem of paying for mining. Off-chain transactions do not generate fees in proportion to how much value the blockchain provides to the transaction as well as on-chain transactions do.
6. It promotes the creation or use of altcoins with larger or more frequent blocks. 
7. It makes bandwidth costs for miners irrelevant, and doesn't encourage miners to improve their connectivity. The capacity of Bitcoin transaction processing infrastructure will not improve, but power consumption for mining may continue to increase. 
8. The environmental cost per on-chain transaction will increase beyond the current 62.5 kWh/tx.

My preferred method for increasing fee revenue is this:

1. Scale up volume aggressively. Aim for 100x growth in 15 years. That would correspond to 36% growth per year. (Bitcoin demand has been increasing about 50% to 100% every year so far.)
2. Keep the default fee/tx in real (USD) terms somewhere between $0.05/tx and $0.25/tx until miner revenue comes 50% from fees, then start to reduce the default fee/tx in order to keep total real miner revenue close to constant.

If volume can't be scaled up fast enough, then average fees will need to increase. It will be much easier to scale up revenue if Bitcoin has high transaction volume in e.g. 2025 than if we scare users away with limited supply and high fees. 
Xthinner requires CTOR (ideally LTOR) for optimal performance. BCH added LTOR in November, 2018. No additional forks are required.
Correct, Blocktorrent is a very different block propagation method than Graphene. I expect it will also be superior.

Blocktorrent includes part of the merkle tree internal nodes with each IP data packet. This allows each packet to be independently verified and immediately forwarded. Block propagation is an exponential process: the number of nodes who have a block at time *t* increases by 10x each time interval. With Xthinner and Graphene, that time interval is the amount of time it takes to send the whole block to around 10 peers in parallel. With Blocktorrent, that time interval is the amount of time it takes to send a single packet to 10 peers. This makes Blocktorrent way faster.

My implementation of Blocktorrent will use Xthinner as the base layer encoding.
Rough plan:

1. Fix block propagation with Xthinner, Graphene, an/or Blocktorrent.

2. Get multicore parallelization into Bitcoin ABC's ATMP process.

3. Benchmarks and stress tests.

4. Increase blocksize limit to around 128 MB - 1 GB, based on test results.

5. Fix any unexpected issues that were uncovered during stress tests that block further scaling.

6. Add UTXO commitments, and maybe rewrite the UTXO database and cache layer at the same time.

7. Parallelize block validation using the OTI algorithm.

8. Benchmarks and stress tests.

9. Increase the blocksize limit to around 1 GB - 8 GB, based on test results.
The signed message could be something to the effect of "CSW's legal claim to control this address is false, and makes him guilty of perjury."
They're an exchange. They earn money when people exchange currencies. What's the big deal?
The simplest thing would be to modify the BU Graphene implementation to drop the transaction ordering information on BCH.
ABC was at 217% last night, but they seem to be settling in for the long game now and not wasting hashrate unnecessarily. Time to let the difficulty back down. (It's currently 909 billion on ABC, compared to 520 billion before the fork and 655 billion on SV.) 

Gee, SV, I wonder where all that new hashrate came from? Didn't you say you owned all of your gear and could go on forever? You wouldn't be ... renting, would you?
And Greg Maxwell supports CSW and Bitcoin SV. nChain = blockstream.

Truth is, neither one of those arguments means anything. Judgments need to be made based on the technology and the ideas, not slurs based on the reputations of the people.
Why is it so important that Bitcoin SV's changes be included in the November fork instead of the May 2019 fork? Why is this 6 month delay worth splitting the chain over?
Are Bitcoin SV or the pools who support it like CoinGeek planning on 51% attacking Bitcoin ABC, Bitcoin Unlimited, and XT by mining empty blocks on the ABC etc. chain while mining full blocks on the Bitcoin SV chain?
Hello, I am your friendly neighborhood downvote bot. I downvote posts that are factually incorrect, misleading, or otherwise detract from the discussion. In this case, I downvoted the post because my neural network detected that the post was:

1. A non-sequitur. The fact that the network couldn't make a 32 MB block does not mean that 128 MB blocks are safe. It [doesn't even mean that 20 MB blocks are safe](https://medium.com/@j_73307/block-propagation-data-from-bitcoin-cashs-stress-test-5b1d7d39a234).
2. No longer factually accurate. The bug in Bitcoin ABC that prevented mempool from growing faster than 300 kB per minute was [fixed yesterday](https://reviews.bitcoinabc.org/D1784). This code will be live on the network long before Bitcoin SV's intended 128 MB fork happens.
3. A tweet. Twitter emphasizes pith and panache over accuracy and truth, and does not provide a good forum for in-depth discussion. Here on reddit we are more interested in the latter than the former.

***

^[ [^PM](https://www.reddit.com/message/compose?to=jtoomim) ^| [^Exclude ^me](https://reddit.com/message/compose?to=jtoomim&message=Excludeme&subject=Excludeme) ^| [^Exclude ^from ^subreddit](https://np.reddit.com/r/btc/about/banned) ^| [^FAQ ^/ ^Information](https://medium.com/@j_73307) ^| [^Source](https://github.com/u/jtoomim)   ^]
^Downvote ^to ^discourage^| ^v0.00
He's comparing BTC's cost at BTC's peak transaction throughput rate to BCH's cost at BCH's peak transaction throughput rate. That seems fair to me.
We can (and should) have another one next month if there are unanswered questions from this one.
They claim 0.075 J/GH in their literature. However, their video shows the miner getting about 15.5 TH/s on 1470 W, which is **0.095 J/GH**. In comparison, a typical Antminer S9 gets about 13.7 TH/s on 1460 W, or 0.106 J/GH. 

Form factor and design looks like a blatant ripoff of an Antminer S9. In fact, I'm not even sure that the device in the video *isn't* an S9. The only differences that I can see are that the case is slightly different (smooth, no rails, and a different decal), and the hashboards have the power connectors on the right side of the PCB (like the L3+ and D3) instead of being on the left side (like the S7 and S9). The PSU also looks like a clone of the Bitmain APW3++.

The cgminer text in the video only shows 4.010 TH/s ([0:31s](https://youtu.be/SRCsQUyR7_I?t=31), bottom line). I'm curious how they get from 4.010 TH/s reported by the miner to 15.5 TH/s reported by the pool without some black magic.

This all looks rather scammy to me. I personally will not be risking my money for a 10% reduction in power consumption with a 2-month later scheduled delivery date from an unknown supplier.
By the way, I posted the same comment in r/bitcoin, and it was upvoted there, too. That's probably because I cited data. People tend to like data.

> only just decided to look

[No](https://www.reddit.com/r/Bitcoin/comments/6w1q8v/this_is_not_a_level_headed_response_this_is_an/dm5bzlw/), [that](https://www.reddit.com/r/Bitcoin/comments/6w1q8v/this_is_not_a_level_headed_response_this_is_an/dm579hd/) [is](https://www.reddit.com/r/Bitcoin/comments/5sejqz/fees_at_4k_satoshiskb_whats_going_on/ddfbp8a/) [incorrect](https://www.reddit.com/r/Bitcoin/comments/5sejqz/fees_at_4k_satoshiskb_whats_going_on/ddgajf3/?utm_content=permalink).
First, Coinbase and Bitpay aren't getting those fees. Those fees are paid to the miners (people like me) who include your transactions into blocks. Don't blame Coinbase and Bitpay.

Don't blame the miners either. We want the fees to be lower than they currently are, but we can't make that happen because of a rule in the Bitcoin code and the economics it creates.

What's going on here is that the Bitcoin network is severely congested. Bitcoin has a block size limit of 1 MB, and that effectively limits the entire Bitcoin network to processing about 3 transactions per second. Bitcoin's users want to send more than 3 transactions per second, and that means that some transactions have to get dropped. The transactions that offer the lowest fees to miners are the ones that get dropped. In order for your transaction to get processed by miners, you have to offer a fee that is high enough to discourage someone else from getting their own transaction processed.

This sucks.

Many companies and Bitcoin users -- including Coinbase and Bitpay, incidentally -- have been fighting to get Bitcoin's block size limit increased. This problem has been foreseen in the Bitcoin community for years. Indeed, several proposals to do just that were made over the last years, including Bitcoin XT, BItcoin Classic, Segwit, the Hong Kong agreement, Bitcoin Unlimited, Bitcoin Cash, and Segwit2x (New York Agreement). However, a large section of the community has opposed most of these proposed increases, and would only agree to allow Segwit, which was the smallest of the increases that were discussed, and the only major one that is not a hard fork. The main opponents of a blocksize increase is a company called Blockstream, which pays most of the active developers of the Bitcoin Core project. He who writes the code controls the network, apparently.
Segwit was Blockstream's idea. Much of the rest of the community wants much larger capacity increases than Segwit alone can offer, but any proposal to increase the blocksize limit in Bitcoin Core gets shut down immediately.

This disagreement has resulted in almost nothing getting done. Even though Segwit activated last week, it has not (yet?) made a dent in the fee and throughput problem. Fees are higher now than they have ever been.

Even worse, many of the main forums for the Bitcoin community are owned by one person, theymos, who believes in censorship and in keeping the blocksize limit small. He actively censors any positive discussion of proposals to increase the blocksize on his forums, which include bitcointalk.org, reddit.com/r/bitcoin, bitcion.org, and en.bitcoin.it. In response to that censorship, this subreddit was created, as well as reddit.com/r/btc (which ended up being more popular).

In May of 2017, a group of major companies and miners got together in New York and decided that they wanted to increase the blocksize limit with or without the support of Bitcoin Core. This group comprised 80 to 90% of Bitcoin's miners and pools (by hashrate), as well as a large portion of the exchanges and payment processors. They asked Jeff Garzik to write code to implement the blocksize increase, and promised to run the software when the time came for the upgrade. Jeff agreed and completed the software, which is called btc1. The upgrade is scheduled for November. At that time, Bitcoin will split into two separate chains. One of them will be the Bitcoin Core chain, which will keep the 1 MB (plus segwit) limit, and will have about 10% of the network hashrate. The other chain will be the btc1/segwit2x chain, which will have a 2 MB (plus segwit) limit, and will have about 90% of the hashrate.

2 MB won't solve the problem forever, but it should alleviate congestion for a year or two while we work on additional solutions. It kicks the can down the road a bit. Fortunately, mid-range computer hardware is capable of much more than 2 MB right now, so we can do a couple more can kicks like this before more ingenious and fundamental changes and optimizations are required.
Dude, it's just variance. 

Forgive my lazy statistics, but the variance is roughly proportional to the square root of the sample size. With a sample size of 1000 blocks, you should expect variance to be around sqrt(1000) = 31 blocks. That's pretty much the size of the fluctuations that you're making a big deal about.
Vertcoin's market cap is around $1.5M, or 1/10,000th the size of Bitcoin. It's not exactly popular.
Note: I am an industrial-scale miner. I have purchased hundreds of thousands of dollars worth of Bitcoin miners. 

But I'm sure you know more about this than I do.
There is only one alert key. 

Disabling the alert key or adding another alert key can cause the network to partition if alert messages are sent from one node to another, and the recipient does not consider the alert key used for the message to be invalid.

As a result, we cannot remove the current alert key or add (and use) a new alert key without running the risk of unintentionally splitting off all Classic nodes from the rest of the network.

https://github.com/bitcoinclassic/bitcoinclassic/issues/27#issuecomment-172995064
Xtreme Thin Blocks are a great improvement in block propagation for normal full nodes. However, they are likely to be slower than the relay network. There are a few reasons for this:

1. The relay network should have smaller data packages, since it uses two bytes per cached transaction instead of six. 
2. The relay network does not require bitcoind to validate the block in between each hop. Since validating a 1 MB block can take up to 1 second, and since it typically takes 6 "bitcoind hops" to traverse the p2p network, and the total block propagation time budget is around 1 to 4 seconds, that's kind of a big deal. **Edit**: XTB have an **[accelerated validation mechanism](https://bitco.in/forum/threads/buip010-passed-xtreme-thinblocks.774/page-6#post-13232)**, and also have the ability to add more blocks-only peers.
3. The relay network has an optimized topology, and will only send each block at most halfway around the world, whereas the topology of the bitcoin p2p network is random and can result in the data traversing the globe several times over the course of the 6 hops.
4. The XTB shorthash mechanism can be attacked by intentionally crafting transactions with 64-bit hashes that collide. This would force additional round trips on each hop, delaying block propagation time through the network by several seconds.
5. The XTB system requires more round-trips than the relay network. In the best case, XTB requires 1.5 round trips, whereas RN only takes 0.5.

On the other hand, XTB has a couple advantages over the relay network:

1. The relay network is a centralized system run by one person. If Matt Corallo is asleep and a server goes down, the miners suffer. If Matt Corallo decides to **[simply stop supporting the relay network](https://bitcointalk.org/index.php?topic=766190.msg13510513#msg13510513)**, the miners suffer. If Matt Corallo decides that he doesn't like one particular miner and denies him access, that miner suffers. If one of Matt's servers gets DDoSed or hacked, the nearby miners suffer. Thin block propagation is p2p and decentralized, and does not suffer from these issues.
2. **Edit** It is possible to request an XTB from several peers in parallel. This provides some protection against the case in which one or more of your peers is particularly slow to respond, which is a very common case when crossing the Great Firewall of China due to its unpredictable and excessive packet loss.

Fortunately, miners can use both XTB and RN at the same time, and improve upon the reliability of the RN while also improving upon the typical-case speed of XTB.
One of BTCC's project managers told me over lunch about a previous DDoS attack they had experienced. 

They received an email from an anonymous hacker who demanded something like 1 BTC immediately, and warned that if they did not pay that DDoS attacks would commence. Further, the extortionist hacker warned that the price would go up if they didn't pay immediately.

BTCC did not respond. A brief DDoS attack commenced.

The attack was larger than BTCC had expected. I think he said something like 10 Gbps. During the attack, they got a few fretful calls from their DDoS protection provider, who said something like "This thing is huge! You guys aren't paying us enough for this!" So BTCC paid them more.

After the attack, they got another email from the extortionist: "Now you have a taste of what we're capable of. Please pay us 10 BTC immediately or more attacks will follow." 

BTCC did not respond. Another attack commenced, and lasted several hours.

During this attack, BTCCs servers experienced some performance issues, resulting in a partial loss of functionality. They upgraded their servers.

After this one, they got another email from the extortionist: "We will keep these attacks up until you pay! The price is now 30 BTC. You had better pay up before you go bankrupt! Mwa ha ha!"

BTCC did not respond. Attacks recommenced. 

More threats were issues, more attacks performed. BTCC stopped noticing many of the attacks, as they usually failed to disrupt their networks for more than a few minutes after the upgrades they rolled out.

Eventually, they got a different email from the extortionist, saying something like, "Hey, guys, look, I'm really a nice person. I don't want to put you all out of business. What do you say we just make it 0.5 BTC and call it even?"

BTCC did not respond.

Eventually, they received the final email from the extortionist: "Do you even speak English?"
At 360 MW and $0.05/kWh, mining costs $3000 per block. As of Dec 29 2015, fees are $0.16/kB: 19 MB would be enough. 

It doesn't even take any fancy fee markets. Default fee settings in Bitcoin Core for miners and for wallet software are enough. Collective bargaining amongst miners on a fee floor would also be plenty powerful enough to keep us at $0.16/kB. There is no need to increase prices if we can increase volume enough.
You haven't seen the final results of our performance testing yet. Neither have I. 

> There are no bugs....  Have some drive, man.

I was up until 6:00am last night working with Prattler on tracking down and figuring out a performance issue (which I think of as basically being a bug) that was adding somewhere around 3 to 30 seconds of delay to our block propagation times, and implementing a workaround.

I do not think that "as soon as possible" is necessarily the optimal time to implement a hard fork. I think that if we put in a couple months more work into this, especially in fixing performance issues, we can make it go much more smoothly, especially if we work our asses off during that time. That's what I intend to do.
A > 30% miner can do a selfish mining attack whether or not large blocks are involved. I suspect that using large blocks makes it more difficult to perform selfish mining attacks, since selfish mining usually relies on carefully timing the publishing/propagation of blocks based on the progress of the rest of the network, and large block propagation is hard to coordinate.
theymos
	
Re: The MAX_BLOCK_SIZE fork

January 31, 2013, 08:59:57 AM
>>Quote from: gmaxwell on January 31, 2013, 08:01:14 AM
It's often repeated that Satoshi intended to remove "the limit" but I always understood that to be the 500k maximum generation soft limit... quite possible I misunderstood, but I don't understand why it would be a hardforking protocol rule otherwise.

>Satoshi definitely intended to increase the hard max block size. See:
https://bitcointalk.org/index.php?topic=1347.0

>I believe that Satoshi expected most people to use some sort of lightweight node, with only companies and true enthusiasts being full nodes. Mike Hearn's view is similar to Satoshi's view.

>I strongly disagree with the idea that changing the max block size is a violation of the "Bitcoin currency guarantees". Satoshi said that the max block size could be increased, and the max block size is never mentioned in any of the standard descriptions of the Bitcoin system.

>IMO Mike Hearn's plan would probably work. The market/community would find a way to pay for the network's security, and it would be easy enough to become a full node that the currency wouldn't be at risk. The max block size would not truly be unlimited, since miners would always need to produce blocks that the vast majority of full nodes and other miners would be able and willing to process in a reasonable amount of time.

>However, enforcing a max block size is safer. It's not totally clear that an unlimited max block size would work. So I tend to prefer a max block size for Bitcoin. Some other cryptocurrency can try the other method. I'd like the limit to be set in a more decentralized, free-market way than a fixed constant in the code, though.

>Quote from: Jeweller on January 31, 2013, 08:18:30 AM

>>So, the wiki should be changed, right?

>It's not yet known how this issue will be handled. The wiki describes one possibility, and this work shouldn't be removed.

> How do you see the network adopting xthinner vs Graphene?

We'll need to see how Graphene performs on very large blocks (> 20 MB), which is the only scenario in which either of these protocols really matter anyway. If Graphene has a significant advantage over Xthinner at that size, we might see both Graphene and Xthinner deployed side-by-side, possibly on overlapping subsets of the p2p network. I think Xthinner will have a reliability advantage over Graphene which will make it useful as a fallback option for Graphene even if Graphene performs sujbstantially better in ideal conditions. Xthinner may also have an encoding/decoding CPU time advantage over Graphene, which could be significant on 100 MB to 1 GB block scales.

Once Blocktorrent is working, I expect it will dramatically exceed Graphene's performance, and all implementations will converge on it.
Rewriting the full node code to run on highly parallel processors (e.g. GPUs) would take at least 18 months.

Gigabyte blocks are fairly easy. Terabyte blocks not so much. Possible, sure, but definitely not easy, and not something we could do "NOW".
If you're looking for people to tell you that the problems you saw are not real or can be overcome, you've come to the wrong place. Most of the people in /r/btc have a pessimistic view of the viability of LN as a consumer-facing technology, and do not think that the UX of LN will ever be good enough for it to cover the majority of payments. If you want to find people who will advocate for LN's future, you ought to post something like this in /r/bitcoin.
Many miners are still making a profit, so we aren't shutting down. Other miners are no longer making a profit, so they're shutting down. The reason that hashrate doesn't drop 50% when the price drops 50% is that not all miners have the same costs. I pay about $0.028/kWh for electricity, for example. Many other miners pay $0.04-$0.05/kWh for electricity. Some pay as much as $0.07/kWh. As revenue falls, the less competitive miners shut down first.

For reference, revenue with an Antminer S9 is currently about $0.06/kWh without Asicboost, or about $0.07/kWh with Asicboost.
[First](https://blockchair.com/bitcoin-cash/block/00000000000000000159f98b1aed7585313068223ddbdb39d459c31b51d1edc1) and [second](https://blockchair.com/bitcoin-cash/block/000000000000000002762ebb7f33319709ae62abc1438adb1077beb50cafee34).
No BUIP is in progress yet. Because this is a matter of node policy, there does not need to be consensus on the choice of algorithms used for selecting the chain tip. If you want to add this to bchd today, you could.

A BUIP sounds like a decent idea, though. But given that the idea is only 1 day old, it hasn't been started.

The algorithm in my code should be pretty complete. If you can implement the compare_blocks_toomim_time(...) function in bchd and use it to compare chain tips, that should be pretty complete. The only major gotchas are IBD and making sure that you don't add DoS vulnerabilities from low-work chains (since the algorithm is O(n^2) as written). The algorithm as written should work properly with the DAA, even in manipulation cases, but the sim code does not model diff changes.
A few smaller miners did move over, but not much. Only about 200 PH/s, by the looks of it. 

A lot of SVPool's hashrate came from Coingeek decreasing their own hashrate. (SVPool is openly owned by Coingeek.) You can see that in the graphs. And SVPool's hashrate ballooned at the exact same time that CoinGeek's hashrate ballooned and Mempool blinked into existence. As CoinGeek is not a public pool, the explanation you are offering cannot apply to CoinGeek's own private-pool hashrate.

If this were organic growth due to the movement gaining steam around the fork date, why does the graph look like there was a single step from 1.6 PH/s to 3.5 PH/s, with relative stability before and after that step? 

Except during the stress test, of course, where they slowed down their hashrate by 75% for a few hours in order to slow down block creation rates to let their mempool fill up so they could make and propagate those 63 MB blocks.

> perhaps you will go on record right here, right now, saying that you will oppose permissioned mining?

I oppose both permissioned mining and 51% attacks.
CTOR was designed to simplify the protocol and [make block propagation faster by eliminating some of the entropy in block encodings](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0).

CTOR was not designed for Wormhole. CTOR makes Wormhole [unequivocally worse](https://www.reddit.com/r/btc/comments/9gmi4z/logical_proof_that_wormhole_omni_is_not_secure/e65c9ei/). The "CTOR helps Wormhole" argument is just FUD that CSW generated in order to drive a wedge in the Bitcoin Cash community and to assist his divide-and-conquer plan. Not only is that FUD untrue, it is completely inverted. If CSW truly doesn't like Wormhole, he should *support* CTOR. But the truth is that CSW doesn't like ABC, and has decided to hate CTOR in order to support his hatred for ABC, and has decided to hate Wormhole to support his hatred of CTOR.
They have no incentive to avoid orphans. So what if they lose a few coins of monopoly money? They're mostly all running with the same funding anyway. It doesn't matter to them if SVPool gets less than CoinGeek, since SVPool *is* CoinGeek.
Rough math:

They have mined 30 blocks in 5 hours. That's about 60% of the normal block rate. They had about 70% of the hashrate before the fork. A 10% difference is well within the margin for luck over a 30 block window.
Count the blocks mined over a given time interval, and calculate the expected value of the number of hashes that would be needed to get that number of blocks for the given mining difficulty.
I would not support SV no matter what because they do not show technical competence. They do not seem to understand the vulnerabilities they can introduce when they take the attitude of "just eliminate the limits (sigop and block size) and let miners deal with performance issues." They do not seem to understand that the performance issues are *software*, not hardware, and it's far more efficient to solve software issues once in open-source software than it is for each mining pool to build their own pool-specific infrastructure for working around these software issues, or to develop private software that solves the issue just for that pool. They also do not seem to care that having each pool develop their own in-house solution would fundamentally change the economics of Bitcoin mining in a way that encourages large superpools and compromises the decentralization of Bitcoin.

I would not support them because they believe that selfish mining is impossible, whereas I know that it is possible because I have been doing a variant of it unintentionally on p2pool for years, and have earned about 3% more revenue on LTC and on BTC than my hashrate deserves because of it. This accidental selfish mining variant becomes inevitable when you have slow propagation/validation times relative to the block interval (i.e. blocks are too big relative to the network's code and node performance), and when you have significant disparities in the proportion of hashrate that each pool or miner has. Because p2pool has low performance (written in Python, and designed in a way that requires it to process and forward each transaction) and has a blockchain-on-a-blockchain design, p2pool is a canary in the coal mine for this accidental selfish mining variant. However, with BCH, we would see similar susceptibility to accidental selfish mining if block sizes were about 40-80 MB, given current tech. Bitcoin SV's "solution" to this problem is to claim that it does not exist, based on a single a priori paper by CSW which was logically flawed and incoherent (it postulates *negative probabilities* in some of its formulae, which makes negative sense).

I would not support SV because they are opposed to important features like CTOR which actually help with the performance issues mentioned above, and are using FUD smear tactics in their arguments against it ("CTOR is used for Wormhole! Wormhole's purpose is to get Bitcoin off Bitcoin!" or some such nonsense; "CTOR makes adding transactions to blocks O(n^2)!" -- factually incorrect, it's O(n log n) -- "and that changes the economics of Bitcoin at a fundamental level!"). 

I would not support SV because they do not show the level of professionalism and technical discussion needed to make a community work unless they are treated as a dictatorial control party. Instead of presenting these (spurious) objections directly to miners at the miner/dev meeting in Bangkok, Craig simply ran out shouting "[Lies and bullshit!](https://www.yours.org/content/my-experience-at-the-bangkok-miner-s-meeting-9dbe7c7c4b2d)", and did not return. When the other nChain/SV representatives were asked to flesh out their objections to CTOR, they were unable to, and hinted that CSW was the only one who knew how to explain his objections.

In summary, most of the objections I have to SV are technical. Their plan does not make sense to me. Their strategic leader (CSW) is a technically incompetent serial plagiarist who is wrong about most of the technical issues he discusses. The demeanor and unprofessionalism contributes to the problem, because it means that nobody can ever engage in a technical debate with CSW; he will simply explode in a ball of anger and either turn the discussion into a shouting match, block you on twitter, or simply walk out of the room. When a person is both wrong and unable to engage in debate, it's a problem. When that person is the leader of a movement, it's a disaster.

/u/nicebtc 


Thank you for making your statement on social media about how you think that Bitcoin SV is better.
> he is far from a scientific fraud

[Plagiarism is scientific fraud](https://www.reddit.com/r/btc/comments/9rddek/new_plagiarism_from_craig_wright_at_least_40_of_a/).
What practical block sizes does Bitcoin SV expect that their code is able to safely achieve over a 10 minute interval? How long will blocks of that size take to propagate across the planet? How long will blocks of that size take to verify?
Your comment is essentially a straw-man argument combined with an ad hominem.

I do not advocate subsidizing small miners.

The limits on block propagation performance have nothing to do with a pool's budget. They are not related to the hardware that the node has. The node with 1 Gbps of bandwidth received blocks just as fast as the node with 25 Mbps of bandwidth. The limitation on propagation performance is the *protocol*. The use of TCP over long-distance, high-latency, nonzero-packet-loss links results in poor effective bandwidth. As such, miners' nodes will not be substantially better than non-mining nodes' performance, and may be substantially worse since many of them have to cross the Great Firewall of China.

A small miner like me can simply join a large pool to avoid orphan rates. I have no financial interest as a miner in seeing blocks be kept small. It's only as a developer and user of BCH that I would be concerned about this.

For the record, I will be leaving the mining business on April 1st, 2020 or before, as my company's electricity supplier has decided to raise rates for cryptocurrency customers by 207%.
Because we did not have mining nodes in our test set, we missed the first one or two hops of propagation.

The rules of TCP congestion control apply just as much to pools/miners as they do to non-pools, and are much (~4x)  harsher for pools in China than elsewhere. I expect that if we had data from miners, the results would be *worse*, not better. Maybe we'll find out in the next stress test.
The USPTO (and probably other authorities) allows for disinterested third parties to file claims of prior art during the patent review process. I am suggesting we all contribute to an association that will do that to every one of his patents in every jurisdiction, to make sure nothing falls through the cracks by a sleepy or confused patent clerk.
CSW maintains a Twitter feed without dissent by blocking anybody (especially devs) who disagrees with him. This means that his followers read his comments in an uncritical environment, and the lack of visible controversy lulls them into believing the claims to be uncontroversial, insightful, and true.

He also is an expert at appealing to people's greed and other desires. He projects an image of carefree wealth, luxury, and power which his followers envy and desire. All we have been doing so far is countering with logic when his followers spill out from his Twitter feed filled with the frenzy and passion of righteous greed. 

It's no surprise we haven't been very successful so far with handling the threat. We haven't even begun to play his game.
EOS is not PoW. It's basically PoA, proof of authority. It's effectively just a glorified shared SQL database. It does not count.
I've been working on exactly that over the last two weeks.

https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244
Not CSW or any who espouse his positions, that's for sure. We are not ready for 128 MB blocks. Selfish mining is real; I've seen it happen unintentionally from performance constraints on a daily basis for months on end in p2pool, and have spent a hundred hours or more working on the performance of p2pool's code in order to mitigate it. Based on the 1.6 MB/s empirical block propagation rate observed in the gigablock tests, inadvertent selfish mining would happen on BCH with blocks larger than roughly 30 MB.

Seems kinda fitting that the largest BCH pool would be the one pushing for 128 MB blocks now. Big pools have less to worry about from orphan rates. A pool with 25% of the network hashrate will have orphan rates that are 25% lower than a small-scale miner, just because they propagate blocks to themselves immediately, and find a second block before anyone else at least 25% of the time. These effects are significant and problematic. 

On BTC p2pool, I'm currently getting 107.4% efficiency, just because I have the 2nd-highest hashrate of any btc p2pool users and also have one of the fastest servers. That means that many small miners on p2pool are only getting 93% of what they deserve. That's screwed up. Fortunately with p2pool, most of us are hobbyists and idealists and don't really care about the revenue that much. But the unfairness from selfish mining and low propagation performance is what keeps it as a hobbyist venture. I don't want to see the same thing happen to BCH.

So yeah, 128 MB bad til code fix done.

While I definitely like the CBO idea, and [wrote an article in favor of it recently](https://medium.com/@j_73307/canonical-block-order-or-dbe3ac48bcd3), I do not think it's worth splitting the BCH community over. If we can get a supermajority of other people in support of CBO, then I'll support forking in CBO. Otherwise, I'll encourage people to not change the status quo at all and try to remain cohesive.

The exception to that cohesion idea is that I kinda just want CSW gone, so if he wants to fork off, I might encourage that. CSW has a tendency to mix in occasional insights with a lot of points that are simply wrong, and to wrap it all up in a massive outpouring of abstruse technobabble, which makes conversations in which he's included generally unproductive.  It's Brandolini's law -- the amount of energy needed to refute bullshit is an order of magnitude greater than that needed to create it.
BTC and BCH block templates get reassembled every 15 seconds or so to make sure that the transactions being included have the highest fees available. This process takes an average of 70 milliseconds on my BTC p2pool server. While this block reassembly is occurring, our miners are still hashing on the old block template to try to find a block, so this 70 milliseconds is only wasted time once every 10 minutes, immediately after a block is received from the network. That's about 0.07/600 = 0.0117% of the time, which is nowhere near being problematic.

On BCH, this process can take longer for larger blocks. If block assembly time scales linearly with block size, one would expect a 32 MB block to take around 2.24 seconds, or around 0.37% of an average block interval. This is significant, but tolerable.

Keep in mind that SHA256 hashing runs at about 400 MB/s on an Intel CPU, and the Merkle tree hashing only hashes the hashes, not the transactions themselves. This process is quite fast. The slow part is checking the transaction dependency graph for Child-Pays-For-Parent (CPFP) support and to make sure that no transaction is included in a block before the transactions it depends on. If it weren't for this requirement, block template assembly would probably be 3x faster. The November hardfork will be adding a canonical transaction ordering which will remove that requirement, incidentally.
Satoshi deserves the funds he mined. If he wants to spend them, he should be allowed to. It may disrupt Bitcoin's exchange rate a little, but without Satoshi there would be no Bitcoin exchange rate, so I think that we should just suck it up and accept it if that happens.
The sharp increase is probably due to a change in how Classic blocks are counted. A block votes for BIP109 (the Classic 2MB HF) iff the following formula is true:

    version & 0x30000000 == 0x30000000

That is, if the third and fourth bits (0x2... + 0x1...) are set in version, then the block is a vote for BIP109.

Up until recently, most BIP109 blocks had version = 0x30000000 exactly. However, that changed with the rollout of the CSV soft fork, which uses this test:
   
    version & 0x20000001 == 0x20000001

That is, if the third and last bits are set, it's a vote for CSV. Now, a block can vote for BIP109 and CSV at the same time by using a block with version 0x30000001, and recently, that's exactly what some miners have been doing.

Unfortunately, nodecounter.com was not correctly interpreting blocks with version 0x30000001, so it was reporting lower votes than was actually the case. Oops.
Right. At 363 kB, or even 1.2 MB, compression techniques like Xthinner aren't needed. We do our engineering based on the maximum size the protocol allows, not based on the typical throughput. At/above 32 MB, Compact Blocks's performance just isn't good enough. Above 128 MB, Xthinner will likely also be insufficient. But there's a lot you can do with 128 MB.
I haven't *really* done performance testing of Xthinner alone. Time is the main limitation here. I currently think my time is better spent fixing bugs, writing the spec, and doing stability/vulnerability testing.

The numbers that I have for coding efficiency so far look [fairly similar to Graphene](https://old.reddit.com/r/btc/comments/bas60b/by_the_power_of_ctor_xthinner_is_now_working_with/ekekzf0/). I suspect that Xthinner will have an advantage in CPU usage/processing delay and reliability, but I don't have any data on this. **Edit:** It looks like Graphene v2 has a [sizeable advantage](https://github.com/bissias/graphene-experiments/blob/master/jupyter/large_block_analysis.ipynb) in coding efficiency in optimal conditions, but Xthinner is competitive (and possibly superior/more reliable) in suboptimal conditions.

At some point, I'd like to compare multi-hop block propagation speed on a LAN with artificial packet loss and packet delay (using Linux's [netem](http://man7.org/linux/man-pages/man8/tc-netem.8.html) module) between Bitcoin ABC with Xthinner disabled (via command-line option) vs enabled. Also doing Graphene at the same time will be a bit trickier, since it will require switching clients rather than just switching a command-line option.

But I might prefer to write Blocktorrent instead of spending too much time benchmarking Xthinner. Xthinner is really just a stepping stone for me; 80% of the work I needed to do for Xthinner was also required for my planned implementation of Blocktorrent, and Xthinner will be 80% obsolete once Blocktorrent is done.
The fact that this sub is named BTC and not Bitcoin is merely a historical point. This sub was created after /r/Bitcoin (then the main Bitcion subreddit) began being censored, and any user advocating for the scaling of Bitcoin by hard forking to increase the blocksize limit in that subreddit was banned. This subreddit was created as a place where nobody would be banned because of their opinions, and consequently most of the proponents of large blocks ended up here. A couple years later, a hard fork actually happened and created Bitcoin Cash.

So most of the people in this subreddit support BCH, despite the subreddit being called BTC. This is unfortunate, but /r/Bitcoin doesn't want to switch names with us, so we're kinda stuck with it.
The pro-BSV faction is still a minority of BU's active membership, but they're a significant minority. It's cause for concern, but not cause for abandonment.
There's an error in the spreadsheet. It shows Jonathan Silverblood as having voted in favor of my membership, but he is not a time traveler, and I became a member before him.
"My model predicts that miners are operating at a loss, so that must be true." - JP Morgan Chase analyst

"Your model is wrong." - Me, who is still mining in the USA at a substantial profit
1. If the order is fixed, then only one code path is required, and Graphene can be made simpler with no fallback options in case the voluntary ordering is not observed.
2. The current topological transaction ordering requirement prevents the sorting algorithm from being LTOR (i.e. sort by txid). Instead, the sorting algorithm will need to deeply inspect each transaction. Deep inspection requires two pointer dereferences, and is likely to make generating the sorting about [20x slower](https://www.reddit.com/r/btc/comments/9cq62n/during_the_bch_stress_test_graphene_block_were_on/e5zweh2/) than the LTOR sort.
3. Many optimizations are possible if it can be assumed that blocks will always be in lexical order. It makes UTXO cache inserts sequential, for example, which can make the use of a tree for UTXO cache storage faster than a hashtable. With Xthinner, it allows for Xthinner message decoding (and encoding) to work by [iterating through the mempool](https://github.com/jtoomim/xthinner-python/blob/master/xthinner.py#L196) instead of randomly seeking within the mempool. However, if you try that algorithm on a non-LTOR block, it fails to find the transaction. There are also benefits to be had in block validation algorithms if LTOR is mandatory, and eventually there will also be benefits in UTXO commitment generation.
4. A known exploit exists called the outcast attack. With this attack, a malicious miner can use transaction reordering in the blocks they mine to add a propagation delay to their block that only applies to honest miners. This can be used for a type of selfish mining attack which the attacker can guarantee will work in their favor as long as they put a portion of the hashrate into the outcast group that is less than the hashrate that the attacker controls directly. The only way to block this attack entirely is to make a canonical ordering mandatory.

[Outcast attack concept](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1219#post-78785) (second half of post)

[Outcast attack math](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1220#post-78819)

If a better order than LTOR is found, then we can switch to that order with another fork. I am very skeptical that this will happen, though, as sorting a key-value store by the raw key will always be faster than sorting by some_function(value).
Elements Alpha is open sourced under the MIT license.

https://github.com/ElementsProject/elements/blob/elements-0.14.1/COPYING

The only IP implications of Elements Alpha's prior implementation is that there's no way that CSW's patent (with a priority date of 2016) would apply to OP_CDSV, since EA provides prior art from 2015.

https://github.com/ElementsProject/elements/commit/a04ea8a81286f1d2917c918a3b5ac3599464895c

https://medium.com/@j_73307/p2pool-as-prior-art-for-nchains-turing-complete-transactions-patent-or-how-to-patent-all-40f3d429eaa4
It was. It made some sense in the BTC context. Why forward transactions at a significantly faster rate than they could ever be incorporated into blocks? But when the block size was increased, that corresponding limit was overlooked.
1. Bitcoin SV is limited to propagating about 3 MB of transactions every 10 minutes (7-14 tx/sec). Bitcoin ABC and Bitcoin Unlimited are not; ABC can handle about 100 tx/sec, and BU can handle several hundred tx/sec. If Bitcoin SV gets spammed, or if both chains get spammed, then some transactions will confirm on ABC+BU but not on SV, even though the users might want to send an unsplit transaction. It's worth noting that a stress test is planned for Nov 17th.

2. Bitcoin SV nodes will ban ABC nodes and vice versa. The network will naturally partition into two separate networks. There may be some linkages between the two networks, but it's not clear how much bandwidth those linkages will have. Especially during stress test conditions, it's likely that many transactions will not cross to the other network.

3. If a user receives a coin from e.g. an exchange or another user, they might not check to ensure that the coin is valid on both chains, and they might consequently be unaware that they have split coins.

4. As time goes on, more and more coins are going to end up being split. A fully split coin can never be reunited. Companies that choose to only accept unsplit coins will see their user base dwindle over time and the backlog of split transactions in the holding queue grow.

5. The "hash battle" will likely end in a stalemate, with both coins persisting forever.

/u/Kain_niaK 
Life would be really cruel and sad if it weren't so funny.
Thanks. 31.08.2017 for the Turing Complete Transactions patent. And they cited Gavin Wood's yellow paper. Good.

So Craig has known this patent is invalid for a year, and he still published threats on twitter.

https://twitter.com/ProfFaustus/status/1033653060004978689

Incidentally, CSW blocked me on Twitter. What a surprise.
I can't find any dates on these opinions. They all say to see form PCT/ISA/220. Where did you find them? Where can I find the opinions' dates?

I would like to know whether Craig knew about these at the time he made his threatening comments towards OP_CDSV.
ikr
Most of them are not developers. Only about 25% of those people write code.
> These were not 'BCH devs' only, this was the BU membership voting on a BUIP which was intended to gather information on the sentiment within its community on the question of CTOR.

> BU officials have stressed that they would implement it if necessary to remain in consensus.

>>BU does not desire unintended forks in the BCH blockchain, so the BU client will remain compatible with the final specification of the November upgrade.

> https://bitco.in/forum/threads/buip096-bch-november-upgrade-enforced-lexicographic-transaction-ordering.22299/

[Thanks, LovelyDay](https://www.reddit.com/r/btc/comments/9cp3ax/bch_devs_categorically_shut_down_cannonical/e5cagdu/).
Since you mentioned being happy to get into more detail...

All serious pools are located in major datacenters with at least 100 Mbps pipes. Datacenters in China are well connected to other datacenters in China. Datacenters outside of China are well connected to datacenters outside of China. Datacenters in China have terrible connectivity to datacenters outside of China, and vice versa. So if you want to have good connectivity to the rest of the Bitcoin network, then either all of the Bitcoin network needs to be inside China, or all of it needs to be outside of China. Since we will never be able to agree on which of those is the right option, we have to deal with the fact that many pools will have bad connectivity to other pools.

Even if you have good connectivity, the nature of TCP gives you far less throughput than you would expect. TCP uses a congestion control algorithm that limits the number of packets in flight to the TCP congestion window (cwnd). When a packet makes the trip successfully, cwnd gets increased by one. When a packet is dropped or times out, cwnd gets decreased by e.g. 50%. This is known as the additive increase/multiplicative decrease feedback control. With this feedback, the cwnd can double during each round trip time (RTT). Thus, if your RTT is 1 ms, you'll send 1 packet at t=0ms, 2 packets at t=1ms, 4 packets at t=2ms, 1024 packets at t=10ms, etc, until you reach the capacity of your pipes and start to see packet loss.

That works pretty well in low-latency networks, but in high-latency networks, things start to suck. If your RTT is 200 ms, then it can take 2 seconds before you're able to scale your bandwidth to 1024 packets per 200 ms, or 7.6 MB/s. During those first two seconds, you will have sent a total of 2047 packets, or 3 MB (1.5 MB/s). So long distance links with high latency are in ideal circumstances only able to provide high bandwidth after they've been transmitting for a few seconds.

But that's only for ideal situations. Things get really bad when you start adding packet loss to the mix. Let's say you have a 50% decrease in cwnd for each lost packet, and you have a packet loss rate of 5% (fairly good for cross-China border communication). In this case, you will reach a cwnd equilibrium where every 20 packets gives you the same amount of linear increase from packets that arrive as you lose from dropped packets. (20 + x)*.50 = x, so x=20. With 5% packet loss, you will get a cwnd that oscillates between 20 and 40. At 1500 bytes per packet, that's an average of 45 kB per round trip time, or 225 kB/s for a 200 ms RTT. This is completely independent of your local pipe bandwidth, so even if you have a 40 Gbps pipe, you're only going to get 225 kB/s through it per TCP connection.

And that's with a 5% packet loss rate. 5% is a *good day* in China for cross-border communication. On an average day, it's about 15%. On a bad day, packet loss is around 50%. With 50% packet loss, your average cwnd will be 2, and you'll get about 15 kB/s.

Yes, 15 kB/s. Even if you have a 1 Gbps pipe. I've seen it happen hundreds of times when I lived there.

The problem is larger in China because packet loss is greater there, but all international links have significant packet loss. Outside of China, it's usually on the 0.5% to 2% range. At 2%, that still limits you to a cwnd of 50, which gives you 375 kB/s on a 200 ms link. At 0.5%, you get a cwnd of 200, or 1.5 MB/s on a 200 ms link. Again, note that this limitation is completely independent of your local pipe size.

Why is it so bad in China? It has nothing to do with technology, actually. China could easily get packet loss to 0.1% if they wanted to. They just don't want to, because it does not align with their strategic goals.

China has three major telecommunications companies: China Unicom, China Telecom, and China Mobile. Of the three, China Mobile mostly just does cell phones and is of only tangential relevance. CT and CU are the big players. Both CT and CU have a policy of keeping their international peering links horribly underprovisioned. Why? Because there's no money to be made off of peering. By making peering slow and lossy, they can drive their international customers to pay a premium for bandwidth that doesn't suck.

And boy do they charge a premium. Getting a 1 Mbps connection from China Telecom in Shenzhen to Hong Kong (20 km away! but it crosses the China border) can cost $100 per month. Getting a 1 Mbps connection from Shenzhen to Los Angeles (11,632 km), on the other hand, will only cost about $5.

Yes, the longer the route, the cheaper the bandwidth is. That is not a typo.

China Unicom and China Telecom both charge more for shorter connections because they can. Hong Kong is more desperate for connectivity than the USA is, so CT/CU charge HK more. They have a government-enforced duopoly, so in the absence of competition or net neutrality laws, they charge whatever they think they can get away with, regardless of how much the service actually costs them to provide.

Because the China-USA and China-Europe connections are cheaper than the China-Asia ones, most routers in Asia are configured to send data to the USA or Europe first if the final destination or origin is China. Occasionally, this happens even when the source and destinations are non-Chinese Asian countries. This is known in network engineer circles as the infamous Asia Boomerang. Bulk traffic from Shenzhen to Hong Kong will often pass through Los Angeles because that's the most economical option. This adds an extra 250 ms of unnecessary latency, and wreaks all sorts of havoc on TCP congestion control.

China Mobile, on the other hand, is usually willing to engage in fair peering practices abroad and does not charge predatory rates. Unfortunately, they mostly only serve mobile phones and rarely have fixed line offerings, so they aren't in direct competition with CT and CU for most of the market. But if you ever find yourself in China having trouble accessing websites abroad, setting up a 3G phone as a mobile hotspot will likely give you better bandwidth than using the 200 Mbps fiber optic connection in your office.

So... do you put all your pools inside China, where most of the hashrate is? Or do you put the pools outside China, where friendlier governments and better telecommunications are? Or do you write a new protocol like Graphene that compresses data so much that it doesn't matter if you only get 15 kB/s? Or -- and this is my favorite option -- do you stop using TCP altogether and switch to UDP with forward error correction?

One thing is certain: you don't blame miners for being in remote regions with poor connectivity. That just isn't what's going on at all.


[Copied from a post I made on bitco.in when someone else raised the same question](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1233#post-80009)
~~It means that they're planning on having a way of splitting their BSV off from BCH by allowing transactions that BCH would forbid. This way, BSV can mine a cloned BTC transaction, and BCH will mark that block as invalid, and allow the BCH chain to continue even if the BSV chain has more work.~~

~~However, this will not allow BSV to continue even if the BCH chain has more work. I suspect that might have been the goal, but it's not what it will achieve. To be honest, the motivation for this change is not very clear to me.~~

~~Edit: It looks like this change will also reject BCH transactions. All BSV transactions must use the same signature format as is valid on BTC. This means that BSV will be able to survive as a minority chain. It also means that any BSV transactions can be replayed on the BTC chain if they spend UTXOs older than Aug 1, 2017, which is probably going to cause BSV users to lose a lot of BTC if they aren't very careful.~~

**Edit 2: LovelyDay's reply is correct. This is disabling the replay protection from the Nov 15, 2018 fork, not the replay protection from the Aug 1, 2017 fork.**
Aww, but that means that you miss the part where I talk about how bitcoin transactions (not blocks) naturally form a DAG due to the nonexistence of the inverse of SHA256, and how that means we can infer that a valid ordering of transactions exists without calculating what it is, which means we don't have to validate transactions in order at all! Isn't that enlightening?

Given, the word "spiritual" might not be logically justified in that context, but there are some things I can't resist doing for the sake of humor.

Anyway, I'm sorry you didn't like it. I'll write more dry technical stuff too, don't worry.
The top 10 coins have a combined market cap of $232 billion, which is 82.6% of the total cryptocurrency market cap of $282 billion. The top 100 have a combined market cap of $270 billion, or 95.7% of the total. Getting rid of the long tail of junk altcoins does not substantially affect market cap dominance charts, as the market cap of minor junk altcoins is tiny.
Let's say you have 5 million users, each of whom opens and closes a channel once a month on average, and who make zero other bitcoin transactions. That's 10 million transactions per month. Channel open/close transactions are about 500 bytes each, so that's 5,000 MB per month. At 144 blocks per day, 30 days per month, this would be 1.16 MB of lightning channel open/close transactions per block. This leaves nearly zero space for non-lightning transactions.
Yes, it's possible that it's an exchange or something. But it's also nearly exactly the type of transaction that you'd make if you wanted to make news by having the largest Bitcoin block ever. The only way you'd be able to have a bigger effect on the block size is by using 15-of-15 or 63-of-63 multisig. But doing that would be a clear tipoff, and would also require more programming work.

If they were planning on consolidating those inputs, why did they hand them out as multisig addresses in the first place? It would be a lot cheaper to have your hot wallet be a monosig wallet, and collect e.g. 20 inputs together into one multisig output. That way you don't have to pay the extra fees for all those multisig inputs. (Segwit reduces the cost premium of multisig, but Segwit multisig is still more expensive than monosig.) But maybe their engineers didn't think of that, or maybe their security model doesn't permit it, I don't know.

In any case, whether or not it is spam, it is clearly one entity responsible for the burst of transactions.
1hash has about 70 PH/s, which would use about 7 MW of power without ASICBOOST. ASICBOOST would save them about 20% or 1.4 MW, and the transaction-permutation variant would save them roughly 5% or 0.35 MW more over the basic extranonce-grinding [B1](https://bitslog.wordpress.com/2017/04/10/the-relation-between-segwit-and-asicboost-covert-and-overt/) method (by allowing for 4-way collisions instead of 2-way collisions). Most miners pay around 4¢/kWh, or around $29.2k/(MW•month), so using regular ASICBOOST (without the transaction permutations) would save them around $41k/month, and using transaction permutations (like what seems to have gotten them in trouble here) would save them an additional $10k/month.

Making blocks with only 256 transactions each sacrifices about 80% of the total transaction fee revenue per block, or around 1 BTC per block. With 70 PH/s (1.1% of the network), that's about 47 btc or $130k in lost fee revenue in addition to the lost $70k in block rewards from these two malformed blocks for the privilege of saving an additional $10k per month.

Let me know if I made any major mistakes on my math. If not, and if this is actually due to transaction permutation ASICBOOST, it seems like 1hash is losing more money from bugs than it's saving on power, and also losing more money on transaction fees than it's saving on power.
> Did you register with the SEC? Under the Howey test you made a public offering of a security.

Our service is providing the actual computation, which we send via stratum to a pool of the customer's choice. At the time of the sale, we already had all of the hardware and most of the software which we needed to provide that service. Is Amazon AWS offering a security? What about companies that sell e.g. access to face recognition APIs for a fee? It's a service, not a security.

> A court would construe the ambiguities against the drafter. (Standard rule of construction to prevent fraudulent/deceptive drafting)

I don't think our phrasing was ambiguous, just easy to misread.

>> We advertised our contracts as non-refundable

> Probably a violation of the deceptive trade practices acts of most jurisdictions where you sold these.

With sales of goods, the jurisdiction of the recipient applies because goods are actually delivered to that location. With digital datacenter services like what we had, the jurisdiction of the datacenter applies, since that's where the actual service was performed. Washington state law (our jurisdiction) permits no-refund policies.

> I don't know the details, but it sounds like you should probably quit making public admissions.

This is probably good legal advice, but it's not my style. I prefer to be honest and straightforward about facts.
>  [A 2MB HF] cannot safely be done without ugly hacks to introduce new limits similar to BIP 109

Nothing can be safely done without new limits similar to BIP 109. Even doing nothing is unsafe. 

**[CVE-2013-2292](https://bitcointalk.org/?topic=140078)** is a vulnerability. A 3 minute verification time is a problem for Bitcoin. Bitcoin is (slightly) unsafe until that vulnerability is fixed. 

The only ways to deal with it are to forbid legacy-style transactions (which is untenable) or to introduce new (absurdly high) limits on them similar to BIP109. As SegWit does neither, it does not fix the vulnerability. The worst-case verification time with SegWit is still 3 minutes, versus about 10 seconds for BIP109. Whether or not we increase the base block size away from 1 MB, we should still fix CVE-2013-2292.
The use of VPS nodes has been helpful as a defense against the **[DDoS attacks](https://np.reddit.com/r/Bitcoin_Classic/comments/47zglz/ddos_started_again_have_a_nice_day_guys/d0gj13y)** that were directed against Classic.

I encouraged people on home internet connections or who couldn't afford to be DDoSed to not open ports on their firewalls for Classic nodes in order to make them less likely to be crawled by the DDoSer. This has the side-effect of making them not visible to people like Alex Petrov or to sites like CoinDance.
It allows for the witness data to be pruned off after a while. (Of course, the old transactions can already be pruned off, but witness pruning can happen a bit sooner.)

It allows for fraud proofs to be performed fairly efficiently.

Because SegWit stores data in a way that hides it from old versions of Bitcoin, SegWit also allows for a bunch of other changes and fixes that we previously thought would require a hard fork:

1. It provides a new format for transactions that do not suffer from the O(n^2 ) validation cost bug (though it does not disallow old-style transactions that do suffer from the bug, so the attacks are still possible).

2. It allows a new format in which Schnorr signatures can be implemented.

3. It allows a new format that does not suffer from transaction malleability.

And probably a few other things I've forgotten.
Malleability is a bigger issue for Lightning than it is for current payment systems.
I think if it weren't for Samson, neither I nor Jeff would have been invited at all. ~~I think the meeting was probably organized at the instigation of another person or entity.~~
**Edit: Check /u/homopit's comment below. I am no longer convinced of the validity of this comment, or the validity of the parent, due to uncertainty about what this graph actually represents.**

Try this graph, which adds some averaging to reduce the noise:

https://blockchain.info/charts/avg-confirmation-time?timespan=180days&showDataPoints=false&daysAverageString=7&show_header=true&scale=1&address=

Confirmation time has gone up by about 13% from around 8.1 minutes to about 9.1 minutes. This indicates that some transactions are getting delayed. Confirmation times are currently slightly worse than they were during the October spam attacks. It has been like this for about a week.

The way it works in these situations is that a transaction that is likely to not get included in one block is also likely to not get included in the next. Thus, what is probably happening is that most transactions are getting into the first block, whereas a small subset of transactions (the ones with the lowest fees/kB) are getting delayed by several blocks. Some are even getting delayed by hours.

My opinion is that if e.g. 5% of transactions are delayed by several hours, Bitcoin has a usability problem. That seems to be about where we are now.

It will probably get worse.

Yes, the 5-ish% of transactions that are being delayed have the lowest fees. People who sent those transactions could have avoided having their transactions by using a higher fee, thereby causing the second-worst transaction to get delayed instead.

Unless we increase the network capacity or users start sending fewer (or smaller) transactions, this problem will not go away. Throwing bigger fees at the problem does not make it go away.
I do not think that Slush owns any significant amount of hashpower. Central Europe is not known for having low electricity prices.
> Do you know when it will go live? 

Expect an announcement on Monday, I think. As for the project being downloadable? When it's ready.

> Also, when will it be on github?

We started filling in the github project a few days ago. https://github.com/bitcoinclassic/bitcoinclassic

> I took a look at the commits of Bitcoin Unlimited and they lack professionalism unfortunately (they need an experienced maintainer). What is the outcome we can expect with Bitcoin classic?

To be determined. I know a few of the people who have expressed an interest in contributing code and code-review to the project. They [don't suck](https://github.com/bitcoinclassic/bitcoinclassic/pull/1#issuecomment-169846464).
http://74.82.233.205:9332/static/share.html#000000000000000009e4f2cc52dc7801416fb57044781e45ef7defd766fd28ab

This block was mined on one of our p2pool nodes.
To get it through? No, it's not that hard. You might have to retry on some of the attempts if it gets stalled.

To get it through within 2 seconds? Yes, that is actually hard. 

Bandwidth across the GFW often averages 50 KB/s due to packet loss killing the TCP cwnd. Sometimes it's less than that, and will stall at 0 KB/s for 10 to 30 seconds. Often it's much faster than that, and you can get 3 MB/s across the GFW.

If anyone wants to see what downloading files across the GFW is like, send me a PM and I'll send you URLs for files hosted on some of my servers in China so you can test it yourself. I'm not publishing those URLs publicly because I don't want them hammered.

https://www.youtube.com/watch?v=ivgxcEOyWNs&feature=youtu.be&t=2h36m20s
I am not aiming for a Jan 11th activation date. I think BIP101 should be activated after a majority of players are convinced that it's the best thing to do. We may have to fix some bugs and optimize some code before that happens, and that's fine. I'm expecting something more like March.
It's easier to prevent BIP101 from happening by continuing to use Core than it is to trigger the fork with a minority supporting BIP101. In order to cause a premature BIP101 fork, you need > 25% of the hashrate to lie, plus another 25% to support Core. Furthermore, you need the 25% supporting Core to not switch to BIP101 when seeing a BIP101 majority.

Spoofing hashrate to cause a premature fork guarantees chaos when no chaos is necessary. The main reason to spoof hashrate is not to defeat big blocks, but rather to profit off of chaos by using the exchanges with insider information. People with control of a lot of hashrate are not likely to do this, as they have a vested interest in seeing Bitcoin work well.

The problem I have with 9,500 out of 10,000 is that it makes it very unlikely that the attempt will succeed. Sometimes, the worst thing you can do is nothing. I think that allowing blocks to fill up with non-spam transactions will result in a lot of damage, and we should do everything we can to prevent that from happening. We're running out of time to do this. Setting unnecessarily high thresholds may reduce the probability of chaos during a fork, but it will increase the probability of chaos during congestion.
I strongly suspect that this is just people using HDDs -- 10 ms seek time per tx => 100 tx/sec.

---

I wrote some code a few months ago that [made AcceptToMemoryPool effectively multithreaded](https://github.com/jtoomim/bitcoin-abc/commits/atmp-inflight). I then wrote a [microbenchmark](https://github.com/jtoomim/bitcoin-abc/blob/eb1616d2c04ced534c525bdf6c2d1e9921644978/src/test/mempool_tests.cpp#L831) to test its performance, and I saw that on a 4-core CPU, it improved ATMP performance by 3x! Great! Throughput jumped from 10,000 tx/sec to 30,000 tx/sec!!!

...

Wait a minute, 10,000 tx/sec on a single core? I thought the limit was 100 tx/sec. How can I be sure that my code fixed a problem that I can't reproduce?

In the Gigablock testnet, BU's initial setup used the default `-dbcache` (UTXO cache) setting of 300 MB, and had their UTXO database stored on the same HDD-based cloud-storage systems that held their blocks. When they ran into the 100 tx/sec bottleneck, Andrew Stone wrote parallelized ATMP code, which was deployed at roughly the same time as an increase in the `-dbcache` setting to around 8 GB. During their 2017 talk, Peter Rizun and Andrew Stone attributed the performance improvement to the ATMP parallelization. However, when I talked to them and sickpig in 2018, they were unable to refute the possibility that it was the dbcache change that actually improved performance.

I was using an SSD in my tests. Also, the UTXO set for my microbenchmark was small enough to fit in RAM. I checked the ATMP code fairly thoroughly, and the only thing I can think of that would slow ATMP down to 10 ms per tx is UTXO lookup. Other than that, the most CPU intensive process is script verification, which takes about 0.1 ms on a single core. Consequently, I believe that it's most likely that ATMP parallelization is currently unnecessary and unhelpful (until we get close to 10k tx/sec), and that we just need to require SSD usage and/or high dbcache values.

It would be very nice to set up a test to reproduce this issue and check this hypothesis. If other people are interested in helping to run such a test, I would contribute and help guide it.
I'm getting close to the point where a scaling test would be helpful in my development. I still really want to add the check-merkle-root-then-forward-then-connect-tip feature to Xthinner before spending too much time on propagation speed tests, as I expect it will make a big difference.

I'm a bit more interested in setting up a LAN-based regtest-mode propagation testbed using the Linux `netem` module to restrict bandwidth and inject latency and packet loss, as I think that will give a lot more useful data than mainnet tests. The main component I'm missing for this setup right now is a good high-speed spam generator. Does anyone have one they can suggest?
> xthinner's reported effectiveness depends criticality on tiny flows of transactions.

I don't know what you mean by "tiny flows of transactions", but if you mean that Xthinner only works at small transaction volumes, you are completely wrong. My standard encoding test case during development was blocks with [1.25 million transactions](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0) and a mempool of 2.5 million transactions. At this scale, Xthinner gets 12.23 bits per transaction. Xthinner's bits/tx performance generally increases with increasing block size until it asymptotes in the 12-16 bits/tx range.

It seems that you might have some incorrect assumptions about how Xthinner works, so I suggest you read [my article introducing Xthinner](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0), beginning with the "The problem and its solution" section.
Offtopic: I looked up their datacenter out of curiosity. Their thermal design sucks.

https://www.hyperblock.co/photo-tour

They have low density, and aren't using any form of hot or cold air containment. This makes their shelves long and numerous, their datacenter hot, and their power lines long and expensive. 

 6 S9s per shelf, 4 shelves per span, 9 spans per row, 48 rows visible in the photos = 10,368 S9s. That's about 15 MW without AsicBoost, or about 12 MW with AsicBoost. There might also be a bit more that's not visible.
System specs? CPU, RAM, SSD?

Are you doing full script execution on old transactions, or do you skip that for transactions in blocks that are before checkpoints?
It's already a thing. https://commerce.coinbase.com/
> The all-in weighted average operational cost across the Assets being acquired in the Transaction is US$0.073 per kWh.

Ouch. Revenue mining BTC is currently $0.063/kWh on S9s. That means that these machines are currently [losing $0.01/kWh](https://www.cryptocompare.com/mining/calculator/btc?HashingPower=10&HashingUnit=TH%2Fs&PowerConsumption=1000&CostPerkWh=0.073&MiningPoolFee=0) when they're running.
He's a coin.dance dev.
They constituted about 2.5 EH/s before. They constituted about 2.5 EH/s after.

Bitcoin.com constituted about 0.4 EH/s before the fork. After, they constituted about 4.5 EH/s.

Coingeek and BMG's hash didn't change. All that changed was what they were up against. The dragon was sleeping in Tokyo until the fork happened. And when the dragon woke, it was hungry.
Counterargument: All of the illegal activity that CSW claims CDSV enables [is already possible without CDSV](https://www.reddit.com/r/btc/comments/9vxc7w/illegal_gambling_futures_markets_on_murder_and/), and is more efficiently implemented with OP_SHA256 oracles than with OP_CDSV.

However, the legitimate uses of OP_CDSV like [securing 0-conf with forfeits](https://gist.github.com/awemany/619a5722d129dec25abf5de211d971bd), [Pay-To-Identity](https://gist.github.com/markblundeberg/bd28871548108fc66d958018b1bde085), and [the use of PGP keys to sign BCH transactions](https://gist.github.com/markblundeberg/af59d7cd234cbdb14dcf9e00f0ea2c17) cannot be done with existing opcodes, as these use cases specifically require the generalized ECDSA signature verification which OP_CDSV provides.
OP_CDSV is not purpose-built for cybercrime. It's a general tool for doing ECDSA verification off the stack of Bitcoin scripts. 

It's like a knife. Sure, it can be used to stab people, but it can also [be used to cut vegetables](https://gist.github.com/markblundeberg/af59d7cd234cbdb14dcf9e00f0ea2c17). If you outlaw knives, people will still commit murder, but they'll use ropes or guns or poison instead, and they won't eat as well.

It's worth noting that this SHA256 method for enabling oracles is actually cheaper in terms of on-chain fees than OP_CDSV is, at about 80 bytes (SHA256+message+script) instead of about 150 bytes (OP_CDSV + pubkey + signature + message + script).
> How did you guys incorporate SV in to mining pool software?

Incorporating Bitcoin SV into mining pool software is as simple as downloading the executable, stopping the old bitcoind process with `bitcoin-cli stop`, and running the new bitcoind. It takes about 3 minutes. All Bitcoin clients (including Bitcoin Core and all Bitcoin Cash variants) have pretty much the same RPC interfaces, and the most important RPC calls are supported on all clients.

Basically, mining pool software gets a block template by running the getblocktemplate RPC. You can do this at the command line with `bitcoin-cli getblocktemplate` to see what the output looks like. The pool software then summarizes this information by condensing most of the transactions into a merkle path instead of a list of transactions (i.e. O(log n) bytes), and sends that information via stratum to the mining hardware. When a mining machine finds a nonce+extranonce value that results in a valid block, the mining machine returns those values via stratum to the poolserver. The poolserver then reassembles the full block from the associated getblocktemplate data, and then submits the block back to bitcoind in hex via the submitblock RPC.

> Another question I have for ages is how do you know on the network what node is running what software?

User agent string.

https://github.com/bitcoin/bips/blob/master/bip-0014.mediawiki

https://en.bitcoin.it/wiki/Protocol_documentation#version

> When miners get new blocks from other miners is there ways to tell from what node software it came?

They often don't, unless the block violates a consensus rule unique to one client, or uses a unique version number in the block header, or unless the miner voluntarily sticks that information into the coinbase transaction's scriptsig field.
Do the Bitcoin SV developers believe that selfish mining is impossible? For the purpose of this question, selfish mining is any mining strategy that achieves greater profit for the selfish miner by creating orphan races which the selfish miner has a high chance of winning.


BU and XT nodes did not perform significantly better in the block propagation data than ABC nodes, as far as I could tell from a quick visual inspection.

http://toom.im/files/stressdata.txt

I might do a more complete analysis of this question soon, but for now, I would recommend refraining from stating that BU and XT are better unless you know of some data or analysis that I do not.

> parallel validation would provide significant improvement if adopted by ABC

Parallel block validation only helps if you have an orphan race with a block that propagates slowly but verifies quickly. Given that all blocks verify quickly except for poison blocks that exploit the now-fixed O( n^2 ) sighash bug, parallel validation provides nearly zero benefit.

Note: BU's parallel validation can only use one CPU core per block being validated. It does not make validation of a single block faster; it only allows two blocks to be validated at the same time in case of an orphan race. BU's parallel validation does not make orphan races any less likely, it can only change who wins the orphan race once it occurs.

> leverage multithreaded validation would provide significant improvement to Graphene and other (Xthiner) schemes

Um, that's not how it works. Multithreading block validation only helps *after* you've received the block. Graphene and Xthinner help you get the block in the first place. Given that block validation is about 5-10% of the total delay, and block propagation is 90-95% of the total delay, multithreaded block validation would currently only provide a very small benefit.
Hi. Most of the discussion of Bitcoin Cash happens in /r/btc, where as most of the discussion of Bitcoin Core's side of the fork (usually just called "Bitcoin") is in /r/bitcoin. It's a bit ironic, as Bitcoin Cash's ticker is BCH, not BTC. 

All of this happened as a result of disagreements about what to do with the blocksize limit. One side of the argument believed that the blocksize limit should be increased commensurate with user demand, and that there should always be enough capacity in the blockchain for real users. The other side believed that a blocksize limit was needed in order to keep the protocol safe against spam, and was also needed long-term to create a transaction fee market that would allow for miners to get paid even after the block subsidy approached zero.

During the great blocksize debates of 2013-2017, there was rampant censorship of the pro-big-block position on /r/bitcoin. Specifically, the moderation rules forbade the discussion of any "controversial hard fork" software, as an extension of their "no altcoin discussion" rule. This caused a lot of people to get banned from /r/bitcoin. In order to have a place to discuss things that was free of censorship, Roger Ver /u/memorydealers founded /r/btc, and most of the big block crowd went over there.

Eventually, after the failures of Bitcoin XT (Mike Hearn and Gavin Andresen), Bitcoin Classic (by Gavin Andresen and me) and Bitcoin Unlimited to gain supermajority hashrate support, it became clear that a hardfork blocksize increase would not happen as a majority event, so efforts shifted to developing a blocksize increase hardfork as a minority event. Development accelerated on this minority fork project when social manipulation techniques were used to convince a bunch of pro-big-block miners to support SegWit in the form of the SegWit2x proposal (supported by Jeff Garzik), which would combine SegWit's activation (which the small blockers wanted) with a hardfork doubling of the block size limit (which the big blockers wanted) to happen 3 months later. This compromise seemed reasonable to most people. However, once SegWit was locked in, the small blockers reneged on their promise to support a hard fork, and the SegWit2x project died, along with Jeff Garzik's soul. 

As a contingency against SegWit2x getting aborted, Bitmain and Amaury Sechet had begun development of a hardfork that would not require a majority of hashrate support. This fork was implemented in the Bitcoin ABC project (Adjustable Blocksize Cap), and later adopted by the Bitcoin Unlimited, Bitcoin XT, and Bitcoin Classic teams. These four software teams collaborated and defined the Bitcoin Cash specification, and each of the four clients implemented the spec. This spec activated a fork on August 1st, 2017, splitting Bitcoin Cash (BCH) off from the chain followed by the Bitcoin Core software (BTC).

The main distinguishing feature of Bitcoin Cash at launch was that it initially had a blocksize limit of 8 MB instead of 1 MB. This allowed for up to 8x as many transactions per second to be committed to the blockchain. Since then, the limit has been raised to 32 MB, and will rise again sooner or later, depending on when the community decides it's safe and appropriate.

Shortly after BCH launched, BTC experienced extreme congestion of its limited 1 MB blocks, and transaction fees went through the roof. For a few days, it cost about $50 on average to send a transaction on BTC. This caused a massive wave of disappointment and disenchantment in BTC, and resulted in a bear cycle in the market that dropped the exchange rate from its peak of $20,000 to today's level of $6500-7000. The loss of users has reduced the load on BTC, and consequently fees are no longer bad on BTC.

Developers on BTC are working on the Lightning Network, a scheme of allowing people to send Bitcoin around without actually sending Bitcoin around. It's a cool technology, but it has some usability issues. For example, it's an "interactive protocol", so it requires both the sender and recipient be online at the same time. It also requires everyone to run their own Lightning node; there are no light wallets for Lightning. It also has some questions about how well it can scale; it's possible that it might just change the scaling limit from a limit on the number of transactions per second to a limit on the number of total users.

Meanwhile, BCH has had consistently low transaction fees, but also consistently low usage. Developers on BCH are active at making further improvements to the scalability and usability of BCH. Proponents of BCH claim that it is more true to Satoshi Nakamoto's original vision. Satoshi once said "[Bitcoin can already scale much larger than \[Visa\] with existing hardware for a fraction of the cost. It never really hits a scale ceiling.](https://bitcointalk.org/index.php?topic=1391350.0)." The Bitcoin Cash team is trying to prove him right. But in order to do that, we had to fork off and create a second Bitcoin chain.

Many BCH proponents claim that because BCH follow's Satoshi's original scaling vision better than BTC does, that BCH is the true Bitcoin, and that BTC should be called Bitcoin Core or Segwitcoin instead. 
https://i.redd.it/f7esey8yg3001.png

https://news.bitcoin.com/wp-content/uploads/2017/10/difference-between-two-forms-of-bitcoin-1024x747.png

https://i.imgur.com/zItopS2.jpg

BTC proponents instead claim that BCH fundamentally changed the rules of the protocol in the hard fork, and that this makes BCH an altcoin, and is not deserving of using the word Bitcoin at all, so it should be called Bcash instead. They further claim that BCH is a scam that is trying to confuse new users and immorally capitalize on the Bitcoin name for short-term gain.

As you might expect, the two sides are not getting along very well these days.
I'm currently losing about 20% of my hashrate on p2pool. It looks like p2pool is taking about 3 seconds on average to process a 3-6 MB getblocktemplate response and generate new work. During that time, p2pool appears to not process or respond to stratum requests from my mining hardware. This problem can probably resolved fairly easily by threading. I'm looking into that now.

Note: this is a p2pool-specific issue. It's also not a reason to keep the blocksize limit low, since a simple workaround to this issue is to only generate smaller blocks.

If you want to see the realtime stats, check http://ml.toom.im:9348/static/graphs.html?Week

/u/rinexc

Edit: I capped my node's block creation at 2.3 MB for now (5am PDT, 12am UTC). I got the data from p2pool that I wanted, and now it's just costing me money in lost hashrate unnecessarily. 
There's a mechanism in the Bitcoin full-node code call DoS banning. If a peer sends you an invalid transaction or an invalid block, that means that they're violating the rules of Bitcoin and are wasting your processing power and bandwidth. In order to prevent such nodes from performing a denial of service attack against your node, when your node detects invalid data like this, it will disconnect that peer and ban it from future contact.
Yup. Unfortunately, parallel code is a ***** to debug, and full nodes need to be bug-free. This can't be rushed.
That's a good and reasonable point. Let's look into it closer. 

If the ratio of average outputs per transaction increased from 2.6 to 3.6, we would expect that the number of non-change outputs per transaction would increase from 1.6 to 2.6, so we would see a 40% reduction in the total number of outputs for the same number of real outputs.

In actually, the number of outputs dropped from around 5000 per block in November to about 3500 per block this month. That's a 39% reduction in the number of outputs, just like our prediction! Only one issue: the average number of outputs per transaction did not increase from 2.6 to 3.6. It only increased from 2.6 to about 2.9. That is only enough to explain a 16% reduction in the number of outputs.

But to answer your original question: if (a) batching made transactions more efficient in generating outputs (more outputs per kilobyte), and if (b) Bitcoin were capacity limited, then batching would result in more outputs. That hasn't happened. So either (a) or (b) is false. Personally, I suspect (b), as I'm pretty sure (a) is true.

For more additional hints that batching is not the explanation for the reduced transaction volume, we can look at the total transaction volume excluding long chains. Batching should make transactions no longer qualify as long chains, so that should make the chart go up. But it went down.

https://blockchain.info/charts/n-transactions-excluding-chains-longer-than-100

Or you can look at the transaction volume excluding popular addresses, and get an idea for how much non-exchange traffic there is. That went down too.

https://blockchain.info/charts/n-transactions-excluding-popular

Or we can look at the amount of BTC sent per day. That's down too.

https://blockchain.info/charts/output-volume
https://outputs.today/

Total outputs are also at 2-year lows. If batching were the explanation, outputs would be up.
They're probably just busy mitigating the recent DDoS attack against them and haven't had a chance to roll out the upgrade yet.

http://news.8btc.com/f2pool-under-a-wave-of-ddos-attacks
> 4MB of data will have 4 MB "worth" of txs.

Only if you think that a single [8.1 kB 1-input 1-output transaction](https://testnet.smartbit.com.au/tx/03bf2bf0c6ff002341c5fed6d3cf9f7b73b2790bfb85ad8c5b60924285d37759) has as much "worth" as **twenty-two** [373-byte 2-in 2-out transactions](https://blockchain.info/tx/a6d819386c1c0631e74013d1b3636ca5e7528b4826b847285ee0360d6c4fb49c), and that [3.7 MB blocks with 468 transactions](https://testnet.smartbit.com.au/block/00000000000016a805a7c5d27c3cc0ecb6d51372e15919dfb49d24bd56ae0a8b) are [totally cool](https://www.reddit.com/r/Bitcoin/comments/5f507l/core_is_the_new_big_blocker_37mb_mined_on_testnet/dahjg2w/).
I think that the activation of SegWit on Litecoin has caused maybe 2% of Bitcoin's market cap to flow to Litecoin via pro-SegWit Bitcoiners. This resulted in a non-significant downward force on Bitcoin (that was drowned out by other effects), but while $400m might be nearly nothing to Bitcoin, it doubled the market cap for Litecoin.

Activation of SegWit on Bitcoin may also increase the price, but the funding source would need to be different.

(Yes, I'm aware that market cap is not a conserved quantity, but it's a somewhat useful, though woefully inaccurate, approximation.)
I think Sergio mentions SegWit to show that these issues are still present in SegWit. Sergio does not say that these issues are unique to SegWit.  /u/sandakersmann implied that these issues are unique to SegWit with the title of this post, but I don't think SDL made that implication.
~100 GB is not unusual for an unpruned blockchain. If you delete the chaindata and resync with a pruned mode (e.g. `geth --fast` or `parity --warp`), you'll end up with only a few gigabytes of usage. 

Usage will creep up over time if you're using geth, since `geth --fast` doesn't continuously prune, it just starts with a pruned snapshot. `parity --warp` seems to do a bit better, so that's what I use on my laptop (currently showing 6.6 GiB).

If you want to switch to parity, you can continue to use your geth key files. If you want to use ethereum-wallet or Mist, just download parity, delete the old chaindata folder (but make sure you don't delete your keystore!), then run `parity --warp --geth`, wait for it to finish (should take around 5 minutes), and launch ethereum-wallet.
> I think It will be soon time to think about forking the POW to get rid of Bitmain. I don't see any worse danger to Bitcoin than this company at the moment.

Bitmain isn't the reason why we have miner production centralization. Economies of scale are the reason.

A substantial portion of the cost of a single mining ASIC is R&D costs. The amount invested in R&D also substantially influences the efficiency and performance of each mm^2 of silicon. The more chips you produce, the less the R&D per chip. Thus, companies who produce large numbers of chips can afford to invest more total in their chips (and make better chips) while also spending less per chip. Having one ASIC manufacturer (like Intel or Bitmain) for a given market that is much larger than the competition is almost guaranteed in this situation. 

Your "solution" of changing PoW functions also wouldn't have the effect that you're thinking. Bitmain has already sold almost all of the hardware that they've produced. They've already made all the money off of those sales that they will ever make. If you change the PoW function, you won't be punishing Bitmain itself; you'll be punishing Bitmain's customers. **Bitmain itself would actually *benefit* from a PoW function change**, as the whole mining industry would have to replace all of the miners that you just made obsolete. Bitmain already has the design expertise and manufacturing infrastructure in place; all they would have to do is design a new ASIC for the new PoW function and push it through their production line.

The true solution isn't to change the PoW function. The best options we have are either to switch to Proof of Stake or to accept that the level of decentralization that we can get from PoW is good enough for our needs. (51% attacks aren't exactly a common problem with Bitcoin, having been performed exactly 0 times so far.)
Since a block's capacity is no longer measured in bytes, spammers can use more bytes for the same block cost with SegWit. It doesn't displace 4x as many bytes of non-SegWit transactions, but it does force people to download 4x as many bytes. This kind of spam attack could also have the side-effect of increasing the incentive for mining pool centralization, especially if performed by a miner.

No, it's not a UTXO bloat attack vector, but it is a bandwidth usage attack vector. Bandwidth is very expensive or limited in some regions (e.g. China), whereas storage has the same cost to everybody. SegWit trades off one type of attack for another.
ASICs aren't produced in mainland China. For the 14/16 nm nodes, they're produced at TSMC (Taiwan), Global Foundries (New York), Samsung (Texas), or Intel (Oregon, Arizona, and Ireland), in the same places that produce graphics cards and thousands of other devices. They're designed by companies like Bitmain or Bitfury, then those designs are sent to the foundry, turned into silicon wafers, and then sent on to the next company in the production line to be cut into chips, packaged into a plastic housing like a QFN32, and eventually put onto a PCB. Most of those steps are pretty easy and can be done anywhere. China does that last bit cheaper, but it isn't the only place that can do it. Heck, I can do some of it in the kitchen with a toaster oven and some solder paste.

> Simple, they won't have access to SHA256(SHA256) ASICs at competitive prices. 

No, that is not the reason why mining is centralized in China. I can buy mining hardware for the same price as the Chinese do. Shipping is more, but for an Antminer S9, that's only $50 out of $1490. The reason that mining is more common in China is that electricity is that electricity is cheap, labor is cheap, and building mining farms is cheap. There is no market barrier that causes mining hardware to be more expensive on one side of the border than the other.

Most of the $1440 sticker price for an S9 is profit for Bitmain, which manufacturers like Bitmain and BW.com don't have to pay for their own mining farms. However, at least half of the mining in China is done by non-manufacturers, people who buy miners on the retail or wholesale markets, and who do so at essentially the same prices that you or I would get.
Dislike. Nodecounter.com should be a non-partisan purveyor of data.
I like that scenario.
That was the joke.
Typically, a "poll" is non-binding (for information purposes only), but a "vote" is a decision-making process.
It will take about a 15x increase in fees per block to be able to pay for bitcoin mining long term. (Mining costs about $3000 per block. Fees are currently about $200 per block.)

Long term, it should be easier to increase transaction volume 15x than to increase fee/tx 15x. 
The problem isn't "affording faster internet". They already operate most or all of their China nodes with 100 Mbps internet connectivity. 

The problem is that the Chinese government built a system that causes between 1% and 50% packet loss for any packets that cross the country's border. The exact rate of packet loss varies widely for any given pair of endpoints and from day to day, and it is not dependent on the amount of traffic between the endpoints. 

This causes TCP to become extremely slow and unreliable. The congestion avoidance algorithms used by TCP assume that packet loss is a sign of congestion and that packet loss goes away if the transmission rate is decreased. Thus, when the Great Firewall drops packets, the TCP algorithms reduce their transmission rate, assuming that that will fix the issue. Since the packet loss rate is not dependent on the amount of traffic, it doesn't fix the issue, and the packet loss continues. TCP then reduces the traffic flow rate even further. Et cetera. The net result of this is that you often will only get 10 KB/s to 100 KB/s of actual TCP traffic between two peers with 100+ Mbps connections just because they're on opposite sides of the Great Firewall.

It's not clear why China does this. Some people think it's an engineering flaw in their internet censorship system. Others (including me) think that it's an intentional effect designed to encourage domestic internet businesses. In any case, it's an algorithmic problem that has to be solved with code (like a good UDP algorithm), not hardware.
Glad to see that people read bitcoin-dev.
I'm currently mining with a 4 MB (3.81 MiB) blocksize soft limit. I will reduce that soon to 2 MB, and eventually 1 MB, then go back to 9.1 MB and repeat, since I want to collect data at many different block sizes. Much thanks to /u/prattler26 and /u/rromanchuk for their spam help, and to /u/darthandroid and /u/toomim for their help with data collection and analysis.

I will be doing some tests over the coming weeks with mining in different locations to see how well blocks propagate into and out of China.

http://52.0.87.218/ is a block explorer that's up sometimes. /u/rromanchuk maintains it.
BitcoinXT will not break from Bitcoin Core unless it has at least 75% support among miners. Polls suggest that support for big blocks among non-mining users is similar to or greater than support among miners. As such, I don't see how Bitcoin XT can be accurately described as forking without agreement from the economic consensus. If > 75% support XT and < 25% support Core, then if anything, I think Core would be the altcoin.

I think the definition of "altcoin" should include a sense of permanence. Altcoins are intended to operate in parallel with their parent coin indefinitely. Hardforks are intended to be resolved as quickly as possible, with only one dominant branch.
Theymos, Nov 2010.

Nice one. I suggest you wait a day or so, then post that as a link in /r/bitcoinxt and /r/bitcoin. 
Litecoin's creators are following Bitcoin Core's dogma, and do not intend to increase their capacity beyond the current 1MB+SegWit+2.5-minute-blocks limit of about 25 tx/sec. BCH is already past that at about 50-100 tx/sec (limited by AcceptToMemoryPool and/or the usage of HDDs instead of SSDs for the UTXO database by node operators).

> I do not trust some of the people promoting BCH...but do think Roger Ver makes great points...

I agree with you on these points. I think Roger Ver's decision to say that "BCH *is* Bitcoin" or "BCH is the true Bitcoin" is misleading and hard to defend. The currency that deserves the name "Bitcoin" (without qualifiers like "Cash" or "Gold") in my opinion is the one that has the most hashrate, and BCH does not have that. However, I think that statements like "BCH follows the original intent of Bitcoin closer than BTC", or "BCH follows the Bitcoin whitepaper better than BTC, as it is a peer-to-peer electronic *cash*", or "BCH follows Satoshi's vision for Bitcon" are valid, true, and defensible. So Roger's comments are close to being accurate, but he stepped too far and has been misleading and incorrect at times. I forgive him for this; he's only human, unlike me.

> This degree of censorship feels terrible, wrong.

Yeah, welcome to the club. The big-block movement had support from a majority of Bitcoin's users in 2014 and 2015, according to all of the polls I have seen from that time. But in 2015, Theymos decided to use his ownership of /r/bitcoin, bitcoin.org, and bitcointalk.org to censor any arguments that advocated for forks which disagreed with his personal philosophy for Bitcoin. When he announced this policy, he said that ["If 90% of /r/Bitcoin users find these policies to be intolerable, then I want these 90% of /r/Bitcoin users to leave."](https://www.reddit.com/r/Bitcoin/comments/3h9cq4/its_time_for_a_break_about_the_recent_mess/?st=ja45nlq1&sh=ab0c785c) Unfortunately, only 10% left. The censorship was effective, and after a year or so of only being able to hear one side of the argument, the people in /r/bitcoin and on bitcointalk (which together were the vast majority of the community) became convinced that the only side they heard was the correct side. 

This is the great irony of the censorship-resistant currency that Bitcoin is supposed to be.

Join /r/btc. That's where people can discuss all Bitcoin-related topics with no censorship and with transparent (and light) moderation.
Hi. I'm the developer of Xthinner. The main discussion thread for this is [here](https://old.reddit.com/r/btc/comments/bas60b/by_the_power_of_ctor_xthinner_is_now_working_with/ekekzf0/) on /r/btc. Feel free to tag me if you have any questions about the tech, or just check out the /r/btc thread.
> You can do the same thing with a minor loss in compression using TTOR.

For small blocks, the compression for a version of Xthinner with TTOR would be about half as good, and would degrade as blocks got bigger. For 1 GB blocks, Xthinner-TTOR would take up about 2.5x as much bandwidth as Xthinner-LTOR and would only be slightly (30%?) better than Compact Blocks. Xthinner-TTOR would also be more CPU intensive, as it would require sorting and desorting stages on each hop that aren't needed with LTOR.

> I'd be curious to see how Xthinner works with large blocks with high transaction counts

Performance is generally a bit better with high transaction counts. I've tested up to a few million tx/block, and that gives about 12.5 bits/tx versus the 15-16 that I have been getting with these mainnet blocks.

> this would produce a scenario where the mining nodes don't have a synced up mempool which is required for Xthinner to work

No, that's not generally correct. Highly desynchronous mempools only naturally happen when full nodes are straining their bandwidth, CPU, or disk IO (UXTO set read) capacity. As long as the full node is powerful enough to handle the transaction flow, each transaction should take the same amount of time to propagate through the network (about 2 sec) when blocks are big as when they're small. Thus, the percentage of transactions that should be missing due to recency should be about 0.3% independent of block size.

You might be thinking of the BSV blocks, which did not have synced mempools. This was not because they were big, but because the BSV miners generated the spam themselves and did not propagate the transactions normally on the p2p network. This scenario is known as a "poison block attack," since until BSV it was presumed that nobody would do this intentionally unless they had malicious intent. In any case, it's not a natural or typical scenario.
This article is misleading. Block size is averaging about [1.05-1.10 MB](https://www.blockchain.com/charts/avg-block-size?daysAverageString=7) nowadays. There was one day (Feb 11) in which block sizes were close to 1.3 MB, but that's what we call "[peaking](https://www.blockchain.com/charts/avg-block-size)" not "averaging."
What? He's a co-founder now? 

...

No. Luke-jr was briefly a contractor for Blockstream, but that didn't last very long. He was never a full-time employee, and definitely not a ~~contractor~~ co-founder.
> Obviously this is going to be a problem for most mature blockchain databases but wouldn't a larger block size add to this problem?

Currently, to get a copy of the current state of the Bitcoin ledger, you need to download and process all blocks sequentially. However, there's a technique that can avoid that requirement known as UTXO commitments. Ultimately, what a full node wants to know is "where" each coin (or fraction thereof) is, and what script ("address") that coin is locked by. UTXO commitments allow a node to download this information directly from any other untrusted node instead of downloading the full blocks. Currently, this UTXO set information is about 3 GB instead of 300 GB for the full blockchain.

But if UTXO commitments aren't implemented soon, sync times will become longer, and the minimum hardware requirements for running/syncing a full node will increase. It shouldn't get too terrible, though. SSD size and speed and network bandwidth both have been increasing at about 30% to 50% per year, which is almost as fast as crypto was growing. If it takes 10 years for crypto to go from a million users to a billion users, hardware will be about 30x as fast while usage will be about 1000x as high. Given that we already have at least 10x performance headroom in Bitcoin Cash vs total crypto demand, and would have 100x headroom or better if the code was in better shape (e.g. better parallelization and better block propagation algorithms), I think it will not be difficult to stay ahead of demand indefinitely.

> The Bitcoin block chain is ridiculously oversized, sometimes taking days or even weeks to fully sync a fresh node. 

By the way, it should only take about four to eight hours if your node is configured properly. If you run your node with -dbcache=8192 as a command-line option (and if you have at least 12 GB of RAM), the sync process will go much faster. Multi-day or week-long sync times generally only happen if you're on a spinning platter HDD or a USB/SD card storage system. If you're using a proper SATA or m.2 SSD, sync times should only be about a day even without enough RAM for a good -dbcache. A good SSD should be considered the minimum spec for running a full node. (The SSD only needs to store the chainstate folder, which is currently about 3 GB; the blocks can be stored on a cheap HDD if you want.)

> Would the increased capacity lead to latency issues under stress?

Do you mean block propagation latency (which matters to miners), or do you mean transaction validation latency (which matters to users)? If the latter, no. If the former, yes, but only until the current block propagation algorithms get fixed by something better (*cough* [Xthinner+Blocktorrent](https://www.reddit.com/r/btc/comments/afeput/xthinnerblocktorrent_development_status_update/) *cough*).
Keep in mind that Xthin and Compact Blocks (i.e. the status quo) get compression ratios of 96% or a bit better. Xthinner is definitely better than the status quo, but it's not 250x better.
It's a feature that reduces power consumption of a mining device by about 20% on devices which support it. Notably, as of November 2018, this includes the Antminer S9, the world's most common mining machine.
1. Graphene is probabilistic, and when it fails, transmission attempts need to be restarted from scratch. There is no graceful degradation of performance. Last time I saw data on this, Graphene only succeeded on 41% of propagation attempts.

2. Because Graphene is probabilistic, it needs a good and reliable fallback system.

3. Graphene performance gets really bad if mempool synchrony is low.

4. Graphene does not lend itself well to encoding of partial blocks with techniques like Blocktorrent, and may have difficulty scaling past around 400 MB blocksizes.

Graphene is still promising, and I'm looking forward to it becoming more mature. I personally don't want to rely on other people's ideas working out properly, though, so I'm coding something myself which I think is more certain to work and has a decent chance of being better overall.
Tagging some people who've expressed interest in this topic. Please do not upvote this comment.

/u/jonald_fyookball /u/markblundeberg /u/thorvszeus 

Edit: 4 points? You guys are rebels.
Possible, but they said that they were all-in on BCH, so if they did this, it would also be an example of dishonesty and hypocrisy.
> To my surprise, I saw a bunch of 'coingeek' during the following days when they were showing off a lot of hashrate, even though they brag they own all of their hashpower. 

Dang, that's really useful information. Huge kudos for that. 

Normally, I'd chaintip you for that, but chaintip is offline for the fork. Would you mind giving me a BCH address?

> What's your feeling about this?

Yes, we're not out of the woods yet. There's a balance to be struck between security and cost. It will be important to not misjudge that balance too much in either direction. I think ABC's hashrate yesterday is too much today, so I think reducing hash for now is the correct direction to take.
I joined Bitcoin.com, and my hashrate is not being subsidized by Roger Ver. I think a lot of other miners did too; it seemed like a good way to keep us organized against a potential 51% attack from CSW. Which might still happen.
It's a super easy fix. I have no idea why they haven't done it yet. Negligence?

The 7-14 tx limit is not in AcceptToMemoryPool. It's actually the rate at which an SV node or an ABC 0.17.2 (or older) node will notify peers about new transactions. If you send transactions *to* an SV node at 100 tx/sec, the SV node will accept them, but it will only forward 7-14 tx/sec of them.
The blog post did not once mention hashpower, the longest chain, or Nakamoto Consensus.

The blog post did mention that if there's a persistent chain split, Coinbase will support both chains, *including the shorter one*. That's kind of the opposite of the SV interpretation of Nakamoto Consensus.
Plagiarizing from a plagiarizer is still plagiarism.

It doesn't matter where it was copied from without attribution. It only matters that it was copied without attribution and passed off as novel work.
> It seems like you are a full on participant in the dev team now?

I'm not committed to ABC in particular, but the things that I think BCH has needed the most recently have been things that specifically ABC has needed. That includes the INV_BROADCAST_MAX(_PER_MB) bug/oversight, which was unique to ABC and SV, as well as the ATMP parallelization, which BU has already done in /u/thezerg's gigaperf1 branch (and which I think was recently merged into BU master). If/when I get to work on Xthinner, I will probably implement that into ABC first too because ABC is more committed to CTOR+LTOR, and LTOR makes Xthinner implementation easier. But I'll make sure to keep in contact with dgenr8 (XT) and the BU team, and will write a formal spec in parallel with the initial prototype implementation to allow the other clients to get their own versions of the code done shortly after the first client has it.

I really like the BU approach of moving fast with experimental new technologies, so I'm only choosing ABC right now based on practical concerns, not based on ideology. 

My mining operation in WA was handed a death sentence by our utility company on Aug 28th, so I've been shifting gears. I've still been interested in crypto, so since then I've devoted most of my crypto energy towards development instead of datacenter design and construction. However, I also have two other neuroscience start-ups that I'm working on, and think that they probably each have a greater expected value to humanity from my participation in them than in BCH, so I will likely be transitioning most of my focus back to them over the next month or three. Really, I'm trying to do a BCH code sprint to get a few things moving before the Nov fork (and hopefully to keep the community from falling apart) before I move mostly on.
LN is not (or should not be) a consumer tech. It's for B2B or for blockchain apps, not for buying stuff.

Want to set up electronic micropayments for ad revenue for your website? Great, use Lightning! Want to pay your facial recognition API provider for each use of their API? Great, use Lightning! Want to make a new video game called Cryptopuppies in which you can breed and trade electronic dogs? Great, use Lightning! 

Want to buy a coffee at Starbucks? Great, use a 0-conf on-chain transaction!
> For example it would be better to say that graphene works just as well with optional ordering as compared to CTOR (miner enforcement of ordering).

No, because:

1. [Adversarial](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1219#post-78785) [conditions](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1220#post-78819).
2. It's a lot easier to optimize code if you know for sure what order the data is coming in.

Most of the benefit? Okay, sure. Just as well? No.
Enabling Graphene is not a fork. Graphene already sort of works on mainnet with BU's implementation. Graphene is still in an alpha or beta state, though, and needs a lot more development time before it is reliable. AFAIK, the formal specification for Graphene has not been completed, so ABC and XT have not begun to implement Graphene yet; the only implementation so far is BU's evolving prototype on which the spec will eventually be written.

In addition to being 7x more efficient, the code for Graphene is simpler if we have CTOR, so it's also possible that ABC has been waiting for the fork before implementing Graphene so that they don't have to throw obsolete code away afterwards.
There are two possibilities here: either he invented Bitcoin, or he didn't. We can evaluate the evidence in light of both possibilities to see which of them fits best.

Satoshi was a very patient, calm, and collected in his conversations with others. He never swore. He never used exclamation points. He always used clear, concise language, and avoided jargon or fancy words whenever he could. Satoshi [listened to criticism](https://bitcointalk.org/index.php?topic=2151.msg28228#msg28228) and responded in a calm, detailed fashion. He was always quick to admit [when he didn't understand something](https://bitcointalk.org/index.php?topic=1540.msg18511#msg18511). Try reading [some of Satoshi's comments](https://bitcointalk.org/index.php?action=profile;u=3;sa=showPosts;start=480) in Craig's voice. It doesn't work.

The inventor of Bitcoin would not have blocked anybody on Twitter who disagrees with him. The inventor of Bitcoin **would not have blocked Gavin Andresen**, the man that Satoshi entrusted to take care of Bitcoin after he left. The inventor of Bitcoin, a person who never uses exclamation points in his writing, would never give an interview in which he [uses the word "fuck" 47 seven times](https://steemit.com/bitcoin/@ultralord/craight-wright-i-ve-got-the-first-fucking-nine-keys-i-ve-got-the-fucking-genesis-bloody-block-i-ve-got-the-fucking-code-i-ve-got). Not that there's anything wrong with that word or anything; I'm just noting that [lexical analysis](https://en.wikipedia.org/wiki/Lexical_analysis) shows that Craig Wright does not use the same vocabulary as Satoshi.

The inventor of Bitcoin might choose to claim to be Satoshi Nakamoto in order to gain recognition and praise, so the fact that Craig claims to be Satoshi is plausible if taken alone.

A non-inventor of Bitcoin might also choose to claim to be Satoshi Nakamoto in order to gain recognition, fame, and access to other people's investment money, so it is also plausible that Craig would claim to be Satoshi even if he weren't.

There is only one creator of Bitcoin on the planet, but there are hundreds of millions of people who lie about their qualifications. Statistically, without knowing anything else about the case, it is more likely that someone claiming to be Satoshi without providing cryptographic proof would be a scammer than the real person. Combining that with the personality analysis and the lexical analysis makes a convincing case that Craig Wright is not Satoshi.

In any case, even Satoshi did not invent Ethereum. Craig Wright's patent was basically a clone of the concepts of Ethereum submitted 2 years after Ethereum's yellow paper. Even if Craig were Satoshi, trying to patent someone else's project after it has already been released is **not cool.** A Satoshi that tried things like that is a Satoshi that I would want gone from the community.
Seems like it might help if the community had a lawyer who watched his filings and submitted prior art to aid the examiners. I'd be willing to help pay for this. I think the Ethereum Foundation might also be interested, and they're not poor.
I think it's better than what we're doing right now. I think the 75% activation threshold is a pretty good target for any change which evokes strong feelings, and 95% is a good target for changes which are non-controversial. (For the latter, you want to minimize disruption from the timing of the fork. For the former, you want to make sure that the risk/cost of a false negative balances the risk of a false positive.)
I think aggressive downvoting is an appropriate response. If you think a comment or post detracts from the discussion quality of a subreddit, you should downvote it to reduce its visibility.
> Point 1 is false. Parallelism is possible with the current ordering.

You are correct; the current topological ordering can be parallelized with the same algorithm that would be used with a canonical lexical ordering.

/u/markblundeberg is also correct in that point 1 was a commonly used argument for canonical lexical ordering. I think I was the first person to show that the parallelization can be [easily done](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244/files#diff-24efdb00bfbe56b140fb006b562cc70bR2222) even with the current topological ordering. As such, point 1 is an invalid argument for lexical ordering.
Hi, miner here. I'm aware of the stress test plans and am looking forward to analyzing the resuts.

However, I run p2pool, and I am not planning on enabling 32 MB blocks on my nodes. P2pool is written in Python, a rather slow programming language, and it currently needs to process every transaction that gets included in each block in Python. This is a big performance bottleneck. I suspect there will be some performance issues on the BCH p2pool even with 8 MB blocks. Consequently, I am keeping p2pool limited to 8 MB for now. I am looking forward to the stress test to see how things turn out with 8 MB block templates (and shares) for a day, and if it goes well, I may patch p2pool to allow 32 MB blocks and shares in the future. If it doesn't go well, then I may put in a weekend or two to try to improve the performance of p2pool.

If there were actual demand for 32 MB of transactions on BCH, I also could switch to solo mining using faster software (e.g. [ckpool](https://bitbucket.org/ckolivas/ckpool)), but for now I prefer to keep p2pool going.
The mod logs are here:

https://snew.github.io/r/btc/about/log#?theme=btc

In them, I see the following entry:

> banuser by BitcoinXio  14 hours ago  Ban evasion permanent bitmaincash

The reason for this ban is alleged ban evasion. This means that BitcoinXio was claiming that /u/bitmaincash is an account created by a user who was previously banned for a different reason, but who created the bitmaincash username (e.g. using the same IP address) in order to avoid this ban. I don't know if that's true or not, but given that the bitmaincash account is only 5 days old, and immediately jumped into conversations as if he was familiar with talking points on the typical discussions, and given that the username is a slur against a specific cryptocurrency that is popular in that sub, it seems plausible that the user behind bitmaincash is actually evading a ban.

/u/BitcoinXio, if you're not banned in this sub, can you comment to explain the ban, and to specify which previously banned account you think bitmaincash is derived from? If you are banned in this sub, feel free to PM me your explanation, and I'll post it here in your place.
Actually, it still works on Core if you do extranonce grinding and if you only go for 2-way collisions instead of 4-way collisions. The "covert ASICBOOST" thing was really just a small optimization that made it about 10x easier to get 4-way collisions. 2-way collisions are easy enough that you don't need the 10x factor.

Only method c2 described in this link would be blocked:

https://bitslog.wordpress.com/2017/04/10/the-relation-between-segwit-and-asicboost-covert-and-overt/
Fast sync doesn't process all the history, but it does process everything after the sync is done. Because it's Ethereum and not Bitcoin, you don't have to process the full history in order to be secure.

Bitcoin does not store a hash of the UTXO database state in the block, which means that the only way to be certain of the correct UTXO state is to play through all transactions ever. Unlike Bitcoin, Ethereum *does* contain a hash of the current database state (`state_root`, the root of the state trie) **[in the block header](https://github.com/ethereum/wiki/wiki/Block-Protocol-2.0)**, which means that you can download the current database state from someone, check its root hash against the block it belongs to, check the proof of work in the previous 1000 or so blocks, and know that the database state was not falsified.
Yes, you can use Trezor on BU just fine.
In comparison, Bitcoin is currently at around **[250k/day](https://blockchain.info/charts/n-transactions?daysAverageString=7)**.


Ethereum currently has about 17% as much transaction volume and 5.5% as high a market capitalization as Bitcoin.
https://blog.ethereum.org/2016/09/22/ethereum-network-currently-undergoing-dos-attack/

Appears to be due to an opcode that doesn't have a high enough gas cost. Someone is abusing it. Lots of the network hashrate (probably one or two big pools) have already responded by reducing the gas limit in their blocks.

On my nodes, I'm seeing up to 16 GiB of virtual memory being used. This crashed one of my nodes twice, since it only had 8 GiB of RAM and 2 GiB of swap. I added more swap space, and that seems to have helped the crashing. I also changed the db cache size according to the blog post recommendations, and I'm now making it through the attack blocks in about 5 seconds on that machine. My other server has 16 GiB of RAM and a 4.4 GHz quad-core CPU, and it makes it through the attack blocks in about 2-3 seconds. Both have SSDs and are running Parity 1.3.
>Is it really true?

No, it isn't. There is no difference between small and large miners in terms of the incentives for including transactions in a block. Both small and large miners want to include the number of transactions that maximizes their expected revenue. Both small and large miners want to minimize orphans. 
logips=1 will only make it report the IP address of the node that connects to you when the connection is made. It's pretty innocuous in terms of performance effects. It adds about 16 bytes of data per minute to your log files.
I urge people to be on the lookout for subtle bugs injected intentionally. Be very careful about submissions to Github or to consider.it from people with questionable agendas.

Try to be welcoming to newbies, and be friendly, but don't let yourself get duped.

Note that these are not Core devs, just Core fanatics. Don't punish the innocent just because it happened in the Bitcoin Core slack.

Cross post on /r/btc: https://np.reddit.com/r/btc/comments/41skqy/it_should_not_be_hard_to_inject_bugs_into_bitcoin/
We'll need something like https://www.reddit.com/r/marijuanaenthusiasts/ then.
Because hard forks are *scaaaary*! /s
We have been in conversations with Brian Armstrong of Coinbase. We have used some of BitPay's code already. We've talked with the Bitcoin Unlimited folks and are discussing collaborations (e.g. on **[blocktorrent](https://bitco.in/forum/threads/towards-faster-block-propagation-jtoomims-blocktorrent-proposal.742/)**).
> you should criticize the other startups with millions in funding for not doing so (Coinbase, 21, etc.)

Agree. Have an upvote.

(I've had a few chats with large-scale mining companies recently. They agree that they should be hiring their own Core developers, and are accepting applications. If you want to get paid to work on Bitcoin Core, send me a note and I'll tell you how and with whom to apply for a job.)
I did not use his name to indicate that he supported it. I used his name to indicate that he proposed it. It doesn't have a BIP number, so using his name is the best way I could think of to identify it.
Apparently, blockcypher uses BitcoinXT or supports BIP101 in their backend. Most of the other block explorers do not. Unfortunately, it does not display the block size, so here's the bitcoin-cli getblock results for that hash:

    "hash" : "0000000000000169b59ac09e7d1bb1db5287a4a0567f80a7e0b1477fc67eb9c5",
    "confirmations" : 4,
    "size" : 1109130,
    "height" : 584265,
    "version" : 536870919,
    "merkleroot" : "f6005da73a7ab6bc6c6bb03e70dad1fa432c8305cd96c32b555d00c0558c3288",
    "tx" : [
        "48b5ae18a41429637c83998868d831401d6b8139a8c361343b7b0f16f444efaa",
    ...

Compare:

Up to 584269 as of this writing:
https://live.blockcypher.com/btc-testnet/

Stuck at 584264: https://www.blocktrail.com/tBTC/blocks/1

Over the next couple of hours, I'll switch over to mining on Core and make the non-BIP101 chain catch up and take over the BIP101 chain, creating a reorg.

Edit: switching back to Core now. Mon Nov  9 07:42:53 PST 2015
It's just luck. We've got about the same amount of hashrate on XT now as we did a week ago, but last week we were very lucky and this week we're pretty unlucky.
If you move to Kansas City (or one of [many other cities](https://fiber.google.com/about/)), you'll have access to Google Fiber. 1 Gbps for $70. My home town also has 1 Gbps internet for $40. Bitcoin full nodes in your home will be practical there. It should never be the case that only datacenters have enough bandwidth for Bitcoin. It might be the case that a lot of home internet connections will not have enough bandwidth for Bitcoin. 

Mining in the People's Republic of Congo? You might have trouble with that, unless you're mining diamonds.

A full node in every home is not a reasonable goal for Bitcoin, in my opinion. It is not reasonable with 1 MB blocks either. The blockchain is already 47 GB. Each time someone uses Bitcoin, that size increases. It's not feasible to get every single user to download the whole blockchain.

It's also not necessary. The blockchain needs to be stored in enough places to keep the miners and the high-bandwidth hub nodes honest. Small leaf-nodes need to be possible for enthusiasts to run in order to verify that nothing fishy is going on. The rest of Bitcoin users can run SPV wallets, and only fully verify the cryptography relating to the addresses they interact with (plus a random sample of other addresses to establish that the server has a full and verified copy of the blockchain).
I've been trying to figure out why theymos considers XT to be an altcoin. Given this quote, I don't think it's because a hard fork is involved. I think the actual reason he thinks it's an altcoin is because it's not Bitcoin Core. That is, Bitcoin XT is an attempt to remove control of the Bitcoin protocol from the Core developers with git commit access, the Council of Five. Many in the Council of Five (plus Theymos, who seems to consider himself an honorary member) have found this to be offensive and a violation of loyalty/fidelity principles, so they think that Gavin and Mike and all the treasonous rebels who support them should be punished for their crimes.
800 inputs will take around 150 kB. The limit on a single transaction's size is 100 kB.

You'll need to consolidate your UTXOs. Send part of your money (half?) to yourself, then send a larger amount, and keep doing this until you can send all of your money to yourself. This will make a single output with all of your coins. Then continue with the splitting procedure.
An investigation of CSW's claimed addresses:

https://blog.wizsec.jp/2018/02/kleiman-v-craig-wright-bitcoins.html
> Fibre is actually robust and has existed for years.

FIBRE is not robust against somebody abducting Matt Corallo and coercing him to modify the code running on his servers in order to delay or censor someone's blocks. It's not robust against DDoS or government intervention. And it's not robust against simply feeding it invalid blocks. (Btw, does it validate the merkle root before sending the first packet?) FIBRE is an amazing system with excellent performance, but it's centralized and requires trust, so it cannot be guaranteed to work at all in adversarial conditions. It's great as a supplementary tool to shave off the last few hundred ms, but it can't be the base protocol in its current form.
I would suggest that people who care about BU's future apply to join the BU membership.

I don't think a partisan battle between BCH and BSV is good for BU. What we need is reasonable people who want to see BU succeed as a project.
I meant the wallet and associated funds belonging to the BU organization, not the wallet implementation in the BU codebase.
Because the Carol - David connection only has capacity in the opposite direction.
Neurogenesis in adult humans is very restricted. It only occurs in the olfactory bulb and in the subventricular zone (SVZ) near the dentate gyrus of the hippocampus. These two structures comprise less than 0.1% of the total brain volume. 

The olfactory bulb neurons migrate to the nasal epithelium. These neurons directly sample the air you breathe through the nose and provide you with your sense of smell. Because they are directly exposed to air, they have a tendency to get killed every now and then, which is why there's neurogenesis to replace them. 

The SVZ neurons migrate into the hippocampus, and are involved with episodic memory. That's the memory you have for your life history. As we're constantly adding to your life history, it's not surprising that we constantly add neurons here. These new neurons are not involved with procedural memories, so the argument that we can learn new skills as adults because we're still generating new neurons and is therefore incredibly plastic is nonsense. We *can* learn new skills as adults, but that isn't because we're adding new neurons; it's because we can reconfigure the networks and synaptic strengths of old neurons.

Most of the excitement about neurogenesis in the adult neocortex was based on a well-publicized few studies done in the late 90's and early 2000's that happened to be wrong. These studies used a technique called BrdU labeling to identify DNA synthesis. They injected BrdU into primate and rodent animals, and found that upon death the brains of these animals showed significant numbers of BrdU-labeled neurons in most parts of the brain, including the most evolutionarily modern and important one, the neocortex. This was interpreted as a sign that these cells had undergone division during or shortly after the BrdU injection. People got excited, and this conclusion reached the mainstream media.

Unfortunately, that conclusion was wrong. BrdU is a sign of DNA synthesis. Cell division is one cause of DNA synthesis, but [it is not the only cause](https://www.sciencedirect.com/science/article/pii/S0165017306001044). DNA repair, cell cycle reentry, and gene duplication events also cause DNA synthesis, and these can cause cells to be BrdU-labeled even without cell division. Subsequent studies were done to try to isolate cells that had DNA synthesis specifically because of cell division, using techniques like [^(14)C](https://www.pnas.org/content/pnas/103/33/12564.full.pdf) or [^(3)H](https://www.researchgate.net/profile/Pasko_Rakic/publication/11536084_PROGRESSNeurogenesis_in_adult_primate_neocortex_an_evaluation_of_the_evidence/links/0deec52b1101020349000000.pdf) radiolabeling, and they did not find any such neurons in the adult primate neocortex.

The idea that adult neurogenesis is widespread makes people feel good, like they're free to redefine themselves and determine their own fate. This made the original meme that neurogenesis happens spread like wildfire, but the later meme that neurogenesis does not happen did not spread very quickly at all. Consequently, many people still believe that adult cortical neurogenesis occurs except in neuroscience circles where people are more likely to have reviewed all of the evidence and where sensationalist journalism biases aren't as strong.

tl;dr: Journalists got excited about early studies on neurogenesis which turned out to be wrong due to methodological issues. The general public learned about the original excitement, but not the later corrections using better methodology.
> If I wanted to do half the non-file storage related things I want to do with the blockchain

Can you explain what things you want to do that you think require committing large amounts of data to the blockchain, and why hash commitments are not suitable for this task?
Historical states in Ethereum are akin to indexed block explorers on Bitcoin.

> but by definition the mode that has the most complete set of data is the full node mode.

By that definition, Bitcoin Core cannot be a full node, because Bitcoin Core does not keep an index of all addresses, nor a map of txid->transaction associations. In order to run a full node, according to your definition, you need to run something like [Bitpay Insight](https://github.com/bitpay/insight), because Insight's copy of the dataset is more complete than Bitcoin Core's.

But you might say, "Bitcoin Core's copy of the blockchain *is* complete! It has every transaction and block ever! The only thing that Bitpay Insight has that Core does not have is multiple *views* of the same dataset!" To which I respond: That's *exactly what the historical states are*. If you don't keep a stored copy all of the historical states on Ethereum, but you do keep a copy of all of the blocks, you can recompute any state you want, although not quickly. If you don't keep an indexed copy of all of the transactions in Bitcoin, but you want to look up a specific address, you can find every transaction you want by re-reading the whole blockchain and scanning for those transaction, but you can't do that quickly.

In other words, historical states are only needed for block explorers and other similar uses. Block explorers have requirements that go above and beyond that of a full node.
> His proclamation about being satoshi was very shortlived, he has not been making those claims for quite a while.

No, he's still claiming to be Satoshi. Nov 8, 2018: "I AM Satoshi. Have a nice life. You will now discover me pissed off."

https://www.reddit.com/r/btc/comments/9v9um9/a_letter_from_csw_to_roger_ver/
No. This reduces the data needed to transmit the block, but it doesn't reduce the size of the block itself. A 2,500 tx block will still take up about 1 MB; it's just that with Xthinner, you could transmit that 1 MB block using only ~~40~~  4 kB of network bandwidth.

BTC also lacks CTOR/LTOR, which means Xthinner won't work as well and would require substantial modifications to work at all.
I think it will still work, but it might make things a little trickier, especially for making data consistently fit within one IP packet..
I'm mostly interested in Nov 14-20, but I'm happy to take the whole thing. If you want to scp it to my server, send me your ssh pubkey privately.
My math says around 2x as much hashpower is currently active on BCH. It does appear that there was more than that during one of the two early bursts, but not 5x. Maybe 3x at peak.
The plan to increase the blocksize limit to 128 MB immediately is dangerous. Current node performance with block propagation [is too poor to safely sustain more than 20 MB blocks](https://medium.com/@j_73307/block-propagation-data-from-bitcoin-cashs-stress-test-5b1d7d39a234); blocks larger than this will create incentives that encourage miners to join a small number of superpools, which compromises BCH's security by centralizing mining hashpower and making 51% attacks on the network more feasible and likely.

Technology like CTOR, Graphene, and [Xthinner](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0) were designed to improve block propagation and allow for safe increases in the block size limit, but BitcoinSV has none of those.
> So there is no way to know what version of bitcoind with the new rule set in it is active on coingeek, bmg and svpool.

Correct, all you can do is take their word for it or not. I don't see any reason to doubt this claim, so I recommend taking their word for it.

> What are possible scenarios that can happen when 50% of the current hashrate is on a different rule set?

Nodes running one ruleset will ban any node that sends a block or transaction to it that violates its ruleset. The network will partition into two independent sections. The two chains will not interact. Transactions probably will not even replay across chains unless someone intentionally sets up a bridge between the two chains.

> I assume nothing will happen until somebody broadcasts a transactions with a script size over 201 bytes.

CTOR will make pretty much all ABC blocks invalid to SV, and all SV blocks invalid to ABC. It's possible to make blocks that follow both TTOR and CTOR by avoiding any transaction chains, but I don't think anybody has bothered to implement this.
On Nov 15, you will suddenly have coins on both the ABC and SV chains. If you spend coins on one chain without making any special efforts, they will probably be spent on the other chain as well. This is because Bitcoin SV removed replay protection from their fork. However, it will still be possible to craft special transactions that are good only on one chain or the other chain -- this process is known as splitting your coins. Some exchanges might provide this service for you (Poloniex did it with the ETH/ETC fork, IIRC), and you also might be able to do it either with a wallet plugin or by receiving some coins from an intentionally tainted wallet. 

I recommend not spending money until you've had a chance to split your coins. Tools for coin splitting will appear shortly after the fork if it appears that both chains will survive.
The group that flies this plane is Condor Squadron Vintage Flying Club. They fly several WWII-era planes, mostly American, and do formation flying and occasional simulated dogfights.

https://youtu.be/5FrCD-WC4-M

https://www.condorsquadron.org/
Will there be a hash war? If so, how will a hash war occur when Bitcoin SV and Bitcoin ABC's rulesets both comprise mutually exclusive hard forks?
Ultimately, I don't care too much about the 128 MB issue. I think it's absurd and foolish, but not absurdly dangerous. The [opcode limit removal](https://github.com/bitcoin-sv/bitcoin-sv/commit/a4bfa1ca55b7f4291b6e790f2c2ae2b063147b6d) hard fork that SV is proposing is a lot more worrisome than the block size limit. But the most worrisome thing is the attempt to block CTOR, since that blocks a lot of engineering work that is being done to improve the actual performance of Bitcoin Cash.
You've probably missed the many discussions in which those specific situations have been discussed. Here, let me link a few of them for you:

https://www.reddit.com/r/btc/comments/9cq62n/during_the_bch_stress_test_graphene_block_were_on/e5zweh2/

https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1239#post-80503

https://www.reddit.com/r/btc/comments/9cq62n/during_the_bch_stress_test_graphene_block_were_on/e5d6tfj/

https://www.reddit.com/r/btc/comments/9cq62n/during_the_bch_stress_test_graphene_block_were_on/e5hlfz4/

https://www.reddit.com/r/btc/comments/9dvsen/why_abcs_ctor_will_not_scale_andrew_stone_medium/e5kw1cg/
ATMP performance should limit throughput to about 100 tx/sec (roughly 15-25 MB per 10 minutes). We saw a limit that was much lower than that, at roughly 3 MB per 10 minutes. This second limit was caused by the [INV rate-limiting code at 7-14 tx/sec](https://www.reddit.com/r/btc/comments/9d94ja/bch_stress_test_failed_to_produce_even_on_maximum/) inherited from Bitcoin Core. That's a very easy fix, though.
Awemany's optimizations are about trying to reduce UTXO accesses by around 5% or so in the best-case scenario. His optimizations make sharding nearly impossible; I checked in detail. And the cost of this is that you can't get embarrassingly parallel processing of transactions in a block, and have to check for dependencies before you can split up the work between threads. To me, this does not seem close to being a good bargain.

> Besides that, there's also the time needed to sort the transactions lexically

Sorting on a single core happens at around 1 to 10 **million** keys (transactions) per second. For a 1 GB block on current hardware, that takes around a second. [Napkin math for total processing of a 1 GB block with good algorithms.](https://www.reddit.com/r/btc/comments/9b3827/the_gigablock_testnet_showed_that_the_software/e52xqap/) In contrast, the UTXO reading and writing on a very fast SSD would be around 30 seconds.

Edit: forgot the word "million"

> the extra effort of rebuilding most of the Merkle tree when additional transactions are added to a block candidate

That is done anyway in the current code. Also, Merkle tree rebuilding is fast. I estimated that at about 1 second for a 1 GB block on 1 core in my napkin math.

> it would make subchains slower because the subchain needs to be resorted for every new weak block.

Or you could store your transaction sequence as a tree (e.g. std::map) instead, and simply use the tree's O(n log n) sorted insertion. Sorted insertion is usually slightly faster in practice than unsorted appending followed by in-place sorting, anyway, as it touches each data element fewer times.

> Further, this just seems too rushed to me.

This is fair. But it's also fair that it was being proposed several months ago, and nobody thought to object to it then. It's kinda a case of late objection more than anything.

I wonder... Was CSW one of the first to object to it, or did he jump on after others started to question it? Who was the first person you noticed to be in opposition to the idea?
Also acceptable.

I read really fast and thought it said "cryptodirty." So are you a dirky girl? Yeah?
Go ahead and move to memo.cash. Personally, I think that the ability of Reddit to pick out insightful and detailed comments and float them to the top of the sorting order far outweighs the drawbacks of vote manipulation. But maybe I just feel that way because it's often my comments on the top.

A version of memo.cash with transparent voting would be pretty cool. I could get behind that.
That's not a fair comparison. It would be faster if implemented in Flowee than it would be in ABC, because the rest of ABC is slower than Flowee.

The fair comparison is whether ABC with parallelized OTI validation is faster than ABC with parallelized topological validation. This comparison is not likely to ever be performed, since ABC does not have a parallelized topological validation algorithm implemented yet.

Another fair comparison would be Flowee with OTI vs Flowee with topo. I've been considering implementing parallel OTI in Flowee after I finish the ABC (and maybe UL?) implementations. But I've got a lot on my plate already, so who knows if I'll ever get around to that.

Another fair comparison is serial OTI vs serial topo in ABC. I've done this comparison, and serial OTI is very very slightly slower, and by about the same amount as one would expect from the order check I had to add due to the lack of CTOR. This suggests that serial OTI with CTOR would likely be about the same or slightly faster than serial topo. I might remove the OTI algorithm's order check and replace it with a dummy lexical order comparison (but which didn't result in markiing the block invalid) as a pure performance test. But again, lots on my plate as it is.
The "minority" chain will only capture the majority of market cap if it has the majority of users behind it.

> The honorable thing to do, would be to accept the result of the hash battle

You're making this sound like a feudal society war. The honorable thing to do is to accept the judgment of trial by combat performed by your selected champions. Only the sword can determine Truth.

No.

Political power comes from the consent of the governed. You can get all of the hashrate you want in your little island nation, but if you don't have citizens living in that nation, you're not going to be able to raise any taxes to pay for your army and your Lambos. 

The true success comes from attracting users to your platform who will pay for your hashrate. If you can't present a platform that people want to use and pay for, then your hashrate means nothing.
This script is mining Monero (XMR). Monero is a currency designed to be mined best by CPUs, although in practice GPUs are a little better at it. 

A midrange desktop CPU running this javascript might get 50 hashes per second (H/s) while using an extra 80 watts of power. In comparison, the same CPU running a native compiled program might get 80 H/s, and a midrange GPU (Rx 580) might get 500 H/s while using 120 watts.

If you're getting 50 H/s on 80 W and paying $0.12/kWh for electricity, then you'd be generating about [$0.08/day in revenue](https://www.cryptocompare.com/mining/calculator/xmr?HashingPower=50&HashingUnit=H%2Fs&PowerConsumption=80&CostPerkWh=.10) while using $0.19/day in electricity, for a total loss of about -$0.11/day.

Of course, the website owner doesn't pay your electricity bill. If they have 10,000 people mining for them at any moment, that translates to about $800/day in revenue while costing their users $1,900/day in electricity. 
Ignore the trolls on twitter and the other forum. They're just trying to perform a Sybil attack to define what Bitcoin is and what it can be used for.
Jimmy Song's numbers seem pretty accurate to me.

> This article also doesn't include initial R&D, 

Around $5m. The S9 is a derivative of the Antminer S7, which is a derivative of the S5, etc. As such, most of the R&D costs were paid for by previous generations.

> employee costs

For production, labor is much less than parts, since most of the assembly is done by robots (e.g. pick-and-place machines) anyway. Last time I visited their Beijing HQ, they had around 100 people. Their website now claims they have [250 employees](https://www.bitmain.com/join_us). If their employees make an average of $25k/year -- about 4 times the Beijing average salary -- that makes about $6.25m/year in labor costs. However, a lot of those costs are accounted for elsewhere in the engineering costs for the ASIC design.

> business licenses cost

These are generally not proportional to revenue, and should range in the thousands of USD, not millions of USD.

> taxes

Electricity costs would be a deduction from taxes, so for the context of evaluating ASICBoost, pre-tax dollars (or RMB) are more appropriate than post-tax.

> Sorry but I don't think 200-250mill is an accurate estimate.

Apology accepted.
Actually, more like 3.5 transactions per second. 7 TPS would correspond to an average transaction size of 238 bytes, but the actual average is closer to 450 bytes.
> SegWit, which increases the "effective" max block size to 1.8-4 MB (the exact size depends on the distribution of transaction types).

Correction: SegWit's "effective" max block size is 1.0 MB to 4.0 MB, depending on the distribution of transaction types. 

The 1.8 MB figure comes from 100% of users switching to SegWit transactions, but with no change in the proportion of P2SH+multisig vs P2PKH transactions. It is unlikely that 100% of users will switch wallets on day 0, but it is likely that the mix of P2SH will not change much on day 0, so the effective max block size will likely be less than 1.8 MB for quite a while. I personally will be surprised if we exceed 1.2 MB in the first month.

The 4.0 MB figure comes from unpractical transactions that are designed to be nearly 100% signature/witness data and nearly 0% transaction data. The only known use for such transactions at the moment is DoS attacks.

If you're going to use the 4.0 MB figure as the upper end of SegWit's max blocksize range, then you should use 1.0 MB as the lower end. If you're going to include the capacity for theoretical attack transactions as "effective block size", then you should also include transactions from non-upgraded wallets (which could also be spam attack transactions).
I think this is the most elegant solution to the PoW problem. Dogecoin did this when its hashrate started dropping off and no longer being competitive with Litecoin, and it seems to have allowed Dogecoin to survive.

http://digiconomist.net/how-auxpow-affected-dogecoin-mining/

https://www.reddit.com/r/dogecoin/comments/2ci90m/dogecoin_to_enable_auxpow_soon_all_infos_inside/

One interesting twist that could be done on this is to apply a discount to AuxPoW blocks, such that the difficulty target for a block mined via AuxPoW is e.g. 2x the difficulty for a normal block. This would encourage miners to deprecate the legacy chain should the two chains achieve roughly equal price.
If there's one thing the fearmongers can't handle, it's **[misleading or false data](https://www.reddit.com/r/Bitcoin/comments/48awq1/is_there_a_stress_test_going_on_right_now/d0inndc)**.


I have made this point to them before.

http://toom.im/files/Jonathan_Toomim_BIP101_propagation_on_testnet_extended2.pdf

Slide 27 +/- 1. I gave this extended talk at Bitmain HQ. I have made similar points in person with other miners. 

Miners should to be hiring their own developers to watch out for their interests.

Edit: It's worth noting that Lightning is not part of Blockstream's business model, just their strategy. Sidechains are their business model, as far as I can tell.
> if you ignore all the centralizing effects, 

Centralizing effects of hard forks? I don't follow. Can you elaborate?

> or the people unknowingly left behind on a low security chain.

I think it would be better described as an obsolete chain, not a low-security chain. 

Note that bitcoin-qt and other clients send alerts to the UI if no new blocks have been seen in some period of time to warn users to check their internet connections. In order for someone to be unknowingly left behind, they would need to ignore those UI messages as well as all the news articles and Alert System messages sent over the last month or more by devs urging everyone to upgrade. In order to be vulnerable, users would need to ignore those warnings and continue to transact with significant amounts of volume at the same time. I find it difficult to feel sympathy for an active Bitcoin business or user that pays so little attention to urgent warnings coming at them from multiple sources.
BIP101 was already tested pretty extensively in regtest mode by Gavin. It was not tested in testnet because testnet is actually a pretty difficult place to test things, despite the name. Nodes on testnet are poorly maintaned, and some of the rules (e.g. resetting the mining difficulty after 20 minutes) are kinda nasty. It will be chaotic, messy, and fun.
Compact Blocks gets about 50 bits/tx or 98.5% on > 1k tx blocks. Xthin is slightly worse. Xthinner is a bit over 3x better than CB, and about 4x better than Xthin. On larger blocksizes, Xthinner may improve to to 4x better than CB and 5-8x better than Xthin. I haven't done substantial tests on CB or Xthin, though, so take those numbers with salt grains.
Because every redditor sucks, apparently.
That might be a win-win scenario.
> The amount of bits or bytes per transaction has to vary as blocks get bigger

[No, it doesn't.](https://old.reddit.com/r/btc/comments/bas60b/by_the_power_of_ctor_xthinner_is_now_working_with/ekf3f1d/) That's exactly the benefit that CTOR brings.

> Anyways, the big problem with xthinner is that decoding has to be single threaded

**No, it doesn't.** Xthinner's code already splits the block into separate segments, each of which can be encoded and decoded independently and in parallel. There is about 20 bytes of overhead for each segment. As long as segments are longer than 128 transactions or so, this overhead is minimal. This was a major design goal of Xthinner, as I intend to use this segmentation for Blocktorrent.

I tested some multithreaded implementations during development. Unfortunately, the current mempool code does not support concurrency or readers-writers locks, so in order to get it to work I had to copy the mempool into a vector, and then use the vector for encoding and decoding. Strangely, this was slightly faster even for the single-threaded case, but the difference was minimal and the increased attack surface made me choose to scrap that approach.

> When a missing transaction is found, a round trip has to be made before the rest of the transaction can be decoded....  If there are 15 missing transactions, 15 round trips must be made.

**No, it doesn't.** When a missing transaction is found, the index of the missing transaction is appended to a vector and queued for later recovery from the block sender. The decoder then continues to decode the rest of the block, and all missing transactions from a single segment are batched and requested at the same time. The stack state machine does not require any data from the missing transaction in order to be able to recognize later transactions in the block. 

> Whats 4.5 minus 2.0? The answer is 2.5. CTOR saves 2.5 bytes per transaction. Two measly bytes. Considering the cost of splitting the network, CTOR was not worth saving two bytes of bandwidth per transaction.

That's a reasonable point. But it should be division, not subtraction, and Xthinner is expected to average about 1.75 bytes, not 2.0. Xthinner saves 61% compared to Superthin, or can handle 2.57x as many transactions with the same bandwidth•latency. If you think a 2.5x factor is not worth forking over, then Segwit and Bitcoin Classic were also not worth forking over.

> Also, you can decode an entire superthin block in one go. If you're missing 15 transactions, you'll know this milliseconds after receiving the block...

Same with Xthinner.
First, thank you for distinguishing between checkpoints and finalization/reorg protection. They are distinct things with significantly different effects and sources of control (devs vs miners), and I find it unfortunate that people often conflate them.

Second, I think it's worth distinguishing between the algorithm that miners and pools use to determine which chain to mine on top of and the algorithm that end users use. If miners choose that they want to not contribute their hashpower to any deep reorg attempts, and always build on the honest chain until a human manually intervenes, I think Satoshi would be okay with that. It's when non-mining nodes flatly reject the longest chain because it was delayed that reaching consensus becomes difficult.

From the miner perspective, anti-reorg protection is a gamble that the attacker can not sustain as much hashrate as the defenders. In the case of the nChain/Coingeek reorg threats of November, I think this was a reasonable position -- the BCH defenders showed a peak of 9 EH/s capacity, and the potential attackers showed a peak of 4 EH/s, but the economic equilibrium was for about 2 EH/s. Should the attackers have used 4 EH/s to reorg BCH, the defenders could have responded by ramping up their hashrate to overtake the attackers, but only if they didn't automatically switch their hashrate over to join the attack as soon as the attacking chain was published. Miners who choose to fight reorg attempts are not necessarily doing so out of altruism: any reorg attempt will necessarily eliminate some otherwise-legitimate revenue that the honest miners would have made, and fighting to defend the first-seen chain also defends the block rewards that they mined which the attacker is trying to orphan.

If the majority of miners are running reorg protection but the non-mining users aren't, the users are still protected. So if you feel uncomfortable with bypassing the longest-chain rule, feel free to disable reorg protection with `-maxreorgdepth=-1`. There's not really any significant risk to you for doing so. The worst that should happen is you might switch back and forth between branches a few times should a reorg attack be attempted. Eventually, though, you should converge on the same chain that the majority of other people are following, because that's the chain that will have the recognized economic value needed to sustain mining over the long term.
No it didn't. The record is 12,239 transactions in a block, set back in 2015:

https://blockchair.com/bitcoin/block/367853

These transactions were not economically useful. They were "anyone can pay" transactions that were 62 bytes each.
I don't think maximalism is a very common viewpoint in this subreddit. Maximalists believe there should be only one coin. Many people are pluralists, and think that multiple coins can exist simultaneously to fill different niches, and that BCH should simply fill the role of mainstream digital cash. Privacy-focused coins like Zcash and Monero will still have some niches, flexibility-focused platforms like Ethereum definitely will, and small-block coins like BTC might too. These alternate systems will likely only be used for specific applications in which their advantages are important, though.
Feature creep, basically. Their goals for making Casper scalable and attack-proof grew over time. As the goals got more complex, the implementation schedule got pushed back.

One of the biggest changes is that they're now planning on including sharding capability in the Casper design. They've also been changing their definitions and targets for finality.

https://medium.com/futuresin/ethereum-casper-update-vitalik-buterins-tweet-storm-2f916dceb72b
The minimum fee is 0 satoshi. A 1 satoshi per tx fee is also possible. The 1 sat/byte figure is the market price, not the technical minimum possible price. You can send a transaction that pays 0.5 sat/byte if you want, but it won't get mined very fast.
This change is a policy change, not a "consensus rule" (in developer-speak), even though the change affects how nodes reach consensus. Only "consensus rules" need to be coordinated forks. Consequently, this change can be added today by a minority of users and miners with no ill effects, and with no effects at all unless there's a deep/hidden reorg attempt.

If a majority of miners follow this rule, it will behave somewhat similarly to a soft fork, but with less absolutism and more flexibility/adaptability.

By the way, thezerg1's account was hacked/stolen a while ago and is apparently unrecoverable, so you probably shouldn't tag it any longer.
Yes.
Thanks for raising that, I forgot to mention it in the OP. They follow the standard most-work rule during IBD.
Nitpick 1: If each chain has 50% of the hashrate, then 10 blocks on each chain will be at least 200 minutes. That's 3h20m. Until *both* chains hit the 10 block mark, automatic reorgs are possible. If the hashrate split is not 50/50, then reorgs will be possible for even longer. Edit: actually, 11 blocks, 3h40m.

Nitpick 2: They can't re-org back *without manual intervention*.
> If bad miners mine 10 blocks, their new whatever becomes the norm

You can't change the rules of Bitcoin just by mining a block. The worst thing that bad miners can do is refuse to accept certain transactions for 10 blocks. 

10 blocks from bad miners would be an annoyance, and 125 BCH in their pockets, but nothing more.
Bitcoin SV's network has been experiencing some severe performance problems recently. I suspect that the hashrate dip is due to loss of effective hashrate from poolservers being overloaded and blocks getting orphaned, rather than from an actual redirection of mining machines.
Yes, there's a practical reason: They will get caught if they try to selfishly mine, and they will get smacked down.

BSV cannot selfishly mine against ABC unless BSV abandons working on their own chain to mine on ABC. Since BSV has far less hashrate than ABC does, BSV is not likely to be able to succeed in a selfish mining attack against ABC unless they are technically much more competent. Given that their fearless leader claims that selfish mining is impossible, I do not believe it likely that they would have the technical skill nor political capital to be able to pull off a selfish mining attack.

"Hey, you know how I wrote a paper claiming that selfish mining was impossible? Well, guess what: we just proved me wrong!"

The irony of that would be pretty delicious, but somehow I don't think that the ABC+BU team would let that happen just for the lulz.
It's actually closer to being the other way around. ABCH is worth about $300 or $400 right now, and BSV is worth about $80, so each hash on ABCH has much higher real revenue than on BSV.

The ABC+BU miners are sacrificing some revenue by not mining BTC right now, but not nearly as much as BSV is. Furthermore, the ABC miners could be mining with slightly more hashrate than BSV has while still making the same amount as they would get on BSV, whereas BSV would be getting about 25-30% as much on BSV.

Ultimately, the amount of hashrate on both chains is excessive right now. This is clearly just a pissing contest. But urination is often used by mammals to mark their territory, so even that makes sense.
> They are pushing their roadmap without having consensus

There was consensus behind the ABC roadmap in December 2017. Even nChain supported it then. That consensus was only removed in August 2018, after the feature freeze for the Nov 2018 hard fork happened.
Yes, they can attack the chain that follows your ruleset, and they can deny you access to your chain. However, they cannot force you to follow their ruleset.

Bitcoin SV is currently trying to convince users to switch rulesets by threatening to make the ABC 0.18 ruleset unusable. This is unfortunate, but if users hold steady and refuse to cave in to SV, they will deplete their funds eventually. It costs users nothing to reject SV's ruleset, but it costs SV about $9k per hour to deny ABC's chain the ability to function.
Actually, Mike Hearn suggested the Bitcoin Core name, not Gavin.
I downvoted this post because CSW does not contribute positively to the Bitcoin Cash community and I don't like seeing his tweets be given extra publicity on reddit.
No, ABC is making changes to its codebase faster than that. SV might be 99% identical to ABC 0.17.2, but ABC 0.18.2 added 274 commits on top of that, and the current ABC master branch is 193 commits ahead of 0.18.2. Bitcoin SV so far has only added 55 commits on top of 0.17.2, and most of those were cosmetic or non-functional (e.g. rebranding).
Maybe because it's competition to the "turing complete" smart contract platform that they want to build on top of BCH?
> you know as well as I do there are many variations that could disprove your assumptions.

I do not know what you're referring to. If you have a more efficient algorithm for calculating Gavin's order, I'd be happy to take a look at it.

> Build it test it and lets see before committing to irreversible consensus rule changes.

I didn't realize that Bitcoin Cash users were so aggressively opposed to hard forks. I thought we had grown past that.

I do not think it's a good rule to require developers to implement and benchmark every suboptimal algorithm just to show that those algorithms are indeed suboptimal. If we followed that rule, then we'd never get anything done.

If you think Gavin's order will perform equally well or better, I encourage you to implement it yourself and benchmark it. As for myself, I'm not going to waste my time on code that I expect will be a dead end when I have a half dozen ideas that I'm quite certain would not be a dead end that I could be working on instead.
Currently, it is not practically feasible to make a 32 MB block, due to the limitations of AcceptToMemoryPool and the INV relay limit.

However, if it were feasible, it would cause problems. We saw a block get orphaned during the stress test because ViaBTC made a 6 MB block that was slow to propagate. BTC.TOP mined a 51 kB block (0000000000000000002d5be4e56f448a247088ee91e31f788d9a6ad5411f71b6) on top of the 6 MB block's parent, causing an orphan race. The 51 kB block out-propagated the 6 MB block to all of the 10 nodes for which I have log files. It seemed like it should win the orphan race. However, ViaBTC had enough hashrate that they were able to mine a second block (1.3 MB) on top of their 6 MB block, which was enough to lock it in and orphan the smaller block.

A pool or miner will never orphan their own block. Pools that have a large portion of the total network hashrate do not suffer from orphan rates as much as pools with a small portion of the total network hashrate. This means that as block sizes increase, there is an incentive for miners to form larger and larger pools. Eventually, this could create 1 or 2 megapools with enough hashrate that they (or [a disgruntled employee](https://www.reddit.com/r/Bitcoin/comments/1qqmr4/ghashiocexio_and_doublespending_against_betcoin/), or someone who hacks their servers) could 51% attack Bitcoin. I think this is undesirable.

At 6 MB, this kind of event is rare enough that it does not compromise the decentralization of Bitcoin. However, at 32 MB, there would be approximately a 1% overall revenue advantage per hash for a pool with 35% of the network hashrate versus one with only 5%. I consider that to be the limit of what is tolerable for this centralization incentive, so I think we should not allow blocks to get larger than 32 MB *(edit)* until we have improved block propagation performance.
Huh? Citation needed. That doesn't sound like him at all.
It's been September 1st for almost 12 hours already in UTC.
There's a [unit test](https://github.com/bitcoin-sv/bitcoin-sv/commit/173bb0efca649fb57c9640507b7861bbc05e8bed) to ensure that the default 32 MB limit can be overridden to mine allow and mine 128 MB blocks. This unit test does not add any new functionality, though. It just tests functionality that was already in Bitcoin ABC 0.17.2.
Actually, hashrate is a little bit out of equilibrium with price. More miners are mining BCH than is economically optimal. Currently, it's about 28% more profitable to mine BTC than BCH. Over the last 7 days, the difficulty-adjusted reward index (DARI) has averaged 17% in favor of BTC.

https://fork.lol/
Yes. That was absurd, and he should totally be made fun of for that.

However, thinking that Lightning will reduce fees is not absurd, though it may be wrong.
This is why PoW functions designed for CPU mining are dangerous. With ASIC and (to a lesser extent) GPU mining, the miners are invested in the long-term survival of the currency. With CPU mining, the miners are frequently botnets.
So we're worrying about time-traveling 51% attacks now?

I mean, what you're saying is technically correct, but I fail to see how it is a concern.
It is strange that you're alleging that Bitmain is favoring Bitcoin Unlimited buyers when both of Bitmain's own pools, Antpool and BTC.com, are mining with Bitcoin Core.
> At a cost of higher inflation.

Paid uncles do not need to come at the cost of higher inflation, that was just the decision made by the Ethereum developers based on their own economic preferences. Paid uncles could be done on Bitcoin by reducing the block reward for later blocks. For example, for each 1 btc in uncle payments, each of the following 10 blocks could have their block reward reduced by 0.1 btc.

>> 14 second block times

> At a cost of higher inflation and can't use compact block.

No, neither of those two points are correct. 

Ethereum's inflationary economic model is unrelated to the chosen block time. 

Ethereum is technically capable of using compact/Xthin block propagation methods, but since most blocks are currently only about 1 kB in size, it hasn't been a priority to develop it. As Ethereum transactions are fundamentally smaller than Bitcoin transactions (about 125 bytes each vs 450 bytes average), block propagation is less of a concern.

Ultimately, Casper and PoS will completely solve the orphan cost from block data propagation, so it's not clear that this will ever be relevant.

> Except letting miners set a gas limit is not incentive compatible. 

The perverse incentive regarding block size has a very low magnitude in Bitcoin. In Ethereum, it's even smaller, as selfish mining is 1/8th as useful with Ethereum as it is with Bitcoin due to the existence of the GHOST/paid uncle mechanism. 

It also won't be relevant after the switch to proof of stake, since orphan rates will fall nearly to zero when block creation is no longer a Poisson process and instead happens at regular intervals that are significantly greater than the block propagation and validation latency.

> Unlike UTXO, account-based transaction model verification can't be parallelized.

It can be parallelized insofar that one account isn't touched by two transactions. The probability of that happening for any random pair of transactions is small. The question parallelism isn't whether any lock conflicts will exist, but whether enough parallelism exists to keep your cores and SSD fully utilized. For real-world data, the lock conflict on a single account would likely reduce available parallelism by less than 1%.

Speaking of SSDs, it is unlikely that CPUs will be the bottleneck. Parallelism doesn't really matter when you only have one disk database. The usage of accounts versus UTXOs makes database caching much more effective in the common case of address reuse.

The main efficiency improvement of UTXOs is that you simply don't need a change output in your transactions, and you never need to deal with aggregating multiple previous change outputs. At steady-state, Bitcoin transactions using the standard wallet model will have 2 inputs and 2 outputs, whereas it's only 1 input and 1 output with Ethereum. That's a 50% (or 100%) difference, which is much larger than the effect of parallelism would have.

>>Perverse incentives
> The only correct part of the post, unfortunately but seeing that currently Ethereum's blockchain is much larger than Bitcoin it is unfortunately not so true. At least its effectiveness is questionable.

The Ethereum community has a different philosophy to Bitcoin about blockchain size. Ethereum does not value keeping the blockchain small very highly. For example, the transaction fee is only 0.2¢, which is about 1/10th what it was for Bitcoin even a few years ago when Bitcoin blocks weren't close to being full.

The concept of UTXO bloat and dust also just doesn't apply to Ethereum very well because of the account model, by the way. A series of very small payments to a single address will be unspendable dust in Bitcoin, but not Ethereum. 

Also, the Ethereum blockchain is a Turing-complete digital computer system, whereas Bitcoin is at best a conditional payment platform. The larger state database and blockchain size is largely the result of being used for its qualitatively greater capabilities than Bitcoin. The use of Ethereum as a simple payment platform currently accounts for relatively little of the database size.
You don't make the transaction 92.5% witness data by making the non-witness section smaller. You do it by making the witness section larger.

There is an absolute minimum to the amount of non-witness data that a transaction can have. Typical transactions (2 input, 2 output) have about 2x as much non-witness data as the absolute minimum, as transactions with the absolute minimum (1 input, 1 output) just aren't very useful.

On the other hand, there's no absolute maximum on the amount of witness data that a transaction can have, except for the blocksize limit or the 100 kB per-transaction soft cap. So these 3.7 MB SegWit blocks just bloat a small number of 1-in 1-out transactions with a huge amount (~11.6 kB) of witness data.
WA state is already a center for bitcoin mining. 

There are at least 6 MW of Bitcoin mines in my town in central WA. (At least 4 big mines. There may be a few more small ones that I don't know about.) My town has a population of about 20,000. That gives a per capita hashrate of around 1 TH/s per person. The worldwide average is 0.0002 TH/s per person.

Note that MGT Capital/McAfee's mine is [2.6 to 5.2 PH](http://www.mgtci.com/datastreamx/d)/s, which is about 0.26 to 0.52 MW of capacity. That's about 1/10th of what my town has.
You're thinking of the DNS seeds for the p2p network, not the relay network.
Spam in your mempool and spam in blocks are very different things. 

All miners and pools now have set minrelaytxfee=0.0001 or higher, and that prevents all of this spam from being included in blocks. The spam attacker from October has been rebroadcasting these transactions twice a day since October, so the spam you're seeing is really probably a 1-day-old rebroadcast of a valid transaction that was first seen in October and which will never be confirmed.
If you have spare bandwidth (which you should if you're running on VPSs), then you should set the maxconnections to something higher than 15. I recommend leaving it at the default (125). 

If your maxconnections is 15, then you're adding to the incoming connection debt. Bitcoin nodes spawn 8 outgoing connections. In an ideal world, all nodes would have port 8333 open, which means that those 8 outgoing connections would turn into an average of 8 incoming connections on other nodes, for a total of 16 connections. 

However, our world is not ideal, and about half or 2/3 of our nodes are behind firewalls. This means that each node with an open port 8333 should be willing to accept 2x or 3x as many incoming connections as it makes outgoing in order to keep the network healthy.

A limit of 15 would entail 8 outgoing and 7 incoming, which makes each node configured this way increase the incoming connection debt by 1 connection.

Please do not limit your total connections unless you can't afford more than 300 GB of traffic per month.
The point of bitcoinclassic.consider.it is to gather information about what users of Bitcoin and Bitcoin Classic actually want in a Bitcoin client. There's not really much point in the Classic developers falsifying that data. If we write code that isn't what people want to run, then people won't run it.

If people think that a fun way to spend their time is by defacing the tool that we use, then we will have to squelch unverified users.

If people want to continue and do a similar attack using Mechanical Turk, then we'll have to add a requirement in the verification process to link to a forum, social media, or reddit account with a post about Bitcoin that is at least 2 months old, or something like that. 

In any case, consider.it is designed to be auditable. I think Travis will be working on rolling out interfaces to the data to make independent audits easier. As it is, most consider.it users have voluntarily included cross-referenceable data in their account names, so if you suspect manipulation, you can take a random sample of the opiners on any particular issue, track them down, and ask if their opinion on the website has been manipulated.  
Just to clarify: you're calling us (the Bitcoin Classic folks) snakes, and Theymos a more trustworthy person, correct?
> So, your fork will start at 2 MB and then double once every 2 years for 20 years, is that correct?
> 

No. It will double between 0 and 2 times. We haven't finalized the parameters yet, but we will not be exceeding 8 MB as the endpoint for now, as per the wishes of miners.

We intend to do subsequent forks (of the blockchain, not the code) to increase that further whenever there is a majority of users and miners in support. Right now, we seem to have consensus about a short-term can kick option, so that's what we're implementing.

> What will your fork do about the Full RBF situation? Full RBF kills 0-conf tx's.

The current contributors to this project do not like RBF. We will try to prevent it from becoming a reality, as long as that's what our democratic overlords tell us to do. If our overlords tell us to implement RBF, we will do it, though I bet some of us volunteers would drag our feet a little.

> Will your fork also propagate the first and only the first double spend attempt like Bitcoin XT?

That strategy was designed before mempool eviction became a thing. When evictions are common, it has a tendency to backfire. The strategy will need to be rethought and redesigned. We may advocate a new message format for transaction packages (which would also be needed for a proper implementation of child-pays-for-parent), and send a package with the original transaction plus the alleged replacement.

> Will your fork also drop random mempool tx's when the mempool gets very large like Bitcoin XT? I think the Bitcoin Core fork drops the tx's with the smallest fee in that case, not a random tx.

I can't say. That policy has too much controversy surrounding it for me to predict how users and miners will vote on it. If you have an opinion, you can put it on the consider.it page that we create for the topic. If you're in the majority, we'll follow it.

> Will your fork re-introduce the 50 KB fee-free section of the block like it was before the Bitcoin Core fork removed that section?

Probably, yes, as it seems to be popular among users. It will probably have a different form than the 50 kB reserved section, but will serve the same purpose. I've **[proposed](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-October/011622.html)** normalizing and combining fee and bitcoin-days-destroyed into a single metric, which should have this effect while keeping the code pretty simple.
> In Satoshi's vision there was no such thing as developers setting the capacity, or a 'safe' capacity.

Yeah, I don't like that either. In all fairness, though, Satoshi was the one who implemented the 1 MB cap. He wanted to [increase it](https://bitcointalk.org/index.php?topic=1347.msg15366#msg15366) eventually. Like maybe 270,000 blocks ago. We're interested in a permanent solution like a flexcap, BU-style stuff, or BitPay's branch, but none of those ideas have consensus yet among both miners and users. I think a 2 MB hard fork in the next few months has consensus, so that's what we're coding now. A more permanent solution can come later.

> In Satoshi's vision, the transaction fee should be sufficient to pay for the marginal cost of adding the transaction to a block. Every transaction that pays such a fee should be processed in the next block, without any artificial capacity limit.

That's the general idea that we have for Bitcoin Classic, yes.
> Is that fork based on a miner super majority or is it a forced fork? 

I'm not sure what you mean by "forced fork". Perhaps you mean a fork based on a flag day that's hard-coded into the implementation?

Yes, we want the fork to activate only in the case of a miner supermajority.

> If the former, how do you plan to convince the miners?

By talking with them (and users), and letting them tell us what they want, and then writing code that gives them what they want. (The astute observer may notice that I have been making progress on all three points.)
User support for the different proposals can be seen here: http://bitcoin.consider.it

Miner support can be seen here: https://docs.google.com/spreadsheets/d/1Cg9Qo9Vl5PdJYD4EiHnIGMV3G48pWmcWI3NFoKKfIzU/edit#gid=0

I'm working on a project that I hope will be acceptable to both.
> The size of of a Seg-Wit transaction is counted as 25% hence resulting in just under 4x the 1MB hard limit. Non witness data is counted the same as usual at 100% of it's size.

No, the size of the *witness portion* of a SegWit transaction is counted 25%. A SegWit transaction can be split into two parts: the transaction data (i.e. where the coins come from, how many coins, where they go to), and the witness data (the signatures and scripts you use to prove that you're allowed to spend the coins). It's only the second half of the transaction, the witness data, that gets the 75% discount. This means that transactions that have a lot of signatures (e.g. large multisig) benefit much more than typical transactions. 

> Deploy segregated witness soft-fork April 2016 (tentative) resulting in a maximum blocksize just under 4MB if only segregated witness is used for transactions.... Reaching 4MB is unlikely as blocks are a mixture of both seg-wit and non seg-wit transactions.

Segregated witness is nearly 4 MB if only 15-of-15 multisig segwit transactions are used. If only segregated witness p2pkh transactions are used, the limit is 1.6 MB. if 100% 2-of-2 multisig with SW, then 2 MB. If a mix of SW transactions that resembles the current mix in terms of the amount of multisig is used, then 1.75 MB. If the current mix is used but only 50% are SW, then 1.375 MB.

> Seg-Wit reduces transaction size by stripping signature data from the transactions.

SegWit does not reduce the size of transactions when they're sent over the network, during processing, or during initial storage. It allows for the witness data of transactions to be pruned once it is no longer needed, say, a few weeks later.
> Can you tell me why miners would say BIP101 is too fast when it doesn't force anyone to mine that big blocks? It sounds really counter intuitive that miners would give up their freedom to mine bigger blocks.

It forces them to accept and process those blocks in a timely fashion should another miner create them.

> Clearly all miners are very conservative and risk averse. Which means that they should not need a blocksize limit at all(!)

I don't follow your logic. I think the issue may be that miners want to be protected from other miners who may not be so conservative in their created block sizes.
> Great post. I wished you asked more pools and asked for more questions

So do I. However, "perfect" is the enemy of the good. Here is the data that I have now. If I get better data later, I'll release that.

> But it seems like a lot of people here think that miners are the most powerful entity in here. ... Here is a pool that thought they had more power then their users

I think your argument shows that miners are more important than pools, not that users are more important than miners.

I tried talking to several miners who use pools (both in Shenzhen at a meetup organized by the Lightning ASIC CEO, and via email with a few others), and I could not get an opinion out of them. A lot of miners, especially in China, just don't care about anything that doesn't directly and clearly affect their profit margins right now. Most of them trust their pools to make decisions like these for them.

On the other hand, the pools that I talked to all had clear and nuanced opinions on the blocksize debate. As a result, I decided to poll the pools and ignore the miners behind them.
Yes. /u/peoplma's answer is correct. Difficulty on testnet3 is really funny because of timewarping, a method for creating difficulty-1 blocks by fiddling around with your system clock. 

(On testnet, you are allowed to produce a difficulty 1 block if the block's timestamp is 20 minutes ahead of the previous block. After that block, the difficulty returns to the previous block's. An exploit happens if you create two diff-1 blocks in a row; then you can jog your clock forward and backward by 20 minutes indefinitely and create as many diff-1 blocks as you want. This is a trick that people use to reset the difficulty on testnet3, if I understand it all correctly.)
    2015-11-08 13:31:47 ConnectBlock: Max block size fork activating at time 1446996660, bigger blocks allowed at time 1447083060

Activation was at:

GMT: 2015-11-08 15:31:00 GMT

PST: 2015-11-08, 07:31:00 GMT-8:00


Bigger blocks will be allowed at:

GMT: 2015-11-09 15:31:00 GMT

PST: 2015-11-09, 07:31:00 GMT-8:00


This block was mined at the Toomim Bros Bitcoin Mining Concern's XT p2pool node.

http://toom.im

Universal heat death.
If you knew which chain your counterparty was watching, you might be able to broadcast and commit an old channel state on the other chain without getting caught. This is unlikely to succeed without replay protection, though, and replay protection would break LN channels entirely.
Does BU+Graphene have the ability yet to forward blocks after PoW+Merkle Root has been verified, but before all transactions have been verified and the block has been connected to the chain? If not, you should get that done. This feature is very important, as it doesn't matter if you only take 200 ms to send a block one hop if each node delays on forwarding it for another 10,000 ms while verifying its validity.

(Xthinner is currently still missing this feature. It's the last thing I want to get done before I make an alpha release.)
Many wallets allow you to create watching-only wallets. These wallets have the public key for an address but not the private key. Electrum and its derivatives have this feature, as does Bitcoin Core and its derivatives.
> Xthinner does 1.5-2b/tx at the expense of a higher collision probability.

Xthinner does not have significantly higher collision probability. Xthinner can handle blocks and mempools with millions or billions of transactions without any collisions. OTOH, Compact Blocks has a ~50% collision probability at 16 million tx. 

The only collisions that can happen with Xthinner are when there's mempool desynchrony, and when that happens you need to do a round trip to fetch the missing transaction anyway.
Isn't minisketch O(n^2) for CPU time? That isn't going to work for 1 GB blocks, and probably won't work for 32 MB blocks.
OP_CDSV, CTOR, alpha version of UTXO commitments (bchd), better OP_RETURN, Schnorr signatures, fixing the O(n^(2)) sighash bug for all transaction types (BTC only fixed it for Segwit), improved DAA, SLP, CashShuffle, Graphene (Bitcoin Unlimited), multithreaded transaction validation (Bitcoin Unlimited), multiple independent and compatible full node implementations (ABC, BU, bchd, bitprim, Flowee, XT, Copernicus, Parity, Bitcoin Verde, Bcash, gocoin-cash)...

https://cash.coin.dance/development
Jonathan Silverblood's membership was accepted.

https://bitco.in/forum/threads/voting-is-closed-for-buips-107-109-110-112.23340/#post-87608
Yes, doing a million transactions per second on a single core is infeasible. We can't scale to 1 TB blocks on a single core. No surprise there. 

Fortunately, sorting is parallelizable, and so is everything else in CTOR and Xthinner. If we're doing 1 TB blocks, the performance-critical code will probably have long since been rewritten to run on GPUs, and then sorting the TXIDs for 1 TB worth of transactions (or 2.5 billion TXIDs) would take on the order of [2.86 seconds](https://devtalk.nvidia.com/default/topic/951795/fastest-sorting-algorithm-on-gpu-currently/), and generating the Merkle root would take about 7.5 billion SHA256 rounds, which would take [about 3 seconds](https://gist.github.com/epixoip/a83d38f412b4737e99bbef804a270c40) on a single GTX 1080. 

Of course, transmitting that 1 TB of transactions in 600 seconds would require 13 Gbit/s of traffic per peer even for non-mining full nodes, which is going to be rather more expensive than a 1080, so one might suggest that the computational requirements for this scenario are inconsequential compared to the bandwidth and storage requirements.

Note: sorting can also be done at the time the transactions are added to mempool. It's pretty easy to use a tree (e.g. std::map or boost::multi_index) instead of a hashtable (std::unordered_map) for the mempool, and if you do that, you don't need to do any sorting in getblocktemplate at all. Random inserts into a std::map take about 600 ns, which is about the same amount of time as checking two UTXOs that are cached in RAM (i.e. two hashtable reads).
I got sick of the parochial tribal warfare of cryptoland, how everybody seems to be focusing on how this coin is better than that coin and how they can improve their own position by bashing the competition, and felt like one of the reasons why I was working on Xthinner in the first place was to participate in this competition and to help my team win against the enemy team. I didn't like that part of me, so I took a few steps back to prune off the motivations I did not like.

I still think Xthinner is cool tech, and has a slightly positive expected value for its impact on people's lives, so I'll probably continue to work on it on occasion until it's done. But given current block sizes, the most likely outcome is that it won't make one whit of a difference for humanity, and I think it's unlikely to be as beneficial as my efforts in [hemoencephalography](https://en.wikipedia.org/wiki/Hemoencephalography), [creatine](http://jtoomim.org/creatine_intelligence.pdf), or reducing climate change could be, so I'm trying to make crypto a hobby and a tool instead of a profession and an all-consuming passion.
> Will see which method is right over time.

Not necessarily. Maybe they all fail for the wrong reasons and come up with scapegoats:

BCH: "We would have succeeded if only we had more users on our platform instead of BTC, and if BSV hadn't fractured our community and turned it into a toxic mess."

BSV: "We would have succeeded if only BCH hadn't cheated and we were able to crush that shitcoin and force users onto our stable platform instead of BCH."

BTC: Maybe "we would have succeeded if only scammers like Roger Ver hadn't diluted our branding."
The command-line version (reorgsim.py) will give the probability of attack success, orphan rate, and some other statistics for a given parameter. However, that (a) is too computationally intensive to build into a server-side live web tool, and (b) does not yet have the ability to sweep over the parameter space in any meaningful way. To do list item.

The web thing is mostly to help people get a feel for how the algorithm works and runs. It's not good to have people accept a change to a system without understanding it, in my opinion.
https://github.com/bitcoincashorg/bitcoincash.org/blob/3d86e3f6a8726ebbe2076a96e3d58d9d6e18b0f4/spec/20190515-may-upgrade.md

https://github.com/bitcoincashorg/bitcoincash.org/pull/143

https://github.com/bitcoincashorg/bitcoincash.org/pull/143/files
Probably asleep or otherwise taking a bit of time off. It's 1 AM on a Friday night in France.
Innocent accidental delays are treated the same as malicious delays. This algorithm encourages pools to not make mistakes that cause their block propagation to be delayed, and it encourages other pools to not build on blocks that make performance mistakes if they're aware of other blocks that did not make the same performance mistakes.

If a network partition event happens, this algorithm will *eventually* reach consensus, but (by design) not for a very long time. Network partition events are not algorithmically discernible from secret mining reorg attack attempts, so this code punishes both equally. If a node operator has outside information (e.g. news articles) indicating that the loss of block propagation was accidental, then they will likely want to manually intervene to accelerate the consensus-reaching process.

The LevelDB bug was a consensus-level disagreement. Certain blocks were marked *invalid*, and could never be adopted by old nodes no matter how much PoW they had. The only manual intervention possible to sort that out would be to get everyone to upgrade to bitcoin-qt 0.8 or to get everybody to downgrade to bitcoin-qt 0.7. This is a policy-level issue, so it's possible to get people to agree just by having the longer chain get long enough.

The amount of time needed for eventual consensus can be tweaked with my algorithm by changing the exponent on the height penalty divisor. Currently, that constant is 2 (squaring) in my code. Lower values reach consensus faster. A value of 1.5 might be worth trying out.
> the chain mined by the partition with the majority hash rate won't necessarily be accepted by the partition with the minority hash rate

Not immediately, but eventually there should be a switch.

In case of accidental partitions, switching can also be accelerated manually with bitcoin-cli invalidateblock or bitcoin-cli parkblock.
I had heard some backchannel mentions of it, but I hadn't seen the code or known exactly how it would work until it was released publicly.

> automatic checkpoint change

Note: it's finalization, not checkpoints. Checkpoints are where a developer types the hash of a specific block into the source code. Using the term checkpoints to describe finalization is inaccurate, and appears to be resulting in a lot of confusion and FUD about developer centralization.


> Do you feel this automatic checkpoint change should be revisited? I'm not talking about getting rid of it right now, especially with calvin and craig's threats, but maybe later or replacing it with a less vulnerable version.

Absolutely. I don't think anyone at ABC thinks that something that they whipped up in a week or two in a wartime situation has to be used forever as-is. 

Currently, I'm trying to think of ways in which time-first-seen can be incorporated into the weighting metric. I'm thinking something like this:

The time-first-received for each block in each potential chain is compared to its nth cousin (i.e. the block in the other chain with equivalent PoW). The difference in time-first-seen is divided by a constant (e.g. around 600 seconds), and the resulting value is divided by sqrt(height_since_fork) and is per_block_penalty. The per_block_penalty for each block in the chain (past the fork) is summed, and that gives the total penalty for that chain. The PoW for each chain is divided by the summed penalty, and those numbers are compared to determine the best chain. In pseudo-code:

    punishment_time_constant = 600. # seconds
    bestchain = None
    for chain in chains:
      chain.penalty = 1.
      for block in chain:
        ts = find_best_timestamp(block.PoW, chains)
        delay = block.timestamp - ts # cannot be less than 0
        block.penalty = delay / punishment_time_constant
        chain.penalty += chain.penalty / (block.height() - chain.fork_height())
      chain.score = chain.PoW_since(chain.fork_height()) / chain.penalty
      if (bestchain == None || chain.score > bestchain.score):
        bestchain = chain

This would penalize chains that had blocks which were mined or propagated after the miner should have known better, and would more heavily penalize chains in which the original block of the fork was delayed. However, over the long term, each chain's penalty would tend to be dominated by the later blocks because they were more numerous.

With this algorithm, I don't think it would be feasible to do any sort of secret reorg mining, as the penalties would rack up far too quickly. Withholding the initial block for 10 minutes gives a 2x penalty compared to the other chain; withholding it for 100 minutes gives an 11x penalty. It would add vulnerabilities in cases of network partitioning and outcast-attack variants, but I think with honest miners the algorithm guarantees eventual blockchain consistency without manual intervention.

Edit: Looks like JFk has a [similar proposal up](https://medium.com/@jonaldfyookball/on-solving-the-51-attack-problem-in-bitcoin-part-2-42d4de930007), except he subtracts the penalty instead of dividing.

Edit 2: I coded up a simple sim for this algorithm. It's [here](https://github.com/jtoomim/antiReorgSim/blob/master/reorgsim.py). Preliminary results are promising. Feel free to download it and run it a bunch of times and tell me what you all think.

Edit 3: There's a dedicated thread for this topic now, along with a more detailed description of the algorithm.

https://www.reddit.com/r/btc/comments/9zrmtx/a_better_antireorg_algorithm_using_firstseen/
Not for crypto mines. They're much cheaper in capital costs than traditional datacenters, if done right. 10 months, not years.
"Longer chain" is an informal phrase, but the correct phrase in the context of choosing the chain tip is "chain with the most work". As ABC's difficulty is higher than BSV's by about 30%, ABC is accumulating about 30% more PoW per block than BSV is. At this point, even if BSV had a sprint and got to a higher block height, it would not have the most work.

Of course, it's a moot point, since the BSV chain and the ABC chain are mutually incompatible due to CTOR, OP_CDSV, OP_MUL, and everything else, so neither chain could ever wipe out the other.

But insofar that hashpower was being used to win over the hearts and minds of hapless users, and insofar that hashpower was being used as a threat to reorg and double-spend transactions to exchanges, I think a clear message was sent today: ABC has way more support from miners than BSV.
> Your claims about 20x slower sorting are looking at nanosecond scale and are not assuming that miners will eventually use custom hardware for some of these sorting operations.

You mean like GPUs? The same computational requirements would also apply to GPUs. GPUs don't like random memory accesses any more than CPUs do. It's a fundamental result of using DRAM.

The idea that miners are going to develop ASICs to do transaction processing to replace GetBlockTemplate code is not plausible. ASICs are fixed-function devices, and GBT needs to be flexible code.

> IMHO these are all very minor issues and you even admit in your post that we haven't even tested the other orders, gavins proposed ordering specifically(but why would we right? to save a few nanoseconds?)

To save a few *hundred* nanoseconds *per transaction*. At 1 GB blocksizes, sorting into Gavin's order is expected to take about 20 seconds *per hop*. For a 3 hop block propagation, that's a delay of 1 minute just from sorting.

> hypothetical attacks that require collusion between miners

No, the worrying thing about the outcast attack is that it specifically does not require collusion. By manipulating how the block is propagated, the attacker can get other miners to support the attacker's block *without their consent or knowledge*.
Bitcoin SV appears to still have a bug which limits it to forwarding transactions at a rate of about 7-14 tx/sec. As long as SV is on a network shared with BU and ABC 0.18, SV will receive transactions in excess of 14 tx/sec, though it will not contribute equally with forwarding transactions. However, if SV is on its own chain, it will not be capable of handling those transaction volumes, as transaction forwarding will bottleneck at around 10 tx/sec.
Furthermore, there IS a way of making transactions that will be invalid on one chain but not the other. You can create a transaction that moves all of your money into a separate wallet, but does so using one of the new opcodes, and that will make the transaction happen on one chain but not the other.

So you send Coinbase BCH but not BSV, and then you ask for BCH back and they return you BCH+BSV because they didn't know any better.

/u/sleepingkernel
> I always have the choice to dump my stash and move to another coin or simply back to fiat

You have another choice, which is to *ignore their hard fork attempt* and continue to follow the same rules as before. You will mark all of their quadruple-inflation blocks as invalid. If any miner decides to mine blocks under the same rules as before, they will be rewarded for their labor with coins that have value on the market, and the old chain will continue to live.
I was under the impression that HalongMining delivered on their orders as promised with only minor delays, used a new design (though obviously inspired by Antminers on the macro scale) and was in no way a scam.
You are, unfortunately.
Why would ViaBTC EVER mine on top of BTC.TOP's block instead of mining on top of their own block? That makes absolutely no sense.

I'm getting really tired of you. I should probably just downvote you and move on.

> However ABC nodes were rate-limiting transactions to approximately 7tps, so it is unlikely that 99% of the blocks had already propagates because ABC nodes and miners where not sending all the transactions to each other.

The INVENTORY_BROADCAST_MAX rate limiting affected the propagation of transactions through the network, and determined the size of the blocks that were eventually mined, but it did not determine the propagation speeds for the blocks that were mined. All ABC nodes were broadcasting the same 7-14 transactions per second, so all mempools were synchronized just as well as if those 7-14 transactions per second were the only ones that had been generated in the first place.

Incidentally, that bug [was fixed](https://reviews.bitcoinabc.org/D1784) today.
It's 99% compared to the basic uncompressed getblock protocol. Xthin/CB gets 96%, so Graphene without CTOR is only about 4x better than the status quo.

Graphene with CTOR improves its efficiency by about 7x over regular Graphene, so Graphene with CTOR is about 28x better than Xthin/CB.

[Xthinner](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1239#post-80503) with CTOR also gets a 4x improvement over the status quo, but with higher expected reliability and code simplicity than Graphene.

> The CTOR optimization of graphene would only be meaningful when blocks are truly enormous. 

Based on the data [I published today](https://medium.com/@j_73307/block-propagation-data-from-bitcoin-cashs-stress-test-5b1d7d39a234), I would say that the CTOR optimization of Graphene will be meaningful when blocks are about 40 MB or larger. (Currently, we are probably getitng a 20 second block propagation delay with 10 MB blocks; Graphene alone would push that to 40 MB; above that, we'd either need CTOR or to stop worrying about keeping BCH decentralized.).

/u/jonald_fyookball 
Replay protection will not cause the split. The fact that nChain's changeset proposal is a mutually exclusive hard fork is what will cause the split. 

https://www.reddit.com/r/Bitcoincash/comments/9ca6cn/craig_says_split_no_split/e59il3o/

Craig Wright seems to not be aware of this fact, and is claiming that they can avoid a split by just using enough hashrate. However, he is simply incorrect on this point.
Relevant username.
No, our argument is that you guys *act* like unthinking robotic puppets controlled by your Faketoshi overlord via Twitter. We don't think you're literally silicon-based artificial life forms.
XT doesn't have Graphene yet. I vote BU.
The strongest arguments for a canonical block ordering come from Graphene's efficiency when the order does not allow arbitrary entropy data. I think this conversation thread (starting with /u/markblundeberg's post at the top of the context) describes the arguments well. 

https://www.reddit.com/r/btc/comments/9bc3k4/a_critique_of_awemanys_critique_of_canonical/e52v46d/?context=10000

The block validation arguments that the Bitcoin ABC team originally used are red herrings, as I showed that you can use the outputs-then-inputs validation algorithm [perfectly fine](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244/files#diff-24efdb00bfbe56b140fb006b562cc70bR2222) even on a topological transaction ordering. However, the IBLT/Graphene benefits are very real, and probably big enough to justify some sort of CTOR. The order does not need to be lexical for the CTOR to have its benefits for Graphene, but lexical order is the cheapest to generate and verify, and I see no reason why it would be worse than any other order, so I like lexical order just fine. But that's just my opinion, and I don't think my opinion is well-founded enough to base a hard fork off of it just yet.

Sharding is equally possible with or without a lexical ordering. I think that sharding will be a bit more efficient with a lexical ordering because the lexical ordering will cause all UTXO insertions to be sequential in-order insertions. While that's kinda cool, it's not worth writing Mom about. Although maybe you should anyway, I'm sure she'd love to hear from you.
It's still pretty ad-hoc and last minute, so I wouldn't call it even remotely professional. But we might be able to glean some useful information from it.
I attacked the works of his that I've read. I attacked his Turing completeness claim, and I've also attacked his selfish mining claim.

I am not attacking the content of his PoW theory, because I have not read it and do not want to read it.

> If csw is the equivalent of the flat earthers, then why is there so much drama?

Yeah, why is there so much drama about whether the Earth is flat? Why is there so much drama about the safety of vaccines? Why is there so much drama about whether CO2 emissions cause global warming?

I'll tell you why: it's because basing your actions on incorrect beliefs can hurt the people around you. If you don't vaccinate yourself or your children, then you turn into a disease vector. Vaccines are only 95% effective at protecting people directly from contracting a disease, so a vaccinated person who is around unvaccinated people still has a (small) chance of getting sick and dying. Choosing not to vaccinate your own children can result in someone else's child dying.

I will never again believe at face value any statements that Jenny Mccarthy makes on medicine and science. I won't automatically disbelieve them either; I'll just ignore her. I don't have time to pay attention to every kook and nut in the world. My time is finite, and I prefer to spend it listening to people whom I deem insightful and accurate in their claims. Jenny is not that kind of person. Eric Kandel is.
Because users define the market value for the coin. Granted, it's weighted by dollars, not by individuals, but the two are closely correlated.

I've read enough papers of CSW's to know that he is wrong more often than not, and that it's a waste of my time to read any more of them. He's like a bad fanfiction author who takes some good ideas from the source material and turns it into rambling pseudo-nerd porn. 

I prefer to spend my time reading papers from people who are right more often than not.
Very succinctly and accurately said.

/u/tippr $5
Point 3 is more along the lines of "we can assume no siblings because we check for that in our code for free." It comes naturally as part of updating the UTXO set database/cache so you can validate later blocks. But otherwise, yeah.

The biggest benefits are in block propagation. That has always been the primary motivation of this idea, I think. There are also some new things you can do with blocks, such as finding a transaction without downloading the full block, or proving the absence of a transaction without sending the full block. Kinda cool, but not game changers.

Validation parallelization can be done without discarding the in-block order information and without the CBO, but it is slightly easier if you do.

> I assume the writing was an attempt at humor.

The style was, yes. Also a little of the content. There aren't actually any brownies waiting. Just like the cake, that was a lie.
The vitriol comes from the assumption that it's a zero-sum game, and that Bitcoin Cash's success somehow compromises Bitcoin Core's value proposition. In this particular case, people are worried that at some point in the future, when a person goes to buy "Bitcoin", they will end up buying Bitcoin Cash (BCH) instead of Bitcoin Core (BTC). This is a frightening prospect to people who only hold Bitcoin Core coins, since they would be missing out on the price bump.
Let's say you have 5 million users, each of whom opens and closes a channel once a month on average, and who make zero other bitcoin transactions. That's 10 million transactions per month. Channel open/close transactions are about 500 bytes each, so that's 5,000 MB per month. At 144 blocks per day, 30 days per month, this would be 1.16 MB of lightning channel open/close transactions per block. This leaves nearly zero space for non-lightning transactions.

Lightning helps a bit, but it's not a magic solution. It changes the limit on the capacity of the blockchain from being based on the number of transactions to a limit on the number of users. A user who sends 0.1 transactions per month would place a larger load on the blockchain if they use Lightning than if they don't. A user who sends 1,000,000 transactions per month will place a much smaller load on the network if they use Lightning. The break-even point is around 3 transactions per channel opening. Thing is, most people don't send Bitcoin transactions very often, so Lightning won't help much with the way people currently use Bitcoin. 

Lightning also does not work very well with large payment amounts. In order to be able to route a payment, every channel along the route needs to have enough money to be able to send the payment. Currently, the average Bitcoin transaction value is around [$85,000](https://bitinfocharts.com/comparison/bitcoin-transactionvalue.html). In order to be able to route an $85,000 transaction along a Lightning network, that would require each of the roughly 15 to 20 hops along the path to have $85,000 of available funds in their channel which could be pushed in one direction. Since each node needs to have no fewer than two channels open, this means that each participating node would have to have a wallet with over $170,000 locked up in Lightning in order to be able to route the average Bitcoin transaction. Furthermore, participating in such a transaction would make that channel unbalanced, and would make it unavailable for subsequent transactions unless they were in the opposite direction, unless the participant had far more than $170,000 in their wallet (e.g. $1.7m). Lightning will enable new uses of Bitcoin (e.g. micropayments), but it won't help with the occasional, large-value transactions that dominate Bitcoin's current usage.
Actually, batch 1 of the S9 had *better* efficiency than later batches of S9s. Batch 1, 2, and 3 were actually 0.099 J/GH at the wall, as per spec, but every batch after that had about 5-10% higher power consumption. Bitmain increased the voltage on their chips for later batches in order to get better yields and reliability, since the RMA rate on the first few batches of S9s was very high. However, despite the increased power consumption, Bitmain never increased the power specs on their advertising material, and still advertise 0.099 J/GH even though they haven't shipped a miner with that efficiency for about a year.

They did something similar with the Antminer S7. The first few batches of S7 had no voltage regulation on the incoming 12 V line. If the PSU supplied more than 12 V, then the ASICs would get slightly more voltage each; if the PSU supplied 11 V, then each ASIC would get 9% less voltage. This was a very efficient design, as voltage regulators are lossy devices, but it gave Bitmain a headache with tech support because bad PSUs could cause the miner to crash. With later generations of S7, they added a buck-boost voltage regulator to make the voltage delivered to each ASIC constant regardless of the PSU type. This improved reliability with poor PSUs, but it also caused about 6% of all delivered power to get wasted as heat in the voltage regulation stage. When Bitmain released the S7s with this change, the efficiency dropped from about 0.25 J/GH to about 0.28 J/GH, but of course they didn't update their spec sheets and kept selling them as if they were still 0.25 J/GH.

In any case, the 0.106 J/GH figure for the S9 is the actual at-the-wall consumption for real devices measured by a third party (me), whereas the 0.75 J/Gh figure is their chip-level claimed efficiency, and their at-the-wall efficiency is 0.095 J/GH but not actually listed on their website.
"Snapshotting" is done every single block with Ethereum.
By waiting until the block time went from 12 minutes down to 7.5 minutes:

https://data.bitcoinity.org/bitcoin/block_time/7d?f=m10&r=hour&t=l

OP probably thinks it was manual fee setting that did the trick, but it was really just that he chose a good time to send transactions.
This isn't a lie, this is just being wrong.

I wish people wouldn't say "XXX is lying!" each time they find something like this.  In order to be a lie, the person needs to *knowingly* say something counterfactual.
All the PoW in the world can't make your full node trust an invalid signature. Nakamoto consensus says that everybody follows the heaviest *valid* chain. If the heaviest chain violates your ruleset, it doesn't matter how much PoW it has; your node will reject it and use a lighter chain, even if that chain doesn't have any new blocks.

Basically, if I'm running a SegWit node, and you are a miner running a non-SegWit node, you might mine a block that spends my money, then show it to me and say you spent my money, and I'll just say "No you didn't." Other SegWit nodes will agree with me. Other non-SegWit nodes will agree with you. There will not be consensus. This is a hard fork.
> A fix to this problem would require a hard fork...

Not true. You can make the quadratic hashing issue not an issue with a soft fork. Simply placing limits on the amount of hashing per transaction works well enough. Limiting the size of an individual transaction is fine too, as does forbidding SIGHASH_ALL in transactions larger than e.g. 100 kB.
The same thing that prevents most other things from changing: inertia.
The hardware for mining ETH is simply GPUs. People with GPUs will mine whatever coin is most profitable at that time, using sites like whattomine.com to compare profitability between coins.

The ETH hashrate is up because the price of ETH is up and the price of ZEC is down.
> Just to be clear, you're endorsing Segwit's increased block capacity?

No, I am not. I am neither in favor of nor opposed to SegWit. It is not my preferred approach to scaling, and I strongly dislike the 4x discount function as well as the soft-fork deployment with the auxiliary block merkle root tucked into the coinbase transaction, but I am neutral about whether SegWit is a good or bad thing overall.

All I'm saying in the parent post is that a block capacity increase fork (be it a hard fork or SegWit) will reduce fees in the short term.
Fees do not create coins. They do not break the 21M coin cap.

If you send 1 BTC with a 0.001 BTC fee, your account gets 1.001 BTC deducted, and the recipient gets 1.000 BTC. The miner who includes your transaction in a block gets the other 0.001 BTC. Total inputs equals total outputs, so the money supply is not changed.
I'm tired of the drama.
In my opinion, the same way that farming yields are affected by the light output of Alpha Centauri.
Interesting idea.
> With the USD, we use multiple coins with differing value: quarters, dimes, nickels, pennies. 

USD is one currency. Pennies, nickels, etc. is like BTC, mBTC, µBTC, bits, and satoshis. We're talking about the difference between e.g. USD and EUR. Paying a bill with a mixture of different currencies complicates accounting due to the fluctuating exchange rates.
Toomim Bros has upgraded our mining node and enabled the --dao-soft-fork flag.
As a medium-size miner, I intend to switch to soft fork software as soon as feasible. However, all of the Parity nodes on https://ethstats.net/ that I see right now (admittedly, only two) have poor block propagation performance.

http://imgur.com/ZmjDHVY

It would be nice to have some evidence that Parity achieves performance parity before we switch to it. It would also be nice to have a soft-fork version of geth available.
There is plenty of room for both if the discounting function in SegWit is changed.
I don't know, does Coinbase trade Bitcoin?

Edit: I just checked their site, http://coinbase.com. It looks like they do.
Segregated Witness will increase the amount of data that miners have to download per block to 4 MB in adversarial conditions. [Perhaps you should think a little more about what happens in adversarial conditions](https://www.reddit.com/r/Bitcoin/comments/3xcshp/bip202_by_jeff_garzik_block_size_increase_to_2mb/cy4i8cv).

It was my understanding that you have no interest in participating in a system that lacks certain censorship-resistance qualities, and that without being able to profitably mine anonymously (e.g. behind Tor) anywhere in the world, Bitcoin would lack those qualities and cease to be interesting to you. Are you saying that you think that we can mine profitably over Tor in China with 4 MB blocks with no relay network in adversarial conditions? Or are you saying that with SegWit's 4 MB adversarial blocks, this is somehow acceptable or possible, but with a hard fork blocksize increase to 2 MB, it is not?

I hope you don't think I'm being too adversarial in my dialogue. I'm just trying to figure out how the different positions I've heard you take can be logically consistent with each other.
> Even if the blocksize grew to 1gb, something which would completely centralize bitcoin, bitcoin would not even support the visa network's capacity,

1 GB blocks at 400 bytes/tx would allow **4167 transactions per second**. Visa and MasterCard combined do an average of **[1971 transactions per second](https://icepay.com/payment-methods/visa-credit-card/)**.

Such a system with current technology would end up being less centralized than Visa and MasterCard, but more centralized than most (including me) would consider desirable. In 10 years, though, that may be different.
> No one gives a shit what their opinion is on the block size debate.

That statement is factually false. Many people care what their opinion is on the blocksize debate, including most Core developers. There is a reason for this. Many Core developers do not think it is safe to have a hard fork in which a substantial amount of hashpower continues to operate on the old/small fork, due to the possibility of a malicious actor using the old fork blocks as a tool to defraud users who haven't noticed the fork and have not yet upgraded their full nodes. These developers believe that no hard fork should ever occur without nearly unanimous support for the hard fork among miners. I disagree with this risk assessment, but I see where the reasoning comes from, and will not dismiss it out of hand.
> It's split into a new block, correct?

No.

With the current system, blocks cannot go above 1 MB. If someone creates a block that is larger than 1 MB, it is invalid according to current consensus rules. If there are more transactions than can fit in 1 MB, those transactions have to wait for the next block. The next block will not be found any earlier because there are more transactions. Blocks are found at the same rate regardless of how many transactions they have. You get one block every 10 minutes on average. This means that 2 MB of transactions would take 20 minutes to be processed on average, and 3 MB of transactions would take 30 minutes, etc. This limits us to about 3.5 transactions per second.

With a hard fork to support larger blocks (e.g. BIP101), a block can be larger than 1 MB. It is still one block, and you still get one block every 10 minutes. However, since blocks can be larger (8 MB to start with in BIP101), you can get a lot more transactions per 10 minutes, and more transactions per second. This means about 28 transactions per second.
Show up in #bitcoinxt and ask that question and we'll answer interactively.

Depends on what you can do. Can you code?

We can always use more nodes, especially ones that log to dragonzone. If you have a node, make sure it's configured properly: 

https://www.reddit.com/r/bitcoinxt/comments/3sq2r3/testnet_is_forking_and_unforking/cwzzhm3

Also add it to our spreadsheet: 
https://docs.google.com/spreadsheets/d/1Oss9KQIR6hg2_gweXiKPDN7yVeEagthaWE7ow9v6V3c/edit#gid=0

We're working out a few final details on configuration still. I'm making a few tweaks to my fortestnet branch (trying to trim out some extraneous logging info -- our central debug.log repository is over 13 GiB already). We should be ready for full-scale data collection soon.

One of the big questions right now is what we do about the China problem. I ordered 3 VPSs in mainland China plus one in Hong Kong through Aliyun, and the ones in China have really poor internet connectivity. The best one (Shanghai) gets about 1.2 MB/s at best from what I've seen, and the other two (Beijing and Shenzhen) get about 300 KB/s. It would be great if we could get some other VPSs set up in China, such as through qcloud (Tencent's service) or Baidu's cloud service if they have one. 51idc may be another option. Other providers might be even better. Really, we want to find the best internet China has to offer so we can compare it to Aliyun (which is what most pools currently use) as an alternative. 

By the way, user tagging with /u/jtoomim doesn't work from titles, only from content. You're lucky I saw the title.
No. Blocks larger than 1 MB are not allowed by BIP101 until at least two weeks after 750 of the last 1000 blocks announce BIP101 support, and also no sooner than Jan 11 2016. Currently, we are at 2 of 1000. 

These blocks are just a vote in support of allowing 8 MB blocks, or a quorum detection mechanism if you prefer.

https://github.com/bitcoin/bips/blob/master/bip-0101.mediawiki
If they're ticked off that early adopters have so many coins, they won't use Bitcoin. They'll continue to use fiat instead.

What you're describing is a wealth redistribution. What system do you think presents more value to stakeholders in the system: a system in which a person's stake can't be revoked without his permission, or a system in which it can? In order to revoke the stake of the early adopters, you need to create a new system (a blockchain fork) in which stakes can be revoked, and then you need to convince the early adopters that it is in their interest to switch to the new system. 
There is a tradeoff on where you set that threshold. The tradeoff is between chaos and stagnation. If you set the threshold too high, then the ability of Bitcoin to evolve and improve is compromised. Unfixed flaws will build up over time. If hard forks do not happen frequently enough to relieve that pressure, there eventually will be an altcoin that surpasses Bitcoin by being nimbler, faster, more efficient, and more flexible.

Keep in mind that 75% is a 3:1 ratio. Most of the remaining 25% will be people who think that the blocksize increase is not a dealbreaker. Most people will switch over to support BIP101 once they see that 75% support it. I would be surprised if we had more than 5% of users who were so opposed to 8 MB blocks that they refused to follow the supermajority. If we had a 5% drop in one day in bitcoin price as a result, that would be unfortunate. However, I think that would be more than offset by the immediately 700% higher block capacity, which should drive the price up much more than the blocksize-dealbreaker crowd's influence.
> Without subdivision...

Sipa's [minisketch github page](https://github.com/sipa/minisketch) claims the decoding complexity is O(n^2), and I thought that was the best it could do. I missed the subdivision option; thanks.

> or if there typically is hundreds of thousands of differences-- which seems excessive and weird

That is not unusual for (a) adversarial conditions which a block propagation algorithm might need to handle, and (b) overload conditions when the system is strained to its capacity limit. My goal with Xthinner and (especially) Blocktorrent is to handle both of those situations with good performance, while still having excellent (but not necessarily optimal) performance in typical and ideal conditions.
> Superthin blocks outperforms both xthinner and compact blocks without using CTOR:

>> average encoded bytes per tx: 4.5118

You're confusing bits and bytes. Xthinner gets about [12](https://www.reddit.com/r/btc/comments/afeput/xthinnerblocktorrent_development_status_update/)-16 bits/tx, or 1.5-2.0 bytes/tx.

Interestingly, Xthinner without CTOR would get around 4 bytes/tx as well, so Superthin seems roughly similar in performance to Xthinner without CTOR.
Pre-fork BCH was split into post-fork BCH plus BSV. Some exchanges use the ticker BCHABC or BAB instead of BCH, but it's the same coin that most exchanges have listed as BCH. The fact that these exchanges continue to use BCHABC is a sore point for the community, as it leads to unnecessary confusion of the type that you're going through.

It's a good idea to split your coins into two separate wallets, one for BCH and one for BSV, so that you can sell the BSV if you so choose or vice versa. Some exchanges can do this for you. You can also do it yourself by following tutorials online.
10k S9s would be 140 PH/s, or about 0.28% of the global SHA256 hashrate. I have no idea what pool they use or whether they mine BCH or BTC.
Just because something makes you feel better doesn't mean that neurogenesis is involved. 

Neurons change their shape and function in response to stimuli. That alone is sufficient to explain these phenomena. There's no need to create additional neurons if you can make better use of your existing neurons.
Perhaps you've forgotten the difficulty oscillations that BCH had in August 2017. They were *way*  worse.

The DAA is actually pretty good at minimizing this problem. It's just not perfect.
Graphene also currently still transmits transaction order information, even though it is redundant. The code needs to be modified to take advantage of CTOR. This modification should be pretty simple, though, but nobody has done it yet.
That is consistent with the [hashrate we observed from them](https://www.reddit.com/r/btc/comments/9zbuqd/coingeek_turned_on_18_ehs_of_apparent_hashrate/). However, it looks like they might have something else:

> Furthermore, as part of the Transaction, Squire would have a twelve-month right of first refusal to acquire CoinGeek’s remaining crypto mining assets.
IBLTs are inherently probabilistic. Decodability is not guaranteed.
Bitmain's new miners are irrelevant. Bitmain has always had enough hashpower to flatly trounce CoinGeek and BitcoinSV pool even without them.

According to Bitmain's [Oct 9th disclosure](https://blog.bitmain.com/en/hashrate-disclosure/), they had 2.3 EH/s of SHA256 hashrate which Bitmain itself directly owned and controlled. In contrast, CoinGeek has about 0.67 EH/s, SVPool has 0.472 EH/s, and BMG Pool has 0.384 EH/s, for about 1.52 EH/s total among the SV side.
There's another way, and it's more effective than mining: speak with your money on the markets. Hashrate follows price.
> to be exact, it takes ~1µs of GPU time

I don't think that's quite correct. A single core of a CPU can do 1 ECDSA verification in 100 µs, and GPUs typically get around 100x higher throughput on compute-heavy tasks like that, but that would be compared to a full CPU, not to a single core. 

For example, if we assume that each ECDSA verification takes 400,000 cycles per core on a GPU as well as on a CPU, and if our GPU runs at 1.25 GHz and has 2304 cores (i.e. RX 580 specs), then our GPU should be able to do 7.2 million ECDSA verifications per second, or an average of one ECDSA verification every 140 ns.

> So how much does this 1µs cost? 

140 ns of a 120 W GPU uses 16 µJ of energy per verification, or 4.7e-12 kWh. If your electricity costs $0.10/kWh, and the amortized cost of the GPU plus maintenance is another $0.10/kWh, then that verification would cost $9.3e-13, or 2.3e-7 satoshis. 

That ECDSA verification also requires about 150 bytes of transaction size (in stack pushes of the signature, pubkey, and message hash), so the actual fee it incurs is about 150 satoshis. This means that OP_CDSV pays a fee that is 640 million times higher than the computational cost on a GPU. (This 150 satoshi fee is correct, however, since there are other costs to miners and to the network other than the computational cost, and those costs are about 8 orders of magnitude larger.)

If we did as CSW, RXC, and /u/2ndentropy suggest, then the fee for doing a ECDSA verification from the stack would be around 1 million satoshis, or around 4.3 trillion times the computational cost of an efficient ECDSA verification on a GPU.

For numbers on a typical CPU instead of a GPU, multiply the cost by 100.
I'd be down for that! But no, I have not been invited.
Why not?
Clearly, you are not a fan of the concept of "permissionless innovation."
A very good decision to include an opcode limit of 500, in my opinion. Limitless opcodes in transactions is pretty scary, IMO.
The non-CTOR version can be used on BTC as well. I actually hope that thebluematt notices this and tries to include it in Bitcoin Core.

But I like the joke. Have an upvote.
> un-proven

There's a testnet.

> un-benchmarked

[There are benchmarks.](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244)

> that will incurr an O(n log n) overhead

UTXO lookup is already O(n log n), except that UTXO lookup takes about 100 microseconds per input or output (SSD) instead of less than [100 nanoseconds per *transaction* (std::sort)](https://probablydance.files.wordpress.com/2016/12/uniform_ints.png)

> where at best there will be a < 1% transmission savings over current non-CTOR based Graphene

No, that 1% number was completely made up by Tom Zander. [My data shows a ~3x smaller transmission size](https://www.reddit.com/r/btc/comments/9gmi4z/logical_proof_that_wormhole_omni_is_not_secure/e67253i/) for Graphene with CTOR with actual BTC blocks versus a reasonably sophisticated order entropy encoding algorithm, rather than 7x smaller for CTOR versus the current naive order encoding algorithm.

> ABC is obviously being funded by Bitmain and they have an agenda with Wormhole.

And yet [CTOR makes Wormhole 0-conf unequivocally worse](https://www.reddit.com/r/btc/comments/9gmi4z/logical_proof_that_wormhole_omni_is_not_secure/e65c9ei/). This "Wormhole Bitmain Fork!" narrative is just a plausible-sounding smear tactic that does not match the technical details of the issue.

> Danger ahead

I agree with this part. Danger is before us, behind us, and in our midst.
It wasn't quite flicking a switch, but it was a lot easier than I was expecting.

[Main commit for enabling BCH support on p2pool](https://github.com/jtoomim/p2pool/commit/dcf9b5b55e75b63dddc1bd1dceda6754a3ad35f2)

[Checking 1 MB and 4 MWeight on BTC, LTC, etc but 8 MB on BCH](https://github.com/jtoomim/p2pool/commit/b2f661370aa2531459f5bac722fa525227522c4f)

[And a few more block weight checking improvements](https://github.com/jtoomim/p2pool/commit/498bf97ef56f23d80da5cefff15dafa2e769a0ef)

The second and third commits were only partially related to adding BCH support. It was really mostly just the first one.

P2pool does not yet have support for the Cashaddr format, and requires people to use legacy addresses. That's not hard to fix, but it's also not that important, and I just haven't taken the time to bother with it. P2pool also has severe performance issues and cannot generate more than about 2-4 MB blocks without losing hashrate, but those issues are pretty specific to p2pool's architecture and not necessarily indicative of the issues that most other pools would have.

The rigs themselves work fine on either coin, and need no configuration for BCH.

/u/tjmac /u/dank_memestorm /u/iKahuna 
CSW has built a personality cult around himself. Often, when [I criticize](https://www.reddit.com/r/Bitcoincash/comments/9glgf7/when_csw_fake_satoshis_coin_forks_it_will_trade/e66j74z/) Bitcoin SV or nChain's technical position, his followers will counter with a naked [appeal to authority](https://www.reddit.com/r/Bitcoincash/comments/9glgf7/when_csw_fake_satoshis_coin_forks_it_will_trade/e66jjvj/), citing his brilliance and reputation as Satoshi. But if we [respond directly by attacking his purported brilliance and reputation](https://www.reddit.com/r/Bitcoincash/comments/9glgf7/when_csw_fake_satoshis_coin_forks_it_will_trade/e66kisa/) as Satoshi, we're suddenly performing an ad hominem attack and are [trolling](https://www.reddit.com/r/Bitcoincash/comments/9glgf7/when_csw_fake_satoshis_coin_forks_it_will_trade/e66ktxu/).

If you want us to focus on technical aspects and not attack his character, then you have to not rely on his character and listen to our technical criticisms of his work. You can't pick and choose when you get to do appeals to authority versus [playing the ad hominem victim card](https://static.tvtropes.org/pmwiki/pub/images/victim_card_monopoly.png).
Yes, you could override the wallet default settings to do something stupid with BCH.

With BTC, you *have* to override the wallet default settings in order to avoid wasting money.
> we will soon be upgrading to even bigger blocks

Yes, we will, *with my help*, which you are getting whether you want it or not. 

We both want big blocks. We both want to see Bitcoin Cash scale.

The difference between you and me is that I think we need to build the infrastructure first so that big blocks are actually technologically feasible and cryptoeconomically sustainable, and I am willing to put in the work to make that happen, whereas you think that blindly increasing the limit with no prior code optimization is the way to go.
> Parallel block validation shifts the risk of making large blocks onto whoever creates the big block.

This is not true. In the ViaBTC/BTC.TOP orphan race, ViaBTC was the one who created the bigger block, but BTC.TOP is the one who lost out as a result of it, even though all of the network except ViaBTC appears to have received BTC.TOP's block first. You should know this if you had read the article. Parallel block validation would not have changed that outcome at all.

> I'm not understanding the motivation for CTOR if it's not to optimize block validation, given Graphene and Xthin's improvement benefits over traditional block propagation techniques.

The motivation for CTOR is that it can help with *block propagation*, which is a far bigger bottleneck than block validation. You would know this if you had paid attention when reading the article.

Xthin *is* the traditional block propagation technique now. It isn't good enough, as you would know if you had read the article. Graphene can do 4x better without CTOR if it can be made to work reliably. That's only good enough for ~40 MB blocks. Graphene can do 28x better than Xthin if we have CTOR. Xthinner can do 4x better than Xthin with CTOR, or 2x better without CTOR.

> The amount of date sent for a 20MB block is insignificant given that 99% of the data has already been sent.

With Xthin, it's only 96% of the data that has already been sent. But more to the point, that 1% or 4% **is significant**. TCP congestion control (and other factors like the multihop propagation algorithm) meant that we only got about 44 kB/s of effective network throughput. Which you would know if you had carefully read the article before sharing your opinion on reddit.

> it's reconstructing it and validating it that is costly, and multilayered applications here would vastly improve scaling.

No node took longer than 4.2 seconds to reconstruct and validate a block, and that node had a much slower CPU than most miners. You would know this if you had carefully read the article. The nodes with miner-level hardware (i.e. a Core i7 4790k) took 1.96 seconds to reconstruct and validate the largest block. This block took on average 33 seconds to propagate through our subset of the network, and probably took 66 seconds total from the time it was mined. 1.96 seconds is a very small portion of that 66 second total.
"Sharding" can mean a few different things:

1. A form of parallel processing in which different parts of a block are processed by different computers, with the majority of data used by each computer stored locally on that computer.
2. A form of parallel processing in which different parts of a block are processed by different threads, with the majority of data used by each computer stored locally in data structures "owned" by that thread.

Yes, #1 is quite some ways away. However, the same programming strategies can also be used for #2, and #2 is definitely not far away.
His attempts at trolling have been hindered by his inability to get his patents approved, but that hasn't prevented him from trying to profit. He has used the threat of his invalid patents in an attempt to cow and intimidate others, and specifically to frighten people away from the Bitcoin ABC fork proposal in favor of his own Bitcoin SV fork proposal. This is a means of profiting from the patent itself. 

nChain is not trying to make an "Agent-based turing complete transactions" system. They aren't patenting products that they intend to produce. Instead, they are trying to patent anything they can that's remotely related to blockchain tech so that they can use those patents aggressively against everybody else. The point of these patents is not to make a new thing; the point is to prevent everyone else from making anything new.

No, threatening to litigate does not make one a troll. Threatening frivolous litigation does. As this patent is obviously invalid (and was marked as such by the ISA), this is a threat of a frivolous lawsuit.
I do not believe these people to be shills. I believe them to be misinformed Twitter followers. See [this comment](https://www.reddit.com/r/btc/comments/9dhvoz/craig_wright_is_a_patent_troll_who_tried_to/e5hqugo/) for how he gets his fervent following. **It's not because he pays them. It's because he *plays* them.** He has played the confidence game, and won theirs by manipulating his image to exemplify their desires.
What I wrote was

> any CTOR will result in a large improvement, and a roughly 7x reduction in bandwidth

And what you quoted was

>roughly 7x reduction in bandwidth

Did you not notice that I said **any CTOR**? That includes the ordering you mentioned. The "canonical" in CTOR just means "agreed-upon-in-advance", not "lexical."

> CTOR is an optimisation that breaks a lot of code out there.

Again, you seem to be missing the distinction between a lexical ordering and a canonical ordering. Canonical orderings can be topological orderings, such as Gavin's ordering. Does Gavin's ordering break your software? No, it doesn't.

I understand why you're confused. The papers from ABC have used the term "CTOR" to refer to the concept of a lexical block ordering. However, that is their error, and you should not follow their error nor assume that I make the same error.

I always say CTOR when I mean canonical (not necessarily lexical, possibly topological), and I say LTOR or lexical when I mean non-topological and sorted by txid.

On a more social note, I would appreciate it if you were less quick to jump to the conclusion that I'm being dishonest just because you see something that doesn't make sense to you. The appropriate next step when something doesn't make sense is to ask for clarification.
Both are forking the chain. Bitcoin SV is performing a mutually exclusive hard fork relative to Bitcoin Unlimited and Bitcoin XT as well as relative to ABC 0.18. As was mentioned in the link I cited, the new opcodes (LSHIFT, RSHIFT, MUL, and INVERT) as well as the removal of the script sigops limit make sure of that.

> nChain's plan is conservative compared to ABC's new untested consensus rules.

[This is a quick fix because we've run out of time. This must be implemented properly in the future.](https://github.com/bitcoin-sv/bitcoin-sv/commit/55c993841725690256fd4b7093142ddd8084312a) -- Bitcoin SV's most recent commit message

Bitcoin SV only started work on August 22nd, and they haven't even included the blocksize limit change yet. Even so, people have already found some troubling bugs in Bitcoin SV's code. That includes gmaxwell on [github](https://github.com/bitcoin-sv/bitcoin-sv/commit/74922dd1f7b802755c158cb448205aac150579b4#commitcomment-30346598) and /u/cryptocached [on reddit](https://www.reddit.com/r/btc/comments/9ce492/bsvs_new_op_lshift_and_op_rshift_are_not/).

The Bitcoin SV change *I* have the biggest issue with in the Bitcoin SV proposal isn't the poorly implemented opcodes, but the [removed limit on opcodes per input](https://github.com/bitcoin-sv/bitcoin-sv/commit/a4bfa1ca55b7f4291b6e790f2c2ae2b063147b6d). Several times in Bitcoin's history, we've seen bugs, attacks, and exploits that would have been far worse if it weren't for the 200 sigop limit. For example, the [O( n^2 ) sighash bug](https://bitcointalk.org/?topic=140078) was limited to a 3 minute attack by the 200 sigop limit. The FindAndDelete() bug was limited to only (!) [5 hours](https://bitslog.wordpress.com/2017/01/08/a-bitcoin-transaction-that-takes-5-hours-to-verify/) by the 200 sigop limit. While there may be a compelling use case for increasing the limit, I have not seen one proposed. Moreover, removing it entirely seems reckless and foolish.

On the other hand, Bitcoin ABC has been working on their code for four months already. Over the last two weeks, I have been testing a branch of Bitcoin ABC that uses the new OTI transaction validation order immediately, without waiting for the hard fork. This test is to ensure that the new block validation algorithm will work okay. That code and some of my early test results are in [PR #244](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244).
https://www.reddit.com/r/btc/comments/9cfi1l/re_bangkok_ama/e5afenr/
I can understand their confusion.
> I wish miners start signaling for no hardfork. So if theirs a split its with insignificant hash power

Yeah, this might be worth doing. Maybe "No Nov Fork" added to the coinbase message. I'll look into adding that to my mining node tomorrow.
Here's some performance data from my mining Bitcoin ABC node while validating this block:

    2018-09-01 14:43:49   - Load block from disk: 0.00ms [0.00s]
    2018-09-01 14:43:49     - Sanity checks: 0.00ms [1.54s]
    2018-09-01 14:43:49     - Fork checks: 0.02ms [0.02s]
    2018-09-01 14:43:49       - Connect 67352 transactions: 236.33ms (0.004ms/tx, 0.003ms/txin) [19.17s]
    2018-09-01 14:43:49     - Verify 67754 txins: 726.22ms (0.011ms/txin) [20.91s]
    2018-09-01 14:43:49 Pre-allocating up to position 0xe00000 in rev01016.dat
    2018-09-01 14:43:49     - Index writing: 42.57ms [0.22s]
    2018-09-01 14:43:49     - Callbacks: 0.03ms [0.00s]
    2018-09-01 14:43:49   - Connect total: 772.52ms [3.44s]
    2018-09-01 14:43:49   - Flush: 23.43ms [0.20s]
    2018-09-01 14:43:49   - Writing chainstate: 0.83ms [0.01s]
    2018-09-01 14:43:50 UpdateTip: new best=00000000000000000069c3ad902b27621493eea7c3504d9a7a279b04c9dad565 height=545978 version=0x20000000 log2_work=87.571388 tx=254346533 date='2018-09-01 14:41:40' progress=0.999998 cache=10.2MiB(75900txo)
    2018-09-01 14:43:50   - Connect postprocess: 402.17ms [3.93s]
    2018-09-01 14:43:50 - Connect block: 1198.95ms [7.58s]
    

So 1.2 seconds total to validate a 15 MB block on a 4.4 GHz Core i7 4790k with dbcache=3000. Not bad at all, but block propagation has always been the main point of concern.

Edit: Some more data. On a different machine, creating an 8 MB block template (time bitcoin-cli getblocktemplate) takes 1.437 seconds, of which about 0.288 seconds appears to be parsing JSON and localhost socket transmission. Block assembly appears to take about 2x longer than block validation for these blocks. I'm spinning up a server to generate up to 32 MB block templates now, and will do some more benchmarking on that in a bit.
No, Blockstream was actually competent. He's a leaked version of Blockstream 0.01.
Of course it can be predicted. This was a calculated frenzy. Craig knows that anger is his best and only defense against that question.
Not me, which is why I have to be more conservative if I want my pull requests to get merged.

Your follow-up question might be: why am I contributing to Bitcoin ABC instead of Flowee?

Because I'm currently running Bitcoin ABC on a poolserver that's serving 6 PH/s of hashrate. Flowee isn't ready for that yet.
Do you believe the earth is round? If so, you should really read this website by the Flat Earth society about [proof of flatness](https://wiki.tfes.org/Frequently_Asked_Questions#Geography_and_Related_Phenomena) and the [Bedford Level Experiment](https://wiki.tfes.org/Bedford_Level_Experiment). By refusing to read it, you are succumbing to Rounder cult of only worrying about the official orthodox story and not paying attention to the truth of flatness. Take a look at the wiki. It is something that can really red pill people.
Core is supposed to skip scripts before checkpoints and verify them after checkpoints, but for whatever reason, that doesn't happen in my test setup. Maybe it's a result of using -reindex-chainstate.

In my test setup, the disk database pretty much is never used, as everything hits the CCoinsViewCache layer. (I use -dbcache=8192.) But yeah, LevelDB is a lot slower than an optimal solution would be. I've been considering writing a journalled memmapped hashtable to replace LevelDB.

Currently, though, I'm working on a replacement UTXO cache system that uses atomics to achieve locklessness. I'm leaning towards using a tree-based map so that I can get better memory efficiency and also avoid using siphash. I might try both a tree and a hashtable and see which performs better.
~~Yes, that's exactly what it means.~~

~~Edit: It also means that old UTXOs spent on BSV can be replayed on BTC, which is way worse.~~

Edit 2: Nope, I was wrong. It's the 2018 fork, not the 2017 fork.
> Why is it a problem if someone else can feed you 128MB blocks?

Performance is dependent on three things:

1. The amount of time it takes for a block mined by someone else to be propagated to me and for me to finish validating it.
2. The amount of time it takes for me to assemble a new block template.
3. The amount of time it takes for me to propagate my block to everyone else and have them validate it.

By controlling the sizes of the blocks I generate, I can control #2 and #3. I have no control over #1. In practice, propagation is about 70% of the delay, validation is about 15%, and template assembly is about 15%. This means that if other people are creating large blocks, I will suffer an increased orphan rate as a result, benefiting them slightly. If I create large blocks myself, I will also suffer an increased orphan rate, and the effect will be about 15% stronger.

This seems like it would result in a fair and stable system. If I generate large blocks, I benefit less from everyone else's orphans than I suffer directly from my own orphans. This suggests that nobody will create excessively large blocks because they don't want to suffer increased orphan rates. But there's a problem:

A pool or miner will never orphan its own blocks.

This means that a pool with 30% of the network hashrate will have a 29% lower orphan rate than a pool with only 1% of the network hashrate. This 29% advantage is greater than the 15% disadvantage in block assembly time. It can actually be beneficial to a large pool to intentionally create orphan races by publishing bloated blocks. Furthermore, most of the orphan rate risk to the block publisher is compensated for by fees, whereas the miners who are forced to validate those blocks get no fees, exacerbating the problem.

The end result is that large pools will be more profitable than small pools regardless of how good their servers are. It's not survival of the fittest; it's survival of the biggest. Miners will flock to the larger pools in order to maximize their revenue, and the large pools will gobble up nearly all of the hashrate. Eventually, we'll be left with one pool that controls >40% or >50% of the hashrate. 

This scenario compromises the security of Bitcoin. The last time we had a pool with >40% of the hashrate, a disgruntled employee at that pool used the pool's hashrate to attempt a [double-spend attack](https://www.reddit.com/r/Bitcoin/comments/1qqmr4/ghashiocexio_and_doublespending_against_betcoin/) against a gambling site. The next time that happens, the pools might get hacked or co-opted by a malicious government to censor or reverse transactions. It would be better if we can avoid that scenario.
I *really* hope you're being sarcastic.
He's referring to block size (through the use of Lightning or other efficiency improvements) **not the block size limit**.
You're confusing miners and mining pools. Mining pools choose the transactions, not the miners. 

Even for mining pools, a transaction isn't free. Choosing to include your own transaction (fee = 0) means that there's another transaction that you're omitting (fee = $15), so even though the direct cost may be zero, the opportunity cost is substantial.
Hi, industrial Bitcoin/Litecoin/Ethereum/Zcash miner here. I only buy GPUs if I expect them to pay for itself before I expect PoS to happen. After PoS, we will either mine other coins if any remain profitable (e.g. Zcash), or try to repurpose our GPUs for something like deep learning, or just sell them off to gamers for 50% of what we paid for them. 

I expect that about 2/3 of all mining GPU rigs are currently mining Ether, so if Ether mining ends and all Ether miners switch to other currencies, the mining difficulty on those blockchains should triple basically overnight, leaving most of them only marginally profitable if profitable at all.

Overall, though, I'm really looking forward to PoS. I dislike the fact that facilities like [mine](https://toom.im) are necessary, and I think that mining uses a lot of energy needlessly. We also hold a substantial amount of Ether, and are looking forward to a reduction in the inflation rate via a reduction in the block reward or a transition to PoS.

Other miners may have different views than I do, of course. I only speak for myself.
Does every single Ethereum full node need to store every detail of every Ethlance contractor out there? In my opinion, no. Ethlance could commit just a single hash to the blockchain which can be used to lookup that contractor's data via IPFS or Swarm. Rather than storing 1988 bytes as per the screenshot, this could be done with around 32 bytes, or maybe 100 bytes to allow for some critical data to be used directly by the smart contract (e.g. ETH address, username).

Gas prices should be high enough to discourage frivolous data usage and lazy programming.
User-activated soft forks are a $20b game of chicken between miners and users. 

The miners say they won't support the soft fork if it passes. The users say they won't support the miners that don't support the soft fork. If both parties hold their ground, then we're left with two chains. In one of those chains, transactions get confirmed but coins have less value. In the other one, coins have value but transactions don't get confirmed most of the time. 

Tastes just like **[chicken](https://en.wikipedia.org/wiki/Chicken_\(game\))**.
Soft forks need to be enforced by miners. A majority of miners needs to enforce the soft fork rule, or else bad things happen.

SegWit repurposes an opcode that previously meant "This transaction is valid no matter what" to mean "This transaction is valid only if accompanied by a valid SegWit signature." Any transaction containing this opcode will be considered valid by non-SegWit nodes, but will only be considered valid by SegWit nodes if properly signed. This means that if you send money into a SegWit address, and most miners and nodes are not running SegWit, then I can spend your money whenever or however I want.

If someone creates an improperly signed SegWit transaction, then miners who enforce SegWit will refuse to follow any block that includes that transaction, and will build their own chain without that block or that transaction. If the SegWit miners are in the majority, then this SegWit chain will eventually overtake the non-SegWit chain. Non-SegWit miners will see the SegWit chain as being both valid and longer than the non-SegWit chain, so they'll follow the hashrate majority. However, if the SegWit miners are in the ~~majority~~minority, then they will never overtake the non-SegWit majority, and there will be two separate chains forever. SegWit and non-SegWit nodes will be unable to agree on the chain state, and Bitcoin will be split. Furthermore, on the longest chain, any attempts to use SegWit addresses would result in anybody being able to spend your money.

Right now, SegWit-supporting miners are a minority, so the result would be a chain split with completely unsecured SegWit addresses on the longest chain. Most people consider this a bad outcome.
That's not how transaction malleability works. With TM, a third party can change the signature slightly without invalidating it, so they can change the txid of the transaction, which is just the hash of the full tx including the signatures. However, they can't change what the signature covers, so they can't change anything covered by the signature. The redeem script (including any NO_OP calls) are therefore not be malleable.
After SegWit activates, full nodes would reject blocks from miners who are trying to roll-back activation. Basically, SegWit activation is a soft fork, but SegWit de-activation is a hard fork.
It may be possible to get around (or get better leverage on) this tradeoff by using multisignature schemes.

Each validator needs to send at least one message attesting to H. However, they do not necessarily need to send that message to every other validator. Consider the following hypothetical arrangement, in which we have 5 validators:

    A -> B -> C -> D -> E

Validator A signs a message attesting to H and sends that message to validator B, who adds his own signature (creating a multisignature) and passes the (AB) multisignature to C, who signs and passes along, etc. Once E signs, the signature is complete, and is broadcast to all other validators and all full nodes. In this scenario, with D validators and N full nodes, we only need to send a total of 2D+N messages, each of which is only the size of a single signature. Assuming that N >> D, this translates to each node receiving and sending approximately one message.

Such an arrangement would have terrible latency issues, since all messages are signed sequentially. A more effective arrangement might be to set up all validators in a k-ary tree. E.g.: 
    
        A
     B     C
    D E   F G

In this scenario (k=2), B aggregates messages from D and E and adds his own, and A aggregates (BDE) and (CFG) together and adds his own. This would require that the multisignature algorithm allowed for two single signatures to be combined by a third party who has neither private key -- e.g., it would need to support [general signature aggregation](https://crypto.stanford.edu/~dabo/papers/aggsurvey.pdf). Each validator receives k messages and sends out 1 message, and the process is complete after log_k(D) steps. High values of k can minimize the number of steps and the consequent latency. 

If not all validators sign the message, then the set who did can be described either as a bit array or some other more succinct method utilizing the tree structure (e.g. "B and all descendants" can be more succinct than a bit array if B has a large number of descendants).

This process of aggregating signatures among validators could happen in between block publishings. Each block could have a single multisignature from e.g. 10,000 validators betting that statement H is true or else p percent of that validator's stake is forfeit, with subsequent blocks using a higher value of p. Signatures in block N+10 might attest to the validity of block N with 100% at stake, block N+1 with 50% at stake, block N+2 with 25% at stake, etc.

In this arrangement, we could have finality after maybe 10 blocks, with a block time on the order of log(D) one-way message times (i.e. roughly the same as the block propagation latency), and each validator sending a small constant number of messages per block.
Ethereum does this, and it seems to work. With Ethereum, each block can vote either to increase or to decrease the "gas limit" (which is the Ethereum equivalent of a block size limit) by a small amount. These votes can either be manually specified by the miner with a command-line option to set a target gas limit, or they can be automatically determined by the miner's software based on how full recent blocks were.

There is no technological obstacle to what you are asking for. It would be politically difficult to achieve with Bitcoin, though, as a substantial minority of people do not want the block size limit to be increased at all.
No, these transactions were created to simulate a post-SegWit spam attack. They are not typical or representative.
Bitcoin Unlimited has a superior block propagation algorithm called Xthin blocks which allows for much larger block sizes before the orphan rate incentive for centralization becomes significant.

https://medium.com/@peter_r/towards-massive-on-chain-scaling-block-propagation-results-with-xthin-792a752c14c2#.jk7x7d651
Thanks, dgenr8, for keeping XT going. I might switch my p2pool nodes (~400 TH/s) over to use this, although the performance of the 0.11 branch is a disincentive.
4 MB is a little bit sketchy without the relay network. Might be okay, might not; depends on what you think "okay" is. I personally think that 4 MB would be okay, but there's too much personal interpretation in that statement for me to be able to say that 4 MB is "safe". "Safe" is pretty hard to define.
http://imgur.com/8srRO4p

The OP was made 3 hours before this screenshot was taken. 
Until the fork happens, Bitcoin Classic and Bitcoin Core will follow the same blockchain, so you can make spends from either one.

After the fork happens, no matter what, Bitcoin Classic will follow the longest chain. That means that Bitcoin Classic will use whatever blockchain branch has a majority of hashpower behind it. On the other hand, Bitcoin Core will use the branch with the smallest blocks. Consequently, the safest thing for users to do is to upgrade now.

If you spend old money with Bitcoin Classic after the fork, it will always be a valid spend on the 2 MB branch, and it will usually also be a valid spend on the 1 MB branch. If you spend money with Bitcoin Core after the fork, it will always be a valid spend on the 1 MB branch, and it will usually also be a valid spend on the 2 MB branch, although it might not ever get confirmed on the 1 MB branch due to a lack of hashrate (slow block rate) and congestion.
No, SegWit provides about 1.75 MB of actual capacity, assuming 100% adoption of SegWit and a transaction mix (P2SH multisig vs P2PKH) that reflects the current mixture. The 4 MB number is what happens if you fill a block with 100% 15-of-15 multisig transactions -- that is, 4 MB is what you can do if you're specifically making your blocks to try to attack the network. That means that with SegWit you get the negative network security effects of larger blocks without most of the capacity of larger blocks.

> Also, why is 4 better than the 8 that is in BIP101?

Large blocks take longer to propagate, especially across the Great Firewall of China. 8 MB blocks are likely to strain block propagation more than 4 MB blocks.
An effective *capacity increase*. 

I think that in the interest of not confusing people, we should try to avoid using words in a fashion that is ambiguous. "Blocksize increase" is usually used to refer to an increase in the MAX_BLOCK_SIZE constant, which SegWit is definitely not. It may also be construed as a way of describing the sum of the header size, the transaction merkle tree size, and the merged-mined witness data merkle tree size, or to describe the transaction capacity, as you seem to be doing; however, it can be argued either way whether those uses are covered by the term, and I think that doing so is counterintuitive. In the interest of not confusing people, I think it is better to use words in which nobody gets confused. 

In particular, I think it was clear given the context that /u/Bitcoinopoly meant a MAX_BLOCK_SIZE increase, not an "effective blocksize increase." This is probably why he said "blocksize increase" and not "effective blocksize increase." It is also desirable to respond to the terms and the meaning that people actually use rather than the terms and the meaning that you think they should be using.

This is the **[second instance](https://np.reddit.com/r/Bitcoin/comments/3yqe7c/segregated_witness_still_sounds_complicated_why/cyhtle8)** in 24 hours in which I have seen you use words in a counterintuitive way. In both instances, it seems as though these uses of terminology may have been intentional and ideologically motivated. If that is the case, please stop. Let's try to come together and try to speak the same language, otherwise we will never be able to achieve consensus.
> Miners can choose the acceptance blocksize themselves. This may or may not work, but it's incorrect to say BU is an amplification of XT's one-size-fits-all forcing of larger block acceptance.

It is not simply an amplification of BIP101's controversial features, but it does amplify many of BIP101's controversial features.

Miners choosing their own acceptance blocksize means that you're playing the [Keynesian beauty contest](https://en.wikipedia.org/wiki/Keynesian_beauty_contest). Now miners no longer have a hard limit on the size of the block they need to engineer their networks to be able to handle in a timely fashion. Instead, they have to guess what blocksize 51% of the network might be willing to create, and they have to engineer their networks accordingly, and they also have to choose a blocksize limit that they think will put them on the majority side lest they lose out on orphan races. This new game theory may result in even stronger hashrate centralizing forces than exist with big blocks alone. It also removes the weak protections that BIP101 has on total blockchain size and initial block download (IBD) times, and may make it very expensive to run a full node.

Miners are conservative and fearful, and I think that BU reduces the amount of security guarantees that miners and non-mining full node users are given. As such, I do not see it gaining enough support to be feasible. 

Whether I think BU is a good idea or not is irrelevant. I am trying to find miner consensus right now, and I do not think that BU has enough of a chance of being that consensus to be worth the time and social capital it would cost me to ask the miners about it.

I think BU has some potential as a long-term solution to scaling Bitcoin. However, it is even less viable as a short-term fix than BIP101 was. I think that we need to be thinking in the short-term right now. After we've done one hard fork successfully, we can work on a more permanent solution.
Yes, it is missing. I was doing most of this census too soon after it had been proposed and hadn't had time to digest it myself. I didn't think the miners had had time to digest it either.
I had some conversations with a few of the miners, and at least BTCC and Bitmain/AntPool are sympathetic to much larger increases in block size in order to increase transaction fees and make mining pay for itself. However, they want to see performance fixes first. Both BTCC and Bitmain are interested in hiring some developers to help with performance fixes.
> when /u/jtoomim[2]  tested 101 on real networks he concluded 3-4MB was max safe with todays network. this real data that the miners have been saying for a long time is why now people are focusing on network data validated proposals like segregated witness, and 2mb or 248 or what /u/rustyreddit[3]  said below.

No, that is not what my conclusion was. I concluded that 3-4 MB was safe, but I did not say that larger than 4 MB is unsafe. Also, my testing was for a near-worst-case scenario in which there was no relay network. My early testing (before we fixed a network receive buffer size bug) showed about 10 seconds for a 1 MB block, and the actual network is running at about 4 seconds for a 1 MB block. Adding the relay network and nothing should bring the times for an 8 MB block down to about 2 seconds transit plus 3 seconds verification, which I think would be acceptable.

The main conclusion of my testing was that my nodes in China were far slower than the nodes elsewhere, and collapsed above around 3 MB. However, talking with Chinese miners and pool operators since then has taught me that my Chinese nodes were not configured in a way that reflects actual mining in China. The Chinese pools and miners have already implemented a lot of proprietary techniques for crossing the GFW. F2pool uses block prepublishing, "SPV mining" (VFM, verification-free mining), and a few other techniques. BTCC uses limited VFM (one block only) and a few other techniques. Antpool uses UDP and a few other techniques. If my tests used these techniques, we probably would have seen tolerable performance up to 8 MB. But we didn't.

I'm not sure I like a scenario in which everybody has to use Matt Corallo's relay network just to get tolerable performance on block relay. The decision to not use RN was a conscious one. I was testing safety in the absence of the RN. I was not testing economic viability, which is better done with the RN. Without RN, outside of China, 9 MB blocks were safe, but slow (up to 20 second propagation times).
No, less. A twitter message can only be 160 bytes. That's all I see here. 
It doesn't save you any bandwidth. You still have to download the witness data in order to fully validate. It's mostly just an accounting change that allows for 1.75 MB of data to be counted as 1.0 MB of data.

It has the potential to improve HDD space usage by about a factor of 2x, but that's not the bottleneck right now.

The main reason why it's being pushed right now is that it allows for a block size adjustment to be performed as a soft fork instead of a hard fork. In my opinion, we need to get over this fear of hard forks.

Also, segwit makes for a very ugly soft fork in my opinion. As a hard fork, it sounds kinda cool and may be worth supporting (depending on the implementation). As a soft fork, it's awkward and does stuff I really don't like, such as breaking the validation of all non-upgraded nodes, severely restructuring transaction data in a way that requires all existing non-Core bitcoin software to be rewritten, and making use of a data field (the coinbase message) which is already overloaded and should be left for miners to use.
It's currently being compiled by Mike and Gavin. It should be released soon.

We had a last-minute bug that needed to be fixed (PR 103), so that delayed the release process.
The Bitcoin Foundation is dead. I'm glad to see that 21 is stepping in to support Bitnodes in their absence.

Edit: The Bitcoin Foundation is not quite dead. However, they are limited in resources and may not be financially viable.
This block was mined at the Toomim Bros Bitcoin Mining Concern's XT p2pool node. 

http://toom.im
> Isn't this similar to what people that support small block sizes have said about BCH?

Yes, the criticisms that small-blockers have incorrectly levied against BCH are actually appropriate against BSV. 

BSV's strategy is to just increase the blocksize blindly without doing any significant engineering in advance to make sure that blocks that large are feasible and safe. 

BCH's strategy is to do the engineering first, and only increase the blocksize limit when safety has been demonstrated.

> but it still shows what's possible

The Bitcoin Unlimited team showed in 2017 that 1 GB blocks are possible, but that we need to fix block propagation before they're safe. BSV's tests haven't added to that knowledge yet, as they're still struggling to do 1/8th that size.
Tests with Graphene v1 showed that [86% of Graphene's data](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/e58k9wu/) was order information which was removed by CTOR. Those numbers may have changed since Sep 1st, though.
Are you referring to the 0.7/0.8 fork? That was 24 blocks, though, and caused by a consensus bug rather than bad block propagation.

https://github.com/bitcoin/bips/blob/master/bip-0050.mediawiki

https://bitcoinmagazine.com/articles/bitcoin-network-shaken-by-blockchain-fork-1363144448/

(Incidentally, the latter article was written by Vitalik in his pre-Ethereum days.)
I disagree entirely. Greg is often wrong, but when he's right, it's usually in an insightful way. He doesn't follow BCH stuff very closely, so he has a lot of misconceptions about BCH-specific stuff, and he also has a very different philosophy for Bitcoin, but on the other hand he knows a lot of specifics about how Bitcoin works. I always value his input and consider it carefully, and I think most other people should too.
Maybe you should have mentioned that, then? E.g. "daily tx per block"
Metcalfe's law applies to the number of users, not the number of transactions. This transaction rate increase is probably due to shuffling increasing the number of transactions per user per day, so the number of BCH users is probably below LTC's.
I have only touched BSV to sell it and to satisfy my curiosity regarding their network's performance. 

That said, I do not begrudge other people the right to use it for whatever they want. Permissionless innovation, dawg.
Several of BU's pro-BSV members voted against approving Jonathan Silverblood as a member because he was predominantly pro-BCH.

https://twitter.com/monsterbitar/status/1086006411149099008

It appears that both sides of the BCH-BSV split are concerned with potential attempts to pack membership with the other side.
Can't fork BU's wallet.
> when someone, somewhere demonstrates a scalable blockchain that won't bottleneck or fall over under load that we can make a real difference for humanity.

I find this to be a legitimate motivation to continue work on Xthinner and (especially) Blocktorrent, and it's one of the things that remains after pruning off the tribal warfare mentality. One of my personal goals is to make mining 1 GB or larger blocks feasible on $500 hardware. I think we're not that far from achieving that goal. Blocktorrent plus a bit of multithreading will probably be enough.
Please re-read the question:

> what's the largest sum that can be send on LN **now?**
Finalization and checkpoints are not the same thing.

Checkpoints are where a developer inserts a hash of a block to force it to be part of the chain. It's entirely under developer control. 

Finalization is where any block can be finalized once it has 10 blocks built upon it. It's entirely under miner control.

> BuT BcHaSh hAs cHeCkPoInTs EvErY 10 BlOcKs

Also, ABC finalizes *every* non-orphan block. Finalization happens after 10 blocks, not every 10 blocks.

BTW, /u/pyalot, it was predominantly BSV trolls (lke edoera below) who were criticizing BCH's checkpoints/finalization, not "BSCore" trolls.
It was a self-fulfilling prophecy.
> I'm not sure if I understand this completely, but it seems like the way this works is that it gives a large POW penality to even a single block reorg?
> 
> How does this deal with accidental orphans, that happen all the time on the network?

The PoW penalty comes as a multiplier on the additional work done on that chain since the most recent common ancestor with the active chain tip. If there are two blocks which share a parent, then the first block seen wins. This is the scenario seen in the case of accidental orphans, and this is the same result as the current algorithm uses (first-seen is already a tiebreaker for equal-PoW chains).

If a block is published at least 120 seconds after its brother, AND if that block gets a second block mined on top of it, then the punishment starts to kick in. If the latecomer gets a second block mined on top of it, miners will still not follow the late 2-block chain and will prefer the early chain, at least for a while. The later that block was, the more miners will resist switching over to the descendants of the late block.

This 120 second number is a tunable parameter, and is intended to represent roughly the 90th %ile of propagation times for a max-allowable-size block. While I think 120 seconds is plenty, if I'm wrong about that it can be increased without harming the algorithm's effectiveness too much.
They look at the economic activity on the various options, the exchanges supporting them, and the exchange rates for coins on each one. The same way any other chainsplit is decided.
j at toom dot im will work. I'll make a separate post asking for data and metadata, but it will be something similar to what I described [last time](https://www.reddit.com/r/btc/comments/9cp2av/please_send_me_your_full_node_debuglog_files_from/).
Forking off cleanly was the original intention. Bitcoin ABC 0.18 had replay protection against Bitcoin ABC 0.17 after the Nov 15th fork. However, Bitcoin SV [intentionally removed](https://github.com/bitcoin-sv/bitcoin-sv/commit/446b66ec3555f1e1e304c3f8a93a3e893896355b) this replay protection because CSW and nChain *wanted* a messy split.
I've already written the scripts for logfile analysis. I can easily run those scripts again on new data.

I will ask for logfiles from people. Please install systemd-timesyncd or ntp on your machines before the test, and ensure that your clocks are synchronized before the test.
The blatantly copied text from Planetmath was this:

> Starting from the simplest primitive recursive functions, we can build more complicated primitive recursive functions by functional composition and primitive recursion. In this entry, we have listed some basic examples using functional composition alone. In this entry, we list more basic examples, allowing the use of primitive recursion:

Note the phrase "In this entry." That phrase is referring to the Planetmath entry, "examples of primitive recursion." That's the Planetmath equivalent of saying "in this Wikipedia article." That phrase makes no sense in the context of Craig's paper, and yet we find it there. It also makes no sense in the context of a textbook.

It seems you're alleging that CSW's direct (and incomprehensible) word-for-word copying of a paragraph from a webpage is excused by the fact that he cited a math textbook. That argument is not valid.

/u/cryptosword
Okay, word-for-word plagiarizing of a legitimate website that wrote its own unique formulation of concepts that were generally known at the time *is still plagiarism*.

If you copy someone else's writing word-for-word, and you don't put it in quotation marks and denote the source, *it's plagiarism*. CSW did exactly that. Planetmath did not.

If you copy concepts that aren't immediately recognizable as being general knowledge, and don't cite your sources, that is either plagiarism or (depending on the circumstances) sloppy scholarship. Planetmath is probably guilty of the latter in this instance.
No, this alone does not eliminate ordering info. Let's say you have the following two different dependency chains ("->" means "depends on" -- A->B means that A spends one of B's outputs):

A->B, 
B->C , B->D
C->E
B->F

G->F

Which transaction do you include first, E or F? After you include F, do you include G or do you include B?

You need to have additional rules to specify what happens at branches in the DAG. Basically, you need to specify a single algorithm for [topological sorting](https://www.geeksforgeeks.org/topological-sorting/) of blocks that everybody uses if you want to use a TTOR-compatible CTOR.
> the minority is free to go ahead and change their PoW or change their consensus rules how they wish.

Yeah, why didn't BCH change their PoW when they changed the consensus rules? Damn that hostile minority! BTC miners really should have 51% attacked that chain and prevented it from ever existing.
That Skynet guy sure likes to hog the conversation.
https://isitgoingtorain.com/

https://doineedajacket.com/
> The loss of competition is a huge loss.

No, it works the other way around. This is a problem of coordination. If everyone is trying to do their own thing, then it doesn't work. If we're all using different block orders, then none of the block orders will get fully optimized for because we'll all have to write code that works acceptably for all block orders instead of focusing on making code that works optimally for one block encoding.

LTOR has by far the most reported optimizations available for it. The most commonly cited alternative [does not come close](https://www.reddit.com/r/btc/comments/9cq62n/during_the_bch_stress_test_graphene_block_were_on/e5zweh2/).

> And I dont't know why you always call it 'graphen'

That's how it's spelled in German, and probably a few other languages. I'm guessing Ant-n is not a native ~~Englisch~~ English speaker.
This post is just describing the idea that later became p2sh, as far as I can tell. But I just skimmed over it.
> Every block received will result in 1000 messages to light wallets per node, or 1000 times the activity. Yet this activity is not addressed in the proposal.

> In Chancellor’s proposal the UTXO set is sharded by transaction id, which has no relationship to addresses. So how to create a MERKLE_BLOCK message?

Responding to SPV nodes does not require UTXO lookups. It just requires the raw block data with no database. You will want to have the full block in RAM if possible, but that's it. 

I envision a full node will be composed of a front-end server which handles the block parsing and work partitioning, followed by a layer of UTXO cache shards, which in turn will be followed by a layer for some sort of non-volatile UTXO database storage (possibly overlapping with the UTXO cache nodes, possibly separate). Merkle block requests would be fulfilled by the server in the front. Different full nodes could advertise caching of different blocks, so an SPV client could connect to 5 or 20 different nodes and get one merkle block from each, thereby avoiding any disk reads for fulfilling the merkle block requests.

Some more info on how to handle SPV requests efficiently:

> SPV traffic is pretty easy to serve from a few high-performance nodes in datacenters. You might be thinking of Jameson Lopp's article a year back. He assumed that each SPV request requires reading the full block from disk just for that one request, and that's not at all true on a protocol level, although it currently is true on the implementation level. You can have different nodes that keep different blocks in RAM, and shard your SPV requests out among different nodes based on which blocks they have cached. These nodes can also condense several different SPV requests into a single bloom filter, and use that one bloom filter to check the block for relevant transactions for 100 or 1000 SPV requests all in a single pass. It's really not going to be that hard to scale that part. Home users' full nodes can simply elect not to serve SPV, and leave that part to businesses and miners. We professionals can handle the problem efficiently enough that the costs won't be significant, just as the costs per user aren't significant now.

[Source comment](https://www.reddit.com/r/btc/comments/9b3827/the_gigablock_testnet_showed_that_the_software/e52xqap/)
> I like that language. That sounds pleasing to my ears. I don't know what it means, but it sounds good.

His Twitter handle is @ProfFaustus. A Faustian bargain is where someone sells their soul to the devil in exchange for money, power, fame, or other material success. By using that phrase, my claim is that Craig has no morals and ethics beyond his own greed and ego, and is willing to do anything in pursuit of material success. Including trying to patent Ethereum after it was released. Including threatening Bitcoin ABC with an obviously frivolous lawsuit based on an invalid pending patent.

I do often use flowery or figurative language, but I try to always use it in a way that has real meaning behind it. Feel free to ask for clarification if you find anything I write to be unclear or obtuse. I'm happy to explain.
For one: https://youtu.be/LDF8bOEqXt4?t=4102

For two: I've been collecting debug.log files from users from the stress test, and am starting to analyze them for block propagation information. For one block, 00000000000000000069c3ad902b27621493eea7c3504d9a7a279b04c9dad565, a 15 MB block early in the test, the last of the 9 nodes in my dataset received and finished processing this block at 14:44:58, but the first node received and finished processing at 14:43:20, a difference of **98 seconds**. 

**Edit:** I think this 98 seconds may have been a side effect of the ATMP bottleneck. Nodes will have poor block propagation performance if they don't have all the transactions in mempool already. With the ATMP bottleneck, many nodes will be missing some transactions. The ATMP bottleneck in turn is likely to have been mostly on nodes that were using an HDD instead of an SSD for the chainstate (UTXO db) storage. I will be looking for evidence of these hypotheses soon. /edit

The last node to receive that block only took 2.38 seconds to verify it, whereas the fastest node (one of my own, as it happens) took [1.2 seconds](https://www.reddit.com/r/btc/comments/9c3kud/new_record_15247mb_block/e58ajn2/). This means that networking issues are the most likely explanation for the delay, not slow CPU processing.

On the other hand, a much smaller block, 00000000000000000171f9472741feadd7c45a313daabd90fadb5815a602324c, showed all but one of the nodes receiving it within a 10 second span, with the last one receiving it 22 seconds after the first.

I hope to publish some more formal analysis in a few days. In the mean time, [please send me your full node debug.log files from the stress test](https://www.reddit.com/r/btc/comments/9cp2av/please_send_me_your_full_node_debuglog_files_from/), and be sure to mention if your system clock has any sync issues.

Block propagation was not limiting the transaction throughput, though. That was being limited by AcceptToMemoryPool. The issue with block propagation is that high orphan rates can break the decentralization incentives for mining, and encourage the formation of large superpools. (You never have to propagate a block you mine to yourself, so if you control a large portion of the hashrate in one server, you get lower orphan rates.)
Yeah. I wish I had time to go into Flowee's code and [do what I've been doing](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244) to [ABC's code](https://github.com/jtoomim/bitcoin-abc/commits/openmp_connectblock). I think it would help calm him down if I could get OTI to mirror or surpass his Flowee block validation code. It should be possible, but too much on my plate as it is.
Yay, p2pool shows up on that graph! :) :) :)

That's me, 0.69% baby! So nice to finally get the recognition I deserve.
>  Propagation is not the bottleneck for the system. Sequential processing during block validation and block creation are the bottlenecks that form the critical path.

This claim runs contrary to all of the data that I have seen and collected. 

With Xthin outside of China, block propagation runs at a rate of approximately [0.6 sec per MB](https://youtu.be/LDF8bOEqXt4?t=4617). For a 15 MB block, that's about 10 seconds. When crossing the China border, block propagation is about 1/4 to 1/10th as fast, so block propagation there would take about 40 to 100 seconds.

Early test results suggest that Graphene without CTOR is about [8x as fast](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/) as Xthin, and would save between 8 and 88 seconds. Adding CTOR would make Graphene about [7x as efficient](https://www.reddit.com/r/btc/comments/9c3wts/graphene_holds_up_better_than_xthin_during/e58k9wu/) as that.

In short, Graphene can transmit the whole block before Xthin can transmit even 1/8th of the block.

Block validation on my node using sequential processing took [1.2 seconds](https://www.reddit.com/r/btc/comments/9c3kud/new_record_15247mb_block/e58ajn2/) for a 15 MB block. Block template creation is about twice as slow as that, and takes about 2.4 seconds. That's about 3.6 seconds total.

So Graphene would save 3x to 30x more time than serial validation and template creation take overall. Even if Graphene prevented further optimizations to validation and template creation -- which I really doubt -- it would still be a huge win.
you're unstrung

from the fiat rat race;

wanna grab that block's coinbase!

What the block explorer's sharing

I'm hooked and I can't stop staring

-

Oh baby, I wanna go spend ya

And get paid with ya

My homeboys tried to warn me

But those blocks you got

Mm-m-make you sound money
    
-

A 0-conf transaction, 

You say you want some coffee beanz?

Well use me, use me, and pay my itty bitty miner fee
    
-
    
I turned [coin.dance](http://cash.coin.dance/blocks) on

To hell with inflation

BCH's set, a hot bet, gonna buy me a turbo 'vette
    
-

I'm tired of [magazines](https://bitcoinmagazine.com/)

Saying small blacks are the thing

Take the average cash man and ask him that

Time to pack up, Back.

-

So BCHers (yeah) BCHers (yeah)

Has your girlfriend got fiat?

Well change it (change it), change it (change it), change it to Cash next block.

Bitcoin's come back.

- Sir Makes-a-Lot
I addressed most of these issues in [this post](https://www.reddit.com/r/btc/comments/9bc3k4/a_critique_of_awemanys_critique_of_canonical/e52zu1s/) and [this post](https://www.reddit.com/r/btc/comments/9b3827/the_gigablock_testnet_showed_that_the_software/e52xqap/).

Tom, I'm afraid I'm going to have to duck out of the conversation at this point. I need to try to get some p2pool code written before the stress test ends. Nice chatting with you, as usual, and perhaps we can pick this back up later.
And if anything, the price going up is nice mostly because it represents a vindication of our technological strategy.
https://xkcd.com/386/

Gavin's observation was technically correct in that the code is multithreaded with a single main lock (mutex), and he felt compelled to share that technical correction. However, the end result of being multithreaded with one main lock is that the code runs pretty much the same as if it were singlethreaded.

Yes, there are plans to reduce the amount of code that is locked by cs_main to running in only one thread at a time. There are also plans to rewrite more sections of the code as multithreaded stuff. 

Maybe calling them "plans" is a bit of an understatement. It's getting done as we speak. Last night, for example, I published a [multithreaded version](https://github.com/jtoomim/bitcoin-abc/commits/openmp_connectblock) of the basic block validation algorithm. That version still has a bunch of locks in it, though, so for now it's actually slower than the single-threaded version, but once I rewrite it to use a lock-free hashtable, performance should get much better.
Thanks, a nice first attempt!

One:

I suspect that the size of the effect might depend on the transaction generation algorithm and how the spends are distributed over txid space such that this might be better to run with real block data rather than data that you generate yourself. That might be too much work to do at this point just to prove a point, though. Do you have a high-level description of the generation methodology, or should I just go read the code in more detail? (I see an exponential distribution versus utxo age mentioned in the code, but I haven't dug into this much. I suspect that this might overrepresent chains and re-spending of transactions.) In my very brief analyses of BTC's transaction flow, I noted about 12% of transactions had an in-block dependency, which is much lower than the number you seem to be reporting for your exponential model.

Two.

It looks like your global writes number is much higher in the lexicographic order than it should be. As I understand it, it should be easy to have an algorithm for lex order that only commits to the disk UTXO db if an output is not spent in that block. 

Furhermore, for the cached results and lex order, it should also be feasible to avoid maintaining a global UTXO cache overlay entirely. Inputs refer to outputs by their (txid, out_index) pair. In lex order, worker threads get assigned particular txid ranges, which means that their writes to the UTXO db will be sequential (with skipping). This means that input-checking-workers can know a priori which output-inserting-workers *could* have the txid in question, and can poll only that output worker for that UTXO. If the global/permanent UTXO DB is also sharded, then the UTXO cache layer and the perm disk layer could be sharded the same way with the output-inserting-worker doubling as a utxo-db-fetch-worker. Instead of implementing a global cache, you can just do a sharded cache with IPC between workers instead of having a shared memory cache or a separate database server.

Alternately, you can create a global uxto overlay by taking the individual output workers' generated overlays and simply concatenating them. That's O(1) for many cache datastructures, including some hashtables. (The local caches end up as the buckets of the hashtable, so you're just updating a top level array of pointers to the buckets.)
The issue is that there was a bug in how gas was calculated for certain opcodes, and around October of 2016 someone found this bug and exploited it with spam, causing about 1 month's worth of blocks that take a very long amount of time (about 2 weeks?) to process. Ethereum was hard forked to fix those opcodes' gas prices, so blocks before and after that spam window take a reasonable amount of time to process.

If you want to sync a node from scratch, without using the state root trie hash commitments, it's possible to do so. However, you will need to use a good SSD and increase the amount of RAM that parity or geth is configured to use as cache in order to make it through the attack period, or else it will take much longer than two weeks to get through those blocks. Parity seemed to work a bit better than geth, so I suggest that.
Actually, it's more that of the 330 GB, 300 GB is extraneous information that pretty much nobody needs. You don't need to store the state of every account at every point in history *in addition* to every block. Just storing the historical blocks is enough. When syncing, even in slow mode, you don't download that 300 GB; it's generated locally during the sync process.
The goal of NYA is to get one blockchain that has both increased capacity and enough users to make the capacity increase useful.
No, the first Bitcoin Cash pizza transaction happened in 2010.
When a miner has learned that a new block has been published to the network, and has been informed of that block's hash, but has not yet downloaded the full block, they have the ability to start mining a new block on top of that block (since the new block only needs to reference the parent block's hash). However, until they know which transactions were in that block, they are unable to include transactions in their new block in case one of the transactions they include conflicts with (or is the same as) a transaction from the old block. This practice is known as headers-first mining, SPV mining, or spy mining.

You'll notice that both of the empty blocks happened less than a minute after the previous block. This indicates that the miner had had enough time to download the parent block's header (and verified the hash and PoW) but not the full transaction list.
Click "Enable weighting" -- most of the companies that oppose Segwit2x are tiny.
It's only anyonecanspend before SegWit. After SegWit, it's see-attached-witness-program.

SegWit is a soft fork. Reverting SegWit is a hard fork. If 51% of miners decide to hard fork, it only works if the full node that you're using or connected to also decides to follow the hard fork.
Litecoin has a 4x faster block interval, which means that 1 day's blocks on Litecoin is equivalent to 4 days' blocks on Bitcoin, and consequently has half as much variance as a 1-day estimate in Bitcoin would.
Every node running the p2pool software will show that block. You can click on any node in this list (within the next 20 or so hours) and see that block:

http://poolnode.info/

For example (and so others can see after the 24 hour "Recent blocks" list expires), this random node also shows that block: http://imgur.com/a/uY9ln

With p2pool, one node actually mined the block, and all of the other nodes are just helping to relay the block.

I repeat, that block was not mined by p2pool.org. It was mined by someone else using p2pool. Some p2pool miners are running Bitcoin Core; others are running Bitcoin Unlimited, Classic, or XT.

(p2pool.org's own hashrate is only 82 TH/s, compared to around 2340 TH/s total among all p2pool nodes and miners.)
With SegWit, the only part of a transaction that can go into the extension block is the signature. While signatures are large, they only comprise about 60% of the total size of a transaction. The remaining 40% goes into the normal block. This means that the 40% of base data can never exceed 1 MB without a hard fork.

SegWit currently discounts signature data by a factor of 4. A typical SegWit transaction therefore costs `(0.4 + 0.6/4) = 0.55` as much as a typical non-SegWit transaction. This means that a block full of typical SegWit transactions (and nothing else) can be `1.0 MB / 0.55 = 1.82 MB` in size. Of that 1.82 MB, 40% (or 0.72 MB) is in the base block and 1.09 MB is in the SegWit extension block.

If you change the SegWit discount to 0, but still using the 60/40 mix, you could get 2.5 MB of total data with typical transactions, with 1.5 MB in the SegWit block and 1.0 MB in the base block. 

However, using a SegWit discount of 0 is a *really bad idea*, since it's possible to make malicious transactions that have an arbitrarily large amount of SegWit signature data. This means you could make a transaction that takes up only 200 bytes of base block space, but uses a bazillion gigabytes of signature data, all for the same fee as a 200 byte non-SegWit transaction.
It looks like you're spending mining earnings. 

Your transaction has 25 inputs of around 3 mBTC each all from two addresses (your mining address plus your change address). At current fee market rates, it costs about 0.15 to 0.3 mBTC to spend each input, which means that you're going to end up spending 5-10% of each input on fees if each input is only 3 mBTC. 

You should configure your pool to use a higher payment threshold, like 10 mBTC or higher. This will make you get payments less frequently (about once every 10 days instead of once every three days), but you will spend a lot less on fees when it comes time to actually spend your earnings.
> It's being alleged that bitmain is not selling to anyone who doesn't support Bitcoin unlimited not that they're not getting a discount.

That allegation can be disproven by [visiting their website](https://shop.bitmain.com/main.htm?lang=en) and placing an order.
144 blocks is too short a time window. It's just variance that makes it look like BU is on top of SegWit in terms of support.
The current type of global pruning (all but the UTXO set) is default-disabled. The signature-only pruning (keep all TXOs and blocks and skip signatures on IBD) has not been enabled yet AFAIK. I don't know if it is planned to make it enabled by default, but I thought that was the intent. Until some form pruning is enabled by default, these kinds of spam attacks will use more storage than current spam attacks do. Afterwards, they will use less storage, but they will still use more bandwidth.
On Monday, I sent four transactions totaling over $100,000. Total fees paid were about $3.00.  After 4 hours, the transactions still had not confirmed. The vendor sent me an email informing me that they had failed to process the payment, and that if I didn't pay within 24 hours they would cancel the orders. All four transactions confirmed about 7-8 hours after they were sent, in the middle of the night.

(At around 30 sat/byte, the fees were a little on the low side given the transaction kb size. My wallet can not automatically set the fee rate, and was manually set to a fee rate that was reasonable a week ago.)
This article is an astroturfing maneuver intended to make it easier for MGT to raise money. 

As a miner in Washington State, I can tell you that there are several mines within 100 miles of me that are of similar size or larger. Most of them prefer to remain anonymous and secret because they're privately funded, and therefore not looking for funding from the public.
Part of the consensus was that some members of the Core team would write 2MB HF code. That hasn't been done.

Also note that there were only a dozen or two people in that room. They do not comprise 100% of the hashrate or 100% of the developers.
>It is no different than using an Intel or ARM chip, or a GPU card.

It is different, because this is a patent that applies only to Bitcoin mining, and to no other application. With h.265 patents and GPU video decoders, for example, the IP holder has a strong incentive to keep rates low, since GPUs are general-purpose devices and would simply exclude the h.265 hardware encoding/decoding block from their design (especially in entry-level models) if the licensing fees were high. With Bitcoin mining and AsicBoost, the IP holder knows that 100% of the ASICs will be using the algorithm 100% of the time.

With AsicBoost, the patent holder can charge whatever they want for the licensing fee. They could e.g. give preferential rates to companies they like, or could refuse to grant a license to companies that refuse to insert government backdoors into their chips. 

If SHA256 had been encumbered by patents, then Satoshi would have used a different hash function. Now that the Bitcoin mining algorithm has become encumbered by patents, there is a good reason why a different mining algorithm could be desirable. That doesn't mean switching the algorithm is the right thing to do, but it's worth thinking about.
(I don't actually think the OP was responsible for the dip. I just thought the correlation was amusing.)
Port 53, UDP. DNS amplification attack. 
>There are a few reasons \[why the relay network doesn't make large blocks as safe as you'd hope\]:

> 1. The relay network is not reliable. It is not part of the actual reference implementation of Bitcoin, and requires on external servers that are currently run by one person. That person has even **[expressed intent](https://bitcointalk.org/index.php?topic=766190.msg13510513#msg13510513)** to stop supporting the network soon.
> 2. The relay network is not scalable. It has substantial per-peer memory requirements that would cause it to cost a lot more money to run if it were used by more than just miners.
> 3. The relay network does not work in adversarial conditions. If a miner wants to perform slow-propagation-enhanced selfish mining, it is trivial to make blocks which the relay network cannot accelerate. All the miner has to do is to mine blocks with unpublished transactions, such as spam that the miner himself generates. In this case, the relay network needs to transmit 1 MB of data for a 1 MB block, rather than just 4 kB. The relay network only works well in cooperative scenarios.
> 4. (a) Since it uses TCP, the relay network has some trouble crossing the Great Firewall of China quickly and efficiently due to packet loss. (b) Since it is (mostly) not a multipath system, it cannot route around one or two high-packet-loss link very effectively.

> Note that 3 and 4(a) also affect Xtreme Thin Blocks just as much. 

> How important these reasons are is up to interpretation. I personally think that even with these shortcomings, with the relay network, blocks up to 8 MB are *probably* okay (though I don't have firm data on this), and without the relay network, blocks up to 3-4 MB should be fine. 

> However, I recognize that these issues are real. That's why I'm working on Blocktorrent. It should address all of these issues quite effectively.

https://np.reddit.com/r/Bitcoin/comments/47quzx/xtreme_thin_blocks_in_action_getting_rid_of/d0gac85
You can see the cyclic behavior a bit in the [Transactions Accepted Per Second](http://statoshi.info/dashboard/db/transactions?from=1455762158863&to=1456366958864) graph on statoshi.info.

It looks like there are actually two humps per day.
> Democracy is where 51 percent rule over 49 percent. Democracy != Freedom. Stop with that nonsense.

That is exactly how the blockchain works. A 51% hashrate majority can do almost anything it wants. (Also note that I did not mention freedom.)

However, I don't think we're going to use a simple 51% threshold for most things. We intend to use a threshold that varies based on the number of people voting in order to account for statistical uncertainty in the sampling due to bias and insufficient sample sizes. When it's only a few people voting, we'll require an overwhelming majority in support. When there's disagreement in the initial voting, we'll wait for more users to vote.

We specifically want to avoid deadlocks on controversial issues due to vocal minorities exercising vetoes. We think that the **[liberum veto](https://en.wikipedia.org/wiki/Liberum_veto)** has been toxic for Bitcoin so far and want to abolish it. 

We want to exorcize vetoes, not exercise them.
I think a hard fork can kick to 2-4 MB is a better idea. I think that will buy us time to fix block propagation, and will give us some experience with rolling out hard forks that will make a longer-term fix easier for everybody to accept.
On the other hand, when this was tried against Coinbase, Coinbase shrugged it off pretty easily. Anti-DDoS technology for websites is pretty mature and robust, and exchanges have a lot more money than mining pools. BTCC is mostly an exchange, like Coinbase. I don't think they're going to have much trouble.
I think that miners do not and will not support Bitcoin Unlimited. Consensus does not exist for BU, so I didn't bother asking about it. The purpose of my census was to uncover existing consensus or near-consensus.

Bitcoin Unlimited takes the things that were controversial about BitcoinXT and amplifies them. It is the opposite of a compromise. I think that choosing not to compromise right now is a recipe for political deadlock and making the 1 MB limit remain. I do not support the 1 MB limit. 

I also do not support ignoring the explicit requests of a supermajority of miners. Bitcoin Unlimited does that.

Whether I personally think that 8 MB blocks are too large right now (I don't) or whether the blocksize limit is actually necessary (I'm skeptical) is irrelevant. This is not about my opinions. This is about the miners' opinions.
Among other things, yes. These proposed fixes include block propagation improvements like IBLTs, blocktorrent, and built-in versions of Matt Corallo's RN algorithm. They also include fixes to the mutex/lock problems that Core currently suffers from which makes most of Bitcoin Core effectively single-threaded. Block pruning and checkpoints are also a medium-high priority. libsecp256k1 is another one which is almost done, as is the getblocktemplate stuff. Bitfury has also asked for performance improvements to the RPC interface, although I don't fully understand the performance problems he was referring to.

Block verification time limits would also be helpful for this goal, although it's not strictly a performance improvement.
It's also equivalent to a blocksize increase in terms of network and CPU performance, but it's a lot more work for the rest of the ecosystem to support the new block and transaction formats.
As long as they don't stick the Merkle root hash for the SigWit data into the coinbase message, I think it's cool. This should be a hard fork, not a soft fork.

SigWit does not offer any meaningful performance advantages that are relevant to our current blocksize scaling issues, as those are related to bandwidth and validation time, not to long-term disk storage.

SigWit has many advantages, but most of them are not really about scaling.
I don't have a LinkedIn, and probably never will.
We don't have any nodes on testnet in China yet. I just started up four VPSs in China, and they totally suck. The best of the four (the one in Shanghai) got about 1.2 MB/s bandwidth when I did my initial testing yesterday.

It might just be this provider that's awful. I also might be able to get performance to not suck by installing some Relay Network stuff. Tests are nowhere near to being over yet.

/u/Prattler26
I'm running into a problem with Mike Hearn's new **alpha** thin block propagation feature now on one of my nodes, and that's preventing blocks from being propagated to its peers. I'm going to continue debugging this node without restarting it for now, which means that block propagation probably will not work very well for other nodes.

Edit2: This is an unreleased feature that I've been testing in parallel with everything else. The fact that it is broken in about 1% of cases is of no concern to users that didn't download and compile my fortestnet branch.

Edit: Yeah, Hearn's fast block propagation is currently pretty broken, resulting in my nodes not agreeing on how many blocks have been produced:

    604756 London (mining node)
    604681 Singapore
    604674 Washington USA
    604667 Tokyo
    604645 Newark

I've looked through the logs on two of those (WA plus Newark, IIRC) and have found persistent merkleblock error messages on attempts to receive block currentheight+1.

**Edit: Please disable thin blocks for now on your servers. Thin blocks are buggy.** In bitcoin.conf:

    use-thin-blocks=0

Edit2: After having disabled use-thin-blocks on all of my nodes, they have been consistently agreeing on the correct block height. Everything seems to be working now.

Edit3: A debug log snippet showing this problem can be found at [this link](https://gist.github.com/jtoomim/4ac03b7713c4d1099337).
Your block propagation metric is the wrong metric. We are interested in a lot more than just the amount of time it takes to send the block over the network on one hop with 100% bandwidth utilization efficiency. Block verification time is important too, since that has to be done before the block is forwarded to the next party according to the current protocol. The number of hops is also relevant, and should be approximately log_8(number of nodes). In practice, the number of hops will be more than that. Six is a reasonable number for a network of 5000 nodes, I think. That means a 2-second delay per hop would result in about 12 seconds of delay between the first and last nodes, or enough to reduce revenue by about 2%. That's assuming that we continue to use the same block propagation protocol that we had two years ago, and don't use the relay network, p2pool, thin blocks, IBLTs, blocktorrent, or any of the other proposed or existing fast relay mechanisms.

The assumption of bandwidth doubling every two years is not conservative. It's historically accurate, and it's what I personally expect to continue, but many critics of BIP101 believe that it will not continue. This is largely due to recent metrics showing that average connection speeds have been increasing more slowly over recent years, largely due to the change of typical internet access devices from computers to mobile devices. If you want to provide conservative estimates, you should use bandwidth growth rates of 17% to 30%. If you want to be realistic, you should provide bandwidth growth rates of 25% to 50% per year. 

On the other hand, your ABDL and ABUL numbers start way too low. Even luke-jr has more than 1.5 Mbps download. I have 100 Mbps symmetric in my house. My VPS nodes mostly have 1 Gbps. I think that something like 10 to 25 Mbps would be reasonable.

Your storage cost estimates seem to use unreasonable numbers for hard drive price towards the end. You assume that HDDs will remain less expensive than SSDs, but most projections show SSDs becoming cheaper than HDDs They also assume no block pruning or checkpoints, which I think is unreasonable. I agree that storage costs will not be relevant, but for different reasons. 
The block explorers have gotten all sorts of confused. They're not reliable because they seem to not pay attention to the chain-with-most-work rule.

While we were mining on BIP101, we saw the Core chain quickly pass us up with about 2,000 blocks mined in rapid succession with difficulty around 1.6. I got rather confused by that, but even though Core was over 1000 blocks ahead of XT, all of the XT nodes stayed together.

We had some difficulty goofs of our own, too. At some point, we crossed a difficulty adjustment border. A few other people mined some blocks when I was otherwise busy, and then when I was ready to mine, I didn't know the difficulty was different. I pointed a half SP10 at my poolserver, and then mined at up to 100 blocks per second. Many of the blocks arrived at other nodes in reverse order. It was interesting.

Later, I got the CPU mining thing worked out, and we mined about 6 full 9.1 MB blocks in a row. We had a reorg in the middle of it, though, so I think we were having some connectivity problems from insufficient node coverage. One of my nodes also wasn't following the rest for a bit.

When it functions properly, it seems that block propagation for 8 MB blocks may be around the 5s to 15s mark. It appears that a lot of that time is due to block validation; the network transit time appears to be pretty low in our configuration. TCP slow start does not appear to be as much of an issue as I was expecting.

I'm currently not mining on XT because I found that leaving it for CPU mining when possible was more convenient.

Edit: I'll post a transcript from #bitcoinxt of today's testing on /r/bitcoinxt in a bit.
We are currently using testnet3. It is quite easy to create another testnet for testing other things. We can create one testnet per BIP if we wanted to. We can create 10 testnets per BIP too, each one to test another aspect. We won't do that because most BIPs don't have much support. 

BIP101 is the only big block proposal that has been implemented, so it's the only one that can be tested.

It's also, you know, the best.
1) In my opinion, we don't need 5,000 full nodes to keep Bitcoin secure. ~~The number of full nodes needed to protect against a Sybil attack is not absolute; the more expensive it is to run a full node, the more expensive it is to run a Sybil attack.~~ [Contested -- see muyuu's post below.]

Running a full node provides very little in terms of consensus or security. All full nodes do is relay valid transactions and blocks and respond to queries about the contents of blocks.

Running a miner on a small pool, on p2pool, or as a solo miner helps a lot more.

Caveat: Companies or individuals that transact in high volumes with anonymous or untrusted customers should either run a full node or (if possible) find a full node operator that they trust. Most others can get away with using SPV wallets. (Luke-jr will probably disagree, and say that anybody who transacts with untrusted customers should run a full node, and most people who don't as well.)

2) A smaller, slower increase would limit Bitcoin's usability and growth. I think it will also result in less total miner fees, which will result in lower security of the Bitcoin network, although that depends on the exact shape of the supply and demand curves.

Edit: But wait, there's more!

3) According to the consensus rules used by the developers of Bitcoin Core, we are nowhere near consensus. Consensus means that there is a proposal that not a single developer of Bitcoin Core vetos. That is a very high bar to reach. Not only is there not currently agreement by 100% of developers that the blocksize should be increased at all (luke-jr for example is opposed to any increase, and thinks the blocksize limit is currently too high), but developers also are nowhere near to agreeing about how much of an increase there should be, or what the schedule should be. Furthermore, the developers are not in agreement about how such an increase should be implemented -- should it be on a regular schedule, or based on miner voting? I, for example, think that voting adds a ton of unnecessary complexity and unpredictability, and think that miners should be building their infrastructure to keep up with the blocksize schedule instead of the other way around. Voting systems create a chicken-and-egg problem: a miner won't vote for an increase until they have the infrastructure in place, and they won't spend the money to build the infrastructure until it has passed the vote.

We've been debating this issue for years now, and we haven't yet made much progress on the issue among the developers. On the other hand, bitcoin users overwhelmingly support a blocksize increase. As a result of this deadlock, two of the main developers (Gavin and Mike) have abandoned the Bitcoin Core project and forked off a separate branch. If I name this branch in this forum, my comment will likely be deleted, and I might get banned, so I won't say its name. (The mods of this reddit think it's a very bad thing.) This branch does not follow the any-dev-can-veto rule, and instead follows the Mike-Hearn-makes-the-final-call-and-if-you-don't-like-his-decision-you-can-make-another-fork rule. This is the same type of rule that is used for developing the Linux kernel, the Python programming language, the Windows operating system, the Mac OS X operating system, and ... well, pretty much all other software. Gavin coded up an implementation of BIP101 for this fork Bitcoin client, and a few people are using it now. However, this client does not have enough support among miners to activate large blocks, mostly because most miners and users think that a blocksize increase should come out of the developers of the Bitcoin Core version of the software, and should come out of the any-dev-can-veto consensus process.

The result is that nothing is happening.
Many people claim that the fee per tx needs to increase in order for us to be able to pay miners to secure the network. I do not think this is the case, as I do not think that price increases alone will be sufficient or necessary to pay for mining. I think this will become clearer if we look at actual numbers.

Currently, the Bitcoin mining network uses about 300 MW of power. Let's say the average miner gets power for 4¢/kWh, and pays another 3¢ on other costs like labor, land leases, and a very slim profit. That means that the cost of mining is about $3500 per block. In order to ensure that the mining network remains secure, we have to ensure that the block subsidy stays above about $3500, or around 15 BTC.

With average block sizes around 500 kB, we are getting about 1000 transactions per block. In order to fund mining with blocks this size, we would need to have fees around $3.50 per transaction. Currently, transaction fees are around $0.05/tx, so this would require a 70x increase in fees.

On the other hand, we could also keep transaction fees the same and increase block sizes 70x, to about 35 MB average block sizes (~70 MB cap). For a miner of my size (0.25% of the network) using today's technology, this would result in bandwidth costs using about 1% of our total budget. Basically, we'd have to pay about 2x as much on bandwidth as we currently do. (Smaller miners would typically use a pool anyway, and won't have to pay bandwidth costs.)

35 MB blocks with $0.05 per tx in fees sounds more reasonable to me than 0.5 MB blocks with $3.50 per tx in fees. I think that if transactions cost $3.50 each, people would not use Bitcoin, and would either create altcoins or use Western Union. On the other hand, if we keep the cost of each transaction approximately constant (and low), I think it plausible that demand will increase 70x over 5 years.

We can also reduce the cost of mining at 450 PH/s by improving the efficiency of mining hardware. Currently, that 300 MW is based on an assumption o 0.67 J/GH miners, which I estimate is what the average miner uses. The best miner on the market uses only 0.25 J/GH, and a few have been announced (but not shown) with efficiency below 0.1 J/GH. By improving the efficiency of mining, we could probably reduce the cost of mining over the next few years by 5x without reducing the hashrate.

With 5x improvement in mining efficiency over 5 years, we might our options be a spectrum between 7 MB and $0.05/tx to 0.5 MB and $0.70/tx. Both of those points sound plausible to me, but the end with 7 MB and $0.05 sounds both more plausible and more desirable.

A bitcoin network that is capable of high transaction volumes and low fees is inherently more valuable than one that is capable of low transaction volumes at high fees. I suggest we build that. While we might be able to fund Bitcoin mining with 7 MB blocks at current fee levels, that would require a lot of dominos to fall perfectly. I think we should aim for 15 MB average block sizes with $0.05/tx fees by 2020, when the block reward halves again to 6.25 BTC. At that point, if the price remains constant, we would have $1500 or 6.25 BTC in transaction fees and 6.25 BTC in block subsidy. 
https://old.reddit.com/r/Bitcoincash/comments/bhdpmb/btc_versus_bch_help_me_understand/els6bf2/

https://old.reddit.com/r/Bitcoincash/comments/bgcrwd/what_is_your_justification_other_than_marketing/elk0ay1/
Looks like that transaction took about 18 seconds. That's pretty good, but there's room for improvement still. When I've watched others use WeChat pay, it has usually taken about 5-10 seconds. A lot of this may just be frequency of use and experience, but there may still be some UI optimizations available. 

1. It took a long time before the QR code was scanned. I'm not sure what interface was being displayed before the scanning, as the video's visibility is poor. If retail BCH is to take off, getting to the QR scanning faster will be important. Maybe launching the app in QR-scan mode under some circumstances? Or binding the camera button to the QR scanner?

2. The slider at the bottom wasn't obvious enough to the user. Perhaps different coloration or an animation of some sort could make it more obvious?
Because it would cause so much chaos to LN to add replay protection, they won't fork with replay protection. 

Probably, they won't fork at all.
Hi bro.
>>>Why do you care about schemes that require more than zero bits per transaction in a block when schemes are available that don't?

>> Because miners are permitted to make blocks that deviate from what my node expects them to make. Zero bits is only possible by completely eliminating miner freedom. If we're going to take that route, why even have mining at all?

> You misunderstand. Zero bits per tx in the block, N bits per transaction in the symmetric difference. Miners have full "freedom" (**strange that you describe transaction censorship as 'freedom'** but I see no need to debate it)...

Miners are also free to include transactions that were not propagated on the p2p network, either because of censorship, nonstandard scripts, insufficient fee, or whatever. I prefer systems that are as efficient as feasible in those cases.

Anyway, thanks for the clarification. To avoid the confusion in the future, you could describe these techniques as being independent of block size or O(1) vs block size. Saying that it's zero bits per tx implies that you can determine the size of the message by multiplying the number of transactions by zero.
> You are disregarding the extra round trip the collision causes, which turns a (say) 50ms transmission into a 250ms transmission, just for having a single unexpected collision in the block.

If the RTT is 100 ms, the result of a standard collision is 150 ms total, not 250 ms. This is the same as the result of a transaction simply being missing.

In a missing tx scenario, the decoder will see that it has no transactions in mempool with that prefix, and will request that index from the sender.

In a standard missing+collision scenario, the decoder will not see the correct transaction but will see the wrong transaction in mempool with that prefix, and will run into a checksum error which isolates the error to a 16 transaction window, so the decoder will request all 16 transactions in that window. This takes only 1.5 RTTs, the same as the missing tx scenario.

There is also a 2.5 RTT scenario, in which there is both a missing transaction within a window (preventing the checksum from being computed) and a collision within the same window (which can't be detected until the missing tx is filled in). Is this the scenario you're thinking of? If so, that wouldn't be "a single unexpected collision."
> Why aren't miners developing those technologies themselves??

Large pools and miners have an incentive to improve block propagation to and from their own servers, but they have an incentive to *worsen* block propagation between strangers. When strangers' blocks get orphaned, that lowers the mining difficulty for them. Consequently, pools often prefer to invest in dedicated block relay infrastructure or proprietary code rather than open source development. For example, pool.bitcoin.com has a worldwide relay network of Bitcoin Unlimited nodes passing blocks around via Xpedited Xthin and (maybe now) Graphene. In 2015 (long before FIBRE), Antpool had a UDP-based block transmission system to send blocks between their mining farms in Inner Mongolia and their main pool servers in Beijing and outside of China so that they could bypass the effects of packet loss on TCP congestion control.
UTXO commitments would mostly solve the historical block download issue. Neither UTXO commitments nor Flowee would fix the SSD/RAM issue nor the tx announcement (inv) issue.

Flowee does not have UTXO commitments. bchd does, though. Flowee is just faster at syncing blocks because it uses memory-mapped IO and avoids unnecessary memory copies and allocations. This gives Flowee a ~2x advantage in performance for block syncing.
TL;DR: If throughput increased suddenly, we'd hit a speed bump at about 30 tx/sec, or roughly 7x BTC's capacity, but after a few days with 2 sat/byte fees that would be increased to about 75 tx/sec. After a few weeks of 10 sat/byte fees, capacity would be increased to about 133 tx/sec. Another month of that would bring the limit to 300 tx/sec. Getting past 300 tx/sec would require new technology to be deployed, like Graphene or Xthinner, but both are close to completion. If throughput rates increase gradually and naturally (e.g. doubling every 3 months for 3 years), the developers and community will remove each of these speed bumps before we hit them.

---

**1)** At around 30 tx/sec, we'll hit the soft limits (8 MB/block) that some of the pools currently have set. If we are at these limits for more than a day or two, some of these pools will raise them. Other pools will only raise them if it's profitable to do so. Each transaction that a pool includes in their block increases the chance that that block will be orphaned. Currently, block orphan risk costs each pool [around 1 satoshi per byte](https://old.reddit.com/r/btc/comments/b8varz/cashshuffle_how_much_can_i_potentially_spend_on/ek1gusw/) of transaction size. When transactions only include a 1 sat/byte fee, they're roughly revenue-neutral for pools. If fees were higher than this, pools would have a financial incentive to include transactions. So if people increased their fee to 2 sat/byte, most pools would increase their soft limits to whatever is safe and most profitable for them. At 2 sat/byte and 50 tx/sec, this would increase *profit* by about 0.12 BCH/block or 1% of revenue. Given that most pools currently have profit margins that are about 1% of revenue anyway, adding 1% more would be a reasonably strong incentive.

**2)** Currently, Bitcoin ABC has single-threaded mempool acceptance code, which according to some older benchmarks (but not my own) may limit nodes to accepting about 50 to 100 tx/sec. If transactions hit that throughput level, pools could switch to Bitcoin Unlimited to get up to 300 tx/sec. But making this switch for most pools would require a larger financial incentive than just 2 sat/byte. 10 sat/byte should be enough, though, as that would provide an extra 2.4 BCH per block in fees at 100 tx/sec, or 7.2 BCH at 300 tx/sec. Alternately, pools could just wait for Bitcoin ABC to finish parallelizing their mempool, which shouldn't take too long. (I wrote some [parallel](https://github.com/jtoomim/bitcoin-abc/commit/eb1616d2c04ced534c525bdf6c2d1e9921644978#diff-24efdb00bfbe56b140fb006b562cc70bR676) [mempool](https://github.com/jtoomim/bitcoin-abc/commits/atmp-inflight) code for ABC in October, but it still has some debugging, testing, and cleanup left to do before it gets merged.) Bitcoin ABC and I haven't made it a priority because BCH currently averages about 0.8 tx/sec of throughput, and 100x headroom is considered sufficient for now.

**3)** Just after that, at around 133 tx/sec, we'd run into the current consensus-level 32 MB per block cap. This cap is the result of a command-line option that pools and users can change, but everybody needs to change it together or risk getting forked off the network. BCH's developers intend to coordinate an increase in the default value of this cap long before actual transaction throughput gets close to the 32 MB limit, so it should only come into play if transaction throughput increases dramatically and suddenly -- say, a 20x increase over 3 months. If that actually happened for some legitimate reason -- like the government of Venezuela promoting BCH's use as an alternative to the bolivar -- the limit would be quickly raised. Increasing this value would probably take a few weeks of coordination if there were a clear and urgent need (e.g. Venezuela) for its increase, and up to 6 months if the need was not urgent.

**4)** Above roughly 300 tx/sec, blocks will take a large portion of the total 600 second average block interval to propagate, and performance will need to be improved further. At that point, three things will need to happen: First, pools will need to switch their code to use the new updated `getblocktemplate` replacements that BU and ABC have added which provide the pool software with just the merkle path instead of the full transaction list for O(log n) performance instead of O(n). This isn't too hard, but would probably take a couple weeks. Second, we'll need faster block propagation technologies like [Graphene](https://news.bitcoin.com/bitcoin-unlimited-merges-graphene-block-propagation-technology/), [Xthinner](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0), and/or Blocktorrent. These technologies will make block propagation for very large blocks (1 GB or 5,000 tx/sec, possibly more) take a reasonable amount of time. At As a side-effect, these technologies will reduce the orphan risk to pools from including transactions in their blocks, which will reduce the fees needed to convince pools to generate large blocks from ~10 sat/byte to ~3 sat/byte (for Graphene or Xthinner) or ≤1 sat/byte (for Blocktorrent). And third, pools and users will need to beef up their full node servers considerably -- 500 tx/sec takes about 8 cores and about 2 MB/s or 172 GB/day of traffic.

**5)**There's one more known speedbump that will come into play eventually. This is due to block validation logic being single-threaded. On my node, block validation runs at about 8 MB/sec, suggesting ~1.2 GB blocks would take around 150 sec to validate on a single core. Getting past around 5,000 tx/sec will require parallelizing block validation and most other parts of the full node code. Doing this will probably require a few months of dedicated work. That work has not been started yet, since we need 1,000x higher transaction rates than we currently have for it to be necessary.

All of these speedbumps are known from previous testing. Developers have already been putting a lot of effort into fixing the hardest problems, and are continuing to work on solutions for both the harder issues (e.g. block propagation) and the easier ones (parallelizing mempool). Currently, we're solving these issues and increasing capacity much faster than throughput and demand are increasing. All of the issues I've listed will probably be solved within 2 years, so chances are that as long as demand grows at a natural and reasonable rate (e.g. doubling every 3 months for a 4000x increase over 3 years) we'll never actually hit any of these limits except during stress tests.
I could probably do this to represent BCH. Jason Cox and Shammah Chancellor are also in the SF area.
By coders writing code.

The people who really matter in BU are the five guys whose names start with Andre or Peter.
This article relies on short quotes taken out of context, and is utterly wrong.

Most of your article relies on this quote from John R. Stanton, Appt., v. Baltic Mining Company:

> it was settled that the provisions of the 16th Amendment conferred **no new power of taxation**...

Your interpretation of this quote is that if the 16th Amendment did not confer a new power of taxation, the income tax must be unconstitutional. This is incorrect.

Since the Constitution was ratified in 1787, Congress has always had the power to levy an income tax on the population. However, an esoteric rule in Article I stipulates that any "direct" tax must be apportioned among the states in proportion to their population:

> No Capitation, or other direct, Tax shall be laid, unless in Proportion to the Census or Enumeration herein before directed to be taken.

Capitations are fixed per-head taxes. Indirect taxes (such as import duties) were not subject to this rule. This apportionment rule is not appropriate for income taxes because it would require that citizens in poor states pay a higher percentage of their income as tax than citizens of rich states, which would be perverse and unfair. However, Article I did not clearly define what was and wasn't a direct tax, which left some ambiguity about how income taxes would be classified. In a [controversial 5-4 ruling from 1895](https://perma.cc/YX8F-MLRZ), SCOTUS ruled that an income tax was a direct tax, making it subject to the proportional apportionment rule. This ruling was widely considered to be an error and inconsistent with the Constitution's and Congress's intent, if not also the letter. One proposal to overturn this ruling was just to bring up another case before SCOTUS and give them a chance to change their opinion, but eventually it was decided that a constitutional amendment to remove all ambiguity was preferable. So in 1913, Congress and the states ratified the 16th Amendment, which explicitly named an income tax as not being subject to the apportionment rule. The full text of the 16th Amendment is this:

> The Congress shall have power to lay and collect taxes on incomes, from whatever source derived, without apportionment among the several States, and without regard to any census or enumeration.

Since Congress had the authority to do an income tax before the 16th Amendment, no new taxation powers were given to Congress. All that was done is explicitly exempting income taxes from the direct tax apportionment rule. If we revisit your original quote from Stanton v. Baltic Mining, but without cherry picking just the phrase you like, we can see that this is precisely what SCOTUS was trying to explain:

> the provisions of the 16th Amendment conferred no new power of taxation, but simply prohibited the previous complete and plenary power of income taxation possessed by Congress from the beginning from being taken out of the category of indirect taxation to which it inherently belonged, and being placed in the category of direct taxation subject to apportionment by a consideration of the sources from which the income was derived...

All the 16th Amendment did was reverse that maligned 1895 decision.

Your other quote is similarly cherry-picked and taken out of context. What you quoted was this:

> “As pointed out in recent decisions, **it [16th Amendment] does not extend the taxing power to new or excepted subjects**,…”

The full paragraph is this:

> The Sixteenth Amendment, although referred to in argument, has no real bearing and may be put out of view. As pointed out in recent decisions, it does not extend the taxing power to new or excepted subjects, but merely removes all occasion, which otherwise might exist, for an apportionment among the states of taxes laid on income, whether it be derived from one source or another.

Again, the income tax power isn't new; all the 16th Amendment did was clarify that Congress's preexisting power to levy income taxes was immune to the apportionment rule.

And all you did was mislead people by taking quotes out of context. Shame on you.

For more background on the 16th amendment, I recommend reading [this article](https://constitutioncenter.org/interactive-constitution/amendments/amendment-xvi).
Most hard forks only affect full node implementations. Bobtail changes the block header and PoW systems, which means that it also affects SPV wallets. Breaking SPV wallet compatibility is not to be done lightly, as fixing wallets and other non-full-node software takes about 10x as much work for the community as just changing the full nodes.
No, 1. The payment has to be routed either through Ella-Frank or through Bob-David. Each of those channels only has 1 coin available in that direction.
Whatsminer, Ebang, and Innosilicon all have machines that are comparable or superior to Bitmain in performance right now. GMO does not have anything competitive with other current-gen hardware; their 7 nm gear is only competitive with the 2.5 year old S9.

1. Ebit E11++: [45 J/TH](http://miner.ebang.com.cn/goods-15.html)

2. Innosilicon Terminator3-43T: [49 J/TH](http://www.innosilicon.com/html/t3-miner/index.html)

3. Bitmain Antminer S15: [57 J/TH](https://shop.bitmain.com/product/detail?pid=00020181210184613385gc3S2eKL0674)

4. Whatsminer M10V1: [65 J/TH](https://whatsminer.net/product/m10v1-33-x-th-s-whatsminer-includes-220v-psu-batch5-ships-on-jan-20-30th/)

5. GMO B2: [81 J/TH](https://gmominer.z.com/en/spec/)

6. Antminer S9 with AsicBoost: Around 80-85 J/TH

7. Canaan Avalon Miner 921: [90 J/TH](https://canaan.io/product/avalonminer-921/)

7. Antminer S9 without AsicBoost: Around 100 J/TH
Bitpay has one which I use.

I think the Coinbase Shift card might also work.
I only said that blocks bigger than 20 MB are not sustainable in a decentralized mining system due to the perverse incentives that happen when orphan rates get high. I did not say that they aren't possible.

Note: the 65 MB block took [about 276 seconds](https://twitter.com/jtoomim/status/1077540085841649665) to propagate, due to the fact that it used transactions that were not in the mempool of most nodes. That's only 235 kB/s. My judgment is that the safe limit for block propagation latency for a sustained decentralized mining network is about 20 seconds.

The only way Bitcoin SV is achieving these large blocks is by ignoring system safety. You guys can take that strategy if you want to, but it's not an approach I can recommend.

Edit: Four other users chimed in with their results. They got the block after [305 sec](https://twitter.com/1reizu/status/1077705346079182848), [404 sec](https://twitter.com/BitVapes/status/1077709405397938176), [293 sec](https://twitter.com/jtoomim/status/1077541162158702593), and [331 sec](https://twitter.com/CashNext/status/1078070701804859395).
In my proposal without finalization, all nodes will eventually converge on the branch with the most hashrate, though it can take a very long time for that to happen if the two branches are both maintained at a similar hashrate level. Nodes syncing to the network while a fork/reorg attack is in progress will eventually follow the same branch as everyone else, but this could take a few days or more before the switchover happens.

My proposal without finalization is immune to 9-block fork attacks. With finalization added, there is the potential for it to happen, but the attacker would need to have a ton of hashrate to have a significant chance of getting it to work, roughly 3x the defender's hashrate. If that's the case, there are far more damaging attacks that the attacker can do, like orphaning all blocks and mining only empty blocks.
Yeah, Xthinner has been pretty fun to code. I think once I have the blocktorrent-xthinner-udp version done, it should be able to transmit a 1 GB block to the entire network in about 3 seconds. That will be baller. There's a long way to go before the UDP version is ready, though.

Currently, I'm about 60% done with the TCP non-blocktorrent version. That is coming along nicely so far:

    jtoomim@light-star:~/dev/bitcoins/xthinner-newabc/src$ test/test_bitcoin --run_test=xthinner_tests
    Running 1 test case...
    Testing Xthinner on a block with 250003 transactions with mempool size 250002
    Encoding is 310733 pushBytes, 621464 commands, 20763 checksum bytes
    total  409179 bytes, 13.0936 bits/tx
    Single-threaded encoding took 565179 usec, 2260 ns/tx (mempool)
    Serialization/deserialization took 89075 usec, 356 ns/tx (mempool)
    Single-threaded decoding took 303314 usec, 1213 ns/tx (mempool)

At 400 bytes per tx average, that would be a 100 MB block.

The TCP version will probably be about 3-6x better than Xthin and Compact Blocks, and close to the performance of Graphene but with much better reliability.
This article is from 2014, and was intended mostly as a marketing piece to advertise Allied Control's proprietary technology. It is an inaccurate and biased picture of the state of the art at the time it was published, and it's also sorely out of date. For example, my facility came online in 2014, and has a PUE of about 1.06. I currently have 2 MW of load in 8,000 square feet, or 0.37 m^(2)/kW, and only about 1/3 of our space is actually used by miners and datacenter infrastructure (1/3 storage, 1/3 office or workshop). And yet, when they talk about KNC, they say that their power density is "1.45 m2 per 1kW of mining power, leaving very little flexibility towards higher density". That's 1/4 the power density I have in my building, and 1/10th the density I have for just the portion of my building that is a datacenter.
Having more than 66% of the hashpower, actually. There's a new penalty of 2x on PoW for mid-depth reorgs. Reorging out 10 blocks requires 20+ blocks from the attacker.
It requires mining [20 blocks](https://github.com/Bitcoin-ABC/bitcoin-abc/commit/321b2b6363c2df70c7427bd1618fdfbe2de81b2d#diff-24efdb00bfbe56b140fb006b562cc70bR2414) to reorg out 10 blocks due to the 2x penalty for mid-depth reorgs.

If the attack is pulled off successfully, it can be manually resolved, either with bitcoin-cli commands, or by doing a resync of the blockchain.

I think overall this approach is better than what we had before. Exchanges can wait for 11 confirmations, and then there's literally no way to reverse the transaction. Even successfully pulling off the chainsplit attack won't let the attacker rewrite history.
Wouldn't work as you described it because of this: 

https://github.com/Bitcoin-ABC/bitcoin-abc/commit/321b2b6363c2df70c7427bd1618fdfbe2de81b2d#diff-24efdb00bfbe56b140fb006b562cc70bR2414

In order to get a chainsplit right then, you'd need a 20 block alt chain at the time of publishing (assuming constant PoW per block), not a 10 block chain.

Keep in mind that manual chain switching is still possible. If this chainsplit attack happens, the miners will probably end up calling each other and asking what the rest of them were planning on doing about it. The side that has the most support will generally be the side that the rest switch over to.
Calvin's offer:

Give up the BCH ticker and use BAB instead, and we won't attack you.

My counteroffer:

Don't attack us, and we won't attack you.
After.

For moderately advanced users:

https://www.reddit.com/r/btc/comments/9ul7mp/electron_cash_coin_splitting_tool_for_nov_2018/

More casual users may be able to wait a while and get some split satoshis from a faucet.
Or OP_MUL tx or pure CTOR block
> What is SV trying to achieve? It's just a nothing to lose situation for him and nChain?

No, they just misunderstand the game theory. They think that "51% attacks" only require 51% of the hashrate. However, that's only true in the pre-BCH era. "51% attacks" were named in 2009 or 2010, IIRC, long before BCH existed. When you have a minority of hashrate on a blockchain like BCH does, the game theory is totally different from what people with an intermediate-level understanding of Bitcoin mechanics would expect.

They think they probably have enough hashrate to win. They're just wrong about that.
Windows support is only for users. Pretty much all businesses use Linux for their servers.
> \[Copying another person's writing without attribution is\] hardly what's going on here, it looks like describing how an existing idea works with bitcoin.

No, CSW is clearly copying other people's writing without attribution.

> From the bottom of page 5 in Wright's paper:

>>Starting from the simplest primitive recursive functions, we can build more complicated primitive recursive functions by functional composition and primitive recursion. In this entry, we have listed some basic examples using functional composition alone. In this entry, we list more basic examples, allowing the use of primitive recursion:

> From the [uncited source](https://planetmath.org/examplesofprimitiverecursivefunctions):

>> Starting from the simplest primitive recursive functions, we can build more complicated primitive recursive functions by functional composition and primitive recursion. In this entry, we have listed some basic examples using functional composition alone. In this entry, we list more basic examples, allowing the use of primitive recursion:

Notice how those two paragraphs are identical? That is obvious and explicit copying of another person's writing without attribution. It's also embarrassingly sloppy plagiarism. Plagiarizers usually will make at least some effort to rephrase the content they copy so that they don't get caught red-handed like this. My guess is that CSW starts writing his papers by copy-pasting content from elsewhere, then makes a second pass through the paper to rephrase various bits in order to obscure the plagiarism, and in the case above, he just missed a section.

In the example below, CSW did rephrase some parts of the copied text, but left enough of it intact that it was pretty clear that he was copying and modifying. I've bolded the language that is identical between the two versions:

> The next section is just as bad. [Here is the (different) uncited source](http://ii.fmph.uniba.sk/cl/oldcourses/lpi1-2004/lect/sli8.pdf), which is copied into Craig's paper starting on page 10.

> Source:

>> **expects a program, which is** a list **of instructions** which modify **a stack of natural numbers**. Such a **machine is Turing complete iff** any numerical function **computable on a Turing machine** can be computed **on the stack machine**

> Craig:

>> **expects a** script that acts as a **program which is** defined to be an ordered set **of instructions** that operate on and alter **a Stack of natural numbers** (the Stack Set). This **machine is Turing Complete IFF* a** decidable program can be run **on the Stack machine** when that program is also **computable on a Turing Machine**.

The funny thing about this is that one of the rephrasings -- replacing "list" with "ordered set" -- is simply *incorrect*. The phrase "ordered set" sounds like a smarter way of saying "list", but they are not equivalent concepts. A list can have a single value multiple times, whereas an ordered set can only contain each value one time. An ordered set of instructions can use each opcode only once. That's clearly not how Bitcoin script works. Craig was trying to find a synonym for "list" to avoid having his plagiarism being obvious, and he chose the wrong phrase. "Sequence" would have been a much more appropriate choice.

This is much better plagiarism than the first instance, but it's still clear that CSW copied and pasted in a source and then replaced a few nouns and phrases (and changed the order two clauses in the last sentence) in order to obscure the copying. If CSW had cited that pdf as a source and had done this rephrasing, it might have been kinda excusable (though still sloppy scholarship), but *he did not cite his source*. Thus, it's plagiarism.
In your example script, shouldn't it be OP_CHECKSIG instead of OP_CHECKSIGVERIFY?

If you had a 500 opcode limit instead of a 200 opcode limit, couldn't you do more OP_CHECKSIGs and fewer garbage data pushes? Seems this should allow you to make the attack about 2.5x worse even with the 10k byte limit. That should make one of these attack transactions 5x as bad as a normal transaction of the same size.
This uses version-rolling AsicBoost, and does not affect transaction inclusion in any way.

It is equally usable on BTC and BCH.
OP_CDSV (the new opcode) is the same calculation as OP_CSV (the old opcode used for verifying regular transaction signatures) except that:

1. The signature data, pubkey, and message comes from the stack instead of from the input's scriptsig field, the previous output's pubkey field, and the hash of the transaction itself; and
2. No sighash calculations need to be done, since the message being signed is explicitly encoded on the stack instead of inferred by sighash flags and the sighash algorithm.

My Core i7 4790k can perform 10,000 ECDSA verifies per second per core using libsecp256k1, so it should be able to handle a block with about 6 million OP_CDSV uses on 2 cores with a 300 second inter-block interval. (Script verification happens when transactions circulate before being incorporated into blocks, and generally is already cached and done when the block itself is published.)

/u/ryancarnated 

Edit: If anyone wants to run the ECDSA benchmark themselves, they can do it by downloading and compiling my WIP [atmp-inflight](https://github.com/jtoomim/bitcoin-abc/commits/atmp-inflight) branch of ABC and running test_bitcoin. There's a benchmark I added to that version of test_bitcoin which will check how many µs it takes per 1-input, 1-output transaction to be added to mempool when all needed UTXOs are cached in RAM. The slowest part of that process by far is ECDSA verification. My machine gets about 98 µs per tx on a single core, and about 30 µs per tx on three or four cores (i.e. if compiled with -fopenmp). There's also an ECDSA benchmark in libsecp256k1 itself, but I've never used it.
I like the way you formulated this question.
You can make a transaction that outputs to a P2SH script with 201 opcodes and send slightly more than the dust limit. Then you make another transaction that spends the output of that transaction with a P2SH script, and also includes your entire wallet balance as other P2PKH inputs in the same transaction, and sends your BSV somewhere else. This transaction will be invalid on BCH but valid on BSV. On BCH, you lose the dust from the first transaction (never spendable), but that's no big deal.

Alternately, you can use a coin that depends on a post-chainsplit coinbase transaction somehow (doesn't matter how indirectly), or you can use one of the new opcodes to do the same thing.
No, 10 TB blocks are no threat. Blocks that are universally rejected do not cause a problem, and blocks that large would propagate so slowly that they would not get accepted by anybody.

Blocks that are universally and rapidly accepted are also not a problem. Thus, small and medium-sized blocks are fine.

The problem happens when blocks are accepted at a moderately slow rate, e.g. between 20 seconds and 600 seconds after they are mined. Blocks of that medium-large size range cause perverse incentives that encourage all miners to join the largest mining pool that exists, thereby making that pool bigger and exacerbating the incentive. Once pools get large and blocks get big, some strange things happen, and large pools can actually have a perverse incentive to [intentionally delay their block propagation to everybody else](https://www.reddit.com/r/btc/comments/9g3xfc/block_propagation_data_from_bitcoin_cashs_stress/e69yw6c/). So perverse incentives can exist in that situation for both the miner's decision about which pool to join (i.e. the pool that is worst for Bitcoin's decentralization is best for the miner's profit margin) and for the megapool's decision about how much to optimize their block propagation.

Note: I have actually been in this situation before on p2pool. I have profited from slow p2pool share propagation speeds when I had 30 to 90% of the network hashrate on p2pool, and when p2pool's code slowed share propagation to about 5-10 seconds out of a 30 second average. During those intervals, I've earned about 103% as much as I had a right to; and if I were to sabotage my internet connectivity, I could have driven that number a lot higher. I was only able to keep it as low as 103% by improving the p2pool code performance with bugfixes and code optimizations, and by teaching everyone else on p2pool how to run it with pypy instead of regular python2.7.

/u/tastymouthfeel
It's not a soft fork. It's a hard fork. Just like BCH was. If it weren't for the concept of UAHF, BCH wouldn't exist.
I see, so you can make assertions without any links or data, but when I make assertions with links and data and code, it's "no scientific/engineereing methodology nor actual math proof."

Clearly, you must be a really hot girl if you are able to get away with double standards like that. Can I have your number?
It's not just Chinese miners. It's also *American* ones, and those are the really slippery ones. American miners have experience with lobbyist politics that Chinese miners lack. /s

([I am one of the funding sources for Bitcoin ABC.](https://blockchair.com/bitcoin-cash/address/1AeFHVVqnzYJXN75qJVPNWsUQZ3Pngw5gy) You should donate too if you want them to be financially independent. It's more effective than complaining, and it helps them hire more devs.)
Yes, unicast UDP is vastly superior to the TCP nonsense we're doing right now, and yes, multicast UDP would be an improvement on unicast UDP (thanks for the reminder of multicast -- I edited a mention of that into my article). None of this is specific to datacenter nodes, though; home users can use multicast UDP pretty much as well as a datacenter. Though there can be some minor MTU issues that a datacenter will be better able to resolve, that should have a fairly minor effect on overall performance.

Home users might not like blindly blasting UDP because it would make their Netflix stutter, but it's not terribly difficult to use UDP ACKs with round-trip latency measurement in order to determine when legitimate congestion is occurring. [Some Bittorrent clients](https://arstechnica.com/uncategorized/2008/12/utorrents-switch-to-udp-and-why-the-sky-isnt-falling/) already have that feature. But for version 0.1, yeah, we can just blast UDP and let packets drop.

One of the tricky things with multicasting parts of blocks is maintaining the ability to attribute bad data to its source. If you're assembling a block using data from 20 different peers, and the block data fails the merkle root check, you will know that one of the transaction hashes you received is wrong, but you won't necessarily know which one. This means you might have to redownload the whole block. Selfish miners or other adversaries might intentionally poison block transmission to other miners in order to cause their blocks to get orphaned.

My 2015 [Blocktorrent proposal](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-September/011176.html) and 2016 [pre-alpha code](https://github.com/jtoomim/blocktorrent-python) was designed to address that issue. Blocktorrent uses the Merkle tree structure to verify chunks of the block from different nodes so that if someone tries to poison the transmission, that poison attempt can be detected and cut off at the source instead of contaminating the whole block.
Regular end-user node data is worth a ton more than no data at all.

It doesn't seem to matter much whether the node is a miner or an end-user. The bottlenecks are the same, and are due to the algorithms (notably [TCP congestion control](https://www.reddit.com/r/btc/comments/9cfi1l/re_bangkok_ama/e5ay09x/)) and not the hardware.

So Graphene so far has only been able to successfully transmit 41% of blocks to your node. Thanks for that data point.

/u/tippr 0.00917112 bch
It's similar to Xthin, but better. Instead of sending the first 8 bytes per TXID, Xthinner sends about 1-2 bytes.

Also, if it's named something else, then we lose the opportunity for a Chinese joke. The word 二 in Chinese means "two" but is transliterated as "er", so Xthin二 would work both ways. Opportunities for cross-lingual puns should not be squandered.
> Sharding is data partitioning. The fact that it allows parallel processing of data is a consequence and not the initial goal.

This is a more precise formulation of what I was trying to say. Thanks.
AFAIK #2 is done in Flowee by processing different *blocks* in parallel during IBD, and that technique cannot provide any acceleration after the node has finished syncing.

> As such, I optimistically check all blocks in parallel until such a moment that I find a transaction (that goes in my wallet). In that case I just rescan the blocks that were already being scanned in parallel. Doing a little extra work to allow the vast majority of work in parallel.

http://zander.github.io/code/txiterator/

This seems to me like a special case that works for non-fully validating wallets, but will not work for fully-validating nodes. But there might be something I'm misunderstanding about his approach. I've barely glanced at his code.

In contrast, the outputs-then-inputs parallelization approaches work in all circumstances with no potential errors, and allow a single block to be split into work for an arbitrary number of threads. I have [code for this already working](https://github.com/jtoomim/bitcoin-abc/commits/openmp_connectblock), although it's not faster yet because I haven't fixed the locks yet.
CTOR is mostly not about sharding. It's mostly about block propagation, which becomes faster if you don't have all the entropy from the transaction order information. Sharding (segregation of data) is a side benefit.
If the price of the BCH (non-SV) chain does not fall, then the hashrate on the "minority" chain will not fall either. If Coingeek and someone else try to start a new chain, then more hashrate will come from BTC to BCH due to the profitability. In order to 51% attack BCH while simultaneously holding BSV to a higher hashrate than BCH would have, they would need to have 152% of BCH's pre-fork hashrate, not just 51%. They do not have 152%.

If people are afraid of nChain, then the BCH price will drop around the time of the fork, and that will give nChain a greater chance of success. Thus, they're now chest-thumping and trying to intimidate. But if we don't get scared and hold our ground, then there will be nothing to be afraid of.
He has filed [50 patent applications in Britain alone](https://www.businessinsider.com/craig-wright-quietly-building-patent-empire-2016-6). Just because the link I gave was to the PCT page does not mean that's the only thing that's going on. 

Maybe you would have to be incredibly naive to think that he only had one patent application going on?

> Also FYI, the ISA does not reject or issue any patents.

I know. The phrase I used was "They **recommended** rejecting all claims." The reason I'm bringing this up and asking people to get organized is because he is still a threat.

> Unfortunately, that does not make him harmless. Although he has a high rejection rate in his patents, Craig is extremely prolific in his applications, and it is likely that sooner or later one of these frivolous patents will get approved. If and when that happens, it will be a huge blow to all cryptocurrencies and blockchain technologies. It behooves us all to organize and form an association whose purpose it is to fight against patents like these, such as by filing prior art as an amicus curiae during the patent search process, or by assisting in the legal defense of whomever is first to be sued as a result of this Faustian bargain by nChain and Craig Wright.
Applying for patents is not trolling. Threatening projects based on a patent application that ISA has effectively rejected *is* a form of trolling, at least in the vernacular sense of the word "troll". Craig has tried to use the threat of his (invalid) patents to control Bitcoin protocol development. That is inexcusable.
> just plain dumb really

Half the world's population is stupider than the average person.
Oh, wow, I didn't even notice the vote ratio. That's crazy.

I guess facts are boring?
And the error was in the title of the posts, which is not editable.

In response, I posted another thread where the title points out the error:

https://www.reddit.com/r/btc/comments/9cpv5l/the_votes_were_by_bu_members_not_bch_devs/

Yes, I noticed that this was the one thread that you had overlooked.
I think

    ln -s /dev/null /r/csw

would be more appropriate. Also more symbolic, if you know what I mean.
> Where are all the posts about progress and software development?

They're around. Here's a couple.

https://www.reddit.com/r/btc/comments/9byuy5/please_set_up_your_full_node_servers_to_log/

https://www.reddit.com/r/btc/comments/9bpp8z/using_pgp_signatures_with_bitcoin_script_op/e54woga/
Because the interviewers asked him immediately prior if he could move "his" early bitcoins to prove that he is actually Satoshi.
Wow, this is incredible. This would allow you to send BCH to someone's email address without them having set up a BCH or PGP wallet first. They could then set up a BCH wallet/PGP/email client, register it with the sender's chosen oracle, and claim their funds. Future payments would not rely on the oracle at all, as the PGP public key will be on public keyservers (and in the sender's records) from then on. This would allow a UI for onboarding new users similar to PayPal, Venmo, or Zelle. But totally decentralized. And it uses email, which is the world's biggest example of a successful distributed p2p system.

The oracle could charge a small fee from the original transaction for the service of authenticating the new user for the first time, so this could even be a self-sustaining business.

Edit: Hmm, one issue is trusting the oracle. The oracle could just forge its own PGP key for that email address and run away with the money, and also steal all subsequent funds sent to that address. This might be a tolerable risk for small payments, as the oracle would be compromising their reputation and their future business if they ever did that. Perhaps there's some escrow system or oracle deposit system that could be set up to punish oracles in case of malfeasance? And maybe multisig for multiple oracles?

Edit 2: By using P2SH, the sender can keep secret some information that's needed to redeem the script, such as the recipient's email address or some token. The secret code can be sent directly to the recipient by email. This way, the oracle can't steal the funds unless they intercepted the email.

Edit 3: OP_CSV can be used to return the funds to the sender if they are not claimed within a week or so.

Is there something I'm missing? /u/awemany, I know you're interested in this opcode too. Can you look over this idea and tell us if you see any problems with it?

/u/chaintip $1000

Mark, what would it take to get you to drop everything else and work on this full time?
Would this allow one to send money to someone knowing only their email address and their PGP pubkey as listed on a public keyserver?

I really hope so. This idea actually excites me. Being able to literally email people money would be a completely new UX, and one which I think could really catch on.

Note: I only scanned most of your post.
I think you mean 1% XT.

I suspect the mining hashrate is more heavily biased in favor of ABC than BU at the moment. Miners tend to be more conservative about using the software version that has the best reputation for being bug-free, and BU still has a tarnished reputation in that respect.

It seems that a lot of people (perhaps a majority?) prefer no November fork at all. This suggests that they will choose to use BU or an old version of ABC.

Personally, I like the ABC fork proposal and would like to see it be adopted. However, I don't like it enough to support a chain split while enacting it. I will support either the BU side or the ABC side, depending on which side has more support. It currently appears to me that more people prefer the BU side (no fork), so I will probably install BU soon and start mining with that instead of ABC. 

However, I am also working on a parallelized version of ConnectBlock() (the function that validates new blocks) to take advantage of the outputs-then-inputs validation algorithm. If I finish that and publish benchmarks which show it to be considerably faster, that might change public opinion somewhat in favor of ABC's fork proposal. If public support shifts in that way, then I will support the fork with my hashrate.

I will not support nChain in any way for as long as they are behaving like stupid, short-sighted children.

Miners will switch software if they think the new software better aligns with their politics, or if the new software is expected to have fewer serious bugs, or if the new software has superior performance.
All of the bottleneck algorithms I can think of use datasets that are either too big to fit into L2 or too small for L2 size to make a difference. The most important dataset sizes are about 6 GB (UTXO set), or around 200 MB (mempool size in unserialized format).

I like the way you're thinking, though. 
I already [donate](https://blockchair.com/bitcoin-cash/address/1AeFHVVqnzYJXN75qJVPNWsUQZ3Pngw5gy) to Bitcoin ABC via mining on p2pool. I also [occasionally](https://github.com/jtoomim/p2pool/commits/1mb_segwit) [contribute](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244) [code](https://github.com/jtoomim/orderencode) myself.
Of which you can only use 1, because the software is mostly single-threaded.
[Here](https://github.com/Bitcoin-ABC/bitcoin-abc/blob/e292694f01a54769ae530388c3ace4a3ee419454/src/validation.cpp#L2132) is an example of transaction validation algorithm that works both for lexically ordered blocks as well as for the current topological ordering. It comprises two loops. The first loop (beginning on line 2132) inserts the new ouptut coins into the UTXO cache. The second (line 2160 and on) loop validates the transactions inputs, spends (removes) the old output coins from the database, and performs a few other checks. The second loop also checks that the ordering of transactions is valid on line 22212 if the canonical order fork hasn't happened.

This code is just as simple as any other transaction/block validation code I've seen. It also lends itself well to parallelization. For kicks, I wrote a parallelized version of this algorithm last weekend using OpenMP and got it to run error-free. (The first attempt turned out to be slower than the serial version because std::unordered_map is not threadsafe so I had to use locks all over the place, and I haven't had a chance yet to switch the UTXO cache over to a hashtable that supports concurrency.)
Fair enough. My writing is usually very technical and precise, but this time I thought it would be amusing to try something different. I thought it was fun to write and would be fun to read, but maybe I missed my target.

In any case, there is serious technical material there. 

There's also serious psychological issues there. I think a lot of why people are worried about CBO is because of emotional reasoning and the fear of the unknown. I think the math is strong. What's holding the proposal back is us.
Here are some arguments in favor of small blocks, as best as I can render them from across the aisle. 

...

Unfortunately, blockchains don't scale. Blockchains inherently have poor performance and capacity. If you try to stuff more data onto a blockchain than the blockchain can support, the result is catastrophic failure of the ecosystem. 

The most likely and worrisome failure mode for an overtaxed blockchain is mining centralization. As blocks get bigger, the amount of time needed for blocks to propagate through the network increases. Slow block propagation increases the likelihood that a second block will be discovered before the first has finished propagating, which would cause one of the two blocks to get orphaned. Since a miner or pool never has to worry about the latency of propagating a block to himself, a miner or pool with a large portion (e.g. > 20%) of the network hashrate will have lower orphan rates than small ones. Orphan rates will therefore be inversely proportional to that pool's hashrate. This encourages all miners to join a single pool in order to minimize their orphan rates. 

Alas, having more than 51% of the hashrate in a single pool would kinda be fatal to Bitcoin, as it would allow that pool to get 100% of the mining rewards, excluding all other miners and pools from being able to get any blocks. It would also allow that pool (or that pool's government, hacker, or disgruntled employee) to censor transactions, undo recent transactions, or double-spend transactions. This pool could take the richest Bitcoin hodlers and refuse to publish their transactions unless these hodlers paid a hefty ransom -- say, 10% of their holdings.

So if we let blocks get too big, we're kinda screwed.

We can't know if blocks are getting too big and causing centralization until it's too late. The percentage advantage that a big miner has over a small miner depends on how big the big miner is, which means that this effect should snowball and grow quite quickly. Consequently, we need to be very careful and conservative with this system to make sure that we don't ever get close to this scenario occurring.

Because blockchains don't scale, if we want to scale Bitcoin, the only safe way to do that is by taking Bitcoin transactions off the blockchain. It's better that this happen sooner rather than later. If we did a hard fork to increase the blocksize once, that would set the precedent that hard fork blocksize increases are possible, and then the community would cry out for one every time transaction fees get high instead of actually scaling Bitcoin the way they're supposed to, by moving transactions off-chain. The fee shock of December 2017 was necessary in order to get Bitcoin users to commit themselves to using blockchain space efficiently, and to suck it up and start using Lightning. The fee shock might have been unpleasant to users at the time, but it was a necessary step in Bitcoin's evolution.
Here's a couple of the spam transactions:

https://bch.btc.com/f36242a5d504bc5e0066c6ef2d5c96de7f5b4b3b88eb1bdbff99b447003cc1af (fan out)

https://bch.btc.com/8ff9836fdbf7fc7b6d156fec3ffe75f220babc2ead9724914a48b59ff99e5bed (fan in)

Some notes:

The spammer is publishing transactions in large batches, something like 100 to 1000 transactions at a time. Batches happen at irregular intervals, with about 4 to 20 minutes per batch. It looks like it might be one batch per block.

The spammer is reusing addresses heavily. Most of the addresses I've looked at for this spam attack show around 600 to 800 transactions in their history between Jan 13, 2018 and now (Jan 14th). A transaction for any one address will involve many or most of the other addresses in the spammer's repertoire.

The spammer is using a fan-out, fan-in pattern. The transactions either have one output and many inputs, or they have 1-2 inputs and many outputs. 

Batches appear to comprise transaction chains. That is, the spammer is publishing transactions that depend on unconfirmed transactions.

The transactions are large, on the order of 5 kB to 10 kB each.

The value sent in each transaction is small, typically about 0.001 BCH per transaction or 0.00001 BCH per input (fan-in) or output (fan-out). 

The transactions are all spending 1 satoshi per byte. The transactions are spending about 10% of their input value in fees.

It would be neat if someone could calculate how much BCH the spammer has inside these addresses; given that, we might be able to calculate how long this can continue for before the spammer runs out of funds (unless he adds more from an external source, of course). For example, if there's 0.001 BCH per address and 1000 addresses, that would be 1 BCH total, which means that this could go on for about one more day.
More like: "Ah, do you remember the account state as of block 1423007? That was a good state. The rich were only 73.5x richer than the poor back then."
I'm not sure there's evidence of 0-fee transaction spam or 200 AWS nodes either. All I see is evidence that blocks are filled with high-fee transactions, which means that any transactions sent with low fees are not getting mined and are sitting around in the mempool for a while.

Zero-fee spam is ineffective.
It looked like your earlier comment was responding to zeldaguru's second paragraph, where he said that running a CPU miner for Bitcoin would not be significant.
While you are mostly correct, there is a checksum method for Ethereum addresses that uses capitalization of the letters in the address to encode the checksum data. However, it isn't used often enough for wallets to be able to actually require the checksum to be valid.
More complicated blocks take longer to propagate on the network and longer for other nodes to process and verify. Uncles happen when two different miners each find a block at about the same time, where "about the same time" refers to the propagation+verification+work_switching latency of the miner for that block. The longer it takes for a new block to be verified by all other miners, the greater the chance of one of an uncle race.
I would like to point out that the OP's title is a question. Can anybody answer it? Can anyone find any evidence for CSW's/Ryan X Charles's claim?
Actually, more like 0.5-2.0 seconds on average, depending on the pool. https://poolbench.antminer.link/

If you look at block orphan rates in 2017, we've been averaging about [0.3 per day](https://blockchain.info/charts/n-orphaned-blocks?timespan=all&daysAverageString=60), or 0.2%. That corresponds to an average time of 1.25 seconds.

Most of this is due to Matt Corallo's work with FIBRE, http://bitcoinfibre.org/stats.html, although Falcon, Compact Blocks, and Xthin also play a role.
Based on past experience, it's more like 10%.

During the Paycoin launch, demand for SHA256 hashing on Nicehash (a marketplace for hashpower) increased dramatically. People were paying (IIRC) 1.1x to **[8x](https://pbs.twimg.com/media/B4-42cKCUAArUZl.jpg:large) as much for SHA256 hashpower as could be made mining Bitcoin. Even so, the supply of SHA256 hashpower on Nicehash was fairly slow to increase. It went from around 1 PH/s on day 1 to around 8 PH/s a week later. As the available hashrate increased, the price premium fell. It seemed to stabilize a few days later when the Nicehash marketplace was offering around a 10% premium.
Pruned nodes *are* full nodes, as they store a full copy of the current (and recent) machine state. Maybe you mean archive nodes? Archive nodes also keep the complete history in addition to the current state.
It looks like these transactions are happening once a week on Mondays. Perhaps they are an exchange cleaning up deposits from customers and loading them into a cold-storage wallet?
Much easier to do [in German](http://namisforum.bboard.de/board/ftopic-66371647nx7465-224.html).

(The link is to a 5,229 word German sentence. When translated, it begins, "When you begin to read this sentence, let go of all hope that you will ever get to the end, because in this sentence, among the tellings of stories and people...", etc., and turns into a treatise on how the author of that sentence is using the reader's interest in the never-ending sentence to guide the reader through a journey of discovery of the meaning of Earth, humanity, artistic intelligence, and Chuck Norris.)
Changelogs on the release pages would be really nice. Geth does this well, could Parity emulate?
It gets the attention of miners like me. It's also accurate, as this code appears to be an attempt to intentionally slow down the network by costing nodes as much CPU time as possible for a minimum amount of gas.
And your mom.
By the way, of the 475 lines of code added in this patch, 358 are unit tests. That only leaves 117 lines added, including comments and other fluff. I don't consider that to be a big change.

> What if there is a bug that starts validating all kinds of illegitimate transactions, or causes some problem with gas price/consumption/limit etc...

Except for the gas limit, those are not plausible bugs given the nature of the changes that were made. The code that was added searches for DAO transactions and marks them as invalid. It does not manually mark anything as valid.

> Very poor software quality and very cavalier attitudes about security are 100% of the reason this fork is necessary in the first place.

Poor software quality on the part of the DAO and Slock.it, not the Ethereum developers. The Ethereum developers are quite good. They've reviewed the changes in the soft fork, and I am satisfied with that.

One of the things that I hate about Bitcoin is that they just don't get things done very well. They're so worried about making mistakes that they make mistakes of omission and allow things to stagnate. The Ethereum project, on the other hand, is not afraid to react quickly or to make big changes that drastically alter the protocol or even the economics as long as they're pretty sure that the changes are net improvements. This means that Ethereum will continue to evolve and improve quickly, and the technology gap between Ethereum and Bitcoin will only widen over time.

Disagree. We don't store any wallet data or stealables on our mining node, so the worst that can happen to us is that we don't mine any blocks. We've already mined a block with the soft-fork enabled, and it was accepted by the network. The worst possibility that remains is that a percentage of our blocks will get rejected. If that's the case, we'll probably know within a few days. This is a cost I can live with, especially when a $50m theft is on the line.

It's not like I review all of the changes made in every new release of geth. Why should this one be different?
My mom asked me for comments on your calculations, so here you go.

Most miners spend $0.04 to $0.05 on electricity. Labor, maintenance, and other expenses (e.g. lease/mortgage) are also significant. I would suggest using a figure of $0.05/kWh overall, which presumes that some of the more expensive mines will shut down or downsize. That's about 2.3x more than your estimated $0.0218/kWh.

You can't actually get those rates in Wenatchee, WA for mining any more -- the Chelan County PUD put a moratorium on new large services, and will soon be instituting special (high) rates for "high density loads" like mining.

The bulk of the network hashrate is composed of Antminer S7s and other devices of similar efficiency. After the halving, and using the current difficulty, S7s will continue to be profitable, earning about $0.11/kWh, so the only way for them to be replaced is for the network hashrate to increase by 100% (implying 200% of the current hashrate added in the form of S9s) or for the Bitcoin exchange rate to drop by 50%. The S7 is about 2.8 times less efficient than the S9.

Overall, I think the operating expenses will be between 2.3x and 6.42x higher than your estimates.

The S9's price is currently massively inflated. The hardware itself probably costs $300 or less to make. The other $1700 is profit or recouping R&D for Bitmain. The sale price will likely drop below $1000 within 6 months. Depreciation is the most significant contributor for brand-new hardware, but OpEx (esp. electricity) is the biggest contributor for old hardware like the S5 or to a lesser extent the S7.

The S9 is going to have a useful life much longer than 180 days. My company is still making a (small) profit off of SP30s that we received in August of 2014, about 700 days ago. That was from the 28 nm generation, where huge progress was still being made in design efficiency and manufacturing processes for mining ASICs. The 2.8x improvement in efficiency in the S9 comes mostly from the switch from a 28 nm manufacturing process to the recently-released 16 nm FINFET process. This change afforded an unusually large improvement in efficiency, and the jump to 10 nm will not give similar improvements. Consequently, the S9 should have a longer useful lifetime than the SP30s did. I would guess we'll see them on their last legs in about 3 years. Most of their profit will be made in the first year, so a linear 365 day model may be the best linear model. An exponential model with a half-life of 10 months would likely be more accurate.

**Edit:** It's also worth mentioning that at the current Bitcoin price, the network hashrate and difficulty are likely going to double or more over the next 6 months.
Because he asked them, silly.
There is a lot of truth to maaku's objection. I have raised it before. When I raise it, I describe it like this:

With the 0.25x discounting funciton, Segwit gives us 1.375x to 1.75x as much on-chain capacity under typical conditions, but it comes with a 4x adversarial condition. When we deploy SegWit, we need to have a network with hardware and algorithms capable of handling 4 MB blocks without problems, but we don't actually get to use that physical capacity. It just sits idle and waits for an attack.

A 2 MB HF gives us more capacity with easier adversarial conditions than SegWit does.

In order to combine SegWit and a blocksize HF, we would probably need to change SegWit's discounting function from 0.25x to 0.5x or 1x.
/u/changetip 1 beer
Cheap prices in the context of mining in 2014 are different from cheap prices in the context of mining in 2015. Margins have gotten a lot thinner. You used to be able to make a decent profit off of $0.10/kWh power, but now it takes about $0.05/kWh or less.

Note: I don't know what the Czech Republic's rates are. I just know that many of their neighbors (e.g. Germany) have residential rates on the order of $0.20/kWh.
> Why not a little bit more aggressive on the size, like 3-6 or 4-8? 

Because **[Bitfury](https://docs.google.com/spreadsheets/d/1Cg9Qo9Vl5PdJYD4EiHnIGMV3G48pWmcWI3NFoKKfIzU/edit#gid=0)**, mostly. 
It looks like he misrepresented BTCC's/Samson's opinion at 21:45. BTCC may be willing to hard fork if Core doesn't join the consensus. They have not made any suggestions that they are thinking of acting as a minority. They have stated that they are interested in being part of a consensus even if Core does not want to join it.
The only thing holding this back is the fact that the developers of Core adamantly oppose a hard fork.
They censored it because BTCC has expressed support for bypassing Core, which would make them an altcoin according to Theymos's rules.

Edit: Would have censored?
/u/jtoomim not /u/jtoomin.

Your reply addresses the potentially recursive aspect of multiple generalized softforks fairly well, but does not address the complexity added by even a single generalized softfork.
Acknowledgements: 

1. The http://toom.im/blocktime visualization was coded by my brother /u/toomim and designed by my brother and I. 

2. The debug.log parsing code was written by /u/DarthAndroid. 

3. Spam generation was handled by /u/prattler26. 

4. Other assistance was given by /u/rromanchuk and a few others too numerous for my mushy weak human brain to remember.
> Do you have any estimates on what the percentage break down is between remote miners connecting to the major Chinese miners, and their own local hashrate from their own hardware?

I think you mean "Remote miners connecting to the major Chinese **pools**."

F2Pool (22%), BTCC (11%), Slush (4%), and Eligius (1%) are mostly remote miners. These are the true public pools.

AntPool (22%) is primarily self-mining, but accepts remote miners as well. I estimate that about 75% of their hashrate is their own.

Bitfury (20%), BW.com (7%), KnC (6%), 21 (2%) and Telco 314 (1%) are self-mining or solo-mining operations.

That totals to about 44% remote miners in public pools, and 53% in self/solo mining operations.
Are you referring to "consensus"? Consensus is a word which is very important for the Bitcoin Core development community. Its a word I do not like, but since it means a lot to a group of individuals I wish to influence, I use it to refer to the concept that they value. I would prefer a simple vote (majority or supermajority) for most issues like this, including whether or not to have a hardfork to increase the blocksize. However, if a majority of people think that we should only have a hardfork if there is consensus (no significant opposing hashpower), so be it.
I'm fairly certain this tweet is a joke aimed at me and my data. It's a way of saying that there is no proof that I actually tested any of this, and that people are just trusting me when I said that I tested 9.2 MB blocks. If anyone has this opinion, please contact me and ask me for proof. Please think of some data or hardware that I should have if I actually performed these tests and ask for it. As long as the resources that you are requesting are not excessive, I will do what I can to comply with these requests.

I will eventually release a torrent with the full log files from our testing. I'm still at the conference/hackathon right now and still trying to hack together some code for block propagation improvements, though, so I don't want to spend much time on the data for a few more days or so. (I might not even have time for it until I get back to the USA.)
From talking with BTCC and Bitmain after my talk, it sounds like their setup for block propagation is *much* better than what I had on testnet. Still, we think that the stratum proxy with coinbase mutation would be a much better technical solution, so we will be trying to implement this over the next few weeks.
BitcoinXT eats spam for breakfast.
A full node is a computer that contains an entire copy of the Bitcoin blockchain, which is currently about 50 GB in size. A full node will check every transaction that has ever been performed with Bitcoin since it was created to ensure that it conforms with the protocol's rules, and stores all of these transactions forever. This takes a lot of storage space, bandwidth, and processor time. A fast computer running a full node will take about 1 day to synchronize with the network from scratch. 

Lightweight wallets, or SPV wallets, only store and verify the transactions that you use. They take about one to ten minutes to synchronize with the network, and only use a few megabytes of storage.

You may notice that theymos gave you a few links to articles that express his viewpoints. These links were to bitcoin.it and bitcoin.org. Keep in mind that both of these websites are owned by theymos (as well as bitcointalk and reddit.com/r/bitcoin), and are moderated according to his viewpoint. While all of these websites contain some useful information, they are not independent or unbiased sources and should be interpreted accordingly.

For example, I disagree with a lot of the content in the "Economic strength" section that theymos linked to as "totally insecure". The argument that's made there states that if most users used SPV wallets, miners could do whatever they wanted and people wouldn't notice. I think that this is not true, since any attempt to do so would be observed by the minority that did run a full node, and they would notify the rest of the Bitcoin users through things like reddit.com/r/bitcoin, IRC, and the news media. That is what has happened in previous crises with Bitcoin mining, like the version [0.8 fork](https://bitcoinmagazine.com/articles/bitcoin-network-shaken-by-blockchain-fork-1363144448), which was detected and resolved within hours, and I see no reason to suspect that future events wouldn't play out similarly even with a small minority of users running full nodes.
> Miners will likely try to force a supply increase because they're so reliant on the subsidy.

Only if the average blocksize stays small. With 0.2 mBTC/kB, a 62.5 MB block would have about 12.5 BTC in fees. 

We probably don't need 12.5 BTC in fees to maintain the current hashrate; for details, see the calculation in https://www.reddit.com/r/Bitcoin/comments/3mvq61/scaling_bitcoin_092915/cvm7f2c. In order to pay for the operating costs of mining with 500 PH/s, plus a small profit margin, 5 BTC should be enough. With hardware with improved efficiency (e.g. all Antminer S7s), 2 BTC should be enough. We can get that with 10 MB blocks. With higher fee/kb, higher mining efficiency, and/or lower electricity costs, we could achieve that at even lower blocksizes.

So perhaps we have a choice between which hardfork we prefer: increasing the block size limit to increase fees, or removing the 21 million coin cap. Miners have to be paid somehow. 
Just to clarify, there were two XT blocks found by p2pool (both on my company's p2pool node), and one block found by BAN yesterday. There are three posts mentioning p2pool XT blocks, and one post mentioning a BAN block. One of the p2pool posts for mentioning block 376338 is a duplicate. That duplicate is either this post or  the post at the link here:

https://www.reddit.com/r/bitcoinxt/comments/3mjxdg/second_p2pool_bip101_block_found_today_where_are/
The "correct" percentage, from several perspectives, is 50%. 

That's the threshold needed for a hard fork to persist. 

That's the threshold for majority rule in political systems.

One might argue that changing the protocol of bitcoin should require greater than 50% hashrate support because it can do all sorts of nasty things like change the block reward or otherwise violate the economic principles of bitcoin. However, it's worth remembering that if you can get 50% hashrate support, you can do much worse things without a fork at all, simply by denying transactions from being incorporated and blocking other miners access to the blockchain. With a hard fork, the conditions are much better, because people who don't like the rules set forth by the new majority can still mine to the minority fork by continuing to enforce the old rules.

Although 50% might be the most justifiable number from a logical perspective, it probably is not the optimal number.

Personally, I think that 75% is a better choice for BIP101 than 50% is, since having only 50% would result in a fork that isn't very clean, both due to economic effects and blockchain effects. With 75% (3:1 support), I expect the fork to happen quite cleanly. I would probably also be okay with 66%. Larger supermajority requirements make it harder for changes to occur. If Bitcoin is not allowed to evolve through orderly and semi-frequent hardforks, it will be supplanted by something else, such as an actual altcoin.
> am I understanding this right when I think blocktorrent main advantage is that it reduces latency?

Sort of. Blocktorrent reduces block propagation latency for large blocks where bandwidth saturation is an issue or where multi-hop propagation is required. It comes with a fair amount of encoding overhead -- I expect around half the encoding efficiency of plain Xthinner.

But I think the best way to think of it is that Blocktorrent allows the p2p network to take advantage of every node's upload bandwidth much more quickly than traditional protocols like CB, Xthinner, and Graphene. Blocktorrent turns block propagation into a swarm (like Bittorrent) instead of a tree.

> Is blocktorrent possible with any encoding for compression?

Blocktorrent requires an encoding scheme that is segmentable -- that is, you need to be able to efficiently encode and decode a small consecutive chunk of a block. I designed Xthinner to have that property. Compact Blocks also has that property. I suspect Graphene does not, but there might be a way to redesign Graphene such that it does.

For efficiency, Blocktorrent's underlying compression method needs to be able to code small segments (around 512 tx) efficiently. Or, more to the point, we need to fit as many transactions into about 1000 bytes as we can (ideally rounded to a power of two). So far, Xthinner appears to be better than Graphene for small segments.

> but not mine them until they've broadcasted them out to at least one peer and it has passes a minimum of n seconds

This is already usually the case. Finding transactions in a block that haven't been in mempool for at least 5 seconds is pretty rare.

> would the advantage of blocktorrent still apply in comparison with graphene?

Yes. Blocktorrent uses Xthinner, so it gets most of the encoding efficiency of Xthinner (minus the extra BT overhead) when transactions are already known by the recipient. Blocktorrent's advantage is proportionally larger when transactions are unknown, though.
Those numbers sound about right for small-ish blocks, though I'll have to do some more testing with properly-synced mempools to know for sure.

On very large blocks with synced mempools and all mempool transactions included in the block, Graphene will have a significant compression advantage over Xthinner. Xthinner simply can't go below 10.75 bits/tx (roughly 99.725% for 500-byte transactions), but Graphene can go down to about 2 bits/tx (up to 99.9% or above) in these ideal situations.

On the other hand, Xthinner is designed to be able to deterministically and reliably resolve errors no matter how numerous they are. If mempool desync reaches 20%, Xthinner can still handle the block with around 70% compression. I suspect Graphene will fail entirely in that case and need to fall back to something else.

I think Xthinner may also perform better on small blocks or chunks than Graphene does. Xthinner's encoded size is about 12-16 bits per tx regardless of the number of transactions in a segment, and the overhead per segment is only about 20 bytes.

Also, I expect Xthinner's encoding and decoding time will be faster than Graphene's. Since Xthinner does not rely on siphash for collision resistance, there's no separate mempool siphash+sort step for Xthinner Encoding and decoding each take about 260 ns per tx on one core of a 4.4 GHz i7 -- that's basically just the mempool lookup time -- and Xthinner is parallelizable.

While Graphene will be the undisputed winner in best-case compression efficiency, Xthinner may have some advantages in other areas that could make it better overall.

Most of these attributes are things I decided would be necessary for Xthinner's intended use in Blocktorrent as the underlying encoding method. For that, we need something that is predictable, segmentable, and relatively compact.

/u/saddit42
https://www.coinex.com/ is one of the few exchanges that is continuing to support BSV.
If my solution is not perfect, I want to be aware of all of those imperfections so that I can decide whether they present acceptable or unacceptable risks. I don't care if he likes me. I care if he teaches me something I didn't know before. And he often does. In this conversation, for example, I learned about the segmentation option with minisketch, which makes decoding time O(n) vs block size, and I also learned that FIBRE has a FEC method for handling missing transactions (I thought it was just FEC for missing packets). Both could be useful for me in Blocktorrent.
>> Overload conditions...

> Are you guessing or have you actually looked. Because I've tested this. Bitcoin relayes transaction higher feerate first for a reason. :)

https://youtu.be/LDF8bOEqXt4?t=4404

High feerate first might solve the problem when blocksize is capped at 1 MB, since all surplus transactions will have fees that are too low to be included in a block. But that strategy does not work in large/unconstrained blocksize scenarios. If a node is not powerful enough to handle the specified maximum throughput, that's mostly the node operator's fault, but it's still desirable to have performance degrade gracefully in that condition.

> Fibre never sends the transactions. ... https://bitcoin.stackexchange.com/a/56494

Thanks for the link. My misunderstanding came from this paragraph in http://bitcoinfibre.org/:

> Because the Compact Block design was being done simultaneously with the work on FIBRE, the cmpctblock message format was designed to ensure it fits neatly into a UDP-FEC-based relay mechanism. The only difference is that we send it over UDP with FEC, and instead of relying on a round-trip to request any missing transactions in our mempool, **we send the block's transactions immediately**, again with **additional** FEC.

Perhaps you could edit that page to clarify the wording there?
> Adversarial is a case it doesn't handle essentially

Right, that is a difference between minisketch's threat model and Xthinner's. The basic getdata protocol works well enough as a backup for 1 MB blocks, but it gets ugly at 10 MB. Xthinner will help as long as less than 90% of the block is poison transactions, but only in proportion to the size of the organic transactions. Blocktorrent will help no matter how many transactions are poison. If we want a 100 MB or 1000 MB blocksize cap, we ought to be able to handle those adversarial cases. Xthinner was designed to eventually work as part of Blocktorrent.

> "Overload" doesn't imply particularly large set differences 

No, during overload conditions your node may be unable to accept transactions to mempool as fast as they are generated, possibly because you're using a hard drive and not enough dbcache. Or perhaps most nodes between you and the transaction generators have hard drives. Or whatever. Mempool desynchrony was one of the characteristic features of overload that the BU team saw during the Gigablock tests. We've also seen it during the BCH and BSV stress tests last year, though the INV_BROADCAST_MAX issue was a big part of that. (Thanks again for pointing that out, by the way.)

> But you do not achieve good performance as soon as there is even a single transaction which is unknown to you. Fibre does.

Isn't FIBRE's FEC just for packet loss, not for mempool desynchrony? My understanding is that FIBRE automatically sends the full transactions after the compact block. While that might be reasonable for a trusted relay network with 1 MB blocks used only by miners, it is not appropriate for an untrusted p2p protocol for up to 1 GB blocks. I do not believe that you should need to join (or be at the mercy of) a trusted relay network in order to mine with reasonable orphan rates. FIBRE is definitely cool tech, but it's just not the solution that my threat model requires.
tl;dr: Missing transactions are always a problem, but extra transactions only rarely cause delays.

---

Xthinner adds TXID data in 8 bit chunks. On average, that gives 4 extra bits beyond what is minimally needed to disambiguate between two similar TXIDs. This means that any additional transaction that was added to the recipient's mempool after the Xthinner block was encoded only has a 1/16 chance of causing an ambiguity and forcing a round trip to disambiguate. Handling missing transactions is a bigger problem than handling extra transactions. If a transaction is missing from the recipient's mempool, there's a 100% chance that it will force a round trip to disambiguate. Rarely, a transaction can be missing at the same location as an extra transaction falsely fits into. In this scenario, the base Xthinner encoding scheme will pick the wrong transaction, and this error will be detected as a checksum error. Checksums will only be able to narrow the problem down to a 16-tx or (1/256 of the time) 64-tx window, so checksum errors require a round trip to fetch 16 or 64 txs.

In theory, some of these scenarios involving extra transactions and ambiguity could be handled by just sending the TXID or even just a single byte from the TXID, or possibly even by just looking at the checksums, but I decided that these scenarios were going to be rare enough in practice that it was a better idea to keep the code as simple as possible rather than to optimize for the corner cases.
> I see its size scaling with the number of users rather than the size of blocks.

I see the size of blocks scaling with the number of users.

More to the point, I do not see the UTXO set size as being solely explained by the number of users. I think a large portion of the UTXOs are from automated services with regular payouts, like mining payouts or gambling services. I think that new services will appear that similarly dominate the UTXO set, like ad income payouts or microtransactions.

As the number of transactions per day increases, I expect the USD value of each transaction will remain approximately constant and the BCH value of each transaction will fall. With more low-BCH-value transactions, the average number of BCH in each output will fall, causing the number of UTXOs to increase.

Conservative engineering suggests that we should design our system for the biggest load that could plausibly occur, not for the expected value. As long as it's plausible that block size and UTXO set size will be approximately proportional, we should use that as a lower bound for what the system should be able to handle.

>  5 UTXOs on average

This does not seem plausible to me. Many coin selection algorithms try to delay consolidating outputs as long as possible in order to maximize privacy. 

A retail business like a gas station might perform one transaction every minute for 20 hours a day, and only consolidate its UTXOs once a day when it needs to order more gas. Such a business would have an average of 600 UTXOs from that day alone at any given time.

I just counted, and I have over 170 UTXOs in a single wallet. That's probably at the high end of the distribution, but in order for the average to be 5 UTXOs there would need to be 42 users who only have 1 UTXO for every user like me. 

Currently, the BTC blockchain has 52,791,290 UTXOs. At 5 UTXOs per person, that would imply BTC has 10,558,258 users. That number seems a few times higher than I'd expect. (The BCH blockchain has 40,370,328 txouts, but many of them are abandoned BTC UTXOs.)

Magnesium is a *voltage-dependent* inhibitor of the NMDA receptor.

The NMDA receptor is unique in that it does nothing at all when a neuron is at its resting potential (i.e. not activated) because magnesium blocks its pore, but once the neuron has been excited somewhat by other glutamate receptors, magnesium departs from the NMDA receptor and allows sodium and calcium to enter the cell in the continued presence of glutamate. In short, the NMDA receptor is a coincidence detector: it senses when an action potential arrives at a synapse shortly after other action potentials have arrived at the same or other excitatory synapses on the same neuron.

The calcium that enters the cell has two effects: first, it depolarizes (activates) the neuron and makes it more likely to fire an action potential. This is the less important effect. Second, it triggers a series of chemical reactions known as a second messenger cascade that ends up activating molecules like cAMP and CREB, which induces long term potentiation of the synapse and makes memories. So more NMDA activity during these coincident-stimulation events makes you learn faster.

There's a pretty famous saying in neuroscience: "Neurons that fire together wire together." This principle is known as Hebbian learning, and it's pretty much the core of how we learn from experience. The NMDA receptor is the coincidence detector that underlies nearly all Hebbian learning in the brain. It's pretty important.

Magnesium usually blocks the NMDA receptor's ion channel pore when the neuron is at its resting potential, but this blockade isn't effective 100% of the time. Sometimes, random thermal jostling and Brownian motion will bump a magnesium ion away from the pore even when the neuron is at rest, and it can take a bit of time (maybe a few nanoseconds) before another magnesium ion comes over. This makes the NMDA's calcium signaling less specific to the coincidence events, and reduces the information content of the calcium signal. Moreover, the calcium that gets leaked into the cell triggers down-regulation of the NMDA receptors, which makes the synapse's NMDA response smaller once the coincidence events do happen. By increasing extracellular magnesium concentrations, magnesium threonate reduces the amount of time that NMDA receptors at rest are without their magnesium plug without affecting NMDA sensitivity for activated neurons, which makes the NDMA calcium signal during coincidence events more specific and stronger, thereby improving learning and memory.

At least, that's the idea. There have only been a [handful](https://www.sciencedirect.com/science/article/pii/S0896627309010447) of [studies](https://www.sciencedirect.com/science/article/pii/S0091305713000658) so far on magnesium threonate, so it's still quite plausible that it doesn't translate to humans for some reason or another, or possibly even has the opposite effect compared to rats. Biology is complicated and unpredictable like that.
No, it doesn't. It has about 46 exahashes/sec, not 60 EH/s. Perhaps you're looking at an estimate of the hashrate that's based on a very short time interval, like 1 day? There's huge statistical fluctuation in short intervals. You should really be using data averaged over 2 weeks at least, like https://bitcoinwisdom.com/bitcoin/difficulty.
Three on-chain transactions, actually.
I haven't benchmarked for 1 TB, but for 1 GB on a single core, sorting into LTOR order takes around 987 ns per transaction. Pretty fast. Keep in mind, that process happens outside the latency-critical path in big-block scenarios, as the mempool should be basically empty for the first block template creation in a block interval, and for subsequent block templates in the same block interval, the mining hardware is already working on a block template with the correct parent, and is only missing the last 15 seconds worth of transactions.
> Having to decode a huge filter will put them at a disadvantage because computationally it will take longer than just downloading the raw block information.

Xthinner does not use any bloom filters or IBLTs, unlike Xthin and Graphene. Decoding an Xthinner message takes about 750 ns per transaction on a single core and saves 30 to 400 bytes of network traffic per transaction per peer, depending on whether the alternative is sending the full TXIDs or sending the full transactions. If the former, then it is only faster to use a dumb raw-bandwidth strategy if you have at least 40 MB/s of dedicated bandwidth per peer. For a node with 8 peers, that would be around 2.5 Gbit/s. If the latter, then 320 MB/s per peer or 20.5 Gbit/s. And that's compared to *single core* decoding.
Yes, txindex will definitely slow things down quite a bit. A high -dbcache should help somewhat, but I'm afraid that txindex is not a very common use case and consequently is getting very little attention for optimization right now. The bottleneck with txindex is still disk reads and writes, so it should be possible to improve performance a lot by using a good RAM cache system and/or Optane (which you might want to try), but there's nobody taking care of that code on Bitcoin Cash right now. AFAIK, most Bitcoin Cash block explorers are based on third-party systems like [Bitpay Insight](https://github.com/bitpay/insight-api/issues/578), which is being maintained and which might be faster. /u/imaginary_username set this up recently; he might be able to tell you what sync times were like.
I think it's less about storage and more about bandwidth. If I encode a video file into a block with a bunch of OP_RETURNs, that means that every non-pruned full node on the network will be serving that video file to anyone who asks for it for all eternity. 

Running a full node with 100 incoming connections, with the serving of historical blocks already can use [17 GiB of upstream bandwidth per day](https://bitcointalk.org/index.php?topic=3286296.msg34291483#msg34291483). That bandwidth is provided voluntarily and for free by Bitcoin users who want to make it easy for new users to join Bitcoin and start up full nodes.

If we make it cheap and easy for application developers to parasitize that bandwidth, then fewer users will continue to serve historical blocks, and starting up (and running) a full node will get harder and more expensive. 

Both pools and non-mining full nodes pay part of the cost for including data in the blockchain. Miners are compensated for it. Full nodes are not.
No, that is not the cause. If it were the case, then we would see the fluctuation in the total (BTC + BCH + BSV) SHA256 hashrate. But the oscillation only shows up in the BCH hashrate.

The fluctuations are hashrate switching between BTC and BCH. BTC's baseline hashrate is high enough that the switching is statistically insignificant and nearly undetectable there, but it's obvious on BCH.
The plan for Blocktorrent+Xthinner is to encode each chunk as either 256 or 512 Xthinner transactions plus the Merkle path hashes plus the header. The Xthinner segment's size is difficult to predict exactly, but will usually be 1.5 to 2 bytes per transaction, so 384 bytes to 1024 bytes. The header is 80 bytes. Metadata will probably take about 20 bytes. With a 1500 byte MTU and the 512 tx chunk size, that means that the worst case packets will have about 1124 bytes of non-Merkle data, leaving around 350 bytes available for Merkle path hashes before a 1500 byte MTU gets exceeded. That allows for 11 or 12 levels in the Merkle path to the root of the chunk's merkle subtree, or around 20-21 levels total (1 or 2 million tx). Blocks with fewer transactions and fewer Merkle levels will generate smaller Blocktorrent chunks even in 512 tx mode. Blocks with more than 1 million tx will need to use the 256 tx mode in order to usually stay within a 1500 MTU size.

It may be desirable to use the 256 tx mode for clients that have known-low MTUs. However, adding code to intelligently select the optimal tx count on a per-client basis adds quite a bit of complexity to the code, so I think I'm likely to not bother with that at least for the initial implementation, and just let the networking protocol and routers handle datagram fragmentation when it makes the code easier to write. As long as I can make 90% or more of the messages between latency-critical nodes are unfragmented, I think it will be fine. If a machine only has an MTU of 1280 or even 512, that will double or triple the datagram loss rate for worst-case messages. For most nodes, that's going to increase datagram loss from e.g. 1% to 3%, which isn't a big deal. Pools and miners can make sure they support the full 1500 byte MTU for optimal performance. Connections with high packet loss can use more FEC or redundant transmission attempts. 

Intended transport is UDP, but I might get lazy and do TCP, possibly as an interim goal or an additional option.

I'm not concerned with RAM consumption or fragmentation. Keep in mind that these ~1 kB chunks are going to be Xthinner-encoded, so a 1 kB chunk will encode around 205 kB worth of transactions. The Blocktorrent encodings for a 1 GB block will only take about 4.9 MB worth of Blocktorrent chunks.

The part that I'm dreading for Blocktorrent is actually the transmission of missing transactions. What if someone includes a 100 kB transaction in the block that wasn't previously transmitted? That transaction needs to be transmitted in full along with the block. Do we try to send it over UDP (which has a 64 kiB datagram size limit), or do we fall back to TCP? If we fall back to TCP, that means that each Blocktorrent peer needs to have both a TCP connection and UDP connectivity, which may make NAT hole punching harder and reduce the number of situations that Blocktorrent can work in. If we do UDP, then how do we split up the jumbo transactions into chunks and verify the chunks without performance going to hell, and without spending a ton of time writing code that almost never gets executed?
Graphene doesn't reduce the data in a block. Graphene just reduces the data needed to transmit a block, assuming that the transactions have already been previously transmitted.

Schnorr sigs (especially with signature aggregation) reduce the size of those transactions. 

> In order to validate an aggregated signature, you have to first "un-aggregate" into it's composite signatures, which takes resources.

This is false. An aggregated signature can be verified against an [aggregated pubkey](https://eprint.iacr.org/2018/068).
Perhaps it would be helpful if a bunch of community members signed an amicus curiae brief with a more honest account of the events and facts? One of the legal allegations is conspiracy to monopolize the currency. Showing broad-based community support for the changes that ABC might help to dispel both the conspiracy allegation and the monopoly allegation.
I am opposed to dynamic blocksize caps. Blocksize limits should be based on what the technology can safely supply, not what the users demand. 

Right now, if block sizes exceed about 20 MB on a regular basis, block propagation suffers, orphan rates shoot up, and nodes start to spend a lot more of their resources dealing with reorgs and mempool set reconciliation instead of efficiently committing transactions to the blockchain. That wouldn't magically change if we had 60 MB/10 min worth of transactions being generated. And the safe capacity of BCH doesn't fall if there's only 600 kB/10 min worth of transactions being generated, so there's no reason to reduce the cap in that scenario. 

The idea of a dynamic cap is attractive because it is based solely on math and data in blockchain itself. However, it's the wrong data for the decision. The blocksize limits need to be set based on benchmarks of the code and the hardware. We shouldn't settle for fundamentally the wrong numbers just because they're more accessible than the right numbers.
> The actual numbers show that the ordering information is such a tiny amount of data that its quite irrelevant for some years to come.

Which actual numbers are you referring to? The data that I've seen said that 37 kB of the total 43 kB total Graphene message size was ordering information. Those numbers might seem small, but only because block sizes were small (a couple MB). If we want to scale past a couple MB average block sizes, having more efficiency can be helpful.

> You stated you are doing a different design than graphene? Why is that?

I explained it in [the article I wrote about Xthinner](https://medium.com/@j_73307/benefits-of-ltor-in-block-entropy-encoding-or-8d5b77cc2ab0).
I don't know about Graphene, but Compact Blocks and (soon) Xthinner have modes for forwarding blocks before validation has been completed. Only the Merkle root and PoW need to be validated for forwarding a block to not add DoS vulnerabilities.

I believe the 99.5% and 98% figures already include the retrieval of missing transactions.
The code that I have written assumes that the block is in LTOR order. Because of that, I can simply iterate through the mempool in lockstep with iterating through the block. If I was not able to make that assumption, I would not be able to iterate through the mempool and would need to do find() operations instead, or a binary search, or something, and the whole thing would be much slower. find() on a tree usually takes around 400 ns per operation, whereas iterating through a tree averages around [80 ns per step](https://www.tommyds.it/doc/benchmark).

If LTOR were optional, I would need to write my Xthinner code in such a way that it had non-exploitable performance for all block orders while still having good performance in the typical order. Writing code like that is hard. Writing code that is optimized (and tested) for a single condition is substantially easier.
Or political rebellion. Or an enemy state's transactions.
I would prefer to start with a double-spend reorg attack, reduce confidence in the system, drive away hashrate with lower revenue, and then use DoS to move in for the kill.
SV can only average about [10 MB every 10 minutes](https://pbs.twimg.com/media/Dsa7oFMUwAEaBPz.jpg:large). They were only able to generate 32 MB or larger blocks by letting their mempool fill up for 30 minutes between (full-ish) blocks. They got to 64 MB by slowing their hashpower 75% in order to decrease the block rates and fit more transactions into each block.

What matters is the transaction throughput rate, not the block sizes. Bitcoin SV is stuck at [about 50 tx/sec](https://twitter.com/PeterRizun/status/1064316656984289280). Bitcoin ABC is able to do a bit better than that, maybe 100 tx/sec (not yet rigorously tested). Bitcoin Unlimited is able to do much better than either, and can get 600 to 1700 tx/sec.

Bitcoin SV's "let's just increase the blocksize limit!" scaling strategy is insufficient and ineffective at actually increasing throughput, but it does make for good marketing material.
1. Hashrate left BTC to join BCH and BSV for the hashwar.
2. The BTC exchange rate dropped enough that some of the less economical mining farms (e.g. in China) are no longer profitable with older gear (e.g. Avalon 741s), so some hashrate has been taken completely offline.
> I see you did the simulation using uniform random block time around the mean value.

No, I did not. I did the simulation using an exponential distribution with gamma=1, which should model the real-world mining intervals accurately. Unless I misunderstood something and goofed. Did I goof?

https://github.com/jtoomim/antiReorgSim/blob/master/reorgsim.py#L126

Note: the "randomcomparison" function is obsolete and not called in the current version of the code. Maybe that's what you were looking at.

Edit: I pushed a commit which deletes randomcomparison(...) to prevent other people from getting similarly confused.
Sorry, it is impossible to estimate that. There is no way to determine how the markets would react to buying $600k/day.

I doubt it will do that much, though. But buying coins could be a good idea right now. We're probably just passing maximum FUD, which means we could be close to minimum price.
The attack requires [2x as much hashrate](https://github.com/Bitcoin-ABC/bitcoin-abc/commit/321b2b6363c2df70c7427bd1618fdfbe2de81b2d#diff-24efdb00bfbe56b140fb006b562cc70bR2414) to pull off as the defenders have. This means that if CoinGee, BMG, etc. have 4 EH/s, the defenders can deny the attack with about 2.5 EH/s. That level is roughly the equilibrium mining rate for BCH right now.

Furthermore, if exchanges simply require 11 confirmations, then even a 10 block reorg causes them no harm.
No, but I generally agree with it. 

I had an idea which I called the no-nephews rule. Any block that was received after it already had a nephew in the blockchain would be parked. I like Amaury's idea better.
There's an existing attack vector for reversing transactions and rewriting history by an attacker with 51% of the hashrate. This change eliminates that attack vector for transactions with 10 confirmations.

It adds an attack vector for splitting the network by an attacker with 67% of the hashrate. This split can be manually resolved, and this attack also does not allow transactions to be reversed once they have been finalized. This new attack vector is less serious and harder to pull off than what came before it.
Yes, when the nukes come rolling in, what I'm going to be worried about is whether my full node is still operating on the right chain. /s
No honest nodes would accept even the first of those 10 bad blocks. The reorg protection only takes effect on nodes that have accepted all 10 blocks. Your node can't tell my node what blocks are unreorgable. My node determines that on its own.

Just because ABC nodes are following this no-reorgs-past-10-blocks rule does not mean that suddenly BitcoinSV can no longer exist. It just means that blocks which follow the ABC rules and which are buried 10 blocks deep under other blocks which follow the ABC rules cannot be reorged out without manual intervention.
Tl;dr: It's very unlikely that there are any orderings that are faster than LTOR, and making a single order mandatory makes the code a lot simpler. Simple code is generally less buggy and inherently faster, and usually easier to optimize.

LTOR can be changed later in the unlikely event that someone comes up with something new that's superior.
Don't assume that the entity or entities doing transaction spamming are the BSV devs or miners. There may even be multiple spammers operating simultaneously.
Empty blocks are not a threat. Excluding honest blocks is a threat. In order to do that, they need to have more hashrate than honest BCH miners. They do not currently have that.

Everybody was aware of the potential for replay attacks, and exchanges should now have split all of their coins. I do not expect anybody to be successfully replay attacked this time. We know better now.
It means we know they were specifically using Nicehash. That's pretty useful.

Also, there's the PR power.
Methodology here:

https://www.reddit.com/r/btc/comments/9xgac0/abc_has_done_47_more_pow_than_sv_so_far/e9s6k3t/

You're comparing log_2 values of the total work since 2009, and that's not going to change much both because it's log_2 and because it's cumulative.
This is such FUD. OP_CDSV is just an opcode for verifying ECDSA signatures, and will be used for things like trustlessly onboarding new users by [allowing you to send money to their email address or phone number](https://gist.github.com/markblundeberg/bd28871548108fc66d958018b1bde085) instead of a BCH address, i.e. without trusting a custodial third party like CoinText with the ability to steal your funds.

All the illegal activity that Craig Wright claims OP_CDSV enables [is already possible with OP_SHA256](https://www.reddit.com/r/btc/comments/9vxc7w/illegal_gambling_futures_markets_on_murder_and/). And there's no way that OP_CDSV is related to "[kiddie porn](https://twitter.com/ProfFaustus/status/1060515026145026049)" in any way. The fact that CSW is using that term, in my opinion, is a clear indication that he's attempting a character assassination by trying to falsely associate CDSV with as much negative sentiment as possible, and doesn't care if his accusations are technically accurate as long as they're emotionally salient.
That is exactly what the code does. You're welcome to continue to run Bitcoin ABC 0.17.2 or Bitcoin Unlimited 1.3.0 if you want. Bitcoin ABC 0.18 and Bitcoin Unlimited 1.5.0 both have replay protection versus those older implementations. The old clients and the new clients will not interfere with each other. You can stay with the current chain if you want to.
But we can get data on testnets a lot more cheaply. Why not organize people to set up a testnet that is constantly spammed?
> My main grief with OP_CHECKDATASIG is that an unknown amount of work needs to be done for just 1 byte, making it much easier to construct a ton of malicious txs with valid fees

OP_CDS requires a known amount of work to be done (about 400,000 CPU cycles), and that requires about 180 bytes, not 1 byte, as you still have to push the signature, pubkey, and message to the stack with OP_PUSHDATAs. Unlike OP_CS(V), OP_CDS(V) is immune to the O(n^(2)) sighash bugs like this one, as the message is explicitly encoded instead of being implicitly computed as the SHA256 of parts of the transaction data.

Signature operations (like OP_CSV and OP_CDSV) are already counted by the code. Blocks with excessive sigop counts are rejected.

A modern CPU can perform 10,000 OP_CDS(V) operations on a single core per second. At 180 bytes of data each, that's 1.8 MB/sec per core, or 1.08 GB for a 600-second block on a single core. That's basically the same limit as we get when processing p2pkh transactions, and without coincidence.
Because sometimes we discover bugs like these two:

https://bitcointalk.org/?topic=140078

https://bitslog.wordpress.com/2017/01/08/a-bitcoin-transaction-that-takes-5-hours-to-verify/#comment-12012

in which the verification cost is O(n^(2)) versus the opcode count in a single input, and those kinds of bugs can be a lot worse if you don't have an opcode limit.
This diagram is incorrect. It confuses the concepts of "replay protection" (which prevents a transaction valid under one ruleset from being valid under the other ruleset) with "wipeout protection" (which prevents a block valid under one ruleset from being valid under the other ruleset).

Replay protection always provides wipeout protection, but not all wipeout protection mechanisms provide replay protection.

Any hard fork will have wipeout protection in at least one direction. A hard fork will have the possibility for blocks that follow the new rules but which are invalid under the old rules. This means that no matter how much hashrate it has, nodes that follow the new ruleset in a hard fork can never wipe out the old ruleset.

Some hard forks are bilateral, and also include wipeout protection in the other direction. The BTC/BCH fork was one of these, but the ETC/ETH fork was not. In a bilateral fork, a block under the new rules will (sometimes or always) be invalid under the old rules, AND a block under the old rules will be (sometimes or always) invalid under the new rules. This is the type of fork that ABC/SV is doing. The removal of the 200 opcode per input limit allows for blocks that ABC will reject but SV will accept, and the removal of the TTOR requirement allows for blocks that SV will reject but ABC will accept.

The only way a hashwar can occur is if a "51% attack" attempt is performed. Surprisingly, it turns out that with a minority hashrate coin like BCH, you actually need 101% of the coin's hashrate in order to be able to sustain a 51% attack. What happens if you get 51% of the hashrate trying to perform an attack is that the attack will succeed for a short period of time until the difficulty falls to 51% of the original level. (Orphaned blocks do not contribute to the difficulty, and 51% attacks orphan 49% of all blocks.) At that point, miners from BTC will see the opportunity to earn 98% more money by mining BCH instead of BTC, and so a bunch of hashrate will join BCH, but these miners will be including transactions. The attacker will try to orphan these new blocks but will fail, as the honest miners will now have more hashrate.
How did they do that exactly? It seemed to me that their hashrate was just what would be predicted from the network difficulty, the transaction fees, and the exchange rate. Did miners fix the exchange rate somehow?

BCH was always mined by a hashrate minority. If the decision to create BCH had been up to the hashrate majority, then BCH would not exist.

Even Bitmain was [calling it a UAHF](https://blog.bitmain.com/en/uahf-contingency-plan-uasf-bip148/).

> UAHF: User Activated Hard Fork. Developers add a mandatory rule set to change the node software. These changes make previously invalid blocks become valid after a flag day, which do not require a majority of hash power to be enforced. Nodes with the rule set changes will follow this chain irrespective of its hash rate. The UAHF proposal is a peaceful and voluntary departure of different community members who have different opinions or visions, and it is not intended to make an attack against other blockchain(s), even if the UAHF chain has the higher hash rate.

On the flag day (November 15th), Bitcoin SV will start following a new rule set. They will allow transactions with more than 200 opcodes per input. Blocks containing transactions like this were previously considered invalid, but after Nov 15th they will be considered valid for Bitcoin SV. This rule will be enforced regardless of whether Bitcoin SV has a majority of hashpower or not. Thus, Bitcoin SV is a UAHF.
> There is no special oracle of truth in the real world. There are those with a good track record of reading the markets, successful investors, bigger miners.

So... people like Jihan Wu?

> Not sure if the fork will happen, but miners will have the final say,not only because the whitepaper says so

Final say? Absolutely not.

The whitepaper says that one CPU, one vote applies to determining the canonical state of the ledger when multiple valid histories exist. It does not state that one CPU, one vote applies to what the rules are that determine whether any given history is valid or not. 

You can't use a hashwar to force in a hard fork. It just doesn't work that way. If Jihan sets all of his hashrate on BCH and outnumbers BSV 20 to one in hashes performed, that will not cause BSV to fail. The BSV minority chain would continue to operate normally, and if the markets decide that BSV is the better option, then its price will appreciate and anybody who bet on BSV will get richer.

*Users* will have a final say by choosing whether to use SV or to dump it. Miners, at best, can make a suggestion to users, but we do not rule the roost.
> you just speculate about what users want with no source

No, I'm not speculating on what the users want. All I'm saying is that it doesn't matter what the miners want or what the miners guess. They aren't some special oracle of truth. The fork will happen, the markets will determine the winner, and miners will play only a minor role in the process.

If the miners disagree with the markets, then the miners lose.
The bug awemany found was for a final release (ABC) too. It also affected the most recent (beta) release of SV. Either way you slice it, it applies.
> more sensible road map to scale horizontally as well as vertically 


Devs will likely be a bit distracted around that date by the hard fork, which is scheduled for 2 weeks later. I would recommend using a date that is more distant from the fork date so that we can make the best and most efficient use of the stress test data collection opportunity.

I personally think somewhere in the December-February range makes the most sense, but a decent case can be made for mid-October as well.
It improves Graphene's efficiency by 7x (versus Graphene without CTOR) and improves Xthinner's efficiency by 2x (vs Xthinner without CTOR). It also makes the code simpler for both of those ptotocols.

Graphene with CTOR is about 28x more efficient than Xthin and Compact Blocks. Xthinner with CTOR is about 4x more efficient than Xthin and Compact blocks.

Citations available upon request, but I'm feeling a bit lazy right now, so I won't link them in proactively this time.
Vermorel's paper assumes code that we do not have. There's no way that two 64 core Xeon Phi processors are going to help when our implementations still have LOCK(cs.main) on nearly every performance-critical piece of code.

I'm [all for parallelization](https://github.com/jtoomim/bitcoin-abc/commit/b61d3dbd43e7dc3a22ccbfde9bc6030a78c03f17), and am actively writing code to do that right now, but bug-free parallel code is very slow stuff to write. If you add strong incentives to deploy parallel code quickly, the code will end up being a disgusting mess full of bugs.

One of the reasons that I'm so fond of the LTOR idea is that it will make the code cleaner and more elegant in about a half-dozen different ways. Clean code is fast code, and elegant code is less buggy code.

> I think a big problem is that miners currently are not using such systems

No, the big problem is that the software can only use one core of a CPU at a time. There's a reason why I use the Core i7-4790K in my main pool servers. That's only a $300 CPU. It only has 4 cores, but those cores can run at 4.4 GHz, which is about 2x as fast as the 2.2 GHz you get from each of the 16 cores of a $3000 [Xeon E5-2698v3](http://ark.intel.com/products/81060). At the time I bought those servers, the i7-4790K was simply the fastest option there is. 

2-3 years later, and the i7-4790K is about 20% slower than the new #1 chip. That chip is the [limited-edition i7-8086K](https://www.cpubenchmark.net/singleThread.html), which costs about $552. 

If you spend any more money than that, you will get worse performance.

But this is irrelevant, as CPU performance is not the bottleneck. Only [5-10% of the total block propagation delay comes from block validation](https://medium.com/@j_73307/block-propagation-data-from-bitcoin-cashs-stress-test-5b1d7d39a234); almost all of it is network delay.

That network delay, again, is because of the code, not the network pipes. You just can't fix these software problems with hardware.
He [came around to the idea](https://www.reddit.com/r/btc/comments/9byuy5/please_set_up_your_full_node_servers_to_log/e575qi6/) after a (last-minute) test plan was published:

> I know it's not ideal, but I've been complaing this stress test had no real test plan, and therefore more of a pr stunt than an actual stress test. At least now there is a test plan, even if it is a last minute addition. 
Yeah, it's not like a hard fork to rewrite transaction history will [ever](https://ethereumclassic.org/) be [controversial](https://www.reddit.com/r/ethereum/comments/9083sn/eip999_drama_explained_simply_catch_up_discuss/). /s
Dude, READ THE ARTICLE.

ViaBTC finds a block **at height 546012** at around 18:52:32

BTC.TOP finds a block **at height 546012** at around 18:53:05, starting an orphan race.

~~a split second~~ Four minutes later ViaBTC finds a second block at height 546013 at around 18:57:17, thereby locking in the block they mined earlier at height 546012 and winning the race in favor of ViaBTC. Their 546013 block effectively gave them a 25 BCH reward instead of just 12.5 BCH.

Parallel validation only makes a difference if two blocks from competing miners arrive during the 1-3 seconds that it takes to validate a block. That was not the case here.

> if ViaBTC was using parallel block validation is would have just added an empty block on top of BTC.TOP's block while it validated it, 

No, they would not do that, because that would orphan their own block and cost them 12.5 BCH. They chose to mine on top of their own block instead of BTC.TOP's because that was in their best interest, not because anybody's block was too slow to validate.

> the way I understand it you are half correct. If there were 100 transactions added to the network and each node got 7 different random transactions

The way you understand it is incorrect. The transactions were not random. The code sends out INVs for the transactions in topological order with the highest fees first.

https://github.com/Bitcoin-ABC/bitcoin-abc/blob/master/src/net_processing.cpp#L3584

https://github.com/Bitcoin-ABC/bitcoin-abc/blob/master/src/net_processing.cpp#L3597
That's the approach we used in 2015, but in order to get the information we wanted, we still had to patch bitcoind. First, we wanted millisecond resolution, whereas bitcoind by default only gives second resolution. Second, we wanted to know when an INV was received, when the getblock (or getthinblock) request was sent, how long it took for the response, and how long validation took, and in order to get that information from from default config bitcoind, you have to use the debug=net option, which in turn gives three lines of debug text for EVERY TRANSACTION. That's unruly. There are a couple of other issues (e.g. timestamps, mempool synchrony, UX) that are better solved by directly editing the bitcoind source.
What price would BCH be at if it didn't have technical debate that fostered creativity and critical thinking?
> Re: #1 (sharding / horizontal partitioning, whatever), it occurs to me that private databases for the node will likely want to use siphash protection techniques precisely for the DOS reasons you mention. This means that the incoming order will be made irrelevant anyway.

No, I don't like siphash. It's kinda slow. I think using a tree (either for the whole UTXO db or as the datastructure within each hashtable bucket) or dynamically sized shards are both better approaches. Dynamic shard sizing with OpenMP is simply a matter of using #pragma omp parallel for schedule(dyanimc).
I prefer to compete by [writing](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244) [code](https://github.com/jtoomim/p2pool/commits/1mb_segwit) instead of patents.

Actually, I take that back. Those are all open source projects. I prefer to collaborate and contribute rather than compete.
Except that there are some people who think that the blocksize limit is an important protection against some strange economics of orphan rates that happen when block propagation is slow. People like me.

If you argue for removing the blocksize limit now, before we have a chance to write the code to improve performance enough to make it safe, then I will end up spending all my time on social media trying to argue for why we should not remove the blocksize limit until after we fix the performance issues, and that will make it hard for me to write code to fix the performance issues.

So can we just postpone the issue for now? Trust me, [I like big blocks and I cannot lie](https://www.reddit.com/r/btc/comments/9c74pg/bitcoins_come_back_feat_sir_makesalot/). I want to get us to the multi-GB era within a few years. But I want to do that in a fashion that's safe against the broken mining economics that happen when orphan rates get high, and that means fixing stuff before putting things in a situation where they could break.
Hard fork chainsplits are the method of last recourse for resolving blockchain conflicts. So I think deadalnix is exactly right. *If no agreement can be found*, then we should just relax and let the fork play out. Once it's done, there will be no more conflict, as each side will have control over their own little sandbox and won't have to fight with each other any longer.
The current order encoding is naive and uncompressed. The length of the Graphene order encoding does not vary based on the actual order. It's just a flat array of offsets, one for each transaction in the block. It is definitely possible to write order encodings that are more efficient than this. I developed [one such encoding myself](https://github.com/jtoomim/orderencode), and it seems that this encoding might end up being used by Dash in their Graphene implementation. But the point is that the encoded data won't automatically shrink; we have to work to make it shrink. And unless a mandatory ordering is imposed, the worst case scenario will always be just as bad as we saw today.

One of the reasons why a canonical ordering may be important is that there is an attack which can be performed using shuffled orders and graphene, and it can only be blocked by a canonical order. It's called the Outcast Attack. 

[Outcast attack concept](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1219#post-78785) (second half of post)

[Outcast attack math](https://bitco.in/forum/threads/gold-collapsing-bitcoin-up.16/page-1220#post-78819)

The outcast attack is not likely to be quantitatively significant until we get to the 1 GB block era, so it's not necessary to fix the vulnerability right now. But I think it's a good idea to fix it soon. And it can only be fixed for Graphene by making a specific ordering mandatory.
> This is false and you know it. For example if ABC had hard forked a larger block size without replay protection it would have been wiped out due to constant reorgs from the dominant BTC chain.

That would have qualified as a "strictly expanding hard fork" instead of a "mutually exclusive hard fork". Yes, strictly expanding hard forks are susceptible to wipeout. Replay protection is one way to make a hardfork mutually exclusive, but it is not the only way. The addition of the new opcodes and the elimination of the poison pill are sufficient to make this a mutually exclusive hard fork. 

Edit: If it were a strictly expanding hard fork, there would be the possibility of wipeout, but *in the opposite direction*. For example, the [script length limit](https://github.com/bitcoin-sv/bitcoin-sv/commit/a4bfa1ca55b7f4291b6e790f2c2ae2b063147b6d) by itself would be a strictly expanding hard fork. Any block mined by ABC will have an acceptable script length to SV, but some blocks mined by SV will have unacceptable lengths to ABC. This means that ABC can unconditionally reject the SV chain, and will continue to mine on its chain if it has a hashrate minority. On the other hand, if ABC has a hashrate majority, then SV will follow it no matter what, and any blocks mined by SV with long scripts will end up orphaned.

For more information on this distinction, I suggest you check [this comment](https://www.reddit.com/r/btc/comments/9c8iom/confirmed_bitcoin_abcs_amaury_is_claiming_they/e590tyj/) from me or [this blog post](https://vitalik.ca/general/2017/03/14/forks_and_markets.html) from Vitalik.

I'm entirely sober. Please stick to the technical discussion. Sure, I'm getting downvoted for expressing an unpopular opinion. I don't care about that. I am only interested in explaining the truth as I see it. It's possible that my perception of truth is flawed, but it would still be dishonest to say otherwise than I believe. I think that intellectual honesty is more important than popularity.
It's still more of a PR stunt than anything. My estimate is about 66% PR stunt.

It just happens that this PR stunt is also a great opportunity for miners and developers to refine their systems. If it weren't, I (and others) probably would have spoken out against it when the idea was proposed.

P.S.: You misspelled my username. It's got an m at the end, not an n. Fortunately, you were replying directly to my post, so I still got notified.
Let's not feed the trolls, everyone. Downvote and move on.
No you don't, because fuck you. /s

I mean, that line never gets old.
As a miner who has been paying very close attention to mining hardware over the years, it explains a lot. The progression in efficiency of S3 -> S5 -> S7 (all on a 28 nm process) was very impressive, especially given the rapid schedule. The subsequent 2.5 year stagnation at the S9 always seemed strange to me. Finally, a month ago, Pangolin announced their Whatsminer M10V1, which was more efficient than the S9 (not surprising) even though it was still a 16 nm chip (very surprising). This indicated that Pangolin had a much better design than Bitmain did. The assertion that it's the same design head, but now with more experience, completely makes sense.
I have done some very rough benchmarks on IBD using [my implementation in Bitcoin ABC](https://github.com/Bitcoin-ABC/bitcoin-abc/pull/244), and it also appears to be about the same, i.e. within +/- 20%. I hope to do a more precise comparison soon.
~~Facial recognition AI? You wouldn't need to identify the face with AI, but just make sure that there was a face on security camera footage.

Or:~~ don't stock anything worth more than a hundred bucks, and/or make sure you have a good enough profit margin to be able to tolerate <= 0.01% losses.

Or buy insurance for that <= 0.01% fraud rate.
That's a fair point. I'll pluralize the pronouns.
There is no need to cover the current full block reward. Most of the mining reward currently is either accelerated depreciation of mining hardware (a miner usually lasts around 2 years before it's obsolete) or profit. As time goes on, we can get the same hashrate with (a) less power as hardware gets more efficient, (b) less obsolescence as it becomes rarer for efficiency to improve with new hardware generations, and (c) less profit as the market stabilizes and the risks of mining fall.

The amount of mining reward we need in order to sustain a safe hashrate level is better thought of in terms of real (or approximately, USD) value, not in terms of a number of BCH. We're currently using about 5 GW of power in SHA256 mining, which consumes about $250k per hour ($25k/block) in electricity at $0.05/kWh. In your $1m per BCH scenario, that means the block reward would need to be a bit above 0.025 BCH in order to sustain our current mining infrastructure size. At 1 satoshi per byte, we would achieve that with 2.5 MB blocks. At 1 satoshi per 225 bytes, we would achieve that with 562.5 MB blocks.
They are currently selling the 17 TH/s, 0.083 J/GH model for about [$1100](http://www.innosilicon.com/html/product/index.html). For comparison, Bitmain is selling a 14 TH/s, 0.1 J/GH model for about $600.
It is correct, *ceteris paribus*. Block propagation time has gotten a lot shorter per kB since the development of Xthin, Compact Blocks, and FIBRE in 2016-2017. To send a 1 MB block to a peer, it used to require the sending of 1 MB of data. Nowadays, a 1 MB block can be encoded in about 20 kB of network data. Consequently, since early 2017, orphan rates with 1 MB blocks have been much lower than they were in 2013 with 100 kB blocks.

That said, in 2018, blocks that are 2x as large have a ~1.9x higher chance of being orphaned. That was true in 2013 as well. The relationship only breaks down if you compare blocks sent via different protocols.
Archive nodes are more than full nodes, especially in Ethereum. Archive nodes store the state trie at every point in the past in addition to all blocks. Full nodes just store all the blocks and prune the excess data from the state trie. Archive nodes are more like storing a copy of the UTXO set at every Bitcoin block, which is not something that Bitcoin nodes do. For more information on this distinction, you can read this article:

https://dev.to/5chdn/the-ethereum-blockchain-size-will-not-exceed-1tb-anytime-soon-58a
Vanity addresses can only have around 5 to 7 characters specified with a reasonable amount of computational power. The addresses described by OP have almost all of the characters specified as 0, which means that they are not vanity addresses and are instead precompiled contracts.
I think it's far more likely that Halong Mining stole Bitmain's designs. These machines look nearly identical to an Antminer S7 or S9, and the purported efficiency improvements are pretty small (about 10%) and within experimental error, so it's quite possible that these machines are just S9s but with a slightly different chip voltage and/or frequency.
I acknowledge that we had a ton of spam around July-October 2015. The October spam attack consisted of transactions between 14780 and 14800 bytes in size, IIRC. The October spam transactions had very low fees, and were rebroadcast about 2 times per day for at least the next 6 months. 

Since October 2015 (and the rebroadcasts), I have not seen anything that was clearly spam. I have seen a few things that were weird, but they've usually made more sense as the activities of a poorly confugured exchange or mixing service rather than a spambot. This instance has a couple of attributes that hint at spam, but it's still ambiguous.

"Creat[ing] a fake emergency" requires *constant* spam, which is prohibitively expensive. Creating a couple of 1.3 MB blocks, on the other hand, is 10x to 1000x cheaper. That makes the hypothesis that this is spam more plausible than the hypothesis that we've been constantly spammed for 2 years. 2 years of spam requires someone with deep pockets, but 2 blocks of spam just requires someone with pockets.
>> regular transactions have no witness discount

>Wrong. Regular SegWit transactions also receive the data discount. Only legacy transactions receive no discount.

I think the two of you are disagreeing about what "regular" means in this context. When he says "regular", he means "legacy", but you mean "organic but SegWit".
Bitmain is only planning on doing the hardfork if BIP148 wins. 

Adding hashrate to BIP148 will just make things worse -- either BIP148 loses but has significant hashrate and we have a chain split with BIP148 as the minority chain, or BIP148 wins and we have a chain split with a hardforked blocksize increase as the minority chain. Chances are that it will be the former condition.

I believe also that most of the 30% of SegWit miners signed the [Consensus 2017](https://medium.com/@DCGco/bitcoin-scaling-agreement-at-consensus-2017-133521fe9a77) scaling agreement, which I think is not consistent with the UASF idea. They plan to activate SegWit via miner signaling at an 80% threshold. As Consensus 2017 came later than the UASF, it's possible that some people who previously voiced support for BIP148 (e.g. Bitfury) may no longer support it.

Of that 30%, 9.5% is f2pool. Wang Chun has said that he thinks the **[UASF is evil](https://twitter.com/f2pool_wangchun/status/852394527079792645?lang=en)**. It's pretty unlikely that f2pool will join BIP148. So that's at best 20.5%, even if everyone else goes along.
Bitfury's hardware uses about 25% more power than Bitmain's current-gen gear and also costs more. Consequently, they aren't selling very much. Most of what Bitfury produces, they use themselves.
83 to 73 sounds like it could just be variance. Do we have any evidence showing otherwise?

My guess is that people are watching http://litecoinblockhalf.com/segwit.php, which shows a 24-hour average (576 blocks). The amount of variance over a 576 block interval is around ± 5%. A swing from 83% to 73% is about the size of the confidence interval for a 24-hour estimate of the SegWit support.

Edit: Support is back up to 76% now. Either Jihan turned some of his miners back off, or perhaps 73% was just a low point caused by variance.
"Evidence" is **[here](https://www.reddit.com/r/Bitcoin/comments/65g6b0/roger_ver_caught_red_handed_bribing_miners_to/dga6pos/)**. 

TL;DR: pool.bitcoin.com currently pays out 110% of 12.5 BTC instead of paying transaction fees.
> downvoted into oblivion

Since only the post author can see the scores, I'll give some data on that. The post linked to currently has a score of 17. Most of my other recent posts in /r/bitcoin have scores between 1 and 10. Choosing "sort by controversial" seems to move most of my posts in that thread towards the bottom of each nesting, suggesting that my comments are less controversial than the other nearby ones.
Geez, is this comment really noteworthy enough to make it a separate cross-post on r/btc? 
> timed attack

Or maybe it's just a weekly cron job.
> Not enough of a safety margin to prevents the last attacks apparently.

The absence of bugs is what prevents attacks, not safety margins. The attacks were ineffective after the bugs were fixed, and stopped shortly afterwards. Are you criticizing the current Ethereum network, or the old obsolete one in which gas costs were calculated improperly?

Ethereum has a much larger attack surface than Bitcoin because it is Turing complete. If you want to criticize Ethereum for having a large attack surface due to its (overly?) ambitious goals, that is an entirely valid criticism. However, that is not related to Ethereum's scalability features.

> Were you there when we did Olympic? Because I was there. When we hit 25 tx/s ONLY 2 MINERS ARE CONTROLLING the blockchain. That's the main reason block time was being bumped to 17s from the original 9s.

Valid, but again, the current clients are *way* faster than Olympic. On my laptop, Parity is around 2x-10x faster at processing new blocks as Homestead cpp-ethereum is (depending on the type of block, and whether it was a DoS block or not). I wouldn't want to do 25 tx/s on mainnet with Olympic-era clients, but with Parity and modern versions of Geth, I think we could handle it just fine.

> You are neglecting that p2p can't be modeled the same way as centralized system. A new node needs to catch up from beginning it is not enough that your system can keep up, it will also need to be 100x faster to be able to contribute to the network.

That's why I said 100 ms. A 14 second block time is 14,000 ms, so 100 ms is 140x faster than the actual block time. If I were neglecting catchup and propagation times, I would have said that Ethereum can handle around 9520 tx/s. But I didn't say that, because that would be silly.

> Yes, putting the number in will give you 13.6 instead of 12.7. Not a whole world of difference there, isn't it?

If you put it into the formula that ignores the adaptive gas limit, then yes, you get a figure of 13.6 tx/s. But the gas limit is adaptive. After a few hours of transactions at the limit, and without manual intervention to the contrary, and the gas limit would increase substantially.

Vitalik has **[suggested](https://blog.ethereum.org/2016/10/31/uncle-rate-transaction-fee-analysis/)** a 5.5 million gas limit as a good target for the near future with an acceptable safety margin. That gives a limit of 18.7 tx/s.

I think the 15 tx/s number is just a rough estimate of the limit because there is no hard limit, and nobody actually knows what the safe limit is, and people can't agree on what a safe-enough limit would be. So I suggest we not nitpick it, and we definitely shouldn't worry about 3 significant figures of precision.

> If you are going to cite real world scenarios you should do so both for Ethereum and Bitcoin. A typical DAO transaction for example costs 100,000 gas

A typical DAO transaction is something that Bitcoin simply isn't capable of. If you're comparing the effect of aluminum vs. steel on vehicle weight, you shouldn't compare a steel car to an aluminum airliner. Steel simply isn't practical for flying. Comparisons should be for two transactions that accomplish the same purpose on different platforms.

Even so, your DAO transaction only costs 5x as much as a simple payment transaction, which is comparable to the scalability advantage that Ethereum has. If 10% of transactions were 100k gas, Ethereum would still be able to handle several times as many transactions as Bitcoin.

> (Paraphrased) Ethereum makes security/scaling tradeoffs that I don't like

An entirely valid position. Enjoy your $0.25-$1.00 transaction fees.
I think Peter R was trying to make a distinction between a central property (in the sense of an essential, sine qua non property) and a property that people care about. 

The 21 million coin limit is not a technically unchangeable feature of Bitcoin. It's just a couple of constants in the code that keeps it what it is. As such, I would not consider the fixed money supply to be a central property of the Bitcoin technology. The PoW system is much more of a central property of Bitcoin. Even the Script processing language is more central to Bitcoin than the 21 million coin limit. 

On the other hand, the 21 million coin limit can be construed as a central property of the social contract that Bitcoin investors and users bought into. People made their decisions to buy or mine Bitcoin based on the fraction of the total currency which they expected to receive. Changing the limit but retaining the current issuance system changes that fraction post-hoc. This would be a violation of trust, and would piss everyone off. Therefore, it won't let it happen.

Most users of Bitcoin don't really care about the Script processing language. They just use it to send money from address A to address B, and aren't concerned with things like multisig, time-locked transactions, and Coinjoins. Even though Script is a central property to how Bitcoin works, it doesn't matter to most people. On the other hand, the 21M limit is a peripheral property of Bitcoin--it's just a couple code constants based on arbitrary decisions that Satoshi made--but it matters a lot to every Bitcoin user.

The fixed coin supply in Bitcoin has always been a valid topic of debate, and it has always been something for which around 90% of people have the same opinion. Because sentiment is overwhelmingly in favor of keeping it fixed, it will remain fixed.

Edit: Peter__R posted his own full description of his position **[here](https://np.reddit.com/r/btc/comments/5poe8j/can_any_bitcoin_unlimited_devs_preferable_peter_r/dcsv08l/)**. 
Fees are block-space-efficient. Transaction outputs are not. Doing pseudofees this way would nearly double the size and required fees for a small transaction.

Cost of including a fee: 0 bytes for the sender, 0 bytes for the recipient when spending (all fees get added together into the single coinbase transaction output, which exists even with no fees).

Cost of including a txout pseudofee: 34 bytes for the sender, 180 bytes for the recipient when spending. Assuming the pseudofee eventually gets spent, this adds about 214 bytes for each transaction that uses a pseudofee, or nearly the size of a small 1-in 1-out transaction. The pseudofee would have to be much larger than normal in order to account for this cost of claiming it. Also, it would be bad for Bitcoin's network capacity.

This proposal would not suffer from the **[propagation cost problem](https://www.reddit.com/r/btc/comments/5mzoeq/new_proof_of_concept_service_called_preferred/dc7utks/)**, though.

If you are a high-volume transactor, you could make something equivalent work efficiently using payment channels. Otherwise, it will be too expensive.

/u/HostFat
The graph is easier to read if you look at the 3-month moving average:

https://blockchain.info/charts/n-orphaned-blocks?timespan=all&daysAverageString=90

Note the "daysAverageString=90" in that URL. You can change that number if you want (e.g. [28](https://blockchain.info/charts/n-orphaned-blocks?timespan=all&daysAverageString=28)).
Orphan rates used to be around 1.5 per day, but are now about 0.35 per day.
> That's a soft-fork, not a hard-fork.

Ethereum can't do soft forks. They are incompatible with Ethereum's gas mechanism for blocking DoS attacks on contract execution time.

Normally, if a transaction execution time is excessive, the miner will still include it in the block but marked as invalid. The transaction has no effect except to pay the gas cost. This makes the transaction expensive for the attacker.

If a miner chooses not to include a transaction in a block for some non-protocol reason (soft fork or censorship), he no longer has access to the DoS protection mechanism. An attacker no longer has to pay gas in order to tie up the miner's CPU. The attacker can make as many CPU-heavy transactions as he wants for free.

Also, I do not think that gas costs could be a soft fork, even ignoring the DoS issue with soft forks. As far as I know, the amount of gas deducted from an account is specified exactly by the consensus rules. If a miner deducted a different amount of gas, the block would be invalid. It's not like Bitcoin where the transaction fee is specified by the creator and taken by the miner (or discarded). In Ethereum, the transaction creator specifies the maximum they're willing to pay, and the miner gets whatever the actual execution used.
> As this was not known, a big percentage switch off was a totally possible scenario.

Actually, it was known, but it changed. The Bitcoin price was around $450 two months ago; now it's $650. Four months ago it dipped below $400. Mining is currently about 50% more profitable than we were expecting it to be at this point. I (and others) expected a moderately large switchoff if the exchange rate remained $450 or went down, but at $650 there was no risk of that.
Keep in mind that gaining 51% of the hashrate or stake does not grant someone access to your money. A 51% attacker cannot magically spend your money or use your stock; those tasks are still protected by the private keys you hold. All that a 51% attacker can do to you is (a) delay or prevent your transactions from happening, and (b) reverse recent transactions. 

Examples: (a) If you try to sell or exercise your stock options, the attacker might block that unless you pay a ransom or vote in their interest. (b) If the attacker makes a transaction that gives you a digital resource in exchange for you performing some action, you should mistrust that transaction because they could reverse it.

But they can't forge your signature.
> Is there any solution to this?

The solution is to just stop worrying about it. 

Gavin has the same key that theymos has. If theymos starts to abuse it, then Gavin can cancel any abusive alerts that theymos creates. If necessary, Gavin can also disable the key.

The current state of affairs with the alert key isn't that bad, and trying to change the key would be bad. So we just leave it as is.
Oh, excellent, they updated the graph title. (I asked a blockchain.info employee about that graph a few hours ago, since the old title was not very descriptive.)

Now that we know it's the **median** confirmation time, it's clear why it's not showing much delay. The median confirmation time will only show delays if more than 50% of transactions are being delayed. If we had 51% of transactions making it into the first block, and the other 49% of transactions took 100 hours to confirm, the graph would still show confirmation times around 10 minutes.

Since the only transactions that get delayed are the ~10% with the smallest fees, and those transactions tend to get delayed by hours, this graph will not reflect the delays we are seeing.

An open question: does this graph include transactions that never confirm (e.g. spam from October)?
Another motivation: We want these VPS nodes to be preferred targets for the DDoS attacks. Much better to DDoS some node in a datacenter (which can probably handle the bandwidth) than someone's home connection. The crawler searching for Classic "subver" version strings will not be able to find your VPSs if they do not have any open connection slots, so they will hit other users (who may actually be using them for wallets) instead.
I can answer this question. There are a few reasons:

1. The relay network is not reliable. It is not part of the actual reference implementation of Bitcoin, and requires on external servers that are currently run by one person. That person has even **[expressed intent](https://bitcointalk.org/index.php?topic=766190.msg13510513#msg13510513)** to stop supporting the network soon.
2. The relay network is not scalable. It has substantial per-peer memory requirements that would cause it to cost a lot more money to run if it were used by more than just miners.
3. The relay network does not work in adversarial conditions. If a miner wants to perform slow-propagation-enhanced selfish mining, it is trivial to make blocks which the relay network cannot accelerate. All the miner has to do is to mine blocks with unpublished transactions, such as spam that the miner himself generates. In this case, the relay network needs to transmit 1 MB of data for a 1 MB block, rather than just 4 kB. The relay network only works well in cooperative scenarios.
4. (a) Since it uses TCP, the relay network has some trouble crossing the Great Firewall of China quickly and efficiently due to packet loss. (b) Since it is (mostly) not a multipath system, it cannot route around one or two high-packet-loss link very effectively.

Note that 3 and 4(a) also affect Xtreme Thin Blocks just as much. 

How important these reasons are is up to interpretation. I personally think that even with these shortcomings, with the relay network, blocks up to 8 MB are *probably* okay (though I don't have firm data on this), and without the relay network, blocks up to 3-4 MB should be fine. 

However, I recognize that these issues are real. That's why I'm working on Blocktorrent. It should address all of these issues quite effectively.
My data did not include the relay network at all.

https://toom.im/blocktime

It will be difficult to make sense of it if you haven't watched my talk on it.

https://www.youtube.com/watch?v=ivgxcEOyWNs&feature=youtu.be&t=2h25m30s
> 1) After reading the arguments by Peter Todd and other core devs in regards of the scaling it makes sense. You can't keep increasing the blocksize, it's the wrong approach. Bitcoin by itself will never be able to handle the amount of transactions Visa can. 

Actually, you can. It's not super easy to do it, but with 5-10 years of work and some modest hardware advancements, we could get there. But that's besides the point. The question isn't whether to rely only on blocksize increases or not. The question is whether blocksize increases should play a significant role in the scaling of Bitcoin. I think it should. The Bitcoin Core developers think it should not.

I would like to see a Bitcoin in which blocks are large enough that everyone can make an on-chain transaction for about $0.10 if they want to, but where most people prefer to use Lightning most of the time for small transactions because it's faster and better-suited for retail transactions. I do not want to see a Bitcoin in which everybody uses Lightning for almost everything because that's the only affordable way to use Bitcoin. I do not want to restrict the growth of Bitcoin over the next two years with an unnecessarily low block size limit while Lightning is being developed. And most importantly, I want to make sure there's enough on-blockchain capacity and transactions so that miners get paid enough in fees. Excessive reliance on the Lightning Network can compromise the long-term viability of Bitcoin.

You mention Peter Todd. You might not know this, but Peter Todd would rather we reduce the block size. He thinks that 1 MB is too much. He would rather have congestion and lower on-chain capacity than let the block sizes get too big, let the blockchain and UTXO continue to grow at the current rate, and let mining orphan rates be what they are. 

Visa averages **[1971 transactions per second](https://icepay.com/payment-methods/visa-credit-card/)**. 600 MB blocks would be needed to handle that volume. Once we have a better block propagation algorithm like IBLTs or Blocktorrent, the hardware requirements for handling 600 MB blocks in a timely fashion would be about 1 Gbps ethernet, an SSD capable of at least 400k IOPS (or enough RAM for the UTXO), and about 32 cores at 2.4 GHz (10 second worst-case validation time). Such hardware is expensive right now, but by the time Bitcoin demand scales 1000x, it will likely be affordable. But whatever, I'm not saying we *should* scale with blocksize alone, just that we *could*. 

My personal goal is to get us to 10 MB blocks by 2020 so that miners can get enough in fees to survive the next halving. If a lot of miners fail to survive the 2020 halving, it can **[seriously jeopardize](https://www.reddit.com/r/Bitcoin/comments/47j3ly/why_i_switched_back_and_support_core/d0e57pl)** Bitcoin's security.

> 3) If you try to read /r/btc it's always the same, excuse my French, circlejerk. Always boiling down to the same issues they have with Core and Blockstream, instead of trying to focus on something positive. After reading this post about one of the main guys behind Classic, I started to have a bad feeling about it. 

This annoys me too, but I don't think it's fair to blame one piece of software for the actions and stupidity of its supporters. 


> 5) The fact that one of the Blockstream employees, Mark Friedenbach, didn't like the consensus and was open about it, showed to me at least that there is more going on than just Blockstream trying to push their agenda and that they do have, for now, Bitcoins best interest in mind. 

maaku's objection to the consensus was that he was not okay with the compromises that were being offered. Specifically, he did not like the reduction of the SegWit discount, and he did not want to commit to a hard fork blocksize increase at all. This suggests to me that a lot of the current developers of Core are too attached to their philosophical goals for Bitcoin to be able to compromise and give users what they want.

> 1) Note: Gavin is not an active programmer anymore and so he has nothing to do with Classic code. 

This is incorrect. Gavin wrote most of the **[2MB fork code](https://github.com/bitcoinclassic/bitcoinclassic/commits/0.11.2)** for Classic. (See the stuff around Jan 19th.)

Gavin has more total commits than anyone else except for Wladmir and maybe Satoshi (who didn't use git):

    git shortlog -s -n
      3259  Wladimir J. van der Laan
      1102  Gavin Andresen
       963  Pieter Wuille
       635  Philip Kaufmann
       533  Jeff Garzik
       331  Cory Fields
       288  Matt Corallo
       245  s_nakamoto
       226  Jonas Schnelli
       210  Luke Dashjr
       190  Gregory Maxwell
    
He ranked #5 in 2014. In 2015, he had 22 commits in Core, or about 40% of what Matt Corallo had, though Gavin spent most of his time on projects other than Core (e.g. 31 commits in XT). "Gavin doesn't write much code" is a common objection raised by people who want to marginalize him, but it's not actually true.

Note: Number of commits is not a good metric of the amount of contributions a programmer has made to a project. Neither is Lines of Code (LOC). There are no good metrics for code contributions, just a few different crappy ones.


> 4) Just increasing the blocksize doesn't work, you need an alternative, LN is the only real realistic solution I see. I don't see 16 MB blocks happening. 

The decision is blocksize + LN (in that order) vs. LN + blocksize vs. LN alone. 

I think that keeping blocks small while LN is not ready is a bad idea, as it will push users onto altcoins or private blockchains instead. I think that keeping blocks small after LN is ready is also a bad idea, as it will cause mining hashrate to fall, which in turn could make 51% attacks about 1000x cheaper via dark hashrate ("rental") attacks.

Lightning Network is cool, but let's not handicap what we already have just to make sure there's enough incentive for people to develop and switch to LN once it's ready. To borrow a metaphor from Alex Petrov, that's like jumping out of a plane in a wingsuit, then burning your parachute because you think it will give you more incentive to learn how to land without one. 
James Hillard [7:11 PM] 
@alp: Peter R is IMO is a sophisticated troll with those charts, they are just enough to convince those that doesn't know any better
4 

anduck [7:12 PM] 
i don't get that why people who want to improve bitcoin can't work for the same codebase

aknix [7:12 PM] 
so i take it governance is all that anyway cares about.. Grow up.. lol

alp [7:12 PM] 
@dts: that's an oldish picture

anduck [7:12 PM] 
maintainers don't choose after all; peer review, which is open to everyone, chooses

dts [7:12 PM] 
@anduck: what kind of user privacy things do you think Classic will decrease?

[7:12] 
@anduck: maybe they will use a stupider form of RBF that requires you to taint your utxo together(edited)

aknix [7:12 PM] 
What does privacy matter if the codebase doesnt improve?

brg444 [7:13 PM] 
yes, I forgot to highlight Xapo

[7:13] 
and Bitnet

dts [7:14 PM] 
Confidential transactions and Coinjoin need to improve a lot but there are few "business cases" that they enable specifically

anduck [7:14 PM] 
@dts: https://github.com/bitcoinclassic/bitcoinclassic/pull/16  https://github.com/bitcoinclassic/bitcoinclassic/pull/15

GitHub
Improve 0-conf tracking by cointracker · Pull Request #16 · bitcoinclassic/bitcoinclassic · GitHub
This change in 0.12 would make our 0-conf tracking much less reliable if deployed.

GitHub
re-enable UPNP by cointracker · Pull Request #15 · bitcoinclassic/bitcoinclassic · GitHub
UPNP makes our 0-conf tracking and compliance tracking more reliable by allowing us to make incoming connections to nodes behind firewalls, we should re-enable it by default.

[7:15] 
@dts: yes also they seem to be very much against opt-in RBF.

**alp [7:15 PM] 
It should not be hard to inject bugs into their code**

anduck [7:15 PM] 
and would prefer the FSS-RBF if some RBF...

[7:15] 
@dts: well, if lightning rolls out, coinjoin isn't needed IMO
1 

dts [7:15 PM] 
@bram: One of the main Classic ideas for improving Bitcoin is incorporating Bittorrent actually

bram [7:16 PM] 
@dts AUGH
1 

anduck [7:16 PM] 
@dts: but it could be done for bitcoin too

Aleph 0 [7:16 PM] 
blocktorrents, yes.

**alp [7:16 PM] 
bram would be a perfect mole**

Gregory "instagibbs" Sanders [7:16 PM] 
Wait what

dts [7:16 PM] 
Some Classic supporters claim that bittorrent will solve the propagation time issues for big blocks

alp [7:16 PM] 
they want to do somoething like bittorrent

Gregory "instagibbs" Sanders [7:16 PM] 
What.

aknix [7:16 PM] 
LMAO

alp [7:16 PM] 
its not exactly bittorrent IIRC

aknix [7:16 PM] 
im dying

anduck [7:17 PM] 
don't die.

Gregory "instagibbs" Sanders [7:17 PM] 
@bram go be their CTO of torrents

aknix [7:17 PM] 
wait torrent like DHT to improve speed lol

James Hillard [7:17 PM] 
The UDP part is the most interesting...since it might be able to punch through the GFW better

**Aleph 0 [7:17 PM] 
we need some people who are good at underhanded C.**

I do not offer cloud mining. I run a **[hosting service](http://toom.im)**.

Our customers purchase hardware on their own and send it to us. We give the hardware power, cooling, and maintenance. It mines to a pool they choose. We do not ever touch their bitcoin. Just their hardware. (Which is more valuable, of course.)

As for why you should trust me? Eh, better to verify. **[Read the code](https://github.com/bitcoinclassic/bitcoinclassic/pull/3).**

You may also find this informative when doing research on who I am:

https://www.youtube.com/watch?v=ivgxcEOyWNs&feature=youtu.be&t=2h25m30s
Yeah, a thorough examination of that proposal is definitely on the to-do list.
I do not understand your comment.
We're not dead-set on 75%. If the miners want that number changed, we'll change it.
One of BTCC's project managers told me over lunch about a previous DDoS attack they had experienced. 

They received an email from an anonymous hacker who demanded something like 1 BTC immediately, and warned that if they did not pay that DDoS attacks would commence. Further, the extortionist hacker warned that the price would go up if they didn't pay immediately.

BTCC did not respond. A brief DDoS attack commenced.

The attack was larger than BTCC had expected. I think he said something like 10 Gbps. During the attack, they got a few fretful calls from their DDoS protection provider, who said something like "This thing is huge! You guys aren't paying us enough for this!" So BTCC paid them more.

After the attack, they got another email from the extortionist: "Now you have a taste of what we're capable of. Please pay us 10 BTC immediately or more attacks will follow." 

BTCC did not respond. Another attack commenced, and lasted several hours.

During this attack, BTCCs servers experienced some performance issues, resulting in a partial loss of functionality. They upgraded their servers.

After this one, they got another email from the extortionist: "We will keep these attacks up until you pay! The price is now 30 BTC. You had better pay up before you go bankrupt! Mwa ha ha!"

BTCC did not respond. Attacks commenced. 

More threats were issues, more attacks performed. BTCC stopped noticing many of the attacks, as they usually failed to disrupt their networks for more than a few minutes after the upgrades they rolled out.

Eventually, they got a different email from the extortionist, saying something like, "Hey, guys, look, I'm really a nice person. I don't want to put you all out of business. What do you say we just make it 0.5 BTC and call it even?"

BTCC did not respond.

Eventually, they received the final email from the extortionist: "Do you even speak English?"
> My example was silly to be obviously something I'm against. The story of me having to sift through the new ruleset, decide if it's for me, then decide if the old chain is just so weak that I have to accept the new one, is still there.

And the argument that you have to do the same thing with a softfork, except that you no longer have the option of the old chain, is also still there.

>>I think the ability to stay on the legacy chain if you disagree with the effects is a nice safety feature of hard forks.

>To each his own, but all I want is to make sure my transactions are included in blocks and stay that way. Two major coins using the same PoW function is a recipe for instability in hashrate and reorgs.

It's a safety feature that I hope will never be used except on the meta-level as a game-theoretic aspect motivating behavior of actors to keep them honest. 

As for determining which branch of the fork you want to take, you can achieve this by asking the individuals you wish to transact with which branch they are taking and use that version of the code. If there is actually controversy about which branch your friends are using, then you can run both branches at the same time. It's not an elegant solution, but it's also not a likely situation.

> all I want is to make sure my transactions are included in blocks and stay that way

A softfork can be used to eliminate a transaction type that you care about, such as, I dunno, any transaction that comes from one of your addresses. A softfork is basically a controlled and institutionalized 51% attack.
> Sure! Let's imagine a land where Core hardforks once a year, without fail. This means that people won't be able to rely on the "math" of Bitcoin for longer than a year, but instead on meta-consensus data feeds, such as the latest blog post by Gregory Maxwell. Each user will then have to sit around and predict if the latest Core Fork will be accepted by miners, if they Don't Like Greg Very Much, etc.
> 

I don't think that is the hardfork mechanism that should be used. Miners can easily prove that they support a hard fork by voting on it, either by using coinbase messages (like the BIP100 voting) or block version voting. If you want to run your node in such a way that you can support the new block format if it gains a supermajority, but if support for the new block format is less than that you remain with the old block format, that is trivial to code. BIP102 does not include this forking mechanism, but BIP101 does. As such, I think this criticism applies only to naïve forking methods, and not to hard forks per se.

>> I think it would be better described as an obsolete chain, not a low-security chain.

>Says who? Someone is mining junk! It's invalid! I spend a lot of extra-consensus man hours and found what was happening. They implemented Greg Gets 10BTC Every Block and I don't agree. I will stay on this chain because I simply don't agree with the change. 

I'm now unclear as to whether you think the legacy chain is the low-security chain (because it has less PoW) or the new chain is the low-security chain (because it has less code review). In any case, I think that miners are unlikely to move to a branch that does something undesirable like giving Greg 10 BTC/block because it makes it unlikely that users will follow that branch in the long run, which means that assigning hashpower to that branch is likely a waste. I think the ability to stay on the legacy chain if you disagree with the effects is a nice safety feature of hard forks.

On the other hand (since you're using extreme examples), a soft fork can be used to perform attacks against a minority of users. Let's say a soft fork is implemented that says Greg Can't Spend His Coins. Any block in which Greg tries to spend his coins is now invalid according to the new rules, and will get orphaned. Greg has no recourse except to talk to the miners and get them to revert their changes or to buy up enough miners to have 51% of the hashrate supporting him. He can't simply stay on the old chain, because there isn't one.
They will soon, I hope.
> 6 months for SW sounds reasonable, but 3 months for a simple block size increase hard fork sounds scary.

6 months for SW sounds a bit scary to me, too. 

My rough schedule for the block size increase is this: 1 month of coding and code review, 1 month of testnet and/or regtest testing, and 1-4 months of deployment and activation grace period. 

It would be nice if we had gotten this done a year or two ago, eh?
> You do realise that Hearn and Andresen have been saying the same thing, right?
 
I think they have been saying that it *would* take many months to actually roll out such changes, not that it *should* take that long. The reason why I think they think that it would take so long is that it takes time to get everybody on board with the fork. That process was started quite some time ago, and people have been discussing the merits of different proposals for quite some time. The question is how far are we and much work do we have left? 

Keep in mind that BIP101's grace period was only two weeks. Gavin seemed to think that two weeks was long enough for people to upgrade their full nodes. Sipa seems to think that 6 to 12 months is the necessary duration. My opinion is 1 to 2 months. 

> But do they prefer an effective increase sooner or a hard fork sooner?

I do not currently have data on that. My impression from the discussion at the conference was that they thought that a hard fork sooner was preferable, but that's just an impression. I have not thoroughly discussed timelines or SegWit with miners yet. To-do list item.

> Because the Core team also wants an effective increase sooner than that, they're just worried about the risks of a hard fork.

Yes, that seems to be an attitude that is prevalent among Core (esp. Blockstream) developers and is not shared by the miners or most of the users.  Core Devs would get most of the blame in the event of a disaster, but they do not get a proportionate amount of reward in the event of a success, so I think they are suffering from a conservative bias compared to what would be optimal for Bitcoin as a whole. I think that Blockstream is serving as an echo chamber for anxiety and fear, and I think that this is a problem for all Bitcoin users.
> A hard fork could take a year to do safely, maybe more.

If that is the attitude that people have, then that is how long people will take to do a hard fork. 

Most of the miners I have talked to do not like that timeline very much, and prefer something much faster. Most of the Core and Blockstream devs I've talked to do not like a fast timeline very much, and prefer something more like 1 year.

We will have to get the relevant parties together and see if we can come to an agreement on the timeline. I think we should do that after we have agreement on what the other parameters of the increase should be.
> Not without first eliminating the risk of rogue transactions with validation times that scale quadratically with tx size. 

I will not support any blocksize increase that does not somehow limit the number of bytes hashed either per block or per transaction to reasonable levels. This is easy to do. Gavin's BIP101 implementation includes code that does this, and should be easy to merge into whatever hard fork branch we decide upon.

> Not without first eliminating the risk of rogue transactions with validation times that scale quadratically with tx size. And even with that in place miners are concerned about anything larger than 2MB for now. /u/jtoomim also found a limit of between 2MB and 4MB.
> 

No, I didn't find a limit of between 2-4 MB. I found data that made me think that 3-4 MB would be safe. I did not find data that made me think that above 4 MB would be unsafe.

I also had a test setup that did not accurately reflect mainnet's performance, and was designed to fail easily.
I do not believe this to be correct.
Not a random number.

https://github.com/bitcoin/bips/blob/master/bip-0009.mediawiki

BIP 9 is for version flags support, which is designed to allow for multiple forks to be deployed in parallel without running into interference issues like what we have with BIP101 and BIP65. When Core decided to implement BIP65, they chose to do it without version bits support and to just use v4 for the blocks.
This is a problem. Because the check in Bitcoin Core is for block version greater than v3, BitcoinXT will show up as supporting CLTV when currently it does not. 

The rules for BIP65 (CLTV) activation are this: At 75%, BIP65 nodes will reject blocks if they have a block version greater than 3 and if they include a transaction with the new OP_CLTV instruction incorrectly (i.e. will enforce OP_CLTV). At 95%, BIP65 nodes will reject blocks with a version of 3 or below, and will enforce OP_CLTV on all blocks. Because BIP101 nodes will be interpreted as enforcing OP_CLTV when they do not, this makes mining with BIP101 without OP_CLTV dangerous as soon as OP_CLTV reaches 75%.

I find it interesting that Core chose to use this v4 check after BIP101 and BitcoinXT had been released. 

The only way I see to resolve this is to add OP_CLTV support to BitcoinXT.
BIP100 will probably never be implemented. I think we've already passed the critical date for Jan 11th activation, since BIP100 requires 95% voting for 12,000 blocks.

I don't like BIP100 as much as BIP101. I might support it if were implemented, but I'm not sure.

Evil is subjective.
Mike Hearn's policy is to merge features from Core when Core makes a release. Currently, that means that OP_CLTV will get merged into XT when Core 0.12 is released. 

There are a lot of changes in Core master right now that I would like to work on in my own work, so I might ask Hearn to start merging early, but I don't know if he'll listen to me. I'm a pretty junior developer for XT.
I would prefer to be the only significant amount of hashrate during the initial stages of the test, as I intend to try to fork it, un-fork it, refork it, etc a few times, and that will be a bit more complicated if I have to coordinate with others to do so. I've got plenty of hashrate for this test without others' help.

We can do more distributed tests later, possibly after we migrate to another (BIP101 exclusive) testnet to try to test performance in a more reasonable scenario.
**Please** talk about anything Bitcoin in this sub. We need more non-BitcoinXT stuff in /r/BitcoinXT. BitcoinXT is a part of the Bitcoin ecosystem, so anything relevant to Bitcoin is relevant in this subreddit.

Discussion of altcoins might also be relevant, depending on the context. My opinion is that if someone posts something about an altcoin that is not relevant to Bitcoin in /r/bitcoinxt, it should be downvoted into oblivion.
> their companies depend on the Bitcoin network, and have chosen BIP 101 over Bitcoin XT as the way forward

and

> The first company to choose sides in the BitcoinXT vs BIP 101 debate was Bitpay

Huh? Does the author of this article realize that BitcoinXT is the first implementation of BIP101?

Also, it's worth mentioning that this article is dated August 24th, 2015. This is not news.
> Does anyone here have experience renting hash power from NiceHash? Is there a tutorial somewhere that shows how to rent hash power and point it to a BIP101 pool?

It's really really easy. You basically go to nicehash.com and read the instructions.

Doing it efficiently is another matter. p2pool has performance problems when the latency between the miner and the p2pool node is high. Nicehash has one server cluster in San Jose, and one cluster in Amsterdam. You don't want to use Amsterdam to mine to a p2pool node on the west coast of the USA, for example. (Our p2pool node has 25 ms latency to their San Jose datacenter, which is decent.) You will also lose some efficiency due to the latency between the actual miners and Nicehash's cluster, but you can't do anything about that.

The latency issue will not affect the number of bitcoin blocks found, but it will affect your share of the p2pool revenue. Thus, if you are a high latency miner on p2pool, you can think of the latency as you giving a donation to other p2pool users with lower latency.

There are other performance issues with p2pool, but for most of them, Nicehash makes the hashpower supplier responsible for them rather than the purchaser.

> In theory, I suppose the cost to rent the hash power should be similar to the bitcoins one would earn from mining with that hash power. Does anyone have insight into how the profit/loss works out in practice?

P2pool is a relatively small pool. Currently, we've got about 0.5% of the total network hashrate. Small pools have high variance. P2pool has had really good luck lately -- over the last 7 days, revenue has been about 200% of the expected value (http://minefast.coincadence.com/p2pool-stats.php). However, that could just as easily go the other way. You might rent 2 PH/s for a day and get literally zero revenue from it. 

Renting hashpower on nicehash has a premium associated with it. Miners will not lease their hashrate out to anyone for less than they can get by mining on a pool for themselves. I haven't looked at the actual numbers, but I'd guess that the expected total loss for using Nicehash with p2pool will be around 5-10% on average. Variance due to luck, even over a 30 day period, will be much greater than that, so it's mostly just a gamble with the odds stacked slightly against you. If you reinvest the revenue, you might be able to run a contract a few dozen times before you run out of money, or you might only be able to do it once. If you quit after a few rounds, you might even end up with more money than you started with.

The cryptoplay.net/vote reward will also help a little bit, maybe adding around 0.25% to 1% to the EV for mining. 

If you want, I can crunch some numbers on one of the Nicehash users who has been using my node exclusively to estimate how much s/he's spent on hashpower (or maybe ask) and compare that to the revenue s/he has made. I think the one who has been doing it the longest is probably around breakeven right now, and the one who has been doing it for a shorter time (on two nodes) probably has 50% more coin than he started with.

If you are thinking of doing this and using my node, I suggest you send me an email at j@toom.im with your approximate intended hashrate so I can help you optimize and keep you appraised of any performance/efficiency issues. These Nicehash rentals are straining node capacities a bit. So far, we've been able to handle the load, but performance can be improved.
A few things:

1. We fixed the problem with p2pool that was preventing BitcoinXT/p2pool blocks from getting the BIP101 block version number. 

2. A few people started renting hashpower on Nicehash and pointing it at p2pool.

3. p2pool had a lucky streak. Currently, p2pool has found 99% more blocks in the last 7 days than expected for our hashrate.
> I'd actually be alright with Back's 2,4,8 proposal. 

Bitcoin block size has been roughly doubling every year for the last few years. Before that, growth was even greater. If the 100% annual growth trend continues, blocks will run out of space in a couple of years following Adam's schedule. We would get:

* 2015: 0.5 MB blocks, 1 MB cap
* 2016: 1 MB blocks, 2 MB cap
* 2017: 2 MB blocks, 2 MB cap, mild congestion
* 2018: 4 MB blocks, 4 MB cap, mild congestion
* 2019: 4 MB blocks, 4 MB cap, 50% unmet demand
* 2020: 8 MB blocks, 8 MB cap, 50% unmet demand

We can handle 8 MB blocks in 2016. Why wait until there's enough demand for 16 MB blocks before allowing 8 MB blocks?

The cost of congestion is much greater than the cost of large block caps. The reason why I cannot support Adam Back's proposal is that it is planning for a capacity trajectory that is unlikely to keep up with demand. It's about half as much growth as we need, and it leaves no safety margin in case demand scales faster than anticipated.

https://blockchain.info/charts/avg-block-size?timespan=all&daysAverageString=1&scale=1
Bitcoin block size has been roughly doubling every year for the last few years. Before that, growth was even greater. If the 100% annual growth trend continues, blocks will run out of space in a couple of years following Adam's schedule. We would get:

* 2015: 0.5 MB blocks, 1 MB cap
* 2016: 1 MB blocks, 2 MB cap
* 2017: 2 MB blocks, 2 MB cap, mild congestion
* 2018: 4 MB blocks, 4 MB cap, mild congestion
* 2019: 4 MB blocks, 4 MB cap, 50% unmet demand
* 2020: 8 MB blocks, 8 MB cap, 50% unmet demand

https://blockchain.info/charts/avg-block-size?timespan=all&showDataPoints=false&daysAverageString=1&show_header=true&scale=1&address=
We're working on them. We have about 360 TH/s on our XT p2pool node, and 34 TH/s on our Core p2pool node. With 360 TH/s, we expect to find one block a week on average.
Cross-posted in /r/bitcoin as https://www.reddit.com/r/Bitcoin/comments/3hkh8h/i_strongly_disagree_with_the_idea_that_changing/
Theymos's claim that "most experts claim that bitcoin is currently ready to handle blocks above the current 1 MB limit" is incorrect. Even the most ardent small-block developers, like Adam Back, support an immediate block-size increase. (2 MB now, 4 MB in 2 years, 8 MB in 4 years: https://twitter.com/adam3us/status/633083909912162304)

The thing that "most experts" are afraid of is a hard fork without strong consensus, not bigger blocks.
> /u/cryptorebel lies and lies....I think he was banned for his aggression..

Correct.

And I was banned from /r/bitcoincashsv by cryptorebel for pointing out that cryptorebel was banned from /r/btc for his aggression.

https://old.reddit.com/r/PurseIO/comments/affda3/how_is_this_guy_buying_amazon_japan_gift_cards/ee36qc1/
You're pretty much correct. You should run a full node if you're a big business with e.g. >$100k/month in volume, or if you run a service that requires high fraud resistance and validation certainty for payments sent your way (e.g. an exchange). For most other users of Bitcoin, there's no good reason to run a full node unless you reel like it.

Personally, I think it's a good idea to make sure that people can easily run a full node because they feel like it, and that it's desirable to keep full node resource requirements reasonable for an enthusiast/hobbyist whenever possible. This might seem to be at odds with the concept of making a worldwide digital cash system in which all transactions are validated by everybody, but after having done the math and some of the code myself, I believe that we should be able to have our cake and eat it too.

By the way, all I see for your /r/bitcoin post where the post body should be is \[removed\], so I think the moderators (or possibly the AutoModerator) decided that this is not a valid topic of discussion in /r/bitcoin and suppressed it.
Are you referring to the ones that had less than 2,000 transactions in them (less than BTC gets with 1 MB blocks!), which used 100 kB OP_RETURNs in order to fill up the blocks without requiring significant block propagation bandwidth or transaction verification resources?

If so, then sure, avoiding high transaction throughput is a perfectly valid way to avoid the mempool desync issues that can happen at high transaction throughput.
Option 1: By 2025, we will have UTXO commitments. UTXO commitments allow for compact fraud proofs. 

Any computer will be able to download a block at a random height (possibly rounded to the nearest 100 or so, if not all blocks get a commitment), get the UTXO commitment from that block, then download the UTXO set as of that block. The UTXO commitment can be checked against the downloaded UTXO set. This node can then validate blocks starting with the one it got the UTXO set for.

If it detects an error or fraud attempt, this computer can provide a compact proof that shows where the error occurred. If a miner tries to spend an output that has already been spent, the fraud proof will be the transaction that previously spent that output, plus the merkle path to the transaction from the block it was included in. If a miner tries to spend an output that does not exist, the fraud proof will be the merkle path to the point in the UTXO set (based on the UTXO commitment) where that output should have been, plus the merkle paths to the places in the block where the transaction generating that output should have been in the blocks between the UTXO commitment point and the fraudulent block. If a UTXO commitment itself is invalid, you can prove this by starting from the previous commitment, and pointing out the correct value of the erroneous key in the UTXO database via the same methods described for invalid spends.

The resources needed to generate these fraud proofs is moderate. The resources needed to verify them is low.

Option 2: Just run your own full node. 128 MB blocks every 600 seconds is really not that expensive. It's about `128 MB * 8 bits/Byte / 600 sec = 1.7 Mbps`. Allowing 10x overhead from INVs and uploading old blocks to syncing peers, that's about 17 Mbps. Getting 17 Mbps of uncapped bandwidth is not hard today, and in 2025 it will be a piece of cake. Note: you don't need to store all 7 TB/year of data. Bitcoin full nodes support pruning old blockchain data using the `-prune=xxxx` command-line option. But if you do want to store all 7 TB/year, that's less than $12/month with 2019's HDD prices.

> It's clear to me now that BTC and BCH just want different things.

What I want is a superset of what the typical BTC user wants. I want free-as-in-speech-and-as-in-beer money. I want something that's censorship-resistant, verifiable, fungible, scarce, trustless, permissionless, fast, cheap, and scalable.

I've looked at the math and the code, and I'm convinced that achieving all of those things in one cryptocurrency is feasible.

> It's equally ignorant of BTC users to mock you for wanting to cheaply transact on-chain (Which they argue isn't needed anyway) as it is for BCH users to mock them for wanting to cheaply verify the integrity of their monetary system. (Which you argue isn't needed anyway)

I don't think the BCH community thinks its unnecessary to verify the integrity of the blockchain. BSV community, maybe. But most BCH proponents are less trusting than that.

BTC proponents would rather have fees hit $1/tx to $50/tx (e.g. December 2017) in order to keep the cost of running a full node at $5/month or less. BCH proponents would rather have the cost of running a full node approach $50/month (enough for 1 GB blocks with today's hardware) in order to keep fees at $0.01/tx or below. At $50/month, most businesses, developers, and motivated hobbyists can easily run their own full node. The chance of miner fraud going undetected in that situation is tiny.
Node policy limit. Transactions larger than 100 kB are considered non-standard. They're valid in blocks, but nodes with default configuration won't accept or relay them otherwise.
> I agree that case is interesting: but the fibre approach achieves that, and none of xthinner/graphene/compact blocks/xthin come close to fibre for that case.

Yes, FIBRE handles that case well. UDP+FEC does wonders. But Xthinner is not equivalent to FIBRE; it's equivalent to Compact Blocks. A version of FIBRE could be made that uses Xthinner instead of CB as the base encoding layer for the condensed block representation.

I hope to be able to build a FIBRE-like FEC system into Blocktorrent, but I'm currently unsure with how to make that work given Blocktorrent's don't-forward-packets-until-checking-the-merkle-root-and-PoW rule. Any FEC/erasure code used by Blocktorrent will need to be systematic. I think there are some Raptor codes I can use for that, but I haven't looked into the details yet. I expect that a code exists such that most packets will be verifiable back to the merkle root immediately upon receipt, but the FEC-only packets won't be verifiable until the whole block has been reconstructed. This will likely result in Blocktorrent not being quite as good as FIBRE in terms of latency, but maybe that's just the price that needs to be paid for having a completely trustless p2p protocol.

> Hm I thought I did

Yeah, I guess you did. My mistake for not reading you carefully enough.
Xthinner is a stepping stone on the path to Blocktorrent. Blocktorrent should "solve" the problem of block propagation once and for all.

Xthinner improves block propagation by 3x, but still suffers from TCP congestion control's delays and the sequential download-verify-upload per-hop process. Blocktorrent will bypass TCP delays and will enable the download-verify-upload process to happen in parallel and independently on 1 kB chunks instead of the full 1 MB or 1 GB block. Blocktorrent should provide a 10x improvement inititially, and will allow propagation performance to scale linearly with bandwidth.
> It'd be nice to hear if ABC is planning to support?

Amaury is telling me on Telegram that I need to start submitting small chunks ASAP because code review on a big 2000-line project is a bitch. He's totally right, of course.

> I guess this doesn't require a hard fork, so could be rolled out any time?

It *required* a hard fork. But we did that in November, 2018.

But yes, it can be rolled out at any time. Like last night. Because *Xthinner is now active on mainnet.* Only between my two nodes so far, but still.
... [me.](https://github.com/jtoomim/bitcoin-abc/commits/atmp-inflight)
I prefer not to receive donations. I'm well-funded by [my mining company](https://toom.im), and want to be a net donor, not donee.
One of the causes of the increased transaction volume is a spammer who has been sending 0.00001111 BCH transactions with an OP_RETURN advertising his service to a ton of different addresses. Here's one of his addresses with 1193 transactions so far:

https://explorer.bitcoin.com/bch/address/bitcoincash:qrwur8aj3nkp00hzd3yyqr5xgnx4c6lhwuwtk5qtkr

And here's another one with 948 transactions:

https://explorer.bitcoin.com/bch/address/bitcoincash:qp5y8kjpfnh2zum72uju56ald08xlveksy2z46c29z

A second cause for the increase is Cashshuffle, which allows people to get more privacy by mixing their coins.

A third cause is likely the increase in BCH's exchange rate. Exchange rate changes (especially increases) tend to correlate with on-chain volume.
Most laypeople are thinking about *neocortical* neurogenesis, but don't use that qualifier word. Adult neurogenesis happens. Adult neocortical neurogenesis does not happen.

In 99.9% of the brain, we stop growing new neurons in adulthood.

> we stop growing new braincells

All over the brain, we grow new glial cells. Glial cells are brain cells, so in 100% of the brain, we do grow new brain cells in the brain.

So... technically, that statement is correct, but for entirely the wrong reasons.
> I want no deviation at all from the principle of hashpower deciding

And if the hashpower is following Avalanche, they will decide to only build upon the chain that Avalanche decides has the first-seen transactions. You don't need to run Avalanche yourself; you can just follow their hashpower.
> Do you have a source for the last sentence?

Nothing written down, just conversations I've had with other industrial miners over the years, and the research I've done myself on potential locations for my facilities.

Miners generally don't publish lists of the best places to mine because power availability in those regions is limited, and prices respond to demand. If a bunch of other miners come to that region, prices go up.
Most releases of geth have silly names. E.g. [Gethy McGethface](https://github.com/ethereum/go-ethereum/releases/tag/v1.4.2). The title was no misspelling; it was a reference to the fact that the original Constantinople fork was delayed due to a last-minute bug discovery. I was just suggesting a different name that I think reflects the fact that the fork is being resumed. While it might have been delayed for a bit, you can't stop Constantinople.
That's assuming that you've already transmitted the transactions. So instead of requiring that each transaction be transmitted 2x -- once when the transaction is announced, and again when the block is announced -- Xthinner allows you to do that with only 1.004 times the transaction's size. (This ignores bytes used for INVs and uploading the transaction to other peers.)

The purpose of Xthinner is not to reduce total bandwidth usage. The purpose is to make the critical code path faster. The economic incentives of Bitcoin require block propagation to be very fast relative to the block interval. In order for mining to stay decentralized, we need to be able to propagate blocks to all miners in the network in no more than about 20 seconds. Xthinner (and Compact Blocks, Xthin, and Graphene) all make this critical step faster by making use of bandwidth during the other 580 seconds per block (during the non-latency critical stage) to preload data and reduce the amount of data transmitted when the block is announced.
Satoshi was mostly a Windows person.
P2pool has been around since 2011.

https://blockchair.com/bitcoin-cash/address/1Kz5QaUPDtKrj5SqW5tFkn7WZh8LmQaQi4
My opinion is that using hashpower to damage someone else's chain is morally repugnant. One of the exciting things about cryptocurrency for me is that it creates an environment that fosters permissionless innovation. If a team thinks they can do a better job than the community they're splitting off from, I think we should let them try.
And we emitted about 21,600 more tons of CO2 in the process, and spent about $3.4 million dollars of *individuals'* money, uncompensated and voluntarily, to do so. A few rich people did this because they could afford it and because they believed in the cause, while most of the rest of the BCH community stood by and watched. While I'm very happy that they stood up to the plate, this is [not a sustainable solution](https://en.wikipedia.org/wiki/Free-rider_problem). We need something better than rich people's charity.
We'll see if the code is finished in time.
If a person sends $1 million to an exchange, waits 10 blocks for it to confirm, trades it for another coin and withdraws it, then publishes 15 competing secretly mined blocks which rewrite history and invalidate the $1m deposit, do you think that is completely okay? In your opinion, should everybody else just accept that new chain because it has the most work? Or if you think that there is something undesirable about that scenario, how do you think that economics would take care of it and prevent that scenario from happening?
Yep. It's a scenario that demands manual intervention, as there's no clear correct answer for all situations.
They don't have the hashrate to do it. They would need about [2x as much](https://www.reddit.com/r/btc/comments/9yy7e6/bitcoin_abc_0185_has_been_released_this_release/ea54yn0/) as ABC has, and they don't have anywhere near that amount.

Even if they did, we can just resolve the chainsplit manually. Either way it goes is not a big deal. 

Edit: not a big deal relative to the cost of attempting the attack.
This dataset is non-mining nodes.

We have observed high orphan rates coming from miners themselves. We've seen [10 orphaned blocks so far](https://www.reddit.com/r/btc/comments/9yimel/it_appears_the_bsv_chain_is_currently_being/ea2722z/), and estimate there are another 10 orphaned blocks that we didn't see, giving a total orphan rate of 4.6%. That indicates block propagation averaged [28 seconds](https://www.reddit.com/r/btc/comments/9yimel/it_appears_the_bsv_chain_is_currently_being/ea1on4h/) for that (older) dataset of blocks. That dataset's blocks were on average 5.018 MB in size, indicating a propagation speed of 0.18 MB/s between the block creator and the average miner.

This newer dataset of only non-mining nodes shows a block propagation rate of 0.29 MB/s between the first and the average non-mining full node. As this dataset skips the first hop from the miner, it makes sense that the non-mining node dataset would show faster speeds than is predicted by orphan rates.
And Bitcoin.com was not the only pool mining ABC+BU today either, though it was clearly the largest.
And full blocks.
Currently, all transaction verification needs to be done by a single thread, because transaction verification requires acquisition of both the cs_main and mempool.cs locks. Parallelizing transaction verification is desirable, but only BU has managed to do it so far in released code, and they had to make some pretty substantial changes to their codebase in order to pull it off. 

I've done some work on [parallelizing the signature verification of AcceptToMemoryPool](https://github.com/jtoomim/bitcoin-abc/commits/atmp-inflight), but it's not quite done yet, and it's certainly not in Bitcoin SV.

Currently, non-standard transactions only get processed if they're found in a block. Nodes that use any of the binary releases of software will reject transactions sent to them over the p2p network otherwise.

P2SH transactions can probably have a variant of this attack while still being standard.
/u/chaintip
