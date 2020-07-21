---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc94f31887526f387413f78fe3270784a4e3bd88
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86525164"
---
1. A virtuális WAN portál lapjának **kapcsolat** szakaszában válassza a **VPN-helyek** lehetőséget a VPN-helyek lap megnyitásához.
2. A **VPN-helyek** lapon kattintson a **+Hely létrehozása** elemre.

   ![Alapbeállítások](./media/virtual-wan-tutorial-site-include/basics.png "Alapbeállítások")
3. A **VPN-hely létrehozása** lap **alapok** lapján végezze el a következő mezőket:

    * **Régió** – előzőleg helynek nevezzük. Ez az a hely, ahol létre szeretné hozni a hely erőforrását a alkalmazásban.
    * **Név** – a név, amellyel a helyszíni helyre hivatkozni kíván.
    * **Eszköz szállítója** – a VPN-eszköz gyártójának neve (például: Citrix, Cisco, Barracuda). Így az Azure csapata jobban megismerheti a környezetet, hogy további optimalizálási lehetőségeket adjon a jövőben, vagy segítséget nyújtson a hibák megoldásához.
    * **Border Gateway Protocol** – az engedélyezés beállítás azt jelenti, hogy a hely összes kapcsolata BGP engedélyezve lesz. Végül beállítja a BGP-adatokat a VPN-helyről a hivatkozások szakaszban található minden hivatkozáshoz. A BGP virtuális WAN-on való konfigurálása egyenértékű a BGP Azure-beli virtuális hálózati átjáró VPN-en való konfigurálásával. A helyszíni BGP-társ címe nem lehet azonos a VPN által az eszközre vagy a VPN-hely VNet-címére vonatkozó nyilvános IP-címmel. Használjon egy másik IP-címet a VPN-eszközön a BGP-társ IP-címéhez. Az eszközön található visszacsatolási interfészhez rendelt címek. Ezt a címeket a helyet képviselő megfelelő VPN-helyen adhatja meg. A BGP-előfeltételeket lásd: [a BGP és az Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). A VPN-kapcsolatot bármikor szerkesztheti, hogy a BGP-paramétereket (a kapcsolaton és a AS #-ban) frissítse, ha a VPN-hely BGP-beállítása engedélyezve van.
    * **Magánhálózati címterület** – a helyszíni helyen található IP-címtartomány. Az erre a címtérre címzett forgalom át lesz irányítva a helyszíni helyre. Erre akkor van szükség, ha a BGP nincs engedélyezve a webhelyhez.
    * **Hubok** – az a hub, amelyhez csatlakozni szeretne a helyhez. A helyek csak olyan hubokhoz csatlakoztathatók, amelyeken VPN Gateway található. Ha nem látja a hubot, először hozzon létre egy VPN-átjárót az adott központban.
4. Válassza a **hivatkozások** lehetőséget az ág fizikai hivatkozásaival kapcsolatos információk hozzáadásához. Ha egy virtuális WAN-partner CPE-eszközzel rendelkezik, egyeztessen velük, hogy az információk az Azure-ral együtt, a saját rendszeréből való feltöltésük részeként legyenek-e kicserélve.

   ![linkek](./media/virtual-wan-tutorial-site-include/links.png "Hivatkozások")

    * **Hivatkozás neve** – a VPN-helyen található fizikai hivatkozáshoz megadni kívánt név. Példa: mylink1.
    * **Szolgáltató neve** : a VPN-helyen található fizikai hivatkozás neve. Példa: ATT, Verizon.
    * **Sebesség** – ez a VPN-eszköz sebessége a fiókiroda helyén. Például: 50, ami azt jelenti, hogy 50 Mbps a VPN-eszköz sebessége a fiókirodában.
    * **IP-cím/FQDN** – a helyszíni eszköz nyilvános IP-címe ezen a hivatkozáson keresztül. Opcionálisan megadhatja a helyszíni VPN-eszköz magánhálózati IP-címét, amely a ExpressRoute mögött található. A teljes tartománynevet is megadhatja. Például: *Something.contoso.com*. A teljes tartománynév feloldható a VPN-átjáróról. Ez akkor lehetséges, ha a teljes tartománynevet üzemeltető DNS-kiszolgáló elérhető az interneten keresztül. Az IP-cím elsőbbséget élvez, ha az IP-cím és a teljes tartománynév is meg van adva.

      >[!NOTE]
      >* A teljes tartományneven egy IPv4-t támogat. Ha a teljes tartománynevet több IP-címhez szeretné feloldani, akkor a VPN-átjáró az első IP4-címet felveszi a listából. Az IPv6-címek jelenleg nem támogatottak.
      >* A VPN-átjáró olyan DNS-gyorsítótárat tart fenn, amely 5 percenként frissül. Az átjáró csak a leválasztott alagutak teljes tartománynevét próbálja meg feloldani. Az átjáró alaphelyzetbe állítása vagy A konfiguráció módosítása A teljes tartománynév feloldását is elindíthatja.
      >
5. A jelölőnégyzettel további hivatkozásokat is törölhet vagy adhat hozzá. VPN-helyek esetében négy hivatkozás támogatott. Ha például négy INTERNETSZOLGÁLTATÓval (internetszolgáltatóval) rendelkezik a fiókiroda helyén, négy hivatkozást hozhat létre. egy-egy INTERNETSZOLGÁLTATÓnál, és adja meg az egyes hivatkozások adatait.
6. Miután befejezte a mezők kitöltését, válassza a **felülvizsgálat + létrehozás** lehetőséget a hely ellenőrzéséhez és létrehozásához.
7. Tekintse meg az állapotot a VPN-helyek lapon. A hely **kapcsolódásra kerül a kapcsolathoz** , mert a hely még nem csatlakozott az elosztóhoz.
