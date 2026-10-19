# 0002 - The Knode brand identity

**Status:** accepted, 2026-10-19

## Context

Knode needed an identity that feels native, calm and technical without being cold, and that a
community can extend without breaking it.

## Decision

- **Logo:** a K made of two blocks joined by a bridge, on a 10 × 25 half-cell grid with a square stem.
- **One rule:** shapes are built from square cells; every corner, convex or concave, is rounded with half a cell.
- **Colour:** three inks only: ink `#0B0B14`, paper `#F3F2EE`, indigo `#818CF8`.
- **Type:** two in-house grid typefaces, Knode Block and Knode Line, plus Geist and Geist Mono.
- **Language:** English first. Tagline: *Connect your thoughts. Naturally.* Domain: knode.md.
- **Developers:** a sub-brand, Knode Developers, writes the same identity in ASCII for developer surfaces.

## Consequences

- Everything is generated from `brand.json` in `knodemd/brand`, so the system stays consistent.
- New shapes, icons or letters are valid if they follow the cell and radius rule.
