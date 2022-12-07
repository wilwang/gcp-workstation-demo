# Port forwarding
Port forwarding and browser preview are features in Cloud Workstation. If you start a service on port 8080, you can preview the service at port 8080 by changing the url path to your workstation.

```
# Url of your workstation
https://80-my-workstation-url


# Url to your running service on port 8080:
https://8080-my-workstation-url
```


# Build Docker image and push to Artifact Registry

```
# gcloud artifacts repositories create [REPO NAME] --repository-format=[FORMAT] --location=[REGION] --description="[DESCRIPTION]"
gcloud artifacts repositories create demo-servers --repository-format=docker --location=us-central1 --description="Small servers for demo purposes"

# gcloud auth configure-docker [REGION]-docker.pkg.dev
gcloud auth configure-docker us-central1-docker.pkg.dev

# docker build [DOCKERFILE DIR] -t [REGION]-docker.pkg.dev/[PROJECT NAME]/[REPOSITORY NAME]/[TARGET IMAGE]:latest
docker build . -t us-central1-docker.pkg.dev/wilwang-sandbox/demo-servers/node-hello-api:latest

# docker push [TARGET IMAGE]
docker push us-central1-docker.pkg.dev/wilwang-sandbox/demo-servers/node-hello-api:latest

```


# Create a GKE autopilot cluster


# Deploy node-server to GKE

```
kubectl create deployment hello-server --image=us-central1-docker.pkg.dev/wilwang-sandbox/demo-servers/node-hello-api:latest
kubectl expose deployment hello-server --type LoadBalancer --port 80 --target-port 8080

kubectl get pods
kubectl get service hello-server


```