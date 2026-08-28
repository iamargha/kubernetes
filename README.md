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
##  Deployment
-  **k create deployment my-dep --image=nginx --replicas=3**
  
  Create a deployment with the name "my-dep" with the image nginx and there will be 3 replicas.\

- **k get deployments**

  to get the deployments

- **k get deployment deployment-name**

  to get the details of the deployment
  
  
