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


