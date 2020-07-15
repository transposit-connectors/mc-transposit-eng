# Manually deleting indices from Elasticsearch

Sometimes it is desirable to delete old indices from a cluster - either to free up storage space, reduce the number of shards, etc.

## Overview

**Warning: before doing this, you should discuss in #oncall and make sure there is a consensus that this is the correct course of action. You should probably also practice on a sandbox before making changes in demo/staging/prod.**

In general, managing indices is done through Elasticsearch’s API. When making API calls to Elasticsearch in AWS, the requests need to be signed. The easiest way to do this is to set up kibana and point it to your desired Elasticsearch cluster: [Elasticsearch](https://transposit.atlassian.net/wiki/spaces/DEV/pages/33783809/Elasticsearch). Open up kibana in your browser and navigate to the **Dev tools** tab, where you can execute API calls.

## Backups

AWS automatically takes hourly snapshots of each individual index in our clusters. If anything goes wrong, we can restore specific indices. In the following screenshot, you can see the call to list snapshots. The result shows that the snapshot name is `cs-automated` and is stored in S3.
```
GET _snapshot
```
You can drill into `cs-automated` to see individual snapshots:

```
GET _snapshot/cs-automated/_all
```
The last API call shows how one would restore a specific index (or multiple indices, comma-separated) to a specific snapshot.

```
POST _snapshot/cs-automated/2020-07-14t20-24-09.deb44672-16dd-4742-942c-91f8fe968d71/_restore
{
  "indices": "start-request-logs-2019-12"
}
```

More information can be found in the AWS [docs](https://docs.aws.amazon.com/elasticsearch-service/latest/developerguide/es-managedomains-snapshots.html).

## Deleting

It’s often useful to start by simply listing out the indices on the cluster:

```
GET _cat/indices
```
Then, to delete an index, simply make a DELETE request to the index name.

```
DELETE end-request-logs
```
The index should now be deleted. You can list the indices again and confirm that the index you deleted no longer shows up.

