---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök frissítése
description: Azure-on futó Kubernetes oktatóanyag – Fürtök frissítése
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a293ebbd2ec07d9de53d168f79b8546576499bcb
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="tutorial-upgrade-kubernetes-in-azure-container-service-aks"></a>Oktatóanyag: A Kubernetes frissítése az Azure Container Service (AKS) szolgáltatásban

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

A fürtök frissítése előtt az `az aks get-upgrades` parancs használatával ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

Itt láthatja, hogy az aktuális csomópont verziója a következő: `1.7.9`. Az elérhető frissítési verziók a frissítések oszlopban láthatók.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------------------
default  myResourceGroup  1.7.9            1.7.9              1.7.12, 1.8.1, 1.8.2, 1.8.6, 1.8.7
```

## <a name="upgrade-cluster"></a>A fürt frissítése

A fürtcsomópontok az `az aks upgrade` paranccsal frissíthetőek. Az alábbi példákban az `1.8.2`-es verzióra frissítjük a fürtöt.

```azurecli
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

```azurecli
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