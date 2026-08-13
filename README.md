# Firebase vs Cloudflare, A Citation First Cost and Scalability Audit

![Status](https://img.shields.io/badge/status-live%20audit-blue)
![Data](https://img.shields.io/badge/prices%20fetched-2026--08--13-brightgreen)
![Method](https://img.shields.io/badge/method-adversarially%20verified-orange)
![License](https://img.shields.io/badge/license-source--available-lightgrey)

Every number below carries a unit, a confidence tier, and a source. Nothing here is quoted from model memory. Prices were fetched from the vendors' own pages on 2026-08-13.

## What you get from this

Most Firebase versus Cloudflare comparisons are search optimised pages that repeat each other's numbers. This one does three things they do not.

1. **It shows the arithmetic.** Where a monthly cost appears, the meter, the quantity and the unit price appear next to it so you can disagree and recompute.
2. **It separates what was read from what was inferred.** A figure produced by multiplication is labelled derived. A figure that could not be confirmed is labelled unverified rather than smoothed over.
3. **It reports what broke under attack.** Claims here were handed to independent reviewers whose instruction was to break them. The ones that broke are listed in Section 12, including several of the author's own.

## The payoff walk, from nothing to the first useful answer

1. Read **Section 1**. It contains the single ratio that decides most of this question.
2. Read **Section 5 and Section 6**. Those are the walls that force a migration, with the user count where each one bites.
3. Read **Section 12**. That is the list of claims that did not survive verification, so you know what not to trust.

## Confidence tiers used throughout

| Tier | Meaning |
|---|---|
| **VERIFIED** | Fetched from the vendor's own page and quoted word for word during this audit |
| **DERIVED** | Arithmetic performed on a verified figure. The arithmetic is shown |
| **REPORTED** | A named first party published it. Not independently reproduced |
| **COMMUNITY** | Practitioner report or forum thread. Indicative, not fact |
| **UNVERIFIED** | Could not be confirmed. Listed so the gap stays visible |

## Section 1. The core cost meters, database

The row that decides the question is the first one.

| # | Meter | Firebase, Firestore Standard, us-central1 | Cloudflare, D1 | Ratio | Tier |
|---|---|---|---|---|---|
| 1.1 | **Reads** | **$0.30 per million documents**, printed as $0.03 per 100k | **$0.001 per million rows** | **300x for Cloudflare** | VERIFIED |
| 1.2 | **Writes** | **$0.90 per million documents**, printed as $0.09 per 100k | **$1.00 per million rows** | **1.11x for Firebase** | VERIFIED |
| 1.3 | Deletes | $0.10 per million, printed as $0.01 per 100k | Counted as writes | Firebase | VERIFIED |
| 1.4 | **Database storage** | approximately **$0.15 per GiB month** | **$0.75 per GB month** | **5x for Firebase** | Firestore side DERIVED, see 1.5 |
| 1.5 | Storage unit as actually printed | $0.000205479 per **GiB hour** | $0.75 per GB month | The monthly Firestore figure is that rate times 730. The multiplication is this audit's, not printed on the page | VERIFIED rate, DERIVED month |
| 1.6 | **Egress to internet** | $0.12 per GiB in the 10 GiB to 1 TiB band | **$0** on R2 and Workers | **Unbounded, for Cloudflare** | VERIFIED |
| 1.7 | Egress, 1 TiB to 10 TiB | $0.11 per GiB | $0 | Cloudflare | VERIFIED |
| 1.8 | Egress, above 10 TiB | $0.08 per GiB | $0 | Cloudflare | VERIFIED |
| 1.9 | Egress, first tier | 0 to 10 GiB free per month | $0 | Tie at low volume | VERIFIED |
| 1.10 | Multi region read premium | $0.06 per 100k in nam5, double the single region rate | Not applicable, D1 is single region | Different models | VERIFIED for the nam5 rate |
| 1.11 | Multi region write premium | $0.18 per 100k in nam5 | Not applicable | Different models | VERIFIED |
| 1.12 | Point in time recovery data | $0.000205479 per GiB hour | Time Travel included, no separate meter located | Cloudflare | Firestore VERIFIED |
| 1.13 | Backup data | $0.000041096 per GiB hour | No separate D1 backup meter located | Cloudflare | Firestore VERIFIED |
| 1.14 | Restore or clone | $0.20 per GiB | Not metered separately | Cloudflare | VERIFIED |
| 1.15 | Committed use discount, 1 year | Reads $0.024, writes $0.072, deletes $0.008 per 100k | No committed use discount offered | Firebase | VERIFIED |
| 1.16 | Committed use discount, 3 year | Reads $0.018, writes $0.054, deletes $0.006 per 100k | None offered | Firebase | VERIFIED |
| 1.17 | Free tier, reads | 50,000 per day | 5,000,000 per day | Cloudflare, 100x | VERIFIED |
| 1.18 | Free tier, writes | 20,000 per day | 100,000 per day | Cloudflare, 5x | VERIFIED |
| 1.19 | Free tier, storage | 1 GiB | 5 GB | Cloudflare | VERIFIED |
| 1.20 | Paid tier included reads | None, billed from zero above the free allowance | 25 billion rows per month | Cloudflare | VERIFIED |
| 1.21 | Paid tier included writes | None | 50 million rows per month | Cloudflare | VERIFIED |

**The structural reading of Section 1.** Firebase is cheaper per write and roughly five times cheaper per gigabyte stored. Cloudflare is three hundred times cheaper per read and free on egress. Because production applications run read to write ratios between 10 to 1 and 100 to 1, and because media delivery is egress, the read and egress rows account for the bulk of almost every real bill. That is how Cloudflare wins the aggregate while losing two of the four meters.

## Section 2. Firestore Enterprise edition, a different billing model entirely

Enterprise edition does not bill per document. It bills per unit of bytes, which changes the arithmetic completely and is widely missed.

| # | Meter | Rate, us-central1 | Unit definition | Tier |
|---|---|---|---|---|
| 2.1 | Read Units | $0.05 per million | 4 KiB tranches | VERIFIED |
| 2.2 | Write Units | $0.26 per million | 1 KiB tranches | VERIFIED |
| 2.3 | Managed Delete Units | $0.26 per million | 1 KiB tranches | VERIFIED |
| 2.4 | Event Units | $0.10 per million | 4 KiB tranches | VERIFIED |
| 2.5 | Real time update units | $0.30 per million | 4 KiB tranches | VERIFIED |
| 2.6 | Stored data | $0.000328767 per GiB hour | approximately $0.24 per GiB month, DERIVED | VERIFIED rate |
| 2.7 | Committed use, 1 year, Read Units | $0.04 per million | | VERIFIED |
| 2.8 | Committed use, 3 year, Read Units | $0.03 per million | | VERIFIED |
| 2.9 | Free tier | 1 GiB stored, 50k read units per day, 40k write units per day, 10k event units per day, 50k real time updates, 10 GiB egress per month | | VERIFIED |

**Why this matters.** A 1 KiB document read on Standard costs $0.30 per million. The same read on Enterprise is one Read Unit at $0.05 per million, a six times reduction. A 16 KiB document is four Read Units on Enterprise at $0.20 per million, which erodes most of that advantage. Enterprise favours small documents read often. Standard favours large documents read rarely. Neither approaches D1 at $0.001 per million rows.

## Section 3. Every other cost meter, both platforms

| # | Service | Firebase or Google Cloud | Cloudflare | Tier |
|---|---|---|---|---|
| 3.1 | Compute base fee | None, pure consumption | **$5.00 per month**, Workers Paid | VERIFIED |
| 3.2 | Function or Worker invocations | $0.40 per million, 2 million free per month | $0.30 per million, 10 million included | VERIFIED |
| 3.3 | Compute time billing model | GB seconds plus CPU seconds | **CPU milliseconds only, no duration charge** | VERIFIED |
| 3.4 | Included compute time | 400,000 GB seconds and 200,000 CPU seconds per month | 30,000,000 CPU milliseconds per month | VERIFIED |
| 3.5 | Compute time overage rate | Deferred to Cloud Run rates | $0.02 per million CPU ms | Firebase UNVERIFIED, Cloudflare VERIFIED |
| 3.6 | Function egress | $0.12 per GB after 5 GB free | $0 | VERIFIED |
| 3.7 | Blob storage | Cloud Storage $0.02 per GB month | R2 $0.015 per GB month | VERIFIED |
| 3.8 | Blob Class A operations | $0.005 per 1,000, equal to $5.00 per million | $4.50 per million | VERIFIED |
| 3.9 | Blob Class B operations | $0.0004 per 1,000, equal to $0.40 per million | $0.36 per million | VERIFIED |
| 3.10 | **Blob egress** | $0.12 per GiB after the free tier | **$0** | VERIFIED |
| 3.11 | Cold or infrequent storage | Nearline, Coldline and Archive classes | R2 Infrequent Access, $0.010 per GB month | VERIFIED for R2 |
| 3.12 | Infrequent access penalty | Retrieval fees and minimum durations apply | Retrieval $0.01 per GB, operations double, **30 day minimum duration** | VERIFIED for R2 |
| 3.13 | Key value store reads | No distinct product, Firestore or RTDB used instead | Workers KV, $0.50 per million | VERIFIED |
| 3.14 | Key value writes | | $5.00 per million | VERIFIED |
| 3.15 | Key value deletes and lists | | $5.00 per million each | VERIFIED |
| 3.16 | Key value storage | | $0.50 per GB month | VERIFIED |
| 3.17 | Queue operations | Cloud Tasks and Pub/Sub priced separately | Queues, $0.40 per million operations | Cloudflare VERIFIED |
| 3.18 | Queue operation definition | | **One operation equals each 64 KB written, read or deleted.** A 127 KB message is 2 operations, and retries plus dead letter writes add more | VERIFIED |
| 3.19 | Realtime database storage | RTDB, $5.00 per GB | No equivalent product | VERIFIED |
| 3.20 | Realtime database download | RTDB, $1.00 per GB | No equivalent | VERIFIED |
| 3.21 | Static hosting storage | Firebase Hosting, $0.026 per GB after 10 GB | Workers static assets, **free and unlimited** | VERIFIED |
| 3.22 | Static hosting transfer | $0.15 per GB after 360 MB per day | $0 | VERIFIED |
| 3.23 | Managed Postgres | SQL Connect, $0.90 per million operations after 250k free, Cloud SQL from $9.37 per month | Hyperdrive is **free**, the database is billed by its own provider | VERIFIED |
| 3.24 | Vector database | Vertex AI Vector Search, priced separately | Vectorize, $0.01 per million queried dimensions, $0.05 per 100 million stored | Cloudflare VERIFIED |
| 3.25 | Inference | Vertex AI or Gemini API rates | Workers AI, $0.011 per 1,000 Neurons | Cloudflare VERIFIED |
| 3.26 | AI gateway or proxy | No distinct product | AI Gateway core free, logpush $0.05 per million, unified billing carries a **5 percent surcharge** | VERIFIED |
| 3.27 | Container compute, vCPU | Cloud Run rates | $0.000020 per vCPU second after 375 vCPU minutes | Cloudflare VERIFIED |
| 3.28 | Container memory | Cloud Run rates | $0.0000025 per GiB second after 25 GiB hours | VERIFIED |
| 3.29 | Container egress | Google Cloud egress rates | **$0.025 to $0.05 per GB by region.** Containers are the one Cloudflare product that charges egress | VERIFIED |
| 3.30 | Container billing basis | | Memory and disk bill on the **provisioned** instance type. CPU bills on active use only | VERIFIED |

## Section 4. Authentication, the meter people forget

| # | Band or item | Firebase Identity Platform | Cloudflare | Tier |
|---|---|---|---|---|
| 4.1 | 0 to 50,000 monthly active users | **$0.00** | No managed end user auth product exists | VERIFIED |
| 4.2 | 50,000 to 100,000 | $0.0055 per user per month | Third party required | VERIFIED |
| 4.3 | 100,000 to 1,000,000 | $0.0046 per user per month | Third party required | VERIFIED |
| 4.4 | 1,000,000 to 10,000,000 | $0.0032 per user per month | Third party required | VERIFIED |
| 4.5 | Above 10,000,000 | $0.0025 per user per month | Third party required | VERIFIED |
| 4.6 | Enterprise SSO, SAML or OIDC | 50 users free, then **$0.015 per user per month** | Third party required | VERIFIED |
| 4.7 | SMS for phone auth and MFA | Per message, per country. Austria $0.03, Australia $0.02, Belgium $0.09, Brazil $0.02, UAE $0.09. First 10 per day not billed | Not applicable | VERIFIED |
| 4.8 | Worked cost at 1,000,000 users | 50,000 free, then 950,000 times $0.0046 equals **$4,370 per month** | Depends on the vendor chosen | DERIVED from 4.3 |
| 4.9 | Worked cost at 100,000 users | 50,000 free, then 50,000 times $0.0055 equals **$275 per month** | Depends on the vendor chosen | DERIVED from 4.2 |

**Reading Section 4.** Firebase Auth is free to 50,000 monthly active users and inexpensive above that. This is a real Firebase advantage with no Cloudflare equivalent. Cloudflare Access and Zero Trust are workforce products, not end user identity. Any Cloudflare stack has to buy or build this layer.

## Section 5. Firebase hard limits

| # | Limit | Value | Escape hatch | Tier |
|---|---|---|---|---|
| 5.1 | **Writes per second to a collection carrying a sequentially increasing indexed field** | **500 writes per second** | **Yes.** Google's text states that if you do not query on the sequential field you can exempt it from indexing to bypass the limit | VERIFIED, quoted |
| 5.2 | **The 500/50/5 ramp rule** | Start at a maximum of 500 operations per second on a new collection, then increase by 50 percent every 5 minutes | None. This is a launch day constraint | VERIFIED, quoted |
| 5.3 | Maximum document size | 1 MiB, 1,048,576 bytes | Split the document | VERIFIED |
| 5.4 | Maximum field value size | 1,048,487 bytes | | VERIFIED |
| 5.5 | Maximum API request size | 10 MiB | | VERIFIED |
| 5.6 | Field transformations per document per commit | 500 | | VERIFIED |
| 5.7 | Transaction time limit | 270 seconds, with 60 second idle expiry | | VERIFIED |
| 5.8 | Composite indexes per database | 1,000 with billing enabled | | VERIFIED |
| 5.9 | Single field index configurations per database | 1,000 with billing enabled | | VERIFIED |
| 5.10 | Realtime Database simultaneous connections | 200,000 per database instance, 100 on the free plan | Run multiple instances | VERIFIED |
| 5.11 | **The old one write per second per document limit** | **No longer documented.** Replaced with the statement that the exact maximum rate at which an app can update a single document depends highly on the workload | Not applicable | VERIFIED, the replacement text was read directly |
| 5.12 | The 10,000 writes per second cap | Not found on the quotas page or on Google's performance guidance page | | UNVERIFIED. Two pages searched. A narrow scope is not proof of absence |
| 5.13 | Total database storage cap | **None was located** | | This is Firestore's structural advantage over D1. Stated as a search result, not as a positive vendor claim |
| 5.14 | Maximum writes per batch | Not confirmed. The quotas page states only the 500 field transformation limit | | UNVERIFIED |

## Section 6. Cloudflare hard limits

| # | Limit | Workers Paid | Free | Consequence | Tier |
|---|---|---|---|---|---|
| 6.1 | **D1 maximum database size** | **10 GB** | 500 MB | Sharding becomes mandatory. At 5 KB per user that is roughly 1.5 to 2 million users | VERIFIED, quoted |
| 6.2 | **D1 maximum storage per account** | **1 TB** | 5 GB | The platform exit. Past this the relational store moves to Postgres | VERIFIED, quoted |
| 6.3 | D1 databases per account | 50,000 | 10 | Bounds how far sharding can go | VERIFIED, quoted |
| 6.4 | D1 queries per Worker invocation | 1,000 | 50 | Caps fan out per request | VERIFIED, quoted |
| 6.5 | **D1 Time Travel, point in time recovery** | **30 days** | 7 days | This is D1's entire backup window | VERIFIED, quoted |
| 6.6 | D1 maximum SQL query duration | 30 seconds | 30 seconds | | VERIFIED |
| 6.7 | D1 maximum row, string or BLOB size | 2 MB | 2 MB | Twice Firestore's 1 MiB document cap | REPORTED |
| 6.8 | D1 maximum columns per table | 100 | 100 | | VERIFIED |
| 6.9 | D1 concurrency model | Single threaded, queries processed sequentially | | A single database is not a parallel engine | REPORTED |
| 6.10 | **Durable Objects requests per second, per object** | **1,000, soft limit.** Exceeding it returns an overloaded error | | Any hot object becomes a bottleneck. This is the least discussed Cloudflare wall | REPORTED |
| 6.11 | Durable Objects SQLite storage per object | 10 GB | | | REPORTED |
| 6.12 | Durable Object classes per account | 500 | 100 | | REPORTED |
| 6.13 | Durable Object alarm handler wall clock | 15 minutes | | | REPORTED |
| 6.14 | Workers CPU time per request | 5 minutes maximum, 30 seconds default | 10 ms per invocation | | REPORTED |
| 6.15 | Workers memory per isolate | 128 MB | 128 MB | Rules out in memory processing of large payloads | **UNVERIFIED this session.** The limits table is client rendered. 244 KB fetched returned navigation only |
| 6.16 | Workers simultaneous open connections | 6 | 6 | | UNVERIFIED this session, same reason |
| 6.17 | Workers subrequests per request | Reported as 1,000, though one extraction returned an ambiguous 10,000 | 50 | **Do not put either figure into a cost model without re-reading the raw page** | UNVERIFIED, conflicting extractions |
| 6.18 | D1 queries per second | No published figure exists | | Any queries per second number is derived from query latency, never documented | UNVERIFIED |
| 6.19 | **D1 jurisdiction** | **Set at database creation and cannot be changed** | | A direct consequence for EU data residency. A wrongly placed database must be recreated and migrated | COMMUNITY |
| 6.20 | D1 REST API rate limit | Reported as 1,200 per 5 minutes | | One operator reports exhausting it in 10 to 20 seconds at 5,000 to 6,000 requests per 5 minute window | COMMUNITY |

## Section 7. Firestore billing mechanics, the part that produces the horror stories

Every row in this section was fetched and quoted word for word from Google's own pricing documentation during this audit. This is the most useful section in the repository.

| # | Mechanism | Google's own words | Consequence | Tier |
|---|---|---|---|---|
| 7.1 | **Security Rules are billed reads** | You are charged for reads that are necessary to evaluate your Cloud Firestore Security Rules. For example, if your rules refer to three documents, but Cloud Firestore only has to read two of those documents to evaluate your rules, you will be charged for two additional reads | Your authorization layer is a per request read multiplier. A rules file checking a membership document and a role document adds two billed reads to every operation | VERIFIED, quoted |
| 7.2 | **Listener reconnect re-bills the entire result set** | If offline persistence is enabled and the listener is disconnected for more than 30 minutes, you will be charged for documents and index entries read as if you had issued a brand new query. If offline persistence is disabled, you will be charged whenever the listener disconnects and reconnects | This is the mechanism behind most bills that jump by an order of magnitude. A commuter losing signal re-bills the full feed query on every reconnect | VERIFIED, quoted |
| 7.3 | **Offset bills every skipped document** | When you send a query that includes an offset, you are charged a read for each skipped document. For example, if your query uses an offset of 10, and the query returns 1 document, you are charged for 11 reads | Page 50 of a 20 per page list bills roughly 1,000 reads to return 20 rows. Use cursors | VERIFIED, quoted |
| 7.4 | **Minimum one read on an empty query** | For aggregation queries that read 0 index entries, there is a minimum charge of one document read | A query returning nothing still costs | VERIFIED, quoted |
| 7.5 | **Index entries are billed reads** | You are charged one read operation for each batch of up to 1000 index entries read by the query | Reads are documents plus index entries, not documents alone | VERIFIED, quoted |
| 7.6 | **count() is roughly a thousand times cheaper than reading** | count() operations that read between 0 and 1000 index entries are billed for one document read. For a count() operation that reads 1500 index entries, you are billed 2 document reads | Never fetch a collection in order to count it | VERIFIED, quoted |
| 7.7 | **Indexes do NOT multiply write cost** | For writes, each set or update operation counts as a single write | **This corrects the most repeated claim in Firestore cost articles, and an earlier claim by this audit's own author.** Indexes cost storage, write latency and the 500 writes per second cap. They do not increase billed writes | VERIFIED, quoted |
| 7.8 | Cloud Functions recursive write loops | Any time you write to the same document that triggered a function, you are at risk of creating an infinite loop | A two line bug becomes an unbounded bill | REPORTED from Google's documentation |
| 7.9 | Rules re-evaluation on reconnect | Rules reads are re-evaluated and re-billed any time the user's device goes offline and then comes back online | Compounds with 7.2 | REPORTED |

**The structural conclusion of Section 7.** A per read price of $0.30 per million is not expensive. The problem is that the number of reads is **bounded by client behaviour rather than by data size**. No joins forces N plus 1 access patterns. No fuzzy matching forces client side filtering of whole collections. Security rules multiply every request. Listeners re-bill on reconnect. A conventional database bills for a machine instead.

## Section 8. Cloudflare billing traps, held to the same standard

| # | Trap | Detail | Consequence | Tier |
|---|---|---|---|---|
| 8.1 | **Durable Objects bill wall clock, not CPU** | Durable Objects bill wall clock time for as long as the object is active and not eligible for hibernation | This is the **opposite** of the Workers model, on the same platform. An idle object that has not hibernated bills continuously | REPORTED from Cloudflare documentation |
| 8.2 | **Queue operations are per 64 KB, not per message** | One operation equals each 64 KB written, read or deleted | A 127 KB message costs 2 operations on write, 2 on read and 2 on delete. Retries and dead letter writes add more | VERIFIED |
| 8.3 | **R2 multipart uploads bill every part** | UploadPart is a Class A operation at $4.50 per million | A large file split into many parts multiplies the operation count | REPORTED |
| 8.4 | **D1 writes cost more than Firestore writes** | $1.00 per million rows written against $0.90 per million documents | A write heavy workload is cheaper on Firestore | VERIFIED, see 1.2 |
| 8.5 | **D1 storage costs about five times Firestore storage** | $0.75 per GB month against approximately $0.15 per GiB month | A storage heavy, read light workload favours Firestore | VERIFIED rate, DERIVED comparison |
| 8.6 | Edge cached KV reads still bill | A read served from edge cache still incurs the full read charge | Caching does not reduce the KV meter | COMMUNITY |
| 8.7 | KV write propagation | A write can take up to a minute to appear globally, and there is a limit of one write per second per key | KV is a cache with an API, not a database | COMMUNITY |
| 8.8 | Containers charge egress | $0.025 to $0.05 per GB by region | The one Cloudflare product that breaks the zero egress promise | VERIFIED |
| 8.9 | Unified AI billing surcharge | AI Gateway unified billing carries a 5 percent surcharge on credit purchases | | VERIFIED |

## Section 9. Reliability, measured rather than asserted

Cloudflare published a post mortem of the 12 June 2025 outage, which lasted 2 hours and 28 minutes. A third party provider took down Workers KV's central storage. The published peak error rates are the most useful reliability datapoint in this audit, because they separate the compute layer from the stateful layer.

| # | Service | Peak error rate | Tier |
|---|---|---|---|
| 9.1 | Workers KV | **90.22 percent** | REPORTED, Cloudflare's own post mortem |
| 9.2 | Durable Objects, SQLite backend | **22 percent** | REPORTED, same |
| 9.3 | D1 | **22 percent** | REPORTED, same |
| 9.4 | Workers runtime | approximately **2 percent** | REPORTED, same |

**The reading.** The compute layer held. The stateful layer did not. This is the empirical form of the criticism catalogued in Section 10.

## Section 10. Practitioner assessment of Cloudflare's data products

From an independent practitioner review, self described as anecdotal and based on a decade of production use. Treat it as informed opinion, not measurement.

| # | Product | Score | Quoted characterisation | Tier |
|---|---|---|---|---|
| 10.1 | Workers | **10 out of 10** | 11 major or critical incidents out of 178 across three years | COMMUNITY |
| 10.2 | Workers KV | 9 out of 10 | 2 of 25 | COMMUNITY |
| 10.3 | R2 | 6 out of 10 | 127 incidents | COMMUNITY |
| 10.4 | **D1** | **3 out of 10** | Good for demos and proofs of concept. Not production | COMMUNITY |
| 10.5 | **Durable Objects** | **2 out of 10** | Technically impressive, operationally terrifying. Treat it as a coordination cache you can afford to lose, never a system of record | COMMUNITY |

Named Durable Object failure modes from the same source. Hardware failure taking entire object sets offline, input and output gate deadlocks, storage growth that is hard to inspect, per object throughput caps appearing in production, and inadequate observability.

Named D1 claims from the same source. A single writer, error rates climbing sharply once a database grows past roughly 8 GB, no interactive transactions, and writes receiving no automatic retries.

## Section 11. Documented bill disasters, both platforms

Neither platform is safe. The difference is which meter runs away.

| # | Who | When | Amount | Mechanism | Tier |
|---|---|---|---|---|---|
| 11.1 | Vaki, Colombian crowdfunding platform | July 2018 | **$30,356.56 in 72 hours** | A method in a service constructor read the entire 16,000 document payments collection on every page load. Two million sessions produced, in the author's words, more than 46 billion requests. A precomputed total was the fix | REPORTED, first party writeup |
| 11.2 | Anonymous developer | April 2026 | **EUR 54,000 in 13 hours**, against a budget alert set at EUR 80 | A Firebase browser key without API restrictions, used externally for Gemini requests after Firebase AI Logic was enabled | REPORTED, Google's own developer forum |
| 11.3 | Anonymous developer | April 2026 | **$67,000 in 19 hours** | A 2016 Firebase provisioned Android key, not covered by Google's May 2024 automatic restriction policy | REPORTED, same forum |
| 11.4 | Anonymous developer | April 2026 | **$13,000** | Roughly 10 test generations, zero external users | REPORTED, same forum |
| 11.5 | Anonymous developer | May 2026 | **$6,486.81** | Not specified | REPORTED, same forum |
| 11.6 | Anonymous developer | May 2026 | approximately **CA$2,918** | A Firebase auto generated browser key whose restrictions permitted the Vertex AI endpoint | REPORTED, same forum |
| 11.7 | **Solo founder, pre launch** | approximately April 2026 | **$34,895 in 8 days, with zero users** | A Durable Object onStart handler called setAlarm on every wake without checking for an existing alarm. Sixty or more independent object instances produced a runaway loop reaching roughly 930 billion row reads per day. Usage notifications monitored CPU time only, not Durable Object operations | REPORTED |
| 11.8 | **Little Bear Apps** | January 2026 | **$4,868 accrued on a $5 per month account.** 4.83 billion rows written | Two bugs. A cron job reprocessing the same batch on every tick, and a missing ON CONFLICT clause on a harvester's inserts. **Four days to notice, eighteen days to resolve**, escalated through support, the forum, the Head of Billing, Reddit and the Director of Support. Waived as a one time courtesy | REPORTED, first party writeup |

**Verified arithmetic on 11.8.** 4.83 billion rows written, minus 50 million included, times $1.00 per million, equals approximately $4,780. The published figure of $4,868 is consistent with the stated meter.

**The operator's own conclusion from 11.8, quoted.** Do not use D1 without your own circuit breakers, because the platform does not have them yet.

**A widely miscited case, flagged.** A roughly $70,000 single day Firebase bill from January 2025 is real and was published by the account holder. The commonly repeated cause, approximately one petabyte in a Cloud Storage bucket, comes from a commenter rather than from the account holder. **This is not a Firestore read cost case and should not be cited as one.**

## Section 12. What broke under verification

This section exists because a comparison table with no error log is asking to be trusted rather than checked.

| # | Claim as originally stated | What verification found | Status |
|---|---|---|---|
| 12.1 | Cloudflare wins database cost by an order of magnitude across the board | Cloudflare wins reads by 300x and egress absolutely. **Firebase wins writes by 1.11x and database storage by roughly 5x** | **Corrected** |
| 12.2 | Every Firestore index costs an additional write on every document write | Google's page states that for writes, each set or update operation counts as a single write. Indexes cost storage, latency and throughput, not billed writes | **Wrong, retracted** |
| 12.3 | The 500 writes per second sequential index limit bites almost every schema | Google's own sentence carries an escape hatch. If you do not query on the sequential field you can exempt it from indexing to bypass the limit. It bites only when you query on that field | **Overstated, corrected** |
| 12.4 | Firestore enforces one write per second per document | No longer documented. Replaced with workload dependent language. Common wisdom on this is stale | **Confirmed as stale wisdom** |
| 12.5 | Neither platform has a hard spend cap | Google's budgets page rendered fully, with 434 occurrences of the word budget, and contains no cap, limit or stop language, which supports alert only. **The Cloudflare half was never checked and is withdrawn** | **Half withdrawn** |
| 12.6 | Cloudflare has no native mobile SDK | Never searched. Retracted pending verification | **Retracted** |
| 12.7 | Workers memory is 128 MB and simultaneous connections are 6 | The limits page is client rendered. 244 KB fetched returned navigation only, with no table body | **Unverified this session** |
| 12.8 | The Firestore quotas page confirms the limits in Section 5 | 446 KB fetched, zero pattern matches across eight probes. Client rendered. Those limits came from a prior agent read, not from this session's own fetch | **Provenance downgraded** |
| 12.9 | Firestore storage is $0.15 per GiB month | Google publishes $0.000205479 per GiB hour. The monthly figure is a times 730 multiplication performed by this audit, not a printed price | **Reclassified as derived** |
| 12.10 | The commonly cited Firestore write rate of $1.08 per million, February 2026 | Traces only to comparison websites, and does not match the $0.09 per 100k, equal to $0.90 per million, printed on Google's own single region page | **Unverified, do not cite** |
| 12.11 | Firestore multi region rates of $0.06, $0.18 and $0.02 per 100k | The multi region table is client rendered and could not be read cleanly in one pass. One agent extracted these figures, a second could not confirm them | **Partially verified, re-check before use** |

## Section 13. Migration evidence

| # | Case | Direction | Published outcome | Tier |
|---|---|---|---|---|
| 13.1 | Traba | Firestore to Postgres | **Reduced database expenses by over 70 percent.** Critical read heavy endpoints went from more than 10 seconds to under 1 second. Approximately one year, zero downtime, incremental collection by collection | REPORTED, first party engineering page |
| 13.2 | Traba, stated reasons | | Capability rather than cost alone. No fuzzy matching, which forced them to pull all worker documents into memory and filter locally. No counting without fetching. A 30 entry array query cap. No multiple inequality queries | REPORTED, same |
| 13.3 | Anonymous operator | Within Cloudflare, D1 to custom Durable Object storage | 421 or more databases, 5,000 to 6,000 requests per 5 minute window, exhausted the REST API limit in 10 to 20 seconds, roughly 18,600 errors across 7 months after general availability. They built their own storage layer on raw primitives | COMMUNITY |
| 13.4 | Independent engineer | D1 scaling within Cloudflare | Hit the 10 GB cap and sharded across 50 databases for 500 GB total. SHA-256 routing overhead reported as well under 0.5 ms. No cost figures published | COMMUNITY |

## Section 14. What no one has published

A finding worth stating plainly. **No operator has published a real bill for the same workload on both platforms.** Every 2025 and 2026 Firebase versus Cloudflare cost article located during this audit is search optimised or affiliate content with no primary source. That absence is why this repository shows its arithmetic instead of citing a comparison.

## Section 15. Open questions still under adversarial review

| # | Question | Why it matters |
|---|---|---|
| 15.1 | Does the EU data residency gate disqualify D1 for German enterprise work | Jurisdiction is reported as fixed at database creation and unchangeable |
| 15.2 | What does the hybrid actually cost at 1 million and 10 million users | Cloudflare edge with Workers and R2, in front of managed or self hosted Postgres |
| 15.3 | Does Postgres on dedicated hardware beat managed offerings at these tiers | Including the operational burden, counted honestly |
| 15.4 | What is D1's real write throughput | No published figure exists |
| 15.5 | Which Cloudflare data products carry a contractual service level agreement | A product without one is difficult to place in a regulated enterprise deal |
| 15.6 | What is the true engineering cost of rebuilding offline sync | Firestore's offline persistence has no managed Cloudflare equivalent |

## Method

Sixteen independent research lenses, each producing structured findings with source URLs. Every load bearing claim from every lens was handed to an independent reviewer instructed to break it rather than confirm it, defaulting to rejection wherever a real problem was found. Cost models were built at five user tiers. Three critics ran separately, one hunting for missing angles, one arguing exclusively for Firebase, one arguing exclusively for Cloudflare.

Corrections produced by that process are in Section 12, including corrections to the author's own earlier claims.

## Contributing

Corrections are the most valuable contribution here. If a figure is wrong, open an issue with the source URL and the date you fetched it. Figures without a source will not be merged.

## License

Source available. Not MIT, and not open source. Free for personal, non commercial evaluation with credit kept intact. Business use, derivative works and AI reimplementation require prior written consent. See LICENSE.

## Trademarks

Firebase, Firestore, Google Cloud and Gemini are trademarks of Google LLC. Cloudflare, Workers, D1, R2 and Durable Objects are trademarks of Cloudflare, Inc. This repository is independent and is not affiliated with, endorsed by, or sponsored by either company. All references are nominative.
