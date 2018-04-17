---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 04/04/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0dcb591db5f487a103feccf92ffa577a1cbf8b23
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
1. Keresse meg, és nyissa meg a virtuális hálózati átjáró lapját. A panelt többféleképpen is megtalálhatja. A VNet1GW átjáróhoz a következő módon navigálhat: **TestVNet1 -> Áttekintés -> Csatlakoztatott eszközök -> VNet1GW**.
2. A VNet1GW lapon kattintson a **Kapcsolatok** elemre. A Kapcsolatok lapon kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** lap megnyitásához.

  ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. A **Kapcsolat hozzáadása** lapon állítsa be a kapcsolathoz tartozó értékeket.

  - **Név:** Nevezze el a kapcsolatot.
  - **Kapcsolat típusa:** Válassza a **Helyek közötti (IPSec)** típust.
  - **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
  - **Helyi hálózati átjáró:** Kattintson a **Helyi hálózati átjáró kiválasztása** elemre, és válassza ki a használni kívánt helyi hálózati átjárót.
  - **Megosztott kulcs:** Az értékének egyeznie kell azzal az értékkel, amit a helyi helyszíni VPN-eszközhöz használ. A példában az abc123 értéket használtuk, de lehetséges (és javasolt) ennél összetettebb értéket használni. A legfontosabb, hogy az itt megadott értéknek egyeznie kell azzal az értékkel, amelyet a VPN-eszköz konfigurálásakor ad meg.
  - A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.

4. A kapcsolat létrehozásához kattintson az **OK** gombra. A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.
5. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** lapján tekintheti meg. Az Állapot *Ismeretlenről* *Kapcsolódásra*, majd *Sikeresre* vált.