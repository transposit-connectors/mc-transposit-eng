# Debugging Workflows - MongoDB

kill_op_in_mongodb and list_current_ops_in_mongodb

### MongdoDB on Yoko's sandbox
* Where are the credentials? Can you find out how the user would access their database using the mongo shell command?
* Differences between Mongo Atlas and Mongo on EC2.
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

### Debugging the workflows
MongoDB workflows call the webhooks for that Transposit app using the raw_transposit_webhook connector.

* Make sure that the connector's configuration is set correctly, using the baseURL of the deployed lambda bridge Transposit app.
* Make sure that any operations that call raw_transposit_webhook have the correct API key listed. Unfortunately, the only way that this can be saved in the workflow itself for now is to hard-code it in the api.run calls.
* Make sure that the workflow calls the webhook correctly and receives the expected results.
