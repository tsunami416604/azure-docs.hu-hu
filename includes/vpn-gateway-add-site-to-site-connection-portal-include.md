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
ms.openlocfilehash: 5149973fe63f867b49e55c970779c005e12536b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68780207"
---
1. Nyissa meg a virtuális hálózati átjáró lapját. A panelt többféleképpen is megtalálhatja. Az átjáróhoz úgy navigálhat, hogy a **virtuális hálózat neve -> áttekintése -> csatlakoztatott eszközök -> az átjáró neve**.
2. Az átjáró lapján kattintson a **Kapcsolatok gombra.** A Kapcsolatok lapon kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** lap megnyitásához.

   ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-portal-include/configure-site-to-site-connection.png)
3. A **Kapcsolat hozzáadása** lapon állítsa be a kapcsolathoz tartozó értékeket.

   - **Név:** Nevezze el a kapcsolatot.
   - **Kapcsolat típusa:** Válassza a **Helyek közötti (IPSec)** típust.
   - **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
   - **Helyi hálózati átjáró:** Kattintson a **Helyi hálózati átjáró kiválasztása** elemre, és válassza ki a használni kívánt helyi hálózati átjárót.
   - **Megosztott kulcs:** Az értékének egyeznie kell azzal az értékkel, amit a helyi helyszíni VPN-eszközhöz használ. A példában az abc123 értéket használtuk, de lehetséges (és javasolt) ennél összetettebb értéket használni. A legfontosabb, hogy az itt megadott értéknek egyeznie kell azzal az értékkel, amelyet a VPN-eszköz konfigurálásakor ad meg.
   - A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.

4. A kapcsolat létrehozásához kattintson az **OK** gombra. A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.
5. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** lapján tekintheti meg. Az Állapot *Ismeretlenről**Kapcsolódásra*, majd *Sikeresre* vált.
