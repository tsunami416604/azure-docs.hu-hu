---
title: Az Azure Kubernetes szolgáltatás (AKS) fürt méretezése
description: Az Azure Kubernetes szolgáltatás (AKS) fürt méretezése.
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 37f949e1e043e62b8bd8f73a2b40d0549f77adc3
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="scale-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes szolgáltatás (AKS) fürt méretezése

Egy AKS-fürt egyszerűen méretezhető különböző számú csomópontokra.  Válassza ki a kívánt számú csomópontot, és futtassa az `az aks scale` parancsot.  Skálázás, ha a csomópontok gondosan lesz [cordoned és merül le] [ kubernetes-drain] minimalizálása érdekében a legkisebb mértékű akadályozása érdekében a futó alkalmazások.  Vertikális felskálázáskor az `az` parancs megvárja, amíg a csomópontokat `Ready` állapotúnak nem jelöli meg a Kubernetes-fürt.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

A fürtcsomópontok méretezéséhez használja az `az aks scale` parancsot. A következő példa egy fürt nevű méretezi *myAKSCluster* egyetlen csomópontba.

```azurecli-interactive
az aks scale --name myAKSCluster --resource-group myResourceGroup --node-count 1
```

Kimenet:

```json
{
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "location": "eastus",
  "name": "myAKSCluster",
  "properties": {
    "accessProfiles": {
      "clusterAdmin": {
        "kubeConfig": "..."
      },
      "clusterUser": {
        "kubeConfig": "..."
      }
    },
    "agentPoolProfiles": [
      {
        "count": 1,
        "dnsPrefix": null,
        "fqdn": null,
        "name": "myAKSCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.7.7",
    "linuxProfile": {
      "adminUsername": "azureuser",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "..."
          }
        ]
      }
    },
    "provisioningState": "Succeeded",
    "servicePrincipalProfile": {
      "clientId": "e70c1c1c-0ca4-4e0a-be5e-aea5225af017",
      "keyVaultSecretRef": null,
      "secret": null
    }
  },
  "resourceGroup": "myResourceGroup",
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