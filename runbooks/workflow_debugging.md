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
* 
* What are some test queries that can be used to test the workflows and how do you run those queries


