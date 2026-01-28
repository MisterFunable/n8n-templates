# Field Types Reference

This document describes all supported field types for the Table Schema format and their implementation across n8n Data Tables and Airtable.

## Basic Types

### string
- **n8n**: `string` type
- **Airtable**: Single line text or Long text (based on length)
- **Description**: Text data
- **Options**: None

### number
- **n8n**: `number` type
- **Airtable**: Number field
- **Description**: Numeric values (integers or decimals)
- **Options**:
  - `precision`: Number of decimal places (0-8)

### boolean
- **n8n**: `boolean` type
- **Airtable**: Checkbox field
- **Description**: True/false values
- **Options**: None

### dateTime
- **n8n**: `dateTime` type (ISO 8601 string)
- **Airtable**: Date or Date time field
- **Description**: Date and/or time values
- **Options**:
  - `format`: Date format string (e.g., "YYYY-MM-DD HH:mm:ss")

### array
- **n8n**: `array` type (JSON array)
- **Airtable**: Multiple select or Linked records
- **Description**: Array of values
- **Options**: None (use multipleSelect for predefined choices)

### object
- **n8n**: `object` type (JSON object)
- **Airtable**: Long text (serialized as JSON)
- **Description**: Complex nested data
- **Options**: None

## Selection Types

### singleSelect
- **n8n**: `string` type with validation
- **Airtable**: Single select field
- **Description**: Choose one option from predefined list
- **Options**:
  - `choices`: Array of string values (required)
- **Example**:
```json
{
  "name": "status",
  "type": "singleSelect",
  "options": {
    "choices": ["active", "paused", "archived"]
  }
}
```

### multipleSelect
- **n8n**: `array` type with validation
- **Airtable**: Multiple select field
- **Description**: Choose multiple options from predefined list
- **Options**:
  - `choices`: Array of string values (required)
- **Example**:
```json
{
  "name": "tags",
  "type": "multipleSelect",
  "options": {
    "choices": ["trending", "viral", "sponsored", "collaboration"]
  }
}
```

## Formatted Types

### url
- **n8n**: `string` type with URL validation
- **Airtable**: URL field
- **Description**: Web URLs
- **Options**: None

### email
- **n8n**: `string` type with email validation
- **Airtable**: Email field
- **Description**: Email addresses
- **Options**: None

### phone
- **n8n**: `string` type
- **Airtable**: Phone number field
- **Description**: Phone numbers
- **Options**: None

### rating
- **n8n**: `number` type (1-max)
- **Airtable**: Rating field
- **Description**: Star/icon-based rating
- **Options**:
  - `max`: Maximum rating value (default: 5)
  - `icon`: Icon type (star, heart, thumbs-up, flag, dot)
  - `color`: Color theme (yellow, red, blue, green, gray)
- **Example**:
```json
{
  "name": "contentQuality",
  "type": "rating",
  "options": {
    "max": 5,
    "icon": "star",
    "color": "yellow"
  }
}
```

### duration
- **n8n**: `number` type (seconds)
- **Airtable**: Duration field
- **Description**: Time duration
- **Options**:
  - `durationFormat`: Display format (h:mm, h:mm:ss, h:mm:ss.s)

### percent
- **n8n**: `number` type (0-100)
- **Airtable**: Percent field
- **Description**: Percentage values
- **Options**:
  - `precision`: Decimal precision (0-8)

### currency
- **n8n**: `number` type with formatting
- **Airtable**: Currency field
- **Description**: Monetary values
- **Options**:
  - `symbol`: Currency symbol ($, €, £, ¥, etc.)
  - `precision`: Decimal precision (default: 2)

## Computed Types

### count
- **n8n**: Not directly supported (use formula or external calculation)
- **Airtable**: Count field (counts linked records)
- **Description**: Count of linked records
- **Options**:
  - `linkedTable`: Name of table to count from
- **Note**: In n8n, implement as a formula or separate aggregation workflow

### rollup
- **n8n**: Not directly supported (use formula or external calculation)
- **Airtable**: Rollup field
- **Description**: Aggregate values from linked records
- **Options**:
  - `linkedTable`: Name of linked table
  - `linkedField`: Field to aggregate
  - `aggregation`: Function (count, sum, avg, max, min)
- **Note**: In n8n, implement as a separate aggregation workflow

### lookup
- **n8n**: Not directly supported (use joins or external lookup)
- **Airtable**: Lookup field
- **Description**: Pull values from linked records
- **Options**:
  - `linkedTable`: Name of linked table
  - `linkedField`: Field to look up
- **Note**: In n8n, implement as a join operation in the workflow

### formula
- **n8n**: Not directly supported (calculate in workflow)
- **Airtable**: Formula field
- **Description**: Calculated values based on other fields
- **Options**:
  - `formula`: Formula expression (Airtable formula syntax)
- **Note**: In n8n, implement calculations in Code nodes before insertion

## Metadata Types

### createdTime
- **n8n**: `dateTime` type (set on insert)
- **Airtable**: Created time field (automatic)
- **Description**: Timestamp when record was created
- **Options**: None
- **Note**: In n8n, set manually during insertion

### lastModifiedTime
- **n8n**: `dateTime` type (update on modification)
- **Airtable**: Last modified time field (automatic)
- **Description**: Timestamp of last modification
- **Options**: None
- **Note**: In n8n, update manually during modification

### createdBy
- **n8n**: `string` type (set on insert)
- **Airtable**: Created by field (automatic)
- **Description**: User who created the record
- **Options**: None
- **Note**: In n8n, set manually or via workflow metadata

### lastModifiedBy
- **n8n**: `string` type (update on modification)
- **Airtable**: Last modified by field (automatic)
- **Description**: User who last modified the record
- **Options**: None
- **Note**: In n8n, update manually or via workflow metadata

### autonumber
- **n8n**: Not directly supported (use auto-increment logic)
- **Airtable**: Autonumber field (automatic)
- **Description**: Auto-incrementing sequential number
- **Options**: None
- **Note**: In n8n, implement custom counter logic

## Special Types

### barcode
- **n8n**: `string` type
- **Airtable**: Barcode field
- **Description**: Barcode data with visual representation
- **Options**: None

### button
- **n8n**: Not supported
- **Airtable**: Button field (triggers automation)
- **Description**: Interactive button in Airtable interface
- **Options**: Button configuration
- **Note**: Not applicable to n8n Data Tables

## Type Mapping Summary

| Schema Type | n8n Data Table | Airtable |
|-------------|----------------|----------|
| string | string | Single line text |
| number | number | Number |
| boolean | boolean | Checkbox |
| dateTime | dateTime | Date |
| array | array | Multiple select |
| object | object | Long text (JSON) |
| singleSelect | string (validated) | Single select |
| multipleSelect | array (validated) | Multiple select |
| url | string (validated) | URL |
| email | string (validated) | Email |
| phone | string | Phone number |
| rating | number | Rating |
| duration | number (seconds) | Duration |
| percent | number | Percent |
| currency | number | Currency |
| count | Custom workflow | Count |
| rollup | Custom workflow | Rollup |
| lookup | Custom workflow | Lookup |
| formula | Code node | Formula |
| createdTime | dateTime (manual) | Created time (auto) |
| lastModifiedTime | dateTime (manual) | Last modified time (auto) |
| createdBy | string (manual) | Created by (auto) |
| lastModifiedBy | string (manual) | Last modified by (auto) |
| autonumber | Custom logic | Autonumber (auto) |
| barcode | string | Barcode |
| button | N/A | Button |

## Implementation Notes

### n8n Data Tables Limitations
- No native support for computed fields (count, rollup, lookup, formula)
- No automatic metadata fields (created time, modified by, etc.)
- These features must be implemented in the workflow logic

### Airtable Advantages
- Rich field types with automatic computation
- Automatic metadata tracking
- Visual interface for complex data types

### Best Practices
1. Use basic types (string, number, boolean, dateTime) for maximum compatibility
2. For computed fields in n8n, calculate values in Code nodes before insertion
3. For metadata fields in n8n, add workflow logic to track creation/modification
4. Keep schemas simple to ensure easy migration between backends
5. Document any backend-specific features used
