# Debugging Customer Workflows

This runbook contains documentation on each customer-facing (global) workflow

## Cloudwatch fetch logs

### Env vars

**Default_pattern** is a plain text field, and is documented at https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html . The pattern entered here will auto-populate as the initial value in an input prompt field where it can be changed.

**Deafult_lookback_time** takes a number of minutes, which is how many minutes back in the logs to search.  The value entered will auto-populate as the initival value in an input prompt field where it can be changed. Note that leaving this blank will return all logs matching the filter pattern, and this may result in timeouts.

**log_group_name** is the name of a cloudwatch log group, and must be defined in the action. This value will be shown in the input prompt, but is not changeable.

## Cloudwatch metric graph

### Env vars

**Metrics** is formatted according to https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/CloudWatch-Metric-Widget-Structure.html#CloudWatch-Metric-Widget-Metrics-Array-Format . This env var takes a list of metrics, separated by commas. 

The fourth example:
	
	[ "AWS/EC2", "DiskReadBytes", "InstanceId", "i-01234567890123456" ],
    [ ".", ".", ".", "i-abc" ],
    [ ".", ".", ".", "i-123" ]

Is written in this field as: `["AWS/EC2", "DiskReadBytes", "InstanceId", "i-01234567890123456"], [".", ".", ".", "i-abc"], [".", ".", ".", "i-123"]`

**Metric Title** is a text name that will appear in the input prompt of the workflow, and as the title of the image generated.

### Example setup:

**Metrics:** `["AWS/EC2", "CPUUtilization"], ["AWS/RDS", "CPUUtilization"]`

**Metric Title:** AWS EC2/RDS CPU Utilization

## ServiceNow

Currently, the Create a ticket workflow takes a `base_url` for a ServiceNow instance, `username` (default: admin) which will represent the user doing actions in ServiceNow, and `table_name` (example: incident) which is what table in ServiceNow the ticket will be created. The only input prompt is for a short description, which is used in the ticket. 

### Step Up
Some of the these steps might not be required if the ServiceNow developer instance has already been provisioned (reclaimed after no use for 10 days), but it is important to go through the steps before trying to use the workflow. 

1. Go to https://developer.servicenow.com
2. Login with ServiceNow Developer Account info in 1Password
2. Once logged in, you will see if there is a instance live on the main page. If there is one provisioned, you will need to wake it up before you use the workflow, takes about 3-4 min. If there is not one provisioned, you will need to request one on the page, takes about 5-10 min. This will give you a URL. 
3. The lastest admin login info for the instance should be saved to ServiceNow Developer Account in 1Password. If you are provisioning a new one, make sure to save it there. 
4. If the action is not already created, create the action with updated configuration information, like base URL and table name. Remember to also update the Integrations auth with the base URL. 

Now, it is ready to be used in a runbook! 

### Troubleshooting

### If you run the action and get no response
Check to make sure the instance is not asleep or has not been reclaimed at https://developer.servicenow.com

