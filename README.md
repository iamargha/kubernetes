# kubernetes

- k run nginx --name=nginx --dry-run=client -o yaml

  **k run nginx** means you are asking kubernetes to run nginx image.\
  by giving **--name=nginx** you are giving the name of the pod nginx.\
  by giving **--dry-run=client** means you are saying you don't want to run it on the server.\
  by giving **-o yaml**, you are generating the yaml output of the nginx pod.\

- k run nginx --name=nginx --dry-run=client -o yaml > nginx.yaml

  you can save the kick start file for pod in the yaml file which you can edit for future.\


- k apply -f nginx.yaml

  your yaml file will be applied and you can see your own pod

- k get pods

  to see all the pods running
  
  
