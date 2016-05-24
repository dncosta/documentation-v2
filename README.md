# Moip V2 | API Reference - english

### About this documentation

This is an english version of Moip V2 API reference. 
This documentation is based on the portuguese version and may not contain all details covered in the original version, 
but it centainlly observes the main aspects of the APIs. 

We strongly recommend you to take a look at the Getting Started topics [here](dev.moip.com.br/docs), 
if you have any questions or need any help just send an email to [dev@moip.com.br](dev@moip.com.br) 
and we'll gladly take some time and make you start playing the game. ;)

### RESTful

V2 APIs are base on RESTful architecture design. Our API resouces are named after the objects they represent (like orders, payments, customers, refunds, etc.) and we support many HTTP verbs for each resource.
This way, one recource (for example: "v2/customers/") can be used to create a customer through the POST method as well as listing customers through GET method.
The APIs were designed with the hypermedia concept (HATEOAS) so you can use our links structure between resources to automate you application and simplify your integration.

### Error states

Every API request will return a HTTP status and a JSON showing the objects you have intected with.

Attributes

name | description  | details
------------ | ------------- | ------------- 
code | ERROR ID | string
path | Attribute related to the error | string
description | A brief description (in portuguese) | string

All mapped errors will return a http status 4xx to your application. If you get a 5xx error, please send us an email giving details of your request and we'll fix it asap.

 * 400 errors - Must be treated from your application. The APIs will return a JSON with details of the problem.
 * 401 errors - Indicates an authentication problem. Make sure you are using the right keys to authenticate and if you are pointing to the right environment.
 * 500 errors - These are internal errors, that shouldn't happen but if it does please send an email to [dev@moip.com.br](dev@moip.com.br)
 
Example:

```
{
  "errors": [
    {
      "code": "ORD-001",
      "path": "ownId",
      "description": "É necessario informar seu identificador próprio"
    }
  ]
}
```

### Response pattern

For `POST` and `GET` methods you will receive a full representation of the resource requested, except by null or empty attributes which will be ommited.
In case of nested resources (like payments inside orders) when consulting the main resource you'll also receive a minimal view of the nested one. The minimal view is small version of a resource, containing only the most relevant attributes.

### Listing and Searching

You can also search for some objects like payments and orders, to do so you have 3 parameters to refine your request.

* Pagination - Determines how many objects will be returned.
* Generic search - Search a string that you have used to create a resource (IDs, Emails, Name, etc)
* Filters - Specifies and segments the search.

#### Pagination

name | type  | description
---- | ----- | ------------- 
limit | integer | Number of objects returned (by page). Default value is 20.
offset | integer | The resource where the search started. Default value is 0.

Example:

`GET: v2/recurso?limit=100&offset=300`

#### Generic search

This is the easiest method to search for a resource inside the APIs.

name | description
---- | -------------
q | Search for a specific string.

Example: 

`GET: v2/recurso?q=meu_id_proprio`

#### Filters

Filters can be used to refine your search, for example you can request orders on a specific status or between dates.
Not all resources have attributes available for filtering. Take a look at the resource you want to filter to know what attributes you can use as a parameter.

Delimiters:

The URI must be built with the attributes using delimiters inside the parameter `filters`.

name | type | description
---- | ---- | -----------
gt(x)	| number or date	| “Greater Than”
ge(x)	| number or date	| “Greater than or Equal”
lt(x)	| number or date	| “Less Than”
le(x)	| number or date	| “Less than or Equal”
bt(x,y)	| string	| - “BeTween”
in(x,y…z)	| string	| - “IN”

### Environments

All examples available in this documentation are refering to the `sandbox` environment where real transacions are not possible.
However, the application of Moip's `sandbox` environment behaves exactly like the `production` environment.

Once you finish to integrate with the sandbox you need to change your API keys and URI to point to the production environment.

`Production: https://api.moip.com.br`
`Sandbox: https://sandbox.moip.com.br`



# CUSTOMERS


