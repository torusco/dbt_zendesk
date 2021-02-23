# Zendesk Support

This package models Zendesk Support data from [Fivetran's connector](https://fivetran.com/docs/applications/zendesk). It uses data in the format described by [this ERD](https://fivetran.com/docs/applications/zendesk#schemainformation).

This package enables you to better understand the performance of your Support team. It calculates metrics focused on response times, resolution times, and work times for you to analyze. 

### Optional features (for Zendesk Professional or Enterprise users)
- Package converts metrics to business hours
- Package calculates SLA policy breaches

## Models

This package contains transformation models, designed to work simultaneously with our [Zendesk Support source package](https://github.com/fivetran/dbt_zendesk_source). A dependency on the source package is declared in this package's `packages.yml` file, so it will automatically download when you run `dbt deps`. The primary outputs of this package are described below. Intermediate models are used to create these output models.

| **model**                    | **description**                                                                                                                                                 |
| ---------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [zendesk__ticket_metrics](https://github.com/fivetran/dbt_zendesk/blob/master/models/zendesk__ticket_metrics.sql)       | Each record represents a Zendesk ticket, enhriched with metrics about reply times, resolution times, and work times.  Calendar and business hours are supported.  |
| [zendesk__ticket_enriched](https://github.com/fivetran/dbt_zendesk/blob/master/models/zendesk__ticket_enriched.sql)      | Each record represents a Zendesk ticket, enriched with data about its tags, assignees, requester, submitter, organization, and group.                           |
| [zendesk__ticket_summary](https://github.com/fivetran/dbt_zendesk/blob/master/models/zendesk__ticket_summary.sql)           | A single record table containing Zendesk ticket and user summary metrics.                                                              |
| [zendesk__ticket_field_history](https://github.com/fivetran/dbt_zendesk/blob/master/models/zendesk__ticket_field_history.sql) | A daily historical view of the ticket field values defined in the `ticket_field_history_columns` variable .                                                        |
| [zendesk__sla_breach](https://github.com/fivetran/dbt_zendesk/blob/master/models/zendesk__sla_breach.sql)           | Each record represents an SLA breach event. Calendar and business hour SLA breaches are supported.                                                              |

## Installation Instructions
Check [dbt Hub](https://hub.getdbt.com/) for the latest installation instructions, or [read the docs](https://docs.getdbt.com/docs/package-management) for more information on installing packages.

## Configuration
By default, this package looks for your Zendesk Support data in the `zendesk` schema of your [target database](https://docs.getdbt.com/docs/running-a-dbt-project/using-the-command-line-interface/configure-your-profile). If this is not where your Zendesk Support data is, add the following configuration to your `dbt_project.yml` file:

```yml
# dbt_project.yml

...
config-version: 2

vars:
  zendesk_source:
    zendesk_database: your_database_name
    zendesk_schema: your_schema_name 
```

The `zendesk_ticket_field_history` model generates historical data for the columns specified by the `ticket_field_history_columns` variable. By default, the columns tracked are `status`, `priority`, and `assignee_id`.  If you would like to change these columns, add the following configuration to your `dbt_project.yml` file.  After adding the columns to your `dbt_project.yml` file, run the `dbt run --full-refresh` command to fully refresh any existing models.

```yml
# dbt_project.yml

...
config-version: 2

vars:
  zendesk:
    ticket_field_history_columns: ['the','list','of','column','names']
```

### Disabling models

This package takes into consideration that not every Zendesk account utilizes the `schedule`, `domain_name`, `user_tag`, `organization_tag`, `ticket_form_history`, or `satisfaction_rating` features, and allows you to disable the corresponding functionality. By default, all variables' values are assumed to be `true`. Add variables for only the tables you want to disable:

```yml
# dbt_project.yml

...
config-version: 2

vars:
  zendesk_source:
    using_schedules:            False         #Disable if you are not using schedules
    using_domain_names:         False         #Disable if you are not using domain names
    using_user_tags:            False         #Disable if you are not using user tags
    using_ticket_form_history:  False         #Disable if you are not using ticket form history
    using_organization_tags:    False         #Disable if you are not using organization tags
    using_satisfaction_ratings: False         #Disable if you are not using satisfaction ratings

  zendesk:
    using_schedules:            False         #Disable if you are not using schedules
    using_domain_names:         False         #Disable if you are not using domain names
    using_user_tags:            False         #Disable if you are not using user tags
    using_ticket_form_history:  False         #Disable if you are not using ticket form history
    using_organization_tags:    False         #Disable if you are not using organization tags
    using_satisfaction_ratings: False         #Disable if you are not using satisfaction ratings
```

## Contributions

Additional contributions to this package are very welcome! Please create issues
or open PRs against `master`. Check out 
[this post](https://discourse.getdbt.com/t/contributing-to-a-dbt-package/657) 
on the best workflow for contributing to a package.

## Resources:
- Find all of Fivetran's pre-built dbt packages in our [dbt hub](https://hub.getdbt.com/fivetran/)
- Provide [feedback](https://www.surveymonkey.com/r/DQ7K7WW) on our existing dbt packages or what you'd like to see next
- Learn more about Fivetran [here](https://fivetran.com/docs)
- Check out [Fivetran's blog](https://fivetran.com/blog)
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](http://slack.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices
