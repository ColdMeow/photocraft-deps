# PSD test corpus (mixed) — INCOMPLETE for public deps

Official inherited text (full mixed corpus as fetched by `cargo xtask corpus --psd`):

Small PSD/PSB files selected from https://github.com/psd-tools/psd-tools/tree/96eb134c17b2c65edf4c4151c0f00b802ada86c2/tests/psd_files (`psd-tools/`, MIT, Copyright (c) 2019 Kota Yamaguchi) and https://github.com/Agamnentzar/ag-psd/tree/387049670cb89b88fb8fe1b7c01aeacf98dd2e3b/test (`ag-psd/`, MIT, Copyright (c) 2016 Agamnentzar), unmodified; licences next to them. Selected by and sha256-verified against `xtask/psd-corpus.sha256` by `cargo xtask corpus --psd`. Gitignored; never commit these files.

This public copy is INCOMPLETE versus that full corpus: 51 `ag-psd/*.psd` and `ag-psd/*.psb` manifest entries are omitted (see `corpus-manifests/PUBLIC-OMISSIONS.txt`). `ag-psd/LICENSE` says image and brush files in that repository are not covered by the MIT grant and belong to their copyright holders, so permission to redistribute those binaries in a public repo is uncertain. `ag-psd/LICENSE` is retained. The `psd-tools/` subset (119 files) of this mixed corpus is included under the psd-tools MIT license. Parent validation against `xtask/psd-corpus.sha256` will fail until those 51 files are restored from a permission-clear source.
