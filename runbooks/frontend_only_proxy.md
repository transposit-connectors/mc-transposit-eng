# Frontend-only Proxy
This setup is meant to be a minimal setup that allows you to run only the mc frontend.

## Pre-reqs
The goal of this setup is so that you don't need to have a full development setup on your laptop to build and run the frontend. However, you will need to do a subset of the [Getting Started](https://transposit.atlassian.net/wiki/spaces/DEV/pages/15400961/Getting+Started) wiki.

You need to have a copy of our [source repo](https://github.com/transposit/transposit).

Packages from brew:
- jq
- yarn
- node

Docker via Homebrew is pretty confusing.  Easiest to grab the package from the [site](https://www.docker.com/docker-mac).

You will also need access to Transposit's CircleCI build, which you should have if you have a Transposit Github account.

## Grab the Certs
Copy the `[frontend_only_certs](https://drive.google.com/drive/folders/102UD3jYLk1560_KN_skJoZDWREVcKWls)` directory to the `transposit/proxy`.

## Install Certs
You need to get OSX to trust our proxy's self-signed certs. Drag and drop `proxy/frontend_only_certs/fullchain.pem` onto the keychain access app to import it. Then click on the `s3-us-west-2.amazonaws.com` cert, expand the Trust section, and set to Always Trust

## Remap the s3 bucket that serves the static assets to localhost
Add the following to /etc/hosts

`127.0.0.1  s3-us-west-2.amazonaws.com`

Reset the Chrome cache by going to the following page `chrome://net-internals/#dns`

## Build and run docker

Build docker one time. You probably don't need to do this often.

`docker-compose -f docker/frontend_only/dev.yml build`

Run docker

`docker-compose -f docker/frontend_only/dev.yml up`

## Download mc artifacts from CircleCI
You probably only need to download and install these artifacts once unless something major changes on the frontend.

Go to [CircleCI](https://app.circleci.com/settings/user/tokens)
In the bottom left, go to User Settings -> Personal API Tokens (this somewhat depends on your CircleCI version)
- Create a new token
- In your terminal, run
`export CIRCLE_API_TOKEN='<TOKEN>'`
`export CIRCLE_CI_BUILD_NUMBER='<BUILD_NUMBER>'`
*You can use latest for your build number

Download artifacts from CircleCI

`cd scripts`

`./downloadFrontendOnlyArtifacts.sh transposit/transposit $CIRCLE_CI_BUILD_NUMBER`

*Note: these will output a bunch of artifacts in /tmp/<CIRCLE_CI_BUILD_NUMBER>*

## Install those artifacts and run the frontend
From the _javascript/mc_ directory:

`npm install /tmp/$CIRCLE_CI_BUILD_NUMBER/0/javascript/graphql/target/transposit-graphql`

`npm install /tmp/$CIRCLE_CI_BUILD_NUMBER/0/javascript/typescript-web-api/target/transposit-mc-api/`

And then run the frontend:

`yarn start`

## Install those artifacts and run the frontend (Sarah's notes)
This avoids changing package.json and its lock file

From the _javascript/mc_ directory:

```
mkdir -p ../graphql/target/transposit-graphql
cp -r /tmp/$CIRCLE_CI_BUILD_NUMBER/0/javascript/graphql/target/transposit-graphql/ ../graphql/target/transposit-graphql
mkdir -p ../typescript-web-api/target/transposit-mc-api
cp -r /tmp/$CIRCLE_CI_BUILD_NUMBER/0/javascript/typescript-web-api/target/transposit-mc-api/ ../typescript-web-api/target/transposit-mc-api 
yarn
yarn start
```

## See it work!

Go to [prod](https://console.transposit.com), [staging](https://console.staging.transposit.com), or [demo](https://console.demo.transposit.com)

How can you tell which version you are hitting?

Go to your network tab in Chrome and find one of the mc bundle files. It will look something like: `https://s3-us-west-2.amazonaws.com/prod.static.transposit.com/mc.8a5302e6b515f3791d80.bundle.js`

If you get a minimized js file, you are hitting prod. If not, you are hitting your dev environment. Toggle your docker from prod to dev to see how it changes.
