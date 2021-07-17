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
1. Create multi-pod with 2 containers:
~~~
kubectl apply -f 12-multipod.yml
~~~
2. Verify the pod:
~~~
kubectl describe pod/multi-pod
~~~
# Pod Design Questions:
1. Understand how to use Labels, Selectors and Annotations:
Label its a way to tag a pod or any resource in the kubernetes system, Example: im created pod with 5 containers, I want that three of them expose out, so i give them a label and expose the label name only, After that the "labeled" containers exposes out. Labels its like group.
Selector are Same as Labels but with Selectors we can attached pod to specific node.
Annotations are provide a extra information to metadata like image information, tool info', release ID.
2. Understand Deployments and how to perform rolling updates
Deployment its a way to deploy pods, replicas, rolling updates. Deployment controller changes the desires state to the actual one. In Addition, we can use Deployment to rollback updates, scale up pods, cleanup replicas and more.
We perform rolling updates by command:
~~~
kubectl set image deployment/nginx-deployment nginx=nginx:1.14 --record
~~~
3. Understand Deployments and how to perform rollbacks
We using rollback when the Deployment not stable or crash, we do it with the command:
~~~
kubectl set image deployment.v1.apps/nginx-deployment nginx=nginx:1.13 --record=true
~~~
In addition we can decide to rolling back to previous version by simple command:
~~~
kubectl rollout undo deployment.v1.apps/nginx-deployment
~~~
the output will be:
~~~
deployment.apps/nginx-deployment rolled back
~~~
4. Understand Jobs and CronJobs
Job its a way to create a pod with spesific mission, The job will not stop execution until the pod will terminate.
CronJob are the same thing but with schedule option to specific time.
How to create a CronJob?  we can perform CronJob with yaml and command. Example's:
~~~
kubectl create cronjob NAME --image=image --schedule='0/5 * * * ?' -- echo "Hello World"
~~~
Yaml Format: (add .spec)
~~~
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
~~~

Full code:
~~~
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
~~~

Verify and geting status about the cronjob we created:
~~~
kubectl get cronjob hello
~~~

5. Type the command for: Get pods with label information.
~~~
kubectl get pods -l information or kubectl get pods --show-labels
~~~
6. Create 5 nginx pods in which two of them is labeled env=prod and three of them is
labeled env=dev
~~~
kubectl run nginx1 --image=nginx --labels=env=dev
kubectl run nginx2 --image=nginx --labels=env=dev
kubectl run nginx3 --image=nginx --labels=env=dev
kubectl run nginx4 --image=nginx --labels=env=prod
kubectl run nginx5 --image=nginx --labels=env=prod
~~~

Verify all the pods are created with correct labels:
~~~
kubectl get pods --show-labels
~~~

Get the pods with label env=dev:
~~~
kubectl get pods -l env=dev
~~~
Get the pods with label env=dev and also output the labels:
~~~
kubectl get pods -l env=dev --show-labels
~~~

Get the pods with label env=prod:
~~~
kubectl get pods -l env=prod
~~~

Get the pods with label env=prod and also output the labels
~~~
kubectl get pods -l env=prod --show-labels
~~~

Get the pods with label env:
~~~
kubectl get pods -l env
~~~

Get the pods with labels env=dev and env=prod:
~~~
kubectl get pods -l env=dev,env=prod \didnt work
~~~

Get the pods with labels env=dev and env=prod and output the labels as well:
~~~
kubectl get pods -l env=dev,env=prod --show-labels
~~~

Change the label for one of the pod to env=uat and list all the pods to verify:
~~~
kubectl label pod/nginx1 env=uat --overwrite #Must to add --overwrite, if not we geting error.
kubectl get pods --show-labels
~~~

Remove the labels for the pods that we created now and verify all the labels are
removed:
~~~
kubectl label pod/nginx1 env-
kubectl label pod/nginx2 env-
kubectl label pod/nginx3 env-
kubectl label pod/nginx4 env-
kubectl label pod/nginx5 env-
kubectl get pods --show-labels
~~~

Let’s add the label app=nginx for all the pods and verify (using kubectl):
~~~
kubectl label pod/nginx1 app=nginx
kubectl label pod/nginx2 app=nginx
kubectl label pod/nginx3 app=nginx
kubectl label pod/nginx4 app=nginx
kubectl label pod/nginx5 app=nginx
kubectl get pods --show-labels
~~~

7. Get all the nodes with labels (if using minikube you would get only master node):
~~~
kubectl get nodes --show-labels
~~~

Label the worker node nodeName=nginxnode: (minikube):
~~~
kubectl label node minikube nodeName=nginxnode
~~~

8. Create a Pod that will be deployed on the worker node with the label
nodeName=nginxnode:
##### Code before add the nodeselector:
~~~
apiVersion: v1
kind: Pod
metadata:
creationTimestamp: null
labels:
run: nginx
name: nginx
spec:
containers:
- image: nginx
name: nginx
resources: {}
dnsPolicy: ClusterFirst
restartPolicy: Never
status: {}
~~~

Now we add the nodeSelector to yaml file and create the pod:
~~~
nodeSelector:
    nodeName: nginxnode
~~~

~~~
kubectl create -f 13-nodeselector.yml
~~~

Verify the pod that it is scheduled with the node selector on the right node… fix it if
it’s not behind scheduled:
~~~
kubectl describe pod nginx
~~~

Verify the pod nginx that we just created has this label: (run=nginx)
~~~
kubectl get pods/nginx --show-labels
~~~

# Deployments:
1. Create a deployment called webapp with image nginx with 5 replicas.
- Use the below command to create a yaml file.
~~~
kubectl create deploy webapp --image=nginx --dry-run -o yaml > webapp.yaml
~~~
- Edit it and add 5 replica’s.

After running the command above, We geting yaml file with 1 replica:
~~~
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: webapp
  name: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: webapp
    spec:
      containers:
      - image: nginx
        name: nginx
        resources: {}
status: {}
~~~

Lets change the replicas to =5 and create the deployment:
~~~
replicas: 5
kubectl apply -f webapp.yaml
~~~

2. Get the deployment rollout status:
~~~
kubectl rollout status deploy webapp
~~~
3. Get the replicaset that created with this deployment:
First, we will find the replicaset name:
~~~
kubectl describe deploy
~~~
We find replicaset name: webapp-5654c984c
~~~
kubectl get replicaset webapp-5654c984c
~~~

4. EXPORT the yaml of the replicaset and pods of this deployment:
YAML replicaset:
~~~
kubectl get replicaset webapp-5654c984c -o yaml
~~~

Pods YAML:
~~~
kubectl get pods -l app=webapp -o yaml
~~~

5. Delete the deployment you just created and watch all the pods are also being
deleted:
~~~
kubectl delete deploy webapp
~~~

Verify the pods deleted:
~~~
kubectl get pods -l app=webapp
~~~

6. Create a deployment of webapp with image nginx:1.17.1 with container port 80 and
verify the image version.
- kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o yaml >
webapp.yaml
- add the port section (80) and create the deployment

First we will run the command:
~~~
kubectl create deploy webapp --image=nginx:1.17.1 --dry-run -o yaml >
webapp2.yaml
~~~

Lets add port 80 and update the pod:
~~~
ports:
        - containerPort: 80
~~~
~~~
kubectl apply -f webapp2.yaml
~~~

Verify the image version:
~~~
kubectl describe deploy/webapp
~~~

7. Update the deployment with the image version 1.17.4 and verify:
~~~
kubectl set image deploy/webapp nginx=nginx:1.17.4 --record
kubectl describe deploy/webapp
~~~
8. Check the rollout history and make sure everything is ok after the update:
~~~
kubectl rollout history deploy/webapp
kubectl describe pods/webapp-9cf988f87-cmvrx #Running
~~~

9. Undo the deployment to the previous version 1.17.1 and verify Image has the
previous version:
~~~
kubectl rollout undo deploy/webapp
kubectl describe deploy/webapp
~~~

10. Update the deployment with the wrong image version 1.100 and verify something is
wrong with the deployment:
- Expect: kubectl get pods (ImagePullErr)
- Undo the deployment with the previous version and verify everything is Ok
- kubectl rollout history deploy webapp --revision=7
- Check the history of the specific revision of that deployment
- update the deployment with the image version latest and check the history
and verify nothing is going on

~~~
kubectl set image deploy/webapp nginx=nginx:1.100 #Get error.
kubectl get pods (ImagePullErr)
~~~
Undo the deploy and back to previous version:
~~~
kubectl rollout undo deploy/webapp
kubectl get pods
kubectl describe pods/webapp-b7889ff56-hxssb
~~~

Check the history of the specific revision of that deployment: (Revision=3 in our case)
~~~
kubectl rollout history deploy/webapp --revision=3 
~~~

Update the deployment with the image version latest and check the history
and verify nothing is going on: (Everything ok)
~~~
kubectl set image deploy/webapp nginx=nginx:latest --record
kubectl rollout history deploy/webapp
kubectl describe pods/webapp-6b9d4c7497-jv7k7
~~~

11. Apply the autoscaling to this deployment with minimum 10 and maximum 20 replicas
and target CPU of 85% and verify hpa is created and replicas are increased to 10
from 1

~~~
kubectl autoscale deploy/webapp --min=10 --max=20 --cpu-percent=85
kubectl describe hpa
kubectl describe deploy/webapp
~~~

12. Clean the cluster by deleting deployment and hpa you just created:
~~~
kubectl delete deploy/webapp
kubectl delete hpa/webapp
~~~

13. Create a job and make it run 10 times one after one (run > exit > run >exit ..) using the following configuration:
~~~
kubectl create job hello-job --image=busybox --dry-run -o yaml -- echo "Hello I am from job" > hello-job.yaml
~~~
- Add to the above job completions: 10 inside the yaml.

First, Lets run the command above:
~~~
apiVersion: batch/v1
kind: Job
metadata:
  creationTimestamp: null
  name: hello-job
spec:
  template:
    metadata:
      creationTimestamp: null
    spec:
      containers:
      - command:
        - echo
        - Hello I am from job
        image: busybox
        name: hello-job
        resources: {}
      restartPolicy: Never
status: {}
~~~

Now we will add the completions: 10:
~~~
spec:
  completions: 10
~~~

Create the Job:
~~~
kubectl apply -f hello-job.yaml
~~~
Verify:
~~~
kubectl get job -watch
~~~

# Config Map:
- Create a file called config.txt with two values key1=value1 and key2=value2 and
verify the file:
##### key1=value1
##### key2=value2

~~~
vim config.txt
~~~
Add the keys:
~~~
key1=value1
key2=value2
~~~
Verify:
~~~
cat config.txt
~~~
- Create a configmap named keyvalcfgmap and read data from the file config.txt and
verify that configmap is created correctly:

~~~
kubectl create configmap keyvalcfgmap --from-file=config.txt
~~~

- Create an nginx pod and load environment values from the above configmap
keyvalcfgmap and exec into the pod and verify the environment variables and delete
the pod.
- first run this command to save the pod yml
~~~
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml > nginx-shay-test.yml
~~~
We running the commad above and got yaml format:
~~~
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
~~~

 
We will set configmap in the yaml file:

~~~
envFrom:
- configMapRef:
    name: keyvalcfgmap
~~~

Updating the pod config:
~~~
kubectl apply -f nginx-shay-test.yml
~~~
Exec to the pod and verify the environment variables:
~~~
kubectl exec -it nginx -- env
~~~
Delete the pod:
~~~
kubectl delete pods/nginx
~~~
