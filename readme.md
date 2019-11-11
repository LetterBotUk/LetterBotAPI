# LetterBot

[Letterbot](https://www.letterbot.co.uk) is a sophisticated promotional system that uses ground-breaking hardware and software to mimic authentic handwritten communication. It allows organisations to quickly create and send personalised, seemingly handwritten letters and envelopes to multiple contacts, at a scale that would be impossible for real people to accomplish, both practicably, and commercially.

# LetterBot API

LetterBot has a JSON-based API that allows to integrate with CRM, eCommerce and Marketing Automation systems.

# Getting Started

To place an order using API for handwriting you would need:

- LetterBot credits, can be purchased [here](https://www.letterbot.co.uk/products/letterbot-credit)
- an authentication key
- a template code (see Templates section).

# Authentication

API requests authenticated using header X-Auth-Key.
Once you will purchase account credit we will issue you with an authentication key.

# Templates

A writing template is something that is prepared before a writing order can be placed. 
The template defines:

- paper size, type and quantity
- content and placeholders the content uses
- postage options (1st class, 2nd class, bundle daily or weekly, etc.)
- processing options (audit trail, pre-printed, supplied stationery, etc.)
- add-on services (audit trail, engagement tracking, etc.)

The process of preparing a template also ensures the suggested content will fit onto desired page size.

Templates work in similar way to Microsoft Word MailMerge. The text can contain placeholders, referencing variables submitted using API.

A typical template would look like this:

> Dear %COL[FirstName]%,
> 
> Thank you for booking your stay with our hotel.
> 
> We are looking forward to welcoming you on %COL[CheckInDate]%
> 
> Yours sincerely,
> Robert Smith
> Managing Director
> Woodlands Manor House Hotel

Templates can be very simple where whole text is supplied every time with an order or quite sophisticated and, for example, include author's signature. 

Please [get in touch](https://www.letterbot.co.uk/pages/contact-us) with us in order to set up one for your project.
Same account can have multiple templates.

Once template is set up we will provide you with a code you can use when placing order.

# Placing an Order

## Endpoint

To place an order send POST request to the following endpoint:

````
https://api.letterbot.co.uk/api/order
````

## Request

Request body:

```
{
	"Template": "TST",
	"Fields": {
		"FirstName": "John",
		"CheckInDate": "2nd December 2018"
	},
	"Address": {
		"Line1": "John Smith",
		"Line2": "Smith Enterprises Ltd",
		"Line3": "Cornwall Manor",
		"Line4": "2 High Street",
		"Line5": "Richmond",
		"Town": "London",
		"PostCode": "London",
		"CountryCode": "GBR"
	},
	"ExternalRef": "SO1201",
	"BulkDispatch": false
}
```

| Field | Description |  
|-----------|-----------|  
| Template | Template to use when processing request |
| Fields | Key-value pair collection. Values will be injected into template when writing |
| Address | Address where result need to be posted to |
| ExternalRef | Optional reference, for example your sales order or transaction number |
| BulkDispatch | Whether order need to be bundled and dispatched in bulk |
| CountryCode | Country code is an [ISO 3166-1 alpha 3](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) country code

When specifying BulkDispatch, all requests to the same address will be accumulated and dispatched based on frequency set up in template.


## Response

Successful response example:

````
{
    "template": "TEST",
    "fields": {
        "FirstName": "John",
        "CheckInDate": "2nd December 2018"
    },
    "cost": 1.18,
    "status": "Queued",
    "id": 2
}
````

| Field | Description |  
|-----------|-----------|  
| id | Request Id. Use this value to check status later |
| status | Request status. See Status Values for more details|
| cost | Amount that will be charged as soon as request is processed and completed |
| template | Requested template |

## Status Values

"status" field in response can have these values:

| Status | Description |  
|-----------|-----------|  
| Queued | Request has been queued for processing |
| Error | There is a problem with request. See errorDescription field for more information|
| Processed | The order has been processed and waiting to be posted |
| Complete | The order has been posted |


## Errors

### Validation error

Server response status: 400 Bad Request

Example payload:

````
{
    "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
    "title": "One or more validation errors occurred.",
    "status": 400,
    "traceId": "00-e4f35e4fffaa0241a6ff119a86b73ba1-f4478f8496e9454c-00",
    "errors": {
        "Template": [
            "Template code is required"
        ]
    }
}
````

### Authentication error

Server response status: 401 Unauthorized

## Pricing

Request prices are pre-set based on template code, destination country and bulk dispatch option. Please [get in touch](https://www.letterbot.co.uk/pages/contact-us) with us for more information.

# Check Order Status

To check order status please send GET request to the following URL:

````
https://api.letterbot.co.uk/api/order
````

The following filters available:

| Filter | Description |  
|-----------|-----------|  
| id | Request Id provided in response when sending a new order through |
| status | Request status, one of the following values: queued, processing, completed |
| lastUpdated | Returns records updated since provided date and time |

An example request is:

````
https://api.letterbot.co.uk/api/order?lastUpdated=2019-11-05T10:55:15&status=queued
````

Please note that result is limited to 1000 records.

Example response:
````
[
    {
        "template": "TEST",
        "fields": {
            "FirstName": "Romey"
            "LastDate": "22nd December"
        },
        "cost": 2.1500,
        "status": "Queued",
        "externalRef": "PO12192",
        "updated": "2019-08-01T08:32:40.5902347",
        "id": 2
    },
    {
        "template": "TEST",
        "fields": {
            "FirstName": "Jane",
            "LastDate": "27th December"
        },
        "cost": 1.7800,
        "status": "Queued",
        "externalRef": "PO12198",
        "updated": "2019-09-11T16:06:11.5901823",
        "id": 3
    }
]
````
