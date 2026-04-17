Ink On Paper allows geek to virtualy print, sign, and scan a PDF document "the traditional way".

# The minimalist tool

A minimalist Bash script automate ImageMagick for a paperless signing. This tool allows you to "traditionally" sign PDF documents by overlaying a signature image onto a rasterized PDF page, ensuring the result matches a professional scan.

## The Workflow

The tool operates in three distinct phases to ensure pixel-perfect accuracy.



1.  **Preview:** Convert a PDF page to a high-res PNG "Canvas."
2.  **Prepare:** Clean your signature (remove background color and resize).
3.  **Sign:** affix the signature to the document and export a flattened, professionally scanned PDF.

---

## Prerequisites

This script relies on the **ImageMagick** and **Ghostscript**.

- **Linux (Ubuntu/Debian):** `sudo apt-get install imagemagick ghostscript`
- **macOS:** `brew install imagemagick ghostscript`
- **Windows:** Msys2 or cygwin are your friend. Ensure that both imagemagick and ghostscript are installed and you have a Bash environment. *Note: You may need to edit your ImageMagick `delegates.xml` to change all instances of "gswin32c" to "gswin64c".*

---

## Usage

### Step 1: Create a Canvas (Preview)
Render the PDF page into a 300 DPI image. Open this image in any viewer (like Paint, GIMP, or Photoshop) to find the exact pixel coordinates (start position and size) for your signature. You may also want to date it or make other changes before signing.

```bash
./sign_pdf.sh preview document.pdf 0
```
*Outputs: `preview_page_0.png`*

### Step 2: Prepare Your Signature
Clean a raw photo of your signature. We assume it is ink on white paper. This step removes "off-white" paper backgrounds.

```bash
./sign_pdf.sh prepare-sig raw_scan.jpg signature_clean.png 50% 15%
```
- **50%**: Resize the signature (optional).
- **15%**: The "fuzz" factor. Increase this if your signature background is shadowy/gray.

### Step 3: Sign and Export
Affix the cleaned signature to the document image and convert it back to PDF.

```bash
./sign_pdf.sh sign preview_page_0.png signature_clean.png final_signed.pdf 1200 2500 jpeg
```
- **1200 / 2500**: The X and Y pixel coordinates as you get from image viewing programs, starting from top left corner of the page.
- **jpeg / zip**: Use `jpeg` for small emailable files; use `zip` for lossless archival quality.

---

## � Technical Details

### Why Rasterize First?
By converting the PDF to a PNG canvas before signing, we ensure:
* **Coordinate Consistency:** The pixel you measure on the preview is exactly where the signature lands.
* **Flattening:** The signature becomes part of the image layer. This makes it significantly harder for third parties to "extract" your signature file compared to standard digital PDF annotations.

### Metadata & Privacy
The script automatically applies a `-strip` command to all outputs. This removes:
* EXIF data (camera info, GPS from your signature photo).
* Invalid PNG color profile chunks (bKGD) that often cause errors in PDF viewers.

### DPI Consistency
The script is hardcoded to **300 DPI**. This is the standard for high-quality office scanning. An A4 or Letter page at this resolution is roughly 2500-3300 pixels tall.

---

## License
This project is licensed under the Apache-2.0 License. Feel free to use it in your personal or professional paperless workflows.
