---
title: Az Azure Managed Disks teljesítményének módosítása
description: Ismerje meg a felügyelt lemezek teljesítményi szintjeit, és Ismerje meg, hogyan változtathatók meg a meglévő felügyelt lemezek teljesítményi szintjei.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 404f435e321e53694807a627121d84f6cbf6724d
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359679"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>A felügyelt lemezek teljesítményi szintjei (előzetes verzió)

Azure Disk Storage jelenleg beépített kitörési képességekkel rendelkezik, amelyek nagyobb teljesítményt nyújtanak a rövid távú váratlan forgalom kezeléséhez. A prémium SSD-k rugalmasan növelhetik a lemez teljesítményét a lemez tényleges méretének növelése nélkül. Ez a funkció lehetővé teszi a számítási feladatok teljesítményének kielégítését és a költségek csökkentését. 

> [!NOTE]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. 

Ez a funkció ideális olyan eseményekhez, amelyek átmenetileg igénylik a magasabb szintű teljesítményt, például az üdülési vásárlást, a teljesítmény tesztelését vagy a képzési környezet futtatását. Ezeknek az eseményeknek a kezeléséhez használhat magasabb szintű teljesítményt, ha szüksége van rá. Ezután visszatérhet az eredeti szintjéhez, ha már nincs szüksége a további teljesítményre.

## <a name="how-it-works"></a>Működés

Amikor először telepít vagy kiépít egy lemezt, a lemez alapteljesítményi szintje a kiosztott lemez mérete alapján van beállítva. A magasabb szintű igények kielégítéséhez magasabb teljesítményszint is használható. Ha már nincs szüksége erre a teljesítményre, visszatérhet a kezdeti teljesítmény szintjéhez.

A számlázási változások a rétegek változásakor változnak. Ha például kiépít egy P10-lemezt (128 GiB), az alapteljesítmény szintje P10 (500 IOPS és 100 MBps) van beállítva. A díjat a P10 díjszabása alapján számítjuk fel. A szintet úgy frissítheti, hogy az megfeleljen a P50 teljesítményének (7 500 IOPS és 250 MBps) a lemez méretének növelése nélkül. A frissítés ideje alatt a P50 díjszabása alapján számítjuk fel a díjat. Ha már nincs szüksége a magasabb teljesítményre, visszatérhet a P10 szintjéhez. A lemez újbóli számlázása a P10 arányban történik.

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
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | Nincs |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 tebibájt | P80 | Nincs |

Számlázási információk: a [felügyelt lemez díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Korlátozások

- Ez a funkció jelenleg csak prémium SSD-k esetén támogatott.
- A lemez szintjeinek megváltoztatásához le kell szabadítania a virtuális gépet, vagy le kell választania a lemezt egy futó virtuális gépről.
- A P60, a P70 és a P80 teljesítmény-szintjeinek használata a 4 096 GiB vagy újabb lemezekre korlátozódik.
- A lemez teljesítményi szintje csak 24 óránként lehet visszaminősíthető.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A felügyelt lemezek teljesítményi szintjének módosítása jelenleg csak a prémium szintű SSD-k esetében érhető el az USA 2. keleti régiójában, az USA déli középső régiójában, az USA nyugati középső régiójában, Dél-Ausztrália keleti régiójában.

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

Ha át kell méreteznie egy lemezt a magasabb teljesítményszint kihasználásához, tekintse meg a következő cikkeket:

- [Virtuális merevlemezek kibontása Linux rendszerű virtuális GÉPEN az Azure CLI-vel](linux/expand-disks.md)
- [Windows rendszerű virtuális géphez csatolt felügyelt lemez kibontása](windows/expand-os-disk.md)
