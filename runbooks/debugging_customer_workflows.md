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

## MongoDB

kill_op_in_mongodb and list_current_ops_in_mongodb

### MongdoDB on Yoko's sandbox
* Where are the credentials? Can you find out how the user would access their database using the mongo shell command?
* Differences between Mongo Atlas and Mongo on EC2 - If the database url is a dns hostname (and not an ip address), we attach `+srv` to the connection string (code in the connector) to tell MongoDB to query DNS for the record (more information [here](https://docs.mongodb.com/manual/reference/connection-string/))
* Yoko has an instance on EC2, where is the link to see that on AWS

### Debugging the lambda that calls MongoDB
There is a lambda that calls the MongoDB commands using the Nodejs drivers. 
    
* Where it's deployed: under the development-admin role in us-west-2. https://us-west-2.console.aws.amazon.com/lambda/home?region=us-west-2#/functions/mongodb_lambda_bridge?tab=configuration. 
* There are test events set up at the lambda itself on AWS. Make sure those work correctly.
* Where the code lives: https://github.com/transposit-connectors/mongodb_lambda_bridge. You can make updates directly in AWS, but make sure those updates are reflected on github

### Debugging the webhook that calls the lambda
The lambda is itself called by a Transposit app that then exposes webhooks that can be used by workflows. 

* Where it's deployed: https://console.transposit.com/dev/t/transposit/lambda_bridge/. 
* The lambda bridge app's webhooks can be tested as well: there are http_event default values set for each of the webhook operations. Modify those to test functionality. 
* Changes should be synced back to its GitHub repo as well: https://github.com/transposit-connectors/lambda_bridge

### Debugging the workflows kill_op_in_mongodb and list_current_ops_in_mongodb
MongoDB workflows call the webhooks for that Transposit app using the raw_transposit_webhook connector.

* Make sure that the connector's configuration is set correctly, using the baseURL of the deployed lambda bridge Transposit app.
* Make sure that any operations that call raw_transposit_webhook have the correct API key listed. Unfortunately, the only way that this can be saved in the workflow itself for now is to hard-code it in the api.run calls.
* Make sure that the workflow calls the webhook correctly and receives the expected results.

## Debugging Workflows - K8s Get Pod Status with Azure Functions Proxy

rocos/k8s_pod_status

### About
Morad and Dan created an Azure functions proxy to allow Rocos to communicate between the Transposit platform (`rocos/k8s_pod_status` workflow) and their Azure AKS kubernetes cluster. The k8s cluster API server is fronted by a self-signed SSL certificate that Transposit does not recognize as legitimate.

This function uses single bearer token authentication tied to a service account /cluster role binding pair configured on the kubernetes cluster in advance. This proxy **WILL NOT** work as is with regular user authentication (which typically depends on client certificate data found in tke kubeconfig file). It is meant to be a temporary measure for Rocos until it can be addressed more appropriately from the Transposit product side.

We have not scoped exactly which permissions the k8s cluster role binding will need to grant for this to work. It is likely to be dependant on the scope of the Transposit workflow making requests to the k8s API server.

### Debugging the proxy that calls the K8s(AKS) API Server
There is a Node function which proxies the request to the k8s (Azure AKS) API server and disables validation of the self-signed SSL cert fronting the specific k8s API server 
    
* Where it's deployed: Access https://portal.azure.com using the credentials for `test-user1@transposit.com` listed in 1password. Navigate to **App Services** and select the item titled *ankrim-test*
* From the Azure Functions app screen, use the nav bar on the left of the screen. Click on **Functions** in the Functions sub-menu, then click *ankrim-proxy-test*.
* Where the code lives: From the *ankrim-proxy-test* screen, look in the nav bar on the left side of the screen for the *Developer* sub-menu. Then click on **Code + test**
* To test: Since the function uses a portion of the incoming request URL to create the proxied request, I've been testing with curl from my local machine and adding `-H "Authorization: Bearer $TOKEN"` to pass the correct auth. Once that worked to satisfaction, I would test from the Transposit dev platform and eventually a runbook embedded action.
* The function outputs logging data to the embedded Azure console. The majority of issues have been visible from the request header, which is printed to the console logs on each invocation.

### Debugging the workflow rocos/k8s_pod_status
This kubernetes workflow calls sends its request to the Azure Functions proxy directly, with the same URL path which would be used if it were communicating diretly with the k8s API server directly. Only the hostname is different.

* Make sure that the connector's configuration is set correctly, using a kubernetes bearer token associated with a k8s service account / cluster role binding pair with correct permissions to get pod status and access the specified namespace
* Do not attempt to use this workflow with "human user" auth for one of the cloud provider kubernetes implementations. This user type generally requires client certificate data as well.
* Make sure that the workflow sends its request to the proxy correctly and returns the correct results. The `kubectl` CLI utility can be used to verify results using the same auth as the workflow (in the kubeconfig file format)


