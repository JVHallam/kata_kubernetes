Everything below this line hasn't actually been verified as useful yet

# Logs, logging and getting those logs
* I'd like to be able to pull logs
* Get an image that also allows for a bit of logging
    * kubectl -n uatrc-mobius-simba logs testseeder-testseeder-55ddb8fcc9-zl52b --timestamps > testseederlogs_new.txt

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
