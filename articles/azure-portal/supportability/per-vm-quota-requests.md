---
title: A vCPU-kvótakorlátok növelésének kérése Az Azure Virtuálisgép-sorozatban
description: Hogyan kérheti a vCPU kvótakorlát ának növelését egy virtuálisgép-sorozathoz az Azure Portalon, amely ugyanazzal az összeggel növeli a teljes regionális vCPU-korlátot.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: de06375dad5999a29691435317e62585a2ea7f64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843729"
---
# <a name="standard-quota-increase-limits-by-vm-series"></a>Normál kvóta: A korlátok növelése virtuálisgép-sorozat szerint

Az Azure Resource Manager kétféle vCPU-kvótát támogat a virtuális gépekhez:

* *Használatalapú virtuális gépek* re és a lefoglalt *virtuálisgép-példányokra* *szabványos vCPU-kvóta*vonatkozik.
* *A direkt virtuális gépekre* *azonnali vCPU-kvóta vonatkozik.*

A használatalapú és a fenntartott virtuálisgép-példányok normál vCPU-kvótája az egyes régiók minden egyes előfizetéséhez két szinten van kényszerítve:

* Az első szint a *teljes regionális vCPU-k korlátját,* az összes virtuálisgép-sorozatban.
* A második szint a *vm-sorozatú vCPU-k korlátja,* például a Dv3 sorozatú vCPU-k.

Amikor üzembe helyez egy új virtuális gép azonnali virtuális gép, a teljes új és a meglévő vCPU-használat az adott virtuálisgép-sorozat nem haladhatja meg a jóváhagyott vCPU-kvótát az adott virtuálisgép-sorozat. Emellett az összes virtuálisgép-sorozatban üzembe helyezett új és meglévő vCPU-k teljes száma nem haladhatja meg az előfizetés teljes jóváhagyott regionális vCPU-kvótáját. Ha a kvóták bármelyikét túllépi, a virtuális gép központi telepítése nem engedélyezett.

Kérheti a vCPU kvótakorlát ának növelését a virtuális gép sorozataz Azure Portalhasználatával. A virtuális gép sorozat kvóta növekedése automatikusan növeli a teljes regionális vCPU-korlátot ugyanazzal az összeggel.

A szabványos vCPU-kvótákról a [Virtuálisgép vCPU-kvóták](../../virtual-machines/windows/quotas.md) és [az Azure előfizetési és szolgáltatáskorlátai](https://docs.microsoft.com/azure/azure-supportability/classic-deployment-model-quota-increase-requests)további információban olvashat.

A vCPU-korlát régiónkénti növeléséről a [Szokásos kvóta: Korlátok növelése régiónként](regional-quota-requests.md)című témakörben olvashat.

Ha többet szeretne megtudni a virtuális gép vCPU-korlátjának növeléséről, olvassa el a [Direktségkvóta: Az összes virtuálisgép-sorozat korlátozásainak növelése című témakört.](low-priority-quota.md)

A következő szakaszokban leírtak szerint kétféleképpen kérheti a virtuális gépsorozatonkénti szabványos vCPU-kvótakorlátok növelését.

## <a name="request-a-standard-quota-increase-from-help--support"></a>Standard kvótanövelés kérése a Súgó + támogatás szolgáltatásból

Szabványos vCPU kvótanövekedés kérése virtuálisgép-sorozatonként a **Súgó + támogatás**szolgáltatásból:

> [!NOTE]
> A kvótakorlát növelését is kérheti több régióban egyetlen támogatási eseten keresztül. A részleteket lásd a 8.

1. Az [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![A Súgó + támogatás hivatkozás](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. A **Súgó + támogatás**csoportban válassza az Új támogatási **kérelem**lehetőséget.

    ![Új támogatási kérelem létrehozása](./media/resource-manager-core-quotas-request/new-support-request.png)

1. A **Probléma típusmezőben**válassza a **Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.

   ![Problématípus kiválasztása](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. **Előfizetés esetén**válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![Előfizetés kiválasztása megnövelt kvótához](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Kvótatípus esetén válassza **a Compute-VM (cores-vCPU) előfizetési korlát növelését.** **Quota type**

   ![Kvótatípus kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Válassza a **Tovább: Megoldások lehetőséget** a **PROBLÉMA RÉSZLETEI**megnyitásához. További információk megadásához válassza a **Részletek** megadása lehetőséget.

   ![A "Részletek biztosítása" link](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. A **Kvóta részleteiben**tegye a következő lépéseket:

   ![TTovábbi kvótaadatok biztosítása](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. A **központi telepítési modellhez**válassza ki a megfelelő modellt.

   1. A **Helyek**területen válasszon ki egy helyet. A kijelölt hely **típusai csoportjában**válassza a **Típus kiválasztása csoportban**a **Szokásos lehetőséget.**

      ![Kvóta részletei - kvótatípusok](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      A **Típusok csoportban**a szabványos és a direktkvóta-típusokat egyetlen támogatási esettől a többszörös kijelölésen keresztül is kérheti.

      A direktszínkvóta-korlátok növeléséről további információt az [Azure azonnali virtuális gépekkel a virtuálisgép-méretezési csoportokhoz](../../virtual-machine-scale-sets/use-spot.md)című témakörben talál.

   1. A **Standard csoportban**válassza ki a termékváltozat-sorozatot a nagyobb kvótákhoz.

      ![Kvóta részletei - Termékváltozat-sorozat](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Adja meg az előfizetéshez szükséges új kvótakorlátokat. Ha el szeretne távolítani egy termékváltozatot a listából, törölje a jelet a termékváltozat melletti jelölőnégyzetből, vagy jelölje be az "X" ikont.

      ![Új vCPU-korlát kiválasztása](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Ha egynél több hely kvótanövelését szeretné kérni, jelöljön ki egy további helyet a **Helyek területen,** majd válassza ki a megfelelő virtuális géptípust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További helyek megadása a kvóta részleteiben](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a **Mentés gombot, és folytassa** a támogatási kérelem létrehozásának folytatását.

## <a name="request-a-standard-quota-increase-from-subscriptions"></a>Standard kvótanövelés kérése az Előfizetések szolgáltatásból

Szabványos vCPU kvótanövekedés kérése virtuálisgép-sorozatonként az **Előfizetések**szolgáltatásból:

> [!NOTE]
> A kvótakorlát növelését is kérheti több régióban egyetlen támogatási eseten keresztül. A részleteket lásd a 7.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Előfizetések lehetőséget, és válassza az **Előfizetések**lehetőséget.

   ![Előfizetések az Azure Portalon keresés](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![A módosításokhoz kiválasztandó előfizetések](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. A bal oldali ablaktáblában válassza a **Használat + kvóták**lehetőséget.

   ![A "Felhasználás + kvóták" hivatkozás](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **Kérelem növelése lehetőséget.**

   ![Jelölje be a kvóta növeléséhez.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Kvótatípus esetén válassza **a Compute-VM (cores-vCPU) előfizetési korlát növelését.** **Quota type**

   ![Kvótatípus kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. A **Kvóta részleteiben**tegye a következő lépéseket:

   1. A **Központi telepítési modell**hez válassza ki a megfelelő modellt, a **Helyek**esetében pedig válassza ki a helyet.

      ![Kvóta részleteinek megadásáról](./media/resource-manager-core-quotas-request/quota-details-deployment-rm-locations.png)

   1. A kijelölt hely **típusai csoportjában**válassza **a Típus kiválasztása**lehetőséget, majd a **Szokásos**lehetőséget.

      ![Szabványos típus kiválasztása](./media/resource-manager-core-quotas-request/quota-details-select-standard-type.png)

      A **Típusok csoportban**a szabványos és a direktkvóta-típusokat egyetlen támogatási esettől a többszörös kijelölésen keresztül is kérheti.

      A direktszínkvóta-korlátok növeléséről további információt az [Azure azonnali virtuális gépekkel a virtuálisgép-méretezési csoportokhoz](../../virtual-machine-scale-sets/use-spot.md)című témakörben talál.

   1. A **Normál**csoportban válassza ki azt a termékváltozat-sorozatot, amelynek kvótáit növelni szeretné.

      ![Kvóta részletei - Termékváltozat-sorozat](./media/resource-manager-core-quotas-request/quota-details-standard-select-series.png)

   1. Adja meg az előfizetéshez szükséges új kvótakorlátokat. Ha el szeretne távolítani egy termékváltozatot a listából, törölje a jelet a termékváltozat melletti jelölőnégyzetből, vagy jelölje be az "X" ikont.

      ![Új vCPU-korlát kijelölése](./media/resource-manager-core-quotas-request/quota-details-standard-set-vcpu-limit.png)

1. Ha egynél több hely kvótanövelését szeretné kérni, jelöljön ki egy további helyet a **Helyek területen,** majd válassza ki a megfelelő virtuális géptípust.

   Ez a lépés előre betölti a korábbi helyekhez kiválasztott termékváltozat-sorozatot. Adja meg a további adatsorokra alkalmazni kívánt kvótakorlátokat.

   ![További helyek kiválasztása a kvóta részleteiben](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a **Mentés gombot, és folytassa** a támogatási kérelem létrehozásának folytatását.
