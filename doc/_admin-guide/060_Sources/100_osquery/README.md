---
title: 'osquery: Collect and parse osquery result logs'
short_title: osquery
id: adm-src-osquery
description: >-
    The osquery application allows you to ask
    questions about your machine using an SQL-like language. For example,
    you can query running processes, logged in users, installed packages and
    syslog messages as well. You can make queries on demand, and also
    schedule them to run regularly.
---

The osquery() source of {{ site.product.short_name }} allows you read the results of
periodical osquery queries (from the
/var/log/osquery/osqueryd.results.log file) and automatically parse the
messages (if you want to use {{ site.product.short_name }} to send log messages to
osquery, read this blogpost).  

For example, you can:

- Create filters from the fields of the messages.

- Limit which fields to store, or create additional fields (combine
    multiple fields into one field, and so on).

- Send the messages to a central location, for example, to
    Elasticsearch, directly from {{ site.product.short_name }}.

The {{ site.product.short_name }} application automatically adds the .osquery. prefix to
the name of the fields the extracted from the message.

The osquery() source is available in {{ site.product.short_name }} version 3.10 and
later.

## Prerequisites

- To use the osquery() driver, the scl.conf file must be included in
    your {{ site.product.short_name }} configuration:

    ```config
    @include "scl.conf"
    ```

- {{ site.product.short_name }} must be compiled with JSON-support enabled.

The osquery() driver is actually a reusable configuration snippet
configured to read the osquery log file using the file() driver, and
process its JSON contents. For details on using or writing such
configuration snippets, see Reusing configuration blocks.
You can find the source of the osquery configuration snippet on GitHub.

## Example: Using the osquery() driver with the default settings

The following {{ site.product.short_name }} configuration sample uses the default
settings of the driver, reading osquery result logs from the
/var/log/osquery/osqueryd.results.log file, and writes the log messages
generated from the traps into a file.

```config
@version: 3.10
@include "scl.conf"

source s_osquery {
    osquery();
};
log {
    source(s_osquery);
    destination {
        file("/var/log/example.log");
    };
};
```

Filter for messages related to loading Linux kernel modules:

```config
@version: 3.10
@include "scl.conf"

source s_osquery {
    osquery();
};
log {
    source(s_osquery);
    filter f_modules {
        "${.osquery.name}" eq "pack_incident-response_kernel_modules"
    };
    destination {
        file("/var/log/example.log");
    };
};
```

### Example: Using the osquery() driver with custom configuration

The following {{ site.product.short_name }} configuration sample reads osquery result
logs from the /tmp/osquery_input.log file, and writes the log messages
generated from the traps into a file. Using the format-json template,
the outgoing message will be a well-formed JSON message.

#### Input message

>{"name":"pack_osquery-monitoring_osquery_info","hostIdentifier":"testhost",  
>"calendarTime":"Fri Jul 21 10:04:41 2017 >UTC","unixTime":"1500631481",  
>"decorations":{"host_uuid":"4C4C4544-004D-3610-8043-C2C04F4D3332",  
>"username":"myuser"},>"columns":{"build_distro":"xenial",  
>"build_platform":"ubuntu",>"config_hash":"43cd1c6a7d0c283e21e026a53e619b2e582e94ee",  
>"config_valid":"1","counter":"4","extensions":"active",  
>"instance_id":"d0c3eb0d-f8e0-4bea-868b-18a2c61b438d","pid":"19764",  
>"resident_size":"26416000",>"start_time":"1500629552","system_time":"223",  
>"user_time":"476","uuid":"4C4C4544-004D-3610-8043-C2C04F4D3332",
>"version":"2.5.0","watcher":"19762"},"action":"added"}

#### {{ site.product.short_name }} configuration

```config
@version: 3.10
@include "scl.conf"

source s_osquery {
    osquery(
        file(/tmp/osquery_input.log)
        prefix(.osquery.)
    );
};
destination d_file {
    file(
        "/tmp/output.txt"
        template("$(format_json --key .osquery.*)\n")
    );
};
log {
    source(s_osquery);
    destination(d_file);
    flags(flow-control);
};
```

#### Outgoing message

>Outgoing message; message='{"_osquery":{"unixTime":"1500631481",  
>"name":"pack_osquery-monitoring_osquery_info","hostIdentifier":"testhost",  
>"decorations":{"username":"myuser","host_uuid":"4C4C4544-004D-3610-8043-C2C04F4D3332"},  
>"columns":{"watcher":"19762","version":"2.5.0","uuid":"4C4C4544-004D-3610-8043-C2C04F4D3332",  
>"user_time":"476","system_time":"223","start_time":"1500629552",  
>"resident_size":"26416000","pid":"19764","instance_id":"d0c3eb0d-f8e0-4bea-868b-18a2c61b438d",  
>"extensions":"active","counter":"4","config_valid":"1",  
>"config_hash":"43cd1c6a7d0c283e21e026a53e619b2e582e94ee","build_platform":"ubuntu",  
>"build_distro":"xenial"},"calendarTime":"Fri Jul 21 10:04:41 2017 UTC","action":"added"}}\x0a'

To configure a destination to send the log messages to Elasticsearch,
see elasticsearch-http: Sending messages to Elasticsearch HTTP Bulk API.  
For other destinations, see destination: Forward, send, and store log messages.
