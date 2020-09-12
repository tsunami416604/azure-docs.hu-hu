---
title: fájlbefoglalás
description: fájlbefoglalás
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 059d15090204c2fce0fddd4b80f4954755ea8f65
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89449679"
---
1. A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforrás létrehozása**lehetőséget. 

   ![Erőforrás létrehozása az Azure Portalon](./media/vpn-gateway-add-local-network-gateway-portal-include/azure-portal-create-resource.png)
2. A **Keresés a piactéren** mezőbe írja be a **helyi hálózati átjáró**kifejezést, majd nyomja le az **ENTER** billentyűt a kereséshez. Ez visszaad egy listát az eredményekkel. Kattintson a **Helyi hálózati átjáró** elemre, majd kattintson a **Létrehozás** gombra a **Helyi hálózati átjáró létrehozása** lap megnyitásához.

   ![A helyi hálózati átjáró létrehozása](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "A helyi hálózati átjáró létrehozása")

3. A **Helyi hálózati átjáró létrehozása** lapon adja meg a helyi hálózati átjáró értékeit.

   - **Név:** Adja meg a helyi hálózati átjáróobjektum nevét.
   - **IP-cím:** Ez annak a VPN-eszköznek a nyilvános IP-címe, amelyhez az Azure-t csatlakozni kívánja. Adjon meg egy érvényes nyilvános IP-címet. Ha most még nem rendelkezik az IP-címmel, használhatja a példában látható értékeket, később azonban vissza kell térnie ide, és lecserélni a helyőrző IP-címet a VPN-eszköz nyilvános IP-címére. Ellenkező esetben az Azure nem fog tudni csatlakozni.
   - A **címterület** a hálózat azon címeire utal, amelyekhez a helyi hálózati objektum (a helyszíni hálózat) vonatkozik. Adja hozzá a helyszíni hálózathoz irányítani kívánt címeket. Több címtartományt is felvehet. Ügyeljen arra, hogy az itt megadott címtartományok ne legyenek átfedésben olyan egyéb hálózatok címtartományaival, amelyekhez csatlakozni kíván. Az Azure a helyszíni VPN-eszköz IP-címéhez irányítja át a megadott címtartományt. *A saját értékeit használja itt a helyszíni hellyel való kapcsolódáshoz, ne a példában látható értékeket*.
   - **BGP beállítások konfigurálása:** Csak BGP konfigurálásakor használja. Más esetben ne jelölje be ezt a jelölőnégyzetet.
   - **Előfizetés:** Ellenőrizze, hogy a megfelelő előfizetés jelenik-e meg.
   - **Erőforráscsoport:** Válassza ki a használni kívánt erőforráscsoportot. Létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy korábban létrehozottat.
   - **Hely:** A hely megegyezik a más beállításokban található **régióval** . Válassza ki azt a helyet, ahol az objektumot létre kívánja hozni. Érdemes ugyanazt a helyet kiválasztani, ahol a virtuális hálózat is található, de ez nem kötelező.

4. Miután végzett az értékek megadásával, kattintson a **Létrehozás** gombra a lap alján a helyi hálózati átjáró létrehozásához.
