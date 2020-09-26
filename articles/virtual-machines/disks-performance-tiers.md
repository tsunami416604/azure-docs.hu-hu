---
title: Az Azure Managed Disk teljesítményének növelése
description: Ismerje meg a felügyelt lemezek teljesítményi szintjeit, valamint a felügyelt lemezek teljesítményi szintjeinek frissítését.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 3d6b243ab517f3663f779d01569acf3d46ad8411
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328122"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>A felügyelt lemezek teljesítményi szintjei (előzetes verzió)

Azure Disk Storage jelenleg beépített kitörési képességekkel rendelkezik, amelyek nagyobb teljesítményt biztosítanak a rövid távú váratlan forgalom kezeléséhez. A prémium SSD-k rugalmasan növelhetik a lemez teljesítményét a lemez tényleges méretének növelése nélkül, ami lehetővé teszi a számítási feladatok teljesítményének kielégítését és a költségek csökkentését, ez a funkció jelenleg előzetes verzióban érhető el. Ez ideális olyan eseményekhez, amelyek átmenetileg igénylik a magasabb szintű teljesítményt, például az üdülési vásárlást, a teljesítménytesztet vagy a képzési környezet futtatását. Ezeknek az eseményeknek a kezeléséhez a szükségesnél magasabb szintű teljesítményt választhat, és visszatérhet az eredeti szintjére, ha a további teljesítmény már nem szükséges.

## <a name="how-it-works"></a>Működés

Amikor először telepít vagy kiépít egy lemezt, a lemez alapteljesítményi szintje a kiosztott lemez mérete alapján van beállítva. Magasabb teljesítményszint kiválasztható úgy, hogy magasabb szintű igényt lehessen kielégíteni, és ha ez a teljesítmény már nem szükséges, visszatérhet a kezdeti alapteljesítményi szintjéhez.

A számlázási változások a rétegek változásakor változnak. Ha például kiépít egy P10-lemezt (128 GiB), az alapteljesítmény-szintet a P10 (500 IOPS és 100 MB/s) értékre állítja be, és a P10 arányban kell fizetnie. A szintet a P50 (7500 IOPS és 250 MB/s) teljesítményének megfelelően frissítheti a lemez méretének növelése nélkül, amely során a P50 díjszabása alapján számítjuk fel a díjat. Ha a nagyobb teljesítményre már nincs szükség, visszatérhet a P10 szintjéhez, és a lemez újbóli számlázása a P10 arányban történik.

| Lemezméret | Alapteljesítmény szintje | Frissíthető a következőre |
|----------------|-----|-------------------------------------|
| 4. GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GiB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GiB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 tebibájt | P40 | P50 |
| 4 TiB | P50 | Nincsenek |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 tebibájt | P80 | Nincsenek |

Számlázási információk: a [felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Korlátozások

- Jelenleg csak a prémium SSD-k támogatottak.
- A rétegek módosítása előtt le kell választani a lemezeket egy futó virtuális gépről.
- A P60, a P70 és a P80 teljesítmény-szintjeinek használata a 4096 GiB vagy újabb lemezekre korlátozódik.
- A lemezek teljesítményi szintje csak 24 óránként módosítható.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A felügyelt lemezek teljesítményi szintjének módosítása jelenleg csak a prémium szintű SSD-k számára érhető el a következő régiókban:

- USA nyugati középső régiója 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Hozzon létre egy üres adatlemezt, amely az alapcsomagnál magasabb szintű.

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Azure Marketplace-rendszerképből származó alapszinten magasabb szintű operációsrendszer-lemez létrehozása

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>Lemez szintjeinek frissítése

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Lemez szintjeinek megjelenítése

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Következő lépések

Ha át kell méreteznie egy lemezt, hogy kihasználhassa a nagyobb teljesítményszint előnyeit, tekintse meg a tárgyban található cikkeket:

- [Virtuális merevlemezek kibontása Linux rendszerű virtuális GÉPEN az Azure CLI-vel](linux/expand-disks.md)
- [Windows rendszerű virtuális géphez csatolt felügyelt lemez kibontása](windows/expand-os-disk.md)
