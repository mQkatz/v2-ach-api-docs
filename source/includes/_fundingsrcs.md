# Funding sources

Add and retrieve ACH bank account information via funding sources, which are available to the `Customers` and `Accounts` resources.  Customers can have a maximum of 6 funding sources.

### Funding source resource

Parameter | Description
----------|------------
id | The funding source unique identifier
status | Is the funding source verified?
type | Type of funding source
name | Customer’s arbitrary nickname for the funding source
created | ISO-8601 timestamp

```noselect
{
    "routingNumber": "222222226",
    "accountNumber": "123456789",
    "type": "checking",
    "name": "My Bank"
}
```

## Create a funding source

Create a new funding source for an `Account` or `Customer`.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth access token with the `Funding` <a href="#oauth-scopes">scope</a>.</li>
</ol>

### HTTP request
`POST https://api.dwolla.com/funding-sources`

### Request parameters
Parameter | Description
----------|------------
accountNumber | The bank account number.
routingNumber | The bank account's routing number.
type | Type of bank account: `checking` or `savings`.
name | Arbitrary nickname for the funding source.

### Errors
| HTTP Status | Message |
|--------------|-------------|
| 400 | Duplicate funding source or validation error.
| 403 | Not authorized to create funding source.


### Request and response

```noselect
POST /funding-sources
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY
{
    "routingNumber": "87654321",
    "accountNumber": "12345678",
    "type": "checking",
    "name": "My Bank"
}

...

HTTP/1.1 201 Created
Location: https://api-uat.dwolla.com/funding-sources/04173e17-6398-4d36-a167-9d98c4b1f1c3
```

## Get a funding source by id

This section covers how to retrieve a funding source by id.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth access token with the `Funding` <a href="#oauth-scopes">scope</a>.</li>
</ol>

### HTTP request
`GET https://api.dwolla.com/funding-sources/{id}`

### Request parameters

Parameter | Optional? | Description
----------|------------|-------------
id | no | id of funding source to retrieve.

### Errors
| HTTP Status | Message |
|--------------|-------------|
| 404 | Funding source not found. |

### Request and response

```raw
GET https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

...

{
  "_links": {
    "self": {
      "href": "https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c"
    },
    "customer": {
      "href": "https://api.dwolla.com/customers/36e9dcb2-889b-4873-8e52-0c9404ea002a"
    },
    "initiate-micro-deposits": {
      "href": "https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c/micro-deposits"
    }
  },
  "id": "692486f8-29f6-4516-a6a5-c69fd2ce854c",
  "status": "unverified",
  "type": "bank",
  "name": "Test checking account",
  "created": "2015-10-23T20:37:57.137Z"
}
```
```ruby
fund_source = 'https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c'

retrieved = DwollaSwagger::FundingsourcesApi.id(fund_source)
p retrieved.name # => "Test checking account"
```
```php
<?php
$fund_source = 'https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c';

$fsApi = DwollaSwagger\FundingsourcesApi($apiClient);

$retrieved = $fsApi->id($fund_source);
print($retrieved->name); # => "Test checking account"
?>
```
```python
fund_source = 'https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c'

fs_api = dwollaswagger.FundingsourcesApi(client)
retrieved = fs_api.id(fund_source)

print(retrieved.name) # => Test checking account
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['funding-sources'].id({id: '692486f8-29f6-4516-a6a5-c69fd2ce854c'})
    .then(function(data) {
       console.log(data.obj._embedded.name); // Test checking account
    });
});
```

## Initiate or verify micro-deposits

This section covers how to initiate and verify micro-deposits for bank verification. Reference the [funding source verification](https://developers.dwolla.com/resources/funding-source-verification.html) resource article for more information on the micro-deposit method of bank account verification.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth access token with the `Funding` <a href="#oauth-scopes">scope</a>.</li>
</ol>

### HTTP request
`POST https://api.dwolla.com/funding-sources/{id}/micro-deposits`

### Request parameters

Parameter | Optional? | Description
----------|------------|-------------
amount1 | no | An amount JSON object of first micro-deposit. Contains `value` and `currency`.
amount2 | no | An amount JSON object of first micro-deposit. Contains `value` and `currency`.


### Errors
| HTTP Status | Message |
|--------------|-------------|
| 200 | Micro deposits verified.  |
| 201 | Micro deposits initiated. |
| 400 | Funding source not found. |
| 404 | Funding source not found. |

#### Request and response (Initiate)

```raw
POST /funding-sources/e52006c3-7560-4ff1-99d5-b0f3a6f4f909/micro-deposits
Authorization: Bearer 8tJjM7iTjujLthkbVPMUcHLqMNw4uv5kG712g9j1RRBHplGpwo
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Cache-Control: no-cache

HTTP 200 OK
```
```ruby
DwollaSwagger::FundingsourcesApi.micro_deposits(new_fs)
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['funding-sources'].microDeposits()
    .then(function(data) {
       console.log(data.status); // 200
    });
});
```
```python
fs_api = dwollaswagger.FundingsourcesApi(client)
fs_api.micro_deposits(new_fs)
```
```php
<?php
$fsApi = new DwollaSwagger\FundingsourcesApi($apiClient);
$fsApi->micro_deposits($new_fs);
?>
```

#### Request and response (Verify)

```raw
POST /funding-sources/e52006c3-7560-4ff1-99d5-b0f3a6f4f909/micro-deposits 
Authorization: Bearer 8tJjM7iTjujLthkbVPMUcHLqMNw4uv5kG712g9j1RRBHplGpwo
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json

{
    "amount1": {
        "value": "0.03",
        "currency": "USD"
    },
    "amount2": {
        "value": "0.09",
        "currency": "USD"
    }
}

HTTP 200 OK
```
```ruby
DwollaSwagger::FundingsourcesApi.micro_deposits(new_fs, {
    "amount1" => {
           "value" => "0.03",
        "currency" => "USD"
    },
    "amount2" => {
           "value" => "0.09",
        "currency" => "USD"
    }
})
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['funding-sources'].microDeposits({id: 'e52006c3-7560-4ff1-99d5-b0f3a6f4f909', body: {
        "amount1": {
            "value": "0.03",
            "currency": "USD"
        },
        "amount2": {
            "value": "0.09",
            "currency": "USD"
        }
    }})
    .then(function(data) {
       console.log(data.status); // 200
    });
});
```
```python
fs_api = dwollaswagger.FundingsourcesApi(client)

fs_api.micro_deposits(new_fs, {
    "amount1": {
        "value": "0.03",
        "currency": "USD"
    },
    "amount2": {
        "value": "0.09",
        "currency": "USD"
    }
})
```
```php
<?php
$fsApi = new DwollaSwagger\FundingsourcesApi($apiClient);

$fsApi->micro_deposits($new_fs, array (
  'amount1' => 
  array (
    'value' => '0.03',
    'currency' => 'USD',
  ),
  'amount2' => 
  array (
    'value' => '0.09',
    'currency' => 'USD',
  ),
));
?>
```

## Verify pending micro-deposits exist

This section shows how to check if pending verification for micro-deposits exists.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth access token with the `Funding` <a href="#oauth-scopes">scope</a>.</li>
</ol>

### HTTP request
`GET https://api.dwolla.com/funding-sources/{id}/micro-deposits`

### Request parameters

Parameter | Optional? | Description
----------|------------|-------------
id | no | id of funding source to check status of validation deposits.

### Errors
| HTTP Status | Message |
|--------------|-------------|
| 200 | Pending micro-deposits exist. |

### Request and response

```noselect
GET /funding-sources/ab9cd5de-9435-47af-96fb-8d2fa5db51e8
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

{
  "_links": {
    "self": {
      "href": "https://api-uat.dwolla.com/funding-sources/6c7de5af-e73e-45a9-946a-cdadec1ee0bb/micro-deposits"
    },
    "verify-micro-deposits": {
      "href": "https://api-uat.dwolla.com/funding-sources/6c7de5af-e73e-45a9-946a-cdadec1ee0bb/micro-deposits"
    }
  }
}
```

## Remove a funding source

Remove a funding source by id.

<ol class="alerts">
    <li class="alert icon-alert-alert">This endpoint <a href="#authentication">requires</a> an OAuth access token with the `Funding` <a href="#oauth-scopes">scope</a>.</li>
</ol>

### HTTP request
`DELETE https://api.dwolla.com/funding-sources/{id}`

### Request parameters

Parameter | Optional? | Description
----------|------------|-------------
id | no | id of funding source to delete.

### Errors
| HTTP Status | Message |
|--------------|-------------|
| 404 | Funding source not found. |

### Request and response

```raw
DELETE /funding-sources/6c8ac833-444a-4eff-979c-c56cef6be26b
Content-Type: application/vnd.dwolla.v1.hal+json
Accept: application/vnd.dwolla.v1.hal+json
Authorization: Bearer pBA9fVDBEyYZCEsLf/wKehyh1RTpzjUj5KzIRfDi0wKTii7DqY

HTTP 200 OK
```
```ruby
fund_source = 'https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c'

DwollaSwagger::FundingsourcesApi.delete(fund_source)
```
```php
<?php
$fund_source = 'https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c';

$fsApi = DwollaSwagger\FundingsourcesApi($apiClient);

$fsApi->delete($fund_source);
?>
```
```python
fund_source = 'https://api.dwolla.com/funding-sources/692486f8-29f6-4516-a6a5-c69fd2ce854c'

fs_api = dwollaswagger.FundingsourcesApi(client)
fs_api.delete(fund_source)
```
```javascript
dwolla.then(function(dwolla) {
    dwolla['funding-sources'].delete({id: '692486f8-29f6-4516-a6a5-c69fd2ce854c'})
    .then(function(data) {
       console.log(data.status); // 200
    });
});
```