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
ms.openlocfilehash: 932aab3f16a571d4c83c77c1cc2274ae60ea3d35
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30196907"
---
1. A portál **Minden erőforrás** területén kattintson a **+Hozzáadás** gombra.
2. Az a **mindent** lap keresési mezőbe, írja be **helyi hálózati átjáró**, majd kattintson az erőforrások listájához való visszatéréshez. Kattintson a **Helyi hálózati átjáró** elemre a lap megnyitásához, majd kattintson a **Létrehozás** elemre a **Helyi hálózati átjáró létrehozása** lap megnyitásához.

  ![helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. A **Helyi hálózati átjáró létrehozása** lapon adja meg a helyi hálózati átjáró értékeit.

  - **Név:** Adja meg a helyi hálózati átjáróobjektum nevét. Ha lehetséges, használjon egy egyszerűen elsajátítható, többek között **ClassicVNetLocal** vagy **TestVNet1Local**. Így könnyebben azonosíthatja a helyi hálózati átjáró a portálon.
  - **IP-cím:** adjon meg egy érvényes nyilvános **IP-cím** a VPN-eszköz vagy a virtuális hálózati átjáró, amelyhez csatlakozni szeretne.

    * **Ha a helyi hálózati jelenti-e egy helyszíni hely:** adja meg a nyilvános IP-címe a VPN-eszközön, amelyhez csatlakozni kíván. Ez nem lehet a NAT mögött, és elérhetőnek kell lennie az Azure számára.
    * **Ha a helyi hálózat egy másik virtuális hálózatot jelöli:** adja meg, hogy a vnet rendelt hozzá a virtuális hálózati átjáró nyilvános IP-címét.
    * **Ha még nem rendelkezik az IP-cím:** jött létre egy érvényes helyőrző IP-címet, majd térjen vissza és csatlakozás előtt a beállítás módosításához.
  - A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Győződjön meg arról, hogy az itt megadott tartományok nem lehetnek, amelyhez csatlakozni más hálózatokhoz a tartományait.
  - **BGP beállítások konfigurálása:** Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
  - **Előfizetés:** Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
  - **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
  - **Hely:** Válassza ki a helyet, ahol az objektum létrejön. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.
4. A helyi hálózati átjáró létrehozásához kattintson a **Create** (Létrehozás) gombra.