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

## Magnetic environment

The app's weakest input is the phone's compass, and the thing that ruins a compass is nearby iron.
A **fixed** lump of iron near the phone does not shift every heading equally — it adds a constant
vector to the Earth's field, so the heading error is *sinusoidal in heading*: zero in two directions,
worst 90° from those. That is why a site can be perfect facing one way and badly wrong facing another.

The app now measures this directly. Field **magnitude** and **dip angle** are independent of which way
the phone is pointing, so they can be checked against the World Magnetic Model without trusting the
heading at all. A clean site matches the model; ironwork shifts one or both.

What it reports, on the Aim tab:

- the measured field and dip against the model values
- the size of the anomaly as a share of the total field
- an **estimated heading error** in degrees
- most usefully, **the share of maximum darkening you are still getting** — because a big anomaly at a
  forgiving geometry can be harmless, while a modest one where the amplification is high is not

The severity wording is based on that last number, not on microtesla, since that is what actually
affects the photograph.

> **On the estimate.** Magnitude and dip reveal the anomaly components along the field and in the
> vertical plane. The horizontal-perpendicular component is what corrupts a heading, and it is not
> directly observable. For a roughly isotropic anomaly its expected size is √((a∥² + a_dip²)/2), which
> is what the app uses. A 20,000-sample Monte Carlo puts the median of estimated/true at **1.14** —
> essentially unbiased — but only **52% of cases land within a factor of two**, with a 10th–90th
> percentile range of 0.34× to 4.9×. It is an order-of-magnitude indicator, not a measurement. The
> optical cross-check is the number to trust.

### If your phone has no magnetometer API

Chrome does not expose raw magnetometer readings unless `chrome://flags/#enable-generic-sensor-extra-classes`
is switched on, so on most phones the field-strength comparison above is unavailable and the app says
so. It falls back to a method that needs no special API at all.

The phone publishes **two independent orientations**:

| Event | Sensors behind it | Magnetically |
|---|---|---|
| `deviceorientationabsolute` | gyro + accelerometer + **magnetometer** | vulnerable |
| `deviceorientation` | gyro + accelerometer only (game rotation vector) | **immune** |

In a clean field the difference between the two headings is a constant. Where iron distorts the
field, that difference **changes as you turn or walk** — and the size of that change is the heading
error you are actually suffering. Sweep the phone through 30–40°, or walk a few paces, and the app
reports it.

The noise floor is the gyroscope's own drift, roughly 2–3° over the 30-second window, so anything
under about 5° reads as clean.

> **Blind spot, stated plainly.** A distortion that is *identical in every direction over the whole
> area* moves both headings together and stays invisible to this test. That is exactly the case the
> optical site correction below exists to handle — and it is the likeliest case on a steel-framed
> roof.

### Why the magnetometer cannot simply correct itself

A natural question: if the app can measure the disturbance, why not just subtract it?

Because it cannot measure the part that matters. Without trusting the compass, only two quantities
are observable — field **magnitude** and **dip**. The anomaly has **three** components, and the one
that rotates your heading (horizontal, perpendicular to the magnetic meridian) is invisible to both.
Two equations, three unknowns. Spinning the phone does not help either: at a fixed spot the disturbed
field is a single fixed vector, so rotating reveals nothing new about it.

That is an information limit, not a missing feature. Magnitude and dip can tell you a site is bad.
They cannot tell you which way it is lying.

### Site correction — using the sky as an independent compass

What breaks the deadlock is a heading reference that does not involve magnetism, and you are carrying
one: the filter.

The sun's position is known exactly. Elevation comes from the accelerometer, which magnetism cannot
touch. Given both, the polarization angle you can *see* through the filter determines the azimuth you
are facing — the inverse of what the app normally computes. Viking sunstones and modern polarimetric
navigation work on this principle.

So: find the darkest position by eye, enter it, and the app solves backwards for your true heading and
compares it with what the compass claimed.

**And one fix corrects every direction from that spot.** Building steel disturbs the field at a
location, so the resulting heading error there is a constant. It does not look constant — the *ring*
error varies hugely with direction, because the amplification does (it ranges from about 0.05× to 3×
across headings for a given sun). That is why one direction can look perfect while another is 21° out,
from a single unchanging cause.

Verified end to end: with a −28.5° site error simulated, ring errors of 25°, 19°, 13°, 4° and 11°
across five headings all dropped to **0.00°** after a single fix taken in one direction.

**Take the fix facing a high-amplification direction.** The app shows the figure live, and rates the
fix *sharp*, *soft*, or *too vague to use* — it refuses to store one below 0.2×, where the sky simply
cannot pin down your heading.

The correction is anchored to where it was taken and applies within **60 m**, then switches itself
off — rooftop anomalies change over a few metres. It only applies when Aim source is the phone
compass.

### What to do about a bad site

Walk a few metres and watch the readings settle — the field from a lump of iron falls off as 1/r³, so
distance is extremely effective. Rooftops are among the worst environments there are: steel decking,
parapet railings, HVAC plant, lift machinery, and reinforcement in the slab beneath you. If the site
cannot be improved, use **Aim source → Type it in** and give the app a bearing you trust.

Keep the loss in perspective. Transmission goes as cos² of the ring error, so even a 20° error still
delivers 88% of the ideal darkening — visible if you look for it, rarely fatal to the photograph.

---

## Staleness — why a reading can go wrong after a journey

If you take a reading, pocket the phone, travel, and look again without reopening the app, the answer
can be badly wrong. The cause is not what it looks like.

**It is not the stale GPS fix.** Position barely matters over city distances:

| Distance from the true position | Ring error it causes |
|---|---:|
| 1 km | 0.01° |
| 15 km (across a city) | 0.10° |
| 40 km | 0.27° |
| 150 km | 0.96° |
| 1000 km | 7.6° |

**It is the heading.** When the page is backgrounded, Chrome freezes it: orientation events stop
arriving and the geolocation watch goes quiet. On resume the app could still be holding the heading it
measured *before* the journey — and 1° of heading error is worth up to 3° of ring, so a heading from
the other side of town is worth nothing at all. Reopening the app silently cured it because that
restarted the sensor session.

Four fixes, all verified:

1. **Every orientation sample is timestamped.** If the compass has not spoken for 2 seconds the app
   says **"Compass has stopped updating"**, states the age of the last reading, and refuses to present
   it as an answer.
2. **Resume rebuilds everything.** On `visibilitychange`, `pageshow` and `focus`, the app detaches and
   re-attaches the orientation listeners (forcing a fresh sensor session), discards every smoothing
   buffer that could hold pre-journey samples, clears the old geolocation watch and requests a new
   high-accuracy fix.
3. **Hold reading cannot survive a journey.** The freeze is released automatically on resume, so a
   reading taken somewhere else can never masquerade as a live one.
4. **Site correction fails safe.** It now requires a position fix known to be current (under 3
   minutes). A stale fix would otherwise keep a correction alive after you had left the site it was
   measured at — and *that* error is large. The position is also no longer saved between sessions, so
   a fresh launch can never start with an old location.

---

## Independent verification

Every quantitative claim was re-derived from scratch and checked against implementations that share
no code with the app.

| What | Checked against | Result |
|---|---|---|
| Solar position | **pvlib** (NREL SPA reference), 152 sun-up cases worldwide | max error **0.013°** |
| Transmission-axis angle | Rayleigh **Stokes/Mueller** model, polarizer angle found by brute-force intensity minimisation, 400 geometries | max **0.005°** |
| Scattering angle γ | same | max 5×10⁻¹⁴° |
| DoP law sin²γ/(1+cos²γ) | same | max 6×10⁻¹⁶ |
| **End-to-end instruction** | virtual filter, front-view ring geometry, 150 geometries | **100.0000%** of ideal darkening |
| Orientation matrix | independent Rz·Rx·Ry composition, 200 orientations | 1.7×10⁻¹⁶ |
| Aim amplification | exact derivative (0.01° step) | p95 error 0.0002 |
| Brightest:darkest ratio | Stokes I_max/I_min | exact |
| cos² retention law | Stokes | exact |
| Declination | **pygeomag** (independent WMM implementation), 4,000 global points | 1.1×10⁻¹³° |
| Declination sign | standard convention, three known locations | correct |

The end-to-end test is the one that matters: it simulates the physical filter, applies the app's
instruction, and confirms the result is the true intensity minimum. A control run with the handedness
deliberately flipped achieves only 54.6%, so the test genuinely discriminates.

**Re-verified after the staleness changes:** solar 0.0132°, orientation matrix 1.7×10⁻¹⁶,
end-to-end instruction 100.0000% of ideal darkening, axis angle vs Stokes 0.005°, sky-compass
inversion 0.0000° worst error among unflagged sharp fixes. Fifteen staleness and fail-safe behaviours
tested separately, all passing.

**Faults found and fixed by these audits:**

1. **Sky-compass inversion could pick the wrong branch.** θ is not one-to-one in azimuth — several
   headings can produce the same polarization angle. The code took the global minimum residual, which
   could land on a distant branch: worst observed error **107.9°**, silently. It now finds every root,
   takes the one nearest the compass heading, and flags ambiguity. Worst error among unflagged sharp
   fixes is now **0.000°**. The threshold for saving a site correction was raised from 0.2× to 0.5×
   amplification, because soft fixes could still be 28° out.
2. **A mislabelled row.** "Max darkening of polarized part" was actually the brightest:darkest
   transmission ratio. The number was right; the label was not. Renamed.

**Assumption, not a measurement:** the sky's peak degree of polarization is taken as P_max = 0.75.
Real skies run roughly 0.7–0.8 in clean air and lower in haze. This affects the strength meter and the
ratio only — **not the angle the app tells you to set**.

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
