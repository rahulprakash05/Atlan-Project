# Atlan-Project
# E-commerce Kubernetes Deployment
This repository contains the Kubernetes manifests and deployment steps for an e-commerce application consisting of the following components:
1. Frontend: React app served by Nginx.
2. Backend: Node.js microservices.
3. Database: MongoDB cluster.
4. Message Queue: RabbitMQ for asynchronous order processing.

## Phase 1: Deployment

### 1. Configuration and Deployment
-  create kubernetes manifest file for each component and apply them.
  
#### Step 1: Frontend (Nginx + React)
- Define a Deployment and Service for the Nginx server that serves the React app.
- # E-commerce Kubernetes Deployment

This repository contains the Kubernetes manifests and deployment steps for an e-commerce application consisting of the following components:

- **Frontend**: React app served by Nginx.
- **Backend**: Node.js microservices.
- **Database**: MongoDB cluster.
- **Message Queue**: RabbitMQ for asynchronous order processing.

---

## Phase 1: Deployment

### 1. Configuration and Deployment

We will create Kubernetes manifests (YAML files) for each component and apply them to the cluster.

#### Step 1: Frontend (Nginx + React)
- Kubernetes front-end deployment had been defined in the **"front-end-deployment.yaml"**
- Apply the manifest:
    #kubectl apply -f frontend-deployment.yaml
- Front-end service has been defined in **front-end-service.yaml**
- Apply the service-manifest:
    #kubectl apply -f front-end-service.yaml
- Front-end config-maps has been defined in front-end-configMaps.yaml file
- Apply front-end-configMap.yaml
    #kubectl apply -f front-end-configMap.yaml

#### Step 2: Backend (Node.js microservices)
- Back-end deployment has been defined in **"back-end-deployment.yaml"**
- Apply the back-end-manifest:
    #kubectl apply -f back-end-deployment.yaml
- Back-end service has been defined in **"back-end-service.yaml"**
- Apply the back-end-service.yaml :
    #kubectl apply -f back-end-service.yaml
- Bront-end config-maps has been defined in backend-configmaps-secrets.yaml file
- Apply backend-configmaps-secrets.yaml
    #kubectl apply -f backend-configmaps-secrets.yaml

#### Step 3: MongoDB
- StatefulSet for MongoDB with Persistent Volumes for data persistence has been defined in statefulset-mangoDB.yaml file
- Apply statefulset-mangoDB.yaml #kubectl apply -f statefulset-mangoDB.yaml
- MongoDB service config is defined in mangoDB-service.yaml file
- Apply mangoDB-service.yaml file
   #kubectl appply -f mangoDB-service.yaml

#### Step 4: RabbitMQ
- Defined a Deployment and Service for RabbitMQ in rabbit-deployment-service.yaml file.
- Apply rabbit-deployment-service.yaml file
    #kubectl apply -f rabbit-deployment-service.yaml

### 2. Networking
Step 1: Install Nginx Ingress Controller
- Run
    #kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
- this will create nginx-ingress controller in your cluster
Step 2: Configure Nginx Ingress for front-end
- Nginx-ingress configuration is defined in **ingress.yaml** file
- Apply ingress.yaml file:
    #kubectl apply -f ingress.yaml

### 3. Persistence
- To ensure data persistence and resilience configured stateful-sets with MongoDB as database and configured PVC
- Apply statefulset-mangoDB.yaml file
    #kubectl apply -f statefulset-mangoDB.yaml
- Apply mangoDB-service.yaml file
    #kubectl apply -f mangoDB-service.yaml
- RabbitMQ could use a PersistentVolumeClaim similarly for persistent queues.
- Apply rabbit-deployment-service.yaml file:
    #kubectl apply -f rabbit-deployment-service.yaml
  




  
