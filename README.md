## PURPOSE

Make text in PDFs easier to read when printed by deepening the dark colors used
for text. You can draw an analogy between this script and browser extensions
that automatically convert web sites to dark mode such as darkreader.

## INSTALLING PREREQUISITES

For command-line programs: pdftoppm, img2pdf, and convert.

    sudo apt install poppler-utils img2pdf imagemagick

## HOW IT WORKS

TLDR: contrast-stretching on rasterized page images.

1. Rasterization: Convert each page of the input PDF into a high-quality image.

2. Contrast-stretching of each page's image: Simple 'convert -normalize'.
   This is a simple technique to boost contrast in images. No AI or anything
   fancy. Yet.

3. Combining page images into a PDF: img2pdf to losslessly make a PDF.

## REASON FOR EXISTENCE

I've started coming across more and more PDFs that have poor readability when
*printed on paper* (both color inkjet and monochrome laser). I prefer to print
PDFs vs reading on the screen.

This script fixes PDFs that have low contrast text: this will be most apparent
as hard-to-read text on paper, when such PDFs are printed. May be apparent on
low/medium quality displays, displays with bad gamma settings, as well as
low-color, low-density displays such as ebook readers.

## TECHNICAL REASON FOR EXISTENCE

TLDR: displays display fundamentally differently than printers print.

This has to do with the underlying technical differences between how
non-LCD/CRT displays. TLDR: an LCD/CRT can set the *intensity* of every single
sub-pixel (RGB) of every single pixel, in addition to whether subpixel is on or
not.

But other output devices, especially printers (inkjet and laser) *CANNOT* set
the intensity of every subpixel. They *may* be able vary the *quantity* of ink,
but *NOT* the color of the ink itself.

Printers try to do their best by mixing colors to achieve the closest match.
More formally, the color gamut of printers is much smaller than the color gamut
of most monitors (circa 2022). The effect will continue to get *WORSE* as
displays improve -- OLED, HDR etc. Printers are fundamentally limited because
they use fixed-color ink/toner. That is why photo printers use many more
fixed-colored inks (home printers use three colors + black, printers that seven
colored inks exist. See the the Canon product page).

**Need to recheck:** IIUC inkjet printers can vary the amount of a ink laid down
at pixel.  I think laser printers cannot even vary the *amount* of toner at a
pixel per color -- its a binary decision) laid down at a pixel.


## CAVEATS

* Output PDFs can be large: Approx 1 MB per page.

  The input PDF's pages are rasterized at high dpi (300 dpi) and high quality,
  which makes them quite large (approx 1MB per page).

  The output files are intended to be printed, not archived. The output PDFs
  *are* intended to be high-resolution, meaning "jagginess"/"blockiness" aka
  aliasing virtually indistinguishable from the input PDF.

  Since the output PDF is essentially *images* of each page in the likely
  vector-format input PDF, to compensate, each output page is rasterized at
  relatively high pixel density (300 dpi) to match the practical densities of
  most home printers.

  450 dpi is when it actually gets to be indistinguishable to my under a
  magnifying glass on my printers, so I left it at 300 dpi for now, because it
  seems to be a more "round" number wrt printer specs.

* High temporary disk usage: About 16 MB per page.

  I opted to use uncompressed ppm files, except in the last stage which uses
  JPEG, to reduce CPU usage in encoding/decoding of intermediate files. Fix:
  replace 'pdftoppm' with 'pdftocairo -png'

  On my computer, this script appears CPU-bound, and I have a relatively large
  disk, so I opted to reduce CPU usage rather than I/O. The impact of I/O on
  the script's total runtime is not noticeable on a spinning hard disk, and the
  effect is drastically smaller on an SSD.

## NORMALIZATION, LEVEL, THRESHOLDING ETC

TODO: Add this section.
For now: see https://imagemagick.org/Usage/color_mods/#normalize

eof
