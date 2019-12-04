---
title: Az Azure spot-alapú virtuális gépek és a méretezési csoportok példányainak hibakódai
description: Ismerje meg, hogy milyen hibakódok jelenhetnek meg a helyszínen futó virtuális gépek és a méretezési csoport példányainak használatakor.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 97e68f4d0d671ffa0f697c484c502b9070a3f20f
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74781942"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>Előzetes verzió: a helyszíni virtuális gépekhez és a méretezési csoportokhoz tartozó hibaüzenetek


> [!IMPORTANT]
> A helyszíni virtuális gépek és a virtuálisgép-méretezési csoportok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Íme néhány lehetséges hibakód, amelyet a helyszíni virtuális gépek és a méretezési csoportok használatakor kaphat.


| Jelmagyarázat | Üzenet | Leírás |
|-----|---------|-------------|
| SkuNotAvailable | A (z) "\<Resource\>" erőforrás kért szintje jelenleg nem érhető el a (z) "\<Location\>" helyen a (z)\<subscriptionID\>előfizetéshez. Próbálkozzon másik szinten, vagy helyezzen üzembe egy másik helyet. | Ezen a helyen nincs elegendő Azure-beli helyszíni kapacitás a virtuális gép vagy a méretezési csoport példányának létrehozásához. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A kizárási szabályzat csak az Azure spot Virtual Machines állítható be. | Ez a virtuális gép nem egy helyszíni virtuális gép, ezért nem állíthatja be a kizárási szabályzatot. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  Az Azure spot virtuális gép nem támogatott a rendelkezésre állási csoporton belül. | Válasszon egy helyszíni virtuális gépet, vagy használjon egy virtuális gépet egy rendelkezésre állási csoportban, nem választhat mindkettőt. |
| AzureSpotFeatureNotEnabledForSubscription  |  Az előfizetés nincs engedélyezve az Azure spot szolgáltatással. | Rendelkeznie kell egy olyan előfizetéssel, amely támogatja a helyszíni virtuális gépeket. |
| VMPriorityCannotBeApplied  |  A megadott prioritási érték ("{0}") nem alkalmazható a (z) "{1}" virtuális gépre, mert a virtuális gép létrehozásakor nem lett megadva prioritás. | A virtuális gép létrehozásakor meg kell adnia a prioritást. |
| SpotPriceGreaterThanProvidedMaxPrice  |  A (z) "{0}" művelet nem hajtható végre, mert a megadott "{1} USD" maximális ár alacsonyabb, mint a jelenlegi "{2} USD" érték az Azure spot VM-méretnél ({3}). | Válasszon magasabb maximális árat. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)díjszabási információi.|
| MaxPriceValueInvalid  |  A maximális ár értéke érvénytelen. A maximális ár értéke csak-1, a nullánál nagyobb tizedes tört. A-1 érték maximális értéke azt jelzi, hogy az Azure spot virtuális gép árát nem lehet kizárni az árak miatt. | Érvényes maximális árat adjon meg. További információ: a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)díjszabása. |
| MaxPriceChangeNotAllowedForAllocatedVMs | A maximális árváltozás nem engedélyezett, ha a (z){0}virtuális gép jelenleg le van foglalva. Szabadítson fel és próbálkozzon újra. | Stop\Deallocate a virtuális gépet, hogy meg lehessen változtatni a maximális árat. |
| MaxPriceChangeNotAllowed | Az árváltozás maximális értéke nem engedélyezett. | A virtuális gép maximális ára nem módosítható. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Az Azure spot nem támogatott ehhez az API-verzióhoz. | Az API-verziót 2019-03-01-re kell állítani. |
| AzureSpotIsNotSupportedForThisVMSize  |  Az Azure spot nem támogatott ehhez a virtuálisgép-mérethez {0}. | Válasszon másik virtuális gép méretét. További információ: [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  A maximális díj csak az Azure spot Virtual Machines esetén támogatott. | További információ: [Spot Virtual Machines](./linux/spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  Az erőforrások áthelyezése kérelem egy Azure-beli helyszíni virtuális gépet tartalmaz. Ez jelenleg nem támogatott. Tekintse meg a virtuális gépek azonosítóinak részleteit. | A Direktszínű virtuális gépek nem helyezhetők át. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Az erőforrások áthelyezése kérelem Azure-beli virtuálisgép-méretezési készletet tartalmaz. Ez jelenleg nem támogatott. Tekintse meg a virtuálisgép-méretezési csoport azonosítóinak részleteit. | A direktszín-méretezési csoport példányai nem helyezhetők át. |
| EphemeralOSDisksNotSupportedForSpotVMs | Az ideiglenes operációsrendszer-lemezek nem támogatottak a helyszíni virtuális gépek esetében. | A helyszíni virtuális gép normál operációsrendszer-lemezét kell használnia. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | A virtuálisgép-méretezési csoport virtuálisgép-méretezési módban nem támogatja az Azure-beli helyszíni virtuális gépeket. | A hangelőkészítési módot állítsa be a virtuálisgép-méretezési csoportba a direktszínes példányok használatához. |


**További lépések** További információ: [spot Virtual Machines](./linux/spot-vms.md).