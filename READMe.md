Ink On Paper

This "Ink On Paper" allows users to virtually print, sign, and scan a PDF document "the traditional way."

# The minimalist tool

A minimalist Bash script to automate ImageMagick for paperless signing. This tool allows you to "traditionally" sign PDF documents by overlaying a signature image onto a rasterized PDF page, ensuring the result matches a professional scan.

## The Workflow

The tool operates in three distinct phases to ensure pixel-perfect accuracy.

1. **Preview:** Convert a PDF page to a high-resolution PNG "Canvas."
2. **Prepare:** Clean your signature (remove background color and resize).
3. **Sign:** Affix the signature to the document and export a flattened, professionally scanned PDF.

---

## Prerequisites

This script relies on **ImageMagick** and **Ghostscript**. Ensure that they are in your PATH and executable.

- **Linux (Ubuntu/Debian):** `sudo apt-get install imagemagick ghostscript`
- **macOS:** `brew install imagemagick ghostscript`
- **Windows:** MSYS2 or Cygwin are your friend. Ensure that both ImageMagick and Ghostscript are installed and you have a Bash environment. *Note: You may need to edit your ImageMagick `delegates.xml` to change all instances of "gswin32c" to "gswin64c".*

---

## Usage

### Step 1: Create a Canvas (Preview)
Render the PDF page into a 300 DPI image. Open this image in any viewer (such as Paint, GIMP, or Photoshop) to find the exact pixel coordinates, i.e., start position and size, for your signature. You may also want to date it or make other modifications before signing.

```bash
sign_pdf preview document.pdf 0
```
*Outputs: `preview_page_0.png`*

### Step 2: Prepare Your Signature
Clean a raw photo of your signature. This assumes the signature is "ink on white paper". This step removes "off-white" paper backgrounds from photography or scanning.

```bash
sign_pdf prepare-sig raw_scan.jpg signature_clean.png 50% 15%
```
- **50%**: Resize the signature (optional).
- **15%**: The "fuzz" factor. Increase this if the signature background is shadowy or gray.

### Step 3: Sign and Export
Affix the cleaned signature to the document image and convert it back to PDF.

```bash
sign_pdf sign preview_page_0.png signature_clean.png final_signed.pdf 1200 2500 jpeg
```
- **1200 / 2500**: The X and Y pixel coordinates obtained from your image viewer, measured from the top-left corner of the page.
- **jpeg / zip**: Use `jpeg` for smaller file sizes; use `zip` for lossless archival quality.

---

## Technical Details

### Why Rasterize First?
By converting the PDF to a PNG canvas before signing, we ensure:
* **Coordinate Consistency:** The pixel measured on the preview is exactly where the signature is affixed.
* **Flattening:** The signature becomes part of the image layer. This makes it significantly harder for third parties to extract your signature file compared to standard digital PDF annotations.

### Metadata & Privacy
The script automatically applies a `-strip` command to all outputs. This removes:
* EXIF data (camera information and GPS coordinates from your signature photo).
* Invalid PNG color profile chunks (bKGD) that often cause errors in PDF viewers.

### DPI Consistency
The script is hardcoded to **300 DPI**. This is the standard for high-quality office scanning. An A4 or Letter page at this resolution is approximately 2500–3300 pixels tall.

---

## License
This project is licensed under the Apache-2.0 License. Feel free to use it in your personal or professional paperless workflows.
