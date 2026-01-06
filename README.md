# Smooth Archive HL7 Conformance Statement

Sphinx-based documentation for J4Care Smooth Archive HL7 Conformance Statement.

- **ReadTheDocs**: https://smooth-archive-hl7-cs.readthedocs.io
- **GitHub**: https://github.com/j4care/smooth-archive-hl7-cs

## Repository Structure

This repository contains a submodule with the upstream dcm4che conformance statement sources:
- `docs/dcm4chee-arc-hl7cs` → upstream HL7 CS from dcm4che

## Releasing New Version on ReadTheDocs

### Step 1: Update Version

Edit `docs/conf.py`:

```python
version = u'5.34.2'
release = u'5.34.2-1'
```

### Step 2: Update Product Labeling

Replace `docs/images/product-labeling.png` with the latest image.

### Step 3: Pull Upstream Changes

```bash
git submodule update --remote docs/dcm4chee-arc-hl7cs
```

**Note:** If there are changes in `docs/dcm4chee-arc-hl7cs/doc/toc.rst`, manually sync them to `docs/toc.rst`.

### Step 4: Update ISO Preamble

Add new release entry to `docs/iso-preamble.rst` revision table.

### Step 5: Commit and Push

```bash
git add .
git commit -m "release 5.34.2-1"
git push
```

### Step 6: Create GitHub Release

1. If exists, delete the tag and release for the version you're releasing (e.g., `5.34.2`)
2. Draft a new release with tag `5.34.2` (use "create tag on release" option)
   - Note: Use version without the `-n` suffix (e.g., `5.34.2` not `5.34.2-1`)

### Step 7: Build and Activate on ReadTheDocs

At https://readthedocs.org/projects/smooth-archive-hl7-cs/:

1. Go to **Builds** → build the `latest` version
2. Go to **Versions** → **Add version** → **Activate a new version**
3. Select the new tag (e.g., `5.34.2`), enable **Active**, click **Update Version**
4. Go to **Versions** → **Settings** → set **Default version** to the new version → **Save**
5. Rebuild both `latest` and the new version (e.g., `5.34.2`)

### Step 8: Verify

https://smooth-archive-hl7-cs.readthedocs.io

## Building PDF Version

### Prerequisites

- Docker with access to `docker.j4care.com` registry

### Build Commands

```bash
VERSION=5.34.2
CS_REPO=/path/to/smooth-archive-hl7-cs
PDF_OUT=/path/to/pdf-output

# Prepare
cd ${CS_REPO}
git pull
git submodule update --remote
rm -rf build/

# Build singlehtml
docker run --rm -v ${CS_REPO}:/doc docker.j4care.com/sphinx:1.0.GA make singlehtml

# Convert to PDF
docker run --rm \
  -v ${CS_REPO}:/doc \
  -v ${PDF_OUT}:/pdf \
  docker.j4care.com/html2pdf:14-1.0.GA \
  html2pdf \
  -p /pdf/smooth-archive-hl7-cs.${VERSION}.pdf \
  -c /pro-cs-html2pdf/assets/smooth-archive-hl7-cs-cover.pdf \
  -h /pro-cs-html2pdf/assets/header-cs.html \
  -f /pro-cs-html2pdf/assets/footer-cs-hl7.html \
  /doc/build/singlehtml \
  /pro-cs-html2pdf/assets
```

### Publishing PDF

```bash
CS_DIR=/data/download-nginx/data/documentation/cs
scp -P 52771 ${PDF_OUT}/smooth-archive-hl7-cs.${VERSION}.pdf j4care@docs.j4care.com:${CS_DIR}/
scp -P 52771 ${PDF_OUT}/smooth-archive-hl7-cs.${VERSION}.pdf j4care@docs.j4care.com:${CS_DIR}/smooth-archive-hl7-cs.pdf
```

### Verify PDF

https://download.j4care.com/documentation/cs/smooth-archive-hl7-cs.pdf
