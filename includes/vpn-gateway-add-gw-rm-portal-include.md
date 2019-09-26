---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/24/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12d3f06ca349413231d69fc9de7b9d2f36645b23
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273791"
---
1. A portál bal oldalán kattintson az **+ Erőforrás létrehozása** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. A keresési eredmények között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson a bejegyzésre. A **virtuális hálózati átjáró** lapon kattintson a **Létrehozás**gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** lapot.

   ![Create virtual network gateway page fields](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw.png "Create virtual network gateway page fields")

   ![Create virtual network gateway page fields](./media/vpn-gateway-add-gw-rm-portal-include/p2sgw2.png "Create virtual network gateway page fields")
2. A **Virtuális hálózati átjáró létrehozása** lapon töltse ki a virtuális hálózati átjáró értékeit.

   **Projekt részletei**

   - **Előfizetés**: Válassza ki a legördülő listából használni kívánt előfizetést.
   - **Erőforráscsoport**: Ez a beállítás a virtuális hálózat ezen a lapon való kiválasztásakor a rendszer kitölti ezt a beállítást.

   **Példány részletei**

   - **Név**: Nevezze el az átjárót. Az átjáró nem egyezhet meg az átjáró alhálózatának elnevezésével. Ez a létrehozni kívánt átjáróobjektum neve.
   - **Régió**: Válassza ki azt a régiót, amelyben létre kívánja hozni ezt az erőforrást. Az átjáró régiójának meg kell egyeznie a virtuális hálózattal.
   - **Átjáró típusa**: Válassza a **VPN**lehetőséget. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják. 
   - **VPN típusa**: Válassza ki a konfigurációhoz megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
   - **SKU**: Válassza ki az átjáró SKU-t a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

      **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót.

      **Átjáró-alhálózati címtartomány**: Ez a mező csak akkor jelenik meg, ha a kiválasztott virtuális hálózat nem rendelkezik átjáró-alhálózattal. Adja meg a tartományt, ha még nem rendelkezik átjáró-alhálózattal. Ha lehetséges, végezze el a tartományt/27 vagy nagyobb (/26,/25 stb.)

   **Nyilvános IP-cím**: Ezzel a beállítással adható meg a VPN-átjáróhoz társított nyilvános IP-cím objektum. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

     - **Nyilvános IP-cím**: Hagyja bejelölve az **Új létrehozása** lehetőséget.
     - **Nyilvános IP-cím neve**: A szövegmezőbe írja be a nyilvános IP-cím példányának nevét.
     - **Hozzárendelés**: A VPN-átjáró csak dinamikus használatát támogatja.

   **Aktív-aktív üzemmód**: Csak akkor válassza ki az **Aktív-aktív üzemmód engedélyezése** lehetőséget, ha aktív-aktív átjáró-konfigurációt hoz létre. Ha nem ez a helyzet, ne válassza ki a beállítást.

   A **BGP ASN konfigurálása** beállítást csak akkor jelölje ki, ha a konfigurációhoz kifejezetten szükség van rá. Ha szükség van a beállításra, az ASN alapértelmezett értéke 65515 lesz, de ez módosítható.

3. Kattintson a **felülvizsgálat + létrehozás** gombra az érvényesítés futtatásához. Az ellenőrzés után kattintson a **Létrehozás** gombra a VPN-átjáró telepítéséhez. Az átjárók teljes létrehozása és üzembe helyezése akár 45 percet is igénybe vehet. A telepítési állapotot az átjáró áttekintés lapján tekintheti meg.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni.
