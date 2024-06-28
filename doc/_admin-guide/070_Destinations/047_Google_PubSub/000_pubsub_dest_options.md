---
title: 'pubsub() destination options'
id: adm-dest-pubsub-opt
description: >-
  The following options are specific to the google-pubsub() destination in {{ site.product.short_name }}.
---

**NOTE:** Since this destination is based on the http() destination, the HTTP destination options can also be used. The `google-pubsub()` destination automatically configures some of these `http()` destination options as required by the Google Pub/Sub AP.
{: .notice--info}

## attributes()

|  Type:|     string|
|Default:| `"--scope rfc5424,all-nv-pairs --exclude MESSAGE"`|

*Description:* A JSON object representing key-value pairs for the Pub/Sub Event, formatted as {{ site.product.short_name }} value-pairs. By default, the `google-pubsub()` destination sends the RFC-5424 fields as attributes. If different fields are required, override the default template. By default, the message part is sent in the `data()` option

## auth()

*Description:* This is an option for cloud-related authentication. Currently only the GCP Service Account authentication is supported.

### Example: storing the key to the service account

```config
auth(
    service-account(
      key("/path/to/service-account-key.json")
    )
  )
```

### service-account()

See the example for `auth()` above.

## data()

|  Type:|     string/template|
|Default:| `"${MESSAGE}"`|

*Description:* The template used as the data element of the Google Pub/Sub message.

## project()

|  Type:|     string/template|
|Default:| |

*Description:* The ID of the Google Cloud project where {{ site.product.short_name }} sends the data. The Pub/Sub API must be enabled for the project.

## topic()

|  Type:|     string|
|Default:| |

*Description:* The name of the Google Pub/Sub target topic to where {{ site.product.short_name }} sends the data.
