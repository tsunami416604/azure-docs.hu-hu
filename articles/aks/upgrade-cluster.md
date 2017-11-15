---
title: "Az Azure-tároló szolgáltatás (AKS) fürt frissítése |} Microsoft Docs"
description: "Az Azure-tároló szolgáltatás (AKS) fürt frissítése"
services: container-service
documentationcenter: 
author: gabrtv
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: "Kubernetes, Docker, tárolók, mikroszolgáltatások, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: 6e012450a78c0c66374a649a594810b424a01acf
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="upgrade-an-azure-container-service-aks-cluster"></a>Az Azure-tároló szolgáltatás (AKS) fürt frissítése

Az Azure tároló szolgáltatás (AKS) megkönnyíti a Kubernetes fürtök frissítése beleértve közös felügyeleti feladatok elvégzésére.

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Fürt frissítése előtt használja a `az aks get-versions` parancs futtatásával ellenőrizze, amely feloldja a Kubernetes érhetők el a frissítést.

```azurecli-interactive
az aks get-versions --name myK8sCluster --resource-group myResourceGroup --output table
```

Kimenet:

```console
Name     ResourceGroup    MasterVersion    MasterUpgrades       AgentPoolVersion    AgentPoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myResourceGroup  1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

Három verzióra frissítéshez tudunk: 1.7.9, 1.8.1 és 1.8.2. Így a `az aks upgrade` parancsot az elérhető legújabb verzióra frissítéséhez.  A frissítési folyamat során csomópontra van közé tartoznak gondosan [cordoned és merül le](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) minimalizálása érdekében a legkisebb mértékű akadályozása érdekében a futó alkalmazások.

```azurecli-interactive
az aks upgrade --name myK8sCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
```

Kimenet:

```json
{
  "id": "/subscriptions/4f48eeae-9347-40c5-897b-46af1b8811ec/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myK8sCluster",
  "location": "eastus",
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
    "fqdn": "myk8sclust-myresourcegroup-4f48ee-406cc140.hcp.eastus.azmk8s.io",
    "kubernetesVersion": "1.8.2",
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

Most már ellenőrizheti a frissítés az sikeres volt a `az aks show` parancsot.

```azurecli-interactive
az aks show --name myK8sCluster --resource-group myResourceGroup --output table
```

Kimenet:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myK8sCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Következő lépések

További információ a központi telepítésére és felügyeletére AKS a AKS oktatóanyagok rendelkező.

> [!div class="nextstepaction"]
> [AKS oktatóanyag](./tutorial-kubernetes-prepare-app.md)