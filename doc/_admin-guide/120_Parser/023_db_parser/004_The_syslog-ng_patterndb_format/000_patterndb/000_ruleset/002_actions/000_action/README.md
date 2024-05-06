---
title: 'Element: action'
short_title: action
id: adm-parser-db-elem-action
---

## Location

/ patterndb / ruleset / actions / action

## Description

OPTIONAL --- A container element describing an action that is performed
when a message matching the rule is received.

## Attributes

- *condition*: A syslog-ng filter expression. The action is performed
    only if the message matches the filter. The filter can include
    macros and name-value pairs extracted from the message. When using
    actions together with message-correlation, you can also use the
    **\$(context-length)** macro, which returns the number of messages
    in the current context. For example, this can be used to determine
    if the expected number of messages has arrived to the context:
    condition=\'\"\$(context-length)\" \>= \"5\"\'

- *rate*: Specifies maximum how many messages should be generated in
    the specified time period in the following format:
    \<number-of-messages\>/\<period-in-seconds\>. For example: 1/60
    allows 1 message per minute. Rates apply within the scope of the
    context, that is, if context-scope=\"host\" and rate=\"1/60\", then
    maximum one message is generated per minute for every host that
    sends a log message matching the rule. Excess messages are dropped.
    Note that when applying the rate to the generated messages,
    syslog-ng OSE uses the timestamps of the log messages, similarly to
    calculating the context-timeout. That way rate is applied correctly
    even if the log messages are processed offline.

- *trigger*: Specifies when the action is executed. The trigger
    attribute has the following possible values:

  - *match*: Execute the action immediately when a message matching
        the rule is received.

  - *timeout*: Execute the action when the correlation timer
        ([[context-timeout]] of the
        pattern database rule expires. This is available only if actions
        are used together with correlating messages.

## Children

- *create-context*

- *message*: A container element storing the message to be sent when
    the action is executed. Currently syslog-ng OSE sends these messages
    to the internal() destination.

  - For details on the message context, see
        [[Correlating log messages using pattern databases]]
        and [[Actions and message correlation]].
        For details on triggering messages, see
        [[Triggering actions for identified messages]].

  - *inherit-mode*: This attribute controls which name-value pairs
        and tags are propagated to the newly generated message.

    - context: syslog-ng OSE collects every name-value pair from
        each message stored in the context, and includes them in the
        generated message. If a name-value pair appears in multiple
        messages of the context, the value in the latest message
        will be used. Note that tags are not merged, the generated
        message will inherit the tags assigned to the last message
        of the context.

    - last-message: Only the name-value pairs appearing in the
        last message are copied. If the context contains only a
        single message, then it is the message that triggered the
        action.

    - none: An empty message is created, without inheriting any
        tags or name-value pairs.

    This option is available in syslog-ng OSE 3.8 and later.

  - *inherit-properties*: This attribute is deprecated. Use the
    **inherit-mode** attribute instead.

    If set to **TRUE**, the original message that triggered the
    action is cloned, including its name-value pairs and tags.

    If set to **context**, syslog-ng OSE collects every name-value
    pair from each message stored in the context, and includes them
    in the generated message. If a name-value pair appears in
    multiple messages of the context, the value in the latest
    message will be used. Note that tags are not merged, the
    generated message will inherit the tags assigned to the last
    message of the context.

    For details on the message context, see
    [[Correlating log messages using pattern databases]]
    and [[Actions and message correlation]].
    For details on triggering messages, see
    [[Triggering actions for identified messages]].

- *values*: A container element for values and fields that are used to
    create the message generated by the action.

  - *value*: Sets the value of the message field specified in the
    name attribute of the element. For example, to specify the body
    of the generated message, use the following syntax:

    ```xml
    <value name="MESSAGE">A log message matched rule number $.classifier.rule_id</value>
    ```

    Note that currently it is not possible to add DATE, FACILITY, or
    SEVERITY fields to the message.

    When the action is used together with message correlation, the
    syslog-ng OSE application automatically adds fields to the
    message based on the context-scope parameter. For example, using
    context-scope=\"process\" automatically fills the HOST, PROGRAM,
    and PID fields of the generated message.

  - *name*: Name of the message field set by the value element.

### Example: Generating messages for pattern database matches

When inserted in a pattern database rule, the following example
generates a message when a message matching the rule is received.

```xml
<actions>
    <action>
        <message>
            <values>
                <value name="MESSAGE">A log message from ${HOST} matched rule number $.classifier.rule_id</value>
            </values>
        </message>
    </action>
</actions>
```

To inherit the properties and values of the triggering message, set the
inherit-properties attribute of the \<message\> element to TRUE. That
way the triggering log message is cloned, including name-value pairs and
tags. If you set any values for the message in the \<action\> element,
they will override the values of the original message.

### Example: Generating messages with inherited values

The following action generates a message that is identical to the
original message, but its \$PROGRAM field is set to
overriding-original-program-name

```xml
<actions>
    <action>
        <message inherit-properties='TRUE'>
            <values>
                <value name="PROGRAM">overriding-original-program-name</value>
            </values>
        </message>
    </action>
</actions>
```