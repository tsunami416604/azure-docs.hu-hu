---
title: Az Azure virtuális gépenként vCPU-kvóta növeléséhez kérelmek |} A Microsoft Docs
description: egy virtuális gép vCPU-kvóta növeléséhez a kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 79879cd7f5ea5af1b794735f32e6e1367458e124
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310723"
---
# <a name="vm-series-vcpu-limit-increase"></a>Virtuálisgép-sorozatok vCPU-korlátjának növelése

Virtuális gépek és virtuálisgép-méretezési csoportok erőforrás-kezelő vCPU-kvóták kényszerítettek, az egyes előfizetésekhez, minden régióban két szinten. 

Az első szintje a **teljes regionális vcpu-k korlát** (között az összes Virtuálisgép-sorozatok), és a második réteg a **Virtuálisgép-sorozatok / vcpu-k korlátozza** (például a D-sorozat vcpu-k). A Virtuálisgép-sorozat az új és meglévő Vcpu használati összege egy új virtuális Gépet, hogy kell telepíteni, amikor nem haladhatja meg a vCPU-kvóta az adott virtuális gép adatsor jóváhagyott. További az összes meglévő és új vCPU-számot telepített összes Virtuálisgép-sorozatok nem haladhatja meg a jóváhagyott az előfizetéshez tartozó összes regionális vcpu-k kvótáját. Ha ezeket a kvótákat valamelyikét túllépése esetén a virtuális gép üzembe helyezése nem engedélyezett lesz.
A Virtuálisgép-sorozatok vcpu-k kvótakorlátozását növekedést is kérhető az Azure Portalról. A Virtuálisgép-sorozatok kvóta emelése automatikusan megnövelje a teljes regionális vcpu-k korlátot is ugyanilyen mértékben csökken. 

Ha egy új előfizetés jön létre, az alapértelmezett teljes regionális vcpu-k nem lehet alapértelmezett vCPU-kvóták minden egyes Virtuálisgép-sorozatok összege. Emiatt egy előfizetésben, de a teljes regionális vcpu-k az összes üzemelő példányhoz nincs elegendő kvótája elegendő kvótával, minden egyes Virtuálisgép-sorozatot, amely számára telepíteni kívánja. Ebben az esetben kell igényelnie explicit módon teljes regionális vcpu-k korlát növelését. Regionális vcpu-k teljes korlát nem lehet hosszabb jóváhagyott kvóta összege az összes Virtuálisgép-sorozatok a régió között.

A kvóták tájékozódhat a [virtuális gép vCPU-kvóták lap](https://docs.microsoft.com/azure/virtual-machines/windows/quotas) és [Azure-előfizetés- és Szolgáltatáskorlátok](https://aka.ms/quotalimits) lap. 

Most már igényelhet növelését **súgó + támogatás** panel vagy a **Usages + kvóta** panel a portálon. 

## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Virtuálisgép-sorozatok vCPU kvótanövelést előfizetési szinten az egy kérelem a **súgó + támogatás** panel

Kövesse az alábbi utasításokat a hozzon létre egy támogatási kérést az Azure súgó + támogatás panelen elérhető az Azure Portalon keresztül. 

1. A https://portal.azure.com válassza **súgó + támogatás**.

![Súgó + támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő listában válassza ki a **szolgáltatás és az előfizetések korlátai (kvóták)** .

![Probléma típusa legördülő menü](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Válassza ki az előfizetést newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza ki **számítási növeli a Virtuálisgép-(mag-Vcpu) előfizetésre vonatkozó korlát** a **kvótatípus** legördülő listából. 

![Válassza ki a kvóta típusa](./media/resource-manager-core-quotas-request/select-quota-type.png)

6. A **probléma részletei**, nyújtanak további információt talál a folyamat a kérelem kattintva **adja meg az adatokat**.

![Adja meg az adatokat](./media/resource-manager-core-quotas-request/provide-details.png)

7. Az a **kvóta részletei** panelen válassza ki az üzembe helyezési modell és válasszon egy helyet.

![Kvóta részletei DM](./media/resource-manager-core-quotas-request/quota-details.png)

8. Válassza ki a **Termékváltozat-családokra** igénylő növelését. 

![Termékváltozat-család](./media/resource-manager-core-quotas-request/sku-family.png)

9. Adja meg az új korlátok szeretne az előfizetés. A sor eltávolításához törölje a jelet a Termékváltozat a Termékváltozat-család legördülő listából, vagy kattintson az elvetési "x" ikont. Miután megadta a kívánt kvótát az egyes Termékváltozat-család, kattintson a **menti és folytatja azt** kvóta részletei panelen a támogatási kérelem létrehozásának folytatásához.

![Új korlátok](./media/resource-manager-core-quotas-request/new-limits.png)


## <a name="request-per-vm-series-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Virtuálisgép-sorozatok vCPU kvótanövelést előfizetési szinten az egy kérelem **Usages + kvóta** panel

Kövesse az alábbi parancs használatával hozzon létre egy támogatási kérést az Azure "használati + kvóta" keresztül utasításokat az Azure-portálon elérhető panelje. 

1. A https://portal.azure.com válassza **előfizetések**.

![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Válassza ki **használat + kvóták**

![Válassza ki a használat és kvóták](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban, válassza ki **növelésére**.

![Növelés kérése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Válassza ki **növeli a virtuális gép számítási (magok-Vcpu) előfizetésre vonatkozó korlát** ajánlat típusaként. 

![Űrlap kitöltése](./media/resource-manager-core-quotas-request/forms.png)
   
6. Az a **kvóta részletei** panelen válassza ki az üzembe helyezési modell és válasszon egy helyet.

![Kvóta probléma panel](./media/resource-manager-core-quotas-request/problemstep.png)

7. Válassza ki a **Termékváltozat-családokra** igénylő növelését.

![Kiválasztott Termékváltozat-sorozatok](./media/resource-manager-core-quotas-request/sku-family.png)

8. Adja meg az új korlátok szeretne az előfizetés. A sor eltávolításához törölje a jelet a Termékváltozat a Termékváltozat-család legördülő listából, vagy kattintson az elvetési "x" ikont. Miután megadta a kívánt kvótát az egyes Termékváltozat-család, kattintson a **menti és folytatja azt** támogatási kérelem létrehozásának folytatása a probléma lépés oldalon található.

![Termékváltozat új kvótakérelem](./media/resource-manager-core-quotas-request/new-limits.png)

