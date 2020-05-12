---
title: Azure Kubernetes szolgáltatás (ak) magas rendelkezésre állása az üzemidő SLA-val
description: Ismerje meg az Azure Kubernetes szolgáltatás (ak) API-kiszolgálójának választható magas rendelkezésre állási SLA-ajánlatát.
services: container-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.openlocfilehash: 1c340f85a107cac437e1241025d8c9bc6991b965
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83125723"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes szolgáltatás (ak) üzemidő SLA

A rendelkezésre állási SLA egy opcionális funkció, amely lehetővé teszi, hogy a fürthöz pénzügyileg magasabb szintű SLA-t biztosítson. A [rendelkezésre állási zónát és a][availability-zones] rendelkezésre állási zónákat nem használó fürtök esetében az Kubernetes API-kiszolgáló végpontjának 99,95% 99,9-os rendelkezésre állása garantált. Az AK fő csomópont-replikákat használ a frissítési és a tartalék tartományok között az SLA-követelmények teljesítésének biztosítása érdekében.

Ennek a funkciónak be kell kapcsolni a szolgáltatást, ha az ügyfelek megfelelőségi okokból vagy az SLA-k kibővítéséhez szükséges SLA-t igényelnek. Azok az ügyfelek, akiknek a kritikus számítási feladatokkal magasabb rendelkezésre állásra van szükségük, a szolgáltatás engedélyezésével garantált SLA-kedvezményt biztosítanak. A Kubernetes API-kiszolgáló magasabb rendelkezésre állásának beszerzéséhez engedélyezze a szolgáltatást a Availability Zones használatával.  

Az ügyfelek korlátlan számú ingyenes fürtöt hozhatnak létre a 99,5%-os szolgáltatási szintű célkitűzés (SLO) használatával.

> [!Important]
> A kilépési zárolást tartalmazó fürtök esetében lásd: a [kimenő forgalom korlátozása](limit-egress-traffic.md) a megfelelő portok megnyitására a rendelkezésre állási SLA-hoz.

## <a name="sla-terms-and-conditions"></a>SLA-feltételek és kikötések

A rendelkezésre állási SLA egy fizetős szolgáltatás, és fürtön engedélyezve van. A rendelkezésre állási SLA díjszabását a fürtök száma határozza meg, a fürtök mérete nem. További információért tekintse meg a [rendelkezésre állási SLA díjszabását](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

## <a name="region-availability"></a>Régió elérhetősége

A rendelkezésre állási SLA a következő régiókban érhető el:

* Kelet-Ausztrália
* Közép-Kanada
* USA keleti régiója
* USA 2. keleti régiója
* USA déli középső régiója
* Délkelet-Ázsia
* USA nyugati régiója, 2.

## <a name="before-you-begin"></a>Előkészületek

* Az Azure CLI verziója 2.7.0 vagy újabb verzió

## <a name="creating-a-cluster-with-uptime-sla"></a>Fürt létrehozása a rendelkezésre állási SLA-val

Ha a rendelkezésre állási SLA-val rendelkező új fürtöt szeretne létrehozni, használja az Azure CLI-t.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```
Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A Tárolókhoz készült Azure Monitor szintén engedélyezve van az *--enable-addons monitoring* paraméterrel.  A művelet végrehajtása több percet is igénybe vehet.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --uptime-sla --node-count 1 --enable-addons monitoring --generate-ssh-keys
```
Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza. A következő JSON-kódrészlet az SKU fizetős szintjét mutatja be, amely azt jelzi, hogy a fürt engedélyezve van a rendelkezésre állási SLA-val.

```output
  },
  "sku": {
    "name": "Basic",
    "tier": "Paid"
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters",
  "windowsProfile": null
```

## <a name="limitations"></a>Korlátozások

* Jelenleg nem adható hozzá a rendelkezésre állási SLA a meglévő fürtökhöz.
* Jelenleg nem lehet eltávolítani a rendelkezésre állási SLA-t egy AK-fürtből.  

## <a name="next-steps"></a>További lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[faq]: ./faq.md
[availability-zones]: ./availability-zones.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[limit-egress-traffic]: ./limit-egress-traffic.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
