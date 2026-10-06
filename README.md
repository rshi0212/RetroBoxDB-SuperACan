# RetroBoxDB SuperACan

English | [中文说明](README.zh-CN.md)

Single-file SQLite preservation database for Funtech Super A'Can. The public Catalog holds metadata only (checksums, DAT and provenance records, header fields, archive recipes and the processing code); it contains no ROM data and cannot restore files. The populated database stays local.

| Item | Value |
| --- | --- |
| Original size | 12 source ZIPs, 11.7 MiB (No-Intro 12); 13 ROM files, 22.5 MiB uncompressed |
| Stored size | populated database 12.5 MiB; public Catalog 1.7 MiB (no ROM data) |
| Ratio | 106.8% of the source ZIPs, 55.6% of the uncompressed ROM files |
| Technology | storage v4: SHA256-deduplicated 64 KiB blocks packed in No-Intro family order into solid LZMA2 groups of up to 32 MiB (32 MiB dictionary); per-block SHA256 and per-object CRC32/MD5/SHA1/SHA256 verification; source ZIPs reproduced byte-for-byte from TorrentZip plans |
| Export performance | Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, Python 3.14.4, all checks included. whole newest-DAT set with `export_set.py` (10 files, each checked against the DAT hashes): 20.0 MiB/s, 88 ms per file on average; single file with a cold cache (the group is decoded up to the file): ROM 0.252 s, TorrentZip 0.412 s on average |

## Downloads and documents

| File / document | Content |
| --- | --- |
| [RetroBoxDB.SuperACan.Catalog.sqlite](https://github.com/rshi0212/RetroBoxDB-SuperACan/releases/latest/download/RetroBoxDB.SuperACan.Catalog.sqlite) | Public Catalog (Release asset with `SHA256SUMS`) |
| [Storage v4 guide](RetroBoxDB.Storage-v4.en.md) / [中文](RetroBoxDB.Storage-v4.zh-CN.md) | Storage evaluation, contents, RA, names and maintenance for every platform |
| [Technical design](RetroBoxDB.Storage-v4.Technical-Design.en.md) | Storage format, platform adapters, incremental updates, verification |
| [RA list](reports/ra-supracan-games.csv) / [summary](reports/ra-supracan.json), [build report](reports/supracan-build-report.json), [audit resolution](reports/audit-resolution-20261004.md) | Detailed data |

## Storage choice and platform specifics

6 block/group combinations measured on the whole local collection (`assessment/data/storage-experiment-supracan.json`): smallest 512 KiB / 32 MiB at 9.40 MiB; by the rule (within 0.5% of the smallest, the smallest block, then the smallest group) 64 KiB / 32 MiB at 9.44 MiB. ZIPs 11.72 MiB, per-file LZMA 9.67 MiB.

- Super A'Can cartridges (0.5–3 MiB) define no internal header; every ROM stays `unclassified` and only the file is recorded. Two local DAT versions are imported and diffed.
- RetroAchievements has no Super A'Can console; the RA report is empty.
- No Chinese name source exists yet; names can be added later with `tools/update_db.py --names`.

## Contents

| Item | Value |
| --- | --- |
| ROM records / games / releases | 13 / 12 / 12 |
| DAT coverage per version | 20240927-111000: 13/13; 20260913-064553: 10/12 |
| Local ROMs in no DAT | 0 |
| ROM files of the RetroAchievements set | in a No-Intro DAT 0, RA only 0, hash not in the latest RA snapshot 0 ([list](reports/ra-supracan-collection-unknown.csv)); RA games still without a local ROM: [gap list](reports/ra-supracan-missing.csv) |
| No-Intro DB Export + Dump Log 20260913-064553 | 12 archives, 15 file identities, 12 documented hardware assertions; Dump Log Verified 0 |
| RetroAchievements | not supported by RetroAchievements (no console, no snapshot) |
| Chinese names | 0 of 0 rows translated (0 unique); 0 local ROMs have a Chinese name |
| Populated-database audit | 17 objects, 1 groups, 16 archive plans, all passed |

Every source ZIP is reproduced byte-for-byte from its TorrentZip plan (`v_file_checksums.exported_bytes_equal_source`).

## Usage

```bash
# Query-only audit with the Catalog's embedded engine (also: stats, checksums FILE_ID, help)
python3 -B -c 'import sqlite3,sys; c=sqlite3.connect(sys.argv[1]); s=c.execute("SELECT content FROM resources WHERE name=?",("engine.py",)).fetchone()[0]; c.close(); exec(compile(s,"RetroBoxDB:engine.py","exec"))' ./RetroBoxDB.SuperACan.Catalog.sqlite audit
# Populated database: export by DAT version, 1G1R, RA achievements, TorrentZip or plain ROMs
python3 -B tools/export_set.py RetroBoxDB.SuperACan.sqlite OUT --set 1g1r --ra achievements --container torrentzip --layout ra-category
# Add new DATs, DB Export / Dump Log snapshots, ROMs and an RA snapshot incrementally
python3 -B tools/update_db.py RetroBoxDB.SuperACan.sqlite --discover --ra --catalog RetroBoxDB.SuperACan.Catalog.sqlite
```

Python 3.10+ standard library only. `engine.py` and the other `resources` entries are executable code; run them only from a database you built or a Release asset whose SHA256 you verified. Releases are produced by `.github/workflows/publish-catalog.yml`: it starts from the base Catalog pinned in `release/catalog-release.json`, injects the engine and documents of this commit, checks every data-table digest, runs the tests and the audit, then publishes.
