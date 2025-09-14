# LaTeX Compilation Instructions
# Lab 03 - Computer Networks

## Prerequisites
To compile the LaTeX document to PDF, you need to install a LaTeX distribution:

### Windows
1. Download and install MiKTeX from: https://miktex.org/download
2. Or install TeX Live from: https://www.tug.org/texlive/

### Alternative: Online LaTeX Editors
- Overleaf: https://www.overleaf.com/ (Recommended)
- TeXmaker online
- ShareLaTeX

## Compilation Steps

### Using Command Line (after installing LaTeX)
```bash
cd "c:\Users\Redes\Downloads\Lab. No.03-SM\Lab03"
pdflatex lab03_guide.tex
pdflatex lab03_guide.tex  # Run twice for proper cross-references
```

### Using Overleaf (Recommended for beginners)
1. Create a new project on Overleaf
2. Upload the lab03_guide.tex file
3. The PDF will compile automatically
4. Download the generated PDF

## Document Structure
The compiled document will include:
- Professional title page
- Table of contents
- Complete DNS implementation guide (8-12 pages)
- Configuration examples with syntax highlighting
- Testing procedures and results
- Troubleshooting section
- Comprehensive conclusions
- References section

## Package Requirements
The document uses the following LaTeX packages:
- graphicx (for images)
- listings (for code highlighting)
- tcolorbox (for information boxes)
- hyperref (for links and references)
- geometry (for page layout)
- fancyhdr (for headers/footers)

All packages are commonly available in standard LaTeX distributions.

## Expected Output
- Document length: 10-14 pages (within the 15-page limit)
- Professional formatting with proper sections
- Code listings with syntax highlighting
- Tables and figures properly referenced
- Complete bibliography

## Troubleshooting Compilation Issues

### Missing Packages
If you get package not found errors:
- MiKTeX: Packages install automatically
- TeX Live: Use `tlmgr install <package-name>`

### Image Errors
- Ensure university_logo.png exists or comment out the \includegraphics line
- All figure references should point to existing image files

### Font Issues
- The document uses standard Computer Modern fonts
- No special fonts required

## Final Checklist
Before submission ensure:
- [ ] PDF compiles without errors
- [ ] All sections are complete
- [ ] Code listings are properly formatted
- [ ] Page count is under 15 pages
- [ ] All figures and tables are referenced
- [ ] Bibliography is properly formatted
