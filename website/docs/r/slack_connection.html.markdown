---
layout: "pagerduty"
page_title: "PagerDuty: pagerduty_slack_connection"
sidebar_current: "docs-pagerduty-resource-slack-connection"
description: |-
  Creates and manages a slack connection in PagerDuty.
---

# pagerduty\_slack\_connection

A [slack connection](https://developer.pagerduty.com/api-reference/reference/integration-slack-service/openapiv3.json) allows you to connect a workspace in Slack to a PagerDuty service or team which allows you to acknowledge and resolve PagerDuty incidents from the Slack user interface. 

**NOTES for using this resource:** 
* To first use this resource you will need to [map your PagerDuty account to a valid Slack Workspace](https://support.pagerduty.com/docs/slack-integration-guide#integration-walkthrough). *This can only be done through the PagerDuty UI.*
* This resource requires a PagerDuty [user-level API key](https://support.pagerduty.com/docs/generating-api-keys#section-generating-a-personal-rest-api-key) set as the `PAGERDUTY_USER_TOKEN` environment variable.
## Example Usage

```hcl
resource "pagerduty_team" "foo" {
  name = "Team Foo"
}

data "pagerduty_priority" "p1" {
  name = "P1"
}

resource "pagerduty_slack_connection" "foo" {
  source_id = pagerduty_team.foo.id
  source_type = "team_reference"
  workspace_id = "T02A123LV1A"
  channel_id = "C02CABCDAC9"
  config {
    events = [
      "incident.triggered",
      "incident.acknowledged",
      "incident.escalated",
      "incident.resolved",
      "incident.reassigned",
      "incident.annotated",
      "incident.unacknowledged",
      "incident.delegated",
      "incident.priority_updated",
      "incident.responder.added",
      "incident.responder.replied",
      "incident.status_update_published",
      "incident.reopened"		  
    ]
    priorities = [data.pagerduty_priority.p1.id]

  }
}
```

## Argument Reference

The following arguments are supported:

  * `source_id` - (Required) The ID of the source in PagerDuty. Valid sources are services or teams.
  * `source_type` - (Required) The type of the source. Either `team_reference` or `service_reference`.
  * `workspace_id` - (Required) The ID of the connected Slack workspace. Can also be defined by the `SLACK_CONNECTION_WORKSPACE_ID` environment variable. 
  * `channel_id` - (Required) The ID of a Slack channel in the workspace.
  * `config` - (Required) Configuration options for the Slack connection that provide options to filter events.
  * `notification_type` - (Required) Type of notification. Either `responder` or `stakeholder`.

### Connection Config (`config`) Supports the following:
  * `events` - (Required) A list of strings to filter events by PagerDuty event type. `"incident.triggered"` is required. The follow event types are also possible:
    - `incident.acknowledged`
    - `incident.escalated`
    - `incident.resolved`
    - `incident.reassigned`
    - `incident.annotated`
    - `incident.unacknowledged`
    - `incident.delegated`
    - `incident.priority_updated`
    - `incident.responder.added`
    - `incident.responder.replied`
    - `incident.status_update_published`
    - `incident.reopened`		  
  * `priorities` - (Optional) Allows you to filter events by priority. Needs to be an array of PagerDuty priority IDs. Available through [pagerduty_priority](https://registry.terraform.io/providers/PagerDuty/pagerduty/latest/docs/data-sources/priority) data source.
  * `urgency` - (Optional) Allows you to filter events by urgency. Either `high` or `low`.

## Attributes Reference

The following attributes are exported:

  * `id` - The ID of the slack connection.
  * `source_name`- Name of the source (team or service) in Slack connection.
  * `channel_name`- Name of the Slack channel in Slack connection

## Import

Slack connections can be imported using using the related `workspace` ID and the `slack_connection` ID separated by a dot, e.g.
```
$ terraform import pagerduty_slack_connection.main T02A123LV1A.PUABCDL
```
