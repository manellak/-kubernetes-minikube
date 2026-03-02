
# kubernetes-minikube

Minikube is a tool that lets you run Kubernetes locally.  
Minikube runs a single-node Kubernetes cluster on your personal computer (including Windows, macOS and Linux PCs) so that you can try out Kubernetes or use it for daily development work.

---

## Docker installation

### Installation for Mac, Windows 10 Pro, Enterprise, or Education

https://www.docker.com/get-started

Choose **Docker Desktop**.

### Installation for Windows Home

https://docs.docker.com/docker-for-windows/install-windows-home/

---

## Kubernetes Minikube installation

https://minikube.sigs.k8s.io/docs/start/

Minikube provides a dashboard (web portal). Access it using:

```
minikube dashboard
```

---

## Download this project

This project contains a web service coded in Java (but the language does not matter).  
The project has already been built and the binary version is included.

Download and unzip the project:  
https://github.com/charroux/kubernetes-minikube

---

# Test this project using Docker

Build the Docker image:

```
docker build -t myservice .
```

Check the image:

```
docker images
```

Start the container:

```
docker run -p 4000:8080 -t myservice
```

- 8080 = container port  
- 4000 = host port  

Test in your browser:  
http://localhost:4000  

You should see **hello**.

Stop the service with:

```
Ctrl + C
```

Check the container ID:

```
docker ps
```

Stop the container:

```
docker stop <containerID>
```

---

# Publish the image to Docker Hub

Retrieve the image ID:

```
docker images
```

Tag the Docker image:

```
docker tag e72b6226a2d4 manellak/myservice:latest
```

Login to Docker Hub:

```
docker login
```

Push the image:

```
docker push manellak/myservice:latest
```

---

# Create a Kubernetes deployment from a Docker image

Check that your cluster is running:

```
kubectl get nodes
```

Create the deployment:

```
kubectl create deployment myservice --image=efrei/myservice:1
```

The image comes from Docker Hub:  
https://hub.docker.com/r/efrei/myservice/tags

You can use your own image instead.

Check the pod:

```
kubectl get pods
```

Check detailed information:

```
kubectl describe pods
```

View logs:

```
kubectl logs <pod-name>
```

Enter the container in interactive mode:

```
kubectl exec -it <pod-name> -- /bin/bash
```

If bash is not available:

```
kubectl exec -it <pod-name> -- /bin/sh
```

Exit the container:

```
exit
```

---

# Expose the Deployment through a Service

A Kubernetes Service defines a logical set of Pods and provides a stable access point.

Service types:

- **ClusterIP** – Internal only (default)
- **NodePort** – Accessible via NodeIP:NodePort
- **LoadBalancer** – External load balancer (cloud environments)
- **ExternalName** – Maps to an external DNS name

---

## Expose using NodePort

```
kubectl expose deployment myservice --type=NodePort --port=8080
```

Retrieve the service address:

```
minikube service myservice --url
```

The format is:

```
NodeIP:NodePort
```

Test this address in your browser.

---

# Scaling and Load Balancing

Check deployments:

```
kubectl get deployments
```

Check running pods:

```
kubectl get pods
```

Scale to 2 replicas:

```
kubectl scale --replicas=2 deployment/myservice
```

Verify:

```
kubectl get deployments
kubectl get pods
```

---

# Create a Service of type LoadBalancer

If a service already exists, delete it first:

```
kubectl get services
kubectl delete service <serviceName>
```

Create the LoadBalancer service:

```
kubectl expose deployment myservice --type=LoadBalancer --port=8080
```

Get the URL:

```
minikube service myservice --url
```

---

# Rolling Updates

Update the image:

```
kubectl set image deployment/myservice myservice=dockerHubId/my-image:v2
```

Check rollout status:

```
kubectl rollout status deployment/myservice
```

Rollback:

```
kubectl rollout undo deployment/myservice
```

---

# Create a Deployment and Service using YAML

Deployment YAML:  
https://github.com/charroux/kubernetes-minikube/blob/main/myservice-deployment.yml

NodePort service YAML:  
https://github.com/charroux/kubernetes-minikube/blob/main/myservice-service.yml

LoadBalancer service YAML:  
https://github.com/charroux/kubernetes-minikube/blob/main/myservice-loadbalancing-service.yml

Apply deployment:

```
kubectl apply -f myservice-deployment.yml
```

Apply NodePort service:

```
kubectl apply -f myservice-service.yml
```

Apply LoadBalancer service:

```
kubectl apply -f myservice-loadbalancing-service.yml
```

---

# Routing using Ingress

Enable NGINX Ingress:

```
minikube addons enable ingress
```

Verify:

```
kubectl get pods -n ingress-nginx
```

Apply ingress YAML:

```
kubectl apply -f ingress.yml
```

Get ingress:

```
kubectl get ingress
```

---

## Configure hosts file

### Linux

Edit `/etc/hosts` and add:

```
<minikube-ip> myservice.info
```

Get IP:

```
minikube ip
```

### Mac

Add:

```
127.0.0.1 myservice.info
```

### Windows

Edit:

```
C:\Windows\System32\drivers\etc\hosts
```

Add:

```
127.0.0.1 myservice.info
```

Start tunnel:

```
minikube tunnel
```

Test in browser:

```
http://myservice.info
```

---

# Delete resources

```
kubectl delete service myservice
kubectl delete deployment myservice
```
