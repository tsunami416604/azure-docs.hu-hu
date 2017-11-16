---
title: "Az Azure-tároló szolgáltatás (AKS) fürt méretezése |} Microsoft Docs"
description: "Az Azure-tároló szolgáltatás (AKS) fürt méretezése."
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
ms.date: 11/15/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: b2fa3ebb7a22b9d19678d45cc50806627ab80e90
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="scale-an-azure-container-service-aks-cluster"></a>Az Azure-tároló szolgáltatás (AKS) fürt méretezése

Akkor is könnyen egy AKS fürt a csomópontok különböző számú méretezése.  Válassza ki a kívánt csomópontokat, és futtassa a `az aks scale` parancsot.  Skálázás, ha a csomópontok gondosan lesz [cordoned és merül le](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) minimalizálása érdekében a legkisebb mértékű akadályozása érdekében a futó alkalmazások.  Ha vertikális felskálázásával, a `az` parancs megvárja, amíg vannak megjelölve az csomópontok `Ready` a Kubernetes fürt.

## <a name="scale-the-cluster-nodes"></a>A fürt csomópontjai méretezése

Használja a `az aks scale` parancsot a fürt csomópontjai méretezése. A következő példa egy fürt nevű méretezi *myK8SCluster* egyetlen csomópontba.

```azurecli-interactive
az aks scale --name myK8sCluster --resource-group myResourceGroup --node-count 1
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

## <a name="next-steps"></a>Következő lépések

További információ a központi telepítésére és felügyeletére AKS a AKS oktatóanyagok rendelkező.

> [!div class="nextstepaction"]
> [AKS oktatóanyag](./tutorial-kubernetes-prepare-app.md)
