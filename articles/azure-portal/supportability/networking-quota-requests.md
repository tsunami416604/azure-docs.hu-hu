---
title: Hálózati korlát növekedése | Microsoft Docs
description: Hálózatkezelési korlát növelése
author: anavinahar
ms.author: anavin
ms.date: 06/19/2019
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 04a22d987e27f054648637890fbd61c56533c2ee
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896804"
---
# <a name="networking-limit-increase"></a>Hálózatkezelési korlát növelése

Ha meg szeretné tekinteni a jelenlegi hálózatkezelési használatot és kvótát, látogasson el a Azure Portal **használat + kvóta** paneljére. A hálózati használat és korlátok megtekintéséhez használhatja a [CLI](https://docs.microsoft.com//cli/azure/network?view=azure-cli-latest#az-network-list-usages)-t, a [PowerShellt](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermnetworkusage?view=azurermps-6.13.0) vagy a [hálózati használati API](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/listusage) -t is.

A portálon a **Súgó + támogatás** panel vagy a **használat + kvóta** panel használatával növelheti a növekedést.

> [!Note]
> A nyilvános IP-előtag alapértelmezett méretének módosításához a legördülő listából válassza a "minimális nyilvános IP-cím összetett előtag hossza" lehetőséget.

## <a name="request-networking-quota-increase-at-subscription-level-using-the-help--support-blade"></a>Hálózati kvóta növelésének kérése az előfizetés szintjén a **Súgó + támogatás** panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure "Súgó + támogatás" paneljén, amely a Azure Portal érhető el. 

1. https://portal.azure.com válassza a **Súgó + támogatás**lehetőséget.

    ![Súgó + támogatás](./media/resource-manager-core-quotas-request/helpsupport.png)
 
2.  Válassza az **Új támogatási kérelem** lehetőséget. 

    ![Új támogatási kérelem](./media/resource-manager-core-quotas-request/newsupportrequest.png)

3. A probléma típusa legördülő menüben válassza a **szolgáltatás és előfizetés korlátai (kvóták)** lehetőséget.

    ![Probléma típusa legördülő lista](./media/resource-manager-core-quotas-request/issuetypedropdown.png)

4. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés-hírolvasó kiválasztása](./media/resource-manager-core-quotas-request/select-subscription-sr.png)
   
5. Válassza a **hálózatkezelés** lehetőséget a **kvóta típusa** legördülő menüben. 

    ![Kvóta típusának kiválasztása](./media/networking-quota-request/select-quota-type-network.png)

6. A **probléma részletei**területen további információkat adhat meg a kérés feldolgozásához a **részletek megadása**lehetőségre kattintva.

    ![Részletek megadása](./media/resource-manager-core-quotas-request/provide-details.png)

7. A **kvóta részletei** panelen válassza a telepítési modell, a hely és az erőforrások, amelyeknek a növelését kéri.

    ![Kvóta részletei – DM](./media/networking-quota-request/quota-details-network.png)

8.  Adja meg az előfizetéshez hasonló új korlátozásokat. Egy sor eltávolításához törölje az erőforrást az erőforrás legördülő menüből, vagy kattintson az Elvetés "x" ikonra. Miután megadta a kívánt kvótát az egyes erőforrásokhoz, kattintson a Save (Mentés) gombra, **és folytassa** a kvóta részletei panelen a támogatási kérelem létrehozásával folytatott folytatáshoz.

    ![Új korlátok](./media/networking-quota-request/network-new-limits.png)


## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quota-blade"></a>A hálózati kvóta növelésének kérése az előfizetési szinten a használat és a **kvóta** panelen

Az alábbi útmutatást követve hozzon létre egy támogatási kérést az Azure-ban a Azure Portalben elérhető "használati + kvóta" panelen. 

1. https://portal.azure.com válassza az **előfizetések**lehetőséget.

    ![Előfizetések](./media/resource-manager-core-quotas-request/subscriptions.png)

2. Válassza ki az előfizetést, amelynek a kvótáját emelni szeretné.

    ![Előfizetés kiválasztása](./media/resource-manager-core-quotas-request/select-subscription.png)

3. **Használat + kvóták** kiválasztása

    ![Használat és kvóták kiválasztása](./media/resource-manager-core-quotas-request/select-usage-quotas.png)

4. A jobb felső sarokban válassza a **kérelem növekedés**lehetőséget.

    ![Kérések növekedése](./media/resource-manager-core-quotas-request/request-increase.png)

5. Kövesse a 3. lépéssel kezdődő lépéseket a *hálózatkezelési kvóták növekedése az előfizetés szintjén* szakaszban a **Súgó + támogatás** panel szakaszban

## <a name="about-networking-limits"></a>Tudnivalók a hálózati korlátokról

A hálózati korlátokkal kapcsolatos további tudnivalókért tekintse meg a korlátozások lap [hálózatkezelés szakaszát](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) , vagy a hálózat korlátai – gyakori kérdések
