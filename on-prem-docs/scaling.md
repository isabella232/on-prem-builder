# Front-end Scaling
With any tiered or HA deployment of the builder services you'll likely want to horizontally scale your front-end nodes. The most common deployment pattern for this usecase is a pool of front-end nodes fronted by a load-balancer.

## Deploying New Front-ends
The on-prem-builder install.sh script now supports scaling front-end nodes as a deployment pattern. It is required that new front-ends be deployed on a separate compute from your initial on-prem deployment. Since builder services will now need to communicate accross your network between the frontend and backend nodes, you must open the folowing ports to these nodes in order to guarantee your on-prem builder properly functions:

* TCP 9638 - Habitat configuration gossip
* UDP 9638 - Habitat configuration gossip
* TCP 9636 - Builder API HTTP
* TCP 5432 - postgresql
* TCP 9000 - minio
* TCP 11211 - memcached

### Create and update bldr.env
The bldr.env file for your single on-prem builder node contains most of the information required to bootstrap a new front-end and will be used during the installation process. However, some configuration will need to change.

Update the values of `OAUTH_REDIRECT_URL`, `OAUTH_CLIENT_ID` and `OAUTH_CLIENT_SECRET` as per the on-premise OAuth2 provider.

In the case that your on-prem-builder cluster is backed by cloud services and you are running multiple front-end instances `OAUTH_REDIRECT_URL` should be pointed to your load-balancer. 

In the case that you are _not_ backing your cluster with cloud services you will need to update the values of `POSTGRES_HOST`, and `MINIO_ENDPOINT`.

Additionally, you will need to edit (or create if it is not already present) `HAB_BLDR_PEER_ARG` to include all frontend and backend nodes hosting builder services. The format is as follows:

```
--peer host1 --peer host2 --peer host3
```

### Install frontend
Run the front-end install script from each new front-end node `./install.sh --install-frontend`
