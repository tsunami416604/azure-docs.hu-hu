---
title: Azure Kubernetes Service- (AKS-) fürt méretezése
description: Ismerje meg, hogyan skálázható a csomópontok száma egy Azure Kubernetes-fürt (AKS) fürtben.
services: container-service
author: iainfoulds
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 55d7a00a0a8c0b655f06810f8bcea7126bb9167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368417"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Csomópontszám skálázása egy Azure Kubernetes Service- (AKS-) fürtben

Ha az alkalmazások erőforrás-igényei megváltoznak, manuálisan skálázhatja az AKS-fürtöt, hogy különböző számú csomópontot futtasson. Ha leskálázódik, a csomópontok at gondosan [kordonnal és kiürítési][kubernetes-drain] minimalizálása érdekében zavar a futó alkalmazások. A skálázás kor az AKS megvárja, `Ready` amíg a csomópontokat megjelöli a Kubernetes-fürt, mielőtt a podok ütemezése rájuk.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

Először az [az aks show][az-aks-show] paranccsal szerezd meg a csomópontkészlet *nevét.* A következő példa lekéri a *myResourceGroup* erőforráscsoportban a *myResourceGroup* nevű fürt csomópontkészletének nevét:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A következő példa kimenetazt mutatja, hogy a *név* *nodepool1*:

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Az [aks scale][az-aks-scale] paranccsal méretezhetők a fürtcsomópontok. A következő példa egy *myAKSCluster* nevű fürtöt egyetlen csomópontra skáláz. Adja meg saját *--nodepool-nevét* az előző parancsból, például *nodepool1:*

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A következő példakimenet azt mutatja, hogy a fürt sikeresen skálázva egy csomópontra, ahogy az *az agentPoolProfiles* szakaszban látható:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben manuálisan méretezett egy AKS-fürta csomópontok számának növelése vagy csökkentése érdekében. A [fürt automatikus skálázójával][cluster-autoscaler] is automatikusan skálázhatja a fürtöt.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
