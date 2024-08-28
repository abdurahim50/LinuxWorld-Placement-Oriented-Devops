# Question 1
## Scenario: Deploying a Multi-Container Application with Docker Compose and Kubernetes
You are tasked with deploying a multi-container application that includes a web server (Nginx) and a database (MySQL). Initially, you need to set it up using Docker Compose and later migrate it to Kubernetes.
### How would you approach this task?
#### Instructions
- **Use nginx:latest image**
- Create a service type NodePort
___________________________________________________________________________________________________________________________________________________________________
## solution
This this task is divided into two part
## Part 1: Setup Docker Compose
First you need to create a docker-compose file for the nginx and mysql image
```
version: '3'
services:
  nginx:
    image: nginx:latest
    container_name: linuxworld-nginx-container
    ports:
      - "8080:80"
    depends_on:
      - db
    networks:
      - linuxworld-network

  db:
    image: mysql:latest
    container_name: linuxworld-nginx-container
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: app_db
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - linuxworld-network

networks:
  linuxworld-network:

volumes:
  db_data:
```

#### Run Docker Compose to start the application:
```
docker-compose up -d
```

### Verify the setup:
Access the Nginx web server via http://server-ip:8080

![image](https://github.com/user-attachments/assets/bf0a7bbd-ca63-4e13-99c0-691bed480d07)

#### Run the following command to check the status of your containers and access the mysql:
```
docker ps
docker exec -it <container_name> mysql -u user -p password
```
![image](https://github.com/user-attachments/assets/bcdf748e-f346-4292-bf35-f3e645bd14a3)

## Part 2: Migrating to Kubernetes
- Create Kubernetes YAML files for deployment and service configuration.
```
# nginx-deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
```

#### nginx-service.yml
```
# nginx-service
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30000
  type: NodePort
```
### Create a Secret YAML File to store the mysql password:
```
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
type: Opaque
data:
  mysql-root-password: <base64-encoded-root-password>
  mysql-user-password: <base64-encoded-user-password>
```

To encode the passwords in base64, you can use the following command:

```
echo -n "yourpassword" | base64
```
#### create the mysql deployment
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-root-password
        - name: MYSQL_DATABASE
          value: app_db
        - name: MYSQL_USER
          value: user
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-user-password
        ports:
        - containerPort: 3306
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
```

#### Create PV and PVC
```
# mysql-pv
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"
```

#### Create PVC
```
# mysql-pvc
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```
I used kustomization to deploy and manage the configuration files

#### directory structure

![image](https://github.com/user-attachments/assets/4a55fb6c-90a6-4adb-9687-5a24fba0d72e)

#### kustomization.yml
```
resources:
  - mysql-deployment.yaml
  - mysql-service.yaml
  - nginx-deployment.yaml
  - nginx-service.yaml
  - secret.yaml
  - mysql-pv.yml
  - mysql-pvc.yml
```

# Question 2
## Scenario: Configuring Persistent Storage in Kubernetes for a Stateful Application 
You need to deploy a MySQL database on Kubernetes with persistent storage so that the data is not lost even if the pod restarts.
How would you configure persistent storage for the MySQL database?
### Instructions
- Create a Persistent Volume (PV) of storage 1Gi & accessModes of ReadWriteOnce
- Create a Deployment for the MySQL Database with PVC with image mysql:5.7

___________________________________________________________________________________________________________________________________________________________________

# solution
#### To configure persistent storage in Kubernetes for a MySQL database, ensuring that data is not lost even if the pod restarts.
1. - First you need to create a persistent volume(A Persistent Volume (PV) is a piece of storage in the cluster that has been provisioned by an administrator). The task is to create  a PV with a storage size of 1Gi and an access mode of ReadWriteOnce.
##### mysql-pv.yaml
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"   # This path is specific to your Kubernetes node and may vary
```

2. - Create a Persistent Volume Claim (A Persistent Volume Claim (PVC) is a request for storage by a user). We'll create a PVC that requests storage from the PV we just created.
     
##### mysql-pvc.yaml
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```
3. - Create a Deployment for the MySQL Database
- Now, create a Deployment for the MySQL database, linking it to the PVC to ensure data persistence as requested by th admin using  mysql:5.7 image.
##### mysql-deployment.yaml
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:5.7
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: "rootpassword"  # You can change this password
          volumeMounts:
            - name: mysql-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-storage
          persistentVolumeClaim:
            claimName: mysql-pvc
```
### Proof of concept: 
![image](https://github.com/user-attachments/assets/2d4d4d1a-9ebb-4bc9-9c80-e1e5376883be)






___________________________________________________________________________________________________________________________________________________________________
# Question 3
## Scenario : Auto-Scaling a Kubernetes Deployment Based on CPU Utilisation
You have a web application running on Kubernetes and want to ensure that the application scales automatically based on CPU utilisation.
How would you configure Horizontal Pod Autoscaling (HPA) for this
application?
### Instructions
- Use nginx:latest to create your web application
#### Deploy the Application
- With requests of 100m cpu & limits of 500m cpu
#### Create Horizontal Pod Autoscaler with
- minReplicas = 1
- maxReplicas = 10
- targetCPUUtilizationPercentage = 50
___________________________________________________________________________________________________________________________________________________________________

# Solution
- Horizontal Pod Autoscaling in Kubernetes automatically adjusts the number of Pods in a workload (like a Deployment or StatefulSet) based on demand. When the load increases, more Pods are added, and when the load decreases, the number of Pods is reduced if it exceeds the minimum. This differs from vertical scaling, where more resources are added to existing Pods.

- The HorizontalPodAutoscaler works as a Kubernetes API resource and a controller, adjusting the Pod count based on metrics like CPU or memory usage. However, it doesn’t apply to non-scalable objects like DaemonSets.  [Horizontal Pod Autoscaling - Kubernetes Documentation](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)
  
#### To configure Horizontal Pod Autoscaling (HPA) for a Kubernetes deployment based on CPU utilization as requested,
1. - create a deployment for the web application
##### nginx-deployment.yaml
```
# nginx-deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:latest
          resources:
            requests:
              cpu: 100m  # Request 100m CPU
            limits:
              cpu: 500m  # Limit to 500m CPU
          ports:
            - containerPort: 80
```

2. - Apply the deployment configuration to your Kubernetes cluster.
   
```
kubectl apply -f nginx-deployment.yaml
```
3.  Next create an HPA for the nginx-deployment to automatically scale based on CPU utilization. The HPA will scale the number of pods between 1 and 10 based on a target CPU utilization of 50%.

##### nginx-hpa.yaml

```
# nginx-hpa
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: nginx-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: nginx-deployment
  minReplicas: 1
  maxReplicas: 10
  targetCPUUtilizationPercentage: 50
```
4. Apply the HPA configuration to your Kubernetes cluster.

```
kubectl get hpa
```



___________________________________________________________________________________________________________________________________________________________________
# Question 4
## Scenario : Create a ConfigMap and Use it in a Deployment
Create a ConfigMap containing a custom HTML page content and update the Nginx deployment to use this ConfigMap to serve the custom page.
#### custom-page.html
```
<!-- custom-page.html -->
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>LW Nginx Page</title>
</head>
<body>
<h1>Hello from LW Nginx Page!</h1>
<p>This is a LW custom page served by Nginx using Kubernetes ConfigMap.</p>
</body>
</html>
```

### Instructions
- Create the Config map
- Update the Nginx Deployment
- USe the image nginx:latest


# Question 5
## Scenario : Creating and Using a Service Account for Access Control
Create a Service Account with specific roles and permissions, and use this Service Account in a Deployment to access a Kubernetes Secret.
### Instructions
- Service account name is my-service-account
- Create Role with permissions
```
apiGroups: [""]
resources: ["secrets"]
verbs: ["get", "list"]
```
- Create a RoleBinding to Bind the Role to the Service Account
- Create a secret with hey my-key & value Redhat
- Deploy an Application Using the Service Account
- Use image busybox with the commands:
  
```
["sh", "-c", "cat /etc/secrets/my-key"]
```





