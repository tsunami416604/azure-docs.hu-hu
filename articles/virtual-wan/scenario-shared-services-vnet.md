---
title: 'Forgatókönyv: útvonal a megosztott szolgáltatások virtuális hálózatok'
titleSuffix: Azure Virtual WAN
description: Útválasztási forgatókönyvek – az útvonalakat úgy állíthatja be, hogy a megosztott szolgáltatások VNet hozzáférjenek egy olyan munkaterheléssel, amelyet minden VNet és elágazáshoz el szeretne érni.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 6045c491ea68d759b2a1739e20aa2f12b8520c87
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88006485"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Forgatókönyv: útvonal a megosztott szolgáltatások virtuális hálózatok

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a forgatókönyvben az útvonalakat úgy kell beállítani, hogy hozzáférjenek egy **megosztott szolgáltatás** VNet olyan számítási feladatokhoz, amelyeket minden VNet és ág (VPN/er/P2S) számára szeretne elérni. Ilyen megosztott munkaterhelések például olyan Virtual Machines tartalmazhatnak, mint a tartományvezérlők és a fájlmegosztás, illetve az Azure [privát végpontokon](../private-link/private-endpoint-overview.md)keresztül belsőleg elérhető Azure-szolgáltatások.

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Tervezés

A forgatókönyv követelményeinek összefoglalásához használhatunk egy kapcsolati mátrixot. A mátrixban minden cella azt írja le, hogy egy virtuális WAN-kapcsolat (a folyamat "from" oldaláról, a tábla sorainak fejléce) megtanulja-e a cél előtagját (a folyamat "to" oldalát, a táblázat oszlopainak fejléceit) egy adott forgalmi folyamat esetében.

**Kapcsolati mátrix**

| Forrás             | Címzett:   |*Elkülönített virtuális hálózatok*|*Megosztott VNet*|*Ágak*|
|---|---|---|---|---|
|**Elkülönített virtuális hálózatok**|&#8594;|                |        X        |       X      |
|**Megosztott virtuális hálózatok**  |&#8594;|       X        |        X        |       X      |
|**Ágak**      |&#8594;|       X        |        X        |       X      |

Az [elkülönített VNet-forgatókönyvhöz](scenario-isolate-vnets.md)hasonlóan ez a kapcsolati mátrix két különböző sor mintázatot biztosít, amelyek két útválasztási táblára fordíthatók (a megosztott szolgáltatások virtuális hálózatok és az ágak ugyanazzal a kapcsolati követelményekkel rendelkeznek). A virtuális WAN már rendelkezik alapértelmezett útválasztási táblázattal, ezért egy másik egyéni útválasztási táblázatra van szükségünk, amelyet a példában **RT_SHARED** fogunk hívni.

A virtuális hálózatok a **RT_SHARED** útválasztási táblázathoz lesz társítva. Mivel az ágakkal és a megosztott szolgáltatás virtuális hálózatok való kapcsolatra van szükségük, a megosztott szolgáltatás VNet és ágainak propagálnia kell a **RT_SHARED** (ellenkező esetben a virtuális hálózatok nem fogja tudni megtanulni az ágat és a megosztott VNet előtagokat). Mivel az ágak mindig az alapértelmezett útválasztási táblázathoz vannak társítva, és a kapcsolati követelmények megegyeznek a megosztott szolgáltatások virtuális hálózatok, a megosztott szolgáltatás virtuális hálózatok is társítani fogjuk az alapértelmezett útválasztási táblázathoz.

Ennek eredményeképpen ez a végső terv:

* Elkülönített virtuális hálózatok:
  * Társított útválasztási táblázat: **RT_SHARED**
  * Propagálás az útválasztási táblákba: **alapértelmezett**
* Megosztott szolgáltatások virtuális hálózatok:
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **RT_SHARED** és **alapértelmezett**
* Ágak
  * Társított útválasztási táblázat: **alapértelmezett**
  * Propagálás az útválasztási táblákba: **RT_SHARED** és **alapértelmezett**

> [!NOTE]
> Ha a virtuális WAN több régióban van üzembe helyezve, létre kell hoznia a **RT_SHARED** útválasztási táblázatot minden központban, és az egyes megosztott szolgáltatások VNet és az elágazási kapcsolatok útvonalait a terjesztési címkék használatával kell propagálni minden virtuális központ útválasztási tábláiba.

További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Munkafolyamat

A forgatókönyv konfigurálásához vegye figyelembe a következő lépéseket:

1. Azonosítsa a **megosztott szolgáltatások** VNet.
2. Hozzon létre egy egyéni útválasztási táblázatot. A példában **RT_SHAREDként**hivatkozunk az útválasztási táblázatra. Az útválasztási táblázat létrehozásának lépéseiért lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md). A következő értékeket használja útmutatóként:

   * **Társulási**
     * A **megosztott szolgáltatások VNet *kivételével* a virtuális hálózatok**válassza ki az elkülöníteni kívánt virtuális hálózatok. Ez azt jelenti, hogy az összes ilyen virtuális hálózatok (kivéve a megosztott szolgáltatások VNet) elérheti a célhelyet RT_SHARED útválasztási táblázat útvonalai alapján.

   * **Propagálás**
      * **Ágak**esetében az útvonalak ezen útválasztási táblázatba való propagálása a már kiválasztott útválasztási táblákon kívül is. Ennek a lépésnek a következtében a RT_SHARED útválasztási táblázat az összes ág-kapcsolat (VPN/ER/User VPN) útvonalát fogja megtanulni.
      * A **virtuális hálózatok**területen válassza ki a **megosztott szolgáltatások VNet**. Ennek a lépésnek a végrehajtása miatt RT_SHARED útválasztási táblázat a megosztott szolgáltatások VNet kapcsolatos útvonalakat fogja megtanulni.

Ez az alábbi ábrán látható útválasztási konfigurációt eredményezi:

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Megosztott szolgáltatások VNet" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).
