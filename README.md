# Structure:
* All katas are below, with minimal / no clues on how to actually do the tasks
* Spoilers and instructions are all inside of the docs/kata_spoiler.md file

---

# 1) Terraform + Setup 
## A) Getting a cluster setup via terraform
* Create a directory for terraform
* Terraform:
    * setup a resource group
    * setup the cluster via terraform
        * output the resource group name + cluster name

* Test: go into the portal, and confirm you have
    * A resource group, containing just the kubernetes cluster
    * A network watcher
    * A resource group that contains network virtual appliances the load balancers, public ip, etc.

## B) Get the credentials
* Get the credential, using the terraform outputs
    * set the terraform outputs for the resourceGroupName 
    * Set powershell variables that contain the terraform outputs:
        * resourceGroupName
        * clusterName

    * Get the credentials

* Test: $ kubectl config view
    * the configs shown, match the values you expected, from the terraform outputs

## C) Getting the image running on your cluster
* Create a deployment, to your cluster, of the image
    * Images:
        * k8s.gcr.io/echoserver:1.4 -> Uses port 8080
        * docker.io/ealen/echo-server:latest -> Uses port 80

    * confirm that the deployment is there, using kubectl

    * confirm that the pods are running, using kubectl

* Create the port exposure service for the image
    * confirm that the service is there, using kubectl

* Create the loadbalancer service
    * confirm that the serices is there, using kubectl

* Test : Check that the thing as available from outside
    * Query the ip address of the loadbalancer, and the port of the service
    * You should get a readout on what's going on

    * Format the output to be nicer:
        * const value=document.querySelector("body > pre").innerText; var form=JSON.stringify(JSON.parse(value),undefined,2);document.querySelector("body > pre").innerText=form
        * var value=document.querySelector("body > pre").innerText; var form=JSON.stringify(JSON.parse(value),undefined,2);document.querySelector("body").innerHTML=`<pre type="json"> ${form} </pre>`;

## D) Tear it all down:
* Tear down everything in the cluster:
    * Tear down the services
    * Tear down the deployment

* Tear down everything in azure
    * Terraform my dude

* Teardown the local contexts from your config


---


#######################################################################
##
## This kata is on hold, and cannot be done, until the
## The problem of not being able to connect to the pods has been 
## Resolved.
##
#######################################################################

# 2) Working with existing cluster : Logs, logging and getting those logs
## A) Use AZ to get all the details
* ( Deploy the existing cluster, via the infrastructure repo, if not running )

* Use az to:
    * Get an object containing : resourceGroup, powerState, clusterName

* Activate the instance, if it's power state is "Stopped"

* get the credentials

* Check that you're using this context, in the config

* Deploy the echo image:
    * deploy the image
        * k8s.gcr.io/echoserver:1.4 -> Uses port 8080
    * setup the ports
    * Get a public ip address
    * Re-route it to port 80

## B) Get the pod details and the logs:
* Get the endpoint
* Query the endpoint, to generate the logs

* Get the name of the pod, 
    * no headers
    * the name only 
    * save it into a variable

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


# 3) Kata one, but you're only allowed to use yml files

## a) Get the cluster setup, you know the drill
* Invoke the terraform
* Get the config into kubectl

## b) Get the image deployed, using $kubectl apply -f file :
* name : echo
* image name : ealen/echo-server 
* Image : https://hub.docker.com/r/ealen/echo-server
* This image uses port 80

## C) Exposing the echo service, using -f:
* Expose the port 80

## D) Exposing the load balancer service, using -f:
* Expose the port, using the load balancer service

* Tests:
    * You can now query the ip address, from above, and get a response on port 80

## E) Setup the environment variables
* If using the ealen/echo-server image, the env variables will be displayed along with the response
* Create the secret
* add the secret to the deployment

* Also add another environment variable, called "JAKE_TEST" with a value of "Yes"

* Tests:
    * Validate that the secret exists, in the list of secrets
    * Check The secrets in the deployment, via the kubectl commands
    * Check that the secret is an environment variable, from the http endpoint

    * check that the "JAKE_TEST" environment variable is set the response

## F) Teardown the setup and the contexts
* Delete the contexts
* Tear everything down from the environment


---

# 4) Setting up an influx db server
