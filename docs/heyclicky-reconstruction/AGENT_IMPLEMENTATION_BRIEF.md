# HeyClicky Reconstruction — Agent Execution Brief

## Mission and destination
Extend this existing repository into an independent, high-fidelity HeyClicky-equivalent desktop companion. This is implementation, not a mockup. Preserve current behavior, naming, signing, permissions, Cloudflare proxy, and user data. Do not replace the app wholesale.

Repository: mysticalsin/clicky. Inspected baseline: e71105ce091d71dff4d89cf5d8d37ea1df460961. Documentation branch: docs/heyclicky-reconstruction-handoff. Existing platform: macOS SwiftUI/AppKit, Xcode project leanring-buddy.xcodeproj, source leanring-buddy/, Worker worker/. Keep intentional leanring spelling and scheme. Recheck current HEAD and working tree before edits. AGENTS.md is authoritative. Do not run terminal xcodebuild; project instructions warn it can invalidate TCC permissions.

## Read first
1. Root AGENTS.md, README.md, CLAUDE.md.
2. docs/heyclicky-reconstruction/README.md.
3. Full companion ZIP: agent brief, REA dossier, motion/drawing addendum, evidence JSON, feature matrix, source notes, implementation contracts, test plans, explorer and motion lab.
4. Inspect existing Swift source/tests before changing anything.

## Provenance boundary
Build independently from observed behavior, documented mechanics, and original code. Do not copy HeyClicky's proprietary binaries, private scripts, prompts, artwork, brand assets, or server implementation. Public farzaa/clicky source is MIT-licensed historical reference; retain its license/attribution if reusing code. Do not represent this as the official product.

## Hard engineering rules
- Inspect first; create baseline inventory/gap matrix; extend existing components rather than duplicating them.
- Keep changes scoped, reversible, and on a feature branch. No unrelated refactors, dependency churn, deployment, secret rotation, permission changes, or production migrations.
- Never hardcode provider secrets; preserve Cloudflare Worker proxy.
- Every external action needs ownership, timeout, cancellation, recovery.
- Every mouse/keyboard action is attributable to agent or user. Never swallow user input or leave a button held.
- Stop revokes active control generation before cleanup; reject late events from stale generations.
- Label observed, inferred, proposed, and unverified behavior separately.
- Add tests with each meaningful change. Respect TCC/xcodebuild restriction; use Xcode UI for native build/run.
- Update AGENTS.md only when architecture/key-file responsibilities materially change.

## Build order

### P0 — Baseline and native safety
Map menu-bar panel, overlay, permissions, screen capture, audio, transcription, TTS, and pointing pipeline. Produce architecture gap matrix. Add feature flags and safe fallback. Establish coordinate-space service for logical points, backing pixels, display IDs, scale, bounds, negative virtual-desktop origins, and per-display transforms.
Pass: existing app and push-to-talk → response → TTS → pointing remain functional; tests do not change TCC state.

### P1 — Physical pointer actuator
Separate real pointer control from decorative companion. Implement deterministic glide, configurable duration/sample count/easing, monotonic clock, rounding/clamping, explicit drag down/move/up, cancellation before every emission. Windows reference: N=max(8,trunc(60D)), piecewise cubic easing, midpoint-to-even rounding, virtual-desktop clamp, absolute input normalization; 450 ms ordinary positioning, 300 ms before scroll. Compare with Mac rather than blindly porting. Physical user input immediately takes ownership; cleanup releases every owned button.
Pass: endpoint/midpoint, rounding, negative origins, mixed scales, stop-before-click, stop-during-glide, stop-after-button-down, stop-during-drag, disconnect, guaranteed-release tests; native trajectory/no-stuck-button test.

### P2 — Companion flight/follow
Independent animation; never drives OS pointer. Follow spring reference response 0.2 s, damping 0.6, offset (35,25) logical units, retained velocity. Flight: quadratic Bézier, smoothstep position, tangent-derived rotation, independent scale/glow. Reference lift min(0.2*d,80), 750 ms quantized flight in 600-unit fixture, scale 1→1.3→1, glow 8→14→8. Preserve landing tangent through return-to-rest; rebase on display changes.
Pass: deterministic samples, angle continuity, bounded motion, retarget, display unplug/replug, reduced motion.

### P3 — Spatial gesture and fading trail
Separate capture, semantic context, smoothing, rendering. Windows reference: activation after 8 logical units from hold origin; sampling opportunity ~14.67 ms; minimum retained spacing 1.5; dwell radius 12 and qualification 350 ms; capture display ID/scale at gesture start; centripetal Catmull–Rom, extrapolated endpoints, <=48 subdivisions; constant 8-unit stroke; age alpha max(0,1-ageSeconds) over 1 s; parent group opacity 0.55. Preserve raw points/dwell anchors after visible ink fades. Stopping watcher hides ink immediately but returns semantic gesture context.
Evidence boundary: inspected fading paint callers connect to push-to-talk user pointing. A separate autonomous Drive trail is NOT established as the same renderer. Do not claim parity; keep separate and only reproduce after direct reference evidence. A proposed trail for moving/waiting/dragging/stopped is an original enhancement.
Pass: stationary hold no stroke; threshold/jitter/fast motion/dwell/scale-change tests; hide-on-release; semantic context retained; parent-level opacity compositing (no dark joints); one-second fade; interruption behavior.

### P4 — Drawing geometry/reveal
Separate deterministic mark model, geometry, pen travel, reveal mask, glow, speech cues, edit lifecycle. Screen annotation reference: 3.5 logical-point width, round caps/joins, glow opacity 0.65, blur 12; do not assume paper/card/tie-arrow share this style. Deterministic irregular outlines, subdivided edges, bounded bow, harmonic ellipse wobble, stable per-mark seed; repaint/recolor preserves geometry. Reveal via centerline mask, normalized progress, dash/trim, endpoint treatment.
Stroke duration reference clamp(0.25 + L/1400, 0.3, 1.4) seconds; reveal cubic Bézier (0.35,0.2,0.55,0.95). Pen travel: 450 ms first eligible mark; jump <110 units no flight; >=110 adds min(0.55,0.2+d/2400) seconds; paced painter 20 ms lead. Distinguish code arrival, cue application, mark release, pen landing, ink start/end, audio schedule, interactive readiness. Keep pause, stop, settle, native finish, rush finish, clear distinct. Erase reverse order; reference base duration 400 ms × active pace, 50 ms stagger.
Pass: 109/110 boundary, deterministic seed, multi-mark, repaint/recolor, pause/stop/settle, reverse erase, delayed code, early/late speech cues, overlaps, long narration, mid-stroke interruption, clear/restart.

### P5 — Speech/drawing timeline
Do not sync only to nominal speech duration. Coordinate painter constraints and pen landing. Apply cached start/end cues atomically before scheduling marks. Prevent first mark committing against estimated narration length when final cue is available. Keep visible progress, audio schedule, debug estimate distinct. Use synthetic regressions: crowded 3-stroke/1-second narration and speech-before-code. Preserve observations; improvements do not rewrite evidence.
Pass: no unintended simultaneous starts; no ink past explicit end unless policy allows; coherent audio/ink under delay, dropped chunks, pause/resume/cancel.

### P6 — Guided targets
Implement target ring/highlight, delayed label, breathing emphasis, screen coordinate transforms, semantic references. Do not conflate target highlight with spatial paint or companion flight.
Pass: display/scale, focus change, target replacement/disappearance, reduced motion, accessibility labels.

### P7 — Agents/browser/integrations/routines/files
Only after native control primitives are stable: separate persistent assistant identity from jobs; event IDs, resumable streams, stop/retry/steer, approvals, artifacts, usage accounting, schedules, browser-tab ownership, integration health.

## Suggested module boundaries (adapt after inspection)
PointerControlCoordinator (ownership/generation/cancel/cleanup); PointerActuator; CoordinateSpaceMapper; CompanionMotionController; SpatialGestureCapture; SpatialTrailRenderer; DrawingScene/DrawingMark; DrawingGeometryFactory; DrawingRevealRenderer; DrawingPenScheduler; SpeechDrawingTimeline; InteractionTelemetry. Proposed names, not claims that these types exist.

## Instrumentation
Record monotonic timestamp, turn/job ID, generation, display ID, coordinate space, source (agent/user), action type, target, requested duration, actual start/end, cancellation reason, renderer phase. Do not log screenshots/transcripts/sensitive content by default. Add optional local debug timeline that does not perturb production timing.

## Verification
For each criterion record stimulus, expected/actual output, environment, display scale, app build, evidence capture, pass/fail. Separate static inspection, deterministic unit tests, synthetic renderer tests, macOS native UI tests, future Windows tests, visual comparison. Synthetic pass is not native parity. Repair defects and rerun relevant tests.

## Required agent deliverables
1. Baseline architecture/gap report before code changes.
2. Small reviewable commits on feature branch.
3. Tests for every changed subsystem.
4. Before/after captures with environment metadata.
5. Recovered-vs-implemented-vs-tested matrix.
6. Known gaps: autonomous Drive trail, Mac/Windows parity, TCC, mixed DPI, native timing.
7. Update AGENTS.md only for architectural changes.
8. No deployment/release until separately authorized.

## Definition of done
Existing Clicky baseline works; pointer, companion, trail, drawing, and speech timelines are independently controllable; all actions safely interrupt and return control; geometry/timing deterministic where required; multi-monitor transforms correct; no stuck buttons, stale actions, duplicate drawing, or unintended late speech/ink; native behavior verified on actual supported Mac. Windows parity is future work unless a Windows client is separately implemented and tested. Completion claims cite executed evidence, not plans.
