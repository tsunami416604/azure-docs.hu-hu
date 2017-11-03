---
title: "Az Azure útmutató - fürt frissítése Kubernertes |} Microsoft Docs"
description: "Az Azure útmutató - fürt frissítése Kubernertes"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Docker, tárolók, mikroszolgáltatások, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f203e01e6aaecf04944ee830df4f6adeb1c74d2f
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>Az Azure Tárolószolgáltatásban (AKS) Kubernetes frissítése

Az Azure-tároló szolgáltatás (AKS) fürt frissítése az Azure parancssori felület használatával. A frissítési folyamat során Kubernetes csomópontra van közé tartoznak gondosan [cordoned és merül le](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) minimalizálása érdekében a legkisebb mértékű akadályozása érdekében a futó alkalmazások.

Ebben az oktatóanyagban nyolc nyolc része, Kubernetes fürt frissítésekor. Feladatokat, a következők:

> [!div class="checklist"]
> * Azonosítsa az aktuális és az elérhető Kubernetes verziója
> * Frissítse a Kubernetes csomópontokat
> * A sikeres frissítéshez ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagok egy tároló lemezképet, az Azure-tároló beállításjegyzék feltöltött lemezkép és a létrehozott Kubernetes fürt alkalmazás lett csomagolva. Az alkalmazás ezután futtatták a Kubernetes fürtön. 

Ha nem volna ezeket a lépéseket, és szeretné követéséhez, térjen vissza a [oktatóanyag 1 – létrehozás tároló képek](./tutorial-kubernetes-prepare-app.md).


## <a name="get-cluster-versions"></a>Fürt verziók beolvasása

Fürt frissítése előtt használja a `az aks get-versions` parancs futtatásával ellenőrizze, amely feloldja a Kubernetes érhetők el a frissítést.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Itt láthatja, hogy az aktuális csomópont verziója `1.7.7` és verzió `1.8.1` érhető el.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades    AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  ----------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.1             1.7.7               1.8.1
```

## <a name="upgrade-cluster"></a>A frissítési fürt

Használja a `az aks upgrade` parancs futtatásával frissítse a fürtcsomópontokat. Az alábbi példák a fürt frissíti a verzióra `1.8.1`.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.1
```

Kimenet:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "westus2",
  "name": "myK8sCluster",
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
        "name": "myK8sCluster",
        "osDiskSizeGb": null,
        "osType": "Linux",
        "ports": null,
        "storageProfile": "ManagedDisks",
        "vmSize": "Standard_D2_v2",
        "vnetSubnetId": null
      }
    ],
    "dnsPrefix": "myK8sClust-myResourceGroup-4f48ee",
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.westus2.azmk8s.io",
    "kubernetesVersion": "1.8.1",
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

## <a name="validate-upgrade"></a>Frissítésének ellenőrzése

Most már ellenőrizheti a frissítés az sikeres volt a `az aks show` parancsot.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Kimenet:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  westus2     myResourceGroup  1.8.1                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.westus2.azmk8s.io
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban Kubernetes AKS fürt frissítése. Befejeződtek a következő feladatokat:

> [!div class="checklist"]
> * Azonosítsa az aktuális és az elérhető Kubernetes verziója
> * Frissítse a Kubernetes csomópontokat
> * A sikeres frissítéshez ellenőrzése

Kattintson erre a hivatkozásra AKS tájékozódhat.

> [!div class="nextstepaction"]
> [AKS áttekintése](./intro-kubernetes.md)