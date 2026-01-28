# CSV Import Guide for n8n Data Tables

The **fastest way** to set up your Instagram Account Metrics table in n8n is to import a CSV file. This guide shows you how.

## Quick Start (2 minutes)

### Option 1: Use Pre-made CSV (Fastest)

1. **Download the CSV file**
   - Use `instagram-metrics-table.csv` (includes sample data)
   - OR `instagram-metrics-table-template.csv` (headers only)

2. **Import to n8n**
   - Go to your n8n instance
   - Click **Data Tables** in the sidebar
   - Click **Add Data Table**
   - Enter name: `Instagram Account Metrics`
   - Click **Import from CSV**
   - Upload the CSV file
   - Click **Create**

3. **Copy the Data Table ID**
   - After creation, click on your table
   - Look at the URL: `datatables/XXXXXXXXXXXXXXXX`
   - Copy that ID

4. **Use in Tracker Workflow**
   - Import `02-instagram-account-tracker.json`
   - In Configuration node, set `dataTableId` to your copied ID
   - Done!

### Option 2: Generate Custom CSV from Schema

If you customized the schema or want different fields:

1. **Import the CSV generator workflow**
   - Import `01-table-schema-to-csv.json`

2. **Paste your schema**
   - Open Configuration node
   - Paste your schema JSON in `schemaJSON` field
   - Example:
   ```json
   {
     "tableName": "Instagram Metrics",
     "backend": "n8n",
     "fields": [
       {"name": "accountId", "type": "string"},
       {"name": "followers", "type": "number"}
     ]
   }
   ```

3. **Generate CSV**
   - Click **Test workflow**
   - Download the generated CSV from output
   - Save as `.csv` file

4. **Import to n8n**
   - Follow steps 2-4 from Option 1 above

## CSV File Structure

### Headers Row
The first row contains field names (column headers):
```csv
accountId,username,followersCount,followingCount,...
```

### Data Row (Optional)
Second row can contain sample data:
```csv
sample_account_123,sample_user,1000,500,...
```

**Tip:** Including sample data helps n8n auto-detect correct field types!

## Field Type Detection

n8n automatically detects types based on CSV data:

| Data Example | Detected Type |
|--------------|---------------|
| `hello` | string |
| `123` | number |
| `true`, `false` | boolean |
| `2026-01-23T12:00:00.000Z` | dateTime |
| `https://example.com` | string (manual change to url if needed) |

**After import, review and adjust field types as needed!**

## Customizing Your CSV

### Add Your Own Fields

Edit the CSV file to add/remove columns:

```csv
accountId,followers,customField1,customField2
sample_123,1000,value1,value2
```

### Change Field Order

Arrange columns in any order you prefer:

```csv
username,accountId,followers
sample_user,sample_123,1000
```

### Include Multiple Sample Rows

Add multiple data rows for testing:

```csv
accountId,followers
account_1,1000
account_2,2000
account_3,3000
```

## Common Issues

### Issue: Import fails with "Invalid CSV"

**Solutions:**
- Ensure file is saved as UTF-8 encoding
- No empty lines at the end of file
- All rows have same number of columns
- Use commas as separators (not semicolons)

### Issue: Wrong field types detected

**Solution:**
After import:
1. Click on the Data Table
2. Click **Edit Table Structure**
3. Adjust field types as needed

### Issue: Special characters broken

**Solution:**
- Save CSV with UTF-8 encoding
- Use a proper text editor (VS Code, Sublime, not Excel)
- For Excel: Use "Save As" → CSV UTF-8

### Issue: Commas in data break columns

**Solution:**
Wrap values with commas in quotes:

```csv
accountId,biography
sample_123,"This is a bio, with commas, and more text"
```

## Comparison: CSV vs Manual vs Workflow

| Method | Time | Pros | Cons |
|--------|------|------|------|
| **CSV Import** | 2 min | ⚡ Fastest<br>✅ Bulk setup<br>✅ Reusable | ⚠️ Manual type checking |
| **Manual UI** | 15 min | ✅ Visual<br>✅ Type control | ❌ Slow<br>❌ Error-prone |
| **Workflow Generator** | 5 min | ✅ Automated<br>✅ Schema-as-code | ⚠️ Extra step |

**Recommendation:** Use CSV import for speed, then verify/adjust types.

## Advanced: CSV from Schema Workflow

The `01-table-schema-to-csv.json` workflow offers advanced options:

### Configuration Options

```json
{
  "schemaJSON": { ... },      // Your schema
  "includeSampleRow": true    // Include example data
}
```

**includeSampleRow: true**
- Generates sample data based on field types
- Helps n8n auto-detect types
- Good for testing

**includeSampleRow: false**
- Headers only
- You add your own data
- Smaller file size

### Sample Data Generation

The workflow intelligently generates sample data:

- **accountId** → `sample_account_123`
- **followers** → `1000`
- **email** → `user@example.com`
- **url** → `https://example.com`
- **dateTime** → Current timestamp
- **boolean** → `false`
- **singleSelect** → First choice from options
- **multipleSelect** → First two choices

### Output

The workflow provides:
1. **CSV content** (text)
2. **Binary file** (downloadable .csv)
3. **Instructions** (step-by-step)
4. **Metadata** (field count, headers)

## Tips for Success

### Before Import

✅ Validate CSV structure (all rows same column count)
✅ Use meaningful sample data (helps type detection)
✅ Check encoding is UTF-8
✅ Remove any Excel formulas or formatting

### After Import

✅ Review auto-detected field types
✅ Adjust types if needed (string → url, etc.)
✅ Delete sample data rows (if you don't want them)
✅ Test with the tracker workflow
✅ Copy the Data Table ID

### For Reuse

✅ Save your CSV template
✅ Version control the CSV file
✅ Document any custom fields
✅ Share template with team

## Example Workflows

### Minimal Setup (3 fields)

**CSV:**
```csv
accountId,username,followers
sample_123,sample_user,1000
```

**Import → Ready in 2 minutes!**

### Full Setup (19 fields)

**Use:** `instagram-metrics-table.csv`

**Import → Complete tracking in 5 minutes!**

### Custom Setup

**Generate from schema → Custom CSV → Import**

## Next Steps

After importing your Data Table:

1. ✅ Copy the Data Table ID
2. ✅ Import `02-instagram-account-tracker.json`
3. ✅ Configure with your Data Table ID
4. ✅ Add Instagram credentials
5. ✅ Test workflow
6. ✅ Activate and schedule

**Total setup time: 10 minutes** ⚡

## Questions?

- **CSV structure?** See examples in this folder
- **Field types?** Check `field-types-reference.md`
- **Import errors?** See [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
- **Schema help?** See [README.md](README.md)

---

**Pro Tip:** Keep your CSV template in version control alongside your schemas. When you update the schema, regenerate the CSV and re-import to a new table for testing!
