# Before the presentation

- Start a 2 node cluster:
  minikube start --nodes 2 -p multinode

- Stop the control plane node from receiving any objects
  kubectl cordon multinode

- Create a namespace for the 3rd scenario. This application takes longer to deploy and should be deployed in anticipation.
  - kubectl create namespace scenario3

- cd scenario3

- kubectl create -f . --namespace=scenario3

# 1st scenario (slides should be referecend on all scenarios) 
    
  - Create a namespace called zion: 
  kubectl create namespace zion

  - Create a Resource Quota:
  kubectl apply -f zion-quota.yaml --namespace=zion

  - Create a Pod called neo-pod:
  kubectl apply -f neo-pod.yaml --namespace=zion

  - Verify that the Pod's Container is running:
  kubectl get pod neo-pod --namespace=zion

  - Check the Resource Quota:
  kubectl get resourcequota zion-quota --namespace=zion --output=yaml

  - Attempt to create a second Pod called trinity-pod:
  kubectl apply -f trinity-pod.yaml --namespace=zion

  - Analyse the output and suggest a solution: 1) Diminish Pod's resource usage 2) Increase Quota's Resource Availability

  - Check the Resource Quota:
  kubectl get resourcequota zion-quota --namespace=zion --output=yaml

  - clean up my namespace
  kubectl delete namespace zion

# 2nd scenario

  - kubectl apply -f kuard.yaml

  - kubectl get pods

  - kubectl describe pod kuard

  - kubectl delete pod kuard

  - Analyse the events and suggest a solution.

  - Make the necessary changes.

  - kubectl apply -f kuard.yaml

  - kubectl get pods

  - kubectl delete pod kuard

# 3rd scenario

  - IF you want to include an error in the app for testing purposes. Otherwhise, start from step 2 of this scenario.
  
    - If you change the source code in the running container, it may make the pod/app behave strangely, but as soon as you pull the image again for another deployment, it will be right again. Best thing to do should be getting the source code and building a new image from the changes you make.

    - Also, this app is from Kodekloud and they made an edit to the worker-app that made it crash for all users. The goal of this scenario is to debug and fix this issue.

      - Deploy application: kubectl create -f .  
      To get Pods name: kubectl get all

      - Get a shell to the running pod's container: kubectl exec -it pods-name -- sh

      - move to correct dir:
      cd /app/templates

      - vi into the index.html to cast an error:
      
      - vi index.html

      - change the value from 'a' to 'c':
      
        ```
        <button id="a" type="submit" name="vote" class="a" value="c">{{option_a}}</button>
        ```

      - Leave the shell:
        exit

- This app was deployed in the beginning, so check the application:
kubectl get pods,svc --namespace=scenario3

- get voting and result services url:
  minikube profile list
  ip+nodeport

- cast a vote and check the result page
  it won't work

- kubectl get pods --namespace=scenario3

- kubectl describe pod worker-pod-name --namespace=scenario3

- the answer to the problem is in this step:
  kubectl logs worker-pods-name --namespace=scenario3

- kubectl logs postgres-deploy-6f787b796b-5c9qs

- make the necessary changes

- kubectl delete deployment postgres-deploy --namespace=scenario3
  kubectl delete deployment worker-app-deploy --namespace=scenario3

- kubectl apply -f postgres-deploy.yaml --namespace=scenario3
  kubectl apply -f worker-app-deploy.yaml --namespace=scenario3

- kubectl get pods --namespace=scenario3

- kubectl logs worker-app-deploy-799b5fb489-wnbhm --namespace=scenario3

- test the app on the browser

- clean up:
  kubectl delete namespace scenario3

# 4th scenario

  - from the screen that is not being shared:
  kubectl taint nodes multinode-m02 key1=value1:NoSchedule
  kubectl taint nodes multinode-m02 key1=value1:NoExecute
  kubectl taint nodes multinode-m02 key2=value2:NoSchedule

  - kubectl apply -f kuard.yaml

  - kubectl get pods

  - kubectl describe pod kuard
  Why doesn't it work?

  - kubectl describe node multinode
  kubectl describe node multinode-m02

  - add the 'solutions'

  - kubectl apply -f kuard.yaml

  - kubectl get pods

  - kubectl delete pod kuard

  - clean up: kubectl taint nodes multinode-m02 key1=value1:NoSchedule- kubectl taint nodes multinode-m02 key1=value1:NoExecute- kubectl taint nodes multinode-m02 key2=value2:NoSchedule- kubectl uncordon multinode

# scenario 5

  - kubectl create namespace kuard-test

  - kubectl create -f . --namespace=kuard-test

  - kubectl get pods,svc --namespace=kuard-test

  - minikube profile list

  - access: ip:32321/

  - check if it has endpoints:
    kubectl get endpoints kuard --namespace=kuard-test

  - check the yaml file

  - make the necessary corrections

  - kubectl delete svc kuard --namespace=kuard-test
    kubectl delete pod kuard --namespace=kuard-test

  - kubectl create -f . --namespace=kuard-test

  - kubectl get endpoints kuard --namespace=kuard-test

  - check the app on the browser

  - kubectl delete namespace kuard-test

# scenario 6

  - kubectl create namespace scenario6

  - go to the folder and:
    kubectl create -f . --namespace=scenario6

  - to check pod's health:
    kubectl get pods,svc --namespace=scenario6

  - check if the service has endpoints, it means the svc is targetting the relevant pods:
    kubectl get endpoints my-svc --namespace=scenario6

  - Deploy a container with curl application in the scenario6 namespace to check if the SVC responds:
    kubectl run curl --image=radial/busyboxplus:curl -n scenario6 -i --tty --rm

  - curl my-svc

  - exit

  - Now, check on another namespace or the default one:
    kubectl run curl --image=radial/busyboxplus:curl -i --tty --rm

  - Suppose your colleague said he was testing with this command: 
    curl my-svc

  - Say what the correct command is and test it

  - exit

  - clean up: kubectl delete namespace scenario6

# scenario 7

## This pod is only created if the svc and mydb was created beforehand. Its purpose is to demonstrate how Init Containers work.

  - kubectl create namespace scenario7

  - kubectl create -f kuard.yaml --namespace=scenario7

  - kubectl get pods --namespace=scenario7

  - kubectl describe pod kuard --namespace=scenario7

  - kubectl logs kuard -c init-mydb --namespace=scenario7

  - kubectl create -f mydb.yaml --namespace=scenario7

  - kubectl get pods --namespace=scenario7

  - kubectl logs kuard -c init-mydb --namespace=scenario7

  - kubectl delete namespace scenario7

# scenario 8

  ## This pod has a sidecar container that shares the same volume and shows the pods' logs. Instead of getting a shell into the pod, you can just kubectl logs the sidecar.

  - kubectl create -f .

  - kubectl get pods

  - kubectl describe pod webserver

  - kubectl logs webserver sidecar-container

  - kubectl delete pods --all

# Operators

  - Kubernetes operators enable you to easily extend Kubernetes capabilities for specific software and use cases. For example, database operators let a Kubernetes user securely deploy and manage specific databases. 

  - Operators use controllers that monitor Kubernetes objects. These controllers are slightly different from regular Kubernetes controllers, because they track custom objects, known as custom resource definitions (CRDs).


