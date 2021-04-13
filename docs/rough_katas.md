
# Stuff to look into:
* look into what's created in azure
* look into the scale sets

----------------------------------------------------------------------------------------------------------------------------

# Working with existing sets : Logs, logging and getting those logs
## Working with an existing set:

* I want to query the sets, using az
    * Get an object that contains just the name and resourceGroup, and the powerState
    * $cluster = az aks list --query "[*].{ name : name, resourceGroup : resourceGroup, powerState : powerState }" | convertFrom-json

* Activate the instance, if it's power state is "Stopped"
    * az aks start --name myAKSCluster --resource-group myResourceGroup

* get the credentials
    * az aks get-credentials --name myClusterName --resource-group myResourceGroupName

* I want to deploy an image, if there isn't already a service on the cluster
    * Just follow everything you know so far
       
* Setup and get some logs:
    * Query the endpoint
    * Get the logs
        * kubectl get pods --no-headers -o custom-columns=":metadata.name" <= outputs just the pods name, nothing else
        * kubectl get pods -> Get the pod name
        * kubectl logs podname --timestamps > logs.txt

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

* I want to setup a storage account
    * Use terraform, create a storage account, an a storage container
    * output the storage account name, and the storage container

* I want to push those logs to a storage account, specifically made for this task
    * az storage blob upload --container-name $sc --file "log.txt" --name "bloblog.txt" --account-name $sac

* Check it's there by getting it or going to the portal:
    * az storage blob download --container-name $sc --file "NEW NAME" --name "bloblog.txt" --account-name $sac

* I want to shut everything down, to prevent myself from incurring more costs
    * az aks stop --name $name --resource-group $rg
    * teardown the terraform rgs

----------------------------------------------------------------------------------------------------------------------------

# Using -f
* Setup a Deployment using this syntax / stuff

----------------------------------------------------------------------------------------------------------------------------

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
