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
ms.openlocfilehash: ea616786d69d41435be2a46e90d4973b21270935
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
1. Keresse meg, és nyissa meg a virtuális hálózati átjáró lapját. A panelt többféleképpen is megtalálhatja. Ebben a példában a „VNet1GW” átjárót a **TestVNet1 -> Áttekintés -> Csatlakoztatott eszközök -> VNet1GW** helyen értük el.
2. A VNet1GW lapon kattintson a **Kapcsolatok** elemre. A Kapcsolatok lapon kattintson a **+Hozzáadás** elemre a **Kapcsolat hozzáadása** lap megnyitásához.

    ![Helyek közötti kapcsolat létrehozása](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. A **Kapcsolat hozzáadása** lapon adja meg az értékeket a kapcsolat létrehozásához.

  - **Név:** Nevezze el a kapcsolatot. A példában a **VNet1toSite2** nevet használjuk.
  - **Kapcsolat típusa:** Válassza a **Helyek közötti (IPSec)** típust.
  - **Virtuális hálózati átjáró:** Az értéke rögzített, mivel erről az átjáróról csatlakozik.
  - **Helyi hálózati átjáró:** Kattintson a **Helyi hálózati átjáró kiválasztása** elemre, és válassza ki a használni kívánt helyi hálózati átjárót. A példában a **Site2**-t használjuk.
  - **Megosztott kulcs:** Az értékének egyeznie kell azzal az értékkel, amit a helyi helyszíni VPN-eszközhöz használ. A példában az „abc123” értéket használtuk, de érdemesebb egy ennél összetettebb értéket használni. Vegye figyelembe, hogy az itt megadott értéknek meg kell egyeznie a VPN-eszköz konfigurálásakor meghatározott értékkel.
  - A fennmaradó **Subscription** (Előfizetés), **Resource Group** (Erőforráscsoport) és **Location** (Hely) mezők értékei rögzítettek.

4. A kapcsolat létrehozásához kattintson az **OK** gombra. A *Creating Connection* (Kapcsolat létrehozása) üzenet elkezd villogni a képernyőn.
5. A kapcsolatot a virtuális hálózat átjárójának **Kapcsolatok** lapján tekintheti meg. Az Állapot *Ismeretlenről* *Kapcsolódásra*, majd *Sikeresre* vált.