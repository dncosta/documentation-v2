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

The customer is the user of a service or a person buying a product. This API allows you to create a retrieve a customer objetc.

Customer attributes:

name | decription | details
---- | ---------- | -------
id | 	Moip customer's ID.	| string(16), response
ownId	| Customer's ownId . External reference.	| string(65)
fullname |	Customer's fullname.	| string(90)
email	| Customer's email.	| string(45)
phone	| Customer's phone.	| structured
├─countryCode | 	Country code. Possible value: 55. |	integer(2)
├─areaCode	| Areacode. |	integer(2)
└─number	| Phone number. |	integer(9)
birthDate	| Customer's birth date.	| date (AAAA-MM-DD)
taxDocument |	Personal documentation.	| structured
├─type	| Type of document. Possible value: `CPF` for social security number, `CNPJ` for tax identification number. |	string
└─number	| Document number.	| string(11)
shippingAddress |	Shipping address. |	Object Address
fundingInstruments	| Funding instruments. |	structured list
├─method	| Method used. Possible values: CREDIT_CARD. | 	string
└─creditCard |	Credit card. |	object CreditCard
createdAt	| Date when the resource were created.	| datetime, response
_links |	Resource links.	| structured, response
├─self	| Hyperlink to the resource itself.	| structured
└─└─href	| URI. |	link

## Create a customer - POST

### Endpoint

**POST:** `https://sandbox.moip.com.br/v2/customers`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{
  "ownId": "qwertyu",
  "fullname": "Ken Masters",
  "email": "ken.masters0@email.com",
  "birthDate": "1988-12-30",
  "taxDocument": {
    "type": "CPF",
    "number": "22222222222"
  },
  "phone": {
    "countryCode": "55",
    "areaCode": "11",
    "number": "66778899"
  },
  "shippingAddress": {
    "city": "Sao Paulo",
    "complement": "8",
    "district": "Itaim",
    "street": "Avenida Faria Lima",
    "streetNumber": "2927",
    "zipCode": "01234000",
    "state": "SP",
    "country": "BRA"
  }
}
```
**RESPONSE**
```
201 (Created)
Content-Type: application/json

{
  "id": "CUS-Y6L4AGQN8HKQ",
  "ownId": "meu_id_sandbox_1231234",
  "fullname": "Jose Silva",
  "createdAt": "2015-01-14T11:28:22-0200",
  "birthDate": "1988-12-30",
  "email": "jose_silva0@email.com",
  "phone": {
    "countryCode": "55",
    "areaCode": "11",
    "number": "66778899"
  },
  "taxDocument": {
    "type": "CPF",
    "number": "22222222222"
  },
  "shippingAddress": {
    "zipCode": "01234000",
    "street": "Avenida Faria Lima",
    "streetNumber": "2927",
    "complement": "8",
    "city": "Sao Paulo",
    "district": "Itaim",
    "state": "SP",
    "country": "BRA"
  },
  "_links": {
    "self": {
      "href": "https://sandbox.moip.com.br/v2/customers/CUS-Y6L4AGQN8HKQ"
    }
  }
}
```

**Parameters:**

name | decription | details
---- | ---------- | -------
ownId	| Customer's ownId . External reference.	| string(65), **mandatory**
fullname |	Customer's fullname.	| string(90), **mandatory**
email	| Customer's email.	| string(45), **mandatory**
phone	| Customer's phone.	| structured, **optional**
├─countryCode | 	Country code. Possible value: 55. |	integer(2), **optional**
├─areaCode	| Areacode. |	integer(2), **optional**
└─number	| Phone number. |	integer(9), **optional**
birthDate	| Customer's birth date.	| date (AAAA-MM-DD), **optional**
taxDocument |	Personal documentation.	| structured, **optional**
├─type	| Type of document. Possible value: `CPF` for social security number, `CNPJ` for tax identification number. |	string, **optional**
└─number	| Document number.	| string(11), **optional**
shippingAddress |	Shipping address. |	Object Address, **optional**
fundingInstruments	| Funding instruments. |	structured list, **optional**
├─method	| Method used. Possible values: CREDIT_CARD. | 	string, **optional**
└─creditCard |	Credit card. |	object CreditCard, **optional**


## Retrieve a customer - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/customers/{customer_id}`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="
``` 

**RESPONSE:** 
```
200 (OK)
Content-Type: application/json

 {
  "id": "CUS-Y6L4AGQN8HKQ",
  "ownId": "meu_id_sandbox_1231234",
  "fullname": "Jose Silva",
  "createdAt": "2015-01-14T11:28:22-0200",
  "birthDate": "1988-12-30T00:00:00-0200",
  "email": "jose_silva0@email.com",
  "phone": {
    "countryCode": "55",
    "areaCode": "11",
    "number": "66778899"
  },
  "taxDocument": {
    "type": "CPF",
    "number": "22222222222"
  },
  "shippingAddress": {
    "zipCode": "01234000",
    "street": "Avenida Faria Lima",
    "streetNumber": "2927",
    "complement": "8",
    "city": "Sao Paulo",
    "district": "Itaim",
    "state": "SP",
    "country": "BRA"
  },
  "_links": {
    "self": {
      "href": "https://sandbox.moip.com.br/v2/customers/CUS-Y6L4AGQN8HKQ"
    }
  }
}
```

**Parameters:**

name | 	description | details 
---- | ------------ | --------
id	| hash of customer id.	| string(16), **mandatory**

## Adding a credit card - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/customers/{customer_id}/fundinginstruments`

**REQUEST:**

```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{
  "method": "CREDIT_CARD",
  "creditCard": {
    "expirationMonth": "05",
    "expirationYear": "18",
    "number": "5555666677778884",
    "cvc": "123",
    "holder": {
      "fullname": "Jose Portador da Silva",
      "birthdate": "1988-12-30",
      "taxDocument": {
        "type": "CPF",
        "number": "33333333333"
      },
      "phone": {
        "countryCode": "55",
        "areaCode": "11",
        "number": "66778899"
      }
    }
  }
}
``` 

**RESPONSE:**
```
201 (Created)
Content-Type: application/json

{
  "method": "CREDIT_CARD",
  "creditCard": {
    "id": "CRC-NLBLOTNB6LLT",
    "brand": "MASTERCARD",
    "first6": "555566",
    "last4": "8884"
  }
}
```

**Parameters:**

name | 	description | details 
---- | ------------ | --------
id	| hash of customer id.	| string(16), **mandatory**
├─method	| Method used. Possible values: CREDIT_CARD. | 	string, **optional**
└─creditCard |	Credit card. |	object CreditCard, **optional**
phone	| Customer's phone.	| structured, **optional**
├─countryCode | 	Country code. Possible value: 55. |	integer(2), **optional**
├─areaCode	| Areacode. |	integer(2), **optional**
└─number	| Phone number. |	integer(9), **optional**

# ORDERS

Order is the representation of the product or service being sold. This API allows you to create and retrieve a customer.

Attributes:

name	| description	| details
---- |------------ | -------
id | 	Order ID.	| string(16), response
ownId	| Own id of an order. External reference.	| string(65)
status	| Orders status. Possible values: CREATED, WAITING, PAID, NOT_PAID, REVERTED.	| string, response
createdAt |	Date when the order was created.	| datetime, response
amount	| Order amount.	| structured
├─total	| Total amount charged in cents. Ex: R$10,32 must be informed as 1032 	| integer(12), response
├─fees	| Moip fees.	| integer(12), response
├─refunds |	Total amount refunded.| 	integer(12), response
├─liquid	| Liquid amount.	| integer(12), response
├─otherReceivers |	Sum of total amount sent to other receivers. Used by marketplaces. |	integer(12), response
├─currency	| Currency. Possible values: BRL. |	string
├─subtotals	| Aditional values.	| structured
├ ├─shipping	| Shipping cost. It will be added to the items amount.  Ex: R$10,32 must be informed as 1032 | integer(12)
├ ├─addition	| Adition amount. It will be added to the items amount. Ex: R$10,32 must be informed as 1032 | integer(12)
├ ├─discount	| Discount amount. It will be deducted from the total amount. Ex: R$10,32 must be informed as 1032 | integer(12)
└ └─items	| Sum of the amount from all items.	| integer(12), response
items	| Items structure.	| structured list
├─product |	Name of the product.	| string(256)
├─quantity	| Quantity of products.	| integer(12)
├─detail	| Description.	| string(256)
└─price	| Inicial amount. (The value is multiplied according to the number of products.) Ex: R$10,32 must be informed as 1032 	| integer(12)
checkoutPreferences |	Checkout setup.	| structured
├─redirectUrls	| Redirect URLs.	| string(256)
├ ├─urlSuccess	| Redirec URL for success payments.	| link
└ └─urlError	| Redirec URL for failed payments.	| link
├─installments	| Installments setup.	| structured list
├ ├─quantity	| Delimiters for installments. Exemple: [1, 3];	| tupla
├ ├─discount	| Discount for installments number.	| integer
└ └─addition	| Adition for installments number.	| integer
shippingAddress	| Shipping address.	| structured Object: Address, response
customer	| Customer related to the order.	| Cliente
payments	| Payments attached to the order. For more information take a lookt at the resource payments.	| Payments collection, response
refunds	| Refunds attached to the order. For more information take a lookt at the resource payments.	| Refunds collection, response
entries	| Payments related to the order. For more information take a lookt at the resource payments.	| Entries colletion, response
events	| Events related to the order.	| structured list, response
├─createdAt |	Event date.	| date(AAAA-MM-DD), response
├─type	| Event type. Possible values: ORDER.CREATED, ORDER.WAITING, ORDER.PAID, ORDER.NOT_PAID, ORDER.REVERTED.	| string, response
└─description |	Event description.	| string(65), response
receivers	| Receivers structure.	| structured list
├─type	| Receiver type. Possible values: PRIMARY, SECONDARY	| string
├─moipAccount |	Structure of Moip Account that will receive a payment.	| structured
├ ├─login	| Login.	| string(256)
├ ├─fullname |	Name of the account holder.	| string(256)
├ └─id	| Account ID.	| string(16)
├─amount	| Structed of values that will be received.	| structured
├ ├─refunds |	Total amount refunded from this receiver.	| integer(12)
├ ├─fees	| Total fees charged from this receiver.	| integer(12)
└ └─total	| Total received. Ex: R$10,32 must be informed as 1032 	| integer(12)
updatedAt	| Date when the resource was last updated.	| datetime, response
_links |	Resource links.	| structured, response
├─self	| Hyperlink to the resource itself.	| structured
└─└─href	| URI. |	link
└─checkout	| Links to Moip checkout.	| object Checkout Moip

## Create an order - POST

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/orders`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{
  "ownId": "seu_identificador_proprio",
  "amount": {
    "currency": "BRL",
    "subtotals": {
      "shipping": 1000
    }
  },
  "items": [
    {
      "product": "Descrição do pedido",
      "quantity": 1,
      "detail": "Mais info...",
      "price": 1000
    }
  ],
  "customer": {
    "ownId": "seu_identificador_proprio_de_cliente",
    "fullname": "Jose Silva",
    "email": "nome@email.com",
    "birthDate": "1988-12-30",
    "taxDocument": {
      "type": "CPF",
      "number": "22222222222"
    },
    "phone": {
      "countryCode": "55",
      "areaCode": "11",
      "number": "66778899"
    },
    "shippingAddress": {
      "street": "Avenida Faria Lima",
      "streetNumber": 2927,
      "complement": 8,
      "district": "Itaim",
      "city": "Sao Paulo",
      "state": "SP",
      "country": "BRA",
      "zipCode": "01234000"
    }
  }
}

``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "ORD-NY92W6N4S220",
  "ownId": "seu_identificador_proprio",
  "status": "CREATED",
  "createdAt": "2015-01-14T11:25:10-0200",
  "amount": {
    "total": 2000,
    "fees": 0,
    "refunds": 0,
    "liquid": 0,
    "otherReceivers": 0,
    "currency": "BRL",
    "subtotals": {
      "shipping": 1000,
      "addition": 0,
      "discount": 0,
      "items": 1000
    }
  },
  "items": [
    {
      "detail": "Mais info...",
      "quantity": 1,
      "price": 1000,
      "product": "Descrição do pedido"
    }
  ],
  "customer": {
    "id": "CUS-73HWY5J45TLO",
    "ownId": "seu_identificador_proprio_de_cliente",
    "fullname": "Jose Silva",
    "createdAt": "2015-01-14T11:25:10-0200",
    "birthDate": "1988-12-30T00:00:00-0200",
    "email": "nome@email.com",
    "phone": {
      "countryCode": "55",
      "areaCode": "11",
      "number": "66778899"
    },
    "taxDocument": {
      "type": "CPF",
      "number": "22222222222"
    },
    "shippingAddress": {
      "zipCode": "01234000",
      "street": "Avenida Faria Lima",
      "streetNumber": "2927",
      "complement": "8",
      "city": "Sao Paulo",
      "district": "Itaim",
      "state": "SP",
      "country": "BRA"
    },
    "_links": {
      "self": {
        "href": "https://sandbox.moip.com.br/v2/customers/CUS-73HWY5J45TLO"
      }
    }
  },
  "payments": [],
  "refunds": [],
  "entries": [],
  "events": [
    {
      "createdAt": "2015-01-14T11:25:10-0200",
      "description": "",
      "type": "ORDER.CREATED"
    }
  ],
  "receivers": [
    {
      "amount": {
        "fees": 0,
        "refunds": 0,
        "total": 2000
      },
      "moipAccount": {
        "fullname": "Moip SandBox",
        "login": "integracao@labs.moip.com.br",
        "id": "MPA-CULBBYHD11"
      },
      "type": "PRIMARY"
    }
  ],
  "shippingAddress": {
    "zipCode": "01234000",
    "street": "Avenida Faria Lima",
    "streetNumber": "2927",
    "complement": "8",
    "city": "Sao Paulo",
    "district": "Itaim",
    "state": "SP",
    "country": "BRA"
  },
  "_links": {
    "self": {
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-NY92W6N4S220"
    },
    "checkout": {
      "payOnlineBankDebitItau": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/itau/ORD-NY92W6N4S220"
      },
      "payOnlineBankDebitBB": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/bancobrasil/ORD-NY92W6N4S220"
      },
      "payCreditCard": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/creditcard/ORD-NY92W6N4S220"
      },
      "payOnlineBankDebitBradesco": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/bradesco/ORD-NY92W6N4S220"
      },
      "payBoleto": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/boleto/ORD-NY92W6N4S220"
      },
      "payOnlineBankDebitBanrisul": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/banrisul/ORD-NY92W6N4S220"
      }
    }
  }
}
```

## Retrieve an order - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/orders/{order_id}`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="
``` 

**RESPONSE:** 
```
200 (OK)
Content-Type: application/json

{
  "id": "ORD-4HY0KOA9Q73F",
  "ownId": "meu_pedido_sandbox_xxxxx",
  "status": "CREATED",
  "createdAt": "2015-01-14T11:39:15-0200",
  "amount": {
    "total": 2000,
    "fees": 0,
    "refunds": 0,
    "liquid": 0,
    "otherReceivers": 0,
    "currency": "BRL",
    "subtotals": {
      "shipping": 1000,
      "addition": 0,
      "discount": 0,
      "items": 1000
    }
  },
  "items": [
    {
      "detail": "Mais info...",
      "quantity": 1,
      "price": 1000,
      "product": "Produto de testes Sandbox - xxxx"
    }
  ],
  "addresses": [
    {
      "city": "Sao Paulo",
      "complement": "8",
      "street": "Avenida Faria Lima",
      "streetNumber": "2927",
      "zipCode": "01234000",
      "district": "Itaim",
      "state": "SP",
      "type": "SHIPPING",
      "country": "BRA"
    }
  ],
  "customer": {
    "id": "CUS-Y6L4AGQN8HKQ",
    "ownId": "meu_id_sandbox_1231234",
    "fullname": "Jose Silva",
    "createdAt": "2015-01-14T11:28:22-0200",
    "birthDate": "1988-12-30T00:00:00-0200",
    "email": "jose_silva0@email.com",
    "phone": {
      "countryCode": "55",
      "areaCode": "11",
      "number": "66778899"
    },
    "taxDocument": {
      "type": "CPF",
      "number": "22222222222"
    },
    "addresses": [
      {
        "city": "Sao Paulo",
        "complement": "8",
        "street": "Avenida Faria Lima",
        "streetNumber": "2927",
        "zipCode": "01234000",
        "district": "Itaim",
        "state": "SP",
        "type": "SHIPPING",
        "country": "BRA"
      }
    ],
    "shippingAddress": {
      "zipCode": "01234000",
      "street": "Avenida Faria Lima",
      "streetNumber": "2927",
      "complement": "8",
      "city": "Sao Paulo",
      "district": "Itaim",
      "state": "SP",
      "country": "BRA"
    },
    "_links": {
      "self": {
        "href": "https://sandbox.moip.com.br/v2/customers/CUS-Y6L4AGQN8HKQ"
      }
    }
  },
  "payments": [],
  "refunds": [],
  "entries": [],
  "events": [
    {
      "createdAt": "2015-01-14T11:39:15-0200",
      "description": "",
      "type": "ORDER.CREATED"
    }
  ],
  "receivers": [
    {
      "amount": {
        "fees": 0,
        "refunds": 0,
        "total": 2000
      },
      "moipAccount": {
        "fullname": "Moip SandBox",
        "login": "integracao@labs.moip.com.br",
        "id": "MPA-CULBBYHD11"
      },
      "type": "PRIMARY"
    }
  ],
  "_links": {
    "self": {
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-4HY0KOA9Q73F"
    },
    "checkout": {
      "payOnlineBankDebitItau": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/itau/ORD-4HY0KOA9Q73F"
      },
      "payOnlineBankDebitBB": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/bancobrasil/ORD-4HY0KOA9Q73F"
      },
      "payCreditCard": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/creditcard/ORD-4HY0KOA9Q73F"
      },
      "payOnlineBankDebitBradesco": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/bradesco/ORD-4HY0KOA9Q73F"
      },
      "payBoleto": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/boleto/ORD-4HY0KOA9Q73F"
      },
      "payOnlineBankDebitBanrisul": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/banrisul/ORD-4HY0KOA9Q73F"
      }
    }
  }
}
```

## Retrieve orders list

### Endpoint

This endpoint allows you to list orders using filters.

**GET** `https://sandbox.moip.com.br/v2/orders`

Example with filters: `https://sandbox.moip.com.br/v2/orders?q=pagamento do pedido&filters=status::in(PAID,WAITING)|paymentMethod::in(CREDIT_CARD,BOLETO)|amount::bt(5000,10000)&limit=3&offset=0`

_**IMPORTANT:** Make sure you are using URL encode to make requests with filters._

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="
``` 

**RESPONSE:** 
```
200 (OK)
Content-Type: application/json

{
  "_links": {
    "next": {
      "href": "https://sandbox.moip.com.br/v2/orders?q=jose silva&filters=createdAt::bt(2014-11-08,2015-05-07)&limit=3&offset=3"
    },
    "previous": {
      "href": "https://sandbox.moip.com.br/v2/orders?q=jose silva&filters=createdAt::bt(2014-11-08,2015-05-07)&limit=3&offset=0"
    }
  },
  "summary": {
    "count": 497,
    "amount": 1914750
  },
  "orders": [
    {
      "id": "380561",
      "ownId": "sandbox_v2_1430950122",
      "externalId": "ORD-U052MFZ4M414",
      "status": "PAID",
      "blocked": false,
      "amount": {
        "total": 4000,
        "addition": 0,
        "fees": 335,
        "deduction": 0,
        "otherReceivers": 0,
        "currency": "BRL"
      },
      "receivers": [
        {
          "type": "PRIMARY",
          "moipAccount": {
            "id": "MPA-CULBBYHD11"
          }
        }
      ],
      "customer": {
        "fullname": "jose silva",
        "email": "sandbox_v2_1430950122@email.com"
      },
      "items": [
        {
          "product": null
        }
      ],
      "payments": [
        {
          "installmentCount": 1,
          "fundingInstrument": {
            "method": "CREDIT_CARD",
            "institution": "VISA"
          }
        }
      ],
      "events": [
        {
          "type": "PAYMENT.AUTHORIZED",
          "createdAt": "2015-05-06T19:09:06Z"
        }
      ],
      "_links": {
        "self": {
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-U052MFZ4M414"
        }
      },
      "createdAt": "2015-05-06T19:08:43Z",
      "updatedAt": "2015-05-06T19:08:48Z"
    },
    {
      "id": "380560",
      "ownId": "sandbox_v2_1430950121",
      "externalId": "ORD-8MFXXQTTPEJ4",
      "status": "PAID",
      "blocked": false,
      "amount": {
        "total": 4000,
        "addition": 0,
        "fees": 335,
        "deduction": 0,
        "otherReceivers": 0,
        "currency": "BRL"
      },
      "receivers": [
        {
          "type": "PRIMARY",
          "moipAccount": {
            "id": "MPA-CULBBYHD11"
          }
        }
      ],
      "customer": {
        "fullname": "jose silva",
        "email": "sandbox_v2_1430950121@email.com"
      },
      "items": [
        {
          "product": null
        }
      ],
      "payments": [
        {
          "installmentCount": 1,
          "fundingInstrument": {
            "method": "CREDIT_CARD",
            "institution": "VISA"
          }
        }
      ],
      "events": [
        {
          "type": "PAYMENT.AUTHORIZED",
          "createdAt": "2015-05-06T19:09:06Z"
        }
      ],
      "_links": {
        "self": {
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-8MFXXQTTPEJ4"
        }
      },
      "createdAt": "2015-05-06T19:08:41Z",
      "updatedAt": "2015-05-06T19:08:48Z"
    },
    {
      "id": "380456",
      "ownId": "sandbox_v2_1430942777",
      "externalId": "ORD-3XCFS4BTBO38",
      "status": "PAID",
      "blocked": false,
      "amount": {
        "total": 4000,
        "addition": 0,
        "fees": 335,
        "deduction": 0,
        "otherReceivers": 0,
        "currency": "BRL"
      },
      "receivers": [
        {
          "type": "PRIMARY",
          "moipAccount": {
            "id": "MPA-CULBBYHD11"
          }
        }
      ],
      "customer": {
        "fullname": "jose silva",
        "email": "sandbox_v2_1430942777@email.com"
      },
      "items": [
        {
          "product": null
        }
      ],
      "payments": [
        {
          "installmentCount": 1,
          "fundingInstrument": {
            "method": "CREDIT_CARD",
            "institution": "VISA"
          }
        }
      ],
      "events": [
        {
          "type": "PAYMENT.AUTHORIZED",
          "createdAt": "2015-05-06T17:06:53Z"
        }
      ],
      "_links": {
        "self": {
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-3XCFS4BTBO38"
        }
      },
      "createdAt": "2015-05-06T17:06:23Z",
      "updatedAt": "2015-05-06T17:06:42Z"
    }
  ]
}
```



## ACTION

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/customers/{customer_id}`

**REQUEST:**
```
``` 

**RESPONSE:** 
```
```
