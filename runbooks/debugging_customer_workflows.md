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

