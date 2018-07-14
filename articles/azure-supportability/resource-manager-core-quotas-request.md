---
title: Az Azure Resource Manager vCPU-kvóta növeléséhez kérelmek |} A Microsoft Docs
description: Az Azure Resource Manager vCPU-kvóta növelésére irányuló kérelmekről
author: ganganarayanan
ms.author: gangan
ms.date: 6/13/2018
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7456785815dbefb2436713814965d90ba0e789ee
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39037269"
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Erőforrás-kezelő vCPU-kvóta növelésére irányuló kérelmekről

Erőforrás-kezelő vCPU-kvóták a rendszer érvényesíti a régió szintjén, és a termékcsalád Termékváltozat-szint.
Tudjon meg többet a módját a kvóták érvényesítése történik meg a [Azure-előfizetés- és Szolgáltatáskorlátok](http://aka.ms/quotalimits) lapot.
Termékváltozat-családokra tájékozódhat, előfordulhat, hogy összehasonlítja költség- és a a [Virtual Machines díjszabása](http://aka.ms/pricingcompute) lapot.

Ha szeretné növelni, alatt látható részletes utasítások segítségével hozzon létre egy támogatási kérést az Azure "használati + kvóta" keresztül az Azure portálon elérhető panelje. 

## <a name="request-quota-increase-at-subscription-level"></a>Kérje a kvóta növelését előfizetési szinten

1. A https://portal.azure.comválassza **előfizetések**.

   ![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelyhez megemelt kvótához kell.

   ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Válassza ki **használat + kvóták**

   ![Válassza ki a használat és kvóták](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban, válassza ki **növelésére**.

   ![Növelés kérése](./media/resource-manager-core-quotas-request/request-increase.png)

5. 1. lépés: - válassza **magok** ajánlat típusaként. 

   ![Űrlap kitöltése](./media/resource-manager-core-quotas-request/forms.png)
   
6. 2. lépés: – válassza ki a központi telepítés, a "Resource Manager" modellezheti, és válasszon egy helyet.

    ![Kvóta probléma panel](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Válassza ki a Termékváltozat-családokra igénylő növelését.

    ![Kiválasztott Termékváltozat-sorozatok](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Adja meg az új korlátok szeretne az előfizetés.

    ![Termékváltozat új kvótakérelem](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- A sor eltávolításához törölje a jelet a Termékváltozat a Termékváltozat-család legördülő listából, vagy kattintson az elvetési "x" ikont.
Miután megadta a kívánt kvótát az egyes Termékváltozat-család, kattintson a Next (Tovább) a támogatási kérelem létrehozásának folytatása probléma lépés oldalon található.

