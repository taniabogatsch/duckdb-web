---
layout: community_extension_doc
title: ulid
excerpt: |
  DuckDB Community Extensions
  ULID data type for DuckDB

extension:
  name: ulid
  description: ULID data type for DuckDB
  version: 1.0.0
  language: C++
  build: cmake
  license: MIT
  maintainers:
    - Maxxen

repo:
  github: Maxxen/duckdb_ulid
  ref: b8368f646d57aa1bc73a8fee37621fcb87e4ccd2

docs:
  hello_world: |
    SELECT ulid() as result;
  extended_description: |
    This extension adds a new `ULID` data type to DuckDB. 
    A [ULID](https://github.com/ulid/spec) is similar to a UUID except that it also contains a timestamp component, which makes it more suitable for use cases where the order of creation is important. 
    Additionally, the string representation is lexicographically sortable while preserving the sort order of the timestamps.

extension_star_count: 7

---

### Installing and Loading
```sql
INSTALL {{ page.extension.name }} FROM community;
LOAD {{ page.extension.name }};
```

{% if page.docs.hello_world %}
### Example
```sql
{{ page.docs.hello_world }}```
{% endif %}

{% if page.docs.extended_description %}
### About {{ page.extension.name }}
{{ page.docs.extended_description }}
{% endif %}

### Added Functions

<div class="extension_functions_table"></div>

| function_name  | function_type | description | comment | example |
|----------------|---------------|-------------|---------|---------|
| ulid           | scalar        |             |         |         |
| ulid_epoch_ms  | scalar        |             |         |         |
| ulid_timestamp | scalar        |             |         |         |

### Added Types

<div class="extension_types_table"></div>

| type_name | type_size | logical_type | type_category | internal |
|-----------|----------:|--------------|---------------|---------:|
| ULID      | 16        | UHUGEINT     | NUMERIC       | true     |



---
