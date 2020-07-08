---
title: Az Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképeinek frissítése
description: Ismerje meg, hogyan frissítheti a lemezképeket az AK-fürtcsomópontok és-csomópontok csomópontjain.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 20379f6a1e87c7553d6567be5b50f22bbadb8db7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84514686"
---
# <a name="preview---azure-kubernetes-service-aks-node-image-upgrades"></a>Előzetes verzió – az Azure Kubernetes szolgáltatás (ak) csomópont-rendszerképének frissítése

Az AK támogatja a lemezképek frissítését egy csomóponton, hogy naprakész legyen a legújabb operációsrendszer-és futtatókörnyezet-frissítésekkel. Az AK egy új rendszerképet biztosít hetente a legújabb frissítésekkel, így hasznos lehet a csomópontok képeit rendszeresen frissíteni a legújabb funkciókhoz, beleértve a Linux vagy a Windows rendszerű javításokat is. Ebből a cikkből megtudhatja, hogyan frissítheti az AK-fürtcsomópontok lemezképeit, és hogyan frissítheti a csomópont-készlet lemezképeit a Kubernetes verziójának frissítése nélkül.

Ha érdekli az AK által nyújtott legújabb rendszerképek megismerése, további részletekért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .

További információ a fürt Kubernetes-verziójának frissítéséről: [AK-fürt frissítése][upgrade-cluster].

## <a name="register-the-node-image-upgrade-preview-feature"></a>A csomópont rendszerkép-frissítési előnézet funkciójának regisztrálása

Ha az előzetes verzió ideje alatt szeretné használni a csomópont-rendszerkép verziófrissítése funkciót, regisztrálnia kell a szolgáltatást.

```azurecli
# Register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "NodeImageUpgradePreview"
```

A regisztráció befejezéséhez több percet is igénybe vehet. A következő parancs használatával ellenőrizheti, hogy a szolgáltatás regisztrálva van-e:

```azurecli
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodeImageUpgradePreview')].{Name:name,State:properties.state}"
```

Az előzetes verzió ideje alatt a Node-rendszerkép frissítéséhez az *AK-előnézet CLI-* bővítményre van szükség. Használja az [az Extension Add] [az-Extension-Add] parancsot, majd keresse meg az összes elérhető frissítést az [az Extension Update] [az-Extension-Update] parancs használatával:

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

## <a name="next-steps"></a>További lépések

- A legújabb csomópont-lemezképekkel kapcsolatos információkért tekintse meg az [AK kibocsátási megjegyzéseit](https://github.com/Azure/AKS/releases) .
- Ismerje meg, hogyan frissítheti a Kubernetes-verziót [egy AK-fürt frissítésével][upgrade-cluster].
- [Biztonsági és kernel-frissítések alkalmazása Linux-csomópontokra az Azure Kubernetes szolgáltatásban (ak)][security-update]
- További tudnivalók a több csomópontos készletekről, valamint a csomópont-készletek [több csomópontos készletek létrehozásával és kezelésével][use-multiple-node-pools]történő frissítéséről.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
