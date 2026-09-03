# PolarFinder — setup and calibration

An offline web app that tells you which marking on your B+W 46mm KSM HTC-POL MRC nano MASTER
to put at 12 o'clock for maximum polarization, for a camera you can't meter through.

---

## What's in this folder

| File | Purpose |
|---|---|
| `index.html` | The entire app — math, UI, ring graphic |
| `manifest.webmanifest` | Makes it installable to the home screen |
| `sw.js` | Service worker; caches the app so it works with no signal |
| `icon-192.png`, `icon-512.png`, `icon-maskable.png` | Home screen icons |

---

## Part 1 — Get it onto the phone (about 10 minutes, once)

Chrome on Android **refuses compass and GPS access on `file://` URLs**. A downloaded HTML file
opened from Files will load but the sensors will stay dead. It has to be served over HTTPS. GitHub
Pages is the least painful way to do that, and it's free.

1. Go to **github.com** and sign in (or make an account).
2. Click **New repository**. Name it `polarfinder`. Set it to **Public**. Create it.
3. On the empty repo page click **uploading an existing file**.
4. Drag in all six files from this folder. Commit.
5. Go to **Settings → Pages**. Under *Build and deployment*, set **Source: Deploy from a branch**,
   **Branch: main**, **Folder: / (root)**. Save.
6. Wait 1–2 minutes. The URL will be:
   `https://YOURNAME.github.io/polarfinder/`
7. Open that URL in **Chrome on the Pixel**. Tap **Enable sensors** and allow location.
8. Chrome menu (⋮) → **Add to Home screen** → **Install**. It now runs full-screen and offline.

> If you'd rather not use GitHub: any HTTPS static host works — Netlify Drop, Cloudflare Pages,
> Vercel. Drag the folder in, done. What matters is HTTPS.

**Privacy note:** a public repo means the code is public. There's nothing personal in it — your
location, calibration and settings live only in your phone's local storage and are never sent
anywhere. The app makes no network requests at all after loading.

---

## Part 2 — Calibration: one five-minute session, and that's it

Everything now happens at a single puddle or glass tabletop. **No sun, no compass, no GPS, no straight
objects, no flat ground.** Declination is computed by the app and follows you as you travel.

### Step 1 · Where is the filter's transmission axis? *(5 minutes, indoors is fine)*

B+W does not index the transmission axis to the engraving, so on your particular filter it sits at some
arbitrary angle relative to "46" — fixed forever once you measure it.

**The reference:** light reflecting off a horizontal, non-metallic surface is polarized **horizontally**.
That is Fresnel's law, and it needs no sun and no assumptions. So the filter position that kills such a
reflection is, by definition, the one with its transmission axis **vertical**.

1. Find a horizontal shiny non-metal surface with a reflection in it — a glass tabletop, polished wood,
   a puddle, a car's paint, a phone screen face-up under a lamp.
2. Look down at the reflection at a shallowish angle, roughly 30–40° below horizontal. **Keep your head
   upright** — a tilted head tilts your idea of "vertical", and that error goes straight into the result.
3. Hold the filter up to your eye **the right way round**: engraved face toward the reflection, back face
   toward your eye. A circular polarizer only analyses light in that direction.
4. Rotate slowly. The reflection darkens dramatically at one point and is brightest 90° away. Park it at
   the **darkest** point.
5. Read which marking is at the **top**. On the Calibrate tab, drag the dial to match, then tap **Save**.

*Sanity check — do not skip this.* Rotate 90° from your saved position: the reflection should come
roaring back, obviously and unmistakably. If the swing between darkest and brightest is subtle, the
measurement is mushy and so is everything built on it. Two common causes:

- **The angle is wrong.** You want to be looking down at roughly **30–40° below horizontal**, which
  puts you near Brewster's angle (~56° from the surface normal) where the polarization is strongest.
  Steeper or shallower and the effect fades.
- **The surface is too matte.** Unfinished or satin wood scatters diffusely and polarizes weakly.
  Varnished or waxed wood, glass, or a puddle work far better. A dark surface beats a light one,
  because you are looking for the reflection rather than the wood.

If nothing changes no matter how you turn it, the filter is mounted backwards.

### Step 2 · Handedness *(indoors, no sun, any hour)*

There are two mirror-image ways the ring could map onto the world, and Step 1 cannot tell them apart —
a horizontal reflector polarizes light horizontally *in the world*, from every viewing direction, so its
geometry is symmetric. (Two earlier drafts of this step were wrong: tilting your head does nothing, and
the version after it showed only one of the two equivalent answers.)

**What works: turning a screen.** Every LCD and OLED emits linearly polarized light, and the polarizer
is bonded to the panel — so the polarization *turns with the device*.

1. Plain white full-screen on a second device — tablet, spare phone, laptop. Dim the room.
2. Hold it upright facing you, head level. Rotate the filter to the **darkest** point, set the dial to
   the marking at the top, tap **Capture 1**.
3. Turn the screen so its **top edge moves toward your right**, by roughly 45° — about halfway to
   portrait. Keep the face pointed at you.
4. Re-find the darkest point, set the dial, tap **Capture 2**. Set the turn-direction menu to match
   what you actually did, then tap **Apply**.

**Which way you turned it is the whole test** — get that backwards and the verdict inverts. The turn
*amount* barely matters: anything from 20° to 70° works, and the app reports the measured rotation so
you can check it against what you did.

The maths: if the screen's polarization sits at angle α in your view, the filter must be perpendicular,
so ψ₀ = ψ_T + α + 90. Turning the screen by δ sends α → α + δ, so ψ₀ → ψ₀ + δ. Both α and ψ_T cancel
when you subtract the two readings — **only the sign of the change matters**.

> **Positions 180° apart are identical.** The transmission axis is a line, not an arrow, so rotating the
> filter half a turn gives exactly the same optical setting. You will often find the darkest point with
> the "other half" of the ring at the top. Enter whichever twin you actually see — the app reduces both
> readings modulo 180° and gets the same answer either way.

**What "darkest" looks like on a screen.** Expect a deep blue-grey at the minimum and a dim white at the
maximum, not a true black. Screens carry retarder and compensation films whose effect varies with
wavelength, so extinction is never complete and the residue is coloured. This is normal and does not
affect the angle — the minimum is still in the right place. If the swing is so weak you cannot locate
the minimum, try an LCD laptop screen instead of an OLED tablet.

### Declination · nothing to do, ever

The app carries the **World Magnetic Model 2025**, the NOAA/BGS model used in aircraft and marine
navigation. It computes the magnetic-to-true-north correction for wherever you are standing, to about
0.4°, and recomputes it continuously as you move. The Calibrate tab shows the current value.

**This replaced the old shadow sight, and it is strictly more accurate:**

| | Old shadow sight | WMM2025 |
|---|---|---|
| Typical error | 3–8° with a pen, 1–2° with a tall pole | **~0.4°** |
| Setup needed | sun 15–35° up, flat ground, straight object | none |
| On travelling 100 miles | redo it | nothing |
| Works indoors / at night | no | yes |

The model is valid through December 2029. After that it needs its coefficients refreshed — a one-line
change in `index.html` when WMM2030 is published.

### Optional · North reference check *(one time, ever — not per location)*

One genuine uncertainty remains. Chrome on Android reports headings from Android's rotation-vector
sensor, which the platform documents as referenced to **magnetic** north — so the app applies the
declination above. But some Android layers hand back true north already, in which case it should not.
This is a property of your phone and browser, **not your location**, so checking it once settles it
permanently.

The check uses **GPS course over ground**, which comes from satellites and knows nothing about magnetism:

1. Get outside with a clear sky view and a straight run of at least 50 m — a path, a pavement, an empty
   car park.
2. Hold the phone in front of you, screen toward you, back facing the way you will walk.
3. Tap **Start walk**, walk straight for about 30 seconds, tap **Stop**.

The app compares the two and tells you which reference your phone uses. The two hypotheses differ by a
full declination — 12.5° in New York, 15° in Seattle — comfortably larger than the 2–4° noise of a GPS
bearing over 50 m, so the verdict is clear-cut. If your local declination happens to be under 3° the app
will say *inconclusive*, which also means it barely matters.

Until you run this, the app assumes magnetic north, which is almost certainly correct.

### Optional · Camera interference check

Camera iron produces a heading error that swings through zero as you turn, so it cannot be calibrated
away with a single number — it has to be avoided by keeping the M away from the phone while the reading
is taken. This tool measures how much yours actually matters: aim at a distant landmark, capture with the
camera away, capture again with it in your shooting position, read the difference. Repeat facing three or
four directions. Under about 3° everywhere and you can ignore it entirely.


## Part 3 — Using it

1. **Camera at arm's length or hanging on the strap.** Point the phone's **back** the same way the
   lens points, screen facing you.
2. Set **Frame** to match how you're holding the camera — landscape or portrait.
3. Tap **Hold reading**. The ring freezes.
4. *Now* bring the camera up. Turn the filter until the marking shown is at the top.
5. Tap **Live again**.

Step 1 and step 4 are deliberately separated: the phone never needs to be near the camera at the
moment it takes the reading. If Step 2b showed under ~3° of interference you can ignore this and
hold everything however you like.

The **polarization meter** tells you whether it's even worth fitting the filter. Below about 25%
you're paying 1⅔ stops for very little. Above 55% the effect will be obvious.

Two things worth internalising so you can often skip the phone entirely:

- **The 90° rule.** Make an L with your thumb and index finger, point the thumb at the sun, and
  your index finger sweeps the band of sky where polarization is strongest.
- **The sun-pointing rule.** The filter's transmission axis should point at where the sun sits in
  your frame — including off the edge of it. Once you know where your filter's axis is (Step 1),
  you can eyeball this.

---

## Accuracy, honestly

| Ring error | Fraction of the ideal effect you still get |
|---:|---:|
| 5° | 99% |
| 10° | 97% |
| 15° | 93% |
| 20° | 88% |
| 30° | 75% |

Transmission goes as cos² of the misalignment, so this is a forgiving instrument. The error budget
now looks like this:

| Source | Typical | Notes |
|---|---:|---|
| Solar position | 0.01° | NOAA algorithm, verified against reference values |
| Declination (WMM2025) | 0.4° | NOAA's published uncertainty for D |
| Filter axis, Step 1 | 2–5° | your judgement of "darkest" and of level |
| Phone magnetometer | 3–8° | the dominant term, and irreducible |
| Camera interference | 0–15° | avoidable: keep the M away while reading |

Total realistically 5–10°, which lands in the 97% row. The magnetometer is now the limiting factor —
worth knowing, because it means further calibration effort buys you nothing.

**Where it doesn't apply:**

- **Wide lenses.** The correct angle varies across the frame. On your 35mm it differs by a few
  degrees corner to corner; on a 21mm it's much worse, and a big sky will show the polarization
  band as a visibly uneven dark patch. The app solves for the centre of the frame.
- **Water and glass reflections.** These are governed by the surface's orientation, not the sun.
  For a horizontal surface the axis wants to be **vertical in the frame** — which is exactly your
  Step 1 position, no phone needed.
- **Rainbows, blue-hour skies, heavy haze.** The Rayleigh model stops describing them well.

---

## Troubleshooting

**The ring angle flips between two values that are far apart** — fixed in v5, and worth understanding
because it was the nastiest bug in this app. Chrome on Android fires *two* orientation events:
`deviceorientationabsolute`, which is referenced to magnetic north, and `deviceorientation`, which on
Android is the **game rotation vector** — a relative orientation referenced to wherever the phone
happened to be when the sensor started. Earlier versions listened to both and fed them into one
handler, so the heading alternated between a real bearing and a meaningless one separated by an
arbitrary constant. The symptom was distinctive: the answer snapped between two clusters with **no
values in between**, each cluster individually rock-steady. The app now ignores relative events
entirely once an absolute one has arrived.

**Pill says "relative — not usable"** — no absolute orientation event ever arrived, so there is no
compass at all. Reload the page. If it persists, use **Aim source → Type it in**.

**Steadiness pill is amber or red** — the answer is moving. Under ±4° it is fine. Above ±12° the app
refuses to vouch for it, and tells you which of the two causes applies:

- *Magnetic disturbance* — the usual one on a rooftop. Steel decking, railings, ducting, lift
  machinery and HVAC all bend the local field. Walk several metres clear and watch the pill settle.
- *Geometric amplification* — you are pointed nearly at or away from the sun, where the sun's
  projection into the frame is tiny and its direction is ill-defined. The **aim error amplification**
  readout shows this: 1.0× at γ = 90°, but 2.9× at γ = 20° and 5.7× at γ = 10°. There is also very
  little polarization to remove at those angles, so the honest answer is usually to leave the filter off.

**A reading that looks stable but is wrong** — check the steadiness pill *and* the polarization
percentage before trusting any answer. A confident-looking number at 15% polarization is not worth
acting on.

**Sky doesn't darken at the position the app gives** — do Step 2, the handedness test. If Step 2 is
done and it is still wrong, redo Step 1 with your head deliberately level, then check that the app's
declination reading looks sane for where you are.

**Readings jump around near the camera** — that's the M's brass and steel. Use **Hold reading**: aim,
freeze, then bring the camera up.

**Filter does nothing at all in any position** — it's mounted backwards.

## The physics, in one paragraph

Skylight is blue because air molecules scatter sunlight (Rayleigh scattering), and it's polarized
because a scattering event emits light with its electric field **perpendicular to the scattering
plane** — the plane through the sun, the scatterer and you. The same is true of the specular sheen
on leaves and paint, for the same single-bounce reason. To block that field, the polarizer's
transmission axis must be perpendicular to it, which means it must lie **in** the scattering plane
— i.e. it must point at the sun's projected position in your frame. That single rule is what the
app computes. How much there is to remove follows
**P = P_max·sin²γ / (1 + cos²γ)**, with γ the sun-to-subject angle: zero looking at or away from
the sun, peaking at γ = 90°. Real skies reach P_max ≈ 0.75 rather than 1.0 because of multiple
scattering, aerosols and ground bounce.

### Sources

- [Rayleigh sky model — polarization perpendicular to the scattering plane, DoP = sin²γ/(1+cos²γ)](https://handwiki.org/wiki/Physics:Rayleigh_sky_model)
- [Daytime sky polarization: measured limits on real-sky maximum degree of polarization (arXiv 1612.00538)](https://arxiv.org/pdf/1612.00538)
- [Polarization by scattering — Harvard Natural Sciences Lecture Demonstrations](https://sciencedemonstrations.fas.harvard.edu/presentations/polarization-scattering)
- [Brewster's angle — reflected light from dielectric surfaces is s-polarized](https://en.wikipedia.org/wiki/Brewster%27s_angle)
- [Brewster's angle overview — ScienceDirect](https://www.sciencedirect.com/topics/earth-and-planetary-sciences/brewster-angle)
- [MDN: `deviceorientationabsolute` — the event Chrome on Android needs for a true compass](https://developer.mozilla.org/en-US/docs/Web/API/Window/deviceorientationabsolute_event)
- [MDN: DeviceOrientationEvent and the alpha/beta/gamma rotation convention](https://developer.mozilla.org/en-US/docs/Web/API/DeviceOrientationEvent)
- [W3C deviceorientation issue #137 — why absolute compass headings are awkward on the web](https://github.com/w3c/deviceorientation/issues/137)

- [World Magnetic Model 2025 — NOAA NCEI / British Geological Survey](https://www.ncei.noaa.gov/products/world-magnetic-model)
- [WMM2025 coefficient files (the `WMM.COF` values embedded in this app)](https://www.ncei.noaa.gov/products/world-magnetic-model/wmm-coefficients)
- [WMM2025 model description and validity period — CIRES/CU Boulder](https://geomag.colorado.edu/wmm2025-and-wmmhr2025)
- [Android position sensors — the rotation vector is referenced to magnetic north](https://developer.android.com/develop/sensors-and-location/sensors/sensors_position)

Solar position uses the NOAA low-precision algorithm (±0.01°), verified against reference values for
the solstices and equinox at several latitudes.

Declination uses the official WMM2025 coefficients (epoch 2025.0, released 2024-11-13, valid to
2029-12-31), with the NOAA reference algorithm ported to JavaScript. The port was validated against an
independent implementation at 4,000 random points spanning all latitudes, longitudes, altitudes to
8 km and dates across the model's life span: **maximum disagreement 1.1 × 10⁻¹³ degrees**.

Citation: NOAA NCEI Geomagnetic Modeling Team and British Geological Survey, 2024. *World Magnetic
Model 2025.* NOAA National Centers for Environmental Information. https://doi.org/10.25921/aqfd-sd83
