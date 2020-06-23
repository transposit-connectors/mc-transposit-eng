# Workflows - Cloudwatch metric graph

## Env vars

**Metrics** is formatted according to https://docs.aws.amazon.com/AmazonCloudWatch/latest/APIReference/CloudWatch-Metric-Widget-Structure.html#CloudWatch-Metric-Widget-Metrics-Array-Format . The env var takes a list of metrics, separated by commas. 

The fourth example:
	
	[ "AWS/EC2", "DiskReadBytes", "InstanceId", "i-01234567890123456" ],
    [ ".", ".", ".", "i-abc" ],
    [ ".", ".", ".", "i-123" ]

Is written in this field as: ["AWS/EC2", "DiskReadBytes", "InstanceId", "i-01234567890123456"], [".", ".", ".", "i-abc"], [".", ".", ".", "i-123"]

**Metric Title** is a text name that will appear in the input prompt of the workflow, and as the title of the image generated.

### Example setup:

**Metrics:** ["AWS/EC2", "CPUUtilization"], ["AWS/RDS", "CPUUtilization"]

**Metric Title:** AWS EC2/RDS CPU Utilization

