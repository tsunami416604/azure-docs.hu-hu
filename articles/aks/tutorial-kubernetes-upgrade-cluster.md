---
title: Azure-on futó Kubernetes oktatóanyag – Fürtök frissítése
description: Azure-on futó Kubernetes oktatóanyag – Fürtök frissítése
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131643"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Oktatóanyag: A Kubernetes frissítése az Azure Kubernetes Service (AKS) szolgáltatásban

Az Azure Kubernetes Service- (AKS-) fürt frissíthető az Azure CLI segítségével. A frissítési folyamat során a Kubernetes-csomópontok megfelelően [el vannak szigetelve és ki vannak ürítve][kubernetes-drain], hogy minimális hatással legyenek a futó alkalmazásokra.

Ebben az oktatóanyagban, amely egy hétrészes sorozat hetedik része, egy Kubernetes-fürtöt frissítünk. A következő feladatokat fogjuk végrehajtani:

> [!div class="checklist"]
> * Az aktuális és az elérhető Kubernetes-verzió azonosítása
> * A Kubernetes-csomópontok frissítése
> * A frissítés sikerességének ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Az előző oktatóanyagokban egy alkalmazást csomagoltunk egy tárolórendszerképbe, a rendszerképet feltöltöttük az Azure Container Registrybe, és létrehoztunk egy Kubernetes-fürtöt. Az alkalmazást ezután a Kubernetes-fürtön futtattuk.

Ha ezeket a lépéseket még nem hajtotta végre, de szeretne velünk tartani, lépjen vissza az [1. oktatóanyag – Tárolórendszerképek létrehozása][aks-tutorial-prepare-app] részhez.

## <a name="get-cluster-versions"></a>A fürtverziók lekérése

A fürtök frissítése előtt az [az aks get-upgrades][] paranccsal ellenőrizze, hogy mely Kubernetes-kiadások frissíthetők.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

A következő példában az aktuális csomópont verziója a következő: *1.9.6*. Az elérhető verziók az *Upgrades* (Frissítések) oszlopban láthatók.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>A fürt frissítése

A fürtcsomópontok az [az aks upgrade][] paranccsal frissíthetők. Az alábbi példában az *1.10.3*-as verzióra frissítjük a fürtöt.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

Az alábbi sűrített példa eredménye azt mutatja, hogy a *kubernetesVersion* az *1.10.3*-as verziót jelenti.

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>A frissítés ellenőrzése

Az [az aks show][] paranccsal ellenőrizze, hogy sikerült-e a frissítés.

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Kimenet:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
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
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade