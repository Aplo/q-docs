---
title: Q API Reference

language_tabs:
  - shell
  - js

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='http://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to Q. The Q API allows you to request estimated subsidies, quote plans, and request producer licensing information for valid health insurance licenses.

We currently have an API library available for Node.js (in beta) and are working on making libraries in other languages available soon as well. You can use the tabs on the right to view code samples in the languages we support (currently Node and shell).

# Authentication

> Authentication using API is handled by passing in your credentials to the client. See the 'shell' tab for instructions on how to manually handle creating the proper request signature and header.

```shell
# Creating the signature
export CLIENT_ID=abc-123-456
export SECRET_TOKEN=fh29s9hf9vejsdaiKwAbde
echo -n "/plans/il/60452/" | openssl dgst -sha1 -hmac $SECRET_TOKEN
#=> "c48ad0f7807dc5820bfcb05c9168c3fc1be7c668"

curl -i \
  -H "Authorization: "abc-123-456:c48ad0f7807dc5820bfcb05c9168c3fc1be7c668" \
  https://q.aploquote.com/plans/il/60452/
```

```js
// Our Node library handles this automatically
```

> Make sure to replace the client ID and secret token with your own API credentials and to generate a unique signature for each request.

Q uses a combination of public API keys and request signing to authorize all requests made to the API. Q expects an `Authorization` header to be sent with every request in the format of `Authorization: CLIENT_ID:SIGNATURE`.

## Authorization header

```shell
"Authorization: "abc-123-456:c48ad0f7807dc5820bfcb05c9168c3fc1be7c668" https://q.aploquote.com/plans/il/60452/
```

```js
// ...
```

The Authorization header uses your client ID and request signature concatenated by a `:` character.

## Request signatures

Request signatures are an HMAC hash of the URI you're sending the request to, any query parameters present in the request, and any request body you are sending if the request is a `POST/PUT/PATCH`. The HMAC should use `sha1` as the algorithm and return a hex digest, not bytes as some languages return by default.

# Quotes with Subsidy

```shell
curl -H "Authorization my-client-id:generated-request-signature" 
  https://q.aploquote.com/plans/il/60654
```

```js
var QClient = require('q-client');

var q = new QClient(key, token);
q.getSubsidy('il', '60452', function(response) {
  res.json(response);
});
```

> The above command will create a JSON request body. Request bodies have the following available options:

```json
{
  "first_name": "Joe",
  "last_name": "Example",
  "email": "joe@example.org",
  "phone": "1-800-111-2345",
  "household_size": 4,
  "zip_code": 60452,
  "age": 29,
  "dob": "10-15-85",
  "household_income": 33000,
  "tobacco_use": false,
  "dependents": {
    "spouse": {
      "age": 28,
      "dob": "12-17-86",
      "tobacco_use": true
    },
    "children": [
      {
        "age": 19
      }
    ]
  }
}
```

> The above command returns JSON structured like this:

```json
{
  "status": "ok",
  "subsidy": 391.26,
  "quotes": [
    {
      "state": "IL",
      "county": "Cook",
      "metal_level": "Bronze",
      "issuer": "Land of Lincoln Mutual Health Insurance Company",
      "plan_id": "79763IL0500003",
      "plan_name": "Adventist Land of Lincoln Bronze PPO 5000",
      "plan_type": "PPO",
      "child_only": "Allows Adult and Child-Only",
      "customer_service_number_local": "1-888-858-9130",
      "customer_service_number_free": "1-888-858-9130",
      "customer_service_number_tty": null,
      "network_url": "https://www.landoflincolnhealth.org/find-a-doctor/",
      "plan_brochure_url": "https://www.landoflincolnhealth.org/wp-content/uploads/2014/10/I-BR-Adventist.pdf",
      "benefits_summary_url": "https://www.landoflincolnhealth.org/wp-content/uploads/2014/10/I-050B-SBC-Adventist.pdf",
      "drug_formulary_url": "https://www.landoflincolnhealth.org/shop-for-plans/formulary",
      "adult_dental": false,
      "child_dental": true,
      "premium_child": 94,
      "deductible": "$10,000",
      "drug_deductible": "Included in Medical",
      "moop": "$13,200",
      "drug_moop": "Included in Medical",
      "pcp_copay": "$50",
      "specialist_copay": "25% Coinsurance after deductible",
      "emergency_room_copay": "$500",
      "inpatient_facility_copay": "25% Coinsurance after deductible",
      "inpatient_physician_copay": "25% Coinsurance after deductible",
      "generic_drugs": "25% Coinsurance after deductible",
      "preferred_brand_drugs": "25% Coinsurance after deductible",
      "non_preferred_brand_drugs": "25% Coinsurance after deductible",
      "specialty_drugs": "25% Coinsurance after deductible",
      "std_rate": "0-20:94.39,28:161.57,29:166.33",
      "tobacco_rate": "0-20:94.39,28:169.65,29:174.65",
      "premium": 422.29
    }
  ]
}
```

This endpoint will return all the health plans for the rating scenario you post to it along with the estimated subsidy for the applicant's rating scenario.

### HTTP Request

`POST https://q.aploquote.com/plans/:state/:zipcode`

You should pass a 2 letter state code and 5 digit zip code to the `/plans/` endpoint along with the JSON shown to the right in your request body. __If the state is unknown or you only have a zip code__, simply pass `none` for the state code. A zip code is *always* required. A state is also required with the only exception being the ability to pass `none` when you are unable to. Passing `state` in your request will generate a faster response from the API.

### URI Parameters

Parameter | Default | Description | Required
--------- | ------- | ----------- | --------
state | null | 2 letter state code or `none` if not available. | true
zipcode | null | 5 digit zip code for the requestor. | true

<aside class="info">
Both <code>zipcode</code> and <code>state</code> are always required.
</aside>

### Request Body

Property | Type | Description | Required
-------- | ---- | ----------- | --------
first_name | String | First name of the primary applicant | false
last_name | String | Last name of primary applicant | false
email | String | Email address of the primary applicant | false
phone | String | The primary applicant's phone number | false
household_size | Integer | How many people in the household (May differ from number of dependents) | true
zip_code | Integer | The same zip code you pass in the URI | false
age | Integer | Age of the primary applicant | If missing then `dob` must be present in the request
dob | String | Primary applicant's date of birth (MM-DD-YYYY format) | If missing then `age` is required
household_income | Integer | Household income for the applicant | true
tobacco_use | Boolean | True if the applicant has used tobacco 4 or more times in the last 6 months | true
dependents | Object | Contains spouse and dependent info | false
dependents.spouse | Object | Contains age and tobacco use information about any spouse | false
dependents.spouse.age | Integer | Age of the spouse | Required if `dob` is not present
dependents.spouse.dob | String | Spouse's date of birth (MM-DD-YYYY format) | Required if `age` is not present
dependents.spouse.tobacco_use | Boolean | Whether the spouse uses tobacco | true
dependents.children | Array | An array of objects representing any dependents of the primary applicant | false
dependents.children.age | Integer | The age of any child of the primary applicant | Required if `dob` is missing
dependents.children.dob | String | The date of birth of any children listed | Required of `age` is missing
