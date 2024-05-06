# Deploying an Online Boutigque Web Application with Kubernetes

## Project Overview 
Online Boutique: Is a **Cloud-Native Microservices Demo Application** which is depolyed in the web with Kubernetes to serve its customers (imaginary customers). In this project, I will replicate this deployment to harness my skillsets in **Kubernetes** and microservice applications. 

## Introduction
Online Boutique is a cloud-first microservices demo application developed by Google. It consists of an 11-tier microservices architecture and is designed as a web-based e-commerce application where users can browse items, add them to the cart, and purchase them (Google). 

The application is used by Google to showcase the use of various cloud-native technologies such as Kubernetes, Google Kubernetes Engine (GKE), Istio, Stackdriver, and gRPC. It is designed to be easily deployable on any Kubernetes cluster, with little to no configuration required.

## Technical Details
Online Boutique is composed of the following 11 microservices:
1. Adservice
2. Cartservice
3. Checkoutservice
4. Currencyservice
5. Emailservice
6. Frontend
7. Loadgenerator
8. Paymentservice
9. Productcatalogservice
10. Recommendationservice
11. Shippingservice

The services communicate with each other using gRPC, and the application also utilizes an in-cluster Redis cache for storing customer cart information.

## Deployment Instructions
To deploy Online Boutique, the following steps are taken:
1. An IDE, I am using **VSCode**. 
**N/B: On the official google repo, they recommended: A Google Cloud project
2. Set up a shell env with `docker`, `git`, and `kubectl` in VSCode.
**N/B**: Google recommds: A shell environment with `gcloud`, `git`, and `kubectl` installed

### Create A Project Repo From Visual Studio Code Terminal
1. Open a new terminal in VScode
2. Create a directory with a repo name you desire (named 

### Get Project Resources
1. Clone the repository:
   ```
   git clone https://github.com/GoogleCloudPlatform/microservices-demo
   cd microservices-demo/
   ```
2. Set the Google Cloud project and region, and enable the Google Kubernetes Engine API:
   ```
   export PROJECT_ID=<PROJECT_ID>
   export REGION=us-central1
   gcloud services enable container.googleapis.com --project=${PROJECT_ID}
   ```
3. Create a GKE cluster and get the credentials for it:
   ```
   gcloud container clusters create-auto online-boutique --project=${PROJECT_ID} --region=${REGION}
   ```
4. Deploy Online Boutique to the cluster:
   ```
   kubectl apply -f ./release/kubernetes-manifests.yaml
   ```
5. Wait for the pods to be ready:
   ```
   kubectl get pods
   ```
6. Access the web frontend using the frontend's external IP:
   ```
   kubectl get service frontend-external | awk '{print $4}'
   ```
7. When you're done, delete the GKE cluster:
   ```
   gcloud container clusters delete online-boutique --project=${PROJECT_ID} --region=${REGION}
   ```

## Other Deployment Variations
- **Istio/Anthos Service Mesh**: See [these instructions](https://github.com/GoogleCloudPlatform/microservices-demo/tree/master/docs/service-mesh-anthos.md).
- **non-GKE clusters (Minikube, Kind)**: See the [Development Guide](https://github.com/GoogleCloudPlatform/microservices-demo/tree/master/docs/development-guide.md).
- **Deploy Online Boutique variations with Kustomize**: The `/kustomize` folder contains instructions for customizing the deployment of Online Boutique with different variations, such as integrating with Google Cloud Operations or replacing the in-cluster Redis cache with Google Cloud Memorystore (Redis), AlloyDB, or Google Cloud Spanner.

## Conclusion
Online Boutique is a powerful and flexible demo application that showcases the use of various cloud-native technologies. By providing a comprehensive set of deployment options and customization capabilities, it serves as a valuable resource for developers and DevOps teams looking to explore and experiment with modern cloud-based architectures and deployment strategies.
