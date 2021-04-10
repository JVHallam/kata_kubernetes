Everything below this line hasn't actually been verified as useful yet

* Get your image onto the cluster
    * Get the credentials, using the outputs from earlier
    * Show the config to prove you're connected

* Show the config, to prove you've connected
* Deploy the sample image to the cluster
* Get stuff:
    * Get the public IP address routed to the image
    * Get the deployments
    * Get the nodes
    * Get replicasets

* Create a service object that exposes the deployment

* Expose it so that you can query it?
    * Figure out which port the thing is deployed to?
    * Get your ip address

* Show the services
* Get the external ip
 
* kubectl expose deployment helloaks --type=LoadBalancer --name=my-service --port=8080
    * after this $ kubetctl get services
    * my-service will attach to helloaks, and expose it to the wide world

* Tearing down:
    * one's that work:
        * $ kubectl delete service my-service
        * $ kubectl delete service helloaks
        * $ kubectl delete deployment helloaks
        * $ kubectl delete pod <podname>

    * Terraform destroy

    * Delete the contexts and clusters
        * kubectl config unset contexts.aks-jvh-n29i
        * kubectl config unset clusters.aks-jvh-n29i
        * kubectl config unset users.clusterUser_rg-jvh-n29i_aks-jvh-n29i    

//Each of these values need to be turned into anki values
resource "azurerm_kubernetes_cluster" "cluster" {
  name                = module.naming.kubernetes_cluster.name_unique
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  dns_prefix          = "exampleaks1"

  default_node_pool {
    name       = "default"
    node_count = 1
    vm_size    = "Standard_D2_v2"
  }

  identity {
    type = "SystemAssigned"
  }
}

az aks get-credentials --resource-group myResourceGroup --name myAKSCluster

az aks get-credentials --resource-group "rg-jvh-n29i" --name "aks-jvh-n29i"

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
