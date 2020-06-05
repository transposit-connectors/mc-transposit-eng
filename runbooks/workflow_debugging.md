# Workflow Debugging

How to debug workflows while oncall

## MongoDB

* Where are the credentials? Can you find out how the user would access their database using the mongo shell command?
* Differences between Mongo Atlas and Mongo on EC2.
* Yoko has an instance on EC2, where is the link to see that on AWS
* There is a lambda that calls the mongodb commands using the Nodejs drivers. It's deployed under the development-admin role in us-west-2. https://us-west-2.console.aws.amazon.com/lambda/home?region=us-west-2#/functions/mongodb_lambda_bridge?tab=configuration
* A link to the lambda bridge
* Links to logs and metrics for lambda bridge
* There is a production transposit app that's also involved, where are the logs for that
* Link to the mongodb workflow github repos and link to them in the transposit org as well
* What are some test queries that can be used to test the workflows and how do you run those queries


