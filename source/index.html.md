---

title: Estonian Internet Foundation's REST EPP

language_tabs:
   - shell

toc_footers:
   - <a href='https://internet.ee'>Estonian Internet Foundation</a>
   - <a href='https://github.com/internetee/registry'>Registry project</a>
includes:
   - errors

search: true
code_clipboard: true
---

# Introduction

Welcome to the Estonian Internet Foundation's REST EPP (REPP for short) API documentation! You can use our API endpoints, which can mimic almost the full functionality of our EPP protocol.

We have language bindings in Shell. You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

This production API project is only available for our active registrars. You can find more information about becoming registrar at our [wiki page](https://www.internet.ee/registrars/terms-and-conditions-for-becoming-a-registrar).

<aside class="success">
Remember — This is a sneak-peek of our REPP service, which isn't in production yet. For now, please rely on standard EPP implementation.
</aside>

# Environments

Production endpoint: https://registrar.internet.ee/repp/v1/

Sandbox endpoint: https://testrar.internet.ee/repp/v1/

Production environment is only available for our active registrars.

Access to our sandbox environment is not publicly available. We can grant access to third parties upon request.

# Authentication

REPP uses API keys and certificates to allow access to our API. Certificates are the same ones that are used for EPP protocol.

API key, in a nutshell, is a Basic authorization key that consists of ApiUser's username and password, separated by colon, in Base64 format.

For example if your username is `test` and password `test123`, , you have to encode `test:test123` to Base64 and put it into `Authorization` header of each request.

For example:

`Authorization: Basic dGVzdDp0ZXN0MTIz`

<aside class="notice">
You must replace <code>dGVzdDp0ZXN0MTIz</code> with your personal API key.
</aside>

# Billing

## Get account balance

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/accounts/balance' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "balance": "420.0",
        "currency": "EUR"
    }
}
```

> Command above with detailed=true flag returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "balance": "432.00",
        "currency": "EUR",
        "transactions": [
            {
                "created_at": "2027-03-05T10:00:00.000+02:00",
                "description": "Refund automatic renewal example.ee",
                "type": "credit",
                "sum": "12.00",
                "balance": "432.00"
            },
            {
                "created_at": "2021-02-26T17:12:46.045+02:00",
                "description": "Create abadiitas.ee",
                "type": "debit",
                "sum": "-6.0",
                "balance": "420.0"
            },
            {
                "created_at": "2021-02-11T11:32:49.158+02:00",
                "description": "Invoice no. 131096",
                "type": "credit",
                "sum": "100.0",
                "balance": "426.0"
            }
        ]
    }
}
```
Get account balance.

Automatic-renewal charges use account activity type `renew`. Reversals create a separate activity of type `refund`, crediting the exact original charge to the original payer's cash account. In detailed balance transactions, a refund has `type: "credit"` and a positive `sum`; the activity type is `refund`. Descriptions and amounts in examples are illustrative. A refund is recorded in the month it occurs, including when the renewal charge was in an earlier month.

### HTTP Request

`GET /repp/v1/accounts/balance`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
detailed | False | Set to true to get detailed transaction data
from | False | Show detailed transactions starting from datetime (yyyy-mm-dd)
until | False | Show detailed transactions created before datetime (yyyy-mm-dd)
# Poll messages

## Get all messages
```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/registrar/notifications/all_notifications' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

```json
{
    "code": 1000,
    "message": "Command completed successfully. Returning 200 out of 500. Use URL parameters :limit and :offset to list other messages if needed",
    "data": {
        "id": 1399,
        "text": "Registrant rejected domain update: koer.ee",
        "attached_obj_type": "Epp::Domain",
        "attached_obj_id": 34
    }
}
```

Get the list of unread pool messages.

### HTTP Request

`GET /repp/v1/registrar/notifications/all_notifications`

### URL Parameters

Parameter | Required | Default | Description
--------- | ------- | ------- | -----------
limit | No | 200 | How many objects to return
offset | No | 0 | Object query offset

## Get latest unread poll message

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/registrar/notifications' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "id": 1399,
        "text": "Registrant rejected domain update: koer.ee",
        "attached_obj_type": "Epp::Domain",
        "attached_obj_id": 34
    }
}
```

Get the latest unread poll message

### HTTP Request

`GET /repp/v1/registrar/notifications`

## Mark poll message as read

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/registrar/notifications/1398' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "notification": {
        "read": true
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "notification_id": 1398,
        "read": true
    }
}
```

Mark poll message as read

### HTTP Request

`GET /repp/v1/registrar/notifications/:notification_id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
notification_id | Yes | Notification ID

### Payload Parameters

Parameter | Required | Description
--------- | ------- | -----------
read | Yes | Set to true to mark as read

## Get a specific poll message

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/registrar/notifications/1398' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "id": 1398,
        "text": "Contact ATSAA:57C8A9CB has been updated by registrant",
        "attached_obj_type": null,
        "attached_obj_id": null
    }
}
```

Get a specific poll message

### HTTP Request

`PUT /repp/v1/registrar/notifications/:notification_id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
notification_id | Yes | Notification ID

## Automatic-renewal poll messages

Registry-initiated automatic-renewal messages have `attached_obj_type: "DomainAutoRenewal"`. The `attached_obj_id` identifies the attached record; use the UUID in `attached_obj_data.cycle.id` for reversal requests. Notifications and the registrar summary include structured `attached_obj_data`:

```json
{
    "event": "renewed",
    "domain": "example.ee",
    "cycle": {
        "id": "3f7c2a5e-0000-0000-0000-000000000000",
        "state": "autoRenewPeriod",
        "original_expire_time": "2027-03-01T00:00:00+02:00",
        "expire_time": "2028-03-01T00:00:00+02:00",
        "cancellation_deadline": "2027-03-16T00:00:00+02:00",
        "attempts": 1
    }
}
```

Events are `renewed`, `fundingWarning`, `renewProhibited`, and `renewalOverdue`. See [Automatic renewal](#automatic-renewal) for when they are sent and the [poll message documentation](https://internetee.github.io/poll-apidoc/) for text and EPP examples. Registrar-requested enable, disable and reversal commands produce no automatic-renewal poll message.

# Contacts

## Get all contacts

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/contacts' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "contacts": [
        "ATSAA:15554596",
        "ATSAA:5767A313",
        "ATSAA:634CF14B",
    ],
    "total_number_of_records": 3
}
```

Get all contacts

### HTTP Request

`GET /repp/v1/contacts`

## Get a specific contact

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/contacts/ATSAA:KARL' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "id": "ATSAA:KARL",
        "name": "John Doe",
        "ident": {
            "code": "12345678901",
            "type": "priv",
            "country_code": "EE"
        },
        "email": "xxx@xxx.ee",
        "phone": "+xxx",
        "auth_info": "ed33c67bedb32b1567b131",
        "statuses": [
            "ok",
            "linked"
        ],
        "disclosed_attributes": []
    }
}
```

Get a specific contact

### HTTP Request

`GET /repp/v1/contacts/:contact_id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
contact_id | Yes | Contact ID

## Delete a specific contact

```shell
curl --location --request DELETE 'https://testrar.internet.ee/repp/v1/contacts/ATSAA:KARL' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {}
}
```

> In case contact can not be destroyed, it returns a JSON structured like this:

```json
{
    "code": 2305,
    "message": "Object association prohibits operation [domains]",
    "data": {}
}
```

Delete a specific contact

### HTTP Request

`DELETE /repp/v1/contacts/:contact_id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
contact_id | Yes | Contact ID

## Create a new contact

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/contacts' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "contact": {
        "name": "John Doe",
        "email": "john@doe.com",
        "phone": "+372.xxx",
        "ident": {
            "ident": "xxx",
            "ident_type": "priv",
            "ident_country_code": "EE"
        }
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "contact": {
            "id": "ATSAA:84F23FD1"
        }
    }
}
```

Create a new contact

### HTTP Request

`POST /repp/v1/contacts`

### Payload Parameters

Parameter | Required | Description
--------- | ------- | -----------
id | No | Custom contact id for contact
name | Yes | Full name of contact
email | Yes | Email address of contact
phone | Yes | phone number of contact, with '.' as CC separator
ident[ident] | Yes | Identity / Registry code of contact
ident[ident_type] | Yes | Type of ident number (priv/org/birthday)
ident[ident_country_code] | Yes | Nationality of contact in alpha-2 format

<aside class="notice">
Note — Wrap your payload attributes into "contact" object, as shown in example.
</aside>

## Update an existing contact

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/contacts/ATSAA:84F23FD1' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "contact": {
        "name": "John Doee",
        "email": "john@doee.com",
        "phone": "+372.590146111"
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "contact": {
            "id": "ATSAA:84F23FD1"
        }
    }
}
```

Update an existing contact

### HTTP Request

`PUT /repp/v1/contacts/:contact_id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
contact_id | Yes | Contact ID

### Payload Parameters

Parameter | Required | Description
--------- | ------- | -----------
name | No | Full name of contact
email | No | Email address of contact
phone | No | phone number of contact, with '.' as CC separator

<aside class="notice">
Note — Wrap your payload attributes into "contact" object, as shown in example.
</aside>

## Check contact code availability

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/contacts/check/PUDERJAKAPSAS' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "contact": {
            "id": "PUDERJAKAPSAS",
            "available": true
        }
    }
}
```

Checks whether the contact code is available for custom use

### HTTP Request

`GET /repp/v1/contacts/check/:contact_id`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
contact_id | Yes | Contact ID to check

# Domains

## Get all existing domains

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/domains' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domains": [
            "kass.ee",
            "koer.ee"
        ],
        "count": 69
    }
}
```

> When ?details=true is appended, it returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domains": [
            {
                "name": "kass.ee",
                "registrant": "ATSAA:KARL",
                "created_at": "2021-01-29T11:15:52.753+02:00",
                "updated_at": "2021-01-29T11:15:52.753+02:00",
                "expire_time": "2022-01-30T00:00:00.000+02:00",
                "auto_renew": {
                    "enabled": false,
                    "cycle": null
                },
                "rgp_statuses": [],
                "outzone_at": null,
                "delete_date": null,
                "force_delete_date": null,
                "contacts": [
                    {
                        "code": "ATSAA:KARL",
                        "type": "AdminDomainContact"
                    },
                    {
                        "code": "ATSAA:KARL",
                        "type": "TechDomainContact"
                    }
                ],
                "nameservers": [],
                "dnssec_keys": [],
                "statuses": [
                    "inactive"
                ],
                "registrar": {
                    "name": "Kreative Digital OÜ",
                    "website": "https://kreative.ee"
                },
                "transfer_code": "2f81ec671b69a2aa5d6375631e259ae7"
            },
            {
                "name": "koer.ee",
                "registrant": "ATSAA:KARL",
                "created_at": "2021-01-29T11:16:23.679+02:00",
                "updated_at": "2021-01-29T11:16:23.679+02:00",
                "expire_time": "2022-01-30T00:00:00.000+02:00",
                "auto_renew": {
                    "enabled": false,
                    "cycle": null
                },
                "rgp_statuses": [],
                "outzone_at": null,
                "delete_date": null,
                "force_delete_date": null,
                "contacts": [
                    {
                        "code": "ATSAA:KARL",
                        "type": "AdminDomainContact"
                    },
                    {
                        "code": "ATSAA:KARL",
                        "type": "TechDomainContact"
                    }
                ],
                "nameservers": [],
                "dnssec_keys": [],
                "statuses": [
                    "inactive"
                ],
                "registrar": {
                    "name": "Kreative Digital OÜ",
                    "website": "https://kreative.ee"
                },
                "transfer_code": "asdf564cs89er3dg46d5f3v1df6534g3f2"
            }
        ],
        "count": 69
    }
}
```

Gets all existing domains under current registrar account. With `details=true`, both the full response and the reduced `simple=true` response include `auto_renew` and `rgp_statuses`, using the fields described in [Automatic renewal](#automatic-renewal). Without details, the list contains domain names only.

### HTTP Request

`GET /repp/v1/domains`

### URL Parameters

Parameter | Required | Default | Description
--------- | -------- | ------- | -----------
limit | No | | How many objects to return
offset | No | 0 | Object query offset
details | No | false | Show serialized domain objects
simple | No | false | With details=true, show reduced domain objects
q[auto_renew_eq] | No | | true selects enabled preferences; false selects disabled preferences
q[auto_renew_grace] | No | | true selects domains in active paid grace; false adds no restriction
q[auto_renew_funding_retry] | No | | true selects domains with an open funding retry cycle; false adds no restriction

Filters can be combined with `limit` and `offset`. For example, `GET /repp/v1/domains?details=true&simple=true&q[auto_renew_grace]=true` lists domains whose paid cancellation window is still open. Funding retry does not set an RGP grace status.

## Get a specific domain

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/domains/biz.ee' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "biz.ee",
            "registrant": "ATSAA:749AA80F",
            "created_at": "2021-01-15T12:07:20.079+02:00",
            "updated_at": "2021-01-21T16:41:59.707+02:00",
            "expire_time": "2023-01-16T00:00:00.000+02:00",
            "auto_renew": {
                "enabled": false,
                "cycle": null
            },
            "rgp_statuses": [],
            "outzone_at": null,
            "delete_date": null,
            "force_delete_date": null,
            "contacts": [
                {
                    "code": "ATSAA:C7A52A30",
                    "type": "AdminDomainContact"
                },
                {
                    "code": "ATSAA:CC53EF9C",
                    "type": "TechDomainContact"
                }
            ],
            "nameservers": [],
            "dnssec_keys": [],
            "statuses": [
                "inactive"
            ],
            "registrar": {
                "name": "Kreative Digital OÜ",
                "website": "https://kreative.ee"
            },
            "transfer_code": "2f81ec671b69a2aa5d6375631e259ae7"
        }
    }
}
```

Returns a specific domain object, even if it doesn't belong to your registrar account.
If you aren't the sponsoring registrar for the domain, `transfer_code` and `auto_renew` are omitted from the response. `rgp_statuses` is returned to everyone allowed to view the domain; it contains `autoRenewPeriod` only during active paid grace and is otherwise empty. See [Automatic renewal](#automatic-renewal) for the preference and cycle fields.

### HTTP Request

`GET /repp/v1/domains/:domain_name`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | No | Domain name

## Register a new domain

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "domain": {
        "name": "kasskoer.ee",
        "registrant": "ATSAA:KARL",
        "period": 1,
        "period_unit": "y",
        "auto_renew": true,
        "admin_contacts": [
            "ATSAA:KARL"
        ],
        "tech_contacts": [
            "ATSAA:KARL"
        ],
        "nameservers_attributes": [
            {
                "hostname": "ns1.kreative.ee"
            }
        ]
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "kasskoer.ee"
        }
    }
}
```

Register a new domain. `domain[auto_renew]` is optional and defaults to false when omitted. It accepts JSON booleans `true` / `false` and strings `"true"` / `"false"`; other values return code `2005`. Enabling when automatic renewal is unavailable returns `2306`. A reversal cycle ID is not allowed on create (`2306`).

### HTTP Request

`POST /repp/v1/domains`

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
name | Yes | String | Domain name
reserved_pw | No | String | Reserved password for domain
transfer_code | No | String | Desired transfer code for domain
auto_renew | No | Boolean | Enable automatic renewal at expiry; omitted means false
registrant | Yes | String | Registrant contact code
period_unit | Yes | String | Period unit. Can be year (y) or month (m)
period | Yes | Integer | For how many period units to register domain
admin_contacts | No | Array | Array of admin domain contact codes
tech_contacts | No | Array | Array of tech domain contact codes
nameservers_attributes | No | Array | Array of nameserver objects
nameservers_attributes[hostname] | Yes | String | Hostname of nameserver
nameservers_attributes[ipv4] | Yes | Array | Array of IPv4 attributes
nameservers_attributes[ipv6] | Yes | Array | Array of IPv6 attributes
dnskeys_attributes | No | Array | Array of DNSSEC key attributes
dnskeys_attributes[flags] | Yes | String | 256 (KSK) or 257 (ZSK)
dnskeys_attributes[protocol] | Yes | String | Key protocol (3)
dnskeys_attributes[alg] | Yes | String | DNSSEC key algorithm (3,5,6,7,8,10,13,14)
dnskeys_attributes[public_key] | Yes | String | DNSSEC public key

<aside class="notice">
Note — Wrap your payload attributes into "domain" object, as shown in example.
</aside>

## Change domain registrant / Auth code

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/domains/kasskoer.ee' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "domain": {
        "registrant": {
            "code": "ATSAA:LIZ"
        },
        "transfer_code": "123"
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "kasskoer.ee"
        }
    }
}
```

Changes domain registrant, auth code or automatic-renewal preference. `domain[auto_renew]` omitted means unchanged; explicit false disables future automatic renewals without refunding a paid cycle. Only the sponsoring registrar may change automatic renewal (`2201`). Accepted booleans are `true`, `false`, `"true"`, and `"false"` (`2005` otherwise).

To reverse a paid cycle, supply its UUID in `domain[auto_renew_cycle_id]`, either alone or with `domain[auto_renew]: false`:

```json
{
    "domain": {
        "auto_renew": false,
        "auto_renew_cycle_id": "3f7c2a5e-0000-0000-0000-000000000000"
    }
}
```

Reversal disables the preference and refunds once. Repeating the same reversed cycle succeeds without another refund. An unknown or wrong-domain cycle returns `2303`; a non-reversible cycle or a new reversal at or after its deadline returns `2304`. Combining true with a cycle ID returns `2306`. Failure rolls back the entire domain update. See [Automatic renewal](#automatic-renewal) for expiry adjustment and cancellation rules. The dedicated endpoint below returns the detailed reversal result.

### HTTP Request

`PUT /repp/v1/domains/:domain_name`

### URL Parameters
Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
domain_name | Yes | String | Domain


### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
registrant | No | Object | New registrant object
registrant[code] | Yes | String | Contact ID of new registrant
registrant[verified] | No | Boolean | Possible values are true / false. Defaults to false. Include it only if set to true.
transfer_code | No | String | New EPP transfer code for domain
auto_renew | No | Boolean | Future renewal preference; omitted means unchanged, false alone does not refund
auto_renew_cycle_id | No | String | Explicit cycle UUID to reverse; may be supplied alone or with auto_renew=false

<aside class="notice">
Note — Wrap your payload attributes into "domain" object, as shown in example.
</aside>

## Automatic renewal

Automatic renewal is off by default and is managed by the sponsoring registrar. When enabled, the registry attempts renewal at expiry for one calendar year, charging the registrar's cash account at the tariff valid at the original expiry. Enabling the preference for an already expired, still-existing domain also triggers automatic renewal, including while it is out of zone or in redemption. Each original expiry has one cycle, identified by a UUID. When automatic renewal is unavailable, new opt-ins fail with `2306`; eligible reversals of existing cycles remain available.

Payment before the cancellation deadline starts paid grace: `rgp_statuses` contains `autoRenewPeriod` (RFC 3915), separately from ordinary domain `statuses`. Cancellation is available strictly before the original expiry plus 15 calendar days in Europe/Tallinn. Use the returned `cancellation_deadline` timestamp, including its time zone. The deadline only bounds the paid-grace reversal window; it does not stop renewal retries. Grace does not itself prevent edits or transfers; other domain restrictions still apply.

Insufficient funds or a renewal-blocking status (including `pendingDelete` and `pendingUpdate`, a pending registrant change) leaves the cycle in `fundingRetry`. The registry retries every 15 minutes, including past the cancellation deadline, until renewal succeeds, the registrar explicitly opts out or renews manually, the domain is transferred, or the domain is deleted. Failed attempts do not change the preference. A later top-up can allow the same cycle to succeed. At the deadline, an unpaid cycle receives one `renewalOverdue` poll message; ordinary expiry processing continues while renewal attempts continue.

A successful renewal always extends from the original expiry by one calendar year and restores the domain to the zone like a manual renewal, clearing expiry-related statuses and dates. Success before the deadline leaves only the remaining cancellation window. Success at or after the deadline goes immediately to `completed`, without `autoRenewPeriod`, and cannot be reversed.

Disabling without a cycle ID is a future opt-out: it cancels a funding retry but leaves a paid renewal and its cancellation window intact. To cancel a paid renewal and obtain a refund, explicitly submit its cycle ID before the deadline. Reversal disables the preference, credits the exact original charge once, and removes the automatic year. If no other time was purchased, expiry returns to the original date; otherwise independently purchased time is preserved by subtracting only the automatic year. If the resulting expiry is in the past, ordinary expiry statuses and dates apply; reversal does not delete the domain. Repeating the same reversed cycle is successful without another refund, even after the deadline. A new reversal at or after the deadline fails with `2304` and changes nothing.

When paid grace ends normally, the cycle becomes `completed`, the paid year remains, and the preference stays enabled. A completed transfer disables the preference. A funding retry is cancelled on transfer; a paid cycle becomes completed, keeping its paid year without refund. The gaining registrar is never charged for that cycle and must explicitly opt in for future automatic renewals.

The registry sends automatic-renewal poll messages for these events only:

Event | When sent
----- | ---------
renewed | A scheduled renewal succeeds, including success after funding retries
fundingWarning | First insufficient-funds warning in the cycle, once
renewProhibited | First renewal-blocking warning in the cycle, once; also used for a missing renewal tariff
renewalOverdue | Once at the cancellation deadline for an unpaid cycle; renewal attempts continue

Illustrative `renewalOverdue` text: "Domain example.ee was not automatically renewed by 2027-03-16 due to insufficient funds; domain goes out of zone. Renewal attempts continue until renewal succeeds, the registrar opts out or renews manually, the domain is transferred, or the domain is deleted."

Normal grace completion sends no poll message. Registrar-requested enable, disable and reversal commands return their outcome in the command response and send no automatic-renewal poll message. Transfer completion produces no extra automatic-renewal poll message; existing transfer notifications still apply.

### Response fields

Domain info and portfolio objects (both full and reduced forms) include the following fields. `auto_renew` is sponsor-only; `rgp_statuses` is visible to anyone allowed to view domain info.

Field | Type | Description
----- | ---- | -----------
auto_renew.enabled | Boolean | Preference for future automatic renewal; independent of cycle state
auto_renew.cycle | Object or null | Latest cycle, or null if no cycle exists
auto_renew.cycle.id | String | Public cycle UUID used for reversal and idempotency
auto_renew.cycle.state | String | fundingRetry, autoRenewPeriod, completed, reversed, or cancelled
auto_renew.cycle.original_expire_time | String | Original expiry timestamp in ISO 8601 format
auto_renew.cycle.expire_time | String or null | Expiry applied by the automatic renewal; null before payment succeeds
auto_renew.cycle.cancellation_deadline | String | Fixed paid-grace reversal deadline in ISO 8601 format; retries continue past it
auto_renew.cycle.attempts | Integer | Number of renewal attempts
rgp_statuses | Array | ["autoRenewPeriod"] while paid grace is active; [] otherwise, including during funding retries

`cycle.expire_time` records the automatic renewal's expiry, including after reversal. Read domain info or `reversal.expire_time` for the domain's current expiry.

## Get automatic-renewal details

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/domains/example.ee/auto_renew' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz'
```

> The above command returns JSON structured like this during paid grace:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": "example.ee",
        "auto_renew": {
            "enabled": true,
            "cycle": {
                "id": "3f7c2a5e-0000-0000-0000-000000000000",
                "state": "autoRenewPeriod",
                "original_expire_time": "2027-03-01T00:00:00+02:00",
                "expire_time": "2028-03-01T00:00:00+02:00",
                "cancellation_deadline": "2027-03-16T00:00:00+02:00",
                "attempts": 1
            }
        },
        "rgp_statuses": ["autoRenewPeriod"]
    }
}
```

Returns the sponsoring registrar's preference, latest cycle and RGP statuses. This endpoint is sponsor-scoped.

### HTTP Request

`GET /repp/v1/domains/:domain_id/auto_renew`

### URL Parameters

Parameter | Required | Description
--------- | -------- | -----------
domain_id | Yes | Domain identifier (domain name in the example)

## Change automatic renewal / reverse a cycle

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/domains/example.ee/auto_renew' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "auto_renew": {
        "enabled": false,
        "cycle_id": "3f7c2a5e-0000-0000-0000-000000000000"
    }
}'
```

> The above reversal command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": "example.ee",
        "auto_renew": {
            "enabled": false,
            "cycle": {
                "id": "3f7c2a5e-0000-0000-0000-000000000000",
                "state": "reversed",
                "original_expire_time": "2027-03-01T00:00:00+02:00",
                "expire_time": "2028-03-01T00:00:00+02:00",
                "cancellation_deadline": "2027-03-16T00:00:00+02:00",
                "attempts": 1
            }
        },
        "reversal": {
            "cycle_id": "3f7c2a5e-0000-0000-0000-000000000000",
            "expire_time": "2027-03-01T00:00:00+02:00",
            "refund": {
                "amount": "12.00",
                "currency": "EUR"
            },
            "repeated": false
        }
    }
}
```

Only the sponsoring registrar may change the preference or reverse a cycle (`2201`). Wrap parameters in `auto_renew`. At least one of `enabled` and `cycle_id` must be supplied. To enable renewals, send `{"auto_renew":{"enabled":true}}`; this also triggers renewal if the domain is already expired, including while out of zone or in redemption. To opt out without a refund, send `{"auto_renew":{"enabled":false}}`. Omitting `enabled` leaves the preference unchanged unless a reversal is requested; reversal sets it to false.

A preference-only response has the same `domain` and `auto_renew` fields, with `reversal: null`. A repeated reversal returns `repeated: true` and the stored refund without issuing another credit. Unknown or wrong-domain cycles fail with `2303`. A cycle outside paid grace, or a new reversal at or after the deadline, fails with `2304`. Enabling when unavailable, or combining `enabled: true` with `cycle_id`, fails with `2306`. Failure leaves all state unchanged. These are response-body EPP result codes; see [Errors](#errors).

### HTTP Request

`PUT /repp/v1/domains/:domain_id/auto_renew`

### URL Parameters

Parameter | Required | Description
--------- | -------- | -----------
domain_id | Yes | Domain identifier (domain name in the example)

### Payload Parameters

Parameter | Required | Type | Description
--------- | -------- | ---- | -----------
auto_renew[enabled] | No | Boolean | true, false, "true", or "false"; any other value returns 2005
auto_renew[cycle_id] | No | String | Explicit cycle UUID to reverse; supply alone or with enabled=false

## Renew a specific domain

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains/kasskoer.ee/renew' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "renew": {
        "period": 1,
        "period_unit": "y",
        "exp_date": "2021-04-20"
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "kasskoer.ee"
        }
    }
}
```

Renews current domain for desired period.

### HTTP Request

`POST /repp/v1/domains/:domain_name/renew`

### URL Parameters
Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
domain_name | Yes | String | Domain name


### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
period_unit | Yes | String | Period unit. Can be year (y) or month (m)
period | Yes | Integer | For how many period units to register domain
exp_date | Yes | String | Current expiry date for domain (yyyy-mm-dd)

<aside class="notice">
Note — Wrap your payload attributes into "renew" object, as shown in example.
</aside>

## Delete a specific domain

```shell
curl --location --request DELETE 'https://registry.test/repp/v1/domains/kanakotlet.ee' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \

--header 'Content-Type: application/json' \
--data-raw '{
    "delete": {
        "verified": false
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "kanakotlet.ee"
        }
    }
}
```

Deletes a specific domain

### HTTP Request

`DELETE /repp/v1/domains/:domain_name`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

### Required Headers

Parameter | Required | Description
--------- | ------- | -----------
Auth-Code | No | domain's EPP transfer code
### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
delete | Yes | Hash | Hash holding verified flag
delete[verified] | Yes | Boolean | Whether to ask registrant confirmation or not

# Nameservers

## Get domain's nameservers

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/domains/domeener.ee/nameservers' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "nameservers": [
            {
                "hostname": "ns1.domeener.ee",
                "ipv4": ["192.168.1.1"],
                "ipv6": ["fe80::aede:48ff:fe00:1122"]
            },
            {
                "hostname": "ns2.domeener.ee",
                "ipv4": ["192.168.1.2"],
                "ipv6": ["fe80::aede:48ff:fe00:1123"]
            },
        ]
    }
}
```

Gets a specific domain's nameservers

### HTTP Request

`GET /repp/v1/domains/:domain_name/nameservers`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

## Add new nameserver

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains/kotlet.ee/nameservers' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "nameservers": [
        {
            "hostname": "ns1.domainer.ee",
            "ipv4": ["192.168.1.1", "192.168.1.2"],
            "ipv6": ["2620:119:35::35", "2620:119:35::36"]
        },
        {
            "hostname": "ns2.domainer.ee",
            "ipv4": ["192.168.1.3", "192.168.1.4"],
            "ipv6": ["2620:119:35::37", "2620:119:35::38"]
        }
    ]
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "kotlet.ee"
        }
    }
}
```

Add new nameserver(s) to specific domain

### HTTP Request

`POST /repp/v1/domains/:domain_name/nameservers`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
nameservers | Yes | Array | Array of new nameserver objects
nameservers[hostname] | Yes | Hostname of nameserver
nameservers[ipv4] | No | Array | Array of IPv4 addresses
nameservers[ipv6] | No | Array | Array of IPv6 addresses

## Delete existing nameserver

```shell
curl --location --request DELETE 'https://testrar.internet.ee/repp/v1/domains/kotlet.ee/nameservers/ns3.domainer.ee' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "666.ee"
        }
    }
}
```

Delete existing nameserver from domain.

### HTTP Request

`DELETE /repp/v1/domains/:domain_name/nameservers/:nameserver_hostname`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name
nameserver_hostname | Yes | nameserver hostname to delete

# DNSSEC

## Get domain's DNSSEC keys

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/domains/666.ee/dnssec' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "dns_keys": [
            {
                "flags": 257,
                "protocol": 3,
                "alg": 13,
                "public_key": "pubkeyhere"
            }
        ]
    }
}
```

Gets a specific domain's DNSSEC keys

### HTTP Request

`GET /repp/v1/domains/:domain_name/dnssec`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

## Add new DNSSEC key

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains/666.ee/dnssec' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "dns_keys": [
        {
            "flags": "257",
            "protocol": "3",
            "alg": "8",
            "public_key": "AwEAAddt2AkLfYGKgiEZB5SmIF8EvrjxNMH6HtxWEA4RJ9Ao6LCWheg8"
        },
        {
            "flags": "257",
            "protocol": "3",
            "alg": "13",
            "public_key": "mdsswUyr3DPW132mOi8V9xESWE8jTo0dxCjjnopKl+GqJxpVXckHAeF+KkxLbxILfDLUT0rAK9iUzy1L53eKGQ=="
        }
    ]
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "666.ee"
        }
    }
}
```

Add new DNSSEC key(s) to specific domain

### HTTP Request

`POST /repp/v1/domains/:domain_name/dnssec`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
dns_keys | Yes | Array | Array of DNSSEC key objects
dns_keys[flags] | Yes | String | 256 (KSK) or 257 (ZSK)
dns_keys[protocol] | Yes | String | Key protocol (3)
dns_keys[alg] | Yes | String | DNSSEC key algorithm (3,5,6,7,8,10,13,14)
dns_keys[public_key] | Yes | String | DNSSEC public key

## Delete DNSSEC key

```shell
curl --location --request DELETE 'https://testrar.internet.ee/repp/v1/domains/666.ee/dnssec' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "dns_keys": [
        {
            "flags": "257",
            "protocol": "3",
            "alg": "8",
            "public_key": "AwEAAddt2AkLfYGKgiEZB5SmIF8EvrjxNMH6HtxWEA4RJ9Ao6LCWheg8"
        }
    ]
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "666.ee"
        }
    }
}
```

Deletes one or many DNSSEC key(s) for specific domain.

### HTTP Request

`DELETE /repp/v1/domains/:domain_name/dnssec`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
dns_keys | Yes | Array | Array of DNSSEC key objects
dns_keys[flags] | Yes | String | 256 (KSK) or 257 (ZSK) of existing DNSSEC key
dns_keys[protocol] | Yes | String | Key protocol of existing DNSSEC key
dns_keys[alg] | Yes | String | DNSSEC key algorithm of existing DNSSEC key
dns_keys[public_key] | Yes | String | Public key of existing DNSSEC key

# Statuses

## Add clientHold to domain

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/domains/biz.ee/statuses/clientHold' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": "biz.ee",
        "status": "clientHold"
    }
}
```

Adds a clientHold status to a specific domain

### HTTP Request

`PUT /repp/v1/domains/:domain_name/statuses/clientHold`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

## Remove clientHold from domain

```shell
curl --location --request DELETE 'https://testrar.internet.ee/repp/v1/domains/biz.ee/statuses/clientHold' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {}
}
```

Removes clientHold status from specific domain

### HTTP Request

`DELETE /repp/v1/domains/:domain_name/statuses/clientHold`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name
# Transfers

## Get transfer info

```shell
curl --location --request GET 'https://testrar.internet.ee/repp/v1/domains/kotlet.ee/transfer_info' \
--header 'Auth-Code: 6699fb661ee3a2006a3d1e5833d7c5abv' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--data-raw ''
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": "kotlet.ee",
        "registrant": {
            "code": "ATSAA:E36957D7",
            "phone": "+372.xxx",
            "email": "xxx",
            "ident": "xxx",
            "ident_type": "priv",
            "name": "xxx",
            "ident_country_code": "EE",
            "city": null,
            "street": null,
            "zip": null,
            "country_code": null,
            "statuses": [
                "ok",
                "linked"
            ]
        },
        "admin_contacts": [
            {
              "code": "ATSAA:E36957D7",
              "phone": "+372.xxx",
              "email": "xxx",
              "ident": "xxx",
              "ident_type": "priv",
              "name": "xxx",
              "ident_country_code": "EE",
              "city": null,
              "street": null,
              "zip": null,
              "country_code": null,
              "statuses": [
                "ok",
                "linked"
              ]
            }
        ],
        "tech_contacts": [
            {
                "code": "ATSAA:E36957D7",
                "phone": "+372.xxx",
                "email": "xxx",
                "ident": "xxx",
                "ident_type": "priv",
                "name": "xxx",
                "ident_country_code": "EE",
                "city": null,
                "street": null,
                "zip": null,
                "country_code": null,
                "statuses": [
                    "ok",
                    "linked"
                ]
            }
        ]
    }
}
```

Gets a specific domain's transfer info

### HTTP Request

`GET /repp/v1/domains/:domain_name/transfer_info`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

### Required Headers

Submit domain's EPP authorization code if domain doesn't belong to you (yet)

Parameter | Required | Description
--------- | ------- | -----------
Auth-Code | No | Queried domain's EPP authorization code

## Transfer domain

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains/666.ee/transfer' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "transfer": {
        "transfer_code": "d79d865fdf589a1cd52ae95fde4e5d30"
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "domain": {
            "name": "666.ee",
            "type": "domain_transfer"
        }
    }
}
```

Transfers domain from another registrar to your account.

### HTTP Request

`POST /repp/v1/domains/:domain_name/transfer`

### URL Parameters

Parameter | Required | Description
--------- | ------- | -----------
domain_name | Yes | Domain name

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
transfer | Yes | Object | Object holding transfer elements
transfer[transfer_code] | Yes | String | EPP authorization code

# Bulk actions

## Bulk domain transfer

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains/transfer' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "domain_transfers": [
            {
                "domain_name": "domain1.ee",
                "transfer_code": "a45e543ddabee202f95eebae5a7c917c"
            },
            {
                "domain_name": "domain2.ee",
                "transfer_code": "cce67ca63adc8c2938f199f10108881e"
            }
        ]
    }
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "success": [
            {
                "type": "domain_transfer",
                "domain_name": "domain1.ee"
            },
            {
                "type": "domain_transfer",
                "domain_name": "domain2.ee"
            }
        ],
        "failed": []
    }
}
```

Transfers multiple domains from another registrar to your account.

### HTTP Request

`POST /repp/v1/domains/transfer`

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
data | Yes | Object | Object holding domain_transfers array
data[domain_transfers] | Yes | Array | Array of domain transfer objects
data[domain_transfers][domain_name] | Yes | String | Domain name
data[domain_transfers][transfer_code] | Yes | String | EPP authorization code

## Bulk tech contact replace

```shell
curl --location --request PATCH 'https://testrar.internet.ee/repp/v1/domains/contacts' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "current_contact_id": "ATSAA:B839862D",
    "new_contact_id": "ATSAA:KARL"
}'
```

> The above command returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "success": [
            {
                "type": "domain_transfer",
                "domain_name": "kotlet.ee"
            },
            {
                "type": "domain_transfer",
                "domain_name": "kanakotlet.ee"
            }
        ],
        "failed": []
    }
}
```

Cycles through every domain and replaces specific tech contact with another one.

### HTTP Request

`PATCH /repp/v1/domains/contacts`

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
current_contact_id | Yes | String | Tech contact ID you wish to replace
new_contact_id | Yes | String | New contact ID to assign as tech contact

## Bulk nameserver change

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/registrar/nameservers' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "type": "nameserver",
        "id": "ns1.domainer.ee",
        "attributes": {
            "hostname": "ns1.ams1.domainer.ee",
            "ipv4": ["192.168.1.1"],
            "ipv6": ["2620:119:35::36"]
        }
    }
}'
```
> If you want to replace nameserver only for specific domains, submit scoped "domains" array like that:

```shell
curl --location --request PUT 'https://testrar.internet.ee/repp/v1/registrar/nameservers' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "data": {
        "type": "nameserver",
        "id": "ns1.ams1.domainer.ee",
        "domains": ["only.ee", "these.ee", "domains.ee"],
        "attributes": {
            "hostname": "ns2.ams1.domainer.ee",
            "ipv4": ["192.168.1.1"],
            "ipv6": ["2620:119:35::36"]
        }
    }
}'
```

> Both commands above return JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "type": "nameserver",
        "id": "ns1.ams1.domainer.ee",
        "attributes": {
            "hostname": "ns1.ams1.domainer.ee",
            "ipv4": [
                "192.168.1.1"
            ],
            "ipv6": [
                "2620:119:35::36"
            ]
        },
        "affected_domains": [
            "only.ee",
            "these.ee",
            "domains.ee"
        ],
        "skipped_domains": []
    }
}
```

Cycles through every domain and replaces their specific nameserver with new data.
The new new hostname of the nameserver must be different. It's not possible to change existing nameserver's IPv4 / IPv6 addresses with this request.

### HTTP Request

`PUT /repp/v1/registrar/nameservers`

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
data | Yes | Object | Object holding nameserver changes
data[type] | Yes | String | Always set it as "nameserver"
data[id] | Yes | String | Hostname of replacable nameserver
data[domains] | No | Array | Array of domain names qualified for nameserver replacement.
data[attributes] Yes | Object | Object holding new nameserver values
data[attributes][hostname] | Yes | String | New hostname of nameserver
data[attributes][ipv4] | No | Array | Array of nameserver's fixed IPv4 addresses
data[attributes][ipv6] | No | Array | Array of nameserver's fixed IPv6 addresses

<aside class="warning">
Note - It's safer to use "domains" scope, although it's not required. If you don't submit "domains" array, we're going to take every domain in registrar's account to cycle, which can, from experience, deal pretty large damage.
</aside>

## Bulk domain renew

```shell
curl --location --request POST 'https://testrar.internet.ee/repp/v1/domains/renew/bulk' \
--header 'Authorization: Basic dGVzdDp0ZXN0MTIz' \
--header 'Content-Type: application/json' \
--data-raw '{
    "domains": ["kotlet.ee", "kasskoer.ee"],
    "renew_period": "1y"
}'
```

> Command above returns JSON structured like this:

```json
{
    "code": 1000,
    "message": "Command completed successfully",
    "data": {
        "updated_domains": [
            "kotlet.ee",
            "kasskoer.ee"
        ]
    }
}
```

Renews multiple domanins at once, for a fixed period.

### HTTP Request

`POST /repp/v1/domains/renew/bulk`

### Payload Parameters

Parameter | Required | Type | Description
--------- | ------- | ----- | -----------
domains | Yes | Array | Array of domain names for renewal
renew_period | Yes | String | Period for domain renew (3m, 6m, 9m, 1y ... 10y)
