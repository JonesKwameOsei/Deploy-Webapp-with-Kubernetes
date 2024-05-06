# Deploying an Online Boutigque Web Application with Kubernetes
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/9690b62b-0ac1-4b4d-a3c9-4304b82b16f9)

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
**N/B**: Google recommds: A shell environment with `gcloud`, `git`, and `kubectl` installed.
3. Lens Desktop 
### Create A Project Repo From Visual Studio Code Terminal
1. Open a new terminal in VScode.
2. Create a directory with a repo name you desire (named mine `Deploy-Webapp-with-Kubernetes`).
3. Initialise directory by running:
```
git init
```
4. Use **HuB** to change the local directory to a remote repo. Run:
```
hub create
```
if you do not have Hub installed, run this command:
```
git remote add origin https://github.com/<github-username>/<repo-name>.git
```

5. Optional: Add README.md (but recommended).
```
echo "# Deploying an Online Boutigque Web Application with Kubernetes" >> README.md
```
6. Push the new repo to GitHub.
```
git add README.md
git commit -m "first commit"
git branch -M main
git push -u origin main
```

### Get Project Resources
1. Clone the repository and  Change directory to microservices-demo:
```
git clone https://github.com/GoogleCloudPlatform/microservices-demo
cd microservices-demo/
```
2. To open and view folders in the current working directory without opening a new window, run: 
```
code -a . 
```
3. in the `kubernetes-manifests`, locate `release` sub-folder and inspect the yaml file to deploy the app. 

**N/B**: Skip steps 4 and 5 if you are not using `gcloud` and `Google Kubernetes Engine (GKE)`. 
4. Set the Google Cloud project and region, and enable the Google Kubernetes Engine API:
```
export PROJECT_ID=<PROJECT_ID>
export REGION=us-central1
gcloud services enable container.googleapis.com --project=${PROJECT_ID}
```
5. Create a GKE cluster and get the credentials for it:
```
gcloud container clusters create-auto online-boutique --project=${PROJECT_ID} --region=${REGION}
```
6. Deploy Online Boutique to the cluster:
```
kubectl apply -f ./release/kubernetes-manifests.yaml
```
**Output**:
```
deployment.apps/currencyservice created
service/currencyservice created
serviceaccount/currencyservice created
deployment.apps/loadgenerator created
serviceaccount/loadgenerator created
deployment.apps/productcatalogservice created
service/productcatalogservice created
serviceaccount/productcatalogservice created
deployment.apps/checkoutservice created
service/checkoutservice created
serviceaccount/checkoutservice created
deployment.apps/shippingservice created
service/shippingservice created
serviceaccount/shippingservice created
deployment.apps/cartservice created
service/cartservice created
serviceaccount/cartservice created
deployment.apps/redis-cart created
service/redis-cart created
deployment.apps/emailservice created
service/emailservice created
serviceaccount/emailservice created
deployment.apps/paymentservice created
service/paymentservice created
serviceaccount/paymentservice created
deployment.apps/frontend created
service/frontend created
service/frontend-external created
serviceaccount/frontend created
deployment.apps/recommendationservice created
service/recommendationservice created
serviceaccount/recommendationservice created
deployment.apps/adservice created
service/adservice created
serviceaccount/adservice created
```
7. Wait for the pods to be ready:
```
kubectl get pods
```
**Output**:
   ```
NAME                                     READY   STATUS    RESTARTS   AGE
adservice-77c7c455b7-sgfqg               1/1     Running   0          8m34s
cartservice-6dc9c7b4f8-2bplz             1/1     Running   0          8m40s
checkoutservice-5f9954bf9f-qm688         1/1     Running   0          8m41s
currencyservice-84cc8dbfcc-bn42r         1/1     Running   0          8m42s
emailservice-5cc954c8cc-75p8t            1/1     Running   0          8m38s
frontend-7d56967868-9tvr6                1/1     Running   0          8m36s
loadgenerator-66c47bdc74-9t7j6           1/1     Running   0          8m41s
paymentservice-55646bb857-4ddjz          1/1     Running   0          8m37s
productcatalogservice-6d58b86c5f-d65pq   1/1     Running   0          8m41s
recommendationservice-5846958db7-kkp9l   1/1     Running   0          8m35s
redis-cart-bf5c68f69-6vcdl               1/1     Running   0          8m39s
shippingservice-67989cd745-gdv4g         1/1     Running   0          8m41s
   ```

8.  Access the web frontend using the frontend's external IP:
```
kubectl get service frontend-external | awk '{print $4}'
```
N/B: The Frontend external service IP might take a while to access. Hence, we will utilise lens to accesss it through port forwarding. 
```
NAME                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
adservice               ClusterIP      10.98.56.54      <none>        9555/TCP         27m
cartservice             ClusterIP      10.97.233.36     <none>        7070/TCP         27m
checkoutservice         ClusterIP      10.103.3.141     <none>        5050/TCP         27m
currencyservice         ClusterIP      10.111.64.38     <none>        7000/TCP         27m
emailservice            ClusterIP      10.108.156.165   <none>        5000/TCP         27m
frontend                ClusterIP      10.108.126.128   <none>        80/TCP           27m
frontend-external       LoadBalancer   10.102.28.139    <pending>     80:30492/TCP     27m                       # The External IP Address is still pending
kubernetes              ClusterIP      10.96.0.1        <none>        443/TCP          7d22h
paymentservice          ClusterIP      10.97.137.30     <none>        50051/TCP        27m
pod-info                NodePort       10.100.226.64    <none>        9898:31797/TCP   7d21h
productcatalogservice   ClusterIP      10.98.161.4      <none>        3550/TCP         27m
recommendationservice   ClusterIP      10.106.153.156   <none>        8080/TCP         27m
redis-cart              ClusterIP      10.105.50.149    <none>        6379/TCP         27m
shippingservice         ClusterIP      10.107.157.192   <none>        50051/TCP        27m
sonarqube               NodePort       10.97.121.56     <none>        9000:32598/TCP   7d10h
```
9. View Deployments from Lens.<p>
Glimpse of Deployments:<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/618f24e5-f9e9-4cd7-9953-faa85acb3b36)<p>
Checking the pods if they are running:<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/9766ce93-99d9-47e3-a48b-0872c47100a8)<p>
Access the frontend through port fowarding in Lens:<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/8c208ff1-78be-43cd-9705-1ee0add5b49c)<P>
Clicking on `Forwarding` will make the application on the browser.<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/b3580bff-6405-403e-8b25-97bd5abf8d34)<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/2ee0b22e-f06a-4367-bbed-3a0a82e9e05e)<p>
To confirm the application is working, let us change the currency to GBP from USD.<p>
Observe that the currecy has changed. <p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/9defb120-a68e-488d-b0aa-d8ca06c707a2)<p>
We want to purchase **Candle holder**, so we will clik on it then add it to cart.<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/a8dc4e40-5cbb-4142-8ba2-9af7cdd1e964)<p>
![image](https://github.com/JonesKwameOsei/Deploy-Webapp-with-Kubernetes/assets/81886509/083259a2-ab84-4a52-adad-6102ed72c199)<p>

### Cleaning Up
When you're done, delete the the clusters
1.To delete the GKE cluster (if you used google cloud), run:
```
gcloud container clusters delete online-boutique --project=${PROJECT_ID} --region=${REGION}
```
2. Stop the port forwarding in Lens
3. To delete the clusters locally in `minikube`, run:
```
kubectl delete -f ./release/kubernetes-manifests.yaml 
```
**Output**:<p>
```
deployment.apps "currencyservice" deleted
service "currencyservice" deleted
serviceaccount "currencyservice" deleted
deployment.apps "loadgenerator" deleted
serviceaccount "loadgenerator" deleted
deployment.apps "productcatalogservice" deleted
service "productcatalogservice" deleted
serviceaccount "productcatalogservice" deleted
deployment.apps "checkoutservice" deleted
service "checkoutservice" deleted
serviceaccount "checkoutservice" deleted
deployment.apps "shippingservice" deleted
service "shippingservice" deleted
serviceaccount "shippingservice" deleted
deployment.apps "cartservice" deleted
service "cartservice" deleted
serviceaccount "cartservice" deleted
deployment.apps "redis-cart" deleted
service "redis-cart" deleted
deployment.apps "emailservice" deleted
service "emailservice" deleted
serviceaccount "emailservice" deleted
deployment.apps "paymentservice" deleted
service "paymentservice" deleted
serviceaccount "paymentservice" deleted
deployment.apps "frontend" deleted
service "frontend" deleted
service "frontend-external" deleted
serviceaccount "frontend" deleted
deployment.apps "recommendationservice" deleted
service "recommendationservice" deleted
serviceaccount "recommendationservice" deleted
deployment.apps "adservice" deleted
service "adservice" deleted
serviceaccount "adservice" deleted
```

## Other Deployment Variations
- **Istio/Anthos Service Mesh**: See [these instructions](https://github.com/GoogleCloudPlatform/microservices-demo/tree/master/docs/service-mesh-anthos.md).
- **non-GKE clusters (Minikube, Kind)**: See the [Development Guide](https://github.com/GoogleCloudPlatform/microservices-demo/tree/master/docs/development-guide.md).
- **Deploy Online Boutique variations with Kustomize**: The `/kustomize` folder contains instructions for customizing the deployment of Online Boutique with different variations, such as integrating with Google Cloud Operations or replacing the in-cluster Redis cache with Google Cloud Memorystore (Redis), AlloyDB, or Google Cloud Spanner.


## Conclusion
Online Boutique is a powerful and flexible demo application that showcases the use of various cloud-native technologies. By providing a comprehensive set of deployment options and customization capabilities, it serves as a valuable resource for developers and DevOps teams looking to explore and experiment with modern cloud-based architectures and deployment strategies.

Through this project, I have significantly expanded my skillset in managing containerized applications on the Kubernetes platform. I now have a deep understanding of containerization with Docker, allowing me to package applications and their dependencies into portable, self-contained units.

My proficiency in Kubernetes has grown immensely. I can now confidently navigate the core concepts, resource management, and deployment strategies of this powerful container orchestration system.
