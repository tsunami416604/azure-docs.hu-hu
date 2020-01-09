---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5975f334eae543ea0f6ddc182170ae185ac5397a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468150"
---
Az alábbi lépésekkel hozhat létre egy VNetet a Resource Manager-alapú üzemi modellben az Azure Portallal. A képernyőképek csak példaként szolgálnak. Ne felejtse el ezeket az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

>[!NOTE]
>Ahhoz, hogy ez a VNet egy helyszíni helyhez csatlakozzon (P2S konfiguráció létrehozása mellett), egyeztetnie kell a helyszíni hálózati rendszergazdájával, hogy különítsen el egy IP-címtartományt, amit kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány található, a rendszer esetleg nem a várt módon irányítja a forgalmat. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ügyeljen arra, hogy a hálózati konfigurációt ennek megfelelően tervezze meg.
>
>

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).  A Azure Portal menüben vagy a **kezdőlapon** válassza az **erőforrás létrehozása**lehetőséget. Megnyílik az **új** oldal.

2. A **Keresés a piactéren**mezőbe írja be a *virtuális hálózat* kifejezést, és válassza a **Virtual Network** lehetőséget az eredmények közül.

   ![Virtual Network erőforrás oldalának megkeresése](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/search-marketplace-for-virtual-network.png "Virtuális hálózati erőforrás keresése lap")

3. A Virtuális hálózat lap alján, a **Telepítési modell kiválasztása** listában válassza ki a **Resource Manager** elemet, majd kattintson a **Létrehozás** elemre.

   ![Resource Manager kiválasztása](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/resourcemanager250.png "A Resource Manager kiválasztása")
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek. Előfordulhatnak automatikusan kitöltött értékek is. Ebben az esetben cserélje ki az értékeket a saját értékeire. A **Virtuális hálózat létrehozása** lap a következő példához hasonlóan néz ki:

   ![Mező ellenőrzése](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/vnetp2s.png "Mező érvényesítése")
5. **Név**: adja meg a virtuális hálózat nevét.
6. **Címtér**: adja meg a címteret. Ha több címteret szeretne felvenni, vegye fel az elsőt. A virtuális hálózat létrehozása után később további címtereket is felvehet.
7. **Előfizetés**: ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg a listában. Az előfizetéseket a legördülő menüben módosíthatja.
8. **Erőforráscsoport**: válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy újat az új erőforráscsoport nevének beírásával. Ha új csoportot hoz létre, a tervezett konfigurációs értékeknek megfelelően nevezze el az erőforráscsoportot. További információ az erőforráscsoportokkal kapcsolatban: [Azure Resource Manager Overview](../articles/azure-resource-manager/management/overview.md#resource-groups) (Az Azure Resource Manager áttekintése).
9. **Hely**: válassza ki a Vnet helyét. Ez a hely határozza meg a VNeten üzembe helyezett erőforrások helyét.
10. **Alhálózat**: Adja meg az alhálózat nevét és az alhálózat címtartományát. A virtuális hálózat létrehozása után később további alhálózatokat is felvehet.
11. Ha szeretné könnyen megtalálni virtuális hálózatát az irányítópulton, akkor válassza a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Létrehozás** gombra.

    ![Rögzítés az irányítópulton](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/pintodashboard150.png "rögzítés az irányítópulton")
12. A **Create** (Létrehozás) gombra kattintva létrejön egy csempe az irányítópulton, amely a VNet állapotát mutatja. A virtuális hálózat létrejöttével a csempe is módosul.

    ![Virtuális hálózati csempe létrehozása](./media/vpn-gateway-basic-p2s-vnet-rm-portal-include/deploying150.png "Virtuális hálózat csempéjének létrehozása")
