# Test Plan

> This document describes the testing strategy for ap-move-master-to-library.

## Overview

**Project:** ap-move-master-to-library
**Primary functionality:** Copy and organize master calibration frames from source to destination library with proper structure.

## Testing Philosophy

This project follows the [ap-base Testing Standards](https://github.com/jewzaam/ap-base/blob/main/standards/standards/testing.md) and [CLI Testing Standards](https://github.com/jewzaam/ap-base/blob/main/standards/standards/cli-testing.md).

Key testing principles:
- TDD for bug fixes
- Business logic isolation from CLI and I/O
- CLI argument mapping verification to prevent attribute bugs

## Test Categories

### Unit Tests

- `test_move_calibration.py` - Business logic for file organization and copying

### CLI/Main Function Tests

**Purpose:** Verify command-line argument parsing and main() entry point integration.

**Standard:** Follows [CLI Testing Standards](../../standards/standards/cli-testing.md)

**Coverage:**
- Argument name mapping (prevents args.attribute_name typos)
- Each CLI flag individually with call_args verification
- Multiple flags combined
- Short flag forms (-q for --quiet)

**Pattern:**
- Mocks sys.argv to simulate CLI invocation
- Mocks copy_calibration_frames() to isolate argparse
- Verifies call_args.kwargs to catch attribute name mismatches

**Rationale:** Prevents runtime AttributeError from CLI argument typos. Catches bugs that
linters, type checkers, and unit tests cannot detect.

**Test File:** `tests/test_main.py::TestMainCLI`

**Test Coverage:**
- `test_minimal_execution` - Basic required args with default flags
- `test_debug_flag` - --debug parameter mapping
- `test_dryrun_flag` - --dryrun parameter mapping
- `test_no_overwrite_flag` - --no-overwrite parameter mapping
- `test_quiet_flag` - --quiet parameter mapping
- `test_quiet_short_flag` - -q short form
- `test_multiple_flags_combined` - Multiple flag interactions
- `test_all_flags_combined` - All flags together

## Running Tests

```bash
# Run all tests
make test

# Run CLI tests specifically
pytest tests/test_main.py -v
```

## Changelog

| Date | Change | Rationale |
|------|--------|-----------|
| 2026-02-14 | Add CLI testing documentation and test suite | Implement CLI Testing Standards across all ap-* modules |
