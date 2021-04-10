# To do:
* Make this into a git repo
* Look into aks
* Run a pipeline, that when ran, sets up and recycles aks, then gets the log files
* Look into lifecycles
* Look into helm charts
* Kubernetes load balancers

# Get a cluster running
* minikube start

# Interact with the cluster
* kubectl get po -A
    * wtf does this do?

# Setup a deployment
* kubectl create deployment hello-minikube --image=k8s.gcr.io/echoserver:1.4
    * kubectl create deployment name --image=path

* kubectl expose deployment helloaks --type=NodePort --port=8080
    * wtf is 8080 here?

* kubectl get services hello-minikube

* minikube service hello-minikube
    * Launch the browser to the thing

* kubectl port-forward service/hello-minikube 7080:8080
    * Alternative of above
    * Now makes the service available on 7080 of my pc

# Destroy minikube clusters
* minikube delete --all

# Write a kata on setting up and having multiple clusters

# Rough notes:
* minikube depends on docker for the virtualisation stuff, idk, lol
* ~/.kube/config
    * where all the config details are stored

* kubectl config view   
    * shows the config details
    * shows the contexts

* kubectl config current-context
    * Get the current context
    * context - Which context we're using
    * Context's describe the clusters
    * contexts are ways of managing multiple clusters

* kubectl config use-context <name>
    * use a particular cluster

* when use-context is set to "", it uses minikube

* kubectl -n uatrc-mobius-simba logs testseeder-testseeder-55ddb8fcc9-zl52b --timestamps > testseederlogs_new.txt

# Potential katas
## Hello world:
* use terraform to create a cluster
* connect to the cluster
* show the cluster details, show the context details
* deploy the image
* query it 
* destroy the deployed image
* teardown terraform
