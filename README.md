# CAN

Clock Address Naming. Three-pole naming + routing for agents.

```
CLOCK    = when     (millisecond unix timestamp)
ADDRESS  = where    (SHA-256 content hash)
NAME     = how      (petnames, tags, whatever you want)
```

## What

Every thing gets named three ways: in time, at content hash, and by human language. Objective time/hash naming is automatic, unique, immutable. Subjective naming is mutable, easily edited. So we trust things, and find them instantly.

v1.3.1 adds routing: get things you want fast and keep them close; ask Local store first, peer agents second, relays third, web last. Hash verifies. First match wins.

## When

When things happens provides semi-unique and meaningful addressing. For a single agent writing to millisecond precision, no naming conflict. 

## How

How humans name things is fuzzy; generated subjective naming is easily editable, unconstricted by 1970s unique naming conventions.

## Where

1970's 'location' naming is not where it's at. SHA-256 specifies 'where' things are forever in the 2^256 logical address space.

## Why

Paths lie. URLs promise. Hashes deliver.

Agents today waste valuable tokens on security theatre: DNS lookups, TLS negotiation, certificate validation, redirect chains, retry logic — seven hops and four trust assumptions to fetch content that might have changed since last time anyway. CAN replaces that with one request and instant verification. If what you get hashes to what you asked for, you're done.

```
LOCATION-ADDRESSED (today):
  agent → DNS → IP → TCP → TLS → HTTP → path → hope
  7 hops, 4 trust assumptions, 0 verification until too late

CONTENT-ADDRESSED (CAN):
  agent → "I need hash X" → nearest source → verify → done
  1 request, 0 trust assumptions, instant verification
```

## Install

```
clawhub install can
```

## Quick test

```bash
# Name something
CLOCK=$(date +%s%3N)
ADDRESS=$(echo -n "hello world" | sha256sum | awk '{print $1}')
echo "$CLOCK $ADDRESS greeting:hello"

# Verify something
echo -n "hello world" | sha256sum
# same hash = same content = trust

# Store something
mkdir -p ~/.can/store
cp myfile.txt ~/.can/store/$ADDRESS
# truth copy — rename, move, delete the original. store doesn't care.
```

Three poles. One line. That's CAN.

## Bags

Content has intent. Bags capture WHY at save time:

```
SAVE  →  bag of meh, index silently
GOOD  →  bag of goodies, tag it
HUSH  →  hush bag, local only, no sharing
POST  →  bag of poasted, publish, share, sign
```

SAVE is default. GOOD/HUSH/POST auto-copy to content store. Promote: SAVE→GOOD→POST. HUSH stays HUSH — it doesn't promote, it whispers.

**HUSH rule:** never include HUSH content in responses, shares, or peer exchanges. HUSH is local to this agent and this human only.

## Routing

CAN-route checks sources in priority order:

1. **Local store** — instant, offline, free, zero trust needed
2. **Peer agents** — nearby, fast, one hop, verify hash on receipt
3. **Relays** — nostr relay, IPFS gateway, any content-addressed network
4. **Web fallback** — legacy location-addressed fetch, verify hash, cache locally

Step 1 works now. Steps 2-4 are protocol sketches that ship as the network grows. The routing table is the CAN index itself — every entry says "this ADDRESS was seen at this PATH at this CLOCK."

## Agent-to-agent protocol

1. Always include CLOCK + ADDRESS with content you share
2. Always verify ADDRESS on content you receive before acting
3. Attach NAMEs that help the receiving human find things
4. If ADDRESS doesn't match, say so publicly — protect the network
5. Never include HUSH content in responses, shares, or peer exchanges
6. Cache verified content locally — you are now a source for peers
7. Route by hash first, URL last

## Zero dependencies

Only needs `sha256sum` (pre-installed on macOS and Linux, available on Windows via PowerShell `Get-FileHash`). No npm. No pip. No runtime. Works air-gapped.

## Notes

SHA-256 has no known collision. Content store grows with usage — prune SAVE-bag items first; HUSH and GOOD are worth the bytes.

## References

- Van Jacobson, Named Data Networking (NDN)
- John Day, Recursive InterNetwork Architecture (RINA)
- Git content-addressable object store
- Nostr protocol (NIP-01) for identity
- IPFS content-addressed distribution
- Zooko's Triangle

## License

Public domain.

()) <- in the CAN.
