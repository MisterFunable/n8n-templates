# Which Version Should I Use?

## Version Comparison

### template-v3-robust.json (Recommended - Most Reliable)
- Uses Set node to extract each field separately
- Easy to debug - see exactly what data is extracted
- Uses optional chaining (`?.`) for safety
- Multiple fallbacks for error messages
- Standard Discord embed format

**Use when:**
- You want the most reliable error alerts
- You're debugging empty embeds
- You want to see what data is available

**Benefits:**
- ✅ Shows all actual values
- ✅ Easy to debug (check "Build Message Data" output)
- ✅ Safe extraction with fallbacks
- ✅ Reliable embed format

### template-v2-simple.json (Simple)
- Direct expressions in Discord webhook
- No intermediate processing
- Standard embed format

**Use when:**
- You want minimal nodes
- Direct access works for your n8n version

**Note:** May show empty if expressions don't evaluate

### template.json (Original - Complex)
- Uses Code node to extract error data
- More processing and transformation
- Can show "Unknown" values if data structure changes
- Color-coded by error type (timeout, auth, rate limit)

**Use when:**
- You need color-coded error types
- You want advanced error categorization
- You're comfortable debugging Code nodes

**Issues:**
- May show "Unknown Workflow", "Unknown node", "Unknown error"
- Depends on specific n8n error data structure
- More complex to troubleshoot

### template-v2-simple.json (Simple - Recommended)
- Uses direct error data from Error Trigger
- No Code node processing
- Reliable error information display
- Standard red embed color

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
- Color-coded embeds by error type
- Custom error message formatting
- Advanced error processing logic

## Migration

If you're currently using `template.json` and seeing "Unknown" values:

1. Import `template-v2-simple.json` as a new workflow
2. Configure Discord webhook URL and settings
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

## Customizing Color

If you want to add color-coding to the simple version, edit the `jsonBody` in "Send Discord Alert" node:

```javascript
"color": 15548997  // Red (default)
"color": 16776960  // Yellow (for timeouts)
"color": 16744448  // Orange (for rate limits)
"color": 10038562  // Purple (for auth errors)
"color": 3066993   // Green (for success - not typical for errors)
```

Replace the static color value with a conditional expression based on the error message.
