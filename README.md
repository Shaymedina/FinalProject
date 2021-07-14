# FinalProject - JohnBryce - K8S
1.Deploy a pod named nginx-pod using the nginx:alpine image.
~~~
kubectl run nginx-pod --image=nginx:alpine
~~~
2.Deploy a messaging pos using the redis:alpine image with the labels set to 'tier=msg'.
Pod name: messaging
Image: redis:alpine
Labels: tier=msg
~~~
kubectl
~~~
3.Create a namespace named apx-998-yourname
~~~
kubectl
~~~
4.Get thelist of nodes in JSOM format and store it in a file at /tmp/nodes-yourname/
~~~
kubectl
~~~
5.Create a service messaging-service to expose the messaging application within the cluster on port 6379.
a. Use imperative commands - kubectl.
b. Service: messaging-service
c. Port: 6379
d. Type: Cluster-ip
e. Use the right labels
~~~
kube
~~~
6.Create a deployment named hr-web-app using image kodekloud/webapp-color with 2 replicas.
a. Name: hr-web-app
b. Image: kodekloud.webapp-color
c. Replicas: 2
~~~
bla
~~~
7.
# bla
blaa
~~~
kubectl apply -f 01-configmap.yaml
~~~

# bla
bla
~~~
kubectl apply -f 03-configmap-alerts.yaml
~~~

# bla
bla
~~~
kubectl apply -f 04-statefulset.yaml
~~~


