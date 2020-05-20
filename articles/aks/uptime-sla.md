---
title: Azure Kubernetes szolgáltatás (ak) a rendelkezésre állási SLA-val
description: Ismerje meg az Azure Kubernetes Service (ak) API-kiszolgáló opcionális, rendelkezésre állási SLA-ajánlatát.
services: container-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e0e1399f69640dddfd618ac99637023390f28a92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683223"
---
# <a name="azure-kubernetes-service-aks-uptime-sla"></a>Azure Kubernetes szolgáltatás (ak) üzemidő SLA

A rendelkezésre állási SLA egy opcionális funkció, amely lehetővé teszi egy adott fürt pénzügyi támogatását, magasabb szintű SLA-t. A rendelkezésre állási SLA garantálja a Kubernetes API-kiszolgáló végpontjának 99,95%-os rendelkezésre állását olyan fürtök esetében, amelyek [Availability Zones][availability-zones] és 99,9%-ot használnak a nem a Availability Zones használó fürtök számára. Az AK fő csomópont-replikákat használ a frissítési és a tartalék tartományok között az SLA-követelmények teljesítésének biztosítása érdekében.

Azok az ügyfelek, akik SLA-t igényelnek a megfelelőségi követelmények kielégítése érdekében, vagy a végfelhasználók számára az SLA kibővítését igénylik, engedélyezni kell ezt a funkciót. A magasabb rendelkezésre állási SLA előnyeit kihasználó kritikus számítási feladatokkal rendelkező ügyfelek is hasznosak lehetnek. A Availability Zones rendelkezésre állási SLA funkciójának használata lehetővé teszi, hogy a Kubernetes API-kiszolgáló kihasználtsága nagyobb legyen.  

Az ügyfelek továbbra is létrehozhatnak korlátlan számú ingyenes fürtöt 99,5%-os szolgáltatási szintű célkitűzéssel (SLO), és igény szerint választhatják az előnyben részesített SLO-t vagy SLA-t.

> [!Important]
> A kilépési zárolást tartalmazó fürtök esetében lásd: a [kimenő forgalom korlátozása](limit-egress-traffic.md) a megfelelő portok megnyitására.

## <a name="sla-terms-and-conditions"></a>SLA-feltételek és kikötések

A rendelkezésre állási SLA egy fizetős szolgáltatás, és fürtön engedélyezve van. A rendelkezésre állási SLA díjszabását a különálló fürtök száma határozza meg, nem az egyes fürtök méretével. További információért tekintse meg a [rendelkezésre állási SLA díjszabását](https://azure.microsoft.com/pricing/details/kubernetes-service/) .

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
```

## <a name="limitations"></a>Korlátozások

* Jelenleg a nem konvertálható meglévő fürtként a rendelkezésre állási SLA engedélyezéséhez.
* Jelenleg a létrehozás és az engedélyezés után nem lehet eltávolítani a rendelkezésre állási SLA-t egy AK-fürtből.  
* A privát fürtök jelenleg nem támogatottak.

## <a name="next-steps"></a>További lépések

A [Availability Zones][availability-zones] használatával növelheti a magas rendelkezésre állást az AK-fürt számítási feladataival.
Konfigurálja a fürtöt a [kimenő forgalom korlátozása](limit-egress-traffic.md)érdekében.

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
