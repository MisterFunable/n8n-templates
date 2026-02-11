# Which Version Should I Use?

## Version Comparison

### template-v3-robust.json (Recommended - Most Reliable)
- Uses Set node to extract each field separately
- Easy to debug - see exactly what data is extracted
- Uses optional chaining (`?.`) for safety
- Multiple fallbacks for error messages
- Text message format (reliable)

**Use when:**
- You want the most reliable error alerts
- You're debugging empty messages
- You want to see what data is available

**Benefits:**
- ✅ Shows all actual values
- ✅ Easy to debug (check "Build Message Data" output)
- ✅ Safe extraction with fallbacks
- ✅ Simple text format that always works

### template-v2-simple.json (Simple)
- Direct expressions in Slack node
- No intermediate processing
- Text message format

**Use when:**
- You want minimal nodes
- Direct access works for your n8n version

**Note:** May show empty if expressions don't evaluate

### template.json (Original - Complex)
- Uses Code node to extract error data
- More processing and transformation
- Can show "Unknown" values if data structure changes
- Includes stack traces and detailed debugging

**Use when:**
- You need detailed error information
- You want stack traces in alerts
- You're comfortable debugging Code nodes

**Issues:**
- May show "Unknown Workflow", "Unknown node", "Unknown error"
- Depends on specific n8n error data structure
- More complex to troubleshoot

### template-v2-simple.json (Simple - Recommended)
- Uses direct error data from Error Trigger
- No Code node processing
- Reliable error information display
- Simpler Block Kit formatting

**Use when:**
- You want reliable error alerts that always work
- You're getting "Unknown" values with original version
- You prefer simpler workflows

**Benefits:**
- Always shows actual workflow name, node name, error message
- Direct access to Error Trigger data
- Easier to debug and customize
- Less code to maintain

## Recommendation

**Start with `template-v2-simple.json`** - it's more reliable and shows actual error details instead of "Unknown" values.

Only use `template.json` if you specifically need:
- Stack traces in Slack
- Custom error message formatting
- Advanced error processing logic

## Migration

If you're currently using `template.json` and seeing "Unknown" values:

1. Import `template-v2-simple.json` as a new workflow
2. Configure Slack credentials and settings
3. Update your workflows to use the new error workflow
4. Delete or deactivate the old version

## Common Issues

### Original Version (template.json)
❌ "Unknown Workflow" - Code node can't find workflow name
❌ "Unknown node" - Code node can't find failed node
❌ "Unknown error" - Code node can't extract error message

### Simple Version (template-v2-simple.json)
✅ Shows actual workflow name from Error Trigger
✅ Shows actual failed node name
✅ Shows actual error message

## Data Structure Reference

The Error Trigger provides this data structure:

```javascript
{
  "workflow": {
    "id": "abc123",
    "name": "My Workflow"
  },
  "execution": {
    "id": "exec456",
    "mode": "regular",
    "lastNodeExecuted": "HTTP Request",
    "error": {
      "message": "403 Forbidden",
      "description": "...",
      "stack": "..."
    }
  }
}
```

**Simple version** accesses this directly:
- `{{ $('Error Trigger').item.json.workflow.name }}`
- `{{ $('Error Trigger').item.json.execution.lastNodeExecuted }}`
- `{{ $('Error Trigger').item.json.execution.error.message }}`

**Original version** tries to extract this via Code node, which can fail if the structure doesn't match expectations.
