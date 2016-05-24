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

Attributes:

name | decription | details
---- | ---------- | -------
id | 	Moip customer's ID.	| string(16), response
ownId	| Customer's ownId . External reference.	| string(65)
fullname |	Customer's fullname.	| string(90)
email	| Customer's email.	| string(45)
phone.countryCode | 	Country code. Possible value: 55. |	integer(2)
phone.areaCode	| Areacode. |	integer(2)
phone.number	| Phone number. |	integer(9)
birthDate	| Customer's birth date.	| date (AAAA-MM-DD)
taxDocument.type	| Type of document. Possible value: `CPF` for social security number, `CNPJ` for tax identification number. |	string
taxDocument.number	| Document number.	| string(11)
shippingAddress |	Shipping address. |	Object Address
fundingInstruments.method	| Method used. Possible values: CREDIT_CARD. | 	string
fundingInstruments.creditCard |	Credit card. |	object CreditCard
createdAt	| Date when the resource were created.	| datetime, response
_links.self.href	| URI to the resource. |	link

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

## Add a credit card - POST

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

# ORDERS

Order is the representation of the product or service being sold. This API allows you to create and retrieve a customer.

Attributes:

name	| description	| details
---- |------------ | -------
id | 	Order ID.	| string(16), response
ownId	| Own id of an order. External reference.	| string(65)
status	| Orders status. Possible values: CREATED, WAITING, PAID, NOT_PAID, REVERTED.	| string, response
createdAt |	Date when the order was created.	| datetime, response
amount.total	| Total amount charged in cents. Ex: R$10,32 must be informed as 1032 	| integer(12), response
amount.fees	| Moip fees.	| integer(12), response
amount.refunds |	Total amount refunded.| 	integer(12), response
amount.liquid	| Liquid amount.	| integer(12), response
amount.otherReceivers |	Sum of total amount sent to other receivers. Used by marketplaces. |	integer(12), response
amount.currency	| Currency. Possible values: BRL. |	string
amount.subtotals.shipping	| Shipping cost. It will be added to the items amount.  Ex: R$10,32 must be informed as 1032 | integer(12)
amount.subtotals.addition	| Adition amount. It will be added to the items amount. Ex: R$10,32 must be informed as 1032 | integer(12)
amount.subtotals.discount	| Discount amount. It will be deducted from the total amount. Ex: R$10,32 must be informed as 1032 | integer(12)
amount.subtotals.items	| Sum of the amount from all items.	| integer(12), response
items.product |	Name of the product.	| string(256)
items.quantity	| Quantity of products.	| integer(12)
items.detail	| Description.	| string(256)
items.price	| Inicial amount. (The value is multiplied according to the number of products.) Ex: R$10,32 must be informed as 1032 	| integer(12)
checkoutPreferences.redirectUrls.urlSuccess	| Redirec URL for success payments.	| link
checkoutPreferences.redirectUrls.urlError	| Redirec URL for failed payments.	| link
checkoutPreferences.installments.quantity	| Delimiters for installments. Exemple: [1, 3];	| tupla
checkoutPreferences.installments.discount	| Discount for installments number.	| integer
checkoutPreferences.installments.addition	| Adition for installments number.	| integer
shippingAddress	| Shipping address.	| structured Object: Address, response
customer	| Customer related to the order.	| Cliente
payments	| Payments attached to the order. For more information take a lookt at the resource payments.	| Payments collection, response
refunds	| Refunds attached to the order. For more information take a lookt at the resource payments.	| Refunds collection, response
entries	| Payments related to the order. For more information take a lookt at the resource payments.	| Entries colletion, response
events.createdAt |	Event date.	| date(AAAA-MM-DD), response
events.type	| Event type. Possible values: ORDER.CREATED, ORDER.WAITING, ORDER.PAID, ORDER.NOT_PAID, ORDER.REVERTED.	| string, response
events.description |	Event description.	| string(65), response
receivers.type	| Receiver type. Possible values: PRIMARY, SECONDARY	| string
receivers.moipAccount.login	| Login.	| string(256)
receivers.moipAccount.fullname |	Name of the account holder.	| string(256)
receivers.moipAccount.id	| Account ID.	| string(16)
receivers.amount.percentual | Percentual of the total amount that goes to the receiver. | interger
receivers.amount.fixed | Value of the total amount that goes to the receiver. | interger
receivers.amount.refunds |	Total amount refunded from this receiver.	| integer(12)
receivers.amount.fees	| Total fees charged from this receiver.	| integer(12)
receivers.amount.total	| Total received. Ex: R$10,32 must be informed as 1032 	| integer(12)
updatedAt	| Date when the resource was last updated.	| datetime, response
_links.self.href	| URI to the resource. |	link
_links.checkout	| Links to Moip checkout.	| object Checkout Moip

## Create an order - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/orders`

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

## Retrieve a list of orders - GET

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

# PAYMENTS

A payment represents the financial transaction that happens between a customer and a seller through a credit card, payment slip or any other method. This API allows you to create and retrieve payments.

Attributes:

name | description | type
---- | ----------- | ----
id | Payment ID | string(16), response
status | Status of a payment. Possible values: CREATED,WAITING, IN_ANALYSIS, PRE_AUTHORIZED,AUTHORIZED, CANCELLED, REFUNDED, REVERSED, SETTLED. | string, response
amount.total	| Total amount charged in cents. Ex: R$10,32 must be informed as 1032 	| integer(12), response
amount.fees	| Moip fees.	| integer(12), response
amount.refunds |	Total amount refunded.| 	integer(12), response
amount.liquid	| Liquid amount.	| integer(12), response
amount.currency	| Currency. Possible values: BRL. |	string
amount.subtotals	| Aditional values.	| structured
installmentCount	| Number of installments. Minimum 1 and maximum 12.	| integer(2)
delayCapture	| Used if you need to pre-capture a payment. Only available for credit cards. |	boolean
fundingInstruments.method	| Method used. Possible values: `CREDIT_CARD`, `BOLETO`, `ONLINE_BANK_DEBIT`, `WALLET` | 	string
fundingInstruments.creditCard.id | Credit card ID. This ID can be used in the future to create new payments. Internal reference. | string, conditional 
fundingInstruments.creditCard.hash | Encripted credit card data | string, condicional
fundingInstruments.creditCard.number | Credit Card number. Requires PCI certification. | string(19), conditional
fundingInstruments.creditCard.expirationMonth | Credit card expiration month. Requires PCI certification. | integer(2), mandadory when `number` is used.
fundingInstruments.creditCard.expirationYear | Credit card expiration year. Requires PCI certification.| integer(4), mandadory when `number` is used.
fundingInstruments.creditCard.cvc | Credit card security code. | integer, opcional
fundingInstruments.creditCard.holder.fullname | Holder name. | string(90), mandatory*
fundingInstruments.creditCard.holder.birthdate | Holder birth date. | 	date(AAAA-MM-DD), mandatory*
fundingInstruments.creditCard.holder.phone.areaCode | Area code | integer(2), optional
fundingInstruments.creditCard.holder.phone.countryCode | Country code | integer(2), optional
fundingInstruments.creditCard.holder.taxDocument.type | Type of document. Possible value: `CPF` for social security number, `CNPJ` for tax identification number. |	string
fundingInstruments.creditCard.holder.billingAddress | Billing address. | object Address optional *
fundingInstruments.boleto.expirationDate | Payment slip expiration date | 	date, mandatory
fundingInstruments.boleto.instructionLines.first | Payment slip instructions, line 1 | string, optional 
fundingInstruments.boleto.instructionLines.second | Payment slip instructions, line 2 | string, optional 
fundingInstruments.boleto.instructionLines.third | Payment slip instructions, line 3 | string, optional
fundingInstruments.boleto.logoUri | Logo that will be inserted on payment slip | link, optional
fundingInstruments.onlineBankDebit.bankNumber | Bank number. Possible values: `001`, `237`, `341`, `041`. List available [HERE](http://dev.moip.com.br/referencia-api/#lista-de-instituies-bancrias) | string, condicional
fundingInstruments.onlineBankDebit.expirationDate | Debit expiration date. | date, condicional
fundingInstruments.onlineBankDebit.returnUri | Return URI. | link, condicional
fees.type	| Fee type. Possible values: TRANSACTION, PRE_PAYMENT.	| string
fees.amount	| Amount charged. In cents Ex: R$10,32 must be informed as 1032 	| integer(12)
events.createdAt |	Event date.	| date(AAAA-MM-DD), response
events.type	| Event type. Possible values: `PAYMENT.WAIING`, `PAYMENT.AUTHORIZED`, `PAYMENT.IN_ANALYSIS`, `PRE_AUTHORIZED`, `PAYMENT.REFUNDED`, `PAYMENT.REVERSED`	| string, response
events.description |	Event description.	| string(65), response
cancellationDetails.cancelledBy | The agent that denied the transaction | Possible values: `MOIP` ou `ACQUIRER`. | string, response
cancellationDetails.code | Denial code | number, response 
cancellationDetails.description | A brief description of the denial code. | string, response
updatedAt	| Date when the resource was last updated.	| datetime, response
createdAt |	Date when the payment was created.	| datetime, response
_links.self.href	| URI to the resource. |	link
_links.order.title	| Order ID. |	string
_links.order.href	| Hyperlink to the order.	| link
_links.checkout	| Links to checkout. This link will redirect to the fundingInstrument informed.	object Checkout Moip

**When using credit card id to create payments, these attribute are not mandatory.**



## Create a payment - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/orders/{order_id}/payments`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{
  "installmentCount": 1,
  "fundingInstrument": {
    "method": "CREDIT_CARD",
    "creditCard": {
        "hash": "HhL0kbhfid+jwgj5l6Kt9EPdetDxQN8s7uKUHDYxDC/XoULjzik44rSda3EcWuOcL17Eb8JjWc1JI7gsuwg9P0rJv1mJQx+d3Dv1puQYz1iRjEWWhnB1bw0gTvnnC/05KbWN5M8oTiugmhVK02Rt2gpbcTtpS7VWyacfgesBJFavYYMljYg8p2YGHXkXrMuQiOCeemKLk420d0OTMBba27jDVVJ663HZDrObnjFXJH/4B5irkj+HO5genV+V4PYoLcOESG4nrI3oFAsMGsLLcdJo0NNvkEmJpn0e9GzureKKFYisYU+BEd9EMr/odS0VMvOYRV65HbPTspIkjl2+3Q==",
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
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "PAY-VZ1HI48256ZX",
  "status": "IN_ANALYSIS",
  "amount": {
    "fees": 187,
    "refunds": 0,
    "liquid": 1813,
    "currency": "BRL",
    "total": 2000
  },
  "installmentCount": 1,
  "fundingInstrument": {
    "creditCard": {
      "id": "CRC-V0AAG27AAFG7",
      "brand": "MASTERCARD",
      "first6": "555566",
      "last4": "8884",
      "holder": {
        "birthdate": "30/12/1988",
        "taxDocument": {
          "type": "CPF",
          "number": "33333333333"
        },
        "fullname": "Jose Portador da Silva"
      }
    },
    "method": "CREDIT_CARD"
  },
  "fees": [
    {
      "type": "TRANSACTION",
      "amount": 187
    }
  ],
  "events": [
    {
      "createdAt": "2015-01-14T12:17:50-0200",
      "type": "PAYMENT.IN_ANALYSIS"
    },
    {
      "createdAt": "2015-01-14T12:17:48-0200",
      "type": "PAYMENT.CREATED"
    }
  ],
  "_links": {
    "order": {
      "title": "ORD-VULX1EWDKXHF",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-VULX1EWDKXHF"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-VZ1HI48256ZX"
    }
  },
  "updatedAt": "2015-01-14T12:17:50-0200",
  "createdAt": "2015-01-14T12:17:48-0200"
}
```

## Retrieve a payment - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/payments/{payment_id}`

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
  "id": "PAY-VZ1HI48256ZX",
  "status": "IN_ANALYSIS",
  "amount": {
    "fees": 187,
    "refunds": 0,
    "liquid": 1813,
    "currency": "BRL",
    "total": 2000
  },
  "installmentCount": 1,
  "fundingInstrument": {
    "creditCard": {
      "id": "CRC-V0AAG27AAFG7",
      "brand": "MASTERCARD",
      "first6": "555566",
      "last4": "8884",
      "holder": {
        "birthdate": "30/12/1988",
        "taxDocument": {
          "type": "CPF",
          "number": "33333333333"
        },
        "fullname": "Jose Portador da Silva"
      }
    },
    "method": "CREDIT_CARD"
  },
  "fees": [
    {
      "type": "TRANSACTION",
      "amount": 187
    }
  ],
  "events": [
    {
      "createdAt": "2015-01-14T12:17:50-0200",
      "type": "PAYMENT.IN_ANALYSIS"
    },
    {
      "createdAt": "2015-01-14T12:17:48-0200",
      "type": "PAYMENT.CREATED"
    }
  ],
  "_links": {
    "order": {
      "title": "ORD-VULX1EWDKXHF",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-VULX1EWDKXHF"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-VZ1HI48256ZX"
    }
  },
  "updatedAt": "2015-01-14T12:17:50-0200",
  "createdAt": "2015-01-14T12:17:48-0200"
}
```


## Capture a pre-authorized payment - POST

This API allows you to capture a payment that is pre-authorized (if you are using the `delayCapture` attribute as `true`).
If you are using pre_authorized payments on multipayments API, you must use the accessToken from your seller to capture a payment.

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/payments/{payment_id}/capture`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{}
``` 

**RESPONSE:** 
```
200 (OK)
Content-Type: application/json

{
  "id": "PAY-ZJOE0VPNGIM5",
  "status": "AUTHORIZED",
  "amount": {
    "refunds": 0,
    "fees": 187,
    "liquid": 1813,
    "currency": "BRL",
    "total": 2000
  },
  "installmentCount": 1,
  "fundingInstrument": {
    "creditCard": {
      "id": "CRC-8XPB5E78J3C5",
      "brand": "VISA",
      "first6": "401200",
      "last4": "3335",
      "holder": {
        "birthdate": "30/12/1988",
        "taxDocument": {
          "type": "CPF",
          "number": "22222222222"
        },
        "fullname": "Jose Portador da Silva"
      }
    },
    "method": "CREDIT_CARD"
  },
  "fees": [
    {
      "type": "TRANSACTION",
      "amount": 187
    }
  ],
  "events": [
    {
      "createdAt": "2015-03-30T09:51:58-0300",
      "type": "PAYMENT.AUTHORIZED"
    },
    {
      "createdAt": "2015-03-30T09:51:41-0300",
      "type": "PAYMENT.IN_ANALYSIS"
    },
    {
      "createdAt": "2015-03-30T09:51:41-0300",
      "type": "PAYMENT.PRE_AUTHORIZED"
    },
    {
      "createdAt": "2015-03-30T09:51:38-0300",
      "type": "PAYMENT.CREATED"
    }
  ],
  "_links": {
    "order": {
      "title": "ORD-Y3HRHLCNQ4VO",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-Y3HRHLCNQ4VO"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-ZJOE0VPNGIM5"
    }
  },
  "createdAt": "2015-03-30T09:51:38-0300",
  "updatedAt": "2015-03-30T09:51:58-0300"
}
```

## Cancel a pre-authorized payment - POST

This API allows you to cancel the capture of a pre_authorized payment.

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/payments/{payment_id}/void`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{}
``` 

**RESPONSE:** 
```
200 (OK)
Content-Type: application/json

{
  "id": "PAY-ZJOE0VPNGIM5",
  "status": "CANCELLED",
  "amount": {
    "refunds": 0,
    "fees": 187,
    "liquid": 1813,
    "currency": "BRL",
    "total": 2000
  },
  "installmentCount": 1,
  "fundingInstrument": {
    "creditCard": {
      "id": "CRC-8XPB5E78J3C5",
      "brand": "VISA",
      "first6": "401200",
      "last4": "3335",
      "holder": {
        "birthdate": "30/12/1988",
        "taxDocument": {
          "type": "CPF",
          "number": "22222222222"
        },
        "fullname": "Jose Portador da Silva"
      }
    },
    "method": "CREDIT_CARD"
  },
  "fees": [
    {
      "type": "TRANSACTION",
      "amount": 187
    }
  ],
  "events": [
    {
      "createdAt": "2015-03-30T09:51:58-0300",
      "type": "PAYMENT.CANCELLED"
    },
    {
      "createdAt": "2015-03-30T09:51:41-0300",
      "type": "PAYMENT.PRE_AUTHORIZED"
    },
    {
      "createdAt": "2015-03-30T09:51:41-0300",
      "type": "PAYMENT.IN_ANALYSIS"
    },
    {
      "createdAt": "2015-03-30T09:51:38-0300",
      "type": "PAYMENT.CREATED"
    }
  ],
  "_links": {
    "order": {
      "title": "ORD-Y3HRHLCNQ4VO",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-Y3HRHLCNQ4VO"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-ZJOE0VPNGIM5"
    }
  },
  "createdAt": "2015-03-30T09:51:38-0300",
  "updatedAt": "2015-03-30T09:51:58-0300"
}
```

# REFUNDS

This API allows you to refund a payment. This same process is also available on orders.

It is possible to refund payments and order totally ir partially. In case the attribute `amount` is not specified than the API will consider as a total refund.
A refund made on a credit card returns the value refunded in the credit card automatically. For payment slips and bank debit can be refunded through a bank account or a Moip account. 

Attributes:

name | description | type
---- | ----------- | ----
amount	| Amount to be refunded. Ex: R$10,32 must be informed as 1032 | structured, optional
refundingInstrument.method	| Method to refund a payment. Possible values: BANK_ACCOUNT e MOIP_ACCOUNT. |	string, condicional
refundingInstrument.bankAccount.type | Bank account type. `CHECKING` or `SAVING` | string, conditional
refundingInstrument.bankAccount.bankNumber | Bank number. | string, conditional
refundingInstrument.bankAccount.agencyNumber | Agency number | integer, conditional
refundingInstrument.bankAccount.agencyCheckNumber | Agency check number | integer, conditional
refundingInstrument.bankAccount.accountNumber | Bank account | integer, conditional
refundingInstrument.bankAccount.accountCheckNumber | Bank account check number | integer, conditional
refundingInstrument.bankAccount.holder.fullname | Bank account holder name | string, conditional 
refundingInstrument.bankAccount.holder.taxDocument.type | Bank account holder document type | string, conditional 
refundingInstrument.bankAccount.holder.taxDocument.number | Bank account holder document number | string, conditional 

## Refund a payment - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/payments/{payment_id}/refunds`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "REF-BP8AAGSG7OTY",
  "status": "COMPLETED",
  "events": [
    {
      "createdAt": "2015-02-26T19:14:25-0300",
      "type": "REFUND.COMPLETED"
    },
    {
      "createdAt": "2015-02-26T19:14:23-0300",
      "type": "REFUND.REQUESTED"
    }
  ],
  "amount": {
    "fees": 0,
    "currency": "BRL",
    "total": 2000
  },
  "type": "FULL",
  "refundingInstrument": {
    "creditCard": {
      "brand": "MASTERCARD",
      "first6": "555566",
      "last4": "8884"
    },
    "method": "CREDIT_CARD"
  },
  "createdAt": "2015-02-26T19:14:23-0300",
  "_links": {
    "payment": {
      "title": "PAY-X2VXEGV7WVFB",
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-X2VXEGV7WVFB"
    },
    "order": {
      "title": "ORD-44GC0PFGU42M",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-44GC0PFGU42M"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/refunds/REF-BP8AAGSG7OTY"
    }
  }
}
```

## Refund a payment (partial) - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/payments/{payment_id}/refunds`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{
    "amount": 1000   
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "REF-I7IEV1RG9T1L",
  "status": "COMPLETED",
  "events": [
    {
      "createdAt": "2015-02-27T11:28:11-0300",
      "type": "REFUND.COMPLETED"
    },
    {
      "createdAt": "2015-02-27T11:28:10-0300",
      "type": "REFUND.REQUESTED"
    }
  ],
  "amount": {
    "fees": 0,
    "currency": "BRL",
    "total": 1000
  },
  "type": "PARTIAL",
  "refundingInstrument": {
    "creditCard": {
      "brand": "MASTERCARD",
      "first6": "555566",
      "last4": "8884"
    },
    "method": "CREDIT_CARD"
  },
  "createdAt": "2015-02-27T11:28:10-0300",
  "_links": {
    "payment": {
      "title": "PAY-3N7TSKQ26L2I",
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-3N7TSKQ26L2I"
    },
    "order": {
      "title": "ORD-1RQDM20CQW8T",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-1RQDM20CQW8T"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/refunds/REF-I7IEV1RG9T1L"
    }
  }
}
```

## Refund an order - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/orders/{order_id}/refunds`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "REF-BP8AAGSG7OTY",
  "status": "COMPLETED",
  "events": [
    {
      "createdAt": "2015-02-26T19:14:25-0300",
      "type": "REFUND.COMPLETED"
    },
    {
      "createdAt": "2015-02-26T19:14:23-0300",
      "type": "REFUND.REQUESTED"
    }
  ],
  "amount": {
    "fees": 0,
    "currency": "BRL",
    "total": 2000
  },
  "type": "FULL",
  "refundingInstrument": {
    "creditCard": {
      "brand": "MASTERCARD",
      "first6": "555566",
      "last4": "8884"
    },
    "method": "CREDIT_CARD"
  },
  "createdAt": "2015-02-26T19:14:23-0300",
  "_links": {
    "payment": {
      "title": "PAY-X2VXEGV7WVFB",
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-X2VXEGV7WVFB"
    },
    "order": {
      "title": "ORD-44GC0PFGU42M",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-44GC0PFGU42M"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/refunds/REF-BP8AAGSG7OTY"
    }
  }
}
```

## Retrieve a refund - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/refunds/{refund_id}`

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
  "id": "REF-BO0ABQCGVZVD",
  "status": "COMPLETED",
  "amount": {
    "fees": 0,
    "currency": "BRL",
    "total": 1000
  },
  "type": "PARTIAL",
  "refundingInstrument": {
    "creditCard": {
      "brand": "MASTERCARD",
      "first6": "555566",
      "last4": "8884"
    },
    "method": "CREDIT_CARD"
  },
  "createdAt": "2015-01-13T18:13:06-0200",
  "_links": {
    "payment": {
      "title": "PAY-6N4P8COQU6OS",
      "href": "https://sandbox.moip.com.br/v2/payments/PAY-6N4P8COQU6OS"
    },
    "order": {
      "title": "ORD-F78K9QTS1UN6",
      "href": "https://sandbox.moip.com.br/v2/orders/ORD-F78K9QTS1UN6"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/refunds/REF-BO0ABQCGVZVD"
    }
  }
}
```

## List refunds from a payment - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/payments/{payment_id}/refunds`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="
``` 

**RESPONSE:** 
```
200 (Ok)
Content-Type: application/json

{
  "refunds": [
    {
      "id": "REF-J4DZBVSRSRMU",
      "status": "COMPLETED",
      "type": "FULL",
      "amount": {
        "fees": 0,
        "currency": "BRL",
        "total": 10000
      },
      "refundingInstrument": {
        "method": "CREDIT_CARD",
        "creditCard": {
          "brand": "VISA",
          "first6": "401200",
          "last4": "3335"
        }
      },
      "events": [
        {
          "createdAt": "2015-12-05T18:57:17-0200",
          "type": "REFUND.COMPLETED"
        },
        {
          "createdAt": "2015-12-05T18:57:15-0200",
          "type": "REFUND.REQUESTED"
        }
      ],
      "_links": {
        "payment": {
          "title": "PAY-3N7TSKQ26L2I",
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-3N7TSKQ26L2I"
        },
        "order": {
          "title": "ORD-1RQDM20CQW8T",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-1RQDM20CQW8T"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/refunds/REF-I7IEV1RG9T1L"
        }
      },
      "createdAt": "2015-12-05T18:57:15-0200"
    }
  ]
}
```

## List refunds from an order - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/payments/{payment_id}/void`

**REQUEST:**
```
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="
``` 

**RESPONSE:** 
```
200 (Ok)
Content-Type: application/json

{
  "refunds": [
    {
      "id": "REF-J4DZBVSRSRMU",
      "status": "COMPLETED",
      "type": "FULL",
      "amount": {
        "fees": 0,
        "currency": "BRL",
        "total": 10000
      },
      "refundingInstrument": {
        "method": "CREDIT_CARD",
        "creditCard": {
          "brand": "VISA",
          "first6": "401200",
          "last4": "3335"
        }
      },
      "events": [
        {
          "createdAt": "2015-12-05T18:57:17-0200",
          "type": "REFUND.COMPLETED"
        },
        {
          "createdAt": "2015-12-05T18:57:15-0200",
          "type": "REFUND.REQUESTED"
        }
      ],
      "_links": {
        "payment": {
          "title": "PAY-3N7TSKQ26L2I",
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-3N7TSKQ26L2I"
        },
        "order": {
          "title": "ORD-1RQDM20CQW8T",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-1RQDM20CQW8T"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/refunds/REF-J4DZBVSRSRMU"
        }
      },
      "createdAt": "2015-12-05T18:57:15-0200"
    }
  ]
}
```

# MULTIORDERS

A multiorder is a colletion of orders used to create transactions involving more than one seller in the same cart.
When you charge a multiorder, Moip creates one payment for each order involved.

**IMPORTANT: Refunding a multiorder is not possible, however the order can be refunded individually**
**IMPORTANT: Multiorder requires the use of OAuth permissions**

Attributes:

name	| description |	type
---- | --------- | ----- 
id	| Multiorder ID. |	string(16), response
ownId	| Multiorder ownId . External reference. |	string(65)
status	| Multiordr status. Possible values: `WAITING`, `PAID`, `NOT_PAID`, `REVERTED`. |	string(65), response
amount.currency |	Currency. Possible values: BRL. |	string
amount.total |		Total amount charged in cents. Ex: R$10,32 must be informed as 1032. |	integer(12)
orders |	Order colletion. |	Order colletion
createdAt	| Date when the resource was created.	| datetime, response
updatedAt	| Date when the resource was last updated. |	datetime, response
_links.self.href	| 	URI to the resource. |	link
_links.checkout	Links to checkout. This link will redirect to the fundingInstrument informed. | object Checkout Moip

## Create a multiorder - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/multiorders`

**REQUEST:**
```
Content-Type: application/json
Authorization: "OAuth qguusgp05mxaa9adyy8kvo4nuq380z7"

{
  "ownId": "meu_multiorder_id",
  "orders": [
    {
      "ownId": "pedido_1_id",
      "amount": {
        "currency": "BRL",
        "subtotals": {
          "shipping": 2000
        }
      },
      "items": [
        {
          "product": "Camisa Verde e Amarelo - Brasil",
          "quantity": 1,
          "detail": "Seleção Brasileira",
          "price": 2000
        }
      ],
      "customer": {
        "ownId": "customer[1234]",
        "fullname": "Joao Sousa",
        "email": "joao.sousa@email.com",
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
        "shippingAddress": 
          {
            "street": "Avenida Faria Lima",
            "streetNumber": 2927,
            "complement": 8,
            "district": "Itaim",
            "city": "Sao Paulo",
            "state": "SP",
            "country": "BRA",
            "zipCode": "01234000"
          }
      },
      "receivers": [
        {
          "moipAccount": {
            "id": "MPA-VB5OGTVPCI52"
          },
          "type": "PRIMARY"
        }
      ]
    },
    {
      "ownId": "pedido_2_id",
      "amount": {
        "currency": "BRL",
        "subtotals": {
          "shipping": 3000
        }
      },
      "items": [
        {
          "product": "Camisa Preta - Alemanha",
          "quantity": 1,
          "detail": "Camiseta da Copa 2014",
          "price": 1000
        }
      ],
      "customer": {
        "ownId": "customer[1234]",
        "fullname": "Joao Sousa",
        "email": "joao.sousa@email.com",
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
        "shippingAddress": 
          {
            "street": "Avenida Faria Lima",
            "streetNumber": 2927,
            "complement": 8,
            "district": "Itaim",
            "city": "Sao Paulo",
            "state": "SP",
            "country": "BRA",
            "zipCode": "01234000"
          }
      },
      "receivers": [
        {
          "moipAccount": {
            "id": "MPA-IFYRB1HBL73Z"
          },
          "type": "PRIMARY"
        },
        {
          "moipAccount": {
            "id": "MPA-KQB1QFWS6QNM"
          },
          "type": "SECONDARY",
          "amount": {
            "fixed": 55
          }
        }
      ]
    }
  ]
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "MOR-9241K3EFW5DV",
  "ownId": "meu_multiorder_id",
  "status": "CREATED",
  "createdAt": "2015-02-27T11:39:02-0300",
  "updatedAt": "",
  "amount": {
    "currency": "BRL",
    "total": 8000
  },
  "orders": [
    {
      "id": "ORD-4LFGB72U2OK1",
      "ownId": "pedido_1_id",
      "status": "CREATED",
      "createdAt": "2015-02-27T11:39:02-0300",
      "amount": {
        "total": 4000,
        "fees": 0,
        "refunds": 0,
        "liquid": 0,
        "otherReceivers": 4000,
        "currency": "BRL",
        "subtotals": {
          "shipping": 2000,
          "addition": 0,
          "discount": 0,
          "items": 2000
        }
      },
      "items": [
        {
          "product": "Camisa Verde e Amarelo - Brasil",
          "price": 2000,
          "detail": "Seleção Brasileira",
          "quantity": 1
        }
      ],
      "customer": {
        "id": "CUS-IRDAWA6Q260J",
        "ownId": "customer[1234]",
        "fullname": "Joao Sousa",
        "createdAt": "2015-02-27T11:39:02-0300",
        "birthDate": "1988-12-30T00:00:00-0200",
        "email": "joao.sousa@email.com",
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
            "href": "https://sandbox.moip.com.br/v2/customers/CUS-IRDAWA6Q260J"
          }
        }
      },
      "payments": [],
      "refunds": [],
      "entries": [],
      "events": [
        {
          "createdAt": "2015-02-27T11:39:02-0300",
          "description": "",
          "type": "ORDER.CREATED"
        }
      ],
      "receivers": [
        {
          "amount": {
            "fees": 0,
            "refunds": 0,
            "total": 4000
          },
          "moipAccount": {
            "fullname": "Lojista 4 Moip",
            "login": "lojista_4@labs.moip.com.br",
            "id": "MPA-VB5OGTVPCI52"
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
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-4LFGB72U2OK1"
        }
      }
    },
    {
      "id": "ORD-29P0JCAJY8ZU",
      "ownId": "pedido_2_id",
      "status": "CREATED",
      "createdAt": "2015-02-27T11:39:02-0300",
      "amount": {
        "total": 4000,
        "fees": 0,
        "refunds": 0,
        "liquid": 0,
        "otherReceivers": 4000,
        "currency": "BRL",
        "subtotals": {
          "shipping": 3000,
          "addition": 0,
          "discount": 0,
          "items": 1000
        }
      },
      "items": [
        {
          "product": "Camisa Preta - Alemanha",
          "price": 1000,
          "detail": "Camiseta da Copa 2014",
          "quantity": 1
        }
      ],
      "customer": {
        "id": "CUS-RE6XARTZD2K7",
        "ownId": "customer[1234]",
        "fullname": "Joao Sousa",
        "createdAt": "2015-02-27T11:39:02-0300",
        "birthDate": "1988-12-30T00:00:00-0200",
        "email": "joao.sousa@email.com",
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
            "href": "https://sandbox.moip.com.br/v2/customers/CUS-RE6XARTZD2K7"
          }
        }
      },
      "payments": [],
      "refunds": [],
      "entries": [],
      "events": [
        {
          "createdAt": "2015-02-27T11:39:02-0300",
          "description": "",
          "type": "ORDER.CREATED"
        }
      ],
      "receivers": [
        {
          "amount": {
            "fees": 0,
            "refunds": 0,
            "total": 3945
          },
          "moipAccount": {
            "fullname": "Lojista 3 Moip",
            "login": "lojista_3@labs.moip.com.br",
            "id": "MPA-IFYRB1HBL73Z"
          },
          "type": "PRIMARY"
        },
        {
          "amount": {
            "refunds": 0,
            "total": 55
          },
          "moipAccount": {
            "fullname": "Maria da Silva Santos",
            "login": "secundario_1@labs.moip.com.br",
            "id": "MPA-KQB1QFWS6QNM"
          },
          "type": "SECONDARY"
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
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-29P0JCAJY8ZU"
        }
      }
    }
  ],
  "_links": {
    "self": {
      "href": "https://sandbox.moip.com.br/v2/orders/MOR-9241K3EFW5DV"
    },
    "checkout": {
      "payOnlineBankDebitItau": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/itau/MOR-9241K3EFW5DV"
      },
      "payCreditCard": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/creditcard/MOR-9241K3EFW5DV"
      }
    }
  }
}
```

## Retrieve a multiorder - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/multiorders/{multiorder_id}`

**REQUEST:**
```
Content-Type: application/json
Authorization: 'OAuth qguusgp05mxaa9adyy8kvo4nuq380z7'
``` 

**RESPONSE:** 
```
200 (Ok)
Content-Type: application/json

 {
  "id": "MOR-9241K3EFW5DV",
  "ownId": "meu_multiorder_id",
  "status": "CREATED",
  "createdAt": "2015-02-27T11:39:02-0300",
  "updatedAt": "",
  "amount": {
    "currency": "BRL",
    "total": 8000
  },
  "orders": [
    {
      "id": "ORD-4LFGB72U2OK1",
      "ownId": "pedido_1_id",
      "status": "CREATED",
      "createdAt": "2015-02-27T11:39:02-0300",
      "amount": {
        "total": 4000,
        "fees": 0,
        "refunds": 0,
        "liquid": 0,
        "otherReceivers": 4000,
        "currency": "BRL",
        "subtotals": {
          "shipping": 2000,
          "addition": 0,
          "discount": 0,
          "items": 2000
        }
      },
      "items": [
        {
          "product": "Camisa Verde e Amarelo - Brasil",
          "price": 2000,
          "detail": "Seleção Brasileira",
          "quantity": 1
        }
      ],
      "customer": {
        "id": "CUS-IRDAWA6Q260J",
        "ownId": "customer[1234]",
        "fullname": "Joao Sousa",
        "createdAt": "2015-02-27T11:39:02-0300",
        "birthDate": "1988-12-30T00:00:00-0200",
        "email": "joao.sousa@email.com",
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
            "href": "https://sandbox.moip.com.br/v2/customers/CUS-IRDAWA6Q260J"
          }
        }
      },
      "payments": [],
      "refunds": [],
      "entries": [],
      "events": [
        {
          "createdAt": "2015-02-27T11:39:02-0300",
          "description": "",
          "type": "ORDER.CREATED"
        }
      ],
      "receivers": [
        {
          "amount": {
            "fees": 0,
            "refunds": 0,
            "total": 4000
          },
          "moipAccount": {
            "fullname": "Lojista 4 Moip",
            "login": "lojista_4@labs.moip.com.br",
            "id": "MPA-VB5OGTVPCI52"
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
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-4LFGB72U2OK1"
        }
      }
    },
    {
      "id": "ORD-29P0JCAJY8ZU",
      "ownId": "pedido_2_id",
      "status": "CREATED",
      "createdAt": "2015-02-27T11:39:02-0300",
      "amount": {
        "total": 4000,
        "fees": 0,
        "refunds": 0,
        "liquid": 0,
        "otherReceivers": 4000,
        "currency": "BRL",
        "subtotals": {
          "shipping": 3000,
          "addition": 0,
          "discount": 0,
          "items": 1000
        }
      },
      "items": [
        {
          "product": "Camisa Preta - Alemanha",
          "price": 1000,
          "detail": "Camiseta da Copa 2014",
          "quantity": 1
        }
      ],
      "customer": {
        "id": "CUS-RE6XARTZD2K7",
        "ownId": "customer[1234]",
        "fullname": "Joao Sousa",
        "createdAt": "2015-02-27T11:39:02-0300",
        "birthDate": "1988-12-30T00:00:00-0200",
        "email": "joao.sousa@email.com",
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
            "href": "https://sandbox.moip.com.br/v2/customers/CUS-RE6XARTZD2K7"
          }
        }
      },
      "payments": [],
      "refunds": [],
      "entries": [],
      "events": [
        {
          "createdAt": "2015-02-27T11:39:02-0300",
          "description": "",
          "type": "ORDER.CREATED"
        }
      ],
      "receivers": [
        {
          "amount": {
            "fees": 0,
            "refunds": 0,
            "total": 3945
          },
          "moipAccount": {
            "fullname": "Lojista 3 Moip",
            "login": "lojista_3@labs.moip.com.br",
            "id": "MPA-IFYRB1HBL73Z"
          },
          "type": "PRIMARY"
        },
        {
          "amount": {
            "refunds": 0,
            "total": 55
          },
          "moipAccount": {
            "fullname": "Maria da Silva Santos",
            "login": "secundario_1@labs.moip.com.br",
            "id": "MPA-KQB1QFWS6QNM"
          },
          "type": "SECONDARY"
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
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-29P0JCAJY8ZU"
        }
      }
    }
  ],
  "_links": {
    "self": {
      "href": "https://sandbox.moip.com.br/v2/orders/MOR-9241K3EFW5DV"
    },
    "checkout": {
      "payOnlineBankDebitItau": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/itau/MOR-9241K3EFW5DV"
      },
      "payCreditCard": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/creditcard/MOR-9241K3EFW5DV"
      }
    }
  }
}
```

# MULTIPAYMENTS

The multipayment is a colletion of multipayments related to a multiorder. This endpoint allows to create different payments for different orders and different sellers at the same time.

Attributes:

name | description | type
---- | ----------- | ----
id | Multipayment ID | string(16), response
status | Status of a multipayment. Possible values: `WAITING`, `IN_ANALYSIS`, `PRE_AUTHORIZED`,`AUTHORIZED`, `CANCELLED`, `REFUNDED`, `REVERSED`, `SETTLED`. | string, response
amount.total	| Total amount charged in cents. Ex: R$10,32 must be informed as 1032 	| integer(12), response
amount.currency	| Currency. Possible values: BRL. |	string
installmentCount	| Number of installments. Minimum 1 and maximum 12.	| integer(2)
delayCapture	| Used if you need to pre-capture a multipayment. Only available for credit cards. |	boolean
fundingInstruments.method	| Method used. Possible values: `CREDIT_CARD`, `BOLETO`, `ONLINE_BANK_DEBIT`, `WALLET` | 	string
fundingInstruments.creditCard.id | Credit card ID. This ID can be used in the future to create new multipayments. Internal reference. | string, conditional 
fundingInstruments.creditCard.hash | Encripted credit card data | string, condicional
fundingInstruments.creditCard.number | Credit Card number. Requires PCI certification. | string(19), conditional
fundingInstruments.creditCard.expirationMonth | Credit card expiration month. Requires PCI certification. | integer(2), mandadory when `number` is used.
fundingInstruments.creditCard.expirationYear | Credit card expiration year. Requires PCI certification.| integer(4), mandadory when `number` is used.
fundingInstruments.creditCard.cvc | Credit card security code. | integer, opcional
fundingInstruments.creditCard.holder.fullname | Holder name. | string(90), mandatory*
fundingInstruments.creditCard.holder.birthdate | Holder birth date. | 	date(AAAA-MM-DD), mandatory*
fundingInstruments.creditCard.holder.phone.areaCode | Area code | integer(2), optional
fundingInstruments.creditCard.holder.phone.countryCode | Country code | integer(2), optional
fundingInstruments.creditCard.holder.taxDocument.type | Type of document. Possible value: `CPF` for social security number, `CNPJ` for tax identification number. |	string
fundingInstruments.creditCard.holder.billingAddress | Billing address. | object Address optional *
fundingInstruments.boleto.expirationDate | Payment slip expiration date | 	date, mandatory
fundingInstruments.boleto.instructionLines.first | Payment slip instructions, line 1 | string, optional 
fundingInstruments.boleto.instructionLines.second | Payment slip instructions, line 2 | string, optional 
fundingInstruments.boleto.instructionLines.third | Payment slip instructions, line 3 | string, optional
fundingInstruments.boleto.logoUri | Logo that will be inserted on payment slip | link, optional
fundingInstruments.onlineBankDebit.bankNumber | Bank number. Possible values: `001`, `237`, `341`, `041`. List available [HERE](http://dev.moip.com.br/referencia-api/#lista-de-instituies-bancrias) | string, condicional
fundingInstruments.onlineBankDebit.expirationDate | Debit expiration date. | date, condicional
fundingInstruments.onlineBankDebit.returnUri | Return URI. | link, condicional
cancellationDetails.cancelledBy | The agent that denied the transaction | Possible values: `MOIP` ou `ACQUIRER`. | string, response
cancellationDetails.code | Denial code | number, response 
cancellationDetails.description | A brief description of the denial code. | string, response
updatedAt	| Date when the resource was last updated.	| datetime, response
_links.self.href	| URI to the resource. |	link
_links.order.title	| Order ID. |	string
_links.order.href	| Hyperlink to the order.	| link
_links.checkout	| Links to checkout. This link will redirect to the fundingInstrument informed.	object Checkout Moip


## Create a multipayment - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/multiorders/{multiorder_id}/multipayments`

**REQUEST:**
```
Content-Type: application/json
Authorization: "OAuth qguusgp05mxaa9adyy8kvo4nuq380z7"

{
  "installmentCount": 1,
  "fundingInstrument": {
    "method": "CREDIT_CARD",
    "creditCard": {
      "hash": "HhL0kbhfid+jwgj5l6Kt9EPdetDxQN8s7uKUHDYxDC/XoULjzik44rSda3EcWuOcL17Eb8JjWc1JI7gsuwg9P0rJv1mJQx+d3Dv1puQYz1iRjEWWhnB1bw0gTvnnC/05KbWN5M8oTiugmhVK02Rt2gpbcTtpS7VWyacfgesBJFavYYMljYg8p2YGHXkXrMuQiOCeemKLk420d0OTMBba27jDVVJ663HZDrObnjFXJH/4B5irkj+HO5genV+V4PYoLcOESG4nrI3oFAsMGsLLcdJo0NNvkEmJpn0e9GzureKKFYisYU+BEd9EMr/odS0VMvOYRV65HbPTspIkjl2+3Q==",
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
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "MPY-8MMS9Y8TE2IK",
  "status": "WAITING",
  "amount": {
    "currency": "BRL",
    "total": 8000
  },
  "installmentCount": 1,
  "payments": [
    {
      "id": "PAY-YVP2NVPMV6YC",
      "status": "WAITING",
      "amount": {
        "fees": 335,
        "refunds": 0,
        "liquid": 3665,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "creditCard": {
          "id": "CRC-31VUZTMZVION",
          "brand": "MASTERCARD",
          "first6": "555566",
          "last4": "8884",
          "holder": {
            "birthdate": "30/12/1988",
            "taxDocument": {
              "type": "CPF",
              "number": "33333333333"
            },
            "fullname": "Jose Portador da Silva"
          }
        },
        "method": "CREDIT_CARD"
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 335
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:07:34-0200",
          "type": "PAYMENT.WAITING"
        },
        {
          "createdAt": "2015-01-14T14:07:32-0200",
          "type": "PAYMENT.CREATED"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-H31PC0X9WTHR",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-H31PC0X9WTHR"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-YVP2NVPMV6YC"
        }
      },
      "updatedAt": "2015-01-14T14:07:34-0200",
      "createdAt": "2015-01-14T14:07:32-0200"
    },
    {
      "id": "PAY-78WM5WUDITVI",
      "status": "WAITING",
      "amount": {
        "fees": 335,
        "refunds": 0,
        "liquid": 3665,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "creditCard": {
          "id": "CRC-GTVZJDJ47I34",
          "brand": "MASTERCARD",
          "first6": "555566",
          "last4": "8884",
          "holder": {
            "birthdate": "30/12/1988",
            "taxDocument": {
              "type": "CPF",
              "number": "33333333333"
            },
            "fullname": "Jose Portador da Silva"
          }
        },
        "method": "CREDIT_CARD"
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 335
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:07:34-0200",
          "type": "PAYMENT.WAITING"
        },
        {
          "createdAt": "2015-01-14T14:07:33-0200",
          "type": "PAYMENT.CREATED"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-3PJ1LMDGGAZ1",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-3PJ1LMDGGAZ1"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-78WM5WUDITVI"
        }
      },
      "updatedAt": "2015-01-14T14:07:34-0200",
      "createdAt": "2015-01-14T14:07:33-0200"
    }
  ],
  "_links": {
    "multiorder": {
      "href": "https://sandbox.moip.com.br/v2/multiorders/MOR-DT554VQVBYCY"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/multipayments/MPY-8MMS9Y8TE2IK"
    }
  }
}
```

## Create a multipayment (with payment slip) - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/multiorders/{multiorder_id}/multipayments`

**REQUEST:**
```
Content-Type: application/json
Authorization: "OAuth qguusgp05mxaa9adyy8kvo4nuq380z7"

{
  "fundingInstrument": {
    "method": "BOLETO",
    "boleto": {
      "expirationDate": "2016-09-30",
      "instructionLines": {
        "first": "Primeira linha se instrução",
        "second": "Segunda linha se instrução",
        "third": "Terceira linha se instrução"
      },
      "logoUri": "http://"
    }
  }
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "MPY-7X3JO2E2AMFL",
  "status": "WAITING",
  "amount": {
    "currency": "BRL",
    "total": 8000
  },
  "installmentCount": 1,
  "fundingInstrument": {
    "boleto": {
      "expirationDate": "2016-09-30",
      "lineCode": "23793.39126 60000.032957 96001.747904 7 69330000008000",
      "instructionLines": {
        "third": "Terceira linha se instrução",
        "second": "Segunda linha se instrução",
        "first": "Primeira linha se instrução"
      }
    },
    "method": "BOLETO"
  },
  "payments": [
    {
      "id": "PAY-FJ6MWU4YM7YO",
      "status": "WAITING",
      "amount": {
        "fees": 155,
        "refunds": 0,
        "liquid": 3845,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "boleto": {
          "expirationDate": "2016-09-30",
          "instructionLines": {
            "third": "Terceira linha se instrução",
            "second": "Segunda linha se instrução",
            "first": "Primeira linha se instrução"
          }
        },
        "method": "BOLETO"
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 155
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:08:27-0200",
          "type": "PAYMENT.CREATED"
        },
        {
          "createdAt": "2015-01-14T14:08:27-0200",
          "type": "PAYMENT.WAITING"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-OK8E3Z5FDBHQ",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-OK8E3Z5FDBHQ"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-FJ6MWU4YM7YO"
        }
      },
      "updatedAt": "2015-01-14T14:08:27-0200",
      "createdAt": "2015-01-14T14:08:27-0200"
    },
    {
      "id": "PAY-TRYJXY9V2KR0",
      "status": "WAITING",
      "amount": {
        "fees": 155,
        "refunds": 0,
        "liquid": 3845,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "boleto": {
          "expirationDate": "2016-09-30",
          "instructionLines": {
            "third": "Terceira linha se instrução",
            "second": "Segunda linha se instrução",
            "first": "Primeira linha se instrução"
          }
        },
        "method": "BOLETO"
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 155
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:08:27-0200",
          "type": "PAYMENT.CREATED"
        },
        {
          "createdAt": "2015-01-14T14:08:27-0200",
          "type": "PAYMENT.WAITING"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-5D7RIISYWZRI",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-5D7RIISYWZRI"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-TRYJXY9V2KR0"
        }
      },
      "updatedAt": "2015-01-14T14:08:27-0200",
      "createdAt": "2015-01-14T14:08:27-0200"
    }
  ],
  "_links": {
    "multiorder": {
      "href": "https://sandbox.moip.com.br/v2/multiorders/MOR-QXZKIF6GPN5V"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/multipayments/MPY-7X3JO2E2AMFL"
    },
    "checkout": {
      "payBoleto": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/boleto/MPY-7X3JO2E2AMFL"
      }
    }
  }
}
```

## Create a multipayment (online bank debit) - POST

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/multiorders/{multiorder_id}/multipayments`

**REQUEST:**
```
Content-Type: application/json
Authorization: "OAuth qguusgp05mxaa9adyy8kvo4nuq380z7"

{
  "fundingInstrument": {
    "method": "ONLINE_BANK_DEBIT",
    "onlineBankDebit": {
      "bankNumber": "001",
      "expirationDate": "2016-12-30",
      "returnUri": "http://"
    }
  }
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "id": "MPY-EP4WDATHIPPU",
  "status": "WAITING",
  "amount": {
    "currency": "BRL",
    "total": 8000
  },
  "installmentCount": 1,
  "payments": [
    {
      "id": "PAY-ET37WTEKYWG4",
      "status": "WAITING",
      "amount": {
        "fees": 155,
        "refunds": 0,
        "liquid": 3845,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "method": "ONLINE_BANK_DEBIT",
        "onlineBankDebit": {
          "expirationDate": "2016-12-30",
          "bankNumber": "001",
          "bankName": "BANCO DO BRASIL S.A."
        }
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 155
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:15:23-0200",
          "type": "PAYMENT.CREATED"
        },
        {
          "createdAt": "2015-01-14T14:15:23-0200",
          "type": "PAYMENT.WAITING"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-HWF5X38EW20T",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-HWF5X38EW20T"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-ET37WTEKYWG4"
        }
      },
      "updatedAt": "2015-01-14T14:15:23-0200",
      "createdAt": "2015-01-14T14:15:23-0200"
    },
    {
      "id": "PAY-U70D39R97FNV",
      "status": "WAITING",
      "amount": {
        "fees": 155,
        "refunds": 0,
        "liquid": 3845,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "method": "ONLINE_BANK_DEBIT",
        "onlineBankDebit": {
          "expirationDate": "2016-12-30",
          "bankNumber": "001",
          "bankName": "BANCO DO BRASIL S.A."
        }
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 155
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:15:23-0200",
          "type": "PAYMENT.CREATED"
        },
        {
          "createdAt": "2015-01-14T14:15:23-0200",
          "type": "PAYMENT.WAITING"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-KUKNFDIXWZ5T",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-KUKNFDIXWZ5T"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-U70D39R97FNV"
        }
      },
      "updatedAt": "2015-01-14T14:15:23-0200",
      "createdAt": "2015-01-14T14:15:23-0200"
    }
  ],
  "_links": {
    "multiorder": {
      "href": "https://sandbox.moip.com.br/v2/multiorders/MOR-BZ1UC1FEZUWG"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/multipayments/MPY-EP4WDATHIPPU"
    },
    "checkout": {
      "payOnlineBankDebitItau": {
        "redirectHref": "https://checkout-sandbox.moip.com.br/debit/itau/MPY-EP4WDATHIPPU"
      }
    }
  }
}
```

## Retrieve a multipayment - GET

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/multipayments/{multipayment_id}`

**REQUEST:**
```
Content-Type: application/json
Authorization: "OAuth qguusgp05mxaa9adyy8kvo4nuq380z7"
``` 

**RESPONSE:** 
```
200 (Ok)
Content-Type: application/json

 {
  "id": "MPY-8MMS9Y8TE2IK",
  "status": "AUTHORIZED",
  "amount": {
    "currency": "BRL",
    "total": 8000
  },
  "installmentCount": 1,
  "payments": [
    {
      "id": "PAY-YVP2NVPMV6YC",
      "status": "AUTHORIZED",
      "amount": {
        "fees": 335,
        "refunds": 0,
        "liquid": 3665,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "creditCard": {
          "id": "CRC-31VUZTMZVION",
          "brand": "MASTERCARD",
          "first6": "555566",
          "last4": "8884",
          "holder": {
            "birthdate": "30/12/1988",
            "taxDocument": {
              "type": "CPF",
              "number": "33333333333"
            },
            "fullname": "Jose Portador da Silva"
          }
        },
        "method": "CREDIT_CARD"
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 335
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:07:42-0200",
          "type": "PAYMENT.AUTHORIZED"
        },
        {
          "createdAt": "2015-01-14T14:07:37-0200",
          "type": "PAYMENT.IN_ANALYSIS"
        },
        {
          "createdAt": "2015-01-14T14:07:34-0200",
          "type": "PAYMENT.WAITING"
        },
        {
          "createdAt": "2015-01-14T14:07:32-0200",
          "type": "PAYMENT.CREATED"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-H31PC0X9WTHR",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-H31PC0X9WTHR"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-YVP2NVPMV6YC"
        }
      },
      "updatedAt": "2015-01-14T14:07:42-0200",
      "createdAt": "2015-01-14T14:07:32-0200"
    },
    {
      "id": "PAY-78WM5WUDITVI",
      "status": "AUTHORIZED",
      "amount": {
        "fees": 335,
        "refunds": 0,
        "liquid": 3665,
        "currency": "BRL",
        "total": 4000
      },
      "installmentCount": 1,
      "fundingInstrument": {
        "creditCard": {
          "id": "CRC-GTVZJDJ47I34",
          "brand": "MASTERCARD",
          "first6": "555566",
          "last4": "8884",
          "holder": {
            "birthdate": "30/12/1988",
            "taxDocument": {
              "type": "CPF",
              "number": "33333333333"
            },
            "fullname": "Jose Portador da Silva"
          }
        },
        "method": "CREDIT_CARD"
      },
      "fees": [
        {
          "type": "TRANSACTION",
          "amount": 335
        }
      ],
      "events": [
        {
          "createdAt": "2015-01-14T14:07:42-0200",
          "type": "PAYMENT.AUTHORIZED"
        },
        {
          "createdAt": "2015-01-14T14:07:37-0200",
          "type": "PAYMENT.IN_ANALYSIS"
        },
        {
          "createdAt": "2015-01-14T14:07:34-0200",
          "type": "PAYMENT.WAITING"
        },
        {
          "createdAt": "2015-01-14T14:07:33-0200",
          "type": "PAYMENT.CREATED"
        }
      ],
      "_links": {
        "order": {
          "title": "ORD-3PJ1LMDGGAZ1",
          "href": "https://sandbox.moip.com.br/v2/orders/ORD-3PJ1LMDGGAZ1"
        },
        "self": {
          "href": "https://sandbox.moip.com.br/v2/payments/PAY-78WM5WUDITVI"
        }
      },
      "updatedAt": "2015-01-14T14:07:42-0200",
      "createdAt": "2015-01-14T14:07:33-0200"
    }
  ],
  "_links": {
    "multiorder": {
      "href": "https://sandbox.moip.com.br/v2/multiorders/MOR-DT554VQVBYCY"
    },
    "self": {
      "href": "https://sandbox.moip.com.br/v2/multipayments/MPY-8MMS9Y8TE2IK"
    }
  }
}
```

# OAuth

OAuth permissions allows you to obtain information of other Moip accounts in order to create order and payments where these account are involved as receivers.

To do so, you'll need basically finish 3 steps:

* Register your APP
* Ask permissions
* Generate accessToken

## Registering your APP

To act on behalf of other sellers you need to create a Moip APP.
[Here](http://dev.moip.com.br/app/) you can fill a form that will request to our team the creation of your app, after that you'll receive an email containing:

 * `appId`
 * `appSecret`
 * `accessToken`

 With these you can create the link to ask OAuth permissions and generate de `accessToken` from the sellers account.
 
## Asking OAuth permissions

name | description | details
---- | ----------- | -------
responseType	| Defines the type os response. Possible values: `CODE` |	string(4), mandatory
appId |	APP ID.	| string(16) mandatory
redirectUri	| Redirect URI	| string(255), mandatory
scope	| Scope of permissions you required. Possible values: `CREATE_ORDERS`,`VIEW_ORDERS`,`CREATE_PAYMENTS`,`VIEW_PAYMENT`	| string(255), mandatory

### Endpoint

**GET** `https://sandbox.moip.com.br/v2/payments/{payment_id}/void`

**URL Example:** `https://sandbox.moip.com.br/oauth/authorize?responseType=CODE&appId=APP1A2B3C4D5E6F&redirectUri=https://url.com.br/callback.php&scope=CREATE_ORDERS|VIEW_ORDERS|CREATE_PAYMENTS|VIEW_PAYMENTS`

The page created with the URL above will look like this:

![Moip Page](https://moip.com.br/docs/images/content/moip-connect-pagina-permissao.png)

If the user accepts to give your APP permission, then he will be redirected to your redirect URL, containing his `code`.

**Example:** `http://mywebsite.com/?code=ohgrvfk2kdq92w27w66mlntasfh24je&scope=create_orders+view_orders+create_payments+view_payments`

## Generate OAuth token (accessToken) - POST

With the `code` you retrieved from the step above you will be able to get the user `acessToken` and then use it to make requests on his behelf.

Attributes:

name | description | details
---- | ----------- | -------
appId | 	APP ID |	string(4), mandatory
appSecret |	Chave privada do aplicativo	string(32) mandatory
redirectUri | Redirect URL. Must be the same you used to require permission. |	string mandatory
grantType | Type of request. Possible values: AUTHORIZATION_CODE	| string(18) mandatory
code	| Code generated when the user gave permission to the APP |	string(32) mandatory
accessToken |	User accessToken.	| string(32) response
access_token |	User accessToken. | genéricos	string(32) response
scope | Scope of permissions granted. Possible values: `CREATE_ORDERS`,`VIEW_ORDERS`,`CREATE_PAYMENTS`,`VIEW_PAYMENT` |	string response
moipAccountId | Account ID of the account that gave permission.	string(16) response

### Endpoint

**POST** `https://sandbox.moip.com.br/oauth/accesstoken`

**REQUEST:**
```
https://sandbox.moip.com.br/oauth/accesstoken
Content-Type: application/json
Authorization: "Basic MDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDEwMTAxMDE6QUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQkFCQUJBQg=="

{
  "appId": "APP-A1B2C3D4E5F6",
  "appSecret": "aa6494500f7711e38ffd0800200c9a66",
  "redirectUri": "https://url.com.br/callback",
  "grantType": "AUTHORIZATION_CODE",
  "code": "550e8400e29b41d4a716446655440000"
}
``` 

**RESPONSE:** 
```
201 (Created)
Content-Type: application/json

{
  "accessToken": "aa6494500f7711e38ffd0800200c9a77",
  "access_token": "aa6494500f7711e38ffd0800200c9a77",
  "scope": "CREATE_ORDERS|VIEW_ORDERS|CREATE_PAYMENTS|VIEW_PAYMENTS",
  "moipAccountId": "MPA-A1B2C3D4E5F6"
}
```





## ACTION

### Endpoint

**POST** `https://sandbox.moip.com.br/v2/payments/{payment_id}/void`

**REQUEST:**
```
``` 

**RESPONSE:** 
```
```
