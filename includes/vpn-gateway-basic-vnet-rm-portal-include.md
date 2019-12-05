---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5bf93980a8be86c77240ab981eb812a738a96204
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828796"
---
Az alábbi lépéseket követve létrehozhat egy VNet a Resource Manager-alapú üzemi modellel és a Azure Portal. További információ a virtuális hálózatokról: [Virtual Network Overview (áttekintés](../articles/virtual-network/virtual-networks-overview.md)).

>[!NOTE]
>Ahhoz, hogy a VNet egy helyszíni helyhez kapcsolódjon, egyeztessen a helyszíni hálózati rendszergazdájával, hogy kifaragjon egy olyan IP-címtartományt, amelyet kifejezetten ehhez a virtuális hálózathoz használhat. Ha egy duplikált címtartomány létezik a VPN-kapcsolat mindkét oldalán, a forgalom nem várt módon lesz átirányítva. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ennek megfelelően tervezze meg a hálózati konfigurációt.
>
>

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).  A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása**lehetőséget. Megnyílik az **új** oldal.

2. A **Keresés a piactéren**mezőbe írja be a *virtuális hálózat* kifejezést, és válassza a **Virtual Network** lehetőséget az eredmények közül.

   ![Virtual Network erőforrás oldalának megkeresése](./media/vpn-gateway-basic-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Virtuális hálózati erőforrás keresése lap")

   Megnyílik a **virtuális hálózat** lap.

3. Az oldal alján, a **telepítési modell kiválasztása** listában válassza ki a **Resource Manager**elemet, majd válassza a **Létrehozás**lehetőséget. Megnyílik a **virtuális hálózat létrehozása** lap.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Virtuális hálózat létrehozása lap")

4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. Amikor kitölti a mezőket, a vörös felkiáltójel zöld pipa lesz, ha a mezőbe beírt karakterek ellenőrzése megtörténik. Néhány érték autofilled, amelyet a saját értékeivel helyettesíthet:

   - **Név:** adja meg a virtuális hálózat nevét.

   - **Címtér**: adja meg a címteret. Ha több címtartományt is fel szeretne venni, adja meg az első címtartomány itt. Később további címtartományt is hozzáadhat, miután létrehozta a VNet.

   - **Előfizetés:** ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.

   - **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének megadásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Hely**: válassza ki a Vnet helyét. A hely határozza meg, hogy az adott VNet üzembe helyezett erőforrások hol fognak élni.

   - **Alhálózat**: adja hozzá az alhálózat **nevét** és az alhálózati **címtartományt**. Később további alhálózatokat is hozzáadhat, miután létrehozta a VNet. 
     
5. Kattintson a **Létrehozás** gombra.
