---
title: Az Azure Kubernetes Service (AKS) fürt frissítése
description: 'Útmutató: Azure Kubernetes Service (AKS)-fürt frissítése'
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028440"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes Service (AKS) fürt frissítése

AKS-fürt életciklusa részeként gyakran frissíteni szeretne a legújabb Kubernetes. Fontos a alkalmazni a legújabb Kubernetes biztonsági kiadások, vagy frissítsen a legújabb funkciók. Ez a cikk bemutatja, hogyan meglévő AKS-fürt frissítése.

## <a name="before-you-begin"></a>Előzetes teendők

Ez a cikk megköveteli, hogy futnak-e az Azure CLI 2.0.56 verzió vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

## <a name="check-for-available-aks-cluster-upgrades"></a>AKS-fürtön elérhető verziófrissítésekről keresése

Ellenőrizze, hogy mely Kubernetes-kiadások érhetők el a fürt számára, használja a [az aks get-frissítések] [ az-aks-get-upgrades] parancsot. Az alábbi példa ellenőrzi a fürthöz nevű elérhető verziófrissítésekről *myAKSCluster* az erőforráscsoport neve *myResourceGroup*:

```azurecli
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

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

A fürt attól függően, hogy hány csomóponttal frissítése néhány percet vesz igénybe.

Győződjön meg arról, hogy a frissítés sikeres volt-e, használja a [az aks show] [ az-aks-show] parancsot:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

Az alábbi példa kimenetében látható, hogy fut-e már a fürt *1.11.6*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta a meglévő AKS-fürt frissítése. Üzembe helyezése és kezelése az AKS-fürtök kapcsolatos további információkért lásd: oktatóanyagok készletét.

> [!div class="nextstepaction"]
> [AKS-oktatóanyagok][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show