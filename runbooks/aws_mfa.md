# AWS MFA

This runbook is to help out with some handy notes on how we use AWS roles and MFA

## Setting environment variables


## AWS CLI as_session

We have this [plugin](https://github.com/transposit/awscli-as-session) that takes your aws role and creates a session token for you that is valid for an hour. What it does under the covers is unsets `AWS_PROFILE` if you have set and instead sets the AWS environment variables `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_SESSION_TOKEN`.

It can sometimes cause problems because AWS is inconsistent in how it wants to deal with profile configuration.