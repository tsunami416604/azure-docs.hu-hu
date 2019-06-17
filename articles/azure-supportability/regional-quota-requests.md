---
title: Az Azure regionális kvótáját növelheti a kérések |} A Microsoft Docs
description: Regionális kvóta növelésére irányuló kérelmekről
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 4c5524cf450959db7055ca5d032c81f79ebac077
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083079"
---
# <a name="total-regional-vcpu-limit-increase"></a>Teljes regionális vCPU-korlát növelésének 

Most már igényelhet növelését **súgó + támogatás** panel vagy a **Usages + kvóta** panel a portálon. 

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Kérelem előfizetési szinten az összes regionális vcpu-k kvótájának növelését a **súgó + támogatás** panel

Kövesse az alábbi utasításokat a hozzon létre egy támogatási kérést az Azure súgó + támogatás panelen elérhető az Azure Portalon keresztül. 

1. A https://portal.azure.com válassza **súgó + támogatás**.

![Súgó + támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő listában válassza ki a **szolgáltatás és az előfizetések korlátai (kvóták)** .

![Probléma típusa legördülő menü](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Válassza ki az előfizetést newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza ki **más kérelmek** a **kvótatípus** legördülő listából.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. A **részletek** panelen adjon meg a folyamat segítségével az alábbi példa szerint további információkat a kérést, és folytassa az eset létrehozása. 
    1.  **Üzemi modell** – adja meg az erőforrás-kezelő
    2.  **Kért régióban** – adja meg a szükséges régió, például: USA keleti RÉGIÓJA 2
    3.  **Új határérték érték** – adjon meg új régióban korlátot. Ez nem haladhatja meg a jóváhagyott kvóta esetében egyes Termékváltozat-családokra ehhez az előfizetéshez összege

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)

## <a name="request-total-regional-vcpus-quota-increase-at-subscription-level-using-the-usages--quota-blade"></a>Kérelem előfizetési szinten az összes regionális vcpu-k kvótájának növelését a **Usages + kvóta** panel

Kövesse az alábbi parancs használatával hozzon létre egy támogatási kérést az Azure "használati + kvóta" keresztül utasításokat az Azure-portálon elérhető panelje. 

1. A https://portal.azure.com válassza **előfizetések**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Válassza ki **használat + kvóták**

![Válassza ki a használat és kvóták](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban, válassza ki **növelésére**.

![Növelés kérése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Válassza ki **más kérelmek** a **kvótatípus** legördülő listából.

![QuotaType](./media/resource-manager-core-quotas-request/regional-quotatype.png)

6. A **részletek** panelen adjon meg a folyamat segítségével az alábbi példa szerint további információkat a kérést, és folytassa az eset létrehozása. 
    1.  **Üzemi modell** – adja meg az erőforrás-kezelő
    2.  **Kért régióban** – adja meg a szükséges régió, például: USA keleti RÉGIÓJA 2
    3.  **Új határérték érték** – adjon meg új régióban korlátot. Ez nem haladhatja meg a jóváhagyott kvóta esetében egyes Termékváltozat-családokra ehhez az előfizetéshez összege

![QuotaDetails](./media/resource-manager-core-quotas-request/regional-details.png)



