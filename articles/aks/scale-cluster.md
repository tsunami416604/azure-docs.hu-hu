---
title: Az Azure Kubernetes Service (AKS)-fürt méretezése
description: Ismerje meg az Azure Kubernetes Service (AKS)-fürt méretezése a csomópontok számát.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/10/2019
ms.author: iainfoulds
ms.openlocfilehash: 558a3b6dc15293ab9a0895aa4f9f709ba2d0a51f
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214623"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>A csomópontok száma az Azure Kubernetes Service (AKS)-fürt skálázása

Ha az erőforrás van szüksége az alkalmazások módosítása, manuálisan méretezhetők egy AKS-fürt futtatásához egy különböző számú csomópontokra. Leskálázás, amikor a csomópontok használata gondosan [szigetelve és ürítve] [ kubernetes-drain] minimalizálása érdekében a futó alkalmazásokra. Vertikális, a `az` parancs megvárja, amíg a csomópontok lesznek megjelölve `Ready` a Kubernetes-fürt.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

Először kérje le a *neve* a nodepool használatával a [az aks show] [ az-aks-show] parancsot. Az alábbi példa lekéri a nodepool nevet a fürtnek nevű *myAKSCluster* a a *myResourceGroup* erőforráscsoportot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Az alábbi példa kimenetében látható, amely a *neve* van *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

A fürtcsomópontok méretezéséhez használja az `az aks scale` parancsot. Az alábbi példa bemutatja egy nevű fürtöt *myAKSCluster* egyetlen csomópont. Adja meg a saját *--nodepool-name* az előző parancs által például *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Az alábbi példa kimenetében látható az a fürt sikeresen van méretezve, hogy egy csomópont, ahogyan az a *agentPoolProfiles* szakaszban:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": true,
  "fqdn": "myaksclust-myresourcegroup-19da35-0d60b16a.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.9.11",
  "linuxProfile": {
    "adminUsername": "azureuser",
    "ssh": {
      "publicKeys": [
        {
          "keyData": "[...]"
        }
      ]
    }
  },
  "location": "eastus",
  "name": "myAKSCluster",
  "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "networkPlugin": "kubenet",
    "networkPolicy": null,
    "podCidr": "10.244.0.0/16",
    "serviceCidr": "10.0.0.0/16"
  },
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_eastus",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "servicePrincipalProfile": {
    "clientId": "[...]",
    "secret": null
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="next-steps"></a>További lépések

Az AKS üzembe helyezésével és kezelésével kapcsolatos további információkért lásd az AKS oktatóanyagait.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
