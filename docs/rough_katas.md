
# Stuff to look into:
* look into what's created in azure
* look into the scale sets

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
