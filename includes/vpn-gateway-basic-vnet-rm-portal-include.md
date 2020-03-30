---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301944"
---
Virtuális hálózatot hozhat létre az Erőforrás-kezelő telepítési modelljével és az Azure Portalon az alábbi lépések végrehajtásával. A virtuális hálózatokról a Virtuális hálózat áttekintése című témakörben olvashat [bővebben.](../articles/virtual-network/virtual-networks-overview.md)

>[!NOTE]
>Ha egy virtuális hálózatot használ egy telephelyközi architektúra részeként, győződjön meg arról, hogy koordinálja a helyszíni hálózati rendszergazdával egy IP-címtartomány kifaragása, amely kifejezetten ehhez a virtuális hálózathoz használható. Ha a VPN-kapcsolat mindkét oldalán duplikált címtartomány található, a forgalom váratlan módon fog továbbirányulni. Továbbá, ha ezt a virtuális hálózatot egy másik virtuális hálózathoz szeretné csatlakoztatni, a címtér nem fedheti át a másik virtuális hálózatot. Ennek megfelelően tervezze meg a hálózati konfigurációt.
>
>

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Az **Erőforrások, szolgáltatás és dokumentumok keresése (G+/) mezőbe**írja be a *virtuális hálózat kifejezést.*

   ![Virtuális hálózati erőforrás lap megkeresése](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Virtuális hálózati erőforrás lap megkeresése")
1. Válassza ki a **virtuális hálózat** a **Piactér** eredményei.

   ![Virtuális hálózat kiválasztása](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Virtuális hálózati erőforrás lap megkeresése")
1. A **Virtuális hálózat** lapon válassza a **Létrehozás gombot.**

   ![virtuális hálózat lap](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Válassza a Létrehozás lehetőséget")
1. Ha a Létrehozás lehetőséget **választja,** megnyílik a **Virtuális hálózat létrehozása** lap.
1. Az **Alapok lapon** konfigurálja a **Projekt részleteit** és a **példány részleteinek** virtuális hálózat beállításait.

   ![Alapok lap](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "Alapok lap") A mezők kitöltésekénél zöld pipa jelenik meg, amikor a mezőbe beírt karaktereket érvényesíti a jelölőnégyzet. Egyes értékek automatikusan kitöltődnek, amelyeket a saját értékeivel helyettesíthet:

   - **Előfizetés**: Ellenőrizze, hogy a felsorolt előfizetés helyes-e. Az előfizetéseket a legördülő menüben módosíthatja.
   - **Erőforráscsoport**: Jelöljön ki egy meglévő erőforráscsoportot, vagy kattintson az **Új létrehozása** gombra új létrehozása érdekében. Az erőforráscsoportokról az [Azure Resource Manager áttekintése című témakörben olvashat bővebben.](../articles/azure-resource-manager/management/overview.md#resource-groups)
   - **Név**: Adja meg a virtuális hálózat nevét.
   - **Régió**: Válassza ki a virtuális hálózat helyét. A hely határozza meg, hogy a virtuális hálózatra üzembe helyezett erőforrások hol fognak élni.

1. Az **IP-címek** lapon konfigurálja az értékeket. Az alábbi példákban látható értékek demonstrációs célokat szolgálnak. Állítsa be ezeket az értékeket a kívánt beállításoknak megfelelően.

   ![IP-címek lap](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP-címek lap")  
   - **IPv4-címterület**: Alapértelmezés szerint a program automatikusan létrehoz egy címterületet. A címterületre kattintva úgy módosíthatja azt, hogy az a saját értékeit tükrözze. További címtereket is hozzáadhat.
   - **IPv6:** Ha a konfigurációhoz IPv6-címtér szükséges, az adatok megadásához jelölje be az **IPv6-címterület hozzáadása** jelölőnégyzetet.
   - **Alhálózat**: Ha az alapértelmezett címterületet használja, automatikusan létrejön egy alapértelmezett alhálózat. Ha módosítja a címterületet, alhálózatot kell hozzáadnia. Válassza **a + Add alhálózat lehetőséget** az **Alhálózat hozzáadása** ablak megnyitásához. Adja meg a következő beállításokat, majd az értékek hozzáadásához válassza a **Hozzáadás** gombot:
      - **Alhálózat neve**: Ebben a példában az alhálózatot "FrontEnd" névre kereszteltük el.
      - **Alhálózati címtartomány**: Az alhálózat címtartománya.

1. A **Biztonság** lapon most hagyja meg az alapértelmezett értékeket:

   - **DDos védelem**: Alapszintű
   - **Tűzfal**: Letiltva
1. A virtuális hálózati beállítások érvényesítéséhez válassza a **Véleményezés + létrehozás** lehetőséget.
1. A beállítások érvényesítése után válassza a **Létrehozás gombot.**
