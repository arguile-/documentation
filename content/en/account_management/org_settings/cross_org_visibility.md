---
title: Cross Organization Visibility
kind: documentation
private: true
is_beta: true
---

{{< callout url="#" header="false" btn_hidden="true">}}
  Cross-org visibility is in <b>private beta</b> for customers with Enterprise plans. If you're interested in the feature, reach out to your customer service manager.
{{< /callout >}} 


## Overview

Some companies rely on multiple Datadog [organizations][1] to gather their observability data.

Companies may use multiple organizations to deliberately isolate different parts of themselves. For example, managed service providers may use a separate Datadog organization for each customer. In another example, company A and company B both use Datadog. After company A acquires company B, they continue to use separate Datadog organizations.

When company data exists in multiple organizations, some insights require aggregating or comparing data across different Datadog organizations. With cross-org visibility, a single Datadog organization can gather data from other Datadog organizations.

This document explains: 
- What cross-org visibility enables [#section] 
- How to expose data across organizations [#section]
- How to create a Dashboard widget with data from other orgs [#section]

## Capabilities

### Organization connection

A _source_ Datadog organization exposes data to a _destination_ Datadog organization through an _organization connection_. The source and destination Datadog organizations must be in the same [account][1]. A source organization can have multiple destinations, and a destination organization can have multiple sources.

After you set up an organization connection, the data the source organization exposes still lives in the source organization and does not move to the destination. Instead, the destination organization queries the data from the source. Connections do not duplicate the data, and do not incur extra charges. The destination organization can query source data from any time range supported by the source data, including prior to the date of the creation of the connection. If you remove the connection, the destination can no longer access any data from the source.

### Scope

Cross-org visibility supports Metrics telemetry in private [Dashboard] widgets.

All types of metrics are supported, including [custom metrics][2], [trace metrics][3], [metrics generated from logs][4], and others.

## Configure connections

Configure connections through the public API `/api/v2/org_connections` endpoint. The application key you use to authenticate to the endpoint must have the [`org_management`][5] permission.

### List connections

List all the connections this organization participates in, either as a source organization or as a destination organization.

<span style="padding:3px" class="font-semibold text-api-get bg-bg-api-get">GET</span>
https://{{datadog_site}}/api/v2/org_connections?api_key={{datadog_api_key}}&application_key={{datadog_application_key}}

### Create a connection

Creates a connection from this organization to the destination organization. You must perform this operation in the to-be-source organization.

<span style="padding:3px" class="font-semibold text-api-post bg-bg-api-post">POST</span> https://{{datadog_site}}/api/v2/org_connections?api_key={{datadog_api_key}}&application_key={{datadog_application_key}}

**Note:** The payload of this call requires the destination org UUID. Get the destination org's UUID from the "List your managed organizations" [endpoint][6].

#### Header
Content-Type: application/json

#### Payload
{{< code-block lang="json" collapsible="true" >}}
{
    "data": {
        "type": "org_connection",
        "relationships": {
            "sink_org": {
                "data": {
                    "type": "orgs",
                    "id": "{{the destination organization UUID}}"
                }
            }
        }
    }
}
{{< /code-block >}}

[1]: /account_management/multi_organization/
[2]: /metrics/custom_metrics/#overview
[3]: /tracing/metrics/metrics_namespace/
[4]: /logs/log_configuration/logs_to_metrics/
[5]: /account_management/rbac/permissions/#access-management
[6]: /api/latest/organizations/#list-your-managed-organizations
