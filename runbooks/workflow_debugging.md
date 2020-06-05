# Workflow Debugging

How to debug workflows while oncall

## MongoDB

* Where are the credentials? Can you find out how the user would access their database using the mongo shell command?
* Differences between Mongo Atlas and Mongo on EC2.
* Yoko has an instance on EC2, where is the link to see that on AWS
* There is a lambda that calls the mongodb commands using the Nodejs drivers. 
    * It's deployed under the development-admin role in us-west-2. https://us-west-2.console.aws.amazon.com/lambda/home?region=us-west-2#/functions/mongodb_lambda_bridge?tab=configuration. 
    * If you make changes to the live version, please update the version at GitHub as well: https://github.com/transposit-connectors/mongodb_lambda_bridge.
* The lambda is itself called by a Transposit app that then exposes webhooks that can be used by workflows. 
    * The deployed app is at https://console.transposit.com/dev/t/transposit/lambda_bridge/. 
    * Changes should be synced back to its GitHub repo as well: https://github.com/transposit-connectors/lambda_bridge
* MongoDB workflows call the webhooks for that Transposit app using the raw_transposit_webhook connector.
    * Make sure that the connector's configuration is set correctly, using the baseURL of the deployed lambda bridge Transposit app.
    * Make sure that any operations that call raw_transposit_webhook have the correct API key listed. Unfortunately, the only way that this can be saved in the workflow itself for now is to hard-code it in the api.run calls.
* Each piece can be tested on its own:
    * There are test events set up at the lambda itself on AWS. Make sure those work correctly.
    * The lambda bridge app's webhooks can be tested as well: there are supposedly-functioning http_event default values set for each of the webhook operations. Modify those to test functionality. 
    * Make sure that the workflow calls the webhook correctly and receives the expected results.


