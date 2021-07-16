# FinalProject - JohnBryce - K8S
# 1
Deploy a pod named nginx-pod using the nginx:alpine image.
~~~
kubectl run nginx-pod --image=nginx:alpine
~~~
# 2
Deploy a messaging pos using the redis:alpine image with the labels set to 'tier=msg'.
- Pod name: messaging
- Image: redis:alpine
- Labels: tier=msg
~~~
kubectl create -f 2-redispod.yml
~~~
# 3
Create a namespace named apx-998-yourname
~~~
kubectl create namespace apx-998-shay
~~~
# 4 
Get list of nodes in JSOM format and store it in a file at /tmp/nodes-yourname/
~~~
kubectl get nodes -o=jsonpath='{.items[0].metadata.name}' > /tmp/nodes-shay/nodes.json
~~~
# 5
Create a service messaging-service to expose the messaging application within the cluster on port 6379.
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
# 6
Create a deployment named hr-web-app using image kodekloud/webapp-color with 2 replicas.
- Name: hr-web-app
- Image: kodekloud.webapp-color
- Replicas: 2
~~~
kubectl apply -f 4-deployment-hr.yml
~~~
# 7
Create a static pod named static-busybox on the msater node that uses the busybox image and the command sleep 1000
1. Locate the kubelet config file by this command:
~~~
ps -ef | grep kubelet | grep config
~~~
2. We will open the config file and find the static pod folder path:
~~~
cat /var/lib/kubelet/config.yaml | grep "static"
~~~
3. We find the static pod path:
~~~
staticPodPath: /etc/kubernetes/manifests
~~~
4. We create a YAML file that contain the pod spec in the path above^^:
~~~
cat <<EOF >/etc/kubernetes/manifests/static-busybox.yaml
apiVersion: v1
kind: Pod
metadata:
  name: static-busybox
spec:
  containers:
  - name: static-busybox
    image: busybox
    command:
        - "sleep"
        - "1000"
EOF
~~~
5. We will restart the kubelet service and verify the POD created:
~~~
sudo systemctl daemon-reload
sudo systemctl restart kubelet
kubectl get pods
~~~
# 8
Create a pod in the finance-yourname namespace named temp-bus with the image redis:alpine
- Name: temp-bus
- Image Name: redis:alpine
1. First of all we need to create a namespace:
~~~
kubectl create namespace finance-shay
~~~
2. Now we will create the pod in yaml format:
~~~
kubectl apply -f 6-podnamespace.yml
~~~
3. Verify that the pod exist:
~~~
kubectl get pods --namespace=finance-shay
~~~
# 9
Create a Persistent Volume with the given specification:
- Volume Name: pv-analytics
- Storage: 100Mi
- Access modes: ReadWriteMany
- Host Path: /pv/data-analytics
1. PV creation:
~~~
kubectl apply -f 7-persistentvolume
~~~
2. View Information:
~~~
kubectl get pv pv-analytics
~~~
# 10
Create a Pod called redis-storage-yourname with image: redis:alpine with a Volume of type emptyDir that lasts for the life of the Pod:
- Pod named 'redis-storage-yourname'
- Pod 'redis-storage-yourname' uses Volume type of emptyDir
- Pod 'redis-storage-yourname' uses volumeMount with mountPath = /data/redis
1. Create the Pod:
~~~
kubectl apply -f 8-storage.yml
~~~
2. Verify that the container in the Pod is running:
~~~
kubectl get pod redis-storage-shay
~~~
# 11
Create this pod and attached it a persistent volume called pv-1.
- Make sure the PV mountPath is hostbase : /data
##### Code-Before PV-1:
~~~
apiVersion: v1
kind: Pod
metadata:
creationTimestamp: null
labels:
run: use-pv
name: use-pvspec-yourname
containers:
- image: nginx
name: use-pv
resources: {}
dnsPolicy: ClusterFirst
restartPolicy: Always
status: {}
~~~
1. Create PV-1:
~~~
kubectl apply -f 11-pv-1.yml
~~~
2. Create PVC:
~~~
kubectl apply -f 9-pvc.yml
~~~
3. Create Pod:
~~~
kubectl apply -f 10-storage3.yml
~~~
4. Verify that container in the Pod is running and uses the PVC:
~~~
kubectl get pod use-pvspec-shay
kubectl describe pod/use-pvspec-shay
~~~
# 12
Create a new deployment called nginx-deploy, with image nginx:1.16 and 1 replica.
Record the version. Next upgrade the deployment to version 1.17 using rolling
update. Make sure that the version upgrade is recorded in the resource annotation.
- Deployment : nginx-deploy. Image: nginx:1.16
- Image: nginx:1.16
- Task: Upgrade the version of the deployment to 1:17
- Task: Record the changes for the image upgrade
1. Create Deployment with nginx:1.16 and 1 replica:
~~~
kubectl create deployment nginx-deploy --image=nginx:1.16 --replicas=1
~~~
2. Verify the deployment running:
~~~
kubectl get deployment
~~~
3. Upgrade the deployment to nginx:1.17 with --record:
~~~
kubectl set image deployment nginx-deploy nginx=nginx:1.17 --record
~~~
4. Verify the changes with rollout history command:
~~~
kubectl rollout history deployment nginx-deploy
kubectl describe deploy/nginx-deploy
~~~
# 13
Create an nginx pod called nginx-resolver using image nginx, expose it internally
with a service called nginx-resolver-service. Test that you are able to look up the service and pod names from within the cluster. Use the image: busybox:1.28 for dnslookup. Record results in /root/nginx-yourname.svc and /root/nginx-yourname.pod.
1. Create a nginx pod called nginx-resolver:
~~~
kubectl run nginx-resolver --image=nginx
~~~
2. Create a SVC called nginx-resolver-service.
~~~
kubectl expose pod/nginx-resolver --port=80 --type=ClusterIP --name=nginx-resolver-service
~~~
3. Verify that we see the pod in the service:
~~~
kubectl describe svc/nginx-resolver-service
~~~
4. Create pod named dns with image busybox:1.28 that record the dns route's and save the results in /root/nginx-yourname.svc and /root/nginx-yourname.pod
~~~
kubectl run dns --image=busybox:1.28 --rm -it -- sleep 3600 -- nslookup nginx-resolver-service > /root/nginx-shay.svc
~~~
# 14
Create a static pod on node01 called nginx-critical with image nginx. Create this pod
on node01 and make sure that it is recreated/restarted automatically in case of a
failure.
1. Create a static pod called nginx-critical with nginx image as we did before:
~~~
ssh node01
~~~

~~~
cat <<EOF >/etc/kubernetes/manifests/nginx-critical.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-critical
spec:
  containers:
  - name: nginx-critical
    image: nginx
EOF
~~~
2. Try to delete the POD:
~~~
kubectl delete pod nginx-critical
~~~
3. We can see that the Pod is still running:
~~~
kubectl get pods
~~~
# 15
Create a pod called multi-pod with two containers.
- Container 1- name: alpha, image: nginx
- Container 2- beta, image: busybox, command sleep 4800.
-  Environment Variables:
i. container 1:
ii. name: alpha
iii. Container 2:
iv. name: beta



