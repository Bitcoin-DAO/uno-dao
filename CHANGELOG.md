# Changelog

Each release is a git tag (`v0.1.1`). A DAO's copy runs one tagged release and records it in its README; a release moves to a DAO's next stage only after passing the one before ([guide, section 8](docs/guide.md#8-the-three-stages-practice-testnet-mainnet)).

## v0.2.0 · 8 November 2026

The findings from UNO's practice run (6–8 November 2026, `docs/runs/2026-11-uno-practice.md`): 25 of 28 built, the rest noted in `MAINNET-READINESS.md`.

**Fees**
- A new rule, **round fees**: **fast** (the default) aims for the next block with a quarter more as headroom for the time signatures take, never under 2 sat/vB; **economical** aims for about an hour. Members change it by a rule-change vote.
- When the network asks more than the DAO's cap, the round goes at the cap with a warning, instead of being blocked.
- The round shows its fee as a share of what it pays, with its aim; the round page warns if the next-block fee has risen above the round's while signatures were collected.

**Broadcasts and confirmations**
- A broadcast goes to every chain source at once, and the round page lists which accepted, which refused and why, plus what a chain gateway reported for the services behind it. "Already known" counts as accepted.
- "On the network ✓" says which sources know the transaction.
- **Receipts wait for depth:** a council phone publishes the receipt only after 3 confirmations (`receiptConfirmations` in the config), so a reorg cannot leave a false receipt.
- Everyone's round page shows the confirmations as they grow ("Confirmed in block … (1 of 3)") and says when the receipt is waiting for a council member. It checks every 20 seconds.
- The Council page checks the chain: a round already on the network is no longer offered for broadcast or counted as a to-do.

**Treasury reading**
- The reading names its source ("Read from the blockchain: 15:24 · via blockbook.tbtc.zelcore.io · btc-dao-gateway").
- Open pages of the same site share one reading instead of each asking the chain.

**Chain gateway**
- Lets the app read which service answered and each broadcast's results; logs every broadcast's answers to the Worker's logs; answers the chain tip; keeps a shallow transaction's answer only briefly, so its confirmations can grow.

**Founding**
- On testnet4 the founders choose the vote length: **practice** (10-minute votes) or **live** (the site file's, 3 days by default). Members switch later by a rule-change vote; the DAO and its record carry on. A **practice mode** banner shows while votes are shorter than a day.
- A warning when the threshold equals the council size: one lost key would lock the treasury and block key replacement.
- After publishing the founding settings: who has approved, who has not yet, and what happens next.
- The first donation card is a **Fund the treasury** step, naming the BTC DAO Testnet Treasury on testnet.

**Members and keys**
- Key replacements that can never complete (the new key already in use, or a later one won) are hidden; a second replacement for a key already under way is stopped; replacing a council member's key when the others cannot reach the threshold is explained, not left waiting.
- The waiting pages say when an approved member or new key is waiting for the council to publish the member list; Sign in points a phone with a sign-up or replacement under way back to its page.
- After clearing a browser's data, the sign-in message explains that the passkey no longer has a key to open and points to the card or "I lost my key and card". Passkeys are named with the date they were made.
- A former member's profile shows only their record and **Join again**.
- Council pages opened while signed out offer Sign in and Come back with your card.

**Wording and pages**
- Browser translation is off (it rewrote "UNO" and proposals' binding text); the page's language is set before the first paint.
- "Remote" everywhere instead of "video call".
- Voting periods read in minutes, hours or days; vote-length changes are offered as 10 minutes, 1, 3 or 7 days.
- The home page says a round can be prepared as soon as payments are approved.
- Payout proposals: **Share** the recipient's confirmation link, with the DAO, proposal, amount and link in one message.
- The app's own confirmation dialogs, in its language, instead of the browser's.
- The identity card prints alone, on one page, without the browser's header and footer.

**Docs**
- Guide: own Google or Apple account per founder; practice or live votes; a spare key on the council.

**Tests**
- Unit (64): fee policy, depth from the chain tip, sources that know a transaction, broadcasting to every source, source names, vote length at founding. Gateway (18).
- End-to-end, Esplora, Blockbook and through the gateway: fast fee suggested, fee share and aim, sources named, depth shown to a member before the receipt, receipt after 3 confirmations; the card prints on one page; the header shows the member right after a restore; a former member is offered no member actions. The mock chain keeps mining so confirmations grow.

## v0.1.10 · 8 November 2026

Found in UNO's closing round: one of mempool.space's servers still reported the transaction while no miner had it, so the round page showed "On the network ✓" and offered no way to send it again.

**Broadcast again while unconfirmed**
- Until the round is confirmed, the round page keeps **Broadcast again** (sending twice is harmless), with a line saying when to use it.
- The signed transaction to copy and each source's broadcast page stay available under **Send it by hand**.

**Tests**
- End-to-end payout round through the chain gateway.

## v0.1.9 · 8 November 2026

Found in UNO's closing round: with mempool.space down, the app had no chain source it could read, although Zelcore's Blockbook was in sync throughout. It only refuses web pages.

**New: the chain gateway** (`gateway/`, design in `docs/chain-gateway.md`)
- A Cloudflare Worker per DAO, next to the relay, on the free plan. It reads the public chain services on the app's behalf and lets the DAO's site read the answers, so Zelcore's Blockbook becomes usable.
- Asks its sources in order, reuses answers briefly so open pages share one reading, refuses other sites' pages, and limits requests per visitor.
- Sends every broadcast to all its sources; "already known" counts as accepted.
- Answers in Blockbook's and Esplora's formats, so the app needs no change: the DAO's `chainApi` lists the gateway first.
- Holds no keys and cannot move coins.

**Tests**
- Gateway unit tests (15): reading, falling back, caching, "not found", allowed requests, origins, the limit, broadcasting to every source, transaction ids, redirects.
- End-to-end: the full payout round with the app reading the mock chain only through the gateway (`CHAIN=gateway bash app/e2e/run.sh payouts`).

## v0.1.8 · 8 November 2026

Found in UNO's closing round: the treasury could not be read ("/address/…: Failed to fetch"), while Blockbook answered in the browser. The message gave only the last failure, so nobody could tell which source failed or why.

**Chain errors name every source**
- When no source answers, the message lists each one with its own reason, for example "mempool.space: no answer in 30 s · blockbook.tbtc.zelcore.io: answers, but does not let this site read it".
- "Failed to fetch" is split in two: a source that cannot be reached, and one that answers but does not let web pages read the answer (no CORS header, often on a "too many requests" page).
- A 429 answer says "too many requests".

**Tests**
- Unit test: two failing sources are both named, each with its reason.

## v0.1.7 · 7 November 2026

Found before UNO's lost-key test: the identity card restores the identity key only, so a council member who restored from the card could vote but no longer sign rounds. In a 2-of-2 practice DAO that would have locked the treasury.

**Trial treasury key: type the 12 words back in**
- Profile → Treasury key: a council member whose phone has no trial treasury key gets a box for the 12 words.
- The words must give exactly the treasury key recorded for that member on the council (same fingerprint and xpub). Words of any other key are refused, so a typo cannot install the wrong key. Capitals and extra spaces are ignored.
- The round page and the Council page say when the key is missing, with a link to the box.
- Test networks only. On mainnet the treasury key stays on the SeedSigner and never touches the phone.

**Tests**
- End-to-end: a council phone loses its trial key, the round page flags it, another key's words are refused, its own words are accepted, and it signs the round.

## v0.1.6 · 6 November 2026

Found in UNO's practice run: the first payout round showed "Broadcast ✓", but neither mempool.space nor Blockbook had the transaction.

**A broadcast counts only when the network has it**
- A source has accepted a transaction only when it answers with that transaction's id. An unstable service can answer "OK" with a holding page, and that read as success.
- A transaction counts as on the network only when a source returns a real status for it. An error answer (Blockbook's "not found") or anything else means "not known".
- After a broadcast the round page names the source that took it ("Accepted by blockbook.tbtc.zelcore.io") and shows "On the network ✓" only once a source reports the transaction. It checks again every minute until the payment confirms.
- Until then the round page keeps **Broadcast again** (sending it twice is harmless) and the signed transaction to copy, with each source's broadcast page for doing it by hand.
- Council phones broadcast a ready round again by themselves if no source has seen it 10 minutes after they last sent it.

**Tests**
- Unit tests: an unknown transaction, an error answer and a holding page are not a status; a broadcast is accepted only with the transaction's id.
- End-to-end (Esplora and Blockbook): the round page names the accepting source, then shows the transaction on the network.

## v0.1.5 · 6 November 2026

Found in UNO's practice run: mempool.space's testnet4 indexer was unstable for most of the day, and it was the only source.

**A second chain source: Blockbook**
- `chainApi` entries can now be Blockbook servers, written `blockbook:<address>`, next to Esplora APIs. The app asks in Esplora's terms and translates Blockbook's answers for all six requests: address activity, transactions, unspent coins, transaction status, fee estimate and broadcast.
- Sources are asked in order; a source that fails is asked last for the next five minutes, so one slow source does not slow every request.
- The site's current `chainApi` is used first, then the one recorded at founding, so a founded DAO can add a source without a vote. A source shows data but never moves coins: every payment is checked against the treasury's descriptor.
- Explorer links and the manual broadcast page follow the first source (Blockbook: `/tx/<txid>` and `/sendtx`).
- Example config: Zelcore's testnet4 Blockbook (`blockbook:https://blockbook.tbtc.zelcore.io`) as the second source.

**Tests**
- Unit tests read UNO's practice treasury from Blockbook answers and fall back from a silent Esplora source.
- The end-to-end mock chain also answers as Blockbook (`CHAIN=blockbook bash e2e/run.sh payouts`): a full payout round, broadcast included, with Blockbook as the only source.

## v0.1.4 · 6 November 2026

Found in UNO's practice run: with mempool.space unstable, the treasury page kept loading or showed only an error.

**The treasury page shows what it last knew**
- Each phone remembers the last treasury reading and shows it at once, with the time it was read ("Read from the blockchain: 6 Nov, 13:48"), then refreshes in the background ("updating…"). A failed refresh shows "not updated, the blockchain is not answering" and keeps the balance.
- The funding address shows while the treasury is loading too.
- One reading at a time; a reading is refreshed after 5 minutes, at once after a payout round moves, and retried by itself at most every 2 minutes after a failure.
- A whole reading gives up after 2 minutes, and errors say what failed ("/address/…: no answer in 30 s").

**Payout rounds use only live coins**
- Preparing a round waits for a fresh reading of the treasury's coins and never builds from the remembered one.

## v0.1.3 · 6 November 2026

Found in UNO's practice run: with mempool.space working again, the treasury still timed out.

**Treasury page asks far less of the blockchain service**
- The treasury check looks 5 unused addresses past the last used one, not 20. The app only ever shows the first unused address and takes change in order, so used addresses stay contiguous. A new treasury now needs about 10 requests instead of 40.
- Requests go one at a time with a short pause, and each may take up to 30 seconds. mempool.space slows bursts down rather than refusing them, so later requests in a burst ran past the old 15-second limit.

**Docs**
- Testnet treasury log: 10,000 sats to UNO's practice.

## v0.1.2 · 6 November 2026

Found in UNO's practice run (2 of 2, two phones).

**Treasury page survives the blockchain service**
- Reading the treasury no longer sends bursts: two addresses at a time with a short pause, each request retried twice and given up after 15 seconds. mempool.space refused the old bursts, which reached the page only as "Failed to fetch", and a stalled request could leave the page loading for ever.
- When the blockchain cannot be read, the page still shows the treasury's funding address, worked out on the phone from the descriptor.

## v0.1.1 · 5 November 2026

**Remote founding without a call**
- Founder sign-up, code page: a **Share** button sends one message (DAO, role, pseudonym, short code and full code) through the phone's share sheet, or copies it where sharing is not available.
- The message names the role, **Council** or **Founder**, so a mistaken council request shows in the group.
- The code page's wording covers founding **in person** and **remote**.
- The setup page's paste box accepts the whole shared message, not only the bare code.
- The council box reads **I request a council seat**.

**Starting again**
- The code page has a **Start again** link.
- Starting the sign-up again also discards the setup draft, which belonged to the old identity.

**DAO-owned files**
- New `site/` folder: the DAO's own static pages and files, copied next to the app at build. Like `config/`, it belongs to the DAO and is never touched by updating btc-dao.

**Docs**
- BTC DAO Testnet Treasury: new dedicated wallet (`2N5SK7BFXGzefmMs9AHui2Y71fUh3uVVn2S`) and the first log entry.
- Guide: the remote route through the DAO's group at founding.

## v0.1.0

The testnet4 prototype: founding, passkey sign-in, identity cards, treasury, proposals and votes, membership, key replacement and payout rounds signed by the council. Tagged afterwards, at the commit the UNO stages were set up from.
