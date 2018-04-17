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
ms.openlocfilehash: 9ab0dfac298ab7246935649010100c14dbe9c76a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
1. A portálon kattintson az **+Erőforrás létrehozása** gombra.
2. A keresőmezőbe írja be a **Helyi hálózati átjáró** kifejezést, majd nyomja le az **Enter** billentyűt a kereséshez. Ez visszaad egy listát az eredményekkel. Kattintson a **Helyi hálózati átjáró** elemre, majd kattintson a **Létrehozás** gombra a **Helyi hálózati átjáró létrehozása** lap megnyitásához.

  ![Helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Helyi hálózati átjáró létrehozása")

3. A **Helyi hálózati átjáró létrehozása** lapon adja meg a helyi hálózati átjáró értékeit.

  - **Név:** Adja meg a helyi hálózati átjáróobjektum nevét.
  - **IP-cím:** Ez annak a VPN-eszköznek a nyilvános IP-címe, amelyhez az Azure-t csatlakozni kívánja. Adjon meg egy érvényes nyilvános IP-címet. Az IP-cím nem lehet NAT mögött, és elérhetőnek kell lennie az Azure számára. Ha most még nem rendelkezik az IP-címmel, használhatja a példában látható értékeket, később azonban vissza kell térnie ide, és lecserélni a helyőrző IP-címet a VPN-eszköz nyilvános IP-címére. Ellenkező esetben az Azure nem fog tudni csatlakozni.
  - A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. *A saját értékeit használja itt a helyszíni hellyel való kapcsolódáshoz, ne a példában látható értékeket*.
  - **BGP beállítások konfigurálása:** Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
  - **Előfizetés:** Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
  - **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
  - **Hely:** Válassza ki a helyet, ahol az objektum létrejön. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

4. Miután végzett az értékek megadásával, kattintson a **Létrehozás** gombra a lap alján a helyi hálózati átjáró létrehozásához.