# Atlan-Project

# E-commerce Kubernetes Deployment
This repository contains the Kubernetes manifests and deployment steps for an e-commerce application consisting of the following components:
1. Frontend: React app served by Nginx.
2. Backend: Node.js microservices.
3. Database: MongoDB cluster.
4. Message Queue: RabbitMQ for asynchronous order processing.

# Pre-requisites
 1. Kubernetes cluster - v 1.26 or higher
 2. Installed helm 
 

## Phase 1: Deployment

### 1. Configuration and Deployment
-  create kubernetes manifest file for each component and apply them.
 
#### Step 1: Frontend (Nginx + React)
- Kubernetes front-end deployment had been defined in the **"front-end-deployment.yaml"**
- Apply the manifest:

        kubectl apply -f frontend-deployment.yaml

- Front-end service has been defined in **front-end-service.yaml**

- Apply the service-manifest:

        kubectl apply -f front-end-service.yaml

- Front-end config-maps has been defined in front-end-configMaps.yaml file
- Apply front-end-configMap.yaml

        kubectl apply -f front-end-configMap.yaml

#### Step 2: Backend (Node.js microservices)
- Back-end deployment has been defined in **"back-end-deployment.yaml"**
- Apply the back-end-manifest:

        kubectl apply -f back-end-deployment.yaml

- Back-end service has been defined in **"back-end-service.yaml"**
- Apply the back-end-service.yaml :

        kubectl apply -f back-end-service.yaml

- Bront-end config-maps has been defined in backend-configmaps-secrets.yaml file
- Apply backend-configmaps-secrets.yaml

    kubectl apply -f backend-configmaps-secrets.yaml

#### Step 3: MongoDB
- StatefulSet for MongoDB with Persistent Volumes for data persistence has been defined in statefulset-mangoDB.yaml file
- Apply statefulset-mangoDB.yaml 
    
        kubectl apply -f statefulset-mangoDB.yaml

- MongoDB service config is defined in mangoDB-service.yaml file
- Apply mangoDB-service.yaml file

        kubectl appply -f mangoDB-service.yaml

#### Step 4: RabbitMQ
- Defined a Deployment and Service for RabbitMQ in rabbit-deployment-service.yaml file.
- Apply rabbit-deployment-service.yaml file

        kubectl apply -f rabbit-deployment-service.yaml

### 2. Networking
Step 1: Install Nginx Ingress Controller
- Run

        kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml

- this will create nginx-ingress controller in your cluster

Step 2: Configure Nginx Ingress for front-end
- Nginx-ingress configuration is defined in **ingress.yaml** file

- Apply ingress.yaml file:

        kubectl apply -f ingress.yaml

### 3. Persistence
- To ensure data persistence and resilience configured stateful-sets with MongoDB as database and configured PVC
- Apply statefulset-mangoDB.yaml file

        kubectl apply -f statefulset-mangoDB.yaml

- Apply mangoDB-service.yaml file

        kubectl apply -f mangoDB-service.yaml

- RabbitMQ could use a PersistentVolumeClaim similarly for persistent queues.
- Apply rabbit-deployment-service.yaml file:
    
        kubectl apply -f rabbit-deployment-service.yaml
  
### 4. Security and Compliance
<<<<<<< HEAD
step 1. Install Gatekeeper
    - Run the following command to install Gatekeeper:

            kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
=======

Step 1: Install Gatekeeper
        - Run the following command to install Gatekeeper:
        - #kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
>>>>>>> 041271cf606045d090406ba2333ccb109c6ea67d

Step 2: Create Security Policies with Gatekeeper

    1: Enforcing Non-Root User Policy
        - config file is under gatekeeper "kubernetes/gatekeeper/Enforcing Non-Root User Policy"
        - Apply both 'non-root-user-enforcement.yaml' & 'non-root-user-template.yaml' files

            kubectl apply -f non-root-user-template.yaml
            kubectl apply -f non-root-user-enforcement.yaml

    2: Deny Privileged Containers
        - config file is under gatekeeper "kubernetes/gatekeeper/Deny Privileged Containers"
        - Apply both 'privileged-container-template.yaml' & 'deny-privileged-containers.yaml' files

            kubectl apply -f privileged-container-template.yaml
            Kubectl apply -f deny-privileged-containers.yaml

    3: Disable Privilege Escalation
        - config file is under gatekeeper "kubernetes/gatekeeper/Disable Privilege Escalation"
        - Apply both 'no-allow-privilege-escalation-template.yaml' & 'deny-privilege-escalation.yaml' files

            kubectl apply -f no-allow-privilege-escalation-template.yaml
            kubectl apply -f deny-privilege-escalation.yaml

Step 3: Monitor Gatekeeper
        - Check the status of policies using:

            kubectl get constraints -- #This will list the active constraints and any violations.


## Phase 2: Observability and Scaling

### 1. Monitoring and Logging:

#### 1. Monitoring Setup (Prometheus & Grafana)
- Install the monitoring and logging tools using the helm charts

    1. Install Prometheus (Helm-based Installation)
    - Run the following commands
    
        helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
        helm repo update
        helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace


        











  




