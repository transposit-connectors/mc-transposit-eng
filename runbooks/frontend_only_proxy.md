# Frontend-only Proxy

This setup is meant to be a minimal setup that allows you to run only the mc frontend.

## Get OSX to trust your cert
Drag and drop proxy/frontend_only_certs/fullchain.pem into the keychain access app. Click on the cert, expand the Trust section, and set to Always Trust

## Remap the s3 bucket that serves the static assets to localhost
Add the following to /etc/hosts
127.0.0.1  s3-us-west-2.amazonaws.com

*You may need to restart chrome to nuke caches when when you toggle on/off the host*

## Build docker
docker-compose -f docker/frontend_only/dev.yml build

## Run docker
If you hit production, chrome often caches such that if you switch back your /etc/hosts, it doesn't take effect unless you restart chrome. So my work around is to always have /etc/hosts redirect the s3 bucket to my server, and then I have 2 proxy configs, one that passes everything through, and another that passes demo -> dev.
docker-compose -f docker/frontend_only/dev.yml up


## Download mc artifacts from CircleCI
So you probably only need to download these artifacts once unless something major changes on the frontend.

Go to [CircleCI](https://circleci.com/gh/transposit)
In the upper right, go to User Settings -> Personal API Tokens
- Create a new token
- In your terminal, run
`export CIRCLE_API_TOKEN='<TOKEN>'`
`export CIRCLE_CI_BUILD_NUMBER='<BUILD_NUMBER>'`
*You can use latest for your build number

Download artifacts from CircleCI
cd scripts
./downloadFrontendOnlyArtifacts.sh transposit/transposit $CIRCLE_CI_BUILD_NUMBER

These will output a bunch of artifacts in /tmp/<CIRCLE_CI_BUILD_NUMBER>

## Install those artifacts
cd javascript/mc
`npm install /tmp/$CIRCLE_CI_BUILD_NUMBER/0/javascript/graphql/target/transposit-graphql`
`npm install /tmp/$CIRCLE_CI_BUILD_NUMBER/0/javascript/typescript-web-api/target/transposit-mc-api/`

## Run the mc frontend
cd javascript/mc
yarn start

## Visit the site and see your changes
https://console.demo.transposit.com

## Prod passthrough
I haven't yet figured out the story with chrome caching. So I created a prod passthrough for the docker conf so that I could switch back and forth to prod without restarting chrome.
`docker-compose -f docker/frontend_only/prod.yml up`

## Other debugging notes

- Is my proxy working?
Go to your network tab in Chrome and find one of the mc bundle files. It will look something like: https://s3-us-west-2.amazonaws.com/prod.static.transposit.com/mc.8a5302e6b515f3791d80.bundle.js

If you get a minimized js file, you are hitting prod. If not, you are hitting your dev environment. Toggle your docker from prod to dev to see how it changes.
