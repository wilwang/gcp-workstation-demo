# Cloud Workstation Demo

## Pre-requisites

1. Install JetBrains Gateway (may need to subscribe if free trial is exhausted)
> The predefined code-oss image is the only image that can be run in a web browser. All of the other images require using [JetBrains Gateway](https://www.jetbrains.com/remote-development/gateway/) to access.
2. Identify or create two subnets in us-central1 and us-east1

## Creating a custom image to use for a workstation

Google Cloud Workstations can be customized for your development environment needs. Simply extend one of the [predefined images](https://cloud.google.com/workstations/docs/preconfigured-base-images) with the packges you need to add and then push it to container registry.

### Extend workstation image ([link](https://cloud.google.com/workstations/docs/customize-container-images#container_image_with_emacs_pre-installed))

```
FROM us-central1-docker.pkg.dev/cloud-workstations-images/predefined/code-oss:latest

# install emacs on top of the base image
RUN sudo apt update
RUN sudo apt install -y emacs
```

See example [Dockerfile](./dev-machine-image//Dockerfile)

### Build image and push to [Artifact Registry](https://cloud.google.com/artifact-registry)

```
# CREATE ARTIFACT REPO
# gcloud artifacts repositories create [REPO NAME] --repository-format=[FORMAT] --location=[REGION] --description="[DESCRIPTION]"

gcloud artifacts repositories create cloud-workstations-external --repository-format=docker --location=us-central1 --description="Cloud Workstation Docker repo"

# AUTHENTICATE
# gcloud auth configure-docker [REGION]-docker.pkg.dev

gcloud auth configure-docker us-central1-docker.pkg.dev

# BUILD IMAGE
# docker build dev-machine-image -t [REGION]-docker.pkg.dev/[PROJECT NAME]/[REPOSITORY NAME]/[TARGET IMAGE]:latest

docker build dev-machine-image -t us-central1-docker.pkg.dev/wilwang-sandbox/cloud-workstations-external/demo-dev-image:latest

# PUSH IMAGE TO REPO
# docker push [TARGET IMAGE]

docker push us-central1-docker.pkg.dev/wilwang-sandbox/cloud-workstations-external/demo-dev-image:latest
```

## Create Workstation Clusters

Create a cluster in `us-central1` and a cluster in `us-east1` using the **advanced network settings** and selecting the subnets identified as part of the pre-requisite for the demo. The clusters are created within the subnets which allows the workstations created in those clusters to be attached to those subnets.

By attaching the workstations to the subnets, you can control ingress/egress via firewall rules.

## Create Cluster Configurations

1.  Create a base configuration in the us-central1 cluster using the predefined `base editor` image 
2.  Create a goland configuration in the us-central1 cluster using the predefined `goland` image
3.  Create a custom configuration in the us-east1 cluster using the `customized image` built earlier

```
Make sure that the Compute Engine default service account has access to pull from Artifact Registry
(us-central1-docker.pkg.dev/wilwang-sandbox/cloud-workstations-external/demo-dev-image)
```


## Create Workstations

1. Create `workstation-base` in cluster us-central1 using the `base` configuration
 1. Open a terminal and type **emacs**; it show that it is not installed
2. Create `workstation-goland` in cluster us-central1 using the `goland` configuration
 1. Connect to the workstation using JetBrains Gateway
3. Create `workstation-custom` in cluster us-east1 using the `custom` configuration
 1. Open a terminal and type **emacs**; should be installed
    
    

## Remote Develop from VSCode

1. Install Remote - SSH extension in VSCode
2. Click "Connect using SSH..." to get gcloud command to open tunnel (make sure you are logged into your local gcloud instance)
```
gcloud auth login
gcloud alpha workstations start-tcp-tunnel \
  --project=wilwang-sandbox \
  --cluster=dev-team1 \
  --config=basic-oss \
  --region=us-central1 \
  workstation-basic-oss 22 \
  --local-host-port=:2222
```
3. Click lower left hand green button to connect over SSH
 1. enter `ssh user@localhost:2222 -A`



## Port forwarding
Port forwarding and browser preview are features in Cloud Workstation. If you start a service on port 8080, you can preview the service at port 8080 by changing the url path to your workstation.

1. Go into `workstation-base` and clone gcp-workstations-demo repository
2. Go into hello-server directory and install dependencies and start server
```
npm install
npm start
```

Once server is running, copy and paste the workstation url into a browser window and change the 80 to 8080. The server response should be 'hello'

```
# Url of your workstation
https://80-my-workstation-url


# Url to your running service on port 8080:
https://8080-my-workstation-url
```


