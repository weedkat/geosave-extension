# GeoSave Extension Specification

- Title: GeoSave
- Version: 0.1.0
- Identifier: `https://raw.githubusercontent.com/weedkat/geosave-extension/v0.1.0/json-schema/schema.json`
- Field Name Prefix: `geosave`
- Scope: Item

This extension adds a shared stack identifier to
[STAC](https://github.com/radiantearth/stac-spec) Items saved by GeoSave.
It follows the repository structure of the
[Earthquake extension](https://github.com/stac-extensions/earthquake).

- [JSON Schema](json-schema/schema.json)
- Examples: [reflectance Item](examples/reflectance-item.json) and
  [indices Item](examples/indices-item.json), saved from the same stack
- [Changelog](CHANGELOG.md)

## Fields

The field belongs in an Item's `properties` object.

| Field Name | Type | Description |
| ---------- | ---- | ----------- |
| geosave:stack | string | Optional. Nonempty identifier of the stack this Item was saved from, shared by Items saved from its groups. |

### geosave:stack

When GeoSave saves groups from one stack as separate Items, those Items share
the same `geosave:stack` value. Clients can use equality on this field to query
the Items together, even though their Item IDs and assets differ.
Assign different identifiers to unrelated stacks within the catalog being queried.

For example, the reflectance and indices Items both contain:

```json
{
  "geosave:stack": "s2-2026-10-09"
}
```

In a table that exposes Item properties as columns, select both groups with:

```sql
SELECT *
FROM items
WHERE "geosave:stack" = 's2-2026-10-09';
```

The field may be omitted. If present, it must be a string with at least one
character; empty strings, `null`, numbers, arrays, and objects are invalid.

## Usage

Include the identifier in the Item's `stac_extensions` array and put the stack
identifier in `properties`:

```json
{
  "stac_extensions": [
    "https://raw.githubusercontent.com/weedkat/geosave-extension/v0.1.0/json-schema/schema.json"
  ],
  "type": "Feature",
  "properties": {
    "datetime": "2026-10-09T03:00:00Z",
    "geosave:stack": "s2-2026-10-09"
  }
}
```

This is a fragment; the examples contain complete STAC Items.
The extension schema validates the extension declaration, Item type, and stack
field. Validate the Item against the STAC core schema as well. Other Item
properties and extension declarations are allowed.

## Publishing

The schema identifier points to the `v0.1.0` Git tag in this repository.
It will resolve after the schema is committed and that tag is pushed to GitHub.
Use a new version tag and update the identifier for future releases.
