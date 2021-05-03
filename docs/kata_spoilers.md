j 1) Hello AKS

* The location of the .kube config file is:
    * ~/.kube/config

* The cluster details in terraform main.tf:
resource "azurerm_kubernetes_cluster" "cluster" {
  name                = module.naming.kubernetes_cluster.name_unique
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = "exampleaks1"

  default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_D2_v2"
  }

  identity {
    type = "SystemAssigned"
  }
}

* Get your image on the cluster
    * Get the credential, using the terraform outputs
        * terraform outputs
            * $clusterName = (terraform output cluster_name) -replace '"', ''
            * $resourceGroupName = (terraform output resource_group_name) -replace '"', ''

        * az aks get-credentials --resource-group $resourceGroupName --name $clusterName

    * Deploy the image
        * Deploy the image to the cluster
            * kubectl create deployment helloaks --image=k8s.gcr.io/echoserver:1.4
        * confirm that it's there
            * kubectl get deployments
            * kubectl get pods

        * Create the port forwarding service:
            * kubectl expose deployment helloaks --type=NodePort --port=8080
            * kubectl get services, to check the name and that it's there

        * Create the loadbalancer service
            * kubectl expose deployment helloaks --type=LoadBalancer --name=public --port=80 --targetPort=8080
            * the --name is the name of the service created, to expose the one from earlier

        * Test : Check that the thing as available from outside
            * kubectl get services publichelloaks
            * Grab the ip address
            * query : ip-address:8080 - Idk if it has to be 8080, but that's what the image requires
            * e.g. http://20.49.219.48:8080/, was the one that i just created

* Tear it all down:
    * Tear down the services, inside of the pods 
        * Teardown the loadbalancer service:
            * $ kubectl delete service public

        * Teardown the image's port exposure service
            * $ kubectl delete service helloaks

        * Teardown the deployment of the image
            * $ kubectl delete deployment helloaks

        * Teardown any pods that are somehow left over
            * $ kubectl delete pod <podname>

    * Tear down the azure resources, via terraform
        * $ terraform destroy --auto-approve
        * Check that it's no longer there in the portal

    * Tear down the context, from the context file
        * kubectl config view, shows:
            * clusters
            * contexts
            * users

        * kubectl config unset "contexts.$($clusterName)"
        * kubectl config unset "clusters.$($clusterName)"
        * $username = "users.clusterUser_$($resourceGroupName)_$($clusterName)"
        * kubectl config unset $username


--------------- --------------- --------------- --------------- --------------- --------------- --------------- --------------- ---------------


# 2) Working with existing cluster : Logs, logging and getting those logs

## Use AZ to get all the details
* ( Deploy the existing cluster, via the infrastructure repo, if not running )
* Get an object that contains just the name and resourceGroup, and the powerState
    * $cluster = az aks list --query "[*].{ name : name, resourceGroup : resourceGroup, powerState : powerState }" | convertFrom-json

* Activate the instance, if it's power state is "Stopped"
    * az aks start --name myAKSCluster --resource-group myResourceGroup

* get the credentials
    * az aks get-credentials --name myClusterName --resource-group myResourceGroupName

* Check that you're using this context
    * kubectl config view

* I want to deploy an image, if there isn't already a service on the cluster
    * Just follow everything you know so far
       
## Get the pod details and the logs:
* Get the endpoint
* Query the endpoint, to generate the logs
* Get the name of the pods, the names only, nothing else, save it into a variable
    * kubectl get pods --no-headers -o custom-columns=":metadata.name" 
* Get the logs
    * kubectl get pods $podname
    * kubectl logs $podname --timestamps > logs.txt

## Setup a storage account to save those logs into
* I want to setup a storage account
    * Use terraform, create an rg, a storage account and a storage container 
    * output the storage account name, and the storage container

* I want to push those logs to a storage account, specifically made for this task
    * az storage blob upload --container-name $sc --file "log.txt" --name "bloblog.txt" --account-name $sac

* Check it's there by getting it or going to the portal:
    * az storage blob download --container-name $sc --file "NEW NAME" --name "bloblog.txt" --account-name $sac

## Shutdown the cluster, to prevent incurring further costs:
* I want to shut everything down, to prevent myself from incurring more costs
    * Stop the cluster
        * az aks stop --name $name --resource-group $rg
    * Stop the scale sets
        * az vmss deallocate --name $ssname --resource-group $rg
    * teardown the storage account details

* Check that the aks instance isn't Going to cost you anything:
    * $cluster = az aks list --query "[0]"
    * $cluster.powerState -> Stopped
    * $cluster.agentPoolProfiles -> Each one's powerState is stopped


--------------- --------------- --------------- --------------- --------------- --------------- --------------- --------------- ---------------


3) Kata one, but you're only allowed to use yml files

a) Get the cluster setup, you know the drill

b) Get the image deployed:
    * name : echo
    * image : k8s.gcr.io/echoserver:1.4
    * Try this image too : https://hub.docker.com/r/ealen/echo-server

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: echo
  name: echo
  namespace: default
spec:
  selector:
    matchLabels:
      app: echo
  template:
    metadata:
      labels:
        app: echo
    spec:
      containers:
      - name: echoserver
        image: k8s.gcr.io/echoserver:1.4

C) Exposing the echo service

apiVersion: v1
kind: Service
metadata:
  labels:
    app: echo
  name: echo
  namespace: default
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080

D) Exposing the load balancer service:

apiVersion: v1
kind: Service
metadata:
  labels:
    app: echo
  name: echo-public
spec:
  ports:
  - nodePort: 31503
    port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: echo
  type: LoadBalancer


E) Environment variables

* Creating the secrets
apiVersion: v1
data:
  INFLUXDB_DATABASE: "test"
  INFLUXDB_DB: "test"
  INFLUXDB_ADMIN_ENABLED: "true"
  INFLUXDB_ADMIN_PASSWORD: "password"
  INFLUXDB_USER: "tgsluser"
  INFLUXDB_USER_PASSWORD: "password"
kind: Secret
metadata:
  name: influxdb-creds
  namespace: default
type: Opaque

* Adding them to the deployment:
spec:
  containers:
  - envFrom:
    - secretRef:
        name: influxdb-creds
    image: docker.io/influxdb:1.6.4
    imagePullPolicy: IfNotPresent
    name: influxdb
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File

NOTE : Try this image instead
https://hub.docker.com/r/ealen/echo-server

F) Teardown the setup and the contexts
    * Delete the contexts
    * Tear everything down from the environment

z) Shit to add to anki

kubectl get deployment name -o yaml : Effect?
Output a yaml file, describing a deployment : Syntax? 

--------------- --------------- --------------- --------------- --------------- --------------- --------------- --------------- ---------------

4) Setting up an influxdb, with PVC and -f shit

a) setup:
* Create your AKS cluster
    * infrastructure repo - run setup

* deploy the influx image, using -f this will make shit easier later
    * kubectl create deployment influxdb --image=docker.io/influxdb:1.6.4
    * kubectl expose deployment influxdb --port=8086 --target-port=8086 --protocol=TCP --type=ClusterIP
    * kubectl expose deployment influxdb --type=LoadBalancer --name="influxdb-public" --port=8086

* Create the deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: echo
  name: echo
  namespace: default
spec:
  selector:
    matchLabels:
      app: echo
  template:
    metadata:
      labels:
        app: echo
    spec:
      containers:
      - name: echoserver
        image: k8s.gcr.io/echoserver:1.4

* Expose the ports
apiVersion: v1
kind: Service
metadata:
  labels:
    app: echo
  name: echo
  namespace: default
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080

* Setup the loadbalancer
apiVersion: v1
kind: Service
metadata:
  labels:
    app: echo
  name: echo-public
spec:
  ports:
  - nodePort: 31503
    port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    app: echo
  type: LoadBalancer

* install the influxdb cli tool
    * choco install influxdb
    * binary is called "influx"
    * stored in c:\influxdata

* Connect to the image:
    * Query the endpoint, in a browser, port and all, should get a 404
    * influx -host 20.49.134.194 -port 8086

b) Create the persistent volume claims:

* PVC : Persistent volume claim
* kubectl create -f pvc.yml
* kubectl get pvc

* Check the storage classes
    * kubectl get sc

* this pvc comes from azure
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium ( standard should probably be used here )
  resources:
    requests:
      storage: 2Gi

* How to check if the pvc went through:
    * kubectl get pvc -> status is approved
    * az disk list
    * If the claim went through, aks would have a matching disk of the same size

* mount and make a claim:
spec:
  template:
    spec:
      containers:
        volumeMounts:
        - mountPath: /var/lib/influxdb
          name: volume

      volumes:
      - name: volume
        persistentVolumeClaim:
          claimName: azure-managed-disk

* Finished deployment yml:
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "3"
  creationTimestamp: null
  generation: 1
  labels:
    app: influxdb
    project: twittergraph
  name: influxdb
  selfLink: /apis/extensions/v1beta1/namespaces/twittergraph/deployments/influxdb
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: influxdb
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: influxdb
    spec:
      containers:
      - envFrom:
        - secretRef:
            name: influxdb-creds
        image: docker.io/influxdb:1.6.4
        imagePullPolicy: IfNotPresent
        name: influxdb
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
        volumeMounts:
        - mountPath: /var/lib/influxdb
          name: var-lib-influxdb
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      volumes:
      - name: var-lib-influxdb
        persistentVolumeClaim:
          claimName: influxdb
status: {}

D) Check the setup:
    connect using influx 

    create a database

    put some data in

    kubectl rollout restart deployment influxdb

    restarts a service and all pods attached to it

    Reconnect and check the claim

----

/Setting environment variables

apiVersion: v1
data:
  INFLUXDB_DATABASE: "test"
  INFLUXDB_DB: "test"
  INFLUXDB_ADMIN_ENABLED: "true"
  INFLUXDB_ADMIN_PASSWORD: "password"
  INFLUXDB_USER: "tgsluser"
  INFLUXDB_USER_PASSWORD: "password"
kind: Secret
metadata:
  name: influxdb-creds
  namespace: default
type: Opaque

apiVersion: v1
data:
  INFLUXDB_DATABASE: 
  INFLUXDB_HOST: 
  INFLUXDB_PASSWORD: 
  INFLUXDB_USERNAME: 
kind: Secret
metadata:
  name: influxdb-creds
  namespace: default
type: Opaque

* kubectl get secrets
* kubectl describe secrets influxdb-creds

Either is good, as the above cmd one can be used with keyvault.

* edit the deployment
    * Get the influxdb-creds put into the image
    * Just put the name bit, into the existing containers section, above the existing name
        Spec:
          containers:
  e       - name: influxdb
            image: docker.io/influxdb:1.6.4
            envFrom:
            - secretRef:
                name: influxdb-creds

* ALTERNITAVLEY This can be an object file, kubectl apply -f filename.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: influxdb
  name: influxdb
  namespace: default
spec:
  selector:
    matchLabels:
      app: influxdb
  template:
    metadata:
      labels:
        app: influxdb
    spec:
      containers:
      - envFrom:
        - secretRef:
            name: influxdb-creds
        image: docker.io/influxdb:1.6.4
        name: influxdb




kubectl describe deployment influxdb
* you should now see:
    * Environment Variables from:
      influxdb-creds  Secret  Optional: false

expose the deployment
https://opensource.com/article/19/2/deploy-influxdb-grafana-kubernetes
