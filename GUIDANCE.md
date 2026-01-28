# ap-move-calibration

A tool for copying and organizing master calibration frames (BIAS, DARK, FLAT) from a source directory to a destination library, organizing them based on FITS/XISF header metadata.

## Overview

This tool copies master calibration frames from a source directory (e.g., WBPP output) to an organized destination library. It reads FITS/XISF headers to extract metadata and organizes files by frame type and optical configuration.

Unlike the original `copycalibration.py` which required separate source/destination directories for each frame type, this tool uses a single input directory and single output directory, automatically organizing frames into type-specific subdirectories.

## Usage

```powershell
python -m ap_move_calibration.move_calibration <source_dir> <dest_dir> [--debug] [--dryrun] [--overwrite] [--help]
```

Options:
- `source_dir`: Source directory containing master calibration files (BIAS, DARK, FLAT)
- `dest_dir`: Destination directory for organized calibration library
- `--debug`: Enable debug output
- `--dryrun`: Perform dry run without actually copying files
- `--overwrite`: Overwrite existing files in destination (default: skip existing files)
- `--help`: Show help message and exit

## Installation

### From Source (Development)

```powershell
make install-dev
```

This installs the package in editable mode along with all dependencies (including `ap-common` from git) and development tools.

### From Git Repository (One-liner)

```powershell
pip install git+https://github.com/jewzaam/ap-move-calibration.git
```

This installs the package directly from the GitHub repository without requiring a local checkout.

### Uninstallation

```powershell
make uninstall
```

## How It Works

1. Scans `source_dir` recursively for master calibration files (`.xisf` and `.fits` formats)
2. Reads FITS/XISF headers to extract metadata (type, camera, optic, filter, date, gain, offset, temp, exposure, etc.)
3. Organizes files by frame type into `dest_dir/{BIAS,DARK,FLAT}/`
4. Within each type directory, organizes by optical configuration (camera, optic, filter)
5. Reports files copied and any errors encountered
6. Skips existing files unless `--overwrite` is specified

## Directory Structure

The tool organizes calibration frames into a library structure optimized for quick lookup by optical configuration:

```
{dest_dir}/
├── BIAS/
│   └── {camera}/
│       └── {filename_with_metadata}.xisf
├── DARK/
│   └── {camera}/
│       └── {filename_with_metadata}.xisf
└── FLAT/
    └── {camera}/
        └── [{optic}/]
            └── DATE_{CCYY-MM-DD}/
                └── {filename_without_date}.xisf
```

### Frame Type Organization

**BIAS Frames**: Organized by camera
- Path: `BIAS/{camera}/`
- Filename includes: camera, gain, offset, temp, readout mode, etc.
- Example: `BIAS/{camera}/masterBias_GAIN_100_OFFSET_10_TEMP_-10.xisf`

**DARK Frames**: Organized by camera
- Path: `DARK/{camera}/`
- Filename includes: camera, exposure time, gain, offset, temp, readout mode, etc.
- Example: `DARK/{camera}/masterDark_EXPTIME_300_GAIN_100_OFFSET_10_TEMP_-10.xisf`

**FLAT Frames**: Organized by camera, optic (if present), and date
- Path: `FLAT/{camera}/[{optic}/]DATE_{CCYY-MM-DD}/`
- Filename includes: filter, gain, offset, temp, focal length, readout mode (but NOT date)
- Date is encoded in directory structure instead of filename
- Example: `FLAT/{camera}/{optic}/DATE_2026-01-27/masterFlat_FILTER_L_GAIN_100_OFFSET_10.xisf`

This organization allows:
- Quick lookup of calibration frames by camera and optical configuration
- Easy identification of calibration frame age (via DATE directories for flats)
- Deduplication of identical frames across multiple sessions
- Efficient storage (same calibration used across multiple targets)

## Metadata Extraction

The tool uses `ap-common` utilities to extract metadata from FITS/XISF headers with robust fallback handling:

- **Type Detection**: Identifies MASTER BIAS, MASTER DARK, MASTER FLAT from IMAGETYP header
- **Camera**: Extracted from INSTRUME header
- **Optic**: Extracted from TELESCOP header (flats only)
- **Filter**: Extracted from FILTER header (flats only)
- **Date**: Extracted from DATE-OBS header (flats only, for directory organization)
- **Exposure Time**: Extracted from EXPTIME header (darks only)
- **Temperature**: Extracted from SET-TEMP or CCD-TEMP headers
- **Gain/Offset**: Extracted from GAIN and OFFSET headers
- **Focal Length**: Extracted from FOCALLEN header (flats only)
- **Readout Mode**: Extracted from READOUTM header

All header normalization is handled by `ap-common.normalization` to ensure consistency across different file formats and camera types.

## Comparison to Original Script

**Original `copycalibration.py`**:
- Required 6 separate directory arguments (src/dest for each type)
- Mixed responsibilities (library organization + copying to light frames)
- Complex argument list
- Example: `--src_bias_dir=X --dest_bias_dir=Y --src_dark_dir=X --dest_dark_dir=Y --src_flat_dir=X --dest_flat_dir=Y`

**New `ap-move-calibration`**:
- Single source directory, single destination directory
- Focused responsibility: organize calibration library only
- Simple argument list
- Automatic type-based organization into subdirectories
- Example: `<source_dir> <dest_dir>`

The original script's functionality for copying calibration frames to light frame directories should be handled by a separate tool (future work).

## Workflow Integration

This tool is intended to be the first step in a multi-stage calibration workflow:

1. **Generate Masters** - Use WBPP or similar to create master calibration frames
2. **Organize Library** - Use `ap-move-calibration` to copy masters to organized library (this tool)
3. **Copy to Lights** - Use a separate tool to copy appropriate calibration frames to light frame directories based on metadata matching

By separating library organization from light frame matching, each tool has a focused responsibility and can be used independently.

## Example

```powershell
# Copy all master calibration frames from WBPP output to organized library
python -m ap_move_calibration.move_calibration `
    "D:\WBPP\_calibration\master" `
    "D:\Dropbox\Astrophotography\Data\_Calibration_Library" `
    --debug

# Dry run to preview what would be copied
python -m ap_move_calibration.move_calibration `
    "D:\WBPP\_calibration\master" `
    "D:\Dropbox\Astrophotography\Data\_Calibration_Library" `
    --dryrun

# Overwrite existing files
python -m ap_move_calibration.move_calibration `
    "D:\WBPP\_calibration\master" `
    "D:\Dropbox\Astrophotography\Data\_Calibration_Library" `
    --overwrite
```

## Dependencies

- `ap-common` - Shared astrophotography utilities for FITS/XISF reading, metadata extraction, and file operations
- `astropy` - FITS file reading (via ap-common)
- `xisf==0.9.5` - XISF file reading (via ap-common)

## Development

Run tests:
```powershell
make test
```

Run with coverage:
```powershell
make test-coverage
```

Format code:
```powershell
make format
```

Lint code:
```powershell
make lint
```
