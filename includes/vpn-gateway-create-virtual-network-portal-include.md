---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b585a47d3950d232eb3e8047c12ee8949030c95
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "68780222"
---
Az alábbi lépésekkel hozhat létre egy VNetet a Resource Manager-alapú üzemi modellben az Azure Portallal. Ha ezeket a lépéseket oktatóanyagként használja, használja a **példa értékeit** . Ha a lépéseket nem az oktatóanyag keretében hajtja végre, ne felejtse el az értékeket a saját értékeire cserélni. További információ a virtuális hálózatok használatáról: [Virtual Network Overview](../articles/virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).

>[!NOTE]
>Ahhoz, hogy ez a VNet egy helyszíni helyhez csatlakozzon, egyeztetnie kell a helyszíni hálózati rendszergazdájával, hogy különítsen el egy IP-címtartományt, amelyet kifejezetten ehhez a virtuális hálózathoz használhat. Ha a VPN-kapcsolat mindkét oldalán ismétlődő címtartomány található, a rendszer esetleg nem a várt módon irányítja a forgalmat. Ráadásul ha ezt a VNetet egy másik VNethez szeretné csatlakoztatni, a címtér nem lehet átfedésben másik VNettel. Ügyeljen arra, hogy a hálózati konfigurációt ennek megfelelően tervezze meg.
>

1. Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.
2. Kattintson az **Erőforrás létrehozása** gombra. A **Keresés a piactéren** mezőbe írja be a „Virtuális hálózat” kifejezést. A visszaadott listában keresse meg a **Virtuális hálózat** elemet, és arra kattintva nyissa meg a **Virtuális hálózat** lapot.
3. Kattintson a  **Create** (Létrehozás) gombra. Ekkor megnyílik a **virtuális hálózat létrehozása** lap.
4. A **Virtuális hálózat létrehozása** lapon konfigurálja a VNet beállításait. A mezők kitöltése közben a vörös felkiáltójelből zöld pipa lesz, ha a mezőbe beírt karakterek érvényesek. Használja a következő értékeket:

   - **Név**: VNet1
   - **Címterület**: 10.1.0.0/16
   - **Előfizetés**: Ellenőrizze, hogy a felsorolt előfizetés szerepel-e a használni kívánt előfizetésben. Az előfizetéseket a legördülő menüben módosíthatja.
   - **Erőforráscsoport**: TestRG1 (új csoport létrehozásához kattintson az **új létrehozása** lehetőségre)
   - Hely: USA keleti **régiója**
   - **Alhálózat**: előtér
   - **Címtartomány**: 10.1.0.0/24

   ![Virtuális hálózat létrehozása lap](./media/vpn-gateway-create-virtual-network-portal-include/create-virtual-network1.png)
5. A DDoS-t alapszintű, letiltottként, a tűzfal pedig letiltottként hagyja.
6. A VNet létrehozásához kattintson a **Create** (Létrehozás) gombra.