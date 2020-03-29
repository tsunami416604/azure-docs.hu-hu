---
title: Virtuális gépek azonnali kvótája – Azure
description: Növelje a kvótakorlátokat a direkt virtuális gépekhez, amelyek az Azure-használat modelljét biztosítják, amely lehetővé teszi, hogy alacsonyabb költségeket vállaljon, ha az Azure szükség szerint eltávolítja a virtuális gépeket.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 3e6dddfc458f390f5648e23a8a2af8d714de4bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842780"
---
# <a name="spot-quota-increase-limits-for-all-vm-series"></a>Direktszínkvóta: Az összes virtuálisgép-sorozat ra vonatkozó határértékek növelése

A virtuális gépek (VM-ek) az Azure-használat egy másik modelljét biztosítják. Lehetővé teszik, hogy alacsonyabb költségeket feltételezhet, ha az Azure lehetővé teszi, hogy a használatalapú fizetéshez vagy a fenntartott virtuálisgép-példányok üzembe helyezéséhez szükség szerint távolítsa el a virtuális gépeket. A virtuális gépekről további információt az [Azure azonnali virtuális gépekkel a virtuálisgép-méretezési csoportokról](../../virtual-machine-scale-sets/use-spot.md)című témakörben talál.

Az Azure Resource Manager kétféle vCPU-kvótát támogat a virtuális gépekhez:

* *Használatalapú virtuális gépek* re és a lefoglalt *virtuálisgép-példányokra* *szabványos vCPU-kvóta*vonatkozik.
* *A direkt virtuális gépekre* *azonnali vCPU-kvóta vonatkozik.*

A direkt vCPU-kvótatípus esetén az Erőforrás-kezelő vCPU-kvóták egyetlen regionális korlátként az összes elérhető virtuálisgép-sorozatban érvénybe lépnek.

Amikor üzembe helyez egy új virtuális gép egy új direktvirtuális gép, a teljes új és a meglévő vCPU-használat az összes direkt virtuálisgép-példányok nem haladhatja meg a jóváhagyott azonnali vCPU kvótakorlátot. Ha a direktszínkvóta túllépi, a direkt virtuális gép üzembe helyezése nem engedélyezett.

Ez a cikk ismerteti, hogyan kérheti a helyszíni vCPU kvótakorlát az Azure Portal használatával.

A szabványos vCPU-kvótákról a [Virtuálisgép vCPU-kvóták](../../virtual-machines/windows/quotas.md) és [az Azure előfizetési és szolgáltatáskorlátok, kvóták és korlátozások.](../../azure-resource-manager/management/azure-subscription-service-limits.md)

A vCPU-korlát régiónkénti növeléséről a [Szokásos kvóta: Korlátok növelése régiónként](regional-quota-requests.md)című témakörben olvashat.

## <a name="request-a-quota-limit-increase-from-help--support"></a>Kvótakorlát-növelés kérése a Súgó + támogatás szolgáltatásból

Azonnali kvótakorlát-növelés kérése az összes virtuálisgép-sorozatra a **Súgó + támogatás**használatával:

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

   1. A **Központi telepítési modell**hez válassza ki a megfelelő modellt, a **Helyek**esetében pedig válassza ki a helyet.

      ![További kvótaadatok megadásáról](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. A kijelölt hely **típusai csoportjában**válassza a **Típus kiválasztása csoportban**a **Direktszín**lehetőséget.

      ![Ponttípus kiválasztása](./media/resource-manager-core-quotas-request/select-spot-type.png)

       A **Típusok csoportban**a szabványos és a direktkvóta-típusokat egyetlen támogatási esettől a többszörös kijelölésen keresztül is kérheti.

       További információ: [Standard kvóta: Limitek növelése virtuálisgép-sorozat szerint.](per-vm-quota-requests.md)

   1. Adja meg az előfizetéshez szükséges új kvótakorlátot.

      ![Új kvóta kiválasztása a direktvirtuális géphez](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Ha egynél több hely kvótanövelését szeretné kérni, jelöljön ki egy további helyet a **Helyek területen,** majd válassza ki a megfelelő virtuális géptípust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További helyek megadása a kvóta részleteiben](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a **Mentés gombot, és folytassa** a támogatási kérelem létrehozásának folytatását.

## <a name="request-a-quota-limit-increase-from-the-subscriptions-pane"></a>Kvótakorlát-növelés kérése az Előfizetések ablaktábláról

Azonnali kvótakorlát-növelés kérése az összes virtuálisgép-sorozatra az **Előfizetések** ablaktábláról:

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

1. Válassza a **Tovább: Megoldások lehetőséget** a **PROBLÉMA RÉSZLETEI**megnyitásához. További információk megadásához válassza a **Részletek** megadása lehetőséget. A **Kvóta részletei mezőbe**írja be a következő adatokat:

   1. A **Központi telepítési modell**hez válassza ki a megfelelő modellt, a **Helyek**esetében pedig válassza ki a helyet.

      ![Kvóta részleteinek megadásáról](./media/resource-manager-core-quotas-request/quota-details-deployment-locations.png)

   1. A kijelölt hely **típusai csoportjában**válassza a **Típus kiválasztása csoportban**a **Direktszín**lehetőséget.

      ![Direktszín típus ának kiválasztása](./media/resource-manager-core-quotas-request/select-spot-type.png)

      További információ: [Standard kvóta: Limitek növelése virtuálisgép-sorozat szerint.](per-vm-quota-requests.md)

   1. Adja meg az előfizetéshez szükséges új kvótakorlátot.

      ![Új érték megadása a vCPU-korláthoz](./media/resource-manager-core-quotas-request/spot-set-new-quota.png)

1. Ha egynél több hely kvótanövelését szeretné kérni, jelöljön ki egy további helyet a **Helyek területen,** majd válassza ki a megfelelő virtuális géptípust. Ezután megadhat egy korlátot, amely a további helyre vonatkozik.

   ![További helyek kiválasztása a kvóta részleteiben](./media/resource-manager-core-quotas-request/quota-details-multiple-locations.png)

1. Válassza a **Mentés gombot, és folytassa** a támogatási kérelem létrehozásának folytatását.
