
# Stuff to look into:
* look into what's created in azure
* look into the scale sets

# Top priority for the future:
* Selectors
    * How do they work, when do i need them, etc.
    * Labels
    * MatchLabels

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

# Env variables
* $ENV:KUBECONFIG=./path/to/config

# Helm charts and yaml
* now that i've done the -f katas
* learn how the fuck helm does it's thing
* Is it of any use to me to know this shit?

# AKS and pipelines
* Setting up an aks cluster, using TF
* Running that via the pipeline
* Connecting to that from the pipeline
* Getting the logs
* Dumping those logs to a storage account

# Lifecycles, hooks and stuff
