# Workflows - Cloudwatch fetch logs

## Env vars

**Default_pattern** is a plain text field, and is documented at https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/FilterAndPatternSyntax.html . The pattern entered here will auto-populate as the initial value in an input prompt field where it can be changed.

**Deafult_lookback_time** takes a number of minutes, which is how many minutes back in the logs to search.  The value entered will auto-populate as the initival value in an input prompt field where it can be changed. Note that leaving this blank will return all logs matching the filter pattern, and this may result in timeouts.

**log_group_name** is the name of a cloudwatch log group, and must be defined in the action. This value will be shown in the input prompt, but is not changeable.

