# ap-move-calibration

[![Test](https://github.com/jewzaam/ap-move-calibration/workflows/Test/badge.svg)](https://github.com/jewzaam/ap-move-calibration/actions/workflows/test.yml)
[![Coverage](https://github.com/jewzaam/ap-move-calibration/workflows/Coverage%20Check/badge.svg)](https://github.com/jewzaam/ap-move-calibration/actions/workflows/coverage.yml)
[![Lint](https://github.com/jewzaam/ap-move-calibration/workflows/Lint/badge.svg)](https://github.com/jewzaam/ap-move-calibration/actions/workflows/lint.yml)
[![Format](https://github.com/jewzaam/ap-move-calibration/workflows/Format%20Check/badge.svg)](https://github.com/jewzaam/ap-move-calibration/actions/workflows/format.yml)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

Copy and organize master calibration frames from source to organized library based on FITS/XISF header metadata.

## Overview

This tool copies master calibration frames (bias, dark, flat) from a source directory to an organized library structure based on metadata extracted from FITS/XISF headers.

## Installation

### From Source (Development)

```bash
make install-dev
```

This installs the package in editable mode along with all dependencies (including `ap-common` from git) and development tools.

### From Git Repository

```bash
pip install git+https://github.com/jewzaam/ap-move-calibration.git
```

## Usage

```bash
ap-move-calibration <source_dir> <dest_dir> [--debug] [--dryrun]
```

Options:
- `source_dir`: Source directory containing master calibration frames
- `dest_dir`: Destination directory for organized library
- `--debug`: Enable debug output
- `--dryrun`: Perform dry run without actually copying files

## Uninstallation

```bash
make uninstall
```
