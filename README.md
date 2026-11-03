# Brushstroke Filter

Renders a video — a file you pick, or your camera — as a grid of individually
coloured monospace characters. The glyph points along the brushstroke; the
colour is sampled per character.

**Live: https://facedawg-inc.github.io/brushstroke-filter/**

## How it works

Each frame is drawn into a sampling canvas at twice the character grid and read
back once.

- **Direction.** From the frame's luminance it builds a *structure tensor* —
  the gradient's outer product, blurred over a few pixels. Its minor
  eigenvector points along edges, which is the direction a brush would take.
- **The swirl.** That direction is blended with a field of slow vortices,
  weighted by how coherent the local image structure is. Detailed areas follow
  the footage; flat areas fall to the swirl. The *Swirl* slider sets the floor
  on that mix.
- **The stroke.** A short line integral convolution along the blended field
  smears luminance and colour into strokes.
- **The glyph.** Direction picks one of five density ramps, brightness picks
  the step along it, so a stroke can darken through a dozen characters without
  losing the angle it points:

  ```
  h  " .,_-~=+*wm#@"        horizontal
  v  " .:;i!|I()[]1tfj#@"   vertical
  a  " .'/7xX%&#@"          up-right
  b  " .`\xX%&#@"           down-right
  s  " .:+*oO08&#@"         round, for highlights
  ```

- **The colour.** Glyphs are drawn white on black, then one scaled multiply
  pass lays a per-cell colour image over them. Exact 24-bit colour per
  character, and the atlas holds one copy of each glyph rather than one per
  palette entry.

Exposure is auto-levelled against each frame's own mean and spread, so a dim
clip and a bright one both use the full range of glyph weights.

## Camera

The camera button only appears where capture can actually work: it needs a
secure context, so `https://` or `localhost`. A page opened straight off disk
at a `file://` address is not one, and the button stays hidden there rather
than failing on a tap.

Nothing is uploaded, ever. Decoding, sampling and rendering all happen in your
own browser, and the page makes no network requests at all.

## Running it locally

Any static server works. The repo is a single HTML file with no dependencies
and no build step — open `index.html` directly for file playback, or serve it
over `localhost` if you want the camera.

## Controls

| | |
|---|---|
| Swirl | how much the vortex field overrides the footage |
| Colour | saturation pushed before the gamma lift; 0 for monochrome |
| Detail | character grid density |
| Flip | front / rear camera |
| Space | pause |
