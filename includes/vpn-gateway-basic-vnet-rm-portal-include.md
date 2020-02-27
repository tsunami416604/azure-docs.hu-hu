---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619686"
---
A következő lépésekkel hozhat létre egy virtuális hálózathoz a Resource Manager üzemi modell és az Azure Portalon. További információ a virtuális hálózatokról: [Virtual Network Overview (áttekintés](../articles/virtual-network/virtual-networks-overview.md)).

>[!NOTE]
>A vnet egy helyszíni helyhez csatlakozzon egyeztetnie a helyi rendszergazda, hogy különítsen el egy IP-címtartományt, amely, kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány, forgalom nem várt módon irányítja át. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ennek megfelelően tervezze meg a hálózati konfigurációt.
>
>

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. A **keresési erőforrások, szolgáltatások és dokumentumok (G +/)** területen írja be a *virtuális hálózat*kifejezést.

   ![Virtual Network erőforrás oldalának megkeresése](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "Virtuális hálózati erőforrás keresése lap")
1. A **piactér** eredményei közül válassza a **Virtual Network** lehetőséget.

   ![Virtuális hálózat kiválasztása](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "Virtuális hálózati erőforrás keresése lap")
1. A **Virtual Network** lapon kattintson a **Létrehozás**gombra.

   ![virtuális hálózat lap](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "Kattintson a Létrehozás gombra")
1. Ha a Létrehozás gombra kattint, megnyílik a **virtuális hálózat létrehozása** lap.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "Virtuális hálózat létrehozása lap")
1. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltésekor a vörös felkiáltójelből zöld pipa válik, amikor a rendszer érvényesíti a, adja meg a mezőben karakterek. Egyes értékek autofilled, amit a saját értékeire:

   - **Név:** adja meg a virtuális hálózat nevét.
   - **Címtér**: adja meg a címteret. Ha több címteret szeretne felvenni, adja meg itt az első címterét. A virtuális hálózat létrehozása után később is felvehet a további címtereket. Ha a konfigurációhoz IPv6-címtartomány szükséges, jelölje be a jelölőnégyzetet az információk megadásához.
   - **Előfizetés:** ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.
   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének megadásával. Ha egy új csoportot hoz létre, adjon nevet az erőforráscsoport, a tervezett konfigurációs értékeknek megfelelően. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/management/overview.md#resource-groups).
   - **Hely**: válassza ki a Vnet helyét. A hely határozza meg, ahol a virtuális hálózaton üzembe helyezett erőforrások megtalálhatók lesznek.
   - **Alhálózat**: adja hozzá az alhálózat **nevét** és az alhálózati **címtartományt**. Később is hozzáadhat további alhálózatokat, virtuális hálózat létrehozása után.
   - **DDos-védelem**: válassza az **alapszintű**lehetőséget, hacsak nem szeretné használni a standard szolgáltatást.
   - **Szolgáltatási végpontok**: ezt a beállítást **letiltottként**hagyhatja, kivéve, ha a konfiguráció megadja ezt a beállítást.
   - **Tűzfal**: ezt a beállítást **letiltottként**hagyhatja, kivéve, ha a konfiguráció megadja ezt a beállítást.
1. A virtuális hálózat központi telepítésének megkezdéséhez kattintson a **Létrehozás** gombra.
