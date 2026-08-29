# kubernetes

- k run nginx --image=nginx --dry-run=client -o yaml

  **k run nginx** means you are asking kubernetes to give nameto the pod "nginx".\
  by giving **--image=nginx** you are saying which image to use "nginx".\
  by giving **--dry-run=client** means you are saying you don't want to run it on the server.\
  by giving **-o yaml**, you are generating the yaml output of the nginx pod.\

- k run nginx --name=nginx --dry-run=client -o yaml > nginx.yaml

  you can save the kick start file for pod in the yaml file which you can edit for future.\


- k apply -f nginx.yaml

  your yaml file will be applied and you can see your own pod

- k get pods

  to see all the pods running

- k get pod pod-name

  to see the details of the pod with the pod-name.\

- **watch -n 1 "k get pods"**

  to run the above commands in 1 sec interval
  
##  Deployment
-  **k create deployment my-dep --image=nginx --replicas=3**
  
  Create a deployment with the name "my-dep" with the image nginx and there will be 3 replicas.\

- **k get deployments**

  to get the deployments

- **k get deployment deployment-name**

  to get the details of the deployment

##Namespaces

- Get Namespaces

```
root@controlplane:~$ k get namespaces
NAME                 STATUS   AGE
cilium-secrets       Active   9d
default              Active   9d
kube-node-lease      Active   9d
kube-public          Active   9d
kube-system          Active   9d
local-path-storage   Active   9d
```
- Create Namespace

```
root@controlplane:~$ k create namespace dev-namespace 
namespace/dev-namespace created
root@controlplane:~$ k get namespaces
NAME                 STATUS   AGE
cilium-secrets       Active   9d
default              Active   9d
**dev-namespace**        Active   5s
kube-node-lease      Active   9d
kube-public          Active   9d
kube-system          Active   9d
local-path-storage   Active   9d

```
- creating the pod inside your created namespace

```
root@controlplane:~$ k run nginx --image=nginx -n dev-namespace 
pod/nginx created
```
- To see the newly created pods 

```
but when do you run k get pods, it will not return anything as, it shows the data from the default namespace
root@controlplane:~$ k get pods
No resources found in **default** namespace.

You need to specify the namespace to get the pods

root@controlplane:~$ k get pods -n **dev-namespace**
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          17s
```
- Setting Namespaces as default

```
root@controlplane:~$ k config set-context -h |less

root@controlplane:~$ k config set-context -n dev-namespace
error: you must specify a non-empty context name or --current

root@controlplane:~$ k config set-context -n dev-namespace --current
Context "kubernetes-admin@kubernetes" modified.

Once the dev-namespace is set as default namespace, now when do you run k get pods, it will your pod

root@controlplane:~$ k get pods
NAME    READY   STATUS    RESTARTS   AGE
nginx   1/1     Running   0          101s
  
```
