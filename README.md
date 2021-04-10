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
