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

##Services

```
root@controlplane:~$ k get pods -o wide
NAME                   READY   STATUS    RESTARTS   AGE   IP              NODE           NOMINATED NODE   READINESS GATES
test-db9dbd4ff-4zhr7   1/1     Running   0          42s   192.168.0.114   controlplane   <none>           <none>
test-db9dbd4ff-bl2xp   1/1     Running   0          42s   192.168.1.68    node01         <none>           <none>
test-db9dbd4ff-fxjm7   1/1     Running   0          42s   192.168.1.233   node01         <none>           <none>
test-db9dbd4ff-gv26f   1/1     Running   0          42s   192.168.1.199   node01         <none>           <none>
test-db9dbd4ff-kh62n   1/1     Running   0          42s   192.168.0.94    controlplane   <none>           <none>
test-db9dbd4ff-mcjvz   1/1     Running   0          42s   192.168.0.112   controlplane   <none>           <none>
test-db9dbd4ff-mtsxq   1/1     Running   0          42s   192.168.1.162   node01         <none>           <none>
test-db9dbd4ff-pxplr   1/1     Running   0          42s   192.168.0.170   controlplane   <none>           <none>
test-db9dbd4ff-rkz98   1/1     Running   0          42s   192.168.1.230   node01         <none>           <none>
test-db9dbd4ff-zkft8   1/1     Running   0          42s   192.168.0.232   controlplane   <none>           <none>


root@controlplane:~$ curl 192.168.1.68
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```


```
First check what are the services are exposed
root@controlplane:~$ k get services   
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   10d


root@controlplane:~$ k get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   10d

root@controlplane:~$ k delete service test -n dev-workspace
service "test" deleted from dev-workspace namespace

##To expose the deployment as a service
root@controlplane:~$ k expose deployment test -n dev-workspace --port 80
service/test exposed

root@controlplane:~$ k get services -n dev-workspace
NAME   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
test   ClusterIP   10.97.233.115   <none>        80/TCP    12s

root@controlplane:~$ k get services -n dev-workspace -o wide
NAME   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE   SELECTOR
test   ClusterIP   10.97.233.115   <none>        80/TCP    18s   app=test

root@controlplane:~$ curl 10.97.233.115
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, nginx is successfully installed and working.
Further configuration is required for the web server, reverse proxy, 
API gateway, load balancer, content cache, or other features.</p>

<p>For online documentation and support please refer to
<a href="https://nginx.org/">nginx.org</a>.<br/>
To engage with the community please visit
<a href="https://community.nginx.org/">community.nginx.org</a>.<br/>
For enterprise grade support, professional services, additional 
security features and capabilities please refer to
<a href="https://f5.com/nginx">f5.com/nginx</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```
root@controlplane:~$ 

'''
