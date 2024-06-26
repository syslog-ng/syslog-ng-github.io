## keep-alive()

This option configures the forwarding of gRPC keepalive pings in {{ site.product.short_name }}.

### max-pings-without-data()

|   Type:|      integer|
|Default:|             |

*Description:* This option definies the maximum number of gRPC pings that are allowed to be sent when there is no data/header frame. Any pings succeeding this limit are not sent. Setting this option to `0` disables this restriction and keep sending pings.

### time()

|   Type:|      number[milliseconds]|
|Default:|                          |

*Description:* {{ site.product.short_name }} sends a gRPC keepalive ping after the amount of time defined in the `time()` option elapsed.

### timeout()

|   Type:|      number[milliseconds]|
|Default:|                        10|

*Description:* The time {{ site.product.short_name }} waits for an acknowledgement.