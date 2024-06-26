---
title: Using embedded log statements
id: adm-log-emb-log-using
description: >-
    Embedded log statements re-use the results
    of processing messages (for example, the results of
    filtering or rewriting) to create complex log paths. Embedded log
    statements use the same syntax as regular log statements, but they
    cannot contain additional sources. 
---

To define embedded log statements, use the following syntax:

```config
log {
    source(s1); source(s2); ...

    optional_element(filter1|parser1|rewrite1);
    optional_element(filter2|parser2|rewrite2);
    ...
    destination(d1); destination(d2); ...

    #embedded log statement
    log {
        optional_element(filter1|parser1|rewrite1);
        optional_element(filter2|parser2|rewrite2);
        ...
        destination(d1); destination(d2); ...

        #another embedded log statement
        log {
            optional_element(filter1|parser1|rewrite1);
            optional_element(filter2|parser2|rewrite2);
            ...
            destination(d1); destination(d2); ...
        };
    };
    #set flags after the embedded log statements
    flags(flag1[, flag2...]);
};
```

## Example: Using embedded log paths

The following log path sends every message to the configured
destinations: both the d\_file1 and the d\_file2 destinations receive
every message of the source.

```config
log {
    source(s_localhost);
    destination(d_file1);
    destination(d_file2);
};
```

The next example is equivalent to the one above, but uses an embedded
log statement.

```config
log {
    source(s_localhost);
    destination(d_file1);
    log {
        destination(d_file2);
    };
};
```

The following example uses two filters:

- messages coming from the host 192.168.1.1 are sent to the d\_file1
    destination, and

- messages coming from the host 192.168.1.1 and containing the string
    example are sent to the d\_file2 destination.

    ```config
    log {
        source(s_localhost);
        filter {
            host(192.168.1.1);
        };
        destination(d_file1);
        log {
            message("example");
            destination(d_file2);
        };
    };
    ```

The following example collects logs from multiple source groups and uses
the source() filter in the embedded log statement to select messages of
the s\_network source group.

```config
log {
    source(s_localhost);
    source(s_network);
    destination(d_file1);
    log {
        filter {
            source(s_network);
        };
    destination(d_file2);
    };
};
```
