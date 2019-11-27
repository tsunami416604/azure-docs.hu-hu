---
title: Oktatóanyag – áramkör létrehozása és módosítása a ExpressRoute
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre, helyezhet üzembe, ellenőrizheti, frissítheti, törölheti és kiépítheti az ExpressRoute-áramkört.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 7327031a7cd05674e9823f21601aab34c859f540
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423559"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Oktatóanyag: ExpressRoute-kör létrehozása és módosítása

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk segít az Azure portal és az Azure Resource Manager üzemi modell használatával egy ExpressRoute-kapcsolatcsoportot létrehozni. Azt is ellenőrizze az állapotát, frissítése, törlése, vagy egy kapcsolatcsoport megszüntetése.

## <a name="before-you-begin"></a>Előkészületek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .
* Győződjön meg arról, hogy rendelkezik hozzáféréssel a [Azure Portalhoz](https://portal.azure.com).
* Győződjön meg arról, hogy új hálózati erőforrások létrehozásához szükséges engedélyek. Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiókadminisztrátorral.
* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create"></a>ExpressRoute-kör létrehozása és kiépítése

### <a name="1-sign-in-to-the-azure-portal"></a>1. Jelentkezzen be a Azure Portalba

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="2-create-a-new-expressroute-circuit"></a>2. hozzon létre egy új ExpressRoute áramkört

> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport számlázása a szolgáltatáskulcs pillanatától kezdve. Győződjön meg arról, hogy ha a kapcsolatszolgáltató üzembe helyezi a kapcsolatcsoportot készen áll a művelet végrehajtása.

1. Egy ExpressRoute-kapcsolatcsoportot is létrehozhat egy új erőforrás létrehozásának lehetősége kiválasztásával. Kattintson az **erőforrás létrehozása** > **hálózatkezelés** > **ExpressRoute**elemre, ahogy az a következő képen látható:

   ![ExpressRoute-kapcsolatcsoport létrehozása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Miután rákattintott a **ExpressRoute**elemre, megjelenik a **ExpressRoute-kör létrehozása** lap. Ha Ön az értékeket, ezen a lapon megadott információkat, győződjön meg arról, adja meg a megfelelő Termékváltozat-szint (Standard vagy prémium) és a számlázási modellt (korlátlan és forgalmi díjas) mérési adatokat.

   ![A Termékváltozat-szint és az adatforgalom-mérést konfigurálása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * A **szint** meghatározza, hogy engedélyezve van-e a ExpressRoute standard vagy a ExpressRoute Premium bővítmény. A prémium szintű bővítmény szabványos SKU-jának vagy **premiumjának** beszerzéséhez megadható a **standard** .
   * Az **adatmérés** meghatározza a számlázási típust. Megadhatja **a mért adatcsomag díjait** , és **korlátlan** számú adatcsomagot. Vegye figyelembe, hogy a számlázási típus a **mért** értékről **korlátlanra**módosítható.

     > [!IMPORTANT]
     > A típus nem módosítható **korlátlanról** **mértre**.

   * A társítás **helye** az a fizikai hely, ahol a Microsofttal való együttműködésre van szükség.

     > [!IMPORTANT]
     > A társítási hely azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol a Microsofttal való együttműködésre van szükség. Ez **nem** a "location" tulajdonsághoz van csatolva, amely arra a földrajzi helyre hivatkozik, ahol az Azure hálózati erőforrás-szolgáltató található. Amíg nem áll(nak), tanácsos válassza ki a hálózati erőforrás-szolgáltató földrajzilag közeli, a kapcsolatcsoport társviszony-létesítési helye.

### <a name="3-view-the-circuits-and-properties"></a>3. a áramkörök és a tulajdonságok megtekintése

**Az összes áramkör megtekintése**

A bal oldali menüben az **összes erőforrás** lehetőség kiválasztásával megtekintheti az összes létrehozott áramkört.

![Kapcsolatok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**A tulajdonságok megtekintése**

A kapcsolatcsoport tulajdonságainak megtekintéséhez jelölje ki. Az áramkör **Áttekintés** lapján a szolgáltatási kulcs megjelenik a szolgáltatás kulcsa mezőben. Másolja a kulcsot a kapcsolatcsoport kell, és adja át azt le a service provider, a kiépítési folyamat befejezéséhez. Kör szolgáltatáskulcsának csak a kapcsolatcsoporthoz.

![Tulajdonságok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. küldje el a szolgáltatási kulcsot a kapcsolat szolgáltatójának az üzembe helyezéshez

Ezen az oldalon a **szolgáltatói állapot** információkkal szolgál a kiépítés aktuális állapotáról a szolgáltatói oldalon. Az **áramkör állapota** a Microsoft oldalán található állapotot biztosítja. Az áramkör kiépítési állapotával kapcsolatos további információkért tekintse meg a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) című cikket.

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolatcsoport a következő állapotban van:

Szolgáltató állapota: nincs kiépítve<BR>
Kapcsolatcsoport-állapot: engedélyezve

![Üzembe helyezési folyamatának elindításához](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

A kapcsolatcsoport változik a következő állapotot, amikor a kapcsolatszolgáltató van folyamatban, amely lehetővé teszi az Ön számára:

Szolgáltató állapota: kiépítése<BR>
Kapcsolatcsoport-állapot: engedélyezve

ExpressRoute-kapcsolatcsoport segítségével tudja meg a következő állapotban kell lennie:

Szolgáltató állapota: kiépítve<BR>
Kapcsolatcsoport-állapot: engedélyezve

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. az áramköri kulcs állapotának és állapotának rendszeres időközönkénti keresése

Megtekintheti, hogy az Önt érdeklő, ha kiválasztja a kapcsolatcsoport tulajdonságainak. Ellenőrizze a **szolgáltató állapotát** , és győződjön meg arról, hogy a folytatás előtt átkerült a **kiépített** állapotba.

![Kapcsolatcsoport és a szolgáltató állapota](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. az útválasztási konfiguráció létrehozása

Részletes útmutatásért tekintse meg az [ExpressRoute-áramkör útválasztási konfigurációját](expressroute-howto-routing-portal-resource-manager.md) ismertető cikket az áramköri társítások létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak 2 réteg szolgáltatás kínáló szolgáltatóknál létrehozott Kapcsolatcsoportok vonatkoznak. Ha használja a szolgáltató által kínált felügyelt réteg (általában egy IP VPN, mint az MPLS) 3 szolgáltatások, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást Ön helyett.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel

Ezután egy virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoportot. Ha a Resource Manager-alapú üzemi modellel dolgozik, használja a [virtuális hálózatok összekapcsolása a ExpressRoute áramkörökkel](expressroute-howto-linkvnet-arm.md) című cikket.

## <a name="status"></a>ExpressRoute kör állapotának beolvasása

Megtekintheti a kapcsolatcsoport állapotát, jelölje ki, és az Áttekintés oldal megtekintése.

## <a name="modify"></a>ExpressRoute áramkör módosítása

Egyes ExpressRoute-kapcsolatcsoport tulajdonságainak kapcsolat befolyásolása nélkül módosíthatja. Módosíthatja a sávszélességet, az SKU-t, a számlázási modellt, és engedélyezheti a klasszikus műveleteket a **konfiguráció** lapon. A korlátozásokkal és korlátozásokkal kapcsolatos információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

Leállás nélkül a következő feladatokat hajthatja végre:

* Engedélyezi vagy letiltja az ExpressRoute Premium az ExpressRoute-kapcsolatcsoport bővítménye.
* Növelje az ExpressRoute-kapcsolatcsoport sávszélességét, feltéve, hogy kapacitás érhető el a porton.

  > [!IMPORTANT]
  > A sávszélesség csökkentése azonban a kapcsolat nem támogatott.

* Módosítsa a mérési tervet a *mért adatokról* a *korlátlan mennyiségű adatokra*.

  > [!IMPORTANT]
  > Mérési csomag módosítása a korlátlan, a forgalmi díjas adatok nem támogatott.

* Engedélyezheti és letilthatja a *klasszikus műveletek engedélyezését*.
  > [!IMPORTANT]
  > Az ExpressRoute-kapcsolatcsoport hozza létre újra, ha nincs elegendő kapacitás a meglévő porton is. A kapcsolatcsoport nem frissíthető, ha nincsenek további kapacitás érhető el az adott helyhez.
  >
  > Bár a zökkenőmentes frissítés a sávszélesség, megszakítás nélküli ExpressRoute-kapcsolatcsoport sávszélességét nem csökkenthető. Alacsonyabb verziójúra változtatása sávszélesség megköveteli, hogy az ExpressRoute-kapcsolatcsoport megszüntetése, és ezután építse ki újra a egy új ExpressRoute-kapcsolatcsoportot.
  >
  > A Premium bővítmény művelet letiltása meghiúsulhat, ha az erőforrást, amely nagyobb, mint a megengedett a standard szintű kapcsolatcsoportot használ.

Egy ExpressRoute-kör módosításához kattintson a **konfiguráció**elemre.

![Kapcsolatcsoport módosítása](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

## <a name="delete"></a>ExpressRoute áramkör kiépítése és törlése

A **Törlés** ikonra kattintva törölheti a ExpressRoute áramkörét. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze-e minden olyan virtuális hálózatok kapcsolódnak-e a kapcsolatcsoportot.
* Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Ha a szolgáltató kiépítte az áramkört (a szolgáltató kiépítési állapota **nincs kiépítve**), törölheti az áramkört. Ez leállítja a kapcsolatcsoport számlázását.

## <a name="next-steps"></a>Következő lépések

Miután létrehozta a kapcsolatcsoportot, folytassa a következő lépések:

* [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
