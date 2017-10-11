---
title: "Létrehozásához és módosításához ExpressRoute-kapcsolatcsoportot: Azure-portál |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan létrehozásához, telepítéséhez, győződjön meg arról, frissítése, törlése és kiosztásának megszüntetése ExpressRoute-kapcsolatcsoportot."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: e3721cd3c031622788f553e71a6555b844f3f7dc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Létrehozásához és módosításához ExpressRoute-kapcsolatcsoportot
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Video - Azure-portálon](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk ismerteti a Azure ExpressRoute-kapcsolatcsoportot létrehozni az Azure portál és az Azure Resource Manager telepítési modell használatával. Az alábbi lépéseket is bemutatják a kapcsolatcsoport állapotának ellenőrzése, a frissítést, vagy törölje és kiosztásának megszüntetése azt.


## <a name="before-you-begin"></a>Előkészületek
* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás elkezdése előtt.
* Győződjön meg arról, hogy rendelkezik-e a hozzáférést a [Azure-portálon](https://portal.azure.com).
* Győződjön meg arról, hogy rendelkezik-e új hálózati erőforrások létrehozásához szükséges engedélyek. Ha nem rendelkezik a megfelelő engedélyekkel, forduljon a fiók rendszergazdájához.
* Is [videó](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) ahhoz, hogy jobb megértése érdekében a lépések megkezdése előtt.

## <a name="create-and-provision-an-expressroute-circuit"></a>Hozzon létre, és helyezze üzembe az ExpressRoute-kapcsolatcsoportot
### <a name="1-sign-in-to-the-azure-portal"></a>1. Jelentkezzen be az Azure Portalra
Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Hozzon létre egy új ExpressRoute-kapcsolatcsoportot
> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportot abban a pillanatban a szolgáltatási kulcs kiadott lesz terhelve. Győződjön meg arról, hogy a művelet végrehajtása, ha a kapcsolat szolgáltatójánál kiépíteni a kapcsolat készen áll-e.
> 
> 

1. ExpressRoute-kapcsolatcsoportot hozhat létre egy új erőforrás létrehozása beállítás kiválasztásával. Kattintson a **új** > **hálózati** > **ExpressRoute**, a következő ábrán látható módon:
   
    ![ExpressRoute-kapcsolatcsoport létrehozása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Miután rákattintott **ExpressRoute**, láthatja a **létrehozása ExpressRoute-kapcsolatcsoportot** panelen. Ha kitöltés alatt az értékeket a panel, győződjön meg arról, hogy megadja a helyes SKU réteg és az adatforgalom-mérést.
   
   * **Réteg** meghatározza, hogy egy ExpressRoute standard vagy ExpressRoute prémium bővítmény engedélyezve van-e. Megadhat **szabványos** lekérni a standard Termékváltozat vagy **prémium** a prémium szintű bővítménnyel.
   * **Az adathasználat mérését** határozza meg a számlázási. Megadhat **Metered** díjköteles adatforgalom tervhez és **korlátlan** egy adatforgalmi a. Vegye figyelembe, hogy a számlázási típusának módosíthatja **Metered** való **korlátlan**, de nem módosíthatja a típusát a **korlátlan** való **Metered**.
     
     ![A Termékváltozat réteg és a mérési adatok konfigurálása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> Felhívjuk a figyelmét arra, hogy a társviszony-létesítési helye jelzi a [helynév](expressroute-locations.md) hol vannak a Microsoft társviszony. Ez az **nem** "Hely" tulajdonság, amely hivatkozik a földrajzi hely, ahol az Azure hálózati erőforrás-szolgáltató csatolva. Nincsenek kapcsolatban, de napjainkban célszerű válassza ki a hálózati erőforrás-szolgáltató földrajzilag megközelíti a kör társviszony-létesítési helye. 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3. A Kapcsolatcsoportok és tulajdonságok megtekintése
**A kapcsolatok megtekintése**

Megtekintheti a áramköröket kiválasztásával létrehozott **összes erőforrás** a bal oldali menüben.

![Kapcsolatok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**A Tulajdonságok megtekintése**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Tulajdonságok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. A szolgáltatás kulcs küldése a kapcsolat szolgáltatójánál történő üzembe helyezéséhez
A panel **szolgáltató állapota** információt nyújt a jelenlegi állapotában a szolgáltatói oldalon kiépítés. **Állapot áramkör** állapotát biztosít a Microsoft oldalon. Kiépítés állapotok áramkör kapcsolatos további információkért tekintse meg a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) cikk.

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolatcsoport lesz a következő állapotot okozta:

Szolgáltató állapota: nincs telepítve<BR>
Áramkör állapot: engedélyezve

![Üzembe helyezési folyamat elindítása](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

A kapcsolatcsoport fogja módosítani a következő állapotot folyamatban van, lehetővé téve, a kapcsolat hitelesítésszolgáltató esetén:

Szolgáltató állapota: kiépítés<BR>
Áramkör állapot: engedélyezve

Meg kell tudni használni az ExpressRoute-kapcsolatcsoportot a következő állapotban kell lennie:

Szolgáltató állapota: kiépítése<BR>
Áramkör állapot: engedélyezve

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Ellenőrizze rendszeresen a kapcsolatcsoport kulcs állapotát és az állapot
A kapcsolatcsoport, hogy az lehetőséget most érdekelt tulajdonságainak megtekintése Ellenőrizze a **szolgáltató állapota** , és győződjön meg arról, hogy átkerült az **kiépítve** a folytatás előtt.

![Kör és szolgáltató állapota](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Az útválasztó-konfiguráció létrehozása
Lépésenkénti útmutatásért tekintse meg a [ExpressRoute-áramkör útválasztási konfigurációja](expressroute-howto-routing-portal-resource-manager.md) cikk létrehozásához és módosításához a kapcsolatcsoport esetében.

> [!IMPORTANT]
> Ezek az utasítások csak a szolgáltatók által biztosított réteg 2 internetkapcsolati szolgáltatás használatával létrehozott kapcsolatok vonatkoznak. A szolgáltató által kezelt használata réteg (általában az IP VPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálása és kezelése az Ön útválasztást.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
A következő csatolja az ExpressRoute-kapcsolatcsoportot egy virtuális hálózatot. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-Kapcsolatcsoportok](expressroute-howto-linkvnet-arm.md) a Resource Manager üzembe helyezési modellel végzett munka során a következő cikket.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoport állapotának beolvasása
A kapcsolatcsoport állapotának megtekintéséhez jelölje ki. 

![Egy ExpressRoute-kapcsolatcsoport állapotát](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>Egy ExpressRoute-kapcsolatcsoportot módosítása
ExpressRoute-kapcsolatcsoportot egyes tulajdonságait módosíthatja kapcsolat befolyásolása nélkül.

Állásidő nélkül a következőket teheti:

* Engedélyezi vagy letiltja az ExpressRoute-kapcsolatcsoportot prémium ExpressRoute bővítményt.
* Növelje a ExpressRoute-kapcsolatcsoportot sávszélességét, feltéve, hogy kapacitás érhető el a port. Vegye figyelembe, hogy a alacsonyabb verziójúra változtatása a sávszélességet a kapcsolat nem támogatott. 
* Díjköteles adatforgalom korlátlan adatokhoz a mérési terv módosítása Vegye figyelembe, hogy a mérési terv módosítása az korlátlan adatforgalom díjköteles adatok nem támogatott.
* Engedélyezheti és letilthatja *klasszikus műveletek engedélyezése*.

Korlátai és korlátozásai további információkért tekintse meg a [ExpressRoute – gyakori kérdések](expressroute-faqs.md).

Egy ExpressRoute-kapcsolatcsoportot módosításához kattintson a a **konfigurációs** az alábbi ábrán látható módon.

![Kör módosítása](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

A sávszélesség, SKU, számlázási modellt módosíthatja, és engedélyezi a klasszikus műveletek belül a konfigurációs panel.

> [!IMPORTANT]
> Előfordulhat, hogy újra létrehozni az ExpressRoute-kapcsolatcsoport, ha nincs elég kapacitás a meglévő porton. A kapcsolat nem frissíthető, ha nincsenek további kapacitást érhető el az adott helyhez.
>
> Nem csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot megszakítása nélkül. Alacsonyabb verziójúra változtatása a sávszélesség szükséges, hogy az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése és majd építenie az új ExpressRoute-kapcsolatcsoportot.
> 
> Prémium szintű bővítmény letiltása művelet sikertelen lesz, amely nagyobb, mint mi a szabványos kör megengedett erőforrások használata.
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Megszüntetés és az ExpressRoute-kapcsolatcsoport törlése
Az ExpressRoute-kapcsolatcsoportot kiválasztásával törölheti a **törlése** ikonra. Vegye figyelembe a következőket:

* Az ExpressRoute-kapcsolatcsoport az összes virtuális hálózatot kell választható. Ez a művelet sikertelen lesz, ha ellenőrizze, hogy virtuális hálózatok a kapcsolatcsoport van csatolva.
* Ha az ExpressRoute-kapcsolatcsoport szolgáltatás szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépítve** kiosztásának megszüntetése a kapcsolatcsoport az oldalon, hogy a szolgáltató együttműködve. Folytatjuk erőforrásokat és kiszámlázni Önnek, amíg a szolgáltató befejeződött, a kapcsolat megszüntetés, és értesítést küld nekünk.
* Ha a szolgáltató rendelkezik platformelőfizetés a kapcsolatcsoport (üzembe helyezési állapota szolgáltató értéke **nincs kiépítve**) a kapcsolatcsoport törölhet. A kör számlázási leállítása

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a kapcsolatcsoport, győződjön meg arról, hogy akkor tegye a következőket:

* [Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoport esetében routing](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-arm.md)

