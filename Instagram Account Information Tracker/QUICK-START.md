# Quick Start Guide

Choose your path based on your needs:

## 🚀 Just Want It Working? (2 minutes)

**Path: CSV Import**

```
1. Download: instagram-metrics-table.csv
2. n8n → Data Tables → Import from CSV
3. Copy Data Table ID
4. Import: 02-instagram-account-tracker.json
5. Configure: Paste Data Table ID
6. Add Instagram credentials
7. Test & Activate
```

**Guide:** [CSV-IMPORT-GUIDE.md](CSV-IMPORT-GUIDE.md)

---

## 🎨 Want to Customize Fields? (5 minutes)

**Path: Schema → CSV → Import**

```
1. Edit: instagram-metrics-schema.json (add/remove fields)
2. Import: 01-table-schema-to-csv.json
3. Paste schema → Generate CSV
4. Download generated CSV
5. Import CSV to n8n Data Tables
6. Continue from step 3 above
```

**Guides:**
- [field-types-reference.md](field-types-reference.md) - All field types
- [CSV-IMPORT-GUIDE.md](CSV-IMPORT-GUIDE.md) - CSV import

---

## 🔧 Want Full Control? (10 minutes)

**Path: Schema Workflow**

```
1. Create/edit: your-schema.json
2. Import: 01-table-schema-maker-v2.json
3. Paste schema → Generate instructions
4. Create table manually in n8n OR Airtable
5. Import: 02-instagram-account-tracker.json
6. Configure backend and table ID
7. Test & Activate
```

**Guides:**
- [README.md](README.md) - Full documentation
- [schema-format.json](schema-format.json) - Schema specification
- [ADVANTAGES.md](ADVANTAGES.md) - Why this approach

---

## 📊 Comparison

| Method | Time | Best For | Flexibility |
|--------|------|----------|-------------|
| **CSV Import** | 2 min | Quick setup, using example schema | ⭐⭐ |
| **Schema → CSV** | 5 min | Custom fields, still fast | ⭐⭐⭐ |
| **Schema Workflow** | 10 min | Full customization, backend choice | ⭐⭐⭐⭐⭐ |

---

## What You'll Track

✅ Account info (username, followers, following, media count)
✅ Insights (impressions, reach, profile views, website clicks)
✅ Engagement rate (automatically calculated)
✅ Metadata (last checked, status, notes)

All updated automatically every 6 hours (configurable).

---

## Prerequisites

### Required
- n8n instance (self-hosted or cloud)
- Instagram Business or Creator account
- Facebook Page (connected to Instagram)
- Instagram access token

### How to Get Instagram Token

1. Go to [Facebook Developers](https://developers.facebook.com/)
2. Create App → Add Instagram Basic Display
3. Configure Instagram account
4. Generate long-lived access token
5. Required permissions:
   - `instagram_basic`
   - `instagram_manage_insights`

**Guide:** See README.md "Instagram API Setup" section

---

## Next Steps After Setup

### Immediate
1. ✅ Test workflow manually
2. ✅ Verify data appears in table
3. ✅ Check calculated engagement rate
4. ✅ Activate workflow for scheduled runs

### Optional Enhancements
- Track multiple Instagram accounts
- Add custom fields to schema
- Build dashboard/visualization
- Set up alerts for metric changes
- Export data periodically

### Troubleshooting
- Having issues? → [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- Understanding the approach? → [ADVANTAGES.md](ADVANTAGES.md)
- Need field help? → [field-types-reference.md](field-types-reference.md)

---

## Support

**Stuck?** Check in this order:

1. ✅ [TROUBLESHOOTING.md](TROUBLESHOOTING.md) - Common issues
2. ✅ [CSV-IMPORT-GUIDE.md](CSV-IMPORT-GUIDE.md) - CSV help
3. ✅ [README.md](README.md) - Full documentation
4. ✅ n8n Community Forum

**Found a bug?** Check [CHANGELOG.md](CHANGELOG.md) for known issues and fixes.

---

## Files at a Glance

### For Beginners
- ⭐ **QUICK-START.md** (this file)
- ⭐ **CSV-IMPORT-GUIDE.md**
- ⭐ **instagram-metrics-table.csv**

### For Setup
- `02-instagram-account-tracker.json` - Main workflow
- `01-table-schema-to-csv.json` - CSV generator
- `01-table-schema-maker-v2.json` - Table creator

### For Reference
- `README.md` - Complete guide
- `TROUBLESHOOTING.md` - Problem solving
- `field-types-reference.md` - Field types
- `ADVANTAGES.md` - Why use this

### For Advanced Users
- `schema-format.json` - Schema spec
- `instagram-metrics-schema.json` - Example schema
- `01-table-schema-maker.json` - Advanced table creator
- `CHANGELOG.md` - Version history

---

## Success Checklist

Before you start:
- [ ] Have n8n instance ready
- [ ] Instagram Business/Creator account
- [ ] Facebook Page connected
- [ ] Access token generated

After CSV import:
- [ ] Data Table created
- [ ] Table ID copied
- [ ] Tracker workflow imported
- [ ] Configuration updated

After tracker setup:
- [ ] Instagram credentials added
- [ ] Test execution successful
- [ ] Data appears in table
- [ ] Workflow activated

You're done! 🎉

---

**Estimated Total Time:**
- Absolute minimum: **10 minutes** (CSV import + tracker setup)
- With token generation: **20 minutes**
- With customization: **30 minutes**

Much faster than building from scratch! 🚀
