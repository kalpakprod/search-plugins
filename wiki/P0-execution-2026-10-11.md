# P0 execution report — 2026-10-11

## Scope and correction

This report executes the P0 triage from `Tracker-repair-roadmap.md`. It does not call RuTracker public unresolved: `rutracker_public.py` is a project-developed, live-qualified adapter that emits real magnet rows. Its direct account adapter remains a separate compatibility artifact.

No P0 source is changed without evidence of a current public route and real torrent delivery. A topic page, opaque ID, direct-file page, Cloudflare challenge, or timeout is not a torrent result.

## Results

| Source | Evidence | Decision |
| --- | --- | --- |
| Bit Search | `https://bitsearch.to/search?q=Ubuntu` fresh ordinary request: 15 s, 0 bytes, timeout. Audit also records `bitsearch.to` timeout and the maintained `.eu` API reaching origin `522`. Existing parser expects the `.to/search` HTML contract. | **BLOCKED: transport/origin.** No invented mirror or API. Revisit only after a maintained endpoint responds. |
| GloTorrents | Maintained source points to `https://glodls.to/search_results.php`; fresh TLS request ended with OpenSSL unexpected EOF. Audit's private metadata-only browser check produced no native output. | **BLOCKED: current origin/transport.** No parser rewrite can be validated. |
| MagnetDL | Fresh `http://www.magnetdl.com/` redirects to HTTPS, then 15 s with 0 body; audit's HTTPS/browser check recorded origin `522`. Both existing variants point to the same `.com` source. | **BLOCKED: origin timeout.** No confirmed current mirror. |
| PediaTorrent | Fresh `https://pediatorrent.com/?s=Ubuntu` returned `HTTP 403`, `cf-mitigated: challenge`; audit's browser check recorded origin `522`. | **BLOCKED: anti-bot/origin.** No challenge bypass or fake rows. |
| Pirateiro | Audit confirms `HTTP200` topic search but no magnet/infohash fields; ordinary and private first-party detail routes timed out. | **SEARCH-ONLY / delivery blocked.** No plugin emitted until a real metadata route is proven. |
| Ali213 | Audit confirms current catalogue pages but no direct magnet/torrent route in two native Cyberpunk pages; old `downUrl` is absent and the tested delivery host was unreachable. | **DELIVERY UNRESOLVED.** Inspect only a new declared native route; do not relabel direct files. |
| AudioBook Bay | Audit source imports, but canonical access returned `URLError` and two queries produced no rows. | **ACCESS UNRESOLVED.** Check canonical domain/maintained source later; no code change yet. |

## Lampac/JacRed compatibility lane

Branch: `compatibility/lampac-public-index`.

Porting boundary:

- allowed: public index request contract, response parsing, exact tracker membership filtering, valid hash/magnet checks, title/size/seed/peer/date mapping;
- not allowed: server crawler credentials, tracker accounts, cookies, session exports, CAPTCHA solving, or access-control bypass;
- output must identify the actual tracker membership and must not relabel another tracker.

The known Lampac NextGen/JacRed public contract exposes `Tracker`, `Title`, `Size`, `MagnetUri`, `Seeders`, `Peers`, and `PublishDate`. RuTracker public already uses this evidence-backed pattern. The next branch work is to port only the anonymous public-index parser for Kinozal, Toloka, and NNM-Club where current endpoint evidence remains valid.

## PR decision

No public P0 repair PR is opened from this run: all sources either failed transport/access or lack verified delivery. Opening a parser-only PR would make the repository claim functionality that was not demonstrated.

When a source becomes reachable, use one PR containing:

1. standalone `unofficial/<stem>.py`;
2. meaningful offline parser/delivery regression;
3. verification record and catalogue row;
4. targeted test output and one serialized live Nova result;
5. source/license/attribution and explicit limits.

Expected commit sequence:

```text
Repair <source> public parser and delivery
Add <source> offline regression
Record <source> live verification
```

No secrets, cookies, payloads, session exports, or browser caches belong in the PR.


## Compatibility endpoint recheck

On 2026-10-11 the Lampac public endpoint from `ModInit.cs` returned HTTP 200 JSON for query `Дюна` in 5.37 seconds. It contained 239 rows, including 60 rows tagged `kinozal`, 17 tagged `toloka`, and 67 tagged `nnmclub`; each sampled row had a real `magnet:?xt=urn:btih:` value and numeric size/seed/peer fields. This revalidates the existing three public adapters; it is not a direct authenticated tracker search.

The compatibility branch therefore has no new production adapter to add: the three adapters already exist and their shared parser contract is covered by `test_three_anonymous_searches_filter_exact_tracker_validate_hash_and_keep_metadata`. The remaining work is maintenance/endpoint rotation when the public index operator changes the endpoint.


## Adapter-level live verification

The three existing Lampac public adapters were run through the real plugin entry point, one at a time under the verification lock, with query `Дюна`:

- `kinozal_public.py`: 60 rows, 60 valid BTIH magnets.
- `toloka_public.py`: 17 rows, 17 valid BTIH magnets.
- `nnmclub_public.py`: 67 rows, 67 valid BTIH magnets.

This confirms the adapters are not report-only stubs. They filter exact `Tracker` membership and reject invalid hashes before printing rows.

BitSearch's explicit HTTP route only returned a `301` to HTTPS; the HTTPS request then timed out without a body. This is an origin/transport limitation, not evidence that the old HTML parser is correct for a future endpoint.
