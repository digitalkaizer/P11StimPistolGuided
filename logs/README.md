# Preserved diagnostic logs

These logs/hashes are preserved so the summarized findings in `docs/RESEARCH_FINDINGS.md` can be audited against actual probe output.

## Committed logs

- `v0.5-complete.log` — first complete self-excluding DataLibrary probe.
- `v0.6-complete.log` — resource-map pair probe; same-index pairs disproved the assumed ProjectileWeapon map.
- `v0.7-complete.log` — direct ProjectileWeapon record probe.
- `v0.11-UNSAFE-crash-session.log` — final mapped/image scan session associated with white flickering and CTDs.

## Larger / historical logs retained by hash only

The full v0.8.1 inline context capture is useful evidence but mostly consists of repeated raw hex dumps. Its key result is reproduced in `docs/TECHNICAL_NOTES.md`: four separate `75/150/300` hits had byte-for-byte identical surrounding contexts, with `400/600/800` immediately preceding the zeroing-like vector.

The full v0.4 contaminated log is **4,599,220 bytes** of mostly misleading self-match output, so it is intentionally not committed. v0.1-v0.4 live-address conclusions are retracted.

## Provenance hashes

| File | Size | SHA-256 |
|---|---:|---|
| v0.1.1-region-scan.log | 4,295 | `024b40977aa22a964e3db95adb31b1c0cc58ff0e2a75be71e2e555ab09579066` |
| v0.1.1-self-match-example.log | 1,473 | `a015b9dc04b0fbb4baadf45e349a506ddb8926092c4a108d9aef8e53caf26df3` |
| v0.4-CONTAMINATED-self-match-heavy.log | 4,599,220 | `c1e206b89b467cc5c4e8d9f88649308f43cb8406bd179ca9472f3eaa5bd2ad8d` |
| v0.5-complete.log | 3,333 | `8c639588679ef71a694274eea1095d04ff18b778b104430084572977c97ca4ee` |
| v0.5-incomplete.log | 2,917 | `f10b3fb805ecf94c2ce3746b931ca37f13a64a92c6d6a3fae6f6eaa3f515e85f` |
| v0.6-complete.log | 2,340 | `97dd339c8173c68e477ba1fa52b4eb861b1c4a7df24beced1d0fb2782928ca1a` |
| v0.7-complete.log | 2,436 | `12c14ed97de1337d44139fbef16d3584d2ecd84c1fb29c60981e46c234b30d43` |
| v0.8.1-inline-context-complete.log | 25,667 | `cffd260a14e856cb1964d508a3976710e2f40e66e03b8387af512f22d1071b13` |
| v0.11-UNSAFE-crash-session.log | 1,599 | `03f3a7f28183c7cea7030eb6ea19c05302bf1fe1beca2b2e21dcf60985b48195` |
