# Setup

## Create the Cluster

```
gcloud container clusters create notebook-test \
    --num-nodes=3 \
    --machine-type=n1-highmem-2 \
    --zone=us-central1-b
```

## Initialize helm

```
helm init
```

Log out and then log back in.

## Generate JupyterHub tokens


# Add config.yaml

```
hub:
  cookieSecret: "2a0f804afce787de9cf90f332b325c827defe741c2c584958c773eaff4890390"
token:
  proxy: "1dfa277014fac9f3d054e203325e8e44c80432c8c8f8bfafd8f2ac1603c3458d"
singleuser:
  storage:
    type: none
  image:
    name: aodewahn/a41124835ed0
    tag: latest
```



## Upgrade

```
helm upgrade jupyterhub-test \
 https://github.com/jupyterhub/helm-chart/releases/download/v0.3/jupyterhub-v0.3.tgz \
 -f config.yaml
