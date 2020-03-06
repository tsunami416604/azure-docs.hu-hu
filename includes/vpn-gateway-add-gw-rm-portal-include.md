---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c3c9dbca6a76bf0b10a83087fc31d9fa41c6bd03
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331263"
---
1. A [Azure Portal](https://portal.azure.com) menüben válassza az **erőforrás létrehozása**lehetőséget. 

   ![Erőforrás létrehozása a Azure Portalban](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. A **Keresés a piactéren** mezőbe írja be a "Virtual Network Gateway" kifejezést. Keresse meg a **virtuális hálózati átjárót** a keresési visszáruban, és válassza ki a bejegyzést. A **virtuális hálózati átjáró** lapon válassza a **Létrehozás**lehetőséget. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** lapot.
3. Az **alapvető beállítások** lapon adja meg a virtuális hálózati átjáró értékeit.

   ![Virtuális hálózati átjáró létrehozása lap mezői](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Virtuális hálózati átjáró létrehozása lap mezői")

   ![Virtuális hálózati átjáró létrehozása lap mezői](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Virtuális hálózati átjáró létrehozása lap mezői")

   **Projekt részletei**

   - **Előfizetés**: válassza ki a legördülő listából használni kívánt előfizetést.
   - **Erőforráscsoport**: ezt a beállítást a rendszer a virtuális hálózat ezen a lapon való kiválasztásakor kitölti.

   **Példány részletei**

   - **Név**: adjon nevet az átjárónak. Az átjáró nem egyezhet meg az átjáró alhálózatának elnevezésével. Ez a létrehozni kívánt átjáróobjektum neve.
   - **Régió**: válassza ki azt a régiót, amelyben létre kívánja hozni ezt az erőforrást. Az átjáró régiójának meg kell egyeznie a virtuális hálózattal.
   - **Átjáró típusa**: válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják.
   - **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
   - **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).
   - **Létrehozás**: VPN Gateway létrehozásával kapcsolatos információkért lásd: [átjáró SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)-i.
   - **Virtuális hálózat**: a legördülő listából válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót.
   - **Átjáró alhálózatának**címtartomány: Ez a mező csak akkor jelenik meg, ha a VNet nem rendelkezik átjáró-alhálózattal. Ha lehetséges, végezze el a tartományt/27 vagy nagyobb (/26,/25 stb.). Nem javasoljuk, hogy a/28-nál kisebb tartományt hozzon létre. Ha már rendelkezik átjáró-alhálózattal, a virtuális hálózatra navigálva megtekintheti a GatewaySubnet adatait. Az **alhálózatok** elemre kattintva megtekintheti a tartományt. Ha módosítani szeretné a tartományt, törölheti és újból létrehozhatja a GatewaySubnet.

   **Nyilvános IP-cím**: Ez a beállítás azt a nyilvános IP-cím-objektumot adja meg, amely hozzá lesz rendelve a VPN-átjáróhoz. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

     - **Nyilvános IP-cím**: hagyja a **Create New (új** ) elemet.
     - **Nyilvános IP-cím neve**: a szövegmezőbe írja be a nyilvános IP-cím példányának nevét.
     - **Hozzárendelés**: a VPN-átjáró csak dinamikus használatát támogatja.

   **Aktív-aktív mód**: csak akkor válassza az **aktív-aktív mód engedélyezése** lehetőséget, ha aktív-aktív átjáró-konfigurációt hoz létre. Ha nem ez a helyzet, ne válassza ki a beállítást.

   A **BGP ASN konfigurálása** beállítást csak akkor jelölje ki, ha a konfigurációhoz kifejezetten szükség van rá. Ha szükség van a beállításra, az ASN alapértelmezett értéke 65515 lesz, de ez módosítható.
4. Válassza az **ellenőrzés + létrehozás** lehetőséget az érvényesítés futtatásához. Az érvényesítést követően a **Létrehozás** gombra kattintva telepítheti a VPN-átjárót. Az átjárók teljes létrehozása és üzembe helyezése akár 45 percet is igénybe vehet. A telepítési állapotot az átjáró áttekintés lapján tekintheti meg.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni.
