---
title: "Azure-on futó Kubernetes oktatóanyag – Fürtök frissítése"
description: "Azure-on futó Kubernetes oktatóanyag – Fürtök frissítése"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: d82232d590bcc5c578ebe8ed7c85d25aebcfe097
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="upgrade-kubernetes-in-azure-container-service-aks"></a>A Kubernetes frissítése az Azure Container Service (AKS) szolgáltatásban

Az Azure Container Service- (AKS-) fürt frissíthető az Azure CLI segítségével. A frissítési folyamat során a Kubernetes-csomópontok megfelelően [el vannak szigetelve és ki vannak ürítve][kubernetes-drain], hogy minimális hatással legyenek a futó alkalmazásokra.

Ebben az oktatóanyagban, amely egy nyolcrészes sorozat nyolcadik része, egy Kubernetes-fürtöt frissítünk. A következő feladatokat fogjuk végrehajtani:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk.

Ha ezeket a lépéseket még nem hajtotta végre, de szeretne velünk tartani, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.


## <a name="get-cluster-versions"></a>A fürtverziók lekérése

A fürtök frissítése előtt az `az aks get-versions` parancs használatával ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők.

```azurecli-interactive
az aks get-versions --name myAKSCluster --resource-group myResourceGroup --output table
```

Itt láthatja, hogy a csomópont aktuális verziója `1.7.7`, és hogy az `1.7.9`-es, a `1.8.1`-es és a `1.8.2`-es verzió érhető el.

```
Name     ResourceGroup    MasterVersion    MasterUpgrades       NodePoolVersion     NodePoolUpgrades
-------  ---------------  ---------------  -------------------  ------------------  -------------------
default  myAKSCluster     1.7.7            1.8.2, 1.7.9, 1.8.1  1.7.7               1.8.2, 1.7.9, 1.8.1
```

## <a name="upgrade-cluster"></a>A fürt frissítése

A fürtcsomópontok az `az aks upgrade` paranccsal frissíthetőek. Az alábbi példákban az `1.8.2`-es verzióra frissítjük a fürtöt.

```azurecli-interactive
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.8.2
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

## <a name="validate-upgrade"></a>A frissítés ellenőrzése

Most az `az aks show` paranccsal ellenőrizheti, hogy sikerült-e a frissítés.

```azurecli-interactive
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Kimenet:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus     myResourceGroup  1.8.2                Succeeded            myk8sclust-myresourcegroup-3762d8-2f6ca801.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban frissítettük a Kubernetest egy AKS-fürtben. A következő feladatokat hajtottuk végre:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

Az AKS-sel kapcsolatos további információkat erre a hivatkozásra kattintva érhet el.

> [!div class="nextstepaction"]
> [Az AKS áttekintése][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md