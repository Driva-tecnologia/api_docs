# Welcome to Driva's Dev Portal

This site contains the documentation for the Driva APIs available for our clients.
The API follows REST conventions with resource-oriented URLs, accepts [JSON-encoded](https://www.json.org/json-en.html) and returns JSON-encoded responses.

# Authentication
We use JWT tokens to authenticate requests. 
You can generate one using the `/v1/users/authenticate` route, check the [docs](./auth.md) on how to use.

!!! danger
    Your token is your identity inside our system, so keep it secure and never share it.

You **must** provide the token in a header like so:

@@@ GET https://api.driva.io/api/v1/users/validate
    Authorization: Bearer $TOKEN


# Errors
We use standard [HTTP response status codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) to indicate if your request succeeded or failed. Below is a summary of the status code that may be returned

| **Status Code** 	|                               **Description**                               	|
|:---------------:	|:---------------------------------------------------------------------------:	|
| 200             	| Everything worked                                                           	|
| 400             	| Invalid request, maybe a parameter is missing                               	|
| 422             	| The request failed some business logic, maybe you don't have enough credits 	|
| 401             	| The request is missing the **Authorization** Header                         	|
| 403             	| You don't have permission, maybe your asked for a premium field             	|
| 5XX             	| Something went wrong on our side.                                           	|

In case of an error `status_code != 200` the response will contain a body like the following
```json
{
    "message": "Human-readable message about the error",
    "help": "Some message to help solve the error, when possible"
}
```

# Pagination
All routes that may return a large payload are paginated, for example `/v1/companies/search` or `/v1/lawsuits/search`.
The pagination is controlled by two parameters:

- size - How many records should be returned. Defaults to **10**. 
    - Defaults to **10**.
    - There may be a limit for this parameter.
- page - Which page to fetch, starting from page 0. Defaults to **0**
    - Defaults to **0**.
    - If there are only **N** pages, asking for anything greater than **N** returns the last page.

# Costs
There are 2 types of credits available for you as a client, **visualization** credits and **solicitation** credits, which credit will be consumed depends on the route.

More details on how your request will be charged can be found on the specific documentation for the route. 

!!! note
    If there is nothing about cost in the documentation of the route it is free.