# Copy Existing AWS Environment

## CloudFormation

CloudFormation is an IaaS tool that AWS provides for creating AWS resources in a straightforward and repeatable manner. There are built-in visual modeling tools, as well as tools to copy existing environments.

If you have an existing AWS environment that you'd like to copy for use as a customized demo or customer sandbox, AWS provides the CloudFormer application. CloudFormer introspects an existing environment and provides a CloudFormation template. 

### Pre-requisites

* An account that has appropriate permissions
* That you are logged into that account through the console

## Create CloudFormer App

* Log in to the AWS CloudFormation console and click Create Stack
* Select `Use a sample template` in the `Choose a sample template` dropdown, choose `CloudFormer`, then click Next to specify parameters
* Choose a name for the CloudFormer stack
* In the `Parameters` section, choose a username and password you'll use to log in to CloudFormer and click Next and Next again on the next page
 * The password field cannot be blank and cannot contain special characters
* Review the information and select `I acknowledge that this template may create IAM resources`, then click Create

The process of creating the CloudFormer application may take a few minutes.

## Launch CloudFormer

* Click the CloudFormer entry in the CloudFormation console and select the `Outputs` pane
* In the `Value` column, click the URL listed to launch CloudFormer
* Type the username and password that you entered from before

## Using CloudFormer to Create CloudFormation Template

* Select the template's region from `Select the AWS Region` and then click `Create Template`
 * This might take several minutes, as it's analyzing the account and determining dependencies
 * Please wait for the `Intro` page to display
* Enter a description for your template on the `Intro` page
* You can choose to either manually specify the resources you want to clone, or choose everything  
 * Click `Select all resources in your account` to choose everything
* You can inspect and edit resource configurations on the next few pages if necessary. Click `Continue` to skip
 * These include DNS, VPC, Network, Storage, Security, Compute, and Applicaton Services
* Review the configuration on the `Summary` page and click `Continue` to generate the template
* The `CloudFormation Template` page shows the generated template. Enter `transposit-config-sandbox-${user}` in the `S3 bucket` field and click `Save Template`
 * Replace `${user}` with your username

 Now that you have a template, you can point CloudFormation at the S3 bucket location and it will create the environment.

## Delete CloudFormer App

* Open the CloudFormation console
* Click `Actions` and then `Delete Stack`
* Select the name that you gave to the CloudFormer app 
