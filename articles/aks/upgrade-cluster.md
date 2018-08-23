---
title: Az Azure Kubernetes Service (AKS) fürt frissítése
description: Az Azure Kubernetes Service (AKS) fürt frissítése
services: container-service
author: gabrtv
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/18/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 4ff2b56afc4496b6344735b4e3c813b06cee17e3
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42059481"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes Service (AKS) fürt frissítése

Az Azure Kubernetes Service (AKS) megkönnyíti a Kubernetes-fürtök frissítése például gyakori felügyeleti feladatok végrehajtása.

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

A fürtök frissítése előtt az `az aks get-upgrades` parancs használatával ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők.

```azurecli-interactive
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Kimenet:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  -------------------
default  mytestaks007     1.8.10           1.8.10             1.9.1, 1.9.2, 1.9.6
```

Három verzió érhető el a frissítéshez: 1.9.1, 1.9.2-es és 1.9.6. Az `az aks upgrade` paranccsal frissíthetünk a legújabb elérhető verzióra.  A frissítési folyamat során az AKS lesz új csomópont hozzáadása a fürthöz, majd gondosan [Ez a három csomópontunk, és a kiürítési] [ kubernetes-drain] futó alkalmazások egyszerre egy csomóponton.

> [!NOTE]
> AKS-fürt frissítésekor Kubernetes alverziót nem hagyhatók ki. Például frissítések között 1.8.x -> 1.9.x vagy 1.9.x -> 1.10.x engedélyezett, azonban az 1.8-as 1.10 -> nincs. Szeretné frissíteni, az 1.8-as 1.10 -> kell, hogy először frissítse az 1.8-as 1.9 -> és a egy másik hajtsa végre egy másik 1.9 verzióról 1.10 ->

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.9.6
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
    "kubernetesVersion": "1.9.6",
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

Az `az aks show` paranccsal ellenőrizze, hogy sikerült-e a frissítés.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Kimenet:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.9.6                 Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>További lépések

Az AKS üzembe helyezésével és kezelésével kapcsolatos további információkért lásd az AKS oktatóanyagait.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
