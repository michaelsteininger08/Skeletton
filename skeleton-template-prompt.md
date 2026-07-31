# Skeleton Video – Master Prompt Template

**So benutzt du das:** Teil A ist bei jedem Video identisch – einfach komplett kopieren.
In Teil B trägst du die vier bis fünf Werte für das jeweilige Video ein.
Beide Teile zusammen in ein neues Claude-Design-Projekt einfügen.

---

## TEIL A – bleibt immer gleich

```
Build a vertical animated video with three.js. The character animation carries the
video — keep visual effects to an absolute minimum.

FORMAT: exactly 1080x1920. renderer.setSize(1080, 1920), camera.aspect = 1080/1920,
canvas fills the viewport, body margin 0, no container, no padding.

CHARACTER LOADING — FBXLoader. Base URL:
  https://cdn.jsdelivr.net/gh/michaelsteininger08/Skeletton@main/
BASE model (provides the skinned mesh): Talking.fbx
Load BASE first and keep its SkinnedMesh — it is the ONLY figure in the scene.
From every other clip file take ONLY animations[0]. Register all clips on a SINGLE
AnimationMixer attached to the base model. Call mixer.update(delta) every frame with a
THREE.Clock. If a clip URL 404s, fall back to the base animation and log it.
Do NOT scale or reposition the figure to fit the camera — move the CAMERA instead.

TRANSITION CLIPS (standtosit, layingdown, gettingup or similar) must play once and hold:
  action.setLoop(THREE.LoopOnce); action.clampWhenFinished = true;
All other clips loop seamlessly.

WIREFRAME LOOK: traverse the model and replace every material with
MeshBasicMaterial({ wireframe: true, color: 0x9FEBFF, transparent: true, opacity: 1.0 }).
Keep skeleton and skinning intact — do not convert SkinnedMesh to Mesh.

HIDDEN LINE REMOVAL (required, or the skull reads as a solid blob): add a second copy of
the figure underneath. THREE.SkeletonUtils.clone() (a plain .clone() breaks skinned
animation), same mixer timing, MeshBasicMaterial({ color: 0x000000 }), scale 0.985,
polygonOffset: true, polygonOffsetFactor: 1, polygonOffsetUnits: 1.
Eye sockets, nasal opening and jaw line must stay clearly readable.

ENVIRONMENT LOADING — GLTFLoader. The .glb files are authored in metres with a
reference figure height of 1.8, while the Mixamo FBX uses different units. Apply the
unit fix:
  envScene.scale.setScalar(53);
  envScene.position.y = <figure bounding box min.y>;
  envScene.position.x = <figure bounding box centre x>;
  // keep the z offsets from the file untouched
Log the applied scale. FloorMat is the ONLY floor — do not draw a separate grid floor.
If the figure floats above or sinks into the floor, offset the FIGURE on Y, never the
environment.

ENVIRONMENT MATERIAL:
  env.traverse(c => { if (c.isMesh) c.material = new THREE.MeshBasicMaterial({
    wireframe: true, color: 0x6ECFEF, transparent: false, opacity: 1.0 }); })
FloorMat gets transparent: true, opacity: 0.5. The figure stays brightest at 0x9FEBFF.

CAMERA — calm and wide, this matters:
- The figure fills 55-75% of frame height. Never closer than head-and-upper-body.
  No tight face close-ups.
- Slow drifts and gentle arcs only. No fast orbits, no rapid push-ins.
- One continuous move per segment, blended over 0.8s between segments. No hard cuts.
- Keep foreground environment objects partially in frame for depth.
- During transition clips hold the camera nearly still in a wide three-quarter view.
- Environment equipment must be visible in every shot; the upper third of the frame
  must never be empty black.

BACKGROUND: pure black (#000000) apart from the loaded environment. About 80 subtle
glow particles as THREE.Points — keep the count low. A faint radial cyan gradient div
behind the canvas.

GLOW: CSS filter on the canvas — drop-shadow(0 0 4px #7FE3FF) drop-shadow(0 0 14px
#3BC9F5).

PERFORMANCE: a stable 30fps matters more than any visual extra. If frames drop, reduce
particles and the most distant environment objects first. The character animation must
never stutter.

RECORDING: create the renderer with preserveDrawingBuffer: true. Add a small "REC"
button as a DOM overlay (not inside the canvas, excluded from the recording), fixed
top-left. On click: restart the animation at t=0, capture with canvas.captureStream(30),
record via MediaRecorder with 'video/webm;codecs=vp9' and videoBitsPerSecond: 12000000,
stop automatically after exactly <LENGTH> seconds and download the .webm. The recording
must contain only canvas content at full resolution.

NO TEXT anywhere: no captions, no subtitles, no labels, no numbers. I add all text
myself in a video editor.

DO NOT ADD under any circumstances: camera shake, glitch effects, chromatic aberration,
white flashes, particle bursts, screen-wide pulses or hard cuts. Earlier versions had
these and the render stuttered and froze.
```

---

## TEIL B – pro Video ausfüllen

```
TOTAL LENGTH: <z. B. 30> seconds.

ENVIRONMENT: load <gym3.glb | bedroom2.glb | ...> from the base URL.
Named nodes: <Liste aus der jeweiligen Datei>

CLIPS AND TIMELINE (crossFadeTo(0.6), or 0.35 into and out of transition clips):
  <0-9s     sleeping   loop,  Y offset 31.3>
  <9-11s    gettingup  once,  ease Y offset 31.3 -> 0>
  <11-19s   nervous    loop>
  <19-24s   walking    loop>
  <24-30s   idle       loop>

Expose all Y offsets as named constants at the top of the file and log them every
second, so I can check whether the figure rests correctly on furniture.

ONE EFFECT ONLY: <z. B. a soft warm glow on the skull, pulsing slowly, brightest during
11-19s, fading to calm by the end>. No other effects.
```

---

## Verfügbare Bausteine

**Umgebungen (im Repo):**
- `gym3.glb` – BenchPress, DumbbellRack, DumbbellRackBack, Kettlebells, PlateTree,
  PlateTreeBack, LoosePlates, Barbell, SquatStand, FloorMat
- `bedroom2.glb` – Bed, NightStand, FloorLamp, Armchair, Rug, WallClock, Window,
  Shelf, FloorMat

**Höhenwerte (Modelleinheiten × 53 = Szeneneinheiten):**
- Matratzenoberkante: 0.59 → 31.3
- Sitzhöhe Bettkante: ca. 0.45 → 24
- Bankhöhe (BenchPress): 0.50 → 26.5

**Mixamo-Clips (im Repo):**
Talking, nervous, walking, running, idle, sleeping, sitting,
bicepcurl, airsquat, pushup, flexing, standtosit, layingdown, gettingup

---

## Checkliste vor dem Export

- [ ] Auflösung 1080x1920, nicht quer
- [ ] Figur steht auf dem Boden, schwebt nicht darüber
- [ ] Umgebung sichtbar im oberen Bilddrittel
- [ ] Kamera nicht zu nah am Gesicht
- [ ] Keine Effekte, kein Text im Bild
- [ ] Länge stimmt mit dem Voiceover überein
