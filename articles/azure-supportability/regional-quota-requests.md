---
title: Az Azure regionális kvótáját növelheti a kérések |} A Microsoft Docs
description: Regionális kvóta növelésére irányuló kérelmekről
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 7ac6dce9aec1c363fa9772caabad9ce542d43888
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310653"
---
# <a name="total-regional-vcpu-limit-increase"></a>A teljes regionális vCPU-korlát növelése 

Virtuális gépek és virtuálisgép-méretezési csoportok erőforrás-kezelő vCPU-kvóták kényszerítettek, az egyes előfizetésekhez, minden régióban két szinten. 

Az első szintje a **teljes regionális vcpu-k korlát** (között az összes Virtuálisgép-sorozatok), és a második réteg a **Virtuálisgép-sorozatok / vcpu-k korlátozza** (például a D-sorozat vcpu-k). A Virtuálisgép-sorozat az új és meglévő Vcpu használati összege egy új virtuális Gépet, hogy kell telepíteni, amikor nem haladhatja meg a vCPU-kvóta az adott virtuális gép adatsor jóváhagyott. További az összes meglévő és új vCPU-számot telepített összes Virtuálisgép-sorozatok nem haladhatja meg a jóváhagyott az előfizetéshez tartozó összes regionális vcpu-k kvótáját. Ha ezeket a kvótákat valamelyikét túllépése esetén a virtuális gép üzembe helyezése nem engedélyezett lesz.
A Virtuálisgép-sorozatok vcpu-k kvótakorlátozását növekedést is kérhető az Azure Portalról. A Virtuálisgép-sorozatok kvóta emelése automatikusan megnövelje a teljes regionális vcpu-k korlátot is ugyanilyen mértékben csökken. 

Ha egy új előfizetés jön létre, az alapértelmezett teljes regionális vcpu-k nem lehet alapértelmezett vCPU-kvóták minden egyes Virtuálisgép-sorozatok összege. Emiatt egy előfizetésben, de a teljes regionális vcpu-k az összes üzemelő példányhoz nincs elegendő kvótája elegendő kvótával, minden egyes Virtuálisgép-sorozatot, amely számára telepíteni kívánja. Ebben az esetben kell igényelnie explicit módon teljes regionális vcpu-k korlát növelését. Regionális vcpu-k teljes korlát nem lehet hosszabb jóváhagyott kvóta összege az összes Virtuálisgép-sorozatok a régió között.

A kvóták tájékozódhat a [virtuális gép vCPU-kvóták lap](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és [Azure-előfizetés- és Szolgáltatáskorlátok](https://aka.ms/quotalimits) lap. 

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



