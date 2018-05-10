---
title: Az Azure Kubernetes szolgáltatás (AKS) fürt frissítése
description: Az Azure Kubernetes szolgáltatás (AKS) fürt frissítése
services: container-service
author: gabrtv
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 04/05/2018
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 3fae465512501b0642d0215df7b73c9879a1a3a1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes szolgáltatás (AKS) fürt frissítése

Az Azure Kubernetes szolgáltatás (AKS) megkönnyíti a Kubernetes fürtök frissítése beleértve közös felügyeleti feladatok elvégzésére.

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

Három verzióra frissítéshez tudunk: 1.9.1, 1.9.2 és 1.9.6. Az `az aks upgrade` paranccsal frissíthetünk a legújabb elérhető verzióra.  A frissítési folyamat során csomópontra van közé tartoznak gondosan [cordoned és merül le] [ kubernetes-drain] minimalizálása érdekében a legkisebb mértékű akadályozása érdekében a futó alkalmazások.  A fürtfrissítések indítása előtt bizonyosodjon meg arról, hogy rendelkezésre áll elegendő további számítási kapacitás a számítási feladatok a fürtcsomópontok hozzáadása és eltávolítása során történő kezeléséhez.

> [!NOTE]
> Egy AKS fürt frissítésekor, Kubernetes alverziót nem hagyhatók ki. Például frissíti közötti 1.7.x > 1.8.x vagy 1.8.x > 1.9.x engedélyezettek, azonban 1.7 > 1.9 nincs.

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