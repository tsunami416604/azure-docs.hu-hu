---
title: "A klasszikus portálon ExpressRoute egy virtuális hálózat és az átjáró konfigurálása |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan használja a klasszikus telepítési modell és a klasszikus portálon ExpressRoute virtuális hálózat beállítása."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 688817d9-51c8-4372-9af8-33fa098c7c5a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2016
ms.author: cherylmc
ms.openlocfilehash: f62254b2a7df50aa55a2a49009702848a9aecebd
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Az ExpressRoute virtuális hálózat létrehozása a klasszikus portál
A cikkben ismertetett végigvezeti egy virtuális hálózatot és egy virtuális hálózati átjáró konfigurálása az ExpressRoute a klasszikus üzembe helyezési modellben és a klasszikus portál használatával.

Ha a Resource Manager üzembe helyezési modellel kapcsolatos útmutatásért, használhatja a következő cikkeket: [virtuális hálózat létrehozása a PowerShell használatával](../virtual-network/virtual-networks-create-vnet-arm-ps.md) és [egy erőforrás-kezelő virtuális hálózatba a VPN-átjáró hozzáadása ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Tudnivalók az Azure üzembehelyezési modellekről**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="create-a-classic-vnet-and-gateway"></a>Klasszikus virtuális hálózatot és átjáró létrehozása
Az alábbi lépéseket a klasszikus virtuális hálózat és a virtuális hálózati átjáró létrehozása. Ha már rendelkezik egy klasszikus virtuális hálózaton, tekintse meg a [konfigurálása egy létező klasszikus virtuális hálózatot](#config) című részben.

1. Jelentkezzen be a [klasszikus Azure portálra](http://manage.windowsazure.com).
2. Kattintson a képernyő bal alsó sarkában **új**. A navigációs ablakban kattintson a **Hálózati szolgáltatások**, majd a **Virtuális hálózat** lehetőségre. Kattintson az **Egyéni létrehozás** lehetőségre a konfigurációs varázsló elindításához.
3. Az a **virtuális hálózati adatok** lapján írja be a következőket:
   
   * **Név** – nevezze el a virtuális hálózatot. A virtuális hálózati név fogja használni, a virtuális gépek és PaaS-példányok, így előfordulhat, hogy nem szeretné a név túl bonyolult központi telepítésekor.
   * **Hely** – a hely közvetlenül kapcsolódik a fizikai helyének (régió) az erőforrások (VM) találhatók. Ha például azt szeretné, hogy a virtuális hálózaton üzembe helyezni kívánt virtuális gépek az USA keleti régiójában helyezkedjenek el, válassza ki azt helyként. A létrehozás után a virtuális hálózathoz tartozó régió nem módosítható.
4. A **DNS-kiszolgálók és VPN-kapcsolat** lapon adja meg az alábbi információkat, majd a továbblépéshez kattintson a jobb alsó Tovább nyílra. 
   
   * **DNS-kiszolgálók** – adja meg a DNS-kiszolgáló neve és IP-címet, vagy válassza ki a korábban már regisztrált DNS-kiszolgáló a helyi menüből. A beállítás nem hoz létre DNS-kiszolgálót. Lehetővé teszi, hogy megadja azokat a DNS-kiszolgálókat, amelyeket névfeloldásra kíván használni ennél a virtuális hálózatnál.
   * **Hely-hely kapcsolatot** -tartozó jelölőnégyzet bejelölésével **a telephelyek közötti VPN konfigurálása**.
   * **ExpressRoute** – jelölőnégyzet bejelölésével **használjon ExpressRoute**. Ez a beállítás csak akkor jelenik meg, ha a kiválasztott **a telephelyek közötti VPN konfigurálása**.
   * **Helyi hálózati** -kell egy helyi hálózati telephely rendelkezik ExpressRoute. Azonban egy ExpressRoute-kapcsolatot, ha a helyi hálózati telephelyhez megadott címelőtagokat lesz figyelembe véve. Ehelyett a Microsoft hirdetni keresztül az ExpressRoute-kapcsolatcsoport címelőtagokat útválasztásra lesz.<BR>Ha már rendelkezik egy helyi hálózati az ExpressRoute-kapcsolatot létrehozni, válassza ki azt a legördülő listából. Ha nem, válassza ki a **adjon meg egy új helyi hálózati**.
5. A **hely-hely kapcsolatot** lap jelenik meg, ha egy új helyi hálózati meg az előző lépésben kiválasztott. A helyi hálózat konfigurálásához írja be a következő adatokat, és kattintson a Tovább nyílra. 
   
   * **Név** -a nevét, a helyi (helyszíni) hívni kívánt hálózati telephelyhez.
   * **Címtér** – beleértve a kezdő IP- és CIDR (cím száma). A címtartomány adhatja meg, amíg nem az a virtuális hálózat címtartománya átfedésben. Általában ez a címtartomány a helyszíni hálózatokhoz lehet megadni, de ExpressRoute, ha ezek a beállítások nem használhatók. Azonban ez a beállítás szükséges ahhoz, hogy a helyi hálózat létrehozása a klasszikus portál használata esetén.
   * **Címtartomány felvétele** – Ez a beállítás nem fontos ExpressRoute.
6. Az a **virtuális hálózati címtereket** lapon. Adja meg a következő adatokat, majd kattintson a pipa ikonra az oldal jobb alsó részen a hálózat konfigurálásához. 
   
   * **Címtér** – beleértve a kezdő IP, és oldja meg a száma. Győződjön meg arról, hogy a megadott címterek nem fedhetnek át olyan a címterek, hogy rendelkezik a helyi hálózaton.
   * **Vegye fel az alhálózati** – beleértve a kezdő IP- és a cím száma. További alhálózatokat esetén nincs szükség.
   * **Átjáró alhálózatának hozzáadása** -hozzáadásához az átjáró alhálózatának kattintson ide. Az átjáróalhálózat kizárólag a virtuális hálózati átjáróhoz használatos, és kötelező ehhez a konfigurációhoz.<BR>Az átjáró-alhálózatot CIDR (cím száma) az ExpressRoute /28 kell lennie, vagy nagyobb (/ 27- / 26 stb.). Ez lehetővé teszi az alhálózat elegendő IP-cím engedélyezése a konfiguráció működéséhez. A klasszikus portálon Ha be van jelölve a jelölőnégyzet bejelölésével ExpressRoute, használja a portál megadja egy átjáróalhálózat /28.  A CIDR-cím száma a klasszikus portálon nem lehet beállítani. Az átjáró alhálózatának frissítésként jelenik meg **átjáró** a klasszikus portálon, bár a valódi név, az átjáró alhálózatának létrehozott ténylegesen **GatewaySubnet**. Ez a név megtekintheti a PowerShell használatával vagy az Azure portálon.
7. Kattintson a lap alján található pipára. Ekkor megkezdődik a virtuális hálózat létrehozása. Amikor elkészült, látni fogja **Created** tartozó **állapot** a a **hálózatok** oldal a klasszikus portálon.

## <a name="gw"></a>Az átjáró létrehozása
1. Az a **hálózatok** lapon, kattintson a virtuális hálózat imént létrehozott, majd **irányítópult** az oldal tetején.
2. Alján a **irányítópult** kattintson **átjáró létrehozása** válassza **dinamikus útválasztási**. Kattintson a **Igen** annak megerősítéséhez, hogy egy átjáró létrehozásához.
3. Indulásakor az átjáró létrehozása megjelenik egy üzenet azzal kapcsolatban, hogy az átjáró elindult tudja. Az átjáró létrehozása akár 45 percig is eltarthat.
4. A hálózat csatolása expressroute-kapcsolatcsoporthoz. A cikk utasításait [Vnetek csatolása ExpressRoute-Kapcsolatcsoportok](expressroute-howto-linkvnet-classic.md).

## <a name="config"></a>Egy létező klasszikus virtuális hálózatot ExpressRoute konfigurálása
Ha már rendelkezik egy klasszikus virtuális hálózaton, konfigurálhatja úgy, hogy a klasszikus portálon ExpressRoute csatlakozni. A beállítások ugyanazok, mint a szakasz fenti, ezért olvassa végig ezek a szakaszok Ismerkedjen meg a szükséges beállításokat. Ha azt szeretné, ExpressRoute/pont-pont vizsgálatát a kísérő kapcsolat létrehozásához, lásd: [Ez a cikk](expressroute-howto-coexist-classic.md) lépéseit. Ezek eltérnek a cikkben található lépéseket.

1. A helyi hálózat létrehozásához, mielőtt frissíti a VNet beállításait a többi szükséges. Hozzon létre egy új helyi hálózatot, amely pedig szükséges a klasszikus portálon keresztül ExpressRoute konfigurálásakor, kattintson a **új**  **>**  **hálózati szolgáltatások**  **>**  **Virtuális hálózati**  **>**  **hozzáadása helyi hálózati**. Kövesse a varázsló lépéseit a helyi hálózat létrehozásához.
2. Használjon **konfigurálása** lap, a többi a Vnethez tartozó beállítás frissítése, és társítsa a virtuális hálózat, a helyi hálózathoz.
3. Lépjen a beállításainak konfigurálása után a [létrehozhatja az átjárót](#gw) című szakaszát, létrehozhatja az átjárót.

## <a name="next-steps"></a>Következő lépések
* Ha szeretne virtuális gépek felvétele a virtuális hálózat, lásd: [képzési terveket, a virtuális gépek](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
* Ha azt szeretné, további információt az ExpressRoute, tekintse meg a [ExpressRoute – áttekintés](expressroute-introduction.md).

