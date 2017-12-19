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
ms.date: 10/20/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: a21fdfbc4396f2b7aff50fae4ca796d8ea6a733b
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
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

## <a name="create"></a>Hozzon létre, és helyezze üzembe az ExpressRoute-kapcsolatcsoportot
### <a name="1-sign-in-to-the-azure-portal"></a>1. Jelentkezzen be az Azure Portalra
Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Hozzon létre egy új ExpressRoute-kapcsolatcsoportot
> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoportot lesz számlázva abban a pillanatban a szolgáltatási kulcs ad ki. Győződjön meg arról, hogy a művelet végrehajtása, ha a kapcsolat szolgáltatójánál kiépíteni a kapcsolat készen áll-e.
> 
> 

1. ExpressRoute-kapcsolatcsoportot hozhat létre egy új erőforrás létrehozása beállítás kiválasztásával. Kattintson a **új** > **hálózati** > **ExpressRoute**, a következő ábrán látható módon:

  ![ExpressRoute-kapcsolatcsoport létrehozása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Miután rákattintott **ExpressRoute**, láthatja a **létrehozása ExpressRoute-kapcsolatcsoportot** lap. Ha kitöltés alatt az értékeket, ezen az oldalon, győződjön meg arról, hogy megadja a helyes SKU-réteg (Standard vagy prémium) és a számlázási modellt (korlátlan vagy Metered) mérési adatokat.

  ![A Termékváltozat réteg és a mérési adatok konfigurálása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Réteg** meghatározza, hogy egy ExpressRoute standard vagy ExpressRoute prémium bővítmény engedélyezve van-e. Megadhat **szabványos** lekérni a standard Termékváltozat vagy **prémium** a prémium szintű bővítménnyel.
  * **Az adathasználat mérését** határozza meg a számlázási. Megadhat **Metered** díjköteles adatforgalom tervhez és **korlátlan** egy adatforgalmi a. Vegye figyelembe, hogy a számlázási típusának módosíthatja **Metered** való **korlátlan**, de nem módosíthatja a típusát a **korlátlan** való **Metered**.
  * **Társviszony-létesítési helye** a fizikai hely, ahol vannak, a Microsoft társviszony.

    > [!IMPORTANT]
    > A társviszony-létesítési helye jelzi a [helynév](expressroute-locations.md) hol vannak a Microsoft társviszony. Ez az **nem** "Hely" tulajdonság, amely hivatkozik a földrajzi hely, ahol az Azure hálózati erőforrás-szolgáltató csatolva. Nincsenek kapcsolatban, de napjainkban célszerű válassza ki a hálózati erőforrás-szolgáltató földrajzilag megközelíti a kör társviszony-létesítési helye.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. A Kapcsolatcsoportok és tulajdonságok megtekintése
**A kapcsolatok megtekintése**

Megtekintheti a áramköröket kiválasztásával létrehozott **összes erőforrás** a bal oldali menüben.

![Kapcsolatok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**A Tulajdonságok megtekintése**

A kapcsolat tulajdonságainak megtekintéséhez jelölje ki. Az a **áttekintése** a kör lapon, a szolgáltatás kulcsmező megjelenik a szolgáltatás kulcs. A kör szolgáltatáskulcsa másolja, és adja át azt le a szolgáltató a telepítési folyamat befejezéséhez. Kör szolgáltatáskulcsának csak az a kapcsolatcsoport elő.

![Tulajdonságok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. A szolgáltatás kulcs küldése a kapcsolat szolgáltatójánál történő üzembe helyezéséhez
Ezen a lapon **szolgáltató állapota** információt nyújt a jelenlegi állapotában a szolgáltatói oldalon kiépítés. **Állapot áramkör** állapotát biztosít a Microsoft oldalon. Kiépítés állapotok áramkör kapcsolatos további információkért tekintse meg a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) cikk.

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolat a következő állapotban van:

Szolgáltató állapota: nincs telepítve<BR>
Áramkör állapot: engedélyezve

![Üzembe helyezési folyamat elindítása](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

A kapcsolatcsoport módosítja a következő állapotot, ha a kapcsolat szolgáltatójánál folyamatban van, lehetővé téve az Ön:

Szolgáltató állapota: kiépítés<BR>
Áramkör állapot: engedélyezve

Meg kell tudni használni az ExpressRoute-kapcsolatcsoportot a következő állapotban kell lennie:

Szolgáltató állapota: kiépítése<BR>
Áramkör állapot: engedélyezve

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Ellenőrizze rendszeresen a kapcsolatcsoport kulcs állapotát és az állapot
A kapcsolatcsoport, hogy az lehetőséget most érdekelt tulajdonságainak megtekintése Ellenőrizze a **szolgáltató állapota** , és győződjön meg arról, hogy átkerült az **kiépítve** a folytatás előtt.

![Kör és szolgáltató állapota](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Az útválasztó-konfiguráció létrehozása
Lépésenkénti útmutatásért tekintse meg a [ExpressRoute-áramkör útválasztási konfigurációja](expressroute-howto-routing-portal-resource-manager.md) cikk létrehozásához és módosításához a kapcsolatcsoport esetében.

> [!IMPORTANT]
> Ezek az utasítások csak a szolgáltatók által biztosított réteg 2 internetkapcsolati szolgáltatás használatával létrehozott kapcsolatok vonatkoznak. A szolgáltató által kezelt használata réteg (általában az IP VPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálja és kezeli az Ön útválasztást.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
A következő csatolja az ExpressRoute-kapcsolatcsoportot egy virtuális hálózatot. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-Kapcsolatcsoportok](expressroute-howto-linkvnet-arm.md) a Resource Manager üzembe helyezési modellel végzett munka során a következő cikket.

## <a name="status"></a>Egy ExpressRoute-kapcsolatcsoport állapotának beolvasása
A kapcsolatcsoport állapotának lehetőséget, és tekintse meg a – Áttekintés lapon tekintheti meg. 

## <a name="modify"></a>Egy ExpressRoute-kapcsolatcsoportot módosítása
ExpressRoute-kapcsolatcsoportot egyes tulajdonságait módosíthatja kapcsolat befolyásolása nélkül. A sávszélesség, SKU, számlázási modellt módosíthatja, és annak engedélyezése, hogy klasszikus műveletek a **konfigurációs** lap. Korlátai és korlátozásai kapcsolatos tudnivalókat lásd: a [ExpressRoute – gyakori kérdések](expressroute-faqs.md). 

Állásidő nélkül a következő feladatokat végezheti el:

* Engedélyezi vagy letiltja az ExpressRoute-kapcsolatcsoportot ExpressRoute prémium bővítményt.
* Növelje a ExpressRoute-kapcsolatcsoportot sávszélességét, feltéve, hogy kapacitás érhető el a porton. Alacsonyabb verziójúra változtatása a sávszélességet a kapcsolat nem támogatott. 
* A mérési terv módosítása *díjköteles adatok* való *korlátlan adatforgalom*. A mérési terv módosítása az korlátlan adatforgalom díjköteles adatok nem támogatott.
* Engedélyezheti és letilthatja *klasszikus műveletek engedélyezése*.

> [!IMPORTANT]
> Előfordulhat, hogy újra létrehozni az ExpressRoute-kapcsolatcsoport, ha nincs elég kapacitás a meglévő porton. A kapcsolat nem frissíthető, ha nincsenek további kapacitást érhető el az adott helyhez.
>
> Nem csökkenti a sávszélesség az ExpressRoute-kapcsolatcsoportot megszakítása nélkül. Alacsonyabb verziójúra változtatása a sávszélesség szükséges, hogy az ExpressRoute-kapcsolatcsoport kiosztásának megszüntetése és majd építenie az új ExpressRoute-kapcsolatcsoportot.
> 
> A prémium szintű bővítmény művelet letiltása sikertelen lehet, amely nagyobb, mint mi a szabványos kör megengedett erőforrások használata.
> 
> 

Egy ExpressRoute-kapcsolatcsoportot módosításához kattintson **konfigurációs**.

![Kör módosítása](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>Megszüntetés és az ExpressRoute-kapcsolatcsoport törlése
Az ExpressRoute-kapcsolatcsoportot kiválasztásával törölheti a **törlése** ikonra. Vegye figyelembe a következőket:

* Az ExpressRoute-kapcsolatcsoport az összes virtuális hálózatot kell választható. Ez a művelet sikertelen lesz, ha ellenőrizze, hogy virtuális hálózatok a kapcsolatcsoport van csatolva.
* Ha az ExpressRoute-kapcsolatcsoport szolgáltatás szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépítve** kiosztásának megszüntetése a kapcsolatcsoport az oldalon, hogy a szolgáltató együttműködve. A Folytatás kiszámlázni Önnek, amíg a szolgáltató befejeződött, a kapcsolat megszüntetés, és értesíti, és az erőforrásokat.
* Ha a szolgáltató rendelkezik platformelőfizetés a kapcsolatcsoport (üzembe helyezési állapota szolgáltató értéke **nincs kiépítve**), a kapcsolatcsoport törlése. Ezzel leállítja a számlázási a kör.

## <a name="next-steps"></a>Következő lépések
Miután létrehozta a kapcsolatcsoport, folytassa a következő lépéseket:

* [Létrehozásához és módosításához az ExpressRoute-kapcsolatcsoport esetében routing](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat csatolása az ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-arm.md)