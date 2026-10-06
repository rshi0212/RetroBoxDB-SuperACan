SuperACan Catalog, storage v4 (64 KiB blocks, 1 solid LZMA2 group of up to 32 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- First release of this platform.
- RetroAchievements has no console for this platform; its report is empty. No Chinese name source exists yet.
- One schema for all twenty-three platforms: the header tables of the eight new platforms (Game Gear, PC Engine, SuperGrafx, MSX, MSX2, Virtual Boy, Game & Watch, Super A'Can: `pce_hardware`, `msx_hardware`, `vb_hardware`) and `rom_annotations` exist in every Catalog; tables of other platforms and provider tables have no rows.
- RetroAchievements reports look up sibling databases (NES<->FDS, SNES<->Satellaview, WonderSwan<->WonderSwan Color, NeoGeo Pocket<->NeoGeo Pocket Color, PC Engine<->SuperGrafx, MSX<->MSX2): a game whose ROM is stored there is `local_other_platform`, not a gap.
- Source: 12 ZIPs (nointro 12), 11.7 MiB (13 ROM files, 22.5 MiB uncompressed). Populated database: 12.5 MiB (106.8% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 13 ROM records, 12 games, 12 releases; DAT versions: 20240927-111000, 20260913-064553.
- RetroAchievements: not supported for this platform.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole newest-DAT set with export_set.py 20.0 MiB/s (10 files); single file with a cold cache 0.252 s (ROM) / 0.412 s (TorrentZip) on average.
- Full audit of the populated database: 17 objects, 1 group, 16 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-SuperACan/blob/main/README.zh-CN.md)
