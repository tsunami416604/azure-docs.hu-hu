---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc9e18427bb3b8094911d2ac7f285d271ecd1a21
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "91025063"
---
1. A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforrás létrehozása**lehetőséget.

   ![Erőforrás](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. A **Keresés a piactéren** mezőbe írja be a **helyi hálózati átjáró**kifejezést, majd nyomja le az **ENTER** billentyűt a kereséshez. Ez visszaad egy listát az eredményekkel. Kattintson a **Helyi hálózati átjáró** elemre, majd kattintson a **Létrehozás** gombra a **Helyi hálózati átjáró létrehozása** lap megnyitásához.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway-ip.png" alt-text="Helyi hálózati átjáró IP-címének létrehozása":::

3. A **Helyi hálózati átjáró létrehozása** lapon adja meg a helyi hálózati átjáró értékeit.

   - **Név:** Adja meg a helyi hálózati átjáróobjektum nevét.
   - **Végpont:** Válassza ki a helyszíni VPN-eszköz végpontjának típusát – **IP-cím** vagy **FQDN (teljes tartománynév)**.
      - **IP-cím**: Ha a VPN-eszköz internetszolgáltatója számára lefoglalt statikus nyilvános IP-címmel rendelkezik, válassza az IP-cím lehetőséget, majd adja meg az IP-címet a példában látható módon. Ez annak a VPN-eszköznek a nyilvános IP-címe, amelyhez az Azure VPN Gateway csatlakozni kíván. Ha most még nem rendelkezik az IP-címmel, használhatja a példában látható értékeket, később azonban vissza kell térnie ide, és lecserélni a helyőrző IP-címet a VPN-eszköz nyilvános IP-címére. Ellenkező esetben az Azure nem fog tudni csatlakozni.
      - **FQDN**: Ha olyan dinamikus nyilvános IP-címmel rendelkezik, amely bizonyos idő elteltével változhat, amelyet általában az internetszolgáltató határoz meg, akkor a dinamikus DNS-szolgáltatással rendelkező állandó DNS-nevet használhatja a VPN-eszköz aktuális nyilvános IP-címére. Az Azure VPN Gateway feloldja a teljes tartománynevet, hogy meghatározza a csatlakozáshoz használandó nyilvános IP-címet. Az alábbi képernyőképen egy példa látható, hogy az IP-cím helyett a teljes tartománynevet használja.
   - A **Címtartomány** annak a hálózatnak a címtartományára utal, amelyet a helyi hálózat képvisel. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. *A saját értékeit használja itt a helyszíni hellyel való kapcsolódáshoz, ne a példában látható értékeket*.
   - **BGP beállítások konfigurálása:** Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
   - **Előfizetés:** Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
   - **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
   - **Hely:** A hely megegyezik a más beállításokban található **régióval** . Válassza ki azt a helyet, ahol az objektumot létre kívánja hozni. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

   Ez ugyanaz az oldal, de a teljes tartománynév kiemelve:

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-gateway-fqdn.png" alt-text="Helyi hálózati átjáró teljes tartománynevének létrehozása":::

   > [!NOTE]
   >
   > * Az Azure VPN minden FQDN esetében csak egy IPv4-címeket támogat. Ha a tartománynév több IP-címre lett feloldva, az Azure VPN Gateway a DNS-kiszolgálók által visszaadott első IP-címet fogja használni. A bizonytalanság megszüntetéséhez azt javasoljuk, hogy a teljes tartománynevet mindig egyetlen IPv4-címen oldja fel a rendszer. Az IPv6 nem támogatott.
   > * Az Azure VPN Gateway 5 percenként frissíti a DNS-gyorsítótárat. Az átjáró csak a leválasztott alagutak teljes tartománynevét próbálja meg feloldani. Az átjáró alaphelyzetbe állítása a teljes tartománynév feloldását is elindítja.
   >

4. Ha végzett az értékek megadásával, kattintson a lap alján található **Létrehozás** gombra a helyi hálózati átjáró létrehozásához.
