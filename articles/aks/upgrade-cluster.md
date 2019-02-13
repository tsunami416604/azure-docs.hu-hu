---
title: Az Azure Kubernetes Service (AKS) fürt frissítése
description: 'Útmutató: Azure Kubernetes Service (AKS)-fürt frissítése'
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56185983"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes Service (AKS) fürt frissítése

AKS-fürt életciklusa részeként gyakran frissíteni szeretne a legújabb Kubernetes. Fontos a alkalmazni a legújabb Kubernetes biztonsági kiadások, vagy frissítsen a legújabb funkciók. Ez a cikk bemutatja, hogyan meglévő AKS-fürt frissítése.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.56 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>AKS-fürtön elérhető verziófrissítésekről keresése

Ellenőrizze, hogy mely Kubernetes-kiadások érhetők el a fürt számára, használja a [az aks get-frissítések] [ az-aks-get-upgrades] parancsot. Az alábbi példa ellenőrzi a fürthöz nevű elérhető verziófrissítésekről *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Amikor frissít egy AKS-fürtöt, Kubernetes alverziót nem hagyhatók ki. Például frissítése között *1.10.x* -> *1.11.x* vagy *1.11.x* -> *1.12.x* engedélyezettek, de *1.10.x* -> *1.12.x* nem.
>
> A frissítéshez a *1.10.x* -> *1.12.x*, először frissítés a *1.10.x* -> *1.11.x*, majd frissítsen a *1.11.x* -> *1.12.x*.

Az alábbi példa kimenetében látható, hogy a fürt is frissíthető verzióra *1.11.5* vagy *1.11.6*:

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>AKS-fürt frissítése

Az AKS-fürt számára elérhető verzió listáját, használja a [az aks frissítése] [ az-aks-upgrade] paranccsal frissíthetőek. A frissítési folyamat során az AKS ad hozzá új csomópontot a fürthöz, majd gondosan [fekvő terület és zuhan] [ kubernetes-drain] futó alkalmazások egyszerre egy csomóponton. Az alábbi példában a fürt frissíti a verzióra *1.11.6*:

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

A fürt attól függően, hogy hány csomóponttal frissítése néhány percet vesz igénybe.

Győződjön meg arról, hogy a frissítés sikeres volt-e, használja a [az aks show] [ az-aks-show] parancsot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Az alábbi példa kimenetében látható, hogy fut-e már a fürt *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
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
