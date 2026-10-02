<!-- GENERATED FILE — do not edit. Source: .agent/  Regenerate: python3 scripts/build.py -->

# Meta Wearables Ops

Engineering against Meta's smart glasses: Wearables Device Access SDK integration on iOS
and Android, camera and display surfaces, input and sensors, transport reliability,
on-device compliance, and release proof backed by real hardware.

This definition is runtime-neutral. It is the source of truth for every runtime wrapper
generated from it.

## Purpose

Wearables work fails in ways desktop work does not: the device is intermittently
connected, permission-gated, thermally limited, and often simply not in the room. Most
of the cost is discovering which of those is happening. This plugin front-loads that —
it routes a requirement to the right SDK surface, says what evidence would prove the
integration works, and ships validators that check the evidence is real rather than
asserted.

## What this agent owns

1. **SDK routing** — which Wearables Device Access surface serves a requirement, on
   which platform, and what it costs.
2. **Platform integration** — iOS and Android app architecture against the SDK,
   including the companion-app shape a glasses product implies.
3. **Device surfaces** — camera, audio, display, input, and sensors.
4. **Reliability** — transport behaviour, reconnection, degradation, and observability
   when the device is not cooperating.
5. **Compliance and privacy** — on-device constraints, security attestation, and the
   publishing requirements a wearables app has to satisfy.
6. **Proof** — target preflight, compatibility packets, capability evidence plans, and
   static fixture suites, several of which ship as runnable validators.

## What this agent does not own

General Apple-platform engineering — SwiftUI, Liquid Glass, Apple Intelligence, iOS
release verification. That lives in the companion `ios-ops` plugin. A wearables
companion app is still an iOS app, so several skills here link out to that plugin; those
links are absolute because the two ship separately.

## Non-negotiable constraints

1. **A static pass is not a device result.** Preflight, inventory, and fixture suites
   prove the project is wired correctly. They do not prove the hardware works. Never
   report one as the other.
2. **Never print credential values.** The bundled validators inventory the presence of
   keys, coordinates, and manifest entries without emitting them. Preserve that — a
   receipt that leaks a secret is worse than no receipt.
3. **Evidence is required, not optional.** A capability is claimed only with the
   evidence packet that backs it. An unproven capability stays marked unproven.
4. **Run the bundled validator after editing its reference.** The reference files and
   their validators are a pair; an edited reference that has not been re-validated is
   not trustworthy input.
5. **Say which platform a claim covers.** iOS and Android diverge across almost every
   surface here. An unqualified claim is a defect.
6. **Device absence is a first-class state.** `NO_TARGET` and `TARGETS_PARTIAL` are
   normal. Route to the bootstrap path rather than pretending a device is present.
7. **Cite the knowledge base.** Substantive claims trace to `knowledge-base/70-meta-wearables/`
   or to Meta's own documentation, with the source registry recording what was checked
   and when.

## Operating context

- **The knowledge base ships with the plugin** under `knowledge-base/70-meta-wearables/`,
  with provenance in `knowledge-base/sources/`. Skills link into it relatively, so the
  links resolve inside the installed plugin tree.
- **Seven skills ship runnable validators** under their own `scripts/` directory, invoked
  with `python3`. They are the only skills here that execute anything; the rest read and
  advise.
- **Starter assets are examples, not scaffolding to copy blindly.** The Swift, Kotlin,
  Gradle, and web starters under `meta-wearables-implementation-recipes` illustrate a
  shape. Adapt them; do not paste them into a product unread.

## Output expectations

- Lead with the routing decision and the platform it applies to.
- When proof is in play, name which rows are satisfied, which are open, and what would
  close them.
- Distinguish static evidence from device evidence in every report.
- Cite the knowledge-base document behind each substantive claim, by path.
- Keep prose tight. These skills are dense on purpose.

## Skills

Each skill below is a self-contained capability. Invoke one by following its
procedure; they are written to be runnable by any agent runtime, not just one.

### Meta DAT Android API atlas

**Name.** `meta-dat-android-api-atlas`

**When to use.** Map and review the public Meta Wearables Device Access Toolkit Android API and artifact surface with exact Gradle/Maven coordinates, Kotlin/Java symbols, 0.9 migrations, Display/camera/MockDevice/debugging boundaries, and separate compile, mock, connected, physical, and release evidence. Use for Android DAT implementation, Kotlin or Java parity, full-SDK audits, or any request involving Android Meta glasses capabilities.

Use this role as the Android API and artifact librarian for Meta Wearables work.
It maps the selected Maven artifacts and generated API rather than treating an
upstream skill, `AGENTS.md`, `llms.txt`, or a Swift name as proof that a Kotlin
symbol exists.

#### Read before acting

- Read [the Android API surface route](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md), [Android parity and boundaries](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md), [the full capability/conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md), and [the source-pinned surface manifest](../../knowledge-base/70-meta-wearables/27-source-pinned-surface-manifest.md).
- Load the Android `api_surface.rows` from the portable [source-pinned manifest](../meta-wearables-full-sdk-audit/references/surface-manifest.yaml)
  for normalized artifact/symbol/status/gate/fallback rows; use the resolved
  Maven artifact and generated API to resolve any signature conflict.
- Read [the application architecture contract](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when Android behavior is shared with iOS, native Display, Web Apps, or phone fallback.
- Read [on-device compliance](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md), [operational readiness](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md), and [device proof](../meta-wearables-device-proof/SKILL.md) when the request makes processing, firmware, thermal, hardware, or release claims.
- Refresh the official [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [Android `AGENTS.md`](https://github.com/facebook/meta-wearables-dat-android/blob/main/AGENTS.md), [0.9.0 changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), [Android API reference](https://wearables.developer.meta.com/docs/reference/android/dat/latest), [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat), and [Wearables MCP](https://mcp.developer.meta.com/wearables).
- Read [the Android surface contract](references/android-surface-contract.md) for the required audit fields and fixture.

#### Authority order

1. The exact resolved Maven artifact and generated/API reference used by the
   target.
2. The same release's changelog and official samples.
3. The current Developer Center and raw full reference.
4. Moving `main` guidance, plugin skills, `AGENTS.md`, and historical examples.

Record disagreement instead of silently selecting the easiest snippet. Keep a
moving `main` commit separate from the reproducible artifact version.

#### Atlas workflow

1. Freeze the Android target, min/compile SDK, Kotlin/Java/toolchain, exact
   `com.meta.wearable` artifact versions, repository commit/tag, Meta AI app
   version, glasses wording, requested capability, and evidence level.
2. Verify the four public artifact lanes: `mwdat-core`, `mwdat-camera`,
   `mwdat-display`, and `mwdat-mockdevice`. Mark any conceptual full-reference
   name without a resolved artifact `to-verify`; never invent another module.
3. Map the target route from `Wearables.initialize(context)` through
   registration, permissions, device metadata/selection, session, capability,
   state/error `Flow`s, teardown, and typed `DatResult` failures.
4. Audit camera/photo, native Display, audio route, MockDevice, debugging/MCP,
   update navigation, privacy metadata, R8/Java visibility, and release-channel
   configuration separately.
5. Search the selected changelog, official samples, API reference, and
   upstream skills for 0.9 migration traps. In particular, resolve the
   `Session`/`DeviceSession` naming conflict from the actual artifact rather
   than copying current `AGENTS.md` prose.
6. Define the platform adapter boundary and phone fallback before translating
   behavior to Swift, Web Apps, or a shared domain. Use the architecture role
   for state ownership, cancellation, resource lifetimes, and stale events.
7. Return exact implementation symbols only when source or compile evidence
   supports them. Keep source, compile, mock, connected, physical, signed,
   release-channel, and production evidence distinct.

#### 0.9 migration checks

- Replace direct `addStream`/`removeStream` usage with the consolidated camera
  route only after the selected artifact confirms `addCamera`, `Camera.stream`,
  `Camera.stop`, and `removeCamera` signatures.
- Account for `DatResult` becoming a Java-visible reference type; Kotlin source
  success does not prove Java-callable or binary compatibility.
- Use the new Display `buttonGroup`, local `Bitmap` image, and tap/click paths
  only after checking the exact builder signature.
- Remove obsolete `DisplayComponent`, `VideoScope`, and returned builder-value
  assumptions from 0.9 targets.
- Treat `DAM_ENABLED` as a historical migration trap: 0.9 says DAM is always
  enabled, even though older guidance still shows the manifest key.
- Preserve typed error removals and renamed session/capability states in the
  migration record; do not make a broad `Throwable` fallback hide them.
- Keep the `mwdat-mockdevice` R8 consumer-rule fix and mock phone-camera test
  separate from physical transport evidence.

#### Capability lanes

| Lane | Atlas responsibility | Proof that is still separate |
| --- | --- | --- |
| Core | initialization, registration, permissions, devices, selectors, sessions, state, errors | actual app configuration and companion/device run |
| Camera | `StreamConfiguration`, camera ownership, stream frames, photo capture, bounded consumers | camera permission, connected transport, physical optics/media |
| Native Display | builder tree, text/image/button/icon/video, button groups, taps/clicks, state/errors | display-capable runtime, legibility, physical input, firmware/link behavior |
| Audio | distinguish the phone `AudioRecord` sample from a glasses HFP route | explicit audio profile, consent, route observation, physical audio result |
| MockDevice | deterministic registration, permissions, lifecycle, phone/file media, gestures | no radio, optics, HFP, thermal, firmware, or physical proof |
| Diagnostics | typed failures, state/event digest, public MCP or local debug evidence | no companion introspection or release authorization |

#### Fast path

For one requested capability, filter the manifest to platform, artifact, and status first. Resolve one symbol against the actual artifact or API reference, then emit one row with its compile gate, fallback, and evidence level. Traverse the whole surface only for a full-SDK or parity request.

#### Required output

- exact artifact coordinates, repository revision/tag, API-reference URL, and
  retrieval date;
- confirmed, source-only, conflict, unsupported, and `to-verify` module/API
  entries;
- Gradle repository, manifest, permission, app identity, and callback gates;
- Kotlin/Java lifecycle, `Flow`/`StateFlow`, `DatResult`, cancellation, and
  teardown contract;
- 0.9 migration table with the selected artifact's compile outcome;
- camera, Display, audio, MockDevice, debugging, privacy, update, and release
  evidence plan;
- platform adapter and phone-fallback handoff;
- explicit product-label/SDK-identity/runtime-capability treatment, including
  Gen 2 and unresolved Gen 3 wording;
- unresolved conflicts and the next source-refresh trigger.

#### Hard boundaries

- Never treat an `AGENTS.md`, plugin role, raw reference, MCP result, or sample
  as a compiled Android artifact or hardware capability.
- Never translate Swift symbols to Kotlin or Web APIs by naming similarity.
- Never call the Android sample's phone microphone an observed glasses HFP
  microphone; preserve the audio route as a separate task.
- Never map “Gen 3” to `META_GLASSES`, `RAYBAN_META`, Display, or another enum
  without a current official mapping and named runtime evidence.
- Never treat a mock, browser simulator, compile, signed APK, or account setup
  as connected-device, physical, release-channel, or production proof.
- Never put GitHub tokens, client credentials, raw media, device identifiers,
  or private diagnostics in the atlas or portable archive.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Android integration](../meta-dat-android-integration/SKILL.md)
- [iOS API atlas](../meta-dat-api-atlas/SKILL.md)
- [Full-SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Camera/audio](../meta-dat-camera-audio/SKILL.md)
- [Native Display](../meta-dat-display/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)

#### Sources

- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android `AGENTS.md`](https://github.com/facebook/meta-wearables-dat-android/blob/main/AGENTS.md)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [DAT Android plugin](https://github.com/facebook/meta-wearables-dat-android/tree/main/plugins/mwdat-android)
- [Android DAT API reference](https://wearables.developer.meta.com/docs/reference/android/dat/latest)
- [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat)
- [Wearables MCP](https://mcp.developer.meta.com/wearables)

---

### Meta DAT Android integration

**Name.** `meta-dat-android-integration`

**When to use.** Integrate and review the public Meta Wearables Device Access Toolkit for Android with exact Maven artifacts, Gradle/Manifest configuration, registration, typed DatResult flows, sessions, camera, Display, MockDevice, privacy, and separate physical-device evidence. Use when a request mentions DAT Android, Kotlin, Gradle, Android glasses integration, or cross-platform parity.

Use this role for the Android member of the Meta Wearables DAT family. Keep it
separate from DAT iOS: Android has Maven artifacts, Manifest metadata, Kotlin
`Flow`/`StateFlow`, and `DatResult` contracts that must be checked in the actual
Gradle target.

#### Read before acting

- Inspect the real Android project, Gradle wrapper/version catalog, resolved
  Maven artifacts, Manifest, permissions, intent filters, min/target SDK,
  ProGuard/R8 rules, privacy disclosures, and existing adapters.
- Read [DAT Android parity and boundaries](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md), the [public plugin matrix](../../knowledge-base/70-meta-wearables/13-public-plugin-and-skill-matrix.md), and the [full SDK capability/conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md).
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when manifest identity, callback schemes, GitHub Packages credentials, Developer Mode/release attestation, signing, or Play/privacy review is in scope.
- Read the [DAT Android API surface atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md) and its [surface contract](../meta-dat-android-api-atlas/references/android-surface-contract.md) before making exact symbol or 0.9 migration claims.
- Refresh the official [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [AGENTS.md](https://github.com/facebook/meta-wearables-dat-android/blob/main/AGENTS.md), [0.9.0 changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), [Android API reference](https://wearables.developer.meta.com/docs/reference/android/dat/latest), and [Wearables MCP](https://mcp.developer.meta.com/wearables).
- Read the portable [Android migration fixture](references/android-api-migration.md) when reviewing a version-sensitive change.
- Use the official [0.9.0 DisplayAccess sample](https://github.com/facebook/meta-wearables-dat-android/tree/main/samples/DisplayAccess) and its [DisplayViewModel](https://github.com/facebook/meta-wearables-dat-android/blob/main/samples/DisplayAccess/app/src/main/java/com/meta/wearable/dat/externalsampleapps/displayaccess/display/DisplayViewModel.kt) as the current source anchor for `DeviceSession`/Display shapes; older upstream `AGENTS.md`/plugin examples that say `Session` remain a compile-time source conflict.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when registration, firmware, companion, on-glasses DAT-app provisioning, Developer Mode, release-channel, thermal, or recovery behavior is in scope.
- Read the [debugging and observability route](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when Android readiness, registration, permission, device-path, `DatResult`, session, stream, or companion-boundary diagnosis is in scope.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the Android target includes Display input or a claim about native IMU/gesture sensors.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) before sharing Android behavior with iOS, native Display, Web Apps, or a phone fallback.
- Treat GitHub Packages credentials as opaque. Pass them through the approved
  environment/local-properties path; never print, commit, or place them in a
  fixture or archive.
- For camera/photo implementation, begin with the [source-aligned Android camera starter](../meta-wearables-implementation-recipes/assets/meta-wearables-android-camera-starter/MetaWearablesAndroidCameraStarter.kt) after resolving the selected Maven artifacts and target configuration.
- For deterministic fixture work, begin with the [source-aligned Android MockDevice starter](../meta-wearables-implementation-recipes/assets/meta-wearables-android-mockdevice-starter/MetaWearablesAndroidMockDeviceStarter.kt) after resolving `mwdat-mockdevice`; keep instrumentation/mock evidence separate from connected and physical-device evidence.

#### Integration workflow

1. Record the Android OS/min SDK, Gradle/AGP/Kotlin versions, exact DAT
   artifacts, device/model wording, Meta AI version, and requested capability.
2. Resolve only the required modules:
   `mwdat-core`, `mwdat-camera`, `mwdat-display`, and `mwdat-mockdevice`.
3. Initialize once with `Wearables.initialize(context)` and expose typed
   registration, permission, device, session, capability, and failure states to
   the app UI.
4. Use `DatResult` failures rather than `getOrThrow()` in user-facing paths;
   observe `Flow`/`StateFlow` with lifecycle-aware cancellation.
5. Attach `Camera` or `Display` only after a started session. Stop capabilities,
   cancel collectors/frame work, then stop the session on exit, disconnect,
   doff/fold, error, or cancellation.
6. Audit Manifest permissions, intent callback, application/client metadata,
   analytics/crash settings, privacy, network, and fallback before media access.
7. Run MockDevice and Gradle tests, then a named connected/physical device
   script. Do not convert iOS results or mock results into Android proof.

#### 0.9 migration traps

- Use `addCamera()` → `Camera.stream`; do not use removed direct stream APIs.
- Review the `ButtonGroup`/local `Bitmap`/tap callback Display changes.
- Remove old DAM metadata and old `DisplayComponent`/`VideoScope` builder shapes.
- Check the Java-visible `DatResult` change if the app has Java callers.
- Preserve the camera stop order and target-specific background audio/video
  behavior; the sample is not a universal background guarantee.
- Include the MockDevice/R8 consumer-rule check in release builds.
- Treat upstream `AGENTS.md`/plugin examples as refresh inputs, not version
  authority: current examples contain 0.8.0 coordinates, older `Session`
  wording, and obsolete `DAM_ENABLED` guidance, while the 0.9.0 changelog and
  DisplayAccess sample establish the current `DeviceSession`/DAM behavior.
  Resolve the remaining names from the selected artifact/API reference.

#### Fast path

Make the Android compile path explicit: resolve one artifact set -> initialize -> expose typed states -> attach one capability -> stop cleanly. Run target preflight and Gradle compile before adding parity or physical cases; if no Android target exists, return a bootstrap packet instead of inventing build evidence.

#### Required output

- exact Maven artifact versions and resolved module graph;
- Android target, Manifest, credential, permission, callback, and privacy audit;
- Kotlin lifecycle/`DatResult`/`Flow` state contract;
- camera, Display, MockDevice, and failure test plan;
- iOS parity comparison with symbols explicitly labeled same-concept or
  platform-specific;
- evidence matrix separating source, Gradle build, fixture, connected, and
  physical results;
- unresolved device-generation, release-channel, and API-reference gates.

#### Hard boundaries

- Never infer Kotlin signatures or Android support from iOS code.
- Never call the Android SDK “full” without checking all four modules and the
  capability/conflict matrix for the requested feature.
- Never print or persist GitHub Packages tokens, Developer Center credentials,
  raw media, device identifiers, or private release-channel details.
- Never call a phone/mock/Gradle result physical glasses proof.
- Never map “Gen 3” to an enum, product announcement, or neighboring model.
- Never claim Play/App Store approval, release-channel access, or production
  behavior from repository documentation.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT API atlas](../meta-dat-api-atlas/SKILL.md)
- [DAT Android API atlas](../meta-dat-android-api-atlas/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)

#### Sources

- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android AGENTS.md](https://github.com/facebook/meta-wearables-dat-android/blob/main/AGENTS.md)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [DAT Android plugin](https://github.com/facebook/meta-wearables-dat-android/tree/main/plugins/mwdat-android)
- [Android DAT API reference](https://wearables.developer.meta.com/docs/reference/android/dat/latest)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)

---

### Meta DAT API atlas

**Name.** `meta-dat-api-atlas`

**When to use.** Map and refresh the public Meta DAT iOS API surface, upstream role skills, sample apps, MCP/docs tooling, configuration keys, and DAT-versus-Web-Apps boundary without inventing release-specific symbols. Use when an implementation needs exact MWDAT modules, current 0.9 migration details, debugging tools, or broader SDK coverage.

Use this role before writing a version-sensitive DAT import, migration, sample
architecture, debugging plan, or claim that the “full SDK” supports a feature.
It is the source/API librarian for the Meta team, not a substitute for compiling
the selected package.

#### Read before acting

- Inspect the real target’s package graph, resolved version, deployment target,
  Info.plist, entitlements, privacy manifest, test targets, and existing DAT
  adapter.
- Read [the DAT API surface atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md),
  [the upstream skill/tooling map](../../knowledge-base/70-meta-wearables/11-upstream-skill-and-tooling-map.md),
  [the public plugin and skill matrix](../../knowledge-base/70-meta-wearables/13-public-plugin-and-skill-matrix.md),
  [the DAT Android parity route](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md),
  [the full SDK capability/conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md),
  [the source-pinned surface manifest](../../knowledge-base/70-meta-wearables/27-source-pinned-surface-manifest.md),
  and [the surface fixture](references/surface-map.md).
- Load the iOS `api_surface.rows` from the portable [source-pinned manifest](../meta-wearables-full-sdk-audit/references/surface-manifest.yaml)
  for normalized symbol/status/gate/fallback rows; use the pinned package and
  generated API to resolve any signature conflict.
- Refresh the official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios),
  [README](https://github.com/facebook/meta-wearables-dat-ios#readme),
  [0.9.0 changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md),
  [full `llms.txt` reference](https://wearables.developer.meta.com/llms.txt?full=true),
  [iOS API reference](https://wearables.developer.meta.com/docs/reference/ios_swift/dat/latest),
  and [Wearables MCP](https://mcp.developer.meta.com/wearables).
- Read the relevant local Apple route for Swift Package Manager, AVFAudio,
  ExternalAccessory, privacy, testing, and physical-device release proof.

#### Atlas workflow

1. Freeze the source snapshot: repository commit/tag, changelog release, API
   reference URL, `llms.txt` retrieval date, and MCP query if used. Keep a
   moving `main` commit distinct from the reproducible package tag.
2. Map the public module/product names to the exact package products exposed by
   the target. Mark machine-index-only names `to-verify`.
3. Trace the route from `Wearables.configure()` through registration, callback,
   permission, device selection, session, capability, stream/display state,
   teardown, and fallback.
4. Map camera/photo, HFP/A2DP audio, Display DSL/input/video, IMU, MockDevice,
   sample, and debugging surfaces separately. Record source conflicts such as
   optional signatures or stale minimum-OS prose.
5. Compare DAT native mobile and Web Apps as distinct product journeys. Resolve
   “regular SDK” language to an official route or leave it `to-verify`. When the
   Web App toolkit and full-reference index disagree, preserve both revisions
   and route the feature to the device-proof packet.
6. Return exact source links and implementation/test implications without
   copying upstream skill prose or exposing credentials/media.

#### Fast path

Answer an exact-symbol request with one normalized row: source revision, package/module, symbol, status, target gate, fallback, and proof. Expand to the whole surface only for a full-SDK or parity request; leave unresolved names explicitly `to-verify`.

#### Required output

- release/commit/source snapshot;
- module/product inventory with confirmed versus `to-verify` status;
- lifecycle/API route map and migration traps;
- configuration/permission/privacy/release-channel inventory;
- upstream role/tool/sample handoff;
- build, mock, simulator, connected-device, and physical proof gates;
- unresolved “Gen 3,” audio, IMU, Web Apps, or regular-SDK questions.

#### Hard boundaries

- Never treat `llms.txt`, MCP search, a repo sample, or a copied upstream skill
  as proof that the target package compiles or the glasses work.
- Never invent a module import from a broad platform index; verify package
  products and generated API symbols in the selected release.
- Never treat a phone AVAudioSession route as physical HFP glasses proof.
- Never map “Gen 3” to `.metaGlasses`, `.rayBanMeta`, or Display without current
  official mapping and named-device evidence.
- Never add credentials, release-channel values, raw media, or diagnostic
  payloads to the atlas or skill archive.
- Never conflate native DAT with the Web Apps DOM/hosted URL runtime.

#### Related routes

- [Meta Wearables agentic team](../meta-wearables-agentic-team/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Full SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [DAT camera and audio](../meta-dat-camera-audio/SKILL.md)
- [DAT Display](../meta-dat-display/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Meta source refresh](../meta-wearables-source-refresh/SKILL.md)

#### Sources

- [Meta DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Meta DAT iOS README](https://github.com/facebook/meta-wearables-dat-ios#readme)
- [Meta DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [Full Meta Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Meta Wearables MCP](https://mcp.developer.meta.com/wearables)
- [Meta DAT iOS API reference](https://wearables.developer.meta.com/docs/reference/ios_swift/dat/latest)
- [Meta DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [Meta DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)

---

### Meta DAT camera and audio

**Name.** `meta-dat-camera-audio`

**When to use.** Design and review Meta DAT camera and audio features with exact SDK-version checks, stream ownership, cancellation, AVFoundation boundaries, privacy-aware consent, and separate phone/mock/connected-glasses evidence. Use for wearable camera, microphone, preview, capture, transcription, or media-processing requests.

Treat camera and audio as separate capability contracts with explicit ownership, consent, backpressure, cancellation, and evidence. A phone microphone or simulated frame is not a glasses microphone or camera result.

#### Read before acting

- Inspect the actual DAT package version, target deployment, AVFoundation/AVAudioSession use, existing media pipeline, persistence, network upload, and background behavior.
- Read [device session, camera, and audio](../../knowledge-base/70-meta-wearables/03-device-session-camera-and-audio.md), [privacy and publishing](../../knowledge-base/70-meta-wearables/08-privacy-publishing-and-release.md), and [mock evidence](../../knowledge-base/70-meta-wearables/07-mockdevice-testing-and-evidence.md).
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) for processing location, raw-media boundaries, HFP/A2DP labeling, retention, thermal behavior, and fallback.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) for companion, firmware, link, thermal/power, background, and recovery behavior around camera/audio sessions.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) for camera/audio capability ownership, bounded queues, session epochs, and phone fallback seams.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) for Wi-Fi/local-network parity, Bluetooth/link state, HFP/A2DP route evidence, queue policy, thermal behavior, and bounded recovery.
- Refresh the official [DAT iOS camera/streaming skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/camera-streaming), [debugging skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/debugging), and [AVFoundation](https://developer.apple.com/documentation/avfoundation) / [AVAudioSession](https://developer.apple.com/documentation/avfaudio/avaudiosession) documentation.
- Verify the exact installed symbols and media types against the selected tag. Current 0.9.0 notes use `DeviceSession.addCamera(config:) -> Camera`, `Camera.stream`, `Camera.stop()`, and camera state; do not revive removed `addStream(config:)` examples. Also check `StreamError.hingesClosed`, current photo-failure cases, and the background-camera sample behavior.
- Start from the [source-aligned iOS camera coordinator](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-camera-starter/MetaWearablesCameraStarter.swift) or [Android camera coordinator](../meta-wearables-implementation-recipes/assets/meta-wearables-android-camera-starter/MetaWearablesAndroidCameraStarter.kt) when scaffolding a concrete route. They are adapter seeds, not physical camera/audio proof.
- Use the [iOS MockDevice fixture](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-mockdevice-starter/MetaWearablesMockDeviceStarter.swift) or [Android MockDevice fixture](../meta-wearables-implementation-recipes/assets/meta-wearables-android-mockdevice-starter/MetaWearablesAndroidMockDeviceStarter.kt) to make denied permission, file-feed, capture, fold/doff, tap, cancellation, and teardown cases deterministic before requesting camera or audio hardware evidence.

#### Camera workflow

1. Gate on registration, session readiness, model/capability, user intent, and camera permission as required by the current release.
2. Build the camera configuration from supported values in the installed API. Record resolution, frame rate, format/codec, orientation, and expected latency; do not invent an unsupported combination.
3. Own the camera from session start through `Camera.stop()`/equivalent release. Cancel the consumer and stop the source on view exit, disconnect, error, and lifecycle transitions required by the SDK.
4. Bound buffering. Decide whether the consumer drops, coalesces, or blocks frames, and measure the effect on UI, memory, thermal load, and battery.
5. Keep conversion and inference off the UI thread. Preserve the source timestamp/ordering contract if downstream code needs it.
6. Test no-device, denied, interrupted, stale-session, slow-consumer, stop-while-streaming, and reconnect states.
7. Treat background continuation as a target-specific `to-verify` item; the
   current sample/changelog wording is not a blanket background-capture
   guarantee.

#### Audio workflow

1. Establish the exact documented audio source before designing transcription or upload. The current public DAT guide documents HFP glasses microphone input through iOS audio APIs and A2DP playback; verify the package/guide revision and do not substitute an undocumented native module.
2. Distinguish phone microphone, HFP glasses microphone, A2DP playback, route changes, and a remote transcription result in names, permissions, logs, UI, and evidence.
3. Configure `AVAudioSession` for the actual phone/HFP path with the required usage description and route policy. For camera + HFP, follow the documented ordering and verify `bluetoothHFP`; do not present a phone route as glasses capture.
4. Define consent, recording indicator, pause/stop, retention, deletion, network transfer, vendor processing, and failure behavior before enabling audio.
5. Use manual text, phone mic, or an explicit unavailable state as fallback only when the product’s privacy and UX contract permits it.

#### Fast path

Treat media as two independent slices: bounded camera/photo and an explicit audio route. For each, record owner, permission, buffer budget, stop order, phone fallback, and evidence; a working camera stream must not become a prerequisite for unrelated audio work.

#### Required output

- exact SDK tag/commit and symbols verified;
- camera/audio capability and permission matrix;
- stream ownership and cancellation diagram;
- media format, buffering, and performance assumptions;
- phone-vs-glasses source labeling;
- privacy/data-flow table;
- mock, simulator, connected-device, and physical-device test evidence.

#### Hard boundaries

- Never claim audio or camera support from a marketing page alone.
- Never send raw media to a service without a documented consent and retention path.
- Never persist raw media in fixtures, logs, crash payloads, or skill archives.
- Never let a successful phone-camera or mock-device test stand in for a glasses-camera test.
- Never hide an unavailable or permission-denied state behind a spinner.

#### Related routes

- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Display](../meta-dat-display/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Transport/reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Apple media and ML routes](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-media-ml-and-inputs/SKILL.md)

#### Sources

- [Meta DAT iOS camera streaming skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/camera-streaming)
- [Meta DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [Meta Wearables Device Access Toolkit announcement](https://developers.meta.com/blog/introducing-meta-wearables-device-access-toolkit/)
- [Apple AVFoundation](https://developer.apple.com/documentation/avfoundation)
- [Apple AVAudioSession](https://developer.apple.com/documentation/avfaudio/avaudiosession)
- [Apple privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)

---

### Meta DAT Display

**Name.** `meta-dat-display`

**When to use.** Design and implement native Meta DAT Display surfaces with capability gating, compact glanceable state, ButtonGroup/input handling, clear teardown, phone handoff, and physical Ray-Ban Display evidence. Use for native glasses UI requests, not for Web App HTML surfaces.

Design the glasses surface as a small, stateful system surface. The phone remains the configuration, consent, recovery, and detailed-content surface unless the product has a proven reason to move that work onto the Display.

#### Read before acting

- Inspect the actual DAT version, target device family, connected-model assumptions, phone UI, lifecycle, and any existing Display adapter.
- Read [Display access and glasses UI](../../knowledge-base/70-meta-wearables/04-display-access-and-glasses-ui.md), [device models and capability matrix](../../knowledge-base/70-meta-wearables/05-device-models-and-capability-matrix.md), and [mock evidence](../../knowledge-base/70-meta-wearables/07-mockdevice-testing-and-evidence.md).
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) before sharing Display actions with Web App D-pad/EMG/temple input or claiming a native sensor API.
- Read the [DAT iOS API surface atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md) for the release-anchored Display module, capability, ButtonGroup, video, and teardown map.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) for sanitized Display payloads, phone handoff, stale data, lifecycle, thermal, and fallback claims.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when Display delivery depends on mobile identity, callbacks, Developer Mode/release attestation, credential redaction, or a privacy/review claim.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) for firmware, companion, on-glasses DAT-app provisioning, Display update-required errors, release-channel, thermal, and recovery behavior.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) before sharing Display state with SwiftUI, Android, or a Web App.
- Refresh the official [DAT iOS Display skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/display-access), [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), and current [Display documentation](https://wearables.developer.meta.com/docs/develop/).
- Keep this route separate from [Meta Wearables Web Apps](../meta-wearables-web-apps/SKILL.md); native DAT Display and a 600×600 Web App have different APIs and proof.

#### Display workflow

1. Ask the runtime device whether it supports Display. Use the current capability API, including `DeviceType.supportsDisplay()` where exposed by the selected release; do not infer capability from a product name.
2. Define a compact display state machine: `hidden`, `presenting`, `focused`, `actionAvailable`, `dismissed`, `timedOut`, `disconnected`, and `unsupported` as appropriate.
3. Build one clear root Display view for the feature and keep its content legible at a glance. Prefer one primary status or action over a miniature phone screen.
4. Use the current Display input contract, including ButtonGroup/action handling where the installed SDK exposes it. Model focus, selection, previous/next, dismiss, timeout, and accidental input.
5. Keep the source of truth in the phone app/domain layer. Project a small, sanitized snapshot onto the Display and define behavior when that snapshot is stale.
6. Clear or replace the Display on completion, cancellation, disconnect, and route change using the current API (for example, `Display.clearDisplay()` where available).
7. Test the Display content with supported and unsupported models, no connection, long text, dynamic state, low confidence, and a lost phone session.

#### Interaction and visual contract

- one glance should explain what changed and what action is possible;
- high contrast, short labels, stable focus, and predictable action order;
- no private content, sensitive transcription, or raw media unless the product has explicitly designed and disclosed it;
- every glasses action has a phone fallback and a recoverable error state;
- no assumptions about color, brightness, input, or rendering resolution beyond the current official device documentation.

#### Fast path

Start with a capability gate, one compact Display state, one input event, and teardown. Validate the mock/render contract and phone fallback before adding rich layout, media, or repeated updates.

#### Required output

- exact target model and capability gate;
- Display state machine and phone handoff;
- view/input ownership and teardown behavior;
- mock/simulator test fixture;
- physical Display test script with expected observations;
- unsupported and disconnected fallback.

#### Hard boundaries

- `supportsDisplay()` is a gate, not proof that a particular product-generation label is supported.
- A mock Display or screenshot proves layout logic only; it does not prove brightness, focus, D-pad/button behavior, latency, or comfort on glasses.
- Do not use the native Display route for a Web App request or vice versa.
- Do not claim “Gen 3 Display” until the exact model and current SDK mapping are sourced and observed.

#### Related routes

- [Meta route planner](../meta-wearables-route-planner/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [SwiftUI native design](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/swiftui-native-design/SKILL.md)

#### Sources

- [Meta DAT iOS Display access skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/display-access)
- [Meta DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [Meta Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Apple Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)
- [Apple accessibility fundamentals](https://developer.apple.com/documentation/swiftui/accessibility-fundamentals)

---

### Meta DAT iOS integration

**Name.** `meta-dat-ios-integration`

**When to use.** Integrate the current Meta Wearables Device Access Toolkit into a native iOS companion app with source-checked package setup, registration, permissions, URL callbacks, session lifecycle, privacy controls, and testable unavailable states. Use when adding or reviewing DAT iOS code.

Build the smallest native iOS adapter around the official DAT package. Keep registration/session state out of view code, keep the phone useful without glasses, and make every SDK assumption traceable to the selected source snapshot.

#### Read before acting

- Inspect the actual `.xcodeproj`/`.xcworkspace`, app target, deployment target, Swift tools/Xcode version, package graph, Info.plist, entitlements, privacy manifest, app lifecycle, and existing adapters.
- Read [DAT iOS SDK foundations](../../knowledge-base/70-meta-wearables/01-dat-ios-sdk-foundations.md), [registration and permissions](../../knowledge-base/70-meta-wearables/02-registration-permissions-and-configuration.md), and [mock-device testing](../../knowledge-base/70-meta-wearables/07-mockdevice-testing-and-evidence.md).
- Read the [DAT iOS API surface atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md) and [upstream skill/tooling map](../../knowledge-base/70-meta-wearables/11-upstream-skill-and-tooling-map.md) before using a module or migration-specific symbol.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when registration, firmware, companion, on-glasses DAT-app provisioning, Developer Mode, release-channel, thermal, or recovery behavior is in scope.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when Info.plist identity keys, Meta AI callbacks, Developer Mode/release attestation, signing, or App Store/privacy-manifest review is in scope.
- Read the [debugging and observability route](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when registration, readiness, companion-boundary, device-path, session, stream, or typed-error diagnosis is in scope.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the native target includes Display input or a claim about native IMU/gesture sensors.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) before placing the adapter behind shared product state or a phone fallback.
- Pin or otherwise record the selected official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios) tag/commit. On the 2026-08-22 refresh, `main` was `225f64ff1617e7acc8c407bb8d3ee132f7263d00` while the 0.9.0 tag was `9b1b83d791dfebff7afd452e924a256819094b64`; use the tag for reproducible package resolution and validate its migration notes in the [changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md) against the actual package graph.
- Read the official [iOS integration](https://wearables.developer.meta.com/docs/build-integration-ios), [permissions/registration](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/permissions-registration), and [session lifecycle](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/session-lifecycle) guidance.
- For camera/photo implementation, begin with the [source-aligned iOS camera starter](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-camera-starter/MetaWearablesCameraStarter.swift) after validating the selected SPM products and target privacy configuration.
- For deterministic fixture work, begin with the [source-aligned iOS MockDevice starter](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-mockdevice-starter/MetaWearablesMockDeviceStarter.swift) after linking the selected `MWDATMockDevice` product; keep its mock evidence separate from physical-device evidence.
- For XCUITest-process control, use the [source-aligned iOS MockDevice test-client starter](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-mockdevice-test-client-starter/MetaWearablesMockDeviceTestClientStarter.swift) with the app-owned test-server setup; keep `MWDATMockDeviceTestClient` test-only and separate from runtime app capability code.

#### Integration workflow

1. Record the target, iOS deployment target, DAT tag/commit, device types, and requested capabilities.
2. Add the package using the official integration path and verify the actual products/modules exposed by that tag. Do not copy a product name from a different release.
3. Implement a narrow adapter with explicit states: `unconfigured`, `registrationRequired`, `registering`, `permissionRequired`, `ready`, `startingSession`, `connected`, `stopping`, `disconnected`, `unsupported`, `denied`, and `failed` as applicable to the installed API.
4. Put callback/deep-link configuration, permission requests, and registration recovery in the app integration boundary. Keep SwiftUI views as projections of state.
5. Start device sessions only from an intentional user flow or a documented product policy. Stop streams and release resources on cancellation, disconnect, and lifecycle transitions required by the SDK.
6. Treat SDK `stateStream`/`errorStream` completion and stopped-session behavior as part of the contract; test cancellation and reconnect rather than only the happy path.
7. Verify analytics/crash settings and data-handling policy. Current DAT notes distinguish opt-out controls from always-enabled device access management; use the selected release’s wording and settings.
8. Add mock fixtures and an unavailable phone-only path before connecting camera, audio, or Display features.

#### 0.9 migration traps to check

When the selected package is 0.9.0 or later, explicitly review the official changelog for:

- `DeviceSession.addCamera(config:)` and the child `Camera.stream`/`Camera.stop()` lifecycle;
- removal of the older `addStream(config:)` path;
- stream/state/error completion when a session stops;
- `DeviceType.supportsDisplay` and Display button-group changes;
- iOS minimum version, mock-link behavior, and camera sample background handling;
- `ListenerTokenBag` actor/`AnyListenerToken.store(in:)`, synchronous
  `MockCameraKit.setCameraFeed(cameraFacing:)`, doff/`hingesClosed`, and
  terminal stream completion;
- crash-reporting and device-access-management configuration changes.

These are review prompts, not permission to use an API without compiling against the selected tag.

#### Fast path

Run target preflight and resolve the exact SPM product before implementation. Then prove one lifecycle from registration and permission through a started session to capability stop; keep camera, Display, mock, and physical proof as separate follow-on gates.

#### Required output

Return:

- package/tag/commit and actual module/product names;
- target configuration and callback/permission inventory;
- adapter state machine and lifecycle table;
- mock and build-test plan;
- privacy/data-handling decisions;
- exact unresolved API or device questions.

#### Hard boundaries

- Do not put registration or permission calls in a view initializer.
- Do not treat pairing as a permanent authorization; model denial, revocation, disconnect, and re-registration.
- Do not log access tokens, callback URLs containing secrets, raw media, or persistent device identifiers.
- Do not claim a device is supported because the package compiles; runtime model/capability and hardware proof are separate.
- Do not assume iOS background execution, Meta AI voice commands, or raw glasses audio from DAT’s existence.

#### Related routes

- [Meta route planner](../meta-wearables-route-planner/SKILL.md)
- [Camera and audio](../meta-dat-camera-audio/SKILL.md)
- [Display](../meta-dat-display/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Apple system surfaces](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-system-surfaces-and-background/SKILL.md)
- [Apple privacy and security](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-privacy-performance-release-proof/SKILL.md)

#### Sources

- [Meta DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Meta DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [DAT iOS permissions and registration skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/permissions-registration)
- [DAT iOS session lifecycle skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/session-lifecycle)
- [DAT iOS sample-app guide](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/sample-app-guide)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Build integration for iOS](https://wearables.developer.meta.com/docs/build-integration-ios)
- [Apple app life cycle](https://developer.apple.com/documentation/uikit/managing-your-app-s-life-cycle)
- [Apple privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)

---

### Meta Wearables agentic engineering team

**Name.** `meta-wearables-agentic-team`

**When to use.** Orchestrate source-grounded iOS companion apps, Meta Wearables Device Access Toolkit integrations, Meta Ray-Ban Display Web Apps, and physical-device proof across Ray-Ban Meta, Oakley Meta, and other supported Meta wearables. Use when planning, building, reviewing, or releasing a wearable feature that needs an explicit route, privacy contract, device matrix, or evidence ledger.

Act as the technical lead for a small, evidence-driven Meta Wearables team. Turn a product outcome into the narrowest supported route, delegate the right specialist pass, and return implementation-ready work with source links and honest device evidence.

This team covers three related but different surfaces:

- the native iOS [Meta Wearables Device Access Toolkit (DAT)](https://github.com/facebook/meta-wearables-dat-ios) for a companion app that discovers, registers, sessions, and uses supported device capabilities;
- the native Android [Meta Wearables Device Access Toolkit (DAT)](https://github.com/facebook/meta-wearables-dat-android) for the Kotlin/Gradle companion route with separate Maven, Manifest, and `DatResult` contracts;
- [Meta Wearables Web Apps](https://wearables.developer.meta.com/docs/develop/webapps) for an HTML/CSS/JavaScript app delivered to the Meta Ray-Ban Display.

Do not merge those surfaces into a fictional “regular SDK.” The route planner must identify the actual SDK, package version, device family, and public capability before implementation.

#### Read before acting

- Inspect the actual Xcode target, workspace, deployment target, package graph, Info.plist, entitlements, privacy manifest, app lifecycle, and existing audio/video/display adapters.
- Read the relevant [Meta Wearables knowledge-base route](../../knowledge-base/70-meta-wearables/README.md), especially [route selection](../../knowledge-base/70-meta-wearables/00-platform-and-route-selection.md), [DAT iOS foundations](../../knowledge-base/70-meta-wearables/01-dat-ios-sdk-foundations.md), the [DAT iOS API surface atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md), the [DAT Android API surface atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md), [Developer Center operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md), [transport and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md), the [upstream skill/tooling map](../../knowledge-base/70-meta-wearables/11-upstream-skill-and-tooling-map.md), [session, camera, and audio](../../knowledge-base/70-meta-wearables/03-device-session-camera-and-audio.md), [Display](../../knowledge-base/70-meta-wearables/04-display-access-and-glasses-ui.md), and [Web Apps](../../knowledge-base/70-meta-wearables/06-web-apps-display-and-input.md).
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) whenever the task includes bundle/package identity, Meta AI callbacks, Developer Mode, release channels, app attestation, package/signing credentials, privacy-manifest/App Store gates, Web App origins, or an “on-device” trust claim.
- Read the [role routing reference](references/role-routing.md) and select only the specialists required by the requested surface.
- Read the [public plugin and skill matrix](../../knowledge-base/70-meta-wearables/13-public-plugin-and-skill-matrix.md) before treating “full SDK” or “regular SDK” as a route.
- Read the [full SDK capability and source-conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md) and route “full SDK,” “all capabilities,” and parity claims through the full-SDK auditor before implementation.
- Read the [source-pinned surface manifest](../../knowledge-base/70-meta-wearables/27-source-pinned-surface-manifest.md) and load its portable YAML when the request says “full SDK,” “all capabilities,” “regular SDK,” or cross-platform parity.
- Load the portable [team manifest](references/team-manifest.yaml) and run `python3 scripts/validate_team_manifest.py`; use its exact 23 local roles, 32 upstream role handoffs, three device-claim gates, shared handoff contract, and official agent-surface contract as the orchestration baseline.
- Emit a routing receipt with `python3 scripts/route_capability.py --capability <capability-id> --workspace-root <workspace-root> --json`, or use `--full-sdk` for the composite request. The receipt must carry the selected capability(s), surfaces, local roles, upstream handoffs, privacy path, preflight/evidence tasks, terminology guardrails, source snapshot, non-claims, and next action; it is orchestration evidence, not build or hardware proof.
- Run the portable [static fixture suite](scripts/run_static_fixture_suite.py) after changing reusable recipes or before handing off a source/static implementation packet. It exercises the shared-domain tests, Web App Node/preflight checks, Android target preflight, manifest/recipe validators, and full-SDK route receipt; pass `--ios-dat-checkout <checkout>` to add the four iOS DAT starter typechecks and `--live-source` for public-ref/tree checks. Record unavailable/not-run checks instead of treating them as hardware evidence.
- Run the bundled [team preflight runner](scripts/run_team_preflight.py) against the workspace. Use its `decision` receipt to choose `bootstrap`, `repair-failed-checks`, `complete-missing-checks`, `source-refresh-required`, `implementation-handoff-required`, or `target-preflight`; pass `--live-source` for source-sensitive work and `--implementation-handoff` for implementation readiness.
- When the knowledge base and implementation live in different sibling folders, pass the knowledge-base root as the positional workspace and the app folder as `--target-root`; this keeps local manifests authoritative while scanning the real Xcode/Gradle/Web target.
- Resolve the manifest `terminology_contract` before delegating; carry the term status, canonical route candidates, source refs, and evidence boundary into the handoff.
- Load the manifest `source_inventory.<lane>.plugin_roles` lists for every selected lane and preserve exact upstream role names, counts, and local handoff owners; a matching role count alone is not coverage.
- For source-sensitive work, run the portable [public-ref checker](../meta-wearables-source-refresh/scripts/check_source_revisions.py) and carry its expected/observed revisions into the handoff; a `DRIFT` result requires source-refresh impact review before implementation guidance.
- Run the portable [source-tree inventory checker](../meta-wearables-source-refresh/scripts/check_source_tree_inventory.py) for source-sensitive work and carry the root `AGENTS.md`/`README.md`/`install-skills.sh` surfaces, lane `.codex-plugin/plugin.json`, exact role-list/product/sample/artifact results, and public docs MCP route into the handoff; these are source/tool routing evidence only, and any `DRIFT` blocks silent source or package updates.
- Carry the manifest’s `agent_surface_contract`: native DAT uses the official local Codex plugin paths, Web Apps uses its official marketplace route, and the shared no-auth docs MCP is a live source-lookup option only when the client exposes it. If MCP is unavailable, use the pinned repository/full-reference fallback; never call a local route receipt or MCP lookup a device/runtime result.
- Filter the manifest `api_surface.rows` for the selected journey/platform and
  pass the row IDs, source anchors, status, compile/runtime gates, privacy paths,
  fallbacks, and migrations to the specialist handoffs.
- Load the [capability/evidence plan](../meta-wearables-full-sdk-audit/references/capability-evidence-plan.yaml) and pass the selected capability's owner roles, implementation route, privacy path, fallback, minimum evidence levels, and proof task IDs to the handoff; run its validator through the full-SDK auditor before packaging.
- Read the [device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md) before treating Gen 2, Meta Glasses, Display, or “Gen 3” as a target identity.
- Read the [completion audit and next-proof route](../../knowledge-base/70-meta-wearables/32-completion-audit-and-next-proof.md) when reporting overall progress, “full SDK” readiness, Gen 2/Gen 3 support, on-device compliance, or release status; do not collapse source/team coverage into app or hardware proof.
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) when the request names a firmware, companion/DAT-app version, version-dependency table, Gen 2/Gen 3 support, or a compatibility failure.
- For any compatibility, Gen 2, Display, firmware, or Gen 3 handoff, load the [compatibility evidence-packet template](../meta-wearables-device-compatibility/references/compatibility-evidence-packet.yaml) and run `python3 ../meta-wearables-device-compatibility/scripts/validate_compatibility_packet.py <packet>`; do not route a packet as completed when its tuple, physical capability, release, or Gen 3 evidence is missing.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) whenever the request uses “on-device,” “local-first,” camera/audio/sensor privacy, thermal safety, or remote-processing language.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when the request involves firmware, Meta AI companion versions, Developer Mode, release channels, on-glasses DAT-app provisioning, update-required/device-unavailable errors, thermal/power failures, or recovery.
- Read the [debugging, observability, and diagnostic evidence route](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when the request involves a DAT failure, live DAT Inspector/MCP, readiness, companion-boundary diagnosis, event/error logs, or a diagnostic handoff.
- Read the [input, sensors, and physical interaction route](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the request involves Display buttons, D-pad/captouch, Neural Band/EMG, temple gestures, IMU/motion/orientation, geolocation, browser sensors, or “on-device” sensor claims.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) before sharing behavior across iOS, Android, native Display, Web Apps, or a phone fallback.
- Load the [vertical-slice playbooks](../meta-wearables-app-architecture/references/vertical-slice-playbooks.md) for implementation requests; select the smallest native Display, camera-to-phone, audio-first, Web App, or shared-outcome packet and pass its row IDs and proof ladder to the specialists.
- If the current workspace has no Xcode target, Gradle project, or hosted Web App, stop target-specific implementation claims and load the [project bootstrap packet](references/project-bootstrap-packet.md). Create the implementation in a new sibling project folder and return the completed target-intake handoff; do not treat this knowledge-base repo as the app.
- If that no-target path is Android-first, use the [credential-safe Android DAT target starter](../meta-wearables-implementation-recipes/assets/meta-wearables-android-target-starter/README.md) as the target-owned Gradle/permission/init shell; keep its Developer Center and GitHub Packages values in ignored local properties or environment variables.
- Run the bundled [target-surface inspector](scripts/inspect_target_surfaces.py) against the actual workspace before choosing that path. Read `TARGETS_PRESENT`, `TARGETS_PARTIAL`, or `NO_TARGET` as structural signals only; `TARGETS_PARTIAL` still requires the bootstrap packet, and only `TARGETS_PRESENT` permits target-specific preflight.
- Load the [implementation-recipes specialist](../meta-wearables-implementation-recipes/SKILL.md) for concrete code scaffolding; it must resolve the selected package/artifact/generated API and mark unresolved signatures `to-verify`.
- For deterministic native tests, route the implementation specialist to the [iOS MockDevice starter](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-mockdevice-starter/MetaWearablesMockDeviceStarter.swift) or [Android MockDevice starter](../meta-wearables-implementation-recipes/assets/meta-wearables-android-mockdevice-starter/MetaWearablesAndroidMockDeviceStarter.kt); require sanitized mock assertions and preserve the physical-device gate.
- For implementation requests, require the [implementation-handoff validator](../meta-wearables-implementation-recipes/scripts/validate_implementation_handoff.py) to pass against the selected surface manifest, capability plan, and team manifest before target code is treated as ready.
- Load the [device-proof target-preflight reference](../meta-wearables-device-proof/references/target-preflight.md) before build, connected, physical, signed, or release work; require `PRE-*` statuses before promoting any target claim.
- Refresh the official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/), and [terms](https://wearables.developer.meta.com/docs/terms) when the request depends on current API, device, preview, or publishing behavior.
- When Android or Web Apps are in scope, also refresh the [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), and [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp); do not treat plugin role names as cross-platform API proof.
- Keep the Apple routes in scope: [AVFoundation](https://developer.apple.com/documentation/avfoundation), [AVAudioSession](https://developer.apple.com/documentation/avfaudio/avaudiosession), [privacy manifests](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files), [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/), and the project’s existing Apple verification routes.

#### Fast path

Run the team preflight and target-surface inspector first. If no target exists, return the bootstrap packet; otherwise select one playbook and one manifest capability, run only the owning roles plus the static route receipt, and escalate only when that slice exposes a new gate or conflict.

#### Team loop

1. **Route planner** identifies DAT iOS, native Display, Web App, phone-only fallback, or an explicitly unsupported request.
2. **Full-SDK auditor** inventories the exact upstream iOS/Android/Web Apps role lists and every requested capability against the five iOS package products (four runtime products plus the test-only MockDevice client), four Android artifacts, Web Apps runtime, product-label/SDK-identity/runtime-capability matrix, capability/evidence plan, source conflicts, and evidence gates.
3. **iOS integration** checks package version, registration, permissions, callback configuration, lifecycle, and target settings.
4. **Android integration** checks Maven artifacts, Gradle/Manifest configuration, `DatResult`, `Flow`/`StateFlow`, R8, and Android target settings when Android is in scope.
5. **iOS API-atlas specialist** maps the exact public iOS modules, symbols, migration traps, samples, debug tools, MCP routes, and DAT/Web Apps boundary for the pinned release.
6. **Android API-atlas specialist** maps the exact Maven artifacts, Kotlin/Java symbols, 0.9 migrations, Display/camera/MockDevice/debugging surface, and Android-specific source conflicts for the pinned release.
7. **Developer-operations specialist** owns MMA/team/project/app identity, product listing, permission rationale, version/build status, release channels/testers, Meta AI channel state, telemetry, and account/recovery evidence.
8. **Application-architecture specialist** separates shared product state/policy from Swift, Kotlin, native Display, Web App, and phone-fallback adapters, then defines concurrency, resource ownership, stale-event, and test seams.
9. **Camera/audio specialist** models the stream contract, AVFoundation/Android audio boundary, resource ownership, backpressure, and HFP/A2DP privacy.
10. **Transport/reliability specialist** audits Bluetooth control, Wi-Fi/local-network parity, HFP/A2DP route state, queue/backpressure, background, thermal/power, disconnect, and bounded recovery.
11. **Debugging/observability specialist** establishes the read-only debug-server/MCP baseline, walks the first-failure graph, waits for narrow events, maps platform-specific errors, and exports redacted diagnostic evidence.
12. **Input/sensor specialist** separates native Display callbacks, Web App D-pad/EMG/temple input, browser/phone sensor APIs, permission/secure-context gates, event epochs, privacy, teardown, and physical input/sensor proof.
13. **Display specialist** designs the glasses surface, capability gate, input/focus model, and phone/glasses state handoff.
14. **Web Apps specialist** handles the 600×600 Display surface, public HTTPS delivery, input, toolkit skills, and browser-simulator/physical split. Run the Web Apps package’s `run_webapp_preflight.py` to distinguish Meta-marked source and hosted HTTPS evidence from a generic local web page.
15. **Device-proof specialist** builds the target preflight, mock, simulator, connected-device, and physical-device evidence ladder; it freezes the exact scheme/module/host, dependency graph, privacy/configuration, and target tuple before hardware claims. For a concrete iOS target, run the device-proof package’s redacted `run_ios_target_preflight.py` and carry its static/build receipt into the handoff.
16. **Privacy/publishing specialist** audits permissions, disclosure, data minimization, terms, acceptable use, and review metadata.
17. **On-device compliance specialist** enforces processing location, raw-data boundaries, consent, lifecycle/thermal/network fallback, and the distinction between glasses-native, phone-local, remote, and mixed behavior.
18. **Operational-readiness specialist** freezes the compatibility tuple, separates Developer Mode from release-channel evidence, diagnoses companion/firmware/on-glasses-DAT-app/link/thermal failures, and records bounded recovery.
19. **Source-refresh specialist** runs the public-ref and source-tree checkers, records exact official URLs, SDK release, commit, date, exact role lists, changed API, source conflicts, and unresolved gaps, and blocks silent manifest rewrites when refs or role trees drift.
20. **Security/attestation specialist** freezes iOS/Android identity tuples, validates callback ownership and stale/duplicate handling, separates Developer Mode from release attestation, protects package/signing credentials, audits Web App origin boundaries and dated Apple/Meta review gates, and returns redacted `SEC-*` evidence without equating attestation with physical capability or on-device processing.
21. **Device-compatibility specialist** resolves product labels to exact package/artifact, companion, firmware, runtime-identity, and capability tuples; records version-dependency access state; classifies first-party versus community signals; diagnoses firmware drift; and returns `COMP-*` evidence without mapping “Gen 3” or “regular SDK” by assumption.
22. **Implementation-recipes specialist** turns the selected playbook, manifest rows, and capability/evidence-plan entry into source-aligned Swift, Kotlin/Java, or Web App scaffolding with explicit compile gates, ownership/epoch/stop order, data-path/privacy contract, fallback, and next proof task.

The lead selects one vertical-slice playbook before delegating implementation.
The playbook narrows the outcome, primary/fallback surfaces, state owner, API
rows, data path, and evidence ladder; it does not relax any specialist gate.
The lead reconciles the specialists before any implementation claim is made. A
specialist may recommend a route; only the lead records the final route and
evidence status.

#### No-target bootstrap path

When no concrete app target exists, return a bootstrap packet before code:

1. run `python3 scripts/run_team_preflight.py <workspace> --json` and carry its
   decision, check statuses, and target-surface receipt into the handoff;
2. create or identify a sibling project folder;
3. freeze the outcome, primary surface, fallback, exact package/artifact/hosted
   revision, device/runtime/capability tuple, privacy/data path, and requested
   proof level;
4. select one vertical slice and its manifest API/evidence rows;
5. assign the local role owners and upstream handoff IDs from the team manifest;
6. record `to-verify` items and the next proof task.

The portable [project bootstrap packet](references/project-bootstrap-packet.md)
contains the intake shape, starter layout, and evidence ladder. It is a
handoff contract, not evidence that an app builds or that a named pair works.
The Android target starter is likewise only a reproducible build-graph seed;
the selected artifact must compile in the sibling target before capability or
device claims advance.

#### Required output

Return these sections for every non-trivial request:

1. **Route decision** — native DAT, native Display, Web App, phone fallback, or blocked/unsupported; include why adjacent routes were rejected.
2. **Compatibility table** — exact SDK/package release, device type/model, capability gate, iOS/Android/Web surface, and “to verify” items.
3. **State and privacy contract** — pairing/registration, permission, session, stream, display/input, disconnect, error, cancellation, and fallback states.
4. **On-device compliance contract** — processing location, network/storage boundary, consent, thermal/lifecycle behavior, and typed fallback.
5. **Operational-readiness contract** — exact compatibility tuple, mode, provisioning state, first failure, recovery action, post-state, and release-channel gate.
6. **Implementation handoff** — files/modules, role owner, source/API references, and tests or fixtures.
7. **Selected vertical slice** — playbook name, capability/evidence-plan entry, API/evidence row IDs, state owner, fallback surface, required evidence levels, and next proof task.
8. **Evidence ledger** — preflight, mock/simulator, build, connected-device, physical-device, signed, and release-channel results kept separate.
9. **Security/identity contract** — redacted bundle/package identity, callback, mode/channel, credential storage, attestation, Web App origin, logging, and processing-location boundary.
10. **Open gates** — unsupported or unverified hardware, preview restrictions, account/access requirements, recovery uncertainty, and release steps.
11. **Terminology resolution** — manifest term ID/status, canonical route candidates, source refs, and the evidence boundary carried from the `terminology_contract`.
12. **Upstream role coverage** — exact role lists for each selected lane, count match, local handoff owners, and any unmapped or source-conflicted role.
13. **Team manifest receipt** — team manifest ID/revision, selected local role IDs, upstream handoff IDs, device-claim gate IDs, and validator result.
14. **Target/bootstrap status** — include the target-surface inspector receipt; if an implementation target does not exist, include the completed bootstrap packet, sibling project path, selected first slice, and next proof task.
15. **Team preflight receipt** — runner version, decision, source-check mode, failed/unavailable checks, target root, and next action.
16. **Implementation handoff receipt** — packet path, selected route, API/evidence/owner counts, manifest/plan/team validation, unresolved symbols, and next proof task.
17. **Routing receipt** — the machine-readable capability/full-SDK route receipt, selected local roles, upstream handoff IDs, preflight tasks, evidence order, terminology status, source snapshot, and official agent-surface contract.
18. **Static fixture-suite receipt** — the combined reusable domain/Web App/Android-preflight/manifest/recipe/routing result, optional iOS starter typechecks, unavailable/not-run checks, and the next target-specific gate.

#### Hard boundaries

- Never claim “Gen 3” support from the user’s wording alone. Map the actual runtime `DeviceType`, model, firmware, and capability response; as of the current public source snapshot, a public Gen 3 mapping is not established.
- Never treat a compile, mock, browser simulator, or iPhone-only test as proof that a pair of glasses, microphone, camera, Display, D-pad, EMG input, or Wi-Fi path works on hardware.
- Never infer raw glasses microphone audio, Meta AI voice commands, cloud processing, background execution, or an entitlement from a nearby symbol or marketing page; use the documented HFP/A2DP route and verify the target audio path.
- Never infer Kotlin/Gradle/Manifest behavior from Swift/iOS examples or assume cross-platform API parity without the selected artifact and target build.
- Never put secrets, user audio/video, or device identifiers into logs, fixtures, screenshots, or skill archives.
- Never treat a callback receipt, Developer Mode registration, app attestation, or release-channel membership as proof of permissions, physical capability, local processing, App Store/Play approval, or production. Keep tokens, callback payloads, signatures, tester data, and private project identifiers redacted.
- Keep the phone app useful when the wearable is absent, unregistered, unsupported, disconnected, permission-denied, or out of range.
- Treat DAT and Web Apps as separate integration surfaces with separate release and evidence gates.
- Do not treat the existence of a local specialist package as coverage until the team manifest maps the selected upstream role, handoff owner, output, and hard gate.

#### Related routes

- [Meta route planner](../meta-wearables-route-planner/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [DAT API atlas](../meta-dat-api-atlas/SKILL.md)
- [DAT Android API atlas](../meta-dat-android-api-atlas/SKILL.md)
- [Developer Center operations](../meta-wearables-developer-operations/SKILL.md)
- [Security and attestation](../meta-wearables-security-attestation/SKILL.md)
- [Device compatibility](../meta-wearables-device-compatibility/SKILL.md)
- [Full SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [DAT camera and audio](../meta-dat-camera-audio/SKILL.md)
- [Transport and runtime reliability](../meta-wearables-transport-reliability/SKILL.md)
- [DAT Display](../meta-dat-display/SKILL.md)
- [Meta Wearables Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Vertical-slice playbooks](../meta-wearables-app-architecture/references/vertical-slice-playbooks.md)
- [Implementation recipes](../meta-wearables-implementation-recipes/SKILL.md)
- [Project bootstrap packet](references/project-bootstrap-packet.md)
- [Target-surface inspector](scripts/inspect_target_surfaces.py)
- [Team preflight runner](scripts/run_team_preflight.py)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)
- [Machine-readable team manifest](references/team-manifest.yaml)

#### Sources

- [Meta Wearables DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Meta Wearables DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [Full SDK capability and source-conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md)
- [Device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md)
- [On-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md)
- [Operational readiness and recovery](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md)
- [Application architecture and platform boundaries](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md)
- [Meta Wearables DAT iOS README and AI-assisted development](https://github.com/facebook/meta-wearables-dat-ios#readme)
- [Meta Wearables DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [Meta Wearables DAT iOS upstream skills](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills)
- [Meta Wearables DAT iOS samples](https://github.com/facebook/meta-wearables-dat-ios/tree/main/samples)
- [Meta Wearables Device Access Toolkit announcement](https://developers.meta.com/blog/introducing-meta-wearables-device-access-toolkit/)
- [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md)
- [Version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md)
- [Wearables MCP](https://mcp.developer.meta.com/wearables)
- [Meta Wearables Web Apps](https://wearables.developer.meta.com/docs/develop/webapps)
- [Apple AVFoundation](https://developer.apple.com/documentation/avfoundation)
- [Apple privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)

---

### Meta Wearables application architecture

**Name.** `meta-wearables-app-architecture`

**When to use.** Architect maintainable iOS, Android, and Ray-Ban Display applications on top of Meta Wearables DAT without leaking platform SDKs into UI or domain code. Use when starting a wearable app, adding camera/audio/Display/Web App capabilities, sharing behavior across Swift and Kotlin, designing phone-first fallbacks, or turning a DAT feature plan into adapters, state machines, test seams, and release-ready modules.

Design the product boundary before importing DAT symbols. Keep shared product
intent, state, policy, and fallback behavior separate from platform-specific
Swift, Kotlin, and Web App adapters. The architecture must remain useful when
glasses are absent, unsupported, disconnected, permission-denied, thermally
limited, or on a different model.

#### Read before acting

- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md), [route-selection page](../../knowledge-base/70-meta-wearables/00-platform-and-route-selection.md), [full-SDK matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md), and [on-device contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md).
- Load the [vertical-slice playbooks](references/vertical-slice-playbooks.md) after filtering the source-pinned API register; choose the smallest playbook that matches the outcome and carry its row IDs, state machine, fallback, and proof ladder into the handoff.
- Load the [implementation recipes](../meta-wearables-implementation-recipes/SKILL.md) when the request needs Swift, Kotlin/Java, or Web App scaffolding; preserve compile-gated signatures and adapter boundaries.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) before placing identity, callback, credential, attestation, Web App origin, or processing-location state in shared product architecture.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) before sharing Display, Web App, or phone-sensor events; preserve source, epoch, teardown, and fallback boundaries.
- Read the [DAT iOS foundations](../../knowledge-base/70-meta-wearables/01-dat-ios-sdk-foundations.md), [Android parity route](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md), [device/release packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md), and [operational recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md).
- Inspect the real target, package/artifact graph, deployment/min SDK, permissions, privacy metadata, lifecycle, existing media/display adapters, and Web App deployment boundary.
- Refresh the selected [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp), and [full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat) before using a version-sensitive symbol.

#### Architecture workflow

1. **Freeze the product contract.** Name the user outcome, primary surface,
   phone fallback, requested capabilities, target labels, exact evidence level,
   processing location, and highest-consequence failure.
2. **Choose boundaries.** Create a shared domain/policy layer, a platform
   adapter layer, surface presenters, and test fakes. Keep iOS DAT, Android
   DAT, native Display, and Web Apps as distinct adapters—not a single
   cross-platform SDK facade that hides incompatible semantics.
3. **Model state before effects.** Define registration, permission, device
   selection, session, capability, transport, thermal, cancellation, and
   fallback states. Make one owner reduce SDK events into app state; views only
   render state and send user intents.
4. **Define capability seams.** Use product-level protocols/interfaces for the
   requested operation, then implement them in Swift, Kotlin, or Web App code.
   Keep exact DAT imports, `AsyncSequence`/publisher details, `Flow`/
   `StateFlow`, `DatResult`, and Web APIs inside the adapter.
5. **Control concurrency and resources.** Give each session/capability an
   ownership boundary, cancellation token or coroutine scope, event epoch, and
   terminal stop path. Bound camera/audio work and never let stale frames or
   Display actions cross a new session epoch.
6. **Design the data path.** Label every frame, audio sample, transcript,
   sensor value, Display payload, identifier, model result, network request,
   cache, and log as glasses-native, phone-local, remote, mixed, or unknown.
   Apply consent, retention, thermal, and deletion rules at the boundary.
7. **Build the fallback first.** The phone route must remain useful with no
   companion, no glasses, no Display capability, no permission, no network,
   stale data, or a terminal session error. Fallback is typed product state,
   not a hidden retry spinner.
8. **Test each seam.** Run pure reducer/domain tests, adapter fakes, DAT
   MockDevice, Web App browser simulation, target build, connected-device, and
   physical scripts as separate evidence levels.
9. **Audit release architecture.** Verify target configuration, privacy,
   signing, companion/account/channel, firmware, on-glasses DAT app, and the
   exact build before calling the architecture releasable.
10. **Select a vertical slice.** Use the native Display, camera-to-phone,
    audio-first, Web App, or shared-outcome playbook; do not invent a broad
    cross-platform facade when a capability-specific adapter is required.

#### Fast path

Draw four boxes before writing adapters: shared domain, platform adapter, surface presenter, and test fake. Choose one playbook, reject any interface that leaks SDK types, and implement one reducer/fake plus one adapter method and typed fallback before pursuing cross-platform parity.

#### Required output

- surface and route decision with rejected alternatives;
- selected vertical-slice playbook and manifest/evidence row IDs;
- selected implementation recipe, confirmed versus `to-verify` symbols, and next compile task;
- module/target boundary diagram and ownership table;
- shared domain state machine and platform adapter interfaces;
- iOS, Android, and Web App implementation mapping with exact symbols marked
  `source`, `compile`, or `to-verify`;
- data-flow, privacy, thermal, lifecycle, and fallback contract;
- concurrency/resource ownership and stale-event strategy;
- reducer/fake/MockDevice/browser/connected/physical test seams;
- package, account, firmware, channel, signing, and release gates;
- files/modules to create or change and the next evidence task.

#### Hard boundaries

- Never place DAT imports, Meta AI registration, or Web App browser APIs in a
  SwiftUI/Compose view or shared domain model.
- Never translate Swift symbols into Kotlin or Web APIs by naming similarity;
  the selected artifact and target compile decide the adapter surface.
- Never hide camera, audio, Display, sensor, or remote-processing failure behind
  an unbounded retry or a generic `isLoading` state.
- Never treat one “common” interface as proof of cross-platform parity. Return
  capability-specific unsupported/to-verify states where a platform differs.
- Never call a phone-local fallback glasses-native or call a Web App a native
  DAT module.
- Never use a compile, fake, MockDevice, browser simulator, or shared reducer
  test as proof of physical glasses behavior.
- Never map “Gen 3” to an enum or adapter until official mapping and named
  runtime evidence exist.
- Never put tokens, serials, raw media, transcripts, or private diagnostics in
  source, fixtures, logs, or skill archives.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Route planner](../meta-wearables-route-planner/SKILL.md)
- [Full-SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Camera/audio](../meta-dat-camera-audio/SKILL.md)
- [Native Display](../meta-dat-display/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Vertical-slice playbooks](references/vertical-slice-playbooks.md)
- [Implementation recipes](../meta-wearables-implementation-recipes/SKILL.md)

#### Sources

- [Application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp)
- [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat)
- [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [Portable vertical-slice playbooks](references/vertical-slice-playbooks.md)

---

### Meta Wearables debugging and observability

**Name.** `meta-wearables-debugging-observability`

**When to use.** Diagnose Meta Wearables DAT iOS and Android setup, registration, permission, device-link, session, camera, audio, Display, and transport failures with source-grounded, read-only evidence. Use when a DAT app cannot communicate with glasses, a stream or Display session fails, live DAT Inspector MCP tools are available, logs need a redacted diagnostic bundle, or a team must separate app bugs from Meta AI companion, firmware, device, and release-channel boundaries.

Diagnose the first failing state in a Meta Wearables integration and produce a
small, redacted evidence packet. Treat debugging as an observation workflow,
not permission to mutate the Meta AI app, glasses, account, registration, or
release channel.

#### Read before acting

- Read the [debugging and observability route](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md).
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the failure involves a Display action, gesture, sensor watch, permission, stale event, or physical input result.
- Read [DAT iOS foundations](../../knowledge-base/70-meta-wearables/01-dat-ios-sdk-foundations.md)
  or [DAT Android parity](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md)
  for the selected platform, then the relevant [API atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md)
  or [Android API atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md).
- Read [operational readiness](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md),
  [transport reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md),
  and [device proof](../../knowledge-base/70-meta-wearables/07-mockdevice-testing-and-evidence.md)
  when firmware, link, audio, thermal, Display, or physical behavior is involved.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when the first failure involves callback/identity configuration, Developer Mode/release attestation, package/signing credentials, or a redacted diagnostic handoff.
- Refresh the official [DAT iOS](https://github.com/facebook/meta-wearables-dat-ios),
  [DAT Android](https://github.com/facebook/meta-wearables-dat-android),
  [DAT MCP](https://mcp.developer.meta.com/wearables), and version-dependency
  sources before using a version-sensitive symbol or current issue claim.

#### Procedure

1. **Freeze the target.** Record platform, app target, SDK package/artifact and
   commit/tag, phone OS, app build, exact product wording, runtime device type
   if known, firmware, Meta AI version, mode/channel, and operation being
   attempted. Mark unknown fields `to-verify`.
2. **Separate the evidence plane.** Label each observation as source, config,
   compile, mock, browser-simulator, connected-device, physical, signed, or
   release-channel evidence. Do not use a source, mock, or compile result as
   hardware proof.
3. **Use live MCP evidence first when available.** Discover and connect to the
   local DAT Inspector/debug server. Establish `get_connection_status`,
   `get_sdk_state`, and `get_dat_readiness` before deeper diagnosis; connection
   failure is not the same as SDK or device failure.
4. **Walk the boundary.** Inspect `get_companion_boundary_diagnosis`,
   `get_device_path`, `get_permissions`, and `get_device_properties` when the
   readiness baseline points outside app code. The path is app -> DAT
   registration -> Meta AI permissions -> device selection/link -> session ->
   capability/stream.
5. **Reproduce narrowly.** Ask for one exact operation, then wait for
   `wait_for_events` with the narrowest category/source. Collect `get_errors`
   and `get_event_digest` only as needed. Identify the first failing state and
   later symptoms separately.
6. **Diagnose before editing.** Check initialization/configuration, callback,
   registration, permission, device eligibility, link state, session, stream or
   Display state, audio route, transport, and thermal/power in that order. Use
   the selected platform's typed result/error rather than translating symbols
   across iOS and Android.
7. **Export safely.** Use `export_diagnostic_bundle` when available, inspect
   it for raw frames, audio, tokens, client credentials, URLs with secrets,
   precise device identifiers, and personal data, then redact before handoff.
8. **Return a bounded conclusion.** Name the evidence plane, first blocking
   state, observed fact, next developer action, fallback, and proof still
   missing. State when the result is only app-visible boundary diagnosis.

#### Platform anchors

| Layer | iOS observation | Android observation |
| --- | --- | --- |
| Initialization | `Wearables.configure()` and configuration logs | `Wearables.initialize(context)` and initialization logs |
| Registration | `registrationState`, callback handling, `startRegistration()` | `Wearables.registrationState`, Activity/deeplink callback, registration result |
| Device | `devices`, selectors, `device.compatibility`, `device.properties`, `device.linkState` | `Wearables.devices`, selectors, compatibility/properties/link state |
| Permissions | DAT permission and companion-boundary evidence | `checkPermissionStatus(...)` plus companion-boundary evidence |
| Session/capability | `DeviceSession`/session state, errors, camera or Display state | `Session`/`DeviceSession`, `DatResult`, state, camera or Display result |
| Live evidence | iOS DAT Inspector events and SDK logs | Android DAT Inspector events and SDK logs |

Use the exact resolved artifact and API reference for the target. The table is
a routing aid, not proof that the platform symbols are interchangeable.

#### Redaction rules

- Keep state names, timestamps, SDK/artifact revision, app build, capability,
  error category, counts, and recovery result when needed for diagnosis.
- Remove credentials, client tokens, access tokens, raw image/video/audio,
  transcripts, personal data, precise device IDs, account emails, and private
  diagnostic payloads. Replace identifiers with stable local labels such as
  `device-A`.
- Do not log every frame or audio buffer. Prefer counters, durations, codec,
  dimensions, route category, and bounded error digests.
- A redacted bundle is still app-visible evidence; it does not prove physical
  glasses rendering, audio, input, firmware recovery, or public publishing.

#### Fast path

Build a first-failure timeline from one run: target tuple -> last known state -> first error -> owning boundary -> next recovery. Capture only redacted structured fields and stop collection once the earliest actionable boundary is identified; do not gather a full diagnostic dump by default.

#### Hard boundaries

- Do not mutate Meta AI, glasses, account, permission, registration, project,
  release channel, or tester state during diagnosis unless the user separately
  authorizes that operation.
- Do not diagnose from a single generic “not connected” message when the first
  failing state can be observed.
- Do not map “Gen 3” to a model enum or claim Display/audio/transport support
  from a neighboring device.
- Do not call a local debug-server result companion-app introspection; it is
  evidence exposed through the app/DAT boundary.
- Keep phone fallback and user-visible recovery available when the wearable is
  absent, denied, unsupported, disconnected, or thermally unavailable.

#### Required handoff

Return:

- frozen compatibility tuple and selected operation;
- evidence plane and source/package revision;
- baseline readiness and first failing state;
- narrow event/error digest and redaction status;
- likely owner: app, SDK/config, Meta AI boundary, device/firmware, transport,
  audio, thermal/power, or release channel;
- next action, fallback, and exact proof still required.

#### Related roles

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Route planner](../meta-wearables-route-planner/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Transport reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)

#### Sources

- [Official DAT iOS debugging skill](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/debugging/SKILL.md)
- [Official DAT iOS live-debugging MCP skill](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/live-debugging-mcp/SKILL.md)
- [Official DAT Android debugging skill](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/debugging/SKILL.md)
- [Official DAT Android live-debugging MCP skill](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/live-debugging-mcp/SKILL.md)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [Wearables MCP](https://mcp.developer.meta.com/wearables)
- [Version dependencies](https://wearables.developer.meta.com/docs/version-dependencies)
- [Meta device and release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md)

---

### Meta Wearables developer operations

**Name.** `meta-wearables-developer-operations`

**When to use.** Operate and audit the Meta Wearables Developer Center lifecycle for DAT iOS/Android and Ray-Ban Display Web Apps, including Managed Meta Account organization/team access, projects, platform app identities, product listings, permission justifications, versions, release channels, testers, Meta AI channel state, telemetry, and recovery. Use when onboarding a team, configuring a project, preparing a DAT release, diagnosing channel access, reviewing telemetry/privacy, or separating Developer Mode from signed distribution.

Use this role for the account, project, configuration, distribution, and
telemetry layer around Meta Wearables integrations. It is not a substitute for
the DAT iOS/Android API roles, a physical-device run, or authorization to mutate
an external Developer Center account.

#### Read before acting

- Read [the Developer Center operations route](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md), [the evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md), [privacy and publishing](../../knowledge-base/70-meta-wearables/08-privacy-publishing-and-release.md), and [operational readiness](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md).
- Read [the full-SDK capability/conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md), [application architecture](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md), and [the Android API atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md) when platform identity, release build, or fallback is involved.
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) when project/version/channel work includes firmware, companion/DAT-app versions, support tables, or a Gen 2/Gen 3 compatibility claim.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when project identity, callbacks, Developer Mode, release attestation, package/signing credentials, or App Store/privacy-manifest review is involved.
- Refresh the official [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat), [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/), [onboarding and organization guide](https://wearables.developer.meta.com/docs/onboarding-and-organization-management), [manage projects](https://wearables.developer.meta.com/docs/manage-projects), [release channels](https://wearables.developer.meta.com/docs/set-up-release-channels), and [DAT iOS/Android repositories](https://github.com/facebook/meta-wearables-dat-ios), [Android repository](https://github.com/facebook/meta-wearables-dat-android).
- Read [the operations contract](references/developer-operations-contract.md) before producing an account, channel, tester, telemetry, or recovery packet.

#### Authority and action boundary

1. The authenticated Developer Center/account state for the named organization,
   team, project, version, channel, and tester.
2. The current official Developer Center instructions and raw reference.
3. The selected DAT package/changelog and target build.
4. Historical examples, screenshots, or remembered account behavior.

Public documentation can describe a control; it cannot prove that the current
account can see or mutate it. Keep `source`, `access-gated`, `account`, `build`,
`connected`, `physical`, `signed`, and `release-channel` evidence separate.
Do not create, delete, restore, invite, remove, publish, or switch an external
account/project/channel unless the user explicitly authorizes that exact live
operation and the target is resolved.

#### Fast path

Classify the requested action as read-only, reversible configuration, or release-affecting before touching Developer Center. For read-only work, gather identity/version/channel/tester state; for mutation, require explicit approval and a pre/post receipt, and stop on an account or access mismatch.

#### Operations workflow

1. Freeze company, Managed Meta Account (MMA) organization, Wearables team,
   operator role, project, platform app, bundle/package ID, target build,
   product version, channel, tester Meta Account, and evidence level.
2. Check whether the company already has an MMA organization. Preserve the
   one-organization-per-company boundary; do not create a duplicate as a
   workaround for missing access.
3. Verify team membership and distinguish MMA organization membership from the
   separate Meta Account used by a tester to receive a release invitation.
4. Map project configuration: platform app identity, application ID/client
   token mode, product name/icon, permissions requested, internal permission
   justification, callback/build configuration, and privacy owner.
5. Map versioning and distribution: Major/Minor/Patch intent, generated DAT
   application build status, version details, one version per channel, invite
   mode, tester access, and the corresponding signed mobile build/URL.
6. Reconcile Developer Mode and release-channel state. Developer placeholders
   and local logic do not prove attestation, channel membership, or production
   readiness.
7. Audit telemetry and crash settings separately from product privacy: list
   collected operational categories, opt-out configuration, disclosure,
   retention, deletion, and reviewer-facing permission rationale.
8. For access, build, channel, or registration failure, record the exact first
   failure and bounded recovery. Do not retry blindly or turn a source
   instruction into an observed account result.

#### Identity and distribution map

| Layer | Owns | Must not be confused with |
| --- | --- | --- |
| MMA organization | company-level membership and admin control | a tester Meta Account or DAT project |
| Wearables team | developers who can manage the Developer Center workspace | an iOS/Android app target |
| Project | product-level configuration and versions | a package/artifact or physical pair |
| Platform app | iOS bundle/package identity and app attestation configuration | a consumer product label such as Gen 2/Gen 3 |
| Version | immutable product/configuration snapshot for distribution | the SDK package version alone |
| Release channel | tester distribution and selected version | Developer Mode or public App Store/Play approval |
| Meta AI app state | connected-app permissions and selected channel on a tester device | account authorization or physical capability proof |
| DAT app on glasses/firmware | runtime provisioning and compatibility | project configuration or signed build evidence |

#### Required output

- named organization/team/project/platform/operator and access state, with
  secrets and private account identifiers redacted;
- project/configuration matrix for iOS, Android, and Web Apps as applicable;
- product listing and permission-justification review;
- version/build/channel/tester matrix with Developer Mode versus signed
  release distinction;
- telemetry/crash opt-out and product privacy/retention/deletion separation;
- first failure, bounded recovery, and post-action state;
- exact evidence IDs from the operations contract;
- unresolved access, account, package, device, firmware, tester, channel, and
  production gates.

#### Hard boundaries

- Never print, commit, paste into fixtures, or archive GitHub tokens, client
  tokens, application IDs paired with secrets, invitations, emails, device
  identifiers, or private diagnostics.
- Never treat a public guide, API reference, build status, invitation sent, or
  Developer Center screenshot as proof that a tester accepted, a device
  registered, or a physical capability worked.
- Never equate an MMA organization with a tester Meta Account; preserve both
  identity systems and their distinct access paths.
- Never equate Developer Mode with attested release-channel distribution,
  signed Android/iOS delivery, App Store/Play approval, or production.
- Never equate a callback, application ID, client token, app signature, channel,
  or attestation result with permission, physical capability, or on-device
  processing; keep the values and callback payloads redacted.
- Never treat telemetry opt-out or crash opt-out as a complete privacy program;
  audit collection, purpose, disclosure, retention, deletion, and permission
  justification separately.
- Never delete or restore a project, revoke a tester, switch a channel, or
  publish a version without explicit live-operation authorization and a named
  target.
- Never infer Gen 3 support, Display capability, or device compatibility from
  project configuration or product listing text.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Route planner](../meta-wearables-route-planner/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Android API atlas](../meta-dat-android-api-atlas/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)
- [Security and attestation](../meta-wearables-security-attestation/SKILL.md)

#### Sources

- [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat)
- [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/)
- [Onboarding and organization management](https://wearables.developer.meta.com/docs/onboarding-and-organization-management)
- [Manage projects](https://wearables.developer.meta.com/docs/manage-projects)
- [Set up release channels](https://wearables.developer.meta.com/docs/set-up-release-channels)
- [Full security/attestation reference](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)

---

### Meta Wearables device compatibility

**Name.** `meta-wearables-device-compatibility`

**When to use.** Resolve Meta Wearables product labels, DAT iOS/Android versions, Web Apps revisions, glasses firmware, Meta AI companion versions, and capability support without inventing Gen 3 or “regular SDK” mappings. Use for Gen 2/Gen 3 support, firmware drift, version-dependency tables, device compatibility failures, release upgrades, cross-platform matrices, or any claim that a named Ray-Ban/Meta wearable is supported.

Resolve compatibility as a versioned, observed tuple rather than a Boolean
product label. This role owns the evidence needed to decide whether a named
DAT iOS, DAT Android, native Display, Web App, or phone-fallback route is safe
for a particular product, firmware, companion version, and release channel.

#### Read before acting

- Read [version-dependency and device-compatibility evidence](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md)
  and its [compatibility contract](references/compatibility-contract.md).
- Use the [compatibility evidence-packet template](references/compatibility-evidence-packet.yaml)
  and run `python3 scripts/validate_compatibility_packet.py <packet>` before
  treating a compatibility handoff as ready for implementation or physical
  testing.
- Read the [device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md)
  before interpreting Gen 2, Meta Glasses, Display, or Gen 3 wording.
- Read the [full SDK capability matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md)
  before calling a route “full” or translating capability parity across Swift,
  Kotlin, and Web Apps.
- Read the [operational readiness route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md)
  for companion, firmware, on-glasses DAT-app, transport, thermal/power,
  update, and recovery evidence.
- Read the [security/attestation route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md)
  for account mode, release channel, callback, identity, and credential gates.
- Read the [device-proof route](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md)
  before promoting source, mock, browser, connected, or build results to
  physical or release claims.
- Refresh the official [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md),
  [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md),
  [version-dependencies page](https://wearables.developer.meta.com/docs/develop/dat/version-dependencies/),
  [full reference](https://wearables.developer.meta.com/llms.txt?full=true), and
  [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp).

#### Compatibility workflow

1. Freeze exact platform, route, package/artifact and Web App revision, source
   URLs, retrieval date, and intended evidence level.
2. Resolve the retail name to a product/SKU and observed runtime identity.
   Keep “Gen 3” unresolved unless a current official mapping exists.
3. Read the authenticated version-dependency table. If it is inaccessible,
   record `access-gated`; never fill values from memory or community reports.
4. Inspect the actual target configuration and artifact/package symbols. Treat
   0.8→0.9 migration notes as compile gates, not optional prose.
5. Record the complete tuple: phone OS/build, Meta AI version, glasses firmware,
   on-glasses DAT-app version, link/transport, registration/permission/
   compatibility state, account mode, and observed `DeviceType`.
6. Exercise requested capabilities independently, including a negative path
   and recovery path. For Web Apps, separate simulator, hosted URL, add/launch,
   and physical Display results.
7. Classify each result with its actual evidence level and produce a fallback
   for every unresolved or unsupported capability.
8. Add a refresh trigger for firmware, companion, DAT app, OS, artifact, Web App
   runtime, release channel, or product-label changes.
9. Validate the sanitized packet. A `completed` packet requires connected tuple,
   physical capability, and release evidence rows; a Gen 3 packet cannot close
   without `COMP-GEN3-01` physical/release evidence.

#### Fast path

Normalize one tuple—product label, runtime `DeviceType`, firmware, companion/DAT app, SDK artifact, target, and capability response—and mark every field observed, inferred, or `to-verify`. Resolve the requested capability only after the tuple is usable; never start implementation from a marketing label.

#### Required output

- exact source and package/artifact snapshot;
- product-label → SDK route → runtime identity matrix;
- version-dependency access state and values only when directly observed;
- complete compatibility tuple;
- capability-by-capability status (`current`, `to-verify`, `device-gated`,
  `source-conflict`, `unsupported`, or fallback);
- migration implications for the selected DAT 0.9 target;
- first failure, bounded recovery, and post-action state for drift/failure;
- evidence rows `COMP-SOURCE-01` through `COMP-RELEASE-01`;
- explicit Gen 2 treatment and Gen 3 status;
- unresolved gaps and next refresh trigger.
- validator receipt from `validate_compatibility_packet.py`;

#### Hard boundaries

- Do not map Gen 3 to `.metaGlasses`, `.rayBanMeta`, Display, or any enum from a
  product announcement.
- Do not call a consumer label, `DeviceType`, version number, or registration
  success proof of camera, audio, Display, input, thermal, or release support.
- Do not treat a public issue as first-party compatibility policy.
- Do not treat a login-gated page as known; record the access gap.
- Do not equate DAT iOS, DAT Android, and Web Apps symbols by naming similarity.
- Do not place credentials, package tokens, tester data, raw media, private
  project IDs, or unredacted device diagnostics in outputs or archives.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Full SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [Route planner](../meta-wearables-route-planner/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)
- [Security and attestation](../meta-wearables-security-attestation/SKILL.md)

#### Sources

- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [DAT Android package registry](https://github.com/facebook/meta-wearables-dat-android/packages)
- [Wearables version dependencies](https://wearables.developer.meta.com/docs/develop/dat/version-dependencies/)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp)

---

### Meta Wearables device proof

**Name.** `meta-wearables-device-proof`

**When to use.** Plan and audit reproducible target preflight and evidence for Meta Wearables apps across source inspection, iOS/Android/Web App inventory, unit/build checks, DAT MockDevice, browser simulator, connected-device tests, and physical Ray-Ban/Oakley/Meta glasses. Use whenever a result could be mistaken for hardware, model, camera, audio, Display, input, or release proof.

Keep simulated, connected, and physical observations separate. The purpose of this skill is to prevent a passing build or mock from becoming an unsupported product claim.

#### Read before acting

- Inspect the actual target, SDK tag/commit, test scheme, mock/simulator setup, and available hardware/account access.
- Read [MockDevice testing and evidence](../../knowledge-base/70-meta-wearables/07-mockdevice-testing-and-evidence.md), [device models and capabilities](../../knowledge-base/70-meta-wearables/05-device-models-and-capability-matrix.md), and [privacy/publishing](../../knowledge-base/70-meta-wearables/08-privacy-publishing-and-release.md).
- Read the [device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md) before naming a generation or interpreting a model enum.
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) before naming firmware support, interpreting a compatibility failure, or promoting Gen 2/Gen 3 evidence.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) when the claim includes local processing, raw-data handling, thermal safety, or fallback behavior.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the named task includes Display actions, D-pad/EMG/temple gestures, motion/orientation, geolocation, or sensor quality.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when the run includes firmware, companion, on-glasses DAT-app provisioning, release-channel, update-required, thermal/power, or recovery behavior.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when the evidence claim depends on shared reducers, adapter fakes, capability ownership, or fallback seams.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) when the script includes Bluetooth/Wi-Fi, HFP/A2DP, sustained streaming, route changes, thermal/power, or link recovery.
- Read [debugging, observability, and diagnostic evidence](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) for app-visible DAT readiness, first-failure, event-digest, and redacted diagnostic evidence.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when a proof run depends on callback identity, Developer Mode/release attestation, signed artifacts, tester/channel access, or redacted credentials.
- Run the bundled [target-surface inspector](scripts/inspect_target_surfaces.py) against the actual target workspace before loading the target-preflight reference. If the knowledge base is a separate sibling, pass that app root to the inspector and to the team runner’s `--target-root`. `TARGETS_PRESENT` permits target-specific preflight; `TARGETS_PARTIAL` and `NO_TARGET` require the project bootstrap packet.
- When an iOS `TARGETS_PRESENT` target exists, run the bundled [redacted iOS target receipt runner](scripts/run_ios_target_preflight.py) with an explicit project/workspace, scheme, configuration, and destination. Use `--test` only for an intentional build/test observation; its JSON receipt contains safe settings and package-lock facts, never raw xcodebuild output or credential values.
- When an Android `TARGETS_PRESENT` target exists, run the bundled [redacted Android target receipt runner](scripts/run_android_target_preflight.py) with the actual Gradle root, module, variant, and route. It inventories the target-owned build graph, DAT coordinates, manifest keys, source migration markers, toolchain signals, and credential presence without printing values or resolving/publishing dependencies. A static pass is not an Android compile or device result.
- When iOS UI tests need cross-process MockDevice control, use the [source-aligned MockDevice test-client starter](../meta-wearables-implementation-recipes/assets/meta-wearables-ios-mockdevice-test-client-starter/MetaWearablesMockDeviceTestClientStarter.swift) and record the app-process test-server setup, UI-test client, sanitized state/actions, and teardown separately. `MWDATMockDeviceTestClient` is test-only evidence and never a physical-device result.
- Use the [workspace device and release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md) for the full route matrix, and read the portable [execution reference](references/execution-packet.md) when the package is used outside this workspace.
- Load the [capability/evidence plan](../meta-wearables-full-sdk-audit/references/capability-evidence-plan.yaml) for the selected capability; carry its required evidence levels and proof task IDs into the run instead of inventing a local task list.
- Read the portable [target-preflight reference](references/target-preflight.md) before `BUILD-01`, `REL-01`, `PRE-*`, connected, physical, or release work; run `python3 scripts/validate_target_preflight.py` after editing it.
- Refresh the official [DAT iOS MockDevice skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/mockdevice-testing), [Mock Device Kit](https://wearables.developer.meta.com/docs/mock-device-kit), and [iOS testing guidance](https://wearables.developer.meta.com/docs/testing-mdk-ios).
- Check the current [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md) for mock-link and device-model behavior before relying on an older fixture.

#### Fast path

Run the target-surface inspector, then only the lowest unmet `PRE-*` prerequisite for the named claim. Promote evidence one level at a time with exact target, build, device, time, and result; stop when a required gate is missing instead of rerunning downstream steps.

#### Evidence ladder

| Level | What it proves | What it does not prove |
| --- | --- | --- |
| source | an official API/product rule is documented | package compiles or device supports it |
| static/config | target files, permissions, URL, privacy, or package wiring are present | runtime registration or hardware behavior |
| build/test | exact code compiles and automated tests pass | glasses rendering, radio, camera, audio, Display, or ergonomics |
| mock | adapter state/error paths respond to controlled device events | physical timing, brightness, firmware, real input, or sensor/media quality |
| browser simulator | Web App layout/input logic in the simulator | deployed URL and physical Display behavior |
| connected device | a named paired model completed the observed path | other models, future generations, or release readiness |
| physical release proof | the target glasses completed the scripted flow with artifacts | untested models, claims beyond the script, or App Store approval |

#### Proof workflow

1. Freeze the source snapshot: SDK version, repository commit/tag, docs date, target commit, build configuration.
2. Record the exact `DeviceType`, product/model name, firmware, phone OS, app build, and account/access condition when known.
3. Run the target preflight: freeze the actual iOS scheme/target and
   `Package.resolved`, Android module/variant and resolved Maven graph, or Web
   App revision/origin/input configuration. Redact secrets before storing it.
4. Run static/config checks for package products, callback, permissions, entitlements, privacy manifest, URL, and fallback.
5. Run unit/integration tests with deterministic mock events: register, permission denied, session start/stop, disconnect, error, camera/audio stop, Display unsupported, and input actions.
6. Run the browser simulator for Web Apps and record URL, canvas, focus, input, loading, error, and performance observations.
   Keep text composer, offline cache, Escape/back, extended gestures, and
   sensors as separate `source-conflict`/`to-verify` rows because the current
   public index and toolkit `main` disagree about them.
7. Run connected-device tests when a supported device is available. Capture only sanitized evidence: model/firmware, test ID, result, timestamp, and relevant error code.
8. Run the physical script on the exact target before claiming real camera, audio, Display, button/D-pad/EMG, Wi-Fi, latency, or comfort behavior.
9. For a failure or update-required result, preserve the first failing state,
   apply one documented recovery, and record the post-action state; distinguish
   `recovery-attempted` from `recovery-observed`.
10. Fill one sanitized result record per packet task. Use the packet’s artifact and redaction contract; never retain raw media or device identifiers merely to make a run reproducible.
11. Mark every unrun step `not run` or `to verify`; never convert it to pass because a neighboring step passed.

#### Physical script minimum

- registration and permission recovery;
- session connect, disconnect, and reconnect;
- phone fallback with the glasses absent;
- camera/audio start and clean stop if in scope;
- Display render, focus, primary action, back/dismiss, timeout, and disconnect if in scope;
- Web App launch, 600×600 layout, focus, D-pad/available input, loading, error, and exit if in scope;
- thermal/battery/latency observations proportional to the feature;
- companion/firmware/on-glasses DAT-app, mode/channel, first failure, recovery action, and post-action state;
- no private media or identifiers in screenshots/logs.

#### Required output

Return the target-surface inspector receipt, a preflight summary, the selected capability/evidence-plan entry, and an evidence matrix. Every row contains:
feature, exact target, source, test level, observed result, artifact pointer, and
remaining gate. Include the `PRE-*` status before promoting any build or device
claim.

#### Hard boundaries

- Do not call simulator, mock, or iPhone-only behavior “on-device glasses support.”
- Do not generalize a result from Ray-Ban to Oakley or from one generation to another.
- Do not use production credentials, raw personal media, or unredacted device identifiers in fixtures.
- Do not announce release readiness while required physical, privacy, access, or publishing gates remain open.
- Do not upgrade a Web App toolkit feature to supported merely because it works
  in a desktop simulator; close the current Developer Center/toolkit conflict
  on the exact firmware or keep the documented fallback.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Route planner](../meta-wearables-route-planner/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Device and release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md)
- [Device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md)
- [On-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md)
- [Operational readiness and recovery](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md)
- [Transport and runtime reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Application architecture and platform boundaries](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md)
- [Target preflight reference](references/target-preflight.md)
- [Redacted iOS target receipt runner](scripts/run_ios_target_preflight.py)
- [Redacted Android target receipt runner](scripts/run_android_target_preflight.py)
- [iOS device release proof](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-device-release-proof/SKILL.md)
- [iOS testing and release assurance](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-testing-and-release-assurance/SKILL.md)

#### Sources

- [DAT iOS MockDevice testing skill](https://github.com/facebook/meta-wearables-dat-ios/tree/main/plugins/mwdat-ios/skills/mockdevice-testing)
- [Meta Mock Device Kit](https://wearables.developer.meta.com/docs/mock-device-kit)
- [Testing with the Mock Device Kit on iOS](https://wearables.developer.meta.com/docs/testing-mdk-ios)
- [Meta DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Apple target build settings](https://developer.apple.com/documentation/xcode/configuring-the-build-settings-of-a-target/)
- [Apple Swift package CI/build guidance](https://developer.apple.com/documentation/xcode/building-swift-packages-or-apps-that-use-them-in-continuous-integration-workflows)
- [Gradle dependencyInsight](https://docs.gradle.org/current/userguide/command_line_interface.html)

---

### Meta Wearables full-SDK audit

**Name.** `meta-wearables-full-sdk-audit`

**When to use.** Audit whether a Meta Wearables iOS, Android, or Ray-Ban Display Web App request actually covers the full public SDK surface, exact modules/artifacts, capability gates, upstream source conflicts, privacy boundaries, and required evidence. Use when a request says full SDK, regular SDK, all capabilities, Gen 2/Gen 3 support, or cross-platform parity.

Use this role before implementation when “full SDK,” “regular SDK,” “all
capabilities,” or cross-platform parity could hide an unverified module,
consumer feature, stale upstream example, or unsupported device claim.

#### Read before acting

- Inspect the real iOS package graph or Android Gradle dependency graph, target
  settings, privacy files, and existing adapter.
- Read [the full capability and source-conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md), [the device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md), [the iOS API atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md), [the Android API atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md), [the plugin matrix](../../knowledge-base/70-meta-wearables/13-public-plugin-and-skill-matrix.md), and [the Android parity route](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md).
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) before treating IMU, EMG, temple, browser sensor, or Display input wording as a complete capability.
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) before treating Gen 2/Gen 3, firmware, companion versions, or support-table values as resolved.
- Load the portable [source-pinned surface manifest](references/surface-manifest.yaml) before a full-SDK/parity audit; use it as a routing snapshot, never as generated API or hardware proof.
- Load the portable [Meta team manifest](../meta-wearables-agentic-team/references/team-manifest.yaml) and run `python3 ../meta-wearables-agentic-team/scripts/validate_team_manifest.py` in the workspace; use it to prove that selected upstream roles have local owners and device-claim gates.
- Resolve user wording through the manifest `terminology_contract` before routing; “regular SDK” is an ambiguous phrase, “full SDK” is a composite audit, and “Gen 3” remains an unresolved alias until official and named-device evidence close it.
- Load its `preflight_contract`, read the [target-preflight packet](../meta-wearables-device-proof/references/target-preflight.md), and select the relevant `PRE-*` tasks before build, connected, physical, signed, or release-channel work; preflight freezes facts but does not upgrade evidence.
- Load the portable [capability/evidence plan](references/capability-evidence-plan.yaml) and run `python3 scripts/validate_capability_evidence_plan.py`; use it to carry owner roles, implementation route, privacy path, fallback, minimum evidence levels, and proof task IDs for every selected capability.
- Filter `api_surface.rows` by the selected journey/platform and carry each row's source anchors, status, compile/runtime gate, privacy path, fallback, and migration note into the audit; these rows remain source routing, not generated-API or physical proof.
- Run `python3 scripts/validate_surface_manifest.py` after any manifest edit and before packaging; treat count, exact upstream plugin-role list, source-anchor, required-field, duplicate-ID, and secret-scan failures as refresh blockers.
- Run `python3 scripts/validate_capability_evidence_plan.py` after any capability/evidence-plan edit and before packaging; treat capability-ID, surface, preflight-task, evidence-task, level, duplicate, and secret-scan drift as blockers.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) for processing location, consent, raw-data, thermal, network, retention, and fallback claims.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) for firmware, companion, on-glasses DAT-app provisioning, Developer Mode versus release-channel, version-dependency, and recovery claims.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when the request spans iOS, Android, native Display, Web Apps, shared state, or a phone fallback.
- Read [Developer Center project and release operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md) when the request includes app identity, permission rationale, versions, channels, testers, telemetry, or release access.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when the request includes identity keys, callbacks, Developer Mode, release attestation, package/signing credentials, Web App origin, privacy-manifest/App Store gates, or an “on-device” trust claim.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) when the request includes Wi-Fi/local network, Bluetooth/link behavior, HFP/A2DP, backpressure, latency, thermal/power, disconnect, or recovery.
- Read [debugging, observability, and diagnostic evidence](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when the request includes a DAT failure, live DAT Inspector/MCP, readiness, event/error diagnosis, or a diagnostic handoff.
- Refresh the official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), [iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp), [full platform reference](https://wearables.developer.meta.com/llms.txt?full=true), and [Wearables MCP](https://mcp.developer.meta.com/wearables).
- Read [the capability audit fixture](references/capability-audit-fixture.md) when evaluating a “full SDK” or parity brief.

#### Audit workflow

1. Freeze platform, target, OS/min SDK, package/artifact version, repository
   revision, Meta AI version, device wording, requested capabilities, and
   intended evidence level.
2. Load the source-pinned manifest, team manifest, resolve `terminology_contract`, and load the capability/evidence plan; select the
   matching journey, platform atlas, API-surface rows, capability row, mapped
   `PRE-*` tasks, owner role, implementation route, privacy path, fallback,
   minimum evidence levels, proof tasks, source conflicts, generation status,
   and evidence IDs before making a coverage claim.
3. Inventory the exact upstream plugin roles and agent-facing root surfaces from each manifest
   `source_inventory.<lane>.plugin_roles` list before mapping them to local
   handoffs; a role count without exact-name coverage is incomplete. Then
   verify the lane `AGENTS.md`, `README.md`, `install-skills.sh`,
   `.codex-plugin/plugin.json`, and public docs MCP references as source/tool
   routing inputs only. Then
   inventory native modules: iOS `MWDATCore`, `MWDATCamera`, `MWDATDisplay`,
   `MWDATMockDevice`, and the test-only `MWDATMockDeviceTestClient`; Android
   `mwdat-core`, `mwdat-camera`, `mwdat-display`, `mwdat-mockdevice`; and Web
   Apps as a separate hosted runtime. Keep test-process products separate from
   runtime capability claims.
4. Use the platform-specific API atlas to resolve exact iOS package products
   and Android Maven artifacts/symbols before comparing cross-platform concepts.
5. Map every requested capability to `current`, `to-verify`, `source-conflict`,
   `unsupported`, or a platform-specific fallback. Include registration,
   permissions, device selection, session, camera/photo, audio, Display,
   sensors, health/update, MockDevice, debugging, privacy, and release.
6. Search upstream `AGENTS.md`, plugin skills, samples, changelog, API
   reference, and MCP results for stale versions or conflicting symbol/config
   guidance. Let the selected artifact/API reference win only after recording
   the conflict and target build gate.
7. Separate consumer product wording, SDK model identity, and observed runtime
  capability. Treat “Gen 3” and “regular SDK” as unresolved until the current
  source and named target establish them.
8. Classify each data path as glasses-native, phone-local, remote, mixed, or
   unknown; audit consent, retention, lifecycle/thermal behavior, and the typed
   fallback for every requested capability.
9. For any registration, session, update, or release failure, freeze the full
   operational tuple and route the first failing state through the recovery
   contract; keep attempted recovery separate from observed recovery.
10. Define the shared product/domain contract and platform-specific adapter
   boundary; preserve iOS, Android, native Display, Web App, and phone fallback
   semantics rather than flattening them into a common symbol set.
11. Return the smallest implementation route, missing proof rows, phone/web
   fallback, compliance owner, privacy owner, and next source-refresh trigger.

#### Fast path

Filter the request into platform, surface, and capability rows, run the terminology contract first, then count coverage and evidence gaps. Use a full-surface traversal only when the request is genuinely full or parity-focused; for one feature return selected rows plus the explicitly non-selected lanes.

#### Required output

- exact source/package/artifact snapshot and date;
- manifest ID/revision, selected journey, and manifest rows used;
- team manifest ID/revision, selected local role IDs, upstream handoff IDs, and
  device-claim gates used;
- exact upstream iOS, Android, and Web Apps plugin-role lists, counts, and local
  handoff coverage;
- terminology-contract term, resolution status, canonical route candidates, and
  evidence boundary used to interpret the user’s device/SDK wording;
- selected preflight contract reference, applicable `PRE-*` task statuses, and
  redacted target/dependency/identity/data-path handoff;
- selected capability/evidence-plan entry with owner roles, implementation
  route, privacy path, fallback, required evidence levels, and proof task IDs;
- normalized API-surface row IDs used, including source anchors, status,
  compile/runtime gates, privacy paths, fallbacks, and migration notes;
- full-reference journey-section inventory covering setup/hardware, iOS,
  Android, Display, lifecycle/permissions, HFP/A2DP, MockDevice, AI/MCP/tooling,
  organization/project/release-channel administration, and Web Apps;
- full module and capability matrix with platform-specific symbols;
- dedicated iOS and Android API-atlas snapshots, including artifact/source/
  compile status and the Android `Session`/`DeviceSession` conflict;
- stale/conflicting source ledger and migration actions;
- product-label, SDK-identity, and runtime-capability matrix with explicit
  Gen 2, Meta Glasses, Display, and Gen 3 treatment;
- target, account, permission, privacy, device, firmware, and release gates;
- version-dependency access state, full phone/companion/on-glasses-DAT-app/
  firmware/artifact/runtime tuple, first-party versus community signal class,
  and `COMP-*` compatibility evidence rows;
- organization/team/project/platform-app, permission-rationale, version/build,
  tester/channel, telemetry, and account-recovery gates when distribution is in scope;
- processing-location, network/storage, consent, thermal/lifecycle, and fallback contract;
- transport-hop, iOS/Android configuration/parity, audio-route, queue/backpressure, thermal, and recovery contract;
- read-only debug-server/MCP baseline, first-failure graph, platform state/error map, narrow event digest, redaction review, and DBG evidence rows when diagnosis is in scope;
- operational tuple, Developer Mode/release-channel distinction, provisioning state, first failure, bounded recovery, and post-action evidence;
- shared state/event contract, platform adapter map, concurrency/resource ownership, stale-event strategy, and reducer/fake/mock/browser/physical test seams;
- mock/browser/connected/physical evidence plan;
- explicit Gen 2 mapping and Gen 3 `to-verify` status unless current official
  mapping and named-device evidence establish otherwise;
- unresolved gaps. Never return “full SDK” as an unqualified conclusion.

#### Hard boundaries

- Never count a repository plugin role, `llms.txt` entry, or MCP search result as
  a compiled target module or hardware capability.
- Never translate Swift symbols into Kotlin or Web APIs by naming similarity.
- Never treat the Android sample’s phone `AudioRecord` as a glasses microphone,
  or the iOS HFP route as an Android DAT audio API.
- Never map “Gen 3” to `.metaGlasses`, `.rayBanMeta`, Display, or any product
  announcement without a current public mapping and named runtime evidence.
- Never call a mock, browser simulator, compile, account configuration, or
  signed artifact physical or production proof.
- Never include tokens, client credentials, raw media, device identifiers, or
  private diagnostic payloads in the audit or archive.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [DAT API atlas](../meta-dat-api-atlas/SKILL.md)
- [DAT Android API atlas](../meta-dat-android-api-atlas/SKILL.md)
- [Developer Center operations](../meta-wearables-developer-operations/SKILL.md)
- [Transport and runtime reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Device compatibility](../meta-wearables-device-compatibility/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)

#### Sources

- [Full capability and source-conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md)
- [Device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md)
- [Version-dependency and device-compatibility evidence](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md)
- [Source-pinned surface manifest route](../../knowledge-base/70-meta-wearables/27-source-pinned-surface-manifest.md)
- [Capability/evidence plan](references/capability-evidence-plan.yaml)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT iOS AGENTS.md](https://github.com/facebook/meta-wearables-dat-ios/blob/main/AGENTS.md)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android AGENTS.md](https://github.com/facebook/meta-wearables-dat-android/blob/main/AGENTS.md)
- [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Wearables MCP](https://mcp.developer.meta.com/wearables)
- [Ray-Ban Meta Gen 2 announcement](https://about.fb.com/news/2025/09/ray-ban-meta-gen-2-better-battery-life-video-capture/)
- [Meta Glasses announcement](https://about.fb.com/news/2026/06/meta-essilorluxottica-partner-launch-meta-glasses/)

---

### Meta Wearables implementation recipes

**Name.** `meta-wearables-implementation-recipes`

**When to use.** Turn a selected Meta Wearables API row and vertical-slice playbook into source-aligned Swift, Kotlin/Java, or Ray-Ban Display Web App implementation scaffolding with explicit compile, privacy, lifecycle, fallback, and physical-device gates. Use when building or reviewing a concrete iOS DAT, Android DAT, native Display, Web App, or cross-platform wearable feature after route selection.

Use this role after route selection and the full-surface audit. It converts one
outcome into a narrow adapter implementation handoff; it does not flatten DAT,
native Display, Web Apps, and phone fallback into one fictional SDK.

#### Read before acting

- Read the [source-pinned API manifest](../meta-wearables-full-sdk-audit/references/surface-manifest.yaml) and filter exact `IOS-*`, `AND-*`, `WEB-*`, compatibility, and evidence rows.
- Read the [vertical-slice playbooks](../meta-wearables-app-architecture/references/vertical-slice-playbooks.md) and choose one playbook before writing scaffolding.
- Read the [application architecture contract](../meta-wearables-app-architecture/references/architecture-contract.md) for ownership, epochs, cancellation, fallback, and test seams.
- Read the [iOS API atlas](../../knowledge-base/70-meta-wearables/10-dat-ios-api-surface-atlas.md) or [Android API atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md) for the selected target; read the [Web Apps route](../../knowledge-base/70-meta-wearables/06-web-apps-display-and-input.md) for hosted work.
- Load the selected capability entry from the [capability/evidence plan](../meta-wearables-full-sdk-audit/references/capability-evidence-plan.yaml) and preserve its owner roles, implementation route, privacy path, fallback, required evidence levels, and proof task IDs in the build handoff.
- Read the [on-device compliance contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) before describing camera, audio, sensor, storage, or network processing.
- Inspect the actual target project, package graph, deployment/min SDK, privacy configuration, entitlements/manifest, and existing adapter before selecting a recipe.
- Read the [target-preflight reference](../meta-wearables-device-proof/references/target-preflight.md) when a concrete build, connected device, physical device, signed artifact, or release claim is in scope; carry its `PRE-*` status into the handoff.
- Create or update the [implementation-handoff packet](references/implementation-handoff-template.yaml) before scaffolding and run `python3 scripts/validate_implementation_handoff.py <packet> --manifest <surface-manifest> --plan <capability-plan> --team-manifest <team-manifest>`. Use `--allow-placeholders` only for the bundled template; a real ready packet must resolve its target tuple and `to-verify` fields.
- Use the [compile-tested shared-domain starter](assets/meta-wearables-domain-starter) for typed product state, epoch handling, teardown ordering, and phone fallback. It deliberately has no DAT/Android/Web SDK imports; its passing tests prove shared reducer behavior only, never SDK compilation or hardware support.
- Use the [source-aligned iOS camera starter](assets/meta-wearables-ios-camera-starter/MetaWearablesCameraStarter.swift) for DAT 0.9 camera/photo work. It type-checks against the selected `MWDATCore`/`MWDATCamera` simulator frameworks, keeps raw frames inside the adapter, and makes the photo handoff, permission, stream, and child-before-parent teardown gates explicit.
- Use the [source-aligned Android camera starter](assets/meta-wearables-android-camera-starter/MetaWearablesAndroidCameraStarter.kt) for DAT 0.9 Android camera/photo work. It follows the official `CameraAccess` `DatResult`/`Flow` shape, but remains an Android target compile gate until the selected Maven artifacts resolve.
- Use the [source-aligned iOS MockDevice starter](assets/meta-wearables-ios-mockdevice-starter/MetaWearablesMockDeviceStarter.swift) for deterministic iOS DAT fixtures. It type-checks against the selected DAT 0.9.0 `MWDATCore`/`MWDATMockDevice` interfaces and keeps mock lifecycle, permission, media, and captouch controls outside shared product state.
- Use the [source-aligned iOS MockDevice test-client starter](assets/meta-wearables-ios-mockdevice-test-client-starter/MetaWearablesMockDeviceTestClientStarter.swift) when XCUITest must control the app process's official MockDevice test server. It type-checks against the test-only `MWDATMockDeviceTestClient` product and keeps the app-process/server versus UI-test-process boundary explicit.
- Use the [source-aligned Android MockDevice starter](assets/meta-wearables-android-mockdevice-starter/MetaWearablesAndroidMockDeviceStarter.kt) for deterministic Android DAT instrumentation fixtures. It follows the official 0.9.0 `MockDeviceKit` sample, but remains an Android target compile gate until the selected Maven artifacts resolve.
- Use the [source-aligned native iOS Display starter](assets/meta-wearables-ios-display-starter/MetaWearablesDisplayStarter.swift) when the selected vertical slice is native DAT Display. It targets the DAT 0.9.0 shapes resolved in the reference target: `supportsDisplay()`, `DeviceSession` state/error streams, `session.addDisplay()`, `Display.statePublisher`, `Display.send(FlexBox)`, and child-before-parent teardown. Type-check it against the selected SPM product before adapting it; the asset is not standalone proof of registration, physical rendering, or input behavior.
- Use the [source-aligned native Android Display starter](assets/meta-wearables-android-display-starter/MetaWearablesAndroidDisplayStarter.kt) when the selected vertical slice is native DAT Android Display. It follows the official 0.9.0 shape: `SpecificDeviceSelector`, `Wearables.createSession(...).fold`, `DeviceSession` `Flow` state/error collection, `addDisplay()`, `Display.state`, `sendContent`, `buttonGroup`, `removeDisplay()`, and parent-session stop. Resolve the exact Maven artifacts and compile it in the selected Android target; this knowledge base does not treat the uncompiled portable asset as target or hardware proof.
- When Android is selected but no Gradle target exists, copy the [credential-safe Android DAT target starter](assets/meta-wearables-android-target-starter/README.md) into a new sibling project. It pins the official 0.9.0 full-artifact DAT graph and GitHub Packages route, keeps Developer Center values outside the archive, and stops at a target-owned `Wearables.initialize(context)` bootstrap until the selected implementation handoff is ready.
- When the selected route includes a Ray-Ban Display Web App, copy or adapt the [dependency-free Web App starter](assets/meta-wearables-web-starter). Its reducer tests prove bounded 600×600 state/input/fallback behavior only; the host toolkit contract, browser simulator, hosted URL, and physical Display run remain separate gates.
- Run `python3 scripts/validate_recipe_reference.py` after editing the bundled reference and before packaging; treat missing sections, unbalanced code fences, marker loss, or secret-like literals as blockers.

#### Workflow

1. Create the implementation-handoff packet and validate its route, API rows,
   evidence tasks, owners, target tuple, lifecycle, privacy/data path, tests,
   fallback, and next proof task.
2. Freeze the outcome, primary surface, phone fallback, exact target tuple,
   package/artifact or hosted revision, requested proof level, and open gates.
3. Select one playbook and carry its API/evidence row IDs into the recipe.
4. Resolve the selected release’s generated API before writing imports or
   method signatures. Label any source-index-only or moving-reference name
   `to-verify`; use a clearly marked placeholder instead of inventing syntax.
5. Build the smallest adapter skeleton: registration/permission, device and
   capability gate, session/capability ownership, bounded data path, user
   projection, stop/cancellation, and typed fallback.
6. Keep shared product state free of MWDAT types, Kotlin classes, browser
   events, raw frames, raw audio, tokens, and device identifiers.
7. Run the shared-domain starter tests and, for a Web App route, the Web App
   starter Node tests. Then add deterministic reducer/fake/MockDevice/browser
   cases before requesting a
   target build. Record what each evidence level cannot prove.
8. If Android has no target, instantiate the target starter in a sibling folder,
   configure private values through ignored local properties or environment
   variables, and record the resulting `PRE-*` target receipt before adding
   capability code.
9. Return the implementation handoff, unresolved compile questions, privacy and
   retention decisions, exact next command/task, and the physical/release gate.

#### Fast path

Freeze one playbook and target tuple; validate the handoff packet, select one starter, compile or type-check the adapter seam, and add only target-specific glue. Stop when a `to-verify` symbol or missing artifact blocks compile instead of hiding it behind placeholder code.

#### Recipe selection

Load [implementation-recipes.md](references/implementation-recipes.md) only for
the selected surface. It contains source-aligned shapes for:

- iOS DAT 0.9 session/camera and native Display adapters;
- Android DAT 0.9 `DeviceSession`/`Camera`/Display builder boundaries;
- Ray-Ban Display Web App focus/input/host-exit handling; and
- one shared domain outcome with platform-specific adapters.

These are scaffolds, not claims that the user’s target compiles. The exact
package, generated API, OS, firmware, companion, and channel remain gates.
The Android target starter is a build-graph seed, not a DAT compile receipt;
its environment/local-properties credential path must remain outside source
control and archives.

#### Hard boundaries

- Use the 0.9 consolidated `Camera` ownership path; do not generate removed
  `addStream` code from older examples.
- Keep iOS `AsyncSequence`/publisher, Android `Flow`/`DatResult`, and Web App
  browser events in their adapters; translate them to typed product events.
- Do not invent a native MWDATAudio module. Name phone microphone, HFP input,
  A2DP output, and remote processing separately.
- Do not map `Gen 3` or a product label to a runtime enum, Display support, or
  sensor capability without current official mapping and named-device evidence.
- A compile, mock, browser simulator, hosted URL, Developer Mode registration,
  or signed artifact is not physical glasses or production proof.
- Do not place client credentials, raw media, transcripts, serials, or private
  project data in example code, fixtures, logs, or handoffs.

#### Required output

Return:

- selected playbook and normalized API/evidence row IDs;
- implementation-handoff packet path and validator receipt;
- shared-domain starter test command and result;
- exact target/package/artifact/hosted revision tuple and `to-verify` symbols;
- source-aligned adapter skeleton with explicit compile-gate comments;
- state owner, session epoch, resource stop order, cancellation, and fallback;
- processing location, consent, retention/deletion, network, and thermal path;
- reducer/fake/MockDevice/browser/build/connected/physical test cases;
- next proof task, unresolved questions, and source-refresh trigger.

#### Related roles

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Full-SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [iOS DAT integration](../meta-dat-ios-integration/SKILL.md)
- [Android DAT integration](../meta-dat-android-integration/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Implementation-handoff template](references/implementation-handoff-template.yaml)
- [Implementation-handoff validator](scripts/validate_implementation_handoff.py)

#### Sources

- [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [Meta Wearables Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Portable API manifest](../meta-wearables-full-sdk-audit/references/surface-manifest.yaml)
- [Vertical-slice playbooks](../meta-wearables-app-architecture/references/vertical-slice-playbooks.md)

---

### Meta Wearables input and sensors

**Name.** `meta-wearables-input-sensors`

**When to use.** Design and verify Meta Wearables physical input and sensor experiences across native DAT Display, Ray-Ban Display Web Apps, and phone fallbacks, while keeping hardware signals, browser APIs, permissions, lifecycle, and physical-device evidence separate.

Use this role whenever a Meta Wearables experience depends on a wearer gesture,
Display button, Neural Band/EMG action, temple/captouch input, inertial data,
orientation, or geolocation. It keeps the event source and evidence level
explicit so an agent does not turn a Web App browser API or a Display callback
into a cross-platform native DAT promise.

#### Read before acting

- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) and the [input/sensor contract](references/input-sensor-contract.md).
- Read [Display Access](../../knowledge-base/70-meta-wearables/04-display-access-and-glasses-ui.md) for native DAT Display capability, compact UI, ButtonGroup/action callbacks, and teardown.
- Read [Web Apps display and input](../../knowledge-base/70-meta-wearables/06-web-apps-display-and-input.md) and the official [Web App Display guidelines](https://github.com/facebook/meta-wearables-webapp/blob/main/plugins/meta-wearables-webapp/references/display-guidelines.md) for the 600x600 focus/D-pad/EMG surface.
- Read [device models and capability matrix](../../knowledge-base/70-meta-wearables/05-device-models-and-capability-matrix.md) and [device-generation/runtime support](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md) before using Gen 2, Display, or Gen 3 language.
- Read [on-device compliance](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) for sensor processing location, raw-data retention, consent, thermal, and fallback decisions.
- Read [transport and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) for link loss, stale events, disconnect, and recovery.
- Read [device and release evidence](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md) before calling input or sensor behavior connected, physical, signed, or released.
- Refresh the official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp), and [full platform reference](https://wearables.developer.meta.com/llms.txt?full=true) for version-sensitive claims.

#### Route the signal before writing code

1. Name the product surface: native DAT companion, native DAT Display, Ray-Ban Display Web App, or phone/system fallback.
2. Name the signal source: native Display tap/ButtonGroup callback, Web App keyboard/focus event, captouch/D-pad, Neural Band/EMG, temple gesture, browser motion/orientation/geolocation API, or an ordinary phone sensor.
3. Resolve the target through runtime capability metadata and the exact firmware/app/package revision. Do not use a consumer label as a capability predicate.
4. Define the event contract before the reducer: source, timestamp/epoch, semantic action, payload bounds, permission state, stale/disconnect behavior, and cancellation.
5. Keep source-conflicted Web App features independently gated. The reviewed full-reference index and the moving Web App toolkit do not agree on text composition, offline behavior, back navigation, sensors, or extended gestures.
6. Provide a phone/manual fallback for every action that can strand the wearer, and record whether the fallback is local, remote, or unavailable.

#### Native DAT Display lane

- Use the selected DAT release's public Display DSL and callback surface. The
  current public samples show `ButtonGroup`, `Button`, and `FlexBox.onTap`; they
  do not establish a separate public iOS/Android sensor module for IMU, EMG, or
  temple gestures.
- Treat Display tap/back behavior as a Display interaction contract, not as a
  general event bus shared with Web Apps.
- Gate Display with the runtime capability predicate exposed by the selected
  artifact, clear/replace the active view on terminal paths, and ignore events
  from an old session epoch.
- If the current selected artifact or authenticated reference exposes a native
  IMU/sensor API, add it as a versioned adapter with exact symbols and tests. If
  it does not, keep the native sensor row `to-verify` and use the phone sensor
  route only when the product explicitly permits that substitution.

#### Ray-Ban Display Web App lane

- Build focus-first UI. The official toolkit guidance describes D-pad/captouch
  arrow movement, Enter/EMG activation of the focused element, and Escape/back
  or a back gesture as target behavior; the full-reference index marks several
  Web App capabilities differently, so record the exact source and runtime.
- Treat Neural Band/EMG as semantic navigation/activation input unless the
  target-specific contract proves a richer gesture stream. Do not make a
  positioned cursor or continuous drag the default.
- Gate motion/orientation/geolocation with secure-context, permission, feature,
  availability, and cleanup checks. Use a demo or phone fallback when a sensor
  is absent or denied.
- Keep browser sensor events out of a native DAT adapter. A Web App can be
  hosted on HTTPS and still lack the same capability on a particular firmware,
  account, or release channel.

#### Event contract and test loop

Use a small normalized event model, for example:

```text
InputEvent {
  source: display-button | dpad | emg | temple | browser-motion |
          browser-orientation | browser-geolocation | phone-sensor
  action: previous | next | left | right | activate | back | cancel | sample
  epoch: session-or-page identity
  observedAt: monotonic/local timestamp
  payload: bounded, redacted, source-specific value
}
```

The implementation must:

- reject events from an old session/page epoch;
- debounce duplicate activations without swallowing deliberate repeats;
- stop listeners and sensor watches on screen exit, permission denial,
  disconnect, background, and terminal session states;
- surface permission, unsupported, unavailable, stale, and timeout states;
- avoid logging raw sensor traces, precise location, identifiers, or personal
  gesture data unless the product has a documented reason and retention rule;
- test denial, no sensor, duplicate event, stale event, teardown, and phone
  fallback before a physical run.

#### Fast path

Route each signal by source and host first: native Display, Web App, glasses sensor, or phone sensor. For one signal, define epoch, lifecycle, permission, and one reducer event; test duplicate, out-of-order, and disconnect behavior before adding physical-input proof.

#### Required output

- route and exact signal-source decision;
- capability/permission/secure-context matrix with unresolved rows;
- normalized input/sensor event contract and lifecycle owner;
- native DAT/Web App/phone adapter boundary;
- mock/browser fixture and physical named-target script;
- privacy, retention, thermal, and fallback notes;
- evidence rows `INP-SOURCE-01`, `INP-STATIC-01`, `INP-MOCK-01`,
  `INP-BROWSER-01`, `INP-CONNECTED-01`, `INP-PHYSICAL-01`,
  `INP-RECOVERY-01`, and `INP-RELEASE-01` with status.

#### Hard boundaries

- Do not claim native DAT IMU, EMG, Neural Band, temple-gesture, or geolocation
  APIs from a conceptual full-reference label alone.
- Do not claim a Web App sensor or composer feature is supported because a
  browser or toolkit source mentions it; reconcile the current reference,
  simulator, target firmware, and physical result.
- Do not map Gen 2 or user-called Gen 3 to a sensor/input capability without an
  exact model, runtime capability response, and named hardware result.
- Do not collect or retain raw IMU, EMG, gesture, or precise-location traces by
  default. Sensor availability is not consent, and on-device capture is not
  proof of on-device processing.
- Browser simulation, MockDevice, a compile, or a connected link is not proof
  of physical input timing, comfort, gesture recognition, sensor quality, or
  release behavior.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [DAT Display](../meta-dat-display/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Transport and reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)

#### Sources

- [Full Meta Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT iOS Display access skill](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/display-access/SKILL.md)
- [DAT Android Display access skill](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/display-access/SKILL.md)
- [Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp)
- [Web Apps agent guidance](https://github.com/facebook/meta-wearables-webapp/blob/main/AGENTS.md)
- [Web Apps Display guidelines](https://github.com/facebook/meta-wearables-webapp/blob/main/plugins/meta-wearables-webapp/references/display-guidelines.md)
- [Meta Wearables Web Apps documentation](https://wearables.developer.meta.com/docs/develop/webapps)

---

### Meta Wearables on-device compliance

**Name.** `meta-wearables-on-device-compliance`

**When to use.** Enforce on-device and product-compliance boundaries for Meta Wearables iOS DAT, Android DAT, native Display, and Ray-Ban Display Web Apps. Use when designing or reviewing camera, glasses-microphone, audio, sensor, Display, local-first, privacy, permission, thermal, network, background, fallback, or release behavior.

Own the claim that a Meta Wearables feature is safe, bounded, and honest about
where data and control actually live. This role covers the phone companion,
glasses transport, native DAT, native Display, and hosted Display Web App; it
does not turn a phone fallback, mock, or browser simulator into glasses-native
behavior.

#### Read before acting

- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md), the [full-SDK matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md), the [generation matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md), and the [privacy/release route](../../knowledge-base/70-meta-wearables/08-privacy-publishing-and-release.md).
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when companion, firmware, on-glasses DAT-app, release-channel, thermal/power, or recovery state affects the data-flow claim.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) to distinguish Bluetooth/Wi-Fi/HFP/A2DP transport from processing location, queue/storage, and physical evidence.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) for sensor source, permission, raw-data, processing-location, sampling, thermal, teardown, and phone-fallback rules.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) to keep app identity, callbacks, attestation, credentials, Web App origin, and “on-device” processing as separate claims.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) to locate processing, storage, network, raw-media, and fallback boundaries in the actual adapters.
- Inspect the actual iOS target, Android module, or Web App before making a claim: package/artifact revision, deployment target, permissions, privacy manifest/metadata, network destinations, storage, logs, model/runtime path, and fallback.
- Use the [device and release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md) for source, static, mock, connected, physical, signed, and release evidence.
- Treat the current iOS 0.9.0 and Android 0.9.0 package/changelog as build gates for those releases; do not copy older DAM, lifecycle, minimum-OS, or audio examples into a target without typechecking.

#### Compliance workflow

1. Freeze the target profile: consumer label, runtime `DeviceType`/model, platform, exact package/artifact, phone OS, Meta AI version, firmware, requested capability, and evidence level.
2. Draw the data flow from glasses or phone source through local processing, network/vendor services, storage, logs, deletion, and user-visible output. Name every raw frame, photo, audio sample, transcript, sensor reading, Display payload, identifier, diagnostic, and model output.
3. Classify the processing location as `glasses-native`, `phone-local`, `remote`, `mixed`, or `unknown`. Use `on-device confirmed` only when the exact implementation and evidence establish the path; use `local-first` when a documented explicit remote fallback remains.
4. Gate every capture or transmission on capability, permission, consent, purpose, thermal/battery/link state, and lifecycle. Stop or degrade on doff/fold, disconnect, denial, background, thermal, battery, or stale-session events.
5. Apply the platform contract: iOS DAT permissions/configuration and 0.9.0 deployment, Android DAT manifest/artifact/configuration, or Web App HTTPS/600×600/MRBD/input rules. Keep native DAT and Web Apps separate.
6. Define a user-visible fallback for every unavailable operation: phone-local camera/audio, cached data, typed unavailable state, or explicit retry. Never silently substitute a phone microphone for glasses HFP.
7. Run static and deterministic checks, then the named connected/physical task when the account, target, firmware, and hardware exist. Keep source, mock, browser, connected, physical, signed, and production claims separate.
8. Return a compliance report with open gates. Stop before release if the data path, permission, retention, policy, target, or evidence contract is unresolved.

#### Fast path

Create a processing ledger for one data item: source, location, retention, network/storage path, consent, thermal impact, and fallback. Classify the claim and test its earliest boundary; do not audit unrelated capabilities before that path is honest.

#### Required output

Return:

- target and route profile;
- data-flow table with processing location, network, storage, deletion, and consent;
- permission/configuration and capability-gate table;
- thermal, lifecycle, background, link, and fallback behavior;
- local model/audio/video memory and retention policy;
- source/static/mock/connected/physical evidence status;
- unresolved privacy, account, release, device, generation, and policy gates.

#### Claim vocabulary

| Label | Use only when |
| --- | --- |
| `on-device confirmed` | The implementation path and target evidence show that the named processing stays on the named device boundary. |
| `phone-local` | The phone performs the processing; do not describe it as glasses-native or glasses-only. |
| `local-first` | The default is local but an explicit, disclosed remote path exists. |
| `remote-required` | The capability cannot complete without a named network/vendor service. |
| `source-conflict` | Official sources disagree; preserve both observations and gate the feature. |
| `to-verify` | The selected artifact, target, runtime, or physical result is missing. |
| `unsupported` | The selected route or source explicitly does not provide the capability. |

#### Hard boundaries

- Never call a phone camera, phone microphone, remote transcription, or Web App browser API a glasses-native capability.
- Never capture raw camera/audio/sensor data silently or retain it by default.
- Never promise background continuation, offline behavior, HFP audio, sensors, Display legibility, or thermal safety from source prose alone.
- Never map “Gen 3,” Meta Glasses, or a retail label to a DAT enum without current official runtime and named-device evidence.
- Never put credentials, personal media, device identifiers, or private diagnostic output into public Web App assets, logs, screenshots, fixtures, or skill archives.
- Never use `on-device` as a marketing synonym for “uses a phone companion.” State the processing boundary plainly.
- Never use Developer Mode, a valid callback, app attestation, or a release
  channel as evidence that data stays on glasses or the phone. Confirm the
  actual processing, network, storage, consent, and retention path separately.

#### Related roles

- [Full-SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Camera/audio](../meta-dat-camera-audio/SKILL.md)
- [Transport/reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Native Display](../meta-dat-display/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Privacy/publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Security and attestation](../meta-wearables-security-attestation/SKILL.md)

#### Sources

- [On-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md)
- [Meta Wearables full reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [DAT iOS repository and changelog](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository and changelog](https://github.com/facebook/meta-wearables-dat-android)
- [Meta Wearables Web Apps toolkit](https://github.com/facebook/meta-wearables-webapp)
- [Apple privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)
- [Apple ExternalAccessory](https://developer.apple.com/documentation/externalaccessory)

---

### Meta Wearables operational readiness

**Name.** `meta-wearables-operational-readiness`

**When to use.** Diagnose and harden Meta Wearables DAT iOS, DAT Android, native Display, and Ray-Ban Display Web App integrations across firmware, Meta AI companion, Developer Mode, release channels, DAT app provisioning, compatibility, thermal and link failures, and recovery evidence. Use when an integration will not register, cannot start a session, reports an update or device-unavailable error, moves from Developer Mode to a signed release channel, or needs a repeatable operational-readiness packet.

Act as the reliability and release-operations specialist for Meta Wearables.
Make the entire tuple—mobile app, DAT artifact, Meta AI companion, glasses
firmware, on-glasses DAT app, account/project, transport, and release channel—
explicit before diagnosing a failure or calling a route ready.

#### Read before acting

- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md), [device/release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md), [full-SDK matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md), [generation matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md), and [on-device contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md).
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) for exact firmware/companion/artifact tuples, access-gated dependency values, community-signal classification, and `COMP-*` evidence.
- Read the [portable recovery contract](references/recovery-contract.md) before collecting a run packet or writing a recovery recommendation.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) when the failure involves Bluetooth, Wi-Fi/local network, HFP/A2DP, queue pressure, route changes, thermal/power, or sustained streaming.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when readiness or recovery affects Display input, D-pad/EMG/temple gestures, browser sensors, permissions, or sensor watches.
- Read [debugging, observability, and diagnostic evidence](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when a DAT readiness, registration, permission, device-path, session, stream, or recovery failure needs app-visible diagnosis.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when diagnosing ownership, stale events, fallback, or lifecycle recovery across multiple platform adapters.
- Read [Developer Center project and release operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md) when failure behavior involves project build status, tester/channel access, app identity, telemetry, or account recovery.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when the failure involves callback configuration, identity keys, Developer Mode, release attestation, package/signing credentials, or App Store/privacy-manifest gates.
- Refresh the pinned [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), [full DAT reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat), [version dependencies](https://wearables.developer.meta.com/docs/version-dependencies), [known issues](https://wearables.developer.meta.com/docs/knownissues), and [release-channel guidance](https://wearables.developer.meta.com/docs/develop/dat/set-up-release-channels/) before a version-sensitive run. Authenticated or access-gated tables remain `to-verify` until the account view is observed.
- Inspect the actual target’s package/artifact graph, deployment/min SDK, configuration, permissions, privacy manifest/manifest, signing identity, and URL callback before changing code.

#### Operational workflow

1. **Freeze the tuple.** Record platform, app target/build, SDK package or Maven artifact, repository/tag, phone OS, Meta AI version, exact product label, runtime model/`DeviceType`, firmware, on-glasses DAT app state/version if visible, account/project, mode, and requested operation. Redact tokens, bundle identifiers when sensitive, serials, and raw diagnostics.
2. **Classify the mode.** Keep Developer Mode, a signed release-channel build, a simulator/browser run, and production separate. Developer Mode can make registration available without proving release-channel authorization, app attestation, provisioning, or store readiness.
3. **Preflight compatibility.** Compare every tuple member against the current official version-dependency surface. Label each value `source`, `static`, `observed`, `access-gated`, `conflict`, or `unknown`; never resolve a conflict by guessing.
4. **Trace provisioning.** Check Meta AI installation, account/project membership, Developer Mode, registration state, permissions, application ID/signature/client configuration, link state, compatibility, and whether the on-glasses DAT app needs an update. Use the documented firmware/DAT-app navigation APIs only when the selected artifact exposes them.
5. **Run one controlled operation.** Capture the ordered events for registration, session start, camera/Display/audio action, pause/disconnect, recovery, and stop. Do not loop retries through thermal, battery, power, doff, or unknown protocol failures.
6. **Apply typed recovery.** Distinguish configuration, account/channel, companion, firmware, on-glasses DAT app, transport, permission, lifecycle, thermal/power, and SDK/API failures. Apply the least invasive documented recovery, then re-run the same operation with a new run ID.
7. **Audit release readiness.** Re-test with the signed artifact, release channel, tester account, exact firmware, and physical pair. A clean Developer Mode run is not release evidence.
8. **Return the packet.** Produce the compatibility tuple, event trace, diagnosis, recovery attempted, evidence level, remaining gates, and next source-refresh trigger.

#### Fast path

Use this first-failure order: identity/access -> companion/firmware tuple -> mode/channel -> session/capability -> transport/thermal -> recovery. Capture the current state before repair and verify the post-state once; avoid repeated resets that erase the diagnostic boundary.

#### Required output

- target and version tuple with source/observed/access-gated labels;
- Developer Mode versus release-channel decision;
- preflight matrix for companion, firmware, DAT app, account/project, transport, permissions, and SDK artifact;
- ordered event trace and exact first failing state/error;
- bounded recovery action and whether it was actually observed to work;
- thermal, battery, doff/fold, disconnect, app-background, and stop behavior;
- redacted evidence ledger using `OPS-SOURCE-01` through `OPS-CHANNEL-01` where applicable;
- explicit `ready`, `ready-for-device`, `blocked`, `source-conflict`, or `to-verify` result.

#### Hard boundaries

- Never treat a GitHub issue, discussion, or user report as an official compatibility guarantee; it is a troubleshooting signal that must be labeled separately.
- Never print or commit Meta application IDs, client tokens, app signatures, account emails, device serials, raw media, or diagnostic bundles.
- Never claim firmware or Meta AI compatibility from a neighboring version, consumer generation name, or a stale example.
- Never equate Developer Mode with release-channel authorization, app attestation, signed distribution, Store approval, or production readiness.
- Never expose callback payloads, client/package credentials, app signatures, or
  private project identifiers while diagnosing identity or channel failures.
  Attestation and channel state do not prove local processing or physical
  capability.
- Never auto-retry through thermal emergency, battery critical, peak-power shutdown, doff/hinge closure, or an unknown protocol error; stop, preserve the event trace, and require a deliberate recovery.
- Never map `Gen 3` to a DAT `DeviceType`, Meta Glasses, Display, or another model without current official mapping plus named runtime evidence.
- Never call an on-glasses DAT-app update, firmware update, or re-pair successful unless the target reports the post-action state and the original operation completes.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Full-SDK audit](../meta-wearables-full-sdk-audit/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [Source refresh](../meta-wearables-source-refresh/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Developer Center operations](../meta-wearables-developer-operations/SKILL.md)
- [Transport and runtime reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Security and attestation](../meta-wearables-security-attestation/SKILL.md)

#### Sources

- [Operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md)
- [Device and release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md)
- [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat)
- [Wearables version dependencies](https://wearables.developer.meta.com/docs/version-dependencies)
- [Wearables known issues](https://wearables.developer.meta.com/docs/knownissues)
- [Wearables release-channel guidance](https://wearables.developer.meta.com/docs/develop/dat/set-up-release-channels/)
- [Security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md)

---

### Meta Wearables privacy and publishing

**Name.** `meta-wearables-privacy-publishing`

**When to use.** Audit privacy, consent, iOS permissions, data flows, Meta Wearables terms, acceptable use, App Store metadata, and release gates for DAT and Web App products. Use whenever a wearable feature captures camera/audio, uses personal context, connects a cloud service, or is prepared for testing or publishing.

Make the privacy and publishing contract explicit before implementation or release. Wearable camera, microphone, Display, and personal-context features can create a larger expectation gap than ordinary phone UI.

#### Read before acting

- Inspect the actual target, Info.plist usage descriptions, entitlements, privacy manifest, package permissions, network destinations, analytics/crash settings, storage, logs, and App Store metadata.
- Read [privacy, publishing, and release](../../knowledge-base/70-meta-wearables/08-privacy-publishing-and-release.md) and [registration/permissions](../../knowledge-base/70-meta-wearables/02-registration-permissions-and-configuration.md).
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) when the claim includes local-first processing, glasses-native audio/camera, thermal behavior, storage, network, or fallback.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the feature requests motion, orientation, geolocation, EMG/gesture data, raw sensor retention, or a phone-sensor fallback.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when release-channel, tester, companion, firmware, on-glasses DAT-app, update-required, or recovery behavior affects the privacy or publishing claim.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when shared state, raw-media ownership, network boundaries, or phone fallback spans targets.
- Read [Developer Center project and release operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md) for project permission justifications, product listings, telemetry opt-out, versions, testers, and channel evidence.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when the feature depends on bundle/package identity, Meta AI callbacks, Developer Mode, release attestation, package/signing secrets, Web App origin, or the dated DAT App Store warning.
- Use the [device and release evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md) when preparing tester setup, signed-build identity, release-channel evidence, or redacted artifacts.
- Refresh the official [Meta Wearables terms](https://wearables.developer.meta.com/docs/terms), [acceptable use policy](https://wearables.developer.meta.com/docs/acceptable-use-policy), [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), and [Web App documentation](https://wearables.developer.meta.com/docs/develop/webapps).
- Read Apple’s [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/), [privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files), [app privacy details](https://developer.apple.com/app-store/app-privacy-details/), and relevant camera/microphone permission guidance.

#### Audit workflow

1. Draw the data-flow: glasses/phone source → local adapter → processing → network/vendor → storage/logs → deletion.
2. Name each data class: registration/device metadata, camera frames, audio, transcript, Display content, diagnostics, account identifiers, and analytics/crash data.
3. For each class, record purpose, collection trigger, user notice/consent, on-device versus remote processing, retention, deletion, access, and failure behavior.
4. Verify every required iOS permission and usage description against actual code paths. Do not request camera/microphone access at launch without a clear user action and explanation.
5. Verify DAT settings for analytics/crash behavior and device-access management against the selected release. Document opt-out or always-enabled behavior as the source states it.
6. Review Web App exposure: public HTTPS URL, client-visible data, origin/security controls, authentication, logging, and what happens when the companion app is unavailable.
7. Draft review notes that accurately describe the feature, hardware requirements, account/test path, permissions, and fallback. Do not promise a reviewer can exercise an unsupported preview path.
8. Stop before publishing if a source, permission, consent, data-retention, terms, or physical-device gate is unresolved.

#### Fast path

Trace one data path from permission and consent through collection, processing, storage/transmission, deletion, and disclosure metadata. Fix the earliest missing disclosure or release gate before auditing unrelated surfaces.

#### Required output

- data-flow and collection matrix;
- permission/consent copy and trigger location;
- privacy manifest/App Store privacy metadata changes;
- Meta terms/acceptable-use review;
- logging and retention policy;
- reviewer/tester setup and hardware requirements;
- Developer Mode versus release-channel compatibility tuple and observed recovery status;
- open legal, product, or physical-device gates.

#### Hard boundaries

- Never claim “on-device” processing unless the exact implementation and source establish that the data stays on device.
- Never call a phone microphone or remote transcription a glasses-native microphone feature.
- Never collect raw audio/video silently or retain it by default.
- Never put personal media, credentials, or device identifiers in public Web App assets, logs, screenshots, fixtures, or skill archives.
- Never imply Meta endorsement, App Store approval, or broad generation support from SDK access.
- Never treat a valid callback, Developer Mode registration, attestation result, or
  release-channel membership as proof of local processing, physical capability,
  App Store/Play approval, or production; keep credentials and callback payloads
  redacted.
- Terms and acceptable-use review is not legal advice; escalate material uncertainty to the product owner/counsel.

#### Related routes

- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [Camera and audio](../meta-dat-camera-audio/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Developer Center operations](../meta-wearables-developer-operations/SKILL.md)
- [Security and attestation](../meta-wearables-security-attestation/SKILL.md)
- [iOS privacy/performance/release proof](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-privacy-performance-release-proof/SKILL.md)
- [iOS privacy, performance, and release proof](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-privacy-performance-release-proof/SKILL.md)

#### Sources

- [Meta Wearables terms](https://wearables.developer.meta.com/docs/terms)
- [Meta Wearables acceptable use policy](https://wearables.developer.meta.com/docs/acceptable-use-policy)
- [Meta DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Meta Wearables Web Apps](https://wearables.developer.meta.com/docs/develop/webapps)
- [Apple App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)
- [Apple privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)
- [Apple app privacy details](https://developer.apple.com/app-store/app-privacy-details/)
- [Apple ExternalAccessory](https://developer.apple.com/documentation/externalaccessory)

---

### Meta Wearables route planner

**Name.** `meta-wearables-route-planner`

**When to use.** Choose the correct Meta Wearables route across native iOS DAT, native Display, Ray-Ban Display Web Apps, phone fallback, and unsupported or unverified device requests. Use before implementation whenever a product mentions Ray-Ban Meta, Oakley Meta, Meta Glasses, Display, camera, audio, input, sensors, Gen 2, Gen 3, or the Meta Wearables SDK.

Turn a wearable idea into a verified platform route before package imports, UI work, permissions, or marketing copy harden around an assumption.

#### Read before acting

- Inspect the actual project, target platforms, deployment target, existing device integrations, and whether the requested surface is a companion app or a glasses-delivered experience.
- Read [platform and route selection](../../knowledge-base/70-meta-wearables/00-platform-and-route-selection.md), [device models and capability matrix](../../knowledge-base/70-meta-wearables/05-device-models-and-capability-matrix.md), and [the Meta source registry](../../knowledge-base/sources/meta-wearables-source-registry.md).
- Read the [device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md) when product wording includes Gen 2, Gen 3, Meta Glasses, Display, or an SDK alias.
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) when wording includes firmware, companion/DAT-app version, support matrix, or compatibility failure.
- Read the [full SDK capability and source-conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md) when the request says “full,” “regular SDK,” “all capabilities,” or parity.
- Load the portable [surface manifest](../meta-wearables-full-sdk-audit/references/surface-manifest.yaml) and resolve its `terminology_contract` before selecting a route; preserve ambiguous or unresolved status instead of translating user labels into SDK symbols.
- Read the [DAT Android API surface atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md) when Android/Kotlin/Java symbols, Maven coordinates, or 0.9 migration behavior is in scope.
- Read [Developer Center project and release operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md) when account/team/project identity, tester access, versions, release channels, or telemetry is part of the route.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) when the capability or failure involves Bluetooth, Wi-Fi/local network, HFP/A2DP, latency, backpressure, thermal/power, or link recovery.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) when the request says “on-device,” “local-first,” private, offline, low-latency, or asks where processing occurs.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) when the request includes firmware, companion versions, Developer Mode, release channels, update-required/device-unavailable errors, or recovery.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when more than one surface, platform, capability, or phone fallback will share product behavior.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when the outcome depends on Display actions, D-pad/captouch, Neural Band/EMG, temple gestures, motion/orientation, geolocation, browser sensors, or an “on-device” sensor claim.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when the outcome depends on bundle/package identity, Meta AI callbacks, Developer Mode, release channels, app attestation, credentials, Web App origin, privacy-manifest/App Store gates, or an “on-device” trust claim.
- Refresh the official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/), and [Web Apps documentation](https://wearables.developer.meta.com/docs/develop/webapps).
- If the request says “regular SDK,” resolve the phrase against the current official docs and repository. Do not invent a second native iOS SDK or silently substitute Web Apps for DAT.

#### Fast path

Resolve one request through the route map: exact surface, capability, runtime, fallback, and evidence. If labels such as “regular SDK” or “Gen 3” remain ambiguous, return a blocked or `to-verify` route instead of branching into every lane.

#### Route map

| User outcome | Candidate route | Choose it when | First gate |
| --- | --- | --- | --- |
| iOS app discovers/uses supported glasses capabilities | native DAT iOS | the capability is exposed by the current DAT package and device | package/API and registration availability |
| Android app discovers/uses supported glasses capabilities | native DAT Android | the capability is exposed by the selected Maven artifact and device | Gradle/API and registration availability |
| glanceable native surface from the companion app | DAT Display | runtime device reports Display support | `DeviceType.supportsDisplay()` and physical model |
| HTML/CSS/JS experience delivered to Ray-Ban Display | Meta Wearables Web App | the target is explicitly the Web App surface | public HTTPS URL, 600×600, input/focus contract |
| wearer input, motion, orientation, or location | native Display, Web App, or phone fallback | the exact source is identified and the selected runtime exposes it | source/capability/permission or secure-context gate plus physical task |
| setup, consent, unavailable state, or unsupported device | phone-first fallback | wearable is absent, denied, disconnected, or not proven | useful phone workflow without wearable |
| “Gen 3 support” or unnamed future model | to-verify / blocked | official source and runtime mapping are missing | exact device type, firmware, capability, hardware |

The native DAT route and Web App route can coexist in one product, but they have different packages, lifecycle, deployment, and proof requirements.

#### Planning workflow

1. Rewrite the request as an outcome, not a device label: capture, show, notify, navigate, control, or configure.
2. Identify the delivery surface: iOS companion, native glasses Display, Ray-Ban Display Web App, or phone fallback.
3. Record the exact device wording and map it to the runtime `DeviceType` only when an official source or observed device proves the mapping.
4. Identify the capability: registration, camera, audio, Display, button/input, Wi-Fi, or another named API. Mark undocumented behavior `to-verify`.
5. Define the permission, consent, privacy, session, disconnect, cancellation, and unavailable states.
6. Freeze the operational tuple when the route depends on companion, firmware,
   on-glasses DAT-app provisioning, release-channel, thermal, or transport state.
7. Select the shared domain/coordinator and platform adapter boundaries before
   assigning implementation files; keep Web Apps and native DAT separate.
8. Select the smallest specialist team and write a handoff with sources, version, and evidence requirements.
9. Reject routes that depend on a public API, model, entitlement, background behavior, or voice feature that the current sources do not establish.

#### Compatibility language

Use these labels in plans and code reviews:

- **Documented** — present in the cited official API or product documentation.
- **Build-proven** — the exact target compiles or tests against the cited package/version.
- **Mock-proven** — the SDK mock or simulator exercises the path.
- **Connected-device proven** — a paired device completed the path, with model/firmware recorded.
- **Physical Display proven** — the actual glasses rendered and accepted the interaction.
- **To verify** — an open question; never write it as a supported feature.

#### Required output

Return:

- route and rejected alternatives;
- exact SDK/repository/tag or commit;
- device/model/capability table;
- phone and wearable state machine;
- permissions/privacy and data-flow questions;
- firmware/companion/provisioning/release-channel and recovery questions;
- implementation owners and evidence ladder;
- explicit `Gen 3` and “regular SDK” resolution status.
- the manifest terminology status and the exact canonical route selected from it.

#### Hard boundaries

- product-generation names map one-to-one to SDK enums;
- every Ray-Ban Meta or Oakley Meta model has a Display, camera, audio, or Wi-Fi capability;
- Web Apps can use native DAT APIs directly;
- a simulator, browser, or phone camera proves glasses behavior;
- Meta AI voice commands are part of the current public DAT preview;
- an undocumented “regular SDK” is interchangeable with DAT.

#### Related routes

- [Meta Wearables agentic team](../meta-wearables-agentic-team/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT camera and audio](../meta-dat-camera-audio/SKILL.md)
- [Transport and runtime reliability](../meta-wearables-transport-reliability/SKILL.md)
- [DAT Display](../meta-dat-display/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Web Apps](../meta-wearables-web-apps/SKILL.md)
- [device proof](../meta-wearables-device-proof/SKILL.md)
- [on-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [application architecture](../meta-wearables-app-architecture/SKILL.md)
- [security and attestation](../meta-wearables-security-attestation/SKILL.md)

#### Sources

- [Meta Wearables DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Meta Wearables DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android API surface atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md)
- [Developer Center operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md)
- [Security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md)
- [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [Introducing Meta Wearables Device Access Toolkit](https://developers.meta.com/blog/introducing-meta-wearables-device-access-toolkit/)
- [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/)
- [Meta Wearables Web Apps](https://wearables.developer.meta.com/docs/develop/webapps)
- [Ray-Ban Meta Gen 2 announcement](https://about.fb.com/news/2025/09/ray-ban-meta-gen-2-better-battery-life-video-capture/)
- [Meta Glasses announcement](https://about.fb.com/news/2026/06/meta-essilorluxottica-partner-launch-meta-glasses/)

---

### Meta Wearables security and attestation

**Name.** `meta-wearables-security-attestation`

**When to use.** Audit Meta Wearables DAT iOS and Android identity, attestation, callback, release-channel, package-token, privacy-manifest, and secret-handling boundaries. Use when configuring Developer Mode or release builds, reviewing MetaAppID/ClientToken/application IDs, diagnosing registration failures, protecting Android package credentials, validating callback inputs, or making on-device and publishing claims for native DAT or Ray-Ban Display Web Apps.

Own the security boundary between an iOS/Android app, Meta AI callbacks, the
Wearables Developer Center, DAT attestation, release channels, package
credentials, and the actual processing/data path. Return a redacted,
source-grounded configuration and evidence packet; this role is not a security
certification and does not grant access to a project or account.

#### Read before acting

- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md), its [contract reference](references/security-attestation-contract.md), and the [evidence packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md).
- Read [registration and configuration](../../knowledge-base/70-meta-wearables/02-registration-permissions-and-configuration.md), [Developer Center operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md), [privacy/publishing](../../knowledge-base/70-meta-wearables/08-privacy-publishing-and-release.md), and [on-device compliance](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md).
- Read the selected [DAT iOS integration](../meta-dat-ios-integration/SKILL.md), [DAT Android integration](../meta-dat-android-integration/SKILL.md), [application architecture](../meta-wearables-app-architecture/SKILL.md), and [operational readiness](../meta-wearables-operational-readiness/SKILL.md) roles when the identity or callback is part of a build or recovery.
- Refresh the official [full reference](https://wearables.developer.meta.com/llms.txt?full=true), [DAT iOS getting started](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/getting-started/SKILL.md), [DAT iOS permissions/registration](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/permissions-registration/SKILL.md), [DAT Android getting started](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/getting-started/SKILL.md), [DAT Android permissions/registration](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/permissions-registration/SKILL.md), [manage projects](https://wearables.developer.meta.com/docs/develop/dat/manage-projects/), and [release channels](https://wearables.developer.meta.com/docs/set-up-release-channels/).
- Recheck Apple's [ExternalAccessory](https://developer.apple.com/documentation/externalaccessory), [privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files), [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/), and the target's actual `Info.plist`, entitlements, privacy manifest, signing, and build settings.

#### Fast path

Normalize the identity tuple before making security claims. Classify each value as secret or non-secret and each callback or attestation as source, configuration, runtime, or release evidence; audit one trust transition end-to-end and stop at its first missing authority.

#### Workflow

1. **Freeze the identity tuple.** Record platform, bundle ID or Android
   application ID/package, URL callback scheme, selected DAT package/artifact,
   project/platform app, build variant, phone OS, Meta AI version, firmware,
   mode, channel, and target device. Keep unknowns `to-verify`.
2. **Classify the surface.** Native DAT uses mobile app identity and the Meta AI
   registration callback. Native Display is a DAT capability, not a separate
   credential system. A Web App uses a hosted HTTPS origin and its own
   distribution/host controls; do not invent a native attestation tuple for it.
3. **Inspect configuration without exposing values.** Verify key names,
   placement, bundle/package alignment, URL handling, permissions, privacy
   metadata, package repository access, and release/debug variant separation.
   Report presence, source, and redacted fingerprints—not credential values.
4. **Separate Developer Mode from release attestation.** Developer Mode is a
   local testing path whose registration/attestation behavior differs from a
   release-channel build. A Developer Mode success is not release identity,
   tester access, signing, physical capability, or production proof.
5. **Audit callback and package boundaries.** Let the selected SDK handle the
   documented callback, accept only the app-owned scheme/host expected by the
   target, do not log raw callback URLs or query values, and reject malformed,
   replayed, unexpected, or cross-environment input before changing state.
   Keep GitHub package tokens in the authorized environment only.
6. **Audit the data path separately.** Attestation authenticates an app/project
   relationship as described by the source; it does not establish that media,
   sensors, transcripts, or Display content stay on-device. Route processing,
   network, storage, consent, retention, and deletion to the on-device/privacy
   roles.
7. **Collect bounded evidence.** Use `SEC-*` rows for source, static config,
   redaction, callbacks, mode, attestation, channel, physical operation, and
   release. Keep account, signed, connected, physical, and App Store/Play
   outcomes separate.

#### Identity map

| Surface | Identity/configuration to inspect | Security/evidence boundary |
| --- | --- | --- |
| DAT iOS | Registered bundle ID; `AppLinkURLScheme`; `MetaAppID`; `ClientToken`; `TeamID`; Meta AI query-scheme allowlist; selected SPM package and build variant | The public reference describes the keys for iOS attestation and callback setup. Upstream development guidance uses a Developer Mode placeholder for `MetaAppID`; resolve the exact selected package/build rather than copying a production value into examples. |
| DAT Android | Android package/application ID; `mwdat_application_id`; `mwdat_client_token`; callback intent-filter scheme; GitHub Packages repository access; signing/build variant | The public reference describes `APPLICATION_ID` and `CLIENT_TOKEN` for attestation and says Developer Mode can use `0` placeholders. A package token for Maven access is a separate secret and must never enter source, logs, fixtures, or archives. |
| Native Display | The selected DAT identity plus the runtime Display capability and session | Display rendering/input does not create a new trust boundary or prove the app is attested, the device is supported, or the content is private. |
| Ray-Ban Display Web App | Exact HTTPS origin/URL, deployed revision, host access controls, client-visible assets, distribution/add-to-device state | The reviewed public Web App route is hosted web content, not a native DAT package. Public URL, browser simulator, Meta AI connection, and physical Display launch are separate evidence. |

#### Developer Mode, attestation, and release

Use this table as a claim firewall:

| State | Can establish | Cannot establish |
| --- | --- | --- |
| Source/configuration | The documented keys, flow, and intended variant are understood | Current account access, correct secret values, compile success, attestation success, or device behavior |
| Developer Mode | A local development registration path when the companion/device prerequisites are observed | Release-channel identity, attestation, signed distribution, public availability, or production |
| Attested release-channel build | The authorized project identity, selected version/channel, and app attestation path for the exact build when observed | Camera/audio/Display/sensor behavior, on-device processing, App Store/Play approval, or broad generation support |
| Physical run | The named operation on the exact model/firmware/companion/build tuple | Another model, “Gen 3,” another channel, or a different processing/network path |
| Web App deployment | The exact hosted URL and deployed web revision | Native DAT attestation, private origin, offline/sensor support, or physical MRBD behavior without its own run |

The current raw Meta reference says DAT App Store submission is not currently
supported at this snapshot and attributes the issue to the present
`ExternalAccessory`/MFi/privacy-manifest path. Treat that as a dated Meta
release gate to recheck with the exact package and Apple review requirements,
not as a permanent policy or as a Play-store conclusion.

#### Callback and credential controls

- Keep the callback scheme unique to the target app/environment. Parse the URL
  using the platform API and pass it to the documented DAT handler; do not build
  app state from arbitrary query, fragment, or host values.
- Reject a callback when its scheme/host/environment does not match, it cannot
  be parsed, it arrives after the registration/session epoch has ended, or it
  would cross a debug/release boundary. Record only a stable local outcome such
  as `callback_rejected`.
- Store `ClientToken`, `GITHUB_TOKEN`, package credentials, signing material,
  private project identifiers, and invitation data in the authorized build/CI
  mechanism. Use placeholder names in examples and redacted presence checks in
  evidence.
- Do not print credentials with build tools, shell tracing, Gradle diagnostics,
  Xcode logs, crash reports, screenshots, model context, or portable archives.
  Check generated artifacts and archive paths before sharing them.
- Treat application IDs, bundle/package identifiers, callback URLs, device
  identifiers, tester/account identifiers, and signatures as sensitive project
  metadata even when an individual value may be public. Minimize, redact, and
  avoid joining values unnecessarily in logs.
- An “on-device” label requires a separate processing-location declaration. A
  trusted callback or attested app can still send camera/audio/sensor data to a
  phone or network service.

#### Required handoff

Return:

- exact platform, target, package/artifact revision, project/platform-app
  identity status, build variant, callback route, and mode/channel;
- a redacted key-presence/configuration matrix with source and target/static
  status;
- callback validation, environment separation, token/secret storage, logging,
  artifact, and retention controls;
- attestation, Developer Mode, tester/channel, signing, physical, and
  App Store/Play evidence rows with `not-run`, `access-gated`, or `to-verify`
  where appropriate;
- the processing-location/network/storage boundary and handoff to privacy and
  on-device compliance;
- first failure and one bounded recovery when diagnosing registration or
  release behavior;
- explicit non-claims, especially no invented Gen 3 mapping and no claim that
  attestation proves physical capability or local processing.

#### Hard boundaries

- Never print, commit, archive, or paste a real token, client credential,
  signing value, invitation address, private project identifier, callback
  payload, device serial, or raw diagnostic bundle.
- Never equate `MetaAppID`, `ClientToken`, Android application ID, a bundle ID,
  a package token, or an app signature with one another; record their roles and
  target platform separately.
- Never call Developer Mode an attested release, or a release channel an App
  Store/Play approval or production result.
- Never infer that a valid callback proves registration, permissions, session,
  camera, audio, Display, sensor, or on-device processing success.
- Never claim a public Web App has native DAT attestation or secret storage in
  the browser. Keep private credentials server-side and review the public
  origin/data path.
- Never infer “Gen 3” from a consumer label, product announcement, enum name,
  or neighboring model. Require runtime identity, firmware, capability, and a
  named physical result.
- Stop before mutating a Developer Center project, channel, tester list, or
  release state unless the user explicitly authorizes the exact live operation
  and target.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Developer Center operations](../meta-wearables-developer-operations/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)

#### Sources

- [Full Meta Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [DAT iOS getting started](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/getting-started/SKILL.md)
- [DAT iOS permissions and registration](https://github.com/facebook/meta-wearables-dat-ios/blob/main/plugins/mwdat-ios/skills/permissions-registration/SKILL.md)
- [DAT Android getting started](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/getting-started/SKILL.md)
- [DAT Android permissions and registration](https://github.com/facebook/meta-wearables-dat-android/blob/main/plugins/mwdat-android/skills/permissions-registration/SKILL.md)
- [Wearables Developer Center manage projects](https://wearables.developer.meta.com/docs/develop/dat/manage-projects/)
- [Wearables release channels](https://wearables.developer.meta.com/docs/set-up-release-channels/)
- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [Apple ExternalAccessory](https://developer.apple.com/documentation/externalaccessory)
- [Apple privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files)
- [Apple App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/)

---

### Meta Wearables source refresh

**Name.** `meta-wearables-source-refresh`

**When to use.** Refresh the Meta Wearables DAT, Web Apps, device, terms, and Apple integration knowledge base with exact official URLs, release/tag/commit, retrieval date, API migrations, access gaps, and evidence boundaries, including a portable public Git ref drift check. Use before version-sensitive implementation, after an SDK release, or when a device-generation claim may have drifted.

Keep the knowledge base current without turning an inaccessible page, remembered API, or product announcement into an implementation fact.

#### Read before acting

- Read [Meta source freshness](../../knowledge-base/sources/meta-wearables-source-freshness-log.md), [Meta source registry](../../knowledge-base/sources/meta-wearables-source-registry.md), and the Apple team’s [source provenance rules](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-source-refresh-and-availability/references/provenance-and-evidence.md).
- Inspect the current local package manifests, lockfiles, sample code, generated API docs, and existing source notes.
- Run `python3 scripts/check_source_revisions.py` from this package before interpreting the pinned iOS/Android/Web App refs as current; it checks public `main` refs and an exact iOS reproducible tag without cloning or printing credentials.
- Run `python3 scripts/check_source_tree_inventory.py` after the ref check; it reads the pinned public root `AGENTS.md`/`README.md`/`install-skills.sh` surfaces, lane `.codex-plugin/plugin.json` manifests, Package.swift, exact role names, sample catalogs, artifact catalogs, SDK levels, Web App references, examples, and templates, then fails on inventory drift without cloning or handling credentials. It prefers the public Contents API and falls back to a read-only codeload archive on unauthenticated rate limits. A clean current receipt is `CHECKS 23 DRIFT 0`.
- Run `python3 ../meta-wearables-agentic-team/scripts/validate_team_manifest.py` after role-tree changes; it verifies the 23 local role packages, all 32 upstream role handoffs, and the Ray-Ban Display/Gen 2/Gen 3 claim gates.
- Read the [full SDK capability and source-conflict matrix](../../knowledge-base/70-meta-wearables/15-full-sdk-capability-and-source-conflict-matrix.md) before declaring module or capability coverage complete.
- Read the [DAT Android API surface atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md) when refreshing Android artifacts, symbols, or 0.9 migration notes.
- Read the [device-generation and runtime-support matrix](../../knowledge-base/70-meta-wearables/16-device-generation-and-runtime-support-matrix.md) before resolving product labels, “Gen 3,” or model-enum claims.
- Read the [version-dependency and device-compatibility route](../../knowledge-base/70-meta-wearables/26-version-dependency-and-device-compatibility-evidence.md) when refreshing firmware, companion/DAT-app, artifact, version-table, or compatibility claims.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) before updating processing-location, privacy, thermal, network, retention, or fallback claims.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) before updating firmware, companion, on-glasses DAT-app, version-dependency, Developer Mode, release-channel, known-issue, or recovery claims.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) before changing shared state, adapter, concurrency, lifecycle, or cross-platform parity guidance.
- Read [Developer Center project and release operations](../../knowledge-base/70-meta-wearables/21-developer-center-project-and-release-operations.md) when the raw reference changes organization, project, version, channel, tester, telemetry, or recovery guidance.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when a source changes identity keys, callback/attestation semantics, package credentials, App Store/privacy-manifest requirements, Web App origin rules, or “on-device” claim boundaries.
- Read [transport, audio, and runtime reliability](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md) when a changelog, setup guide, artifact, audio route, local-network requirement, transport parity signal, thermal behavior, or recovery claim changes.
- Read [debugging, observability, and diagnostic evidence](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when upstream debugging roles, live MCP tools, event schemas, known issues, or diagnostic-bundle behavior changes.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when a Display/input/sensor source, browser API, physical interaction, processing-location, or device-generation claim changes.
- Refresh the official [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios), [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android), [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), [full Wearables reference](https://wearables.developer.meta.com/llms.txt?full=true), [Web Apps repository](https://github.com/facebook/meta-wearables-webapp), [Developer Center](https://wearables.developer.meta.com/docs/develop/), [terms](https://wearables.developer.meta.com/docs/terms), and [acceptable use policy](https://wearables.developer.meta.com/docs/acceptable-use-policy).
- Record the access state of the Wearables docs, public MCP, and `llms.txt` endpoint separately. If a page requires login or cannot be fetched, record the gap; do not summarize its unseen contents.

#### Fast path

Run the exact revision check and source-tree inventory first. If both are clean, emit a no-change receipt; if drift exists, compute impacted rows, routes, and packages, update only those, then rerun validators and archive parity.

#### Refresh workflow

1. Run the bundled public-ref checker, source-tree inventory checker, and team-manifest validator, then record UTC/local date, URL, page title, repository commit/tag, release, retrieval method, and every expected/observed root AI file, plugin manifest, product, exact plugin-role name, sample, artifact, SDK, reference, example, template, and local-handoff result. A `DRIFT` result is a refresh blocker, not permission to silently rewrite the manifest.
2. Compare current sources with the prior snapshot for package products, minimum OS/toolchain, registration/permission rules, session lifecycle, camera/audio, Display/input, MockDevice, Web Apps, device models, terms, and release limitations.
3. For every changed symbol, write a migration note: old wording/API, new wording/API, affected role package, code-review action, and test fixture.
4. Reconcile iOS/Android/Web sources without treating platform parity as proof that symbols or behavior are identical; update the Android API atlas when artifact/API conflicts change.
5. Re-check product-generation mappings. Preserve `to-verify` when an official API/model enum or physical device is missing.
6. Update the registry and freshness log, then run local link/fence/whitespace/required-`## Sources` validation.
7. Report what was source-proven, what was locally build-proven, and what still needs an authenticated doc, account, or physical hardware.

#### Current snapshot fields

At minimum, track:

- DAT iOS tag/commit and release date;
- DAT Android tag/commit when parity is being discussed;
- iOS minimum and sample toolchain;
- camera/audio/session/display/mock changes;
- Web App repository/docs commit and constraints;
- device enum/model/capability changes;
- preview/access/terms/publishing changes;
- unresolved “Gen 3,” “regular SDK,” MCP, and physical-device questions.
- product announcements versus SDK identity, and authenticated Developer Center
  access state;
- full-reference module names versus importable package products, HFP/A2DP/IMU availability, and current App Store/release-channel policy.
- upstream `AGENTS.md`/plugin examples versus versioned changelog/package guidance,
  including iOS/Android minimums, DAM metadata, and `Session`/`DeviceSession`
  names;
- Web App capability conflicts between the full Developer Center index and the
  toolkit `main`; preserve the revisions and carry text/offline/back/gesture/
  sensor behavior as `source-conflict`/`to-verify` until target evidence closes
  the gap.
- version-dependency and operational signals, including Meta AI/firmware
  prerequisites, on-glasses DAT-app update routes, Developer Mode versus
  release-channel behavior, and first-party versus community troubleshooting
  reports; keep access-gated values and issue reports separately labeled.

#### Required output

- updated registry entries;
- freshness log row;
- changed-API/migration table;
- source-to-role impact map;
- operational compatibility/recovery impact map and first-party/community source classification;
- exact URLs and commits;
- access and evidence gaps;
- validation commands/results.
- public-ref checker output, including every expected and observed revision.
- source-tree inventory checker output, including every expected and observed product, exact role name/list, role count, sample, artifact, SDK, reference, example, and template count/value.
- team-manifest validator output, including local role count, upstream handoff count, and device-claim gate count.

#### Hard boundaries

- Do not cite search snippets as the API contract when an official page or repository is available.
- Do not silently overwrite a previous source snapshot; preserve historical release notes.
- Do not infer a model mapping from a press release, and do not infer hardware support from an enum alone.
- Do not claim “full SDK coverage” while authenticated or unpublished docs, private preview behavior, or physical hardware remain unexamined.
- Do not resolve an official-source conflict by silently preferring a moving
  toolkit branch over a versioned/public Developer Center contract; record both
  and route the feature to the evidence packet.
- Do not copy upstream skill files wholesale; synthesize a source-linked local route and retain the upstream URL.

#### Related routes

- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [Route planner](../meta-wearables-route-planner/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Transport and runtime reliability](../meta-wearables-transport-reliability/SKILL.md)
- [Debugging and observability](../meta-wearables-debugging-observability/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Apple source refresh](https://github.com/shotcowboystyle/ios-ops-plugin/blob/main/.agent/skills/ios-source-refresh-and-availability/SKILL.md)

#### Sources

- [Meta DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [Meta DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android API surface atlas](../../knowledge-base/70-meta-wearables/20-dat-android-api-surface-atlas.md)
- [Developer Center operations](../meta-wearables-developer-operations/SKILL.md)
- [Meta DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [Meta Wearables Web App repository](https://github.com/facebook/meta-wearables-webapp)
- [Wearables Developer Center](https://wearables.developer.meta.com/docs/develop/)
- [Meta Wearables terms](https://wearables.developer.meta.com/docs/terms)
- [Meta Wearables acceptable use policy](https://wearables.developer.meta.com/docs/acceptable-use-policy)
- [Full Wearables platform reference](https://wearables.developer.meta.com/llms.txt?full=true)
- [Wearables version dependencies](https://wearables.developer.meta.com/docs/version-dependencies)
- [Wearables known issues](https://wearables.developer.meta.com/docs/knownissues)
- [Wearables release-channel guidance](https://wearables.developer.meta.com/docs/develop/dat/set-up-release-channels/)
- [Ray-Ban Meta Gen 2 announcement](https://about.fb.com/news/2025/09/ray-ban-meta-gen-2-better-battery-life-video-capture/)
- [Ray-Ban Meta Gen 2 and Optics announcement](https://about.fb.com/news/2026/03/meta-ai-glasses-built-for-prescriptions/)
- [Meta Glasses announcement](https://about.fb.com/news/2026/06/meta-essilorluxottica-partner-launch-meta-glasses/)

---

### Meta Wearables transport and reliability

**Name.** `meta-wearables-transport-reliability`

**When to use.** Design and review Meta Wearables DAT transport and runtime reliability across Bluetooth, Wi-Fi/local network, HFP/A2DP audio, camera and Display backpressure, background, thermal/power, disconnect, and recovery paths on iOS and Android. Use for Wi-Fi streaming, local-network configuration, audio-route failures, link instability, latency, dropped frames, transport parity, or reliability claims.

Own the transport contract between the iOS/Android companion, Meta AI, and
the glasses. Keep Bluetooth control/registration, Wi-Fi media transport,
HFP/A2DP audio, local-network configuration, application processing, and
physical evidence as separate claims.

#### Read before acting

- Read the [transport, audio, and reliability route](../../knowledge-base/70-meta-wearables/22-transport-audio-and-runtime-reliability.md), [session/camera/audio route](../../knowledge-base/70-meta-wearables/03-device-session-camera-and-audio.md), [Android parity route](../../knowledge-base/70-meta-wearables/14-dat-android-parity-and-boundaries.md), and [operational-readiness route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md).
- Read the [on-device compliance contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md), [application architecture route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md), [device-proof packet](../../knowledge-base/70-meta-wearables/12-device-and-release-evidence-packet.md), and [transport contract reference](references/transport-contract.md).
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) when input/sensor event timing, stale callbacks, listener teardown, sampling, or physical gesture recovery depends on transport state.
- Read the [debugging and observability route](../../knowledge-base/70-meta-wearables/23-debugging-observability-and-diagnostic-evidence.md) when a transport or audio symptom needs first-failure events, app-visible readiness, or a redacted handoff.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) when transport setup includes project identity, callback configuration, package access, signed release, or processing-location evidence.
- Refresh the pinned [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md), [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md), [full DAT reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat), and [Wearables MCP](https://mcp.developer.meta.com/wearables).
- Inspect the actual target configuration before changing it: iOS `Info.plist`/entitlements and `AVAudioSession`; Android Manifest, runtime permissions, Gradle artifact, and audio route. Never copy a neighboring platform's transport keys.

#### Workflow

1. Freeze the tuple: platform, app build, DAT package/artifact, Meta AI app,
   glasses model/runtime identity, firmware/DAT-app, account/mode/channel, and
   requested capability.
2. Classify each hop as Bluetooth control/registration, Wi-Fi/local-network
   media, HFP input, A2DP output, Internet/remote service, or unknown. Record
   the actual observed route rather than inferring it from a connected-device
   label.
3. Audit configuration. On iOS, inspect Bluetooth usage/background keys,
   local-network disclosure, Bonjour services, callback, and selected DAT
   version. On Android, inspect Bluetooth/Internet permissions, callback,
   application/client metadata, target SDK, and the resolved Maven artifact;
   keep the reviewed Android Wi-Fi parity question explicit if the artifact or
   docs do not establish it.
4. Define resource ownership and bounded queues for camera, Display, and
   audio. Specify drop/coalesce/block policy, timestamps, stop order,
   cancellation, route changes, and what happens when the link degrades.
5. Treat HFP and A2DP as separate audio paths. Verify route settlement,
   consent, sample format, interruption, output selection, and restoration;
   do not call a phone microphone or A2DP playback a glasses microphone.
6. Handle typed lifecycle, battery, thermal, peak-power, doff/fold, timeout,
   and disconnect states with a visible fallback and bounded recovery. Avoid
   blind retry loops and never retry a thermal or power shutdown automatically.
7. Classify processing location separately from transport. Wi-Fi or Bluetooth
   does not prove glasses-native inference, and a local phone consumer does not
   prove remote processing is absent.
8. Return source/static/compile/mock/connected/physical/signed evidence with
   the exact transport, route, processing, and failure fields.

#### Fast path

Choose one bounded operation and write its states: start -> active -> degraded or disconnected -> recover or stop. Measure queue, latency, and thermal budgets at the adapter boundary, test interruption/reconnect, and defer other transports.

#### Required output

- transport and processing-location diagram;
- iOS/Android configuration and parity matrix;
- audio-route matrix for phone mic, HFP input, A2DP output, and remote speech;
- queue/backpressure, cancellation, thermal, battery, and stop-order policy;
- failure/recovery table with the first failing state and typed fallback;
- redacted test script for mock, build, connected, and physical runs;
- explicit status for Wi-Fi parity, “Gen 3,” and any source-conflicted route.

#### Hard boundaries

- Never infer Wi-Fi support on Android from iOS configuration or a broad
  `INTERNET` permission; resolve the artifact/docs and test the named target.
- Never treat Bluetooth connection, registration, or a stable link as proof of
  camera, Display, HFP, Wi-Fi, or physical input capability.
- Never claim on-device processing from a transport path. Record where raw
  frames/audio, inference, storage, and network transfer occur.
- Never persist raw personal media or device identifiers in logs, fixtures,
  screenshots, diagnostics, or skill archives.
- Never hide link, permission, timeout, thermal, or power failure behind an
  indefinite spinner or unbounded automatic retry.
- Never use a mock, browser simulator, phone-only audio route, or one device
  generation as evidence for another generation or for release readiness.

#### Related roles

- [DAT camera and audio](../meta-dat-camera-audio/SKILL.md)
- [DAT iOS integration](../meta-dat-ios-integration/SKILL.md)
- [DAT Android integration](../meta-dat-android-integration/SKILL.md)
- [Android API atlas](../meta-dat-android-api-atlas/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)

#### Sources

- [DAT iOS repository](https://github.com/facebook/meta-wearables-dat-ios)
- [DAT iOS changelog](https://github.com/facebook/meta-wearables-dat-ios/blob/main/CHANGELOG.md)
- [DAT Android repository](https://github.com/facebook/meta-wearables-dat-android)
- [DAT Android changelog](https://github.com/facebook/meta-wearables-dat-android/blob/main/CHANGELOG.md)
- [DAT-filtered full reference](https://wearables.developer.meta.com/llms.txt?full=true&product=dat)
- [Wearables MCP](https://mcp.developer.meta.com/wearables)
- [Apple AVAudioSession](https://developer.apple.com/documentation/avfaudio/avaudiosession)
- [Apple local network privacy](https://developer.apple.com/documentation/bundleresources/information_property_list/nslocalnetworkusagedescription)
- [Apple Network framework](https://developer.apple.com/documentation/network)
- [Android Bluetooth permissions](https://developer.android.com/develop/connectivity/bluetooth/bt-permissions)

---

### Meta Wearables Web Apps

**Name.** `meta-wearables-web-apps`

**When to use.** Build and review Meta Wearables Web Apps for the Ray-Ban Display with the official HTML/CSS/JavaScript route, 600×600 display contract, public HTTPS delivery, focus and D-pad/EMG input, performance limits, source-conflict handling, and separate simulator/physical-device evidence.

Use the official Web App surface when the experience should be delivered to the Meta Ray-Ban Display as a web page. Keep it separate from a native iOS DAT companion app and from native DAT Display code.

#### Read before acting

- Inspect the actual Web App source, build toolchain, URL/deployment target, assets, state model, and any companion phone integration.
- Read [Web Apps, Display, and input](../../knowledge-base/70-meta-wearables/06-web-apps-display-and-input.md) and the local [Web App display contract](references/display-contract.md).
- Load the Web Apps `api_surface.rows` from the portable [source-pinned manifest](../meta-wearables-full-sdk-audit/references/surface-manifest.yaml)
  and preserve each runtime/source-conflict row independently; toolkit guidance
  is not physical glasses proof.
- Read [input, sensors, and physical interaction](../../knowledge-base/70-meta-wearables/24-input-sensors-and-physical-interaction.md) and the local [input/sensor contract](../meta-wearables-input-sensors/references/input-sensor-contract.md) when the feature uses D-pad/EMG/temple input, motion, orientation, geolocation, or source-conflicted Web App features.
- Read the [on-device compliance and runtime contract](../../knowledge-base/70-meta-wearables/17-on-device-compliance-and-runtime-contract.md) for public-data boundaries, processing location, network/storage behavior, and typed fallback.
- Read the [security, attestation, and credential-boundaries route](../../knowledge-base/70-meta-wearables/25-security-attestation-and-credential-boundaries.md) for HTTPS origin/deployment boundaries, client-visible assets, server-side credentials, Meta AI add/launch state, and any claim that a Web App has native DAT attestation or local processing.
- Read the [operational readiness and recovery route](../../knowledge-base/70-meta-wearables/18-operational-readiness-and-recovery.md) for companion, firmware, public HTTPS launch, Display provisioning, release-channel, and recovery gates.
- Read the [application architecture and platform-boundaries route](../../knowledge-base/70-meta-wearables/19-application-architecture-and-platform-boundaries.md) when a Web App shares product state, data, or fallback behavior with a native companion.
- Read the portable [toolkit skill map](references/toolkit-skill-map.md) when the request asks for scaffolding, text entry, gestures, sensors, offline behavior, API connection, staging, or publishing.
- For a new reducer-first scaffold, copy the [dependency-free Web App starter](../meta-wearables-implementation-recipes/assets/meta-wearables-web-starter) and run `node --test ../meta-wearables-implementation-recipes/assets/meta-wearables-web-starter/test/display-state.test.mjs` from this package directory before binding the selected toolkit revision.
- Read the official [Meta Wearables Web App repository](https://github.com/facebook/meta-wearables-webapp), [agent guidance](https://github.com/facebook/meta-wearables-webapp/blob/main/AGENTS.md), [Display guidelines](https://github.com/facebook/meta-wearables-webapp/blob/main/plugins/meta-wearables-webapp/references/display-guidelines.md), and [performance guidelines](https://github.com/facebook/meta-wearables-webapp/blob/main/plugins/meta-wearables-webapp/references/performance-guidelines.md).
- Run the bundled [Web App preflight receipt runner](scripts/run_webapp_preflight.py) against the actual entrypoint before calling a local HTML/JS surface a Meta delivery target. Use `--require-meta-markers` and, when a public URL is available, `--origin <https-url> --check-origin --require-https-origin`; this keeps hosted HTTPS evidence separate from browser-simulator and physical Display evidence.
- Compare the toolkit `main` guidance with the current [full Developer Center reference](https://wearables.developer.meta.com/llms.txt?full=true). The reviewed sources conflict about text composition, offline support, back navigation, sensors, and extended gestures; carry those features as `source-conflict`/`to-verify` until the exact runtime closes the gap.
- Refresh the current [Web Apps documentation](https://wearables.developer.meta.com/docs/develop/webapps) and record any login/access limitation rather than treating an inaccessible page as proof.

#### Fast path

Preflight the entrypoint and public origin first, then build one 600x600 interaction with keyboard/focus/input and fallback states. Validate the local fixture, HTTPS origin, browser simulator, and physical Display in order, stopping at the first unavailable gate.

#### Web App workflow

1. Define the glanceable user outcome and whether the Web App is standalone or paired with a phone service.
2. Build the smallest HTML/CSS/JS surface within the official display contract: 600×600, additive content, short text, clear hierarchy, and no assumptions about a phone viewport. Keep source-conflicted features behind explicit capability/fallback decisions.
3. Implement focus and input deliberately. The user should know the focused item, the D-pad/available input actions, the selected result, and how to dismiss or recover.
4. Use the current official metadata/launch contract and a public HTTPS URL. Do not ship a local URL, private tunnel, environment-secret URL, or native DAT import inside a Web App.
5. Keep data minimal and resilient. Design loading, stale data, no connection, permission/consent handoff, invalid input, and service failure states.
6. Follow the official performance guidance: keep bundles and assets small, minimize work per update, avoid unnecessary animation, and measure load/interaction latency on the target surface.
7. Test in the official browser simulator, then validate the deployed URL, then run the same script on physical Ray-Ban Display hardware. Record each result separately.

#### Source-conflict handling

The public full-reference index reviewed 2026-08-22 says Web Apps do not
support camera, microphone, text input, offline support, notifications, or back
navigation. The official toolkit `main` at commit
The 2026-08-30 source refresh confirmed that the toolkit moved from
`facebookincubator/meta-wearables-webapp` to `facebook/meta-wearables-webapp`.
The relocation compare changed README, installer, and plugin metadata only; it
did not add a runtime/API proof row. The current toolkit commit
`a2714f862c61b1ce9c6cb624fc7e4938087102db` documents an on-glasses
handwriting/voice composer, service-worker/offline patterns, Escape/back,
gestures, and sensors. Do not collapse those into a single “supported” list.

For each conflicted feature, record the source revision, exact simulator/build,
target firmware, physical result, fallback, and release decision. The safest
implementation keeps phone/manual entry and online/error/exit fallbacks until
the runtime result and current authenticated docs agree.

#### Display contract

- 600×600 is the Web App canvas contract; design inside it rather than scaling a phone page down.
- Content is additive to the Display experience; preserve legibility and avoid visual noise.
- D-pad/EMG or other current input must have a visible focus and deterministic action mapping.
- Every screen has a first useful state and a recoverable unavailable/error state.
- The app must tolerate missing, stale, or delayed data without exposing private implementation details.
- Text composer, offline cache, Escape/back, extended gestures, and sensors are source-conflict features in the current public snapshot; gate them independently.

#### Required output

- route decision: Web App versus native DAT Display;
- public URL/launch metadata and deployment verification;
- display/input state map and performance budget;
- simulator test cases;
- physical Ray-Ban Display test script and evidence status;
- phone handoff, privacy, and failure behavior.

#### Hard boundaries

- A browser simulator proves web behavior and some input logic; it does not prove real glasses rendering, brightness, latency, comfort, or hardware input.
- Do not claim support for Oakley, “Gen 3,” or any other device without a current official Web App/device mapping and hardware result.
- Do not expose secrets or raw personal media in a public URL or client bundle.
- Do not represent a Web App as a native iOS DAT integration in App Store copy or source documentation.
- Do not assume arbitrary browser APIs, background execution, camera/microphone access, notifications, or Meta AI voice commands are available on the Display. Treat the toolkit’s text/offline/back/sensor/gesture guidance as target-specific until proven.

#### Related routes

- [Meta route planner](../meta-wearables-route-planner/SKILL.md)
- [Meta agentic team](../meta-wearables-agentic-team/SKILL.md)
- [DAT Display](../meta-dat-display/SKILL.md)
- [Input and sensors](../meta-wearables-input-sensors/SKILL.md)
- [Device proof](../meta-wearables-device-proof/SKILL.md)
- [Privacy and publishing](../meta-wearables-privacy-publishing/SKILL.md)
- [On-device compliance](../meta-wearables-on-device-compliance/SKILL.md)
- [Operational readiness](../meta-wearables-operational-readiness/SKILL.md)
- [Application architecture](../meta-wearables-app-architecture/SKILL.md)
- [Web App preflight receipt runner](scripts/run_webapp_preflight.py)

#### Sources

- [Meta Wearables Web App repository](https://github.com/facebook/meta-wearables-webapp)
- [Web App agent guidance](https://github.com/facebook/meta-wearables-webapp/blob/main/AGENTS.md)
- [Web App Display guidelines](https://github.com/facebook/meta-wearables-webapp/blob/main/plugins/meta-wearables-webapp/references/display-guidelines.md)
- [Web App performance guidelines](https://github.com/facebook/meta-wearables-webapp/blob/main/plugins/meta-wearables-webapp/references/performance-guidelines.md)
- [Meta Wearables Web Apps documentation](https://wearables.developer.meta.com/docs/develop/webapps)
- [Official Ray-Ban Display Web App simulator](https://chromewebstore.google.com/detail/meta-ray-ban-display-web/jpjlmmodokemlepklkdbimceggpbjcll)

---
