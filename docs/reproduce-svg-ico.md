# Reproducing the SVG-to-ICO Rasterization Measurement

## Source file

A 256×256 viewBox SVG gear icon: 2 concentric circles (stroke-width 8) + 8 radial spokes + center dot. stroke-width = 8, viewBox = "0 0 256 256".

## Method

1. Load the SVG into an HTML canvas via `drawImage()`.
2. For each target size (16, 32, 48):
   - Create a canvas at the target size.
   - Draw the SVG with `imageSmoothingEnabled = true`, `imageSmoothingQuality = 'high'`.
   - Read back pixel data with `getImageData()`.
   - Count painted pixels (alpha > 0) and fully transparent pixels.
   - Calculate ink coverage = painted / total.

## What the numbers mean

At 16×16, the gear has 256 pixels total. The rasterizer paints 116 of them (alpha > 0), giving 14.7% ink coverage. None are fully opaque — every painted pixel is partially transparent because the vector strokes are anti-aliased.

This is why small icons look "soft" compared to their larger counterparts: at 16 px, each pixel samples the vector path once, and anti-aliasing spreads the stroke across multiple partial-coverage pixels.

## Source

The measurement was generated at https://icoforge.com/svg-to-ico using Chrome's canvas2d rendering pipeline. The SVG source and ICO output files are available for download on that page.
