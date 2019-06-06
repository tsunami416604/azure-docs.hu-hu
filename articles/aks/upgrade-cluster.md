---
title: Az Azure Kubernetes Service (AKS) fürt frissítése
description: 'Útmutató: Azure Kubernetes Service (AKS)-fürt frissítése'
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 2cadd4b33cb52307599ce1e83eee8370ef9850fe
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692777"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes Service (AKS) fürt frissítése

AKS-fürt életciklusa részeként gyakran frissíteni szeretne a legújabb Kubernetes. Fontos a alkalmazni a legújabb Kubernetes biztonsági kiadások, vagy frissítsen a legújabb funkciók. Ez a cikk bemutatja, hogyan frissítse a fő összetevőket vagy egy, az AKS-fürt alapértelmezett csomópontkészletek.

Az aks-ben több csomópontkészletek vagy (jelenleg egyaránt előzetes verzióban az aks-ben), a Windows Server-csomópontok használó fürtök Lásd [frissítése az aks-ben csomópontkészletek][nodepool-upgrade].

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.65 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>AKS-fürtön elérhető verziófrissítésekről keresése

Ellenőrizze, hogy mely Kubernetes-kiadások érhetők el a fürt számára, használja a [az aks get-frissítések] [ az-aks-get-upgrades] parancsot. Az alábbi példa ellenőrzi a fürthöz nevű elérhető verziófrissítésekről *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Amikor frissít egy AKS-fürtöt, Kubernetes alverziót nem hagyhatók ki. Például frissítése között *1.11.x* -> *1.12.x* vagy *1.12.x* -> *1.13.x* engedélyezettek, de *1.11.x* -> *1.13.x* nem.
>
> A frissítéshez a *1.11.x* -> *1.13.x*, először frissítés a *1.11.x* -> *1.12.x*, majd frissítsen a *1.12.x* -> *1.13.x*.

Az alábbi példa kimenetében látható, hogy a fürt is frissíthető verzióra *1.12.7* vagy *1.12.8*:

```console
Name     ResourceGroup    MasterVersion  NodePoolVersion  Upgrades
-------  ---------------  -------------  ---------------  --------------
default  myResourceGroup  1.11.9         1.11.9           1.12.7, 1.12.8
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AKS-fürt számára elérhető verzió listáját, használja a [az aks frissítése] [ az-aks-upgrade] paranccsal frissíthetőek. A frissítési folyamat során AKS ad hozzá egy új csomópontra a fürtben futtatják a meghatározott Kubernetes-verziót, majd gondosan [fekvő terület és zuhan] [ kubernetes-drain] egyik futó leskálázáskor a régi csomópont az alkalmazások. Az új csomóponton futó alkalmazás podok megerősítik, amikor a rendszer törli a régi csomópont. Ez a folyamat ismétlődik mindaddig, amíg a fürt összes csomópontja frissítettek.

Az alábbi példában a fürt frissíti a verzióra *1.12.8*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.12.8
```

A fürt attól függően, hogy hány csomóponttal frissítése néhány percet vesz igénybe.

Győződjön meg arról, hogy a frissítés sikeres volt-e, használja a [az aks show] [ az-aks-show] parancsot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Az alábbi példa kimenetében látható, hogy fut-e már a fürt *1.12.8*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.12.8               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta a meglévő AKS-fürt frissítése. Üzembe helyezése és kezelése az AKS-fürtök kapcsolatos további információkért lásd: oktatóanyagok készletét.

> [!div class="nextstepaction"]
> [AKS-oktatóanyagok][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
