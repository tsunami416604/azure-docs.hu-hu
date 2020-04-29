---
title: Az Azure spot-alapú virtuális gépek és a méretezési csoportok példányainak hibakódai
description: Ismerje meg, hogy milyen hibakódok jelenhetnek meg a helyszínen futó virtuális gépek és a méretezési csoport példányainak használatakor.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 5a34dc2b9468c6c5af4af0e0addfd8b9ebb7e792
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80547816"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>A helyszíni virtuális gépekhez és a méretezési csoportokhoz tartozó hibaüzenetek

Íme néhány lehetséges hibakód, amelyet a helyszíni virtuális gépek és a méretezési csoportok használatakor kaphat.


| Kulcs | Üzenet | Leírás |
|-----|---------|-------------|
| SkuNotAvailable | Az erőforrás\<"erőforrás\>" kért szintje jelenleg nem érhető el a (z)\<\>\<subscriptionID\>előfizetés "location" helyén. Próbálkozzon másik szinten, vagy helyezzen üzembe egy másik helyet. | Ezen a helyen nincs elegendő Azure-beli helyszíni kapacitás a virtuális gép vagy a méretezési csoport példányának létrehozásához. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A kizárási szabályzat csak az Azure spot Virtual Machines állítható be. | Ez a virtuális gép nem egy helyszíni virtuális gép, ezért nem állíthatja be a kizárási szabályzatot. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Az Azure spot virtuális gép nem támogatott a rendelkezésre állási csoporton belül. | Válasszon egy helyszíni virtuális gépet, vagy használjon egy virtuális gépet egy rendelkezésre állási csoportban, nem választhat mindkettőt. |
| AzureSpotFeatureNotEnabledForSubscription  |  Az előfizetés nincs engedélyezve az Azure spot szolgáltatással. | Használjon olyan előfizetést, amely támogatja a helyszíni virtuális gépeket. |
| VMPriorityCannotBeApplied  |  A megadott prioritási érték{0}("") nem alkalmazható a ({1}z) "" virtuális gépre, mert nem lett megadva prioritás a virtuális gép létrehozásakor. | A virtuális gép létrehozásakor válassza ki a prioritást. |
| SpotPriceGreaterThanProvidedMaxPrice  |  A (z) "{0}" művelet nem hajtható végre, mert{1} a megadott "USD" maximális ár alacsonyabb, mint{2} a jelenlegi "USD" ár az Azure{3}spot VM-méretnél (). | Válasszon magasabb maximális árat. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)díjszabási információi.|
| MaxPriceValueInvalid  |  A maximális ár értéke érvénytelen. A maximális ár értéke csak-1, a nullánál nagyobb tizedes tört. A-1 érték maximális értéke azt jelzi, hogy az Azure spot virtuális gép árát nem lehet kizárni az árak miatt. | Érvényes maximális árat adjon meg. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)díjszabása. |
| MaxPriceChangeNotAllowedForAllocatedVMs | A maximális árváltozás nem engedélyezett, ha a (z{0}) "" virtuális gép jelenleg le van foglalva. Szabadítson fel és próbálkozzon újra. | Stop\Deallocate a virtuális gépet, hogy meg lehessen változtatni a maximális árat. |
| MaxPriceChangeNotAllowed | Az árváltozás maximális értéke nem engedélyezett. | A virtuális gép maximális ára nem módosítható. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Az Azure spot nem támogatott ehhez az API-verzióhoz. | Az API-verziót 2019-03-01-re kell állítani. |
| AzureSpotIsNotSupportedForThisVMSize  |  Az Azure spot nem támogatott ehhez a virtuálisgép- {0}mérethez. | Válasszon másik virtuális gép méretét. További információ: [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  A maximális díj csak az Azure spot Virtual Machines esetén támogatott. | További információ: [Spot Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Az erőforrások áthelyezése kérelem egy Azure-beli helyszíni virtuális gépet tartalmaz. Ez jelenleg nem támogatott. Tekintse meg a virtuális gépek azonosítóinak részleteit. | A Direktszínű virtuális gépek nem helyezhetők át. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Az erőforrások áthelyezése kérelem egy Azure-beli virtuálisgép-méretezési készletet tartalmaz. Ez jelenleg nem támogatott. Tekintse meg a virtuálisgép-méretezési csoport azonosítóinak részleteit. | A direktszín-méretezési csoport példányai nem helyezhetők át. |
| EphemeralOSDisksNotSupportedForSpotVMs | Az ideiglenes operációsrendszer-lemezek nem támogatottak a helyszíni virtuális gépek esetében. | Használjon normál operációsrendszer-lemezt a helyszíni virtuális géphez. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A virtuálisgép-méretezési csoport virtuálisgép-méretezési módban nem támogatja az Azure-beli helyszíni virtuális gépeket. | A hangelőkészítési módot állítsa be a virtuálisgép-méretezési csoportba a direktszínes példányok használatához. |


**További lépések** További információ: [spot Virtual Machines](./linux/spot-vms.md).