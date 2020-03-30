---
title: Hibakódok az Azure Spot virtuális gépekhez és a méretezési csoportok példányaihoz
description: Ismerje meg a hibakódokat, amelyeket a direkt virtuális gépek használatakor és a méretezési példányok használatakor esetleg láthat.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/20/2019
ms.author: cynthn
ms.openlocfilehash: 1863c510967dcf80044d768aee1a37643a068ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115852"
---
# <a name="preview-error-messages-for-spot-vms-and-scale-sets"></a>Előzetes verzió: Hibaüzenetek direkt virtuális gépekhez és méretezési csoportokhoz


> [!IMPORTANT]
> A direkt virtuális gépek és a virtuálisgép-méretezési csoportok jelenleg nyilvános előzetes verzióban vannak.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)


Íme néhány lehetséges hibakód, amelyet a direkt virtuális gépek és a méretezési készletek használatakor kaphat.


| Kulcs | Üzenet | Leírás |
|-----|---------|-------------|
| SkuNotAvailable | Az\<erőforrás " erőforrás\>" kért szintje jelenleg\<\>nem érhető\<el a hely " hely " helyen " az előfizetési " előfizetési azonosítóhoz".\> Próbálkozzon egy másik szinttel, vagy telepítse egy másik helyre. | Nincs elég Azure Spot kapacitás ezen a helyen a virtuális gép vagy a méretezési csoport példányának létrehozásához. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  A kilakoltatási szabályzat csak az Azure Spot virtuális gépeken állítható be. | Ez a virtuális gép nem egy direkt virtuális gép, így nem állíthatja be a kilakoltatási szabályzatot. |
| AzureSpotVMNotSupportedinavailabilityset  |  Az Azure Spot virtuális gép nem támogatott a rendelkezésre állási készlet. | Meg kell választania, hogy egy direkt virtuális gép, vagy egy virtuális gép egy rendelkezésre állási csoportban, nem választhatja mindkettőt. |
| Az AzureSpotfeaturenotEnabledForSubscription  |  Az előfizetés nincs engedélyezve az Azure Spot funkcióval. | Olyan előfizetéssel kell rendelkeznie, amely támogatja a direkt virtuális gépeket. |
| VMPriorityCannotBeAalkalmazott  |  A megadott prioritási{0}érték ' ' nem alkalmazható{1}a virtuális gépre ' ' , mivel a virtuális gép létrehozásakor nem adott meg prioritást. | Meg kell adnia a prioritást, amikor a virtuális gép jön létre. |
| SpotPriceGreaterThanProvidedmaxPrice  |  Nem hajtható{0}végre a ' "{1} művelet, mivel a megadott{2} max ár ' USD'{3}alacsonyabb, mint az Azure Spot VM méretének ' aktuális azonnali ára ' USD'. | Válasszon magasabb maximális árat. További információt a [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) vagy Windows díjszabási információi című témakörben [talál.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)|
| MaxPriceValueInvalid  |  Érvénytelen maximális árérték. A maximális ár csak -1 vagy decimálisan nagyobb érték, mint nulla. A -1 maximális árazt a értéket jelzi, hogy az Azure Spot virtuális gép árokok miatt nem lesz kizárva. | Adjon meg érvényes maximális árat. További információ: Pricing for [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) or [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVms | A maximális árváltozás nem engedélyezett,{0}ha a virtuális gép ' ' jelenleg le van foglalva. Szabadítsa fel a felszabadítást, majd próbálkozzon újra. | Stop\A virtuális gép felszabadítása, hogy módosíthatja a maximális árat. |
| MaxPriceChangeNemengedélyezett | A maximális árváltozás nem megengedett. | A virtuális gép maximális ára nem módosítható. |
| Az AzureSpotnotSupportedForTHISAPIVersion  |  Az Azure Spot nem támogatott ebben az API-verzióban. | Az API-verziónak 2019-03-01-nek kell lennie. |
| Az AzureSpotnemtámogatottthisVmSize esetén  |  Az Azure Spot nem támogatott {0}ebben a virtuális gép méretben. | Válasszon másik virtuális gép mérete. További információ: [Spot Virtual Machines](./linux/spot-vms.md). |
| MaxPriceIsTámogatottcsakazurespotvirtualmachines  |  A maximális ár csak az Azure Spot virtuális gépek esetében támogatott. | További információ: [Spot Virtual Machines](./linux/spot-vms.md). |
| Erőforrások áthelyezéseazurespotvmnottámogatott  |  Az erőforrások áthelyezése kérelem tartalmaz egy Azure Spot virtuális gép. Ez jelenleg nem támogatott. Ellenőrizze a virtuális gépazonosítók hibarészleteit. | A direkt virtuális gépek nem helyezhető át. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  Az erőforrások áthelyezése kérelem egy Azure Spot virtuálisgép-méretezési készletet tartalmaz. Ez jelenleg nem támogatott. Ellenőrizze a virtuálisgép-méretezési készlet azonosítóinak hibarészleteit. | A Direktszín méretezési példányait nem helyezheti át. |
| EphemeralOSDisksNotSupportedForSpotVMs | Az ideiglenes operációsrendszer-lemezek nem támogatottak a direkt virtuális gépek számára. | Normál operációsrendszer-lemezt kell használnia a direkt virtuális géphez. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | Az Azure Spot virtuális gép nem támogatott virtuálisgép-vezénylési móddal. | Állítsa be a vezénylési módot a virtuálisgép-méretezési készletre a Direktszínpéldányok használatához. |


**Következő lépések** További információ: [spot Virtual Machines](./linux/spot-vms.md).