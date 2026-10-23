# Particle Life VR

An immersive, sonified **Particle Life** simulation for the browser, built for **Meta Quest 3** with WebXR. You stand inside a periodic box of interacting particles. Its periodic copies surround you in every direction, so the space has no edges. You can push, sweep and attract particles with your hands while a spatial soundtrack reacts to what the particles are doing.

It is a single `index.html` file, with no build step and no install.

**Live demo:** `https://abm1971.github.io/particle-life-vr/`

---

## What is Particle Life?

Particle Life is an artificial-life model in the same family as Conway's Game of Life. Both produce complex emergent behavior from simple local rules. Particle Life is continuous, though. Particles move in continuous space, have velocity and friction, and never appear or disappear.

Each particle belongs to a **species**, shown by its color. An **attraction matrix** `a[i][j]` sets how strongly species *i* is attracted to (positive) or repelled by (negative) species *j*. The matrix is usually **asymmetric** (`a[i][j] ≠ a[j][i]`). That breaks Newton's third law, so the system is driven out of equilibrium. Cells, membranes, chasing pairs, filaments and other lifelike structures emerge from it.

### Force law

For two particles at distance `d`, with `r = d / r_max`:

```
F(r) = r/β − 1                              if r < β        (universal short-range repulsion)
F(r) = a[i][j] · (1 − |2r − 1 − β| / (1 − β))   if β ≤ r < 1    (species-dependent)
F(r) = 0                                    if r ≥ 1
```

Velocities are damped by friction with a configurable half-life. Positions live in a unit cube with periodic boundary conditions.

---

## Features

- **Immersive VR:** you are inside a 3 m periodic cube, which you can resize from 0.5 to 10 m while in VR. The 26 periodic images are rendered around it, so the particles fill the whole sphere around you (4π).
- **Mixed reality:** particles float in your room using Quest 3 passthrough (`immersive-ar`).
- **Hand interaction:** works with Touch controllers or hand tracking. You can push, attract and drag particles along with the motion of your hand.
- **Interactive spatial music** (Web Audio, HRTF):
  - Each species is a voice in a minor pentatonic scale.
  - A background pad follows the activity (mean speed) of each species, and a filter opens as the whole box gets more active.
  - On every eighth note, nearby particles play their note **from their position in space**.
  - Your hands play an arpeggio when you start pushing or attracting, plus a filtered whoosh that follows hand speed.
  - A new matrix also changes the key.
- **Editable attraction matrix:** with presets (Random, Symmetric, Shells, Filaments, Chase, Mutate).
- **Desktop preview:** works in any modern browser. The camera starts inside the cube and you drag to look around.
- **Smooth rendering:** physics runs in a Web Worker, so the render loop keeps its frame rate. The central box is drawn as real instanced spheres, and the periodic images as cheap point-sprite impostors.

---

## Controls inside the headset

| Action | Input |
|---|---|
| Push particles (drag them as you move) | **Trigger** or **pinch** (analog: harder = stronger) |
| Attract particles to your hand | **Grip** |
| Grow / shrink the cube | **Thumbstick up / down** |
| Mute / unmute music | **Thumbstick click** |
| New random matrix + key change | **A / X** |
| Respawn particles | **B / Y** |

The matrix, physics, music and hand settings are adjusted on the page panel before you enter VR.

---

## Running it

WebXR requires a **secure context (HTTPS)**, so the file must be served over HTTPS. Opening it from disk is not enough.

### GitHub Pages (recommended)

1. Put `index.html` at the root of a public repository.
2. Go to **Settings → Pages → Build and deployment**. Set *Source* to **Deploy from a branch**, then choose `main` and `/ (root)`.
3. After a minute or two the site is live at `https://<your-user>.github.io/<repo>/`.
4. Open that URL in the **Meta Quest Browser** and press **Enter VR** (or **Mixed reality**).

### Locally (desktop preview only)

Any static server works, for example:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

An internet connection is needed the first time, because three.js and the controller and hand models are loaded from a CDN.

---

## Performance tips

- The default is **3,000 particles**. Lower it if the headset stutters, or raise it (up to 8,000) if it runs smoothly.
- Turning off **periodic copies** gives the biggest rendering saving.
- Physics cost grows with particle count and interaction radius. The HUD shows the milliseconds per physics step.

---

## Tech

- [three.js](https://threejs.org/) 0.160.0, loaded through an import map from jsDelivr, with the WebXR controller and hand models from `three/addons`.
- Custom GLSL shaders for instanced spheres and point impostors, with depth fog.
- Spatial hashing (counting-sort grid) for O(N) neighbor search in a periodic box.
- Web Audio API: oscillators, a convolution reverb with a generated impulse response, and HRTF `PannerNode`s tied to the XR camera pose.

---

## Credits

- Particle Life idea: Jeffrey Ventrella's *Clusters*.
- Inspired by Tom Mohr's [particle-life-app](https://github.com/tom-mohr/particle-life-app).
- Built on [three.js](https://threejs.org/) and the [WebXR Device API](https://www.w3.org/TR/webxr/).

## License

Add the license of your choice (for example MIT) in a `LICENSE` file.
