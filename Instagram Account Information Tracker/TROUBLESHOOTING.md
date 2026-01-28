# Troubleshooting Guide

## Common Issues and Solutions

### Issue: CSV import fails

**Symptoms:** Error when importing CSV to n8n Data Tables

**Common Causes:**
1. File encoding not UTF-8
2. Wrong separator (semicolon instead of comma)
3. Inconsistent column count across rows
4. Special characters not properly escaped

**Solutions:**

1. **Check file encoding:**
   - Open in a text editor (VS Code, Sublime)
   - Save as UTF-8 (not UTF-8 with BOM)
   - Re-upload to n8n

2. **Verify CSV format:**
   ```csv
   # ✅ Correct
   accountId,username,followers
   sample_123,user1,1000

   # ❌ Wrong - semicolons
   accountId;username;followers

   # ❌ Wrong - inconsistent columns
   accountId,username,followers
   sample_123,user1
   ```

3. **Escape special characters:**
   - Wrap values with commas in quotes
   - Example: `"This is a bio, with commas"`

4. **Use provided CSV files:**
   - Start with `instagram-metrics-table.csv`
   - Known working format
   - Then customize as needed

**See also:** [CSV-IMPORT-GUIDE.md](CSV-IMPORT-GUIDE.md) for detailed instructions

---

### Issue: "You need to define at least one pair of fields in 'Fields to Match' to match on"

**Problem:** The Merge nodes in the original workflow were using `mergeByPosition` mode, which requires both inputs to have data.

**Solution:**
✅ **FIXED** - Use the updated `01-table-schema-maker.json` file (both Merge nodes now use `append` mode)

Or use the simplified version: `01-table-schema-maker-v2.json` (no merge nodes needed!)

---

### Issue: "No schema data found"

**Symptoms:** Error in Parse JSON node

**Causes:**
1. Configuration node `schemaSource` is set incorrectly
2. File path is wrong or file doesn't exist
3. Manual schema JSON is empty

**Solutions:**

**Option 1: Use the Simplified Version (Recommended)**
- Import `01-table-schema-maker-v2.json`
- Paste your schema directly in Configuration node
- No file handling required!

**Option 2: Fix the Original Version**
- For file input:
  - Set `schemaSource` to `"file"`
  - Provide absolute path in `schemaFilePath`
  - Example: `/Users/you/schemas/instagram-metrics-schema.json`
- For manual input:
  - Set `schemaSource` to `"manual"`
  - Paste complete JSON object in `schemaJSON`

---

### Issue: "Airtable baseId is required"

**Problem:** You set `backend` to `"airtable"` but didn't provide Airtable configuration

**Solution:** Add `airtableConfig` to your schema:

```json
{
  "tableName": "Instagram Metrics",
  "backend": "airtable",
  "airtableConfig": {
    "baseId": "appXXXXXXXXXXXXXX"
  },
  "fields": [...]
}
```

Find your Base ID:
1. Go to Airtable
2. Open your base
3. Look in the URL: `airtable.com/appXXXXXXXXXXXXXX/...`
4. The part starting with `app` is your Base ID

---

### Issue: Instagram API errors in tracker workflow

#### Error: "Invalid OAuth access token"

**Causes:**
- Token expired
- Token revoked
- Wrong token in credentials

**Solutions:**
1. Generate a new long-lived access token
2. Update your n8n Bearer Auth credential
3. Verify token has required permissions:
   - `instagram_basic`
   - `instagram_manage_insights` (for Business/Creator accounts)

#### Error: "Unsupported get request" for insights

**Cause:** Account is not a Business or Creator account

**Solution:**
1. Convert Instagram account to Business or Creator
2. Connect to a Facebook Page
3. Or comment out the insights fetching in the workflow

#### Error: Rate limit exceeded

**Cause:** Too many API requests

**Solutions:**
1. Increase the schedule interval (default: 6 hours)
2. Reduce the insights period
3. Wait for rate limit to reset

---

### Issue: Data Table not found

**Problem:** `dataTableId` in tracker configuration doesn't match actual table

**Solutions:**

1. **Find your Data Table ID:**
   - Go to n8n → Data Tables
   - Click on your table
   - Look in the URL: `datatables/XXXXXXXXXXXXXXXX`
   - Copy that ID

2. **Update Configuration node:**
   ```javascript
   {
     "backend": "n8n",
     "dataTableId": "PASTE_ID_HERE"
   }
   ```

---

### Issue: Workflow doesn't create data

**Symptoms:** Workflow executes successfully but no data appears in table

**Debugging steps:**

1. **Check execution log:**
   - Click on the workflow execution
   - Inspect each node's output
   - Look for errors in orange/red nodes

2. **Verify API responses:**
   - Check "Fetch Account Info" node output
   - Ensure Instagram API returned data
   - Verify token permissions

3. **Check deduplication:**
   - Look at "Check if Exists" node
   - If record exists, it goes to Update instead of Insert
   - This is correct behavior!

4. **Verify table schema:**
   - Ensure table fields match schema
   - Check field names are exact (case-sensitive)
   - Verify field types are compatible

---

### Issue: JSON parsing errors

**Error:** "Unexpected token..." or "Invalid JSON"

**Causes:**
- Malformed JSON in Configuration
- Missing commas or brackets
- Trailing commas (not allowed in JSON)

**Solution:** Validate your JSON:

1. Copy your schema JSON
2. Use JSONLint.com or similar validator
3. Fix any syntax errors
4. Common mistakes:
   ```json
   // ❌ Wrong - trailing comma
   {
     "name": "field1",
     "type": "string",
   }

   // ✅ Correct
   {
     "name": "field1",
     "type": "string"
   }
   ```

---

### Issue: Field type not supported

**Problem:** Schema uses a field type not recognized

**Solution:** Check `field-types-reference.md` for supported types

Basic types that work everywhere:
- `string`
- `number`
- `boolean`
- `dateTime`
- `array`
- `object`

Advanced types (may need special handling):
- `singleSelect` / `multipleSelect` (require `choices` in options)
- `percent`, `currency`, `rating` (work differently in n8n vs Airtable)
- `formula`, `rollup`, `lookup` (Airtable only, use Code nodes in n8n)

---

### Issue: Engagement rate shows as 0

**Causes:**
1. No followers (division by zero)
2. Insights data not fetched
3. Reach data is 0

**Solutions:**

1. **Check followers count:**
   - Inspect "Fetch Account Info" output
   - Verify `followers_count` > 0

2. **Check insights:**
   - Inspect "Fetch Insights" output
   - Verify `reach` data exists
   - Business/Creator account required

3. **Adjust calculation:**
   - Edit "Process & Calculate Metrics" node
   - Modify engagement rate formula if needed

---

### Issue: Schedule trigger not firing

**Problem:** Workflow doesn't run automatically

**Solutions:**

1. **Check workflow is active:**
   - Toggle at top of workflow should be ON (green)

2. **Verify trigger configuration:**
   - Open Schedule Trigger node
   - Confirm interval is set correctly
   - Default: Every 6 hours

3. **Check n8n is running:**
   - Self-hosted: Ensure n8n service is running
   - Cloud: Should always be running

4. **Review execution history:**
   - Go to Executions tab
   - Check for automatic runs
   - Look for error messages

---

## Getting Help

### Before asking for help:

1. ✅ Check this troubleshooting guide
2. ✅ Review the README.md
3. ✅ Check field-types-reference.md for field type questions
4. ✅ Inspect workflow execution logs
5. ✅ Try the simplified v2 workflow

### When asking for help, provide:

- n8n version
- Which workflow file (v1 or v2)
- Complete error message
- Node where error occurs
- Schema JSON (sanitize sensitive data)
- Execution log (if possible)

### Useful resources:

- n8n Community Forum: https://community.n8n.io/
- Instagram Graph API Docs: https://developers.facebook.com/docs/instagram-api/
- n8n Docs: https://docs.n8n.io/

---

## Quick Fixes Checklist

When things aren't working:

- [ ] Using latest workflow files (check for v2)
- [ ] Schema JSON is valid (use validator)
- [ ] All required fields provided
- [ ] Absolute file paths (if using file input)
- [ ] Credentials configured correctly
- [ ] Table exists and ID is correct
- [ ] Instagram token is valid and has permissions
- [ ] Workflow is activated (for scheduled runs)
- [ ] n8n is up to date

---

## Performance Tips

### For large-scale tracking:

1. **Optimize API calls:**
   - Increase schedule interval
   - Reduce insights time range
   - Batch process if tracking multiple accounts

2. **Optimize storage:**
   - Use n8n Data Tables for speed
   - Use Airtable for rich features
   - Consider archiving old data

3. **Optimize workflows:**
   - Use `alwaysOutputData: true` sparingly
   - Disable unnecessary nodes during testing
   - Clear execution history periodically

---

## Migration Tips

### Moving from v1 to v2 workflow:

1. Export your schema from Configuration node (v1)
2. Import v2 workflow
3. Paste schema in Configuration node (v2)
4. Test execution
5. Deactivate v1, activate v2

### Switching backends (n8n → Airtable or vice versa):

1. Update schema `backend` field
2. Add/remove `airtableConfig` as needed
3. Run Table Schema Maker
4. Create new table
5. Update tracker Configuration
6. Test with manual execution
7. Export old data (optional)
8. Activate new tracker

---

## Still stuck?

1. Try the simplified v2 workflow first
2. Start with the example schema
3. Add fields one at a time
4. Test after each change
5. Check executions after each test

Most issues come from:
- Invalid JSON syntax
- Missing configuration
- Incorrect IDs/paths
- Expired credentials

Double-check these first!
