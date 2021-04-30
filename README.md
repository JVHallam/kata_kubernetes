# Structure:
* All katas are below, with minimal / no clues on how to actually do the tasks
* Spoilers and instructions are all inside of the docs/kata_spoiler.md file

---

# 1) Hello AKS!
## Getting a cluster setup via terraform
* Terraform:
    * setup a resource group
    * setup the cluster via terraform
        * output the resource group name
        * output the cluster name
        * try and output the names into powershell variables

* Test: go into the portal, and confirm you have
        * A resource group, containing just the kubernetes cluster
        * A network watcher
        * A resource group that contains network virtual appliances the load balancers, public ip, etc.

## Get the credentials
* Get the credential, using the terraform outputs
    * set the terraform outputs for the resourceGroupName 
    * Set powershell variables that contain the terraform outputs:
        * resourceGroupName
        * clusterName

    * Get the credentials

* Test: $ kubectl config view
    * the configs shown, match the values you expected, from the terraform outputs

## Getting the image running on your cluster
* Create a deployment, to your cluster, of the image
    * confirm that the deployment is there, using kubectl
    * confirm that the pods are running, using kubectl

* Create the port exposure service for the image
    * confirm that the service is there, using kubectl

* Create the loadbalancer service
    * confirm that the serices is there, using kubectl

* Test : Check that the thing as available from outside
    * Query the ip address of the loadbalancer, and the port of the service
    * You should get a readout on what's going on

## Tear it all down:
* Tear down everything in the cluster:
    * Tear down the services
    * Tear down the deployment

* Tear down everything in azure
    * Terraform my dude

* Teardown the local contexts from your config


---


# 2) Working with existing cluster : Logs, logging and getting those logs
## Use AZ to get all the details
* ( Deploy the existing cluster, via the infrastructure repo, if not running )
* Get an object that contains just the name and resourceGroup, and the powerState
* Activate the instance, if it's power state is "Stopped"
* get the credentials
* Check that you're using this context, in the config
* I want to deploy an image, if there isn't already a service on the cluster

## Get the pod details and the logs:
* Get the endpoint
* Query the endpoint, to generate the logs
* Get the name of the pods, the names only, nothing else, save it into a variable
* Get the logs

## Setup a storage account to save those logs into
* Setup a storage container, via terraform
* Upload the logs to that storage account, via the az tool
* Check it's there by downloading it via the az tool

## Shutdown the cluster, to prevent incurring further costs:
* I want to shut everything down, to prevent myself from incurring more costs
    * Stop the cluster
    * teardown the storage account details

* Check that the aks instance isn't Going to cost you anything:
    * Check the power state of the cluster
    * Check the power state of the agents in the pools


---


3) Kata one, but you're only allowed to use yml files

a) Get the cluster setup, you know the drill
    * Invoke the terraform
    * Get the config into kubectl

b) Get the image deployed, using $kubectl apply -f file :
    * name : echo
    * image : k8s.gcr.io/echoserver:1.4

C) Exposing the echo service, using -f:
    * Expose the port 8080

D) Exposing the load balancer service, using -f:
    * Expose the port, using the load balancer service

E) Teardown the setup and the contexts
    * Delete the contexts
    * Tear everything down from the environment

E) Could:
Use a different image

https://hub.docker.com/r/ealen/echo-server

Set environment variables ( Everything at the bottom of the file )

pass them into the image

Check that they're getting set via the thing

---


