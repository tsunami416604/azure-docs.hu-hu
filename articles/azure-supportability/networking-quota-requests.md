---
title: Hálózatkezelés a korlát növelésének |} A Microsoft Docs
description: Hálózatkezelés korlát növelésének
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 48d7e9cc4a3034e149901931f2addbc7df78e2bc
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67297367"
---
# <a name="networking-limit-increase"></a>Hálózatkezelés korlát növelésének

Az aktuális hálózati használati és a kvótaházirendek megtekintéséhez lépjen a **Usages + kvóta** panel az Azure Portalon. Is használhatja a használati [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) vagy a [hálózati API használati](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) megtekintéséhez a hálózat használatát és korlátait.

Egy növelésére keresztül **súgó + támogatás** panel vagy a **Usages + kvóta** panel a portálon.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Előfizetési szintű használatával hálózati kvóta növelésére a **súgó + támogatás** panel

Kövesse az alábbi utasításokat a hozzon létre egy támogatási kérést az Azure súgó + támogatás panelen elérhető az Azure Portalon keresztül. 

1. A https://portal.azure.com válassza **súgó + támogatás**.

    ![Súgó + támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

    ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő listában válassza ki a **szolgáltatás és az előfizetések korlátai (kvóták)** .

    ![Probléma típusa legördülő menü](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Válassza ki az előfizetést newSR](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza ki **hálózatkezelés** a **kvótatípus** legördülő listából. 

    ![Válassza ki a kvóta típusa](./media/networking-quota-request/select-quota-type-network.png)

6. A **probléma részletei**, nyújtanak további információt talál a folyamat a kérelem kattintva **adja meg az adatokat**.

    ![Adja meg az adatokat](./media/resource-manager-core-quotas-request/provide-details.png)

7. Az a **kvóta részletei** panel, válassza ki az üzembe helyezési modell, egy helyen és növelni szeretné az erőforrásokat.

    ![Kvóta részletei DM](./media/networking-quota-request/quota-details-network.png)

8.  Adja meg az új korlátok szeretne az előfizetés. A sor eltávolításához törölje az erőforrást az erőforrás legördülő listából, vagy kattintson az elvetési "x" ikont. Miután megadta a kívánt kvótát az egyes erőforrások, kattintson a **menti és folytatja azt** kvóta részletei panelen a támogatási kérelem létrehozásának folytatásához.

    ![Új korlátok](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>Hálózatkezelés kvóta növelésére előfizetési szintű az **Usages + kvóta** panel

Kövesse az alábbi parancs használatával hozzon létre egy támogatási kérést az Azure "használati + kvóta" keresztül utasításokat az Azure-portálon elérhető panelje. 

1. A https://portal.azure.com válassza **előfizetések**.

    ![Subscriptions](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. Válassza ki **használat + kvóták**

    ![Válassza ki a használat és kvóták](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban, válassza ki **növelésére**.

    ![Növelés kérése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Kövesse a lépéseket a 3 lépéstől kezdve a *kvótanövelést hálózati kérelem előfizetési szinten* szakasz használata a **súgó + támogatás** panel szakasz

## <a name="about-networking-limits"></a>Hálózatkezelési korlátok kapcsolatban

Hálózatkezelési korlátok kapcsolatos további információkért tekintse meg a [hálózatkezelés szakasz](../azure-subscription-service-limits.md#networking-limits) a korlátok lapon vagy a hálózati korlátok – gyakori kérdések