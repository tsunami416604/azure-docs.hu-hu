---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78301944"
---
Az alábbi lépéseket követve létrehozhat egy VNet a Resource Manager-alapú üzemi modellel és a Azure Portal. További információ a virtuális hálózatokról: [Virtual Network Overview (áttekintés](../articles/virtual-network/virtual-networks-overview.md)).

>[!NOTE]
>Ha egy virtuális hálózatot a létesítmények közötti architektúrák részeként használ, mindenképpen koordinálja a helyszíni hálózati rendszergazdájával, hogy kifaragjon egy olyan IP-címtartományt, amelyet kifejezetten ehhez a virtuális hálózathoz használhat. Ha egy duplikált címtartomány létezik a VPN-kapcsolat mindkét oldalán, a forgalom nem várt módon lesz átirányítva. Emellett, ha a virtuális hálózatot egy másik virtuális hálózathoz szeretné kapcsolni, a Címterület nem fedi át a másik virtuális hálózatot. Ennek megfelelően tervezze meg a hálózati konfigurációt.
>
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **keresési erőforrások, szolgáltatások és dokumentumok (G +/)** területen írja be a *virtuális hálózat*kifejezést.

   ![Virtual Network erőforrás oldalának megkeresése](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Virtuális hálózati erőforrás keresése lap")
1. A **piactér** eredményei közül válassza a **Virtual Network** lehetőséget.

   ![Virtuális hálózat kiválasztása](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Virtuális hálózati erőforrás keresése lap")
1. A **Virtual Network** lapon válassza a **Létrehozás**lehetőséget.

   ![virtuális hálózat lap](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "A Létrehozás lehetőség kiválasztása")
1. A **Létrehozás**gombra kattintva megnyílik a **virtuális hálózat létrehozása** lap.
1. Az **alapok** lapon adja meg a **Project details** és a **instance details** VNet beállításait.

   ![Alapbeállítások lap](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Alapbeállítások lap") A mezők kitöltése után zöld pipa jelenik meg, ha a mezőben megadott karaktereket érvényesíti a rendszer. Néhány érték autofilled, amelyet a saját értékeivel helyettesíthet:

   - **Előfizetés**: Ellenőrizze, hogy a felsorolt előfizetés megfelelő-e. Az előfizetéseket a legördülő menüben módosíthatja.
   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy kattintson az **új létrehozása** lehetőségre egy új létrehozásához. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../articles/azure-resource-manager/management/overview.md#resource-groups)).
   - **Név**: adja meg a virtuális hálózat nevét.
   - **Régió**: válassza ki a VNet helyét. A hely határozza meg, hogy az adott VNet üzembe helyezett erőforrások hol fognak élni.

1. Az **IP-címek** lapon adja meg az értékeket. Az alábbi példákban látható értékek szemléltetési célokat szolgálnak. Módosítsa ezeket az értékeket a szükséges beállításoknak megfelelően.

   ![IP-címek lap](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP-címek lap")  
   - **IPv4-címterület**: alapértelmezés szerint a Címterület automatikusan létrejön. A Címterület elemre kattintva beállíthatja, hogy tükrözze a saját értékeit. További címterület hozzáadására is lehetőség van.
   - **IPv6**: Ha a konfigurációhoz IPv6-címtartomány szükséges, jelölje be az **IPv6-címterület hozzáadása** mezőt az információk megadásához.
   - **Alhálózat**: Ha az alapértelmezett címtartományt használja, a rendszer automatikusan létrehoz egy alapértelmezett alhálózatot. Ha megváltoztatja a Címterület méretét, hozzá kell adnia egy alhálózatot. Válassza az **+ alhálózat hozzáadása** lehetőséget az **alhálózat hozzáadása** ablak megnyitásához. Adja meg a következő beállításokat, majd válassza a **Hozzáadás** lehetőséget az értékek hozzáadásához:
      - **Alhálózat neve**: ebben a példában a "FrontEnd" alhálózatot nevezték el.
      - **Alhálózati címtartomány**: az alhálózat címtartomány.

1. A **Biztonság** lapon most hagyja meg az alapértelmezett értékeket:

   - **DDos Protection**: alapszintű
   - **Tűzfal**: letiltva
1. A virtuális hálózat beállításainak ellenőrzéséhez válassza a **felülvizsgálat + létrehozás** elemet.
1. A beállítások érvényesítése után válassza a **Létrehozás**lehetőséget.
