---
title: 'Forgatókönyv: forgalom irányítása a NVA-n keresztül egyéni beállítások használatával'
titleSuffix: Azure Virtual WAN
description: Ez a forgatókönyv segítséget nyújt a forgalom NVA-n keresztüli átirányításához egy másik NVA az internethez kötött forgalomhoz.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: e1cf9faeab60264d491539256828151e496ade8f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267499"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Forgatókönyv: forgalom irányítása NVA – egyéni (előzetes verzió)

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a NVA (hálózati virtuális berendezés) forgatókönyvben a cél az, hogy átirányítsa a forgalmat egy NVA keresztül a virtuális hálózatok és az ágak közötti kommunikációhoz, és egy másik NVA használjon az internethez kötött forgalomhoz. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tervezés

Ebben az esetben az elnevezési konvenciót fogjuk használni:

* "Service VNet" olyan virtuális hálózatok esetében, amelyekben a felhasználók az **1. ábrán**szereplő NVA (VNet 4) telepítették a nem internetes forgalom vizsgálatához.
* A "DMZ VNet" olyan virtuális hálózatok esetében, amelyekben a felhasználók telepítettek egy NVA az internetes forgalom vizsgálatához (VNet 5 az **1. ábrán**).
* "NVA küllők" a NVA VNet (VNet 1, VNet 2 és VNet 3 az **1. ábrán**) csatlakoztatott virtuális hálózatok esetében.
* "Hubok" a Microsoft által felügyelt virtuális WAN-hubokhoz.

A következő kapcsolati mátrix összefoglalja az ebben a forgatókönyvben támogatott folyamatokat:

**Kapcsolati mátrix**

| Forrás          | Címzett:|*NVA küllők*|*Szolgáltatás VNet*|*DMZ VNet*|*Ág statikus*|
|---|---|---|---|---|---|
| **NVA küllők**| &#8594;|      X |            X |   Társviszony-létesítés |    Statikus    |
| **Szolgáltatás VNet**| &#8594;|    X |            X |      X    |      X       |
| **DMZ VNet** | &#8594;|       X |            X |      X    |      X       |
| **Ágak** | &#8594;|  Statikus |            X |      X    |      X       |

A kapcsolati mátrix minden cellája azt írja le, hogy egy virtuális WAN-kapcsolat (a folyamat "feladó" oldala, a sorfejlécek) megtanulja-e a cél előtagot (a folyamat "to" oldalát, a dőlt betűs oszlop fejléceit) egy adott forgalmi folyamat esetében. Az "X" azt jelenti, hogy a kapcsolat a virtuális WAN által natív módon van megadva, a "statikus" pedig azt jelenti, hogy a virtuális WAN a statikus útvonalak használatával biztosít kapcsolatot. Lássuk részletesen a különböző sorokban:

* NVA küllők:
  * A küllők más küllőket érnek el közvetlenül a virtuális WAN-hubokon.
  * A küllők a szolgáltatás VNet mutató statikus útvonalon keresztül kapnak kapcsolatot az ágakkal. Nem kell megtanulniuk az ágak adott előtagjait (ellenkező esetben ez lenne konkrétabb, és felülbírálja az összegzést).
  * A küllők internetes forgalmat küldenek a DMZ-VNet közvetlen VNet-közvetítéssel.
* Ágak
  * Az ágak a szolgáltatás VNet mutató statikus útvonalon keresztül fognak megszólalni. Nem kell megtanulniuk a virtuális hálózatok meghatározott előtagokat, amelyek felülbírálják az összegzett statikus útvonalat.
* A szolgáltatás VNet hasonló lesz egy megosztott szolgáltatások VNet, amelynek minden VNet és ág esetében elérhetőnek kell lennie.
* A DMZ-VNet nem kell a virtuális WAN-kapcsolaton keresztül csatlakoznia, mivel az egyetlen támogatott forgalom a közvetlen VNet-társításokat veszi át. Ugyanakkor ugyanazt a csatlakozási modellt fogjuk használni, mint a DMZ VNet a konfiguráció egyszerűsítése érdekében.

Így a kapcsolati mátrix három különböző csatlakozási mintát biztosít, amelyek három útválasztási táblázatra fordíthatók le. A különböző virtuális hálózatok tartozó társítások a következők lesznek:

* NVA küllők:
  * Társított útválasztási táblázat: **RT_V2B**
  * Propagálás az útválasztási táblákba: **RT_V2B** és **RT_SHARED**
* NVA virtuális hálózatok (belső és internetes):
  * Társított útválasztási táblázat: **RT_SHARED**
  * Propagálás az útválasztási táblákba: **RT_SHARED**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **RT_SHARED** és **alapértelmezett**

Ezekre a statikus útvonalakra azért van szükségünk, hogy a VNet és a VNet közötti forgalom a szolgáltatás VNet NVA (VNet 4) haladjon át:

| Leírás | Útválasztási táblázat | Statikus útvonal              |
| ----------- | ----------- | ------------------------- |
| Ágak    | RT_V2B      | 10.2.0.0/16 – > vnet4conn  |
| NVA küllők  | Alapértelmezett     | 10.1.0.0/16 – > vnet4conn  |

A Virtual WAN már tudja, hogy melyik kapcsolattal küldi el a csomagokat, de a kapcsolatnak tudnia kell, mi a teendő a csomagok fogadásakor: ez az a hely, ahol a kapcsolati útválasztási táblák vannak használatban.

| Leírás | Kapcsolat | Statikus útvonal            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 – > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 – > 10.4.0.5 |

Ezen a ponton mindennek a helyén kell lennie.

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektúra

Az **1. ábrán**egy hub, **1. hub**található.

* Az **1. hub** közvetlenül kapcsolódik a NVA virtuális hálózatok **VNet 4** és a **VNet 5**rendszerhez.

* Az 1., 2. és 3. virtuális hálózatok és az ágak (VPN/ER/P2S) közötti adatforgalmat a rendszer a **VNet 4 NVA** -10.4.0.5 keresztül elvárta.

* Az 1., 2. és 3. virtuális hálózatok összes internetre kötött forgalma a **VNet 5 NVA** -10.5.0.5 keresztül várható.

**1. ábra**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="1. ábra":::

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

Az Útválasztás NVA-n keresztüli beállításához a következő lépéseket kell figyelembe venni:

1. Ahhoz, hogy az internetre kötött forgalom a VNet 5 használatával menjen el, a virtuális hálózatok 1, 2 és 3 értékűnek kell lennie, hogy közvetlenül kapcsolódjon a VNet-kapcsolaton keresztül a VNet 5-öt. A 0.0.0.0/0 és a Next hop 10.5.0.5 virtuális hálózatok beállított UDR is szüksége van. A virtuális WAN jelenleg nem engedélyezi a következő ugrási NVA a 0.0.0.0/0 virtuális központban.

1. A Azure Portal navigáljon a virtuális hubhoz, és hozzon létre egy egyéni útválasztási táblázatot **RT_Shared** amely az útvonalakat az összes virtuális hálózatok-és ág-kapcsolaton keresztül továbbítja. A **2. ábrán**ezt a rendszer üres egyéni útválasztási táblázatként ábrázolja **RT_Shared**.

   * **Útvonalak:** Nem kell statikus útvonalakat hozzáadnia.

   * **Társítás:** Válassza a 4. és az 5. virtuális hálózatok, ami azt jelenti, hogy a virtuális hálózatok 4 és 5 kapcsolat az útválasztási táblázat **RT_Shared**van hozzárendelve.

   * **Propagálás:** Mivel azt szeretné, hogy az összes ág és VNet kapcsolata dinamikusan terjessze az útvonalakat az útválasztási táblázatba, válassza az ágak és az összes virtuális hálózatok lehetőséget.

1. Hozzon létre egy egyéni útválasztási táblázatot **RT_V2B** az 1., 2. és 3. virtuális hálózatok érkező forgalom az ágakba való irányításához.

   * **Útvonalak:** Vegyen fel egy összesített statikus útválasztási bejegyzést az ágakhoz (VPN/ER/P2S) (a **2. ábrán**10.2.0.0/16) a következő ugrással, a VNet 4 kapcsolattal. Egy statikus útvonalat is konfigurálnia kell a VNet 4 kapcsolatához a fiókirodák előtagjaihoz, és a következő ugrást kell megadnia a NVA adott IP-címéhez a VNet 4-ben.

   * **Társítás:** Válassza az összes virtuális hálózatok 1, 2 és 3 elemet. Ez azt jelenti, hogy az 1., 2. és 3. VNet-kapcsolatok ehhez az útválasztási táblázathoz lesznek hozzárendelve, és képesnek kell lenniük arra, hogy megismerjék az útvonalakat (statikus és dinamikus propagálással) ebben az útválasztási táblázatban.

   * **Propagálás:** A kapcsolatok propagálják az útvonalakat az útválasztási táblákhoz. Az 1., 2. és 3. virtuális hálózatok kiválasztásával engedélyezheti a terjesztési útvonalakat az 1., 2. és 3. virtuális hálózatok az útválasztási táblázatba. Nincs szükség a fiókirodák kapcsolatainak RT_V2Bra való propagálására, mivel a VNet-forgalom a VNet 4 NVA keresztül megy keresztül.
  
1. Szerkessze az útválasztási táblázat alapértelmezett **DefaultRouteTable**.

   Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat az alapértelmezett útválasztási táblázathoz van társítva. Az összes VPN-, ExpressRoute-és felhasználói VPN-kapcsolat továbbítja az útvonalakat ugyanahhoz az útválasztási táblákhoz.

   * **Útvonalak:** Vegyen fel egy összesített statikus útvonal-bejegyzést az 1., 2. és 3. számú virtuális hálózatok (10.1.0.0/16 a **2. ábrán**) a következő ugrással, mint a VNet 4 kapcsolat. A VNet 1, 2 és 3 összesítő előtagjaihoz is konfigurálnia kell egy statikus útvonalat a VNet 4 kapcsolata esetében, és a következő ugrást kell megadnia a NVA megadott IP-címére a VNet 4-ben.

   * **Társítás:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, és gondoskodjon arról, hogy a helyszíni fiókirodák kapcsolatai a *defaultroutetable*legyenek társítva.

   * **Propagálás forrása:** Győződjön meg arról, hogy az ágak (VPN/ER/P2S) beállítás van kiválasztva, így biztosítva, hogy a helyi kapcsolatok propagálják az útvonalakat a *defaultroutetable*.

**2. ábra**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="2. ábra" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Következő lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
