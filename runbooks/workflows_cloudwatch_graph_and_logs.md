# Workflows - Cloudwatch (graph and logs)

## Cloudwatch graph metrics

### Env vars

**Metrics** is formatted according to https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/CloudWatch-Metric-Widget-Structure.html#CloudWatch-Metric-Widget-Metrics-Array-Format . The env var takes a list of metrics, separated by commas. The 4th example:
```
        [ "AWS/EC2", "DiskReadBytes", "InstanceId", "i-01234567890123456" ],
        [ ".", ".", ".", "i-abc" ],
        [ ".", ".", ".", "i-123" ]
```

Could be written in this field as: `["AWS/EC2", "DiskReadBytes", "InstanceId", "i-01234567890123456"], [".", ".", ".", "i-abc"], [".", ".", ".", "i-123"]`

**Metric Title** is a text name that will appear in the input prompt of the workflow, and as the title of the image generated.

### Example setup:
Metrics: `["AWS/EC2", "CPUUtilization"], ["AWS/RDS", "CPUUtilization"]`
Metric Title: AWS EC2/RDS CPU Utilization



## Cloudwatch fetch logs

Runbook steps are defined by second-level headings. Text following a second-level heading, like this, describes and contains information about a runbook step.

Click on **Add action** above to insert a URL pointing to an action. These are just Markdown links, but are rendered as buttons inside Slack, and when clicked, will run the action.

You can use `Markdown` *formatting* and [the usual syntax](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet), but be aware some formatting and some Markdown features, including tables, images, and inline HTML, won't be displayed when viewing a runbook inside Slack.

## Step 2

Each second-level heading defines a separate step. The runbook's table of contents is made up of each of the runbook's steps.

### Third-level headings like this are treated as headings only

To divide information within a step, you can use a third-level heading.

## Step 3

And that's how runbooks are made. Happy runbooking!