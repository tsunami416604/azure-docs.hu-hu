---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444176"
---
1. A portáloldal bal oldalán kattintson a **+** gombra, és írja be a „Virtuális hálózati átjáró” kifejezést a keresőmezőbe. Az **Eredmények** között keresse meg a **Virtuális hálózati átjáró** elemet, és kattintson rá.
2. A „Virtuális hálózati átjáró” lap alján kattintson a **Létrehozás** gombra. Ez megnyitja a **Virtuális hálózati átjáró létrehozása** lapot.
3. A **Virtuális hálózati átjáró létrehozása** lapon adja meg a virtuális hálózati átjáró értékeit.

    ![Virtuális hálózati átjáró létrehozása lap mezői](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Új átjáró")

   - **Név**: Adjon nevet az átjáró. Ez nem ugyanaz, mint egy átjáró alhálózatának elnevezése. Ez a létrehozni kívánt átjáróobjektum neve.
   - **Átjáró típusa**: Válassza ki **VPN**. A VPN-átjárók a **VPN** virtuális hálózati átjárótípust használják. 
   - **VPN-típust**: Válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációhoz útvonalalapú VPN-típus szükséges.
   - **TERMÉKVÁLTOZAT**: A legördülő listából válassza ki az átjáró-Termékváltozatot. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

     Csak akkor válassza ki az **Aktív-aktív üzemmód engedélyezése** lehetőséget, ha aktív-aktív átjáró-konfigurációt hoz létre. Ha nem ez a helyzet, ne válassza ki a beállítást.
   - **Hely**: Előfordulhat, hogy kell görgetnie a hely. Állítsa be úgy a **Hely** mezőt, hogy a virtuális hálózat helyére mutasson. Az értéke lehet például az USA nyugati régiója. Ha a hely nem egyezik meg azzal a régióval, ahol a virtuális hálózat található, akkor a következő lépésben egy virtuális hálózat kiválasztásakor az nem jelenik meg a legördülő listában.
   - **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Kattintson a **Virtuális hálózat** elemre a Virtuális hálózat választása lap megnyitásához. Válassza ki a VNet elemet. Ha a VNet nem jelenik meg, ellenőrizze, hogy a Hely mező arra a régióra mutat-e, amelyikben a virtuális hálózata található.
   - **Átjáróalhálózat címtartománya**: Ez a beállítás csak akkor jelenik meg, ha azt előzőleg nem hozott létre átjáróalhálózatot a virtuális hálózat. Ha korábban létrehozott egy érvényes átjáróalhálózatot, ez a beállítás nem jelenik meg.
   - **Nyilvános IP-cím**: Ezzel a beállítással a nyilvános IP-címobjektumot, amely hozzá lesz rendelve a VPN-átjárót. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A VPN Gateway jelenleg csak a *Dinamikus* nyilvános IP-cím lefoglalását támogatja. Ez azonban nem jelenti azt, hogy az IP-cím módosul a VPN Gateway-hez való hozzárendelése után. A nyilvános IP-cím kizárólag abban az esetben változik, ha az átjárót törli, majd újra létrehozza. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.

     - Hagyja bejelölve az **Új létrehozása** lehetőséget.
     - Adjon egy nevet a nyilvános IP-címnek a **szövegmezőben**.

4. A **BGP ASN konfigurálása** beállítást csak akkor jelölje ki, ha a konfigurációhoz kifejezetten szükség van rá. Ha szükség van a beállításra, az ASN alapértelmezett értéke 65515 lesz, de ez módosítható.
5. Ellenőrizze a beállításokat. Ha azt szeretné, hogy az átjáró megjelenjen az irányítópulton, válassza a lap alján lévő **Rögzítés az irányítópulton** elemet. 
6. A **Létrehozás** gombra kattintva hozzákezdhet a VPN-átjáró létrehozásához. A rendszer ellenőrzi a beállításokat, és az irányítópulton megjelenik a „Virtuális hálózati átjáró üzembe helyezése” csempe. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.
   átjáró.

Az átjáró létrehozása után úgy tekintheti meg a hozzárendelt IP-címet, ha megnézi a virtuális hálózatot a portálon. Az átjáró csatlakoztatott eszközként fog megjelenni. A csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információkat jeleníthet meg.