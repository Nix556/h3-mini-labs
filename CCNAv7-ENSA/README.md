Project structure

- configs/base/: Full base configs for routers/switches. Paste these if you want a complete router config.
- configs/ospf/: Full OSPF blocks per-router for reference or bulk application.
- patches/: Paste-able CLI snippets for each phase/step (numbered prefixes for ordering).
- phases/: Optional grouped snapshots for each lab phase.

How to apply a patch

1. Connect to the router in privileged exec.
2. Paste the desired patch file contents (copy the file and paste into the router terminal).
3. Verify with `show running-config | section router ospf` and `show ip ospf neighbor`.

Recommended workflow

- Use `patches/01-...` files to apply incremental changes during lab exercises.
- Keep `configs/base/` as a reference or to quickly restore a known-good config.
- Update `CHANGELOG.md` whenever you add new patch files.
