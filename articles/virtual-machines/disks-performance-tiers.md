---
title: Az Azure Managed Disk teljesítményének növelése
description: Ismerje meg a felügyelt lemezek teljesítményi szintjeit, valamint a felügyelt lemezek teljesítményi szintjeinek frissítését.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974139"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>A felügyelt lemezek teljesítményi szintjei (előzetes verzió)

Azure Disk Storage jelenleg beépített kitörési képességekkel rendelkezik, amelyek nagyobb teljesítményt biztosítanak a rövid távú váratlan forgalom kezeléséhez. A prémium SSD-k rugalmasan növelhetik a lemez teljesítményét a lemez tényleges méretének növelése nélkül, ami lehetővé teszi a számítási feladatok teljesítményének kielégítését és a költségek csökkentését, ez a funkció jelenleg előzetes verzióban érhető el. Ez ideális olyan eseményekhez, amelyek átmenetileg igénylik a magasabb szintű teljesítményt, például az üdülési vásárlást, a teljesítménytesztet vagy a képzési környezet futtatását. Ezeknek az eseményeknek a kezeléséhez a szükségesnél magasabb szintű teljesítményt választhat, és visszatérhet az eredeti szintjére, ha a további teljesítmény már nem szükséges.

## <a name="how-it-works"></a>Működés

Amikor először telepít vagy kiépít egy lemezt, a lemez alapteljesítményi szintje a kiosztott lemez mérete alapján van beállítva. Magasabb teljesítményszint kiválasztható úgy, hogy magasabb szintű igényt lehessen kielégíteni, és ha ez a teljesítmény már nem szükséges, visszatérhet a kezdeti alapteljesítményi szintjéhez. Ha például kiépít egy P10-lemezt (128 GiB), az alapkonfigurációi teljesítményszint P10 (500 IOPS és 100 MB/s) van beállítva. A szintet úgy frissítheti, hogy az megfeleljen a P50 (7500 IOPS és 250 MB/s) teljesítményének, a lemez méretének növelése nélkül, és térjen vissza a P10, ha a nagyobb teljesítmény már nem szükséges.

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

## <a name="restrictions"></a>Korlátozások

- Jelenleg csak a prémium SSD-k támogatottak.
- A rétegek módosítása előtt le kell választani a lemezeket egy futó virtuális gépről.
- A P60, a P70 és a P80 teljesítmény-szintjeinek használata a 4096 GiB vagy újabb lemezekre korlátozódik.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

A felügyelt lemezek teljesítményi szintjének módosítása jelenleg csak a prémium szintű SSD-k számára érhető el a következő régiókban:

- USA nyugati középső régiója 
- 2. Kelet-Egyesült Államok 
- Nyugat-Európa
- Kelet-Ausztrália 
- Dél-Kelet-Ausztrália 
- Dél-India

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Adatlemez létrehozása/frissítése az alapcsomagnál magasabb szinten

1. Hozzon létre egy üres adatlemezt, amely magasabb szintű, mint az alapcsomag, vagy frissítse az alapszinten magasabb lemez szintjét a minta sablon [CreateUpdateDataDiskWithTier.js](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. A lemez szintjeinek megerősítése

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>OPERÁCIÓSRENDSZER-lemez létrehozása/frissítése az alapszintűnél magasabb szintűként

1. Hozzon létre egy operációsrendszer-lemezt a piactér rendszerképből, vagy frissítse az alapszinten magasabb operációsrendszer-lemez szintjét a minta sablonnal [CreateUpdateOSDiskWithTier.js](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. A lemez szintjeinek megerősítése
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Következő lépések

Ha át kell méreteznie egy lemezt, hogy kihasználhassa a nagyobb teljesítményszint előnyeit, tekintse meg a tárgyban található cikkeket:

- [Virtuális merevlemezek kibontása Linux rendszerű virtuális GÉPEN az Azure CLI-vel](linux/expand-disks.md)
- [Windows rendszerű virtuális géphez csatolt felügyelt lemez kibontása](windows/expand-os-disk.md)