# Files Overview

Complete guide to all 15 files in this system.

## 🚀 Quick Start Files (Start Here!)

### QUICK-START.md
**Purpose:** Choose your path and get started in minutes
**Read if:** You're new and want to get running fast
**Time:** 2 minutes to read

### CSV-IMPORT-GUIDE.md
**Purpose:** Step-by-step CSV import instructions
**Read if:** You want the fastest setup method
**Time:** 5 minutes to complete full setup

### instagram-metrics-table.csv ⭐
**Purpose:** Ready-to-import CSV with sample data
**Use:** Import directly to n8n Data Tables
**Benefit:** 2-minute table creation

### instagram-metrics-table-template.csv
**Purpose:** CSV headers only (no sample data)
**Use:** When you want to add your own data
**Benefit:** Clean starting point

---

## 🔧 Workflow Files (Import These to n8n)

### 02-instagram-account-tracker.json ⭐⭐⭐
**Purpose:** Main workflow - tracks Instagram metrics
**Features:**
- Fetches account info & insights
- Calculates engagement rate
- Handles deduplication
- Supports n8n + Airtable backends
**Required:** Yes, this is the core tracker

### 01-table-schema-to-csv.json ⭐⭐
**Purpose:** Generate CSV from schema JSON
**Use case:** You customized the schema and need a CSV
**Output:** Downloadable .csv file with proper structure
**Required:** No (optional helper)

### 01-table-schema-maker-v2.json ⭐
**Purpose:** Simple schema processor (paste JSON, get instructions)
**Use case:** You want to understand schema before creating table
**Required:** No (alternative to CSV import)

### 01-table-schema-maker.json
**Purpose:** Advanced schema processor (file or manual input)
**Use case:** Need file-based input or webhook trigger
**Required:** No (use v2 instead unless you need advanced features)

---

## 📚 Documentation Files (Reference)

### README.md ⭐⭐⭐
**Purpose:** Complete system documentation
**Contents:**
- Full setup guide
- All configuration options
- Instagram API setup
- Customization examples
- Advanced use cases
**Read:** At least the Quick Start section

### TROUBLESHOOTING.md ⭐⭐
**Purpose:** Solutions to common problems
**Contents:**
- CSV import errors
- API errors
- Merge node fixes
- Data table issues
- Performance tips
**Read when:** Something doesn't work

### ADVANTAGES.md ⭐
**Purpose:** Why use this vs alternatives
**Contents:**
- Comparison vs Airtable Omni
- Cost analysis ($240-600/year savings)
- Real-world scenarios
- ROI calculations
**Read if:** Evaluating this approach

### CHANGELOG.md
**Purpose:** Version history and updates
**Contents:**
- Bug fixes
- New features
- Migration guides
**Read when:** Upgrading or checking for fixes

### field-types-reference.md ⭐⭐
**Purpose:** Complete field type documentation
**Contents:**
- All 25+ supported field types
- n8n ↔ Airtable mapping
- Type-specific options
- Examples for each type
**Read when:** Customizing schema

---

## 🎨 Schema Files (Templates & Specs)

### instagram-metrics-schema.json ⭐
**Purpose:** Example schema for Instagram metrics
**Contents:**
- 19 pre-configured fields
- Account info + insights + metadata
- Ready to use or customize
**Use:** Copy and modify for your needs

### schema-format.json
**Purpose:** JSON schema specification
**Contents:**
- Schema structure definition
- Required/optional fields
- Validation rules
- Examples
**Read when:** Creating custom schemas

---

## 📊 File Purpose Matrix

| File | New Users | Developers | Customizers | Required |
|------|-----------|------------|-------------|----------|
| **QUICK-START.md** | ⭐⭐⭐ | ⭐ | ⭐ | Start here |
| **CSV-IMPORT-GUIDE.md** | ⭐⭐⭐ | ⭐⭐ | ⭐⭐ | Recommended |
| **instagram-metrics-table.csv** | ⭐⭐⭐ | ⭐ | ⭐ | Fast setup |
| **02-instagram-account-tracker.json** | ⭐⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | **Required** |
| **README.md** | ⭐⭐ | ⭐⭐⭐ | ⭐⭐⭐ | Reference |
| **TROUBLESHOOTING.md** | ⭐⭐ | ⭐⭐ | ⭐⭐ | When stuck |
| **01-table-schema-to-csv.json** | ⭐ | ⭐⭐ | ⭐⭐⭐ | Optional |
| **field-types-reference.md** | ⭐ | ⭐⭐ | ⭐⭐⭐ | For custom |
| **instagram-metrics-schema.json** | ⭐ | ⭐⭐ | ⭐⭐⭐ | Template |
| **ADVANTAGES.md** | ⭐ | ⭐⭐ | ⭐⭐ | Background |
| **01-table-schema-maker-v2.json** | ⭐ | ⭐⭐ | ⭐⭐ | Alternative |

---

## 🎯 Reading Paths

### Path 1: "Just Make It Work" (10 minutes)
```
1. QUICK-START.md (choose CSV path)
2. CSV-IMPORT-GUIDE.md (follow steps)
3. instagram-metrics-table.csv (download & import)
4. 02-instagram-account-tracker.json (import & configure)
5. Done! ✅
```

### Path 2: "Understand Then Build" (30 minutes)
```
1. README.md (Overview + Quick Start)
2. instagram-metrics-schema.json (see example)
3. CSV-IMPORT-GUIDE.md (import method)
4. 02-instagram-account-tracker.json (import & configure)
5. TROUBLESHOOTING.md (if issues)
6. Done! ✅
```

### Path 3: "Full Customization" (1 hour)
```
1. README.md (complete read)
2. ADVANTAGES.md (understand approach)
3. schema-format.json (learn structure)
4. field-types-reference.md (available types)
5. instagram-metrics-schema.json (copy & modify)
6. 01-table-schema-to-csv.json (generate CSV)
7. CSV-IMPORT-GUIDE.md (import)
8. 02-instagram-account-tracker.json (configure)
9. Done! ✅
```

---

## 💾 File Sizes

| Category | Files | Total Size |
|----------|-------|------------|
| **Workflows** | 4 | ~52 KB |
| **Documentation** | 7 | ~48 KB |
| **Schemas/Data** | 4 | ~10 KB |
| **Total** | 15 | ~110 KB |

Lightweight and fast to download! 🚀

---

## 🔄 Update Frequency

| File | Updates When |
|------|--------------|
| **CSV files** | Schema changes |
| **Workflows** | Bug fixes, features |
| **README** | Major changes |
| **TROUBLESHOOTING** | New issues found |
| **CHANGELOG** | Each version |
| **Others** | Rarely |

---

## 📦 Bundled Downloads

**For beginners, download these 3:**
1. `QUICK-START.md`
2. `instagram-metrics-table.csv`
3. `02-instagram-account-tracker.json`

**For developers, add these 3:**
4. `README.md`
5. `field-types-reference.md`
6. `01-table-schema-to-csv.json`

**For complete system, get all 15 files**

---

## 🎓 Learning Order

Recommended reading sequence:

1. **QUICK-START.md** - Orient yourself
2. **CSV-IMPORT-GUIDE.md** - Get hands-on
3. **README.md** (Quick Start section) - Understand basics
4. **TROUBLESHOOTING.md** (if stuck) - Solve problems
5. **field-types-reference.md** (when customizing) - Deep dive
6. **ADVANTAGES.md** (optional) - Broader context

Don't read everything at once! Start with what you need.

---

## 🔗 File Dependencies

```
instagram-metrics-schema.json
    ↓ (can generate)
instagram-metrics-table.csv
    ↓ (imports to)
n8n Data Table
    ↓ (used by)
02-instagram-account-tracker.json
    ↓ (tracks)
Instagram Metrics ✅
```

Or use workflows:
```
instagram-metrics-schema.json
    ↓ (input to)
01-table-schema-to-csv.json
    ↓ (outputs)
Custom CSV
    ↓ (same as above)
```

---

## 🏆 Most Important Files

**If you only read 3 files:**
1. ⭐⭐⭐ **QUICK-START.md** - Where to begin
2. ⭐⭐⭐ **CSV-IMPORT-GUIDE.md** - How to set up
3. ⭐⭐⭐ **README.md** - Everything else

**If you only use 2 files:**
1. ⭐⭐⭐ **instagram-metrics-table.csv** - Quick table
2. ⭐⭐⭐ **02-instagram-account-tracker.json** - Core workflow

That's it! You can be productive with just these 5 files.

---

## 🎯 Quick Reference

**Problem?** → TROUBLESHOOTING.md
**Custom fields?** → field-types-reference.md
**Why this approach?** → ADVANTAGES.md
**What changed?** → CHANGELOG.md
**Full guide?** → README.md
**Get started?** → QUICK-START.md

---

**Remember:** You don't need to use all 15 files. Start simple, add complexity as needed!
