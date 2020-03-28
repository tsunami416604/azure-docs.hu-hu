---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488935"
---
1. A virtuális wan portállapján a **Kapcsolat** szakaszban válassza a **VPN-webhelyek lehetőséget** a VPN-webhelyek lap megnyitásához.
2. A **VPN-helyek** lapon kattintson a **+Hely létrehozása** elemre.

   ![Alapvető beállítások](./media/virtual-wan-tutorial-site-include/basics.png "Alapvető beállítások")
3. A **VPN-hely létrehozása** lap Alapjai lapján **hajtsa** végre a következő mezőket:

    * **Régió** – korábban helyként emlegetett. Ebben a helyben szeretné létrehozni ezt a helyerőforrást.
    * **Név** – Az a név, amellyel a helyszíni webhelyre szeretne hivatkozni.
    * **Eszközszállító** – a VPN-eszköz szállítójának neve (például: Citrix, Cisco, Barracuda). Ezzel segíthet az Azure Team jobban megérteni a környezettovábbi optimalizálási lehetőségek a jövőben, vagy segít a hibaelhárítás.
    * **Border Gateway Protocol** - Engedélyezése azt jelenti, hogy a helyről származó összes kapcsolat bgp engedélyezve lesz. A Hivatkozások szakaszban a VPN-helyről származó egyes hivatkozásokhoz végül be kell állítania a BGP-adatokat. A BGP konfigurálása virtuális WAN-on egyenértékű a BGP konfigurálásával egy Azure virtuális hálózati átjáró VPN-en. A helyszíni BGP-társcím nem egyezhet meg a VPN és az eszköz nyilvános IP-címe vagy a VPN-hely virtuális hálózatának címe. Használjon másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Ez lehet egy olyan cím is, amely az eszköz visszacsatolási hálózatához van rendelve. Ez azonban nem lehet APIPA (169.254.x.x) cím. Adja meg ezt a címet a helyet képviselő megfelelő VPN-helyen. A BGP előfeltételeiről a [BGP és az Azure VPN-átjáró című témakörben](../articles/vpn-gateway/vpn-gateway-bgp-overview.md)látható. A VPN-hely BGP-beállítása engedélyezése után bármikor szerkesztheti a VPN-kapcsolatot a BGP-paraméterek (Társviszony-létesítési IP-cím és az AS #) frissítéséhez.
    * **Privát címtér** – A helyszíni helyen található IP-címterület. Az erre a címtérre címzett forgalom át lesz irányítva a helyszíni helyre. Erre akkor van szükség, ha a BGP nincs engedélyezve a webhelyen.
    * **Hubok** – Az a hub, amelyhez csatlakozniszeretne a webhelyhez. Egy hely csak vpn-átjáróval rendelkező elosztókhoz csatlakoztatható. Ha nem lát egy hubot, hozzon létre egy VPN-átjárót a hubban.
4. Válassza a **Hivatkozások** lehetőséget az ágon lévő fizikai hivatkozásokra vonatkozó információk hozzáadásához. Ha rendelkezik egy virtuális wan partner CPE-eszközzel, kérdezze meg, hogy ezeket az adatokat kicserélik-e az Azure-ral a rendszerükből beállított fiókadatok feltöltésének részeként.

   ![Linkek](./media/virtual-wan-tutorial-site-include/links.png "Hivatkozások")

    * **Hivatkozás neve** – A VPN-helyen található fizikai hivatkozáshoz megadandó név. Példa: mylink1.
    * **Szolgáltató neve** – A VPN-helyen található fizikai hivatkozás neve. Példa: ATT, Verizon.
    * **Sebesség** – Ez a VPN-eszköz sebessége az elágazási helyen. Példa: 50, ami azt jelenti, 50 Mbps a VPN-eszköz sebessége a telephelyen.
    * **IP-cím** - A hivatkozást használó on-prem eszköz nyilvános IP-címe. Opcionálisan megadhatja az ExpressRoute mögött álló helyszíni VPN-eszköz privát IP-címét.
5. A jelölőnégyzet segítségével további hivatkozásokat törölhet vagy adhat hozzá. VPN-helyenként négy hivatkozás támogatott. Ha például a telephelyen négy internetszolgáltató (internetszolgáltató) található, négy hivatkozást hozhat létre. minden egyes isp-nként, és adja meg az egyes hivatkozásokra vonatkozó információkat.
6. Miután befejezte a mezők kitöltését, válassza a **Véleményezés + létrehozás** lehetőséget a webhely ellenőrzéséhez és létrehozásához.
7. Az állapot megtekintése a VPN-webhelyek lapon. A hely a **Kapcsolat szükséges,** mert a hely még nem csatlakozik a hubhoz.