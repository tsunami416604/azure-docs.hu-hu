---
title: Az Azure virtuális gépenként vCPU-kvóta növeléséhez kérelmek |} A Microsoft Docs
description: egy virtuális gép vCPU-kvóta növeléséhez a kérelmek
author: sowmyavenkat86
ms.author: svenkat
ms.date: 06/07/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: f921b4a95c1b0cfb29d84c0bacc17d268af6e6c5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082819"
---
# <a name="vm-series-vcpu-limit-increase"></a>Virtuálisgép-sorozatok vCPU korlát növelésének

Most már igényelhet növelését **súgó + támogatás** panel vagy a **Usages + kvóta** panel a portálon. 

## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Virtuális gép vCPU kvótanövelést előfizetési szinten az egy kérelem a **súgó + támogatás** panel

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


## <a name="request-per-vm-vcpu-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Virtuális gép vCPU kvótanövelést előfizetési szinten az egy kérelem **Usages + kvóta** panel

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

