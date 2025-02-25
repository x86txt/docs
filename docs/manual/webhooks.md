# Webhooks

## Webhooks
Webhooks are an industry standard way for internet applications to communicate with each other.  Rundeck Webhooks are an entry point to automation with Rundeck.  A Webhook can be configured to accept incoming payloads and trigger Rundeck Job(s).  Webhook events are handled by [Webhook Event](/developer/16-webhook-plugins.md) Rundeck plugins.


## Webhook Administration
> Released in version 4.0

Click on `Webhooks` to go to the webhook administration page to add, configure, and remove webhooks for a project.

![Webhook Administration](/assets/img/webhook-admin.png)

The [Webhook API](/api/rundeck-api.md#webhooks-beta) or using the rd cli tool can also manage webhooks.

### Webhook HTTP Authorization String

Webhooks have an an optional HTTP Authorization String feature, when used, can increase the the security of the webhook.  To enable the feature check the _Use Authorization Heeader_ box when creating or editing a webhook.  When the Webhook is saved a box will be shown below the check box with the Authorization string assigned to the webhook.  **Note: This will only be shown once!** After navigating away from the webhook it will no longer be visible.

Include the string as an `Authorization` header when making a call to the webhook.

```
curl --location --request POST 'http://your.rundeck.domain:4440/api/40/webhook/wvNE6e1R06eqCQSl5jwXgXy3IUgt5vxS#Advanced_Webhook' \
--header 'Authorization: the-authorization-string' \
--header 'Content-Type: application/json' \
--data-raw '{
    "messages": {
        "field1": "value1",
        "sub1": {
            "subfield": "subval"
        }
    }
}'
```

It is possible to regenerate the Authorization String by clicking the **Regenerate** button on the Webhook.  The new value will be displayed after saving.

### Webhook Handlers

Webhook Event Handlers are custom handlers that can be developed as [Rundeck Plugins](/developer/16-webhook-plugins.md) to interact with external systems.
They can respond to authentication calls, pre-process inputs, and provide configuration defaults for specific systems.

Rundeck includes the following Webhook Handlers:

* [Advanced Run Job (Enterprise)](./webhooks/advanced-run-job.md)
* [PagerDuty Run Job (Enterprise)](./webhooks/pagerduty-run-job.md)
* [DataDog Run Job  (Enterprise)](./webhooks/datadog-run-job.md)
* [AWS SNS (Enterprise)](./webhooks/aws-sns-webhook.md)
* [GitHub (Enterprise)](./webhooks/github-webhook.md)
* [Run Job](./webhooks/run-job.md)
* [Log Events](./webhooks/log-events.md)


For a full tutorial on creating a Webhook follow our [How To Use Webhooks](/learning/howto/using-webhooks.md) guide.

### Webhook Tips

:::warning
Please note that after the webhook has been created, it is not possible to change the `Webhook User`. This field will become read only.
:::

:::tip
When configuring a user on a Webhook ensure that the user has logged into Rundeck at least once.  If the user has never logged in to Rundeck before a validation error will occur
saying that the user cannot be found. Once that users logs in one time it is possible to specify the user in the webhook form.
:::

### Webhook Debugging

>_“Why didn’t my webhook do X?!”_

Process Automation includes a Webhook Debugger integrated with the Webhook builder. The Debugger makes troubleshooting incoming webhooks and why they did (or did not) trigger certain rules in the Advanced Rule Processing Handlers.

![Debug Panel](/assets/img/wh-debug-panel.png)

Alternatively, use the configuration below to send events and log messages generated by the webhook activity to the `$RD_LOGS_DIR/rundeck.webhooks.log` file.

Default log4j configuration

```properties
log4j.logger.org.rundeck.webhook.events=INFO,webhook
log4j.additivity.org.rundeck.webhook.events=false

log4j.appender.webhook=org.apache.log4j.DailyRollingFileAppender
log4j.appender.webhook.file=${rundeck.log.dir}${file.separator}rundeck.webhook.log
log4j.appender.webhook.append=true
log4j.appender.webhook.layout=org.apache.log4j.PatternLayout
log4j.appender.webhook.layout.ConversionPattern=[%d{ISO8601}] %-5p %c{2} - %m%n
```  

## Disable Webhooks
:::tip
Webhooks are on by default as of `3.2.0`. They can be disabled by following the following instructions.
:::

#### WAR/Deb/RPM
To disable the use of webhooks for your Rundeck server add the following configuration property to
your `rundeck-config.properties` or equivalent.

```properties
rundeck.feature.webhooks.enabled=false
```

#### Docker
Set the following environment variable:
```properties
RUNDECK_FEATURE_WEBHOOKS_ENABLED=false
```
