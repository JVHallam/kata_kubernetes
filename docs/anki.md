    * $cluster = az aks list --query "[*].{ name : name, resourceGroup : resourceGroup, powerState : powerState }" | convertFrom-json
    * az aks start --name myAKSCluster --resource-group myResourceGroup
    * az aks get-credentials --name myClusterName --resource-group myResourceGroupName
    * kubectl config view
    * kubectl get pods --no-headers -o custom-columns=":metadata.name" 
    * kubectl logs $podname --timestamps > logs.txt
    * az storage blob upload --container-name $sc --file "log.txt" --name "bloblog.txt" --account-name $sac
    * az storage blob download --container-name $sc --file "NEW NAME" --name "bloblog.txt" --account-name $sac
    * az aks stop --name $name --resource-group $rg
    * $cluster.powerState -> Stopped
