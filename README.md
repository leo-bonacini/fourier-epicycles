<img src="assets/logo.svg" width="56" height="56" alt="Fourier Epicycles logo" align="left">

# Fourier Epicycles

<br clear="left">

Fourier Epicycles is a single-file, static HTML tool that turns a drawing into a sum of rotating circles (the discrete Fourier series of its outline). It has no backend, no build step and no API keys: everything runs client-side in the browser, and images you open never leave your machine. The interface is available in English, Brazilian Portuguese and Spanish (Spain).

## Features

- **Freehand drawing**: click and drag on the canvas; when you release, the stroke is closed and the animation starts
- **Outline from an image**: open or drop a PNG, JPG or SVG; the outline is extracted automatically, with an adjustable threshold and an invert option
- **Built-in examples**: heart, star, trefoil and the letter π
- **Number of terms**: 1 to 300 circles, with the share of the outline's energy they cover
- **Coefficient table**: the 8 largest coefficients (frequency, magnitude and phase)
- **Display options**: speed, circles and original outline can each be toggled
- **Three languages**: English (US), Portuguese (Brazil) and Spanish (Spain), English by default, switchable from the language menu in the top bar and remembered afterwards

## Usage

1. Draw on the canvas, click **Open image** (or drop a file onto the canvas), or pick something from **Examples**.
2. For images, check the preview in the **Image** panel. If the outline looks wrong, move the **Threshold** slider or tick **Invert colors**.
3. Move **Terms** to watch the drawing gain detail: a few circles give a rounded shape, more circles get close to the original.
4. **Pause** shows the full curve for the current number of terms, **Restart** resets the animation and **Clear** removes everything.

## How it works

1. The outline is a list of (x, y) points, each treated as a complex number `z = x + iy`.
2. The list is resampled to 512 points evenly spaced along its length, so parts drawn slowly don't weigh more than the rest.
3. The discrete Fourier transform gives one coefficient per frequency:

   ```
   c_n = (1/N) · Σ z_k · e^(−2πi·n·k/N)
   ```

4. Each `c_n` is a circle: its magnitude is the radius, its argument is the starting angle, and `n` is how many turns it makes per cycle (negative values spin the other way).
5. The circles are sorted from largest to smallest and added tip to tail. The tip of the last one draws the curve:

   ```
   z(t) = Σ c_n · e^(2πi·n·t),  t from 0 to 1
   ```

The energy percentage is the sum of `|c_n|²` over the terms in use divided by the sum over all terms.

For images, the file is downscaled, converted to grayscale and split into shape and background (the initial threshold comes from Otsu's method). The largest connected region is kept and its border is walked pixel by pixel (Moore neighbor tracing). That border becomes the point list in step 1.

## Notes and limitations

- Only the outer border is used: an "O" becomes a filled disc, without the hole.
- If an image has several separate shapes, only the largest one is used.
- The DFT is the direct O(N²) version. With 512 points it takes a few milliseconds, so there is no FFT.
- Nothing is saved: reloading the page clears the drawing.
- The visitor counter in the status bar uses [abacus.jasoncameron.dev](https://abacus.jasoncameron.dev), a public service with no key and no SLA. If it doesn't respond, the counter shows "n/a".

## Stack

Vanilla JavaScript and Canvas. No frameworks, no build step, no dependencies.

## License

[MIT](LICENSE)
