---
title: "The new webhooks Connector module"
date: 2022-03-02T12:00:00+02:00
categories:
    - blog
tags:
    - connector
    - update
---

## Introduction

Hello everyone!

Today we want to announce a new version of the webhooks Connector module.

The old version of the module had many limitations that we could only address with a complete rewrite. To be backwards compatible the old version of the module is still available under the name `webhooks`.
The new module can be configured under the name `webhooksV2`.
If you don't want to use the new module it doesn't require further actions.

We still recommend to switch to the new module as soon as possible as the old module is now deprecated and will not be maintained anymore.

## The new module

The new module gives you the possibility to define so called `triggers`, which represent the names of the events we already send Connector-internally (e.g. `transport.messageReceived`). The module then listens to the configured events and simply sends the event data to the configured URL. For a detailed documentation of how to configure the new webhooks module, see [the Connector configuration docs]({% link _docs_integrate/11-connector-configuration.md %}#webhooksv2).

While in the old module you could configure an interval in which the new information is published, the new module calls the webhook immediately after the configured event was triggered.
_Do you miss the possibility to configure a publish interval? Raise a feature request in our [feedback repository](https://github.com/nmshd/nmshd.github.io/issues/new/choose), and we will see what we can do about it._

Since you can use arbitrary events as triggers now, the new module gives you much more points at which you can integrate. We provide a [list of these events]({% link _docs_integrate/32-connector-events.md %}). Note that the list will grow in the next weeks, so check the docs from time to time. If you wish for a specific event, you can also [raise a feature request](https://github.com/nmshd/nmshd.github.io/issues/new/choose).

## Migration

In order to be notified about the same stuff as with the old module, you should configure the new module in the following way:

```json
{
    "webhooksV2": {
        "targets": {
            "target1":  {
                "url": "https://example.com/webhook",
            }
        }
        "webhooks": [
            {
                "triggers": ["transport.messageReceived", "transport.relationshipChanged"],
                "target": "target1"
            }
        ]
    }
}
```

The data sent to the given URL has changed though:

```ts
{
    "trigger": string,
    "data": {
        // properties depend on the event
    }
}
```

In the POST body you will receive the `trigger` of the webhook (which is the name of the event), as well as the `data` of the event. Both are documented in the [Connector Events]({% link _docs_integrate/32-connector-events.md %}) section.
