# 1) Hello AKS

* The location of the .kube config file is:
    * ~/.kube/config

* The cluster details in terraform main.tf:
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

* Get your image on the cluster
    * Get the credential, using the terraform outputs
        * terraform outputs
            * $clusterName = (terraform output cluster_name) -replace '"', ''
            * $resourceGroupName = (terraform output resource_group_name) -replace '"', ''

        * az aks get-credentials --resource-group $resourceGroupName --name $clusterName

    * Deploy the image
        * Deploy the image to the cluster
            * kubectl create deployment helloaks --image=k8s.gcr.io/echoserver:1.4
        * confirm that it's there
            * kubectl get deployments
            * kubectl get pods

        * Create the port forwarding service:
            * kubectl expose deployment helloaks --type=NodePort --port=8080
            * kubectl get services, to check the name and that it's there

        * Create the loadbalancer service
            * kubectl expose deployment helloaks --type=LoadBalancer --name=public --port=8080
            * the --name is the name of the service created, to expose the one from earlier

        * Test : Check that the thing as available from outside
            * kubectl get services publichelloaks
            * Grab the ip address
            * query : ip-address:8080 - Idk if it has to be 8080, but that's what the image requires
            * e.g. http://20.49.219.48:8080/, was the one that i just created

* Tear it all down:
    * Tear down the services, inside of the pods 
        * Teardown the loadbalancer service:
            * $ kubectl delete service public

        * Teardown the image's port exposure service
            * $ kubectl delete service helloaks

        * Teardown the deployment of the image
            * $ kubectl delete deployment helloaks

        * Teardown any pods that are somehow left over
            * $ kubectl delete pod <podname>

    * Tear down the azure resources, via terraform
        * $ terraform destroy --auto-approve
        * Check that it's no longer there in the portal

    * Tear down the context, from the context file
        * kubectl config view, shows:
            * clusters
            * contexts
            * users

        * kubectl config unset "contexts.$($clusterName)"
        * kubectl config unset "clusters.$($clusterName)"
        * $username = "users.clusterUser_$($resourceGroupName)_$($clusterName)"
        * kubectl config unset $username