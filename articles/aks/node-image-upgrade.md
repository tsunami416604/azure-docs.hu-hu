---
title: Az Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképeinek frissítése
description: Ismerje meg, hogyan frissítheti a lemezképeket az AK-fürtcsomópontok és-csomópontok csomópontjain.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 744e62f8a2207cff400a96069fc6ea82866f6e2d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055685"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Az Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképének frissítése

Az AK támogatja a lemezképek frissítését egy csomóponton, hogy naprakész legyen a legújabb operációsrendszer-és futtatókörnyezet-frissítésekkel. Az AK egy új rendszerképet biztosít hetente a legújabb frissítésekkel, így hasznos lehet a csomópontok képeit rendszeresen frissíteni a legújabb funkciókhoz, beleértve a Linux vagy a Windows rendszerű javításokat is. Ebből a cikkből megtudhatja, hogyan frissítheti az AK-fürtcsomópontok lemezképeit, és hogyan frissítheti a csomópont-készlet lemezképeit a Kubernetes verziójának frissítése nélkül.

Ha érdekli az AK által nyújtott legújabb rendszerképek megismerése, további részletekért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .

További információ a fürt Kubernetes-verziójának frissítéséről: [AK-fürt frissítése][upgrade-cluster].

## <a name="limitations"></a>Korlátozások

* Az AK-fürtnek virtuálisgép-méretezési csoportokat kell használnia a csomópontokhoz.

## <a name="install-the-aks-cli-extension"></a>Az AK CLI-bővítmény telepítése

A következő alapvető CLI-verzió kiadása előtt a Node-rendszerkép frissítéséhez az *AK-előnézet CLI-* bővítményre van szükség. Használja az az [Extension Add][az-extension-add] parancsot, majd az az [Extension Update][az-extension-update] paranccsal keresse meg a rendelkezésre álló frissítéseket:

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Az összes csomópont összes csomópontjának frissítése

A csomópont rendszerképének frissítése a-vel történik `az aks upgrade` . A csomópont rendszerképének frissítéséhez használja a következő parancsot:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

A frissítés során a csomópont rendszerképeinek állapotát a következő paranccsal tekintheti meg a `kubectl` címkék beszerzéséhez és a csomópont-rendszerkép aktuális információinak kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Ha a frissítés befejeződött, használja a következőt: `az aks show` a Node-készlet frissített adatainak beolvasása. Az aktuális csomópont képe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Adott csomópont-készlet frissítése

Egy csomópont-készlet rendszerképének frissítése hasonló a fürtön lévő rendszerkép frissítéséhez.

Ha a Kubernetes-fürt frissítésének végrehajtása nélkül szeretné frissíteni a csomópont operációsrendszer-rendszerképét, használja a `--node-image-only` következő példában szereplő beállítást:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

A frissítés során a csomópont rendszerképeinek állapotát a következő paranccsal tekintheti meg a `kubectl` címkék beszerzéséhez és a csomópont-rendszerkép aktuális információinak kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Ha a frissítés befejeződött, használja a következőt: `az aks nodepool show` a Node-készlet frissített adatainak beolvasása. Az aktuális csomópont képe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Csomópont-lemezképek frissítése a Node túlfeszültséggel

A csomópont-rendszerkép frissítési folyamatának felgyorsításához a Node-lemezképeket testreszabható csomópont-túllépési érték használatával frissítheti. Alapértelmezés szerint az AK egy további csomópontot használ a frissítések konfigurálásához.

Ha szeretné bővíteni a frissítések sebességét, használja a értéket a `--max-surge` frissítésekhez használni kívánt csomópontok számának konfigurálásához. Ha többet szeretne megtudni a különböző beállításokkal kapcsolatos kompromisszumokról `--max-surge` , tekintse meg a csomópontok túlterhelésének [frissítését][max-surge]ismertető témakört.

A következő parancs a csomópont-rendszerkép frissítésének maximális túllépési értékét állítja be:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

A frissítés során a csomópont rendszerképeinek állapotát a következő paranccsal tekintheti meg a `kubectl` címkék beszerzéséhez és a csomópont-rendszerkép aktuális információinak kiszűréséhez:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show`A csomópont-készlet frissített adatainak beszerzéséhez használja a következőt:. Az aktuális csomópont képe megjelenik a `nodeImageVersion` tulajdonságban.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Következő lépések

- A legújabb csomópont-lemezképekkel kapcsolatos információkért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .
- Ismerje meg, hogyan frissítheti a Kubernetes-verziót [egy AK-fürt frissítésével][upgrade-cluster].
- [Biztonsági és kernel-frissítések alkalmazása Linux-csomópontokra az Azure Kubernetes szolgáltatásban (ak)][security-update]
- További tudnivalók a több csomópontos készletekről, valamint a csomópont-készletek [több csomópontos készletek létrehozásával és kezelésével][use-multiple-node-pools]történő frissítéséről.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
