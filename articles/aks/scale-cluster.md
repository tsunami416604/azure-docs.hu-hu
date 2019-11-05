---
title: Azure Kubernetes szolgáltatásbeli (ak-) fürt méretezése
description: Megtudhatja, hogyan méretezheti a csomópontok számát egy Azure Kubernetes-szolgáltatásbeli (ak-) fürtben.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472588"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Csomópontok számának skálázása Azure Kubernetes szolgáltatásbeli (ak-) fürtben

Ha az alkalmazások erőforrás-szükséglete megváltozik, manuálisan méretezheti egy AK-fürtöt úgy, hogy más számú csomópontot futtasson. A skálázás során a rendszer gondosan [kiüríti és kiüríti][kubernetes-drain] a csomópontokat az alkalmazások futtatásának minimalizálásához. Ha vertikális felskálázást végez, az AK megvárja, amíg a csomópontokat a Kubernetes-fürt `Ready` jelöli meg a hüvelyek ütemezése előtt.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

Először kérje le a Node-készlet *nevét* az az az [AK show][az-aks-show] paranccsal. A következő példa beolvassa a *myAKSCluster* nevű fürt Node-készletének nevét a *myResourceGroup* erőforráscsoporthoz:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

A következő példa kimenete azt mutatja, hogy a *név* *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

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

A fürtcsomópontok méretezéséhez használja az az [AK Scale][az-aks-scale] parancsot. Az alábbi példa egy *myAKSCluster* nevű fürtöt egyetlen csomópontra méretezi. Adja meg saját *nodepool-nevét* az előző parancsból, például *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

A következő példa kimenete azt mutatja, hogy a fürt egy csomópontra való skálázása sikeresen megtörtént, ahogy az a *agentPoolProfiles* szakaszban is látható:

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

Ebben a cikkben manuálisan méretezhető egy AK-fürtöt a csomópontok számának növeléséhez vagy csökkentéséhez. A fürt automatikus [méretezésével][cluster-autoscaler] is automatikusan méretezheti a fürtöt.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
