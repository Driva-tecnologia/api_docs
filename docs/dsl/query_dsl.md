# Query DSL
The Query DSL was heavily inspired by the [MongoDB DSL](https://www.mongodb.com/docs/manual/tutorial/query-documents/) and the [Elasticsearch DSL](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl.html).

Our DSL, just like Elastic's, has two types of clauses:

- Leaf clauses - Apply directly to fields
- Compound clauses - Apply to a Leaf clause or another compound clause

Almost all queries are made of operators and operands, the operators are identified by the `$` symbol, like `$all` or `$exists`.


## $all
### Description
This operator is a **no-op**, it does not filter anything.

### Usage
If you don't send a request body this is the default query. Or you may be explicit:

```json
{"$all": {}}
```

## $field
### Description
Matches documents where the field's value is the given value.

!!! note
    The match may not be exact depending on the type of field your are trying to match. Check the docs about [mappings](../mappings.md) for details.

### Usage
=== "Explicit Numeric Field "
    ```json
    {
        "$field": {
            "cnpj" : 191
        }
    }
    ```
    !!! note
        The $field is optional, the other examples will omit it.
=== "Numeric field"
    ```json
    {
        "cnpj": 191
    }
    ```
=== "Text field"
    ```json
    {
        "razao_social": "Driva tecnologia"
    }
    // "DRIVA TECNOLOGIA" or some variation would also match
    ```
=== "Exact Text field"
    ```json
    {
        "situacao_cadastral": "ATIVA"
    }
    // "Ativa" or any other variation would NOT match
    ```


## $not
### Description
Matches documents where the field's value is NOT the given value

### Usage
=== "Numeric field"
    ```json
    {
        "cnpj": {
            "$not": 191
        }
    }
    ```
=== "Text field"
    ```json
    {
        "situacao_cadastral": {
            "$not": "ATIVA"
        }
    }
    ```
## $exists
### Description
Matches documents based on the existence of the field.

### Usage
=== "Field must exist"
    ```json
    {
        "telefones": {
            "$exists": true
        }
    }
    ```
=== "Field must not exist"
    ```json
    {
        "telefones": {
            "$exists": false
        }
    }
    ```
## $lt, $lte, $gt, $gte
## Description
Matches document where the field's value is within the range given.

### Usage
=== "Greater than"
    ```json
    {
        "capital": {
            "$gt": 1000000000
        }
    }
    ```
=== "Less than or equal"
    ```json
    {
        "qtde_filiais": {
            "$lte": 5
        }
    }
    ```
!!! warning
    The field must be numeric or a date to be used with this operator.

## $near
## Description
Matches documents where the field's value in geographically close to the given coordinates.

### Usage
```json
{
    "coords": {
        "$near": {
            "lat": -17,
            "lon": -48,
            "distance_km": 1000
        }
    }
}
```
!!! warning
    - The field must be geopoint to be used with this operator.

    - If the document does not have the field it will no match, since its distance is considered to be infinite. 

## $in / $nin
### Description
Matches documents where the field's value is (isn't for `$nin`) in the list.

!!! check
    This operators are just for convenience, since their behavior could be constructed with other operators.
    
    For example, `$in` may be replaced by an `$or` 
!!! warning
    The field must support exact matching to be used with this operator.

### Usage
=== "In the list"
    ```json
    {
        "cnpj": {
            "$in": [191, 272]
        }
    }
    // Matches documents where "cnpj" is one of the values in the list
    ```
=== "NOT in the list"
    ```json
    {
        "situacao_cadastral": {
            "$nin": ["BAIXADA", "INAPTA", "SUSPENSA"]
        }
    }
    // Matches documents where "situacao_cadastral" is none of the values in the list
    ```
## $and
### Description
Matches documents that match ALL the inner queries

### Usage
```json
{
    "$and": [
        {"situacao_cadastral": "ATIVA"},
        {"cnpj": {"$in": [191, 272]}}
        {"telefones": {"$exists": true}}
    ]
}
```

## $or
### Description
Matches documents that match AT LEAST ONE of the inner queries

### Usage
```json
{
    "$or": [
        {"situacao_cadastral": "SUSPENSA"},
       {"matriz": true}
    ]
}
```

!!! note
    Remember that for the same field the `$in` operator is preferred. 
    Only use this operator for the same field if it need fuzzy matching.
 
## $nand
### Description
Matches documents that DO NOT match ALL of the inner queries at same time. 

This is just and $and negated/inverted.
### Usage
```json
{
    "$nand": [
        {"situacao_cadastral": "ATIVA"},
        {"sigla_uf": "SP"}
    ]
}
```

## $nor
### Description
Matches documents that DO NOT match ANY of the inner queries.

This is just and $or negated/inverted.
### Usage
```json
{
    "$nor": [
        {"situacao_cadastral": "ATIVA"},
        {"sigla_uf": "SP"}
    ]
}
```

## $simple_query
### Description
This is a wrapper for the [Simple query string query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-simple-query-string-query.html from Elasticsearch.

The contents will be sent directly to the `query` and `fields` parameters of resulting Elasticsearch query. Check their documentation for the syntax available. 

### Usage
=== "Basic"
    ```json
    {
        "$simple_query": "tecnologia"
    }
    // Matches any document that has the word "tecnologia" inside
    ```
=== "Selecting field"
    ```json
    {
        "$simple_query": {
            "query": "tecnologia",
            "fields": ["razao_social", "nome_fantasia", "cnae_principal_desc_subclasse"]
        }
    }
    // You can restrict wich fields to search, you can use regex.
    ```
=== "Boosting field"
    ```json
    {
        "$simple_query": {
            "query": "tecnologia",
            "fields": ["razao_social^5", "nome_fantasia^2", "cnae_principal_desc_subclasse"]
        }
    }
    // You can give more importance to some fields.
    ```
## $query_string
### Description
This is a wrapper for the [Query string query](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html) from Elasticsearch.

The contents will be sent directly to the `query` parameter of resulting Elasticsearch query. Check their documentation for the syntax available. 
### Usage
```json
{
    "$query_string": "razao_social:(padaria OR confeitaria)"
}
```

