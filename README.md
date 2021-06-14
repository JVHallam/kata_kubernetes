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

## E) Create a secret yml:
* Create a secret using a yml
    * Have the key be "LOGINNAME"
    * Set the value to be "ADMIN"
    * Set the type to opaque
    * Call it details

* test:
    * Get the list of secrets, yours should be there
    * Describe the details secret - Should see the LOGINNAME key there

## E) Setup the environment variables
* add the secret to the deployment as an environment variable

* Add another environment variable:
    * call it "JAKE_TEST"
    * set the value to "yes"

* Tests:
    * Describe the deployment - You should see both are there
    * Check that the secret is an environment variable, from the http endpoint
    * check that the "JAKE_TEST" environment variable is set the response

## F) Teardown the setup and the contexts
* Delete the contexts
* Tear everything down from the environment


---

# 4) Setting up an influx db server
## Setup)
* Setup the kubernetes cluster
* You know the drill

## Write the deployment and service yaml)
* Create a deployment for an influxdb image
    * This kata was designed using image version 1.6.4

* Create a service that exposes port 8086

* Create a service that exposes that port to the internet

* test:
    * Open the port in the browser, you should get a 404 message

    * Install the influx tool
    * connect to that port on the server
    * influx -host 20.49.134.194 -port 8086
    * You should be able to connect

## Check databases don't yet persist:
* Connect to the influxdb instance
* create a database
    * create database katatest
* Restart the pods
* Reconnect
    * Confirm that your database is no longer there

## Persistent Volume Claim)
* Write the 
* Create the object
* Run it

* Test:
    * kubectl get pvc -> shows your pvc, is using the managed-premium type

## Mount the volume)
* Mount the volume in the container
    * Path: /var/lib/influxdb

* Give the container the pvc

* test:
    * Describe the deployment, check it's claims
    * az disk list -> Should show the new disk, with the matching size ( 2g as of writing this )

## Restart and recheck)
* Create a database 
    * connect with influx
    * create database test

* Restart the deployment
    * kubectl rollout restart deployment influxdb

* Test:
    * After adding a database
    * After restarting the deployment
    * It should still be there
