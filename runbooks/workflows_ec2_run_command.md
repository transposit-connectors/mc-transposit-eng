# Workflows - EC2 Run command

The workflow takes an instance id and a shell command, which is executed and then checked until the command reaches a terminal state. The terminal state is printed to slack along with the standard output and standard error, if they exist.

## Requirement: AWS Systems Manager

An instance must be managed by AWS Systems Manager for a command to be sent to it. The workflow will check that the instance ID entered is in the list of managed instances retrieved from the AWS Systems Manager API.

To get a list of managed instances in AWS Console, navigate to the service "Systems Manager" and click on the link "Managed Instances" in the left menu.

You can also get a list of managed instances from the workflow in dev, by running the operation "list_instances" https://console.transposit.com/dev/t/transposit/ec2_run_command/code/op/list_instances

