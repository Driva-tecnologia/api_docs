# Count companies API 

## Endpoint:  `GET /v1/companies/count`
Returns how many companies match your query.

## Query parameters

### exclude_exported_after
(Optional, date) Any company that was exported after the date will be **excluded** from the results.

Defaults to not exclude any company.

### extra_file
(Optional, string) Name of the file to be used in combination with the query. 
This file may be an Excel or CSV file and **must** have only one column.

!!! note
    The file will be combined with the query using an `$and` operator, meaning the companies must match both the file and the query.



### extra_file_mode
(Optional, string) Defines how to combine the file (if passed) with the query.
Must be one of:

- `include` - The values of the file will be put inside and `$in` operator 
- `exclude` - The values of the file will be put inside and `$nin` operator 

#### Example
If the file passed in `extra_file` is the following
```csv
cnpj
191
272
```

If you use `extra_file_mode=include` the only companies that will be returned are the ones that match the query AND the `cnpj` **IS** one of the values in the list.

If you use `extra_file_mode=exclude` the only companies that will be returned are the ones that match the query AND the `cnpj` **IS NOT**  one of the values in the list.

## Request body
You can send an optional JSON payload to customize the result. There are 2 optional keys in the payload.

### query
(Optional, object) Defines which companies to return using the [Query DSL](../dsl/query_dsl.md) 


### runtime_fields
(Optional, object) Defines some scripts to create new fields at runtime using the [Runtime DSL](../dsl/runtime_dsl.md) 
!!! Idea
    Save the runtime_fields with another route and reuse (like the mappings)
