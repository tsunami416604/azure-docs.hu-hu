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
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444160"
---
1. A portál **Minden erőforrás** területén kattintson a **+Hozzáadás** gombra.
2. Az a **mindent** írja be a lap keresőmezőjébe írja **helyi hálózati átjáró**, majd kattintson ide az erőforrások listáját adja vissza. Kattintson a **Helyi hálózati átjáró** elemre a lap megnyitásához, majd kattintson a **Létrehozás** elemre a **Helyi hálózati átjáró létrehozása** lap megnyitásához.

   ![helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. A **Helyi hálózati átjáró létrehozása** lapon adja meg a helyi hálózati átjáró értékeit.

   - **név:** Adja meg a helyi hálózati átjáróobjektum nevét. Ha lehetséges, használjon egy intuitív, mint például **ClassicVNetLocal** vagy **TestVNet1Local**. Így könnyebben azonosíthatja a helyi hálózati átjárót a portálon.
   - **IP-cím:** Adjon meg egy érvényes nyilvános **IP-cím** a VPN-eszköz vagy virtuális hálózati átjárót, amelyhez szeretné csatlakoztatni.

     * **Ha a helyi hálózat egy helyszíni helyet képvisel:** Adja meg, amelyhez csatlakozni kíván a VPN-eszköz nyilvános IP-címét. Ez nem lehet a NAT mögött, és elérhetőnek kell lennie az Azure számára.
     * **Ha a helyi hálózat egy másik virtuális hálózatot képvisel:** Adja meg, hogy a virtuális hálózathoz rendelt hozzá a virtuális hálózati átjáró nyilvános IP-címét.
     * **Ha még nem rendelkezik az IP-cím:** Egy érvényes helyőrző IP-címet alkotó, és ezután térjen vissza, és módosíthatja ezt a beállítást a csatlakozás előtt.
   - A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Győződjön meg arról, hogy az itt megadott címtartományok ne legyenek átfedésben a tartományok, amelyhez csatlakozni más hálózatokhoz.
   - **BGP-beállítások konfigurálása:** Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
   - **Előfizetés:** Győződjön meg arról, hogy a megfelelő előfizetés jelenik-e meg.
   - **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
   - **Hely:** Válassza ki a hellyel, ez az objektum létrejön. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

4. A helyi hálózati átjáró létrehozásához kattintson a **Create** (Létrehozás) gombra.
