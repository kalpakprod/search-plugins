# Tracker repair roadmap

**Plan date:** 2026-10-11  
**Evidence basis:** `wiki/Plugin-verification.json` (147 records, checks through 2026-10-10), the mediawiki catalogue, and the current standalone test command. This is a triage roadmap, not a claim that every tracker on the Internet was live-tested today.

## Current inventory

The repository has 65 standalone plugin files and 147 audited source records. The audit separates public, snapshot, service, private, archive, and unresolved sources. A zero-row search is not treated as proof that a tracker is closed.

Already qualified public sources should receive maintenance only, not speculative rewrites. They include the current public/index adapters and direct repairs recorded in `Plugin-verification.json` (for example 1337x, Nyaa, RuTracker public, NNM/Toloka/Kinozal public-index variants, ThePirateBay, TorrentDownload, TorrentGalaxy `.one`, YourBittorrent, and the 2026 repair batch).

## Priority queue

### P0 — high-value unresolved public sources

Attempt only where the source can produce genuine torrent metadata or a magnet without an account or guessed URL.

| Source | Current evidence | First action | Expected outcome |
| --- | --- | --- | --- |
| Bit Search | Current probe timed out after 40 s; no usable rows | Inspect current HTML/API and maintained Jackett definitions; bounded native request | Repair or precise transport/site blocker |
| GloTorrents | Two public queries returned no rows | Check current domain/mirrors and maintained parser; validate a known current title | Standalone repair only with real infohash/magnet |
| MagnetDL (both variants) | Legacy variant raises `IndexError`; category variant has no rows | Verify domain status and current routes before coding | Repair, archive, or blocked report |
| PediaTorrent (both variants) | Legacy error/no rows; current native probe was HTTP403/origin522 | Inspect current source and legitimate public fallback | Do not emit fake rows; likely blocker unless a maintained route exists |
| Ali213 | Catalogue reachable but tested pages had no direct torrent delivery | Map current download controls and host; do not relabel direct files | Repair only if a real torrent route is public |
| AudioBook Bay | Source imports, but access returned `URLError` and no rows | Check current canonical domain and maintained implementations | Repair or evidence-backed unavailable |
| Dark-Libria | Import works, two queries return no rows | Verify current API/domain and category query | Repair or blocked |
| Pirateiro | 17 topic rows, but no magnet/hash; detail delivery timed out | Inspect detail/download route and Jackett equivalent | Repair only after metadata delivery proof |

### P1 — high-interest sources with a concrete site obstacle

| Source | Evidence | Roadmap |
| --- | --- | --- |
| DODI Repacks | Current topics exist; delivery hosts return 403/404 or human verification | Re-check only with ordinary guest flow; never solve CAPTCHA or invent torrent URLs. Keep dated snapshot separate. |
| FitGirl Repacks | Current search reached origin 504; historical snapshot is not current | Verify alternate first-party feed or RSS; otherwise document blocked. |
| GOG-games | Native site announced shutdown; alternatives inspected were discussion/direct-file delivery | Close as unavailable unless a public BitTorrent endpoint is evidenced. |
| TomaDivx | Native and browser probes reached origin 522/timeouts | Inspect maintained source/mirror once; no blind retries. |
| CalidadTorrent | HTTP403/origin523 | Check maintained Jackett route and current mirror; likely blocker. |
| RockBox | Search timed out | Verify canonical service and RSS before considering a parser repair. |
| Torrenflix, Nyaa.pantsu, Demonoid, BTSow, iDope | Legacy or native source has no qualified current delivery | One provenance/domain pass, then archive with exact evidence. |

### P2 — private, account, service, or network-bound

Do not attempt anonymous bypasses. Keep these as separate compatibility lanes:

- Account/cookie sources: BakaBT, C411, FileList, GazelleGames, IPTorrents, Kinozal direct, Lat-Team, LostFilm direct, Milkie, nCore, Redacted, RuTracker direct, Tapochek, TorrentLeech, UnionFansub, YggTorrent, and similar records.
- Service/API sources: TorrentClaw requires an API key for delivery; Jackett and Prowlarr require a user service/configuration.
- Overlay/network source: YGGtracker requires Yggdrasil (`0200::/7`).
- Archived/retired sources: Zamunda.RIP, CloudTorrents, SolidTorrents, original TorrentGalaxy `.to`, and original Nyaa/Pantsu variants.

A future private-source PR requires explicit owner-provided test access and must not log credentials, cookies, or session files. It is not part of the public repair queue.

## What each repair must contain

1. Read the exact source, caller contract, attribution, license, and captured failure.
2. Confirm the current canonical route using a bounded guest request or a maintained public implementation. Never guess a mirror.
3. Implement one independently installable `.py` under `unofficial/`, with class name matching its file stem and no new runtime dependency.
4. Preserve source attribution and license; add a version bump and a precise coverage/limitation note.
5. Add the smallest offline regression through the public search/download interface. Assert meaningful title, category, hash/magnet, and metadata identity; do not test only “does not crash”.
6. Run the targeted standalone command and one serialized live Nova check. Verify one real metadata/magnet delivery path where the source claims delivery.
7. Record the result in `wiki/Plugin-verification.json`, `wiki/Plugin-verification.md`, and the catalogue. Mark blocked, snapshot, private, and working distinctly.

## PR and commit plan

Use one PR per coherent repair batch, not one PR per plugin:

1. **PR-1 transport and parser repairs:** one or two P0 public sources with shared failure shape. Include source, tests, fixture, verification record, and catalogue row.
2. **PR-2 delivery repairs:** sources whose search works but magnet/torrent resolution fails (currently Pirateiro and similar). Keep delivery proof separate from search proof.
3. **PR-3 evidence-only closure:** unresolved sources that have a precise 403/522/404/account/API obstacle. No speculative code; update the report and catalogue.
4. **PR-4 private compatibility:** only with explicit credentials/test authorization; never mix with public repairs.

Keep commits reviewable:

- `Repair <source> parser and delivery`
- `Add <source> offline regression`
- `Record <source> verification`

Before opening a PR:

```text
PYTHONDONTWRITEBYTECODE=1 python3 -m unittest test_rutracker_public test_x1337x test_unofficial_updates test_public_indexes test_direct_trackers test_private_plugins -v
git diff --check
git status --short
```

The PR description must state: source URL, original implementation and license, changed route, query/category used, result count, magnet/infohash or metadata evidence, test command, browser/runtime requirements, and known limits. Do not include secrets, cookies, payload files, or session exports.

## Stop conditions

Stop coding a source when its current public path is account-only, API-key-only, a CAPTCHA/anti-bot challenge that cannot be completed through ordinary guest behavior, a dead origin, or a direct-file route without torrent metadata. Record the exact evidence and move to the next source. Never turn a topic URL, opaque ID, or generic download page into a torrent result.

## Next execution order

1. Bit Search and GloTorrents: highest likely payoff, unresolved but not proven closed.
2. MagnetDL and PediaTorrent: verify whether any current public route remains before implementation.
3. Pirateiro: delivery-only investigation because search rows already exist.
4. Ali213 and AudioBook Bay: current-domain and delivery mapping.
5. DODI/FitGirl/TomaDivx/CalidadTorrent/RockBox: one bounded route check each, then close or repair.
6. Update the catalogue and verification records; run the targeted suite; open the smallest coherent PR.
