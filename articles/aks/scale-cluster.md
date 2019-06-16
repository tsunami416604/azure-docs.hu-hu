---
title: Az Azure Kubernetes Service (AKS)-fürt méretezése
description: Ismerje meg az Azure Kubernetes Service (AKS)-fürt méretezése a csomópontok számát.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475135"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>A csomópontok száma az Azure Kubernetes Service (AKS)-fürt skálázása

Ha az erőforrás van szüksége az alkalmazások módosítása, manuálisan méretezhetők egy AKS-fürt futtatásához egy különböző számú csomópontokra. Leskálázás, amikor a csomópontok használata gondosan [szigetelve és ürítve] [ kubernetes-drain] minimalizálása érdekében a futó alkalmazásokra. Vertikális, az AKS megvárja, amíg a csomópontok lesznek megjelölve `Ready` által a Kubernetes-fürt előtt podok ütemezett rajtuk.

## <a name="scale-the-cluster-nodes"></a>A fürtcsomópontok méretezése

Először kérje le a *neve* a csomópont készlethez használatával a [az aks show] [ az-aks-show] parancsot. Az alábbi példa lekéri a csomópont-készlet nevét, a fürt nevű *myAKSCluster* a a *myResourceGroup* erőforráscsoportot:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Az alábbi példa kimenetében látható, amely a *neve* van *nodepool1*:

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

Használja a [az aks méretezési] [ az-aks-scale] parancsot a fürtcsomópontok méretezéséhez. Az alábbi példa bemutatja egy nevű fürtöt *myAKSCluster* egyetlen csomópont. Adja meg a saját *--nodepool-name* az előző parancs által például *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Az alábbi példa kimenetében látható az a fürt sikeresen van méretezve, hogy egy csomópont, ahogyan az a *agentPoolProfiles* szakaszban:

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

Ebben a cikkben azt manuálisan méretezi a AKS-fürt növelése vagy csökkentése érdekében a csomópontok számát. Is használhatja a [méretező fürt] [ cluster-autoscaler] (jelenleg előzetes verzióban érhető el az aks-ben) a fürtök automatikus méretezése.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
