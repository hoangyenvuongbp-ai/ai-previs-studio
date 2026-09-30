# AI PREVIS STUDIO

## Codex Master Specification — V0.1

**Handoff build • 30 Sep 2026**

> Purpose: direct handoff to Codex. This document is the product/technical source of truth for V0.1.

## 1. Mission / Product Definition
Build a distributable desktop previs application for filmmakers who create final live-action-looking video with generative video models such as Seedance. The app is NOT a Blender clone and NOT a photorealistic 3D character generator. It is a specialized staging, motion, camera and continuity tool.

Core separation: Character sheets / final visual references define APPEARANCE. The previs scene defines SPACE, SCALE, BLOCKING, MOTION, CAMERA, LENS, TIMING, OCCLUSION and INTERACTION.

The app must make 3D staging accessible to a filmmaker who does not want to operate Blender directly. Blender is the hidden 3D/motion/render engine where practical.


## 2. Non-negotiable Product Principles
P1 — Visual proxy, human motion. Characters may look deliberately abstract, but their movement must target live-action human behavior rather than mannequin/game-like motion.

P2 — Geometrically informative, visually non-authoritative. A reference render must communicate geometry and motion strongly while minimizing accidental costume/face/material information that a video model might copy.

P3 — Editor View and AI Reference View are separate render/display layers. Editor View may use strong colors, labels, markers, outlines and helpers. AI Reference View automatically strips them.

P4 — Silhouette-first modeling. If a small detail does not materially change silhouette, collision, interaction or camera occlusion at the intended shot distance, do not model it for the proxy.

P5 — Do not rebuild reliable Blender capabilities. Build the filmmaker workflow and abstraction layer on top of Blender.

P6 — V0.1 is a vertical slice. Do not attempt a complete commercial V1 in the first implementation.


## 3. Reference Project: Jenny Pixel Studio
Use Jenny Pixel Studio as a behavioral/UX reference for simple scene building, asset browsing, transform controls, camera/lens/framing workflows, shot-oriented staging and AI-assisted scene layout. Do not copy source code or branding. Reimplement concepts independently.

Reference repository: https://github.com/vienglacial/TranDucVien-Storyboard-3d-Previz/blob/main/jenny-pixel-studio.html

Known reference file scale at handoff: GitHub reports jenny-pixel-studio.html at 9,071 lines / 525 KB. Treat it as a reference implementation to study, not a dependency.

Our differentiator is temporal previs: animation timeline, realistic human motion, camera motion, actor tracking, terrain/contact adaptation and AI-video-oriented reference export.


## 4. Target User Workflow
1) Create/open project. 2) Create/import environment proxies. 3) Create/import character entities. 4) Assign faction/unit/hero-extra roles. 5) Place actors and props. 6) Assign actions/mocap and edit paths. 7) Place camera and lens. 8) Build a shot on timeline. 9) Preview in real time. 10) Refine human motion. 11) Render AI Reference video. 12) Use that video together with character/environment reference images and prompt in the final video model.


## 5. Application Shell / UX
Primary workspace: Asset Library (left), 3D Viewport (center), Inspector / Camera / Character properties (right), Shot strip + Timeline (bottom), optional Reference Pin / Camera Monitor.

Top-level concepts visible to users: Project, Scene, Character, Environment, Prop, Action, Camera, Shot, Timeline, Render. Hide Blender-specific complexity unless an Advanced/Developer mode is enabled.

Core viewport actions: select, multi-select, move, rotate, scale, duplicate, delete, frame selected, solo selected, hide, lock, snap to ground, assign action, create camera, set camera target.

Support Vietnamese UI first, with internal keys/IDs in English for maintainability and later localization.


## 6. Character Entity Model
A Character Entity is not the mesh. It is a production record linking: identity metadata, optional character sheet/reference images, motion proxy, skeleton/rig, faction/unit, hero/extra status, editor identity settings, and animation tracks.

Required fields: character_id, display_name, faction_id, unit_id, role (hero/extra), height, body_build, shoulder_width, torso_ratio, leg_ratio, head_scale, proxy_profile, reference_images[], motion_rig_id, editor_identity_profile.

Character sheet images remain visible inside Character Card / Reference Pin but are never baked automatically into the AI Reference render.


## 7. Proxy Identity System — Critical
Do NOT rely on height alone to distinguish actors. In a wide shot, realistic height differences are visually insufficient.

Editor-only identity signals may include: faction color, unique hero marker, screen-space floating label, ID number, body pattern overlay, selection outline, halo, and Character Navigator. These are viewport/display features, not costume geometry.

Identity View modes: OFF, MINIMAL, NORMAL, STRONG. MINIMAL = faction only. NORMAL = faction + hero marker. STRONG = faction + marker + ID/label + outline/pattern.

Character Navigator groups Faction → Unit → Hero/Extras. Clicking an entry selects and frames the actor. Double-click may solo/frame. Search by name/ID.

Hero characters receive stable unique editor identities. Extras primarily use faction/unit identity plus controlled body and motion variation; do not require unique visual design for every extra.

AI Reference export must forcibly disable text labels, bright faction colors, UI markers, gizmos, selection outlines and editor-only patterns. A subtle neutral actor differentiation may remain only if needed for temporal tracking.


## 8. Proxy Geometry Rules
Human proxy: anatomically readable body volume, simple head, hands/feet sufficient for pose/contact reading, no realistic face, skin, hair or fabric texture.

Allow body silhouette controls: height, build, shoulder width, torso/leg proportions, head scale and posture.

Headgear / clothing / equipment should only be represented when they materially change silhouette or interaction. Large backpack, long weapon or shield-like object may be kept. Buttons, seams, straps, canteens and small pouches are normally omitted.

Environment proxy follows the same rule: preserve dimensions, elevation, openings, walkable surfaces, occluders, collision and major silhouettes; omit decorative surface detail.

Vehicle proxy: preserve bounding dimensions, wheel/track volume, turret/body orientation and large moving/interactive parts; do not spend V0.1 effort on fine mechanical detail.


## 9. Create New Model / Proxy
Provide three character creation paths: Basic Proxy, Describe Character, Import Reference.

Describe Character: parse natural-language description into semantic proxy parameters (body proportions, headgear silhouette, torso volume, large equipment, held prop). Do not generate photorealistic appearance.

Import Reference: analyze a character sheet or reference image to estimate silhouette/proportion and large-form proxy attributes. Present extracted attributes for user review before Accept.

Provide Silhouette Match control conceptually ranging from Generic to Accurate, but accuracy applies to large forms only; never infer/add face/skin/fabric realism in proxy mode.

Future plugin interface may support detailed text/image-to-3D services, but detailed AI 3D generation is explicitly OUT OF SCOPE for V0.1.


## 10. Faction / Unit System
Every actor can belong to a faction and optional unit. Editor View can color-code factions strongly for battle readability. Units may use shade/pattern variations within a faction.

Faction readability should also be supported by broad silhouette categories when historically/logically appropriate, but do not distort realistic human proportions merely for identification.

AI Reference View converts faction colors to neutral/clay treatment by default. Keep only subtle tonal separation if testing shows it improves actor tracking without causing appearance leakage.


## 11. Motion System — Highest Character Priority
Motion quality is more important than proxy visual detail. Target live-action plausibility.

Architecture should support mocap/animation clips, retargeting to a standard humanoid rig, root motion, motion blending, transitions, foot IK, hand IK where needed, ground contact, terrain adaptation, collision awareness, head/look-at control and secondary motion hooks.

Avoid abrupt state changes. Example state chain: Stand → Anticipation → Start Run → Accelerate → Run → Decelerate → Crouch → Kneel, with blended transitions.

Terrain adaptation: raycast/ground sampling for feet, pelvis compensation, slope alignment limits and step-height handling. Characters must not visibly float, skate or penetrate terrain in accepted V0.1 shots.

Crowd variation: randomized start offsets, clip variants, speed, cadence, stride, head direction and small path deviation. Do not let groups move in perfect synchronization.

Hero Motion: heroes can have individual action tracks, look-at targets and refined transitions. Extras may use lower-cost variation.

Two quality modes: Fast Previs for interactive staging; Refine Human Motion for final AI-reference export. V0.1 may implement the architecture and a limited subset of refinement features, but the distinction must exist.


## 12. Action Library
Initial categories: Locomotion (idle, walk, fast walk, jog, run, sprint, crouch-run), Combat/pose (crouch, kneel, prone, aim/fire proxy, throw proxy), Transitions (stand↔crouch, stand↔kneel, stand↔prone), Interaction (sit/stand, climb/step-over placeholder).

V0.1 may ship with a small curated set of legally distributable or user-provided motion clips. Design import/retarget hooks so the library can expand later.


## 13. Timeline / Shot System
Timeline must be temporal, not merely pose snapshots. Tracks: Character Action, Character Transform/Path, Camera Transform, Camera Lens/Focus (where implemented), Object/Vehicle Transform, Visibility/Event markers.

Shot entity fields: shot_id, name, duration, fps, active_camera_id, aspect_ratio, resolution preset, timeline range, notes, render preset.

Shot browser supports create, duplicate, rename, reorder, activate, preview and render. Preserve scene continuity across shots by referencing shared scene entities rather than regenerating them.

V0.1 target: at least one 5–10 second shot with multiple animated characters and an animated camera, playable in viewport and renderable to MP4.


## 14. Camera / Cinematography
Camera is a first-class filmmaking tool. Controls: focal length presets and numeric value, sensor/FOV, camera height, transform, target/look-at, framing guides, safe areas, aspect ratio, near/far clipping.

Camera motion V0.1: static, keyframed transform, target tracking/look-at. Architecture should later support dolly, orbit, crane, handheld procedural motion and path-based tracking.

Camera Monitor shows the active camera framing while user manipulates the scene. Provide common focal presets such as 18/24/28/35/50/75/100 mm, but allow custom values.


## 15. Environment / Props
Support primitive/procedural proxy assets: box, cylinder, plane, wall, bunker-like block, trench/ditch placeholder, tree/canopy proxy, rock/obstacle, road/path placeholder, vehicle proxy and generic prop.

V0.1 does not require a historically complete asset library. It requires an extensible asset schema, import path (GLB/FBX where feasible through Blender) and enough primitives to stage a battlefield/interior test.

Text-to-environment may initially produce structured scene instructions/primitive placement rather than a full generated mesh.


## 16. Editor View vs AI Reference View
EDITOR VIEW may show: faction colors, hero colors/markers, labels, IDs, outlines, trajectories, camera frustums, gizmos, grid, paths, selection states and reference cards.

AI REFERENCE VIEW must hide: all text, UI, gizmos, labels, bright ID markers, selection outlines, camera helpers and nonessential overlays.

AI Reference material style: matte/clay, low-detail, non-photorealistic, no skin, no face, no realistic fabric, restrained neutral tones. Preserve readable body volume and scene depth.

Risk statement: no abstraction strategy can guarantee zero appearance leakage in a generative video model. The product goal is to reduce appearance authority of the previs so that explicit image references and prompts dominate final appearance.

Add export abstraction presets: LOW DETAIL, ABSTRACT (default), MOTION ONLY. MOTION ONLY may remove nonessential environment objects while preserving interaction, occlusion and spatial cues.


## 17. Render / Export
Minimum V0.1 export: MP4 AI Reference video from active shot, with user-selectable resolution/FPS and AI Reference material/overlay policy enforced.

Useful auxiliary outputs, prioritized after core MP4: depth sequence/video, edge pass, segmentation/object IDs, character masks, pose/skeleton visualization, normal pass, optical flow and camera metadata. Do not block V0.1 completion on all passes.

Provide a render validation warning if editor-only overlays are still enabled or if unsupported visual detail is accidentally included in AI Reference preset.


## 18. Blender Integration
Preferred architecture: application/frontend controls a scene data model; Blender provides 3D scene, rigging, animation, IK, collision/terrain queries, camera and Eevee rendering.

Use Blender Python API (bpy) behind a clean adapter/service layer. Do not scatter bpy calls throughout UI code. Define interfaces such as SceneEngine, CharacterEngine, MotionEngine, CameraEngine and RenderEngine.

The app should be distributable to end users. Do not assume end users have ChatGPT Plus or Codex. Codex is a development tool, not a runtime dependency.

V0.1 may require a supported Blender installation if bundling Blender is legally/technically premature. Keep packaging strategy modular so a later installer can manage dependencies cleanly.


## 19. Suggested Technical Architecture
Frontend: choose a desktop-capable UI stack that can communicate reliably with Blender and support a responsive timeline/asset browser. Codex must document the choice before implementation. Avoid locking the product into a single giant HTML file.

Core domain model should be engine-agnostic JSON-serializable data: Project, Scene, Asset, Character, Faction, Unit, Camera, Shot, Track, Clip, RenderPreset.

Blender bridge: local process/service or embedded workflow with explicit request/response messages and deterministic IDs. Scene state must be serializable and recoverable.

Project format: versioned JSON manifest plus asset references. Include schema_version from day one and migrations later.

Autosave/recovery is desirable but secondary to core vertical slice.


## 20. AI Features and Runtime Boundaries
V0.1 core editing/rendering must work without requiring ChatGPT Plus. Optional AI parsing/image analysis can be behind provider adapters and may require API credentials or later backend services.

Never hard-code a single AI provider into the domain model. Define AIAdapter interfaces for TextToProxy, ImageToProxy and SceneLayout.

When AI generates a proxy or layout, show editable extracted parameters and require explicit user acceptance before committing destructive changes.

Do not send user reference images to third-party APIs silently. Any network AI operation must be explicit and provider-aware.


## 21. V0.1 Scope — Build This First
- A. Desktop project shell with asset library, viewport, inspector, shot/timeline area.
- B. Blender bridge and persistent scene IDs.
- C. Primitive environment + import of basic 3D assets.
- D. Character Entity + humanoid proxy + body proportion controls.
- E. Faction/Unit/Hero/Extra metadata and Editor Proxy Identity overlays.
- F. Small motion library, retarget/assignment path, timeline playback, root-motion support, basic blending and basic foot-to-ground correction.
- G. Camera with focal length, transform, look-at/target, camera monitor and keyframed movement.
- H. Shot entity, 5–10 second timeline and MP4 render.
- I. Editor View / AI Reference View toggle and enforced clean AI Reference render.
- J. Save/load project.
- K. Text/Image → Proxy may be implemented as a stub/interface in the first runnable build if model/API selection would delay the vertical slice; the UI and data contract must exist.

## 22. Explicitly Out of Scope for V0.1
Photorealistic character generation; face reconstruction; detailed cloth simulation; final-quality hair; historically complete asset packs; full crowd simulation at hundreds of agents; advanced ragdoll/destruction; full NLE editing; direct Seedance API integration; commercial licensing/payment/account system; cloud collaboration; fully automatic text-to-film generation.

Do not spend time polishing detailed costume accessories that do not change silhouette or interaction.


## 23. Acceptance Tests for V0.1
- AT1 — User can create a project, add terrain/obstacles, add at least 6 characters split across 2 factions, and visually distinguish heroes in Editor View without relying on height.
- AT2 — User can attach a reference image/character sheet to a Character Card without that image appearing in the rendered AI Reference video.
- AT3 — User can assign at least two different locomotion/action clips and play a 5–10 second scene. Transitions must not hard-snap under normal use.
- AT4 — Feet remain acceptably grounded on a simple uneven test surface; no persistent visible skating/floating/penetration in the reference shot.
- AT5 — Multiple extras using the same base motion do not start and move in perfect synchronization.
- AT6 — User can create an active camera, set 35 mm (or another focal length), animate camera movement/look-at, and preview through Camera Monitor.
- AT7 — Editor View clearly shows faction/identity overlays. AI Reference render contains no labels, IDs, gizmos, selection outlines or bright editor faction colors.
- AT8 — AI Reference MP4 exports successfully at a defined FPS/resolution and preserves camera framing, actor paths, timing and occlusion.
- AT9 — Save project, close/reopen, and recover character IDs, faction/unit assignment, shot/timeline, camera and asset placement.
- AT10 — Architecture review confirms Blender-specific code is isolated behind adapters and the domain project format is versioned/serializable.

## 24. Test Scene for the First Vertical Slice
Use a neutral battlefield-like test rather than a polished historical recreation: uneven ground, one trench/ditch, two bunker/block forms, several tree/canopy proxies, one vehicle proxy, two factions, 6–10 characters.

Hero A performs a crouched run toward cover, slows, kneels and looks toward a target. Hero B moves on a different path. Extras advance with timing/motion variation. Opposing extras remain/move near the bunker. Camera performs a low 35 mm tracking move for 6–8 seconds.

This scene is deliberately chosen to test silhouette readability, faction/hero identification, terrain contact, motion blending, occlusion, camera tracking and AI Reference abstraction in one shot.


## 25. Development Rules for Codex
Before coding, produce: (1) architecture decision record, (2) repository tree, (3) V0.1 milestone plan, (4) risk list. Then implement the smallest runnable vertical slice.

Commit in small logical increments. Add automated tests for serializable domain models and non-UI logic. Add a repeatable smoke-test project.

Do not silently change product principles to simplify implementation. If a requirement conflicts with Blender/runtime constraints, document the tradeoff and propose the smallest alternative.

Prefer placeholders/stubs for future AI services over blocking the core 3D/motion/camera pipeline.

Keep all externally sourced code/assets license-compatible and document licenses. The Jenny Pixel project is a UX/behavior reference only unless its license explicitly permits a specific reuse.

Do not optimize for visual beauty at the expense of motion fidelity, editing speed or clean AI-reference abstraction.


## 26. Post-V0.1 Roadmap
V0.2: stronger motion blending, better foot IK/terrain adaptation, motion import/retarget UX, hero look-at/hand interactions, more camera rigs.

V0.3: Text/Image → Semantic Proxy adapters, scene-layout assistant, improved Character Card/reference workflow.

V0.4: depth/edge/segmentation/pose export, batch shot render, render diagnostics.

V0.5: installer/tester distribution, dependency management, asset packs, performance profiling for larger scenes.

V1.0: production-ready project management, extensible plugin SDK, mature motion system, robust packaging, documentation and optional AI provider integrations.


## 27. One-Sentence Product Test
If a filmmaker can stage a complex shot with clearly identifiable proxy actors, preview believable live-action-like movement and camera motion, then export a deliberately abstract reference video that communicates motion/space without dictating final character appearance, the product is doing its job.


## 28. Initial Codex Instruction
Read this specification completely before modifying code. Do not start by building a Blender clone or a photorealistic character creator. First return an architecture proposal and V0.1 implementation plan mapped directly to Sections 21 and 23. After approval, create the repository and implement the smallest runnable vertical slice that passes AT1–AT10.

