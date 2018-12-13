---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53111653"
---
1. Jelentkezzen be az Azure Portalon, és válassza **erőforrás létrehozása**. A **új** lap megnyitásakor.

2. Az a **keressen a piactéren mezőben**, adja meg *virtuális hálózati átjáró*, és válassza ki **virtuális hálózati átjáró** a keresési listáról. 

3. Az a **virtuális hálózati átjáró** lapon jelölje be **létrehozás** megnyitásához a **virtuális hálózati átjáró létrehozása** lap.

   ![Create virtual network gateway page fields](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Create virtual network gateway page fields")

4. Az a **virtuális hálózati átjáró létrehozása** lapon, töltse ki a virtuális hálózati átjáró értékeit:

   - **Név**: Adjon meg egy nevet az átjáró objektumot hoz létre. Ez a név nem egyezik az átjáró-alhálózat neve. 

   - **Átjáró típusa**: válasszon **VPN** VPN-átjárókhoz. 

   - **VPN típusa**: válassza ki a konfigurációjához megadott VPN-típust. A legtöbb konfigurációt igényelnek a **útvonalalapú** VPN-típust.

   - **SKU**: válassza ki az átjáró SKU-ját a legördülő listából. A legördülő listában szereplő SKU-k a kiválasztott VPN-típustól függenek. Az átjáró-termékváltozatokkal kapcsolatos további információkért lásd: [Gateway SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku) (Átjáró-termékváltozatok).

      Csak válassza **aktív – aktív üzemmód engedélyezése** létrehozásánál egy átjáró aktív-aktív konfigurációban. Ha nem ez a helyzet, ne válassza ki a beállítást.
  
   - **Hely**: Előfordulhat, hogy kell görgetnie a **hely**. Állítsa be **hely** arra a helyre, ahol a virtuális hálózat is található. Ha például **USA nyugati RÉGIÓJA**. Ha a hely nem állít be a régió, ahol a virtuális hálózat is található, nem fog megjelenni a legördülő listából válassza ki a virtuális hálózat kiválasztása.

   - **Virtuális hálózat**: Válassza ki azt a virtuális hálózatot, amelyhez hozzá kívánja adni az átjárót. Válassza ki **virtuális hálózati** megnyitásához a **válasszon egy virtuális hálózatot** lapon, és válassza ki a virtuális hálózathoz. Ha a virtuális hálózat nem jelenik meg, ellenőrizze, hogy a **hely** mező értéke pedig a régió, ahol a virtuális hálózat is található.

   - **Átjáróalhálózat címtartománya**: csak láthatja ezt a beállítást a korábban létrehozott átjáró-alhálózatot a virtuális hálózat. Ha korábban már létrehozott egy érvényes átjáró-alhálózatot, akkor ez a beállítás nem jelenik meg.

   - **Nyilvános IP-cím**: Ezzel a beállítással a nyilvános IP-címobjektumot, amely a VPN-átjáróhoz van társítva. A nyilvános IP-címet a rendszer dinamikusan rendeli hozzá ehhez az objektumhoz a VPN-átjáró létrehozásakor. A VPN gateway jelenleg támogatja a csak *dinamikus* nyilvános IP-címek hozzárendelését. Azonban a dinamikus kiosztási nem jelenti azt, hogy az IP-cím módosul a VPN gatewayhez való hozzárendelése után. Csak ha az átjárót törli, majd újra létrehozza a nyilvános IP-cím módosításainak van. Nem módosul átméretezés, alaphelyzetbe állítás, illetve a VPN Gateway belső karbantartása/frissítése során.
    
      - Hagyja bejelölve az **Új létrehozása** lehetőséget.

      - A szövegmezőben adja meg egy nevet a nyilvános IP-cím.

   - **BGP ASN konfigurálása**: hagyja, a beállítás nincs bejelölve, kivéve, ha a konfiguráció kifejezetten igényel. Ha ezt a beállítást igényel, az alapértelmezett ASN szám van *65515*, amelyet módosíthat.
     
5. Ellenőrizze a beállításokat, és válassza ki **létrehozás** a VPN-átjáró létrehozásához. A rendszer érvényesíti a beállításokat, és láthatja a **üzembe helyezése virtuális hálózati átjáró** csempét az irányítópulton. Az átjáró létrehozása akár 45 percet is igénybe vehet. Előfordulhat, hogy a kész állapot megjelenítéséhez frissítenie kell a portáloldalt.

6. Miután létrehozta az átjárót, ellenőrizze az IP-cím van hozzárendelve a portálon a virtuális hálózat megtekintésével. Az átjáró csatlakoztatott eszközként fog megjelenni. Kiválaszthatja, hogy a csatlakoztatott eszközre (azaz a virtuális hálózati átjáróra) kattintva további információ.