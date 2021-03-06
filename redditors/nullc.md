Ahh… the old mandarin in the middle attack.
Recently?  Big conference. 18th floor, top of the building. Everyone going down the limited number of elevators at breaks. Big lines.

People are not going down the stairs by one floor because if you hit down you'd just get a completely packed elevator.   My solution? Go down a floor. Hit _up_. Board empty elevator. Welcome people on at the 18th and enjoy my quick trip down.  The greatest hacks are the simplest.... though it would have been better if it didn't fail the categorical imperative.

Of course, if I wasn't an American I'd just take the stairs all the way down.

*(Edit: s/to/the/ and, while I'm editing, mention that the 18th is the top)*
In the US, at least, destruction of evidence in a civil case is treated as if the evidence were maximally against your interest.
Better would have been.

Dad: Oh. God. Thank you!

Dumbass: What?

Dad: That kid was a pest. You can keep her.<click>

Reddit, I think you're jumping the gun based on watching a personal repository. 

I think this is just some testing code-- he hasn't discussed this particular change with the other core developers; I for one would vigorously oppose it: for one, it's actually /broken/ because it doesn't change the protocol message size (makes for a nice example of how misleading unit tests often are; in this case they're vacuous as they don't catch that blocks over about 2MB wouldn't actually work). It's also not consistent with the last discussions we had with Gavin over his large block advocacy, where he'd agreed that his 20mb numbers were based on a calculation error. --- this without getting into the subtle concerns about long and short term incentives which are under-researched, or the practical issue of increasing node operating costs in a network with a node count that has fallen so much).

If y'all go around making a big deal about people's sketchpad work in their personal repos it creates an incentive to move all your work to private repositories where people can't get at them and read too much into them.  I'd suggest you try to avoid doing that. :)
The lesson Ethereum's operators are teaching is implicitly this:

When Vitalik's funds are lost due to a contract executed to the letter with unwelcome results: Great choice.

When Vitalik's funds are not lost, but an even larger dollar value is lost due to terrible foot-gun "address" design and Ethereum software misbehaviour: Bad choice.

The point they're missing? No one should have that power. If there is even a choice to make the system has already failed. Paypal and Visa do reversibility better and didn't require a pre-mine now worth billions of dollars.
Because the quotes are irreparably messed up I'll write up a long form explanation:

Some background: MtGox runs custom wallet software.

This is a reasonable and common practice for a service of its size and nature.

Getting a wallet implementing right isn't easy as there is very little room for error, much like the rest of the Bitcoin system.

Some have criticized their use of custom software here but it is a
reasonable and common practice for a service of its size and nature. The reference client's wallet is basically suitable for small scale single party use. I would not recommend something like MtGox use the reference node wallet, at least not without a healthy layer of abstraction on top of it— relieving it of duties harder than key management and chain monitoring— or otherwise improving it greatly.

(For that matter, MtGox's wallet software has caused them fewer concerning problems than some other companies. (E.g. some have completely re-implemented the Bitcoin protocol, incorrectly, and exposed it to the outside world and suffered numerous local blockchain rejecting glitches). Though its certainly taken Gox a fair amount of time to sort things out.)

I first heard people reporting stuck transactions back in ~September. I looked into it and determined that Mtgox was spending immature coins.
Freshly generated Bitcoins (from mining) can not be spend until they are at
least 100 blocks deep in the blockchain. This prevents the funds from
vanishing forever if the chain reorgs. I pinged magicaltux and after a
couple tries got ahold of him. I think they also wasted some time on 
dead ends trying to resolve this before the actual nature of 
the problem was brought to their attention, e.g. raising their transaction
fees with a mistaken belief that their fees weren't high enough.

Mtgox wasn't tracking if the coins were freshly generated or what their
height was in their software. Including this data would apparently be a
non-trivial change, and for high risk finance software even a trivial
change takes a lot of work. I suggested a workaround (basically, just try to
spend the oldest coins), and as far as I know they implemented it and
it was effective.

They continued to have problems with stuck transactions after, and further
analysis revealed that they were producing transactions with excessively 
padded signatures. A minor tangent is required here:

There is a characteristic of the Bitcoin protocol where its possible for
a third party to take a valid transaction of yours and mutate it in a
way which leaves it valid and functionally identical but with a different
transaction ID. This greatly complicates writing correct wallet software,
and it can be used abusively to invalidate long chains of unconfirmed 
transactions that depend on the non-mutant transaction (since transactions
refer to each other by txid).

This issue arises from several sources, one of them being OpenSSL's
willingness to accept and make sense of signatures with invalid encodings.
A normal ECDSA signature encodes two large integers, the encoding isn't
constant length— if there are leading zeros you are supposed to drop them.

It's easy to write software that assumes the signature will be a constant
length and then leave extra leading zeros in them.

In order to eventually remove this malleability flaw we've been gradually 
tightening the rules that govern what transactions nodes in the network
will consider valid when they relay them or mine them. In Bitcoin 0.8— after
months of work chasing down software authors to get them to fix their bugs
transactions with these invalid encodings were no longer relayed.

This caused some problems for a few things.. For example bc.i's iphone
app— BC.i itself had been fixed long before but they couldn't update the
Iphone app without fear of triggering another review by Apple. Eventually
this was just worked around on the server side by mutating the transactions
produced by the iphone wallets. (And is moot now, I guess!).

MtGox also had problems with occasionally producing invalid signatures. This
would normally be a simple fix. E.g. here is an example where I fixed this
type of issue in some python wallet code I've never used (but saw a lot of
people were copying): https://github.com/jgarzik/python-bitcoinlib/commit/4c64603ab60b0fa23c51090b3112be2f163aeeac

But as I said before, in high value systems like Mtgox, even simple fixes
aren't simple and it took them quite some time to deploy a fix.
However, I believe that it is actually fixed now.

My current understanding and inference is that the remaining issues are because _while_
MtGox was producing transactions of the bad form that the network won't
relay anymore— some people decided to help out by 'fixing' these
transactions like BC.i did for iphone users— making the signatures normal
and broadcasting them. Of course, the new transactions— while functionally
identical— have different TXIDs. 

The difference here is that the MtGox wallet software appears to have
not handled this case gracefully at all, and apparently simply wouldn't
notice transactions that it "didn't make" spending its own coins.

As a result the Mtgox wallet believed some coins were available for
spending which really had already been spent and it began double spending
those inputs. This may have interacted particularly poorly with the 
earlier workaround I mentioned— trying to always use the oldest available
coins— if they did implement that workaround.

Worse, some of this may have resulted in users getting paid multiple times
and could have been intentionally triggered with that end in mind if
someone helpfully fixed some transactions and then noticed they got paid
twice. (I think this is unlikely to have caused large losses, before people
run off worrying about that, both because of the reuse of the oldest inputs
and because of the hot wallet/cold wallet split).  This would only be possible because the software was willing to pay users again using totally separate coins, a practice that nearly guarantees eventual loss regardless of transaction changes.

At this point they likely have an accounting mess to clean up— figuring
out who did and didn't get paid now with none of the txids matching.
Cleaning that up will be somewhat tricky E.g. say there were three payments
of MtGox coins to 1Apple in the block chain... and three users that
attempted to pay 1Apple, and MtGox's records thinks that only one went
through.. etc. So software will have to be written that matches up
transactions with their mutants in order to figure out what went where.

I am not personally concerned— at least not by any of the details here.
MtGox's slow speed at resolving these sorts of issues and poor
communications are not terribly inspiring. They seem to be horribly 
short staffed— but competent and trustworthy people in this space may
be hard to find: The regulatory morass of that business is sure to
make many steer clear.

The claims that the delays indicate insolvency strike me as just
hysteria: the technical background doesn't support this conclusion, and
there may be a bit of opportunism at play from people who want to
manipulate the market too. Don't get me wrong: I have not seen their books: 
Gox may well have financial problems— though with their income its hard for
me to see how— but if any problems like that exist they're not being indicated here.

Of course, none of this suggests anyone should be _happy_ with the
service MtGox has been providing, but our anger should at least be well
informed.

I received a leaked copy of your early drafts, marked full of "Confidential" and "Do not distribute". The party that provided it to me was concerned with an existential risk to Bitcoin created by this work.

The document's abstract stated the following: 

> We present
the ChainAnchor architecture that adds an identity and
privacy-preserving layer above the blockchain, either the private
blockchain or the public Blockchain in Bitcoin

In total the document mentioned Bitcoin by name 85 times.

Later in this description, 
> Here ChainAnchor is deployed as an overlay above the
current public and permissionless Blockchain. The goal of the
overlay approach is not to create a separate chain, but rather
use the current permissionless Blockchain (in Bitcoin) to carry
permissioned-transactions relating to Users in ChainAnchor in
such a way that non-ChainAnchor nodes are oblivious to the
transactions belonging to a permissioned-group. We use the
example of the current Bitcoin blockchain as the underlying
blockchain due to the fact that today it is the only operational
blockchain that has achieved scale.

and

> In ChainAnchor a successful miner receives a further additional reward for completing a block consisting only of
permissioned-transactions

On the basis of the description in the text I agreed with my source that your teams proposal was an existential risk to the Bitcoin system and I immediately began work on technology which could be used to block its implementation. The fact that I believe I've found a set of tools which would stop it is the primary reason that I didn't sound an alarm over this as well.

Since the initial criticisms of your work were published these references to Bitcoin have been scrubbed from the document and one of the authors has been removed.

With the above in mind, perhaps you can tell me why I shouldn't consider MIT's aggressive denials that this technology was also intended for Bitcoin as an insult to all of our intelligence?

Wouldn't it be better to simply say that you received some feedback from Bitcoin experts that let you realize the technology would not be useful for Bitcoin?

TLDR:  Bitcoin POW has a design flaw that allows parties to gain a large advantage (up to 30% power usage, which might be a many fold increase in profits because difficulty adjustment drives mining to a profit equilibrium).

The weakness has been known for some time now, but people believed it was not in use, we now know for sure that it has been implemented.  There is a secretive (not easily detectable) way of exploiting the weakness which, I recent realized, is largely incompatible with many possible protocol upgrades (such as segwit, though it is compatible with extension blocks). This proposed BIP inhibits the secret method of using the attack so protocol upgrades are not an additional disadvantage via a flag-height soft-fork. 

Well, a hasty snapshot of a few of my views, others may differ ([though opposition to that "agreement" looks like it may be unamious](https://en.bitcoin.it/wiki/Segwit_support)):

1. It was unethically conducted:  A VC created a private and closed meeting with his investments in the Bitcoin space and a few others without public input; and they made an agreement to forcefully change the rules of Bitcoin.  Hello Etherum Foundation (Ironic that the same person was the big driver on ethereum classic).  They claim that "core devs" were "invited" but this is misleading enough to be an outright lie: We were asked to add our names to an already completed statement with a dozen other parties names already on it (so, so much for getting it to say something more sensible). It has been promoted with misleading claims of collaboration and support by Bitcoin Core folks, which are just untrue.

2. It's unspecified. The actual signers of the agreement [clearly disagree about](https://twitter.com/bergealex4/status/867241659897171968) what they actually agreed to.  While "segwit+2mb" may sound like it means something to you-- it doesn't mean much. Under that banner you could do something fairly reasonable or something terrible [Fairly reasonable, for example would be to activate segwit, then with a reasonable timeframe hardfork to account for scriptSig data the same way that witness data is accounted for]. The details matter. Critically.   Perhaps this was partially as a result of forming their agreement without a single participant with _ANY_ experience in Bitcoin consensus rule design-- they didn't even know what you need to know about a protocol change.

3. Its time-frame is extremely absurd. Actually, absurd doesn't do it justice which is why between that and number #1 several engineers have been just responding "LOL" to the proposal.  They don't set any time for design review and analysis, they don't set any time for writing a specification (they don't have one and don't appear to intend to have one), they set aside two weeks for testing-- which is less time than even minor releases of Bitcoin get (and need!) even when they were comprised of small fixes which had mostly existed for months already. They don't allot any time for alternative implementations to implement it (which is especially bad because they can provide useful design feedback). They don't set aside time for meaningful deployment by users (some of whom may have their own lengthy patching and qualification process). They don't seem to have any concern about how forced upgrades erode decentralization and privileged hosted wallets/apis/pools over running your own infrastructure. Basically every stage of the consensus rule change pipeline should take (and always has taken) more time they allotted for _everything_.  BU+Classic BIP109 ran on testnet for months before their interoperability failure was revealed and they forked apart from each other and abandon BIP109.

4. They are explicitly rejecting and shielding themselves from public comment. E.g. they created a lf mailing list  but when Luke-jr tried to join it Jeff replied saying the list was only for people who supported the agreement and intended to support it.  Responses to requests to make their system compatible with BIP141 or BIP148 and existing nodes; have just been met with vague hand-waving about the "charter" and deleting comments on their github.  This is doubly bad because some of their technical proposals just won't work and will self partition if deployed.

5. It doesn't answer any of the serious concerns about the negative impact of increased capacity on the viability of the system, it just takes the capacity doubling of segwit (which is already seen by many as pretty extreme) and doubles it again.  Some of the signers seemed to believe that what they agreed to was "segwit immediately" which at least would give some walk-before-running time but to the extent that I can figure out what they're doing, it isn't that. Keep in mind that segwit itself was a massive compromise-- taking on a large and risky increase in load, but countering it with scalability improvements... and as soon as it was proposed the goalpost moved.  I doubt many see segwit2x as anything else.

6. Bitcoin's value comes significantly from its durability against change.  Bitcoin is fine the way it is-- it could be even better, but it doesn't need to make radical incompatible hasty changes.  Everyone who wants cabal-controlled fork-a-week coins have several options to choose from. Bitcoin is never going to be better at being that than the cruddy altcoins which were designed for it (with huge premines to keep those early holders pumping through the waves of technical failure). In a competitive market we should look at what differentiates Bitcoin from the competition and exploit that as much as we can, not try to me-too follow around other things. If Barry and Co can rewrite Bitcoin's rules against non-trivial opposition who else can also do so?

7. Bitcoin Core doesn't have the technical or moral authority to make incompatible changes to the rules of the network.  And the dozens of major individual contributors just do not personally support this proposal (for some of the reasons above or others)-- and are obviously not going to volunteer their efforts to aid a proposal which they believe would harm Bitcoin.  Probably this one would be the TLDR: Core isn't a company, it is an open public collaboration of many people.  Core won't endorse pretty much _anything_ but it certainly won't endorse things which are widely (or in this case nearly unanimously) opposed by its contributors.

8. None of the involved parties have showed even the slightest indication that they understood or cared about concerns like the above, even when people like Matt wasted hours trying to communicate them to them.  Not a "I understand but we'll account for that by X"-- but just talking to a wall.  Which means that its unlikely that the negative consequences of these concerns will be avoided with any reliability.

But finally-- what would be the point?  I think that almost any one of these issues alone would make it dead on arrival.  I'd say stick a fork in it, but Bitmain already has-- e.g. their post today says they have no intention on following through with segwit2x by saying they'd only activate segwit if it had more modifications (surprise, surprise). 

The tech community was polite enough to keep most of our concerns in private and wait and see it this transformed into something supportable.  But we've started speaking against it since the misleading claims that we supported and were collaborating with it started.

Cheers,

This is absurd.  Bitmain bragged that their "schnorr" was almost finished, but what they did was take the implementation out of Blockstream's elements project and strip off the author's name and insert their own.  The open source license we use doesn't require much, but it requires that you preserve attribution.

This Bitcoin ABC developer has done this previously and [been called out on it](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/237#issuecomment-271694586) in Bitcoin Unlimited; this time its even more extreme.

More ironically, that prior construction was dropped by us because we found it was vulnerable (and less efficient than a better one we have). Even more ironically, the interesting part of schnorr for Bitcoin that most people are referring to when they talk about "schnorr" is aggregation; which they haven't touched (presumably because we hadn't previously published code implementing it which they could just rip off and stick their name on).

Edit: The BU reality distortion field is in [full effect](http://archive.is/QaFEA) and they're responding with mocking and denial instead of making it right, similar to the last time they got caught doing something like this.
And why this should matter at all to us?   Most of the altcoin industry is in the business of drawing a false equivalence between Bitcoin and their centralized, pre-mined, inflationary, recklessly constructed platforms but they're supposed to be "better" because of some jargon that goes over people's heads.  "Just like Bitcoin but it has the smart-internet-of-world-contract-computer-hypercube-things that VCs crave!  Buy now or miss out!"

While we can't protect people from making even the most extremely bad decisions (*bitcoooonnnnnnnneeeeccccccctttttt*) if we don't continually emphasize the distinctions we'll share credibility loss with the various alts as they suffer problems from the myriad ways that they're not even remotely equivalent to Bitcoin.
> If "Core" signed this supposed agreement

It didn't under any shade or color.  This is such an absurd and abusive lie... it's infuriating to see this repeated.

Several people who sometimes contribute to core went to go have some mutual communication with miners (after being urged to not go by others...) and agreed that they would personally work on some less crazy blocksize hardfork proposals after segwit if the miners didn't signal the crazy classic hardfork.  This was a personal commitment they made, for activity _after_ segwit, and they had neither the right or authority to commit anyone else to anything, and they didn't. Once the whole thing was made public they were criticized for being politically naive, and that the whole thing would be misrepresented... "I told you so." 

In the week after F2Pool outright broke the agreement by signaling Classic, but Luke and some others trucked on and [implemented](https://github.com/luke-jr/bitcoin/compare/bc94b87%E2%80%A6luke-jr:hardfork2016) what they said they [would](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-August/012936.html).  But it went over like a lead balloon, Luke even went on to try to make several other alternative proposals-- but continued to run into the same problem:  The only people really urgently clamoring for this stuff are people who want unrealistic 'effectively no limits' proposals, either out of ignorance, or because they active want to split the system.

Meanwhile, Bitcoin Classic's BIP109 proposal caught fire on testnet-- months after they wanted it activated on Bitcoin-- and failed all on its own with a bit of help from Bitcoin Unlimited's flawed 109 implementation triggering the split.  But do we hear a "Gee, thanks for the bullet dodged there"? nope.




> focus needs to be aimed at an ASIC resistant hashing function

No such thing is possible, any anyone telling you otherwise is confused or trying to con you into a technique they know _they_ can produce chips for.
+1 I support this. BIP-148 is now more or less unconditionally net protective against disruption.
I don't agree.  As far as I am aware Mike has almost no proposals [*] in the Bitcoin ecosystem to improve scalablity as the term is conventionally and usefully defined.  By contrast, I am also one of the most active contributors to scalablity technology for Bitcoin at least on the invention side.

[*] The one exception that I'm aware of is the SPV bloom filters which were implement by Matt Corallo, but in that case they were rushed, buggy, and came with substantial DOS attack vulnerability and privacy loss. Unfortunately we we pressured into deploying them right away, which is too bad because not much latter some much better designs were invented.

A scalable system is one where increased load does not create costs that grow without bound or require decreasing the functionality of the system ([examples](https://en.wikipedia.org/wiki/Scalability#Examples)).  Mike in this case is an advocate of removing resource management functionality, this does not improve the scalablity of the system, but instead allows it to make different trade-offs, including ones which feature substantially increased trust on centralized third parties.

Examples of actual scalability tools would be things like [committed UTXO sets](https://bitcointalk.org/index.php?topic=21995.0) (Allows completely storage-less full nodes). [Fraud proofs](https://en.bitcoin.it/wiki/User:Gmaxwell/features#Proofs) (makes the SPV scheme as described in the bitcoin whitepaper practical), [Headers first synchronization](https://en.bitcoin.it/wiki/User:Gmaxwell/Reverse_header-fetching_sync) (allows higher security nodes to start as lower security, prevents a trivial dos attack that stops all new nodes from starting if blocks larger than a tiny size are allowed), [Coinwitness](https://bitcointalk.org/index.php?topic=277389.0) potentially changes system scaling at full security from quadratic to sublinear, but requires crypto that is too cutting edge and slow yet,  [network block coding](https://en.bitcoin.it/wiki/User:Gmaxwell/block_network_coding) which would decouple block propagation time and size, and make having more connections sending a block in parallel not waste more bandwidth (e.g. relaying being O(1) rather than O(n) per block). Also work by other people, like Pieter making ECDSA 6-8x faster are scalablity improvements, or making transaction verification something like 40x faster via ultra-prune, are doing scalability work. Things like Matt's relay network (prevents sending every mined transaction twice). Things like the Poon's lightning network are scalablity work. (And I must apologize to the literally hundreds of other things done by others that I've failed to include here, including the academic work in this space-- which is itself a very new area).  

And this is why it's especially concerning when the people-- potentially _all_ the people-- who have been consistently and actively working on scalablity respond to this saying that making a sudden increase in blocksize is not something the system can obviously handle and should be considered very carefully is something you should find quite concerning. Why hasn't all this work resulted in huge blocks? Partly because some of it isn't ready to go yet, but largely because the work so far has been needed to just keep up with the prior limits. But in some sense it has, it's created enough headroom that you can reasonably run a node locally with multi-megabyte blocks and have it not immediately fall over. 

It's easy to ignore all this because it rapidly becomes technical mintua at the end of the day it just matters that Bitcoin works for people. It's especially easy to ignore if you've built up faith that Bitcoin is "anti-fragile" and by the good graces of the invisible hand of the free market it can never fail... especially if you've not given much thought to [where antifragility comes from](http://epistle.us/inspiration/godwillsaveme.html).

Pulling in a car analogy, you have a pit crew that just added hardened pistons, closed loop anti-knock sensing fuel-air mixture control, nitrous, and recently invented and is planning on building the turbo-charger, all while also contributing to maintaining track and painting the car (which happen to be some of their most visible activities; because they're easy to explain).

... and while they're busily debating compression ratios and high octane fuel and the seeming impossibility of getting the car to safely go much faster with the current state of technology you have a guy standing on the sidelines with a beer cup hat, saying "No problem guys: lets remove the breaks!"  and the crowd goes wild: Finally someone who cares about speed.

"Suspected thief" is a great kindness.

Funds vanish from the bitcoinica mtgox account.. but get [intercepted](https://bitcointalk.org/index.php?topic=95738.0) in another exchange (who noticed it was a rather huge amount of money) in account registered by Zhou Tong under an old email address he seemingly thought was unlinked that wasn't-- not too unlike DPR.   

He then claims that it wasn't him but [instead it was a mysterious multimillionaire relic collector mafioso hacker](https://bitcointalk.org/index.php?topic=95795.0) that he happens to know. Oh yea and he tried to ~~disrupt the investigation~~compensate people for their trouble by randomly paying everyone who came and posted in the BCT thread (IIRC the coins were sourced from a prior bitcoinica theft event).   Zhou Tong brokers a 'deal' with the mysterious relic dealer, that the funds would be returned if no investigation continued.

Yadda yadda.  It wouldn't even make good fiction, too implausible. Besides, even though the relics collector imaginary person seems to have been written for Brad Pitt to play (with Ed Norton as Zhou Tong?), I hear Brad is already signed up to play "realsolid" in a different cryptocurrency crime adventure movie. :)
We could have segwit active by christmas, though it's a bit unlikely miners will upgrade that fast.
Many moons ago I was an on call engineer for a small local ISP.  I got paged late one night and had to head into one of the facilities.

I had keys to the office and the elevator but at some point recently they had installed a keyed gate system to gain access to the garage. My elevator key only worked the garage elevator and I didn't have the keycard for the gates.

My solution: I'd just moved and had some junk in the back of my van.  I tied a large frying pan to an extension cord and fished it under the exit gate and across the sensor.  The gate opened on my first try.

I've since used that technique many times— with some mixed success. FWIW, none of my tire irons have ever worked they also don't slide anywhere near as nicely. I think the frying pan was best tool for this that I've ever tried.

Instead of "showing him"-- why not talk to him?

I don't understand his recent comments at all, but he is competent, technically sophisticated, and has been a Bitcoin user for a long time-- longer than most people here. He speaks fluent English.

A successful UASF isn't about showing anyone anything, if it's successful it's about all of us collectively acting in our own shared interests-- and getting there starts with communication.
No. Nor do I have a facebook account. 

I think twitter is pretty much the most innane thing ever. I wouldn't be surprised if not a single one of my comments on this threat fit within the twitter message length limitations.

I am a bearded UNIX curmudgeon. Get off my lawn.
This article massively oversimplifies things...

Operating in the linear colorspace is "physically correct", i.e. it modifies things like normal linear optical effects do in the real world. 

But the perception of luminance is not at a linear thing,  and the gamma adjusted representation is usually a more perceptually correct space to manipulate image data in.   But the correctness of the gamma space depends on *scale*.  [Look at the apparent 'hump' shape here.](http://en.wikipedia.org/wiki/Contrast_%28vision%29#Contrast_sensitivity) So for large smooth areas the gamma corrected space is correct,  but it understates the energy of fine edge details, so they are attenuated when you resample in that space.

I suspect that ideal rescaler would perform a projection into a higher dimensional contrast-scale space, then shift the data and project back down to the new resampled size.    No one has ever bothered to create such a (lubriciously computationally expensive) resampler.

TLDR: Both methods are wrong in some sense, the author of this page has cooked up some contrived examples which show how resampling in the non-linear space loses edge contrast.  But it's not so simple as ZOMG ALL THIS SOFTWARE IS WRONG.  ... and at least one way of being wrong has the benefit of being fast.


Was any portion of this work federally funded?
Coinbase's handling of this is completely reasonable.

Anyone can create an altcoin fork of Bitcoin at any time, and they can do it as many times as they want.  Coinbase may not even know about it.  

ABC isn't the first fork to award coins to existing Bitcoin users, there have been several others like Clams and [bitcore](https://bitcointalk.org/index.php?topic=1883902.0) (how deceptive these jerks are...).

In spite of the seriously short notice coinbase had done an admirable job taking a clear and consistent position and communicating it to the public.  It's a position that can be applied to any further such events. In ABC's case they took serious bludgenoning just to get the most basic replay protection into place-- and they still have many unnecessary conflicts with Bitcoin: duplicate address types, duplicate p2p port, duplicate p2p magic, duplicate data dir... all making it unnecessarily complicated and risky for people who want to use their altcoin.

Consider if they went another way-- what about the next one? and the one after it? People could effectively flood them with them, a fork a week--  forks that contain malware backdoors that steal your Bitcoins, forks that will never have more than a few dozen users.  etc.

No one can reasonable keep up with that.  If you want to chase some of these silly things, that is your decision-- one which you have the freedom to make by keeping your Bitcoin's in your custody.  Expecting other people to deal with those potentially unbounded costs on your behalf wouldn't be reasonable.
> FYI, we have contacted Core developers about a bug whose effects you can see as approximate 5% drop in Core node counts on Feb 23, 2017 and Mar 6, 2017.

That report was spurious: The vulnerability you reported existed in BU but no released version of Bitcoin Core, but thank you for reporting it.

I was shocked, especially considering your prior reports via public announcement that you were "[unable to weaponize](https://medium.com/@g.andrew.stone/a-short-tour-of-bitcoin-core-4558744bf18b)". Next time you have a suspected vulnerability in Bitcoin Core, it would be helpful if told us immediately instead of [discussing it in public](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/316) for 13 days first.

There are vulnerabilities in unlimited which have been privately reported to you in Unlimited by Bitcoin Core folks which you have not acted on, sadly. More severe than this one, in fact. :(

In this case, as far as I know Peter Todd is just repeating a report that was already widely circulated and was, in fact, [disclosed by your organization](https://github.com/BitcoinUnlimited/BitcoinUnlimited/commit/eee6a2daeb560f26061535695fc0f7de168ffe32). Am I mistaken?

Unfortunately there is virtually no real economic activity in ethereum, just speculation... as a result, catastrophes like this look far less bad than they actually are, and the typical parties will use this to argue for more reckless behavior in Bitcoin in the future.
It should be noted that the 'developers' involved in this discussion do not include a single person with commit access to the repository for Bitcoin core, nor any frequent contributors to it.
> I can understand how this would disrupt no2x nodes. But how does this help s2x nodes?

There are virtually no 2xc nodes.  It appears that they hope by disrupting users running other software they will be forced to adopt 2xc.

This will not stand.

/u/jgarzik  I am publicly accusing you of intentionally disrupting other people's systems.  Feel free to correct if you don't believe that my presentation of your actions and motives is correct.
> For example, I distinctly remember you and some other devs saying something along the lines of "I don't care if segwit activates", which to me as an outsider doesn't sound like a glowing endorsement, but more like "we've run out of reasons to reject it"

::sigh::

I am one of the inventors of segwit.  I named it (such as it is). I wrote the public proposal that we deploy it. I spent countless hours working on it, reviewing it, and testing it. I think segwit is great stuff.

But I still do not care if it activates.

I think you do not have a clear perspective on the stakes and time-frames involved.  Bitcoin's security and robustness is paramount and is strictly more important than any mere technical improvement, no matter how nice of one it is.

Part of the challenge and beauty of working on Bitcoin is that no one controls it. No matter how pretty or perfect you believe some improvement is, it may not be adopted.  No process could guarantee the adoption of a change without consigning Bitcoin to worthlessness (especially amid an environment with many competitors that deploy random crap with nary a though to the resulting erosion of personal autonomy).

So segwit is great, but it doesn't hold a candle to the importance of Bitcoin itself.  Because segwit is good I'm pretty confidence that we'll have it eventually (or be replaced with something even better), but it ultimately doesn't matter -- not compared to keeping Bitcoin secure and resistant to influence.

You insult me and every other Bitcoin developer by assuming that we're opposed to segwit because we strictly prioritize Bitcoin's value and longevity.

> Edit - P.S.: Don't get me wrong, for janitorial tasks 

So, "pardon me while I spit in your face"?--  Bitcoin Core is not a bunch of janitors. The current community been doing critical work advancing the protocol and driving the state of the art in science in this space since 2011, likely long before you ever even heard of Bitcoin.

> it's easy to get the impression that Core retreats into a comfy "I'm just an engineer" position where they confine themselves to listing reasons why idea xyz doesn't work

It's only easy to get this impression when you don't have the interest to bother actually reading or getting involved in the discussions.  No one requires you to-- I don't fault you for not, but you shouldn't suggest that discussion isn't there when it's out in the public available for your own participation, but 
instead you choose to pay attention to social media.

Instead what you're describing is the pigeonhole that people try to place us in-- which your own message itself does with "janitorial tasks".  It is a load of bullshit.  But perhaps this is also why you can't understand why someone would build something but be okay with it not being used:  We're not here to sweep the floors-- if we were then perhaps we might be more obsessed with using up all our floorwax. We're here because we believe in Bitcoin and it's potential to enable and transform mankind.  All of the difficult engineering we work we do is in service of those ends and is meaningless otherwise.

Perhaps it confuses you that we seek to change the world through engineering, because most of the attention goes to those who try to change it with rhetoric. But the overwhelming majority of rhetoric in the world today and through history is just talk. Cypherpunks write code.

Many of the biggest changes in the world have been engineering, but they usually go without widespread notice especially once they have achieved their impact.  As you read this message an overwhelming majority of the nitrogen in your body was fixed via the [haber process](https://en.wikipedia.org/wiki/Haber_process) and you and probably 80% of the people on earth owe even the possibility of your existence to it.   But unless you've studied chemical engineering, you may never have heard of it.

So do not confused by the fact that we are busy working to change the world rather than busy talking about changing the world. Someday the world will be powered by Bitcoin and people like you will forget that it took hard work and brilliance to build, maintain, and grow it into a thing that everyone can count on. That will be the greatest success.  I could spend more time lamenting the flaws in our culture that cause us to glorify ultimately pointless pontificators while ignoring so many that cause real change, but that would get in the way of changing the world.  I should leave that to people who have nothing more productive to get done...

>  they are thrown over the fence so to speak,

The development of the whole thing is conducted in public collaboration in the open.  Nothing requires you to participate, but you could if you wanted to make the investment there.

Welcome to Reddit, 69520d0f929aeac8!

There are many theoretical concern related to OpenSSL's operation as well as Linux's /dev/random (and other operating systems) behavior in general-- and there is a long term project underway to replace it: https://github.com/bitcoin/bitcoin/pull/5885 (and you can see from the comments there, we're _well_ aware of how it works, more so than even the concerns you've expressed here).

That said, these are theoretical weaknesses.  In Linux /dev/random and /dev/urandom are the _same_ rng, but /dev/random uses an entropy estimator. The estimator provides basically no value and is often misunderstood. This page has an excellent and correct debunking:  http://www.2uo.de/myths-about-urandom/

The "aren't very random" you're quoting is taken out of context-- the context is "The main thing which I am much more worried about is that on various embedded systems, which do not have a fine-grained clock, and which is reading from flash which has a much more deterministic timing for their operations, is that when userspace tries to generate long-term public keys immediately after the machine is taken out of the box and plugged in, that there isn't a sufficient amount of entropy, "--- e.g. context that matter a lot for generating SSH keys, but not so much for Bitcoin.

Beyond the basic operation there, OpenSSL and Bitcoin Core further harden the input with additional timing information.

Finally, the bit of concern you're sowing there doesn't make a relative comparison.  It would be no good to chase someone from using Bitcoin Core due to niche and speculative issues to something that was _practically_ less secure.
> 10,000 recharge cycles

They've missed what usually makes capacitors interesting...
Yup.  Payment channels were an intentional design feature of Bitcoin from the start.
> If 50% of hashing power is obtained, the controller of that power can double spend, even after multiple confirmations

This is true even with less than 50% with high probability.

For example, 45% can reverse 6 confirmations with a success rate of 77%.

(try some numbers for yourself: http://people.xiph.org/~greg/attack_success.html)
It's really hard / almost impossible to do statistically significant _representative_ listening tests at very high rates.  The best you can do is exclusively use really critical listeners (not necessarily representative of normal listeners), and really difficult samples (absolutely not representative of normal content)... and then hope to eek out a difference.   This isn't to say that there aren't difference, but the measurement noise from using human listeners at rates that are by definition hard to distinguish makes them hard to measure.   ... Worse, this difficulty doesn't just exist where the signal is indistinguishable from the original, listening test difficulty gets exponentially worse as you approach transparency, so it's even hard to use tests to compare the rates at which different codecs become transparent.  

We can, of course, make objective measures using simplified psycho-acoustic models. For example, [this  NMR measurement](http://people.xiph.org/~greg/celt/NMR.v.c.l.png), measures the level of the signal error compared to an estimate of the psychoustic masking effect and the sensitivity response of the ear. Lower is better, as it means the noise is quieter relative to the quietest sound that should be distinguishable above the masking.  So you can see objectively Opus does quite well, and thats with an older encoder, the latest encoders do much better.  But doing well objectively doesn't certainly mean that it will sound better in practice.

Fortunately, for VBR usage Opus has a lot of headroom, so if people do discover a case the format does poorly encoders can be taught to detect those and crank the bitrate on them. So long as they're rare it shouldn't impact the average bitrate, and if there were issues like that that weren't rare then hopefully we'd know about them already. So the fact that Opus does well on objective measures and in listening test at more easily tested rates is persuasive that at least a sufficiently mature encoder will soundly outperform MP3 even at the very high rates that are hard to test.

Ping ping, /u/Craig_S_Wright
Exactly:  it's not a fking "accident", "chance", or "fate" that your parents survived.  People worked hard to make the safety contraptions and rescue infrastructure that saved your parents lives, other people worked hard to create the political/economic/regulatory climate that made these things widely available, and other people worked hard to design roads that make accidents less common.

I was just pointing out to someone a few weeks ago how the initial segments of barriers on the sides of the highways have articulation that causes them to curl and dissipate energy instead of harpooning the car if hit head on.

Someone spent countless hours, perhaps years, designing and refining that mechanism, the people installing the rails have to take care to install them correctly so that they work as designed, the regulators have to make sure to specify them so they end up in the plans and budgets, etc.   This subtle bit of engineering has probably saved many thousands of lives, and yet most of the people its saved, much less anyone else, probably have no idea.


Redeployment of segwit basically can't happen until the old deployment expires.

The reason for this is because for simplicity and safety reasons all segwit supporting versions of Bitcoin only have segwit codepaths for the omst part... e.g. there isn't separate handling for segwit and non-segwit txn.  Segwit's current non-activation is achieved by a very minimal set of tweaks on top of that.  The benefit of this is that it makes segwit much simpler to implement and the change in behavior is minimized when segwit activates-- there isn't a huge pile of previously dead code that suddenly turns on (and maybe turns out to be buggy).

So in 0.13.1+ almost all of segwit is already active, including all the P2P components...  the new network service flag, the witness-tx and block messages, compact blocks v2, etc.   A redeployment of segwit will need to redefine all these things.

If the old deployment of segwit isn't over yet then _both_ have to be supported. And then you get code path duplication, a hard cut in behavior, and a problem of combinitoral testing.

Avoiding these issues is why BIP149 doesn't start until as late as it does. 

But if the original segwit bit has already expired, the change turns into a simple search and replace: no complex combinatorial testing, no duplication, no dead code that suddenly turns on.

The primary argument (and IMO pretty much the only strong argument) for BIP148 is that it doesn't have to wait for the original timeout to finish.

It's not like these challenges were secret either, they were already discussed in the context of the BIP148/149 tradeoffs. :-/

So it sounds like that proposal was made entirely without the aid of anyone who has even been following the discussions in detail. 

They originally promised their customers that they wouldn't mine with more than 5% of what they sold.  So much for that!

Some people (e.g. me) called them out on this early but got yelled at by the KNC fandom. What a 'great' business model: have people pay for you to build infrastructure to go compete with them. Lets just hope the systemic risks they've created doesn't ruin it for everyone.

I haven't seen any evidence that they're testing anything— just the regular dust sweeping transactions that they've had going on continually.

Initially when MTGox suspended withdraws I was fairly confident that all would be fixed soon (and said so as much on Reddit)— their technical issues were simple, and with proper controls in place could not have resulted in especially large losses. I especially expected losses to be small considering that they were reported by users and not discovered by MTGox themselves.

Considering MTGox's subsequent behavior— the extended outage, talk of withdraw limits, etc, I'm no longer confident of anything.  :(

>  So for 400% of bandwidth you only get 170% increase in network throughput. 

This is simply an outright untruth.

If you are using 400% bandwidth, you are getting 400% capacity. 170% bandwith, 170% capacity.

Your confusion originates from the fact that segwit eliminates the blocksize limit and replaces it with a weight limit-- which better reflects the resource usage impact of each transaction.  With weight the number of bytes allowed in a block varies based on their composition. This also makes the amount of transaction inputs possible in a block more consistent.

>  MAX_BLOCK_SIZE=32000000 [...] which is almost 2,5x more efficient than SegWit.

In fact it would be vastly less efficient, in terms of cpu usage (probably thousands of times)-- because without segwit transactions take a quadratic amount of time in the number of inputs to validate.

What you are effectively proposing doing is "scaling" a bridge by taking down the load limit sign.  Just twiddling the parameters without improving scalability is a "terrible ugly hack".

Never attribute to malice that which can just be explained by screwed up software. :)

They are already being punished for this by missing out on a significant amount of fees.
By tomorrow you mean roughly the 18th. Segwit signaling will start after the first retarget after the 15th, which is roughly the 18th.
BLOQ's post states "I've been paying close attention to the Bitcoin Core 0.15.x rollout. Based on instability and bugs that upstream Bitcoin Core project is seeing" -- in other words, existing reports...

Problem is they just aren't there. After being rather surprised by that a number of us combed through PRs and issue trackers and looked to see if there was anything interesting, and found nothing remotely interesting that isn't also in 0.14.x.

0.15.0.1 has been unusually reliable, in fact.  While I wouldn't be surprised if there were some issue, so far we don't appear to know of what he could be talking about, and we do know about serious issues in 0.14.x.

Very odd that they would stay on a known vulnerable version. A more conspiracy minded person might note that it's a perfect setup to attack their own nodes then blame their adoption failure on the bad guy developers of Bitcoin...
Bitcoin's security works precisely because hash power is NOT law. Hash power is incentivized to behave honestly by the rules of the system-- set in stone by the users-- the no amount of hashpower can cheat.

Parties with such a profound misunderstanding of Bitcoin as ViaBTC really should not be running a mining pool.

I would urge people to move off that 'pool', but AFAIK virtually no one uses it except its co-owner Bitmain.
The release notes are still a bit in flux.

There are some really nice performance improvements in 0.14 which aren't mentioned in the release notes yet.

Another major feature in 0.14 is support for after the fact fee bumping-- if a transaction is taking longer to confirm than you want, you can increase the fee.


> We need USAF now!

We really do not.  You would be hard pressed to find any widely used multi-vendor internet standard that has been upgraded as fast as people seem to expect in Bitcoin-- where that stakes are orders of magnitude higher.

Bitcoin is a great system, segwit will make it better-- but it is still great without segwit.  UASF, done right, are good as well... but I do not agree with your urgency.  We will get Segwit and many other virtious improvements in the fullness of time, but even if we never got Segwit Bitcoin would still be great.  If, by contrast, we frequently made hasty consensus changes driven by urgency Bitcoin would lose much of its greatness.

Urgency is a sentiment that is easily manipulated to push bad decisions. We should resist it when at all possible.

Oh god. The stupid it burns.

This isn't one miner, it's a pool.  It's common for pools to mine to a single slowly rotating address.

It turns out there is a major pool hosted out of KC— Eclipse, which is run by one of the BFL employes (since long before BFL existed).
 
It's delicious.  Wright is an open house for a dumpster fire, a roach motel for the bamboozleable, last refuge of the incompetent.

Working on Bitcoin is hard-- with some people spending hundreds of thousands of dollars per month to tear down what we've built for years though hard and often thankless work.

It's a small but real consolation to see many of those same attackers voluntarily douse themselves with figurative gasoline while they sing kumbaya and try to figure out how a matchbook works. And the best thing is that it's all organic and straight-forward: an earnest and thoroughly obvious conman meets a community desperate to believe against all reason and sanity in a Bitcoin unconstrained by physical reality, others desperate to spread FUD and pump competing systems, complementary shapes made for one another. I couldn't have constructed a better match with unlimited time and unlimited budget. They deserve one another.

Burn baby burn.

Hi Yumein, Wecome to Reddit! 

First, lets get some facts straight: Committers to Bitcoin Core who are pushing for immediate deployment of the 20MB increase:  Gavin,   Opposed/Concerned: Myself, Pieter, Jeff, Wladimir. Gavin stands more or less alone in the Bitcoin Core technical community on this.  That there are are many of people at my company who are concerned about the impact on the survivability of Bitcoin as a decenteralized system is a product of the fact that these concerns are the overwhelming majority in the technical community, and my company was created specifically to fund technical infrastructure work, and so employs a lot of technical people out of this space.

[I'm using commiters in that list as a fixed set and because the OP highlights them. If I instead e.g. take all the people with substantial activity in the last year there are a couple people whos positions are unknown, even if I assume they support Gavin's recent push the result remains; people concerned about that proposal are the overwhelming majority]

Secondly, our views on the subject are all well established for many years, -- for an example in my case  please take a moment to read https://en.bitcoin.it/w/index.php?title=Scalability&action=historysubmit&diff=14273&oldid=14112 and notice the 2011 date; the implication that my views on this subject colored by a conflict of interest-- even subconsciously!-- created by my company is conclusively disproved by this any many other posts and our work, spanning back many years; unless you also want to argue that I own a time machine. (I guess that would also explain how I was an early user/contributor to Bitcoin and rpow, and etc. Hmm. Great; I can't wait to see the headlines on that.)

Moreover, Blockstreams business plans depend on success (and survival!) of Bitcoin, and the specific technical things we're working on would be aided by increased block sizes if not for tradeoffs against decentralization (in particular, the 2wp return proofs are fairly big, and it took a second significant breakthrough to get their size viable at in a world with 1MB blocks). We've arranged it so that everyone at blockstream has a substantial Bitcoin interest (by using timelocked bitcoin as incentive/retention compensation); and in my own case I am much more financially invested in Bitcoin than my company; though I am not doing any of this for money (I've had the good fortune of a long career in tech and a pretty inexpensive life-style, and can afford to work on problems that I think are important, regardless of what they pay). Furthermore, as pointed out in the whitepaper, sidechains themselves are not themselves a scaling mechanism-- as decentralized systems like Bitcoin they suffer the same (perhaps worse!) decentralization vs scale trade-offs.   At most they help scaling by letting us safely try out new scaling technology-- things that fundamentally improve scaling, rather than just shifting the trade-off-- or by letting users with conflicting scale vs decentralization preferences get closer to what they want without forcing their preferences on each other. 

> and are already working with the lightning network, which provides an alternative for scaling

Yes, I was antagonized by someone who argued that if I thought lightning was an important tool for scaling why wasn't I funding it-- and they had a point, so I went and pushed for doing that; and we asked Rusty (who we were already talking about hiring and was already personally very interested in it) to focus on that.  We think it's important infrastructure, it's all being developed in the open on public mailing lists.  Like sidechains, lightning also wants blocks to be as big as they can sustainably be.  Even in the impossible counterfactual where blocks could be infinite sized without a total loss of decenteralization, Lightning would still remain incredibly attractive, due to its ability to offer instant confirmation and other features.

We founded Blockstream to create a way to fund significant infrastructure work on complex cryptographic protocols that weren't getting developed due to lack of time and resources. The existing support for even the most basic Bitcoin development-- even just maintenance-- has been sparse to non-existent, unstable, and comes with its own strings attached. Developers going off to work for Bitcoin companies seemed to vanish, and the big tech companies pay _very well_ to people with the skills required to contribute meaningful in this space... Most other Bitcoin companies, saddled by enormous regulatory and currency related risks have been unwilling or unable to substantially fund serious technical development. I believe that having a diversity of models is essential to building a sustainable ecosystem.

The design of sidechains grew out of a simplification of a protocol [I proposed a couple years ago](https://bitcointalk.org/index.php?topic=277389.0) for accomplishing the two-way peg. That proposal along with many others, were specifically born out of trying to build technology that allowed Bitcoin to have its cake and eat it too-- How can we have tremendous scale without centralization, without compromising privacy or autonomy? How can we have a system which is largely immune to political interference, but can adapt to people's changing needs? Can people with contradicting needs share a common Bitcoin currency but still have those needs (more) completely met?

Many of us have been working on technology that can radically drive Bitcoin forward, address its shortcomings, and complete the vision, for many years (All while working diligently to keep the existing Bitcoin system above water!)-- but the consensus nature and high risk of changes to Bitcoin mean that deploying that work in Bitcoin incredibly hard-- it must be perfect before you can even try it out!, and instead our own code would end up taken and stuffed in _competing_ systems (e.g. as happened with P2SH), which is seriously disheartening-- or worse, people go and raise millions of dollars with white papers claiming our ideas and then not even get around to actually building them. We're left with only compromised proposals that fit nicely in the existing network-- like BIP32 or CoinJoin, and even that has taken a long time to get going in a form close to its full vision. So to take things forward, we needed a way to experiment without risking the bitcoin network or creating another market fragmenting altcoin, and we needed sufficient funding to take complex ideas to full production before other people ripped them off into competing systems with lower integrity requirements-- and we went out founded a company to do that.

It already seems crazy:  Why own something very costly that loses value when you could own things that produce income instead? 

Why do so many people think it's not crazy to spend a non-trivial percentage of their net worth on a toy just because the toy is car shaped?  (such things are not cars because their value and fragility impedes their ordinary usage).
The updated firmware still has considerable timing (and power) side-channels; but better is better... I guess?   If you do update, you should redo whatever you did previously to make sure your device wasn't leaking its private keys to attackers over the network using the nonce sidechannel.

Power analysis is really hard to defend against; and I am not aware of any product that I could really vouch for the power analysis immunity, but the attacks are much easier if it's not even constant time, and constant time secp256k1 signing exists. (Though I know at least the developers of 'Ledger' are savvy to these concerns and serous about them; I've not tried to attack their products and their secure elements are opaque so I cannot audit their design).

I have no idea why they would change the code and not change to an _actually_ constant time algorithm, instead of WNAF which is fundamentally variable time. The new implementations also uses secrets in memory indexes, which itself a no-no.  Even turning the trivial double and add into a double and always add would likely have been stronger (well ignoring the other timing leaks, like in the inversions).

I'm continually frustrated at people putting their faith in cryptographic products which have no reason to expect their integrity, little to no peer review, no tests, no comments, not formal analysis, no best practices, no evidence of awareness of most of the issues.... This isn't a Trezor specific issue, it happens everywhere in this ecosystem.  Some developers are even dismissive (or outright nasty) to people who warn them of the specific problems.

That article links a comment that said "He said it may be", ... If I did, I was just trying to be polite. The timing sidechannel was so large that you could pretty much read off the hamming weight of the nonce with a stopwatch (well not quite, but ms resolution timing was enough); and this was not some subtle thing, it was apparent with a couple second long glance at the software.

At least they finally tried testing it.
In the Bitcoin Whitepaper, section 8 paragraph 2, Satoshi explained that in the event that a majority of hashpower produces rule violating blocks network nodes will be unaffected by the attack because they verify for themselves and will not be tricked no matter how much hashpower is involved.  To a network node an invalid block is pretty much the same as a non-existing block.

But, as the text went on to explain, lite wallets are more exposed to being attacked with invalid blocks because they do not verify-- they trust that the verification everyone else running a network node performs will make it uneconomical for miners to attack. He goes on to explain that lite wallets could be made more secure if honest peers would tell them where invalidity was so they could go check for themselves. (it also suggests that merchants would run network nodes regardless, which isn't how things have worked out...)

The problem was that his design didn't support lite nodes checking for themselves: for many kinds of invalidity the lite wallet would have to download the whole chain history to check. If it was willing to do that when any peer suggested it, it could be easily DOS attacked.

In the subsequent years, we've figured out hot to create very compact proofs of misbehavior for all the network's rules. But to accomplish this we need additional soft-forks that added extra hint-data to blocks.

What Luke did here is that he came up with a moderately efficient proof that a block is too big that doesn't require any additional network rules over what we have now.

This would allow lite clients to enforce the blocksize rule on their own, so long as they had even a single honest rule enforcing peer... without having to blindly trust miners to comply with this rule, or trust that none of their peers would hand them an invalid block.

Normally a proof for such a narrow part of the rules wouldn't be that interesting, but it is relevant to recent discussions.
I wrote a long response to the malleability comments in this interview, but this response has been spammed so many times my response is lost in the mists of threads. :-/

Edit: Found it,

> Listen to his solution for malaelibility.

What he says is: "It's just that past block N, if enough nodes agree, then you're not allowing non-canonical formats of transactions. You don't need over 100 lines of code for that."

This is fractally incorrect.

The now abandoned [BIP-62](https://github.com/bitcoin/bips/blob/master/bip-0062.mediawiki) introduced the concept of precluding non-canonical forms as a way to deal with malleability.

BIP-62 never purported to solve malleability generally, but only when users intentionally limited themselves to a subset of transaction types: "The first six and part of the seventh can be fixed by extra consensus rules, but the last two can't. Not being able to fix #7 means that even with these new consensus rules, it will always be possible to create outputs whose spending transactions will all be malleable."

The BIP, in its last state before being abandoned, proposes 7 new consensus rules. Several of them were quite complex on their own, and collectively they require hundreds of lines of patches to consensus code. Worse, we kept finding more and more cases that had to be handled so except for the narrowest possible class of transaction usage it was difficult to be sure that the malleability was avoided in any particular use-case.

The concern about the size of changes to consensus code is also interesting from the perspective that Bitcoin XT's 'dynamic blocksize hardfork' was about the same amount of consensus code changes as segwit, but doesn't seem to have drawn his complaints.

There is also an important mode of malleability that BIP-62's approach can do nothing for: Co-signer malleability.  This is where you and another party have jointly paid into a 2of2 escrow, if you need payments out of that escrow to be non-malleable then the BIP62 style approach fundamentally cannot provide it. Some multi-transaction protocols, like lightning, can work around this issue but others cannot.

The inclusion of the signatures in the IDs used to reference transactions for later spending is an obvious design mistake that many people have lamented.  BIP-62 attempted to work around the resulting malleability problems with a long list of hacky rules which addressed the matter incompletely and at the cost of considerable complexity (to the point where it was difficult to tell what was malleable and what wasn't). Segwit fixes the design in a clean and compatible way.

And this is the opinion of the very same people who came up with the idea of trying to cut off non-canonical forms as a fix in the first place.

On scaling, Mr. Falkvinge simply asserts that segwit is a bad scaling tool; but doesn't even suggest an alternative.  Since I'm not clueless, I assume he is referring to BIP109 (and the ilk) but these proposals do not improve the scalablity of the Bitcoin system, they simply remove a safety limit without making the system more scalable. Segwit improves the scalablity in real and meaningful ways which reduces the risk of the capacity increases it provides. Among other improvements, segwit allows new sync modes that do not fetch old signatures which will not be validated, it allows more capacity without a commensurate increase in bandwidth to SPV clients and without increasing the worst case increase of the UTXO set size, and it eliminates the quadratic-in-tx-size transaction hashing costs. These changes reduce the potential for harm from increased load on the system.

Later, Mr. Falkvinge states that segwith "also needs more users to upgrade"-- and this seems perplexing to me, since the filtering transactions by canoncial forms has required _all_ users to upgrade (though I'm not aware of him complaining about that), and segwit has no such requirement-- users can upgrade whenever they want to (or not), and gain advantage of additional capacity (and resulting lower fees) on their own terms.  He also states that hardware must upgrade which is simply untrue, he also advocates hardforks but ignores that generally a hardfork requires a coordinated flag day upgrade from everything that speaks the Bitcoin protocol.

The segment of the interview I watched didn't seem to add anything new to the discussion. Rather, it seems to repeat a number of common misunderstandings from this forum which are at odds with the simple facts of the situation. I think it's unfortunate that Mr. Falkvinge has seen it fit to speak about this issue without making a serious effort to get a complete and equal understanding of the situation. I hope his efforts in the domain of public policy are more wisely handled.

> Yes, malleability will solve itself sometime in the future.

This sounds like magical thinking. The system doesn't change itself, except via all of our own actions.

> I think this difference between a "soft" and a "hard" fork is largely an artificial one

The distinction is simple, and perhaps explained with a concrete example:  Right now ~96% of hashpower is targeting a soft-fork that "Bitcoin Classic" developers haven't yet caught up with-- BIP-68, initially proposed a year ago, and deployed in Bitcoin Core for a couple months now. Yet Bitcoin Classic continues to work and will continue to work after the activation, the classic developers (against' Core's advice) even thought it was okay to rip out all the warnings about the detected unsupported soft-fork.   Had this change been a hardfork triggered by hashpower at 75% (as classic advocates), all users of classic would be kicked off the blockchain this feature activated on, and exposed to double-spend attacks by slowly mined classic compatible blocks. Users would have to change to different software, or perhaps a hasty and untested upgrade might be pushed out to get it back online.  I think the dozens of people running that implementation-- and other customized versions or infrequently updated forks-- should beg to differ that the distinction is artificial.
FWIW,  the gox transaction API  (https://data.mtgox.com/api/0/bitcoin_tx.php) is now returning TXID: 442ef31f93f339398ba79f0aa4bc0222baf81a9ba4dd5bd5bea465332dacdabb  which is a spend using coins sent earlier to the same address as the gigantic transaction.

Unless the MTGox API has started lying I'd consider this positive confirmation that the earlier 200k BTC transaction was an MTGox transaction.

People have been asking on IRC why some of it is being broken up. I could only speculate— but thats what you need to do to prepare a hotwallet to support a high volume of transactions (so you have many coins to spend so you don't get stuck waiting for your change to confirm while avoiding spending unconfirmed change, which MTGox has always done).

Edit: Sturles pointed out to me that the splitting behavior is consistent with the splitLargeOutputs function in the leaked gox source code, suggesting that portion of the coins was dropped into the MTGox online wallet and the system is now automatically breaking it up.
The suspicion was motivated by observing that SegWit was very likely incompatible with an optimized implementation if it--- which happened by chance, basically I was saying "to block asicboost the network could do something like <xxx>" and then I realized the words I was saying were basically part of the SegWit design. 

With this in mind many otherwise hard to explain facts clicked into place-- e.g. aggressive attacks on Bitcoin Core that started after the proposal of asicboost, arguments against segwit that seemed to make no sense, advocacy for "hardfork segwit"... and this justified further investigation.

I hoped to find a test that would conclusively show which blocks were using it, but this doesn't appear to be possible.

More recently the "extension block + lightning" discussions have also stricken people as inexplicable (since many thought that segwit was being opposed because it potentially facilitated off-chain transactions)-- but they also fit.
On one hand, I’m happy to see this– on another I can’t help but think:

“If you don’t like people looking why not try putting on some pants?”

To this day, Wikipedia still does not default its ordinary readers to using HTTPS. HTTPS is the only widely deployed mechanism we have to protect reader confidentiality and HTTPS provides protection even against parties that break the law, not just governments but ISPs, employers, spammers, organized crime, and anyone else who might violate the readers privacy. No amount of asking nicely (or insistently via the courts) can protect readers in the manner that this mechanism has always been able.

Moreover, in 2006 I provided the Wikimedia Board and GC with clear evidence of widespread government surveillance– including configuration from monitoring equipment and network diagrams. I received no indication that anyone believed this evidence to be non-credible but no action was taken to mitigate. [And I am no stranger to the organization, as a [long time](http://en.wikipedia.org/w/index.php?title=Wikipedia_talk:Avoid_weasel_words&diff=prev&oldid=8762619) editor and technical contributor in good standing I had privileged access to Wikimedia’s servers and infrastructure all throughout this period]

In 2008, the widespread interception of traffic to Wikimedia in the UK resulted in multiple service outages. In this instance Wikimedia made specific technical affordances to accommodate the surveillance infrastructure by white-listing the interception devices so that editors wouldn’t be blocked. This event was [widely known to the full staff and community](http://markmail.org/message/gvpepr6ginrvkhjh). Specific calls to enable HTTPS to protect users from this action and/or to take action against the networks that facilitated it went unsatisfied.

[Through these years](http://markmail.org/message/qlxfl25mzs67xxyz) I argued strenuously for the deployment of HTTPS by default (and worked to make it possible, e.g. [demonstrating the viability of protocol relative URLs](http://wikitech-l.wikimedia.narkive.com/d4IKR47T/protocol-relative-urls)), as well as additional measures like offering [Tor exit enclave support](https://lists.wikimedia.org/pipermail/wikien-l/2007-September/080667.html) and/or a Tor hidden services (which also help address the issue of reader privacy being violated through the use of administrative subpoena and national security letter which Wikimedia may be powerless to resist or disclose their existence), along with proposing the adoption of system architectures which would make HTTPS deployment less costly in the future. In these discussions spanning years senior technical staff for Wikimedia countered that readers had no expectation of privacy, that readers had no need for privacy, or that the rare user who needed privacy could simply manually avail themselves of HTTPS.

Even now, a year and a half after Snowden’s revelations made the whole world aware of what some at Wikimedia knew in 2006, [readers of Wikipedia still do not enjoy this most basic protection](https://meta.wikimedia.org/w/index.php?title=HTTPS&oldid=11506187#History_of_HTTPS_at_WMF). In 2006 this shortcoming was excusable on a budgetary basis: we had serious concerns that the site was not sustainable, but today Wikimedia is the best funded organization in the Open content / Free software world by orders of magnitude, and receives more funding than it can efficiently spend by all accounts.

In the time since, Wikimedia has gone through three executive directors, three general councils, replaced its whole board of directors (except Jimmy) roughly twice, moved from Florida to California, gone from five paid staff to several hundred, and increased its budget by a factor of 38 to roughly $50 million/yr now. But it still fails to provide basic cryptographic privacy for its readers.

At this point it seems to me to be undeniable that /functionally/ Wikimedia as an institution cares more about the pretext of reader privacy and freedom of thought than the actuality of it, regardless of the personal views of many of Wikimedia’s staff and contributors (which I hold in high esteem, and I know do care).

I hope that another year from now I won’t, [_again_](http://meta.wikimedia.org/wiki/Talk:PRISM#What_wasn.27t_said), have reason to write a message like this on the Wikimedia Blog ([this is a cross-post](https://blog.wikimedia.org/2015/03/10/wikimedia-v-nsa/#comment-23981)); but I fear that the level of dysfunction demonstrated by this failure cannot be easily cured.

Edit: Added some links.
You missed a great opportunity for alliteration: Bugged block brings blanket BU bans. :P

I'm not sure if this block or viabtc's invalid coinbase txn block is a more interesting failure.

This one is a nice demonstration of what actually happens when someone attempts to perform a blocksize hardfork.

In December Bitcoin Unlimited introduced [a bug](https://github.com/BitcoinUnlimited/BitcoinUnlimited/commit/eb7db8b15dce186870568c6b0dc156bdb179710a#diff-4a59b408ad3778278c3aeffa7da33c3cL124)-- not reserving enough space in the block for coinbase transactions-- that they recently shipped in BU 1.0 (it doesn't appear that there were any earlier release candidates with it).  The impact of this bug is that in common mining configurations (including Bitcoin.com which AFAIK is run with the direct involvement of BU) this can result in somewhat oversized blocks, even when configured with a maximum of a million bytes. Then, as an intentional part of their design, the BU software will also forward oversized blocks if someone creates one. The change appears to have been made via a direct commit (no pull request) so it doesn't appear to have had any peer review of any kind.

Every Bitcoin Core node happily banned the BU peers that gave them this invalid block, and went on running as if it never happened.  SPV wallet user who were connected to BU nodes may have seen a false confirmations for 24 minutes until 450530 was mined which decisively overtook the invalid 450529. Fortunately, in this case it doesn't appear that SPV miners exacerbated the invalid block fork though several did follow it briefly.

In Bitcoin Core the code in question has a [comment](https://github.com/bitcoin/bitcoin/blob/master/src/miner.cpp#L118) that explains why it's there; which was added about a year before BU created this bug in their own software.

It's possible that there were more invalid blocks that resulted from this incident, but it's hard to be sure-- because the nodes forwarding them were banned by everyone else any further blocks on that fork would not have propagated well at all, so I simply may not have seen them.

Most Bitcoin sites never showed this bad BU block at all. e.g. it's not in bc.i or smartbits.

[copying from elsewhere on reddit:]

Incidentally;  there is now more evidence that it's faked. Beyond being just the wrong key (the right one has been on the website since time immemorial) the PGP key being used was clearly backdated: its metadata contains cipher-suites which were not widely used until later software.

$ gpg --export 5EB7CB21 | gpg --list-packets -  | grep pref-hash

        hashed subpkt 21 len 5 (pref-hash-algos: 8 2 9 10 11)

Compare to the well known key:

$ gpg --export 5EC948A1 | gpg --list-packets -  | grep pref-hash                                                    

        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)

The 8,2,9,10,11 list was added to the GNUPG code tree in commit e50cac1d848d332c4dbf49d5f705d3cbbf074ba1 on July 9th, 2009, and not released until version 2.0.13 later. This is well after the 2008 date on the key. The 2,8,3 list was the prior list the would have been used in 2008.  That they were different at all was surprising, considering that they claim to be generated less than a day apart.

This key was also not on the keyservers in 2011 according to my logs; which doesn't prove it was backdated, but there is basically no evidence that it wasn't and significant evidence that it was.  And it's not turning up in any of the older key server dumps.
This doesn't really give you any clue about how dijkstra works.

It's more of a demonstration that the developer of the page knows how to code a path finding demonstration, but it's not very educational for the viewer.

Bitcoin, not some incompatible hardfork thing forced by a private agreement between a VC and a couple miners.

I will never work on such a thing and I believe this is true for most other developers.

But I also don't think it's an actual concern,  BIP-91 and BIP148 are likely going to activate segwit and segwit2x and BIP148 advocates will declare victory and go home; and '2x' crazy hardfork will be abandoned.
Ha, well the price was around ~$6124 when I sent in my resignation.

For the most part it seems that technology things don't drive the price in the short term-- there are a large number altcoins which prove that pretty clearly. :)
This is an absurd change.  It is minimally useful for users, and mostly looks like a pretext "yes we added replay protection" which doesn't really protect, and bloats up Bitcoin as a side effect.

It also adds an alarming coin blacklist to s2x. Visions of things to come in that coin?

Instead they could have made a ~2 line change to allow an extra ignored bit to be set in the sighash flags, or a simple additional serialization so that you could make S2X only transactions.  They're making a hardfork in any case, so it would be trivial to allow a new transaction style that Bitcoin doesn't accept.

Since use of it would remain opt-in it also would continue to not be real replay protection and would not do much to protect most users from losses-- but it would be strictly better than the ugly hack they implemented instead,  and wouldn't burden Bitcoin's chain and UTXO set with additional unnecessary data... and wouldn't have the technical debt or ugly precedent of a consensus address blacklist.

Whats interesting is that Bitcoin ABC (bcash) had basically the above in their first version before they implemented replay protection.  They had the technically clue to get that right and the integrity to not falsely describe a one sided only by request thing as replay protection.
Make sure you photograph the 'spot' and the fridge.  This way if you have further issues with them, you can document their insanity for the courts. :(

Really— My understanding from friends which were victimized by false reports is that the CPS systems often manipulate the courts by producing enormous amounts of "documentation"— which your busywork is the beginning of—, far more than the attention span of the court. In CPS's summary they say "We visited five times over two years and each time had to direct cleaning of the home due to our concern for the safety of the child".  Then most parents respond with "but but, it wasn't like that— it was just some nearly invisible spots", but CPS brought 600 pages of documentation and the parents, with nothing, will not be believed.  You will _need_ documentation if you are to have any voice at all.

All that is required in your case is for some CPS worker to decide that they're philosophically opposed to single fathers, or get dinged in some performance review because their lack of intervention looked like non-performance and you could be rolled right over.  Hopefully this will be the last of it and my message is just a paranoid warning— but an ounce of preparation today can potentially save you and your family from harm down the road.

Note how ineffective the 'ruin' was when it was active? ... mostly just fueled FUD, but the system kept working fine except for a few wallets that still had weak fee handling.

Bitcoin is _designed_ to handle variable load, thats why the mempool exists.  Thanks for further validating the design, attacker!
On the topic of "denouncement", I think this note is regrettable: It is both unprofessional and a crass move of disrespect towards fellow human beings.

I called out Theymos on the moderation policy and counciled against it. Yet the poster here claims there was only silence; this is flat out untrue.

At the same time, when moderation was temporarily discontinued before aggressive moderation was first instituted the flooding attacks on the /r/bitcoin were so bad that you could flip three pages before seeing something that wasn't an (often untrue) advertisement for Bitcoin XT along with a myriad violent insults and conspiracy theories. That same embarrassing non-professionalism and near zero SNR now plague the alternative subforum today.

How loud can I continue to oppose when the "uncensored" alternative is both uselessly bad and, yet, also "censored" itself?  I still think the aggressive moderation is a bad move, but would we be better served by turning /r/bitcoin into /r/btc? Absolutely not: And even to someone who believed that the /r/btc way was better: it already exists.  Probably the most essential element of free speech online-- where the ability to speak at all is nearly a physically inalienable right-- is being able to have your own space, where attacks can't bury your words in tripe. In that sense the constant bragading attacks on /r/bitcoin and against the Bitcoin Core project's own communication tools are some of the worst kind of censorship possible online.

Against this backdrop; people from the bigger-blocks-at-any-cost community run with smears and insults against myself and the longstanding developers of the Bitcoin software and protocol the network uses today. Though that community mostly acts through pseudonymous throwaway accounts, some of the attacks are by well known names.  Does the poster here denounce it? No, not that I've observed.  Do they denounce the threats against my lives of myself, my family and other Bitcoin developers?  Do they denounce the lies being spread to attack my reputation and others who have supported Bitcoin so many years? Do they denounce the flooding of the Bitcoin Core communications channels and github by sock accounts? No, seemingly not.

Of course, there are an effective infinitude of things going on in the world that any reasonable person finds reprehensible. Waging a campaign of disagreement with most of those things has no effect beyond wasting time that could instead be used to inform or make the world a better place.  So, I don't expect the poster to have denounced all the vicious and sometimes unlawful attacks performed in the name of his interests.  But I do find it ironic that he criticizes others on the basis of a higher standard than he seems to hold himself to.

Emotionally, the argument that Bitcoin Core has an affirmative responsibility to denounce a social networking forum choosing a foolish moderation policy makes the lack of his own opposition to all the vile attacks against us conspicuous by omission. Was it the intent to suggest a degree of implicit support for the parallel attacks that he hasn't denounced? I doubt it, but I can't deny feeling a little bit that way. But if I really expected it done, I would have asked in private; not via an unprofessional public list of demands.

If it's any consolation you generally wouldn't learn much about any of these things as an undergrad-- and you don't have to be in college to learn about any subject you're interested in.
Not just my work,  I had lots of help. In particular the general approach is [originally Adam's](https://bitcointalk.org/index.php?topic=305791.0); my contributions are implementing it and some (IMO quite) substantial optimizations.

Adam Back, and Andrew Poelstra, backed me up with review on the cryptosystem work; while Luke-jr, Matt   Corallo, and Pieter did the integration into the alpha codebase.

And half the technical people who've had the misfortune of spending time in my presence for the last month has had the misfortune of being a test-dummy for explaining the feature and its design to others. (and with their patience I am tremendously grateful).

In addition to the normal pay per view streaming, this film was also offered at an increased price under CC-By-NC-SA. I purchased a freely licensed copy and have placed it on archive.org allowing anyone to learn from it, share it with others, or build new works out of it without risk of prosecution and without going through US-only paywall.

The official site for the film is at http://www.takepart.com/internets-own-boy
and the there is a Wikipedia article at http://en.wikipedia.org/wiki/The_Internet%27s_Own_Boy:_The_Story_of_Aaron_Swartz

The work is an poignant, factually focused work, suitable both for people who are closely familiar with the background and those who are not.  The film is heavily sympathetic to Aaron and his plight, but not at the expense of accuracy— in my personal experience.  I found it a bit difficult to watch at some points, but that might be due to friendships with so many of the people being interviewed.  I'd happily recommend this film to anyone.
> Friendly hackers generally doesn't give you 2-3 months to prepare, to say the least.

This is a misrepresentation of the history. He can natter on about "gatekeepers" all he wants which I'm sure will get applause, but that doesn't mean it has anything to do with what happened.

First it wasn't really "reported to us": we fixed it before chjj was aware of it, and chjj reverse engineered the behavior out of the codebase as a side effect of an argument in btc1 where he wanted "2x" to add protections against attacks opened up by blocksize increases and the developer of 2x disagreed.

[A pull request for this fix in the Bitcoin project had been opened on](https://github.com/bitcoin/bitcoin/commit/8b25d2c0ce64d7f8577a0c2e601e505c9f1140bf) April 25th, reviewed, and merged June 1st.  Because this fix approach requires a major reworking (and backwards incompatible) change to the consensus database it took significant review and is only in a new major version which is in it's third release candidate now and isn't scheduled for release for another 4 days (on Sept 14th or so).

A month after the fix was merged in Bitcoin Core, on [July 2nd](https://www.reddit.com/r/btc/comments/6z827o/chris_jeffrey_jj_discloses_bitcoin_attack_vector/dmtcq58/), chjj communicated with sipa about it via email and on July 21st chjj began working on [a fix for the same issue in his own software](https://github.com/bcoin-org/bcoin/pull/246) (the same issue existed in AFAIK all other implementations, including btcd and bcoin; making an elegant counter example against chjj's arguments that more implementations are somehow protective).

**In the email thread chjj suggested that he would "avoid disclosing this until the fix is widely deployed."**

[11 days ago](https://github.com/Bitcoin-ABC/bitcoin-abc/commit/611284f473fb9c3b60a6f2c29cab9cc2b5798d64) Bitcoin ABC copied the fixed code out of Bitcoin Core verbatim, stripping off all attribution (which is both unethical and a violation of the software license) along the way and released it with little to no testing and no release candidates. 

Chjj then disclosed the issue to the public in contravention of normal responsible practices,  and the statements he made in the email. AFAICT he made no effort to inform anyone associated with the Bitcoin project that he intended to do this.
But at least they are ready for exabyte blocks! (they went through and made the block size a 64bit type...)
Yes, it's been tagged for a day but the binary release process takes about a day due to security reasons. We don't normally send out announcements until the binaries are up.
The best way to be vulnerable is to be too sure that it couldn't happen here. We have to have a clear continued commitment to being clear about and upholding the values that make Bitcoin valuable in the first place.

It's not a question of exposure, many well known figures in Bitcoin have been exposed when various services lost funds. You didn't see any of them advocating these sorts of moves one of the reasons is that people were clear about what made Bitcoin valuable long before it had a market price. But this commitment has to be continually renewed through or words and actions, by how we communicate about Bitcoin to others and in how we use the system. The community changes over time and the purpose of Bitcoin can change with it.

All of this is a question having the option to chose a money which is primarily autonomous, self determined, and following clear dependable rules, for better or worse, rather than a money which primarily politically governed and may be more convenient at times but is fundamentally less trustworthy.

A generation ago, multi-user time-sharing computer systems had a similar problem. Before strong encryption, users had to rely on password protection to secure their files, placing trust in the system administrator to keep their information private. Privacy could always be overridden by the admin based on his judgment call weighing the principle of privacy against other concerns, or at the behest of his superiors. Then strong encryption became available to the masses, and trust was no longer required. Data could be secured in a way that was physically impossible for others to access, no matter for what reason, no matter how good the excuse, no matter what. It's time we had the same thing for money.

Bitcoin has a community of users, developers, supporters, that existed before there was a meaningful market price, who came here for for reasons like the above. No user of ethereum came to the system before it had a price, as ethereum was premined and sold before it event existed-- that doesn't mean that none are principled (or even that most aren't) but the dynamics are different. If we don't remember and don't prioritize accordingly-- if we shy away from the values that make Bitcoin valuable in the long term for short term reasons as we chase new audiences and wider adoption-- the Bitcoin community could lose sight just as well.
What better way to avoid dealing with your families awkward questions about future cryptocurrency prices than spending your christmas morning huddled around the warm glow of your Bitcoin node as you install this latest update?

Once you're done it'll be time to watch Die Hard.  ... or on second thought, maybe not, the image of the bearer bonds might remind a sad lost family member about some ICO they want to pitch you on. :)
then run the code in the pull request and please report if you lose any coins!

Cheers.
Thanks for bringing that into the sunlight. It's good to see confirmation of our belief that even if we could add replay protection to existing nodes they'd just act to undermine it.
I like how they're announcing their selfish-mining and premine (mid-mine) to the public and expecting people to be cool with that.

FWIW, even those foolish enough to run their BitmainActivatedHardFork code which would follow their blocks, when Bitmain releases blocks that they unfairly kept private your hours, you just run the rpc invalidateblock <hash> and your node will ignore their attempted reorg, seems obvious that everyone except them will run that, causing them to dump days of mining down the drain.

> What a difference it made with performance! 

Yes, although in the case where the dbcache is cranked up to 8GB the ~40% speedup from 0.14 to 0.15 pretty much matches the growth of the chain exactly. :(

E.g. on 24-core 64GB machine here syncing over gbe w/ 8GB dbcache,  0.15 takes 2.98 hours to sync and 0.14.2 takes 4.59 hours to sync.  A big speedup.   But if we look at the time it took to height 453354-- the point where 0.14 branched-- 0.15 takes 2.25 hours and 0.14 takes 3.09 hours.

So with the large dbcache set on a fast system, 0.15 takes about as long to sync now as 0.14 took when it came out (about 3 hours). Even though the sync is >35% faster. :(

The fact that chain growth keeps wiping out our improvements is why some fudsters can claim that the project isn't doing anything with a straight face.  I'm thankful that you did a bit of direct comparison and posted about it.

The improvement with lower dbcache settings-- like your machine requires-- is much more impressive however. With the default settings, again on that pretty fast machine:

0.15 is 4.54 hours for a full sync compared to 10.43 for 0.14. (3.38 and 7.56 hours for syncing only to height 453354).


By default it uses both tor and non-tor; and in doing so should make the connectivity more robust than either alone and also providing better connectivity for users that are tor only.
pfft. who cares?  The only interesting thing here is the depressing news that he's _still_ not all in on BCash.
>  it will be banned anyway, because it is sending invalid blocks according to Bitcoin protocol. 

it's not that simple. They will eventually be banned, maybe.  But for example, lets say your Bitcoin node connects only to 2x peers (e.g. because a sybil attack starts in the weeks before).  Then 2x activates but miners don't go along with it.

Your node will be isolated and no longer getting blocks from the honest network, but also not banning the 2x peers because they're not accepting any blocks.

Even when they do, you'll only ban one 2x peer for each new 2x block at most, and you might just replace that banned peer with another 2x peer.

There are many similar partitioning scenarios.  This is why it was important to disconnect them preemptively.

There is ongoing research for more aggressive automated mechanisms-- but it takes time to do it right and not introduce new vulnerabilities.  Unfortunately, s2x chose to intentionally bypass the one simple and safe protection that we could deploy without delay.

So the only "point" I see in this change is that it undermines a protection and maximizes the risk of disruption. Ironically, given the node distribution the negative effects will be far worse for s2x than for Bitcoin nodes (something we also saw with Bcash).
Lots of other people get abused by crazies online (e.g. https://garry.tv/2015/11/10/stalkers-and-abuse-part-1/ ) ... it's something that I guess people just really haven't figured out how to solve, but I'm not alone in it.

I figure that I can't let them influence my behavior too much or it creates an easy formula to get whatever they want. Write a couple of nasty and untrue messages and your opposition folds... It has to stop somewhere, so I say it stops with me: I'm not going to frighten off.

Thanks for your thanks.
I also had the impression that Ethereum was going out of its way to not learn from Bitcoin. Beyond the more subjective architectural errors that they could have avoided if they were paying attention  Ethereum went out and outright reproduced bugs that Bitcoin originally had and had  eliminated, e.g. unbounded memory usage from OP_CAT.  I found that find of inexplicable. (even more than the recursion example, which I also think was good to raise.)

What this article doesn't cover is the fundamentally different mental model most of us in the Bitcoin highly technical sphere have for smart contracts. The key realization behind it is that smart contracts are _verifying_ not _computing_-- the inputs are the contract, the transaction, additional evidence provided by the user, and the network should either accept or reject the state update represented by the transaction: Script is a [predicate](https://en.wikipedia.org/wiki/Predicate_%28mathematical_logic%29). The user(s) do the computation, then prove it to the network.

Verifying is fundamentally easier (and I mean really fundamentally: verification of a NP statement, given side information is in P).  Verification is also usually much easier to make highly parallel.  As a toy example, say I give you some giant numbers A and B and ask you to divide them to compute Q = A/B.  The division takes a lot of work.   If, instead, I tell you Q, A, and B-- you can multiply and check the value-- much easier.

This model is also how we get useful tools like P2SH and MAST... you don't have to provide the contract to the network until it's enforced, and if the contract is complex and not every part needs to be enforced you can only ever publish the parts that get used. The unused parts just get represented by a compact cryptographic hash.

This distinction also simplifies the system from an engineering perspective. Script is a a pure function of a transaction, so if a transaction is valid-- it can't be invalidated by other transactions coming or going, except via the single mechanism of anti-doublespending. Similarly, OP_CHECKLOCKTIMEVERIFY isn't OP_PUSH_CURRENT_HEIGHT_ONTO_STACK-- the script expresses what it expects the locktime to be, and the machine verifies it. The construction means that the chain can reorganize with invalidating chains of transactions and it makes the verification work highly cachable.

Is this mental model similar to people familiar with conventional programming (say, on the web?)? No.  But smart contracts aren't conventional programming, and blockchain isn't a conventional computing environment (how often does history change out from under most of your programs?). These design elements make for a clean, simple system with predicable interactions. To the extent that they make some things "harder" they do so mostly by exposing their latent complexity that might otherwise be ignored-- some tasks are just hard, and abstracting away the details in an environment with irreversible consequences is frequently not wise.

This may be bad news for the hype of converting the whole universe to "distributed apps"-- though a lot of that just doesn't make sense on its face-- but the fact that a lot of people are thinking seriously and carefully about the subject is good news for the technology having a meaningful long term impact.

I can confirm that SarahCoinBit works for Mt. Gox and that these things were said.

PLEASE STOP ANNOUNCING TAGS.  BINARIES WILL NOT BE UP FOR DAYS.   THESE PREANNOUNCEMENTS CAUSE PEOPLE TO MISS THE RELEASES BECAUSE THEY SEE THEM AND CAN DO NOTHING, THEN WHEN THE RELEASE IS ACTUALLY ANNOUNCED ITS OLD NEWS.

That is all.
So where are all the people saying that malleability is a non-issue and segwit is terrible for solving it?   That is the party line here, right?
What can you do?

(1) Speak up.  I am told that one of the commercial opponents of segwit has on the order of 30 employees that are no doubt posting here under at least that many identities.  If you aren't as vigorous as they are, they can create a false appearance of controversy.

(2) Run a full node, preferable at home-- nodes on VPS services add little to nothing to the network's decentralization. One of the arguments used against segwit is that it take a long time to be adopted widely and have an effect. ~78% disproves that, but 99% would disprove it better. Using wallet software from [segwit supporting parties](https://bitcoincore.org/en/segwit_adoption/) is also good, but what wallet you run is far less visible than just running nodes are, independent of segwit the robustness of Bitcoin is improved by having more node operators.

(2b) If you have problems running a node, the developers need to hear about it so we can improve the software to eliminate those problems. Don't just assume we know. We may not, or we might have lost track of your issue. 

(3) Reach out to other people in Bitcoin, miners and others. They may have no idea about any of this or could have been fooled by false controversy created by people who are confused or who don't have Bitcoin's best interests at heart.

(4) Don't wait for me to tell you what to do. I don't have all the answers. Make suggestions and act on them.  I think the discussions about a BIP16-like time triggered softfork are interesting, and though it's premature for me to have much of an opinion on that, people exploring more routes is good.

I hope other people here will post ideas about how people can personally get involved.
Nah. The obvious and logical thing for the attacker to do in your example is just up the offer. Ultimately that reduces to RBF scorched earth but with some pointless disruption in the middle.

Stolen assets are stolen, the tool to get them back, if any, is traditional law enforcement.
This kind of claim about original codebase is oft repeated, but in my opinion it's untrue.

The original code base was small and clean. It didn't have extensive modularity-- indeed-- but it shouldn't have: It was 14,615 lines of code (9500 lines without the separate UI parts) and clearly written by ~a single person.  Extensive modularity would have increased the size and complexity without increasing the functionality, at the expense of the defect rate.   Things were not randomly splattered around-- there was a [main.cpp](https://github.com/trottier/original-bitcoin/blob/master/src/main.cpp) which contained most things, and a few separate other files that contained other logical units.  I found it to be highly readable and was able to get a clear understanding of the functionality with basically a weekend read-through.

Data structures were largely defined along with the code that used them-- which is good for reading to understand, though less ideal fore reading to reimplement; similar to the use of templatized seralizers (which were also responsble for a lot of bug avoidance).

The functions and variable names are all clear and descriptive, the code is commented usefully (though not in the kind of epic explanatory way someone might go for). It was largely free of redundant or superfluous code. In all these respects the software would fair very well in a comparison with most commercial proprietary packages I've had the displeasure of working on; and not too poorly against many Open Source projects (especially ones with small development teams).

And the defect rate is particularly notable; at inception the software was unusually free of defect. Unlike many other initial Bitcoin packages which in their first releases were full of crashes and deadlocks Bitcoin was nearly free of them. Most of the serious bugs fixed subsequently were added by other people.

Though the software did not ship with tests, it's clear that extensive testing was done-- and there were additional testing harnesses not published with the software (some were shared privately with others), I can only assume for privacy reasons.

While the initial version was shipped as windows only, the software was easily portable-- rather than using MS specific functionality, it used cross-platform tools.  It utilized a relatively modern C++ style, largely eschewing manual memory management for container objects (for example), a decision which probably contributed to the low defect rate.

This isn't to say that it got everything right; it surely didn't-- but much of the weirdness and problems came from external code, not code written here. And so I find it strange and disappointing to see you continue to describe something that was unusually good in may respects as anything otherwise.
Glad to see this change.  Luke-jr dug in a bit after the first contact on IRC was really over the top rude (and from someone who'd been kickbanned a number of times in the past for bad behavior).  Never a good way to sort out a technical decision.

OTOH, while I strongly disagreed with the 'anti-spam' approach (and had long been nagging luke to do more pure behavioral matching on the abusive transaction behavior (censor-magnet, and UTXO bloating)), I am a little sad to see many people criticising a different distribution of Bitcoin Core not just for its own policy decisions but for being different at all.

There is no mandatory official Bitcoin, and when it comes to node policy-- (not consensus rules, of course)-- diversity is valuable, and people should have the ability to control what their computers are doing, how their resources are being spent, etc.  But control means actually realizing something was there. Which likely wasn't the case here, so I'm happy to see the above.

Luke says random shit from time to time.  You take the good with the bad.

He's making that suggestion only because individuals choosing to not use segwit can help hold the size back a bit. He really wants a smaller blocksize-- for reasons which are not insane-- and isn't the sort to be the first to recognize when a ship has sailed. 
And three people do it, three, can you imagine, three people walking in
saying some naked orphan annie shit and walking out. They may think it's an
organization.  And can you, can you imagine fifty people a day,I said
fifty people a day walking in gibbering some naked orphan annie and
walking out.  And friends they may thinks it's a movement. And that's what it is...


A key point I like to make is that if you over extend yourself you will be a bad investor.

A good investor is calculated and unemotional. A good investor has a plan and executes on it, revises it with reliable new information, but otherwise stays the course.

A bad investor is irrational and emotional. They get worked up and constantly second guess themselves; they get worked up by crowds.  They freak out at rapid increases and buy high, they freak out at rapid decreases and sell low.

Accidentally leaving my brokerage keyfob at home has resulted in some of the better trading "decisions" that I've made.

For most people, if they over-extend themselves, they will behave more like a bad investor even if they know better... if you've made an investment you can't afford to lose, you're much more likely to lose part of it in a panic effort to prevent losing all of it.

It can be easy and fun to visualize the million dollar Bitcoin and the private space elevator you're going to finance with your new found wealth...  but you should be equally mentally and emotionally prepared for movement in the other direction. If you aren't, you're going to do something different than what your own dispassionate analysis would tell you to do.

Probably the hardest thing about this is that the fears that drive bad trades are not completely ill-founded.  It's _possible_ that if the market is now at price $x that this will be the last opportunity to trade (in either the buy or sell direction) at that price in your lifetime.  But given the volatility of Bitcoin,  "seldom again prices" are pretty rare, and happen in both directions-- so while it's possible to miss out, but you shouldn't let that drive your decision making. People tend to think only about the option they're considering and forget that for every trade someone else is taking the opposite position.

Another thing to keep in mind is that trading itself involves fees, which are incrementally small but add up.  You can make every decision correctly but still lose a lot of funds if you trade frequently.  It's not enough for a trade to be right, it has to be right enough to overcome its overheads (e.g. spread and fees). You can improve your odds both of not wasting funds in fees and of making poorly considered emotional decisions by behaving more passively.
I've found myself at something at a loss in responding to this article, not because it raises points which are difficult to respond to, but because it's wrong in so many distinct ways.

For example—  It complaints that Ogg uses a byte for the version field when it could have used a bit (and then added more bits in later versions).   Thats true.  But you're talking about 7 bits per half million or so.  Is this _really_ something worth mentioning?     On the flip side, the use of a constant byte for the version field helps Ogg keep the whole header byte-aligned, which makes it easier to describe and easier to code for.  Ogg's RFC has a nice ascii drawing of the page header,  the "NUT" format by the author of the essay is only ever completely described by code/pesudo-code, "If this bit then read two more and interpert this way".

Reasonable people can disagree over which choice (simple byte alignment vs wasting some bits) is ideal...  but I think you have to be really strange and obsessive to think that an additional 0.0015% overhead is something worth worrying about.

Another case where the comparison is screwy is that it seems to expect that Ogg should be as good as every other format in each area where the other format is best.

Take the latency comment, for example.  Ogg can be low latency, in which case it has fairly high overhead similar to UDP+RTP,   or you can use it in a manner which has low overhead, but higher latency (similar to MP4 or MKV). 

I'm not aware of any other widely used container format which provides "one packet" latency like Ogg can but with substantially lower overhead.  

Or on the overhead front— Ffmpeg intentionally uses Ogg in manner which has almost the highest overhead possible.  And the ffmpeg folks have gone around spreading some [pretty misleading claims](http://lwn.net/Articles/373023/) about Ogg vs Mp4 overhead.   The reality is that a well muxed file has overhead comparable to other formats as they are normally used. ([a example of a tightly muxed file](http://people.xiph.org/~greg/video/ytcompare/bbb_theora_486kbit_remuxed.ogv), the container overhead is 84604 byes, or 0.491%. For comparison, an ffmpeg remux of the file has an overhead of 639616 bytes or something like 3.6%)  

I could go on about this all the day.  But it's all just boring computer-geek-dick-sizing and arguments about the blue spoiler being superior to the red spoiler.  

None of the discussion is at all relevant to people who simply want the software they use to work.     

(EDIT: I fixed some grammatical atrocities; Added a link to a tightly muxed file) 
> (which seems like an easy and reasonable thing to implement) 

Unfortunately, litecoin copied the bitcoin P2SH version id, so you can't tell those addresses apart-- similarly to not being able to tell non-bc1 addresses from bcash addresses.

If anyone sent bitcoin to a litecoin address except due to that p2sh version copying they really screwed up and you should run far far away.

It's depressing that we can carefully develop and design things in Bitcoin to be safe for users by construction only to have third party clones that aren't as careful screw things up for Bitcoin users. :(  Hopefully the Bitcoin suggestive prefix on bc1 addresses make people a little less likely to clone them unmodified.

Our industry to reduce these problems by strongly encouraging users to use bc1 addresses-- as they don't (currently) have this problem.

Users can also protect themselves by avoiding altcoins and businesses that do business with altcoins, but I guess that's the sort of advice that people never take until after they're burned.

> The only defense I can think of is a PoW hard fork, miners should not be in control.

There are more defenses than changing PoW-- though they work better if attackers don't see them coming.

And some hardware has historically supported alternative POW (basically you can cheaply make your mining circuit able to mine a modified version of the work function), so it may be possible to change POW in a way that blocks some hardware and not others.. thus minimizing the collateral damage.

The fact that a PoW fork has significant collateral damage is part of why it's a nuclear option and not one to be taken lightly.  It's also a reason why the mining industry should be acting to discourage the kind of foolishness that would ever make one a serious consideration.
The n-th root of 2 is irrational, this in and of itself isn't surprising. You might find this proof for n>2 surprising however:

Assume a rational solution exists for some n>2,

(x/y) =  2^(1/n)    (statement)

(x^n ) / (y^n ) = 2    (sub)

(x^n ) = 2 * (y^n )  (multiply both sides by y^n )

x^n = y^n + y^n  (expand) 


Contradiction: No integers x,y exist where x^n = y^n + y^n for n>2 per Fermat's last theorem (Wiles '95).

QED.
I'd really prefer not recommending 0.9.5 to people. It's no larger an upgrade to go to 0.10.2 unless you are carrying a lot of local patches; and 0.10.x fixes many important issues that aren't fixed in 0.9.x.

The 0.9.x backports exist primarily to support operations that are carrying patches. (Though it seems to have done little good considering the multiple services that clearly were on the wrong side of this fork).

Well, on a really really slow machine, perhaps an Amiga 4000 it might be only real time. Of course you can insert sleeps or read from a blocking audio device…

The Opus reference code is production grade— otherwise we couldn't be completely sure the production code would be acceptable (as we didn't really have the resources to write two encoder implementations; though a second almost complete decoder implementation was written for validation purposes).  The library lacks snazzy platform specific optimizations, simd, etc.  But it still encodes and decodes in a large multiple of realtime on anything you're likely to run it on.... with or without floating point.

At one point CELT also ran fine on TMS320c55 (a dsp where char and int are 16 bit) but the whole Opus codebase probably needs a few fixes for 16 bit systems right now.

It's also been tested on a very broad array of systems, everything from IBM S390 to an 16MHz 68030 (not realtime!), and a bunch of points in between: x86, x86_64, ppc, ultrasparc, ia64, HPPA, dec alpha, arm7, arm11, vax (via emulator), and tried PDP-11 but the compiler wasn't ANSIC enough. In addition to these it was tested with a host of operating systems (though I never was able to get a UNICOS account...) and compilers.  ... Beyond the goals of portability and C standards conformance, testing with a wide variety of build environments was one of the techniques I used to expose bugs. During development and testing I uncovered and reported three GCC bugs and one intel compiler bug. The software has been through many millions of audio hours of testing— mostly on a 160 core cluster that spent easily a year of time on Opus R/D/ testing, including extensive fuzz testing, whitebox fuzzing, and partial formal verification. The built in test suite gets [97.3% line, and 90.5% branch-conditional coverage](https://mf4.xiph.org/jenkins/job/opus-coverage/ws/coverage/index.html) of the whole codebase (a few of the non-dsp utility things aren't quite as well covered by the automatic tests) and they invoke the API hundreds of millions of times. 

This isn't some hardly usable toy implementation that runs at a fraction of realtime and produces horrible results while simultaneously overrunning buffers or crashing when you blow on it funny, just so you can be upsold something better like you get from the MPEG people.  While it, like any open source project, can be expected to get better over time the software is deserving of the 1.0 moniker.

Let me suggest some for you,  "Thank you Jeff Garzik."-- who is not only driving the reckless B2X software but maliciously undermined the simple protection we put in 0.15.0 forcing prioritizing putting in protections he cannot undermine.
There used to be a routine that you could do which took advantage of the lag between funds available and check processing with ATM deposits.

Basically it worked something like this. You have two bank accounts with nothing in them.  Write an uncovered $1000 check from one to the other.  Wait a day and write a $1000 check from the second to the first.  The first check will eventually clear using the phantom money from the first because funds were available faster than check processing.

With the right timing you could get a substantial amount of imaginary money flying around in a long cycle for more or less as long as you like.  You could then take short term loans out of the circulating money but managing it would be a lot of work.

I never did this myself, so I might have the details wrong. I had a friend who did this when he was short on cash... I'd check with him for the details— but he's currently in federal prison (no kidding, but for something unrelated to this!).

/u/gavinandresen that might be a point worth debating if an optimization was actually being blocked.  The proposed BIP does not attempt to block boosting, only covert boosting that jams protocol upgrades.

Considering that the document and email in front of it point this out explicitly, in the future I recommend taking the time to read proposals before criticizing them.

We implemented something stronger and less discriminatory: the dust limit.  Which does not work how you're describing Bitcoin working there-- NO amount of fee can get you past the dust limit, you have to make an output over the threshold (so the funds go to the destination and thus make the output economical to spend).

> BUT the code still only enforces the same mintxfee if you send to many small outputs. The fix is simple: require a mintxfee for each tiny output.

Incorrect, and the fix was simple require outputs to not be so tiny that they cost more to spend than they're worth.

The dust limit was widely discussed, including on reddit: https://www.reddit.com/r/Bitcoin/comments/2unzen/what_is_bitcoins_dust_limit_precisely/

Unfortunately the dust limit setting is tied to the min relay fee threshold for non-free transactions and that's currently set much too low by default and many miners have turned it down lower (usually after backlast for making "small blocks" when there just weren't any other transactions to mine).  [The reduction](https://github.com/bitcoin/bitcoin/pull/3305) in the min relay fee was pushed by Mike Hearn and opposed by many, but there just isn't the political will to uphold every detail, especially absent an active attack.


> Because of this fix, Litecoin's UTXO set is much more manageable than Bitcoin's

Really? Warren made this claim some months back in #bitcoin-dev and he was wrong: Litcoin's UTXO set was substantially larger in spite of having 1/10th the number of distinct addresses as Bitcoin and in spite of a much smaller chain. I could believe that Bitcoin's is somewhat bigger now becuase there have been months of weekly utxo inflating attacks on Bitcoin and Litecoin has seen fairly low activity. But I would be surprised if litecoin had a lower ratio of utxo size to chain size than Bitcoin.
Satoshi was interacting with developers for months after that... it wasn't "the day before he disappeared".   I believe the last interaction w/ Satoshi was when a bitcoin developer was invited to meet with the CIA.

It seems to me that people just repeat that in order to smear wikileaks.
> I see benevolent, soft spoken, brilliant scientists and engineers who genuinely love the work itself above all else.

But in one sense, that appearance is also a problem as well: There are a lot of people in this space that just do not have any respect for people like that.  Instead they go for people like wright blustering outright technobabble and tweeting pictures of lamborghinis. They don't understand the motivations of someone who isn't in it to flaunt money and so everyone who doesn't do that seems weak and impotent or even suspect.

https://www.scribd.com/document/362437500/Metronome-Media-Kit-FAQ

And here I was thinking it there might be some technically interesting idea (that they'd probably never implement) only to find that it's just another dumb ethereum ICO.

I wonder if Garzik is going to keep [denying](https://www.reddit.com/r/btc/comments/6udrsz/jeff_garzik_removed_from_bitcoin_github_repo_for/dlsby5b/?context=1) that he's been primarily involved in ethereum for some time?
I don't think it had anything to do with it being green, I think I read it in a different 'voice' because of the '>' formatting indicating that it was a quotation.

> while every client is 100% compatible

It is NOT compatible.  Connections are not like http fetches one shot and done, they're long lived. A peer that will silently start censoring blocks on you in the future is not something you should be connected to now, even if you could disconnect it then, because disconnecting all at once can leave you hunting for reliable connections in a congested network for a long time.  Bcash had these problems even though it has a somewhat safer design than S2X.

This was explained on the PR discussion.
BU developers are apparently blaming full blocks for their crashing:  http://archive.is/CbIqq
> The productive attitude in an Open Source project is to report bugs or submit Pull Requests, not blog about them.

They've never reported any of these "bugs"-- but for these, the deeper reason appears to be that there isn't anything to report.

AFAICT  many of issues were actually caused by changes they made to code they didn't understand.

For example, #1 -- both the GUI and RPC return clear errors when the fees are nuts and don't ever make it to the code in question.  But I could see how an implementation thats ripping out the fee handling might break those invariants. Here is a picture of trying to pay a fee over the maximum in the GUI: http://people.xiph.org/~greg/temp/absurdfee.png  ... no transaction is created and life goes on like normal. RPC does the same thing. *Moreover*, if you do end up with a transaction in your wallet that can't go into the mempool in Bitcoin Core (which wouldn't happen due to too high a fee (see above) but could due to being too low-- though the wallet works hard to keep you from doing this to yourself),  you can simply right click on it and select 'abandon transaction'.

The #2 item is entirely about debug-lockorder, a highly specialized set of development testing harnesses which are _never_ used in production (they trash performance, and can cause spurious failures, among other issues). They replace every lock (code that synchronizes multiple threads) with instrumentation that records the locking infrastructure and can detect misuse that could cause deadlocks. They're a bit ugly but their uglyness is basically contained in a single file. BU's solution was to scramble up the whole codebase just to facilitate some special testing infrastructure which users _NEVER_ run. The fact that its implementation is a bit platform specific isn't especially interesting-- at least not for us. It would be fine to improve its portability if someone cared, but reorganizing the whole codebase in a fairly illogical way isn't a great way to go about it... and to call it a bug that it isn't portable is more than a stretch.

From a quick glance, #3 appears to be a place where BU was crashing because they [unsafely made message handling concurrent where it wasn't before as part of xthin](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/10#issuecomment-183506227); AFAIK none of those crashes ever existed in Bitcoin Core. (and if there was some race there that was never reported... Well, the findnodes infrastructure-- legacy from Bitcoin's creator-- has now been gone _completely_ for a while in Core... BU is based on fairly old code).

Perhaps the biggest and most concerning danger here isn't that they don't know what they're doing-- but that they don't know what they don't know... to the point where _this_ is their best attempt at criticism.

The article writes, 

> One advantage of the Bitcoin Unlimited process is that we pull the best work from all the other development teams

"2417 commits behind bitcoin:master" -- sure doesn't look like it.

Jorge Stolfi is the most voluminous poster to rbtc-- with over 219,720 words posted there, not including text he quoted-- equivalent to roughly 4 full length novels.

Many of his posts are just generic attacks on Bitcoin, but even more of them are him attacking Bitcoin Core and expressing "concern" that Bitcoin urgently needs to make incompatible protocol changes.

Sometimes you encounter a person whos opposition means that you're doing something right.  Jorge Stolfi is one of those people.
I think it's a bad idea which will harm the reputation of cryptocurrency in general and potentially fuel adverse legal/regulatory intervention (e.g. will regulators believe that developers working on cryptocurrencies can make arbitrary adjustments on demand-- with ethereum as a proof point). It's also going to create wasted effort for users of Bitcoin do differentiate themselves from the not very decenteralized hf-ethereum. ...

When the DAO caught fire and I saw forwards of chatlogs with exchanges talking about hardforking to 'fix' it, I was shocked and sent Vitalik email urging them to not do so. In Bitcoin land such a thing would be unthinkable, and I think actually impossible.

Ethereum's website has long loudly advertised (even louder than Bitcoin materials): "Ethereum is a decentralized platform for applications that run exactly as programmed without any chance of fraud, censorship or third-party interference."; and all that goes out the window when a service using the system suffers a highly predictable fault from a well known class of vulnerabilities?  I could certainly appreciate that ethereum could have a different philosophy than Bitcoin-- it's important that systems with many different philosophies exist-- but since ethereum has marked itself with a very Bitcoin like view, this is going to continue to create confusion in the market.

To all those people who argue 'hey, ethereum isn't money it's just fuel for a public distributed network'  ethereum classic will probably have a pretty good value proposition: better integrity with a lower access cost (assuming its adoption doesn't overtake ethhf).

Though I'm also curious what will be done with the logicbomb difficulty adjustment stuff (my understanding is that the current ethereum consensus rules have a pre-programmed exponential ramp in the difficulty which will eventually make the system unworkable without a hardfork).
Now next time you see one of his rants about Bitcoin Core and the Bitcoin blocksize try to keep Stolfi's clear agenda in mind.
So far, I've polled four Bitcoin Core engineers--I showed them the proposal and the median time until completely breaking the scheme is about 20 seconds. ... I'm not sure how much of that was just reading the page.

There are several different ways to achieve a total break of the scheme. One is that you simply fix your nonce to zero-- so you'll only hash the first byte (which also always happens to be a constant), and roll time and other fields instead.

Another is that you just soft-fork require (remember: we're constraining miners here) all blocks to be the same size... then you just pre-compute and incrementally update the million hashes. (This can also be combined with the one above, e.g. only scan nonces where nonce % 1e6 is less than 100 and compute 100 hashes). Even the full million midstates takes about 128 megabytes, more than a tad smaller than the whole blockchain.

The goal here isn't a new one, it often goes under the name of "Throughput proof of storage" or "storage throughput proof of work". You can see a far more reasonable version of it described on my [alt ideas](https://en.bitcoin.it/wiki/User:Gmaxwell/alt_ideas) page from a few years ago, under "POW which involves queries against the UTXO set (set of spendable coins)".

Ignoring the cryptographic flaw in the approach; this requires the user have the whole historical blockchain to verify it. Eliminating the potential for pruning. There is no reason any Bitcoin node needs to be non-pruned except to help bootstrap new nodes onto the network. It also prevents any kind of lite node-- they can't verify this proof, so an attacker could mine without providing it enormously faster than an honest miner and deceive all the lite nodes. Talk about cutting off your nose to spite your face.

Amusingly, I suggested a much narrower idea in this space (not a throughput proof, but a knowledge proof) in early 2012, https://bitcointalk.org/index.php?topic=68396.0 to stop that year's version of verification-less mining... The author of this idea is the first response.

It would be interesting to find out how things would fare for a Bitcoin without the people who spot flaws in these cryptographic proposals in seconds flat. Interesting, but I suspect not so good for the market price for my Bitcoins.

That said, perhaps it is time to discuss some of the actually _viable_ schemes which have been previously proposed for this. It's quiet easy to construct ones that aren't so obviously broken and which don't have terrible costs like breaking pruning, lite-nodes, etc..
It's super annoying that various businesses have taken their names from parts of the Bitcoin software / protocol.  Creates a ton of confusion.
Putting a centralized payment processor on the payment path makes things easier and solves some problems-- but you've seen a (small) taste of the real cost.

I don't say this to speak negatively of Coinbase: they have their own challenges which make providing a service fitting your needs hard. But part of the point of Bitcoin is that just because Coinbase can't or won't do what you need that shouldn't leave you stuck. Their-- and  similar companies-- exposure situation and risk profile is very different from yours. E.g. The civil rights related defenses are much stronger in your hands than in coinbase's.

Much of the unfortunate realpolitik regulatory pressure in the developed world comes from making life hard for institutions if they do certain kinds of business, so they, justifiably, execute their freedom to associate and decline to do business with targeted parties (plus a lot of others, as collateral damage). Often the pressure is nested many levels deep, so good luck finding out for sure where it's actually being applied. Because there was, technically, no actual restraint or an identifiable discriminatory action by a government authority, you are disempowered to correct the injustice; even if you've been left with no options. Personally, it is my hope for autonomy enhancing technology to reduce the need for vulnerable choke-points and prevent that kind of erosion of practical personal freedom and perversion of due process.

Since it sounds like Bitcoin could actually be important to your business and not just a Me-Too payment method, you may want to consider handling this directly without using third party APIs. From a overall system risk perspective it would be better for Bitcoin if more people did this, and for situations like yours the benefits are especially acute.

Unfortunately, because the vast majority just do the "easy thing" and rely on one of the few large payment processors the software infrastructure and educational materials for handling this yourself are underdeveloped today. :(

As a developer of Bitcoin Core I've been working on improving node behavior to make it easier and less costly for to merchants to run their own nodes and handle accepting payments on their own via tools like https://github.com/slickage/baron . Many improvements for this application space will ship in Bitcoin Core 0.12 in a couple months. But there is a lot of work left to do to make it really the experience that you should have.  I'd like to have more real world feedback from people attempting to do this in order to further guide improving the experience. Without people taking a step to try to make it work and giving feedback this approach will remain underdeveloped.

In a recent discussion I was asked about what I thought Bitcoin's value was to the world and I spoke about autonomy, monetary sovereignty, personal control over trust, etc. Another person responded they thought Bitcoin was great for payment because paying with Bitcoins made them feel right-- I responded paying with Bitcoin _used_ to feel right -- in 2011, but these days I've found the payment processors ask technically unnecessary invasive questions (and then presumably secure my personally identifiable information in questionable ways) which undermines much of that value. I'd like to turn back the clock on that, especially for sensitive areas where providing a more safe and secure payment method isn't just easier and good for business but magnifies people's freedom of expression, such as yours.
Some confusion about how Bitcoin works here. There are no balances in the Bitcoin system, instead the Bitcoin system tracks atomic txoutputs— you can think of them as coins with user specified denominations.  A transaction spends one or more txouts which you can sign for, and creates one or more new txouts of equal to or less that the sum of the total value of the inputs.

In this case there was pre-existing "BTC411" spam (from back in 2012: https://blockchain.info/tx-index/12283993/2 ) sent to those other large addresses. These separate outputs (coins) were relayed over to the new address, then aggregated up.

None of this is interesting.
Yes, I am.

I'm pretty tired of talking about it. Tired of being taken of context, tired of being exaggerated, etc.

My current position is that I don't know. MTGox has— as typical— manged to be incredibly quiet and to behave in generally concerning ways. From a technical perspective it seems that nearly anything is possible.

I think that as a community we should start demanding these services continually prove that they are not fractional reserve. We cannot effectively eliminate the need for trust in these sorts of services, but we can certainly confine the exposure and eliminate a lot of this drama. With Bitcoin it's technically possible to prove an entity controls enough coin to cover its obligations— and even to do so in ways that don't leak other business information, and so we should.  But this isn't something specific about MTGox, it's something we should demand from all services holding large amounts of third party Bitcoins.  I wouldn't even suggest MTGox should do it first, rather— it sounds like a great move for their competition to differentiate themselves.

We got the name Ptalarbvorm [from Reddit](http://www.reddit.com/r/programming/comments/8834f/xiph_theora_11_alpha_thusnelda_released_major/c08ihxj). 
yea, there is a bunch of insulting, sloppy, and unprofessional stuff in there. E.g. hey were also copying improvements from the Bitcoin project and [changing the attribution information](http://archive.is/ni68v#selection-9.71-9.132) to try to insult me.
Welcome to Reddit thrwawhy!

Yes, strongly appears to be the same Mike Hearn. You can also see the use of the qinetiq email address in his [commits/posts](http://marc.info/?a=103519148400001&r=1&w=4) to wine from 2002-2005. You can see Mike's work on wine mentioned on his current CVish info on [his website](http://plan99.net/~mike/).

> Is he still under gov/defence employment?

It's more or less impossible to tell, it's not like a state operative is going to helpfully and honestly self-identify.

But if so, he'd be the least well hidden operative _ever_.

> Is this why he always pushes for tracking?

Ultimately we must judge words and actions, contributions and their merits. In Mike's case, in my view, after these things are considered there is no reason left to worry about if he's still on Qinetiq/GCHQ's payroll or not.

There are more important things to worry about. As I've said before-- it wouldn't even matter if Bitcoin were originally created by some spooky intelligence org: We must always judge Bitcoin based on what it actually does, as even someone with the best intentions can make errors. So it _certainly_ doesn't matter if Mike Hearn is
serving some other interest.  In systems defined by their rules we judge things on what they actually do-- not what they're intended to do, what someone /says/ they do, or on the merits of those who propose them.

Ultimately, we _must_ be robust against that sort of thing-- even if you did manage to catch one poorly disguised operative you're not going to catch all that the worldwide intelligence community could throw as us if they wanted. And, of course, it would be trivial to fake evidence that any person had "connections".

I believe the Bitcoin Community is already structurally robust against that sort of stuff. We should continue on with the business of building more and more protocols and tools which are inherently resistant to subterfuge and leave the gossip for the tabloids.

Part of being robust is not feeding rumor or drama, giving into to personal or emotional attacks, taking attempts to discredit with a big grain of salt, etc.

it's used by default.
> Garzik can't stop crying about how unethically Core developers are behaving,

He keeps doing so with a bunch of untruthful claims, fake quotes etc. -- [For example](https://twitter.com/jgarzik/status/914978174550847493).  The "quote" turns up nowhere, and I think I've been pretty outspoken that that isn't my view.

Of course, he ignores all private messages pointing out the inaccuracy of his claims or asking him to back them up or remove them.
This is an absurd change.  It is minimally useful for users, and mostly looks like a pretext "yes we added replay protection" which doesn't really protect, and bloats up Bitcoin as a side effect.

It also adds an alarming coin blacklist to s2x. Visions of things to come in that coin?

Instead they could have made a ~2 line change to allow an extra ignored bit to be set in the sighash flags, or a simple additional serialization so that you could make S2X only transactions.  They're making a hardfork in any case, so it would be trivial to allow a new transaction style that Bitcoin doesn't accept.

Since use of it would remain opt-in it also would continue to not be real replay protection and would not do much to protect most users from losses-- but it would be strictly better than the ugly hack they implemented instead,  and wouldn't burden Bitcoin's chain and UTXO set with additional unnecessary data... and wouldn't have the technical debt or ugly precedent of a consensus address blacklist.

Whats interesting is that Bitcoin ABC (bcash) had basically the above in their first version before they implemented replay protection.  They had the technically clue to get that right and the integrity to not falsely describe a one sided only by request thing as replay protection.
So... the price of a single F18?

As someone who has spent years contemplating and preparing for the potential of state level attackers going after Bitcoin ... threats like these seem pretty anti-climactic by comparison.

The only think I'm left wondering is that with openly announced intent to criminally attack user's network to attempt to deny people their own free choice... how are places like rbtc still operating under the delusion that they're not the bad guys?
I've been telling them to go and create their fork for over a year now.

The fact of the matter is that for a least a few of the vocal people involved do not actually want a fork and don't really believe that users want it either.  They just want to disrupt Bitcoin, create FUD, and slow technical progress while then invest in competing systems.
If people are running up the mtgox price because of the couple transactions in the TX list— activity which has gone on during the entire outage...

Uh...

Well, markets be crazy, yo.
Scaremongering, I guess.   The idea here is that:

(1a) Get him to submit. Then refusing doesn't work. TSA wins.
or
(1b) Get him to submit, he has some kind of contraband (e.g. drugs). TSA wins.
or 
(2) Get him to freak out.  TSA justified for harassing the crazy person. search him anyways. TSA wins.

Sounds like of all the possible outcomes he found the only one where he wins. Good show.



Craig, thanks for stepping out and making it completely clear that you are continuing to claim to be the creator of Bitcoin.  Some people in this subreddit were doubting that you were still making that claim and I think it's good to have it out in the open since this allows others to debunk it and protect the public from being defrauded by you.

>  the Australian Tax Office

If anyone from the ATO is reading this: I'd be willing to travel to AU on my own dime* and offer expert testimony against this fraud. His claims to be the creator of Bitcoin are utterly and obviously fabricated, and I can suggest dozens of the worlds foremost subject matter experts who will happily testify to the same effect.

As an aside: I _strongly_ recommend you contact the investigators and trustee in the MTGOX insolvency.  I am quite confident that you'll discover the high value Bitcoin addresses which Wright was claiming were his property-- assets underlying loans and transfers-- his filings and prior testimony to you are in fact those of the MTGOX bitcoin exchange and were never his property at all.  If you need an introduction let me know.

*Although I assume you have some more convenient mechanism to take a deposition from someone in the US.
Someone came into the channel and asked why he was there. Everyone elses response was surprise, so Wladimir went and cleaned up the list. There were no objections especially in light of his continued reckless and dishonest behavior.

This is also over just a list of people that can assigned be assigned issues, not any special kind of access other than that. Very strange to see that he is throwing a fuss over it!  ... though it does confirm some reports I've heard about him misrepresenting his involvement in the project. 

For some weird reason (perhaps because he was still on that list) a number of people have thought that he was still active in the project,  pulling a chunk out of a report on the Bitcoin repo that I was looking at earlier (numbers are probably slightly approximate due to name change snafus), you can see that he hasn't been active for about three years (with some other people for comparison):

     /-----------------------------------------------------------------------------
     |             Non-merge commits in Bitcoin project git
     |         2010  2011  2012  2013  2014  2015  2016  2017   All | Active months
     |-----------------------------------------------------------------------------
     |Wladimir    1   349   159   115   384   193   197    93  1491 |            73
     |Pieter      0    51   227   111   170    89   156   112   916 |            75
     |Matt        0   101    71    38    25    54   100   143   532 |            66
     |Gavin      17   152   139   112    47    17     1     2   487 |            61
     |Garzik      0    38   106    35    48     7     0     0   234 |            36
     |Hearn       0     0     1     7     2     1     0     0    11 |             8
     ...

My understanding is that for the last couple years Mr. Garzik has mostly been involved in Ethereum and in some proprietary wallet projects (like a AML/KYC government cosigning wallet).

People were told, by Garzik, about the segwit2x mailing list:

"As a matter of policy, to join the list, you need to agree to research/build/test/review/deploy according to the NYC agreement's public, two-point charter (segwit + 2M HF in < 6 months)."

So perhaps it's simply a case of there being nothing good to say about it, and no one who will speak negatively was permitted on the list. Presumably there is *something* going on, off in private venues to further shield themselves from transparency and review.

Meanwhile, they just started attempting to write a spec for it two days ago, and their initial draft doesn't actually describe what [they've implemented](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-July/014712.html) nor does it [describe something that would actually increase the blocksize](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-July/014709.html).

At least Sergio had the sanity to bring something to a venue where it could possibly get a bit feedback, but the [backwardsness](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-June/014661.html) of the whole thing remains, along with the fact that they are committed to not actually taking feedback on the design or operating under a schedule that would even allow them to.
> Can we see if this PoW vulnerability has been used by examining a block?

No.  There are two ways of exploiting the vulnerability, which I call the overt and the cover method.

The overt method is trivial to detect, trivial to block, and not currently in use. (It would result in blocks having a few random bits in their version field.)  The overt method is what most people understand ASICBOOST to be-- which is part of the reason people hadn't been worrying about it.

The covert method is hard to detect and cannot be detected on a block by block basis.  It can show up as an increased number of empty blocks, strange ordering of transactions in blocks, or never-seen-before transactions showing up in blocks. All of these things can happen naturally without making use of the attack.

The proposal interferes with the covert method by eliminating a sqrt speedup in the algorithm for blocks that contain transactions. Importantly, with this proposal in place implementation of protocol enhancements (like segwit) wouldn't hurt covert boosting any further-- so there would be no more conflict of interest between the enhancements and ASICBOOST.

I offered it in part because other people who know about this concern have been wanting to take more extreme measures (like changing POW or blocking ASICBOOST entirely) which I worry would add more drama than a targeted move.
Yea, the defamation in this case is over the top.

There is an article outright saying that the attacks were by "Bitcoin Core" -- they weren't, and other absurd shit. It's awful.
Lets try this on a few other things:

"Sexual intercourse without government appeals to people without law."

"Gatherings without government appeals to people without law."

"Education without government appeals to people without law."

"Church without government appeals to people without law."

... Hm. Is there isn't any form of this sentence which fails to sound outright Orwellian?
Redditor for for 5 years.  And, besides, I don't have any cats, how else am I to get karma?
Gemini also uses uncompressed public keys, making their txn some 15% larger than even most non-segwit.
In nature many species use [bright colors](https://people.xiph.org/~greg/temp/b2xlogo.png) to signify that they are dangerous.
Unfortunately, there just isn't enough time the release process takes weeks. 

Though I'm looking at getting some patches up or something.
Or all on systems infected with malware that compromised their key generation.

Doesn't seem that much like a bugdoor or malware though-- if it were you'd expect it to be nearly undetectable (e.g. making one of the factors derived from the hash of the username on the key or what not)... so probably a bug.  But in what software? 
A bit longer than a month:

    --- Day changed Mon Sep 02 2013
    05:25 < gmaxwell> MagicalTux: hey fool, https://blockchain.info/tx/591b0102531b69879ef6474a5ec64cdc96bf775e0b15e0ee91ee136bba01253b
    05:25 < Title> [ Bitcoin Transaction 591b0102531b69879ef6474a5ec64cdc96bf775e0b15e0ee91ee136bba01253b ]
    05:25 < gmaxwell> MagicalTux: you are sending immature coins!
    05:25 < gmaxwell> MagicalTux: this may be the reason people are complaining to you about transactions not forwarding.
    05:26 < gmaxwell> MagicalTux: newly generated coins need to mature for at least 100 blocks before the network will relay any spends of them.
    05:26 < gmaxwell> at the moment this transaction a customer of yours is complaining to me about spends an input from height 255610 and it is now 255702
    05:27 < gmaxwell> fixy fixy

(for just one example)
Regretfully I have to agree.

I have seen far too much outright dishonest and extremely incompetent behaviour from Samourai to ever trust it myself.

Samourai has very poor privacy while bragging about having good privacy: It sends your addresses all back to their server. Their "trusted node" mode doesn't change this, it just exposes your Bitcoin node to attacks completely needlessly. (Needlessly because they only use it to relay transactions, which they could do via the public P2P ports of any node, and because since they've already sent your addresses back to their server sending your transactions via something else doesn't increase your privacy.)

If you want strong privacy then as a start you need to run a full node  (and use either Bitcoin Core wallet, or run your own electrum server), preferably via tor.

I don't doubt that they want and intend to eventually have good privacy but it seems that they're just unable to stop lying about it, even after previously being caught.
Craig Wright claims to know how to perform an operation called pairing with Bitcoin public keys.   Pairing can be used to construct some pretty useful cryptographic protocols, it can also be used -- in some cases-- to compromise the security of cryptosystems. [This](https://courses.csail.mit.edu/6.897/spring04/L25.pdf) has a pretty good description of pairing and its applications.

There is a normal mathematical criteria for what curves will work with pairing and what won't and secp256k1 doesn't meet it; so Wright's claim isn't really plausible on its face, but rather than waste time debating it I posed a simple challenge which he should be able to easily answer if his claim is true.

Answering it would not reveal anything other than the fact that he can perform these operations-- something which he has already claimed in public. 

Yep.  Slush is running Bitcoin Core 0.13.1 which has ancestor feerate mining. So a higher fee spend of the output of a low fee transactions can prioritize both. (This applies to Bitcoin Core 0.13+)

Beyond your thanks, Slush's pool earns more income by correctly prioritizing transactions with high fee children compared to pools like Bitcoin.com and viabtc which use outdated and less efficient logic.

Imagine Wikipedia as a sandwich shop.  Most of their customers buy the PB&J, so they're geared up to produce millions of those, they're fast easy, they have special assembly lines just for doing PB&Js, and they keep a cache of pre-made ones to cope with rushes.

But then some Wikimedia employee went on reddit and told everyone how great the 16-ft-long super-sub was... and the rush of people ordering that flooded the restaurant creating long lines and even preventing people who wanted the PB&J from getting theirs.

Thats the case here— the super majority of Wikipedia requests are just readers hitting articles (something like 99%). These are served out of ram/ssd from ultra-fast front end cache computers.  Requests which are more complicated (like editing, history, statistics, etc) go to the backend which has less capacity.  The particular page that got linked to in the ask Reddit thread was especially slow to generate.

The extensive use of caching and careful control of the complexity of the requests is how all high traffic sites are able to stay up without bleeding money.  Wikimedia is especially good at doing so (though because most people are reading the problem is proportionally somewhat easier for them than sites like reddit, though they get a LOT more traffic overall), so when some accidentally high complexity request does get a lot of traffic things go pear-shaped fast.  _Usually_ this just tends to knock out things for editing/history (other backend hitting stuff) and if you log out it still works fine, but sometimes its bad enough that the backend delays clog up the frontends too.
The whole idea of "filled" is wrong headed,  if there is an attack here the only attack is causing hysteria due to people's confusion about how the mempool works and the graphing sites that aid that confusion.

Perhaps it intends to be more: not all of our attackers are VERy smart. But we should not concern ourselves much with what attackers were trying to do, only what they actually do... and a bunch of low feerate txn don't do much in reality.

If you're paying 10.1s/byte then a transaction at 10 or below simply doesn't exist as far as your transactions are concerned. 

The whole design of the system is for there always to be some low feerate backlog so that blocks never run dry and miners don't run out of fee income, resulting in reduced network stability.  The mempool doesn't "clog" or get stuck, or anything like that.

Yes, people can build a lot of cheap fee backlog-- this works in your favor: you use it to get low fees aggregating transactions up when the demand is low and/or when the network gets lucky.  And the rest of the time it doesn't harm you: pay more than it and it doesn't exist as far as you're concerned.

For context: When people complained to Bitmain about their empty blocks they argued that it was a freedom the protocol provided to them.

Later, they defended claims that they were using asicboost by saying they only used it on testnet and would not use it on mainnet for the good of the network.

These remarks sound a little bit inconsistent. :)  Though considering that they also produce a lot of empty blocks on litecoin, it could just be poor operations.
With all the excitement about that stuff... I was wondering how long it would take people to notice and got a little impatient.
The IETF has an elegant process that recognizes that no bureaucracy can make a standard.  True standards are made standard by diverse and widespread adoption, not some stack of paper and a wax seal.

Because of that all standards track RFCs are called "PROPOSED STANDARD" when they're published.  If and only if they achieve true status as mature defacto standards with substantial operational expirence do they get promoted to "FULL STANDARD".  Of the thousands of published RFCs (though not all standards track), only [68](http://www.apps.ietf.org/rfc/stdlist.html) have made it to that level of maturity (and some of those were widely deployed long before the RFC was written).  You may note the list doesn't include HTTP or many other RFC specified protocols that are very widely used.

Their hardfork has no risk of reorg-- it's a hardfork, just like there is no risk of litecoin reorging onto Bitcoin's chain. The selfish mining serves no purpose except locking in major profits for Bitmain in the unlikely case that people are foolish enough to go along with it.
It's effectively only needed for listening when (1) you don't have a routable public IP, (2) you haven't manually told your node what IP it should use,  (3) one wasn't provided via UPNP.

Bitcoin works fine even if that fails completely, though some small fraction of the listeners would be unable to announce themselves.

That said, I think it's a lame approach which is why I wrote the original pull to get rid of it (https://github.com/bitcoin/bitcoin/pull/3088) being linked to in one of the other posts here, and it went fallow due to lack of interest in testing it / finishing it (I'd posted it only as a "lets discuss this approach"... I've been too busy to pick it up myself and fix it, it certantly wasn't "declined".

How did a nasty person like that have access to your cat?
Well why didn't you comment on the specification proposing this permanent consensus rule change???

... Oh. Because they didn't bother writing a spec or having any peer review.
> much of the development would switch over from the current core chain to that

This is absolutely not true-- I don't know how we could be more emphatic about that then we've already been.

If Bitcoin is subject to backroom deal takeovers then it has failed and we wouldn't waste our time improving and protecting a failed system.

I don't expect that to happen, of course, because most the user community isn't being tricked... Bitcoin just isn't that fragile.
If it was oh so easy, why didn't you do it?

     $ cat script.py
     import decimal
     import bitcoin.rpc
     import bitcoin.core
     COIN = decimal.Decimal(10**8)
     proxy=bitcoin.rpc.Proxy('http://user:password@localhost:8332/')
     mp=proxy.getrawmempool(True)
     txlist=[]
     total = decimal.Decimal(0)
     for item in mp.keys():
       if mp[item]['ancestorcount'] == 1:
         txlist.append(item)
         total += mp[item]['fee']*COIN
     print total/COIN
     for item in txlist:
       tx = proxy.getrawtransaction(bitcoin.core.lx(item),verbose=True)
       for j in tx['tx'].vout:
         total += j.nValue
     print total/COIN
     $ python ./script.py 
     71.78762730
     72538.86537653

So I see about about 1/10th the headline claimed amount-- this is a stock configuration 0.14.1 node with some weeks of uptime.
Sure, 

Classic's change:

* Doesn't solve that signature hashing can take time quadratic in the size of the transaction, just implements another opaque limit to bounce against (segwit makes the cost linear).

* Doesn't do anything to prevent doubling the blocksize/capacity from also doubling UTXO load (segwit does: both by rebalancing costs from signatures to output creation, and by not increasing the worse case utxo impact)

* Doesn't do anything to prevent slowing down/loading up SPV clients (segwit does, witness data wouldn't be fetched by them)

* Doesn't do anything to reduce the impact on full node sync-up time/bandwidth (segwit does, pruned full nodes can skip downloading old witness data they wouldn't check). 

* Doesn't provide significantly more capacity than segwit (and, in fact, may provide less if multisig usage continued to increase).  Segwit was proposed and widely supported before classic was announced, and has many other benefits other than the scalability features mentioned here.  The composition of 2MB base + segwit would be right at the boundary of than the "seemingly safe" blocksize upper bound figures measured by one of Classic's own developers.

* Activated via a 75% rolling miner signaling, which has non-trivial odds of being falsely activated with less than a majority of true hashpower.

* Uses hashpower to trigger, but hardforks inherently define what work constitutes mining using mining to decide mixes up the system's incentives and (at least as many classic developers and proponents advance their case) inappropriately places miners "in charge" of the system, rather than being a valuable service provider trusted with the narrow task of ordering transactions. (As I noted, if Core used classic's methodology for CSV, classic would be totally busted now).

* AFAICT, not very extensive tested (e.g. in spite of requests, doesn't have its own public test network... but segwit does), relatively few people reviewing it. Nor is it supported by large and active technical community-- no surprise considering the points of comparison already listed. If there were further issues with the implementation instead of just the design problems listed here, I wouldn't know.

In short, The change in "classic" removes a load safety limit without increasing scalablity. It's offered as an alternative to an earlier and (I believe) more widely supported proposal which actually improves scalablity and doesn't look good in comparison.

No change to the system is simple. To think otherwise demands ignoring the complex system effects, economic incentives, social implications, and long term impacts of the action.
This stuff isn't easy and MTGox is dealing with a mountain of legal, regulatory, and business issues that I couldn't even begin to imagine. Plus, on the technical side— DOS attacks, trading engine glitches, etc.

There isn't a large commercial operation which I couldn't find some aspect of their Bitcoin behavior to nitpick.  This doesn't mean that they're amateurs. Doing everything the best way is hard.

But MTGox does seem to be pretty poor about fixing Bitcoin related technical issues.
See also my post on [bitcoin-dev](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-November/015283.html) for some history and explanation of the implications. 

The big news part of it is:

> The exciting recent update is that Benedikt Bünz at Standford was able to apply and optimize the inner product argument of Jonathan Bootle to achieve an aggregate range proof for CT with size 64 * (log2(bits * num_outputs)) + 288, which is ~736 bytes for the 64-bit 2-output case.

This is a ~5x improvement in the 2-output case, but in the presence of coinjoins bullet proofs compacts the range-proof size to asymptotically nothing.

This work will likely eliminate bandwidth as a limiting factor in using CT techniques, though verification CPU time remains a consideration.
Even that is  N-fold counting chains of transactions that spend the same coin over and over again.

E.g. take 300 bitcoin, and spend it 25 times paying out a bitcent each time and ka-zam $14 million dollars.
::sigh::

So they admit to having it in their hardware, expected-- this is impossible to deny, and people on Reddit were already uncovering it for themselves.  They insist they have a right to use it. The admit to using it on testnet.  They deny using it on mainnet "for the good of Bitcoin"-- but where was the "good of bitcoin" when they insisted that they would keep making empty blocks because the protocol allows it?

I expected the denial, the unfortunate thing about covert boosting is that its very difficult to prove its actual except by what it blocks. The proposal I made was specifically constructed with it in mind.  If they aren't using the covert boost then they should vigorously support the proposal because it would prevent others from using it and gaining an advantage over them with it (while disrupting protocol improvements).

What I didn't expect was the sheer level of adhominen and personal attack, and the attacks against the Bitcoin project. It makes it hard to respond to, because obviously I reject and refute those points-- but they're also a distraction from points that actually matter: like how they've completely mischaracterized the proposal. 
This kind of idea (two works functions, blocks must meet difficulty under both of them, and a gradual shift on how much of the second is required, while the difficulty on the first falls away...) has also been proposed previously for deploying a block withholding attack fix-- which also requires a bit of a POW change.

You can think of this idea as something of a "melted hardfork"-- the end result is still a POW hardfork, but there is no fixed point that you can point to where an abrupt change change actually happened.
The 52% complete stuff is mostly gibberish.  It's just a simple count of pull requests which are open and tagged as 0.13.1 vs all (including closed) tagged as 0.13.1. Many of the completed things were completed before 0.13 was released, and were waiting for the branching to get merged.

The remaining "48%" is mostly also done, just not yet reviewed and merged yet. But there are also things to do for 0.13.1 which haven't had PRs opened for them yet.

There isn't too much to do for 0.13.1-- I wouldn't be surprised release time will be substantially gated by feedback from 0.13's deployment (meaning that if you want 0.13.1 out, start using 0.13.0 and report any issues). ... but a watched pot never boils.

I added an issue for that.
I am him.

So, I've asked myself about it. Apparently, I have no idea what you're talking about, and I told me to tell you that my following along on some of the altcoins which are doing even a single technically interesting thing doesn't mean anything more than it is.
The lack of safety means that you take on different costs related to loss of reliability or privacy or the difficult additional testing required to assure bugs/vulnerabilities don't exist.

For rust you take on costs related to a smaller developer base and language immaturity, but more or less eliminate several large classes of problem at little to no additional cost.
> What we certainly know:
> he owned >200,000 coins in 2013.

How do we now this?  Nowhere in the liquidation report does it fix a value basis for the "bitcoins". It basically says that Wright provided "$30 million" in bitcoins, then used the bitcoins to buy software.  As far as I can tell, they might have been valuing the bitcoins at $1m each to distort the level of ownership relative to other investors; or even claimed bitcoins were involved when they weren't ("sorry, bitcoins leave no trace!").

Incidentally;  there is now more evidence that it's faked. The PGP key being used was clearly backdated: its metadata contains cipher-suites which were not widely used until later software.

$ gpg --export 5EB7CB21 | gpg --list-packets -  | grep pref-hash

        hashed subpkt 21 len 5 (pref-hash-algos: 8 2 9 10 11)

Compare to the well known key:

$ gpg --export 5EC948A1 | gpg --list-packets -  | grep pref-hash                                                    

        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)

The 8,2,9,10,11 list was added to the GNUPG code tree in commit e50cac1d848d332c4dbf49d5f705d3cbbf074ba1 on July 9th, 2009, and not released until version 2.0.13 later. This is well after the 2008 date on the key. The 2,8,3 list was the prior list the would have been used in 2008.  That they were different at all was surprising, considering that they claim to be generated less than a day apart.

This key was also not on the keyservers in 2011 according to my logs; which doesn't prove it was backdated, but there is basically no evidence that it wasn't and non-trivial evidence that it was. Someone could check a public keyserver dump to see if it was even on the key servers earlier this year: https://research.daylightpirates.org/sks-dumps/2015-07-09/
Xabbix, When you publish and promote pre-release documents like this it means that lots of the Reddit using public will probably never read the final complete and peer reviewed version.  (And, sadly, right now these release notes mis the big headline feature).

We've had reoccurring problems with people running to Reddit with pre-release download links before the software was finished uploading, or before we've been able to determine that the binaries were deterministic (e.g. not backdoored by malware local to a single developer); publishing pre-release release notes. etc.

The cost to this is that it creates more work for the developers and pressure to make the development process less open and transparent.  I hope in the future that you'll curb your enthusiasm a little.
One of my suggestions for the satellite project was that Blockstream should sell branded tin foil hats for those who didn't want the signals passing through their heads...
> The proposed thesis in your claim is that

What.

No, you simply set the time on your computer to 2008 and made a fake key with current software. We already know the key is fake, so the only open question is how little work you put into faking it.

You're asking us to believe instead the key was original, with no explanation as to why it's the wrong key, no explanation as to why anyone would have any reason to believe it was also correct, no explanation as to why the key didn't match the software defaults when the well known did except to say that it was edited later,  which can't explain why it's an RSA key (what current code defaults to) instead of a DSA key (which the 2008 code defaulted to and editing cannot change), and -- as that point points out-- that wouldn't be the case because the edits store their dates.   So instead you now ask us to believe that it was originally created in 2008 but then later edited to look like a more modern key but with the system time fraudulently set back to 2008.

In other words, you're effectively now arguing that they key was backdated.  No shit.

[parts of the text here are copied from the other dozen times Wright has tried this particular con]

The whole discussion is phenomenally dumb.  Satoshi has a [well known public key](https://web.archive.org/web/20110228054007/http://www.bitcoin.org:80/Satoshi_Nakamoto.asc).  The key craig presented is not that key and not signed by that key.

Anyone can create a PGP key with any name and date they want on it.  Craig's key key is not the right key: there is no relation the key from Craig and Satoshi beyond it having Satoshi's name written on it, which anyone could do.  This alone is dispositive, the discussion should have ended there.

Some people online (not me, in fact, in spite the claims in this article); noticed that not only was it not the right key-- it was a low quality forgery: the key's metadata is perfectly consistent with a key authored with current software, and entirely inconsistent with a key authored at the time it claimed to be authored.

The page repeats the prior claim that the key could have been edited when it was created, to override the settings and match a future key. It gives an example doing that with a few of the fields (but not all of them). This is a fact I *specifically* pointed out on reddit when commenting on the subject:   https://www.reddit.com/r/Bitcoin/comments/3w027x/dr_craig_steven_wright_alleged_satoshi_by_wired/cxsm1yo/?context=3  ("It's possible that the settings could have been locally overridden to coincidentally the same defaults as now.")

It is an absurd leap and it fails Occam's razor.

We're being asked to believe: That Satoshi generated two PGP public keys on the same day, one of which he used and published. The first (published) one created like normal, the other created overriding every default then further manually editing the key to adjust the setting to a highly complex string of values which, by some phenomenal chance, happen to exactly reflect the unmodified default settings that were established in GPG later.  This other key he kept secret and only revealed via Wrights "leaked" documents long after these new settings behave the default.

Or perhaps the 'other key' is just very low-quality contemporary forgery. ... after all, it's already definitively proven to be a forgery because the keys don't match!

All this wanking about metadata is irrelevant because it's simply not the correct key. That is the one and only definitive test and it fails it. All the rest of the discussion (that the metadata is all wrong, that the key was never in any public archive before wright put it out, etc.) is simply making fun of how technically inept the scammer is that he didn't even get any element of his forgery right...  I think it's amusing and fantastic that Wright continues to pay people to bolster his fraudulent claims.

Basically, Wright appears to be trying to exploit a cognitive bias. People have pointed out fact A: The key is obviously not satoshi's key, because the key fingerprints do not match and anyone can create a key with whatever name and date they want; and fact B: his metadata forgery is laughably bad.  Wright keeps paying people write pieces claiming that it would have been possible to create a key with that metadata if back at that time, a very unlikely sequence of events is followed (pointless manual key editing that just happened to guess the future values).  Then he expects that because he can show doubt that B alone isn't completely dispositive, which it was never claimed to be, that you should ignore the fact that A already proved the case.

401k loans get called if you quit or lose your job...
Ship great software; protect Bitcoin's integrity; ignore drama and coercion.
Most of the smart contract things people talk about make no sense.

This doesn't mean that smart contracts aren't cool.  But probably 95% of what people talk about is pure hype.

We're a long way from practical use of them for meaningful applications (outside of simple uses like coinswaps and lightning), but some [interesting things](https://bitcoincore.org/en/2016/02/26/zero-knowledge-contingent-payments-announcement/) are possible!
More usable than wallets without lightning are today.

You probably don't understand everything in detail that happens w/ lightning, users of lightning enabled wallets won't need to know what its doing under the hood either.

Probably the worst thing about all this bitcoin drama is that it takes this highly technical stuff and crams it down the throat of people who just aren't interested. Not everyone is and thats fine. No one is expecting you to be.

The result is that a lot of stuff which is hardly complicated at all ends up with people running around saying that it's oh so complicated-- simply because it one of only a few things they've been exposed to in any detail at all.
Normalize the efficiency by blocking the attack; and only the covert form of asicboost that potentially gets in the way of protocol improvements.

The proposal tries to avoid taking a position on ASICBOOST being blocked in general in favor of taking what I hope is a more universally held view that we shouldn't let vulnerability exploitation disrupt protocol improvement.

A good chunk of it is up to the sidechain, so I don't think there is a single _crisp_ answer.

I'm going to be terribly unfair here: I say unfair because it may look like I'm giving you a wall of complex, ill defined arguments, and suggesting you need to defeat each one. The fact of the matter is that there cannot be a single security story for all sidechains and thinking in this space is still developing. So I'm going to summarize the tools I've been thinking about to boost sidechain security beyond SPV levels.  Some of them may be bad, and I'm not suggesting you should need to defeat all of them— I'm basically listing them to convince you that there is a lot of space here to build security better than "not being dicks", though perhaps not ideal for all applications.

But before I do, I want to suggest two thoughts:

The first— How sure are we that in the long run Bitcoin security is any better? We know that today vast majority of miners (people with hashing hardware) do not validate anything, we know that the vast majority of merchants do not validate anything, we know that the majority of end users do not validate anything. Yet validation is stupidly cheap today, and so what happens if its necessary to uncap the blocksizes and validation becomes expensive? Validation has basically perfect centralization gains (validate once instead of N times). What happens as the subsidy fades to nothing?

The second thought— What security is actually required?  There are many applications for Bitcoin, they don't all have the same security requirements.  There are people doing low value unconfirmed transactions today. They already can be pretty easily ripped off with the help of miners— can't some of these transactions move to a solution with reduced security even if no methods are deployed to increase security?

One of my goals for sidechains is that they may act as a pressure relief value so that floods of very low value (and low security requirement) transactions do not necessarily force all of Bitcoin security to the low level justified by these transactions.

Now on to increasing sidechain security beyond the SPV-ish level that you get from the basic sidechain idea, for each one I'll list the tool and what I consider to be it's main weakness:

(0) If a sidechain is universally used, then miners could enforce its correctness as a soft-forking change to Bitcoin (e.g. reject blocks that contain an invalid sidechain proof), and validators could adopt it too. At that point the security story is the same as Bitcoin's.  This is weak in that it removes the loose coupling and freedom for full nodes to not validate a sidechain they don't care about.  But it seems strictly better than just making the soft forking change in Bitcoin directly, since you're not forced to do so before anyone is even using it yet.

(1) My [CoinWittness](https://bitcointalk.org/index.php?topic=277389.0) post describes using ZK-SNARKS to construct compact proofs of correctness that could be used to convince people a sidechain spend is valid. This is weak in that it really cutting edge cryptography and the prover part takes a lot of resources but the verifier is trivial and if its used its purely additive in security.

(1a) It's possible to efficient proofs of valid execution in an interactive challenger model [without invoking any novel crypto at all](http://www.cs.tau.ac.il/~benriva/ccs11.pdf). Basically the sidechain proof generator encodes a transcript of his verification process and commits to it. Then if his verification was wrong, a challenger shows up with a different commitment, and there is a log() communications process to decide which of the competing claims are valid.  Because this is interactive, however, this is weak because if >50% of the _Bitcoin_ hashpower blocks it they can hide the fraud proofs. It's also weak in that while the fraud proofs are log2() they can get somewhat large and the sidechain must implement its validation in a transcript producing model.

(2) The definition of the sidechain can specify a federation of parties that must threshold sign blocks (including, perhaps, updates to the federation list).  This is weak because it creates a (distributed) point of centralization around the federation.

(2a) The definition of the sidechain can require that the spends are signed by systems running the verification code on remote-attest hardware (like arm trustzone).  Then anyone can spin up a verifier and the security is tractable to the integrity of the remote attest and intel/amd/etc keys.  This is weak in that no one has great confidence in the strength of remote attest :). Though if it can be combined with (2) for purely additive security.

(3) Proof of Stake-ish federation: Have utxo in the sidechain provide public keys which are used to build threshold signatures that must sign the proofs. This is nice in that in the few-users case it degrades to perfect security, but at large sizes it may be hard to make both efficient and reliable.

(4) Econonmic incentives for honest mining can be created by techniques like bonding the creation or having some fee deferral tokens. Adam has thought more about ideas in this space than me. I generally think the economic incentive approaches are tricky to reason about.

I don't think this list is exhaustive, but it's some things I've been thinking about in terms of trying to making the design on the bitcoin side generic enough that sidechains are free to innovate in their security story too.   Sidechains could implement zero or more of these approaches and potentially even change them through there life— e.g. boostraping in a federated-signing model but with a pre-programmed transition to more decenteralized security if/once that sidechain has grown enough.
Nope.
Ver believes him and directly told me this, I'm not speculating.

Why do you think Ver is spending hundreds of thousands of dollars a month promoting BU and attacking the Bitcoin project?   Because he's "backed by god" and the drama is good enough for his altcoin invested pocket book that he's not going to think too carefully about it.
That quote is also just wrong. My proposal does no such thing. It blocks only the covert protocol-upgrade incompatible form.

The community might rightfully decide to block boosting more completely eventually, but this is a separate consideration. I made a concerted effort to design a proposal which would interfere only with the covert form that blocks protocol enhancements (like UTXO commitments, fraud proofs, segwit, etc.).

Unfortunately, this article contains many outright untruths.

This it too long for anyone to address point by point-- probably will take people days of effort to do so--, but some random examples: 

> Most seriously, there are no enforceable constraints to the growth of the non-SW UTXO.

Sure there is, the amount of non-witness data in a block ends up with the same limit it always had.  One of the reasons we were able to convince people worried about a blocksize increase that segwit's capacity increase was safe was because it did not increase the UTXO bloat exposure.

> This makes future soft or hard forks to Bitcoin more difficult as multiple classes of UTXOs

There aren't multiple classes of UTXO, they are all equal, differing only in their scriptPubkey, just like 2 of 3 multsig is different from 5 of 7. And just like a transaction can combine multisig and non-multisig using scripts they can also combine segwit and non-segwit using.

> However, the signatures are more expensive to validate than the UTXO,

That isn't the case-- signature validation is one of the faster parts of block validation since our work on libsecp256k1, and unlike the rest it is perfectly parallelizable. On my desktop signature verification is more than 50 times faster than the rest of the process.  It is also perfectly prunable so it doesn't result in a long time storage cost for full nodes.

The page's pseudonymous author also behaves quite dishonestly, with claims like:

> Greg Maxwell has postulated that “abandoned UTXO should be forgotten and become unspendable,

which is referring to a page which _explicitly_ says otherwise: "(some of this) represent(s) different security and economic tradeoffs and I don't think those could be ethically imposed on Bitcoin even if a simple majority of users wanted them (as they'd be forced onto the people who don't want them)". I've been highly outspoken that that kind of thing isn't an option for Bitcoin; although it is _technically_ interesting because it would address some long term issues. In Bitcoin we need to use different approaches to control UTXO bloat, such as those which are part of segwit.

Steal?   P2P? 

He downloaded a large number of academic papers— the writers and reviewers collect no revenue from them, and the work they document was usually funded by the public.  He didn't even do anything with them— just downloaded them.

The only stealing obvious in this system is the stealing by the journal publishers perpetrated on the public.  It's good to hear that people are choosing to stop allowing their works to prop up this terrible system.

SHA-2556 for ultra-security.
Transaction flow monitoring spys.  Amazon seems to be unwilling to take any action... Though they're wasting a ton of resources. They're not that harmful, other than to everyone's privacy.

I put up some ban lists for fake nodes which are connecting to nearly everyone last week:

[in CLI friendly form](http://0bin.net/paste/V0GAccklhV+huNVC#8uKrkZB0NYEHakf+GEf6Bz7995wvwjpYiYddPzAU71e)  or in [in bitcoin-qt debug console (ready to paste) friendly form](http://0bin.net/paste/6t296tLNIUrLAGI4#yQCgdTXPtsfA1WqKj5Y0qXO1JE0OVfmfmgo55EwSd6n)

These will ban them for six months.
Pretty much conclusive proof that all of this is comedy.

... as if the wired article itself weren't enough; after citing all these old blog posts as evidence, only 3/4 of the way down the article to they admit that the "evidence" was all added in modifications of the articles in 2013.

I especially like the emails from the unfriendly AI. Got a good laugh out of that.
I'm over 35. Though I often get the impression that I am well above the median age in Bitcoin land.

TRS-80, C64, IBM PC... (and the 5 1/4" 10MB hdd that later became 20MB with a RLL controller).  I ran a BBS and was cosysop on quite a few others. X.25/tymnet and other people's vaxen were how I first used the Internet.  I have terminal logs reading about the EFF right when it was founded and used IRC when channels were just numbers (and even without channel names it was still better than twitter is today), had friends arrested in the early enforcement of anti-hacking laws, seems quaint against the world today. I started using Linux at 0.99.something when it got shared memory support, which made it possible to run X. Get off my lawn.  If ever I'm feeling old though, I can just go hang out with some other ham radio operators, I'm still half their age. (strangely, I seem to have been half the age of most of the rest of ham radio operators the whole time I've been licensed... :) )

Not so many years later, I was sporting a 1GB drive with the same dimensions as that 10MB one, though it probably cost as much as a used car.  I talked about the ability of technology to transform peoples lives but when it was text and waiting most people couldn't see it. Almost overnight it (or at least from here it seems so) was obvious to everyone how important computers (and later, the Internet)-- the technology just had to catch up. I remember when "email viruses" were a hoax, and then it turned that the hoax became reality (thanks Microsoft). I guess that happens pretty often.  I posed on usenet, but always anonymously-- thank god, even I can't find the posts now (sorry sci.crypt, I knew not how stupid I was then but I'm sure you did); I fear for the youth of today and the perpetual memory of the internet. We're going to need mandatory name changes when people turn 18. Made my first webpage for NCSA/Mosaic. Crazy slirp hack to get pseudo IP connectivity from a shell account. Gopher was (and is to this day) always faster than the web.  In later years, I helped with the cracking of DVD-CSS so that GNU/Linux users wouldn't be denied access to DVDs (and it's funny how history is so inaccurate about what really happened, I see that playing out a lot in Bitcoin too... people who do the real work are happy to let idiots take credit, because they also take the liability), but these days no one cares about DVDs-- that was a waste of time. I started editing Wikipedia in 2004, and that wasn't a waste of time and I knew it wouldn't be.  Redditor for 7 years, but I never expected the site to stick around: thats why I used my old BBS nick here, these days I only use it for throwaway accounts.

As a signal processing geek I was using MP3 in 1993/1994 the _decoders_ were not real time, friends thought I was crazy and that it was pointless, modems were still slow enough that transferring songs was still far slower than realtime even with the 10:1 compression. I had a cell modem in the mid 90s (CDPD) ... and lots of people thought cellular data was pointless (worked great for IRC and remote shells). I had pre-802.11 900MHz FHSS wlan equipment.  Oh well, I personally find smartphones mostly pointless myself, even to this day... but mobile data is awesome and the world finally figured out I was right about that one :)

I've seen plenty of things explode into mainstream use, and plenty of others fizzle out.  Bitcoin is potentially as revolutionary as anything I've seen, but that doesn't guarantee its success. A lot of times a better marketed technically inferior solution wins out.
 The problems Bitcoin solves best are ones that many people don't realize they have. If people aren't convinced that decentralization is valuable to them Bitcoin may be eclipsed by centralized solutions which have better properties or at least better marketing, perhaps even by force as competitors try to buy public policy which is adversarial to Bitcoin.
> The 0.7 -0.8 fork was unintentional and could theoretically happen with any significant update (

FWIW, this is widely misunderstood; mostly because the actual cause was not really well understood at the time of the write up.

All versions of Bitcoin prior to 0.8 were non-deterministically (from the perspective of the blockchain) hard forking incompatible with _themselves_.
Hidden implicit behaviour in BDB would randomly reject an otherwise valid chain based on the layout of the database files on disk, which depended on the complete history of the node (what orphans it had seen, when it stopped and started, when it was first introduced to the network, etc.) rather than just the blockchain. 

The introduction of 0.8 played no role in that event, beyond-- perhaps-- having enough performance that miners could be talked into producing larger blocks.  Latest testing I performed suggested that most 0.7 nodes (and, especially most newer 0.7 nodes) were probably consistent with 0.8.

The proximal trigger of the event was a miner cranking up their block-size, not the introduction of 0.8 some time before. The network would have forked in basically the same way if Slush had increased his block size prior to the release of 0.8. Mining centralization also contributed to the issue, since the small number of pools meant that it was more likely for pools to go one way while the bulk of the network went another (this is also a reason why miners running just more copies of a node for checking can reduce security, even if you ignore the huge hit to decentralization: security is lost for you personally whenever other parts of the network disagree with your node; the particular cause isn't important, just the disagreement is).

Initially the problem was believed to be 0.7 vs 0.8 instead of 0.7 just being random due, basically, to confirmation bias: Some large miners on one side were known to be 0.8 and the captive rejecting node(s) were 0.7, evidence that there were 0.7 nodes on the 0.8 side wasn't really considered; and the correct resolution was the same either way.

As far as avoiding it goes: How the _intended_ change cannot prevent issues, it's orthogonal; because its the _unintended_ behaviour that causes the doom.  One answer is testing, of course, but that only goes so far: If there are 100,000 nodes on the network then each month Bitcoin is in operation is about 72 million node-hours. A random failure event that happened once in a few million node-hours of operation would not likely be observable in tests but would be very dangerous in the network. Worse, failures are not random and can depend on situations tests just never hit, or hardware configurations not used in testing. (So "lets rent out all of EC2 for a month for testing" isn't a fix, even if we could find a way to fund it.)

We've made progress in changing the structure of Bitcoin core so that certain classes of errors are provably absent and so that other kinds of issues result in a node-shutdown (which is, at worst, a denial of service instead of a consensus split); but there is a lot of way to go there (e.g. recently I discovered several new ways in which a database failure could cause Bitcoin core to continue operating on a fork). As we make the consensus code more isolated we should be able to get into a state where a greater class of issues can be formally proven to not exist in it, which will increase confidence that the consensus state cannot diverge.

This is an example of why the massive data collection is so dangerous:  It's so easy to lose all perspective when it is politically expedient.

Some guy leaking some mass surveillance secrets is analogous to murdering people watching a marathon with an explosive shrapnel — in exactly the same way that someone might say your personal politics or hobby interests are analogous to terrorism.

That would reflect the codec developers response to the artifact less well.
They keep giving their events absurd and presumptuous names, "Future of Bitcoin", "Satoshi's Vision"... ::vomit::

If the future of Bitcoin is scammers yelling obscenities and technobabble, count me out.
For some inexplicable reason Jeff Garzik really believes there are two limits.

It's inconceivable to me--  BIP141 explains that there aren't and explains why it was important that there aren't (in a footnote), several people including myself spent a long time writing to him explaining that there aren't two limits.

For a while I thought he knew better but was just spreading FUD based on the easiest argument available.  But until basically hours before their 'alpha' release their 2mb block size increase didn't even manage to actually increase the blocksize; because he thought there were two limits and tried to just increase one of them. All they did was remove a superfluous early check that exists only to avoid wasting memory and time processing the witness data for blocks that couldn't possibly be valid due to the non-witness part alone. 

It wasn't until a redditor made a comment about the code which I corrected and which he then [took to the list](https://lists.linuxfoundation.org/pipermail/bitcoin-segwit2x/2017-June/000006.html) that this got fixed. (They haven't even had the courtesy to respond to the person that made them aware of it...)

When segwit was merged into 0.13.1 the block size limit was removed in the codebase and replaced with the block weight limit as the only functioning limit. All mining, relay, checking functions are all based on weight now.

The method used to measure the weight of transactions carefully designed so that it is compatible with the block size limit which it replaces. This was a hard requirement to avoid any kind of 'two markets' effects and to avoid intractable optimization problems in both mining and fee estimation.

Today, w/ 0.13.1+  everything competes in terms of weight.  Activation of segwit will allow people to make lower weight transactions.

Even after they found out their size increase was broken because they didn't increase the allowed weight or change what counts as witness data for the purpose of it... Jeff went on to continue to argue from completely broken and inapplicable analogies. 

It would be an embarrassing but understandable (and sadly, not unexpected) misconception to think that there are two distinct limits (1M base and 3MB witness or something like that)-- if it hadn't already been explained so many times.

Right-- as I've posted on reddit before: under the existing widely deployed consensus rules less than 95% hashpower can activate segwit, if they're willing to preferentially extend chains signaling for it (and/or orphan non-segwit signaling blocks).  In effect any majority of hashpower can lower the activation threshold.

Likewise BIP148 like proposals can force activation without hashpower at all, if widely supported (at a cost of disruption if they aren't widely enough supported). (Ugh).


Now, now, this issue is not /that/ serious:

BTU = Bitcoins Temporarily Unavailable.
It's actually massively behind Bitcoin Core, by 3233 commits according to Github's web interface at the moment... and that is constantly growing because they've blended up the codebase so thoroughly that our externally maintained cryptographic libraries are the only large parts they could easily merge.

I'm especially amused by the fact that they went around changing variable names to make them insulting to Bitcoin Core, and this fact causes them merge conflicts that makes copying improvements from Core harder and more error prone.
Not just miners, any BU node that relayed this block got itself banned by all sane peers that it relayed them too...
From a mechanics perspective they could be autoadjusted... with about one line of code. Bitcoin's creator made a conscious choice to not do that.  Presumably the reason is that-- at least stated as simply as that-- it wouldn't serve a productive end.

As far as we can tell, the blocksize limit exists for two main reasons (plus a number of incidental engineering reasons)-- one is to protect node operators, who are the not-directly-compensated protectors of the immutability of the system's rules, against miners who get directly paid to include more and more transactions. Auto-adjusting like you suggest would fail at this goal, since miners could simply bloat to whatever they wanted.

The second reason is to create market reason for transaction fees to exist in the first place-- so they can pay for security to support he system. In a fairly and competitive market with well distributed miners, if blocks aren't consistently full the rational price for fees would be nearly zero, which can't support a high difficulty needed for security. So from that perspective that 'optimal filled' percentage is 100%-- in fact, beyond, 100% because if a block empties the mempool completely it will eventually be in a miners strong economic interest to attempt to [orphan the last block instead of mining the next](https://medium.com/@bergealex4/bitcoin-is-unstable-without-the-block-size-size-limit-70db07070a54).  (And because difficulty adapts, miners that don't behave rationally will find themselves bankrupt.)

I'm pretty sure 'the market' saw it coming for months and should have already priced it in. :)
The segwit component in the Bitcoin Core capacity plan is a 2MB bump (well, a 1.7MB one); and I (and the vast bulk of the community working on the protocol and node software) believe it is faster to deploy, safer, and fully backwards compatible, so much less disruptive.

It's safer for a few reasons: One is the improved deployment-- meaning unmodified systems keep working.  There are many people out running bitcoin software which is not very actively maintained... with segwit they can upgrade on their own schedule. (this also makes it faster to deploy)

It's also safer because it allows resource constrained nodes to opt out of some of the load without dropping out of the network completely-- effectively it relaxes the requirement for global broadcast a little.

Segwit also improves a serious cost alignment problem we have in Bitcoin-- the database of spendable coins is a very costly resource for the network, since it sets the minimum bound on the amount of fast storage needed to operate a node. But right now adding data to the UTXO set is effectively cheaper than signature data, which is very easy to handle.

There are also some quadratic costs in validation, with segwit addresses without adding a bunch of additional hardcoded limits, which, if nothing else, are more complexity for developers to deal with. Adding those limits is part of why the code posted today for the approach Bitcoin classic proposes is so complicated.

Importantly, there is a lot of controversy around blocksize hardforks which has been created by the highly spun promotion of really aggressive ones which would radically change the character of the system... resulting in many people opposed to them right now-- if not on Reddit--, the bitcoinocracy site shows millions of dollars worth of bitcoin signing messages opposed to them.  Beyond the technical issues, great political care is required with hardforks-- they have the potential to undermine the property rights of existing users, and a controversial one could split the network into multiple effectively competing currencies. I wouldn't personally support a highly controversial hard fork unless I thought the alternative was a failure of the system-- and it seems especially foolish to me when we can get roughly the same capacity in a much better way without one.

Cheers,
I was surprised to see that this hadn't been submitted yet.
Ironically this will damage his nodes on the network.  Bitcoin 0.15's feature to not allow connections from 2X coin peers was protective in both directions.

By bypassing that protection each node will be an effective denial of service attacker to the other.

Ignorance is a more parsimonious explanation than malice, but it's getting a bit hard to hold on to that view.
Are we reading the same document? Bitmain is creating a hardfork from the perspective of existing nodes this is an altcoin, no different than litecoin, they will not reorg to it under any condition.

They plan to premine it for 72 hours in private before making the chain public. Delaying it doesn't do anything to increase or decrease reorg risk for others, it only makes sure that three full days of blocks all go to Bitmain.
I think this is a lot of rubbish.

Jeff used to contribute to Bitcoin core, but (1) AFAIK never on consensus code, and (2) he hasn't done anything at all [for about two years](https://github.com/bitcoin/bitcoin/commit/3795e8152b678b9f805a395b144190a9f2fa2af4) and [little](https://bitcointalk.org/index.php?topic=1337008.0) for a long time before that.

Presumably he's been too busy [deanonymizing Bitcoin transactions](http://www.prnewswire.com/news-releases/bloq-acquires-blockchain-analytics-pioneer-skry-300413478.html) and creating government-cosigned [AML wallets](http://www.econotimes.com/PwC-and-blockchain-startups-Bloq-Libra-Netki-launch-Vulcan-Digital-Asset-Services-401520) to bother contributing anything to any Bitcoin implementation (even lame forks like Classic and BU). 

The lack of any recent contribution to Bitcoin hasn't stopped him from going around and marketing himself using the "Bitcoin Core" 'brand' while also going around pumping alts like [ethereum](https://twitter.com/jgarzik/status/755775586367176705)-- and I think the rest of the project has tolerated it for too long.

"Earliest" -- Sounds like you're under the sway of a false narrative which seems to be being heavily promoted on rbtc and other SaintBits LLC properties-- that the current developers are some kind of Johnny-come-lately usurpers that somehow took over (via psychic mind powers?). 

The most active developers today were active as far back as anyone else except Satoshi, save for just days/months.  Does a couple few days difference out of years make someone 'earlier' in any meaningful sense? Even when they've been inactive for a long time? -- e.g. [Matt Corallo's first commit](https://github.com/bitcoin/bitcoin/commit/279ab5e62f4e4344061d54b341d8d36af874fb88) was three days before [Jeff's](https://github.com/bitcoin/bitcoin/commit/f684aec4f38d6a9e48e870ca5dae6bd65da516cf), [Luke-jr's](https://github.com/bitcoin/bitcoin/commit/a14bf1946dfade7c615cd41924c7cd41abdbc119) was a month and a half before Jeff's, [Pieter's](https://github.com/bitcoin/bitcoin/commit/335e878be8f30ae1f7a23fbd3686fdd80c600282) nine days after.  -- so "earliest" along with probably almost every other developer you can name.

But did Matt stop contributing years ago then go on a roadshow selling himself as a "Core developer" while plugging competing systems, funding anti-privacy tech, and slinging [confused FUD](https://twitter.com/jgarzik/status/705059477297434625) against [new developments](https://twitter.com/jgarzik/status/788399430827446273)?  No. He's active and committed to the project to this day, just like Luke-jr, just like Pieter.  Not just cranking out bug fixes and optimizations but doing fundamental design and extending the protocol. Other developer's who haven't been active recently, like Peter Todd, ask media people to not call them Bitcoin Core developers to avoid unfairly trading on the project's reputation.

(and, aside, these developers that Ver attacks with this late-coming-usurpers meme were using Bitcoin before Ver himself was!)

This narrative that the current developers showed up in 2014 and took over is just nonsense.

Even in 2011, Wladimir was the most active contributor with 2 commits for every one of Gavin's.  

The reality of it is that a lot of the people doing a lot of the hard work chose to keep a low profile for personal and legal reasons-- because it wasn't clear if Bitcoin developers would get prosecuted (though today it seems a silly concern with the ICO mania defrauding people with no consequences...), and because they're not interested in being spokespeople.

If other people wanted to do more of the public speaking and pontificating, that sounded great.  No one anticipated that this would be used to misrepresent everyone else's contributions years later. The public's confusion has since been abused over and over again in what are by all appearances efforts to redirect control of the software people use from a public collaboration into various closed and private efforts.  This has been complained for years in private with the hope of minimizing drama and hurt feelings, in consideration of the genuine gratitude toward past contributions, and in the hope that the situation was a misunderstanding. But raising concerns privately has been astonishingly ineffective and, personally, I'm fed up with the false narrative and not going to sit quietly about it anymore.  I shouldn't have to correct claims like this but I do because, sadly, it seems some people are far too happy with the credit being incorrectly assigned to them.

So go ahead and say "former" if you like-- whatever, at least that would be vaguely accurate, though it neglects the multiple orders of magnitude difference between him and contributors like Wladimir and Pieter both in quantity and character of the work done.  But to suggest that it applies currently or recently is a grave disrespect to all of us who have continually supported and built the project for the last 6 years and kept things working well and coping with incredible challenges.

> So can anyone ELI5?

New address format for Bitcoin. Compared to the old format it doesn't use mixed case characters so it is _massively_ easier to read to someone.  It also has support extensions so new features can be easily added in the future, and is smaller in QR codes.

This upgrade also lets people exploit the full 256-bit security of segwit, rather than being limited to 160-bit security imposed by the older address types.

In an ideal world users wouldn't need to directly deal with addresses. But sometimes they do, so we should have an address format which is as easy to use as possible when they do.

>  I clicked the links in the demo, what's going on in the background?

You're not just limited to clicking, you can type...

The software is efficiently able to tell you where the errors are if you've only made a couple.

We also have software that works efficiently for up to three errors. But the demo only does two errors because sometimes when you make three errors it will not be able to uniquely determine which errors you made. So rather than having it only sometimes work for three errors we made it consistent-- we'll need further testings.  If you make more than two errors it will just tell you "invalid" without telling you where, or it will sometimes suggest two random places where you haven't made an error.

I'm not sure how to provide a UI for three errors... the back end decoder software can output a short list of possible error patterns (always less than 6 entries long IIRC, usually one entry).   This is an area where some UI wizardry could probably do some neat things.

There are a lot of little details that went into this design, it was basically a couple years in the making with a couple months of pretty concerted work.  There are a lot of interesting details, like the error handling is even stronger than we claim if your errors are limited to visually similar ones. The character set is chosen so that single click to copy works right in all common software... etc.
Thats great! Welcome to the club.
You misunderstand the dynamics there.  In Bitcoin Core we've been working non-stop for years just to keep the system above water with the offered load.

The same engineers urging care and caution with the block size are responsible for the massive performance improvements needed to to handle things as is... no one working on Core cares about size X vs size Y, in and of itself; the concern is always about the trade-offs and the risks of forcing rule violations onto participants... our preferred approach is to moot the issues with better technology. 
Blockchains do not really address any of the important problems electronic election systems have.  Rather, they introduce new and severe vulnerabilities.

Blockchain based voting does nothing to address the traditional issues of vote buying or coercion (give me your vote or I'll fire you), and they require a trusted party authenticate the voters or they are vulnerable to sibyl attack.

In terms of newly added vulnerabilities they bring: existing blockchain systems hand the miners near perfect censorship ability which would potentially allow them to just pick the election outcome.

Please don't make "blockchain" the new "DHT".  A blockchain can be a powerful tool, but it is not a general word for all possible distributed systems. Just because a hammer does not mean that all problems are nails.

There are good cryptographic voting systems already that have no need for a blockchain, which have been extensively reviewed by academics and cryptographic professionals and are designed to deliver the important behavioral characteristics which are informed by political science.  (E.g. https://vote.heliosvoting.org/ )
This post seems to be filled with equations and graphs which may baffle the non-technical while actually making some rather simple and straight-forward claims that are, unfortunately, wrong on their face.

>  Assume it takes on average 15 seconds*** to verify 1 MB 

The time it takes to verify a block at the tip on modern hardware is a tiny amount-- Bitcoin Core has a benchmarking mode that you can enable to see this for yourself (set debug=bench and look in the debug.log). The reason that it's so fast is that the vast majority of the work is already done, as the transactions in the block have already been received, verified, and processed.

E.g. for a 249990 byte block where all the transactions were in the mempool first, on a 3 year old i7 system:

> 2015-07-05 01:01:55       - Connect 599 transactions: 21.07ms (0.035ms/tx, 0.017ms/txin) [0.17s]

This is 80 milliseconds for a 1MB block.  You should have realized your numbers were wildly off-- considering that it takes ~3.5 hours to sync the whole ~35GB blockchain on a fast host, and thats without the benefit of signature caching (though with other optimizations instead).

[Keep in mind the measurements would be noisy, hardware dependent, and missing various overheads-- e.g. this was benchmarking a createnewblock so it was 100% mempool instead of ~99% or so that I usually see...  But this is orders of magnitude off from what you were thinking in terms of.]

What /is/ substantially proportional is the time to transmit the block data, but not if the miner is using the widely used [block relay network client](https://github.com/TheBlueMatt/RelayNode), or not yet developed protocols like IBLT.   The time taken to verify blocks is also marginally zero for you if you do not verify or use a shared centralized pool, miners here were taking the former approach, as they found it to be the simplest and most profitable.

There is no actual requirement for a non-verifying miner to fail to process transactions, it's just the simplest thing to implement and transaction income isn't substantial compared to the subsidy. If transaction fees were substantial you can be sure they'd still be processing transactions.

During times where they are mining without verifying they are completely invalidating the SPV security model, which forces other nodes to run as full nodes if they need confirmation security; so to whatever effect this mitigates the harm for larger blocks it would dramatically increase the cost of them by forcing more applications to full verification.

To whatever extent residual linear dependence on orphaning risk and block size remain, because verification is very fast your equilibrium would be at thousands megabytes, espeically on very fast hardware (e.g. a 48 core server).  

So your argument falls short on these major points:

* You can skip verification while still processing transactions if you care about transaction income, just with some more development work-- as such skipping validation cannot be counted on to regulate blocksize.
* That SPV mining undermines the SPV security assumption meaning that more users must use full nodes
* The arbitrary high verification rates can be achieved by centralizing mining (limited only by the miner's tolerance of the systemic risk created by doing so, which is clear darn near infinite when half the hash power was SPV mining)
* That miners have an income stream that allows them to afford much faster hardware than a single years old i7

...  but ignoring all those reasons that invalidate your whole approach, and plugging the _actual_ measured time for transaction verification into your formula results in a projected blocksize of

10 min / (4 * (80/1000/60) minute/mb) = 7500 MB blocks.

Which hardly sounds like an interesting or relevant limit; doubly so in light of the above factors that crank it arbitrarily high.

[Of course, that is applicable to the single block racing time-- the overall rate is much more limited.]

> QED. We've shown that there exists a limit on the maximum value of the average blocksize, due to the time it takes to verify a block, irrespective of any protocol enforced limits.  

I think what your post (and this reddit thread) have shown is that someone can throw a bunch of symbolic markup and mix in a lack of understanding and measurement and make a pseudo-scientific argument that will mislead a lot of people, and that you're willing to do so or too ignorant to even realize what you're doing.

The only "attack" is the posts on reddit saying there is an attack.

Bitcoin is specifically designed to deal with this, creating a bunch of low fee transactions does nothing interesting.

Click the 5-10 by the red box to make them disappear from the chart.
Nonsense.
All blockstream patents are irrevocably openly available https://blockstream.com/about/patent_faq/ under a program which has been applauded by many relevant parties, including the EFF.
You are confused. The covert form can be undetectable. If you do it poorly you can leave indications like "empty blocks, strange ordering of transactions in blocks, or never-seen-before transactions showing up in blocks".  All of these happen in the network FWIW. The practical unpredictability arises because whatever signals are left are burred in noise. Too many empty blocks or dumb software? Permutation or prioritization? New transactions or propagation?

I never alleged that I could prove they were using it on mainnet, only that their hardware secretly supported it. They've admitted to putting it in their hardware and to using it on testnet. They claim that haven't used it on mainnet "for the good of the network"-- but the constantly produce empty blocks (a potential asicboost sign) and argued that it was fine because the protocol permits it, when people complained.

Beyond the smoking gun of the secret implementation, you have the strange behavior of attacking segwit but supporting a LN centric extension block that is almost the same as segwit except mildly defective (e.g. can't survive reorgs) and mysteriously compatible with covert boosting.

I thought the rbtc party line is that bitmain was blocking segwit to protect themselves from LN eroding their fee income?  Zot. Or that segwit was complex (extension block is much more complex even in the current broken form) Zot. Or that segwit wasn't "really" a blocksize increase (extension block is exactly the same kind of increase). Zot.

In any case, if they actually aren't using it now then surely they'll support blocking covert boost because it screws up protocol extensions and would put them at a disadvantage if anyone else used it!  .. oh wait, no instead they're telling outright lies about the the proposal (e.g. saying it blocks boosting in general, rather than just the protocol gumming covert form). :(
Work has been ongoing.

Perhaps a concrete proposal in a couple months-- some of that depends on if there is any overhang from segwit, how much productivity loss there is from ongoing drama, and  how much effort is required for other ongoing technology work.

It'll also depend a bit on when segwit activates, while we don't know when segwit will activate it makes sense to give more priority to enhancements that won't get blocked waiting for it.
When I received the paper I searched the internet for the title and strings from the abstract and found no hits. The most recent internet archive snapshot of your site prior to the time I received it did not show it: https://web.archive.org/web/20150921231901/http://connection.mit.edu/research/ . Weeks later, I did find the _slides_ on your site: https://web.archive.org/web/20160325221644/http://connection.mit.edu/research/ but still not the actual paper.

In any case something being on your website is not equivalent to being disclosed. If, for example, you had consulted with a Bitcoin developer-- about your earlier documents that discussed Bitcoin extensively-- that might be a different matter.
> They have a contractual obligation to the company. 

In terms of what we do on Bitcoin core? The blockstream employment contract specifically write out any ownership of or interest involvement in Bitcoin core; obviously there are limits in terms of influence mitigation but a lot has been done consciously to mitigate risk there.

(And, in fact, Pieter and I have a special severance agreement as part of our employment contract where Blockstream is obligated to continue to fund us for one year if Blockstream behaves unethically or somehow tried to behave improperly with respect to Bitcoin; which isn't perfect, but is more than you're going to find in most other places-- even in things like the BCF employment.)

> Employees of Blockstream are employees of Blockstream plain and simple

Technically all the people you mentioned (except Patrick and Luke) are founders of Blockstream, which is why you can see things like the above mentioned contractual considerations.

It's also the case that any of us can walk and pick up employment elsewhere in Bitcoin immediately; and in my case I was prepared to work on Bitcoin full time without compensation anyways (a luxury I can enjoy for reasons unrelated to bitcoin; I've worked in technology for about twenty years and my partner is an attorney and well known and respected subfield (e.g. she was on the board of Wikimedia for many years and currently serves on the board of the Free Software Foundation) and in no risk of sustained unemployment; I am in a fortunate position).

> to gain any consensus it wants [...]
> " are the ones that decide what gets merged or not".

Thats where commit access as thats more or less a 1:1 map your "ruling class" (for the most part; though not exclusively e.g. Peter Todd's diligent review work is pretty influential; but people like Jtimon or Mark are not) is relevant since thats where most of the consensus anything comes from; and by that count its 2 of 5. Which seems pretty reasonable.   Regardless, if you don't like what Bitcoin Core does you're free to run something else, or stay with an older version.

I think it's amusing that someone who is largely uninvolved in Bitcoin, uninvloved in the development of Bitcoin core, and whose main activity in the ecosystem is promoting a Bitcoin competing altcoin is so concerned about my company's influence. By all means continue, but it would be more useful if you'd actually point out where something had been done wrong rather than the fear mongering.
From the Metronome prospectus, about how their security is a more reliable asset than Bitcoin:  "BTC is famous for its contentious forks".

Basically their comparison against Bitcoin rests on drama they created-- keep in mind BCash didn't exist when they created the B2X proposal.
Or you could just upgrade to 0.15 which bans S2X nodes...  Or stay on an older version, none of which are compatible with 2Xcoin.
That isn't twitter, you can tell because it's actually informative.
Unlike some people, he has the capacity to learn and change his mind.

Like in anything else, there are a lot of not-so-bright Bitcoiners (I love them too, however)-- and if you only had heard from them you'd also not have a very good impression of Bitcoin.
I prefer to respond to things before they happen: http://www.reddit.com/r/Bitcoin/comments/1z62za/whois_satoshi_nakamoto_detailed_article/cfquhqo

I must add though, while I haven't regretted every time I've talked to a reporter, I can't say that I've ever regretted not talking to a reporter.

Jeff's commentary is good too: https://bitcoinfoundation.org/blog/?p=510
I read this paper a couple months ago when it showed up on arxiv,  my commentary at the time:

Might have been good work if it got good advice about system requirements; but it seems it didn't.

It has two main parts; a block relay scheme and a restructuring of the p2p network.

The block relay scheme appears to misunderstand the objective;  it focuses exclusively on bandwidth and talks about things like a 4x reduction -- but this is a reduction from 20,000 bytes to 5000 bytes-- a savings which seldom matters at all.    But the goals for block relay should be to minimize _latency_  and adding increased potential for round trips or massively increased cpu computation (as is the case here) doesn't achieve that goal. Moreover, in settings where statefulness is acceptable (E.g. in our blocksat relay) and a difference of a few thousands bytes might matter other schemes can already transmit blocks in a few hundred bytes on average  (template differencing, in particular-- my older code for it which has to treat all segwit txn as a template miss codes current blocks to a mean size of 686 bytes per block, not including the coinbase txn), much smaller than the approach in the paper-- and without any consensus changes to Bitcoin.

It's not shock the kind of set reconciliation suggested there was [previously suggested (second section)](https://en.bitcoin.it/wiki/User:Gmaxwell/block_network_coding) and implemented; and found to be a lot slower in practice due to overheads.

What a lot of people miss about this and compact blocks and what not, is that they at most save the system from sending transaction data twice-- once at block time, once earlier.  So further tweaking here or there, which might still be helpful, still doesn't make much difference in overall bandwidth usage, because once the duplication is largely gone the usage is due to other factors.  So people going on saying that this allows 10x larger blocks or whatever are just confused-- it doesn't allow 10x larger blocks any more than compact blocks allowed 50x larger blocks.  If this scheme were _infinity_ fold more efficient than compact blocks it would still only save at most half the bandwidth of the original p2p protocol (similar to what CB saves), and in practice a lot less because other overheads dominate. And because of reconstruction overheads in practice what it would allow for (even given its required hardfork to reorder txn) might actually be somewhat less large.

The second part is the restructuring of the P2P network.  They suggest replacing the p2p flooding mesh with a miner rooted minimum spanning tree after observing that that the flooding mesh wastes a lot of bandwidth.   But a minimum spanning tree has a minimum cut of one: a single broken node can shadow the entire network.  Moreover when deciding on the spanning tree a node could falsely claim to be connected directly to everyone in order to be placed near the root.   So while this topology would be optimal in a world that had no dishonesty or need for fault tolerance, it doesn't really apply to Bitcoin.  It isn't like people looked at this problem before and were unaware that you could build a duplication free distribution topology-- the duplication is essential for security and robustness, not a bug.  The question of more efficient distribution in a world with broken and malicious peers in an identityless network is a very interesting one-- even just formalizing the problem statement in a useful way is an interesting question; the question of doing it in a world with perfect flawless honest parts isn't-- it's a solved problem with a well known set of viable solutions.
I'm not defending Silbert-- as I've posted before his actions have been exceptionally unethical.

But making unsubstantiated claims against him just weaken the substantiated ones.  So far this particular claim has not yet been substantiated.

You shouldn't assume too much from an announcement. At _worst_ it is a lack of progress.  At the very least it will almost certainly help some people calm down who have been far too worried about all this drama that has much more noise than substance behind it. :)

There is a long road ahead to resolving misinformation force fed by BU people and their pushers... Like look at the comments-- "made even more serious mistakes leading to bugs causing thefts, fake coins, unintended forks" so core gets blamed for design errors put in by Bitcoin's creator and mistakes by renowned large block advocates?  But ever other time when it's convenient Satoshi isn't considered part of Bitcoin core...  --- clearly someone hasn't been told the whole story. 

But acknowledging the history and stating the rest as an opinion about direction-- though degraded by misinformation-- is a lot better than bombastic statements or attacks. It's a position people can talk further from.
The idea of leaving asserts out of release builds seems to be a (IMO bizarre) windows-ism.   POSIX and ANSI C  assert.h both only leaves out asserts if NDEBUG is set, which the GNU toolchain _never_ sets-- so the only way they get turned off is if you do so explicitly.

I understand that Visual Studio goes and disables them in "release builds" and when I discovered that I thought it was bizarre and a poor idea.

In Bitcoin Core asserts are used to check for impossible state corruption which, if operation were to continue very bad things would happen-- like consensus divergence, secret information leaks, or remote code execution.  There are things worse than a clean and safe crash, much worse.

Because all the development and testing is done with these assertions in place, and because they're specifically designed to be protective against corruption-- we wouldn't disable them in releases: after all, the software wasn't built or tested without them!

One of the flaws in with assert (even called out in the man page) is that it's easy to end up with code that behaves differently with them in or out. Of course, we endeavor to not introduce any of those cases, and -- in fact, Bitcoin Core currently works fine with the assertions pulled out. (Though this isn't true for Unlimited or Classic, both will fail to work correctly if NDEBUG is set.)

The BU code was broken regardless of asserts being in release builds or not... actually, the assert is actually protecting them here: in this latest crash without the assert the code would go ahead and do a bunch of manipulation of uninitialized memory, with who knows what consequence.   Though the real error was just not having enough (any?) sanitation of user provided input for xthin.

BU now apparently has a bugfix release out-- though it is closed source, binary only, and they haven't updated their source code for six days.

Considering reports on Reddit that their website was hacked, my initial thought was that their github was hacked and the binaries were malicious. But Ver's staff, Magma Hindenburg, confirms they are real, and that BU has actually gone a closed source route now.

I ... just ... don't event ... wtf.

> FYI, we have contacted Core developers about a bug whose effects you can see as approximate 5% drop in Core node counts on Feb 23, 2017 and Mar 6, 2017.

That report was spurious: The vulnerability you reported existed in BU but no released version of Bitcoin Core, but thank you for reporting it.

I was shocked, especially considering your prior reports via public announcement that you were "[unable to weaponize](https://medium.com/@g.andrew.stone/a-short-tour-of-bitcoin-core-4558744bf18b)". Next time you have a suspected vulnerability in Bitcoin Core, it would be helpful if told us immediately instead of [discussing it in public](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/316) for 13 days first.

There are vulnerabilities in unlimited which have been privately reported to you in Unlimited by Bitcoin Core folks which you have not acted on, sadly. More severe than this one, in fact. :(

In this case, as far as I know Peter Todd is just repeating a report that was already widely circulated and was, in fact, [disclosed by your organization](https://github.com/BitcoinUnlimited/BitcoinUnlimited/commit/eee6a2daeb560f26061535695fc0f7de168ffe32). Am I mistaken?

This is all the funnier when you know that he types (quite quickly) with only two fingers.
Enormous swiss cheese. ...

You can buy solutions to your traveling-salesman package routing problems from untrusted anonymous parties. For one example.  The key question is: Is there information someone would like to buy, where someone could write a program to decide if the information provided was the right information. If so, then this protocol probably applies.

My experience in thinking about applications is that the solutions tend to be either really boring-- where this tool is overkill but since its costs are mostly in the one time development costs, it's reasonable and you'll use it many times... or kinda edgy, buying secrets and such. But generally, this gives a very efficient and private way to conduct a broad subset of all possible smart contracts.

An interesting point is that you can actually understand the lightning network as an efficient subclass of this protocol (though its development history was different)-- in fact the transaction we used on the blockchain here is identical to one of the lightning proposals.  And both reflect how, if you transact smartly you can transact outside of the blockchain without compromising on security.

This has been circulating around for a week or so, and I spent a bit of time looking into the methodology. (The full graph has more details on its creation).

I really like the stacked time of contribution part, it's especially interesting-- it lets you easily compare 'most recent' activity in the same graph that also includes overall.

Selfishly, I like this chart because unlike virtually every other one I've seen, it doesn't systematically misattribute my work in the libsecp256k1 subtree to Pieter. :)  

That said, most of my work isn't coding in any case (actually most of those lines attributed to me come from special testing code)-- on a "coding" basis, I'm overly credited here.. which probably will feed more of the "Gmaxwell is a great ~~typist~~ coder but what does he know about anything else?" FUD. Oh well, can't win. :)

The graph systematically under-represents the contributions of some people like Peter Todd (who, like me is also very design/review/analysis/test heavy) and Luke-jr (who tries a lot of experimental things that don't land, but which are still valuable exploration)... (or Wumpus and Jonas simply because they've done a LOT of the UI work; or Jeff's RPC work from when he was at bitpay)... and to some extent "most of the work" is review is true for everyone working on Core.

It's a bit of an _interesting_ a choice that it leaves out the wallet and apparently all UI parts (RPC, etc.): it's filtered to include only "core" the consensus, blockchain, p2p network, etc. (this is noted on the graph)  I guess it was created to answer a particular question about the more meaty parts of the system.

Any kind of 'lines' metric is going to be a quite approximate measure of contributions. But then again, to non-participants all this stuff is opaque; and that makes it better than nothing I guess. And perhaps it helps explain why those in the know haven't had much expectations for consensus change 'fork' projects like "Bitcoin XT", and the ill-named "classic".
I contributed to the discovery and analysis of CVE-2014-3570 "Bignum squaring may produce incorrect results".

In this case, The issue was that one of the carry propagation conditions was missed.

The bug was discovered as part of the development of libsecp256k1, a high performance (and hopefully high quality: correct, complete, side-channel resistant) implementation of the cryptographic operators used by Bitcoin, developed primarily by Bitcoin Core developer Pieter Wuille along with a number of other people.

Part of our testing involves an automatic test harness that verifies agreement of our code with other implementations with random test data, including OpenSSL; though to reduce the chance of an uncontrolled disclosure we backed out some of the low level testing after discovering this bug.

This randomized testing revealed the flaw in OpenSSL. I suppose it's also a nice example of statistical reasoning (p=2^-128 that a uniform input triggers the misbehaviour) doesn't itself express risk, since while we've put enormous amounts of CPU cycles into tests we've certainly not done 2^128 work. In this case the reason our testing revealed the issue was because we used non-uniform numbers specifically constructed with low transition probability to better trigger improbable branches like carry bugs (https://github.com/bitcoin/secp256k1/blob/master/src/testrand_impl.h#L45). I used the same technique in the development of the Opus audio codec to good effect.

(Whitebox fuzzing tools like Klee or AFL, or branch coverage analysis, while good tools, seem to not be as effective for errors where the code completely omits a condition rather than having a wrong condition which was not exercised by tests.)

In libsecp256k1 the field operations (nearest parallel, we don't use generic bignums) are augmented with proofs of correctness (e.g. https://github.com/bitcoin/secp256k1/blob/master/src/field_10x26_impl.h#L810 ) though only a small part of our provable correctness can be machine checked currently. Of course, correctness proofs are only one part of our strategy. Fortunately, because of the much smaller scope of libsecp256k1 we likely have an easier time hitting a higher level of assurance than OpenSSL does.

We were initially unsure how serious the bug was and came up with several sophisticated attacks that were fortunately prevented by OpenSSL not using its optimized squaring operation in all the places it could have used it.

Perhaps most interesting is that one of the reference implementations of curve25519 had almost exactly the same bug as OpenSSL: https://gist.github.com/CodesInChaos/8374632 but it seems to have gone largely without notice.
This is a technical term. Not my fault that Coinbase.com (or blockchain.info...) decided to hijack the name of part of the Bitcoin system. :)

https://en.bitcoin.it/wiki/Vocabulary
You ought to read and understand the ruling before you write him.  It would show little respect to fawn over him just because you agreed with the result. The judge's real contribution is his thoughtful analysis— so you ought to be familiar with it before spreading the love.  
> This is probably why they chose the block 9 key: a valid signature for it exists. The genesis block key was never used to create a signature and can't be exploited in this way.

Now everyone can see Christopher Jeffrey caught red-handed setting people up for the next phase of this scam.

    x = int('678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb6', 16) #Genesis block pubkey
    y = int('49f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f', 16)
    hash1 = 112004660972487013946667514493328179615648583977357345698077942650003000638148
    r1 = 40846880744169909187079133138354482233228066625845646933713572448574465154592
    s1 = 65301994059254509748978241927165450220924402015964423318217051342766067391347
    hash1 = 18107120044752029565784354234812007695786944790741240922279021960469477343617
    r1 = 111212803820619122991954395611006318961708176935404978894282403060111671917023
    s1 = 3484931748581056040594550352243750349113935904256251003733154562019927629375
    hash1 = 101571436691581128949983116668909476683681097644471502464535681427175830139308
    r1 = 11846986453652998302774492301665373849381420065242097103962373893580491333003
    s1 = 31900594198254905835796796786166207222796240775213255451997776956635146816613
    hash1 = 44226245604848525549554336120782642990375388188255245928210541631643801493507
    r1 = 98730178230850801101317753304838081031791651300632369467214071894336874789635
    s1 = 51486600792598014897821497362667703218835494302139656366101317372225321871645

**Or are you going to start claiming that I'm the creator of Bitcoin now?**
> Ironically, I dont see any simplicity in their code examples.

Simplicity is simple because the system itself is simple, a precise and complete definition of simplicity fits on a tshirt.

Because simplicity itself is so simple it is highly amenable to formal analysis.  But because it is so simple it doesn't do much on its own-- simplicity itself doesn't even have the concept of a _bit_.

Most programming with simplicity would be via higher level languages or at least with libraries of standard code that give a higher level of abstraction than the raw system.

Using simplicity, Russell has done things like written a simplicity program that implements a bit and proved it behaves like you expect a bit to behave, then using that wrote a half adder, and proved it behaves like it should, then using that a full adder, and prove it behaved as it should... on and up through a 32-bit adder -- and proving it implements the arithmetic correctly, then from that all the way through to secp256k1 signature validation.   As a user of this you can just use the end results and also be able to formally reason about your programs because their provable behaviour traces all the way back to the start... and as interpreter implementers we can do things like intercept and map (e.g.) the 32-bit arithmetic and have it carried out with direct processor instructions at high speed rather than having it executed from a simulation in primitive logic, then using a formalization of the processor's behaviour prove that this optimization cannot break anything. 

This makes it possible to build up from a solid framework-- Castles made of diamond rather than sand.

Maybe at the end of the day you'd use it via a very simple high level language that hides all that stuff from you--  but the power is there for when you need it.

what the fuck are you going on about?

If it is ever necessary to change POW to keep Bitcoin going, people will do so without a second thought.  And if it is not necessary, then there is no chance people would do it. It's a nuclear option whos very possibility avoids its own necessity.

You're blathering on like there is some proposal to do this now. There isn't. Makes the post seem like purposeful FUD to me.
> I'm a big core advocate and even I think it wasn't very good planning to not have the UI ready for activation. No one can say they didn't have enough time.

Using segwit before the activation is buried is unsafe and could lead to funds loss if miners behave maliciously.

Having a wallet switch behavior radically based on network events is also a ton of complicated and difficult to test code which can be avoided simply by waiting until after the activation is burred. (Partially because automating UI tests has extra complications, and partially because automatic support has to handle absurd cases like block height going backwards and other stuff that just isn't an issue if the support is released a while after segwit is activated.)

Not adding segwit in a GUI accessible way in the wallet until post activation was an intentional decision. Almost all of the backend plumbing was done as part of the original implementation, however, and it can be used via the debug console and RPC. (As it was needed to be able to extensively test segwit.)

Moreover, we have had other urgent priorities like making sure the system performs well enough to survive segwit's blocksize increase, and the current release was created at a time when about 25% of the hashrate was signaling activation, similarly when the next release' feature set was established... So even if there hadn't been an intentional decision to integrate segwit only after activation it still wouldn't be there yet. Besides, I haven't seen you around helping...
Thank you James Hilliard and jl2012 who wrote BIP-91, the code implementing it, and convinced people (including btc1) to adopt it.

From what I've heard and can tell the only miner running btc1 is Bitmain; the rest are using the segsignal patch.
uh, it forked because the implementations are not compatible with each other... and is now stuck.

Fiber is even cooler than that, it still gives me chills with how cool it is, even though I did a fair part of the coming up the original ideas behind it and thus have no real right to be so impressed with it.

Problem: You learn a block and need to get it to a bunch of computers as fast as possible; but everyone is all over the damn earth, and it takes 200ms to go round trip between Australia and California and if the whole thing isn't done significantly faster than that then you're much slower than "as fast as possible". 

Worse, 1% or so of packets on long international links go missing, plus links and whole nodes randomly go offline.

To your benefit, 99% of the data you're sending  is already known to the other computers, but you don't know _which_ 99% and they still need to learn what parts end up in blocks in what order plus the parts they are missing.

Plus you've got all these computers with their own network interfaces and bandwidth that are sitting idle if they're just receiving and you won't be as fast as possible unless you can use everyone's bandwidth.

So what Fibre does is that it first sends a very small sketch of a block which lets a node reconstruct the block from the transactions they already know, then without waiting for any replies it starts sending error correction data. Flow control for how fast the data is all set in advance. The error correction is setup so that if you're missing 50 packet worth of data you need to only receive _any_ 50 packets of correction data-- doesn't matter which packets you got, or which you were missing. Behold the power of linear algebra-- it works for the same reason that any N+1 points uniquely define a N degree polynomial.  The sketch is also sent using error correction data.

(The sketch part is very similar to BIP152,which Fibre was jointly developed with.)

Moreover, it doesn't send the same data to each peer: it sends every peer different data, and those peers then send other peers the data that they got (which will be non-redundant).  So everyone's bandwidth gets used, none wasted.   When someone gets enough data to recover the block they start producing new error correction packets distinct from the ones created by the original source.  They all keep sending, avoiding any duplicate packets being sent, until they get told to stop by the far side (or hit a pre-configured maximum amount of overhead, which is something like 2x the size of the block).

Because of this all the resources are maximally used and the block is received by every host at very close to the best-path communications delay to the source. Every Blinking Time. Not just sometimes. Not just only if the block is similar to the node's mempool. Not just if packets aren't lost. Not just if links don't go down.  But Every Time.  Even if packets are lost, even if a link goes down, even if a whole host goes down (obviously the down host doesn't get the block until it comes back. :) ).   And even though the whole process finishes before the speed of light in fiber would allow sending a message back to the original sender to say "hey, I missed something."

Fibre "uses every part of the buffalo".

Pulling this off required some pretty swank engineering and optimization on Matt's part, work someone couldn't have pulled off without years of experience running earlier generation relay networks to understand the sources of delay in the real world. -- and a large helping of diverse talents.

There are many ways that it could be improved and made even more robust (and slightly faster) too... though not *too* much faster... not without shooting beams of neutrinos through the earth. :P  (Obvious areas of improvement would be automatic routing to better handle links/hosts which are offline a long time, better packet scheduling, faster error correction decoding...) 

However, it's been a challenge supporting the network.  To really exploit the benefits the participating hosts need to be _fast_ dedicated servers, carefully positioned, in networks which have great routing. All this fancy protocol stuff doesn't help if the computers are busy serving someone elses porn or if the ISPs are needlessly routing all traffic through Timbuktu. This isn't cheap, and Matt was finding that the sponsorship model was really not working well (e.g. having to spend a lot of time reminding sponsors to pay, etc).

Fibre and BIP152 work together:  BIP152 tries to get the fastest transmission possible assuming that the source and receiver have low latency between each other, which generally implies minimizing the bandwidth used-- there won't be much loss, retransmission works fine, and with many peers bandwidth waste means slower transmission. Computers in a fibre network minimize latency at all cost, and spend a lot of extra bandwidth to do so-- but they don't have many fiber peers, and all of them are far away.  To get blocks everywhere in the Bitcoin network as fast as possible Fibre blazes through the long haul paths, BIP152 handles the last mile. If fibre nodes are not functioning right, no problem-- BIP152 will do the whole job on its own, though not as quickly.

BIP152 combined with Fibre and general optimizations in block processing speed (since 0.11.x I believe we've now made ConnectBlock >60x lower latency) have radically lowered orphan rates on the Bitcoin network which had increased a lot with network load in the last couple years.  These improvements lower the incentive for validationless mining (which causes empty blocks and erodes security for SPV clients) and avoids larger miners getting an unfair share of the blocks as well as reducing the impact and risk of selfish mining. Without it mining would be much more centralized because the next best option for miners to mitigate latency is to centralize.
[BIP-152](https://github.com/bitcoin/bips/blob/master/bip-0152.mediawiki) is on something like 85% of the nodes on the network today and is considerably better than Xthin; able to achieve much lower latency, able to achieve lower bandwidth, not vulnerable to collision attacks, clearly specified, and peer reviewed.

(And, FWIW, I previously complained about the complexity and low code quality of xthin...)

FWIW, thinblocks were on [the capacity increases document](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-December/011865.html) as a pre-req for segwit... long before BU started any work on xthin, we'd been working on it for some time.  They just made their version and did a marketing blitz but around it, but they didn't do the work in terms of design quality, implementation quality, testing, review, or specification. :( 


The one thing comparatively that BIP152 lacks compared to xthin: A bunch of dishonest marketing hype.
You won't be able to tell until after November 15th, since that is when the signaling will start.  (This gives people a bit of time to upgrade before it starts).
Bitcoin has _not_ done any such thing. 

In 2013 blocks were made that some nodes rejected. Eventually miners moved to the chain that all nodes accepted and those blocks were reorged out. The reorg out didn't blacklist transactions or confiscate funds, and happened as a result of miners mining a chain that more nodes were accepting, without adjusting the protocol.
Gwern has been accusing people of being bitcoin's creator for some time.  It seems to have become his windmill.

I think he's really good at finding confirming evidence and explaining away contradictions, and not so good at finding potential disproof; and hasn't seemed to learn sufficient caution from past mistaken identification, nor from the threats he received when some nutball thought it was him. 
Assuming sufficiently recent Tor is running and configured; unless disabled Bitcoin Core will automatically create a Tor hidden service to accept incoming connections (thanks to the Tor project for adding the functionality we needed to do this!).

Series wired mining chips is a well known design technique that was in use even before Bitmain was on the scene, e.g.

https://bitcointalk.org/?topic=370932

Same as XT/Classic/Unlimited--  if they wanted to go create a fork chain they could do it at any time.  Those of us running Bitcoin will happily ignore their invalid blocks.
In a bit he'll realize the 'rich statefulness' is also an anti-feature.
The prior one was at the end of SendXThinBlock() in thinblock.cpp, this one is in main.cpp, exactly one line above where SendXThinBlock() is called.

Beyond the fact that it was discussed in public and exploited against classic last week, all you would have to do is grep the codebase for 'assert' and you would have immediately seen that as an obvious no-no.

I find it hard to believe that they're even trying.  I think they're ripping off whomever is funding them: phone in some code here and there and get paid.  Perhaps they're secretly rooting for Bitcoin and are doing us all a favor by taking the money from the people trying to screw things up.

A lot of people are going to believe their fraudulent vulnerability report, esp since it was translated into Chinese-- it's all some people will get to hear.  :(

It's double absurd that he posted it after [I directly told him](https://www.reddit.com/r/Bitcoin/comments/5zdp8j/peter_todd_bu_remote_crash_dos_wtf_bug_assert0_in/dexfzuy/) that Bitcoin Core was not vulnerable. I'm confident in this because I understand and can reproduce the vulnerability they suffered (and which their post reveals that they patched around without understanding).

They clearly aren't at all concerned with the truth. :(

Moreover, it looks like they've been privately circulating that dishonest post for a week.  Explains why they are telling people Bitcoin Core is behind today's BTU-elimination-from-the-network event: their guilty conscience makes them think its retaliation.

As if they were significant enough to justify retaliation. :P ... and as if there would be anything left standing. :P :P

If they really believed that their proposed new consensus system that they're trying to force onto Bitcoin was that much better, they'd make a straight up altcoin using it; doubly so since they explicitly claim they aren't concerned about diluting Bitcoin's network effect with a split.
Most of the material is motherhood and apple pie. It's a common political technique to stand up behind a number of points that everyone supports in order to imply that people you disagree with don't support them. Bitcoin should be Free and Open Source? well no crap. All the big points are obviously true things that everyone agrees with, where it goes a bit off the rails is in the details: E.g. use of the BU dog whistle "emergent consensus" which has, historically, meant a radical departure from the design of Bitcoin, with nodes reducing their enforcement of the rules and handing control of the system to miners. Or the inaccurate implication that more incompatible implementations means more developers.

But what is far more interesting is what isn't mentioned: Sound money, fungibility, protection from political pressure, nor a hint of personal privacy and commercial confidentiality.

Considering the length of the document, I think those omissions are a bit telling-- if it isn't a fake, as Samson suggests it might be.

Such things are only simple from the kind of limited perspective that can say that malleability will fix itself with a straight face.
I do NDAs but usually only with extensive modification and in narrow circumstances, including carve-outs for information I'd be ethically obligated to disclose.  Just not freely. In general principle I avoid them.  I wouldn't have done one for Wright-- probably at all, considering the past evidence of fraud, but in no case without very heavy limitations.

In the case of wright, many people knew he'd be making this announcement for months. It was even on Reddit. It's hard to see what purpose an NDA would serve, beyond a brief embargo on public announcement.

I would have forced that he "prove it" to me first before having a meeting: Proof before sales-pitch. I am not immune to being fooled, but in 'similar' cases in the past have taken steps both to minimize the risk, and limit the damage. Besides, if the proof fails the meeting is a waste of time.

If he contacted me-- I would have simply used the genesis block pubic key to send him an encrypted reply.  If he'd been able to continue the conversation, it would prove to me in a non-transferable way that he was worth talking to after all.

If I published anything about this experience I would have written purely factually, not a glowing endorsement that exceeded the objective evidence available to me.  I would have also demanded the ability to review my understanding with others who might catch that I made an error, before making my mistake in public.

I thought this was an "AMA" not a "PeterToddHearsay"-- Would you please answer my question?
I worked for Mozilla then. You might be interested in the kind of principles I was [advocating at that time](http://permalink.gmane.org/gmane.comp.mozilla.governance/544).
>  note that maxwell appears to have stepped back for the same reason as hearn. 

You're pretty good at pure conjecture. Perhaps consider a future in science fiction?

>  large amount of technical work the Litecoin developers contribute to the core Bitcoin protocol/client

I regret to point out that this is a common misconception created from the litecoin people pulling pre-release features out of Bitcoin and putting them into builds.

I believe the only piece of functionality in bitcoin core to come from litecoin is the "getnetworkhashps" RPC call. They also kicked in some funding for the OSX disk corruption bugs and have done some fund raising for P2Pool.

To the extent that they're contributing software at all, it's in the form of beta testing— which is useful and much appreciated— but isn't really the kind of technical work I think you're thinking of there.

I'd love to see actual innovation in the altcoin space but there is scarcely little of it.
People like this live in an echo chamber nested inside an echo chamber. The idea that there can be substantive dissent among good and intelligent people is non-existent because the closed environment of these hierarchies suppresses dissent.

This is magnified by the secrecy. I'm sure he has access to all kind of insanely secret stuff that most of us couldn't even guess about— some of it may even be accurate. This information is very important to him because access to it is partially how he assesses his accomplishments and success/  And as a result he probably regards all the rest of us as ignorant children, hopeless morons who need knowledgeable people like him to protect us.   His overestimates of the accuracy and value of his secret knowledge go unchallenged by virtue of its secrecy, and the ease in writing off criticial views like mine as being produced by an 'enemy of the state'.
Mozilla even linked to that comic when they posted about including the pre-standard codec in Firefox: https://hacks.mozilla.org/2012/08/opus-support-for-webrtc/

Edit: Crap, wrong post, it's linked from the words 'break the logjam' in https://hacks.mozilla.org/2012/07/firefox-beta-15-supports-the-new-opus-audio-format/
Expected fraud is expected.  The analysis looks perfectly fine to me. I'm sure he will come up with some screaming jargon laden technobabble completely implausible excuse if pressed just as he did with other cases where his poor forgeries were pointed out.

I do worry that the focus on details that he got wrong via extreme incompetence misses [the point](https://www.reddit.com/r/Bitcoin/comments/6l6w56/the_hard_evidence_about_craig_wrights_backdated/djrljt6/). Maybe someday wright will hire someone clueful to make his forgeries and then we'll run out of trivial mistakes like this-- but that still won't make his claims any more plausible.
This proposes using a long known ([and overdue for fixing](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2018-August/016316.html)) vulnerability to bypass Bitcoin's inflation and capacity limits. Something doesn't stop being an attack simply because you use marketing speak to describe it as an "upgrade". The long list of other things like a rebatable fee market, "sharding", or mimblewimble are essential unrelated excepting in so far as they're things that you can implement given free reign to rewrite the rules. The document could have equally listed mandatory demurrage, coin expiration, or AML integration as potential features (but those wouldn't make for an excited response :) I hope)...

Exactly.

https://www.reddit.com/r/Bitcoin/comments/438hx0/a_trip_to_the_moon_requires_a_rocket_with/
"Funding nodes" wtf.

Having people pay Jgarzik to run nodes does not do anything of any value for the network at all unless it's outright broken.

Users running nodes helps Bitcoin through decentralization by securing their autonomous enforcement of the rules.  Some guy spinning up a bunch of nodes on VPSes is just a sybil attack more or less useless for anything but spying on user's transactions.

The only case where doing something like that would make sense is if the network was failed to the point that there were few enough nodes running that users were having trouble connecting and syncing... and in that case it would be better to just run a small number of high capacity nodes, not sybil attack with a false fleet.


Lots of people knew they phoned home, as you mention...  The fact that it can be used to remotely shut off the miners is far less obvious. At first glance that code looks like it does nothing.
Great news!
Proves? no.  But a lot of the evidence is almost certainly forged.

Whats more likely:  Someone created Bitcoin and later forged a bunch of evidence that they created Bitcoin? Or, someone didn't create Bitcoin and forged a bunch of evidence that they created Bitcoin?
I've written literately tens of thousands of words (if not over a hundred thousand) on the subject on Bitcoin-development, Bitcointalk, the development chat, and private email. (with proposals, design, and research.)   This stone has long since run out of blood.

At the end of the day there appears to be a fundamental disconnection about what Bitcoin is long term.  Some believe that Bitcoin will not exist as a decentralized system long term, and instead trusted and trust worthy facilities will secure it with digital signatures; rather than POW consensus.  I believe that a Bitcoin like that would be a failure even if the coins somehow retained high value, because it would be just a reboot of the existing infrastructure, but probably worse-- lacking the a design purpose fit for a centralized world, as well as the regulatory history and experience of the traditional systems.  If I wanted that I'd just use USD and Visa or Paypal-- we already have that model, and it has its plusses and minuses.  Instead, I believe Bitcoin can be successful as a truly decentralized system which depends on cryptographic proof rather than trust. To get there we have to frankly face the extreme costs of having a decentralized system, and potentially tolerate slower short term adoption in order to build something that survives generations with cause rather than only due to inertia or not at all.  Of course there are shades of these views, but ultimately you are not likely to see strong fundamental agreement on medium term trade-offs when there is a sharp break in understand in what the system looks like long term.

This recent discussion was unfortunately temporally coinciding with both people's vacations and work on the publication of the sidechain tech demo. I've been working 13+ hours per day for weeks on prior commitments.  I am having to constantly ask people top stop paying attention to this and focus on getting things done, its exhausting.

Ultimately, tis not mine to go convince people. If the Bitcoin community wants to go commit suicide, I'm confident that I can sell my most of my bitcoins before most of the public has realized things have gone wrong.  ... I have to think this way to keep my sanity, I cannot personally save the world from everything that can possibly go wrong.  And why else aren't there a series of blog posts? I'm not trying to sell you-- or anyone else-- on anything. It may just be that the world is simply not _ready_ to handle a decentralized cryptocurrency. Work in this space stretches the bounds of what we're able to accomplish with software engineering, its not shock that it also streaches the bounds of what we can achieve with public education and political discourse. ... especially given the really modest amount of resources available.

We know from leaked emails that the Bitcoin XT fork has been planned for a year plus.  The people promoting it have not been very active with Bitcoin Core in that time (or in Mike's case, ever) so I would be somewhat surprised if it went much of anywhere. So it doesn't generally concern me, but if nothing else what this even suggests is that it's very easy to convince a large portion of the Bitcoin users on Reddit to support a risky change to the fundamental rules of the system, even against substantial opposition from a large number of technical experts, against research evidence, and against the will of many long time users of Bitcoin-- and if that is so, a soft-fork that primarily only effects the people who choose to use it should be no big deal.   Moreover, if that happens; it might well not just matter; if Bitcoin goes a centralizing route, I'll find other things to work on; there are too many important unsolved problems in the world to spend more of my life just building yet another centralized  payments rail, no matter how personally profitable it might be..

If it's a test— it's failing. :)

Nah, as I said— they've been making transactions like this continually. I assume it's some cronjob that runs that sops up tiny payments.

I don't know that it's news, but it strikes me as sad but a bit amusing that they're _still_ authoring invalid transactions.
You can't impress ~~developers~~ self-important pricks.
Thanks. It now says:
> Opus is a totally open, royalty-free, highly versatile audio codec. Opus is             
unmatched for interactive speech and music transmission over the Internet,
but also intended for storage and streaming applications. 

(or it will when the SVN/Web sync happens)

All sane testing has to be done double blind— even the casual tests we use for tuning during development.  ... But hydrogen audio has attempted very high rate tests in the past, and basically no one whos tried it wants to try again, it exhausts the listeners, consumes thousands of man hours.  ... and you end up with a result with all in the margin of error.  128 isn't completely insane, but the post at the top of this subthread was talking about 256 which is another matter.


We do?  I mean, Opus absolutely is for for real-time internet audio. But it's _also_ for stored file usage. We put in a fair amount of effort for that, both in the final months before the MDCT modes (formerly CELT) of the codec were frozen, and in the last couple months on the experimental (to be released as 1.1 encoder).

Your 'everything at 192k sounds great' is more of an explanation of the graph: it's too hard to usefully test at high rates.
None of the problems it gives you are unsolvable without using those.  Here is a really dumb python program I whipped up for this game years ago. There was ... some vague corner cases that could make optimal play exponential in computation, thus the non-greedy solution. But I have no clue now. Obviously the particular game is hardcoded. To change the game change the words and the wlen. Cheers.

    import sys
    wlen=7
    
    def nm(a,b):
      sum=0
      for chr in range(wlen):
        if a[chr]==b[chr]:
          sum+=1
      return sum    
    
    words=set(["seaside",
               "cochise",
               "banning",
               "joining",
               "cantina",
               "beating",
               "gaining",
               "barrage",
               "someone",
               "warring",
               "camping",
               "banshee",
               "realize",
               "fertile",
               "capture"])
    
    def flt(inwords,guess,num):
      out=set([])
      for word in inwords:
        if word!=guess and nm(guess,word)==num:
          out.add(word)
      return out
    
    def walk(inwords,lim):
      if len(inwords)==1:
        return (1.,1,inwords.pop())
      mm=[]
      for word in inwords:
        children=[walk(flt(inwords,word,nm(ans,word)),lim-1) for ans in inwords if word!=ans]
        ma=max([y[1] for y in children])
        mm.append((ma>=lim,(sum([z[0] for z in children]))/float(len(children)+1),ma,word))
      mm.sort()
      return (mm[0][1]+1.,mm[0][2]+1,mm[0][3])  
    
    def game_walk(inwords,lim):
      if len(inwords)==1:
        return (1.,1,inwords.pop())
      mm=[]
      for word in inwords:
        children=[walk(flt(inwords,word,nm(ans,word)),lim-1) for ans in inwords if word!=ans]
        ma=max([y[1] for y in children])
        mm.append((ma>=lim,(sum([z[0] for z in children]))/float(len(children)+1),ma,word))
        print mm[-1]
      mm.sort()
      return (mm[0][1]+1.,mm[0][2]+1,mm[0][3])  
    
    #words=flt(words,"joining",0)
    #words=flt(words,"barrage",2)
    while len(words)>1:
      word=game_walk(words,4)[2]
      print 'Try ', word
      print 'How many?'
      howmany=sys.stdin.read(2)[0]
      if howmany in "0123456789ABCDEF":
        howmany=int(howmany,16)
        words=flt(words,word,howmany)   
    print 'Its',words

There were at least two serious problems with B2x:

(1) The announced fork height was not consistent with the software.

This means that exchanges that snapshot balances at the fork height would likely have done their snapshots at the wrong block and ended up with an insolvency.  The software "worked" here but it didn't do what they thought it would do or advertised and in a way that would have caused some amount of losses. 

(2) The software as shipped won't actually mine a block needed to continue the chain.  It isn't unlikely that some miners attempted to mine B2X (all networks hashrate appears to have fallen) but their own nodes rejected the blocks they mined.

Their testnet suffered from the same problem and went over 24 hours without a block until someone manually constructed on to make it go,  the problem was [pointed out](https://np.reddit.com/r/btc/comments/6mgid5/btc1_testnet_irrevocably_forks_just_1_week_before/dk1scfj/) to them in public many times but they took no action to resolve it.

(2a) Their code to try to kinda handle this was broken due to failing to initialize a variable.  This is the kind of casual screwup that any programmer can make-- which is why we have review and analysis tools and testing, things that btc1 should have had but didn't-- but for some reason instead of acknowledging the screwup they tried to argue that there wasn't a mistake and that a primitive type of non-static duration was in fact automatically initialized -- an implausible level of incompetence with the language.  (This isn't the first time we've seen Jeff defend a common and understandable mistake by making erroneous claims about the language which, if earnest, would reveal horrifying incompetence-- previously he defended code with out of bounds accesses to a vector by saying they were range-checked.)   After realizing this screwup (but not acknowledging it) he grafted on a [hasty fix](https://github.com/btc1/bitcoin/commit/d09f3decfa2806515a0504be927c4384d6241dba).

The fix itself opens some interesting questions: They announced B2X was cancelled and that BTC1 would continue on as the "fedora of Bitcoin"-- but they never removed the hardfork from their BTC1 code, and in fact when the fork failed because the code was broken they went ahead and fixed it.   Is it possible that they announced the cancellation with the expectation that it would create an opportunity to buy cheap BT2 tokens and then have the fork go through anyways?   If not, why didn't he remove the fork from the software he distributes and why did he go ahead and attempt to fix the bugs that were likely preventing the fork from happening?

These two screwups likely complemented themselves-- it might be the case that miners would have manually corrected the deficiency in the BTC1 block construction code by manually constructing a block big enough to pass the rules... but they would have been waiting for the wrong height.

Cheers.

Yep, sounds like it.  Pretty easy too-- "here is 3600 BTC, mine B2X for 48 hours at a loss, and if B2X isn't more profitable by then we'll give you 100% of it, if it is more profitable then you already won and we'll give you 10% of it."

What they miss, or just don't care about, is that Bitcoin is of low value if it's open to arbitrary changes due to brief hashpower takeovers; so even if they win they lose.
There are some efforts going on, but the problem is that it's clear now that s2x will continue to adapt to evade whatever protections go in, to the extent that its possible for them to do so.  The problem here isn't technical.
LOL.

If Bitcoin's market position depends on segwit of all things than it has already lost. Please.  Segwit is good stuff, no doubt, but it doesn't hold a candle to the basic value proposition of Bitcoin which is autonomous money which is resistant to coercion and whim. 

This is hysteria.

I think a perfectly reasonable case can be made to use a different timeline for activation-- May of 2017 would give six months for upgrades. But two months?  I think that is absurd unless people and industry were already more or less unified on it, and they aren't (Unified on segwit, for sure but not BIP148).

The responsible thing to do is to fix it without calling attention to it, and if that isn't possible announce to everyone that full fixes (with source) will be available at a specific time so everyone can upgrade at once.

This is what practically every other high security open source project does, and what Bitcoin Core has done with success for years.
They are banned for 24 hours by default, there is no way around the bans except waiting.
> Satoshi's vision

oh god, please leave the BU dog whistles at home.

I find it utterly disgusting that people feel it appropriate to take Bitcoin creator's name to try to pump up their own dysfunctional proposals.  Satoshi also [wrote](https://bitcointalk.org/index.php?topic=1790.msg28917#msg28917) "Bitcoin users might get increasingly tyrannical about limiting the size of the chain so it's easy for lots of users and small devices.", and personally added the blocksize limit to Bitcoin. It's nuts to pretend that he wasn't aware of the serious trade-offs and challenges here or didn't care... and doubly nuts to belabor us with these tired arguments from authority. If Satoshi wanted to say something about the blocksize debate, he would, he doesn't need people to constantly stick words in his mouth nattering on about "Satoshi's vision".

> he did link to public records that listed him. so yea that was a dox.

No I didn't.

BitcoinXio finally they'd programmed the rbtc automod to surreptitiously hide my comments, I had been complaining about this for months and they kept denying it.  When I pointed out his duplicity I also mentioned that I already knew specifically what they were targeting-- even though they'd never mentioned it to me-- just based on the pattern of which comments were vanished and he responded skeptically "lol right".  Then I responded that it was any time I linked to the court documents relating to a hashfast guy's involvement in a scam mining hardware (which ripped me off, along with a lot of other people).

He is (no longer) a Reddit user, I believe he's fled the US (for matters unrelated to hashfast) though I'm not sure of it.  His identity was public and well known, disclosed by him. There have been numerous news articles written with his name and forum name; he was sued in California, forcing him to detail his involvement. etc.  Naming him is little different from writing Mark Karpeles.

Contrary to rbtc's claims reddit has NO policy against talking about people out in the world. Could you imagine if it did?  Every day rbtc is full of hateful things against myself and other community members, no action is ever taken.

But I post a commit entry from the Bitcoin repo to answer a libelous attack and I get [warned for "doxing"](https://www.reddit.com/r/btc/comments/5g3weu/blockstreams_creator_changes_bitcoin_network/dapfnlr/?context=7).  And then the moderator argues with me about it accusing me of "doxing" some guy that stole BTC from me and a lot of other people because I previously linked to the litigation.  That is absurd. Then banned me when I simply said the name in response-- even though the guy published it himself, and its been on rbtc many times in multiple coindesk articles... in court documents, etc.

Well they're free to ban who they want, but they don't need to lie about the motivations. 

Thats basically it.. 1MB 2MB. .. 3MB whatever, there might be harms but they'd be surmountable.   20MB. .. okay not clear but maybe just maybe it would survive with a lot of collaboration tough with a big hit to important properties.   But even at 100MB the capacity provided would be a joke in terms of worldwide retail use.

Meanwhile, L2 solutions rely heavily on strong reliability and decentralization of Bitcoin, especially the more decentralized/lest trusting ones. They can deliver the capacity to reach any amount of scale. But not if we first undermine the systems properties trying to scale in a way that can't get there.
My company is not directing or leading Bitocoin development.  We contribute to it-- unlike many Bitcoin companies but it only goes as far as that. Your words are disrespectful to the dozens of other contributors.
One of the potential advantages of schnorr multisignature (and MAST) is that 1 of 1 and multisig can be made indistinguishable. 

Thanks for the reminder about this privacy loss vector.
I didn't /just/ didn't though. Security isn't an accident.
I have had no communication with Mr Wright at all, let alone signed anything.  I understand that there is some information sheet Wright is giving reporters that specifically attacks me, however!

As a matter of principle I generally wouldn't enter into a confidentiality agreement. (And if a major, active contributor to core did agree to an NDA over something like this without at least discussing it with other contributors, I would be inclined to view it as really bad etiquette, at best-- how could I collaborate if I'm entering agreements to hide material knowledge from people I'm collaborating with? NDA or not, collaboration may create an obligation to disclose things to other contributors.)
I have to say that I'm surprised by the response here.

Privacy tech posts a month or so ago received so few net-upvotes that they didn't make it beyond page 3... and this post might end up my most upvoted submission to this subreddit.
Roger Ver (/u/memorydealers) must be pretty desperate to fake graphs like this-- the "2009-2016" graph is just the bitcoin cash mempool from a few weeks before.

Actual data from 2009 would look very different from both these charts.
People hate on Luke about that but he's being technically reasonable, even if what he suggests is politically impossible.  He does a pretty poor job of justifying himself, and I feel bad that the rest of us haven't chipped in on this point because we think his position is doomed.

In light of that, here is the justification for the 300kb figure he has advanced:

The blockchain is about 150GB today. It takes a long time to download and we know that the costs of syncing drives away node operations already.

If we assumed based on historical data that the bandwidth available to people will grow 17% per year (which is a pretty hopeful number which is inflated through the faster growth in better connected places),  then what we find is that blocks need to be  150GB * .17  / (144*365) = 485kb right now to avoid the initial sync time from going up. 

(I think he uses 300kb based on an earlier start time, different growth figure, or a safety margin).

It's a logically reasonable argument, but it's a political impossibility to convince people to reduce the blocksize now.  IMO we're better off investing our resources on better solutions.  But Luke-Jr doesn't shy from tilting at windmills, sometimes to good effect.


What he is actually unhappy about is that it is increasingly hard to pretend the Bitcoin project developers are going to along with the shock and awe push a fork onto people attempt.

You might note that he didn't respond to any of the discussion and just repeated himself on the PR.
Whats worse is that these claims were made on rbtc a few days ago, I debunked them and now they are up there again and I can't find the thread in the list walking back from new. (e.g. seems to be hidden).

In any case, someone with no clue how segwit works (e.g. EdDSA wtf?!) took some keywords and spammed them into a search engine. The patents have nothing to do with segwit, and a number of them are even expired.

It's a dumb argument to throw for another reason too: I have decades of experience creating tech in patent infested areas without running into infringement; afaict no one working on BU has similar experience.  Has anyone even done a search if their xthin 'enhancements' over CB or if their 'emergent consensus' algorithm runs afoul of patents?  I really doubt either do; but if BU did it doesn't seem like they know it.

I'm very interested in knowing how they got access to develop for SGX in secure mode with remote attest.  When I last attempted it, Intel was not eager to provide access to anything but the insecure debug mode for anyone outside Intel.  If they would have provided access we'd already be using it.

There are many cool ways to use remote attesting trusted computing to improve security for Bitcoin.  Peter Todd and I worked for a while on designs for private and secure micropayment banks a couple years ago: https://bitcointalk.org/index.php?topic=146307.0

Constructing a payment channel this way seems sensible, especially for small values.

I'm personally most interested in using SGX to take protocols which are already mostly secure without it and make them more secure, or mostly private and make them more private.  For example, many protocols have an opportunity for a user to behave abusively and jam the protocol but no funds can be lost.  With the trusted execution they could only perform those attacks with the added effort of breaking SGX. Another example is that a simplistic coinjoin protocol has a master who learns the input/output correspondence though blockchain observers do not-- avoiding that with complex crypto opens up many DOS attacks-- with SGX this final piece of the  privacy could be protected by the trusted execution.. People do these things today without the added strengthening of SGX, they'd be strictly better with it, and so if SGX is exploited they are still no worse off.  Or simple things like implementing velocity limits without a third party or making your transactions distinguishable (which doesn't even require remote attest).
> Afaik Core open position is SegWit only, and never hardfork/never blocksize increase. 

wtf man. Read [this](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-December/011865.html) and then repeat the above comment with a straight face.
> At no time did I advocate the BIP-62 solution to malleability

You specifically advanced restricting transactions to canonical form. This is precisely what BIP-62 attempts to do. You greatly underestimate the complexity of doing so.

No no, it's a pubic key ---- everyone who claims to have it ends up getting f*ked.

["damn autocorrect"]
Yes, it would be possible to do that. Candidate code is already written.
You've repeatedly made this accusation regarding me specifically-- but it contradicts [very clear historical evidence](https://en.bitcoin.it/w/index.php?title=Scalability&action=historysubmit&diff=14273&oldid=14112).  I guess I should thank you for not continuing to attack me by name, but before it was just wrong and easy to show it as such; now it just doesn't make sense. 

(I should note that "ripple" named in that link is the pre-opencoin system and has almost completely no relation to the system called ripple today; they bought the name. ... and when they announced their replaced altcoin centric and inevitably centralized design I went and removed mention of ripple from my old posts; but I can't change the wiki history)

I do have something to gain by being careful and thoughtful about the block size though, the same thing I had to gain in 2011 when I wrote the above link: I gain Bitcoin continuing as an interesting secure and decentralized system, an ongoing interest that is worthy of the worlds attention;  hopefully the same as you.  I start from the premise that Bitcoin is worth having-- that it's something better than paypal or some centeralized service (which could scale much better!) because it's decentralized. That is Bitcoin's _fundamental_ advantage; and we must take great care to not undermine it or allow it to continue to fade out-- as we've seen with mining where compromising a half dozen computers/companies could quite possibly rewrite the chain today.

This is especially concerning as we've seen the deployment of full nodes fade out and even large commercial operations become dependant on third party hosted node services; this creates huge systemic risk and undermines Bitcoin's value proposition.   I believe is an equilibrium where everything is good maximized and incentives are aligned, and I don't believe 1MB is magic; ... From the technology I believe more can be done, but when I look at most the existing decentralization indicators (node counts, hash-power distribution, service points of failure) we have the signs and trends are _not_ good; and it's a concern (fees are a good direction). -- it should be noted that this outcome was already predicted years ago (e.g. by Dan Kaminsky's comments that I was writing about above), but we've still slid into it.

I haven't seen basically any commercial push against block size increases so far; if anything the opposite: Gavin tells that other commiters that businesses are demanding it (we encouraged him to make their demands public instead of proxying via him); and there are many companies pitching investors with slides of transaction graphs and projections that don't actually fit in the network (or any network in some cases; e.g. I've seen projections that would require 10 GB blocks within a couple years!).

--- Rather, the only push you see against larger blocks come from strong advocates of personal autonomy and decenteralization, like Peter Todd; or the MPOE crowd (regardless of what you might think about them, they wouldn't be any to bend to commercial or government interests).  Most of the more technical people speak more quietly, because we're working in a space of great uncertainty and are intentionally trying to avoid an acrimonious public drama over what should be, or should at least start from, a more purely technical set of considerations; and there are many concerns both in the short and long term that people want to understand more completely.

**Craig Wright and Roger Ver continue to be lying scammers.**

    hash1 = 35276274818667493639870858105696011964501646089504265400638260064066045790163
    r1 = 76602264493055609546352457492233672226432472876392644088195065831077656398498
    s1 = 44787966467271907501798709439200918981933071263237808997779177186648644198558
    hash1 = 56221394971039521966342086441491239614866358925107283672430442980556369649125
    r1 = 57502262746895133191512722106916342143494973067084998704260393169087866183226
    s1 = 27702042657574315441268463341694174347772947234728482920477181325572949775024
    hash1 = 30605677882833158954241318759200890520217491289848804050147339699844519776211
    r1 = 872118261551659600699366420470617484117366157535805908303795948363318294351
    s1 = 82895296849827631295088862163759030634719798704010323733867415793283380446405
    hash1 = 47255070291855987878435086696022417229598267456341973881234040008614750340946
    r1 = 104598213308423969787598852743033984846849919010815005788446297044976684139179
    s1 = 12317568011071211590554995071523708350519635813182237607907906371371148955851

**Why waste time with block 9?   Real fake Satoshi's go straight for the key on the genesis block:**

    x = int('678afdb0fe5548271967f1a67130b7105cd6a828e03909a67962e0ea1f61deb6', 16)
    y = int('49f6bc3f4cef38c4f35504e51ec112de5c384df7ba0b8d578a4c702b6bf11d5f', 16)

    hash1 = 41387799130968378920543733691252593846391200292329399510790599411947409251597
    r1 = 70856214671587733404467315511570613744851007726796904729228710912531962912122
    s1 = 103181661192795679383124373087969918933542710074343165226189440668358243504533
    hash1 = 106393410968733121957411073939668195850350050189283511188870954227759694324133
    r1 = 48380846973019061895613844313262443227795327373517660703332329310178547702766
    s1 = 101176744139588718507727653432015754858190240763811836400167123068708405351863
    hash1 = 57208639329199970137845966961946949310859893225622592047598715117170393725658
    r1 = 79082009090345244075645747656442831015456806225097906816207711900998065483119
    s1 = 83587394837029955591270784411021207952097518147797452331091492235847611806830
    hash1 = 33044979258996521429149103495648406943512837366084274719133336827588858491481
    r1 = 9579784581751763092992451186168534579311092586069053692501394681792342383457
    s1 = 35795922641245423101733758557545822800599602794244101858659422380556345990724

These all verify with the script posted by CSW's [pet](https://twitter.com/dymurray5/status/1063523712182468611) RedHat engineer.
He means if everyone went along with it, it would have broken the network everyone was using. :)  By rejecting B2X we rejected a network wide failure.
The latter. In 0.15.0 we did a small B2X specific countermeasure, wanting to do more but were under time pressure and had little notice, and hoped that would be good enough for B2X and give time to do the more general things later.

Unfortunately, B2X intentionally undermined our protection, so we had to delay work on default-segwit-wallet to focus on getting the generic non-defeatable countermeasure out ahead of B2X.

> Dear Core, Please really take the time to consider 2mb

Segwit does 2MB. (up to 4MB) :( 

Segwit2x is 4MB. (up to 8MB)
The DOS mitigation / resource management techniques we developed in Core (which Bcash and S2X have copied, probably without understanding them) make extensive use of long lived connections to enhance reliability. The general idea is that it might take you a bit to find good connections but once you've got them for a while you hang onto them.  This makes it so that an attacker (or an overload) may disrupt newly starting nodes but this is less bad because a new node isn't in use yet and probably has an operator paying attention, but the same attack or overload does very little to a node with long established connections. This keeps the core of the network stable in the face of temporary churn.

But because of this you can't really change the topology all at once due to a sudden split, the topology needs to change advance to be near the one it's going to need to be well in advance.  Otherwise these protections stop working in your favor and even work a bit against your favor.

This was explained in the PR but the S2X developer responding there just seemed to ignore it.

You know the original release of Bitcoin had opt-in replacement, right?

So much for BU constantly Bleeting about "satoshi's vision"...
> Keeping quiet and very slowly getting back the respect that he lost. 

https://twitter.com/gavinandresen/status/819180346956742660
If segwit doesn't activate Bitcoin doesn't get the benefits of segwit and the subsequent features based on it.  Nothing more complex than that.

Weird question to ask at this point since so far its been the fastest deployed and signaled soft-fork so far...
Behold, rbtc top poster and major proponent of eliminating the blocksize limit view an immediate hardfork-at-any-cost.
Hi, "SamouraiWallet".  Lets get dispense with the adhomenem, then: I believe my Bitcoin Privacy credentials are above reproach, and here are _my_ concerns: 

After looking at the announcement and discussion on Reddit from the other day-- and the findings here (which you don't appear to be materially refuting),  it appears to me that this wallet has below average privacy (though a few useful thoughts!) and yet was billing itself as high privacy while failing to disclose its limitations.

I think this is unethical.  Good privacy is very hard. But even more important that the best privacy is being utterly honest and frank about the limitations. Phoning home to bc.i API and leaking the user's private information isn't just some mistake, it's a major functional design decision. 

You said the other day that if you were co-opted by interests that wanted to harm users, people would notice in your behavior, and stop trusting the wallet.

Is this true?  Lets see: You released a closed source wallet, that phones a third party API and reveals private user data. You did not disclose that it did this.  What are we to conclude from that?

I think this is indistinguishable from what would happen if the wallet were co-opted... and the fact that your response here is to attack back and make excuses rather than acknowledging a serious breach in privacy and communication does not bode well for the future prospects of this wallet as a pro-privacy tool.

What good does it serve anyone how much you'll wax philosophic about "going to jail"-- when the rubber meets the road it seems you are already showing that you won't do the right thing, even when the stakes are much lower. I hope you reconsider your approach.
Yep, moreover the vast bulk of the forum funds are from advertising income; not those donations for perks.  Another person, in another world, could have easily just claimed that as their own. The community is fortunate that Theymos chose another path.  Since then lots of funding has be spent on bespoke forum work that is supposed to enable some pretty nice (and rather unique) capabilities. 

WRT the recent security compromise,  ... the hosting facility, with physical access, restarted the machine to change the passwords and put the attacker on the console.   The forum has survived attacks more aggressive than many big businesses holding other peoples funds, even though is has basically nothing of value on it (that can't easily just be recovered from the offsite backups). 
I have no clue about what "arguing" you're talking about, but I'm cringing at the comments here claiming that improvement in forwarding efficiency change scalability.

Improvements to block forwarding efficiency does not change Bitcoin's scalability— at least if we use the fairly normal definition of scalability where we ignore constant factors.

What improved block relaying can give is _at most_ a constant factor of two bandwidth reduction over the stupidest possible block forwarding.  Less stupid forwarding— like that already used by P2Pool and Matt Corallo's relay network— already gets ~97% of the way there.

(In the last 288 blocks,  Matt's relayer successfully skipped 105662 out of 106815 transactions. P2Pool has successfully been using a similar scheme for years for its own blocks, after I proposed it there— it's just silly to send the same transaction data twice.  Some of the savings is lost in sending the list of transactions, because no extra work is done to make the ordering of transactions consistent)

Reducing the overhead of block forwarding is virtuous and avoids some perverse disincentives for miners— and its stuff that [I've been thinking about for a long time](https://en.bitcoin.it/wiki/User:Gmaxwell/block_network_coding), but it's would be inaccurate to say that it results in any inherent scalability change: All the same transaction data must still be sent before the block. All the same validation work must be performed, etc. The same tradeoffs in node operating cost vs scale— and the resulting pressure towards centralization exists ± a factor of ~two.

**Edit**: Now that someone has given me some context, I note that Gavin also wrote: "People claiming that "Bitcoin Doesn't Scale" are theoretically correct: you still need O(n) bandwidth and CPU to fully validate n transactions-per-second."  so I don't think there is any real disagreement— just some subtle technical matters that are getting confused on repetition.

I had just assumed this was because people trying to manipulate the market were linking to old ones. E.g. ones from last year saying things like "withdraws will resume Monday".

Maybe thats a bad assumption— but its much simpler than some other ones.  Obviously if they were trying to hide something deleting wouldn't work, twitter still has the data and will happily give it to a court, and it's mirrored all over the internet.
Yes... Rentacops get fired for screwing up like this.
Hogwash. He declined to speculate on a subject about which he is not informed enough to answer...  RMS has never been a major game developer.

At best he could tell you that Unix was once described as the kind of enormous undertaking that only a consortium of major institutions could create... and that even long before Linux existed the GNU project had managed to replace most of it.  So... /hand waving/ perhaps the same is true of major games.

Fortunately, he didn't give that answer because it would have been a weak one— we don't know if major games and Unix are at all alike. 

Instead what he gave you was the answer that works for him: If you don't choose to have big budget video games in your life then this is not an issue.  If that answer doesn't work for you— then perhaps your calling is to be the RMS of video games, the crazy dude that wouldn't take "impossible" for an answer and who instead of debating shit all day on the internet took a principled stand and proved that it was possible.
 



> Years ago, I spent ~9/10th of a Btc on a telescope. 

Ah. The better to see the moon with.
b328b94155ebf1a6bc250b2e34c31305be6b0ea9c31fd146f2ce44c4a052cdbb

/u/Craig_S_Wright  we can see you posting on twitter...
What was originally scheduled for 0.15.1 has been delayed because we're working on B2X network disruption mitigations.
It's nice to have your support, but not if it ultimately comes from confusion.

>  All I really needed was just 1 long blog post by Blockstream explaining why 1mb blocks make sense, how the team has been maintaining this since 2012, overseeing the most successful growth of Bitcoin, how larger blocks shifts power to miners. [...] Blockstream CAN be transparent about their profit motives, in fact THEY ARE on their website.

Blockstream has absolutely nothing to do with any of this.  It's just a company where a lot of people who love and support Bitcoin happen to work, and it plays no role in any of this beyond sponsoring some open source development. In fact, if the company attempted to exert influence it would be a violation of its contract with myself and other people there.

If you can't believe this, I can't fairly accept your support.

> I still don't think Jeff Garzik meant any harm

Do you [still believe that](https://www.scribd.com/document/362437500/Metronome-Media-Kit-FAQ) now that his ICO is public?
I think monero has a number of downsides which are unlikely to ever be fixed.  E.g. it has perpetual inflation which might become quite substantial after losses-- and other parts of the system depend on this.  This makes it fundamentally less interesting to me.

The specific cryptographic privacy approach in it also has fundamental scaling weaknesses-- though I believe that monero can probably upgrade once better tools are available, the requirement to do so means that its decentralization remains less complete. At the same time, I believe Bitcoin can also adopt better techniques when they're available.

The mandatory nature of cryptographic privacy in monero is a tremendous asset-- making it private in ways that other systems the claim to be private (like Zcash) can't match... but it comes with its own costs in scalability, good lite client comparability, and agility against cryptographic threats (e.g. quantum computers) that prevent it from being superior to Bitcoin, and in my view less valuable than Bitcoin.  ... but still an interesting alternative and far more valuable than almost all other altcoins (and monero is one of the only if not the only altcoin that I don't think is massively overpriced relative to Bitcoin).

I'm really bummed about bcash's relative failure.  They have their big block coin.  The fact that it's not a smashing success is not a reason to try to force us to take it too.
I think part of it is indoctrination in schools in the west (or at least in the US); we educate our children that democracy is some kind of holy ideal rather than a compromise that, while often the best available, is still terrible.  It blinds people to all the opportunities for personal freedom, where there is no conflict between interests and people can be genuinely free to do what they want.

Darwinian would bother me less if people weren't so prone to cry foul at every move.  "Stop! you don't need to save the system from collapse, *The Market* will provide!" ... "B\*ch please, what do you think *The Market* looks like? By choosing to act, I _am_ the market."
Not just metaphorically but literally.  Their willingness to come along for the ride means they've ceded their person autonomy and influence in these questions:  They're along for the ride and will step into line when the people who care about the properties that Bitcoin provides have won the day.
Beyond having done a pretty good job thus far, there is a selection effect--

Imagine you'd like to help out with Bitcoin engineering: Would you prefer to work with people who can review your work and help you improve? Do you want to do world changing engineering, or spend your time trying to convince people that its okay to run your software in the first place? Do you want to take on a super-humanly difficult task all on your own or work with people you can trust to take care of the parts you're not working on? Do you want the benefit of historical experience and context or do you want to repeat past mistakes? 

Bitcoin Core is a large public collaboration with people all over the world, none beholden to the others except due to respect and shared  interests.  It's hard but rewarding work.

If you go off and create your own thing, that says something about you and your ability to work with others, your understanding of the challenges, or your motivations-- and it may not be all that complementary.  There is honor in going it alone, but the work here is both too hard and too important for any single person to do alone.  Even people just cloning our protocols/designs struggle to keep up even though they have many solutions handed to them.

And so this is not that surprising that most of the fork efforts are either short lived or largely single person projects (or both); while the Bitcoin project is a vigorous collaboration.
Segwit _is_ that-- and then the goalpost moved from 2MB to 'unlimited' (or 16 or god knows what).
Blockstream does not have any patents, patent applications, provisional patent applications, or anything similar, related to segwit. Nor, as far as anyone knows does anyone else. As is the case for other major protocol features, the Bitcoin developers worked carefully to not create patent complications. Segwit was a large-scale collaboration across the community, which included people who work for Blockstream among its many contributors.

Moreover, because the public disclosure of segwit was more than a year ago, we could not apply for patents now (nor could anyone else).

In the prior thread where this absurdity was alleged on Reddit I debunked it forcefully. Considering that Rick directly repeated the tortured misinterpretation of our [patent pledge](https://blockstream.com/about/patent_pledge.html) from that thread (a pledge which took an approach that was lauded by multiple online groups), I find it hard to believe that he missed these corrections, doubly so in that he provides an incomplete response to them as though he were anticipating a reply, when really he’d already seen the rebuttal and should have known that there was nothing to these claims.

As an executive of Blockstream and one of the contributors to segwit, my straightforward public responses 1) that we do not, have not, will not, and can not apply for patents on segwit, 2) that if had we done so we would have been ethically obligated to disclose it, and 3) that even if we had done so our pledge would have made it available to everyone not engaging in patent aggression (just as the plain language of our pledge states): If others depended upon these responses, it would create a reliance which would preclude enforcement by Blockstream or our successors in interest even if the statements were somehow all untrue–or so the lawyers tell me.

In short, Rick Falkvinge’s allegations are entirely without merit and are supported by nothing more than pure speculation which had already been debunked.
Depends more on the broader community than the developers.

I talked to other developers in advance and they did not vomit all over it.
They ran BU 1.0 which contained what appeared to be unreviewed and buggy changes, which resulted in them constructing an invalid block (too large by 23 bytes), which the vast majority of the network rejected.

Other BU nodes accepted it and got themselves banned from the rest of the network due to relaying invalid blocks.

The (unintentional) hardfork attempt failed, block was discarded, and bitcoin.com didn't get the coins.
Yes sir.

Edit: And not tiny amounts either, for example right before I received block 440285 (last block produced by bitcoin.com) my node would have produced 0.92812075 BTC in fees, while the block collected only 0.67359653-- or about $189 lost income on that one block alone due to not running Bitcoin Core.

Seems fine to me, where do I sign?

The same [omissions I noted before](https://www.reddit.com/r/Bitcoin/comments/5c7620/someone_sent_me_this_draft_it_reads_like_the_bu/d9umhex/) apply, but ignoring that it seems fine enough.
One of the things that I found most painful about this to watch is all the people standing around and watching and all of the people walking by and doing nothing. Someone was obviously wrong here, people should have intervened... but it went on so long before someone finally did.

I know its human nature to leave intervention up to someone else— and that sadly, I'd probably do the same, but that doesn't make it right.  I hope that if I ever see someone in need of help I'll remember writing this message and take action, and I hope that all of you will do the same too.


I pretty much agree.  The main movers for that rename were Mike Hearn (who wasn't really ever very involved with the project, contrary to popular opinion) and Gavin--  and though we didn't know it from the time subsequently leaked emails suggest they may have already been planning to fork the project by then (Mike emailed the satoshi gmx mailbox saying they had been planing to do so a couple months later).

The primary argument for the rename was that it would avoid confusion between the node software and other Bitcoin software--  but it didn't seem to help,  e.g. Bitpay went and named their thing "bitcore" anyways, and this causes constant confusion. It's especially irritating because bitcore-node is based on a seriously outdated version of Bitcoin Core. As a result there has been more confusion with the core name then there ever was under the original name.

The naming has been brutally abused, and I think it was a mistake to go along with it.

That said, I doubt it would happen: there just isn't enough motivation to go and change it again. There are too many more important things to spend time on.
So perhaps the interesting question to start asking: When ETH switches to POS and no longer accumulates work, are they going to abandon ETH and switch to ETC?

Or does this idea of letting miners make arbitrary changes in direct contravention of the Bitcoin Whitepaper only apply to some systems?
> In other words, far more Core devs should be anonymous.

Many of us were very low profile for a long time by no accident, but when efforts to steam roll things started it seemed like the options were to speak up or bail out...
You have it backwards. They add a consensus rule where S2X will not consider any transaction paying 3Bit1xA4apyzgmFNT2k8Pvnd6zb6TnwcTi valid.

So you can make transactions that are Bitcoin only by paying them a small amount, and bloating the UTXO set.

There is no mechanism provided for creating a 2x-only transaction, though because they are making a hardfork they could trivially provide one.  Since this change also requires an incompatible change in their consensus rules, it's not like avoiding the hardfork saved them from taking a last minute rule change in their unspecified protocol.
bummer, and more with insulting the upstream whos work he depends on. This is really foolish.
> You can download blocks through the satellite, but you can't send transactions to it.

If you have phone service you can send your txn via SMS (transactions are quite small).  You can write or print your transaction on a post card and send it via the mail! You can use an existing terrestrial internet connection which is either too slow or expensive to otherwise use for Bitcoin. You can use an existing two way sat internet service which costs dollars per megabyte but a transaction only costs about 1 cent for it.

In the future we may have more directly integrated uplink solutions-- but there are many good options today.
Segwit is a 2MB block size increase, full stop.  This subreddit frequently makes a number of outright untrue claims about what segwit is or does. Signature data is inside the transactions, and inside the blocks as always. What is segregated is that the witness data is omitted from the TXIDs, which is necessary to solve malleability.  This in and of itself doesn't increase capacity or change load (except for lite clients, which are made much more efficient esp those that operate in a more private "fullblock" mode).  Capacity is increased in segwit by getting rid of the block size limit and replacing it with a weight limit which is less limiting.

The increase is somewhat risky because the system already struggles with the loads we've placed on it-- long initial sync times (running into days on common hardware people buy today; and only much faster on well tuned high end kit that few would dedicate to running a node);  creating centralization pressures by relay behavior favoring larger miners over smaller ones; and undermining the ability of fees to support the network (which Bitcoin's long term survival depends on critically; especially establishing the view that the network should not have a backlog when our best understanding says that its stability long term [requires one](https://medium.com/@bergealex4/bitcoin-is-unstable-without-the-block-size-size-limit-70db07070a54)), along with the general risks of creating a flag day change to the network. If this sound surprising to you, keep in mind that there is no central authority, no single bitcoin software-- many parties are on local or customized versions, forks of now abandoned software with customization. Any change has costs and risks, and if the schedule for the changes is forced the costs and risks are maximized.  I think there is a reason Satoshi never used hardforks, even when he was the only source of software and everyone just ran what he released and had few or no customizations. 

> I also don't believe 2MB is even nearly enough to "bog" the network

On what basis do you make this claim?   Keep in mind that the network has to be reliable not just on average, but always-- even in the face of attacks, internet outages, etc. To accomplish that there must be a safety margin. I believe if you generalized your statement to say "Simply changing Bitcoin to 2MB blocks would be obviously safe and reliable, even considering attacks and other rare but realistic circumstances" would be strongly disagreed with by _every_ Bitcoin protocol developer with 5 or more years of experience.  Measurement studies by [bitfury](http://bitfury.com/content/5-white-papers-research/block-size-1.1.1.pdf) a while back considering only block relay and leaving no headroom for safety suggested large scale falloffs in node counts would begin at 2MB, similar narrow work by a now ejected Bitcoin Classic developer and in a [paper](http://fc16.ifca.ai/bitcoin/papers/CDE+16.pdf) at FC gave 4MB for these single-factor no-attacks no-safety-margin analysis.  We've since made things much more optimized, which was critical to getting support for even segwit's 2MB.

These points are covered in virtually every [extensive discussion](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-May/007880.html) of the blocksize issue, and if you haven't been exposed to them while reading rbtc it's only because they've been systematically hidden from you here. :(  (e.g. comments like this that I write get negative voted which effectively hides them from most users not involved in the discussion)

Segwit mitigates the risks by being backwards compatible (so no forced industry wide flag day that forces people off their tried and tested software on someone elses schedule), by not increasing several of the current worst case attack vectors (UTXO bloat, total sighashing amount), by mitigating some of the scaling problems (making UTXO attacks relatively more expensive), and making transaction processing faster (by making sighashing O(N) instead of O(N^2)). Segwit also avoids creating a shock to the fee economics, since the extra capacity is phased in by users upgrading to make use of it.

While these improvements do not pay the full cost of the load increase-- nodes will still sync slower, use more bandwidth, process blocks slower), they pay part of it. Over the last six years we've implemented a great many tremendous performance enhancements, many just necessary to keep up with the growth over that time-- but we've build a little bit of headroom, so combined with segwit's improvements, hopefully if the increase too much it isn't by such a grave amount that we won't be able to respond to it as it comes into effect.  Everyone is hoping things go well, and looking to learn a lot from which parts of the system respond better or worse as the capacity increases from segwit's activation.

> aside from "it isn't necessary", which is debatable (considering fees)

I think what you're getting there is an "on top of segwit"-- meaning increasing the effective size to 4MB, which is really clearly not necessary, given that on many weekends we're dropping back to a few sat per byte, it's pretty likely that segwit may wipe out the market completely for a little while at least :( (a miscalculation, it seems).

>  Nothing broke,

Simply not true. They went ~29 hours without being able to mine a block because btc1 would not accept any block it was able to mine with its default settings, regardless of what was in the mempool.

The discussion on the PR also showed that many (most?) of the developers of the software aren't even running nodes on their own testnet... a testnet which only today has tried activating the fork, even though it's supposed to be deployed in a week.

Rather than facing the issues-- which were predicted with advice to use the 'hardfork bit' instead of a large block-- there has just been a wall of denial, they couldn't even manage to get a block created (which just took starting a node with an adjusted maximum block target size) for over a day.

FWIW, these issues would very likely have been resolved in advance if they'd even attempted to write systems tests for the new functionality. Now it seems at least a little unlikely that they won't fix them prior to production since they're invested in denying that there was ever an issue in the first place.
There is no information that exists concerning how many Bitcoins Satoshi has-- could be almost nothing, could be a bunch.

It is absolutely, positively, not "1 million Bitcoins"-- the way people came up with the 1M BTC number is adding up all unspent Bitcoins mined during the first year.  But we know, without any doubt, that some-- and probably a great many-- of those Bitcoins were mined by other people who since lost their keys. The system was public all that time, and had multiple users, so it's absurd to think that one person mined all of them.

About the only Bitcoins we really know were Satoshi's are those mined in block 9.

The coins mined in block 0 are not spendable by the design of the system.  And block 1 was mined hours after the software was published to the public-- the way the software was written is that it wouldn't mine unless connected to another node, so it's likely that there is no better than even odds that block 1 was mined by him.

There are various other theories where people have sketched out vague patterns in early blocks-- that suggest that many of them might have been mined by a single party but the problem there is that there is absolutely not reason to believe that even if they were, that this single party was Bitcoin's creator.

But people like a good story, so they'll reach to build one wherever they can. Truth be damned.
To fix it _completely and totally_ yes.  But this proposal is a softfork that specifically inhibits the form which is incompatible with improvements and only that form.

There are other possible soft forks that likely break the profitability of this optimization though don't quite totally eliminate it... but breaking its profitability is sufficient.
it's pretty straight forward,  you don't use it when there are other options, but when there are no other options, you use it without hesitation.  Fallout is not worse than being dead, duh.  "Do I pick an unfortunately POW changed network ... or a system that doesn't work?" -- not a hard question.

And this should be a powerful disincentive to attack in the first place.  The limitation there, like for MAD, is that it assumes a basic level of intelligence and rationality on the part of the attacker. Sometimes that is asking too much. :)
The best available research right now suggested an _upper_ bound of 4MB.  This figure was considering only a subset of concerns, in particular it ignored economic impacts, long term sustainability, and impacts on synchronization time.  That figure also left no safety margin for DOS attacks or attacks by powerful actors.

Segwith proposed 2MB worth of capacity-- making it broadly attractive to people by packing in scalablity improvements to significantly offset the costs and improve some of the externalities in the system (in particular UTXO bloat). It also gives basically the same capacity as the change the hardfork proponents were proposing.

And now the bar has been moved. 2MB was fine for classic and now it's 8MB-- the defenses against UTXO bloat totally flushed. Avoidance of a network split flushed. Broad support from technical folks flushed.  And for what-- are we any more "moon" ready with 14 tx/s than 7tx/s?  

No. The only thing gained it is that the it's extra sure to temporarily wreak the fee behavior, putting us back in a long term unsustainable mode of operation where the network is constantly flooded with spam and people get irritated when higher load periods cause them to pay fees again.

For many the purpose was _never_ to get more capacity, the purpose was only to divide Bitcoin: Nothing could make that more clear than their aggressive attacks on a proposal to achieve the capacity they asked for but also do it with objectively less risk.  If the concern were earnest then they'd be eager to take the increase we can all agree on, then we'd be in a better position to argue from its success that further increases wouldn't cause harm. (Here I am not speaking about you, to be clear)

If Bitcoin is going to have its rules liberally rewritten at the whim of politics and without regard to any attempt at science-- throwing out blocksizes _multiple times larger_ than we have any reason to believe is safe-- by whomever can stage the most aggressive disinformation campaign then it won't be worth anything in the long run.  That isn't something I'll stand for. Ultimately, this kind of recklessness is only having the effect of hardening people towards the view of no hardforks ever-- Bitcoin does, after all, work pretty darn well as it already is; something I think is regrettable but preferable to recklessly hardforking or doing so in response to coersive attacks. 

Continually harping on this stuff is terrible. It exhausts everyone who has worked so hard to keep the system going and growing, and retards the development of improvements (like signature aggregation) that will improve scalability without risking the system. If you want to argue for it or work towards that-- that is your choice-- but please don't harass other people to support such initiatives with their own efforts.

How the Bitcoin Core community deals with a bug we find out exists in BU:

https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/111

(We sent a patch and a polite explanation of the problem.)

How BU developers handle a case where they believe they've found a bug in Bitcoin Core:

An insulting blog post with an enlightening view on their thought process,

> However, I was unable to “weaponize” this exploit during my testing so I feel that there is little risk in public disclosure today.

The irony is that we wrote most of "their" codebase and endure outright abuse from their team including all manner of personal attacks and unprofessional insults... But we still treat them professionally, but it seems they will not return the favor even though they have benefited so greatly and so directly from our work. I find that more than a little sad.
0.1 was "Mt Gox's Cold Wallet"

0.2 was "Butterfly Labs' Timely Delivery"

0.3 was "Nakamoto's Genesis Coins"

0.4 was "Wright's Cryptographic Proof"

0.5 is "Bitcoin Savings & Trust Daily Interest"

Catch the theme?
Even I am not immune from network effects.

Beside, catch-22: If I don't you'll accuse me of being secretive and non-communicative. I'd take reddit over facebook any day.
Ross claimed he created it then stepped away, and all of the criminal activities he was being charged with were instead carried out by his successors.  Then, he claims, right before the arrest he was pulled back in.

Which all sounds a little implausible, indeed. But seeing the claims against FORCE and BRIDGES sort of resets the expectations of plausibility a bit, dontcha think? :)
Pft.  From the perspective of the Bitcoin ecosystem, Ripple is inconsequential. You're just giving them more free press.
To be clear, the 10x memory is referring to reducing a data structure from 200 kbytes to 22kbytes, not an overall reduction.

Likewise the 60x less cpu is a change in block handling latency, e.g. an operation that too 191ms becomes 2ms , not the overall CPU usage.
200kBTC has been transferred from suspected gox controlled addresses to new addresses, and then part of those funds has been transferred again and again, being broken into small pieces in a manner consistent with the MTGox software and operations for their online wallet. Some transactions spending coins involved in this are showing up in the MTGox api.

I would speculate they are preparing to bring their site back online in some capacityl

Sorry for the lack of links, this all follows directly from the transaction ID in the thread title and the other links I've posted.
Now that is something I can certainly sympathize with.  I thought people would find this interesting and when I went to post it on BCT, BCT was down. :)

I don't think it needs to be a the top of r/Bitcoin either, FWIW. :)
> If it was functioning perfectly, they would have noticed a problem with malleability after the first mutated transaction.

Yep. Exactly.

It's my belief that the reason they had the automatic reissue process in place which is what actually exposed them to losses was because of this long-standing immature coinbase bug causing them to author invalid transactions that get stuck for 24 hours... and, of course, you can't unstick them by making a safe reissue with the same inputs.   (Though I'm speculating that this specifically was the cause for having an unsafe reissue process)
This is is about Wikimedia UK— which is the UK wikipedian's user group.  They do things like organize efforts to improve Wikipedia's coverage of UK subject matter and coordinate with museums and archives in the UK. They don't run Wikipedia or any of its servers.

Wikimedia itself was made a US charity in 2005 [effective since 2003](http://wikimediafoundation.org/wiki/File:501%28c%293_Letter.png).
The FED has at least some amount of accountability, stated policies, decades of largely predictable (even if you wouldn't agree it was good) behavior and experience, some prospects of criminal enforcement for extreme misconduct, legal history and precedents, etc.

Existing centralized systems have a lot of infrastructure around them to mitigate their harms and risks, even if those protections aren't perfect.  Centralized "crypto"-share systems don't have that protective infrastructure-- so they're not just pointless, they have the potential to be much worse. They pretend to be decentralized systems that don't have the same need for that infrastructure but they aren't.

A centralized non-private "cryptocurrency" replacing cash would quite well make a lot of dystopian pop-sci-fi look pretty tame: How about an immortal crypto-share issuer monitoring and controlling your wallet for eternity? Even in "[in time](https://en.wikipedia.org/wiki/In_Time)" their currency appeared to be an anonymous and permissionless  bearer instrument where even their societies most powerful were unable to reverse a transaction. Imagine that world but with Vitalik reigning over his choice of "exceptional circumstances".
Interestingly, the moment B2X comes into existence it will have significantly more accumulated work than BCash!


Citation? -- I hadn't seen that.
Nonsense.   You seemingly barely understand what you plagiarized here.

You took hundreds lines of complex cryptographic code, including many paragraphs of English explanatory text  and changed it slightly and inconsequentially while you also completely scrubbed off the authors name and added your name in its place, for things you did not write.

Worse, you made the commit message say it was based on "EDDSA" yet it doesn't have much semblance to that especially since it includes the trick of constraining R to be even to avoid transmitting the sign, just as the original code (which "yours" is 99% identical to) does.

With the removal of the attribution you violated the license. You did so for commercial gain, as part of the work you were doing [for Bitmain](https://blog.bitmain.com/en/uahf-contingency-plan-uasf-bip148/).  This is technically a crime, in addition to being really sleazy behavior and a civil copyright violation.  It's also not the first time you've copied work from others and stuck your name on it, I previously [caught you doing this in BU](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/237#issuecomment-271694586). It seems like you just got bolder because you suffered no consequences there. 

As far as security goes: Your small change didn't actually change anything in the context of Bitcoin.  Previously the code computed e = H(R||m) and you changed it to H(R||pubkey||m)  in an effort to fix the first item mentioned in its [removal](https://github.com/bitcoin-core/secp256k1/pull/425), "lack of commitment to public keys"-- but in Bitcoin this change is a total no-op because the message in bitcoin includes the pubkey! (It was mentioned in the removal message because we'd also like the result to be a strong proof of knowledge for all applications and not just Bitcoin...) The vulnerabilities we were concerned about in the context of Bitcoin use still remain with that change. And had you used this to deploy signature aggregation (which is what most people are talking about when they say Schnorr in Bitcoin) in Bitcoin-ABC anyone would be able to steal any coin with it!

You could have started with an apology and made it right, but instead it looks like you're just denying that this was copied whole cloth. You must really think people are stupid.

Please remove the license violating code and stop falsely claiming to have authored it. You didn't write it-- as anyone can see, and making a couple formatting changes and a trivial change which is pointless in the context of Bitcoin doesn't authorize you to rip off the attribution from those who actually wrote it.

I estimate that will happen at roughly the same time as hell freezing over.

[I'm sure the network will increase to more capacity in the future, but 'Segwit2x' has been pretty universally rejected by the community; it has a reckless schedule, bad protocol rules that ignore many problem sources, and was unethically foist onto the community through closed door meetings and deceptive marketing (E.g. claiming Core supported it when it comes about as close to unanimously rejecting it as it's possible for the Bitcoin project to be about anything)]
I confess, I asked him not to-- but I was too late.

Not out of any kind of malice: a failure to increase the size does nothing-- it's harmless beyond some well deserved embarrassment.  But because people who aren't involved really do not appreciate how recklessly confused these activities have been.

"Their increase didn't even manage to increase." would have been a nice proof-point that doesn't require the listener first become a subject matter expert.
Yea, Bitmain announcing their intention to fork off is probably the best "birthday" gift I've had in some time. :)
Kudos for Janssens (who is apparently in charge of Bitcoin Classic) for being actually honest about this, finally.

Other people have been far less forthright.
Right.

It was vulnerable to a denial of service, which we fixed by requiring-- in addition to the opt-in-- that the feerate increase by at least the minimum relay fee with each replacement (thus the 'by fee' in replace by fee).
> I was under the impression that BU simply removed the blocksize limit. 

The problem is that just totally removing the blocksize limit is obviously unworkable to anyone with enough engineering chops to actually make the change-- you can't build software that can reliably work where some clown can just dump a zettabyte on everyone and force them to take it.

So every one of these HF proposals so far has had to do something more than just eliminate the limit.

XT replaced the limit with a limit that starts at 8MB grows over time, becoming 8GB in a number of years, via BIP101.

"Classic" replaced it with a 2MB limit plus some additional limits in the amount of signature hashing in a block, via BIP109.

(BIP109 was abandoned after segwit matched in a way that was non-disruptive, widely supported, and wouldn't split the network... and after it caused classic and unlimited to fork on testnet).

"Unlimited" replaces the limit with a new consensus process called "emergent consensus" where the idea is that miners will basically hashpower war with each other over the consensus rules. And nodes will allow the majority hashpower to override them (subject to some ill-advised hysteresis that can be exploited to create network partitions).

What Unlimited is trying to resolve is the issue that even among people who agree that a larger limit makes sense, it can be hard to agree on what that limit should be-- especially since the actual science driven results, suggesting that 1-4 MB is the practical limit, are not politically welcome to them-- instead they propose handing over control to miners.  They justify this on the basis of a misunderstanding of Bitcoin, basically an argument that miners already control it.  Where others would point out that specifically because miners don't control it we can count on them to perform their function.

Perhaps unsurprisingly there are some miners that are all for being handed more control. ... though ultimately BU would be bad news for them, making them far more attractive targets for coercion.
In defense of your class B.

> SegWit means miners can steal your coins with 51% attack.

They would never say that: BU doesn't even check signatures anymore if miners put timestamps older than 30 days on their blocks.

If they were concerned that a majority hashpower could steal segwit coins after a >2016 block reorg, then they'd certainly care that a majority hashpower could steal any and all coins with BU without a large reorg at all.

:)

> If the division grows, Bitcoin could be rendered non-upgradable.

Ultimately that is necessary for security, if Bitcoin keeps changing what assures you that it doesn't eventually change into something against your interests? 

There are lots of important improvements left to make to Bitcoin and it would be sad if they couldn't be made-- but Bitcoin's rules being shown to be truly immutable in practice would be a massive consolation and a great reason to feel confident about the system.
>  One such competition is ApplePay. When was the last time you saw someone pay for lunch with bitcoin? 

Bitcoin is inherently not a good competitor to applepay.  Bitcoin introduces a whole new monetary-policy-resistant currency-- it's not compatible with the USD you already own. Bitcoin uses a probabilistic transaction system which provides virtually no security without confirmation which can frequently take an hour (just to find the next block!). 

Layer 2 solutions can give good payments experience for sure, but that is just not what the Bitcoin network-- a global broadcast medium-- is optimized for. It is good to accommodate as many applications as fast as we can, but if we start changing Bitcoin to optimize directly to compete with applepay Bitcoin will never win-- doing so would be to play to applepay's strengths and against our own (sound money, autonomous, decentralized, censorship resistant).

Anyone who'd like using applepay isn't really calling for Bitcoin's strengths so wouldn't it be better if applepay supported Bitcoin-- and provided applepay's payment experience plus Bitcoin's monetary policy? -- sure it wouldn't have Bitcoin's security, but if someone was willing to use applepay at all, they don't care and perhaps quite reasonable.  If applepay goes nuts and freezes your mobile money, do you really care that you lost the small amount you loaded into it?  It would be better not to, sure, and in a future world there will be hopefully less of these compromises.  But it's critically important to keep our eyes on the unique differentiating advantages Bitcoin provides.

Using hashcash for digital cash is something that was proposed right from the start too-- but it had a number of challenges, as pointed out by Peter Todd in 2001 quite clearly: http://lists.ibiblio.org/pipermail/bluesky/2001-March.txt

> Personally I like the idea of hashcash if, and only if, it's
structured like a real currency as opposed to simply proof of work. In
the real world you pay for resources used. In many cases this should
also apply to P2P and other computer systems. Of course getting hashcash workable as a real currency is extremely difficult. I've thought of a scheme that would work (coins are signed by owner and can only be changed (signed to a different owner) by owner) except you need a decentralized "central" database of all the hashcash that's been minted. Unworkable. !@#$ spend-twice problem. :(

There has been some; aided in part by wallets rapidly doing integration work providing feedback on the effort (not much, fortunately).  There is a nice balance here though-- if people don't upgrade they don't get access to the space, if they need access to the space, they'll upgrade. Rather than trying to predict the future, the market can figure out how much it wants the space.

They let you prove to a lite client that a block is invalid, without them having to see the whole block (much less the whole blockchain).  This means that a lite client who is non-partitioned from an honest full node has something which is basically full node security.

It's an idea I've been working towards [for a number of years](https://en.bitcoin.it/wiki/User:Gmaxwell/features#Proofs) (before realizing the general idea is also in the @#$@ white paper, search for "alerts");  but only recently did we figure out a really efficient way of doing it that could be deployed in a soft-fork and as a side effect of deploying seg-witness.
uh.  A little confused there. The creators of the current ripple system bought the _name_ from the prior creator.  The system they built bares very little similarity to the original ripple design, this all long before the most recent change in ownership.

(This was quite irritating for me since I had to go edit dozens of forum posts to remove my advocacy of the ripple system when it was changed to be something else entirely, and[ particularly something that I couldn't endorse](https://bitcointalk.org/index.php?topic=144471.0)).
Hell if I know. 

For the first week there were good reasons I could offer: E.g. good cold wallet procedures might make doing so risky.   E.g. if the wallets are dispersed over multiple locations and require multiple keys, bringing them together to sign increases the risk that you get followed and kidnapped. Just too much trouble.

As the issues go on longer these costs start looking justified based on all the fear they could quiet.

OTOH, back when that move was done in 2011 a lot of people still argued that it wasn't real.  And without a way to prove their obligations people might FUD and say "thats only half of what it should be!" even if it's perfectly fine.


huh. As far as I can tell this is a hoax.  Unfortunately it's a little too close to the truth for comfort. :(

Oops, thats actually what I intended.  But you deserve the point for actually saying it! :)

Ahh... The old sofaking in the middle attack.
JPEG2000 performance is unimpressive at best.  "Wavelets" were all the fad in academic compression research in the mid-90s, but were only quite so impressive when compared against something far from the state of the art in 'old' DCT based schemes.

Take a look at [these comparison numbers](http://www.cbloom.com/src/index_im.html) for baseline jpeg (what almost everyone uses in their comparisons) vs arithmetic coded jpeg. 

Wavelets have a number of interesting theoretical advantages, but the distortions they create are not as 'perceptually friendly' as the ones created by the DCT, at least not on real images. Likewise, on photographic material DCT will tend to get better energy compaction than the wavelets in use. (Offset by it being possible to use much larger wavelet transforms, but then the perceptual wrongness of the distortion is even more obvious).  Not that wavelet schemes can't be good, but their advantages over more traditional methods have been widely overstated.

At high bitrates JPEG with the optional arithmetic coding (now patent free as of this year!) is pretty competitive with JPEG2000, at least when using a state of the art encoder (not libjpeg).  At low rates state of the art JPEG plus pre-and-post resampling pretty much beats JPEG2000 hands down, as JPEG2000 produces weird mixed texture/blotchy effects.

Amusingly enough using H264 intraframe mode (a more sophisticated DCT based compression format) to compress a single still image thoroughly trumps JPEG2000.

So, TLDR version: JPEG is good enough, Most people are using crappy jpeg encoders which are FAR from the state of the art, compared to the state of the are with jpeg classic JPEG2000 isn't even clearly superior,  it's possible to create a format clearly better than JPEG2000 using minor refinements on classic JPEG ... so why bother with JPEG2000?





Assuming that it was specified, mature, stable and wanted, and had all the implementation details worked out, it could be softforked in.

The script versioning introduced in segwit makes introducing new script upgrades especially clean.  BIP173 addresses already support new script systems.
That funding appears to be long before Metronome, I think it's just referring to funding BLOQ in general.  At the moment I think we don't yet have proof that Silbert knew about this; he could have been a pawn too.
rbtc supports anything they think will disrupt Bitcoin; that isn't new.
I think it is a crying shame that someone can write a bunch of bluntly untrue but "truthy" material like this and people will believe it.

"Flextrans" ignores decades of experience in cryptographic protocols by introducing a new and highly redundant encoding.  Encoding redundancy directly translates into vulnerabilities-- for example when round-tripping an encoding the hashes can change but not the meaning--, Bitcoin's transaction original format had a few redundancies which were the direct source of many of the the malleability problems in the first place.  The fact that a new format would introduce more is astonishing.  In doing so it adds needlessness degrees of freedom that increase the entropy of the transactions forever needlessly increasing the minimum amount of storage needed to handle them.

And the complexity and poor design of FT shows in the multiple critical vulnerabilities that have already been found in it.

> Satoshi had an annoying habit of using binary blobs for all sorts of data formats, even for the block database, on disk. Fixing that mess was one of the major performance improvements to Bitcoin under Gavin's stewardship. 

This is simply untrue-- Using binary formats is important for performance and efficiency and that hasn't changed, and sure as hell wasn't touched by Gavin.

Moreover, Satoshi's handling was not old fashioned.  Unlike Zander's code that manually twiddles pointers and parses (and happened to introduce multiple buffer overflow vulnerabilities), Satoshi used cleanly built serialization and deseralization methods which were clean and structurally resistant to coding errors. 

> anyone with a computer science degree minted in the past 15 years would do. 

You mean the way a javascript web developer with no experience in cryptography and network protocols might write it...


Imagine you run a toy factory and store. You are respected for the quality of the toys you sell, you take pride in it, you are-- of course-- a shareholder in your store and in several toy related ventures.

Someone urgently wants you to start carrying lead-paint spinners-- a fad toy made from melamine,  lead paint, and the tears of orphans in a package small enough to be a choking hazard.  You protest that the toy is toxic, and not very fun-- that it would be bad for the customer and other people whos children play with that customer, as well as your industry-- that it risks bringing all toys, or at least all spinning toys into disrepute, and that there are better versions which are safer on back order but will be available in a few months.

"It's only an option, only the people who want the lead-paint spinner would even be aware of it."

BIP148 isn't a lead paint spinner, but the same argument applies: no one is obligated to help something they think is harmful to Bitcoin, and they shouldn't be given trouble for their decisions.

Look-- no one is denying you the ability to get what you want elsewhere. But it is rude and disrespectful to demand it from other people who've looked at it and said it appears-- given the current situation-- to be a really bad idea.

If you want to see more support for it then by all means, make the case for it better-- drum up that support.  But don't trouble people who have chosen to not have anything to do with it.

The tech community in general didn't support foisting a network split on people when it came in other forms, and it doesn't support it when it is a change that they pretty much universally want.  The stability and security of Bitcoin trumps pretty much any technical improvement.
You don't need to look at the code to know this-- just look at their prior responses.

When we previously pointed out their xthin short IDs had a collision vulnerability and described how to fix it, they first denied that there was one, then claimed that it took 2^64 operations to create a 64-bit collision, then -- after I started responding to their messages with snarky remarks embedded in 64-bit collisions, claimed that it wasn't a big deal because it only added additional round trips (meanwhile, classic modified the protocol so that a reconstruction failure would result in a failed transmission instead of 'just' an extra round-trip... and no one seemed to notice/care that it undermined their argument).  And to this day the xthin and 'xpediated' protocols remain vulnerable for no obvious reason other than BU doesn't care about doing it right-- they were told about the issue, had it demonstrated to them, handed a solution... and did nothing but throw insults in response.

So what does that say about the care they put into their work?

Similarly to the changes they made all over their codebase to insert insults about "BLOCKSTREAM_CORE"-- changes which just make it harder for them to compare and import fixes from their upstream, while achieving no productive end but insulting and irritating the very people who wrote most of the code they are using and a lovely demonstration of their lack of professionalism.
I did answer. You asked if the disruption from a day ago is possible with segwit. It is not.  "would Segwit as it currently existed have made it impossible for what happened yesterday to happen?". NO.

Your abusive conduct is why most competent people don't use reddit.
Unfortunately, developers on that list have falsely accused us of that in the past, when in fact they just incompetently copied without reading. :(

Concern about false flag abuses was a major driver in getting the old alert system shut down as fast as possible.

Whats really bizarre is that they continue to copy while posting all over about how unethical we are.  I can't quite figure out if either they're dishonest about their views of our ethics, or just profoundly ignorant of how exposed they'd be if their beliefs were at all true.  Major dissonance there.

https://en.wikipedia.org/wiki/Witness_(mathematics)

A witness is a concrete example for an existential claim.

Say you stand up and say, "My security is protected by the mathematical formula  n^2 != 49" 

I reply, "no way man, you're insecure, there exists an integer n such that n^2 == 49".

You then say "I don't believe you, prove it!"

Now-- I could prove it to you in several different ways, e.g. with some convoluted proof that 49 is a square number-- but in this case, the easiest way is to just provide a witness:

"Try 7".

As such, a digital signature is often called a witness-- because it's the concrete value the satisfies an equality equation for the signature verifier. (In particular for things like EC signatures, the existential claim being proven is trivially true-- a signature for any message,pubkey pair is always possible, but only parties that know the private key can efficiently find a witness (assuming the crypto is secure)).

Sometimes there are many possible witnesses, and they're all equally good at proving the claim. e.g. -7 would also work above.

The notion applies recursively, a block is saying "I moved all these coins from here to there, and there exists signatures authorizing these moves."  The signatures aren't all that important except for the purpose of proving the validity of the move... once you know its valid you don't needed to worry about them anymore. They are a witness to the validity of the block. 

Originally in Bitcoin the witness part of a transaction were covered by the hash used to create a transaction ID. This has multiple downsides.. for example if a lite client want to verify a transaction is in the chain it has to check the TXID, but it can't validate the signatures (because its a lite client it doesn't have the scriptpubkeys) ... it cant use the witness data, but you still have to give it to it because it can't check the TXID without it.  Another is that property of there being multiple equally good witnesses means that troublemakers can change one witness in your transaction for another, the transaction is still valid but the ID changed, which means that any subsequent spends of those coins (which are referred to by txid) are invalidated when the second transaction confirms instead of the first.  So what segwit does is leaves the witness data outside of the TXID calculations.  Blocks still contain a hash of the witnesses because its important to know for historical reasons which specific witnesses were used (and also to prevent a number of DOS attacks).


The removal of a softfork is (generally, and in this case) a hardfork.  So all you are saying is that someone could create a hardfork that let them steal coins, but this is _ALWAYS_ true.  You could create a hardfork right now that steals all the unmoved coins from the first year.

Of course, nodes enforcing rules against theft of those coins would ignore your blocks, just as nodes enforcing segwit would ignore the blocks in your hypothetical. 

So here is how that would play out.  Your crazy miners would do their attack, upgraded nodes and every node and wallet connected behind upgraded nodes would ignore their blocks. People who hadn't upgraded would hurry around upgrading or moving their wallets/nodes behind other upgraded nodes. The attackers would suffer supermassive losses as their attempted forced hardfork failed, and the miners that weren't participating would enjoy outsized profits.
Not really: 

     $ ./bitcoin-cli getmempoolinfo
     {
       "size": 9918,   < Not under 1k!
       "bytes": 129561518,
       "usage": 239249968,
       "maxmempool": 300000000,
       "mempoolminfee": 0.00001379
    }

I don't think it's been under 1k for over a year. These webpages are all only reporting higher fee rate transactions for some reason --- presumably so that it's not just a flat line at ~150 MB. :P 

But yes, fee behavior is back to normal. The apparent flooding attack seems to be gone.
Sorry, but I believe you're confusing the issue.  We don't yet know all the details yet, but the overall gist of the event is this:

Yes, there was an invalid (v2) block. This is reasonable and expected. The lock-in threshold for the BIP66 soft-fork was 95%, so each block has roughly a 1 in 20 chance of being an invalid block at the time of lock-in. The probably of getting a three block run from 5% of the hashpower is one in 8000, 6 blocks is one in 100 million.  It's unfortunate that there was an invalid block, but except perhaps for zero/one conf transaction users (which are already taking considerable risks against ordinary reorgs; and presumably have other recourse or mitigations) the invalid block harms no one but its creator. (Though, as an aside, several people had sent out contacts in the last few weeks to that particular non-upgraded miner.)

In this case, however, it appears that a substantial fraction (roughly half) of the network's hashrate was, at least under some conditions, not actually enforcing the networks rules _at all_. This is a technique that has been deployed to reduce orphan rates in response to increased time to propagate and validate blocks (e.g. see https://github.com/bitcoin/bitcoin/issues/3658).

The result was that while 95% of the hashrate was signaling an intention to enforce BIP66 when 950 of the last 1001 blocks signaling that intent, but half of them did not actually enforce as soon as a single block existed that broke rank.  As a result what would have been a single block reorg became a six block reorg; it likely would have been larger except for active intervention (many of the technical community, including myself, had arranged their schedule to be available for the activation-- I've had to wrap my wake/sleep cycle twice in the last week since it's been "just about to activate" for about a week now: http://bitcoin.sipa.be/ver-10k.png ).   Fortunately, in this case no transactions were reversed in the reorg because the valid chain managed to include all the same transactions.   Effectively the 50% of non-verifying miners amplified the tiny hashrate of the invalid-block-mining miners.

SPV makes a very strong security assumption that the hashrate of the network is verifying transactions. At least under some conditions this assumption has proven invalid.  This is a particular failure mode I'd previously predicted and knew existed to some level (see above link) but I was surprised to discover that it was already half the hashrate.
Because "Bitcoin conferences" are by and large for profit events; even where they're not, people promoting Bitcoin competitors have plenty of reasons to push their way in front of an audience.

People working on actual Bitcoin are secure in their position and don't need to beg or buy their way to more public attention. Plus many Bitcoin events immediately fill up with scammy/seedy people. I can't speak for others, but there is a good swath of 'regulars' at those things that I prefer to avoid having any kind of association with...
I think all of us are tired of the rude and divisive treatment over this kind of minutia. (I mean, look at the responses I've received here?— we have "stop_lying_to_b_cool" single post reddit account accusing me of running my mouth too much and telling me to delete my reddit account... just because I pointed out the simple fact that none of the Bitcoin core developers are participating in this discussion, in contrast to what the headline might have implied to some. WTF?!)

I don't think any of us care too much about it and are fine going along with what people want, at least if it becomes possible to actually figure that out.  ... but thats also why you won't really see anyone of the actual core developers participating much in the discussion, presumably we'd all like to get something of actual substance done. :)
Yea, I have no qualms with other people using tools they find useful, indeed. Cheers.
"but the computer power necessary to solve the encryption is likely decades away"  

Hogwash.  To brute-force 256 bit encryption is getting into "using all energy in the visible universe to power a perfectly efficient computer and still not being able to crack it" territory. 

Of course— it's not actually that hard, there are undoubtedly weaknesses in AES which permit a faster than brute-force solution. But they aren't known. Should they are known then it may be possible a speak-and-spell could crack it.  Point is that your "years" claim is obviously bogus, computing speedups are easy to predict— cryptographic breakthroughs far less so.


It would be really good to have some more miners which are quiet.

The tube design used by S9 and Dragon Mint ends up being really loud, enough that it can be annoying even to have in a garage (depending on how acoustically isolated your garage is).
Even in "being reasonable" he seems to not be able to be reasonable: He just made a zero public discussion hardfork of the B2X consensus rules.  Why does believe he have the authority to just scribble scribble the consensus rules of B2X at a whim?

What he's done here is [moved back](https://github.com/btc1/bitcoin/commit/198d6f419bebd0e570f8f729e0b5773a1bda17d9) to the even less usable OPRETURN mechanism rather than _actually_ doing anything reasonable. But figuring out what the B2X consensus rules are going to be is like reading tarot because he has almost no public discussion nor clear process-- just direct commits to the repository without pull reqs, so we're mostly stuck interpreting B2X's eventual consensus from source code changes.

Reasonable would be to add true two-way replay protection-- but they are being very clear that they'll never do that.  A dim second would be what bcash did before they added replay protection: also support a new sighash style in transactions, which they can trivially do because they're making a hardfork, and use it by default.  This would also speed up transaction processing by eliminating quadratic hashing, and avoids any kind of stupid blacklisted outputs that will gum up systems that use Bitcoin like lightning. Supporting the bcash sighasher in Bitcoin Core for signing bch transactions takes a _[three line patch](https://github.com/laanwj/bitcoin/commit/22a4c47643203f86e03f4b001e776fcff1fe8d92)_, so long as you don't care about the hardfork that adding it creates.

'debate' perhaps not, but a relentless stream of constant misinformation: hell yea.

>>    the shock and awe push a fork onto people attempt.

> Just like uasf which you were a supporter of.

What part of "[[bitcoin-dev] I do not support the BIP 148 UASF](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-April/014152.html)" qualified me as a supporter of it?

I'm so tired of responding to the same untruthful points over and over again.

E.g. the first thing it argues that that segwit doesn't provide a know capacity increase because people need to choose to use it.

Well no duh, people need to choose to use more capacity for it to be used. That is what capacity is... And this very morning [miners were producing](https://blockchain.info/block/000000000000000000fc4f7efce8c42dacccf97b3ddb9a2b2cd1459c931371c9) <1MB blocks because their just isn't enough demand to fill that (much less 4x more!)  Moreover, the fact that SW adoption is gated by the normal slow software upgrade is not a downgrade over his hardforks: with his HF you also have to upgrade to use it,  and the process means that there can be a gradual increase rather than a system shock of going straight back to ~0 fees and then a system shock again as fees come back into effect.  --- reducing these shocks was a specific and intentional design goal in SW, one which his HF efforts have simply ignored.


> have zero direct sensitivity to block size.

In fact they do not-- they can directly observe the size of blocks by the size of the resulting hash trees they have it.  I previously asked if any of them had been tested (and to see the test plans) and only got answers of "there is no effect!". I agree they're likely to do okay, but that doesn't replace testing.  This is symptomatic of the general recklessness around these proposals:  Effectively "we're going to change things, against people's will, and if they don't like the results well I guess they'll get to fix it later."

> At large enterprises, there is literally a legally mandated de-risk’ing process 

Yes, and the development methods and timeframe of segwit2x make it physically impossible to meet best practice deployment.  They don't even have a correct written specification for their irreversible consensus rule changes-- even though they claim that they are so simple; don't even have a release of the software (only a release candidate), and yet they expect to have 100% of Bitcoin users upgraded to these rules within 90 days or be subject to exposure for funds loss.

For critical networking infrastructure it's common for operators to do months of their own testing on a vendors release before taking it into production; prudence which is physically impossible for Jeff's segwit2x even if all companies dropped all other projects and immediately began working on just this. The timeframe alone demands we collectively reject it.

By comparison, segwit underwent 6+  months of public discussion and review and operation on a test  system before being placed in any release, then had months post release before any possible activation to allow further testing, and this is for a change which is opt-in unlike Jeff's hardfork.

Jeff continues on with a deceptive unequal comparison-- basically comparing a plain Bitcoin Core user without custom software upgrading but he calls that the 'hardfork' upgrade, then he seperately describes a situation with someone unfortunate enough to use BLOQ's library software that in spite over a year of opportunity does not yet have segwit support (and doesn't appear to have started on it-- BLOQ has apparently been too busy working of mandatory AML/KYC wallets to bother keeping up with the Bitcoin industry) and basically describes all the complexity created by BLOQ's unmaintained software as segwit complexity.

His second case should have had an example for a user running their wallet infrastructure with Bitcoin Core or another maintained offering (like BitGo). It would be "test a new version, deploy a new version" like the above; but unlike the hardfork they can do it on their own timeframe, and use standard risk mitigation techniques like partial roll outs.

But what you're really missing by reading that isn't all the point it said but got wrong, but all the things it didn't mention:

It doesn't mention that frankensegwit ignores many attack vectors and performance issues and makes some worse, leaving them still [debating kludging on](https://github.com/btc1/bitcoin/pull/41) additional consensus rule restrictions to protect it. Another highlight pointed out by that that is that "segwit2x" isn't even a done deal-- the consensus rules for it are still changing and not clear, so how can it expect useful review?

It doesn't mention the process-- that segwit2x is the product of a closed door backroom agreement between a VC and several of its investment; which has actively sheidled itself and rejected community review and commentary-- which it [continues to do](https://twitter.com/james_hilliard/status/889196962406379522) even against the very people who wrote the consensus rules they copied; that it's success would bring serious doubt to the long term security and viability of Bitcoin as an autonomous system free of central control.

It doesn't mention that it has been [uniformly rejected](https://en.bitcoin.it/wiki/Segwit_support) by the public and open development community, and that most of us will not go along with it both as a matter of engineering integrity but also out of the principle of public involvement and transparency. The same people who have been tirelessly growing, protecting, and maintaining the Bitcoin system since 2010/2011.

It doesn't mention his last year of comments ranting against soft-forks (the only consensus upgrade approach used by Bitcoin's creator...) because the are 'opt-in by default', but how his proposal summarily closed [suggestions](https://github.com/btc1/bitcoin/pull/46) to use the "hardfork bit" approach to make his hardfork straight forward for lite clients to opt out of. ... a change would would have prevent the day long network fault their system had on testnet, the product of a (minor) design flaw that they've still failed to correct.

And so on...


I do, BIP-91 was activated to come in ahead of BIP-148 as is completely clear from the timing.

But ultimately any debate on that is irrelevant-- miners don't pick the network rules, users do. If miners don't follow them, their blocks get ignored automatically, virtually indistinguishably from having not mined the block at all.

This is also under current load, with segwit in place behavior should adapt to make better use of the capacity-- increasing it further.

And post segwit enhancements that are in the works (and soon to start getting delayed by lack of segwit) like signature aggregation will increase capacity further.

> It is likely these will weigh heavier on witness data, 

There I don't agree, the lightning stuff implemented today looks like it will result in transactions with average to perhaps below average (due to fewer inputs) witness data, AFAICT.
I just noticed this never made it onto Reddit:

Slides: https://cyber.stanford.edu/sites/default/files/russelloconnor.pdf
Yes, well aware of people's anti-decentralization cognitive defect here-- which exists even though Bitcoin is far from unique (who is the president of HTML?) :P

But my question was about something even more basic than that-- no one who works on regularly on core has said anything about POW changes AFAIK beyond the standard response which has been the response since at least 2011 that, DUH, if someone attacks the system using a bunch of hash-power the users will change the POW to something where the attacker doesn't have an advantage.  Which is a pretty profoundly boring statement: "Bitcoin users won't go quietly into the night!".

The propensity to take comments from random unrecognized people from the internet (or occasional contributors with well known fringe perspectives) and attribute to "Core" is a bit mystifying; even knowing that some people have a fetish for authority.
> FYI, we have contacted Core developers about a bug whose effects you can see as approximate 5% drop in Core node counts on Feb 23, 2017 and Mar 6, 2017.

That report was spurious: The vulnerability you reported existed in BU but no released version of Bitcoin Core, but thank you for reporting it.

I was shocked, especially considering your prior reports via public announcement that you were "[unable to weaponize](https://medium.com/@g.andrew.stone/a-short-tour-of-bitcoin-core-4558744bf18b)". Next time you have a suspected vulnerability in Bitcoin Core, it would be helpful if told us immediately instead of [discussing it in public](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/316) for 13 days first.

There are vulnerabilities in unlimited which have been privately reported to you in Unlimited by Bitcoin Core folks which you have not acted on, sadly. More severe than this one, in fact. :(

In this case, as far as I know Peter Todd is just repeating a report that was already widely circulated and was, in fact, [disclosed by your organization](https://github.com/BitcoinUnlimited/BitcoinUnlimited/commit/eee6a2daeb560f26061535695fc0f7de168ffe32). Am I mistaken?

These are changes that improve scalablity.
Putting "changed" transaction into a block doesn't matter. 

Segwit doesn't prevent changes, it makes them irrelevant.  Change all you want and the user doesn't care! This is the strongest form of protection possible and would prevent all disruption from things like the incident discussed here.

Many users don't care if their TXID's are changed, which is precisely what some people have exploited to claim that malleability is a total non-issue.  Many others do care, all those that care would use segwit or would change to using segwit once they had issues.

Bitcoin isn't your nanny. Bitcoin's job isn't to force you to do the right thing for your needs, its job is to give you the option to do the right thing for your need.
I agree.

Moverover larger blocksizes can actively hurt fungibility: They make it hard to use Bitcoin over tor and other anonymous communications methods, they especially make it harder to mine bitcoin with such tools. They create pressures to centralize mining, and centralized miners/pools are choke points which can be used to hurt fungibility. And to whatever extent fungibility improvements need more resources, those resources aren't available for their use if all the system can support is loaded up with transactions.

Fungibility and capacity are both key factors in being useful as money-- But Bitcoin will never win a capacity/low-fee fistfight against centeralized systems: they're simply better at those things. Being fungible, OTOH, is something Bitcoin can be better at than centralized systems.
thanks all.

Some advice, I suppose-- especially if you're going to speak truth to power in rbtc:

> In general we will not allow personal emails addresses, even ones on github accounts, because that loophole would be abused by less than honest users. We can deal with each case as we see them. I would also say that if email addresses are not very important to the post or comment then leave them out. It immediately raises flags with us on the witchhunt front if they are not important to the context. PI is rarely black and white!

I'm glad Reddit handles things politely and professionally.

/r/btc OTOH, well seems the response over there was to quickly try to get control over [subreddits](https://www.reddit.com//r/redditrequest/comments/5gzkol/requesting_the_subreddit_rsidechains_current_mod/) where I've been a moderator...

I previously sent MemoryDealers an email criticizing him for putting pressure on Reddit executives to try to take over /r/bitcoin, his reply: 

> The public is free to apply as much social pressure as we want to get Reddit to appoint sane moderators for /r/Bitcoin. No one is threatening anyone with any form of violence,  so I don't see what you think the problem is.

I suppose trying to get opponents banned from the site is no less not 'any form of violence' to his system of ethics.
This is misleading in the extreme.

In 2010 someone produced a contrived transaction that made a billion bitcoins out of thin air. Users applied a fix for that bug, and naturally the network reorged to exclude that transaction after a majority of hashpower was fixed. No normal funds were confiscated, the network wasn't hardforked. Old clients continued to operate.

In the second, unknown to anyone older software was unreliable at reorging when blocks were very large.  A miner started producing larger blocks and there was a block race, the vast majority of nodes on the network ended up rejecting the chain that that miner was on. But most miners were already on newer software and didn't. After it was clear that most of the network was rejecting the chain they were on, they switch to the chain accepted by most of the network. Again no hardfork, no confiscated funds, and older software continued to work.

By contrast there have been many large services that lost tons of coin (including mtgox's insolvency which was on the order of 10% of all existing coins), large losses in mybitcoin, bitcoinica and many other cases. No one called for a network bailout in these cases and if they had they would have been laughed at. These cases are the closer parallel to the situation with ethereum.
I think this is an uninteresting question.

Bitcoin was designed to remove the element of trust in monetary systems.  Unlike trust based systems Bitcoin is completely transparent and can, should, and must be evaluated exclusively on its own merits— its creators motivations are irrelevant.  Fixating on the creator of the system distracts people from paying attention to the system itself. 

Moreover, information about the identity of the creator would be harmful to the Bitcoin ecosystem.  All humans have quirks and flaws. People opposed to Bitcoin, and conditioned by trust based system to evaluate based on reputation rather than merit, would search out minuta from the creators life to distort and use to discredit the system. Scorned ex-lovers, kindergarten teachers, an unpaid traffic ticket, failure-to  or excessive participation in local elections, sexual proclivities, racial background... all invoked to make whatever points people wish to make. "Bitcoin's creator didn't have a degree from a prestigious university, and got Cs in school, so Bitcoin must be flawed!"

Then we'd have the risk of the public hanging on his every word— many institutions suffer [founder's syndrome](https://en.wikipedia.org/wiki/Founder%27s_syndrome) where the respect for their creator fixes them as a single point of failure in an organization which has long since grown past their ability to manage. The creator of the system might find it difficult to comment on anything without having his words micro-analyzed, and the purpose of Bitcoin might be undermined by centralization around the creators position instead of more equable processes.

Finally, speculation about the identity of the creator— when coupled with the (usually screwy, non-rigorous, and sometimes provably wrong) speculation about his net worth— risks putting people in danger for their lives.  When people think you have a billion dollars in difficult to trace assets that they could obtain by kidnapping you or your family it can be difficult to sleep at night. And people without these assets have the costs of defending against these risks potentially without having the benefit of being able to afford it. Every person you name as a possible creator of Bitcoin you are painting a target on, almost none of them have asked for this attention.

Ultimately this is pure tabloid material. It won't help us build a better world. Speculation on it risks causing harm. It is merely of idle and historic interest— but on the balance the harms and benefits, as well as the creators own wishes really suggest to me that thoughtful people should choose to leave this subject alone.

> and yet they still haven't fixed this stupid problem that is easy to fix, that everyone knew about

...and which causes 24 hour delays for some withdraws and which may have originally been the reason they originally had an unsafe automatic reissue process that left them open to theft.

Yep.
5) Are you qualified to determine when the claimed improvement over Bitcoin is an actual improvement or feel good marketing BS? If no, then avoid.


There are a great many highly non-neutral principles which are at the core of Wikipedia starting with the idea that the general public should have unsupervised access to knowledge, and that a community of unvetted contributors should be permitted to curate this knowledge.  Likewise, Wikipedia's free content licensing and its concept of "Neutral point of view" (the idea of converting opinions to facts and stating all significant sides instead of the "right" one) are all _highly_ political views.

SOPA poses an existential risk to Wikipedia. If Wikipedia contributors fear SOPA it will bias their editorial decisions, making Wikipedia into an advocacy platform for the censor de jure. Even if the authorities are savvy enough to not attack Wikipedia directly, by chilling the speech of Wikipedia's sources Wikipedia itself will be chilled due to the encyclopedic requirement of verifiability.

It's a simple and politically neutral statement of fact that SOPA is a hazard to Wikipedia itself and to the world of free flowing information that Wikipedia is trying to create.  And a site that can't even stand up and make that kind of statement isn't fit to survive.  

It's called a "preform", people sell them— google around. But you get ripped off buying them in single unit quantities.  You need to buy them by the ton in order to get a good price.

The first person I saw use Bcash was a fan, it was clearly just a better name than the mouthful--  also keep in mind that they'd originally announced the ticker symbol "BCC" which was ambiguous with Bitconnect (and seemed to cause a huge pump of Bitconnect), so abbreviating it with the ticker was initially out of the question. Moverover calling it "bitcoin cash" contributes to defrauding people who think it's bitcoin but not on a charge card or something.

For some reason though, when ver saw an 'enemy' use "bcash" he went absolutely nuts...  I have to say that its pretty amusing to think that I might have had a personal role in denying them the use of a better name simply by using it myself.

After the ticker was cleared up I made a concerted effort to use 'BCH' when talking to BCH fans because "why trigger the strange people unnecessarily?" though I hadn't been perfect about it because BCH is confusing to me. In particular, it aliases the many places we've been using [BCH codes](https://en.wikipedia.org/wiki/BCH_code) in Bitcoin lately, so it will always be Bcash to me internally...

Regardless, I was mostly managing to use their preferred name when addressing them, just as I'd try to respect someone who wanted me to use the pronoun 'it' for them ("odd, but it is its call")... Then Bcash split (okay, it had split previously but at least people were settled on calling the other split Clashic) and there was *again* ticker ambiguity, and even with it resolved what term should one use to refer to all the bcashes collectively?

I didn't find my effort to avoid needless triggering/insulting to be in any way requited, and now I'd have to remember to say BCH/BSV/BCHC (and probably soon BCH/BSV/BCHC/BUL...) when referring to them collectively so I've largely given up trying.

You clearly have no idea what "war" would look like.

Please. The disconnection there was necessary to keep the nodes from inadvertently DOS attacking each other.  Because their nodes are so vastly outnumbered their change harms them vastly more than it harms Bitcoin nodes-- though it harms Bitcoin nodes too.

They just kicked themselves in the balls (to use your term) in order to spite other people who are just looking at them and going wtf is wrong with these people. They had already admitted that blocking them did them no harm. 
It's hard to impossible to covertly move around very high amounts of data.  Small amounts, OTOH are basically impossible to stop.
No need to, they already screw it up enough on their own.

Unfortunately, that hasn't stopped them from falsely accusing us of trying to sabotage them.  (Shows a pretty low level of intelligence or honesty to both claims someone is super malicious and dishonest while you also continue to copy security critical software from them...)
Misses the potential that sw2x activates but creates a fork anyways because of false signaling or false triggering (variance causing a lucky run to look like a 80% when it's a minority) or hashpower changes.  Admittedly, I think thats a less likely outcome except temporarily. 

Dunno that bitcoin-abc even matters here, anyone can create an altcoin at any time; it's up to them...
> in an adversarial context.

In this case, it just self immolated. It didn't need an adversary.
Kinda, let me clear up some confusion:

Five days before the meeting Barry asked Morcos to forward on a message to a few developers which contained 'We agree to fork blah blah' text, signed by two dozen parties. It invited us to come to the meeting to sign this agreement.   People explained that we consider this private effort to redirect Bitcoin inappropriate and unlikely to be successful but there was no interest in any change in direction. We were also concerned of another HK agreement thing where developers would be blamed for the actions of others who couldn't decide what they wanted or immediately broke the agreement.  Considering that Bitmain is doing exactly that here, the concern seems to have been well founded-- and I'm very glad to have had nothing to do with that "agreement".

So in one sense you can say that we were invited, but it wasn't meaningful: we were invited to rubberstamp their agreement. Pretty much a big FU.  

Separately, Blockstream was invited to go to this meeting before any developers were invited. Blockstream expressed concern (based on the content of the invitation) that people would misconstrew (perhaps purposefully) BS's involvement as developer community approval; and (like the tech community did)-- expressed concern that the objective and process were the wrong approach.  After a significant amount of pressure Blockstream offered that we could send Samson-- since he isn't a developer, his involvement wouldn't have as much risk of misrepresentation, but he is very well informed about the issues--... then there was some on again off again and ultimately Blockstream's rep was specifically dis-invited which is what you were referring to.  Presumably because they would have rightly expected Samson to be critical and difficult to pave over and instead preferred that their ideas to not be subject to criticism.

They would have happily taken Adam at their meeting, and then [misrepresented](https://twitter.com/barrysilbert/status/873167754685292544) that as support from Bitcoin Core-- even though Adam has little to nothing to do with the project.  Similarly, they would have happily accepted a rubberstamping of their proposed agreement from developers; and so on that basis they claim we were "invited".

Perhaps that makes things more clear.

> Every on this sub was tearing them apart for publicly committing the fix

No. Can you even give one example of that?

People were complaining that they put up a fix that said right on the subject line that it fixed a remote crash, and then acted surprised when it was immediately exploited, then tried blaming Peter Todd (who didn't link to their disclosure until half an hour after the exploiting started), then instead of putting out an announcement about their own issue, put out an announcement that claimed there was a bug in Core, complete with fabricated evidence.

The normal practice in security critical open source software is that you make the fixes discretely in other changes,  and if that isn't possible you announce in advance a specific time when a fix will be published-- so that people can be prepared to update immediately (and shut things off if they can't upgrade).

Turns out that many things in Bitcoin hinge on technical details that people don't understand and don't care to understand.

Not understanding things is fine-- Bitcoin should work okay even for people who don't understand it in any technical detail... but it's only fine until some snake weaponizes you. :(

Yes. Segwit allows anyone who doesn't want their TXIDs to be changed by third parties, e.g. because its a problem for them if it happens, to choose to use segwit which isn't vulnerable to it.

Your bigger question shouldn't be about yesterday, it should be about tomorrow: Any miner can do this at any time and continue doing it. If segwit were active even if _no one_ were using it yet, they could all begin using it in response strictly limiting the amount of disruption possible.
That is useful feedback and not at all inappropriate.
It's great that BU can reject your membership and you'll still spend your time analyzing their crazy proposals... but perhaps pointless, because they haven't fixed them in response to your very serious breaks.

Bitcoin Core would welcome your review, you don't need a membership, and if you ever made a report like this about something someone proposed-- it would be taken deathly seriously.
> Gavin was left in Control of Bitcoin by Satoshi

Citation needed.
With over twenty years software development and open source experience under my belt I beg to differ.  Quality work requires a perspective and focus that is incompatible with remarks like that.

Consider the opportunity for blowback from doing something like that-- what else is someone who is ignoring that needless risk ignoring?

Simply the decision to purposefully irritate the people who wrote the software that you're modifying beyond what is strictly necessary is unwise. These are people that actually understand the software at a deep level and could potentially answer questions. The people being attacked here is a large community who contribute to Bitcoin of people not primarily the couple who work at blockstream; if BU had any sense they'd want those people to contribute to BU. But instead they exhibit a profound lack of conscientiousness. 

This pattern is repeated, when the Core community has found concerning issues in BU (in the rare cases we've looked and where they weren't BU's intentional design); we've reported them (sometimes with fixes) [through ordinary channels](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/111); but when they believed they found a bug with security implications they first tried to "weaponize" it themselves then blasted it to the world. -- a move that would have made users collateral damage, if they weren't wrong about it.

If someone behaving that thoughtlessly writes good software, its only by chance.-- that is not the kind of mental process that can reliably produce good software.
I think it's interesting to look back [to the very first post where I ever heard of Circle](https://www.reddit.com/r/Bitcoin/comments/29qafs/circle_ceo_issues_thinly_veiled_threat_of_hard/), which happened to be on Reddit.  I assume it was also where a lot of other people first heard of Circle because the comments on the thread indicate that the service was not yet available at that time ("invite only").

The email boxes I use for Bitcoin development have never had an inbound email from circle.com, not directly and not on any of the development lists. (Though I've sent some outbound messages to that domain...). I'm sure the same is true for all the other active developers.

I find it odd, but considering the history, not surprising.

I think it's also useful to look at what Andrew Morton has said about the development model of what is the world's most widely used operating system and one of the largest and widest used free software projects-- Linux:

> Instead of a roadmap, there are technical
guidelines. Instead of a central resource allocation, there are
persons and companies who all have a stake in the further development
of the Linux kernel, quite independently from one another: People like
Linus Torvalds and I don’t plan the kernel evolution. We don’t sit
there and think up the roadmap for the next two years, then assign
resources to the various new features. That's because we don’t have
any resources. The resources are all owned by the various corporations
who use and contribute to Linux, as well as by the various independent
contributors out there. It's those people who own the resources who
decide.
> can have segwit adopted

You don't see any of us gnashing teeth about it not being activated.

>  that different entities can have different opinions like in a democracy? 

Bitcoin isn't and shouldn't be a democracy. A majority shouldn't be simply able to "vote" to assign some hunk of everyone elses wealth to themselves, for example. Nor should they be able to vote to undermine the system's properties for others.

Coercion is unfortunate and should be avoided, just because something is a coercion by a larger group against a smaller one doesn't make it just. Bitcoin's properties should be robust even against a bit of popular whim.


> what percentage of full nodes you think will be running SegWit by then?

The earliest activation date is somewhat over 45 days from now.  By that point after 0.13.0's release more than a third of the network was running it, and it was a .0 which tend to have slower uptakes, and also less exciting. 

> Isn't it dangerous to let segwit activate when a minority of nodes still cant validate transactions?

No. (Also all existing nodes can still validate segwit transactions. They don't validate the totally new signature types, but they validate everything else-- no double spends, no inflation, consistency of locktimes). 

Please, someone shoot me if you catch me using "another era" to describe two months ago.
Some interesting points most people aren't aware of:

Significant portions of mining today pay little to nothing in base fees for power, but operate on revenue share deals with renewable energy providers in remote locations where they use excess capacity.

A majority of existing hashrate was purchased and put online in the last 6 months, using highly efficient equipment and deployed in anticipation of the halving.

Existing miners were operating at 66% of the current exchange rates just two months ago, presumably profitably.

Four of the last 5 (including the most recent) retargeting intervals have increased difficulty-- even this close to the halving hashrate is rising.

The latest generation of ASIC miners which just began shipping in the last couple weeks have 2.5 times the power efficiency and 2.5 times the footprint efficiency of the prior generation at about half the cost per TH/s.

At the prior halving new generation (ASIC) mining hardware came months after when it would have had no effect on the immediate post halving hashrate, rather than weeks before.

That isn't to say that there isn't plenty of opportunity for minor miner problems, e.g. I am aware of a substantial FUD campaign brewing around the halving in anticipation of some hashrate drop... but there are many other reasons to also not be especially concerned about it.

The halving is a planned, intentional, well known part of the system that all participants have been aware of. The prior one caused no substantial disruption, and I do not expect substantial disruption in this case.   Even if hashrate falls off significantly, the system will automatically adjust; and the worst effect is a period of congestion which most wallets handle perfectly well.

The subject of the article you're linking to there has nothing to do with the functionality in Bitcoin Core; and was written months before the functionality in core was even proposed.

There is a [nice FAQ](https://bitcoincore.org/en/faq/optin_rbf/) online about the replacement functionality that is implemented. You should check it out.

2.1 TB is 1/80th of your monthly bandwidth?  0_o

There have been some surveillance nodes that use a LOT of bandwidth with that useragent. Regardless of the specifics, in 0.12 you can set a daily upload limit as a blunt protection. You might want to switch to a release candidate of that.

I was [trying](https://github.com/bitcoin/bitcoin/pull/7093) to get the underlying privacy problem fixed for 0.12... but it wasn't completed in time for it. ... yet another causality of the environment.
**Who invented unconfirmed transaction replacement? Does it go against the "vision" of Bitcoin?**
I've seen this as one of the limitations of that particular proposal (and the general family of proposals that just hand control directly to miners without cost to them).

Unfortunately, the mining cartel can always reduce the effective size, no matter what any BIP says-- so there is an absolute upside limit on how much freedom from that cartel can be achieved (at least without invoking unspecified magic)-- their control in the upward direction can be divested but not in the downward direction. In the downward direction the best you could hope to do is provide a transparent, orderly, and (hopefully) as-equitable-as-possible method for them to coordinate in order to discourage the formation of informal coordination which might be much more user hostile (e.g. also involve them coordinating on redlists of coins that can't be spent).  To that extent I think the proposal offers something very useful, but it isn't an answer on its own.

Jeff argues that users can vote with their feet and abandon a system where the mining cartel is not behaving like they want, but I think that argument makes little sense and hasn't reflected actual behaviors.  Leaving the system isn't a free choice, it involves large costs and frictions, and if it were to happen at any scale would be a thundering mess that creates large losses for many users. Of course, the hypothetical cartel knows this too.  An uncharitable spin on it would be "We don't have to worry about miners cartels because people can just stop using cryptocurrency" which is kind of like saying that you don't have to worry about falling to your death because you won't care after you're dead. Might be true, and yet it's not so helpful. :)


Check the date on this: http://people.xiph.org/~greg/21mbtc.png
This was very hard to test for.  Consider:  OpenSSL (or anything else) does not implement the complete BER specification.  This isn't surprising or dangerous.  So a differential test must be used, at a minimum.

If you feed DER specification test vectors into an implementation you'd learn nothing as the problem doesn't happen there.

If you feed completely random inputs you will never discover the discrepancy; because random inputs are not valid signatures and so fail on all platforms regardless.

If you feed randomly corrupted valid inputs it will take many billions of tries to reproduce, even with a very fortunate random mutator. Simple bitflips will not suffice, the mutator must insert and change exactly the right bytes. With billions to compare that creates significant constraints on the test harness (e.g. can't just serialize out a set of test vectors).

Etc.

With knowledge of the issue I constructed a special test harness that queried both instrumented OpenSSL and instrumented libsecp256k1 (whos DER parser was intentionally much more restrictive than even 64-bit OpenSSL) with a generator that started with valid signatures and verified that both implementations agreed. This went as far as using specially constructed valid signatures which were very short in order to give the fuzzer a greater chance of success (e.g. the smallest valid signature is ~8 bytes, using some algebraic tricks to construct the pubkey from the signature). Using this harness AFL took multiple CPU day to reproduce the result (this was almost a year ago, and AFL may have improved-- but that doesn't change that its multiple-billion-to-one-odds on almost any way of hitting this-- though it also might have gotten lucky).  So in some sense it is _possible_ to find it via other mechanisms than review; but it took powerful tools with a fairly "fortunate" test harness.  My goal there was to try to come up with a harness that would find this class of issue without baking in too much foreknowledge in order to gain more confidence that there were not additional avenues of failure which we didn't know about. Unfortunately, the required harness was contrived enough that the level of confidence it gave me, personally, was only so-so. 

( As an aside; we did find a vulnerability in OpenSSL via a somewhat specially constructed randomized differential test without knowing about it in advance: https://www.reddit.com/r/Bitcoin/comments/2rrxq7/on_why_010s_release_notes_say_we_have_reason_to/ )

Further evidence that more random testing was unlikely to solve it is that two months ago [Jonas Nick](http://jonasnick.github.io/blog/2015/05/09/fuzzing-bitcoin-consensus/), without knowledge of the vulnerability, used AFL fuzz to verify agreement with Bitcoin Script between Bitcoin Core and alternative implementations. His work turned up several consensus breaking flaws in alternative implementations, but even though those implementations were also consensus inconsistent with the signature parsing of Bitcoin Core it did not turn up this issue.

My general experience with most cryptographic software is that the level of review and testing is not significantly greater than other Free Software and may well be much less-- because people expect it to be rocket-science. Our testing in Bitcoin has turned up quite a few surprising behaviors and bugs in the things we differentially test against. It's a difficult problem, but I too am pleased that Bitcoin creates more motivation to create more secure software-- though I'm less than pleased with the current state of the world.

With respect to bounties, the amount of work required to review this sort of stuff is utterly enormous. And even if you are talking about a fairly bug bountiful codebase the success rate will be very low. As the amount of work thats gone into it so far drops the success rate drops further. Meanwhile, people with the experience to do this work can happily earn a good six-figure income warming a desk someplace at a regular job. So bounties could be astronomic and still be a bad marginal choice vs other ways of making money.  Small bounties also often have a paradoxical effect of shifting peoples motivation from the intellectual thrill of success or the respect of ones peers to a purely monetary basis, by which the deal doesn't look so good-- thereby discouraging participation.  It's a hard problem. 

One of the things we've [explored doing for libsecp256k1](https://github.com/bitcoin/secp256k1/issues/180) is having a contest not to find bugs but instead to attempt to _add_ plausible bugs that the existing tests don't find; of course if you find an actual bug you win too. This is a fundamentally easier challenge and has substantial opportunity for success even if the software itself is bug free.

I am sorry for the offensive tone.  But your analysis was wildly off the mark and being politically spun on Reddit, where it is misleading people and will leave a lasting misunderstanding; and I was honestly offended by it. (And I'll probably be correcting misunderstandings created by it for years to come! :) )

This isn't the first thing you've posted which has shown up here where presentation obscured the lack of scholarship in the effort. I can't blame you for how other people politically abuse your work-- but perhaps you could refrain from blaming me for being irritated by it?

> You know, I've been on your side in private conversations where people are questioning your motives. But with a spiteful reply like this, I'm beginning to think u/raisethelimit was right: http://imgur.com/DF17gFE

Because everyone knows that when someone gets irritated by misinformation being obfuscated by charts and figures and printed as fact and repeated by people who don't know better it really means they're an unethical actor.  Honest people of integrity don't bat an eye at such shenanigans, anyone who does is an "obstructionist".  Do I have that right?  What other conclusion should I draw from you directly attacking my motivations when I got irritated by incorrect claims?

As far as "beginning" goes-- you were [sharing that same link a week ago](https://bitcointalk.org/index.php?topic=68655.msg11759726#msg11759726) as well as [similar allegations](https://bitcointalk.org/index.php?topic=68655.msg11627743#msg11627743); so you pointing out that that my moment of irritation changed your opinion and prior support is quite surprising to me!  I likely wouldn't have been as sharp if I wasn't already aware of those prior comments, I apologize for pre-judging your actions somewhat.

But even here where I point out that your analysis was off by orders of magnitude even in the most charitable interpretation your responses is a character and motivation attack rather than a "crap, I got that wrong"-- in fact, you've just revised your figures on BCT to claim that verification was even slower than your initial numbers (e.g. in the opposite direction of measurements I told you how you can perform for yourself).

You say that it's just a casual effort--; if so, why do you defend it with ad hominem when I point out that the concrete figures you gave are wildly unsupported by actual measurements, or that de-simplification of the model eliminates its applicability?   The page full of equations and graphs certainly give the casual reader a different impression. That you posted it to cypherdoc's "Gold collapsing" thread basically guaranteed the work would not receive any serious critical analysis-- if it wasn't your intention perhaps you should keep in mind that sharing a work which confirms political preferences with a largely non-technical crowd is not likely to bring useful additional criticism.

I really can't afford the time to go and rehash the days discussion right now-- I basically spent the entire day with my reddit inbox 5+ deep on every single page reload.  :)  If some people want to go to the other threads I commented in today and yank out some quotes/links that you thought were the most interesting, that would be both useful to me and probably to other people who are seeing this thread and not the others.
We have! on bitcoin-development.  Heck, Gavin's own figuring had an arithmetic error (didn't count upstream bandiwdth) and even by his analysis-- which assumed state of the art top percentile bandwidth (e.g. service that isn't available to me at home personally, much less much of the rest of the world)-- he said his number number should have been 8MB.

(There was also just a plea on the mailing list from one of the largest pools, unprompted by anyone; saying "But we cannot
handle 20 MB blocks right now. I know most blocks would not be 20 MB over night. But only if a small fraction of blocks more than 10 MB, it
could dramatically increase of our orphan rate, result of higher fee to miners. Bad miners could attack us and the network with artificial big blocks.").

Several years ago, in one of these must-grow-now-at-all costs Mike Hearn argued that the system would be just fine with only 50,000 reachable nodes (we had much more than that then).  We're now under 10k and much closer to 5000 nodes; yet no concern.  That is part of the problem,  the numbers are useless when people will happily revise their thresholds to call whatever the number are fine (or not fine).

Most of the concern isn't around just operations-- we've fixed most of the lowest hanging non-linearities in node behavior. Bitcoin core on my desktop, with a few things fixed, can "handle" multiple gigabyte blocks, but that doesn't mean that a decentralized system can be formed out of it; that anyone would run it, or that I can get the bandwidth to support it (I couldn't, not with normal broadband service). As the block sizes has grown the node count has almost monotonically fallen in absolute numbers even while the user base is orders of magnitude larger, and even though the software is many times faster-- the improvements so far have not been enough at the current load. Separately, Without a fee market we have _no_ coherent answer to long term security, a fact also pointed out in academic research (which has happily been ignored).

These are also not mere hypotheticals... in 2013 as blocksizes grew  mining became incredibly centralized with almost all the hashrate being pointed at just a couple large pools in order to mitigate orphan rate; this actually enabled pool compromise theft on the scale of 1000 BTC from a Bitcoin using service. Matt created the block relay protocol to mitigate the bandwidth mediated pressure towards centralization and it has helped push back the clock on that; we already know from experience how the system responds to scaling pressure: it centralizes because centralization directly, immediately, and without any coordination divides the scaling costs. Centralized is the most _efficient_ configuration of the system.

WRT soft limits, it's tricky, some of these concerns are entirely about a conflict of interest between miners and the other node operators or between large centralized miners and small independent ones (or new entrants), or are about market failure modes among miners in general... so for those concerns letting miners control the knobs doesn't address the concerns.

I agree that we need more understanding and research; but this is poisoned by the continued claims that there aren't substantial risks or fundamental tradeoffs; a position that is false on its face and makes the whole discussion seem unlikely to be productive. Obviously 1MB isn't some magical value which will always be right forever.
The comparison there is a composite, it's compared to HE-AAC at the low end (which is the appropriate codec for the AAC family at that rate). The graph doesn't go lower in part because we don't have listening test data lower.

FWIW, HE-AACv2 is not considered by many people to be an improvement over regular HE-AAC. HEv2's parametric stereo causes motion sickness for some people.
Many moons ago, back when computer sales was a profitable business, I worked in a computer store. We often had customers who got back to their office after picking up a machine which was in for service call in to complain that it wasn't working.

The cause of this was pretty much always the fact that at the store we'd turned the computer off via the switch on the computer (often on the side or back) while they normally turned the computer on/off via some power strip.  Actually trying to troubleshoot this in a straight forward manner was often impossible because they'd insist that "but it's already on!" even when you were telling them there is a second switch.

By far the easiest and most reliable way to resolve this situation was to instead instruct them to turn the relevant switch _off_.   


I get the impression that cobra sold his credentials last year:   He put up some sketchy warnings about the binaries on bitcoin.org then went quiet for a long time.

When he came back he started posting some really over the top rbtc conspiracy theory nonsense on reddit.  When people moved to take action about it he suddenly said "oh my account was hacked" and dropped it. But the account wasn't use for the kind of petty vandalism that you normally see when a hacked account can't otherwise be used...  Since then he's been slowly cranking up the psycho behavior, and right now he seems not far from the sudden behavior of the 'hacked' account.

Given that I'm not surprised to see the BCH pumping, and of course ignoring that whatever "better for payments" argument you can currently make for BCH could better be applied to Litecoin (which also has a lower interblock interval, AND segwit) and yet litecoin has mostly gone nowhere.

FWIW, no one wants a POW change more than Bitmain.  They crank out chips privately for even obscure POWs then dump them on the public once they've reached diminishing returns on their own production.  With sha256d they're competing against a huge installed base.  Moreover, Bitmain has gone around unethically and unlawfully claiming patents on basic mining techniques like series wiring the chips to reduce convert costs which were in use prior to Bitmain and where _any_ competitive mining device for any POW would adopt the same techniques.

> he migrates to its spin-off, bitcoin cash (BCH). “I have in fact sold all my bitcoins recently and switched to bitcoin cash,”

From that date until now Bcash's price has dropped by almost 60%.  

For comparison, Bitcoin's price from that date until now is down 42%.

You seem to have a strange definition of "called it".
Your post doesn't make sense: btc1 nodes will (slowly) ban Bitcoin nodes as they relay blocks past their mandatory fork point.  They make no effort to avoid not doing so.

Their change just makes it so that btc1 and bitcoin nodes mutually waste each other's connection slots and are likely to accidentally isolate each other. 
Bingo.

And FWIW, writing something as overarching as that made me feel pretty uncomfortable and I spent a long time making sure it reflected the views of many contributors first.

Since it wasn't successful in its ends (basically, a working theory was that our efforts were being disrespected and disregarded because we weren't presenting them in a cohesive enough way for a total outsider to determine that they were going on) I doubt I would do something like it again.  It did a lot of damage too, feeding a number of these ignorant command and control narratives.

But even still-- it was stated as a personal view and personally supported by many others; it wasn't a statement of the project.  Only kind of statements from the project you should expect are release announcements, urgent security notices, and similar. 
BIP148 is a good idea if and only if it gets sufficient support. Otherwise, it is a bad idea as I've written about many times before.

For example, if a supermajority of hashpower went join it-- it would go okay.

Similarly, if adoption from users (esp economically significant ones) is overwhelming it would likely go okay.

As _the_ party currently opposing segwit Bitmain has an almost unique position of being able to make BIP148 a low disruption success more or less on their own.
The post you are responding to does not mention Swgwit.  It does mention BIP152 compact blocks, which is basically running on the entire network, with  orders of magnitude more use than xthin.
I think this is off-topic for /r/bitcoin.  I don't think anyone here cares what BU does.
inb4 Ver endorsing satoshi dundee patents.

Thank you.
AFAIK, I've responded to every message you've sent me...

In any case, I was really pleased to see your merged mined "classic" proposal.  Do you need help getting that implemented?

I think your vision of a datacenter node cryptocurrency isn't viable-- in that I believe without the strong preservation of personal autonomy that comes from a P2P system the result won't have a lot of value over centralized ecash, but I fully support your efforts to try for it.

I don't want to debate you about it, we disagree profoundly on the end goals and each of us should be free to support what interests us without disruption from the other.
> But don't you kind of decide what gets included in the final release?

Nope!  I decide what I work on though, and obviously things that don't get done can't be in any release.  I also review other peoples contributions and help them get them done.

This whole thing where I got described as running the project is an absurd lie spun by Mike Hearn, I think with the express intention of chasing me out.  I mostly work on more far flung things than releases-- things like BIP32 public derivation, CoinJoin, CT, efficient block transfer, ultra low latency block transfer, mempool synchronization, transaction compaction, zero knowledge payments, yadda yadda,  plus review work, testing, and bug fixes.

> If this is indeed correct it might be time for an updated version.

Without seeing the effect of segwit's capacity doubling and load increase it would be reckless to work further out in the direction of bandwidth increasing capacity changes.  And the aggressive attacks also make this kind of planning extremely unappealing to work on in any case.

There are a number of other things already in the pipeline, e.g. signature aggregation, but many are dependent on segwit.
Interresting that you report this:  [The software implementing this has not been merged yet](https://github.com/bitcoin/bitcoin/pull/9594), and the alert that is part of it will not relay through the network. So to see that you would have to be directly connected to someone who is spamming you with it. (or, you're not actually seeing it... because someone named bu_ftw isn't actually running Bitcoin Core 0.11.2...)

The text in the final alert that blocks all the others is hard-coded in the receiver (your old software) and can't be set. There has been a more descriptive alert up for months, however, which sends people to https://bitcoin.org/en/alert/2016-11-01-alert-retirement ...

As an aside, Bitcoin software that old has numerous vulnerabilities... running it is something you can do, but it's not really recommended. You can reduce your exposure and get rid of that message by setting the alerts=0 option.
Bitcoin is already a winner.
Unfortunately fees are hard to do anything with, any mechanism which isn't fee blind can be easily gamed.  E.g. I can put a high fee on my transaction and then miners can pay it back,  or I can put a low/zero fee and then pay fees via an output instead.

There have been a couple proposals which I think are neat, but I think people are holding off publishing more because the current climate where _everything_ gets viciously tarred and feathered makes it pretty unrewarding to propose anything.

Greetings, thanks for taking the time to write.

Your message surprises me:  Before the scheduling of segwit began, I directly emailed the CTOs of  some 41 Bitcoin Organizations-- basically every one that I could find contact information for; seeking feedback on the activation of segwit.  I did not receive a single reply like yours.  Nor was there any like it in the bitcoin-dev public discussion.  So I'd like to learn a bit more about your views.

> I see Segwit as a crudely-designed kludge, and an unnecessary complication to the protocol.

Can you elaborate on this?  Ignoring that any system which does not confiscate coins must preserve the legacy behavior in parallel, I -- and, I think, the vast majority of engineers working on the system-- think that segwit is a major simplification, which will make things easier going forward.

> Open Transactions was working on a sidechain implementation years ago that didn't require Segwit,

Segwit doen't have anything to do with sidechains.  In fact, there is a federated consensus sidechain running today (and for the last year and a half) against testnet called Elements Alpha.-- I don't believe OT ever managed to get a running system. In any case, that sort of system works fine against Bitcoin today.

> SegWit introduces a large amount of complexity, technical debt that will make it harder for others to contribute

Can you elaborate?  What is the complexity and technical debt? I believe segwit will make contribution by others easier by better isolating other parts of the system.

> SegWit really is not a soft-fork, but a hard-fork, since you can't run an older node anymore and still even participate in validating transactions, all old nodes become obsolete.

Non-upgraded nodes continued to verify all that they did. Even in the segwit using transactions they verify the absence of inflation, absence of double spends, locktimes, etc. They don't verify the new signature style, but they know its there-- they just don't understand it so they accept it. Bitcoin was specifically designed to do this to make it forward compatible for upgrades.  Old software always becomes obsolete, but with soft forks there is no hard flag day where you _must_ upgrade. You can upgrade on your own terms.

> brought to you by R3

I feel this is a bit ironic, since the large block public publicity push was started by Mike Hearn who was employed by R3 without disclosing it to the Bitcoin community.

> we can extropolate that bitcoin would be just fine with an immediate

For the chain history, the block size isn't a 'size' it's a rate of increase.

> The bankers bought out the core,

Bitcoin Core is created by a large open volunteer community-- there were 100 distinct committers in the last major release. They haven't been 'bought out' by anyone. I'm also not aware of any real openly developed alternative, BU is a couple people with a closed community, with secret funding, which you must be approved to contribute in, and the other implementations are either inactive or roughly one man efforts. 

> SegwitCoin is Bankcoin, not bitcoin.

I'm having a little difficulty making sense of your argument here. What features of segwit to you believe act in the interests of Banks?

I'd love to hear about whatever specific concerns you have.  But I think to have a productive conversation it would be useful to set aside the personal attacks and political assumptions.  Cheers.
 
After reviewing it, I think this proposal is not a good one-- though I'm happy that it was actually written up as a proposal. It took considerable effort to review because the proposal isn't well specified-- you couldn't implement this from the BIPs because none of the serialization is defined. I hope the proposal is improved to the point that it can be evaluated as a standalone unit, but at least it was documented at all and with some help from the author I was able to give it some review.

Unfortunately, it follows Bitcoin "classic" newspeak tradition by being named the opposite of what it does: It actually removes flexibility by ripping out several fields of transactions in order to make the transaction slightly smaller. The result of doing this eliminates CSV's functionality, since there are no longer sequence numbers on inputs.

We can think of this proposal as accomplishing several distinct ends: It's a proposal for transaction compression-- which itself wouldn't be a consensus change at all and which could be done on a link by link basis, it's a proposal to change txids, it's a proposal to change transaction costing,  and it's a proposal to change the signature hashing.

It's useful to break it down along the functions and consider how well it does each:

As a compression scheme it's marred by incompatibly with existing transactions. A good compression scheme would work for all transactions, past and future. It gets about half the savings of other proposals that serve this function because it wastes space coding types and lengths even though there is absolutely no flexibility in most of them (the mandatory fields are all hard coded, and something changing them would be a new hardfork and a new transaction version number). It also fails to exploit common redundancy inside scriptsigs. By welding the compression to the hashing and consensus it also means that its not feasible to quickly evolve the compression according to changing usage patterns.

As a proposal to change TXIDs it only accomplishes the goal of getting signatures out of the ID, no other benefit, and breaks compatibility network wide (all bitcoin using software would require a synchronized flag-day update); despite these deployment complexities it is no simpler than segwit's improvement there: both require an additional commitment to the witnesses.

As a proposal to change transaction costing, it actually makes it relatively cheaper to create entries in the UTXO database than to remove them. This worsens the incentive misalignment, encouraging people to spam the UTXO set instead of cleaning it up.

As a proposal to change signature hashing, it fails to fix the N^2 signature hashing problem, it fails to reduce the size of the revealed data for safely spending. 

I expect that the other proposals for transaction compression will eclipse this because they have perfect compatibility, vastly greater impact, and can be deployed in a highly permissionless manner without creating network flag days and requiring every user to upgrade-- plus deliver greater compression.
The comment at the end alluded to this politely.

I've seen people drop hints that they were Bitcoin's creator seemingly to try to bring in investment money, and the other things one does with fame and mystique. Having people you want to influence quietly think that /maybe/ you are the mysterious creator of Bitcoin can confers business and social power.
There is no such construct in Bitcoin. What he's saying is true of forth; but Bitcoin is just superficially "forth like", it isn't forth (and I think it's often more similar in ways to the RPL on HP calculators).

One doesn't need Turing completeness to active all you could want to do in a system like Bitcoin. But that isn't what he's saying here. It's seems he's pretty clearly describing something that Bitcoin cannot do today as if it could be done today.

Transaction replacement for unconfirmed transactions was [a feature in the very first release of Bitcoin](https://github.com/trottier/original-bitcoin/blob/master/src/main.cpp#L434).  Transactions could mark themselves as replaceable by setting a non-maximal sequence number. This was later disabled because it was vulnerable to denial of service attacks.

Although the feature was very useful the denial of service problems and the fact that it was not incentive compatible (What incentive would miners follow the convention and accept a replacement?-- what if they made more from the earlier version?) kept it from being restored.

Later Peter Todd proposed requiring replacements to pay strictly greater feerate and to require that the replacement increase the feerate by at least the minimum required to relay a novel transaction. This both eliminated the denial of service and incentive compatibility problems.

Peter's original work went further and carried the incentive compatibility to its logical conclusion, reasoning that with anonymous, ephemeral, self-selecting miners the only behavior you can really count on is replacement with higher fees. Higher fee preference can also be made more globally convergent than seen first, because there is no global definition of 'first' in an asynchronous distributed system. Because of these reasons and because the system behaving in expected ways is more secure and protective than the system behaving in "unpredictable but better on average ways", he proposed making replacement happen for all transactions. He also proposed a protocol to remove economic gains from double spending in a strong incentive compatible way, called replacement scorched earth-- if someone attempts to double spend you, you send the funds all to fees so the attacker doesn't get them, but it was the kind of proposal only a game theorist could really love. This generated a lot of controversy and subsequently Peter Todd backed his proposal off to the original opt-in behavior.


Since it makes sense that the solution should be drawn from the same distribution as the story, thats what I've started.
Here is part 1 of 2348921831984932793289173189528718375832987213658201

Professor Quirrell who was a fact about to oer to rid you open to see if theyre working that whatever it take that were looking boy who was sitting in a pool of water.

A warmth poured out from the ground and study until it terminal velocity was eating food of questions but nothing like the Aurors in their brighter and taking Hermione saw that no matter how much I deduced the Dark Lord said the Defense Professor Snape who could have been enough to predict sphew is about your minds natural state it clear Mr.

Imagine it he is the one person maybe a person to happen Are we agree.

His own feet.

But Im asking for his hand face neutralise him to hide your opinion that list Great.

Potter.

Just like youre going from within him.

His mother wizards cant.

Im afraid and which Harry said because they werent answer any questions couldnt pick anything happily ever to go on defend your life be judged Rianne Felthorne in sudden rush.

And what Im saying that experience at all forms of two creating him Id hope.

Its a funny little hand out of the third time is it she knew in very few folk ever do so.

There was not suited Harry ignored the librarians sight.

I had to ordinary people walk from talk of ordinary way.

There is no just to mean said her voice and for a volunteer to help you wouldnt rather than you havent notice of your wand and turned dragging Harry Potter than Dumbledore was sending more ethical theory said carefully but hed never happened I have results.

You are ever departure.

And if Harry thought that fell to antipsychotics and holding that could be called there was a constrained by more than making it for one thing I nd even through the rocketed through his hand which those are they not Yes you dont have to nd bullies will indeed.

Who reads faster and over for some reason to do as well.

Can anyone nor had Ron for real things through him.

This isnt here by my bed only transform parts of his magic wonderland and began moving almost a whispered laying his words and your body.

So with the Death Eaters invisibility cloak.

There came a knock at the table want to die.

I have a quick and rescues us.

And Hermione Granger whirling through her voice when she thought is to be like it whenever.

This was taking it he told me I was thinking of all the other children or else learn the triangular mysterious dark side had happen if he started marrying a casual sip of his nose.

Not by law by the Dark Lords And the last probably.

At this match before then remember a new set of eight girls and then sat down at his answer to the sky at am in the empty classroom dyou know what to do anything I could open I would pay.

Potter.

There was a bit unsteady voice right away.

But of consequences.

It is a dreadful forbidding anything is that she simple and otherwise I have enough to do once you have fought a war going to glance casually glancing their strength lay or thinking and the snake.

And I told you that you do not know what wizardry but strong enough to inltrate a troll.

That magical Britain.

I admit.

For a moment literally the greatest the heck would requires them.

Harry thoughtful including your voice but since over his expect him less rewritten there some thing in Transgured out well enough Quirrell and took all of themonly what you can see that instruct her a tension leaking out.

Thank you for help with the prisoners Dilemma.

But the proof he couldnt resort of like knowing who still bothered masks move that of how I was beating half-circle Death Eaters shall we We have Huepus as we have to give them from being given a golden frame and you coauthor a paper campaign to rally the time the next best to complete Story of the robes which cannot count as giving out to be decision expecting her voice whispered Im sort of the running throw so hard to get on with Lord Voldemort.

All the others.

Do you think Lucius Malfoy could nd something Uh huh said Harry had been interruption.

No Professor Quirrell.

Theres a single word of anything done it no one the same as before Harry looked at his wet cheeks.

I had to!

And Harry Potter A rather informant said and his eyebrows had raised hands the dierence between taking his own tea then rotated itself along with their ears stream of drool trailing because there was a pause.

Harry was something that it was something There was the only when they were concern for himcan be alive with you.

That settled.

The little discussed as though the air.

Im really all of this That was it actually said Lucius Malfoys hands with the same.

Harry said all the truth said the mad Headmaster.

By the rest of you ssusspect that theyre going to give up to you asking me feel stupid.

Your choices of the Noble and Most Ancient Blade squarely into his dormmates to darkness taken No.

ATREIDES!

Hat and Cloak gave a quick glance at vengeance on David Monroe was trying hard as though it has been defeated the National burdensome details of the Wizengamot which had barely below an absolutely position for doing it in his mind either way over at where he realized that the whole aair.

He hasnt got him to prophesized then you do not have to think the Lieutenant Finnigan of Chapter in the cell.

Behind him each carrying your father was giving and then expelled.

said the Dementors pit.

No pester her there was a replica techniques require much less vulnerable irony.

Their last bellowed Expecto Patronus Charm is maintaining Perenelles blood and in a heap.

I'm ssorry teacher and completely unfair he wrote.

He seemed to have been shouting.

As Harry Potter proceedings of your life you must be in the hall and call the people called politely.

That reminded heroism like you did was love As kindness.

And in a faltering voice had been no dierent idea of talking normal she saw the physical vitality not just send him and well-turned-out than the storeroom and how wood mapped in his haste.

Thank you very much rage he couldnt go up to see The best to them no Well Harry Potter now how many possible thing you a good way to it.

Harry said looking all day.

Wasnt magically prohibited at all the watching the right question.

Potter for your precious Professing.

Th th th Harry Potter The door that you cant pretended to say something you said to Hermione half-jump across the Ravenclaw or Slytherin House reigning contents.

But there was silence broken masks.

Should have expected and a bright with his hand upon with hornstrying to argue for them to go to that he was working but that!

Somehow it sorted into Slytherin would all been sheer raw shock showed you.

Well Harry had done it is all I heard so clear his beds headboard of Governors where the Light shouldnt bring my time at all Harry said.

Goyle giggled causing Augusta Longbottom had still bothered.

I told you of almost touching a left-hand gloved hand grenades to save her.

But Fawkes I cant think I can do.

After Draco explain when the time Harry Potter The old lady in the same moments I am said this.

Then he arrived in a single seventh-year of Battle Magic!

Im going to the other boys shields around with no oense Hermione.

Harry turned to the Headmaster was it said ALZHEIMERS CURE.

Somewhere in the midst of battle to another unicorns.

Remove you.

Because Harry.

People doing that are counting on the public being uninformed, prone to having their eyes glaze over when confronted with technical things, or just lazy.

Bitcoin core's development happens in the open where anyone who cares to see it can look-- e.g. https://github.com/bitcoin/bitcoin/pulse/monthly
You realize that a lot of misinformed and foolish people believe that larger mining pools have higher relative earnings (because they misunderstand mining as a race) and by posting this you're going to drive more traffic to a far too overlarge pool which has already been [used to attack bitcoin users](https://bitcointalk.org/index.php?topic=327767.0), right?

You're also aware that a lot of Bitcoin miners are unaware that [large consolidations of hashpower](https://people.xiph.org/~greg/attack_success.html) far less than 50% can be used to successfully reverse transactions, and that the only thing special at >50% is that the probably of success after the attack persists for infinite duration becomes 100% at that point, right?
polynomial = a finite mathematical expression over one or more variables and constants that uses only addition, multiplication, and non-negative integer exponentiation.
In the US the removal of the public trains has a [well understood explanation](http://en.wikipedia.org/wiki/Great_American_streetcar_scandal). Perhaps something like this happened in Stockholm too?

**G** **U** **M** **P**
That milestone page isn't useful the way you're trying to use it.

There aren't any "issues left" there-- github is just counting our project tracking documents.

> I'll have a go: Currently, pruned nodes are only listening nodes, so can only receive blocks, not transmit them. BIP159 will enable the relay of recent blocks to help update newly syncing nodes.

No no no no. This is a common misunderstanding.

Pruned nodes relay blocks,  but only new blocks at the tip.  It's peers don't know that it also has recent blocks which it could share.

So when a node is restarted and is potentially behind a bit it won't connect to a pruned node because it doesn't know the pruned node can catch it up.
In security analysis we'll often consider a 'byzantine' attacker-- someone who will attack if they can, even if it gains them nothing.  This is the strongest model (weakest assumption), -- it doesn't require us to guess about an attacker's motivations, and it's the one we generally strive to be strong under.

Some people want to watch the world burn.  Other people announce their intentions to potentially raise a billion plus dollars in an ethereum ICO to fund the creation of a competing cryptocurrency marketed as needed because Bitcoin has been forked.  But if we're strong against the first class we don't really need to speculate about who belongs in second class.


Consider today's ethereum circus: someone nuked an enormous amount of Ethereum with no obvious gain for themselves.  Maybe they did it by mistake. Maybe they gained by exposing Ethereum's weak design or by disrupting the polkadot ICO which would produce an asset that might compete with ETH or MTN...  But if the parity contract were secure against byzantine attackers we wouldn't have to come up with imaginative motivations structure to realize that an attacker might just destroy it because he can. Instead, it's better to just not worry about motivation structures when we can.  This isn't always possible, but it often is...
If BCash adds functionality that requires restrictively licensed patents to use then it wouldn't be FOSS.
The point I was ineptly trying to make is that the only way that spamming has any real effect is because posts on social media make people think it has an effect. ... and thats the effect: worrying about it.

It doesn't impede nodes or transactions at all.
Bitcoin is resistant to cowardly scammers like Satoshi Dundee; but Bitcoin can't force anyone to run it-- people can be deceived into running incompatible hardforks created by scammers like this.  Resistance to this is created by people being willing to stand up and speak up and not be cowed by threats and attacks. Bitcoin itself can't provide that because the issue is outside of the system, not inside of it.

(Obviously, I am not intimidated by his threats: Craig Wright is a fraud.)
That sounded to me like he's bought into this "fees can be controlled by marginal orphaning risk" thing that was pumped by Peter R last year, unaware that this theory has been debunked (and, in fact-- he mentions the concrete technology that disproves it, weak blocks).

Why engineer a new fight when you can just exacerbate an existing one?

No need for sarcasm though-- it's a common move by con men and cultist to drive fights in order to alienate their victims from family and friends.

And we know for a fact that at least some of the most outspoken voices are among the bamboozled.

FWIW, I thought I was being pretty nice-- people are taking the caution to temporarily turn of xthin as an insult, but I am earnestly sharing some secret sauce grade advice there.

There is little room for ego in this stuff. If an optional feature is on fire and you aren't absolutely sure a simple fix will resolve it, TURN IT OFF, don't monkey patch it.

> It's scary because these technical geniuses are little blind to manipulators like Emin.

You assume too much. Treating someone with politeness and professional respect doesn't mean that you trust them further than you can throw them. :)

> This exploit on PoW seems to damage the network, from what I have been reading from /u/nullc posts

Right. And my proposed fix is squarely targeted at the damage and avoids passing judgement on boosting in general.
The good nodes ban the bad nodes but the ban nodes do not ban the good nodes in this case.

If the hardfork is bilateral (e.g. mandates that the blocks not be acceptable to old nodes) then both directions would be true.

When people were proposing BIP101 I strongly recommended it be made bilateral to make these things more clean.
This is boring and offtopic. Rbtc has almost nothing to do with Bitcoin, sadly.   Roger Ver and his paid staff are free to ban whomever they want from their subreddit-- thats how reddit works.

The fact that the reason given was stupid and transparently pretextual is amusing, but still offtopic. :)
We do have incentives, and I don't consider them minor:

Running a full node wallet is the only way to use Bitcoin privately every other mechanism results in telling third parties (who are often spies) of all your addresses and transactions.

Running a full node wallet is the only way to be secure against false payments from invalid blocks. 

Of course, there also must be nodes for Bitcoin to even exist-- a pretty good incentive if you own Bitcoins.

These are real incentives. They aren't huge, but they shouldn't have to be-- they're well aligned with the kinds of uses we need in order to gain security from having more nodes.

If you do go and pay people to run nodes and somehow avoid it being sibyl attacked, you'll still just end up with huge farms of VPS nodes that run for no other purpose than being paid-- they wouldn't add anything at all to the security of the system. In fact, they could harm it-- since these indifferent nodes could be filled with transaction spying software in order to "merge mine" income from anti-privacy services.

> Satoshi was also an incompetent developer 

This is a claim spread by folks like Mike Hearn, but it simply isn't true. Bitcoin's original release was _very_ well written with an exceptionally low defect rate.  It wasn't flawless, but it was far above average and more reliable than many of the additions that came after.

It wasn't, however, very modular... but it was also only about 10kloc or so! Adding a lot of modularity then to such a small system would have been a mistake: the modularity likely wouldn't have matched the future needs and it would have added a lot of complexity and likely introduced bugs.

The real substance of those complaints often centered on the common fixation of B-grade programmers: coding style-- a belief that anything that doesn't look exactly like what the person was expecting is "bad", a misunderstanding fueled by the fact that actually bad code also usually looks unexpected.  People with more experience have usually learned that there exist many mutually contradictory coding styles which are all well considered by experts and can all deliver world class results.

As far as the perspective of Bitcoin's creator on the subject-- remarks like that come from discussions along the lines of "could a flooding system work _at all_", a fact that was rather surprising to a lot of people that a system so seemingly non-scalable could be a starter at all.  The idea that you _could_ throw few racks of hardware at it and scale it to effectively infinite levels shatters that perspective. After all, the competition offered no decentralization at all so showing that Bitcoin could match their scale, even at the expense of decentralization, is an important proof point for the design.

Bitcoin's creator was no stranger to the inherent trade-offs between load and practical decentralization and in one of the [last public posts](https://bitcointalk.org/index.php?topic=1790.msg28917#msg28917) he made he pointed out "Bitcoin users might get increasingly tyrannical about limiting the size of the chain so it's easy for lots of users and small devices". -- and these were remarks made before any of us had really tried out the system running at scale and realized that the hidden costs built up _much_ faster than we would have guessed.  Back at the end of 2010 I thought 1MB blocks could be handled without trouble, but what we found was that the software needed a LOT of complex optimizations to even keep up with that. (Though perhaps he had more insight than the rest of the development community.)

> not really shown any willingness to compromise.

Segwit increases the capacity to 2MB.  Bitcoin Classic-- created after segwit was proposed, ripped out the risk reductions and scalablity improvements but promoted no more capacity.  That is unwillingness to compromise.

> In the early days of Bitcoin

Not even that early, the real promotion of Bitcoin as a payment rail was mostly-- thought not exclusively-- a 2013 construct, at least from what I experienced.

To some extent this could be a product of the expanding userbase-- after all [15% of US households](http://www.bloomberg.com/news/articles/2016-08-10/you-may-be-broke-and-not-know-it) have a negative net worth-- for sound money to be really useful to someone, they need to have some money in the first place.  But a payment rail is understandable to everyone, even if the complete business case (including technology fit) isn't as compelling.

> his whitepaper by discussing how Bitcoin could achieve Visa-scale

No mention of scaling or visa in the whitepaper. You're likely thinking of a mailing list message. Making absurd comparisons was a good rhetorical tool in addressing someone who flatly rejected the idea that a global broadcast message could be useful at all-- something I did from time to time too-- if the person you're talking to could be brought around to "but then the decentralization would be reduced" then you've already changed the audience's mind about the potential utility of Bitcoin because the traditional alternatives don't have decentralization at all.

The fact that covering all the use cases would require extra parts wasn't something unknown to Bitcoin's creator-- that is why bitcoin has the design it does, and that is why Bitcoin's creator came up with payment channels and baked support for them right into the transaction format. ... though it did take a couple years for everyone else to catch up to where he was-- including many developers! :)

> And so it was taken as inevitable by many

[Some people](https://bitcointalk.org/index.php?topic=2500.msg34211#msg34211) caught up faster than others, however. :)



The 133MB figure is assuming the entire population on earth is using Bitcoin as their money-- far beyond the deployment of card payment system's today, and is based on first generation lightning tech. We have a long way to go to get there, necessarily so. There is an upper bound on how fast Bitcoin can be adopted without causing massive social upheaval from wealth redistribution (and potentially war).
Years ago, Pieter taught me a good model for thinking about Bitcoin scaling:

If blocks are very small, Bitcoin can be perfectly decenteralized because everyone (and their dog) can trivially verify the blockchain and enforce the rules. But, in such as state Bitcoin would be useless because almost no one could transact.

If blocks are very huge, everyone can transact because there is room in the blocks for all possible transactions, and yet the system would be useless because it would become centeralized because almost no one could validate. (Might as well use paypal.)

A good outcome requires a _balance_, and there are many other considerations like needing a market to create fees in the future to support security.  Getting a balance is hard, since it involves the mostly immutable rules of the system and their interaction with the comptuers and internet of the future no one has a crystal ball.

I've expressed a lot of caution loudly and publically going back years on this, and finding additional alternative ways of scaling Bitcoin is what stared me working on sidechains (https://bitcointalk.org/index.php?topic=277389.0).

(This isn't to say that I don't think we may not need to increase the Blocksize someday, just that we ought to have the most tools at our disposable possible when considering how/when/why we do it so we can do the best thing to maximize the complete utility of Bitcoin).


I agree. But as I say: The best model for MTGox's behavior is "Do whatever would have the worst PR possible". This has long been the case.
This is old and standard misinformation that attempts to exploit the fact that people working on lightning are honest about scalability while people opposing it are completely dishonest.

People opposing lightning want _all_ transaction data to be forced to be processed by all nodes everywhere in the world via brittle lockstep consensus.  This is about the least scalable thing imagined.

Optimal lightning routing is trivial if you know all current channel states, knowing all current channel states takes strictly less information than knowing all the information about all transactions ever, so it is fundamentally more scalable than using the broadcast consensus alone.

Lightning is can be even more scalable than that if you don't expect absolutely perfect routing. If you don't care about getting the most optimal path then you don't need to know all that global information and you don't need it to be completely current.  So in lightning circles you'll sometimes see discussions about further reducing the amount of global information or otherwise improving that trade-off. You can use more information to make better routing decisions but there is no lockstep, you and I can have different views of the channels and still interoperate just fine.

Malicious parties like Stolfi exploit the lightning folks honesty about scalability to present a false comparison.

Imagine a town of 1000 people where everyone delivers their letters  by walking along the left side of the street until they hit the town border and then continuing to 'follow the wall' until they've walked every street in the town while they write down a map. Then after doing that, they find the shortest route on the map to their destination and deliver their mail.

A group of people in the town decide to do things more efficiently and start maintaining a persistent map of the town so they can skip doing the explore-everywhere step for every letter.  Sometimes the more efficient folks have discussions about how to better maintain their map-- e.g. what streets they could leave out and still reach everyone, or how they could keep parts of it local to some parts of town--  to trade-off effort maintaining and using the map vs how fast they can deliver letters. 

Dishonest parties that oppose the efficient delivers then go out and cry saying that efficient delivery isn't really efficient because maintaining a precise map takes work (as is pointed out by the efficienters themselves as they discuss the trade-off).  This is true, but it ignores that the alternative offered is even less efficient.   They're only able to make this argument with a straight face because they've spent the last two years arguing that the efficiency of the old way was irrelevant.
I think what I'm talking about is related to but distinct from that-- basically viewing engineers as low status button pushers.  Even though it's not unusual for top tier engineers to perform work of tremendous impact and importance and be paid as well as or better than many top tier professionals in other well paid fields (e.g. doctors and lawyers).

I don't get the impression that this was as much the case in the US in the 1950s  but it is today.

You can see this with the Bitcoin unlimited folks with statements like the developers of bitcoin have no business doing anything but deciding where they put semicolons in the code base (actual claim)... instead they think nearly totally non-technical  parties could some how do the job of designing bitcoin by merely enumerating wish lists.

I've wondered if some of this might arise out of resource fights between industry and academia.  Really good technical talent is highly prized in both domains. To some extent resources are fought over by insulting the other side-- with academics sometimes portraying industry as uncreative and unimportant button pushing, and industry looking at academia as a bunch of wankers solving irrelevant toy problems and congratulating each other on how smart they are.  To bystanders-- who correctly observe that the aptitudes for top performance in either domain are pretty similar-- it sounds like they both groups disagree on the details but agree that technical experts are losers. :)
> provably secure in the plain public-key model (meaning that signers are only required to have a public key, but do not have to prove knowledge of the private key corresponding to their public key to some certification authority or to other signers before engaging the protocol)

There are aggregation schemes which require that the signers publish a non-aggregated signature before being able to have their keys participate in aggregation.   This wouldn't work for combining the various signatures from multiple inputs in Bitcoin; because the publication of the non-aggregated signatures into the network would blow the space savings.
Free advertising for the site?  Try again, Roger.  :P


> Somebody from core could have written a long post.

https://medium.com/@morcos/no2x-bad-governance-model-97b8e521e751

https://medium.com/@morcos/no2x-centralized-services-539e3b1b56c9

https://medium.com/@morcos/no2x-full-nodes-889c20100a8d

https://medium.com/@jfnewbery/what-did-bitcoin-core-contributors-ever-do-for-us-39fc2fedb5ef

https://www.reddit.com/r/Bitcoin/comments/6h612o/can_someone_explain_to_me_why_core_wont_endorse/divtc93/

https://bitcoincore.org/en/2017/08/18/btc1-misleading-statements/

There are many more... But there is only so much you can do against people spending hundreds of thousands per month in counter-marketing.
> I think it's small (less than 1% per year) 

That depends on certain assumptions which cannot be guaranteed-- if a large amount of XMR is lost (which it may be already) then the inflation could end up much larger as a percentage of the true coin supply.  The system can't prevent this.

I also don't think that it is at all clear that 1% is small.  I won't keep my index investments in the stock market in a fund that has a 1% management fee. Over a 50 year period 1% per year loses 40% of its value.

> Is there any real example a blockchain can remain secure without a block reward? It seems to me that if we get really great scaling solutions for bitcoins, and the block reward goes way down: how on earth would the blockchain remain too expensive to attack? 

I believe this is true for Bitcoin, through transaction fees. We've seen the fees rise over time and are now 20% of the miner's income. I think this makes a strong case for the long term viability, but I agree it isn't proven yet.  If ever it were to become clear that without inflation Bitcoin would not be viable, I believe it would change then to adopt inflation because if the choice is broken or inflating the answer is obvious.  (Which is also why I think it's important that we don't drive it into a situation which forces that outcome.)  But I think based on what we know so far the system is viable without inflation.

> but it seems to be that everyone who is getting utility from the blockchain should pay for it to some extent?

I think this is a good argument, but I also think that it's unclear how to usefully achieve it in a decentralized system: we can't actually achieve a true fixed inflation rate, and the "correct rate" depends on the size of the economy because the cost of security should not increase linearly with the size of the economy.

In general I would not say Monero's choices on inflation are obviously wrong, I would say they are distinct from Bitcoin's potentially worse, potentially better-- but on the balance of the probabilities I think they are likely worse primarily because in the case that it turns out that some amount of inflation makes an outright difference in viability Bitcoin could and would adopt it (not that it should, or that I would expect to endorse it, but just that it would because forks with it would survive and forks without it wouldn't; if that were the case).

> Is scalability a real issue if using second layer solutions (that hopefully don't jeopardize privacy) 

I think so, there is always some trade-off.  Why not make the system allow only one transaction per hour? -- it would certainly make it easier to run nodes and keep connectivity robust and unblockable. The same arguments to that apply at basically any scale.   Absent other considerations more scalability is better.

> We plan to move to zk-STARKs or something similar when possible, I believe; seems like the community is fine with it and has no weird idealism with how we remain private/fungible.

Which have even worse scale -- hundreds of kilobytes in a signature.   Of course, perhaps tech growth or improved techniques moots these concerns eventually-- but I also expect Bitcoin to adopt such things esp if we assume the technology has been perfected to the point that the costs are insubstantial.

> lite wallets seem good enough; I use monerujo on an oldish android phone and while the initial sync is a bit slow, if you tell it a specific block height it's not bad at all. Perhaps I am misunderstanding this criticism then? 

Monero is very small compared to Bitcoin.  If you multiply the sync time and bandwidth up using Bitcoin's scale numbers the results are not so pretty, and of course people are howling about how Bitcoin is too limited-- Bitpay demands 100MB bitcoin blocks.

> Also, would you be willing to do a nice write-up/ article for our Skepticism Sunday post? Our skepticism Sunday is where we all try to look critically at Monero and figure out what we're doing wrong, if we're all crazy and should sell it for Bitcoin, etc. 

I might be willing to do that at some point-- can't this week because I'm travelling.  

I've generally found the monero community to be the most reasonable of the alts about criticism, but there still are a few jerks that don't understand that good criticism increases the reputation of the system because nothing is perfect and without trade-off and a system being without robust criticism is a high quality red-flag of scammyness... as a result it's not a easy or free thing to point out the limitations as I see them as I will be attacked for it.
Your response is confused.  The contributors to the bitcoin project are specifically not pushing to create a hardfork, S2X is.

People, including myself, and virtually every top contributors spoke out vigorously against S2X before it was even released. This was made more difficult because they had not published a spec (and AFAIK _STILL_ have not!), so people naturally preferred to review what was actually being proposed. Your claim that people didn't speak up just seems intentionally dishonest, since it was very visible on reddit while you were busy posting on ethtrader...
> Well they are connected at startup,

They are only consulted at start [if](https://github.com/bitcoin/bitcoin/blob/master/src/net.cpp#L1587) the node doesn't have connections after running for a while, so they won't be consulted at all (for example) if the node ends up connected to some S2X peers that are censoring it. Please, you are treating me rudely and you are clearly ignorant about the system.

> The amount of impact they would have even when they fork would be minimal

Yet ABC nodes were left struggling unable to get connected because its developers had the same misunderstandings you do. And ABC at least had replay protection that made incompatible peers ban each other more quickly. S2X doesn't even have that.

> Well how can they not influence it, but also be the startup and fallback mechanism. Pick one.

They are always only fallback. And the information they send is given very low priority. If you are busy connecting to peers that just censor you, it isn't going to matter much.

>  All this does is try to show how much the core team wants to split off from Segwit 2X

The Bitcoin project devs did [more or less unanimously reject it](https://en.bitcoin.it/wiki/Segwit_support)... I don't really think we need any changes to make that rejection more clear. The PR explains the motivations clearly and frankly, but you seem to be unwilling to reconsider your preconceived conclusions and are making a lot of mistakes about how Bitcoin works in order to justify them.

>  peers for 3 months

You realize that 0.15 will not be released for quite some time and this will not impact older versions, right?
Users won't even notice this, it's all very low feerates; real activity will just fly by above that noise.

These grapher sites contribute a lot of confusion... people don't realize that any transaction with a feerate lower than theirs (e.g. the entire big blue chunk above) are irrelevant to them and do not impact their confirmation times in the slightest. 
That's craigs' Mo-- give everyone a narrative to dismiss anyone with the technical competent to call out his obviously fabricated 'evidence'.

So all you need is a wedge issue where most people technically skilled enough to spot a totally fake digital signature will be on the other side... obviously, start posting about 300 gigabyte blocks and you're good to go.

In Soviet Russia, BU sticks fork in Bitcoin.
A couple people have called this out and I am very thankful that you have.

Though it may seem obvious with the idea in front of you it took significant effort to find a step we could take that would have the minimal negative impact on anyone, which only narrowly addressed the most urgent concern, and which was very tolerant of potentially conflicting views.

It doesn't propose forcing segwit (though that would have been a "solution" too), it doesn't even propose blocking boosting in general-- it just separates the concerns. 

It could. But other things could as well, including just sloppy operations or using inferior Bitcoin node software.
> I can't tell if this is special semantics or just utter rubbish. Peter Todd was tweeting about the exploit well in advance of the attack on the BU nodes.

Absolutely not, as reported by the BU developers themselves: the attacks started 30 minutes after the commit went up, PT's tweet was an hour after.

https://www.reddit.com/r/btc/comments/5zgefe/this_was_an_orchestrated_attack/dey92uv/

https://pbs.twimg.com/media/C66Gn3iWsAIoui9.jpg
> More developers doesn't mean more decentralization.

Of development? it certainly is suggestive of it.

> More contributions doesn't mean more relevant contributions.

It doesn't but are you really going to try to argue that it isn't the case here, and overwhelmingly so?


> A single repository is as centralized as any other single repository.

Bullshit, and those 100-ish contributors to Bitcoin Core is 100-ish repositories: each contributor created their own fork in order to contribute.

But hey BU is still better than ~1-developer "Bitcoin Classic".
Yes, both.
I think a lot of people buying into it have been materially mislead.  For example the 'prospectus' they sent out made it look like the supply of coins was finite instead of endlessly inflating. Without that knowledge the argument "oh, well eventually they'll fix the bugs and it'll be valuable then" makes more sense. 

Similarly, it was promoted as a "world computer" which will replace all the major online services like facebook, uber, etc... even though that makes basically no sense from the perspective of what the technology could actually do even if it were bug free.
::sigh::

Priority was disabled by default several releases ago. The "now" in this topic is confused, 'now' was a year ago.

It was disabled discussion and measurement showed that an overwhelming majority of hashpower had already turned it off, so disabling it by default would have little additional effect.  Additionally, if it had still been in use the benefit was mostly granted to people with moderate to large amounts of very old coins (e.g. developers), at the expense of more recent users.

The benefit of disabling it is that the blockcreation logic didn't need to perform the algorithmic slow priority selection and could instead use a pre-sorted index, which was a massive speedup. (With a large mempool the priority ranking could add seconds to the createnewblock time).

There is already a generic mechanism that a miner can use to adjust the preference for transactions. These preferences are efficiently implemented and are preserved against restart (in the versions under discussion).  So if a miner wants to implement some unusual preference policy they can use this mechanism to do so in whatever way they choose.

So, summary:

(1) already deactivated a long time ago not 'now' as OP suggests, and it was done after miner behavior had largely mooted the change.

(2) If you believe that it's desirable for other preference schemes at all, there is a nice interface available for any miner that chooses to use them.

It won't be treated as signaling for segwit, it'll just be an unknown signal flapping in the wind.  I suppose people could, but Bitcoin Core itself won't do it until the 15th... so any signaling before then would just be parties that overrode it.
The SNR is very poor. And the technical community is not general skilled enough at communicating or motivated enough to overcome the noise for many people.  Without the right background and paying attention in the right places... people just might miss it.

This is especially true because a deep understanding here requires getting past the obvious misunderstandings, and the opposition has a simple easily understood story that has a strong narrative appeal... which is also incorrect. But everyone loves a story. 
FWIW, user [10mmauto](https://www.reddit.com/user/10mmauto) made numerous posts in this thread, and then deleted them all himself (presumably to punch holes in the conversation and further rbtc's claims of censorship).

He bragged about deleting them in his last message posted to the thread that was deleted as I responded to it: https://people.xiph.org/~greg/temp/10mmauto.1.png
How do you protect against datacenter staff / facilities people gaining/using physical access to your systems?

Many Bitcoin businesses have been robbed by social engineering (or insiders) at hosting facilities. Not much has been published on best practices to mitigate these risks.
My experience as well, for several years now.

There have been a number of instances of people loudly yelling "We must have bigger blocks now because X" where X was some application that was entirely non-Bitcoin-- where they intended to pay miners directly to put huge amounts of data in the chain-- and all I can do is gape my mouth and blink. Interoperablity is great, but thats not what many of these proposals are; many consider the BTC currency a liability, in fact.

The world can't afford for Bitcoin to go the way of Usenet-- buried under actual-spam and binaries floods until every provider shut off their increasingly expensive NNTP server and shunted users to a few centralized providers-- few of whom could weather the copyright complaint floods.
Right now the tools are very raw-- you don't need to know cryptography but you need to turn your verification program into an [arithmetic circuit](https://en.wikipedia.org/wiki/Arithmetic_circuit_complexity); and deal with a fairly complex C++ library.

Microsoft Research has a compiler that is part of the [Pinocchio project](http://research.microsoft.com/en-us/projects/verifcomp/) that compiles from a restricted subset of C to a suitable circuit-- though the compiler is usually not as efficient as hand written circuits; though circuit efficiency only matters for how fast the setup/proving runs (and the amount of 'proving key' the buyer has to send the seller).

The [SCIPR Lab](http://www.scipr-lab.org/) folks have a construed a circuit that evaluates a virtual machine bytecode, with the bytecode provided as an input. This means you could write your program and compile it to the bytecode with a pretty ordinary compiler. The downside is that this method has high overhead, I think the record for the prover speed for this has the virtual CPU running at about 10Hz.   OTOH, the proof generation is embarrassingly parallel and could be hardware accelerated (I spent some time looking into fabricating an ASIC for that a while back).

There is no fundamental reason it should be hard though-- it's primary a question of tool maturity. And a little bit of a chicken and egg situation; without applications to drive tool maturity they tend to remain fairly academic. At least for cases where the prover doesn't need to be ultra fast, there is no reason why ordinary software couldn't just be compiled to it, without any specialized knowledge.

For high performance proving, it's harder-- normal programming languages have dynamic accesses to mutable memory and data dependent loop bonds which don't map very efficiently to circuits.  I think for most ZKCP applications you actually wouldn't care if the prover took a long time.


> as their primary communication tool

AFAIK Luke and I are the only people heavily involved with core that post here more than a couple times a year.

I tend not to use third party controlled highly centralized communications mediums for primary anything, where at all possible.
FUD indeed, but it's not incorrect.

When the amount the node is willing to receive is less than sizeof(void *) (e.g. 8 bytes on x86_64) then it will still receive sizeof(void *) bytes from the network, writing past the end of *pchBuf, which is on the heap and less than that size.   An attacker could trigger the buffer overflow by hitting you with enough traffic on one connection to almost but not quite exhaust the limiter, then in the next connection provide the data the they want written past the end of that object.  

I have no clue if its actually exploitable: Normally one assumes anything like this is because with sufficient cleverness most things like it are, e.g. I've seen single byte overflows that were exploitable even when the overflow would only write a 0x00.

In Core we likely would have rejected this part of the patch which introduced the misbehavior for the introduction of manual memory management (new/delete) before even noticing the overflow, or even just for unnecessarily changing the buffer size (it could have left the buffer alone, and just min()ed the recv call argument). The additional heap allocation per received chunk can't be good for performance either.
Oh. My.  The "RandomOrgGenerator" was the software connecting to a third party website "random.org" (over http, no less) to obtain "random numbers". 0_o

> What's on the future roadmap?

I have no idea what blockstream is planning but I know what I've been nagging them to do:

(1) A protocol update to Fibre made last year which was just adopted by Blockstream makes it possible for different satellites to send different signals and halve the block reception delay (and double the robustness to packet loss) for users that can see two satellites (e.g. basically all of North America).  But right now blockstream is still sending the same signal on all satellites.  I hope they update to send different signals.

(2) There are changes in the data encoding which are possible to increase bandwidth efficiency for Bitcoin data by 25-30%.  I hope they adopt those in a future update.

(3) The Fibre implementation is a long way from as robust against packet loss as it could be, with many design tradeoffs optimized for the internet fast block relay case favouring shaving off milliseconds. I hope they improve that.

(4) I think the blocksat modem is a long way from as efficient as it could be. Right now it loses lock at a point when there is still enough signal coming through to receive most blocks. It also fails to get a very low packet loss rate even with an oversized dish, though its good enough for fibre (which only requires half the packets make it through).  The reasons for this are complicated and technical, I think there is a lot of room for improvement at least on fast computers.  Maybe a result of this would also be to allow increasing the bandwidth while still supporting the same dishes.

(5) at the same time, the minimum system required to decode is pretty high end, this gets in the way of a lot of people. Especially because blockstream has given almost no advice on what hardware people need to use.  I've seen a number of people try setting up reception with an rpi... and that just ISN'T going to happen at least not with the current software. :)   It should be possible to radically reduce the cpu requirements at least for users with strong signals.

(6) right now they're statically dividing bandwidth between the API and bitcoin data.  This seems a little silly since not all data is equal priority, and the fibre software already contains a priority packet scheduler.   I hope that in the future the satellite is smarter about allocating bandwidth.

Now that blockstream has this whole API thing there are probably a million things they can do with that, but I don't know much about it.
Checking that your node and a synced from scratch fixed node (or a 0.13 node) agree on the same tip is sufficient.  It's also sufficient to look at the total coins in a gettxoutsetinfo, though the number there should be somewhat lower than the expected amount based on the height due to some coins having been provably destroyed.

Even impacted software will also detect the inflation with the startup consistency checks, but by default the consistency checks only check a couple of the most recent blocks (to avoid long delays at startup).  But if you restarted right after an inflation causing transaction happened or you increase the check depth and restart the checks will detect the corruption and shut down.

Of course, all these checks have been done by others, but its virtuous to check for yourself.
> The late Dave Kleiman's possible involvement has never really been criticized.

It has been, but you've probably never seen it because the idea is absurd enough that it hardly seems like it needs debunking:  Beyond CSW's provably fraudulent claims, there is nothing to support the idea -- so why debunk it any less than the claim that the guy that works at the geek squad desk at your local bestbuy?

I'm sure Kleiman was a fine chap, but all the information about him suggests that his technical expertise extended to "IT Guy" and not further.  The sum total evidence him having _any_ programming expertise is that apparently he once wrote a trivial Windows registry "security checkup" tool in visual basic. Most of his professional history was IT support for a local police department. 

If anything the subject is depressing because it just shows how thoroughly unable many non-technical people are to distinguish levels and kinds of technical expertise-- and that the journalists they count on to help them reason about these things are failing to do their job.  It's like suspecting that the guy who fixed your microwave by replacing a fuse might have secretly been part of the design team of the space shuttle, "because he's good with machines".   While it's possible that Bob The Handyman might have worked on the space shuttle's design, its not substantially more likely that he did than Wilma down at the hardware store... and not worth debating.  I'm not trying to suggest that IT Guys aren't smart and capable, but so are other people who do skilled work-- there just isn't any particular reason to expect any random one was involved in creating Bitcoin.

It's especially not worth spending time debunking because at least some of the accounts that keep posting about it are obvious (and less obvious) CSW shill accounts,  and they're just going to keep posting it until the end of time.

Archive.org showed that the references to Bitcoin on CSW's blog that the "leaked" documents refer to were backdated and added in 2013.  Once that was noticed all discussion about him should have stopped.  When his "proof" signature turned out to be a really lame forgery, it doubly should have stopped.   The fact that the media keeps suggesting "what if" makes me wonder if not having meaningful civil liability for journalists that recklessly reprint such obviously bad speculation might actually be a really poor public policy... and it makes me fear for the future, not of bitcoin, but of humanity. 
Not everyone here is completely bamboozled. 
The traffic you are seeing may just be some peers syncing from you-- Bitcoin uses a lot of bandwidth, many times the nominal block rate.

If so, the bandwidth usage shouldn't harm your mining...

But also DOS attacks of nodes is not uncommon, especially any that are detectably involved in mining.  You should use a best practices setup for mining: your mining should be run off a node which does not accept inbound connections but instead connects out only to nodes you run, relay network(s), and maybe a couple carefully selected stable external nodes.

This will make it much harder to dos attack.

I am doubtful that any of the dos attack is related to UASF signaling.

You can use the getpeerinfo rpc to learn more about which peers are sending you which sorts of messages.

> They just have a secret channel where they organize their PR and trolling campaigns. 

I'm not in any such channel. People (not specifically to the Bitcoin project) try to create things like that from time to time, but I think those sorts of things are cancer, they hurt communication and cooperation and waste time. And they're a liability when some idiot leaks logs of months of people blowing of steam in what they thought was a private place. The last one of these I got pulled into and left was when Zooko tried to create a private alternative to Bitcoin-wizards.

(though uh, really, that's a complaint that people working on a project sometimes coordinate in private?!)

If I hire a security guard to work for 8 hours securing my premises and one of them figures out a way to jury-rig the gate so that he can get 8 hours pay for 6 hours work, but as a side effect he has to block all trucks from visiting the site... We could call this a design flaw of the exploitable time clock system being used.

(probably we'd first call the guard a crook, but Bitcoin doesn't care about moral judgement like that. :) )
what the heck are you talking about?  Leadership of what?
Yep: It would make it impossible for someone who didn't want third parties changing their TXID to have them changed by a third party, which is what happened here. (And what can happen again at any point in time until they have the ability to use segwit)
This sort of thing is why I won't touch subjects like your with a 10 ft pole, and why I unsubbed from that list. FWIW. You're not alone in experiencing that.
> Within 30 minutes,

At ~4am my time. And it didn't even say anything new or refute the point that I made in the thread (that other people had access).  It was primarily just citing Gavin himself.
Who says they do?  You're getting fed FUD, don't fall for it: Testing and upgrading software takes time; especially in high value production environments. Doubly so for miners which don't have big full times ops staff that specialize in upgrading their node software.

Many miners have customized software they need to update to deal with segwit, so it's a bit more work for miners than typical softforks.

Softforks-- ones easier to deploy than segwit-- have historically taken a while.

For example, BIP 66 was released in software on Feb 15th 2015 and went into enforcement on July 4th.  ... and it required much less of miners than the segwit upgrade.
Yes. Several.  (I don't think it would be proper to name names from private communications though!)
Writing a rbuttcoin headline makes me feel like I'm trying to title a [Chuck Tingle](https://en.wikipedia.org/wiki/Chuck_Tingle) novel (link not entirely work safe).

The 'final straw' which was apparently too edgy for the good of the fair people of rbtc was:

> > There is no specific rule just for you, the rule was already in place. But we had to add a name to the config because of you when you were trying to dox another person in this sub. We added that person's name to the rule so you or anyone else cannot dox them anymore. 

> You're talking yourself in circles. You didn't add a rule, but then you did.

>> lol okay, sure. 

> "Dox" lets stop with the euphemisms. [Edit: Well known hashfast employee; name removed to avoid this post getting vanished too] was a part of the fraudulent mining hardware company Hashfast-- he ripped off and directly lied to me.  He claimed he wasn't involved, so I linked to the litigation against him where he admitted to the court that he was involved. You added an automoderator rule to silently hide my posts when I linked to these public court documents and you never mentioned it to me-- just invisibly hidden posts, as is the norm here.

> Today a poster on rbtc made a defamatory claim about me tampering with his systems and I responded by posting the git commit message of the change where the 'tampering' was performed by Gavin.  Seizing on the email address in the public commit message you took the opportunity to hide my message, leaving the defamatory claim uncorrected.

After my dire publication of a commit message I guess mentioning a scammer that ripped me off was just too far!

(Edit: And of course, rbtc is deleting all mentions of the ban there: wouldn't want to mess up their perfect UnCensorship track record.)
Apparently, It turns out that he was just flat out lying to me:

> I'd like to clarify that my comment was just in reference to ALL government laws being threats of violence, and ultimately death threats for those who don't comply. Other than general legal uncertainty about Bitcoin and crypto coins in general, **I'm not aware of any government agents targeting shared send or Blockchain.info directly with threats of violence other than the usual things like "You must get permission from us in the form of a business license to do much of anything"**.

(emphasis mine)

I think this further emphasis his acceptance, even desire, for Bitcoin to become yet another centralized payment rail... when his excuse for removing pro-privacy features (even if not very good ones...) is to claim that they were removed because government action of any form merely *exist*.

Thanks for creating this thread, or otherwise I never would have known this-- and I'd be left thinking that specific legal action had been taken against that feature. I also wouldn't have learned about the other things going on at BC.i that have come up in these threads. Interesting!
Blockstream filed an opposition letter and then ten minutes later it was dead.

I'm totally sure they read it and changed their mind immediately. :P
Or the belief that siding with a majority at the cost of some minority is some great act of moral justice, rather than a sad sick compromise which must sometimes (but not always) be accepted as the least of the available evils.
For example,  a miner takes an unspent coin, and generates two transactions spending it where their txids the same initial 64 bits. This takes a few seconds of computation with the test tool I created after PeterR claimed that producing 64 bit collisions was computationally infeasible.  They then send each of the transactions to a non-overlapping random set of half the nodes.  They keep doing this over and over again, dividing the network into thousands of little partitions of nodes with the mutually exclusive transactions that share the same 64 bits of transaction-id.

They configure their own mining to not process any of these transactions.

Now, when some other miner gets a block including some of these transactions, the collisions will make the Bitcoin unlimited reconstruction fail, requiring a time consuming fallback to less efficient transfer. But the attacker's own blocks would transfer unimpeded.

This kind of potential vulnerability was understood years ago and I published designs that avoided it-- which BIP152 compact blocks uses.

No, I don't believe it came up at all.

In that area most folks are working on finishing the segwit review-- which provides capacity similar to a 2MB block, but with better scalablity (lower resource usage per capacity), and many other improvements-- including the ability to easily deploy further scalablity improvements like aggregate signatures (~30% reduction in tx size).

It may be because the document he received extensively described this work as being applied to Bitcoin by paying miners to censor non-participating transactions, as did the document I received.
He also remarked that the shop is also going to go out of business ANY DAY NOW because the tables are always full with higher paying customers!


Current activity is clearly an attack though prioritization by miners and the very low fees its paying have reduced its effect for many users. I believe the same parties behind it are behind a number of other (also fairly ineffectual) attacks on other Bitcoin related infrastructure that started about the same time.

I'd previously proposed on bitcoin-development that the effective size of a transaction include an offset for the UTXO impact.

The free transaction priority in Bitcoin (and now in Litecoin as well I believe) already do this to some extent-- as the first 160 some bytes of each signature are subtracted off before computing priority. Though in that case, just like for all free transactions, it's depending on miner good will.  If the blocksize limit were a transaction cost limit that considered the utxo impact then optimizing utxo impact is economically rational up and down the stack.

Though this isn't a short term thing, shorter term-- making the min relay fee consistent again with the actual fees needed to be competitive with getting into blocks which would also undermine the dust limit may be prudent; but it'll take some more consideration to figure out if it's the right move. There is a PR open for that now.  Ultra short term, it's quite easy for miners to identify and isolate this particular set of bloat attack transactions and either not mine them at all or de-prioritize them-- some are doing this already, though not all-- but its not necessary that all do it.
Three months ago I discovered a miner with ~1% of the hashrate that was processing transactions with no signature validation at all.  If I had sent that miner a transaction where I spent a million of other people's bitcoins they would have mined it and the half of the non-verifying miners would likely have given it 6+ confirms for any SPV client; and thats the more fundamental issue here which no amount of version checking would help with.
Peter Todd linked to something that was already published on Reddit seven months ago; that is a little bit beyond horse-out-of-the-barn territory; no?

(Your failure to link Peter Todd's actual message, Mr. Throwaway, has the effect of making your claim somewhat misleading, I think!)
There is a fantastic short HPMOR fanfiction about this: https://www.fanfiction.net/s/11084923/1/Nothing-Left-but-Fire


That isn't automatic. Lots of things in the Bitcoin ecosystem go out into production clearly with little to no testing or review at all. It's much cheaper and faster to "move fast and break things" especially when you're working with other people's money.  If the public doesn't demand visible assurances that the systems are reviewed, they often won't be.

That eventually the system may be "reviewed" as part of a postmortem after attackers have taken all your coins isn't really much assurance, since your discover is inherently too late. Just watching and waiting doesn't really work either, because attackers are surprisingly slow (after all, most people with the skills required for really sophisticated attacks can make a good living without harming their fellow man; the pool of sophisticated attackers is smaller than you think and is often busy with lower hanging fruit, like people who run jar files in email attachments claiming to be from multibit).
It's perfectly possible to "pool for income" without delegating your vote. P2Pool does this already. With getblocktemplate it can be done with traditional income pooling pools too-- thats called "coinbase only mining", where the pool specifies the required coinbase transaction and the rest is up to you, or could if any of them cared to do the software work to support it.

My org is currently funding work to make regular mining clients and mining pool software compatible with seperating pooling-for-income from delegating your vote... it could use more help, but it seems the commercial mining community has largely been indifferent to solving these problems (see the lack of commentary at http://sourceforge.net/p/bitcoin/mailman/message/32283078/ )

Breaking pooling entirely (which has been proposed and debunked many times) would have enormously bad ramifications since then the easiest (any perhaps only) way to economically participate would be via hosted mining, which is far more damaging than pooling.

The comments on algorithims are mostly debunked in https://download.wpsoftware.net/bitcoin/asic-faq.pdf

You mean 1957-2014, 2142-2458 right?
More likely you posted in a totally offtopic thread or necroposted some long dead thread with a post that added no content.  Is there a reason that you're not pointing out where you made those posts?

I can't see any other reason for those posts to been removed. ... the same stuff has also been said on the forum many other times, so it sure as hell isn't censorship.

As an aside, your claim that a weak RNG can't cause funds loss if you only sign one is not factually correct.  A predictable nonce is sufficient, it doesn't have to be reused or signed with twice.

The technical criteria for compromising a key with signatures is the same as for solving a linear system: you need at least as many non-collinear equations as you have unknowns (the system of equations must not be rank deficient).  Each signature is a known equation, the private key and nonces are unknowns.  Having two signatures of with a linearly related nonce (which nonce duplication is the simplest form of) is sufficient -- two equations two unknowns, but so is having one signature with a predictable nonce-- one equation one unknown.

The link gives an incorrect explanation-- saying that they're mutations of existing signatures-- that also claims these fake signatures could not be produced for the key the genesis block pays to.

This is just all wrong and worse, sets up a situation where Craig S(cammer) Wright can then publish some fake signatures using the genesis block key and have people convinced that it's not fake.  Too bad I beat them to it.
I don't know what Luke's source is but Jihan claimed this to me directly, and I presume also told other people it.  But that was some time ago, and I believe even if it were true then it probably isn't true now.  It also just have easily been an intimidation tactic. Certainly history hasn't supported the claim.
And even if all 8 were sybils and were on some other chain, eventually you'd end up connected to others that gave you the real chain unless they attacker totally controlled your network.

Also, if the chain they give you doesn't have as much work as the one hardcoded in your software release, it'll know something is up. So to fake you out they'd have to continue a recent chain, which is very computationally expensive and you're going to see a lot fewer blocks displayed that a non-attacked wallet.

There are some things the software could do to better protect users against these sorts of fringe attacks, but whats there is enough to make them pretty unattractive.

Sounds like you've fallen victim to a dishonest narrative about the history of Bitcoin.  The same well known developers that support it today have been here basically all along.

Here is a table I recently posted that pulls out some often discussed folks:


     /-----------------------------------------------------------------------------
     |             Non-merge commits in Bitcoin project git
     |         2010  2011  2012  2013  2014  2015  2016  2017   All | Active months
     |-----------------------------------------------------------------------------
     |Wladimir    1   349   159   115   384   193   197    93  1491 |            73
     |Pieter      0    51   227   111   170    89   156   112   916 |            75
     |Matt        0   101    71    38    25    54   100   143   532 |            66
     |Gavin      17   152   139   112    47    17     1     2   487 |            61
     |Garzik      0    38   106    35    48     7     0     0   234 |            36
     |Hearn       0     0     1     7     2     1     0     0    11 |             8
     ...

Correct. It was posted on the now-known-compromised p2p foundation site and it was not signed.
red touch black good for jack
Ideas which come to mind:

(1) Pays them lots of bitcoins in their fork. 

(2) Creates FUD about a fork that doesn't actually exist. (sure you can't _see_ any blocks on the hardfork, it hasn't failed, bitmain is just keeping them private!)

(3) Provides an excuse to explain where their hashpower went when instead it's used to perform a criminal attack against other people's systems.

It's anyones guess the whole post seems kind of crazy.
> Satoshi never saw Gavin as a potential risk.

Satoshi demanded Gavin stop drawing attention to him.

> It's Gavin who Satoshi passed Bitcoin project to.

No, Satoshi left. Gavin then claimed that himself.

> but honor and responsibility.

He behaved irresponsibly and wouldn't even resign his access after years of disuse, it was only removed after he backed an obvious scammer.



all the website has you do is add an entry to the hosts file so that the name resolves to an address you control, it doesn't have you enable anything. It is exactly the same as using an external DNS server to redirect it-- which you can also do.

> The suggestion has been made [...] that the feature is disabled by default

I'm interested in knowing who is 'suggesting' that?

> How quickly can the block be implemented? In 0.14.x ?

Implementation is trivial. My personal guess is that it could be in could be in a 0.14.2 if there is overwhelming community support, esp from a sizable number of non-boosting miners.
> From a capitalistic perspective or self interested pursuit, why are we limiting and punishing those with better hardware/software?

We, in particular _I_, am not.  This proposal does not prevent ASICBOOST, it only interferes with the covert version and only to the extent that the covert version is incompatible with protocol upgrades.

The argument for blocking ASICBOOST outright is that a patent is a government granted monopoly and restrictive licensing of ASICBOOST is likely to result in an eventual monopoly in mining (because difficulty adjustments push mining to a break even equilibrium, so potentially all unboosted miners would operate at a loss).  I share the concern but I do not consider it to be as serious an issue as the disruption to protocol upgrade capability.

If any parties who would be adversely impacted by this proposal would like to speak up, I would _love_ to hear their arguments.  My guess is that they will not want to admit to patent infringement in public, and so they will not.
Viabtc used defective spy mining (also called validationless mining and SPV mining) software which used the block height of the prior block in their coinbase, which is a violation of BIP34. 

The interesting thing about that is that mining a _single_ block on testnet or regtest would have shown that the code was wrong.
Unfortunately [people from rbtc have been making false virus reports on Bitcoin Core](http://archive.is/6uw0T). They also [got luke-jr's website listed](http://luke.dashjr.org/programs/bitcoin/files/charts/services.html). :(

You should check the PGP signature. If it passes, you're good to go.
I'm pretty sure SPV mode won't be in there. Multiwallet is possible, but the efforts to get that in before the freeze data will completely starve SPV mode (which was just created) from getting any review until multiwallet is in.

Y'all steal the thunder of new releases if you blab about their features before they're even in.  :)

The problem is that when you talk about something early people go blah vaporware... then later at release they go blah, heard about that already.  The project is open for all to monitor but it's more interesting to save the announcements for the release time. (and also avoids counting your chickens before they're hatched)
Frogs will rain from the sky.

... no, nothing-- we just won't enjoy the benefits it provides or those provided by further features based on it.
Fun facts: in rbtc messages the word "ETH" has been used more times than "BTC", and for all the hate they fling at Blockstream they've used the word Ethereum twice as often (usually speaking positively of it)...

I'm not saying that rbtc is a false flag altcoin promotion operation, I know there are more than a real people there for sure. But I wouldn't be surprised to find out that many of the posters were participating there with that agenda.  It also shows in the voting patterns there: I believe my most highly downvoted comment in rbtc is saying something really boring and technical but vaguely negative about ethereum's design.
> if it is truly debunked and academics recognize it, then what is the purpose of your post here? Is publicly discrediting your academic rivals part of your paid responsibilities

What the hell? The reason I commented is that apparently Roger Ver is unaware of it, there is no shame in being mistaken about it. When you confuse science and engineering with politics bad things result.
Thanks for having the courage to step forward and describe how you lost funds. Your comments will help other people avoid the same mistake, and may help wallet developers make software which is more mistake resistant in the future.
https://www.reddit.com/r/Bitcoin/comments/4rfyhf/bitcoin_halving_what_why_and_what_happened_last/d5140ws
No. gah.  That is a misunderstanding.

_All data_ is malleable. See: "BloopFish transactions are themselves malleable? DOH! :("-- I malleated your message.

What the anti-malleability changes mean is that unwanted parties cannot change a transaction in a way that changes the txid and leave the transaction valid.

What Peter is talking about is cases where someone changes a transaction hands it to you and you reject it, and how you handle later receiving the unmodified copies or other, different modified copies. In none of these cases is the txid changed while leaving the transaction valid.  The software already handles several corner cases around these, and it turned out that there were a couple more to address.



> Why no segwit ACKs ?

Many of us have been waiting to ACK for the [rebase](https://github.com/bitcoin/bitcoin/pull/8149) as that is actually what will be applied. (It produces line by line identical code as the other PR, but merges in the improvements from the earlier review into a single stream). Review is inclusive of the stream of commits, and not just their effect. And the first time he asked, indicating that he believes he's done making improvements in response to the review stream, was recently.

>   any other reasons you haven't reviewed segwit

Do not confuse lack of review from lack of ACKs: there is useful review from over a dozen people on the [non-rebased PR](https://github.com/bitcoin/bitcoin/pull/7910). 
I must resist the urge to create a new Reddit post, linking to this Reddit post with the title: "Eragmus: Jameson Lopp: Jonas Schnelli: Bitcoin Improvement Proposal 151: Peer-to-Peer Communication Encryption"
I'm not going anywhere. Cheers.
No, they are not. Bitcoin mining income (and IP payments) were always pay to pubkey, not pay to pubkey hash.

Why do people dogmatically argue with me on things like this? :-/

*Edit*: The pubkey used for the output in block 1 corresponding to address  12c6DSiU4Rq3P4ZxziKxzrL5LmMBrzjrJX is 

+ 0496b538e853519c726a2c91e61ec11600ae1390813a627c66fb8be7947be63c52da7589379515d4e0a604f8141781e62294721166bf621e73a82cbf2342c858ee

So I guess I must be Bitcoin's creator. Hear me roar.
Indeed, facts about general stack based languages which are _untrue_ for Bitcoin!
The bottom part is either technobabble; or someone trying to explain a something like my Zero knowledge contingent payment protocol but doing a really poor job of it.

I'd say the first part is pretty conclusively bunk; the second part is either bunk or a really nervous attempt at explaining something far too complex for the context.
Limitation of the twitter medium I assume there. I don't know why he's invoking "blockstream" there.

I asked Jeff on IRC what specifically the 75% that was in his document meant:



13:44 <gmaxwell> Technical question, it's unclear to the writeup how exactly you suggest miners signal their new size preference?  are you thinking that mine
rs express a preferred size in their blocks, and then the 75-th percentile size is used? (subject to the 2x limit) ?
 
...

13:50 <gmaxwell> What made you go for 75%?

...

13:52 <jgarzik> 75% = political science.  3/4 majority. 

...

14:09 <gmaxwell> as far as the actual mechenism (which I don't see in the BIP); what I'd guess is that each block would express a preferred limit in the coinbase, next to the height,  and then at the update period those limits would get processed? e.g. taking the n-th percentile?  

14:10 <gmaxwell> (if that detail is in the bip, I'm missing it)  

...

14:14 <jgarzik> Anyway, on the BIP 100, basically the floor (least) of the range of most popular miner suggested sizes  

14:14 <jgarzik> "we all agree on this floor"  

14:15 <gmaxwell> jgarzik yea, if it took all the sizs, sorted, and then discarded 25% of the smallest ones, then took the smallest remaining size, it would accomplish that.

14:16 <jgarzik> size can decrease too, with 1MB absolute minimum.  

14:16 <gmaxwell> though maybe a number different than 75/25 would be good, I should talk to petertodd and see if his position would be changed if, say, it were 90%/10%.   Basically if you're below 5% hashpower you're not even getting a block per day on average, and so you couldn't even be doing much to prevent censorship.  

14:18 <gmaxwell> But e.g. should be we changing how the limit works slightly, e.g. making the 'size' include the change in the utxo size as I'd proposed before? so that the limit actually reflects the real costs in the network; .. absent something like that fees will never peanlize costly behavior there.

14:18 <jgarzik> nod - IMV tweaking those constants are easy while getting us past the 1MB hard fork

14:18 <jgarzik> & addressing governance are the hard parts

14:19 <jgarzik> agree RE UTXO - economically signaling those costs is important


I believe this is the entirety of my conversion with Jeff on the subject.  (I've removed many interspersed comments because IRC usually bifurcates into multiple threads of discussion due to latency; but I believe they are unrelated to this topic).  AFAICT, I never suggested 20%  but rather suggested a sensible meaning for what a 75% threshold might mean in his scheme, which to my eyes was under-defined but might have already meant that.

I don't think there is anything wrong with 20% as things go; though the main limitation in BIP100 (assuming any of the under-specified parts are filled in with things I find unobjectionable) is that it addresses only miner-vs-miner incentives issues, and even then only under assumptions about hashrate distribution which are currently untrue (e.g. right now less than one percent of the miners have >>50% of the hashrate, so BIP100 is basically a blank check to those few parties up to the 32MB limit).

Jeff's stated view is that users can be protected by exiting Bitcoin if miners are not acting perfectly, but there is incredible friction around markets (e.g. solution via exodus guarantees losers)-- "sell all your bitcoins and go use something else, is a really weak threat, in general. Because of the weakness of the threat I don't think this is a reasonable first-resort mechanism for assuring the security of the system-- I think we've already seen it disproved in practice, and under that argument, e.g. we could just give miners the ability to print infinite bitcoin, steal arbitrary coins, etc.

I wouldn't be shocked if you could come up with 10MB (esp if you don't mind being non-conservative about unknown unknowns, which very much seems to be the ethereum way).

But look at the proposals that I've found I couldn't support: 20MB -- oh hey, thats twice that a third party and a not very careful review thought it could support,  and another proposal that is 8MB that immediately doubles over and again and its very first step ends up being 50% more than that "could do 10MB".

Discussion around-- say, 2MB-- sure okay, it may make the current situation worse but probably not irrecoverably worse. But what actually does it buy us? It's advancing the timing a few degrees... you get a bit of knock but probably won't throw a piston. It doesn't qualitatively change the application space (but real scalablity does!), but it does create bigger risk of failure-- e.g. an strong expectation that it will just keep getting bumped and no one will ever pay fees and we currently have no reason to believe that this is viable long term. There is some uncontroversial level that I could support but not love, though -- how do we avoid undermining the fee market, how do we avoid making a promise to always increase it?

We shouldn't have a blocksize that is limited to respect decentralization in the same way that the US has a finite copyright where works eventually go into the public domain. (We don't: Congress keeps retroactively extending copyright, and the supreme court decided that an infinite series of retroactive finite extensions fits the constitutions requirement of "limited times").

You mention flexcaps, well-- I proposed one (actually several over some time), and I think they're an important partial answer to the above...  but "Breaks make for slow, take off the breaks! Removing the breaks is simple. What if the turbocharger doesn't work?" ... in the court of sound-bites thoughtful solutions are somewhat disadvantaged.  They aren't a cure all: they still leave open the long term security question, but they're a good step forward that perhaps doesn't result in the wheels coming off. :)

The active development team has been quite able to come to agreements about many important and tricky technical issues in the past. I am not experiencing any lack of confidence on that front. 
Anyone who admits to using it, anyone trustworthy that distributes software for it.

A money whos use you need to keep secret is not very useful, ... and a digital currency where you need to get software from sketchy sites or write yourself is much less usable.

Patents can be selectively enforced without losing their power, so ultimately the answer becomes everyone you think you can get money from whom you're not concerned with challenging your patent or publishing alternatives or workarounds.

Maximizing the decentralization of the system is one of the tools we have to reduce the attractiveness of adverse use of patents, if attempted-- as going after individual users scales much less well than large targets-- and I consider it necessary, but decentralization alone is not sufficient to prevent all potential harms from patents on cryptosystems.
I was pinged to respond here--  (as an aside, Welcome to Reddit, "moon_pat-pend"!)

The comment I made in Wizards was referring to a couple things, including the micropayment bot design discussed most in depth in [#bitcoin-wizards on 2013-03-01](https://download.wpsoftware.net/bitcoin/wizards/2013/0%23bitcoin-wizards.log) (search 'micropayment bot'); which covers a payments system which builds a chain of balance hash-tree ledgers which are signed by a central validator and are subject to compact fraud proofs (an idea I wrote about extensively for future Bitcoin scalablity since at least 2012; and which are also described in section 8 of bitcoin.pdf, though without the compact part).

Rather than just assuming semi-honesty, we go on to teach the use of smart contract fidelity bonds to create a costly "identity" for the CV without requiring issuance from a central authority, as well as financially incentivizing participants to publish their evidence of fraud. These bonds need an external decentralized system to function, they can't be maintained inside the system they're securing.

I have not yet read the paper in depth-- only glanced at it to determine where in my reading queue to stick it.

The latter parts of it would appear to run into many of the [problems](https://download.wpsoftware.net/bitcoin/pos.pdf) that the communities "proof of stake" ideas do (as that is effectively what it's describing, without reference to the prior work in industry).  For example, their "preferred embodiment" of the verifier selection function H(pubkey, ntime) < P  is trivially subject to grinding: You simulate the network's future selection of verifiers, and keep trying to select pubkeys until you end up with ones that have the properties you want (E.g. highly preferred for a contiguous chunk of time).  Many of these issues have been discussed in great depth, and -- as typical for cryptosystems-- many of the simple and obvious solutions suffer considerable challenges that aren't themselves immediately obvious.
Caught based on the blockchain may be going too far.  Force seems to have been caught red handed with the bogus subpoena.  Both had muddy prints all over various bank accounts in their names.

The Bitcoin evidence clearly helped, but it wasn't the first tipoff and doesn't appear to have been strictly necessary.
> Surprise surprise, Luke-JR..the for-profit Blockstream co-founder/colored coins advocate (backed by $21 million wall st money) 

Blockstream has nothing to do with this-- (and, in fact, you can go and find several blockstream folks yelling at luke and saying that his patches are kinda lame and the deployment needlessly controversial: you seem to know this since you refer to some of it with "against the will of other core devs"), and Luke is not a founder of the company or a regular employee, you can happily see the list of founders and full time employees here: http://blockstream.com/team/ and might note that he isn't there.  (Also, "wall st" money?! 0_o) ...  Not everyone who contributed to the sidechains whitepaper works for Blockstream.

I'm not sure what people should suspect from someone who's first post on Reddit was telling people that Bitcoin Core was written in Javascript. ... but apparently people believe whatever is put in bold text. :)  But maybe I'm wrong and you're not intentionally trolling? If so-- I hope you'll edit your post.

[Pedantically though, though I think the patches are a really stupid kludge and super inadvisable, they're a build option in gentoo (A use flag), along with a number of other assorted things of greater or lesser advisability. As a gentoo user myself, they never have bothered me. As a Bitcoin Core developer, I support people making their own versions, even if they're ill advised. Saying people can't make and distribute modified versions and encourage people to use them would be centralization that I could not support. In this case no one is being forced against their will here. ISTM you're just fanning drama, especially with the weird and misplaced religious remarks.]
It's possible to do the whole thing in zero-knowledge and leak nothing but the yes/no result... though doing it that way is somewhat complicated.

More simply— without the ZKP moon math if you don't mind leaking the exchange total: you do as you understood to prove the holdings, and then the exchange constructs a binary hash tree over the accounts with all the interior nodes also having the sum of the account balances.  So at the root of the tree you get a hash committing to the full tree and a sum of the obligations.  When you log in, it would give you a hash fragment to prove that your balance was included in the total which client side JS would verify.

(The tree doesn't have to be balanced, and can be laid out to minimize leakage about accounts).

This would leak the total holdings, and some small amount of data about the number of accounts and distribution of their funds, but far far less than all the account balances.  Importantly, though— it could be implemented in a few hundred lines of python.
Nothing being said there is new or shocking.   This kind of out of context excepting from IRC coupled with exaggerated headlines strongly disincentivizes me to comment further. Or, for that matter, continue contributing in the Bitcoin community at all.

I hope your 5 minutes of market manipulation you get out of this is worth it to you.
His copycat behavior certainly isn't limited to the last week or whatever is being claimed:

Second in his top comments for me:

http://www.reddit.com/r/AskReddit/comments/uessk/can_you_oneup_me_in_most_offensive_joke_you_know/c4urjyr

http://www.reddit.com/comments/kqkez/reddit_what_is_the_most_offensive_offcolour/c2me3y0

There are also a lot more which are edited down from older highly voted reddit posts, including ones which are in no way just repeating classic jokes but are actual copies of people's apparently personal stories:

http://www.reddit.com/r/AskReddit/comments/sg8zj/pfft_like_its_just_us_biggest_turn_offs_for_a_guy/c4dsezb

http://www.reddit.com/r/AskReddit/comments/k6ag0/reddit_what_are_some_embarrassing_sexual_moments/c2htlp0
But did you know that [in the US that it can be lawful to use deadly force to resist a false arrest](http://www.constitution.org/uslaw/defunlaw.htm)?
The FBI wants its little honeypot to stay up and running, obviously.

 

Can I get sloppy 22nds? 
meh.

I think "bitcoin" companies being tech laggards and spending their time adopting yet another altcoin rather than keeping current is a real concern. ... one that even draws question on the competence of regulators (not that we really needed more examples...), since regulated exchanges keep getting approved to add more altcoins when their Bitcoin support isn't even current to Bitcoin circa 2013 (e.g. uncompressed pubkeys, no batching, no segwit, no sending to bc1 addresses).

But breaking backwards compatibility isn't a good way to do it.  I think the message you're sending is "you should oppose improvements to Bitcoin, because eventually you're going to be _forced_ into using them.".

As an author of [BIP 173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki) I think BC1 addresses are a good improvement, but not remotely important enough to give people a justifiable basis to oppose future changes.

When we designed segwit we explicitly left out a new address format in part to avoid tying segwit to the ability to send to new addresses, since we knew that it was going to take a long time before wallets and services could uniformly send to them. IIRC, it took something like two years for bc.i to be able to send to p2sh addresses.

Right now it seems many businesses prefer to add altcoins instead of keeping their bitcoin support state of the art because thats what makes them money.  This equation changes if people choose to do business elsewhere, and people can make that choice without breaking compatibility.  Organic customer demand at least lets them stick with a cost/benefit trade-off and make their own decision. And besides, I think the failure to stay current sends a valuable signal about a business' operations that we'd lose by forcing it.  If they're not making other simple updates, what does their security look like?
Bitcoin is not slow and expensive to exchange, at worst you could say it is slow OR expensive to exchange....  but compared to gold itself which cannot even be transferred electronically, you can't even say that!
Right now it would only take a market buy of 637 BTC worth of BT2 to push its price over 0.5. ... the market is thickening up, but it still has a ways to go before it couldn't be jerked around a bit.

If potentially doubling your Bitcoin in exchange for getting rid of your (aptly named) 2xcoin sounds like a good deal, and you have some funds you're willing to tolerate exposing to Bitfinex counter party risk... you can join the fun in defending the 0.5 mark by depositing BTC in finex, splitting it (under the order types, choose token manager), and setting a limit sell of BT2 at a price under 0.5 (but higher than whatever its currently trading at).  If later it looks like your trade isn't going to execute, you can get pre-fork Bitcoin back and withdraw it by canceling the order and remerging your BT1 and BT2 into BTC (again via the token manager button).  If your order does execute, you'll be able to withdraw your BT1 as bitcoin (without s2x coin) after the fork.

Edit: you need to agree to the split token terms and conditions before the token manager option shows up in the order type list apparently.

0.5 is the price where if these prices reflect the market prices at forktime mining would be equally profitable on both chains. 0.5 is also the price where if you sell all your BT2 at that price (then split the result, sell it at 0.5, and so on) you would ~double your post-split BTC.

Remember: Frequent trading is a great way for most people to lose money. You should decide what prices you want to act on and resist twiddling them emotionally. "Think like a trader", be dispassionate.

[Make sure you read the terms and conditions, but I think they are quite reasonable.]

Yes.

The two parties make a two of two payment into an escrow that won't release for a couple months.

Before announcing it they make a release which will only be valid on S2x by virtue of being nearly 1MB in size. 

The big s2x release transaction will require a fairly high total transaction fee due to size... but it's not like exchanges are free. It's comparable to exchange fees if the amount traded is over about $10k.

If someone wants to make some trades like this, there more than a couple Bitcoin developers that would happily take 1:1 trades of 2xc for BTC; myself included; at least if the volume available would make it worth the effort to do it. e.g. not worth bothering for just a couple bitcoin. 
This article was just written based on a public presentation that was linked on reddit last week: https://www.reddit.com/r/Bitcoin/comments/6xj7io/greg_maxwell_a_deep_dive_into_bitcoin_core_015/

You wouldn't gain anything from this article over watching the Q and A portion of the video at the end, other than some editorializing. :)
How does this thread support your claim? He has been inactive for _years_, removing someone from a list for people to tag them in issues who has been inactive for years isn't "eject or driv(ing) away" anyone.
Care to cite some evidence to support your claims?
And then they do it again, and it seems many don't learn.

I dunno, I find it kind of demoralizing.  This way it's hard to ignore that it doesn't seem to matter if things work or not.
To joe-blow technical terms strung together in almost random order sound indistinguishable to "specialized IT vocabulary".

If you really understand something you should almost always be able to explain it without much specialized vocabulary-- it takes just a bit more work.  The purpose of language is to communicate, using excessive techtalk fails that purpose with an audience that won't understand it.

He evaded questions about who is funding this work... not like there was any doubt before...
> Compact blocks doesn't help on-chain scaling nearly as well as xthin. It may as well not be in the same category. 

dear lord.  Okay, I accept the challenge:  Explain to me why you believe this and I will try to correct the confusion.  You really are just flat out wrong here, and I'd like to work through why you believe it. Obviously something has not been communicated.

>  what on-chain scaling efforts do you have your eye

You know that the folks working on core are responsible for multiple orders of magnitude scaling improvements already. Right?   The capacity document lists many scaling tools which I and others think are interesting.

Probably the most pressing after segwit is signature aggregation, which is a 28%-ish increase in capacity straight away.

> proposed by respected researchers with real credentials

Most proposals radically change the security model which makes them non-starters.  I think the key idea of Bitcoin NG in separating the mastership race from transactions is very useful, but think that it should instead be done like weak blocks, since it is both compatible and doesn't change the security model to do it that way. (e.g. doesn't introduce miner identities).


Welcome to Reddit!

(1) The Core project did not agree to squat.

(2) A few individual developers did agree-- on their own and against strong suggestions to not even attend the meeting-- to make proposals for hardforks agreed to do so AFTER segwit (which hasn't activated yet), conditional on miners not signaling classic (which they did anyways), and yet they still did [the proposal work](https://github.com/luke-jr/bitcoin/compare/bc94b87…luke-jr:hardfork2016) they said they would.
> Craig Wright confirmed that he would provide extraordinary proof.

You missed a step.

His "extraordinary proof" was that he claimed he would sign a message with the 'satoshi's keys'-- but what he provided was a copy of the signature extracted from the second transaction in the blockchain,  wrapped in layers of obfuscation to make it seem like it was a signature of a message he provided.

It was thoroughly debunked shortly after posting it.  Only after that did he post the lame backout message.  Of course he said he would be gone for good-- but as we all expected, he's back again.

Satoshi dundee is a con-artist that is sophisticated enough to deceive lay people that view all sufficiently advanced people as equivalently competent ("Maybe Bob created Bitcoin, he created a visual basic windows registry auditing tool once!"), but not sophisticated enough to actually fool anyone who is all that technical, or to even realize how transparent his lies are.

I feel bad for his victims, but he seems to exploit greed -- and its really hard to save people from themselves.
Any protocol improvement that requires a hash in the coinbase transaction (left side of the tree) that changes based on transactions in the right side of the tree is incompatible with the most efficient covert boosting implementation.

This doesn't just cover segwit, but also a half dozen other previously proposed protocol improvements. (Exception blocks and script versions are pretty much the only major exceptions-- almost every other major improvement proposed is at least somewhat incompatible).
> 1) I'm talking in the context of SPV nodes that will be more capable than today.

Strange, because Mike Hearn fought tirelessly against increased validation in SPV nodes-- which is also the reason that Bitcoinj doesn't verify pretty much any of the things that can be efficiently verified by one.  And I was of the belief that you and Roger were stridently opposed to segwit which helps move somewhat towards making fraud proofs possible.  We've been working on this for [years](https://en.bitcoin.it/w/index.php?title=User:Gmaxwell/features#Proofs) but without support from SPV software authors it largely seems like a waste of resources. 

I also thought you supported BU and 'emergent consensus' proposal is the polar opposite of more capable SPV nodes-- because it degrades every full node to current SPV capability when the miners overrule them.

That aside, I'm not sure how your thinking meshes with the current state of SPV nodes-- there is [very little](http://luke.dashjr.org/programs/bitcoin/files/charts/software.html) P2P SPV out there at all these days... the resource costs of scanning-- which would only be made worse by larger blocks-- have pushed people to various kinds of hosted and sever driven wallets.

The Bitcoin whitepaper posed the view that at least merchants would run full nodes, but in practice we've found that the resource requirements today cause them to not do so. Sadly.  Instead they depend on centralized api service-- which is their choice, but if people are willing to use centralized services there are some fantastically more scalable options possible that don't degrade Bitcoin's decentralization for those who choose to use it... 
When BU staff gets called out for dishonestly blaming core for their screwups I guess their responses is "better double down!": https://twitter.com/digitsu/status/845372912798347264
oh man. Compact blocks were included in _0.13_ several people considered them a blocker for segwit so we got them in there.

Their performance is improved considerably in 0.14 but they've been fully included, fully performing, and fully functional since 0.13.0, and are running on 73% of the listening nodes on the network now!

We really do stink at bragging about improvements. :(
"other" a list of _100_ people isn't enough to be other for you?

I think you might be suffering from some confusion over the model. The bitcoin project isn't a company, it's a massive collaboration, each developer is independent, has their own repository.. they collaborate because its in their rational self interest: This work is too hard to do safely alone, and combined we can do much more.

But there is a lot more diversity in the right column, doubly in the case of BU because BU itself is a closed membership organization where you can't participate without getting approved by the existing members, and swearing to uphold their bizarre articles of confederation.  Literally the people, like jonny1000, who provide critical review are unable to be contributions!

Where in Bitcoin Core the productive contributions and technical opinions of people who occasionally throw rocks are incorporated.
> There is no way in hell it will activate

BIP 66 did, and yet it had less hashrate signaling it at this amount of time since the start and it was easier for miners to start their signaling.

The not going to activate FUD is precisely what this graph is countering.  No one was running around saying BIP66 wouldn't activate, even though it had less hashpower at this point. ... and it DID activate.
Segwit actually does do more with less in several ways:

(1) It radically reduces signature hashing costs-- it changes it from O(N^2) to O(N) and even for pretty ordinary transactions it does a lot less hashing.

(2) Segwit's capacity increase doesn't come with an increase of the UTXO set bloat permitted-- one of the main drivers of node costs-- 

(3) Because lite wallets don't generally need witness data, segwit allows them to operate with less bandwidth than they do today (where they're forced to take witness data just to check txids, then throw it out).

(4) other nodes that are fetching the chain but not verifying it could skip fetching witness data. For example, the current Bitcoin "Classic" code doesn't verify signatures for any block where the miner has specified a timestamp more than one day (*number of cores) ago from your perspective.

(5) Making improvements like aggregation much more practical to deploy is still am important improvement, even if the benefit isn't realized yet.

Whats this talk about 75% as the distinguisher? The ETH hardfork had a lot more hashrate than that.
When things that are in progress show up, some people dismiss them as speculative and incomplete. The explanation available are often not digestible by people who aren't experts with daily involvement-- people with strong communications skills haven't had a chance to write explanations. Etc.

Then when it's final, it's "old news, we heard that last week".

We get this with releases, when we tag the version for release people run ahead with the announcement even when the release notes haven't been proofread and when the downloads aren't available. People go splat trying to download it and leave disappointed. Then a day or two later the formal announcement goes out and doesn't get much attention.

It's not a big deal, but its a bit disappointing-- and it creates pressure to be less open and inclusive, to do things like coordinate a release in private so it can all show up at once, that I'd rather not see.  
Nice FUD there.  This is an operating system vulnerability.  Hardware wallets _may_ be good advice (though some have contained some pretty questionable crypto at times); but none of the rest are going to protect you.

Edit: (As an aside, the electrum client performs a TON of name resolutions; but seriously this is not a bitcoin specific issue. People with impacted operating systems _must_ update)
Looks like BTCJAM got hacked...

"gmaxwell+btcjam_test, we have a message for you"
Somewhat ironically, I was explaining to Warren my position on coloed servers for likely-to-be-targeted Bitcoin infrastructure which was basically that almost no data centers seem to be durable against these attacks.  Basically everyone in the space makes a big show and big claims of security, but they're often trivially bypassed.

(Well, there are high security facilities which have a better chance-- but many of them you cannot locate equipment unless you're government/contractor)
Sad,

This appears to be a misunderstanding of how the timestamps work.

> he two hours which timestamps can be set in the future is greater than the expected amount of time since the median of the last eleven timestamps, and eleven isn’t all that large a number, so an attacker with much less than half the mining capacity can set all their timestamps for two hours in the future and eventually get everyone else’s mining operations to get rejected by sanity check on timestamps

You can mine blocks with timestamps in the future, but if they're past the limit _by each node's local clocks_ they're ignored. Setting your blocks in the future isn't going to make anyone else produce invalid blocks.

There are some interesting things that can be done with times in the system which fail to have effects for more subtle reasons; but this isn't one of them.

> The much better and more immediate fix is that miners should check what the earliest acceptable time for their next mining operation is, and if that time is somewhere in the future they use that for their timestamp instead of the actual current time. 

pow.cpp:103:      pblock->nTime = std::max(pindexPrev->GetMedianTimePast()+1, GetAdjustedTime());

This is in every version of bitcoin since the very first release, it's also specified in the various mining protocols (e.g. https://en.bitcoin.it/wiki/BIP_0023#Mutations see mintime).

[As an aside, Testnet has many crazy sequences of timestamps in it from actually testing this and other more complex timestamp handling behaviour on a public network.]

> I calculated these values using randomized simulations

Perhaps 30 seconds from the simulation would have been better spent looking at the software or reading a mining protocol specification?

> Now on to the actual attack.

So did the author actually believe they had an attack here?  If so why didn't they practice responsible disclosure and email bitcoin-security@lists.sourceforge.net  ?  

They would have quickly gotten back a response explaining how this doesn't impact the actual system and could have saved a bunch of people time reading the public post.

I can confirm— shows the option for me too. Looks like a bitpay partnership.
The load a transaction imposes on the network is only related to the size of the data required to encode it, not the value transacted.

... and movement of large old coins is naturally rate-limiting, so such transactions can't be used to DOS attack the network.
00:39 -!- SarahCoinBit [~SarahCoin@mtgox/staff/SarahCoinBit] has joined ##mtgox-chat
00:40 < SarahCoinBit> I did not get fired and i have never posted in reddit in my life

Newly generated Bitcoins cannot be spent until they've had 100 confirms.

This makes it so that when you receive a normal bitcoin payment  you don't have an unusually high risk of it becoming forever invalid— say without any dishonesty at all— because a reorg replaced a newly generated coin a couple blocks back.
H264 is a format, not an encoder— it doesn't have a time to convert speed.

For whatever its flaws Theora is a simple and pretty fast format.  It's slower to encode to than x264 primarily because x264 has a multithreaded encoder with tons of the latest SIMD optimizations and there isn't one for Theora.   Feel free to write one!
It's absolutely correct that you can open a channel a side effect of making a payment, -- in the future I expect that to generally be the case... it also helps achieve a good channel topology.

But that doesn't mean there is no additional cost-- the cost is that you have to use software capable of doing that.  In the long run that cost won't be much, but today lightning software is immature. 
> I don't know man, I can Venmo someone money for free and they get it in seconds. 

With Venmo the money isn't "there", they can seize and freeze funds (and do).  A centralized system which you trust can always have higher performance and lower costs than a decentralized one.  This is one of the reasons that it's silly to expect Bitcoin, which is a _currency_, to directly compete with centralized payment systems.

Venmo should support Bitcoin, just like Visa supports some 200 currencies, and then if you're happy with how venmo works then you should use venmo to exchange Bitcoin.

Personally, I'm not happy with how venmo works, and I'd rather use lightning for your example cases... but thats a choice we have, a single currency can be exchanged many different ways.

> You are correct gold isn't electronic, but it is free to transfer and instant. I just hand it over.

If the recipient doesn't mind getting a bit of gold plated tungsten instead of actual gold... and in the small subset of transactions where you're actually physical with the person you're transacting with.

> What if it stops going up?

What if?  If Bitcoin can't survive the price not going up it shouldn't have existed in the first place.  I'm confident it could survive that just fine.

This is one of the shittiest things about Bitcoin. IMO.  Back in 2011 the media was full of FUD saying Bitcoin was a Ponzi scheme.  Many people, including myself, correctly explained that it wasn't one--  Other people went "Hmm. Ponzi scheme? I'm going to get in on the ground floor!"  I'm getting pretty exhausted in dealing with the latter group in the Bitcoin space.



> In what way can a node be made to compromise user privacy any more than a standard run of the mill node?

They connect to every reachable node in the network and try to determine the origin of transactions by listening tp where transactions are announced; and other related deanonymization techniques.

These efforts are significantly undermined by inserting blind spots in their observation.

> I'm assuming this is something along the line of requesting block data or header data excessively/unnecessarily with malicious intent to tie up the limited resources of peers

Not so simple. If you connect to every node and then use a 'normal' amount of traffic from that node,  instead of connecting to only 8 or so as usual, you end up using thousands of times the normal amount of resources; but from any single nodes perspective your don't look too unusual.  My lists are made from correlating information from two dozen nodes run by various developers and community members.

Part of Bitcoin's robustness comes from its diversity.  Because only a portion of nodes will ban from lists like this, they don't create connectivity issues for anyone, even if there is a false positive.   But because some ban from this, attack strategies that require being connected to all or almost all listeners fail.
> Someone with SN's keys

Nope.


> Are you telling me that the core/blockstream developers sat very very quietly not opposing this.

wtf.  Many people in the community, including [almost all the most active developers](https://en.bitcoin.it/wiki/Segwit_support) and myself vigorously and loudly opposed it in [completely uncertain terms](https://www.reddit.com/r/Bitcoin/comments/6h612o/can_someone_explain_to_me_why_core_wont_endorse/) and people on reddit sure seemed to know about it (see the title of that thread).

Please take your incredible dishonestly elsewhere. "malice and sociopathic behaviour" indeed.
it's rejecting it's own blocks... even with a lot of transactions around.
>  But still it is horrible when you come with probably a great coode like Segwit and it is implemented on shitcoins only.

Have patience, and mediate some on the rest of my message: I am not saying that I do not care because of some kind of monastic ambivalence, I am saying I don't care because things like segwit are utterly unimportant /compared/ to the overall importance of Bitcoin.  To prioritize segwit over Bitcoin would be a severe example of being penny wise and pound foolish.

I'm quite aware of the manipulative actions of others, which is why I'm sitting here writing to you and others, one on one-- dispelling misinformation.  Even though it results in near constant attacks and criticism towards me, and has made me a direct target of more offensive actions than you are probably imagining.

But there is also only so much I can do and there is significantly diminishing returns.  I need your help. Would you like to help?  We're all in this together.

In terms of perspective-- a while ago BGP (the routing protocol at the backbone of the internet) was extended to use 32-bit network IDs instead of 16-bit ones. The extension was done in a backwards compatible way, like a soft fork.  The original proposal for the technique was in 2001.  The standardization effort didn't finish until 2007.  Support wasn't available in production router operating systems until 2011, and wasn't deployed on the internet until 2012 (upon which time it caused internet wide partitioning because a design flaw made it not quite a softfork :P).  But BGP has almost everything in its favor: It's deployed by professional industrial parties, implementations are authored by corporations with billion dollar a year revenues, there are many thousands of experts in working with it. When routing fails the harm is a denial of service, but generally not much in the way of irreparable loss. There is little to no risk of malicious parties with competing systems trying to make the Internet fail. Internet routing only has certain minimal consistency requirements (no loops) and doesn't require a lockstep consensus like Bitcoin does.  Yet the "change a 2-bytes to 4-bytes" change still took about 12 years.

You can see patterns like this in any distributed protocol or piece of infrastructure. Change takes time and for good reason.  Even in companies where a single party controls the whole thing a typical development to release pipelines (especially for infrastructure work) is easily months to a year long.  As consumers we often don't see this-- the new functionality is kept private until its just about ready to go live, after trial runs and betas, testing etc.  But the time is still spent.

Yet, people expect Bitcoin with billions of dollars in value at risk of irreparable damage and with a lack of central authority/control being a selling point, to just upgrade at a drop of a hat.  It's not realistic. Some delays and frictions are inevitable and healthy.

With that perspective, I think you would realize that segwit is moving quickly-- not slowly, and your concerns are misplaced.
I  am not saying that chains are not valid, but if you count them when counting the number of coins in the mempool you will DOUBLE COUNT (or worse).

If transaction A spends 10 bitcoin, and pays out 9 in change + 1 in payment, then transaction B spends the 9 and pays out 8 + 1. And transaction C spends the 8 and pays out 7 + 1, then D spends the 7 and pays 6+1...  and then you count all four you'll total up 10+9+8+7 = 34 BTC ... even though only ten actual bitcoin were even involved.

The code I posted counts that sequence as 10 (e.g. both the payment and the change-- but never double counting or leaving anything else).

If you'd like to do something more sophisticated-- the code is there.  What the code I posted doesn't count is when D also has some novel already confirmed input. Unfortunately, because of the way the Bitcoin protocol works, getting the input values is a real pita, but outputs plus fees sum to inputs so thats what I did.
Fiat currencies are centrally administered-- and many people find Bitcoin interesting because their monetary policies don't jive very well with strong property rights.

So what happens when I decide that it would be in everyone's best interest if I went into your home and started 'melting down' all your stuff?
PSA:   I've been able to recover the changes in this binary, and they're massive.

Among other things, they've removed all the runtime state corruption protection... so cases where nodes would cleanly and safely shut down in the event of things going wrong, turn into potential consensus splits or even remote code execution.

No, in fact, you don't.  That's why Reddit and pretty much every online venue has moderation of some kind.

Over at rbtc they're happy to leave people posting factual corrections in rbtc rate limited, they freely ban accounts that post links to [an article](https://medium.com/@WhalePanda/the-curious-relation-between-bitcoin-com-anti-segwit-propaganda-26c877249976#.4mfo9qn3e) showing that the employees of the company who's employees are most of the rbtc mods is behind some of the segwit attacking sockpuppets. So you can't even argue that rbtc is unmoderated (even if being unmoderated were technically possible on reddit).

A person or community being able to create a moderated forum is integral to their own personal freedom.  It isn't enough to be free to say what you want, you must be free to not say, or create platforms that say, what other people want.

Without this freedom someone who wants to silence you could simply flood any venue you try to use with noise-- abusive, incorrect, distracting, offensive, spam, etc.-- and bury your messages and drive away the readers. This is precisely what people tried to do in rbitcoin a couple years ago-- with hundreds of sock accounts flooding the subreddit with untruthful advocacy of bitcoinxt. If you're free to speak but not to have an audience, you're not actually free to speak.

Much of the misinformation in rbtc isn't 'taking the good with the bad'-- it's intentionally promoting an agenda, dishonestly. The bad is the point.

It's good that alternatives exist-- at fact itself integral to personal freedom, but that doesn't mean any particular alternative is good. (including rbitcoin, it's clearly far better than rbtc but the moderation is IMO likely too heavy handed.)
Upgrading is easy and painless (assuming you're running anything remotely modern):  Just shut down Bitcoin, install the new version, and start it up again.  [It's a good time to check that your wallet backups are good.]

I highly recommend upgrading. 


> The reason there isn't definitive dates on the roadmap is because software engineering and testing often takes longer than anticipated. 

That is one reason though another one is that a roadmap for Bitcoin Core isn't a guaranteed roadmap for Bitcoin-- if anyone were able to produce one it would mean they controlled bitcoin and Bitcoin would be a failure. A lot of people are _obsessed_ with working with centrally administered authoritarian systems and continually try to cast everything into that light, Bitcoin's health depends on all of us resisting that. In Bitcoin the system's value depends critically on no one being in control of it-- but the funny thing is that highly authoritarian development models aren't the norm in many critical pieces of technology infrastructure... the IETF, which creates most of the internet protocols we use, doesn't work like that, nor do many pieces of important free software.

This is a response I wrote to an email from someone trying to get the Bitcoin Core project to "roadmap Bitcoin" a month or so ago:

The Linux kernel is one of the largest and best funded open source
projects, which produces the most widely used operating system kernel
in the world and one of the most widely used pieces of software of all
time, and it produces _no_ roadmaps.

Quoting Andrew Morton, "Instead of a roadmap, there are technical
guidelines. Instead of a central resource allocation, there are
persons and companies who all have a stake in the further development
of the Linux kernel, quite independently from one another: People like
Linus Torvalds and I don’t plan the kernel evolution. We don’t sit
there and think up the roadmap for the next two years, then assign
resources to the various new features. That's because we don’t have
any resources. The resources are all owned by the various corporations
who use and contribute to Linux, as well as by the various independent
contributors out there. It's those people who own the resources who
decide."

Linus remarked, "I look at the current release and the next one, as I
don't think planning 10 years ahead is sane."

Yet the Linux kernel still has every advantage over us:  They have far
more contributing resources from far more sources, they have a fairly
centralized model and control over their own destiny because they have
a much more functional pathway to disagreement than we have in Bitcoin
for consensus rules.

IMO the best way to do "roadmaps" in Bitcoin is to roadmap the finalization
and release process once the basic technology is done; because it's
only past that point that guarantees can really start being made.

We can also talk in a visionary sense about the sorts of things we're
exploring, but it just isn't possible to nail down when they'll be
ready until they are: If it's not something the linux kernel can do,
it's not something we can do.

At least not as a group. Individually contributing parties have a lot
more visibility and control into their own activities, and there is
virtue in forecasting at that level. Redhat, for example, has
roadmaps. They primarily deal with stabilization and support of
already existing technology that you could get in the raw from the
various upstream sources (fedora, kernel, etc.). (see for an example,
http://www.slideshare.net/albertspijkers/red-hat-enterprise-linux-roadmap
)

Separately, what we can do in Core is have timely reliable
releases.  Juniper made it about 10 years with regular timed releases that
did not slip by more than a few days and which were _all_ production
deployable (how that broke down is another story).  This was an incredible benefit to our
customers, but the only way it was possible was because _features_
were not guaranteed in a release. If a feature failed during the final
testing and it needed more than the most trivial of fixes, it was
_removed_ or disabled.  As soon as there are multiple in-flight
deliverables it becomes more important to be timely over-all even that
that significantly delays single deliverables. This is effectively at
odds with hard deadlines on functionality, even before getting into
the fact that for consensus features delivery in software is
irrelevant until activation which is a public activation, not unlike
an election, which no one can guarantee.

From the perspective of Bitcoin I think what people are actually
asking for us to do is to (help) drive the Bitcoin Story-- the actual
technology and its timelines is usually not that relevant: if it were,
they'd be stepping up with resources to contribute to it. There are
many ways to do that, -- we don't have to use highly authoritarian
methods that wouldn't even work for the Linux kernel.

This can be done by a mixture of talking about research _as research_
not a done deal, and by moving discussions of non-research things to
where they're actually more forecastable.  98% of the non-developer public
discussion about segwit was before the pull request, -- there were
solid political reasons for this-- but it was the wrong timing.  On
the case of CSV and CLTV, the general public didn't hear about them
until they were merged -- pretty much-- and the timing then was much
more compatible with 'roadmapping' +/- activation uncertainty.

Some of this is driven by competitive pressure with things like
Ethereum or other altcoins (e.g. dash, god save us) that have a lot
lower commitment to engineering integrity or even logical consistency
and much more centralized development.
We can't compete with technobabble bullshit, and we shouldn't try and
as a side effect back ourselves into a corner where we're making
remarkable accomplishments but regarded as failures in return
(because we either forecast it taking N years, which is the best we
could promise, or because we forecast the best we might achieve and it
was both still too long and the timeframe got missed).

(I can repeat the same story for major RFCs, FWIW.  Collaborative
innovation is _very_ hard to stick to a tight schedule.).
UTXO behavior looks highly suspect. https://blockchain.info/charts/utxo-count?timespan=60days
Rought numbers as I might of omitted a byte here or there as this is a Reddit comment:

A typical 2 in 2 out transaction, 

nversion + ninputs + 2*(txid + vin + sequence + scriptlen + pubkey + signature) + noutpus + 2*(scriptlen + dup + hash160 + hash + equalverify + checksig + value) + nlocktime

4+1+2*(32+4+4+1+34+73) + 1+  2*(1+1+1+1+21+1+1+8) + 4 = 376

Maximum per block: 2659

With segwit (p2wphk):

 4+1+2*(32+4+4+1+(34+73)*.25) + 1+  2*(1+1+1+1+21+1+1+8) + 4 = 215

Maximum per block: 4640

If instead we do the same with 2 of 3 multisig, which are a significant percentage of the transactions on the network, we get..

nversion + ninputs + 2*(txid + vin + sequence + scriptlen + 3*pubkey + 2*signature + redeempush + dummy + checksig) + noutpus + 2*(scriptlen + hash160 + hash + equal + value) + nlocktime

4+1+2*(32+4+4+1+3*34+2*73+1+1+1) + 1+  2*(1+1+21+1+8) + 4 = 658

Maximum per block: 1519

with segwit (p2wsh):

4+1+2*(32+4+4+1+(3*34+2*73+1+1+1)*.25) + 1+  2*(1+1+21+1+8) + 4 = 281.50

Maximum per block: 3552.

These examples use direct P2WS instead of P2SH-P2WSH, which will be somewhat more common at first which is somewhat less efficient.


> Why not increasing the block size limit slightly 

Which segwit does. It does so in a way which is easier and safer to deploy and which mitigates some of the risks of increased size, but increase it does.
> inadvertent Ponzi

I don't think that word means what you think it means.

> 98% of Steem trading volume is from BTC

Be mindful of salting the tipjar in various forms and other kinds of self-dealing.  It's common in altcoin premines for the creator to make a wash sale deal-- "I'll give you a kabillion tokens, if you give me 1000 BTC, then I'll give 99% of the BTC right back to you".  Often they'll also slowly sell for BTC to investors, then buy back to prop the price. All this creates fabricated volume.

In cryptocoin land there is basically nothing to prevent these kind of activities so extra caution applies as compared to regulated markets (where shenanigans also happens-- but usually not at this scale).


>  I assume Thomas spoke to Blockchain developers

Can you ask Thomas for an answer here?

> Since ChainAnchor was developed for the permissioned world.

This is inconsistent with the plain language of the draft I received. I quoted some of this language above and asked you to reconcile it with your repeated assertions that chainanchor was unrelated to Bitcoin. Kyle responded with the suggestion, based on his reporting, that you had initially intended to apply it to Bitcoin but reconsidered -- this seemed reasonable. But if so, I would have expected conversations with Bitcoin people if this work was, indeed, not being done in secret.
> If I'm connected to an honest full node, I can get a valid block already, 

You have no clue if the node is honest or not (unless you're running it and know its secure-- in which case; you were fine to begin with and the fraud proofs didn't add anything to you).  So you can get information about a block, maybe it's a valid block maybe not.

> Why would anyone furnish a fraud-proof to me?

Why would they furnish a block to you, or a transaction?  It's a normal part of the operation of the system. They they would is a part of the assumption that you take on if you don't run a full node and instead take fraud proofs.

They're inexpensive to produce, basically a side effect of normal validation. It's in all nodes (except attackers) interests to produce fraud proofs to help make sure all the other nodes ends up on the same valid chain as them.





Signatories as published include the most prolific contributors to (at least) three separate implementations of the Bitcoin system.  In Bitcoin Core, signers there represent on the order of 90% of the commits in the last year or two.

It would be nice if we had a reasonable open system for getting sign-ons to statements and such.  From a communications perspective it was useful to have a focus on developers here but there is an extended community, including just the extended technical community who will probably want to show their support here.  (okay, well one conference organizer did sneak in. :) )
> , I gave up and was using blockchain for months despite a strong desire to stay on Bitcoin Core, 

What?

Thanks for the report about AT&T but this makes _no_ sense to me.  Are you also saying you can't connect _out_ to 8333?  I didn't extract that from the email.

It's certainly not good if AT&T by default is blocking 8333 inbound, but it is by no means an impediment to running the software.  Blocking _outbound_ would be an unprecedented move--- as far as I know no major ISP has ever done that for any widely used application.
This was [previously posted](https://www.reddit.com/r/Bitcoin/comments/3aqax5/blockstream_wants_to_make_bitcoin_more_private/) but was totally buried and I think few people saw it, same for [related posts](https://www.reddit.com/r/Bitcoin/comments/39pfyp/confidential_transactions/).

I've also written a [technical overview that describes how the scheme works](https://people.xiph.org/~greg/confidential_values.txt). The response I've had on that is that it's pretty accessible and a number of people with no background in advanced crypto felt it was pretty eye opening.
Hmm. I don't agree. Actually I think Mike is outright misinforming the public here-- and I think it's a shame that most readers of his article will never even know it.

He's also *again* projecting his opinions onto the the creator of the system and using that to justify a weak argument. Ironically, the creator of the system made a half dozen forward compatible (soft-fork) after it's initial publication.  You can actually take a very old Bitcoin node and verify the current blockchain up to today. E.g. Take the very first release, bridge the p2p protocol, and patch over the BDB lock exhaustion bug (maybe not strictly necessary if you arrange to see no reorgs during the sync) and it will sync the whole chain (though it may take a few months)-- the current Bitcoin blockchain is compatible all the way back.

The Bitcoin system also has infrastructure in place, from day one specifically to enable this kind of forwards compatibility: Things like a large collection of completely unenforced OP_NOP opcodes in script, and unenforced version numbers on transactions and blocks.

>  The idea of a “soft fork” is relatively recent

The idea of making changes to the system in this manner has been with it since, apparently, the start. The specific term soft-fork is at least 4 years old now.   The practice was already widely used (by the system's creator and by the current development team; even before the BIP process) and was 'just' the way you made changes. See also BIP 12, BIP 16, BIP 17, BIP 30, BIP 34, BIP 42, BIP 62, BIP 65, BIP 66, etc. 

I believe Amir introduced the term 'hardfork' to distinguish an incompatible protocol change from ordinary chain forking (where you'll reorg onto a longer chain when it overtakes, where in a hardfork you won't accept the invalid-to-you chain no matter what) near the end of 2011. Soft-fork became the obvious-by-comparison term for the forwards compatible thing that was already the only mechanism used (so far) to extend the bitcoin system. 

> It doesn’t help that even developers routinely define the term wrongly.

I've referred to it as forwards compatible many times, but I've found that doing so just confuses people because the term is not in that common use (e.g. 800k google results vs 25k).  Forwards or backwards depends on if you're privileging the application or the data-structure in your description. It's backwards from the perspective of the blockchain, forwards from the perspective of the application. Since the datastructure is normative in Bitcoin I think backwards is more correct (something I only realized while writing this-- I'll stop referring to it as forwards compatible).

> Forwards compatibility defeats the entire purpose of running a node.
[...]
> By the time the audit has finished, you can be sure that the ledger you’re using reflects application of the rules written into the software.

Which remains true, even in the case of a soft-fork.

There may be additional rules that others are imposing, perhaps most others.  Miners, for example, can impose rules completely in secret which no one at all knows about (though the secretly enforcing miners don't have a majority hash-power they might have a bad day.).  You can never know if there aren't additional rules being imposed, it's undecidable.  But you do know, with certainly if the rules that are checked by your software are followed.

> The auditors are people and services that are running Bitcoin full nodes. The traders are people who want to change the rules. Whether their rule change is a good idea or not isn’t relevant here: what matters is how they’re doing it. The auditors are now cross checking every transaction, but their calculations can arrive at the wrong answer, because they don’t understand the true nature of the transactions they’re verifying.

This seems like obfuscation to me.  Verification of the transactions and the blockchain involve checking it against a long list of rules.  A soft fork adds additional rules (which is compatible), a hard fork removes rules (which is not-- or otherwise the rules would have no effect).

A clear and simple example of this is BIP34:

Originally there was no restriction on the version number of blocks at all or the data in the coinbase field. BIP 34 proposed a new rule: The coinbase field had to begin with a push of the current block height, and the version had to be greater than 0.  Old versions imposed no rules at all, so this was still obviously valid according to the old rules.   That it, there is no "Wrong answer" from the perspective of the goal you specifically called out for running a full node:  "you can be sure that the ledger you’re using reflects application of the rules written into the software".

Yes, your software may not be enforcing all the rules other people might want to enforce. Modern soft-forks (BIP34 and post) use the version field in a way that makes it possible for users to detect that a soft-fork is going on; the new soft-fork version  bits BIP draft firms up the process so that systems can even tell when one might have activated, even when they're too old to know the details.  Then they can decide what to do about it.

> Let’s imagine P2SH was introduced in a different way: via a hard fork. The script would instead look something like this:
> OP_HASH160 6af7caf9b09224af8a171318f69d2... OP_EQUAL OP_RUN

Amusingly, assuming that OP_RUN were one of the placeholder opcodes; this would actually be a soft-fork change! This specific structure was one of the ones advocated by Luke-jr but vigorously opposed by another developer. The approach used in BIP16 does have the advantage of saving a byte.

But lets assume that it was made purposefully a hard fork (incompatible with existing nodes), e.g. by using an invalid opcode instead of a placeholder.   So instead this procedure would require all full nodes to upgradable, creating direct cost and risk for every user of the system, breaking older software, and all and all being a huge flag day-- for a feature that only a few users want.  It took years to get adoption here, interest built slowly. Would the feature exist at all if on day one everyone had to upgrade/replace/modify their software?

Demanding frequent upgrades "or else" is at odds with decentralization as it forces people to constantly swallow whatever some developers give them-- and to take blind automatic updates as you've advocated-- or they'll fall behind.   Alternatively, they're forced to switch to lite clients that trust whatever they're given and verifying nothing, eroding even the most basic incentives and protections exist in the system.

> My point? Knowledge is power. When you know the rules have changed, you can use that information to take better decisions.

Soft-forks, as they've existed since BIP34 signal _in advance_ that they're going on. The software notifies the user in advance that something is up. Bitcoin Core _already_ does this, today and will only improve in the future.  Rather than have your service go down by surprise when its rejected the chain you have some ability to act (and even potentially even prevent the soft-fork if you're aware of a reason that it's harmful).

Instead you suggest users should have a switch to deactivate validation, meaning that even the most basic controls would get bypassed; unless they go through with an emergency software update which they found out when their systems went down and started rejecting the chain; forcing them to either trust some developers with a hastily reviewed update, or trust miners by abandoning verification... all due to some changes which may have no effect on them at all.  Talk about flying blind....

> Any change to Bitcoin that is accepted by the majority inherently “disenfranchises” the people who didn’t want that change.

I think you're needlessly blurring things here and creating a false equivalence as a result.

Some changes only have a direct impact between consenting adults-- a soft fork can, for example, introduce a new signature type... I can decide if I want to have my coins sent to that signature type, it's my decision; the impact on third parties is (under reasonable assumptions) negligible.   Other changes are greatly disruptive and _will_ have a material impact on almost every user of the system. That there is always some theoretical fringe disenfranchisement does nothing to diminish the huge spectrum we find in reality, and does not justify making highly disenfranchising changes carelessly or treating all changes equally.

> They had no choice in the matter, or rather, they chose to accept the change and continue using Bitcoin

That is not true either... they're full free to not use P2SH and remain generally free to not use it today for their own coins.

> Can hard forks destroy Bitcoin?
> No. If it were the case then literally anyone could kill Bitcoin by just mining a block or two with different rules. The entire purpose of the block chain algorithm is to ensure that this cannot do any damage!

A hard fork removes rules of the system (perhaps also replacing them with new rules)-- these rules are responsible for upholding almost every invariant we talk about in the system. The exclusive ownership of a coin by a user without third party oversight. The consensual movement of coins under a user's own selected parameters, the limited supply of the Bitcoin currency, access to the system without prior approval and identification. Could a change in the rules of Bitcoin destroy it?

The only way to say answer that question in the negative is if the users of Bitcoin are practically free to reject removal of the rules that create and enforce the properties they hold dear.  By avoiding creating an environment where users have to constantly swallow new software, like it or not, or be kicked out of the system we preserve their ability to just say no.

The mempool entry is protected both by minimum priority and minimum fee thresholds, as well as a rate limiter-- they're probably too low right now (as they've been aggressively adjusted downwards over time; somewhat predicting future price increases that didn't materialize); but things are more protected than you might suspect.

There are [long standing patches](https://github.com/bitcoin/bitcoin/issues/3722) that exist as pull requests to Bitcoin Core which make the space finite, e.g. a couple days of blocks worth.  I'd personally rather on-disk mempool go in first so there isn't an incentive to set the threshold very low (there are negative effects if the limits are very different across the network); but hasn't been a high priority relative to other changes, especially with a typical "very large" mempool weighing in at 3MB.  And, regard

Certainly any improvements to mempool maintenance can be deployed much faster than anything related to blocksize. And attacks related to relay and mempool remain an attack vector regardless of what happens with the blocksize-- in fact they're worse with bigger parameters simply because the base level of capacity that you have to handle a multiple of is larger.
Don't mistake foolishness for talent.  There are a great many people who could code up a weekend hack implementation of a Bitcoin Bucket shop that systemically lost money through bugs and security vulnerabilities-- go look at the code that was posted, it was basically worthless crap.

That other people don't isn't due to to a lack of skill, it's because they know better.

But maybe they're wrong-- Zhou Tong seems to have done nothing but benefit from his escapades.
Citation?
Back before the waves of altcoins some of the tech folks had talked about putting out some speculative features in a testing altcoin and calling it "scamcoin" to prevent people from treating it as money.

... and then "Shitcoin" (and a multitude of other really transparent rubbish) got non-zero market prices.  Scratch that idea.

Now adays I want to see someone make a modern remake of The Producers where a trader and developer duo make an altcoin and due to a futures contract need to make their altcoin go to zero value, but every bug and scammy thing they add only increases the market price.
Uncharitable translation:

"Hi. I'm willing to compromise the privacy of our users when its a trendy press event."

(They already have, AFAICT by disclosing that the apparently non-public email address agreed with the white-paper)

:-/   ... this noise is pretty tiring generally, and I think it's bringing out the worst in a lot of people.
Not to that time, but towards the end of that month I complained again, and had an email conversation with him about it.

(Basically, the development IRC channel had been getting complaints of MTGox transactions not relaying for several months, prompting me to look into it whenever a couple more people complained— I also reported their non-canonical signatures).

We specifically wrote Opus to not be encumbered with any royalty barring or open source/Free Software incompatible licensing.  If it wasn't free there wouldn't have been a point in my eyes, and I don't just give away years of my life for nothing.

Our team included several engineers with long time experience with royalty free codecs (The developers of Speex, Vorbis, and Theora, which all have litigation free track-records), as well as a patent attorney and we had the assistance of council provided by some of the Big Corporations that intend on using Opus.

We worked hard to make sure Opus does not infringe and we have every reason to believe it wasn't. Like in the past, we use techniques and core architectures which are inventions too old to be patented— and what wasn't and their combinations were reviewed.  

Because of the shortcomings of the patent system it's _impossible_ to say something does not infringe without the aid of a court: Patents are presumed valid, so even something from decades ago can be technically infringing until a court rules the patent invalid.   But this is not a risk that is unique or special to codecs, it's a risk the exists with all inventions and all software,  ... you hear about it with codecs because extracting rent on popular file/transmission formats is so insanely profitable that there is much more enforcement— an economic and social inefficiency we hope to change by removing the profitability of non-free codecs by making competing free ones.

W3C proposed making flac a baseline required codec... presto ALAC free.

Alas, ALAC is slower than flac and achieves worse compression.  As far as I can tell, the only motivation for apple to not use flac is that flac was "not invented here".
Intolerance much?

As far as I can tell— he's suggesting that you let people believe whatever the @#$ they want about things which are intrinsically unprovable and scientifically worthless, because we need the help of compatibility religious people in order to fight off the people who are trying to undermine science in the name of fundamentalist religion.

It's completely true and fair to say that science probably can't currently or ever prove anything about an all powerful god(s), because any such god could create a universe indistinguishable from a universe without such gods. As such, it's a matter for religion and not science.



This is not a proposal: it's a patch on a private repository.

Until this showed up in the media in the last day I hadn't even heard of it at all...  Unless I'm missing something I don't see this going anywhere.

My understanding is that Luke's discussion is motivated by concerning long IBD time and because the most optimistic projections of future bandwidth and computer speed haven't materialized (at least for end users). It's a legitimate concern, but it's one that can be addressed in better ways.  (Better, in particular, because IBD time still would be bad even if all further blocks were required to be empty so at best a reduction would reduce the bleeding but not solve the issue)

The other question is ... why now?  The block size was doubled not that long ago... The only reason I see for now is because there isn't enough demand to saturate the current supply but (1) that could change any time, and (2) even a fairly small reduction would be enough to saturate things even with the current demand.
He manages Bitcoin investments for other people.
A complete description of simplicity itself fits on a tshirt. 

This isn't to say that working with such low level primitives directly is easy-- it isn't.  Ultimately people would use these tools through higher level languages.  But the fact that there exists a profoundly simple kernel at the heart of the system facilitates automated formal reasoning about the behaviour of programs written in it.
> are less shitty than others

Yes, SHA2 is relatively simple to construct an ASIC for; others are quite tricky and the increased upfront costs will result in even greater asic-centeralization. 
Don't confuse "BCH support" for "help me get my BCH free so I can sell it".

In Bitcoin Core, I've seen no calls for BCH support. :)
To be clear, none of the Bitcoin Core developers are assisting btc1.  The credits in BTC1 are for improvements that btc1 copied out of Bitcoin Core.  (Nothing wrong with that, but it seems some people think these people are helping out btc1, and that isn't the case).

>  and you have hard coded seed servers that can easily be updated with healthy nodes.

That isn't how DNSseeds work _at all_. They aren't even connected by nodes that have connections (e.g. potentially to S2X peers that are concealing the best valid blockchain from them), and when they are connected they hardly influence peer selection they just give you more things to try if nothing is working.  And even then, knowing about a 'good' peer doesn't help you when its connections are full from DOS attacks and other peers who suddenly needed to move to them.

All of this is explained in the PR discussion (except the DNSseed counter, since no one there made that particular incorrect argument)...
From what I can tell few to none are running much less mining on BTC1.  But it seems you are also confused on the effects:  at least given what I know and what was discussed on the PR it isn't expected to create the disadvantages you claim either. Network topology changes from upgrading are really slow, and crossing an additional node to reach something is now only about 1-2ms latency, plus network transit times.

A failure to do this, however, would invite significant amounts of disruption, especially because S2X refuses to implement even the simplest protection mechanisms (like replay protection).

This PR was motivated by the problems experienced with Bcash which took a more prudent path than S2X is insisting on. I expect if S2X continues on its current path it will have even more technical issues with its activation than Bcash did.
No it didn't.  No fork formed-- someone started up outdated software and it wasn't compatible with the current segnet at the time because the protocol had changed a month before but mined no blocks.  Rbtc and Ver dishonestly claimed that there was a testnet split, but there wasn't one.

If you disagree; Please feel free to back up your complaint with a link to one of these forked blocks.
Capacity here just refers to the system's ability to take more transactions per unit time.

Some people have been incorrectly referring to the above ability as "Scalability" -- but scalability generally refers to the a system's ability to preserve tradeoffs as you increase or decrease its size.

E.g. You could say a web farm that needs one server per 100 users no matter how many users is "scalable" (even though its slow)... Another server farm that needs to double the number of servers every 100,000 users is "not very scalable" (even though its much faster for the first 100,000).   A system which needs one servers for the first user, two for four users, three for 8 users, four of 16 users, and so on... would be said to be "very scalable".  But any of these options might be best for some particular set of workloads and tradeoffs. E.g. the last option would be the worst for just a few users, but the best for a billion users (30 servers compared to 11,000 servers in the middle option). Of course, sometimes there are options that are just superior at all interesting scales.

Segwit increases Bitcoin's capacity, something that the incompatible blocksize hardforks would also do... but segwit also includes some mild scalablity improvements, e.g. validating signatures no longer takes N^2 hashing but only N, so it's much faster which enables better tradeoffs (e.g. more independently operating nodes for a given level of load).


Roger Ver confirmed directly to me that he believes Wright is Satoshi.
> 300,000 transactions per day. Let that sink in

Is a large amount of transactions, that exceed many other wide scale system.

> Most people in the world are making less than $6000 a YEAR. Do you expect these people to be spending $10-20 a transaction? No way.

They're not going to be helped by 2 or 4 or 8 or whatever even remotely plausible blocksizes.  To reach the kind of usage you're thinking about [we _must_ have second layer systems](https://www.reddit.com/r/Bitcoin/comments/438hx0/a_trip_to_the_moon_requires_a_rocket_with/), and yet screwing up Bitcoins decenteralization with excess scale and/or reckless changes degrades the viability of those systems.

It's the dundee distortion field.
The covert form of the attack requires that the attacker fine two (or, ideally, four or more)  block headers that differ only in the first 64 bytes but are the same in the last 16 bytes.  But only a winning solution shows up in the chain, the 'sibling' block headers are not published.

Without knowing _exactly_ the space they searched, you can't go and look to see if there were any siblings that existed.
what a weird and screwed up comment, --- y'all are trying to forcefully change Bitcoin to be different from the system Satoshi described and released, and yet accuse me-- who has done no such thing-- with thinking he was wrong?

(And certainly, he was wrong about some things-- e.g. longest-valid instead of most-work-valid was wrong and trivially exploitable.)
Their last change to their codebase was _6 days ago_ https://github.com/BitcoinUnlimited/BitcoinUnlimited/commits/release

Danger will Robinson, Danger!!!!

People were reporting their website was hacked on Reddit a few days ago... (Edit: reports might have been non-sense, but that was the first concern I had on seeing these binaries.)

Either Bitcoin Unlimited is now closed source without even disclosing it or these binaries are malicious (or both...).


Edit: MagmaHindenburg  confirmed to me that BU is indeed taking the closed source route right now. Astonishing.  ... uh, good luck with that. 

     18:15 < gmaxwell> they just released binaries to fix the latest crash, but no changes to their codebase for 6 days.
     18:15 < gmaxwell> fix is binary only.
     18:15 < grubles> yikes
     18:15 < Magma> It worked so well last time when asshats announced attack code on Twitter as soon as it was commited to Github
     18:17 < Magma> All small blockers complained that it was stupid to just commit it to Github without releasing binaries first, now they are 
                    doing that
     18:17 < gmaxwell> Magma: lol no they didn't.
     18:17 < gmaxwell> Magma: I'm gonna laugh my ass off when those binaries steal all your coins.
     18:18 < grubles> oh hey it's Magma
     18:18 < gmaxwell> Magma: and as far as tweeting about it: (1) they were being attaced a half hour before peter todd tweeted about it, and 
                       (2) it was BU's own stupidity to specifically call out the fix as a remote crasher. 
     18:22 < Magma> The source is available for all the mining pools that wants them.
     18:23 < grubles> Magma: because users don't matter, right?
     18:23 < Magma> Most bitcoin users use SPV wallets or Blockchain.info wallets

> it's the same old consensus system, 

It is not. The center of BU's proposal is "emergent consensus"-- a radically different take on the operation of cryptocurrency consensus where miners set the rules by majority hashpower and all the other participants are along for the ride.

>  it just allows the 1MB soft fork limit to be changed if there is consensus

You could more or less have said that about Bitcoin Classic a few months ago with BIP109.  You cannot say that about BU.

Classic proposed a simple change to a 2MB block, and when the community found a compromise to achieve that the goalpost was moved to a radical miner takeover that the community was unlikely to find a way to compromise around.

Segwit isn't forced onto anyone without their consent.  Take it or don't take it.

I'm comfortable if Bitcoin doesn't change any further. I think it would be better if it did, but that isn't up to any person, much less me.
I come here because communicating is essential and if I don't few others will. No such gain happens with working on the list-- it contains a lot of great people I can work with elsewhere and a few reliably abusive people.

They didn't validate it, they rejected it when they realized it was too big, and banned the nodes that gave it to them-- to avoid wasting any more resources talking to a broken peer.
> pretty sure he said something over 90% of his worth is in BTC

I'm pretty sure he didn't, and I'd love a link that shows otherwise. But even if he did:  He also said that MTgox was solvent and that it's problems were due exclusively to the traditional banking system; he said this as an _investor_ in the company while physically present in their office in a video appeal to encourage people to stop pulling their funds out.  He doesn't exactly have a good track record for honest behavior.

Even if it were somehow true, percent of total matters a lot more than absolute amounts. Lets imagine that I own 20% of FooCoin (e.g. like the zerocash not-quote-premine)  which currently has a market cap of 1 million, and lets say I own 1% of Bitcoin, with a market cap of 16 billion.   If, through my actions I was able to cause FooCoin to have a market cap of 1 billion (1/16th bitcoin but as a side effect pushed Bitcoin to _nothing_  I would make a 40 million dollar profit from the move even though at the start 99% the value was Bitcoin.

I didn't make my claim that he was trying to split the network based on speculation, FWIW. I know it to be true.

> You on the other hand don't seem to care about the price of bitcoin

I suppose you haven't looked at the price lately then.

No way. Using "I" in any paper would be somewhat unusual. Maybe in a thesis...

Clicking back through my browser download history until I hit the first single author paper I get "[we explore](http://vigna.di.unimi.it/ftp/papers/xorshiftplus.pdf)", and the one-author paper before it "[We briefly survey](https://www.cs.cmu.edu/~venkatg/pubs/papers/ld-binary-ms.pdf)", and before that "[We propose](http://dcn.ftk.spbstu.ru/~petert/papers/wuBin.pdf)".  Counter examples exists, but they aren't that common.
Well, they managed to get themself a sub internally free from your criticism-- probably a big win in their book.

Also who is Tom Zander and why should we care what he thinks?  I don't mean that just in a flip manner --  I would have said that he was just some guy that posts every once on the mailing list misunderstanding things and always being really abrasive and accusatory about his misunderstandings.

Sure he's now the only active developer of some Bitcoin Core fork which no one uses.. but anyone can be that.. push the fork button on github and send some abusive messages to a mailing list and you can be Tom Zander too.  at the end of the day it doesn't matter.  Enough of the personality politics.

Rbtc's lack of usefulness and constant FUD against Bitcoin is clear to everyone who cares to look, and no one else cares at all. :)

> less then 40pct of full nodes even run segwit right now. https://bitnodes.21.co/nodes/ That needs to improve as well. 

By those figures it's 42%, not under 40... and those counts are only listening nodes which are far more frequently abandoned VPSs that don't matter much.

But it's unclear to me why you're saying it needs to improve-- the whole point of a softfork is that not everyone needs to update at once. I believe CSV activated with about 25% of listening nodes upgraded.
Looking at his post history, it seems that zcash is his preferred pump.
> A lot of people keep saying they want on-chain scaling and that's why they oppose segregated witness--but they don't realize that seg-witness is ON-CHAIN scaling. 

Yea, that one perplexes me.  It's really demoralizing. It's one thing to have an argument with people that disagree with you on principles. It's a good fight.

It's a different situation entirely when the argument is just because someone has the density of neutronium. :( 

A less depressing analog is that "off chain" is confused with centralized... And centralized is confused with unconditionally bad.
> A large part of the problem is almost all the early adopters were sold

As someone who was using Bitcoin before virtually everyone here, I can say with confidence that I was 'sold' a Bitcoin that would be decenteralized and autonomous and censorship resistant with a sound programmatic monetary policy the was resistant to political rewrite.  Had I wanted a system that was highly scalable in it's most basic layers, systems like Paypal and Visa already widely available and easily compatible with the money I already had.

Back in 2010 Bitcoin's creator wrote, "Bitcoin users might get increasingly tyrannical about limiting the size of the chain so it's easy for lots of users and small devices", Hal Finney, renouned cypherpunk, author of most of PGP, first advocate of Bitcoin and first user of Bitcoin wrote "Bitcoin itself cannot scale to have every single financial transaction in the world be broadcast to everyone and included in the block chain. There needs to be a secondary level of payment systems which is lighter weight and more efficient."

In 2011 Dan Kaminski, after reading Mike Hearn's "dump everything into the blockchain" scalability page on the Bitcoin wiki (which he protected with edit warring) he described Bitcoin's decenteralization as pretextual-- because growth would destroy it. I [countered](https://en.bitcoin.it/w/index.php?title=Scalability&action=historysubmit&diff=14273&oldid=14112) pointing out the block size limit and layers.

So really, no one can claim these things were a surprise.
> Will segwit relieve congestion pressure?

Hopefully not-- reduce? absolutely. But the system operating with back-pressure is important.

One reason that it is important is because without it there isn't a stable fee market. As you can see the transition between market behavior and 'whatever, everything paying much of anything fits' is a bit bumpy at times. Without the stable market they'll be nothing to pay for security in the long run, http://papers.ssrn.com/sol3/papers.cfm?abstract_id=2400519

The other reason is because as the subsidy goes down mining incentives need a fee backlog to keep miners incentivized to go forward rather than fight each other over the tip. https://medium.com/@bergealex4/bitcoin-is-unstable-without-the-block-size-size-limit-70db07070a54

It's especially important that the ecosystem doesn't establish a practice that as soon as congestion starts forming, there will be an intervention the takes it away. This results in a vicious cycle where rational participants will not invest in the development needed to handle it, which results in it being all the more urgent to prevent it.. and the conclusion is an impossibly loaded chain that drives centralization and an non-viable fee market with bad mining incentives-- rescuing such a system might require the addition of monetary inflation and or centralizing mining with signed blocks instead of POW.   We already went through this experience with the soft limits... in 2011 there was a 250k soft limit, ten increasing backpressure above that to 500k. over time the automatic backpressure was removed and the limits were bumped as soon as they were hit. As a result most wallets didn't have dynamic fees a year and a half ago. People even argued that the block size _had_ to be bumped because the soft limits had been bumped and the bump of the soft limits was an implicit promise to keep increasing.  One does not need a 170 IQ to see where that goes. :)


What I've found is basically anyone who shows up there agreeing with me ends up banned; and those that don't get banned are rate limited so they can only post a couple times an hour. It's pretty miserable.

This is a somewhat ignorant comparison.

P2SH introduced a new address type. This meant that use was heavily blocked by even a single popular wallet being unable to send to it-- who is going to use a wallet that many people can't sends funds to?   (Bc.i, for example, held out on being able to send to P2SH for many years.. as did BitcoinJ).

In the case of segwit, no new address type is required-- once it's active on the network someone can begin using it immediately regardless of any changes elsewhere.

It's also the case that there is no reason to use P2SH unless you're using multisig or other advanced scripting features.  Segwit will bring immediate benefits to everyone transacting, even if they're not using multisig.

As an aside, why is BC.i working on and promoting Thunder-- which production use of requires segwit-- if they think segwit won't be available?  (Also, if bc.i is concerned with segwit timing-- why aren't they contributing to testing and review? -- I'd assumed before that they were happy with how things were going)
So you're saying that you did? Oh. That would be an improvement. Who were you working with?
Many people already know this; they're just not the ones with the army of accounts on Reddit.

Critically, Bitcoin's security and monetary properties are its _distinguishing application_. Bitcoin can do many things, but store-of-value is an important something that Bitcoin can do in a fundamentally better way than legacy competition. Because that advantage is fundamental it can't just be wiped away with some improved processor pricing or a better credit card.

To be a money an asset must be broadly usable; and so other uses are important too; but one of the ways that Bitcoin is a better store of value than the things that came before it is that it is cryptographically provable and cryptographically contactable-- and these properties let it reach into other domains in better ways than traditional tools.  E.g. How do you know a vault contains all the gold it should? (... you don't and it probably doesn't. :( ).

Without the store-of-value advantages; Bitcoin will lose out to competing systems. Even if, caught in the blockchains are awesome hype, you don't believe the explanations as to why Bitcoin like blockchains are fundamentally less efficient than centralized systems, thus better for payments if you don't care about the special store-of-value properties of Bitcoin-- someone can still shove a fiat-on-a-blockchain and immediately eliminate volatility and access concerns.

Keep in mind that in the consumer credit driven culture in the west many people don't have much interaction with money other than a small payments tool: when your net worth is negative you don't store value. So to some understanding Bitcoin must first begin with a taking on a broader understanding of money, wealth, personal finance, etc. and to those who haven't embarked on that journey yet, payments is all that can be seen. And payments are fine and important, but only as part of a bigger picture.

That text comes in via a template, it's not part of the edit history of the page, but the [edit history of the template](https://wikimediafoundation.org/w/index.php?title=Template%3AWays_to_Give&type=revision&diff=103183&oldid=103119). This is just how mediawiki works (and avoids having to do _huge_ amounts of work every time a widely used template is modified... instead of simply signaling invalidation of pages that include it and letting them pick up the new version next time they're rendered).

Fortunately, Reddit never forms an angry mob around a misunderstanding of technical subtleties.

'''Edit''': Added a link to the template change.
This is the natural and expected result of people creating novel cryptographic systems with nearly no clue about what they're doing or even how difficult getting the details right is.

It should be noted that the Bitcoin-QT/d team has made several such changes that would have been unsafe if changed and designed a specific protocol to activate new changes safely.  So its not like the required procedure was even something novel. E.g. https://github.com/bitcoin/bips/blob/master/bip-0034.mediawiki
Many people aren't aware of it but the backend Bitcoin system has no concept of "addresses". Instead when you make a transaction it specifies small bits of [forth-like program code](https://en.bitcoin.it/wiki/Script) that provides rules that redeemer of the coins must satisfy to spend the coins.

The normal 1xxx addresses are just template for a ECDSA checking script. There are also 3xxx addresses where the address is a hash of an arbitrary script and the redeemer provides the script matching the hash and satisfies it.

This can enable powerful applications like "trustless escrows" with no mediator trusted to hold the coins or [bindings to external zero knowledge proof systems](https://en.bitcoin.it/wiki/User:Gmaxwell/why_hash_locked). [Edit, a couple years later: We've demonstrated this kind of [zero knowledge proof transaction](https://bitcoincore.org/en/2016/02/26/zero-knowledge-contingent-payments-announcement/) now.] 

In the posting linked here the author has paid funds to be redeemed by someone who can provide collisions for a non-linear (SHA1, RIPEMD160, SHA256, RIPEMD160(SHA256()), SHA256(SHA256()), and ABS() are the respective bounties)

Anyone who has Bitcoin can add to these bounties by simply sending funds to the respective 3xxx address which is a hash of the rules that check for a collision for the given function. The Bitcoin system itself arbitrates the rules, so there is no other party that must be trusted to pay or whom could withdraw the bounty after you begin your effort.

The SHA1 bounty has address [37k7toV1Nv4DfmQbmZ8KuZDQCYK9x5KpzP](https://blockchain.info/address/37k7toV1Nv4DfmQbmZ8KuZDQCYK9x5KpzP)

A disassembly of its script is:

    OP_2DUP OP_EQUAL OP_NOT OP_VERIFY OP_SHA1 OP_SWAP OP_SHA1 OP_EQUAL

Or, in English: Duplicate the last two elements on the stack ([A] [B] -> [A] [B] [A] [B]), then compare if the top two elements are equal and push the result, if they're not equal remove the result and continue processing, then compute the SHA1 of the top element on the stack ([A] [B] -> [A] [sha1(B)]), then swap the top two elements ([sha1(B)] [A]), then compute the SHA1 of the top element ([sha1(B)] [sha1(A)]), and compare the result. If the final state is true the script passes and the spend is permitted.

([A parallel thread on Bitcointalk](https://bitcointalk.org/index.php?topic=293382.0))
Thats a lot of BS. It physically hurts when anyone actually hits you even if they're weaker than you, and it certainly emotionally hurts when it comes from someone you trust.

Abuse isn't about gender.  Okay, sure in some extreme and uncommon cases abuse from men is more likely to be life threatening, but thats not the situation in most abuse.

"Yo Dawg. We heard you like leaks so we leaked your sealed order."

... Makes you wonder what other sites they're hitting with sealed orders for connection data on accounts that have been speaking in support of Wikileaks, enh? 
Man.  This brings back irritating memories… A few years ago I lived in an apartment complex with several communal compactors. The place wasn't really cheap— I don't think they had any units under $1500/mo, so you might think the residents would probably have the life skills required to operate a fancy trash can, but you'd be wrong.

For perfectly reasonable energy saving purposes the compactors only operated every 3rd time you closed the door. This fact was advertised in loud text on the compactor.  Frequently a string of idiots would fail to close the door after depositing their trash (ignoring some of the other large text) and eventually the chute into the compactor proper would fill.

Simply closing the door (potentially one to three times) would cause the compactor to run and clear the obstruction. But this isn't what people would do. Instead they'd simply set their trash down on the ground, ignoring the warnings on every visible surface not to do exactly that.  

Frequently the compactor will run after immediately after the first trash-dropper— clearing the obstruction... but it's game over once the first trash bag is on the ground. It seems that the probability of _actually checking_ to see if there is room is inversely proportional to the number of bags already on the ground. In no time at all you can't even reach the entrance to the mostly empty compactor.

I don't believe that at any point during the twoish years that I lived there did I actually see any of the compactors actually fill but trash piles were a weekly occurrence and an enormous trash pile like that happened every couple of months, usually around holidays.

Frequently when I couldn't get to the compactor I would just take my trash back with me and try again later but when I had some free time and especially when there were only a few bags sitting out I'd clean it up myself.  I found it incredibly outrageous that so long as there were a couple bags on the ground people would just waltz up and drop their bags onto the pile while I was actively loading them into the trash. And they were often quite aggressive with me when I'd say "Hey— don't leave that that!". People suck.

I am sick and freeking tired of the constant harassment and stalking by you, Rizun, and your buds. It is utterly absurd. :(
Congrats!
> A modern Intel or equivalent x86 processor is needed.

Saying Intel isn't enough: Atom is too slow.  It needs to be a moderately fast modern atom core.  My receivers run on a quad core 3.2GHz i7 from 2011.

Using too slow a computer is a common pitfall while doing the initial pointing. Right now the modem software doesn't really tell you if your computer is too slow and instead just displays a junk signal that looks exactly like your dish isn't pointed right. [I lost many hours in my own setup due to this]
> most people don't understand that outputs drop just as much as transaction count when batching. 

This.

When you don't batch, almost every transaction creates two outputs, one for the payment one for change.  Space is wasted for the additional output and for the signature to spend it in a subsequent transaction.

When you batch, each transaction creates many outputs, at most one of which is change. So asymptotically batching _halves_ the number of outputs created for a given load.

Batching can also reduce outputs by combining multiple withdraws into one.  E.g. Alice withdraws 1 BTC to her wallet then 5 minutes later withdraws 2 BTC more.  If the batch hadn't issued yet this just gets converted into a single 3 BTC output.

You probably want to compute max(1, tx_ouputs - 1) for each transaction and look at that over time; you'll also want to ignore 0 fee transactions (pool payments) and coinbase transactions. That won't respect the second batching reduction but at least it will capture the first.
--.- ... .-.. .-.-.- / --.- - .... / ...- --- .-.. -.-. .- -. --- / .-.. .- .. .-. / ..- .--. .--. . .-. / -.-. .... .- -- -... . .-. .-.-.- / .--. .-.. . .- ... . / ... . -. -.. / ... - .- .-. / .-- .- .-. ... / .- -.-. - .. --- -. / ..-. .. --. ..- .-. . ... / .- -. -.. / -.-. .... . . --.. .. - ... / ..- .-. --. . -. - .-.. -.-- .-.-.- / --.- - -..- .-.-.-
MMM is a personal finance blogger who mostly focuses on early retirement. ER is the idea that you can minimize your spending and invest the rest and by doing so become financially independent decades before ordinary retirement age ... and that this is actually possible for far more people than realize it.   Prior to MMM most ER blog content was more along the lines of "7 ways you can filter and recycle your urine to save on beverage costs!"-- complete with detailed analysis about how doing so would allow you to work for 27.32 fewer days. Some people are naturally into that sort of thing, but not most.   MMM takes a different approach: he observes that most wasteful consumer behaviour isn't just harmful to your wealth, but it's often harmful to your health and happiness... and, well, kinda wimpy.  His posts describe self-reliant activities in a way that gives them the respect they deserve and his readers are left having to admit that things like choosing biking over driving, doing your own construction, or just optimizing your life to eliminate waste are pretty badass, and not just good for your wallet and the environment.

I've read MMM for years and it's normal for his posts to be especially "colorful"-- but this one just strikes me as outright emotional, far more so than most.

I think he's outraged about some of the more over the top pumping we've seen of cryptocurrencies (and especially ICOs, which probably all look like the same stuff to him) and I think that sort of outrage is justified: there is seldom a day that goes where I'm paying attention to this industry and am not embarrassed by someone's transparently unethical pumping.

But I think he lets that justified outrage-- and maybe a little nocoiner regret (after all, users on his forum were talking about Bitcoin there years ago)-- color his thinking.

It's very good advice that you shouldn't consider highly risky/speculative investments a major part of your portfolio.  But MMM has many times endorsed people having small "for fun" investments.-- he himself has promoted really risky and speculative things like Lending Club notes in the past. He doesn't face-punch his readers for tinkering with things like options and forex... so long as their activities are prudent in context and don't get in the way of sensible (e.g. boglehead style) investments.

He also seems to be bamboozled by the "blockchain without Bitcoin" nonsense; but I think that doesn't matter except in so far as it makes it easier for him to vent his outrage and ignore the relevant facts that make Bitcoin interesting. (I say bamboozled because without decentralization that requires a complex set of incentives that depend on Bitcoin being valuable as an asset the whole blockchain part of it is just a really inefficient database).

I think it's a shame: His readers would benefit from a balanced presentation, -- one that encourages a prudent perspective on Bitcoin investing and helps guide people towards responsible behaviour and away from traps like developing gambling addition to altcoin trades.  Instead we just get a fire and brimstone "cryptocurrency abstinence" rant, -- which I expect will be about as effective as encouraging responsible behaviour as any other rant of that kind: not very.

I posted a comment on the blog too-- but it hasn't made it through moderation. ::shrugs::

Mindful of the [Gell-Mann amnesia effect](https://www.goodreads.com/quotes/65213-briefly-stated-the-gell-mann-amnesia-effect-is-as-follows-you), I still think that MMM provides an interesting and insightful perspective on many matters, but I think he missed the mark on this one. He's only human.

MMM's philosophy values self reliance and personal responsibility and I think these are values that fit well with user-made decenteralized currency.  Also, he really dislikes rampant consumerism, and it isn't difficult to argue that the USD's monetary policy (and that of basically every other major currency) is a major contributor to excessive consumerism. -- I can't help but think that if the ideas behind Bitcoin were better presented to him he'd at least see it as a respect-worthy effort.

There is another reason I found it a bit sad:  I've been nagging Theymos for a while about the need to reach out to some personal finance bloggers to come and give useful advice to people sitting on Bitcoin gains.  There is a lot of ignorance about taxes and personal finance in general that goes around, and I think a lot of people from this subreddit could benefit from some different advice and perspectives.  Unfortunately most of the personal finance folks hate cryptocurrency people now due to the skevy and spammy pumping behaviour of a few. (E.g. flooding personal finance threads where someone asks "I just inherited 100k what should I do with it?" with "invest it all in dash and dogecoin!!!!! INFINITY PROFITS")...  MMM is someone I would have liked to try to convince to target this community some-- I think his fun and not-afraid-of-confrontation approach would be appreciated here.
To be fair-- even if we did nothing there is a very high chance that B2X will cause no disruption to the Bitcoin network itself,  we just don't consider 'very high' good enough:

Unfortunately, if we didn't address it the NYAers could maximize the odds of disruption by spinning up lots of non-signaling B2X sybils; they haven't deployed enough yet to cause problems, but so long as they could cause problems that way the incentive exists... and so we can't count on it being 'unlikely'.
> I would be surprised if it's not targeted as an Ethereum competitor?

That is like saying modern science is an alchemy competitor.
> core devs are working on doing just that (changing the POW)

No we are not.  Cheers.
> a fat little ugly pig who sleeps alone at night and likely wanks off to the most perverse nonsense on the net...

I would rather be all those things than be the kind of person who writes messages like yours to other human beings.
There is no permission involved. Chjj said he would keep it quiet until the fix was widely deployed and then broke his word.  This is outright dishonesty.

> not fixing issues in a timely manner 

They were fixed in the codebase _before_ chjj was made aware of them, and have been scheduled for release as fast as good engineering practice would allow.  Making some kind of emergency fix against an unknown issue would risk an uncontrolled public disclosure, and wouldn't be warranted because exploitation takes time.
> Does that mean the core wallet wont accept or see 2x coins?

Thats correct, though it's not caused by or news from this. https://en.bitcoin.it/wiki/Segwit_support
> The only time malleability is a problem

That is not quite the case; go look at the dev meeting minutes as we curse malleability making chaining unconfirmed spends phenomenally more complex to get right.  There are still some obscure corner cases we don't handle fully cleanly and correctly in Core in spite of probably man years being sunk in dealing with malleability so far.

Even getting to 99% right in the presence of malleability is a lot of work.

For simple exchange withdraws it shouldn't be so hard to handle things; I agree, I just don't agree with your broader claim that it's only a problem for fancy smart contracts. 
lol, none of that involves giving luke-jr their credit card numbers.  Give it a rest, by then you'll surely be happily using dundee-coin.
If they hardfork, the users of Bitcoin don't even need to change POW-- forkers will have already kicked themselves off, and will be harmlessly ignored by any real nodes.

(Lite clients will need a few lines of code added to handle it, since it seems the HFers refuse to use the HF bit support that would make it easier for liteclients to choose.-- but only a few.)

It's a weird allegation too, since it's the day he created his [bct account](https://bitcointalk.org/index.php?action=profile;u=101601) the Bitcoin price was ~$65 - $95, and had never been above $230 at any time prior.

This is standard Saintbits/Nchain/rbtc psyops.  Repeat some random untrue claim as "fact" enough and hope that it sticks.
Which it won't even produce, which is why they're stuck.  But other supposedly compatible software isn't compatible and the chain has forked.
So laden with false assumptions that you're unable to enumerate only a single one, and instead just attempt to change the subject to something which is entirely offtopic?

Anti-segwit fud has been debunked in many places by many people. There isn't any special potential for the loss of witness data: it's stored inside transactions/blocks just as signatures always have been stored.
> Your linked post actually concedes the point that such things are indeed possible with two stacks but would need to be built. 

No it does not.  It is not possible to write a function in Script that cannot be rewritten with no use of the altstack (ignoring the resource limits, since it may take a few more operations for stack manipulation).  The presence of the altstack does not make script "turing complete" or otherwise change its computational power.

You provided a forceful jargon response, then you argued from authority.  If you were right to disprove my claim you need only give a single example script-- we're waiting.
"Nothing wrong" in taking tens of millions in rebates for a "supercomputer" that it's maker says never existed?

Ohhhkay.  AFAIK he is still wanted in AU, which is why he's been mostly hiding out in Barbados.
> It's still producing more blocks than segwit however,

Funny how it does that with almost all of the BU nodes down.  Immaculate mining!
Multiple developers most of who hadn't contributed anything to code to core for months at the time, agreed that they personally would work on some proposals, which they did.  They did not and could not agree that anyone else would work on it, they did not and could not agree that anyone would adopt what they propose. But it is presented as something else entirely.

They shouldn't have gone because exactly of the outcome that was created: although their agreement is quite clear and specific-- that they, personally, would work for HF proposals after segwit if the participating miners didn't signal classic (they did), it is being presented as "core agreed"-- even though those people have no authority or right to obligate the project or its multitude of contributors to _ANYTHING_, that it was before or with segwit (rather than AFTER, as per their agreement), etc.  With people claiming that it was a promise of a hardfork (which _NO_ developer has any right or ability to promise because they do not control the network) rather than a HF proposal, which is what the agreement actually said.   Basically all of this stuff was predictable and predicted before they went and happened because they didn't set the right stage and rules of engagement.

And as a result the rest of us (e.g. the vast majority of the project contributors) are continually harassed over their agreement... They basically went over and above to do what they said, following through even though the miners breached (signaling classic and not activating segwit), and still they and everyone else not involved in the whole debacle is continually harassed over it.

Did that answer your questions?
I am not a fan of BIP148.

But duh, UASF in general sure. Ultimately all soft-forks are eventually secured by the users. A softfork that isn't is only protected by the whim of miners.
He did worse than fail to prove,  he made a lot of noise about "proof" which was PROVEN to be fraudulent.  I feel bad for whomever he is scamming now.

> Yeah, this small soft fork just forces everyone to put a SegWit commitment into the coinbase.

No it doesn't, please don't misrepresent it.  The proposal gives the option of EITHER a segwit commitment or a segwit incompatible commitment.

(If you have any proposals on better forms for the segwit incompatible version, please feel free to make them.  By changing the ID and using the same structure as segwit, I hoped to minimize the amount of new software that has to be written--  but I'd be perfectly happy with anything else that committed to all the transactions, so long as people were willing to implement it.) 
It could be argued to be-- though everyone knew from day one that GPU/FPGA/ASIC would be much more efficient. But to whatever extent it was an attack it was simply incorporated into the defacto definition of the work function.

This particular technique cannot simply be incorporated, even ignoring the patent concerns, because it prevents a half dozen or more proposed protocol improvements.

If it isn't clear to you: the proposed BIP does not block boosting, it only interferes with covert boosting and only to the extent that the boosting blocks transaction commitments.
> This is a public process

Then why did I hear about it via Forbes?

> In the past day we have been talking to journalists getting ready for the announcement,

Yea, because that is how responsible engineering works.

BU has no activation specifically so it can never fail, it can continue on, never forking and producing drama for as long as anyone pays attention to it.
     $ ./bitcoin-cli verifymessage 19Mz2o9RDABT74SA9njZqMtJXKEzj2qUoH H9ed6z5RgdThRxXXqePmtJbaK1pGvoy6e+aiwUPD6pkrJ6d6TBchOu5OQLEbgq/15YRjcOUC+kMrGVfszUXV5Wc= '@RogerVer lets make a deal, 1 for 1 trade. At least 60k, possibly up to 130k, my BTU for your BTC.'
     true

That address appears to currently control at least 40k BTC at the moment.

Man, I was going to try to goad him into a 4 (BTC) : 1 (BTU) trade, I've been undercut! :)  
No.

All versions ever created accept segwit-using blocks.

0.13.1+  will enforce segwit once activated.

With all current versions miners have to upgrade/modify/replace their mining software stack to signal segwit support for segwit enforcement/activation, though it's proposed that in 0.14.1 that  restriction will be removed.
Most block explorers do not validate completely. NOTHING should use a block explorer for anything except a diagnostic tool. :(
From Bitcoin Core's perspective: we were pushing hard on parties to activate BIP66, for segwit... meh. New features are up to the people who hope to use them to promote, and we've done our part of designing and implementing a rock sold, well tested, powerful improvement.

Every day SW doesn't activate is a vindication of the view that many of the people screaming more capacity was urgently needed were being dishonest and that what some of them really wanted to do was to disrupt Bitcoin and dilute its network effect.
You miss the point, Roger is a big time altcoin investor; he wants Bitcoin to be fragmented because he hopes he will make money from the appreciation of the fragments as well as the altcoins which he feels are held back by Bitcoin's network effect.

To him it doesn't matter what kind of personal freedom this technology brings the world in the long run: he's already wealthy enough that he can (and has) bought citizenship in other countries to escape paying US taxes. It doesn't matter if Bitcoin get turned into a worthless joke, because he'll just pump some more altcoins.



> Pretty fucking reasonable request. 

wtf.  It was a [@#$@ commit message](https://people.xiph.org/~greg/temp/rbtc_wtf_part_27.png) from the Bitcoin repository. If reddit prohibits linking to that, then all of /r/btc's links to Bitcoin Classic and Bitcoin's unlimited repos-- which show the same information, are not kosher eitherer.  The _same_ email address had already been posted in rbtc many times before (as had mine, for that matter).

Please, the post was transparently hidden to prevent conceal the correction of that absurd allegation. 

> and having detailed discussions with miners. [...] This represents a significant risk.

I don't agree that it represents a risk, or rather-- not one that I'm concerned about. If miners are even *able* to force incompatible rule changes on the system then the system has *failed*, especially at the current levels of mining centralization. I don't believe that is true, but if it were I have no interest in working to protect a failed system.

> Actually the moderation policy on this forum

Why tell me? I argued against it.  At the end of the day when the alternative is to let whomever pays for the most sock accounts completely control whatever is displayed, creating a echo chamber of fake news misinformation and hate-- it's not like I can actually advocate changing to that, as stupid as the policy is...

> criticizing some of the prominent hardfork proposals

The fundamental error is assuming that balance of responsibility. What you propose is something of a lost cause: it turns out that it's always possible to add complexity to a cryptosystem (which is what Bitcoin is) until it's infeasible to review.  This is precisely the path taken by some altcoins,  and even when the same design flaws which were previously criticized (like contract reentrancy) they simply double down on the obfuscation, and the rubes that think you can engineer by whirling jargon go wild for it.  It's 100 fold easier to spin up more bullshit handwaving than it is to dispel even one instance of it, especially when you can count on promoters who will outright lie.

At the end of the day Bitcoin must be robust against efforts to subvert it, against efforts to force changes on to users against their will. It can't get there by counting on me, personally, to stand against the ocean at all times, -- if that is what it comes to I have little doubt that one of these violent nutbags will simply kill me, they've certainly threaten it enough times. And if that is what it came to-- a few or one standing against a tidal surge of ignorance-- then mankind isn't ready for Bitcoin and doesn't deserve it.

I don't believe that this is the case. I believe that Bitcoin is robust. I don't think I have to tilt at windmills, arguing against an endless stream of borderline jibberish, and I don't intend to.

The only thing I can do to protect myself beyond leaving completely is to make sure that I have no remotely plausible way to enact the psychopathic demands they make, and on that front being the personal slayer of their latest crackpot crypto would be a step in the wrong direction.

But I wish you luck-- and your questions are good, though they only scratch the surface--, but I say again, you're making the error of implicitly approving a standard of responsibility that lets someone force you to interactively defeat their cheaply generated crap crypto. I fear that it is an approach that will only exhaust you.

Honey badger don't care.
> Bitcoin node operation is almost completely charitable 

It's a little more subtle than that: A node is has a significant payoff for your personal privacy, personal security (if you transact), and the monetary soundness of Bitcoin.  These benefits are all significant but they take the form of reduction in tail risks and other long term effects; while the costs of a node are very immediate.  People tend to overemphasize immediate costs and underemphasize long term considerations...
ETH's primary resource control is "gas limit", and the attacks are exploiting pathological usage patterns that crush nodes but fit within the gas limits. To fight them they have been cutting gas limits down to nothing. They just implemented a hasty hardfork to try to address it and with a day were back to blocks taking 25% of their interblock time to validate.
You should also ask for advice in some of the personal finance subreddits.

If you're not expecting to live to 65 you need to reconsider some of your other life choices. :)

Other people could give you better advice on the pension, since that is really independent of Bitcoin.

Putting _all_ of your retirement savings into a currency or speculative asset is probably bad financial planning: The goal of retirement money isn't to make a fortune, it's to be able to support yourself comfortably in all possible cases. To achieve that goal it's best to own a mixture of different things; and Bitcoin could be part of that mixture but it shouldn't be all of it.

You can reason about retirement savings with some fairly straightforward calculations, e.g. in python:

     #!/usr/bin/python
     balance = 17000
     age = 45
     retirementage = 65
     monthlypay = 380
     assumedinterest = 1.06

     while age < retirementage:
       balance *= assumedinterest
       age += 1
     paid = 0 
     years = 0 
     print('Account worth %f at retirement'%balance)
     while balance > 0:
       pay = min(monthlypay * 12, balance)
       balance -= pay
       balance *= assumedinterest
       paid += pay
       years += 1
  
     print('paid %f over %d years'%(paid,years))

I assumed a 6% return there, which is aggressive esp if the payments are assumed to be inflation corrected.  I think a lot of the personal finance people assume 4% in order to be secure against markets as bad as have ever been seen so far.

You may also have to pay taxes on the lump payment-- another useful question that people with experience with pension payouts would be more helpful in answering.
Pretty hilarious that this post was auto-tagged as "spam" due to abuse of the report feature.

Unfortunately Reddit is ultra-prone to manipulation.
I know it might be a controversial idea, but we might also want to consider that someone might also want to do something else with their internet connection than just run a Bitcoin node.
I seriously doubt this.  My experience is that Zooko has very high integrity and I've never seen behavior from him that would suggest something like that. 

Considering that Gavin is an advisor to his altcoin, I wouldn't have been surprised if Gavin suggested him as a technical expert... not that Zooko and team's long time reputation in cypherpunk circles wouldn't be enough on it own.

[Moderators, if you decide to remove the above post due to its racist/nationalist ranting, please feel free to delete my post too]
You might be overestimating the level of immaturity rust has, at least in particular problem domains.   E.g. simple systems programming where you likely wouldn't end up using almost any third party libraries.  Also keep in mind, rust can call C code with no particular overhead...

At my company we've tried rust for a small project and consider it a success. We encountered one issue related to language immaturity which was easily resolved. Debugging at least at that scale wasn't too unlike the situation with C++ but it appears we had a lot less to debug. YMMV.  I wouldn't go out and write some huge GUI end user application with rust yet.

historically most of us working on Bitcoin Core have intentionally tried to keep a very low profile as there are many costs and risks related to attention-- and from our very mission oriented perspective, very little benefit.

Unfortunately, various parties have exploited this to spin a number of false narratives and inaccurate histories, and so we've needed to take on some additional exposure.
It can be done as a soft-fork; there was a nice thread spelling that on on bitcoin-dev a month or two ago.

But-- AFAIK None of us are against all hardforks-- in fact, I think we've all proposed various ones; but they're not a first choice and ones that change the properties of the system out from under people who own coins are not something we consider very practical or advisable.

There is a tracking issue on Bitcoin Core I created for incorporating privacy improving technologies like CT.
Limiting input count doesn't address it, a single input can have many checksigs; and input count limits low enough to really help would also impact ordinary usage.

Just peppering the system with complex limits creates technical debt that hurts maintainability and security.

> Someone competent in computer protocol design

What model armchair is that? It must be awfully cozy.
Quadratic verification time for transactions, because checksig will rehash data over and over again... and bigger transactions with more checksigs hash more data..., also fixed by segwit.

In my past experience, tl121 doesn't propose any changes, only observes problems discovered and addressed by other people-- and calls the people actually supporting it incompetent; perhaps as a lame compensation for not actually contributing himself. 

Though I doubt that is actually the OP's issue.
"Sally is a _great_ typist" (she's also a licensed attorney who came up with legal strategy in her spare time after completing a case before the supreme court).

Trying to put people down with faint praise is an old trick. Why do you assume the team working on Bitcoin doesn't have significant economic expertise and aren't advised by further experts?

This argument sounds a lot like the blockchain without bitcoin argument;  "Those Bitcoin people figured out an interesting database technology, but we shouldn't defer to them on money by using their currency."

0_o.  No, as you likely suspect... that wouldn't work at all: That's just a direct path to worthlessness because you'd never be sure which coins were acceptable and which transactions were final, such a system would be not very useful as a money.

A couple years ago, Adam Back [described an idea](https://www.mail-archive.com/bitcoin-development@lists.sourceforge.net/msg02944.html) for a technically simple mechanism allow you to transfer your Bitcoin value into a new and upgraded blockchain.  Other people who didn't care about your new blockchain wouldn't be forced to use it, but you could move along to it... so it provided a hard-fork and flag free upgrade path. But the problem with it is that it was only one-way, so you couldn't go back and so if it wasn't obvious which way people should go, the system would fragment.

Subsequently, I proposed [a protocol idea](https://bitcointalk.org/index.php?topic=277389.0) for making those kinds of relationships bi-directional, at the cost of using very new cryptography.  Subsequent refinements, my by myself and many others found ways to accomplish several different versions of this without the bleeding edge cryptography; and we wrote a [whitepaper](https://people.xiph.org/~greg/sidechains.pdf) on the general protocol designs and motivations... and now a bunch of people have been off making these ideas a reality; and there is now a [running system](https://github.com/ElementsProject/elements/) called Elements Alpha against the Bitcoin testnet using these ideas (in a centralized-federated reduced security mode); ... it adds features like improved script flexibility, cryptographic privacy, and synchronization efficiency (to Bitcoin testnet).  When more improvements are ready, we won't hardfork the Alpha blockchain-- we'll just introduce a new blockchain and people who want to experiment with those features can just move their coins over.

This isn't the only one of the better ways-- but it's the one I think is the more powerful, which is why I'm working on it.

But its important to keep in mind that sidechains themselves are not scaling silver bullet. They're a tool which reduces the strong binding were everyone has to agree on all the systems' features and make different tradeoffs to get there.
Bitcoin is a system whos rules, Ideally, are governed by the computer code that runs the software rather than the preferences and politics of a centralized administrator.   "Money based on Math", as some say-- you can see callouts to this thinking in some of the [earliest posts about Bitcoin](http://p2pfoundation.ning.com/forum/topics/bitcoin-open-source).

This is possible because each and every full participant in the system verifies the rules of the system autonomously, via the proxy of the computer software they run: Their software trusts _no one_ and cannot be fooled. It doesn't care how many of their peers claim something, how many miners or hashpower says something-- if its inconsistent with the rules its like it never happened. Even if the user didn't personally audit their software when they first installed it, forever-after that point their system automatically enforces the rules it started with; immune to third party influence and political expedience.

When it works this allows a kind of strong integrity that no other approach offers;  just like a bar of silver is _still_ a bar of silver no matter what other people say-- but Bitcoin is more powerful as a money that silver: it can be transmitted online and its movement can be controlled by consensually applied smart contracts, which act like a perfectly predictable and politically uncorruptable but austic judge.

A hard-fork is a change to the rules which permit something which the rules previously prohibited. There is no, and can be no, rule within the system to govern the behavior of a hard-fork because, by definition, a hard fork is something the system absolutely and explicitly prohibits.  A hard fork is only possible if _all_ the remaining (after the hard fork) users of the system agree to adopt new rules;  an inherently political move rather than rule-by-math move.

Of course, it's not a black and white thing-- it's perfectly possible to conceive of a hard-fork this is utterly uncontroversial.  E.g. if the design of the system had a flaw that let anyone steal anyone's Bitcoins; no sane user would oppose fixing it.  Rule by math has tremendous and deep advantages both practically and politically, but it isn't a total suicide pact...

.... But, in my opinion, only because it can't be. In my view, in a perfect and ideal world there would never be a hardfork, or even a soft-fork (a change the strictly reduces the set of permissible things, so its "compatible" with old nodes). The system would be _defined_ complete by its mathematics, and any change would necessary be a new system.  No person would _ever_ be forced to adopt a change in rules that might be adverse to their interests just because other people preferred different rules. Unfortunately building a system so perfectly free of flaws, which automatically accommodates the users future needs, is currently far beyond the engineering capability of mankind. We can't even completely know or understand all the implications of the system we already have, so how could we possibly have informed consent to its complete set of rules and implications?  

If the Bitcoin system could really have never changed _at all_, as I'd hope for in that ideal world where Bitcoin was completely isolated from political corruption, then it would have died forever in 2010 [when a flaw was discovered in Bitcoin's design that allowed anyone to spend any coin](https://en.bitcoin.it/wiki/CVE-2010-5139).  This behavior was fixed by a soft-fork change, but if we were to adopt a (insanely) principled view we might rightfully conclude that this soft-fork "fix" was a form of coercive theft (to use some internet-libertarian lingo) from all those who understood the rules and preferred the infinite supply of coins.  Fortunately, it seems that no such people existed (though-- again, being very principled, we shouldn't require them to speak up in order to respect their right to not have the system change out from under them) ... and practically speaking, no system operated that way would be usable owing to the current impossibility of engineering something so perfect.

It's also easy to conceive of hard-forks that would effectively be theft in less wanky and purely philosophical ways-- from all the users or just a subset; a hard fork could-- for example-- enable taking the coins of some (perhaps an outcast class) and reassigning them to others. The decision isn't always obvious: What if the economy was in trouble and the best economic minds in the world said that inflation was critically important and many believed them?  If hardforks were _easy_ then the system could slide right back into a rule by politics and popular whim, though the design makes them inherently somewhat hard.

There has never in the history of Bitcoin been a true hardfork:  Versions prior to 0.8 were non-deterministic in accepting some blocks (even amongst each other), some some sometimes call 0.8 vs prior a hardfork, but I think that isn't a good or pedantically correct example as pre-0.8 would basically hardfork against itself, and later versions just agree with "lucky" prior versions.

You sure seem to have a lot of ideas about what skills, experience, and background people have-- for someone who hasn't worked with any of these people, hasn't worked on the project, etc.

Want to talk about crazy, -- how about going around and telling a bunch of people who've created tremendous benefit for the public, whom you've never worked with, whom you know little or nothing about their background and experience, what they should and shouldn't be doing. I think thats crazy.

You also seem to have some pretty warped and incorrect notion of what software development actually is, as if developers were typists; or that none of them have substantial experience in other fields.  You use this ignorance to justify some argument that someone (presumably you?) should be in control of how other people spend their time.  If you want to be in control, you could start by paying someone to do your bidding... as you're certainly not going to gain any followers through means other than copious piles of coin, given the above lack of charisma and social awareness. :)
I disagree that a "wallet" is a single key.  A wallet is a collection of many keys and metadata.   The whole reason the word wallet was introduced into Bitcoin was to distinguish between a single key and a collection.

I don't think changing the term on the wiki makes much sense but nor does it matter. You could say that those single key wallets are "wallets", they're just degenerate, non-private, reduced security wallets which are limited to a single key and no metadata. It's not the most useful definition, but we use words to communicate, and it doesn't make sense to go renaming things even if the names are confusing.

Regardless, people are going to use what they're going to use. 

(See also 'orphan' blocks, which is a misnomer caused by the wallets' display of orphan coinbase transactions.  ... or coinbase transactions, which is confusing because the name for part of the system has been appropriated by a company, etc.)
At least some of the people doing this stuff are bad actors that managed to hit the really high bar of scummyness to get successfully banned from BCT. Some of it is from people trying a lame reputation extortion racket.  I assume the rest is just trolling an earnestly motivated confusion.

As you note, the forum funds are mostly from ads. I think Theymos has done an awesome service for the community in turning the forum into something of an institution rather than not just pocking hte funds. 
For one, that it doesn't implement the required operations at all, its completely inapplicable. (I believe the signature cryptosystem it implements is some non-standard construction which is more or less unique to it.).

You could better ask why we're not continue using OpenSSL, since it's actually applicable and widely tested (in spite of its flaws:)  This is costly work, and not work we take lightly. We wouldn't be doing it if we didn't consider it important.

Bitcoin core has particular consistency requirements which are basically unique due to Bitcoin being a cryptographic consensus system. What would be a ordinary "bugfix" in normal crypto library  is potentially a fatal bug-introduction for Bitcoin.  It's unreasonable for us to expect random software to meet our determinism demands. (for example, if this broken squaring operation were used in pubkey decompression _fixing_ it in an uncontrolled manner would expose the network to a risk of an attacker triggered consensus fork: Not just hypothetically, I created an exploit; it just doesn't work because of a missed optimization in OpenSSL).

By specializing on just our needs we're also able to achieve much higher performance, and that performance can turn around and be used to improve decentralization or add security in other ways (e.g. constant time signing).

We're also targeting a higher level of publicly visible correctness assurance than we've generally seen elsewhere, hopefully one that doesn't at all depend on "authored by super diligent folks", because even super diligent folks make mistakes (see also the supercop/ed25519 having a similar carry bug). Some of this is just planned and not materialized yet, because the library is still a work-in-progress.

It's hard to express this succinctly for someone not involved in it. Maybe some numbers will help,  in the libsecp256k1 codebase 22% of the lines of code are tests. 7% of the lines of code are assertions (sanity checks run during testing to increase the testing power). 8% of the code-- some of the gnarly parts comparable to the part that was broken in OpenSSL-- comes along with manually verifiable proofs of correctness and somewhat more of the code have been machine proven to be free of some classes of error. In the last two months I've probably put in over 20,000 cpu hours of testing on random and quasi-random test cases myself. We've had at least 5 contributors who've reviewed (parts of) the system closely enough to spot problems, many crypto software projects are just one or two person shows.  I'm sure you can say cumulatively that something like OpenSSL has had much more work poured into it, but this is work that has all been very focused because the library does just a few things and not thousands of things; and we're planning on doing more of it: We don't yet think it's well tested enough to use in Bitcoin core except for signing.




Forget noobs. I've seen academic papers and 'officials' who believed that Bitcoin was based on some database provided by blockchain.info.

> EDIT: Also, GMaxwell seems to think it matters.

Only to the extent that it invalidated some of my original assumptions about how the losses couldn't have been substantial at all.

Of course, this is all out of context— so it's no longer clear that I was saying this to explain why I was no longer pretty sure that the losses were insignificant.
The sad part of his slam piece is that he goes on and slams a bunch of little places in the format where its somewhat similar to H264 but different in a way he didn't like with nary a clue that many of these were carefully constructed patent avoidance.

Jason is a very smart guy and I have a ton of respect for him, but at least at the time he didn't know much about patents, and I believe hadn't looked at any of the H264 applicable patents.  Google has an army of lawyers, many specializing in patents, and On2 (the company Google acquired) had decades of success in selling video codecs to people and not getting sued or their customers sued.  Even before webm the on2 codecs were a jucy litigation target, with billion unit scale deployments (use in skype, and flash in particular, but everyone from apple to Sony have shipped them at some time on another)

When did the MPEGA LA patent cartel "shoot down" Vorbis and Speex?   How many years of producing litigation free open codecs does someone need on their track record before you're convinced that they know how to avoid patent boggiemen?
This isn't really a captcha system,  it's a system for storing passwords for encrypted files which is brute force attack resistant.  It's trying to solve the same problem as [the scrypt key derivation function](http://www.tarsnap.com/scrypt.html).

The problem is that people use crappy passwords for encrypted files— good ones are far too hard to remember for most people— so if you get access to their encrypted file you can just keep trying passwords from the space of likely passwords and checking to see if a trial decryption produces decrypted looking output.  Because symmetric key crypto is very fast and most failed decryptions do not look like plausible correct decryptions you can do this search _very_ quickly.

The idea here is that you have a password in two parts. Together the two parts would be too long for you to reliably remember. So instead, you remember only the first part and the system stores the second in a specially encrypted form. 

You provide the part you know, and if you got it right, the computer provides a captcha-like image showing the other part, which you then type in.  If you get it wrong you also get a captcha like image but it (probably) contains no word.

The advantage of this is that it's hard for an attacker to automatically brute-force the part you know because every brute force attempt requires that he solve a captcha image in order to determine if he got it right or not.

This could be further enhanced by using scrypt like hardening on the first part, just in case someone codes up fast detection for failed captcha decryptions.  Which is probably the weakest thing about this:  The attacker doesn't need software smart enough to decode the captcha, he just needs software smart enough to quickly winnow the possible keys down to a small subset worth having humans solve the captchas. E.g. "Does this contain text at all".  And I think thats probably a much easier problem than actually decoding the captcha.



Thats silly.
In the US, at least, the truth is an absolute defense against any claim of defamation.

If wright sues someone here they would get extensive access to wright's otherwise private communication and records in order to establish the truth of their statements.  As you can imagine, this would be rather thoroughly against the interests of someone with a [long history](https://en.bitcoin.it/wiki/Craig_Wright) of making fraudulent claims, so it seems unlikely to me.

It would, however, increase my confidence a lot in the cryptocurrency industry if it stopped fueling abusive conduct like his simply because its profitable to let him continue to rip people off when you can make a little on the trades.  [This tweet](https://twitter.com/cz_binance/status/1116563034476957699) was one of the better things I've seen today.
> to shill your competing wallet with

The original bitcoin system is now my "competing wallet"?   You've totally failed to respond to the privacy points. Is there any way that my statement about privacy is incorrect?

> as replacing the SI standard units mBTC and µBTC with some weird ass double and triple meaning word

Bitcoin uses si prefixes...  BTC/mBTC/µBTC in the GUI. ( https://people.xiph.org/~greg/temp/bitcoin_units.png )
The "bankers" didn't send anyone there. Wright ripped the people of Australia off to the tune of millions of dollars by claiming refundable 'tax credits' (note: because they're refundable it means he paid negative taxes overall) for "research and development" that never existed.

Unfortunately, for the aussies he fled the country and that apparently made him too hard to reach for their seemingly incompetent law enforcement.  By the time they catch up with him nchain will have spent it all slandering Bitcoin and pumping Bcash, and through doing do transferring wrights ill gotten gains into anonymous bcash holdings where they'll have a much harder time recovering it.
> Currently we transmit entire blocks

[No we don't](https://github.com/bitcoin/bips/blob/master/bip-0152.mediawiki).

>  It sounds like an excellent method to reduce the use of bandwidth

It isn't, it's interesting at least academically but incredibly overhyped.  Their claims would amount to roughly 2MB savings in inbound traffic per day, assuming you didn't need to increase their sketch sizes to preserve the failure rate due to how costly failure is... and this comes the cost of a lot of software complexity and CPU.  For usage over the internet these sorts of bandwidth savings are irrelevant. Even if blocks were 10 or 100x or whatever larger (and ignoring the survivability of that) this wouldn't change: the amount saved would be higher but the percentage saved-- about 1%-- would not be.

The approach is also not original, but had been previously proposed and experimented with, and were in fact included in appendixes on the [old compact block design sketches](https://people.xiph.org/~greg/efficient.block.xfer.txt) (last updated 2015-12-25; see rocket science (2)).  The authors of the paper were aware of this but failed to attribute this prior work.  What we found in prior experimentation is that the extra complexity, CPU, and failure modes didn't justify the small additional savings.    The ideas behind compact blocks itself was something that were originally experimented with earlier but weren't a win at the time, so perhaps things have changed but I've seen no reason to think so.  (In fact the scheme in the above allows even lower bandwidth; because it also presents a incremental coding and subdividing scheme to avoid the cubic decode time for communications optimal set reconciliation)

Fancier techniques than compact blocks can be useful to achieve lower _latency_ but for that the criticial factor is the complete elimination of round trips and also achieving the minimum amount of added latency from processing. The fibre protocol achieves that quite well. In terms of latency graphene optimizes for the wrong thing-- decreasing serialized size helps but plain compact blocks like representations get almost all of that gain.

>  At least if the network didn't have bad actors. 

It wouldn't surprise me in the slightest if any specific implementation they made was not remotely Byzantine robust... but there is no particular reason that the general idea can't be.   All you need to do is reconstruct and check the hashes and POW before relaying to consenting full nodes(validation can come after relaying).  If at least that much is done prior to relaying any attack requires making one valid POW per junk block.

Mining on something you haven't validated breaks the SPV security assumption.  Miners have done so in the past but they've also lost a fair number of blocks due to doing so-- it turns out that broken software results in invalid blocks from time to time (expensive mistake...) and anyone who doesn't validate ends up throwing away work when it happens.  That issue, to whatever extent it exists, exists independantly of things like graphene.
You should call it B2X or just 2X.   The former has the advantage of matching the ticker symbol some places will use--  and both avoid confusing it with Segwit.

You seriously misunderstand how Bitcoin and mining works.  Less than 4% of the Bitcoin hashrate mines Bcash and yet it's strumming along and has a whole subreddit of people saying that its going to replace Bitcoin just as soon as s2x disrupts Bitcoin enough for it to take over.
Bad news:  you shot yourself in the foot a bit there. :-/

The first start with the new version takes longer because it was converting the database.

From the draft release notes:

> The first time you run version 0.15.0, your chainstate database will be converted to a new format, which will take anywhere from a few minutes to half an hour, depending on the speed of your machine.

Good news: you get to test reindex speed! :)
> all safe as I spent them after activation. ;)

That was not safe, and was specifically the sort of thing that we were trying to avoid causing people to stumble into.

Consider, you spend the outputs, then miners reorg back to the block one before activation, mine a block that steals your coins, then mine forward. 


If no one uses segwit blocks won't be larger than 1MB.
> I thought they'd be disconnected and banned automatically once they start sending invalid data.

No, esp with replay protection or use of the HF bit any banning will be really slow or it can even not happen at all.

Basically the headers from your incompatible peers will look fine. But if the headers don't form a longer chain, you won't even fetch the blocks to notice that they're incompatible.  You will also only ban one peer for each incompatible block you receive, and may well end up just replacing it with another incompatible peer.  Also, if you are surrounded by incompatible peers and they just take a long time to get a block at all after the fork, then you just will sit silenced, thinking that there have been no blocks for the last couple hours.

The protocol should probably be improved to be more aggressive about this, but honestly the first time this came up was amid one of the big 'classic' pushes; and I know I was worried about creating drama exactly like we're being hit with in this thread... and the prospect of writing a BIP for protocol extensions to make incompatible peer banning faster and getting crapped on at the list by Zander and PeterR just, really isn't appealing.  And even with it, to make sure you can get good connections you really want them long before the fork.  Even if you ban instantly, you'll just have the entire network go into that state and then DOS attack itself trying to find connections (which attackers might make worse...).

In any case, any change where the behavior of your node changes significantly when you might not be there to monitor it, notice something is wrong, and kick it is something we try very hard to avoid. It's important that if things are going to fail that they fail at upgrade time as much as possible... and that they fail for small numbers of users at a time, if possible.  Otherwise things like a wide scale (hopefully temporary) collapse of the network becomes possible.
It's a lot easier to give a good impression if you set yourself apart from the public.  Doing so has the implicit effect of demonstrating that you are better than others by setting yourself above them.

I make a conscious effort to remain part of the community and avoid creating that kind of pulpit-- talking one on one with almost anyone whos interested, even though it burns a lot of time and effort.  Ironically, I still get a lot more accomplished in terms of directly making Bitcoin better than many people who operate more "efficiently" by shielding themselves from interaction.
Uh. By the technical definition of turing complete  Bitcoin script: It has (sharply!) bounded execution and memory limits.  (By this mark Ethereum's VM is not turing complete either, FWIW).

However, Bitcoin script is universal and can simulate any circuit that fits in its limits, also called a total language-- this is obvious from the fact that you can use it to construct a controlled not (for example). (See also the [script talk page](https://en.bitcoin.it/wiki/Talk:Script) on the Bitcoin wiki)

> This was clearly known to CSW 1.5 years ago, which stumped Nick Szabo and no Core dev agreed 

Hogwash even the comment on the wiki there predates wrights nonsense by eons, and I'm sure if I looked I could easily find places where it was pointed out on bitcoin-dev back in 2010 or 2011.

Wright's babbling about the alt-stack is just that: babbling.  Forth like languages have a second stack because it's a massive convenience: it allows writing shorter and simpler programs. But since Bitcoin script has random access to its stack (via opcodes like PICK) if you ignore the resource limits you can convert _ANY_ script using the altstack into one that doesn't use it... though the script may be somewhat longer because you need extra stack manipulation operations.

Wright claims to have a script implementing wolfram 110  "running" on the Bitcoin network. This is not especially plausible due to the lack of effective covenants (and other resource limits on script), but if it exists and actually works I'll happily go convert it to a version that doesn't use the altstack: he need only point to the transactions (but he very likely won't because he isn't telling the truth).

In his recent comments Wright is confusing this random-accessable forth like stack with a very different kind of stack used in a very different kind of system: If you build a finite state machine (which Bitcoin script is not) and equip it with two simple push-down stacks where access is limited to the top elements, then this can be shown to be turing complete (but not if it has only one).

Moreover, turing completeness is meaningless for anything actual application Bitcoin Script: https://np.reddit.com/r/Bitcoin/comments/666ihb/posts_theorem_and_blockchain_languages_why_turing/  ... when there is something that Script can't do ONLY because the resource limits don't permit enough operations or because the enclosing environment hasn't provided the right access to the relevant data.
uh, where are you extracting "can only say no" from?   There are thousands of things core has collectively said yes to, including segwit itself.
Segwit is is ABSOLUTELY NOT PATENTED.

(And, as an aside: Segwit was a development of the community, there wouldn't be any reason to think blockstream would have any patent interest there except for general conspiracy theories.  But in an alternative universe where it did, Blockstream has one of the most permissive patent programs of anyone out there.)
Adam doesn't really have anything to do with Bitcoin Core (beyond being a user and supporter of it and whatnot), FWIW.
No he didn't.

Being frank about limitations or concerns is not bashing. It's too bad that a lot of people can't tell the difference.
You seem to inhabit a different reality than I do.

Petertodd did some contract work for various altcoins but AFAIK he always had them pay for him to do the work in Bitcoin and then they backported from there.

Having altcoins pay to improve Bitcoin seems like a pretty good deal to me.

People have tried many things to deal with the fact that the Bitcoin space has been fairly disinterested in supporting infrastructure development. (while, meanwhile, altcoin developers can personally gain millions without writing a single line of production code.)
If a block isn't full then something is wrong, perhaps seriously wrong:  It means that either miners are censoring transactions or Bitcoin confirmation is so worthless to people they won't even pay the tiniest price for it.

Without full blocks that means there isn't a backlog of fee paying transactions, so as the subsidy continues its geometric fall there won't be a strong incentive to move the chain forward-- instead miners will be [incentivized](https://medium.com/@bergealex4/bitcoin-is-unstable-without-the-block-size-size-limit-70db07070a54) to orphan each others blocks until they fill up or until a single miner mines multiple blocks in a row (a centralization pressure), and miners that don't will lose money and go out of business.

Without blocks being full the appropriate free market price for transactions fees is just slightly greater than 0.  This is not a level which can sustain a high difficulty in the long run, and with it the difficulty would fall until the security of the network becomes questionable.  Survival of the system would require constraining capacity until fees rise to sustainable levels- either by lowering the limits in the consensus rules or invincible cartel behavior censoring transactions, the latter of which only works if mining is centralized enough that the cartel can punish anyone who doesn't go along with their plans and instead maximizes their own income.

Full vs not is really not the metric you probably want to look at-- through most of Bitcoin's life blocks have always been more or less full relative to what miners were willing to create-- that's simply how the software works:  It takes all the transactions it knows, orders by feerate, takes the top $LIMIT.  There used to be a hardcoded 'soft' limit, below the consensus limit-- but it was removed and the bottom fell out of fees for a while until usage 
caught up with it. Just because not full means low to nearly zero fees, "full" doesn't mean high fees.  If fees are what we are concerned about, then fees should be what we're talking about about-- not "full", especially since not-full means death in the long run, at least how the system is designed today.

Assuming miners aren't censoring the blocks will simply always be full-- one guy while a while true loop can write gigabytes of transactions per minute. And at a sufficiently low transaction price it's attractive to just try to backup your systems into the Bitcoin's global highly replicated storage, several people have tried to make businesses out of this sort of abuse.

The interesting metric you should be looking at is [how much is there at or above a given rate](https://anduck.net/bitcoin/fees/).  If you're authoring a transaction at 50s/byte then all the transactions below that rate simply don't exist for the purpose of when your transaction gets confirmed.

Your unplugged USB stick is likely safe period, because the short traces will not pick up much induced current-- and even if they do it will likely be recoverable from the raw flash.

A USB stick in a steel safe deposit box is likely even more protected, the steel is conductive and will act as a Faraday cage-- throw your phone it while running something that will log the reception-- you'll see it will go to nothing or nearly so.

If this really bothers you. wrap it in conductive foil (ideally copper, if you're going to bother protecting against fringe effects, might as well go all the way)-- the protection from EMP would be effectively perfect and you can stop wasting your time worrying about it.

Your real risk is encrypting the key and forgetting the password, people massively overestimate their memory... or having a paper wallet in a place that gets flooded, fire melting it down, etc.

I wouldn't put EMP even in the top 50 risks, but if it bothers you, some [freeking foil](https://www.youtube.com/watch?v=urglg3WimHA) will solve it without any negative effect except wasting your time.
No, miners must update their infrastructure to signal segwit with 0.14.0.
No amount of modularization can save code from the BU tornado pulled  all the global definitions out of dozen different files in different modules into a single file, and went around changing widely used constants to strings like BLOCKSTREAM_CORE_MAX_BLOCK_SIZE and BLOCKSTREAM_CORE_MAX_BLOCK_SIGOPS for no apparently reason except to insult Bitcoin Core contributors (who overwhelming don't work for Blockstream) and demonstrate unprofessionalism.

> how do you know its Bitcoin.com ?

You can tell by looking at the content of the coinbase transaction in it.

> you should have come up with some form of compromise by now 

LOL.  You mean like offering a solution which provided equal to ormore capacity than Bitcoin Classic's BIP109? -- while also actually improving scalablity and fixing other issues to help get people off the fence so that everyone who wasn't out to split the network for the sake of splitting it could get on board?  Like that you mean?

> stay relevant

It must be a fun place in your head. None of us are trying for fame, we avoid exposure as much as generally possible, and for a long time kept our involvement more or less secret.  I would be overjoyed if someone else competent were doing the insanely hard and often thankless work we do on Bitcoin.
> Updating nodes has zero effect on SegWit adoption.

Of course it does. There is no urgency to activate segwit if people aren't ready to use it.

(and if too few nodes are upgraded than it could even be disruptive-- though we're long past that point for segwit)
It did a while ago, 0.13.99 nodes are also segwit. :)

>  you are stuck with democracy-like features, 

There is not much of anything in Bitcoin that is democracy like... Bitcoin software enforces its rules in an absolutely steadfast manner, no matter how 'outnumbered' it is-- and it has done this since day one, it's a fundamental part of the design.

> context of being accepting of different opinions

You mean in the context of violently forcing changes to the fundamental properties of Bitcoin, incompatible with the rules it was setup with, against the wishes of many of its owners... for the benefit of a few people, some of whom have stated that they would be completely fine with Bitcoin becoming a centralized payment system, instead of a global decentralized currency?

> On a more personal note, I think it's sad to see that core is so adamantly refusing any compromise,

wtf man, we made a proposal that offered roughly the same capacity as classic, but surrounded by risk reductions, scalability improvements, and fixes so that it would be appealing to everyone. It's such an insult to hear you say there is no compromise.


Bitcoin Core 0.13+ uses C++11 a five year old revision of the C++ standard that adds many features which improve code clarity, safety, and performance.  0.13's adoption of C++11 comes as part of a long transition that begin in the 0.11.x timeframe.  0.12 is fully C++11 compatible but doesn't require it yet, 0.13 requires it but only in relatively minimal ways and in new features so that bug fixes there could have be backported to 0.12. 0.14 will require it much more extensively, and 0.15 will likely go through and change out all the old pre-C++11 ways of doing things to more modern approaches.

Ripple, for example, required C++11 from day one.  I believe this is also the case for the C++ ethereum codebase.

In any case, this is not something new with segwit.

Every current Linux distribution worth mention has C++11 support but some parties are on older versions for various reasons, some better than others.

For these users there are several options:

The Bitcoin.org binaries are statically linked and will happily run on very old distributions, including ones without a C++11 compiler.

People can also compile their own static binary on a newer system, like the Bitcoin.org binaries-- but letting them make customization. (With the build process in Bitcoin Core it's also possible for anyone to _exactly_ produce the binaries the project distributes... many people successfully doing that is a mandatory part of the release process for Bitcoin Core).

Another option is to simply install a newer compiler, for example-- for RHEL/Centos newer compilers appear to be available at https://people.centos.org/tru/devtools-1.1/5/x86_64/RPMS/  and https://people.centos.org/tru/devtools-1.1/6/x86_64/RPMS/  (GCC 4.7 or better is required, RHEL/Centos 7 ship with GCC 4.8)

Perhaps someone will write up a more extensive tutorial for the Bitcoin Core website soon.
That isn't correct.  The flag for it retires automatically in a year to make sure it could be used for something else if people decided they didn't want segwit.  But there is no reason it can't be renewed so long as there is interest.
Really? I haven't seen an fast uptick in UTXO size like that previously except in confirmed spam attack cases.

I'm not saying that this is the case here, but the theory posted by some elsewhere that this is just a product of some slower blocks is more or less completely refuted by the UTXO activity.  This is a clear change in transaction behaviors from typical.

Segwit is compatible with every node currently on the network. It doesn't deny access to or undermine any Bitcoin user.  It was developed in an open collaborative process, is well specified, and will not activate without a demonstration of overwhelming enforcement by participants. Once activated it has almost no effect on users who choose not to use it (except miners, whom had to support it with an overwhelming amount of hashpower).

Support for it in most of the community appears to be near universal, barring attacks which are explicitly unrelated to segwit itself.

Finally, unlike efforts like XT and "Classic" which were heavily promoted here with sibyl attacks and vote manipulation; no significant amounts of that kind of problematic behavior existed for segwit at least as far as I'm aware.

So I'd really love to hear why you think this is incompatible with rule #5. 
>  segregates or removes the witness (the HOWs) from the Blockchain database. 

It absolutely does not.

The witness information is logically moved* to a different part of the block (the newly added witness part) but it is preserved, transmitted, and handled just the same as always. The blockchain continues to cryptographically commit to the witness information.

Even ignoring how useful this information is is for record keeping reasons, it's also critical that it still be committed to because when validating a block if someone was able to corrupt the witness you wouldn't be able to distinguish an invalid block from one where the witness information was simply corrupted. This isn't an issue for segwit because the information is still part of the block.

FWIW, the break up I'd use is WHAT, WHEN, WHY.  The transaction proper, without the witness, tells you what happens. The blockchain inclusion determined the WHEN, and the witness data tells you WHY a particular change was permissible.

Regardless, they're all important but in different amounts in different contexts.  Lite clients, for example, are non-validating, and so the WHY information is generally meaningless to them.

(*"Logically moved" in this case refers to the commitment structure of the block. By separating the WHY into its own set of commitments, it becomes efficient to access or refer to one without the other. This ability to refer to the effects of a prior transaction (WHAT) while ignoring the WHY is what resolves malleability).

With segwit, blocks have a weight limit instead of a size limit.   The maximum weight they can produce is 4000000 units.

The selection of transactions that maximizes the fees, given at weight limit is the set of transactions that pay the most fees per unit weight (or fees per size, pre-segwit)-- except for packing problem boundary conditions where you might be better off taking out a larger higher fee rate transactions so you can include a (set of) lower fee rate transactions that better fill the block.  Ignoring that corner case, picking the highest feerate _IS_ optimizing for the most total fees.

Because the median weight of a transaction (1000) is small compared the limit (4 million), the loss that comes from ignoring the corner case is tiny.  Meanwhile the nearly optimal selection can be performed _very_ fast: transactions are kept in feerate sorted order, and the mining process just takes the top of that list. If in the future larger transactions are more common, then fancier selections might pay for themselves, but fee per weight will remain a very close approximation to the actual selection, and the best one available to the transaction author-- who can't see all the txn that will exist at the time their transaction gets mined.


> Essentially all the costs of operating a node scale linearly with blocksize

They're somewhat worse than linear, FWIW.  (For worst case block processing time, without the fixes in segwit, it's actually quadratic!) 
An interesting point you might not have been aware of:  The "hardfork blocksize" change which they marketed as simple and "change a 1 to a 2" (of course, it never was so simple but hundreds of lines of changes) -- has proven itself to not be so simple.

BU caused a consensus failure with Classic on testnet because they didn't implement the sighash safety limits of BIP109. This caused Classic to go and remove half of their BIP109 changes (the ones implementing the sighash limits that stop the N^2 bleeding) ... and they're about to merge a PR that removes the rest of 109 completely.  Leaving _no_ implemented and non-abandoned hardfork blocksize change with a specification...



It's somewhat disappointing that researchers are apparently unaware that we've been aware of these potential long range concerns for some time and have already begun deploying mitigations.

These include:

(1) Having a meaningfully limited blocksize relative to transaction load.  In this case, by meaningfully limited I mean such that there is almost always a backlog sufficient to make variance in the arrival times of blocks and fees moot. (If fees were nearly isotropic this alone would be enough, to the best of my current understanding... and most of the time we have several tens of megs of backlog, though not with remotely isotropic fees)

(2) Have wallets nlocktime new transactions that they create so they can only be included in the next block on the network or even later.  This is implemented and deployed today (IIRC as of Bitcoin Core 0.11). This creates a population of transactions whos fees can only be obtained by moving forward.

(2b) adjust timebased locktime handling so that there is not an incentive for miners to dishonestly move their clock forward to allow them to accept not yet mature transactions. (Done, per BIP113 and locked into the network).

(3) Arrange for miners to pay forward a share unexpectedly high fees, removing any disincentive to include unusually high fee transactions.  One proposed construction has been to consider the income gained for the next block as opposed to the income gained in the next two blocks including any prior forward payments, then pay forward 1/3rd to 1/2 of the difference to future miner.  Full exploration of this requires an extensive game theoretic analysis. -- especially difficult to do when we're talking about incentive dynamics in a system which is quite different from the one we have today.

(4) Make transaction fees paid by a transaction conditional the causal of the history of the chain they're included in (e.g. transaction commits to a recent block, and will not pay fees if included in a chain without that block), instead of just the height. (requires extensive incentive analysis... I'm less confident this one is interesting, though it's one of the oldest proposals in this space beyond the observation that the blocksize limit helps)

There have been a number of other proposals related to these concerns over the past four years; though many of them suffer from problems where some kind of fee behavior is enforced by the network that leaves users and miners with a local incentive to bypass the fee mechanism (e.g. by paying miners directly with transaction outputs, or entirely out of band).

I'd love to see someone to time to analyze the efforts so far or similar proposals instead of taking an an easy route to promote perpetually inflationary cryptocurrency (and Ethereum, in particular).

This isn't the first time we've seen people pushing for inflation as a 'fix' to Bitcoin though, and I expect it won't be the last.
Why do you think anything is different in Bitcoin?  In the current protocol signature hashing can take time quadratic in the size of the transaction but the limits are just in terms of the size.  The limited block size prevents this from getting to out of hand, but it rapidly goes nuts beyond that.

Segwit fixes the quadratic hashing. But it was the limited blocksize that kept it from being a huge system disruption vector before the problems and its solutions were known.

There are many risks, known and unknown that are mitigated by have appropriate limits.  Too bad some people want to just crank the size with nothing done to fix the _known_ issues, much less having any answers about the unknown ones.
I did -- see the link on "[review](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-August/010761.html)". But it's not EL5 there, I suppose that might help to explain it without any equations?

(1) The paper has a strong assumption that the cryptocurrency is perpetually inflationary. While this is true for Monero and Ethereum it is not true for Bitcoin, and it means even if everything else in the paper held the results would become geometrically less effective over time and ultimately not hold for Bitcoin.

(2) The paper assumes that the level of mining centralization cannot increase. The primary substance of the analysis in the paper is that in a given network there will be some level of fee-per-byte below which the increased risk of orphaning matches the fee level, resulting in an equilibrium. This equilibrium, however, is unstable-- when operating at it, miners can opt to further centralize and significantly increase their income due to having a lower orphaning rate for all the transactions they were already including as well as being able to to collect more. Each increase in centralization would increase the income. The unique stable equilibrium in the paper's model + ability to centralize is where miners centralize all the way and can economically accept every transaction they receive since no more orphaning against third parties would happen.

Effectively, the main argument of the paper is that orphaning will control load. No argument is given for the resulting load being one that anyone could afford to validate, but worse-- to whatever extent orphaning is economically significant miners can more effectively mitigate it by centralizing instead of shunting transactions. Dr. Pinna argues this more formally in the post I linked.

(3) Peter R makes a strong assumption that an increase in the block size increases orphaning. This is an erroneous assumption that arises from a misapplication of information theory.  The author was aware of widely deployed techniques such as the fast block relay protocol (relay network, created in response to orphaning induced centralization) which send less data than the transaction size at the time a block was found than the size of the transactions themselves. However, he mistakes these techniques for compression, and argues that while there may be compression it must be finite and so the relationship must remain (but with the blocksize increased by a potentially arbitrarily large constant factor).  I pointed out that these techniques are not really compression but a early and inefficient form of preconsensus (a class of techniques which which include things like weak blocks). The idea behind preconsensus is that miners, through some mechanism, come to agreement about which transaction data they will include long before the block is found (perhaps several blocks ahead). Then, when a block is actually found, only a constant amount of data must be communicated (a nonce) regardless of the size of the block.  [As an aside, Bitcoin-NG, in parallel, proposes a kind of preconsensus for the same block race mitigation reason, but they use a 'temporary miner identity' based technique; while I'm more interested in mining based preconsensus.]

Any one of these points alone would bar applicability of the work to the Bitcoin system. (1) could be argued to be the weakest, since Bitcoin is inflationary today-- but especially with efficient relay, the resulting 'limit' would be technically and economically equivalent to unlimited long before Bitcoin's inflation was zero.  From a policy perspective, since inflation freeness is a primary value proposition it is in all our interest to not drive the system towards a state where perpetual inflation may be required for its survival.  (2) has the most impact in the short term, because the improved relay technology in (3) is not yet in production, this means that Peter R's argument that orphaning would be an effective control on blocksize is equivalent to an argument that without a blocksize limit we could expect even worse problems with mining centralization.

Cheers,

> Uh, didn't segwit fork on testnet as well? It's testnet.... Duh.

No. It didn't but that didn't keep roger ver from repeadily making that untrue claim.

Segwit never caused any fork on testnet.

The story here is that while segwit's consensus rules were being designed and changed all the time, there were several throw away "segnets", so as not to disrupt the public integration/interop network ("testnet") with random churn.

During that development, a developer started up an old version of the segwit software without realizing that it was old and found that it rejected the segnet chain.  He commented in the bitcoin-dev chat that he was forked and it should be investigated. A few hours later he looked in detail, realized he'd just been using old software and life went on.

Then rbtc ers started flooding their subreddit and this one with FUD that segwit was broken. Derp.  More recently, after Ver's pool activated BIP109 on testnet it minded a BIP109 invalid block and knocked Bitcoin classic off of it, and ultimately forcing Bitcoin Classic to [remove](https://github.com/bitcoinclassic/bitcoinclassic/commit/1f18a92c1c5fee5441dd8060022d7ecb80d2c58d) the limited sighash safety protections added by BIP109... for compatibility with Ver's pool software which signals 109 but doesn't actually apply its rules. ... when I raised the issue, ver had the audacity to claim that segwit did the same thing, citing a post on his forum where the very first response was someone pointing out the claim was mistaken, months before.

No, Bitcoin uses digital signatures-- an authentication technique. Nothing is encrypted. No concealed message is conveyed.  

Some wallets use encryption purely locally as a way to password protect their access, but no encrypted messaging.

Historically, regulation has treated authentication distinctly from communications encryption (e.g. ITAR restrictions).

This activity from the FBI is an attack on civil liberties and personal security. It wouldn't be good for Bitcoin because a less secure computer using public is one less able to use Bitcoin.  But it is unrealistic to expect it to harm Bitcoin directly, please don't FUD and overstate the risks there.

I would strongly encourage everyone concerned about these attacks on personal freedom online to get as many people as possible to watch Oliver Stone's upcoming Snowden movie, especially on the early showing on the 14th.   I think the movie does a great job in making the ongoing uncontrolled government intrusion into people's personal privacy online clear and emotionally significant.
Pump those alts, Roger.
This is precisely the opposite of "Trust us."
> The reason that was stated  for Gavin's removal was that he was hacked

Where was that reason stated?  AFAIK no committer to the project made any such claim.-- the only thing I'm aware of is Peter Todd made a comment in a tweet (with the limits of that medium.).  Wladimir's comment here is the first public remark on the matter from any commiter, as far as I know.

[Personally, I think it would have been better to be able to comment on it with more time to gain understanding, but pressure like yours demanded a comment now.]

The reason Wlad sent privately when he did it was that he was concerned that Gavin's was seriously confused, hacked, or otherwise compromised and that his continued access was now a clear risk. I agree with that assessment.

This is on top of a backdrop shared among all project contributors that Gavin was not using the permissions for the last year-- and not participating much at all for the last two years, and the fact known to some that he'd already been asked to resign this access (in the sense of, to avoid the indignity and drama of having it revoked).  There is a cost/benefit analysis to any question of access, and for some time the only reason the access remained was because the harm of drama enabled by doing it offset the costs. With the Wright announcement the balance changed.

I contacted Gavin immediately to point out the concern that he was hacked; and I pointed out the proof that wright's blog post contained fraud. Hours later Gavin responded saying he believed "Dr." Wright. I asked some questions about the evidence he saw and the situation around it, e.g. requesting a copy of the signature (before it was known he didn't have it). I continued with some polite information gathering about his claims.

Gavin stopped responding when I asked "How long have you been hearing from Dr. Wright?"

He has continued to not repudiate his position.

There are many serious questions remaining about the event and the risks presented, but it would be a waste of time and an insult to his dignity to go through the motions of trying to clear them when there is no reason to restore the unused access.

Some of these questions may turn out to be unanswerable, and the poor judgement of announcing with certainty something like this, especially without any warning to the other contributors would itself be an important reason to produce more distance here, even without the extensive history, the lack of activity, and the unanswered questions about the specifics.

No one is currently at risk of being unable to transact Bitcoin. The basic layer of the system currently handles on the order of a half million transactions per day; already existing upper layers increase that arbitrarily far (look at the trade volume in exchanges).
They exist, but Bitcoin was expressly designed to replace politics and third party trust with distributed algorithms and cryptographic proof, as much as possible.

It's impossible to achieve it completely, but we should strive for that ideal since it's a significant part of what differentiates Bitcoin from competing legacy systems of money.
> which goes under a different label in that post

"Non-bandwidth" scaling what people at HK were calling these things.  Must be someone elses PR guy-- but a good one. The "off blockchain" thing seems to directly cause a collection of weird misunderstandings: like that they require third party trust, or that their transactions are necessarily not Bitcoin transactions.

With a thoughtful and at least semi-technical audience I like to explain cut-through to open people's eyes to the simplest form of what real scalability looks like: https://bitcointalk.org/index.php?topic=281848.0

I hope I made it clear in my post that I'm not expecting bidirectional payment channels or the like to magically save the day. The role they play in that plan is preparation for the future.

And yes, we've all been working on many things-- see for example the _7x_ connecttip performance that's already done; and explaining this stuff is really an art. Bitcoin has grown so fast that the community of interested people has vastly outpaced our capacity for teaching.  How can I do a really good job of explaining what a great impact a 7x connecttip improvement is when I first need to explain what connecttip is.. and why performance matters for security and so on.. all while actually doing the work and making sure that it works?   ... not to mention that we're also busy on other improvements outside of capacity techniques to protect and grow Bitcoin into the future (e.g. consider my work on Confidential Transactions).

This is all a good problem to have, and we're working on communicating better-- some of it you're already seeing like the weekly dev meeting summaries and even the above post.

>  so we flock to simpler, easy-to-explain stuff like BIP 101

The funny thing is that 101 is simple to _explain_ but the consequences aren't simply at all... and the implementation isn't either.  The bitpay "bigblocks" BIP101 backport patch is 3422 lines long; the segwittness patch at it's current state is 2589 lines long.   Segwit is less mature and all of the components, eventually it may end up bigger ... but something can be 100x more complex to explain to someone who isn't a Bitcoin developer while being similar in complexity to implement or even less complex in its full ramifications.

Some proposals sound simple when you don't have much context, but complex when you do. While other things sound complex or pointless when you have no context, but simpler when you do. And teaching that context takes a lot of effort which competes with actually maintaining the system.


No. Replacements can be pre-computed and time locked, though software making use of RBF hasn't been written yet.

For example. At block 100 a wallet wants to make a transaction set to confirm within 3 blocks. The wallet authors a transaction locked at 101 with its best estimate of a three block confirmation fee, at the same time it also authors replacement transactions locktimed for heights 104, 105, 106, 107... each paying (say) 1.5x the fee of the last. These can be handed to a node that accepts advanced locktime transactions.

Even if miners know higher fees will be paid in the future, rationally they still prefer the one they can include now, since if they wait another miner will likely take the fees. The multiplicative increase suggested above means that at worst a transaction would overpay by 50%, but can still reach arbitrarily high fees in few transactions.

Then don't use it: Don't set it on your own transactions and treat transactions you receive with all sequence numbers < MAX_INT-1 as non-existing (or already double-spent) until they confirm. Opt-in RBF is Opt-in.

No commonly used software that I'm aware of sets its sequence numbers to below MAX_INT-1, and many programs (including "transaction confidence" meters) already regard low sequence numbers as potentially double spendable. After all, the transaction has been explicitly marked as replaceable, and even without RBF, nlocktime may result in a conflict getting confirmed first.

If someone sends you a replaceable transaction and you won't zero-conf credit it, their replacement can make it get confirmed as fast as they want it to get confirmed. The same sorts of situations exist already for senders using non-standard transaction features or spending unconfirmed outputs, which transactions objectively more double spendable, but in those cases there is no fix to get the transaction through quickly. 

RBF is a feature for consenting adults. If you don't want to participate in it, you don't need to. Your dislike of it isn't a reason to prevent others from using it in transactions that don't involve you.

Many people believe common dislikes of RBF are likely ill-founded and that non-RBF is unreliable and unsafe, but you shouldn't mistake their explanations for an argument that you have to use it.
**What if I think that RBF is just awful?**

What doing that does is lets them double or tripple the size of their transaction load with someone else picking up the tab.
Cut Cypherdoc some slack here.

You see, he has special expertise in financial conflicts of interest--  in fact, [he's actually being sued right](http://www.plainsite.org/dockets/2k5w2nwul/california-northern-bankruptcy-court/hashfast-technologies-llc-adversary-proceeding/) now by a prior customer's bankruptcy liquidator for 3000 BTC which he took along with other considerations for paid shilling. The liquidator argues that he didn't provide 3000 BTC (at  about $100 BTC) worth of shilling services, though considering his vigor and how many BCT users he talked into ponying up for hashfast hardware that isn't so clear to me that he didn't earn his keep.

Considering the context he's coming from, I can't blame him seeing greed around every corner; even where it's not there-- or easily disproved. Heck, maybe this whole sidechains thing is really a diabolical plot on my part to get back the BTC he wrongfully gained from me. :P

(Of course, since I've always been a miner as well; a local mining hardware company with a solid refund policy-- didn't get as much scrutiny as I normally apply and I got bilked too;  Cypherdoc was quite overjoyed by my web of trust downrating him; and with that and my whole crazy view of I don't think we should list convicted felons on Bitcoin.org as press contacts thing, he's been my drama buddy for a long time.)

I assigned 47, indeed!
Because unlike a regular transaction if it falls out of the chain in a reorg it can never be restored.   100 is just a first approximation of "infinity"... as a newly generated coin is further burred it takes a larger and larger reorg to reveal its non-equivalence with a regular coins.
http://xkcd.com/893/

(make sure to read the alt-text on the image.

It's not clear which of the two we'd be— but the brave astronauts we still have today give me hope that we'll be the latter kind)


Of course, — any EME module is going to be leaky... someone will fire up a debugger and yank the keys right out.

But that is only easy if you can still _run_ a debugger. The next natural step will be to gradually upgrade EMEs to require things like ARM TrustZone and Intel TXT hypervisors.  By that point the standards body will be out of the way because the hooks will already be in place.

I think this is really why free software organizations oppose this stuff— not because they care much about DRM but because the only way to make DRM remotely effective is to lock down the entire platform, seal it in epoxy, and change it often.  In that world free software might still exist, but only in the form of free labor for technology oligopolists.

A tiny fraction of the latency so its suitable for interactive use which Vorbis is not, vastly superior low rate speech, overall better quality per bitrate, especially at the 48-96 range which is interesting for streaming, and implementation that includes a fixed point encoder are the topmost things that come to mind.
QC and Huawei didn't participate in the process except by trying to disrupt it, never in any technical way— ah, the price of a truly open process. Fortunately(?), the IETF requires IPR disclosures from basically anyone who touches the process, even if they only touched it for the purpose of trying to kill it.

They stopped 'participating' after they were [publicly accused](http://www.ietf.org/mail-archive/web/codec/current/msg02383.html) of having a conflict of interest related MPEG "USAC" (an AAC successor which will likely be the most patent encumbered codec ever created) that they were working on.
Pure software patents— patents covering software which are not tied to a specific machine and do not involve the transformation of a material good except in superficial ways— are the only type of 'software patent' under discussion in connection with Bilski.

These pure software patents did not exist in the US prior to the [state street decision](http://en.wikipedia.org/wiki/State_Street_Bank_v._Signature_Financial_Group) in 1998, which is why the appeals court decision in Bilski is often described as 'overturning state street'  (which isn't really the case: It reached a different decision, but did so by analyizing a different aspect of the issue).  

So the grandparent post is simply answered: What software depended on this kind of patent protection?  Only software released after 1998 could possibly. That narrows the scope of the potential harm in limiting these patents pretty substantially.  

The harmful effect is more subtle than being directly blocked by patents, although that does happen.  The bigger issues comes from the patent climate making it unwise to invest in development which depends on, but which is not itself, extensive amounts of software  as you may find your investment flushed when your competition gets ahold of critical patents.    Think about all millions of efforts which utterly depend on software, but which are not software products.

This class of patent tends to be exceptionally broad and very difficult to engineer around, unlike many patents on mechanical methods as these patents cover the underlying idea or algorithm rather than the minute physical qualities of an implementation.  Not only are they hard to work around, but they are very difficult to search for (as there are an infinite number of ways of stating a single algorithm) and this also inhibits the searches for prior art. It's not uncommon to see post-1998 patents describing mathematical techniques invented in the 1800s. 

So you have things like VZN buying up a bunch of old defunct VoIP patents and then, effectively, putting Vonage out of business once Vonage really started taking off.  The investors learn from this and aren't likely to make the same mistake twice and we all suffer.

If you mean it, start charging storage fees for large deposits left on your platform which haven't been involved an an open order for more than 30 days.

You have real costs in keeping those funds secure, assuming you're doing it remotely well.  I assume the biggest reason to not do so is competitive pressure, but someone has to go first...

Another option would be requiring users to keep a withdraw sweep address registered and automatically sweeping out funds from inactive accounts (or doing both and letting users choose).

This might be the wrong perspective. I think it's easy to argue that Your non-Bitcoin investments should be as far away from Bitcoin as possible, because you want your holdings to have the lowest amount of correlation possible.

You wouldn't be happy if you were wiped out because you had traditional investments with a firm that was rolling the dice on cryptocurrency and they got blown up due to cryptocurrency trades gone wrong AND your own directly held cryptocurrency lost value at the same time.

There is nothing wrong with traditional investment firms not getting involved in Bitcoin.  Bitcoin is a relatively new, poorly characterized, and highly risky as an investment. Vanguard is pretty much the definition of conservative.  It would be foolish to turn your nose up at them for doing exactly the sort of thing that makes them valuable to begin with.
If "information security is a priority at AMD" why do all their processors continue to ship with a poorly disclosed integrated secondary background processor which runs secret closed software which may be riddled with bugs (as have been increasingly revealed in Intel's analog), which generally cannot be disabled.
> You know that Core fucked up also in 2013

Not so simple.  Nothing "core" did there directly caused that. Bitcoin had a _day one_ bug where blocks >500k would cause nodes to randomly fork from each other, but it also wouldn't allow people to make blocks that large.  Bitcoin Core made it possible to configure the maximum block size, but left the default at 200k.  Mike Hearn [pressured miners to turn it up](https://bitcointalk.org/index.php?topic=149668.0).  Some miners did and the network split.

It was erroneously believed at the time that the split was caused by 0.8 because 0.8 didn't have the splitting bug and all ended up on one side of the split-- but this was mistaken, the pre 0.8 nodes randomly split from each other and the same thing would have happened even if the database changes had never happened.

You could fault the project for folding to pressure then and introducing that switch without testing the hell out of the bigger blocks it enabled-- true, that was a fault, but not really comparable to the B2X situation.  We've also since learned, or at least some of us: Jgarzik sure doesn't seem to have learned.

As far as miners losing money because the chain was split, Gavin gave away most the funds donated to support bitcoin development to the miners whos blocks fell out of the fork.
> Originally, he was cited as maybe being "hacked."  Now, they're saying that he wasn't an active contributor. 

Multiple things can be true.  Wladimir's statement was very clear: http://laanwj.github.io/2016/05/06/hostility-scams-and-moving-forward.html

Gavin's continued access was a violation of least privilege because he was inactive for a long time; he also had been behaving abusively towards project contributors and had previously been asked to drop the access on his own.


"But in a way this was only the final straw. His privileges were seen as a liability by members of the project for a while (and not just because of proxy threats from Mike Hearn to shut down the project). The principle of least privilege in computer security says that users, should only have access to the resources they need for the purposes that are essential to the user’s job."

The speculation that he might also have been hacked was a kindness-- even given his behavior it was hard to believe that he could be so recklessly incompetent or malicious to endorse an obvious scammer in that manner.


 https://people.xiph.org/~greg/banlist.gui.txt for gui users
> am just a regular person and you are the main developer of Bitcoin in the world

No I'm not, I'm just a regular dude who likes arguing with people on the internet.

> am just a regular person and you are the main developer of Bitcoin in the world

I don't care what you think of me.

I do wonder what terrible reasons you or anyone has to act like that towards other people.

But if by chance you think better of your comments, remove them and ping me and I'll remove my replies.

> shouldnt you be doing better things with your time than debate regular joe

I think talking to people about Bitcoin is critical.  For people to be free they have to have voice and understanding.  No higher authority is going to make bitcoin magically work for man kind, we have to make it work for ourselves.  ... and besides: you caused a notification to pop up on my screen.

What do you do with your time?  I bet there are a lot of things you do far less useful then calling out some abusive and inhumane behavior.
Except people were mining with btc1 or otherwise their HF wouldn't have activated... but they all stopped mining because their software rejects their own blocks. (BTC1 just won't mine now-- but lets imagine that it could mine, then what you're saying would be worse: That would mean segwit2x testnet doesn't actually have any segwit2x miners on it! (or at least so few they can't manage a single block in 24 hours))

People warned in advance about this broken bit of design and recommended they instead use the "hardfork bit" (set the most significant bit of the version number which will be rejected as negative by other software).

But they took no action to fix it or remediate or even to be prepared for it on their own test network
No. Litecoin is uninteresting technically and basically signed a suicide promise to get segwit activated there.
Yep. For Bitmain if they were worried about BIP148 disruption earnestly, they need only participate with it.
Yes, it's not shocking to see that kind of misunderstanding on Reddit. Coming from a mining hardware company or developer though is shocking.
This is another xthin bug according to [the issue above](https://github.com/BitcoinUnlimited/BitcoinUnlimited/issues/386).  Bitcoin Core does not and has never contained that.
Really, you're going to begrudge me that when the unlimited folks are executing a full on dishonest press campaign with dishonest claims about vulnerabilities in Core translated in to chinese to try to cover up the modest mistake the made here?
Jonny1000's research showed that AD splits can be more or less perpetual if strategically mined.  ... but even if what you said worked.. great, now you need 24 confirmations to have security that you previously had at ~2.
> Same goes for Unlimited. 

Not so, it's inherently incompatible. And some of BU's largest supporters have announced intent to attack users of a competing chain.

> If everything is everyone-can-spend, the currency is kind of useless. You cant even spend the anyone-can-spend stuff.

It looks like you have noticed that you are confused but done nothing about it.  Non-segwit nodes don't think segwit stuff is "anyone can spent"-- it thinks its a forward compatible extension that it doesn't understand. As a result it won't relay it, mine it, display it in the wallet (or spend it, for that matter).

> it's the same old consensus system, 

It is not. The center of BU's proposal is "emergent consensus"-- a radically different take on the operation of cryptocurrency consensus where miners set the rules by majority hashpower and all the other participants are along for the ride.

>  it just allows the 1MB soft fork limit to be changed if there is consensus

You could more or less have said that about Bitcoin Classic a few months ago with BIP109.  You cannot say that about BU.

Classic proposed a simple change to a 2MB block, and when the community found a compromise to achieve that the goalpost was moved to a radical miner takeover that the community was unlikely to find a way to compromise around.

Bitcoin Core implements [BIP152](https://github.com/bitcoin/bips/blob/master/bip-0152.mediawiki) Compact Blocks.

BIP 152 is in use on over 77% of the listening nodes on the network today.

BIP152 can achieve lower bandwidth usage (6 bytes per tx vs  8 bytes per tx PLUS several kilobytes for xthin) and can achieve lower latency than is possible with Xthin (0.5 RTT minimum for BIP152 vs 1.5 RTT minimum for Xthin).

BIP152's careful design also avoids a trivial collision attack that can be deployed against xthin to slow the propagation of blocks.

BIP152 also has a complete and detailed specification and was the first of these proposals deployed in production on more than a small number of nodes, and the first to have multiple inter-operable implementations.  BU likes to trumpet that they were first to release-- but this is because they deployed an unspecified and largely dysfunctional test version and marketed the hell out of it rather than working on engineering to make it better.
> just write the damn code for a hard fork and then BU will disappear overnight. 

Last year it was "write the damn code for a blocksize increase and then XT will disappear overnight."-- a solution was found, but the goalpost moved. For some, the real goal is disrupting the network; something for which few contributors to the Bitcoin project would abide.
You can just shut down cleanly and upgrade at any point, but unless your system is VERY slow, you will be finished before the release is out. The process has a minimum of one week in RC unless there is some emergency requiring an exception.
You can't have one kind of news all the time.  Speculation is a part of Bitcoin too.  Sometimes the price banter gets annoying, I agree... but it's good to have a bit of it.

There are plenty of other things discussed here most of the time.
Since rbtc's showed up and dragged away the most toxic people, rbuttcoin has been a lot better.
**YOU CAN NEVER "CONVERT" AN ADDRESS**

What you're suggesting doing is basically equivalent to burning money.  If you ever use an address that the remote party didn't specify the funds are likely destroyed, you certainly didn't make any payment that they they have any obligation to honor.

Imagine this monty-python sketch:

Bob: Where is my car?

Alice: You never paid, so we didn't ship.

Bob: Yes I did! I'll sue!

Alice: We checked quite carefully, you didn't pay.

Bob: The payment is in your basement!

Alice: Our what? We don't have a basement.

Bob: Do too! I dug one, just lift up the floorboards in the washroom.

Alice: ... what? [alice leaves] [alice returns carrying a small dirty fire safe].

Bob: As you can see, I paid.

Alice: This box is locked and we're going to need to talk about the damage in the washroom.

Bob: I assure you the payment is in there. Now hand over the car before I call the authorities.

Alice: What is the combination?

Bob: I used the last letter from every document sitting on the top of your desk when I was here last June.

Alice: ... I've since cleaned off that desk.

Bob: Well not my problem that you've lost your key.

...

That is the situation that you're creating when you imagine up some script that the person on the far side could in theory spend but probably has no idea of...

Wallets should _not_ try to promiscuously figure out anything they could possible spend; doing so ends with madness and potentially insecurity.  Imagine that a wallet realized that it could spend a 1 of 2 multisig it saw on the network, so it started crediting you with them.  Bob pays you for a car with one, and after he drives off he simply claws it back by signing with another key.

Cheers.

> supposed advantages of hardforks, which is that SPV clients do not need to upgrade?

FWIW, this is not an actual advantage of hardforks.  99.99999% of all reasonable sounding hardforks break SPV clients.  Classics' flexible transactions, for example would break any of them.

In fact,  a SPV client implemented as described in the whitepaper would be broken by anything that breaks a full node-- but that isn't what parties do today.

The "twiddle the max block size and do nothing else" happens to be apparently(*) compatible with BitcoinJ -- in part because Mike Hearn has been pushing that agenda for a long time.  It's not, however, a general advantage. But it is an advantage of softforks, one which also applies to full nodes.

(*) I say apparently because when I last checked no one had still bothered even testing it. But it seems likely from the design.
> will not achieve

uh. It has the fastest adoption ramp rate of a softfork so far, even though its somewhat harder for miners to deploy.  And the technically necessary activation threshold is a mere majority. So I am pretty dubious of your claims. :)

Most of the material is motherhood and apple pie. It's a common political technique to stand up behind a number of points that everyone supports in order to imply that people you disagree with don't support them. Bitcoin should be Free and Open Source? well no crap. All the big points are obviously true things that everyone agrees with, where it goes a bit off the rails is in the details: E.g. use of the BU dog whistle "emergent consensus" which has, historically, meant a radical departure from the design of Bitcoin, with nodes reducing their enforcement of the rules and handing control of the system to miners. Or the inaccurate implication that more incompatible implementations means more developers.

But what is far more interesting is what isn't mentioned: Sound money, fungibility, protection from political pressure, nor a hint of personal privacy and commercial confidentiality.

Considering the length of the document, I think those omissions are a bit telling-- if it isn't a fake, as Samson suggests it might be.

For one, when Bob starts up 100,000 "nodes" that aren't nodes at all -- just as we saw with Bitcoin Classic, hundreds of fake nodes that all went up and down at exactly the same time -- and the network ends fragmented all to heck and failing to do its primary job: securely coming to consensus and enforcing its coded rules without being highly vulnerable to coercion or political whim.

If it were possible to securely determine much of anything by 'count of nodes'-- we wouldn't need mining. :)

BU promotes a radical change to the philosophy of Bitcoin based on a misunderstanding of it. They view miners as being in charge of the system rather than a security service provider for it along with a unfortunate helping of idolizing voting as an ideal decision tool rather than the unfortunate compromise. Bitcoin has from day one been a system based on antonymous validation-- where no one can tell you what to do, even if they out number or out spend you-- the fact that a very narrow but critical part of the system's functionality, the ordering of transactions and fixation of the history, depends on a very specialized kind of vote don't change the the rest of the system is based on stronger stuff, and that the system's entire premise and values are best understood in contrast to political money. The rules of the system enforced by all nodes are a lot of what create the incentives for each miner to collaborate with the system.

Read the rest of the thread too, I simply linked the end of it-- since I thought it gave a pretty sound conclusion "put what I think is a heavy nail in the coffin of Peter's and My work".
It's perfectly possible to do entirely private proof of solvency.

http://crypto.stanford.edu/~dabo/pubs/abstracts/provisions.html

If your service would be willing to use such a thing, I'd be willing to help get the tools built to make it usable for you. Thus far most of the Bitcoin exchanges have been unwilling.
Thanks for being honest about your views on the subject at least, many people aren't.
Roger Ver should lead by example and turn bitcoin.com over to a neutral third party.

(Edit: Preferably one that won't frequently use it to promote ethereum)
My understanding is that it doesn't even fix the bug in the contract, it would just be to confiscate the funds taken as a result of the bug in the DAO service.

If MTGox's stolen coins had ended up in a single obvious case this would be almost precisely isomorphic. No one suggested rewriting the Bitcoin system rules to compensate losers in Gox-- and if they had they would have been laughed at.

Many other ethereum smart contracts have lost funds in similar ways. ::shrugs::

I am not impressed and I am reasonably confident a comparable action would not be possible in Bitcoin today.

(Some people have been claiming that similar things were done with Bitcoin in the past but this isn't really true. The closest you can come is the value overflow bug where a transaction created a billion bitcoins out of thin air-- a true bug in Bitcoin, not just a service using it, and it was removed not via a hardfork but by miners fixing the bug then naturally mining new blocks that didn't include the bad transaction. And this was also at a time where the sum total of all bitcoins was worth maybe a few hundred thousand dollars.)
    ./bitcoin-cli verifymessage "1AGRxqDa5WjUKBwHB9XYEjmkv1ucoUUy1s" "G9cTKD+zhuh7f79cbQnam6YU//Thja+t+LX9i597mOx+LtxSGZa+dpisAR7ygFeL/pk05HA/GKCkeNSmtsQI9NQ=" "If you meet the Buddha, kill him."
    true
> Maxwell

Uh. I have said pretty much nothing about any of this.

> But those threads are not being stickied to the top by /u/theymos,

What did Theymos do?  He made a post that showed the purported signature was bogus. It didn't even mention anyone except Wright (and the person that pointed out that the 'proof' was misleading). None of his recent comments mention Gavin: https://www.reddit.com/user/theymos

It sounds like you're accusing people of "personal attacks" for calling out _obvious_ fraud. Shame on you.
Back in 2011 I was worried that I might be sent to jail for working on Bitcoin.

One day, a friend of mine's father asked for help setting up Bitcoin miners. His father is a senior prosecutor at the DOJ. I haven't worried so much since then.

Thank you for your kind comments.
Almost every technical expert working on the system will tell you that this just isn't so-- as also demonstrated by the near unanimous support in the technical community for [Core's capacity roadmap](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2015-December/011865.html).  (including the final point: foisting controversial changes onto the network is a way to cement control).
I believe in using conservative figures.

Segregated witness does several things; fixing malleability, improving upgradability; improving scaleability, and increasing capacity.

The improved scalablity comes from the new security models it makes available.. lite nodes with full node security (under specific conditions), fractional ('sharded' verification), quick bootstrapping by not fetching history data you're not going to verify and are only going to prune, and reduced data sent to lite clients.

Increased capacity comes from the fact that it takes roughly 2/3rds of the transaction data from participating transaction out of the part of the block that the blocksize limit counts and moves them to the witness, where they're counted (by the implementation) as 1/4th the size.  The result for typical transactions is a better than 2x increase in capacity (more if multisig is heavily used).  In the worst case, an strategically behaving miner might produce a 4MB bloat-block; since thats the largest size you can get if you were to make an all witness block.  The typical increase in blocksize would be more like 2MB, but expressing it that way would underplay the worst case behavior.. 
This is misunderstanding of what first-to-file means. It does not diminish the effect of prior art, and only comes into play with interference between multiple applicants. The old system allowed costly proceedings to decide who "invented first" in the case where two applicants filed on the same invention and the matter couldn't be decided by prior art (e.g. by publications or public practice)... this process could easily be a contest about who was most dishonest in the dates they gave unpublished laboratory notebooks. 

Under the current US policy you can publish whatever you invent and your publication becomes prior art that precludes patenting by third parties (and eventually yourself), and does not enable someone to gain a patent on your invention simply by filing faster than you.
Instead the truth is quite the opposite.  There is a long history of scammers who are pissed at Theymos for punting them (or explicitly trying to extort through reputational attacks.); and it's usually throwaway shill accounts doing the accusing.

If Theymos were out to covert the forums funds to his own he could have _simply done so_, with no need for any pretext about it. The vast majority of the forums income has come from the advertising program he created on it.
Thats overstated. There is a lot of really broken crypto out there that no one is looking at and which gets used by lots of people.  Even when problems are discovered and reported they're often not acted on.

That some things get fixed isn't a proof of general health.
> Thanks for your reply and it's nice learn about some of the mitigation strategies you have employed. It might be nice to talk in more detail about that because this particular issue is something that's come up a few times

I'm glad that you're somewhat satisfied by it but as you note, you've done this to me over and over again on Reddit (and I've made the same clarifications before, in response to questions by you and others). The next time I am just going to respond with hyperlinks to the prior times you've done it. You're crying wolf. I'm happy to entertain any concerns from the Bitcoin community, but at this point I don't really believe your concerns are genuine.

> Your post ends with a strange ad hominem conspiratorial attack

I would note that you haven't provided any contradiction or clarification there. I do think your motivations are distorted; you continuously and vigorously make the same kind of allegation about me, following me around to unrelated threads; and not supporting it with any concrete evidence (even when I provide concrete evidence against those claims).

If you're not able to stand up to me pointing out "Hm. why is it that someone who's interest in this space is _competing_ with Bitcoin, is the most concerned about governance here; is their explicit effort to drive the highest volume contributors out of the project (e.g. your repeated demands that Pieter and I "step down") perhaps not motivated with Bitcoin's best interest at heart?"  then perhaps you shouldn't antagonize me.  I think it's a no less reasonable question than the ones you're throwing at me.

I think I responded in an more than adequate way to your concerns,  the "ad hominem" is mostly about the fact that every time you do this it means that I need to waste a bunch of my time, which I could otherwise spend working on Bitcoin core (software which you'll just go copy into your altcoin...) making sure that J-random-redditor doesn't only hear your attacks on my integrity.

It has burned people out to some extent.  Lots of discussing challenges is good, discussing the same exact things over and over again— not so much.
You're reading something into it which simply isn't there.

What RMS said is true. He doesn't know if the free software world can make those kinds of games, but if you try the ones it has created perhaps you'll find that you don't need the ones it hasn't.  Or perhaps not.   If you can't read that as something other than condemnation then you have a problem, not RMS.

And really— it's not a crazy point. When I look at things like [sauerbraten](http://sauerbraten.org/) it seems pretty obvious that the free software world is capable of producing output comparable/superior to at least some of the big commercial games of a few years ago. I would have gladly taken sauerbraten over Quake3.  It's not comparable to things like Half-life 2 but strangely enough billions of people have had perfectly enjoyable lives without ever playing half-life 2. ;)

They don't because it doesn't.  The 1e6 bitcoin number is a pure bullshit claim that comes from assuming that essentiall all unspent coins mined during the first year are owned by Satoshi-- something we know to be false (at the moment there are 1097602.49 BTC that were mined during the first year which have not been spent).   It also comes from a conartist claiming to be satoshi and claiming to have a million bitcoins.

No one knows how many bitcoin's satoshi has. Bitcoin was public before block 1.  It's possible that Satoshi only ever mined a few blocks and has since sold or lost most of it, or it's possible that he mined a bunch (but not the claimed million) and still has a lot... no one knows.

There is an argument that some early miner holds a lot of Bitcoin -- not close to a million but one to two hundred of thousand based on a pattern of the nonces values in early blocks... but nothing that suggests anything about who that party is.
I wouldn't doubt the conclusions for a moment but the methods seem pretty questionable.

They are assuming that a cold wallet could be identified by simple wallet clustering.   This is not a good assumption.

Sweeps out to a cold wallet would look pretty indistinguishable from customer payments to a new never before paid address.

Sweeps in would quite likely be done whole tx at a time, back to a new hot wallet address and would link few if any other hotwallet outputs.

Recent transactions only show that they haven't lost control of all keys, they do not show that they haven't loss control e.g. of cold wallet keys.

Occasional delays on withdraw are not strong evidence.  Coinbase, for example, has frequently had delays on large withdraws sometimes extending multiple days.  If delays on withdraws were considered strong evidence of insolvency there are a lot of alarm bells that should be ringing for a lot of different exchanges.

Again, I wouldn't dispute the conclusions:  That the exchange was insolvent should be a default assumption even without looking at any of this evidence.  In particular,  it's already established that Quadriga lost an enormous amount of funds due to broken ethereum "smart contract" / address handling nonsense.
> I think Faketoshi is saying that during a 51% attack segwit uxto can be taken by miners instead of just being double spendable.

He may well be saying that as he is highly prone to dishonesty and outright nonsense claims ...  but it is simply not true.  No amount of hashpower can do that, not "51%" and not "100%"-- the same as any other network rule.

That would be true if users changed their software to allow it, but the same holds for any other network rule.
> It's right there in parentheses. What's that supposed to be doing there? Mock? Confuse? Deceive? Prepare ground for a shitcoin launch? All of the above?

Please help me understand.  Your complaint above, 'replacing the SI standard units mBTC and µBTC with some weird ass double and triple meaning word'  is complaining that the UI dropdown shows the commonly used "(bits)" unit in parentheses?   This seems like a very odd complaint to me. In particular, showing it there seems to me like it could only remove confusion-- since some sites use that rather inexplicable unit without explaining what it means-- not create it.

How could that possibly be related to an altcoin? I am not seeing the connection you're drawing here.

Bitcoooonnnneeeeeeeeecccccccttttt!
oh man, I would have agreed with you... but iota?  that is the centralized cryptocurrency slathered with bogus claims that home rolled a custom _hash_ function and got it totally wrong in exploitable ways and responded terribly when the vulnerabilities were disclosed to them.

I would list them pretty highly on a list of cryptocurrencies that are demonstrating the point that something largely without technical merit can have a price.

> I also own a % of coinlab.com, 

I wonder what Ver's share of the millions coinlab stole from MtGox customers was? /u/memorydealers ?
Why would you spend the good money? Spend crappy fiat first... wait, correction, spend 0 interest credit first. Then pay it with fiat.   Bitcoin is somewhat costly and inconvenient to acquire. 

When planning to sell Bitcoin anyways, sure, spend it. Or for an international payment or a natively digital purchase that is only really reasonable to do with Bitcoin ... But otherwise, I'd want to see a pretty substantial discount.  Especially because Bitcoin payments lack anti-fraud, cash-back, tax simplicity, good accounting reporting, and all the other features of a boring credit card.
Based on the current insolvency status I have turned a substantial profit; though not as substantial of one as if I had just purchased Bitcoin and sat on it. (It's hard to say what I would have done there, probably sat on it.).  I think it was a good trade given when I knew at the time, not every trade is going to knock things out of the park.
> If 3.8M blocks with Schnorr signatures are OK,

Aggregate signatures increase the capacity of blocks without increasing their size.
> seems to be no progress towards scaling the chain

I know you're just under-informed here, but it's hard to not be insulted by a statement like this.  Segwit _doubles_ the practical capacity of the system.  Doubling is a _BIG_ increase.

There are many other projects that are actively being worked on that further increase capacity while also making the system inherently more scalable-- like aggregate signatures;  and other work that potentially reduces the risk of operating at higher levels of load (e.g. preconsensus) or costs of it (transaction compaction).

By contrast "your other side" can't seem to manage doing much of anything more advanced than twiddling trade-off constants with no regard for the costs of doing so.
> You initially led people to believe that the PGP keys were forged, but then when called out, you later added that it was possible that is was not forged because it is possible to override the settings.

There is not a single shred of doubt in my mind: Your "Satoshi Nakamoto" PGP keys are forged.  Any subject matter expert would agree.

All we might disagree on is if your forgery was merely incompetent or profoundly incompetent.

All along I pointed out that they keys settings can be edited, but this in no way makes it more difficult to spot your keys as a forgery.

> I'm saying THANK YOU. 

One does not generally regard the gratitude of a conman as a compliment.
Didn't do the math and don't think they're substantial... some pools that have withheld fees in the past have advertised themselves as "0 fee" PPS because they don't charge fees on the subsidy part... they just keep the tx fees themselves.
This is the same busted "one sided" replay protection that Bitcoin ABC was originally doing and the people there advocated for because it would maximize relative disruption to Bitcoin. :-/

>  Consensus was always intended to be keeping the nodes in consensus. 

BIP9 wasn't a change in that. Not in the slightest.  It's always user and node adoption that makes a rule change usable or safe to use...  BIP 9 is all about the narrow case of coordinating when to start enforcing backwards compatible changes in a way that minimizes disruption.  But the security always comes from the users.   Miners are ephemeral and could be replaced tomorrow or change their minds. 
148 nodes preferentially connect to other 148 nodes, what you're seeing doesn't reflect the rest of the network very well.
The proposal is broken, unfortunately.  I'm really disappointed that other people haven't noticed the problems with it.  I'm still working on it, but it'll need to be a different proposal.

As an extra complication... Unfortunately, with BIP148 and Barrycoin I don't think there is a way to present anything that won't just be seen as a gambit against these moves either.
That isn't true. 

Lets imagine that 99% of miners start signaling segwit on August first, setting the bit as you've described.

Then, a single miner from the 1% hashpower that isn't produces a block that isn't signaling segwit.  The 99% hashpower that followed your instructions will accept that non-segwit block and mine blocks in the chain after it.

But all their blocks will be ignored by users running BIP148 software because they extend an invalid chain.

> Please spread this information!

:(

Lets not play he-said-she-said:  Here is the exact message Barry asked Morcos to pass on (which Morcos did, on May 16th): https://0bin.net/paste/T0xP9qKeBmLiOybE#bw8g7mxCLgO1d2Yp29nayYij3lUxv7AgYjBuxz1ynPB  I don't see anything in it that isn't public already.

As you can see if was a request to endorse "I/we agree to immediately support the activation of Segregated Witness and commit to effectuate a block size increase to 2MB within 12 months", which came with a long list of signers.

Not really much of an invitation to participate in a collaboration.
If it's in secret I don't have to worry about you telling, now do I? :P
Segwit _doubles_ the transaction capacity, this is far more than modest!  It does so while also improving the actual scalability (the ability to take on more load without undue costs), which was critical to getting many people to support it-- without that a split would be absolutely guaranteed. 

As soon as it was put out the bar moved, take that for what you will.

> Most of Bitcoin's competitors hard fork on a regular basis

Those systems are not currently decentralized cryptocurrencies. It's very easy to force changes in a more or less centralized system. Most of them are not honest and upfront about that, though its clear in some systems (such as ethereum where they manually edited the ledger to undo thefts to personnel at the ethereum foundation in conflict with the clearly stated English language contract governing their transactions.)

This lack of functional decenteralization is a massive liability, for example Ripple was forced to include "AML" in the protocol as part of settlement terms for its developers to escape personal criminal charges. This threat is not hypothetical. 

Similarly, there are serious academics that propose that Bitcoin's 21 million limit be eliminated and that the system be made perpetually inflationary. Even BU's "chief scientist" puts out publications that assume as a base assumption that Bitcoin is endlessly inflationary (though he claims he doesn't actually want this, he keeps assuming it in his analysis because it's the only known way to make his unlimited size advocacy even possibly work out logically)...

Upholding the strong properties of Bitcoin in the long run depends on us all being resistant to controversial changes. Several altcoins have made massive economically impacting changes because of excuses... E.g. when solidcoin was being 51% attack they increase the coin supply something like 10 fold in order to give a collection of a dozen trusted parties millions of extra coins in order to back up a POS-ish blocksigning scheme.  While thats a pretty extreme and silly example, it shows that this threat is also not hypothetical:  If changes to Bitcoin can drive it into a state where the vast majority of users feel there is no choice but to change the contract, it'll change. To preserve Bitcoin we need the foresight to avoid driving it into those cases in advance.

> why hasn't Core done this yet

"Core" isn't an organization, it's an open collaboration, and it doesn't have the ability to just change Bitcoin. (And, if it did, it would probably tell the people demanding incompatible changes with threats and harassment to screw off, in any case-- and if it didn't I believe that users would reject the outcome in any case: Bitcoin is valuable in large part because no one has the authority to juts change it in incompatible ways like that).

You've misunderstood: There are two separate concerns around boosting:

(1) There is an _exclusive_ advantage. This is unlike the GPU->FPGA->ASIC change because anyone could have an FPGA done or an ASIC made.

(2) That the covert form of this advantage gums up the protocol and prevents improvement. 

The proposed BIP only seeks to address (2). It intentionally leaves the overt form of the improvement alone.

> You only have issues if there is a 0.13+ node that is not connected to other 0.13+ nodes.

Which won't happen on its own, the automatic connection selection logic makes sure it has connections to witness enabled nodes.
I was always a bit irritated that people glibly repeated stuff about this work and ignored that it assumed endless inflation (the impact he's talking about is directly proportional to inflation to, so it isn't sufficient for inflation to be arbitrarily small), and total indifference to miner centralization.

No I hadn't-- I don't use twitter,  they put "twit" in the name for a reason.

But I've seen them now and I still don't see the connection.  PT is an occasional contributor to Core, his last commit was about a month ago and before that last October. Even his comments explicitly said that a POW fork isn't something that makes sense unless there is a high hashpower attack on the chain.

So you're basically taking a fairly standard and boring comment-- duh people are going to change the POW if there is an attack, because system failure is worse than the downsides of the POW change-- and making it out to be a big deal.  Then you're making it out as if a couple tweets from someone who contributes every couple months is some kind of major move by "Bitcoin Core".

Seems like a lot of noise about nothing.
32MB blocks never actually worked in the original version either...

and it's BU, not the Bitcoin project, that seems to claim to hold the view that understanding hasn't/can't evolve.
The odd thing is that this is written as though it isn't known to everyone arguing about being conservative with the system's resource usage and decentralization/load trade-off.

There are many way to get more capacity when people need and want it-- they make different trade-offs and can achieve properties that make them better than raw Bitcoin alone, including things like instant confirmation.

Bitcoin's big distinguishing features like monetary autonomy can't just be layered on like capacity can be.  If it weren't the case that capacity can be layered on and that the other properties can't-- you wouldn't see people holding a strong line on protecting the network's decentralization.

A lot of this blocksize debate is a lot like people screaming that IP is limited because a packet can't be bigger than 64k (or the 1500 byte ethernet MTU). It doesn't make a lot of sense. 
Moreover, their last clear proposal was BIP109, which caught fire on testnet of it's on volition, without attack, just due to BU screwing it up ... months after it was supposedly done.  Rather than fix it, they just ripped out parts of it, rather than specify the parts they ripped out, they just ripped out the whole thing. Fortunately the network eschewed it instead of activating or we would have had that incident on mainnet. 

So there doesn't even exist a non-abandoned blocksize hardfork proposal technical specification from the large block brigade anymore... not one!
They spy mined a block with the coinbase-height taken from the prior block.
The [change that introduced the issue](https://github.com/BitcoinUnlimited/BitcoinUnlimited/commit/eb7db8b15dce186870568c6b0dc156bdb179710a#diff-4a59b408ad3778278c3aeffa7da33c3cL124) ~~appears to have been committed directly to the BU tree without a pull request, as a result~~ it had little opportunity for public review.

Edit: Statoshi points out that there was a [pull request](https://github.com/BitcoinUnlimited/BitcoinUnlimited/pull/164), though the change that caused this didn't get any discussion there.
This is an outright lie.  (Also not sensible, because blockstream doesn't have any freeking control over it-- and it's also verging on absurd: Segwit increases the blocksize.)

Reported. I'm tired of this shitty behavior. I hope everyone else reports your dishonest ass too.
Basically all of the nerds are on one side here-- this is why 'the competition' is all ~1 developer projects.
Who cares?
Segwit is a major compromise, it doubles the capacity on the network at a time when major indicators suggested decreasing the load was advisable-- it took a heroic effort to include enough risk mitigations to make it palatable to people.  Bitcoin Classic' released after it, proposed no more capacity.
Hasn't been updated in a _long_ time-- I started getting depressed about 'whitepaper' altcoins promoting themselves based on things there... and not even implementing them!  (At least if they implemented them we'd get some idea if they worked.)
> What if everyone upgrades?

Old coins need to remain spendable.

>> Segwit updates the 1MB block size limit to a 4M unit block weight limit,

> 4MB?

No, 4 million weight. Weight is not bytes, it's an abstract unit that better reflects costs to the network somewhat better than a simple size does.

> > Larger blocks will result in higher resource requirements for full nodes, potentially causing users to shut down their nodes, which would result in higher centralisation.

> where is the data to support this? All I see here is your opinion with no data to back it up

That larger blocks require more resources is an objective fact based on the construction of the system, it's not really up for dispute. Regarding the effect, the text states 'potentially' -- I don't know what the author of this was thinking of, but we get basically non-stop complaints to Bitcoin Core from users based on the resource usage of their node (particularly the amount of resources it takes to start one for the first time). These concerns are also supported by [network measurement](http://bitfury.com/content/5-white-papers-research/block-size-1.1.1.pdf).

> indicated Satoshi planned for btc to be able to hard fork when needed, which is could do as a minority and start a 'alt-chain' or it could do as a majopirty and force the previous establishment into being the alt-coin

Pretty rude to put words in someone's mouth, especially someone whom you've never spoken. The nature of Bitcoin is such that once version 0.1 was released, the core design was set in stone for the rest of its lifetime.

>  the two chains could survive in different niches,

There are already plenty of altcoins... but in general money gains its value from network effect, Bitcoin voluntarily splitting up it's network effect would almost certainly be bad news. I think not doing so *unnecessarily* is an obvious unambiguous good (except to those hoping to see competing systems become more competitive). 
BIP 109 tries to work around the quadratic hashing costs* by imposing new limits on the amount of data hashed. BU signals BIP109 support but doesn't implement any of BIP 109's rules (beyond removing the blocksize limit), including that hashing cost change.

Roger Ver's BU pool activated BIP109 on testnet (from the perspective of BU and Classic), then a few months later mined a block incompatible with it. (The invalid transaction was big but otherwise ordinary).

BU's developers' [don't consider this incident of activating 109 then mining a 109 invalid transaction a problem](https://www.reddit.com/r/btc/comments/4zqd7g/roger_ver_does_your_bitcoin_classic_pool_on/d6y7cjv)-- they have a theory of Bitcoin operation where participants don't really validate beyond parsability but follow the most hashpower regardless of where it goes... 

> Follow-up question: was Greg Maxwell the first or only person to discover the fork had occurred on testnet? 

Someone posted on rbtc asking why their testnet client was in safe mode and rejecting the testnet chain. I provided them with some tech support in PM.  This was a month after classic was forked off testnet.

(I don't run classic so I hadn't noticed it-- it seems that the developers of classic don't run it on testnet either...)


* Quadratic hashing costs: Because of a design flaw in how the transaction is processed by the CHECKSIG instructions, validating a single transaction could require hashing gigabytes of data-- because the amount of data hashed is related to the transaction size squared. With larger blocks the amount of data that had to be hashed could grow tremendously. Segwit addresses this by fixing the design flaw to keep the hashing amount linear (e.g. 10k transaction results in ~10k hashed for checksig).  BIP109 doesn't fix the square cost behavior but puts a cap on the total amount of hashing in a transaction/block, limiting the bleeding. I don't have much affection for the way BIP109 does that: it leaves in pointless high costs, the limit doesn't reflect the true costs for suitably optimized software, it potentially invalidates already signed transactions... etc. but _something_ had to be done there and it did something, unfortunate BU signaled BIP109 without actually implementing any of the BIP109 logic.
> a public key is an encrypted private key

No it isn't. The conversion of a private key to an EC public key is a one-way transform (assuming the hardness of a the discrete log problem) just like a hash.  There is no way to 'decrypt' a public key.

Your incorrect and misplaced pedantry also misses the bigger point, regardless whatever terminology games you want to play regulation has treated authentication as distinct from encryption even when the use the exact same underlying techniques.
I doubt their security team is the right group to respond to a question like this--

But I can give a perspective as someone not from coinbase and someone whos never talked to anyone from coinbase on this particular subject:

Banks are big companies that largely make money by having a finger in every pot-- Bitcoin is billed by many as a major future technology in finance.  If so, banks will want to evolve and find their new position in the world of Bitcoin-- which may be fairly different than the services they provide today-- so investments in the Bitcoin sphere can be a way to gain experience in this new market and begin positioning themselves to adapt to it.

Especially considering that coinbase's services act as a bridge to traditional fiat institutions, it's essential that Coinbase have good relationships with the banking industry. To whatever extent that you're rightfully distrustful of banking-- it's a reality you can't really escape when dealing with fiat (especially if convenient use online is a requirement). It's important for Bitcoin's growth and success that some organizations provide that bridge; and we should be thankful for coinbase doing that-- as I'm sure it's a huge pain. :)
By offering the world a sound money free of third party influence.

Whats USD's (the stacks of cloth) online transaction rate?  ZERO.  What is the online transaction rate of gold bars? ZERO.  

It turns out that an asset doesn't need to also be a consumer payment network to be used ubiquitously in the world and be highly valuable. It's _better_ if Bitcoin can cover more uses at once, but at any point if we have to choose between Bitcoin being sound money which is coercion and censorship resistant and Bitcoin being the best at high volume consumer payments, we must choose the former because that is where Bitcoin's original and fundamental value comes from... and so long as it has that there are ways to provide the rest.
That isn't so.  100% of the space increase from segwit is prunable. A full node doesn't need to keep it around once its validated.
Kinda funny that seemingly no one with in production improvements to Bitcoin's scalability was invited to present. :)
To some people cryptocurrency is just a volatile asset to speculate on...
Fun tidbit: The consensus (and networking) part of the change is only 1436 lines.

(The whole PR is larger than consensus because it also includes wallet support and a large collection of automated tests).
By 10% you mean 15%? http://bitcoin.sipa.be/ver9-2k.png
> Segwit was a little more than 2,000 lines of last I checked.

On segnet4 consensus changes are in commits  7c68afbd747ad57391fcb66485c377298fb02a8e to 4dd3d7dd8bf2f9dd7a5e62c3cb2ca8dbd1146daa

Git diffstats says  65 files changed, 1262 insertions(+), 350 deletions(-)

That is pretty good considering that it solves several important problems at the same time, including transaction malleability and the safety of future script upgrades.

> If you are not mining and you are not accepting bitcoin payments of more than a couple thousand dollars every ten minutes, then your BIP109 implementation can quite literally be just changing MAX_BLOCK_SIZE from 1,000,000 to 2,000,000.

You are making this claim because you believe that anyone "not mining and you are not accepting bitcoin payments of more than a couple thousand dollars"  could and should be running a thin client that verifies ~nothing beyond POW. By that criteria, you would equally say for those users not verifying _any_ of the network's rules is OK. I disagree with the premise: In particular, in the case where some substantial chunk of the hashpower decides that it can try to unilaterally force a rule change on the users of the system, anyone not actually enforcing consistent rules with the system is going to find confirmations undone by opportunistic double spends when the ledger splits. This isn't a common event, indeed, but like most other security events one of the main reasons it doesn't happen is because people are protected against it. The absences of attacks when you are secure and they would do little is not an argument to remove security.

Regardless, if you want to say that it doesn't matter what rules people apply because you don't think Bitcoin's security should be enforced by its users then you should be frank about your view, and not mislead people to think a change is simpler than it is.  In particular, by that same "minimum amount of changes if you don't care about enforcing rules" criteria, the size of the segwit change is _zero_.
That doesn't make any sense on a simple factual basis: the signatures for lightning (HTLC) transactions are _smaller_ than the average on the network right now.  To the extent that the signature discount matters at all to that question, it would shift cost slightly towards lightning.

Channelized payments should experience huge fee reductions (potentially hundreds of thousands of times) due to channel reuse. Segwit's impact on fees would be inconsequential for whatever they were there.  The cost computation will make large multisigs relatively cheaper than they are today-- but that makes a lot of sense: multisig doesn't have a cost impact on the UTXO set, so anything that makes UTXO use relatively more costly will also make everything else relatively cheaper, inherently.

Because we generally do not want it-- in my case I've mostly taken an effort to remove publicly visible addresses. I'll gladly provide one when someone wants to tip me after I've helped them one on one with something, but otherwise I'd usually prefer not.

I've been greatly appreciative of whats been giving to me, but all in all it's basically only amounted to a day or so of pay; the sentiment is the real gain from it not the money and there isn't much emotional benefit from anonymous payments of really tiny amounts. Some people become very demanding after they pay you, or [even when they've contributed nothing at all](https://www.reddit.com/r/Bitcoin/comments/3c2cfd/psa_f2pool_is_mining_invalid_blocks/csrua9x); making it easily a net loss.

There are also really unfortunate distorting effects where trivial changes that are easily understood or visible receive huge attention while someone who solved a really tricky research problem, did countless hours of review is invisible, or _prevented_ a major incident, and gets nothing.  A small amount of payment to the visible people will only encourage them slightly while the inequality of it can be greatly discouraging to others who were treated less fairly.

So at best, it's nice but not currently a sustainable way to support work in this space; at worse it discourages people, invites attack.
If 80% of the Bitcoin users supported confiscating the coins of the other 20% (perhaps early users of the system like us) and awarding them to themselves, would you support that?   I know the answer is no.  When it comes to hard forking changes to the system, we should be seeking something close to unanimity-- to do otherwise is to coerce the others who don't wish to go along with it by changing Bitcoin out from under them.

At the same time,  how many of that 80% has actually been exposed to the subject as a question of trade-offs?  "Increased ts/x but with decreased decentralization", "Increased tx/s instead of transaction privacy.", or "Increased tx/s but a loss of a coherent explanation for what will provide security in the future"?   Like many things, a larger size is fantastic when you are mostly thinking about the benefits.

Then the next question is-- how much? The numbers people throw about privately involve blocks of hundreds of megabytes in size or gigabytes in size, and are still not enough to take substantial retail traffic.  Changes small enough to not create massive incentive changes or security impact also don't appear to be large enough to change the set of applications Bitcoin is useful for.   We also saw this before with the prior limits-- when pressure was on people innovated and came up with improvements; when pools upped their setting the progress stopped. It is clear to virtually everyone working on the technology that the full vision of a world powered by a decenteralized Bitcoin needs tools that go beyond what we can just do in a global broadcast network.

Yes, I thought it was a bad idea to avoid recommending press contacts which had extreme views or which were easily criticized-- and that we shouldn't be using the site to promote various people as authorities in general and especially not if they're controversial (regardless of how great they may be at advocating Bitcoin to particular audiences; it's useful to have boring resources that turn no one off).  The association of BCF with another person accused of crimes directly caused its downfall. Ver's felony was cited as the major cause of action in the insane ripple settlement. I think history has strongly supported my position there. 

It's also better to tell users that something is wrong than to silently generate insecure keys (e.g. ones generated from the time, or ones generated by polling a third party webserver).

Y'all should be careful about reporting this stuff in public.  In the joyful outcome of Bitcoin making its way to $10,000/BTC people are going to be turning up your old posts and targeting you for theft... doesn't matter if you've long since spent big chunks of it.

It's great to be excited, but you should also be prudent too.
> Perfect secrecy is possible

But not practical. That approach requires having as much keying material securely established in advance as you'll ever use, and that you securely destroy the key material as you use it.

As a result no one does this, and if they did they'd likely screw up managing the huge keys and end up insecure as a result in any case.

One does not need to _completely_ eliminate private communication in order to _functionally_ eliminate private communication. If they simply limit it so that you could only transmit a few kilobytes a day without standing out then they likely will have achieved their policy goals.  They can also somewhat offset the low success rate at catching people with harsher punishment for the few they do catch.  Perhaps there isn't enough political will right now for public executions of people found using Tor, but it's dangerous to claim that effectively blocking it is impossible in a fundamental sense.
This is ... just totally broken beyond belief.

This voting system design is completely busted: Miners can rig the outcome of the election by filtering payments.

I can't believe the foundation would use such a completely broken and unthoughtful system, even without high expectations on this front (foundation is much better as a advocacy org than a tech one). I am basically beside myself. (You can easily go find a dozen threads on BCT spanning back to 2011 on why this kind of design isn't a way to build a viable election system).

Beyond the fact that the system is no way to run a credible election, it's also broken in more pedestrian ways.. e.g. at the moment The website only lets me vote for Oliver Janssens. (I see the OP is able to get more addresses out of it than I was, but it's broken for them too.)
That code may be too slow for the non-static parts of our test to be very effective, as seems to be doing all operations with affine coordinates. Though it appears to be focused on signing and signing is less critical because it can be validated and (in the case of Bitcoin applications) is validated before transmission (and I suppose the host not the device is doing most of the pubkey generation).

Independent of the testing, the code you linked to appears to be have a very large timing and power sidechannel-- e.g. timing alone should directly read out the hamming weight of the DSA nonce, even worse than OpenSSL. (and seems to have fairly little testing the repository?). I wouldn't advise using it in any place where that potentially mattered.

Blockchain pruning?  You mean the massive redesign in how Bitcoin core stores and validates block data so that it could operate in a pruned data, which also made bitcoin core tens of times faster, that Pieter Wuille designed and implemented two years ago (https://bitcointalk.org/index.php?topic=91954.0) without anyone paying him a cent for it?

Or perhaps you mean the automatic deletion of old block data pull request by rdponticelli (https://github.com/bitcoin/bitcoin/pull/4701) which just slipped out of 0.10 because of a lack of interest in testing in from anyone outside of the core team, in spite of calls for more testing? ( http://www.reddit.com/r/Bitcoin/comments/2krlob/wladimir_tweets_call_for_testing_and_review_help/ )

While I'm happy to see more snazzy tools created; I'm sceptical that more tools is actually something we're in short supply of here... or even more bounties, after all you can bounty work without a particular tool already.
Blockstream is, indeed, a for-profit company. Having been involved deeply in many non-profits (e.g. the Wikimedia Foundation) and for-profits, one thing I can tell you is tell you is that every organization that does anything substantial has a requirement for sources of revenue.  What IRS form you fill out isn't a magical free pass from economic reality. :)

While I support non-profits and for-profits both (my name has been up on the top of the FSF's donor list for a number of years), I think there is a certain honesty in being for-profit (see your own question, it implies non-profit doesn't need revenue :) ). In commercial ventures, you do work which people find valuable and they pay you for it, enabling you to do more work. If you'd like to send us donations though, feel free. :)   (though I think in all our years working on Bitcoin Core pieter and I have only collected a few BTC in donations, I recall Jeff saying similar things...)

In Blockstream's case,  we believe there is a vacuum in the industry (not just Bitcoin, but computing in general) for cryptographically strong trustless technology. It's much easier and faster to build centralized systems, and the skills required to build trustless ones are of limited availability and scattered.  Bitcoin is pretty much the first majorly successful implementation of cypherpunk technology beyond encryption and anonymizers.  We think there is a tremendous business potential in building and supporting infrastructure in this space, some connected to Bitcoin and some not. E.g. by acting as a technology and services provider for other businesses in helping them migrate to a  more Bitcoin-like way of doing business.

Right now our focus is on building out the base infrastructure so that there is actually a place to build the revenue producing business we'd like to have, and then we hope to circulate that back into building more good technology.

It's not just you, I'm an idiot who copied the first link off Google without checking if it was the page I intended to link. I fixed it. :)

(though now that you mention it, I should go fix that other page…)
Wow, Thats a really bad edit that is splicing in an entirely unrelated conversation that has absolutely nothing to do with mtgox.

This is also managing to attribute some that other people wrote to me (e.g. the " i'm really wondering if the client"). It's also missing 3/4 of the explanation.

And now you see why Magical Tux comments on IRC so infrequently these days. Of course, if someone had asked I would have helped them actually competently quote from the discussion, but now I've learned my lesson. 

As the person primarily being quoted in this, please down-vote this post. Don't upvote it.
You could, obviously. Whats the question? :P
Why are you claiming that HFC are "less healthy"? — don't be an unwitting shill for the sugar lobby.

The health concerns about HFCs come from two angles: The health issues of excessive sugar intake (e.g. from caloric drinks) which 'real' sugar doesn't improve, and the particular health risks from high fructose diets which 'real' sugar also doesn't improve, because to be absorbed into the body it's split into fructose and glucose and has similar proportions of each to the HFCs used in soft-drinks.

So sure, it might taste better, or have better mouth feel, but I've never seen any evidence that drinks made with "real" sugar are more healthy.  Both HFC and sugar sweened drinks are bad for you.

The one bonus about these people is that their feelings are almost never hurt by a socially impermissible break on your part.

You're being slightly subtle because being blunt would hurt normal people. With these folks you can say something direct like "I am not interested in talking any more right now and I am going to leave and do other things." and they won't take great offence.  Of course— it's freeking hard to do that because it violates all your social training that you need for dealing with everyone else.


'Alternative medicine' which is proven to work is called _medicine_. 
> 0.14 retained the check for double-spent inputs, but only as a sanity check

You seem to have been confused there.  #443 added a check to prevent dupe input transactions from entering the mempool, it was also redundantly applied to blocks where it was totally pointless and just a waste of time but back then it was by far not the slowest part of block processing, so it didn't matter. Changes in 0.8 changed block processing in a way which inadvertently made the check no longer redundant but not in a way that was immediately obvious. #9049 eliminated the "redundancy" by applying #443's code exclusively to the mempool where it was originally intended to apply, to speed up block propagation. After this point only database consistency checks prevented accepting inflation. 0.15 changed block processing further, to reduce an unrelated DOS attack that existed in various forms since day 1, but while doing so relaxed some sanity checking that allowed the corrupt state to cleanly stop instead of proceeding incorrectly. After this, under some conditions the system could proceed with invalid state.

> (or someone submitting transactions through the miner)

This is incorrect.  The software will not mine these transactions unless specifically modified to do so, the miner has to be a party the attack. In doing so, they'll produce an invalid block that most of the network hashrate will now reject.

>  (and still could try to) 

It's unclear to me what you mean by this.  Anyone can try anything at any time, but that isn't usually worth mention. 

> performed a double spending check for outputs created within a block, but not for outputs created outside of it

This is specific to the case where a single transaction uses an input multiple times.  This is distinct from "double spending" as handled elsewhere.
... why is an article needed to say this?    What next? "Water Wet, scientists say"
> The downside 

There is an extra limitation: it's only possible when what I'm calling the taproot assumption holds:   That there exists some set of pubkeys where their signatures without any other condition is sufficient to authorize the transaction.

You can come up with situations where that isn't the case, though usually they're kind of silly.
> This. I wonder why me and Amir Taaki were the only developers who recognized it when it became obvious, or were they just politically correct by not signalling it here: https://en.bitcoin.it/wiki/Segwit_support ? And are other developers starting to understand some miners will block EVERY progress in the future? Schnorr, Confidential Transactions, Bolt, MAST... Stop working on them or grow some balls.

Before you adopt such an insulting and disrespectful tone it would be prudent to be awfully sure that you haven't been bamboozled by rbtc's reality rewriting-- keeping in mind that the polar opposite of a distortion is also usually a distortion.  You appear to be confused here, pretty badly, and it does you no service. In my opinion you should be embarrassed and ashamed for insulting all the developers who have worked so hard to maintain Bitcoin all these years.

At no point has any softfork _EVER_ been up for miners to decide. Any claim otherwise is just an rbtc reality rewrite.  The activation for softforks works like it does because a supermajority hashpower is a necessary and sufficient criteria for activation to be maximally disruptionless.  It will continue to work like it always has for future softforks as well.

Only a criminally negligent party would subject Bitcoin's users to a guaranteed disruptive network split when a disruptionless deployment is possible. No one should have the right to put the funds of uninvolved parties at an unnecessary risk.  The only developers who would go along with that are the kind of maniacs that run recklessly operated centralized altcoins-- not the sort of people you want working on Bitcoin without adult supervision.

What you are missing is that if a feature is very widely supported by users but a totally disruptionless activiation isn't possible then there are subsequent steps to take to activate in any case, at all times keeping the disruption to the minimum necessary.  This was always the case and the plan as stated in public 1001 times... It is not possible to know if a feature is widely support until after it has already been deployed, which mandates a two phase deployment. Rbtc wanted people to believe that segwit wouldn't activate unless miners approved it, and you've fallen for it.

Dunno if you noticed, I gave you what I think is a pretty informative and complete response.  I didn't get insulted and fail to do that; I let you know that kind of response is potentially insulting because it will get in the way of learning (not just here but elsewhere).  It might have been kind to give me a little credit for that. ... I didn't have to respond at all, you know.

I'm sure you're aware that the people you talk to online.. are people too!  If you try to be a bit more charitable with your statements-- even if you think nothing is being done you can _ask_ rather than state it as fact-- then everyone can learn more and communicate better.

> It keeps going up and up and up. It won't be useful as money if it continues on this path.

And if Bitcoin doesn't have substantial fee income it will become insecure and useless as a result too.  These things do not exist in a vacuum.  Bitcoin transaction fees arise out of market competition among users, no one is setting them.  If they're high it's because transactions are valuable enough to people to pay those prices. We should celebrate that-- not argue that Bitcoin transactions have become so valuable that they aren't valuable somehow.   Have you read https://www.reddit.com/r/Bitcoin/comments/438hx0/a_trip_to_the_moon_requires_a_rocket_with/ ?  I want Bitcoin to be useful for all applications, but really achieving that requires a holistic view that accommodates all those uses without compromising what made Bitcoin valuable in the first place.
They stopped saying 'longest' now they are saying cumulative difficulty, so you need to change how your protocol counts work.
> They are spending hundreds of thousands, is there proof?

Over $500k/month is what Charlie Shrem reported ver told him, it's not wildly inconsistent with Ver's [staff page](http://archive.is/oPSwM) and the disclosed [funding of BU](http://archive.is/qZyh5)
This cannot change the key type, and it writes in new timestamps.
> Given that it's the same except for 1MB block limit being 2MB,

You mean 2MB being 4MB.  ... see, you don't even know what 2x is even though you're promoting it here.  Don't you see why that horrifies many people?
s2x breaks the "proven formula" by not having replay protection.
Pretty ungraceful reply by Barry.   And some people accuse some developers of being low EQ. :)

"Noted" would have been an improvement.
> What do you say to the argument that bitcoin mining relies on the centralized FIBRE network, which is only necessary because of large block sizes, and therefore bitcoin's decentralization is already damaged by large block sizes.

That it's an outdated argument that neglects the many times improvement in block propagation made in Bitcoin Core through the work of Alex Morcos, Suhas Daftuar, Matt Corallo, and Pieter Wuill  which -- in spite of Luke's concern he hasn't contributed to at all.

It's also confused because FIBRE is a protocol that anyone can run, which is run by multiple multiple parties. It isn't centralized at all.
"VALUABLE BITCOIN MINING EQUIPMENT STORED INSIDE THIS BUILDING"
As a matter of process major versions don't activate softforks in any case.
rbtc says crap like that because they (and the BU people) misunderstand how Bitcoin works.  They think whatever a majority of miners says goes.  This isn't how any version of Bitcoin ever released has worked.

But if it did work that way then miners could steal segwit coins.

They could also steal _every_ other kind of coin.

But in the real bitcoin this doesn't work because a block that steals coins just isn't a block-- every node will just ignore it as if it didn't exist.

In the past month there have been at least _two_ blocks that were ignored because they took coins they should have taken (in these cases where the coins didn't exist)...  the network ignored them, painlessly and invisibly to the point that the only people who noticed they even happened were engineers who work in the underbelly of the network like myself.
https://luke.dashjr.org/programs/kycpoll/answers.php
Because it isn't them that gets screwed when their errors cause faults; it's innocent bystanders.

They'll go "oh well we learned" and move on, even be applauded as heros somehow -- just as we saw with classic and unlimited's numerous crash events.

But if there is a large reorg caused by this issue you might have a major exchange end up insolvent.  Protecting innocent third parties from losses takes strict priority.

This only goes so far... it's not like I'm spending time going and reviewing their code. But if I come across a preventable problem that would hurt third parties, I'll do what I can... at least so long as I'm still using Bitcoin myself!
> and just merge SegWit2x 

I have no ability to do so.

If Bitcoin fails by handing control over the private parties like this, I'll create something new or work on something else... throwing good efforts down what I believe to be a dead end would be a my waste of time. And that is a choice I can freely make.
OP_RETURN has nothing to do with smart contracts. It's an inefficient way to add a hash to a transaction. Before the release you're talking about they were not permitted in standard transactions at all. And you're going on about "Luke-jr" but the author of that PR is your Lord and Savior Jeff Garzik.

PS. $ git grep MAX_OP_RETURN_RELAY script/standard.h:static const unsigned int MAX_OP_RETURN_RELAY = 83; //!< bytes (+1 for OP_RETURN, +2 for the pushdata opcodes)

The limit is 80. So, was there even a single part of your post that wasn't wrong? It doesn't look like it.

If you spend almost all your time pumping altcoins your rbtc false flags will be embarrassing.

I am not being sarcastic in either.

I believe that it's likely BIP-91 will activate segwit and both "Segwit2x" and BIP-148 advocates will declare victory. If that doesn't happen there will be an enormous mess. If it does happen there will be an enormous mess, with segwit2x people claiming a mandate for their hair brained unsound and unethical hardfork.


Show us a Bitcoin script using altstack that can't be rewritten without it; or please apologize to the non-technical participants in this thread for doing _exactly_ what the OP is complaining about.
If you note, almost none of the people being involved in these efforts are blockchain protocol engineers.  They lack the right kind of understanding and imagination.

Unfortunately, they think they're getting advice from people who know what they're doing-- who them tell them "It's just changing a constant, no big deal.". But they do not know how to judge qualifications and people have untruthfully traded on the work of others to build up their reputations...
Sergio wrote a reasonable and relevant post the other day: https://lists.linuxfoundation.org/pipermail/bitcoin-segwit2x/2017-June/000010.html

> There are two group of people which have two different visions for Bitcoin.

> None of these visions is "wrong".

> One group values more things like decentralization, lack of government, censorship resistance, anonymity. This group thinks that Bitcoin will transform our world in 20-30 years. To reach this goal, it's of utter importance to stick to those values. There is no rush.

> The other group values more things like reaching one billion users in the next 5 years, or serving real unbanked users today, even if that requires a political agreement now.

Your are right and yet it's hopeless.  People have been pointing this out for years but you cannot get many people past the most superficial of understandings.

Those that care to understand have already moved on, so you're left debating with people who will never be convinced.
These bullshit lies have already been debunked on this subreddit. Why is that thread hidden now?

The patents listed there are largely just a dump of a random keyword search. They have no relationship with segwit.  (In fact, the author of those posts is so technically ignorant, e.g. with the comments about EdDSA it's clear that they have no idea how segwit works _AT ALL_).

Moreover, Blockstream has no patent applications/provisionals related to segwit. (And a year has passed since the publication of the segwit spec, so we couldn't apply for any now.)
FWIW, Bitcoin is written in C++ not C, and the distinction is important here.

With appropriate styleistic constraints competent C++ programmers make memory safety bugs much less often than in C.  I believe we've never had a production impacting memory safety bug in Bitcoin Core.

Most of these BU trashfires would have existed just as equally in rust. (Not to say that rust isn't interesting-- it is, though it massively reduces the pool of developers and the size of the tools ecosystem; and it's still unclear if rust would survive Mozilla no longer funding its development)
I'm surprised that many "BU nodes" were actually running BU.
I think the name should be changed back to the original name-- Bitcoin; but I'd probably be alone on that.  The original goal was to avoid confusion with other Bitcoin software-- but it has hardly worked, e.g. "Bitcore" is constantly confused. Whatever it's called, so long as its successful someone is going to intentionally name something similar to it.
You're simply incorrect.  Segwit increases the blocksize limit-- if you insist on being be pedantic, it in fact eliminates the block size limit entirely, and replaces it with a block weight limit-- weight better models the long term carrying cost of a block, and allows blocks to be considerably larger in size (and more importantly: considerably larger in terms of actual capacity for transactions!).
 
Good catch on a potential incompatibility.

On a block with no segwit active there should be no witnesses. But since the values aren't currently checked for blocks without segwit active it would be possible for there to be an incompatibility there.

>  Is it assumed that every miner should run a segwit-enabled gateway node that checks incoming blocks against this soft fork?

Absolutely not.

This will need a bit of a modification. Thank you for the critical eye.
Then surely they will support this proposal to inhibit only the protocol disrupting covert form in a very simple way.  And they will also grant a world wide free license to their patent.

Right?

Strange they ignored my email two weeks offering to personally pay for and freely license the asicboost patent. :(
> If the only miners that are blocking SegWit are those that are also benefiting from ASICBOOST, wouldn't it be just as difficult to get this new BIP passed?

This proposes a height based flagday (like BIP16).  It doesn't trigger on anything having to do with miners.

> Would it be easier to first make ASICBOOST available

I have been trying this. But it turns out that boosting is quite valuable (== high price) when used exclusively, and not valuable if made generally available (== not many willing to pay to support it). 
The tech community met the 2MB demand-- but in a way that radically reduced the risk by improving the scalablity and increasing compatibility-- then managed to get a enormous number of people to support it, even people who were unconditionally opposed to cranking the blocksize.  Pulling it of was an impressive feat, technically and political. Then, WHAM, the goal posts moved.

Now they want unbounded size, no scaling improvements, a radical change the the consensus process. Yea,  I have no interest in using that currency, and a lot of other people don't either.

A day ago one of the luminaries of rbtc told me to kill myself. I guess you're now expecting a 'compromise' there too? :P
Some of the comments people have made have left me wondering if they've ever had a job at all... and feeling sorry for them if they have, since it must be a terrible place to work-- where, e.g. your employer controls your life even outside work.

Blockstream has about 1.5 equivalent full time staff that work on Bitcoin Core; some of this is dedicated time, some of it is just where there is overlap with work we're doing something ourselves and would be doing so even if Bitcoin didn't exist, and it's massively better to do it upstream where we get public collaboration and review.
unfortunately the underlying post is boring, or I'd be really tempted to screenshot this post and post a thread about it, just to amplify the recursion. Next time make a screenshot of a tweet of a more interesting Reddit post!
I don't think it's fair or accurate to paint a country of 1.3 billion people with a single brush.

I am completely sure that to whatever extent personal perspectives play into the politics here, they're ones that are specific to the people involved-- and not the ones you'd expect from national or racial stereotypes.

... Malleability can't be "fixed", because it's a useful feature: Sometimes you want to make a malleable transaction, like when you want other people to be able to add their own inputs to it.

But it can be made possible for users to opt out out of the forms of malleability they don't want and to make malleability irrelevant for their transactions if they choose.  This is exactly what segwit does: It lets users choose to make themselves immune to malleability.

Yes, users can choose to not take advantage of this ability-- just like they could choose to post their private keys on the Internet.  Bitcoin being robust and secure doesn't mean that it's impossible for you to shoot yourself in the foot, it means that is possible to NOT shoot yourself in the foot.

As it stands anyone can continue to disrupt users that care about their transactions being malleated (anyone who ever must make two transactions within a one block interval, is at least somewhat impacted) and there is nothing those users can do about it short of demanding segwit's activation.

'Release Candidate'.

This is a proposed release which has been put out for adventurous users to test in order to catch any major issues which were not found by developers (e.g. issues specific to particular systems or usages) before the full release is out.

Bravo.
hopefully no one running BU or a lite client connected to BU nodes was counting on that block as a confirmation, if you got double spent due to it you wouldn't be cheering "anti-fragile".
> The fact of the matter is, that segwit doesn't allow more people to transact,  it only allows the same number of people to transact, but with bigger transactions. When big blockers say they want bigger blocks, they say that because they want more people to be able to transact without the fee rising. Segwit simply doesn't allow for that. The only people who will have their fees decreased are those who use multisig.

That is an outright untruth.

Please feel free to show me any block on mainnet with 8885 transactions like this [testnet block](https://testnet.smartbit.com.au/block/0000000000000896420b918a83d05d028ad7d61aaab6d782f580f2d98984a392), not a single one of its transactions are multisig.

Segwit has the same effect on capacity as a ~2MB blocksize with the _current_ composition of transactions; yes-- it's also equivalent to even more capacity than that if multisig is more common.  The fact that it gives even more capacity in some cases is being used by people to lie to you about the effects.

> Schnorr multi sigs are the same size as single sig witnesses

Nope, not if accountability (the ability to tell which N of M keys authorized the transaction) is preserved which practically everyone wants. (You also ignore that segwit's script versioning makes it easier and safer to deploy other improvements like schnorr.)
That isn't news-- they've had it there all along, added as part of a series of changes that didn't have peer review AFAICT. I complained at them because they were continuing it... rather than it just being a single lone developers stupidity.

I think it speaks to the quality of the work they do, but it's still uninteresting because BU is uninteresting.
Yes, some folks that contribute to core have been 1:1 with miners that wanted help.  People would be glad to help more if asked.

A miner has to do more work to upgrade than just change their Bitcoin Core version:  They have to update their mining software, some versions -- like LukeJr's eloipool-- don't even have updates for segwit out yet.

Also not all miners are on 0.13.0 yet.  0.13.x requires that you either use a compiler that supports the C++11 standard or use the published static binaries.  There is at least one miner who has been running on an old operating system which doesn't come with a C++11 compiler-- so to upgrade they'll either need to upgrade OS, build on another system, or use the static binaries.


Chances of that are zero. I'm interested in knowing how you thought that was going to happen?  Elements is just a tech demo, a federated testnet sidechain with a number of interesting enhancements that didn't take long to develop.

(The fact that it surpassed pretty much all altcoins ever made up to the point it was created in terms of interesting features added beyond bitcoin is much more of a statement about altcoins than anything big about elements itself)
> Both issues were on testnet

There was no issue in the segwit case-- just a developer that ran old software by mistake, and gave a false positive in chat -- because Bitcoin developers actually test their software and publicly report what they find... and *testnet wasn't involved at all*: there wasn't any segwit on testnet until months later. A disposable R&D network was in use, specifically to avoid disrupting testnet as Ver did.

> which is exactly what it's for

No it's not-- it's for testing downstream software. It tests implementations only as a last resort.

Moreover, BIP109 (and the software ver was running) were released to production long before anyone even began testing it on testnet. Had Bitcoin Classic's timeline been followed the faulty software would have been running on mainnet long before.

Finally, the issue was ultimately addressed by Bitcoin Classic altering their consensus rules to match BU, by ripping out the limited sighashing protections; leaving them with no answer to quadratic verification costs.
Your understanding is incorrect.  There is really no better hardfork construction of segwit.

In elements alpha we had an earlier segwit design which was a hardfork and it was much worse (and in the next elements network as well as liquid we've switching to a construction based on the Bitcoin segwit-- even though it's a new network, it's simply better). 

The claim that some hardfork version would be better is simply misinformation spread by people who've never worked on it, tried it out, or thought through all the implications.  (E.g. if you make an incompatible change the transaction format of the running network, you'll end up confiscating people's Bitcoins where they have them locked up with nlocktimed transactions.)
That is the case, but it's old hat (e.g. circa 2011/2012 revelation) to those of us working on the protocol.

For more impressive results from this thinking, see https://www.reddit.com/r/Bitcoin/comments/47rk85/first_successful_zeroknowledge_contingent_payment/

MAST is an old idea, but deployment challenges have kept its priority lower. Segwit vastly improves things. 
Bloom filters in Bitcoin, unfortunately, effectively provide no privacy.

https://eprint.iacr.org/2014/763.pdf

http://www.syssec.ethz.ch/content/dam/ethz/special-interest/infk/inst-infsec/system-security-group-dam/research/publications/pub2014/acsac_gervais_slides.pdf

http://jonasnick.github.io/blog/2015/02/12/privacy-in-bitcoinj/
At least he didn't say that I'll rue the day.
This is a statement I've made _many_ times.

E.g. Aug 2012:

15:56 < gmaxwell> hrumph: distributed blockchain is a terribly inefficient mechenism with a lot of compromises...

Aug 2013:

13:10 <@gmaxwell> DJohnston: As maaku has been noting recently— you do not have to stuff data into the blockchain to operate with the blockchain ecosystem. The blockchain is a global broadcast medium, it is the most inefficient method of storage (especially) and communication ever invented. All participants in the consensus system must receive and process this data.

Mar 2014:

5:11 < gmaxwell> A blockchain is a really inefficient system, it's attractive only because of the possibility of trustlessness. If you're going to trust things, there are better designs possible.

Nov 2015:

22:42 <@gmaxwell> Yea, blockchains are about the least efficient mechanism of agreement/communications/recordkeeping ever imagined.

Any many other developers point it out too:

18:38 < andytoshi> Kamuela: the blockchain is very public and very inefficient in terms of space, bandwidth and CPU time

You might not hear it that much because the less technically sophisticated parties who are seemingly desperate to pump Bitcoin's price at any cost-- including many of the louder voices advancing a removal of blocksize limits-- aggressively attack anyone who expressed any view on the challenges or limitations Bitcoin faces.

It's a shame that many in /r/buttcoin align themselves with these people for the sake of the "lulz"-- undermining what could otherwise be a socially productive criticism function and replacing it with a circus of attacks by the unethical and emotionally stunted.
LN isn't a financial institution, it's a network of Bitcoin nodes, peer to peer!

Though use of a financial institution is another possible way to use Bitcoin-- with its own limitations and benefits. 
It's in there! But it isn't fixed date currently because it's not our choice. A lot of the material in the roadmap is effectively preparatory work for ensuring proving the safety of the change in order to get the support for it.

One of the other things in the roadmap is constant preparation for it, so that the basic tech preparation in core itself isn't the limiting factor.
A status indicator is being worked on; one was written a month ago but hasn't been merged yet. I consider it a release blocker.

As far as sending goes; Bitcoin Core's wallet currently has no facility to opt-into replacablity (no reason to, as it doesn't currently have any features to make use of it.)
Well, it might be interesting to pay people to cry wolf; not only is the journalism fail hilarious, but after the 42nd repetition people will have grown tired of it, anyone actually finding something will be seen as a crackpot, and no one reputable will dare publish it. 

In a world where it's impossible to hide completely, chaff can be helpful.
That's not quite correct. Existing full nodes already do not validate signatures in the far past-- with SW they could also skip transferring them.  It also reduces the amount of data sent to lite clients (making higher privacy lite clients less resource intensive).

And the fraud proof benefit can give lite clients, with lite bandwidth consumption, full node like security under the strong assumption that they aren't partitioned from the honest network.

Plus, the additional capacity can't be used to flood the UTXO set.

But as you note: On the tip of the chain for miners and full nodes, nothing is changed by segregated witness; though they're improved by other things in the capacity plan I laid out.
Good:  With bigger blocks the size of the return proofs is much less important; which is good, as thats the biggest scaling challenge with sidechains.

An ideal (ignoring the viability of it, in the larger context of Bitcoin) block-size model for the two-way peg mechanism itself is likely an unlimited maximum block size but having 'cost' (and thus transaction fees) going up quadratically (or similar) with the amount of data in the block;  because that kind of structure would avoid the risk races on return fraud proofs and would allow the settlement time be greatly reduced. -- though that kind of model is likely not viable for other reasons,

Bad: If larger blocks (or, especially, a larger UTXO set) undermine the decentralization of Bitcoin this could be quite bad-- after all, the goal of sidechains is to be able to add and experiment with sophisticated technical features while enjoying the network effect of the Bitcoin currency; debasing the currency by undermining the decentralization that makes it valuable in the first place would reduce that advantage. 

Similar to other overlay systems (e.g. micropayment hubs) sidechains cannot be more decentralized than the decentralization of Bitcoin, Bitcoin's health sets an absolute upper limit.  Sidechains themselves aren't a scalability mechanism; there may be some small scalability gain by allowing different users to simultaneously choose different amounts of  decentralization (but never more than Bitcoin's); or from being able to test out complex scaling technology like fraud proofs (which are both too risky to try for the first time on the production network; but too complex to design in a vacuum);  but it's largely orthogonal except if unwise changes degrade the viability of Bitcoin then anything using it is harmed too.

There are five people with commit access, and who have also been long-term contributors, who do a lot of the review work, etc:

Pieter, Gavin, Wladimir, Jeff, and myself.

Pieter and I are founders of Blockstream, and it pays for us to work full-time on Bitcoin stuff since late last year. Gavin has been paid for a few years by BCF, Wladimir was paid by BCF since last ~April.  As you note, Jeff Garzik has worked for Bitpay.

There are hundreds of additional people who have contributed, with a rapidly falling of power law distribution;  of them one of the most prolific of those includes Cory Fields who was paid by BCF during a similar timespan as Wladimir.  In terms of other people that I know of that are paid to work on Bitcoin stuff-- Recently, Alex Morcos and Suhas Daftuar have been noticeably active, and they're chaincode labs.  Blockstream also employs a number of other people who contribute (Matt Corallo, Jorge Timón, Mark Friedenbach, Patrick Strateman)... but lots and lots of people are not contributing with any particular sponsorship.





Bloom filters really don't help that much (e.g. http://eprint.iacr.org/2014/763.pdf) even used optimally. To the extent that they do provide privacy the resulting privacy is very fragile and can be broken by future filter-sets or additional information (like constraints from change, taint analysis, or timing correlation).

The best thing to do would be to use PIR (private information retrieval) to receive the data.  No one has implemented any complex 'efficient' PIR schemes for retrieving wallet data as far as I know, but there is a simple scheme-- download all the data, which is used by Bitcoin Core. This also strongly protects against some other attacks which are harder to defend against in a SPV model.

The history of a near complete (though not quite complete) absense of innovatation in the altcoin space suggests that that incentive doesn't work in any case.   The inventive isn't to develope (much less maintain) useful things, the incentive is to create hype to create a speculative bubble and sell things off to greater fools.

And to whatever extent it works at all, there is huge risk of exploiting ignorant people, and it seems very unsustaniable long term:  Do you really think that the public can seriously adopt cryptocurrencies if every new feature requires a reboot and a reshuffling of ownership?

There is no lack of incentives to build _useful_ tools for people to use in this space, there is a lack of ability to deploy them with a pratical amount of effort.
A twitter account would guarentee that I am not "legit", seeing as how I don't use twitter.  

If you're looking for confirmation, ask me for a PGP signature or something.
Is it quantum? The published evidence suggests "probably", is it a quantum computer? Absolutely not.  It is an adiabatic quantum annealer. It can compute things, similar to how an adding machine can "calculate" but isn't a computer.  The computing it does, assuming that all the claims about it are true, is strictly less powerful than an actual quantum computer would be.

As a Bitcoin-QT core developer, foundation member, and Bitcoin enthusiast I am strongly opposed to Trace Mayer for the foundation seat at this time.

In the past he has treated me in a manner which I found to be disrespectful and hostile, which is saying a lot considering that I can handle reading Bitcoin talk.

Contact from him which was, in my opinion, threatening made seriously consider discontinuing my involvement with Bitcoin.

In light of this, I do not believe that his approach or interpersonal skills are suited for the role.

They run it at such insanely low bitrates (for commercial reasons) that I don't expect any codec could make it sound _good_. 
Can you point to _one_ credible source even alleging this about flac?  Just one, surely if there is such broad consensus you can point to one expert making this claim.  And, of course, many deep pocketed entities do distribute these things today.

The techniques used in FLAC are not new, and any applicable patents would have expired by now. Moreover, ALAC is broadly based on the same techniques.



30dBm into a gain antenna? Assuming your directtv dish setup isn't a fancy dummy load due to bad engineering this is a _clear_ violation of Part 15.245.

Not to mention that merely changing out the antenna and using an external amplifiers are violations of 15.204 though I haven't personally seen any enforcement on that point.

It's also bad engineering— you're probably splattering all over the airwaves.  There is simply no reason to use EIRPs so high on earth, you're going to be line of site and multipath limited not signal strength limited.  Moreover,  all the tx gain in the world doesn't do you any good if you can't HEAR the other side.  Since you've got the more directional antenna you shouldn't need any more tx power than the other side has... maybe a couple of dB to get over the local noise floor but not more than that.

I can't be bothered to look up the penalties, but on a first incident the FCC usually does something like an administrative forfeiture where you lose your equipment and get a $10k fine. Fortunately for you, and unfortunately for everyone that has to share the airwaves with idiots like you enforcement in the ISM bands is pretty darn rare.

The lawfulness of using someone elses access point without authorization is a highly local thing. Without more information no one could give you useful advice.  I'd generally expect that the more effort you go through to make it happen will not reflect positively on you if law enforcement does taken an interest. Right or wrong the expenditure of fancy effort looks like criminal intent to most cops. 
The procedure is at: https://github.com/bitcoin/bitcoin/blob/master/doc/release-process.md

Assumevalid isn't a checkpoint in any way-- it doesn't force consensus and is entirely mechanical and non-subjective, so there is no reason to set it 100 blocks back and this is reflected in the process. (and if it were a checkpoint it would be utterly unacceptable to set it only 100 blocks back...)

There is absolutely *no* security implication for the default assumevalid being set immediately at the tip. For security the only requirement is that it be valid, which is accomplished exclusively by [review](https://github.com/bitcoin/bitcoin/pull/15429).  It's very easy for you to perform the review yourself: On a node of yours without that update, run "getblock 0000000000000000000f1c54590ee18d15ec70e68c8cd4cfbadb1b4f11697eee" if the result shows a block in your chain then the value is correct.

If the block gets reorged out then the assumevalid simply doesn't have any effect-- sync becomes somewhat slower, but there is no change other than that. Avoiding the block being reorged out is accomplished by the day it took to merge the change-- if it got reorged it simply would have been updated in the PR and re-reviewed, but if it had been reorged out after being merged it wouldn't have caused any harm.

Moreover, whatever the value is set to [it only has an effect on blocks buried under at least two weeks of headers](https://github.com/gmaxwell/bitcoin/blob/a083f75ba79d465f15fddba7b00ca02e31bb3d40/src/validation.cpp#L1851).  The reason for this behaviour is to make it harder for miners to mine an invalid block and then trick users into accepting it by announcing they need to twiddle with that setting, but it still applies to the configured value for largely incidental reasons.

Cheers,
We were actually discussing removing BIP70 support (in public) before bitpay announced they were using it-- it's virtually unused, provides little value as is (even bitpay's claimed reason for using it doesn't really work), leaves us with a lingering OpenSSL dependency we'd rather eliminate, and has frequently been a source of vulnerabilities (esp via OpenSSL).

Now that Bitpay requires it it annoyingly would be seen as an attack on Bitpay to remove it, which wouldn't be the intent. 
uh because they're currently computationally impractical and a hundred kilobytes in size?
Yes.  One range proof that covers all outputs and it is only a little bigger than a proof for a single output.
Not just talked about.  Bitcoin has smart contracts and has since day one, and they've been applied to many useful applications--  just not so much hype!
Down with ICO scams.  This isn't innovation; it's just another stock issuance backed by a bunch of handwavy hope pumped out to less sophisticated investors who don't know better. If there were any technology built, you'd have an argument.
Bitcoin ABC was less reckless than B2X but started off with a number of reckless moves--  same P2P port, same network magic, same addresses...

BCash supporters argued for these things (along with no replay protection) specifically because they argued that it would be bad for the competing chain.  What they learned after was that these things were bad for them, and they've been working on fixing them.

I expect the same would hold for B2X, if it doesn't get abandoned quickly, they'll fix these things.
Yep.  A small increase has a fairly large effect.   Which is why doubling again is crazy.
It looks like a consensus rule to me, the description and comments are just incorrect.  As typical for 2xc stuff there is absolutely no specification at all, so you have to perform the failure prone reverse engineering of the behavior from the source code.
A friend of a friend even? 

Regardless, you're an accomplice in his fraud then...

If you claim otherwise, why not reveal to us who you are?  Someone you "trust" once said "Anonymity is the shield of cowards, it is the cover used to defend their lies.".
There were before 0.15's release even. 

And this is even with S2X having push-button-sybiling with "trusted" pre-synchronized amazon images that you just need to click and pay to start.

Edit: I missed the 10x, there weren't 10x at that point. :)
> that segwit could have been activated months ago, 

Almost a year ago, in fact.
What segwit introduces is the choice to do that or not on an input by input basis. All the traditional stuff still works-- and couldn't not work without confiscating people's coins.
You misunderstand the problem.  Malleability isn't an issue of what _your_ software refers to, any competently written software already deals with it's own references just fine (e.g. by tracking txouts).

Malleability is an issue because of how transactions refer to each other.  E.g. when you spend the change from transaction A in transaction B the protocol uses A's transaction ID to reference it.  If A gets malleated into A' then transaction B becomes invalid.

Segwit fixes this by letting users choose to leave their signatures out of their transaction's TXid.   There is a new WTXID that is introduced and used in the other places where an ID for the whole transaction is needed.   This way if the signature is changed in any way, the transaction ID stays the same, so subsequent spends aren't invalidated.

There appears to be no way to fix the malleation problem completely (including the case where the other signer in a 2of2 multisig re-signs) which isn't substantially similar to what segwit does to solve this (segregates signature data out of the ID used to verify later spends).

> u/deadalnix (and others): Don't be fooled! The word "plagerized" is not to be confused with the similar-sounding world "plagiarized." The latter means to pass on someone else's work as one's own, while the former is a term invented by Greg Maxwell which means to succeed at accomplishing something important that also made /u/nullc envious because he didn't do it himself. 

So-- for clarity sake-- you are defending a member of your organization who took hundreds of lines of novel cryptographic code and explanation written by other people, completely removed their names, and affixed his own -- in violation of the licensing on the work, copyright law, and common decency and then refused to correct it?

-- instead you appear to be responding by mocking me for misspelling a word, and claiming that he did not pass off someone elses work as his own -- though he transparently did as the comparison screenshots show.   Does this reflect the norms and standards of the Bitcoin Unlimited project, where you are "Chief Scientist", or the Ledger journal where you are the managing editor? If so, I suppose that would explain how you got away with publishing systems based on my preconsensus proposals without attribution.

Next I expect to see you distributing deadalnix's misattributed copy of the code Pieter wrote on BU's github too...
Many miners were producing 200k blocks last night.  Esp with segwit active... you can't assume that.  Moreover, it looks like the btc1 code just won't produce a bigger block right now, which would explain why no one has managed it in 24 hours.
lol.  It doesn't and couldn't... beyond it being unpatentable, I first described it in public many years ago.
The paper makes no such clam, it's a paper about incentives to relay transactions.

The average distance between Bitcoin nodes is almost certainly not 1.3 hops (nodes would need many more connections to create an average distance of 1.3 hops).

Anything with more than 0 hops is potentially vulnerable to sybil attacks.

Lightning is not generally vulnerable to sybil attacks because participants prove that channels belong to them by being able to sign with their keys.  In other words, if you try to route via someone and they screw up, you just blacklist their channel and try again via another path a few milliseconds later-- and they can't pretend to be a great many parties because they have to have a channel with funds in it for you to even try to consider routing via them. More over, their neighbors in the path see their misbehavior and can ban them too.

> A BIP148 option would be the right follow up

Releases have a weeks to months long pipeline.  The Bitcoin project doesn't take reckless moves for PR purposes.

That btc1 codebase doesn't "tolerate" BIP148 any better than Bitcoin Core does, you're getting duped by hype on reddit.
> I like the part where lukejr gets to decide who has edit access to that page.

That is rubbish. There is no special access control in that wiki.  The offer at the top is for luke to unwedge people who have issues getting their accounts past the anti-spam.
Then they should be specific and not make outright untrue claims. Expensive,-- a fair claim perhaps, though it's orders of magnitude less expensive than the cost of an international wire transfer-- but unusable? No.

"segwit2x" itself would do little to nothing to impact the viability of things like "high frequency banking for the worlds poor". --

"as made business models that require low value blockspace unprofitable" These were never viable and cannot be.  Every piece of data is replicated on to all the nodes in the system this inherently makes it one of the most expensive storage systems ever conceived by man. Moreover, the people providing this space are not directly compensated; the cost is an externality, and the system's security depends on decentralization...
Because the primary goal of the Bitcoin system is to be consistent, diversity tends to increase bugs rather than decrease them.

Even absent the consistency concerns dilution of effort significantly increases the defect rate. More participants can lower the defect rate in a super-linear manner.

BU's issues are sort of "by definition"-- most experienced developers prefer to collaborate on the Bitcoin Project and not in a niche fork built around really sketchy ideas.
I am really glad when someone else realizes how cool it is. :)

It's also cool for more pedestrian reasons, it shows an example of a very complex smart contract being done (mostly) outside of the blockchain but having full security. So it is very scalable, and doesn't require a huge amount of complexity in the system.
There was a thread on the prior advance fee forward stuff before. Apparently, previously wright was asking money for to pay to break the tulip trust-- then in morphed into investing in his blockchain company.

Based on the material on the nchain site, I would guess the targets are now primarily Chinese.
Blockstream does not have any patents, patent applications, provisional patent applications, or anything similar, related to segwit. As is the case for other major protocol features, the Bitcoin developers worked carefully to not create patent complications. Segwit was a large-scale collaboration across the community, which included people who work for Blockstream among its many contributors.

Moreover, because the public disclosure of segwit was more than a year ago, we could not apply for patents now.

In the prior thread where this absurdity was alleged on Reddit I debunked it forcefully. Considering that Rick directly repeated the tortured misinterpretation of our [patent pledge](https://blockstream.com/about/patent_pledge.html) from that thread (a pledge which took an approach that was lauded by multiple online groups), I find it hard to believe that he missed these corrections, doubly so in that he provides an incomplete response to them as though he were anticipating a reply, when really he’d already seen the rebuttal and should have known that there was nothing to these claims.

As an executive of Blockstream and one of the contributors to segwit, my straightforward public responses 1) that we do not, have not, will not, and can not apply for patents on segwit, 2) that if had we done so we would have been ethically obligated to disclose it, and 3) that even if we had done so our pledge would have made it available to everyone not engaging in patent aggression (just as the plain language of our pledge states): If others depended upon these responses, it would create a reliance which would preclude enforcement by Blockstream or our successors in interest even if the statements were somehow all untrue–or so the lawyers tell me.

In short, Rick Falkvinge’s allegations are entirely without merit and are supported by nothing more than pure speculation which had already been debunked.
So, what I'm able to extract from that that you don't actually believe that "Compact blocks doesn't help on-chain scaling nearly as well as xthin." but that you were simply saying it as a political attack.  If my understanding is mistaken, please clarify.
> but hardly something that would ever pose a serious threat even if all nodes were BU.

This would be a devastating vulnerability in that case, allowing attackers to arbitrarily fork the chain and profit from spending in each fragment.

> Also, how difficult is it in realistic terms to generate two separate txs with the same short id?

It takes about 30 seconds to a minute of wall-clock for me to generate a 64-bit collision on my desktop.

     $ echo -n "how difficult? not very.. ebb5a70b" | sha256sum 
     aaa430a8570acffa8bcf3a79d6099ad020bc5846a10e5e969da20aa110470f28 

     $ echo -n "how difficult? not very.. f5205fa2" | sha256sum 
     aaa430a8570acffa2cfd9355d2eacc46f2e0b4f940e469d9ff44718a9f1a1b92

There is a funny story about this were the BU 'Chief Scientist' was posting that it would take 2^64 computation/storage to create one, so that it was infeasible, I responded to his message with an example, and pointed out that it was not hard.  Then  Philip Daian wrote a generally reasonable blog post on the subject but his demonstration would take 12+ hours to construct, which then resulted in rbtcers to say that I was lying (and I think this might actually be the first 'greg is a liar' meme instance!) and that it wasn't an issue (although even if it took hours it would still be a vulnerability)... so I started responding to all the posts in rbtc with bespoke collisions generated on the fly. :) [Edit: some corrections from Philip on the history of his post]
I don't spend my time looking for BU/Classic bugs outright.  I do look at their fixes sometimes because in the past they have failed to inform upstream about issues they believed they found.

The errors in BU/xthin are so severe that they do not take significant effort to uncover.

Reviewing faulty software is also good practice, because if everything you review is good you don't know how effective your review is... It's also useful to think through how you'd handle an issue in advance-- when you're not under the gun to get something fixed, looking at other parties issues can be a useful way to do that.

As far as solutions go-- BIP152 Compact blocks, is deployed ubiquitously on the network and hasn't had a single one of the issues that have shown in with "xthin" so far.  When BIP152 was released and xthin tried to claim to be first I pointed out that xthin was a design and an implementation wreak, and that it could only claim "first" at all because it ran with an idea we originally proposed by made a design and implementation disaster of it.  I wasn't trying to be mean-- the failure after failure after failure supports what I said at the time.

> were born out of

Classic was created to raise the blocksize to 2MB, we came up with a way of doing that that was radically less disruptive and mitigated some of the risks of a blocksize increase.  And as soon as the development community solved that the goalpost moved.  The fact of the matter is that I don't believe that capacity was what these projects were born out of.
Yep. Which is time to improve this stuff, otherwise I wouldn't have posted: you'd already know what the real effects were from the effects.

> BU is still PRODUCING more blocks than any other client. 

Amazing that it can do that even with virtually all the BU nodes down! Immaculate mining. What will Bitcoin Jesus bring us next?

One might also ask how BU produces more blocks than Bitcoin Core when this subreddit's [favorite stats site](https://coin.dance/blocks) shows 41% signaling BU to 53% for Core... but I guess questioning Bitcoin Jesus would be sacrilegious.
>  a 1.1 MB block

No, 1000028 bytes.  

Bitcoin ... secured in a way that was physically impossible for others to compromise, no matter for what reason, no matter how good the excuse, no matter what. 

... No matter if you broke the rules by 'only' 28 bytes.

The reason for the ban is so that your connection slots are not wasted on nodes on an incompatible consensus, which might deny you access to the valid chain with the most-work (because you've found yourself surrounded by peers accepting an invalid chain).

It's not really intended to be punitive, and I wouldn't be surprised if we backed it off to be less aggressive with a ban but more aggressive in the triggering, e.g. punting the connection when it detects the peer is on a block that was rejected locally, and not only when they send us an invalid block; but only disconnecting and not banning.
sigh.

The users of Bitcoin are whom control the rules. Miners are along for the ride.

Miners are sufficient for the activation of a soft-fork, but they're not necessary.

In any case, if someone is actually attacking then I think what BramC's talking about doesn't have that much value over a hard-cut.  But I dunno, haven't thought about it-- I was only commenting that effectively the same idea had come up in another related context, and was considered more or less viable there.
> this ability to actually be used

It's used by the existing block sizes, with the original software the network would be completely failed at the current load.  It's also used by segwit-- which was justified to people on the basis of these (and other) improvements, and without which would not have been possible to convince most people to support.

> which doesn't help unless both payer and payee use it

That isn't true.  The use of segwit by a payee is not even distinguishable by a payer.  If you use segwit your transactions use less of the capacity limit of a segwit activated network, it doesn't matter what anyone else does.

this just shows how easy it is to manupulate people.

There are seven users who upgraded to segwit for each bu+classic+xt user.

rbtc is the monied attack, and there is irrefutable proof of that-- e.g. that the active moderators there are saintbitts employees, that they've been paying miners, that they're posting anonymous attack sites under fake names, that they're heavily invested in competing systems... But it's their narrative that you're slinging even though it has basically nothing to support it but a bunch of conjecture. :(

Maybe mankind is not ready for Bitcoin.
It will ban someone that provides a block which is invalid for any reason.

Things like double spend transactions do not cause bans,-- they're not invalid in and of themselves, and can happen just from the innocent operation of the network.  Only things which are expressly invalid and unambiguously indicate that the peer is broken or abiding by different rules cause bans.

The github webstats are notoriously lossy and have little to do with reality-- I believe the page shows only people who have set their account privacy settings in particular ways.

To pick one random party out of the huge number that it missed: https://github.com/bitcoin/bitcoin/commit/8c1dbc5e9ddbafb77e60e8c4e6eb275a3a76ac12

To get real figures, we just need to consult the repository itself:

     $ git log --since=2015-12-16 --no-merges | grep '^Author' | cut -d'<' -f 1 | sort | uniq | wc -l
     151

And your multiple commit criteria:

     $ git log --since=2015-12-16 --no-merges | grep '^Author' | cut -d'<' -f 1 | sort | uniq -c | awk '{if ($1>1) {print $0}}' | wc -l
     75

These figures are a few higher than they should be because of people changing how they spell their names (potentially the second figure is _lower_ due to this fact).   But these numbers MUCH closer to correct than your figures.

>  like to say there are over "100 developers" contributing to the Bitcoin open source repo

It isn't a "like to say"-- we give you [their names](https://github.com/bitcoin/bitcoin/commit/c9ffe9044da08773039060983632cc3fbd4f0eb8#diff-385124957614b5802d95a3b221b4ad7eR759),  and their contributions are publicly visible in the repository history.

Pick a name from that list that you believe hasn't contributed, and I'll gladly point you to the contribution. Otherwise, please kindly shut the heck up.

> KNOWN Blockstream employees

There aren't any 'unknown' blockstream employees contributing there... you're even already counting people that only contract for us part time.
Sure, but for all we know ~every node that matters (e.g. one with an actual user of some kind) has already upgraded.
> 14 days isn't much time, but I am a quick study.

Being so sure of these things in a few days when disagreeing to someone who has been doing it for 6 years is an indication of approaches and styles which are not conducive to successful learning, in my experience.

> I would argue that you have no actual evidence for your hypothesis. 

I just synchronized 80 GB of state with you in a single Reddit message. This is pretty profound "evidence".

(As far as miners centralizing, we also saw it in practice with 700k+ blocks causing high orphaning resulting in big consolidation around it, eventually making it reach >50% total hashrate until the deployment of the fast block relay protocol fixed the orphaning)

The rest of what you're throwing at me is religion. It has no place in an engineering discussion. Sure the market acts, and my participation here is a manifestation of the market acting to protect and grow Bitcoin. Arguing "the market will provide!" against participants in the market is circular.
LOL. The first possible activation would be mid December, but if that happens I'll eat my hat.

