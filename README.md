[Uploading Untitled document - Google Docs.pdf…]()
�
�
 Portfolio Website Deployment Using 
Kubernetes (EKS) 
�
�
 Overview 
This project demonstrates the deployment of a portfolio website using Kubernetes 
(EKS), Docker, and Docker Hub. It includes the steps for setting up EKS, building a 
custom Docker container, and deploying the application using Kubernetes services like 
LoadBalancer. 
�
�
 Table of Contents 
●  Prerequisites  
�
�
 
●  Setup & Installation  
�
�
 
●  Docker Setup  
�
�
 
●  Kubernetes Deployment  
�
�
 
●  Accessing the Application  
�
�
 
●  Cleaning Up Resources  
�
�
 
●  Conclusion  
✅
 
�
�
 Prerequisites 
Before you start, ensure you have the following: 
●  An AWS account 
�
�
 
●  eksctl and kubectl installed for interacting with EKS cluster 
●  Docker installed for building and pushing Docker images 
●  A Docker Hub account for pushing your Docker images 
●  Basic knowledge of Kubernetes and Docker 
�
�‍💻
 
�
�
 Architecture 
The architecture of the portfolio website deployment involves the following components: 
●  AWS EKS Cluster: A managed Kubernetes cluster that hosts the application. 
●  Docker: Used to build and containerize the website. 
●  LoadBalancer Service: Exposes the website to the internet. 
●  Kubernetes Deployment: Ensures the application is running in pods, with multiple 
replicas for scalability. 
The application follows a microservices architecture, where the frontend service is 
independently deployed and managed. 
�
�
 Setup & Installation 
1
 ️
 ⃣
 Install eksctl and kubectl 
●  Install eksctl and kubectl following the official documentation. 
2
 ️
 ⃣
 Create a Cluster for Your Portfolio Website 
eksctl create cluster --node-type t2.micro --nodes 2 
eksctl get cluster 
eksctl get nodes 
�
�
 Docker Setup 
1
 ️
 ⃣
 Install Docker 
●  Download Docker and Docker Desktop from the official Docker website. 
●  Create a Docker Hub account. 
2
 ️
 ⃣
 Create a Dockerfile 
Here is the Dockerfile that configures your website container: 
FROM  httpd 
COPY  assets/ /usr/local/apache2/htdocs/assets 
COPY  website-demo-image/ /usr/local/apache2/htdocs/website-demo-image 
COPY  error.html /usr/local/apache2/htdocs/error.html 
COPY  index.html /usr/local/apache2/htdocs/index.html 
3
 ️
 ⃣
 Build the Docker Image 
Run the following command to build your custom Docker image: 
docker build -t custom-httpd . 
docker images 
4
 ️
 ⃣
 Run the Container Locally 
docker run -d -p 8080:80 custom-httpd 
(If port 8080 is already in use, run on port 8081 instead.) 
To check the locally hosted website, go to:  http://localhost:8080 
5
 ️
 ⃣
 Push the Docker Image to Docker Hub 
●  Login to Docker Hub: 
docker login --username=<username> 
●  Tag and push the Docker image to your Docker Hub repository: 
docker tag imageid <dockerhubrepo>/<username>/custom-httpd 
docker push <username>/repo 
�
�
 ### Kubernetes Deployment 
1
 ️
 ⃣
 Load Balancer and Deployment YAML 
Here’s the manifest file for the LoadBalancer service and deployment: 
apiVersion  :  v1 
kind  :  Service 
metadata  : 
name  :  lb-service 
labels  : 
app  :  lb-service 
spec  : 
type  :  LoadBalancer 
ports  : -  port  :  80 
selector  : 
app  :  frontend --- 
apiVersion  :  apps/v1 
kind  :  Deployment 
metadata  : 
name  :  frontend-deployment 
spec  : 
replicas  :  2 
selector  : 
matchLabels  : 
app  :  frontend 
minReadySeconds  :  30 
strategy  : 
type  :  RollingUpdate 
rollingUpdate  : 
maxSurge  :  1 
maxUnavailable  :  0 
template  : 
metadata  : 
labels  : 
app  :  frontend 
spec  : 
containers  : -  name  :  frontend-container 
image  :  <dockerhubrepolink> 
2
 ️
 ⃣
 Deploy the Application 
Use the following command to deploy the LoadBalancer service and the frontend 
application: 
kubectl apply -f loadbalancerservice.yaml 
kubectl get all 
This will create 2 pods for your frontend application. 
3
 ️
 ⃣
 Access the Running Container 
To enter the running container: 
kubectl  exec  -it  <  pod-name  >  -- /bin/sh 
You can verify the files inside the container by using the  ls  command. 
4
 ️
 ⃣
 Access the Website 
To get the LoadBalancer URL: 
kubectl get service 
�
�
 Output 
After successful deployment, you can access the portfolio website via the LoadBalancer 
URL. The website should be available and accessible from the internet, displaying the 
portfolio content. 
Example of access: 
●  Open the LoadBalancer URL obtained from the  kubectl  get service  command 
in the browser. 
●  The website will be visible, served from the containerized application. 
�
�
 Cleaning Up Resources 
To delete the Kubernetes resources (deployment and service), run the following 
commands: 
kubectl delete deployment  <  pod-name  > 
kubectl delete service lb-service 
This will remove the deployed application and associated resources from your cluster. 
✅
 Conclusion 
This project demonstrates how to deploy a portfolio website using Kubernetes on AWS 
EKS. It covers the steps to set up the environment, build a custom Docker container, 
push the container to Docker Hub, and deploy it on Kubernetes using LoadBalancer 
services. This setup enables seamless access to the website via a scalable 
architecture. 
