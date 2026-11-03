# Project Integration Note

Destination: https://github.com/mysticalsin/clicky
Inspected baseline: e71105ce091d71dff4d89cf5d8d37ea1df460961
Documentation branch: docs/heyclicky-reconstruction-handoff

The existing project already has a macOS SwiftUI/AppKit menu-bar companion, floating overlay, voice pipeline, screen capture, and cursor pointing. The next agent must extend these systems, not scaffold a second app or replace the architecture.

Read AGENT_IMPLEMENTATION_BRIEF.md first. Build order: P0 baseline/safety; P1 physical pointer; P2 companion flight/follow; P3 spatial trail; P4 drawing/reveal; P5 speech/drawing timeline; P6 guided targets; P7 broader agent/browser/integration/routine systems.

Critical evidence boundaries: fading spatial paint is tied to push-to-talk user pointing; autonomous Drive trail is not established. Recovered Windows constants are not proof of identical macOS behavior. Synthetic renderer tests are not native parity. This repository is macOS-only. No app source was changed in this documentation integration. No deployment, release, permission change, or secret change is authorized.
