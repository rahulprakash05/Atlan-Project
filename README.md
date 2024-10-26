# Atlan-Project






![diagram-export-10-26-2024-2_41_38-PM](https://github.com/user-attachments/assets/58296a8d-0bf2-4fa3-8f90-18bfbc11a5b0)









# E-commerce Kubernetes Deployment
This repository contains the Kubernetes manifests and deployment steps for an e-commerce application consisting of the following components:
1. Frontend: React app served by Nginx.
2. Backend: Node.js microservices.
3. Database: MongoDB cluster.
4. Message Queue: RabbitMQ for asynchronous order processing.

# Prerequisites
 1. Kubernetes cluster - v 1.26 or higher
 2. Installed helm 
 

## Phase 1: Deployment

### 1. Configuration and Deployment
-  create kubernetes manifest file for each component and apply them.
 
#### Step 1: Frontend (Nginx + React)
- Kubernetes front-end deployment had been defined in the **Atlan-project/kubernetes/frontend**
- Apply the manifest:

        kubectl apply -f frontend-deployment.yaml

- Front-end service has been defined in **Atlan-project/kubernetes/frontend**

- Apply the service-manifest:

        kubectl apply -f frontend-service.yaml

- Front-end config-maps has been defined in **Atlan-project/kubernetes/frontend**
- Apply front-end-configMap.yaml

        kubectl apply -f frontend-configMap.yaml

#### Step 2: Backend (Node.js microservices)
- Back-end deployment has been defined in **Atlan-project/kubernetes/backend**
- Apply the back-end-manifest:

        kubectl apply -f backend-deployment.yaml

- Back-end service has been defined in **Atlan-project/kubernetes/backend**
- Apply the back-end-service.yaml :

        kubectl apply -f backend-service.yaml

- Bront-end config-maps has been defined in **Atlan-project/kubernetes/backend**
- Apply backend-configmaps-secrets.yaml

    kubectl apply -f backend-configmaps-secrets.yaml

#### Step 3: MongoDB
- StatefulSet for MongoDB with Persistent Volumes for data persistence has been defined in **Atlan-project/kubernetes/database**
- Apply statefulset-mangoDB.yaml 
    
        kubectl apply -f statefulset-mangoDB.yaml

- MongoDB service config is defined in **Atlan-project/kubernetes/database**
- Apply mangoDB-service.yaml file

        kubectl appply -f mangoDB-service.yaml

#### Step 4: RabbitMQ
- Defined a Deployment for RabbitMQ in **Atlan-project/kubernetes/rabbitMQ**
- Apply rabbit-deployment.yaml file

        kubectl apply -f rabbit-deployment.yaml

  - Apply rabbit-service.yaml file
 
        kubectl apply -f rabbit-service.yaml

  - Apply rabbit-pv.yaml file

        kubectl apply -f rabbit-pv.yaml

  - Apply rabbit-pvc.yaml file
 
        kubectl apply -f rabbit-pvc.yaml
    

### 2. Networking
Step 1: Install Nginx Ingress Controller
- Run

        kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml

- Above link will get ingress-controller.yaml file 

Step 2: Configure Nginx Ingress for front-end
- Nginx-ingress configuration is defined in **Atlan-project/kubernetes/ingress** file

- Apply ingress.yaml file:

        kubectl apply -f ingress.yaml

  
### 3. Security and Compliance

Step 1: Install Gatekeeper
        - Run the following command to install Gatekeeper:
        
          kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yam

Step 2: Create Security Policies with Gatekeeper

    1: Enforcing Non-Root User Policy
        - config file is under gatekeeper "kubernetes/gatekeeper/enforcing-non-root-user-policy"
        - Apply both 'non-root-user-enforcement.yaml' & 'non-root-user-template.yaml' files

            kubectl apply -f non-root-user-template.yaml
            kubectl apply -f non-root-user-enforcement.yaml

    2: Deny Privileged Containers
        - config file is under gatekeeper "kubernetes/gatekeeper/deny-privileged-containers"
        - Apply both 'privileged-c-constraints.yaml' & 'privileged-enforcer.yaml' files

            kubectl apply -f privileged-c-constraints.yaml
            Kubectl apply -f privileged-enforcer.yaml

    3: Disable Privilege Escalation
        - config file is under gatekeeper "kubernetes/gatekeeper/disable-privilege-escalation"
        - Apply both 'no-allow-privilege-escalation-template.yaml' & 'deny-privilege-escalation.yaml' files

            kubectl apply -f no-allow-privilege-escalation-template.yaml
            kubectl apply -f deny-privilege-escalation.yaml

Step 3: Monitor Gatekeeper
        - Check the status of policies using:

            kubectl get constraints -- #This will list the active constraints and any violations.


## Phase 2: Observability and Scaling

### 1. Monitoring and Logging:

#### 1. Monitoring Setup (Prometheus & Grafana)
-   Install Prometheus (Helm-based Installation) 
    - Run the following commands
    
            helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

            helm repo update

            helm install prometheus prometheus-community/prometheus --namespace monitoring --create-namespace
        
-   Verify Scrape Targets

        kubectl get svc -n monitoring

-   Get prometheus serviceIP and port
-   Access Prometheus at http://<IP-address>:9090
-   Check Targets:
        - Go to Status > Targets in the Prometheus UI. 
        - Here, you should see all your targets.

- Install Grafana
        - Install Grafana using Helm:

        helm install grafana grafana/grafana --namespace monitoring

        - Get Grafana service details:

        kubectl get svc -n monitoring

        - Note down the external IP of the Grafana service. Access grafana through web UI

- Login to Grafana:
        - Default credentials:
                - Username: admin
                - Password: admin (Change this after login)

- Configure Prometheus as a Data Source in Grafana
        - Open Grafana → Configuration → Data Sources → Add data source.

        - Select Prometheus.

        - Set the URL as:

                http://prometheus-server.monitoring.svc.cluster.local

- Click Save & Test.

- Set up Dashboards
        - Import pre-built dashboards for Kubernetes, Node.js, MongoDB, and RabbitMQ in Grafana.
        - Go to Dashboards → Import.
        - Enter the Dashboard IDs for the components:
                
                Kubernetes: 315
                MongoDB: 1860
                RabbitMQ: 9961
                
#### 2. Logging Setup (ELK)
Step 1: Install Elasticsearch (Elasticsearch will serve as the data store for logs collected from your Kubernetes components.)
- Install Elasticsearch using Helm

        helm repo add elastic https://helm.elastic.co   #Add the Elastic Helm chart repository
        helm repo update
        helm install elasticsearch elastic/elasticsearch --namespace logging --create-namespace   #Install Elasticsearch using the Helm chart

- Verify the installation

        kubectl get pods -n logging
        kubectl get svc -n logging


Step 2: Install Kibana
- Install Kibana using Helm

        helm install kibana elastic/kibana --namespace logging

- Kibana config file is in **Atlan-project/monitoring-logging/**
- Apply the config file

        kubectl apply -f kibana-lb.yaml

- Now access Kibana at http://<IP-Address>:5601


Step 3: Install Logstash
-  Install Logstash using Helm

        helm install logstash elastic/logstash --namespace logging  #Install logstash in your kubernetes cluster

- Configure Logstash Pipeline
        - Create a ConfigMap to define the Logstash pipeline
        - Apply the ConfigMap: **Atlan-project/monitoring-logging/logstash-pipeline-configmap.yaml**

                kubectl apply -f logstash-pipeline-configmap.yaml

- Modify Logstash Deployment: To mount the logstash-pipeline config     
        
        kubectl edit deployment logstash -n logging

- In the Logstash deployment YAML, add the following
        
        spec:
           containers:
           - name: logstash
           args:
           - "-f"
           - "/usr/share/logstash/pipeline/logstash.conf"
           volumeMounts:
           - name: logstash-pipeline
           mountPath: /usr/share/logstash/pipeline/logstash.conf
           subPath: logstash.conf
           volumes:
           - name: logstash-pipeline
           configMap:
              name: logstash-pipeline


Step 4: Install Filebeat for Log Collection: (collect logs from Kubernetes pods and send them to Logstash)
- Install Filebeat using Helm

        helm install filebeat elastic/filebeat --namespace logging

- Filebeat config file is in **Atlan-project/monitoring-logging/filebeat-config.yaml**

- Apply the ConfigMap

        kubectl apply -f filebeat-config.yaml

- Update Filebeat DaemonSet

        kubectl edit daemonset filebeat -n logging

- Add the following volume and volumeMounts to the filebeat container

        spec:
           containers:
           - name: filebeat
             volumeMounts:
             - name: filebeat-config
             mountPath: /usr/share/filebeat/filebeat.yml
             subPath: filebeat.yml
           volumes:
           - name: filebeat-config
           configMap:
             name: filebeat-config


Step 5: Verify ELK Stack Setup
- Access Kibana: http://<EXTERNAL-IP>:5601

- Set Up Kibana Index Pattern
        - In Kibana:

        Go to Management → Index Patterns → Create Index Pattern.
        Create a new index pattern for your logs: kubernetes-logs-*.
        In the Discover tab, you should be able to see the logs collected from your Kubernetes components


### 2. Autoscaling
Step 1: Horizontal Pod Autoscaling (HPA)

Horizontal Pod Autoscaling (HPA) scales the number of pods in your deployment based on resource metrics such as CPU or memory usage. Here's how to implement it for your frontend (Nginx) and backend (Node.js) services.

- Prerequisites
    - Metrics Server: Ensure that the Metrics Server is installed in your cluster, as HPA relies on it to collect CPU and memory usage metrics.
    - Install Metrics Server:

        
        kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml


    - Verify that Metrics Server is running
        
        kubectl get apiservices | grep metrics

1. Create HPA for Frontend (Nginx)
     - Deploy the Frontend Application: Ensure that your frontend (Nginx) service is deployed in Kubernetes.
     - Create an HPA for Nginx: Define an HPA for the Nginx deployment based on CPU usage.

                kubectl autoscale deployment frontend-nginx \ --cpu-percent=50 \ --min=2 \ --max=10


     - Check the status of the HPA

                kubectl get hpa

2.  Create HPA for Backend (Node.js)
        - Deploy the Backend Application: Ensure that the backend (Node.js) service is deployed.
        - Define an HPA for the backend service.

                kubectl autoscale deployment backend-nodejs \ --cpu-percent=60 \ --min=3 \ --max=15

        - Monitor HPA: 

                kubectl get hpa


Step 2: Cluster Autoscaling
- Prerequisites for Cluster Autoscaler
        - Ensure that your Kubernetes cluster is deployed on a cloud platform that supports Cluster Autoscaler for example AWS cloud
        - Set up IAM permissions for the Cluster Autoscaler

1. Install Cluster Autoscaler using Helm: 

        helm repo add autoscaler https://kubernetes.github.io/autoscaler
        helm repo update
        helm install cluster-autoscaler autoscaler/cluster-autoscaler \ --namespace kube-system \ --set cloudProvider=aws

2. Configure Cluster Autoscaler
        - Cluster autoscaler config file is in **Atlan-pproject/Autoscaling/cluster-autoscaler-deployment.yaml**
        - Apply the Autoscaling/cluster-autoscaler-deployment.yaml

                kubectl apply -f Autoscaling/cluster-autoscaler-deployment.yaml

Step 3: Verify and Monitor Autoscaling

1. Verify Horizontal Pod Autoscaling (HPA): simulate the cpu usage load using kubctl run command 

        kubectl get hpa  #check the status of HPA

2. Monitor Cluster Autoscaling

        kubectl get nodes

3. Monitor the Cluster Autoscaler logs

        kubectl logs -f deployment/cluster-autoscaler -n kube-system






  




