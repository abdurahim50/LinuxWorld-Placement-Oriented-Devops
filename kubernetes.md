# Question 1
## Scenario: Deploying a Multi-Container Application with Docker Compose and Kubernetes
You are tasked with deploying a multi-container application that includes a web server (Nginx) and a database (MySQL). Initially, you need to set it up using Docker Compose and later migrate it to Kubernetes.
### How would you approach this task?
#### Instructions
- **Use nginx:latest image**
- Create a service type NodePort
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






