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
ms.openlocfilehash: 835f23f98ebe56e0b19081f07dc3302ef93b27b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109244"
---
A következő lépésekkel hozhat létre egy virtuális hálózathoz a Resource Manager üzemi modell és az Azure Portalon. Virtuális hálózatokkal kapcsolatos további információkért lásd: [Virtual Network áttekintése](../articles/virtual-network/virtual-networks-overview.md).

>[!NOTE]
>A vnet egy helyszíni helyhez csatlakozzon egyeztetnie a helyi rendszergazda, hogy különítsen el egy IP-címtartományt, amely, kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány, forgalom nem várt módon irányítja át. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ennek megfelelően tervezze meg a hálózati konfigurációt.
>
>

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com) válassza **erőforrás létrehozása**. A **új** lap megnyitásakor.

2. Az a **keresés a piactéren** írja be a következőt *virtuális hálózat* válassza **virtuális hálózati** a visszaadott listában. A **virtuális hálózati** lap megnyitásakor.

   ![Virtuális hálózati erőforrás keresése lap](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Virtuális hálózati erőforrás keresése lap")

3. Az a **telepítési modell kiválasztása** listában válassza az oldal alján **Resource Manager**, majd válassza ki **létrehozás**. A **virtuális hálózat létrehozása** lap megnyitásakor.

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet.png "Virtuális hálózat létrehozása lap")

4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltésekor a vörös felkiáltójelből zöld pipa válik, amikor a rendszer érvényesíti a, adja meg a mezőben karakterek. Egyes értékek autofilled, amit a saját értékeire:

   - **Név:** adja meg a virtuális hálózat nevét.

   - **Címtér**: adja meg a címteret. Ha több címteret szeretne felvenni, adja meg itt az első címterét. A virtuális hálózat létrehozása után később is felvehet a további címtereket.

   - **Előfizetés:** ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.

   - **Erőforráscsoport**: Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport-név beírásával. Ha egy új csoportot hoz létre, adjon nevet az erőforráscsoport, a tervezett konfigurációs értékeknek megfelelően. További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).

   - **Hely**: válassza ki a Vnet helyét. A hely határozza meg, ahol a virtuális hálózaton üzembe helyezett erőforrások megtalálhatók lesznek.

   - **Alhálózat**: az alhálózat hozzáadása **neve** és alhálózati **címtartomány**. Később is hozzáadhat további alhálózatokat, virtuális hálózat létrehozása után. 
     
5. Kattintson a **Létrehozás** gombra.
