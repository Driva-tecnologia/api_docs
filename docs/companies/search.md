# Search companies API 

## Endpoint:  `POST /v1/companies/search`
Returns the companies that match your query.

## Query parameters
### mapping
(Optional, string) Name of the mapping to apply in the companies before returning them.
This is used to rename the fields and also to select which ones you want.

Defaults to return all fields with the same name used in the database.

### size
(Optional, int) How many companies should be returned. This value should be positive and less than **10000**

Defaults to 10.

### page
(Optional, int) Which page to fetch. This value should be positive.

Defaults to 0.

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
You can send an optional JSON payload to customize the result. There are 3 optional keys in the payload.

### query
(Optional, object) Defines which companies to return using the [Query DSL](../dsl/query_dsl.md) 

### transform_fields
(Optional, object) Defines some transformations to be applied in place for the fields using the [Transform DSL](../dsl/transforms_dsl.md)
!!! Idea
    Save the transformations with another route and reuse (like the mappings)

### runtime_fields
(Optional, object) Defines some scripts to create new fields at runtime using the [Runtime DSL](../dsl/runtime_dsl.md) 
!!! Idea
    Save the runtime_fields with another route and reuse (like the mappings)


## Cost
If you only ask for a subset of the following fields your request is free:

* cnpj
* razao_social
* nome_fantasia
* matriz
* uf
* situacao_cadastral

!!! check
    There is a mapping already created for this fields called `basic_free`, so if you send `mapping=basic_free` your request will **NOT** be charged.

Requesting companies from our Telegram Bot **LIDIA** will not cost any credit, no matter the fields requested.

!!! danger
    If you request any field outside of those you will be charged **1** credit for each company returned. 

    The type of the credit will be **visualization**, unless you asked for an export on the App, in which case the type of credit consumed will be **solicitation**. 