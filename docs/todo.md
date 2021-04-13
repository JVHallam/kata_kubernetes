# Todo

* Be a nosy fuck:
    * Take a look at the doomlords deployment details
    * Make a list of things used by them in their scripts
    * Try and kata-ise them

* Task areas to investigate:
    * Learn the language, even if i result in around 4 mini katas, then moving onto something else
        * Kata 3/4:
            * Wtf the difference between a node and a cluster is
            * Take a look at the az commands below
            * Monitoring logging and debugging
            * How do i de-allocate the vm's being used and stop it from costing me money when i'm not working
            * all the kubectl commands and how to use then ( not when to )
            * Auto pod scaling - Try and get a pod to auto scale and get-pod

        * Manage kubernetes objects ( the -f something.yaml bull )
            * Try and repeat the same as kata 1, but with these objects, to an existing cluster
            * Take a look into the things that can be created via this method
            * Create a pair of setups or what not, just to see what happens
            * This will likely handle kata 2/4
            * what about the az tool, for querying the details of the aks cluster setup

    
        * Kata 4:
            * I want to see parallel computing, handled by the cluster
            * Run applications
            * Run jobs
            * Administer a cluster 

The az tool and logging, managing a cluster, stopping and restarting it:
    * az aks list --resource-group $resourceGroupName --query "[*].name"
	* kubectl get services | select-string -pattern "hello" 
    * kubectl logs podname --timestamps > logs.txt
    * SPONGEBOY ME BOB YOUR COSTING ME MONEY : Locate, spinup and slowdown aks clusters
        * az aks stop --name myAKSCluster --resource-group myResourceGroup
        * az aks list --query "[*].{name : name, powerState :powerState, resourceGroup :resourceGroup }" 
        * az aks list --query "[?powerState.code=='Running'].{name : name, powerState :powerState, resourceGroup :resourceGroup}"
        * $name = az aks list --query "[?powerState.code=='Running'].name" | convertFrom-json

        * az storage blob upload --file "test.txt" --container-name scjvhtfstate --name "upload.txt" --account-name "stjvhtfstate"

