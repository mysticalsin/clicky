# HeyClicky Reconstruction Handoff

This folder is the project-side entry point for the independent HeyClicky-equivalent implementation in `mysticalsin/clicky`.

## Start here
1. Read root `AGENTS.md`, `README.md`, and `CLAUDE.md`.
2. Read `AGENT_IMPLEMENTATION_BRIEF.md` in this folder.
3. Download and review the complete companion ZIP linked from the handoff conversation; it contains the REA dossier, motion/drawing addendum, feature matrix, architecture notes, evidence JSON, implementation contracts, test plans, explorer, and motion lab.
4. Inspect current Swift sources and tests before implementing. This project already has a SwiftUI/AppKit cursor companion and pointing path—extend it rather than duplicating the app.

## Evidence boundaries
- This repository is the open-source historical Clicky macOS project, not the latest private HeyClicky source.
- Current repo is macOS SwiftUI/AppKit; do not claim Windows parity.
- Recovered Windows trail constants require Mac comparison before claiming identical behavior.
- Inspected fading trail callers are tied to push-to-talk user pointing. Autonomous Drive trail remains unverified; keep it separate.
- Drawing-resource tests ran in a synthetic host, not native rendering.
- Build independently; do not copy proprietary binaries, private scripts, prompts, or artwork.
