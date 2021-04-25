
# Stuff to look into:
* look into what's created in azure
* look into the scale sets

----------------------------------------------------------------------------------------------------------------------------
# This one:
* Deploying an influxdb image
* Everything here has to be IAC, us the -f stuff as much as possible
    * This can be practises with env variables and any old setup
* Setting up a Persistent volume
* Setting the password and stuff via environment variables

https://opensource.com/article/19/2/deploy-influxdb-grafana-kubernetes

choco install influxdb
$influx = "C:\influxdata\influxdb-1.8.4-1\influx.exe"
& $influx -host "20.49.134.194:8081"

$dep_name="influxdb"
kubectl create deployment $dep_name --image=docker.io/influxdb:1.6.4

# The secrets are used as the login details
kubectl create secret generic influxdb-creds --from-literal=INFLUXDB_DATABASE=twittergraph --from-literal=INFLUXDB_USERNAME=root --from-literal=INFLUXDB_PASSWORD=root --from-literal=INFLUXDB_HOST=influxdb
kubectl get secrets
kubectl describe secrets influxdb-creds

# Then share the new details, with the pod, via env variables in the pod
kubectl edit deployment influxdb

Spec:
  containers:
  - name: influxdb
    image: docker.io/influxdb:1.6.4
    envFrom:
    - secretRef:
        name: influxdb-creds

Just put the name bit, into the existing containers section

kubectl describe deployment influxdb
* you should now see:
    * Environment Variables from:
      influxdb-creds  Secret  Optional: false

----

* Permanent data stores : A persistent volume ( prevents data loss upon pod recycling )
* PVC : Persistent volume claim
* kubectl create -f pvc.yml
* kubectl get pvc

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  labels:
    app: influxdb
    project: twittergraph
  name: influxdb
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi


come back to this section later, to finish this off, there's stuff about mounting the pvc

----

expose the deployment

kubectl expose deployment influxdb --port=8086 --target-port=8086 --protocol=TCP --type=ClusterIP

--- 

This is where i take over from the tutorial

$dep_name="influxdb"
$port=8086
kubectl expose deployment $dep_name --type=LoadBalancer --name="$($dep_name)-public" --port=$port

influx -host <ip address>

----------------------------------------------------------------------------------------------------------------------------

# Using -f
* Setup a Deployment using this syntax / stuff

----------------------------------------------------------------------------------------------------------------------------

==== * AKS ( TO DO! ) ====
* Monitoring logging and debugging

* Nodes
    * kubectl get nodes -o yaml | egrep '\sname:|cpu:|memory:'

* Starting a debug session
    * kubectl debug node/mynode -it --image=ubuntu

==== ==== ==== ==== ==== ==== ==== ==== ==== ==== ==== ====



==== * AKS: ( TO DO! ) ====
    * Attempt to auto scale / add new nodes
    * I don't know how any of this would be done, K6?
    * Try and get something in here, with regards to node work


* Scale up to 3 replica pods
    * kubectl scale deployment hostnames --replicas=3

==== ==== ==== ==== ==== ==== ==== ==== ==== ==== ==== ====

kubectl logs helloaks-5f6746c8b6-cfvxr --timestamps > logs.txt
az aks list 

kubectl get nodes

kubectl describe nodes

# Stuff:
* how gab got the test seeder logs
kubectl -n uatrc-mobius-simba logs testseeder-testseeder-55ddb8fcc9-zl52b --timestamps > testseederlogs_new.txt

# Hello world
* kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
    * kubectl create deployment name --image=path

# Inspecting contexts
* Get list of contexts
* Switch context
* manage many clusters

# Getting logs to persist
* Getting the logs
* have them persist via redeployments

# Helm charts and yaml

# AKS and pipelines
* Setting up an aks cluster, using TF
* Running that via the pipeline
* Connecting to that from the pipeline
* Getting the logs
* Dumping those logs to a storage account

# Lifecycles, hooks and stuff
