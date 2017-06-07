# Setup

Here's an abbreviated version of the longer article on JupyterHub.  Basically, this is what I used to set up Paco's class, which is our pilot.  Remember that this is meant to be temporary for a single event and then torn down.

Some thought while doing it:

* Could we enable GitLab's container registry to hold the Docker images we're building?  (https://about.gitlab.com/2016/05/23/gitlab-container-registry/).  This would eliminate another system (DockerHub) and help us continue to build GitLab expertise
* Could we use the GitLab runner infrastructure (https://docs.gitlab.com/ce/ci/runners/README.html) to deploy the content to the cluster on a new commit?
* How do we add a DNS entry and HTTPS on the fly?
* How do we add monitoring/GA directly to the notebooks themselves so that we cn get some visibility on what people are doing?


## Create the Cluster

```
gcloud container clusters create notebook-test \
    --num-nodes=3 \
    --machine-type=n1-highmem-2 \
    --zone=us-central1-b
```

## Initialize helm to the cluster

```
helm init
```

Log out and then log back in.

## Add config.yaml

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

## Upgrade the helm package

```
helm upgrade jupyterhub-test \
 https://github.com/jupyterhub/helm-chart/releases/download/v0.3/jupyterhub-v0.3.tgz \
 -f config.yaml
```

## See if the container is created

```
kubectl --namespace=jupyterhub-test get pod
```

Once status is running, you can go to the next step.

## Open service

First, find the port where JupyterHub is proxying:

```
$ kubectl --namespace=jupyterhub-test get svc proxy-public
NAME           CLUSTER-IP     EXTERNAL-IP       PORT(S)        AGE
proxy-public   10.11.246.60   104.198.245.125   80:30603/TCP   37m
```

Then open the browser to http://104.198.245.125
