# Reproducing the WebP-to-ICO Alpha Measurement

## Source file

A 256×256 RGBA image: red rounded rectangle (192×192, corner radius 48) with a yellow circle (radius 60) at center. The exterior is fully transparent (alpha = 0). Saved as WebP at quality 90.

File size: 2,556 bytes.

Download: https://icoforge.com/samples/webp-to-ico/source-transparent.webp

## Method

1. Load the WebP into an HTML canvas.
2. For each target size (16, 32, 48, 64, 128):
   - Create a canvas at the target size.
   - Draw the image with `drawImage()` and `imageSmoothingQuality = 'high'`.
   - Read back pixel data with `getImageData()`.
   - Count transparent (alpha = 0), opaque (alpha = 255), and partially transparent (0 < alpha < 255) pixels.
   - Record the RGBA values at all four corners.
   - Encode the canvas as PNG, then wrap it in an ICO container (6-byte header + 16-byte directory entry + PNG payload).

3. The ICO bytes in the CSV include the header and directory entry. The PNG payload bytes are the embedded image only.

## Verification

Open any of the output ICO files in a hex editor or icon viewer. Confirm:
- Bytes 0–1: `00 00` (reserved)
- Bytes 2–3: `01 00` (type = icon)
- Bytes 4–5: `01 00` (1 image)
- Byte 6: size (0 for 256)
- Byte 8: 0 (no palette)
- Bytes 10–11: `01 00` (1 plane)
- Bytes 12–13: `20 00` (32 bpp)

All measurement files are at:
https://icoforge.com/samples/webp-to-ico/

## Key finding

At every size from 16 to 128, all four corner pixels have alpha = 0. The transparent exterior of the WebP is preserved in the ICO output. No white fill, no flatten.
