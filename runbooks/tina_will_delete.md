# Tina will delete

## Introduction

This runbook is based off of our friend Split's [Getting Started Docs](https://help.split.io/hc/en-us/articles/360025035812-Introduction-getting-started)


Welcome to Split! This article will walk through setting up your split instance, installing the Split SDK, and creating your first split to release rapidly and safely with feature flags and measure impact with experimentation. Specifically, it will cover:

- Step 1: Install the SDK
- Step 2: Create a split and target users
- Step 3: Send event data
- Step 4: Create a metric, monitor and measure the impact

For additional information or to dive right into reference docs, explore our help center to find feature reference guides, best practices, video walkthroughs and frequently asked questions.

- Install: [Learn](https://help.split.io/hc/en-us/categories/360001534111-Install) how to install Split’s SDKs to control feature flags in your application

- Administer: Learn about managing and maintaining your account and users [here](https://help.split.io/hc/en-us/categories/360001534151-Administer)

- Target & Configure: Understand how to target your customers using Split’s feature flags [here](https://help.split.io/hc/en-us/categories/360001538072-Target)

- Monitor & Experiment: Measure impact and make data-driven decisions with experimentation [here](https://help.split.io/hc/en-us/categories/360001538172)

- Integrate: Understand Split’s API and connect Split with the solutions you use today [here](https://help.split.io/hc/en-us/categories/360001538192-Integrate)

- Collaborate: Functionality for teams that need more controls and additional organization [here](https://help.split.io/hc/en-us/categories/360001538152-Collaborate)

- Secure: Carry your security best practices through into Split [here](https://help.split.io/hc/en-us/categories/360001551491-Secure)

- Support: Understand support definitions and incident response times [here](https://help.split.io/hc/en-us/categories/360000489832-Support-and-Training)


Want to watch step by step videos? Explore all our video content [here](https://help.split.io/hc/en-us/articles/360025949151-Split-Videos)


## Getting Started

### Create an account / join an account
We assume you’ve created an account already, but if not, sign up [here](https://www.split.io/signup/). If your team has an account, an admin can invite you from the users page in admin settings.

### Understand Organizations and Workspaces
When you sign up you are sent a link to create an organization. We utilize your company name as the organization name. This can be changed later in your organization’s admin settings.

Within this organization, you can create multiple workspaces. Your company should have one organization in Split but can have multiple workspaces within that organization. When you first create your organization, you'll have one workspace named Default. This workspace has two environments and one traffic type underneath it. You can rename and edit these environments and traffic types as well as add more.

Workspaces allow you to separately manage your feature flags and experiments across your different business units, product lines, and/or applications. Each workspace in Split has its own separate set of environments, SDK API keys, splits, segments, metrics, and event types.

Learn more about [workspaces](https://help.split.io/hc/en-us/articles/360023534451).

### Understand Environments, Traffic Types and API keys
Environments allow you to manage your splits throughout your development lifecycle — from local development to staging and production. When you first create your organization, your workspace is provided with two environments. These two environments are named staging and production, and can be re-named. Learn more about [environments](https://help.split.io/hc/en-us/articles/360019915771).

Each environment is automatically set up with its own API keys. Use these API keys to connect the Split SDK to a specific environment. If you are setting Split up with a server side SDK, be sure to use the server API key type. If you are setting Split up in the browser or on a mobile client, be sure to use the browser API key type. If you are interested in using Split’s public API, create an admin API key type. Learn more about [API keys](https://help.split.io/hc/en-us/articles/360019916211).

Use traffic types to easily identify the customer traffic you are splitting. A traffic type is a particular identifier type for any hierarchy of your customer base. Traffic types in Split are customizable and can be any key you choose to send to Split, i.e. a user ID, account ID, IP address, browser ID, etc. Essentially, any internal database key you're using to track what "customer" means to you. When you first create your organization, your workspace has one traffic type - user - but you can easily create additional like guest or anonymous. Learn more about [traffic types](https://help.split.io/hc/en-us/articles/360019916311).


## Step 1: Install the SDK

### Overview
Our SDKs are designed live at the application layer of your application and provide a secure and out-of-the-box method for controlling your experiments and feature flags. Each SDK has two main functions:

- Serve as a decision engine for your application
- Automatically capture what variants of your features your customer's are served

### Decision Engine
When you set up rules for your feature flags and experiments to be targeted to subsets of your customer base in Split(e.g. run a 50/50 test of a new home page on customers in New York), our SDKs automatically download down these rules and maintain a local copy of them on your machines. From there, our SDKs then take care of keeping themselves up to date by periodically checking for any changes to the rules that are made in the Split web console.

When your application then loads for your customers, you can simply ask the SDK via a method called getTreatment to decide what variant of a feature the customer should see. Since the SDK is maintaining a local copy of the rules that govern your features and experiments, it can simply reference that copy of your rules and make the decision to serve "on" or "off" to your customer without having to make a single remote call. From there, you can take the decision returned by our SDK and use that information to serve up the proper experience to your customer. In this manner, our SDK is able to abstract out any need to hardcode this type of decision making in your application.

You can find out how to spin up one of our ten SDKs at each languages specific reference doc:

- Android [docs](https://help.split.io/hc/en-us/articles/360020037072). 
- GO [docs](https://help.split.io/hc/en-us/articles/360020093652).
- iOS [docs](https://help.split.io/hc/en-us/articles/360020401491).
- Java [docs](https://help.split.io/hc/en-us/articles/360020405151).
- JavaScript [docs](https://help.split.io/hc/en-us/articles/360020448791).
- .NET [docs](https://help.split.io/hc/en-us/articles/360020240172).
- Node.js [docs](https://help.split.io/hc/en-us/articles/360020564931).
- PHP [docs](https://help.split.io/hc/en-us/articles/360020350372).
- Python [docs](https://help.split.io/hc/en-us/articles/360020359652).
- Ruby [docs](https://help.split.io/hc/en-us/articles/360020673251).

### Capturing what your Customer was served
Each time our SDK makes a decision of what your customer should be served, it automatically takes that information and queues it up on your machines. The SDK then takes care of all the work in passing this information up to Split in the background without ever slowing down your application. By capturing this information, you can easily understand what customers are being served and set the basis for being able to properly measure your experiments



## Step 2: Create a split and target users
You can create a split in three easy steps:

1. Set your split name

2. Add your split to an environment

3. Decide who sees what version of your split.

### 1. Set your split name and information
To create your first split, click on the “splits” icon in the left navigation and click the button labeled “create split”. From there enter the split name.

Select the traffic type “user” to get started. Traffic types give you the ability to split on different levels of your customers - (e.g. For B2B software, you could create the traffic type "account" or “user”). If you’d like, you can create additional [traffic types](https://help.split.io/hc/en-us/articles/360019916311) in settings. Learn more about traffic types. We will refer to the traffic type “user” throughout the rest of this document.

Owners, Tags, and the Description make it easy to sort, filter, and locate the features your team is rolling out. By default administrators and the creator of the split will be considered its owners. Utilize groups with this owner field to organize your splits across your team. Learn more about [owners](https://help.split.io/hc/en-us/articles/360020582092) and [tags](https://help.split.io/hc/en-us/articles/360020839151).

Learn more about [creating a split](https://help.split.io/hc/en-us/articles/360020523792).

### 2: Add your split to an environment
To configure your Split for a particular environment, select the environment and then click the “add rules” button. Once you click “add rules” you will be able to create and define targeting rules including treatments.

### 3: Decide who sees what version of your split
Treatments are the different variants or versions of your split that you serve to your users. When you click “add rules” you will be shown the targeting tab for a particular split. You can use this targeting tab to assign your treatments. We preset "on" and "off" for each new split. You can edit these treatment names and/or add additional treatments.

You can use the next three sections - whitelist, traffic allocation, and targeting rules - to explicitly assign treatments or target based on dependencies or demographic data as attributes.

- Whitelist: This section allows you to explicitly assign individual users or groups of users to one treatment.
- Traffic allocation (advanced): This section allows you to randomly assign a percentage of your users to be evaluated by the rules below. This feature is recommended for more advanced experimentation use cases.
- Targeting rules: This section allows you to build out if/else-if statements to use demographic data as attributes to assign treatments to users and/or build dependencies with other features you manage in Split.

The final section default rule, serves as a catch-all for the users not targeted in prior sections. We preset the default rule to "off" to allow you to deploy in a safe state. The default shown below the default rule is used for anyone not included in traffic allocation, or as the safest treatment should you need to kill the split. Learn more about the [default treatment](https://help.split.io/hc/en-us/articles/360020527672-Set-the-default-treatment).

Click save changes to configure the rules for this split in the environment you selected. Learn more about [targeting](https://help.split.io/hc/en-us/articles/360020791591-Target-customers).

Now that you've set up this split's targeting rules, let's take a look at how this split would work in your code. To split a feature, you can copy and wrap the provided code snippet around your feature's treatments. You can find the code snippet in the syntax in the menu next to Save Changes. Select the language of your application and use the "getTreatment" method to decide which treatment is served to your users. Below is an example in Javascript. Learn more in the basic use section of each SDK reference guide ([Javascript example](https://help.split.io/hc/en-us/articles/360020448791-JavaScript-SDK#basic-use)).

```
client.on(client.Event.SDK_READY, function() {
  var treatment = client.getTreatment("SPLIT_NAME");
  if (treatment == "on") {
      // insert code here to show on treatment
  } else if (treatment == "off") {
      // insert code here to show off treatment
  } else {
      // insert your control treatment code here
  }
});
```


## Step 3: Send event data

### Send event data
Measure the impact of your feature rollout on your customer experience by sending Split event data and calculating metrics based on those events. Events allow you to record any actions your users perform and experiences your users encounter, such as response times or errors.

Event data can be sent to Split in one of three ways:

1. Call Split's SDK `track` method (example below)
2. Post a JSON body to Split's `events` [API](https://docs.split.io/reference#events-overview)
3. Integrate with [Segment](https://help.split.io/hc/en-us/articles/360020742532-Segment) for product usage data
4. Integrate with [Sentry](https://help.split.io/hc/en-us/articles/360029879431-Sentry) or install our [RUM agent](https://help.split.io/hc/en-us/articles/360030898431-Javascript-RUM-Agent) for engineering performance data

The API and integration routes allow you to ingest event data from existing sources (i.e. take advantage of telemetry from existing instrumentation or analytics). Use the track method if you want to explicitly add instrumentation code to your application to record events.

Below is an example of calling the `track` method of the SDK in Javascript. See links above for the API and integration routes.
```
// parameters
var queued = client.track('TRAFFIC_TYPE', 'EVENT_TYPE'), eventValue);
// Example
var queued = client.track('user', 'page_load_time', 83.334)
```

Learn more in the track section of each SDK reference guide ([Javascript example](https://help.split.io/hc/en-us/articles/360020448791-JavaScript-SDK#track)).

To help verify that events are being received by Split, click over admin settings > event types. There you can see event types and a stream of events per event type. Learn more about [events](https://help.split.io/hc/en-us/articles/360020585772-Track-events).


## Step 4: Create a metric, monitor and measure the impact

You can create a metric, monitor and measure impact in four easy steps:

1. Define your metric name and meta information
2. Define your metric definition
3. Create an alert policy
4. Manage alerts
5. Review metrics impact during a review period
6. Analyze metric impact details and trends over time

### 1. Set your metric name and information
Once you are sending us event data, to create your first metric, click on the “metrics” icon in the left navigation and click the button labeled “create metric”. From there, you can enter your metric name. We recommend describing what you intend to measure with a unique and recognizable name. Similar to splits, you can easily specify meta information for your new metric such as name, description, owners, and tags. This information can help you and your team manage your metrics. Learn more about [creating a metric](https://help.split.io/hc/en-us/articles/360020586132-Create-a-metric).

### 2. Define your metric definition
When defining the metric definition you can determine the winning direction. If increasing this metric is good for your business and customer experience, select “increase”. If not, select “decrease”. The traffic type applies to the event type associated with the metric. Select traffic type “user”. To get started, select “count of events per user” and select an event you sent. Learn more about [defining your metric](https://help.split.io/hc/en-us/articles/360020843931-Metric-definition)

### 3. Create an alert policy
An alert policy allows you to configure a degradation threshold for your metric. If the feature flag causes the metric to go below or above that threshold you will be alerted via your configured notification channel. Learn more about [creating an alert policy](https://help.split.io/hc/en-us/articles/360030044331)

### 4. Manage alerts
Any alerts which have been triggered will be displayed both on the "Targeting" and "Alerts" tab on the split page. When youn receiving an alert you can quickly rollback the split by clicking "kill" or dismiss the alert. Learn more about [managing alerts](https://help.split.io/hc/en-us/articles/360030045491-Manage-alerts)

### 5. Review metric impact during a review period
The review periods will be preset via the admin settings. You can access your metric impact by searching for the split you want to see the impact for and navigating to the third tab on the right, “metrics impact”. Once here you will see the impact on your organization’s metrics, first ordered by key metrics and then ordered by the additional organizational metrics which reached statistical significance. Learn more about [review periods](https://help.split.io/hc/en-us/articles/360020635912-Review-period-check) and [filtering your metrics](https://help.split.io/hc/en-us/articles/360020848451-Apply-filters)

### 6. Analyze metric impact details and trends over time
Information such as the impact % , p-value, and error margin will be surfaced on the metric card. You can click on the metric card to access additional information. This additional information includes how the impact has changed over time, the metric dispersion and the sample size used to calculate the metric value. Learn more about metric details and trends.



























