# graylog-kubernetes-mongo
# graylog cluster in kubernetes (with MongoDB)
# Steps to install graylog cluster 

### 1. Make sure kubectl is up and working , connecting to kubenetes
### 2a. Run graylog cluster with Clustered mongo With replication and EBS persistent volume
```
  kubectl create -f ha-mongo-volume.yaml,elasticsearch-deployment.yaml,elasticsearch-service.yaml,graylog-service.yaml,graylog-deployment.yaml,ha-mongo-headless-service.yaml,ha-mongo-statefulset.yaml

```
### 2a. Deleting the mongo cluster with replication (if you need to cleanup above):
```
  kubectl delete -f ha-mongo-volume.yaml,elasticsearch-deployment.yaml,elasticsearch-service.yaml,graylog-service.yaml,graylog-deployment.yaml,ha-mongo-headless-service.yaml,ha-mongo-statefulset.yaml

Cleanup:

  kubectl delete pvc mongo-persistent-storage-mongo-0 mongo-persistent-storage-mongo-1 mongo-persistent-storage-mongo-2 -n graylog
```
```
After Creating Mongo Cluster
Connect to cluster and run rs.initiate()
```

### 2b. Run graylog cluster with single instance of mongo and EBS persistent volume
```
kubectl create -f ha-mongo-volume.yaml,ha-mongo-claim.yaml,elasticsearch-deployment.yaml,elasticsearch-service.yaml,graylog-service.yaml,graylog-deployment.yaml,mongo-deployment.yaml,mongo-service.yaml
```
### 3. Update AWS loadbalancer created by graylog service
```
kubectl get svc graylog -n graylog
```
Copy the aws loadbalancer created:
``` 
(ex: ac19231203981012938012312319203-129380129830.us-west-2.elb.amazonaws.com):
```

Edit the deployment of the graylog service and paste into WEB_ENDPOINT_URI
```
kubectl edit deployment graylog -n graylog

name: GRAYLOG_WEB_ENDPOINT_URI
value: http://ac19231203981012938012312319203-129380129830.us-west-2.elb.amazonaws.com/api
```
### 4. Forward port 9000 
```
 kubectl port-forward graylog-3226681439-97w36 9000:9000
```