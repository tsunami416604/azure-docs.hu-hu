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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331263"
---
1. Az [Azure Portal](https://portal.azure.com) menüben válassza az **Erőforrás létrehozása parancsot.** 

   ![Erőforrás létrehozása az Azure Portalon](./media/vpn-gateway-add-gw-rm-portal-include/azure-portal-create-resource.png)
2. A **Keresés a piactéren** mezőbe írja be a "Virtual Network Gateway" kifejezést. Keresse meg a **virtuális hálózati átjárót** a keresési visszaadásban, és válassza ki a bejegyzést. A **Virtuális hálózati átjáró** lapon válassza a **Létrehozás lehetőséget.** Ez megnyitja a **Virtuális hálózati átjáró létrehozása** lapot.
3. Az **Alapok** lapon töltse ki a virtuális hálózati átjáró értékeit.

   ![Virtuális hálózati átjáró lapmezőinek létrehozása](./media/vpn-gateway-add-gw-rm-portal-include/gateway1.png "Virtuális hálózati átjáró lapmezőinek létrehozása")

   ![Virtuális hálózati átjáró lapmezőinek létrehozása](./media/vpn-gateway-add-gw-rm-portal-include/gateway2.png "Virtuális hálózati átjáró lapmezőinek létrehozása")

   **Projekt részletei**

   - **Előfizetés**: Válassza ki a használni kívánt előfizetést a legördülő menüből.
   - **Erőforráscsoport**: Ez a beállítás automatikusan kitöltődik, amikor a virtuális hálózatot választja ki ezen a lapon.

   **Példány részletei**

   - **Név**: adjon nevet az átjárónak. Az átjáró elnevezése nem ugyanaz, mint egy átjáró alhálózat elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
   - **Régió**: Válassza ki azt a régiót, amelyben létre szeretné hozni ezt az erőforrást. Az átjáró régiójának meg kell egyeznie a virtuális hálózattal.
   - **Átjáró típusa**: válassza ki a **VPN** elemet. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják.
   - **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
   - **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).
   - **Létrehozás**: A VPN-átjárók létrehozásáról az [Átjáró termékkészletei](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)című témakörben talál további információt.
   - **Virtuális hálózat**: A legördülő menüből válassza ki azt a virtuális hálózatot, amelyhez hozzá szeretné adni ezt az átjárót.
   - **Átjáró-alhálózat címtartománya**: Ez a mező csak akkor jelenik meg, ha a virtuális hálózat nem rendelkezik átjáró alhálózatával. Ha lehetséges, a /27 vagy nagyobb tartományt (/26,/25 stb.) kell meghozni. Nem javasoljuk, hogy /28-nál kisebb tartományt hozzon létre. Ha már rendelkezik átjáró alhálózattal, megtekintheti a GatewaySubnet adatait a virtuális hálózatra való navigálással. A tartomány megtekintéséhez kattintson az **Alhálózatok** elemre. Ha módosítani szeretné a tartományt, törölheti és újra létrehozhatja a GatewaySubnet-et.

   **Nyilvános IP-cím**: Ez a beállítás azt a nyilvános IP-cím-objektumot adja meg, amely hozzá lesz rendelve a VPN-átjáróhoz. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

     - **Nyilvános IP-cím**: Hagyja az **Új létrehozása** lehetőséget.
     - **Nyilvános IP-cím neve**: A szövegmezőbe írja be a nyilvános IP-címpéldány nevét.
     - **Hozzárendelés**: A VPN-átjáró csak a dinamikus t támogatja.

   **Aktív-Aktív mód: Csak aktív-aktív**átjárókonfiguráció létrehozása esetén válassza az **Aktív-aktív mód engedélyezése** lehetőséget. Ha nem ez a helyzet, ne válassza ki a beállítást.

   A **BGP ASN konfigurálása** beállítást csak akkor jelölje ki, ha a konfigurációhoz kifejezetten szükség van rá. Ha szükség van a beállításra, az ASN alapértelmezett értéke 65515 lesz, de ez módosítható.
4. Az ellenőrzés futtatásához válassza **a Véleményezés + létrehozás** lehetőséget. Az érvényesítés sikeres ségének beolvasása után válassza a **Létrehozás** lehetőséget a VPN-átjáró központi telepítéséhez. Egy átjáró teljes létrehozása és üzembe helyezése akár 45 percet is igénybe vehet. A központi telepítés állapotát az átjáró áttekintése lapon tekintheti meg.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni.
