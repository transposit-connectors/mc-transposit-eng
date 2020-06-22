# Workflows - ServiceNow

Currently, the Create a ticket workflow takes a `base_url` for a ServiceNow instance, `username` (default: admin) which will represent the user doing actions in ServiceNow, and `table_name` (example: incident) which is what table in ServiceNow the ticket will be created. The only input prompt is for a short description, which is used in the ticket. 

## Step Up
Some of the these steps might not be required if the ServiceNow developer instance has already been provisioned (reclaimed after no use for 10 days), but it is important to go through the steps before trying to use the workflow. 

1. Go to https://developer.servicenow.com
2. Login with ServiceNow Developer Account info in 1Password
2. Once logged in, you will see if there is a instance live on the main page. If there is one provisioned, you will need to wake it up before you use the workflow, takes about 3-4 min. If there is not one provisioned, you will need to request one on the page, takes about 5-10 min. This will give you a URL. 
3. The lastest admin login info for the instance should be saved to ServiceNow Developer Account in 1Password. If you are provisioning a new one, make sure to save it there. 
4. If the action is not already created, create the action with updated configuration information, like base URL and table name. Remember to also update the Integrations auth with the base URL. 

Now, it is ready to be used in a runbook! 

## Troubleshooting

### If you run the action and get no response
Check to make sure the instance is not asleep or has not been reclaimed at https://developer.servicenow.com