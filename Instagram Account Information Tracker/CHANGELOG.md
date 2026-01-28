# Changelog

## Version 1.2.0 (2026-01-23) - CSV Import Support

### Added

**CSV Import Support** ⚡

- **Files:**
  - `instagram-metrics-table.csv` - Ready-to-import with sample data
  - `instagram-metrics-table-template.csv` - Headers only template
  - `01-table-schema-to-csv.json` - Workflow to generate CSV from schema
  - `CSV-IMPORT-GUIDE.md` - Complete CSV import guide

- **Benefits:**
  - **2-minute setup** - Fastest way to create Data Tables
  - **No manual clicking** - Import entire table structure at once
  - **Sample data included** - Helps n8n auto-detect field types
  - **Reusable templates** - Save and share CSV files

- **Workflow:** `01-table-schema-to-csv.json`
  - Converts schema JSON to CSV file
  - Generates intelligent sample data
  - Outputs downloadable .csv file
  - Includes step-by-step instructions

### Changed

**Updated README**
- CSV import now recommended as fastest method
- Restructured Quick Start section
- Added CSV import option prominently

**Repository Structure**
- Added 3 new files for CSV support
- Updated file count in documentation

### Why CSV Import?

Before this update:
- Manual field creation: 15 minutes
- Workflow-based: 5 minutes

With CSV import:
- **2 minutes** to fully set up table! ⚡

---

## Version 1.1.0 (2026-01-23) - Bug Fix Release

### Fixed

**Critical: Merge Node Error** ✅

- **Issue:** `"You need to define at least one pair of fields in 'Fields to Match' to match on"`
- **Cause:** Merge nodes were using `mergeByPosition` mode which requires both inputs to have data, but workflow design only sends data through one branch (file OR manual, n8n OR Airtable)
- **Solution:** Changed both Merge nodes to use `append` mode instead
- **Files updated:**
  - `01-table-schema-maker.json` - Fixed "Merge Schema Data" and "Merge Results" nodes

### Added

**New Simplified Workflow** 🎉

- **File:** `01-table-schema-maker-v2.json`
- **Benefits:**
  - No file handling complexity
  - Paste schema directly in Configuration node
  - No merge nodes (simpler design)
  - Easier to use for beginners
  - Same functionality as v1
- **Recommended for:** Most users, especially first-time setup

**Comprehensive Documentation** 📚

- **TROUBLESHOOTING.md** - Detailed solutions to common issues including:
  - Merge node errors
  - API errors
  - JSON parsing issues
  - Field type problems
  - Performance tips
  - Migration guides

- **ADVANTAGES.md** - Explains benefits of this approach:
  - Comparison vs Airtable Omni
  - ROI calculations
  - Real-world scenarios
  - When to use each backend

- **Updated README.md**:
  - Added v2 workflow info
  - Quick fix recommendations
  - Better troubleshooting references
  - Version comparison

### Changed

**Improved Workflow Robustness**

- `01-table-schema-maker.json`:
  - Merge modes changed from `mergeByPosition` to `append`
  - Better handling of empty inputs
  - More reliable execution

### Migration Guide

**If you already imported v1:**

1. **Option A: Re-import fixed v1**
   - Delete old workflow
   - Import updated `01-table-schema-maker.json`
   - Your Configuration settings should transfer

2. **Option B: Switch to v2 (recommended)**
   - Import `01-table-schema-maker-v2.json`
   - Copy your schema JSON from old Configuration
   - Paste into new Configuration node
   - Test and activate

**No data migration needed** - this only affects the table creation workflow, not the tracker.

### Technical Details

**Merge Node Configuration Changes:**

```json
// Before (v1.0.0)
{
  "mode": "combine",
  "combinationMode": "mergeByPosition"
}

// After (v1.1.0)
{
  "mode": "append"
}
```

**Why this works:**
- `append` mode combines items from all inputs
- Empty inputs are ignored (no error)
- Whichever branch executes (file OR manual) passes through
- Same behavior, more robust

### Known Issues

None! All reported issues have been resolved.

### Deprecation Notices

None. Both v1 and v2 are fully supported.

---

## Version 1.0.0 (2026-01-23) - Initial Release

### Added

**Core Workflows**

- `01-table-schema-maker.json` - Dynamic table schema creator
- `02-instagram-account-tracker.json` - Instagram metrics tracker

**Schema System**

- `schema-format.json` - JSON schema specification
- `field-types-reference.md` - Complete field types guide (25+ types)
- `instagram-metrics-schema.json` - Example schema

**Documentation**

- `README.md` - Complete setup and usage guide
- Setup instructions
- API configuration guide
- Customization examples

**Features**

- Backend-agnostic schema definitions
- Support for both n8n Data Tables and Airtable
- 25+ field types with automatic type mapping
- Instagram account metrics tracking
- Engagement rate calculation
- Automatic deduplication
- Scheduled execution
- Error handling

### Known Issues

- Merge node error when one input is empty (fixed in v1.1.0)

---

## Roadmap

### Planned Features

- [ ] Multi-account tracking in single workflow
- [ ] Historical data visualization
- [ ] Export functionality (CSV, JSON)
- [ ] Additional social media schemas (TikTok, YouTube, Twitter)
- [ ] Alerting based on metric thresholds
- [ ] Automated reporting workflows

### Under Consideration

- [ ] Schema validation CLI tool
- [ ] Web-based schema builder
- [ ] Community schema library
- [ ] Integration with other n8n templates

---

## Contributing

Found a bug? Have a feature request?

1. Check existing issues in repository
2. Create new issue with:
   - n8n version
   - Workflow version
   - Error message / feature description
   - Steps to reproduce (for bugs)

## Support

- Documentation: Check README.md and TROUBLESHOOTING.md
- Issues: Repository issue tracker
- Community: n8n Community Forum

---

**Note:** This project follows [Semantic Versioning](https://semver.org/).

- **Major** (x.0.0): Breaking changes
- **Minor** (1.x.0): New features, backward compatible
- **Patch** (1.0.x): Bug fixes, backward compatible
