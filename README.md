# FinalProject - JohnBryce - K8S
1.Deploy a pod named nginx-pod using the nginx:alpine image.
~~~
kubectl run nginx-pod --image=nginx:alpine
~~~
2.Deploy a messaging pos using the redis:alpine image with the labels set to 'tier=msg'.
- Pod name: messaging
- Image: redis:alpine
- Labels: tier=msg
~~~
kubectl create -f 2-redispod.yml
~~~
3.Create a namespace named apx-998-yourname
~~~
kubectl create namespace apx-998-shay
~~~
4.Get list of nodes in JSOM format and store it in a file at /tmp/nodes-yourname/
~~~
kubectl get nodes -o=jsonpath='{.items[0].metadata.name}' > /tmp/nodes-shay/nodes.json
~~~
5.Create a service messaging-service to expose the messaging application within the cluster on port 6379.
- Use imperative commands - kubectl.
- Service: messaging-service
- Port: 6379
- Type: Cluster-ip
- Use the right labels
~~~
kubectl apply -f 3-service.yml
~~~
Or:
~~~
kubectl create service clusterip messaging-service --tcp=6379
~~~
6.Create a deployment named hr-web-app using image kodekloud/webapp-color with 2 replicas.
- Name: hr-web-app
- Image: kodekloud.webapp-color
- Replicas: 2
~~~
kubectl apply -f 4-deployment-hr.yml
~~~
7.Create a static pod named static-busybox on the msater node that uses the busybox image and the command sleep 1000
~~~
kubectl apply -f 5-staticpod.yml
~~~
8.Create a pod in the finance-yourname namespace named temp-bus with the image redis:alpine
- Name: temp-bus
- Image Name: redis:alpine
First of all we need to create a namespace:
~~~
kubectl create namespace finance-shay
~~~
Now we will create the pod in yaml format:
~~~
kubectl apply -f 6-podnamespace.yml
~~~
Verify that the pod exist:
~~~
kubectl get pods --namespace=finance-shay
~~~
9.Create a Persistent Volume with the given specification:
- Volume Name: pv-analytics
- Storage: 100Mi
- Access modes: ReadWriteMany
- Host Path: /pv/data-analytics
PV creation:
~~~
kubectl apply -f 7-persistentvolume
~~~
View Information:
~~~
kubectl get pv pv-analytics
~~~
10.Create a Pod called redis-storage-yourname with image: redis:alpine with a Volume of type emptyDir that lasts for the life of the Pod:
- Pod named 'redis-storage-yourname'
- Pod 'redis-storage-yourname' uses Volume type of emptyDir
- Pod 'redis-storage-yourname' uses volumeMount with mountPath = /data/redis
Create the Pod:
~~~
kubectl apply -f 8-storage.yml
~~~
Verify that the container in the Pod is running:
~~~
kubectl get pod redis-storage-shay
~~~

