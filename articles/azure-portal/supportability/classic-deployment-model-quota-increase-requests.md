---
title: Klasszikus Azure-telepítési modell
description: A klasszikus üzembe helyezési modell, amelyet most az Erőforrás-kezelő modell helyettesít, globális vCPU kvótakorlátot kényszerít ki a virtuális gépekre és a virtuális gép méretezési csoportjaira.
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: a3d5106cafc1d3bfe77f3e42e85cedb668fc4fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76835614"
---
# <a name="classic-deployment-model"></a>Klasszikus üzemi modell

A klasszikus üzembe helyezési modell a régebbi generációs Azure-telepítési modell. Globális vCPU kvótakorlátot kényszerít ki a virtuális gépekre és a virtuálisgép-méretezési csoportokra. A klasszikus üzembe helyezési modell már nem ajánlott, és most az Erőforrás-kezelő modell felváltotta.

A két telepítési modellről és az Erőforrás-kezelő használatának előnyeiről az [Erőforrás-kezelő és](../../azure-resource-manager/management/deployment-models.md)a klasszikus telepítés című témakörben olvashat bővebben.

Új előfizetés létrehozásakor a vCPU-k alapértelmezett kvótája hozzá van rendelve. Bármikor, amikor egy új virtuális gép kell telepíteni a klasszikus üzembe helyezési modell használatával, az összeg az új és a meglévő vCPU-használat az összes régióban nem haladhatja meg a klasszikus üzembe helyezési modell jóváhagyott vCPU-kvótát.

A kvótákról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../../azure-resource-manager/management/azure-subscription-service-limits.md)témakörben olvashat bővebben.

Kérheti a vCPU kvótakorlát ának növelését a klasszikus üzembe helyezési modellhez. Használja a **Súgó + támogatás** vagy a Használat + **kvóták az** Azure Portalon.

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-help--support"></a>Kérelem virtuálisgép-sorozatú vCPU-kvótanövelésére előfizetési szinten a Súgó + támogatás használatával

Kövesse az alábbi utasításokat, hogy hozzon létre egy támogatási kérelmet az Azure **Portalsúgó + támogatás** használatával.

1. Az [Azure Portal](https://portal.azure.com) menüben válassza a **Súgó + támogatás**lehetőséget.

   ![Válassza a Súgó + támogatás lehetőséget az Azure Portalon](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. Válassza **az Új támogatási kérelem lehetőséget.**

   ![Új támogatási kérelem létrehozása az Azure Portalon](./media/resource-manager-core-quotas-request/new-support-request.png)

1. A **Probléma típusa mezőben**válassza **a Szolgáltatás- és előfizetési korlátok (kvóták)** lehetőséget.

   ![Kvóták kiválasztása a probléma típusaként](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![Válassza ki az előfizetést, amelynek kvótáját növelni szeretné](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. Kvótatípus **esetén**válassza **a Számítási -VM (magok-vCPU) előfizetési korlát növelését**lehetőséget.

   ![A növelni kívánt kvótatípus kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Válassza a **Tovább: Megoldások lehetőséget** a **PROBLÉMA RÉSZLETEI**megnyitásához. További információk megadásához válassza a **Részletek** megadásával lehetőséget.

   ![Adja meg a részleteket, hogy segítse a kérelmet mentén](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. A **Kvóta részletei ben**válassza a **Klasszikus** lehetőséget, és válasszon egy **helyet.**

   ![Részletek hozzáadása, például a telepítési modell és a hely](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. **Termékváltozat-család**esetén válasszon ki egy vagy több növelni kívánt Termékváltozat-családot.

   ![A növelni kívánt Termékváltozat család megadása](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Adja meg az előfizetésben az új korlátokat. Vonal eltávolításához szüntesse meg a termékváltozat kijelölésének megszüntetését a **Termékváltozat családból,** vagy válassza az "X" ikont. Miután megadta az egyes termékváltozat-családokra vonatkozó kvótát, válassza a **Mentés és folytatás** a kvóta **részleteiben** lehetőséget a támogatási kérelem folytatásához.

   ![Új korlátok kérése](./media/resource-manager-core-quotas-request/new-limits-classic.png)

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usage--quotas"></a>Kérelem virtuálisgép-sorozatú vCPU-kvótanövelésére előfizetési szinten használat + kvóták használatával

Kövesse az alábbi utasításokat, hogy hozzon létre egy támogatási kérelmet az Azure Portalon **a Használat + kvóták** használatával.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Előfizetések lehetőséget, és válassza az **Előfizetések**lehetőséget.

   ![Ugrás az Előfizetések az Azure Portalon](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. Válassza ki azt az előfizetést, amelynek a kvótáját növelni szeretné.

   ![A módosítandó előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. Válassza **a Felhasználás + kvóták lehetőséget.**

   ![Előfizetés használatának és kvótáinak kiválasztása](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. A jobb felső sarokban válassza a **Kérelem növelése lehetőséget.**

   ![Jelölje be a kvóta növeléséhez.](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. Válassza **a Compute-VM (cores-vCPU) előfizetési korlát növekedését** a **kvótatípussal.**

   ![Kvótatípus kiválasztása](./media/resource-manager-core-quotas-request/select-quota-type.png)

1. Válassza a **Tovább: Megoldások lehetőséget** a **PROBLÉMA RÉSZLETEI**megnyitásához. További információk megadásához válassza a **Részletek** megadásával lehetőséget.

   ![Adja meg a kérés részleteit](./media/resource-manager-core-quotas-request/provide-details-link.png)

1. A **Kvóta részletei ben**válassza a **Klasszikus** és a **Hely lehetőséget.**

   ![Kvótarészletek kiválasztása, beleértve a telepítési modellt és a helyet](./media/resource-manager-core-quotas-request/quota-details-classic.png)

1. Jelöljön ki egy vagy több Termékváltozat-családot növekedéshez.

   ![A termékváltozat család kiválasztása a növeléshez](./media/resource-manager-core-quotas-request/sku-family-classic.png)

1. Adja meg az előfizetésben az új korlátokat. Vonal eltávolításához szüntesse meg a termékváltozat kijelölésének megszüntetését a **Termékváltozat családból,** vagy válassza az "X" ikont. Miután megadta az egyes termékváltozat-családokra vonatkozó kvótát, válassza a **Mentés és folytatás** a kvóta **részleteiben** lehetőséget a támogatási kérelem folytatásához.

   ![Új kvóta megadása](./media/resource-manager-core-quotas-request/new-limits-classic.png)

