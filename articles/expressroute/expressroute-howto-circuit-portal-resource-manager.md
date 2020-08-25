---
title: Oktatóanyag – áramkör létrehozása és módosítása a ExpressRoute
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre, helyezhet üzembe, ellenőrizheti, frissítheti, törölheti és kiépítheti az ExpressRoute-áramkört.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "74813141"
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

Ebből a cikkből megtudhatja, hogyan hozhat létre ExpressRoute-áramkört a Azure Portal és az Azure Resource Manager üzemi modell használatával. Az áramkör állapotát, frissítését, törlését vagy megszüntetését is megtekintheti.

## <a name="before-you-begin"></a>Előkészületek

* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és a [munkafolyamatokat](expressroute-workflows.md) .
* Győződjön meg arról, hogy rendelkezik hozzáféréssel a [Azure Portalhoz](https://portal.azure.com).
* Győződjön meg arról, hogy rendelkezik az új hálózati erőforrások létrehozásához szükséges engedélyekkel. Ha nem rendelkezik a megfelelő engedélyekkel, forduljon a fiók rendszergazdájához.
* A lépések megkezdése előtt [megtekintheti a videót](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) .

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kör létrehozása és kiépítése

### <a name="1-sign-in-to-the-azure-portal"></a>1. Jelentkezzen be a Azure Portalba

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="2-create-a-new-expressroute-circuit"></a>2. hozzon létre egy új ExpressRoute áramkört

> [!IMPORTANT]
> A ExpressRoute-áramkör számlázása a szolgáltatási kulcs kiállításának pillanatától számítva történik. Győződjön meg arról, hogy ezt a műveletet akkor hajtja végre, ha a kapcsolati szolgáltató készen áll az áramkör kiépítésére.

ExpressRoute-áramkör létrehozásához válassza az új erőforrás létrehozása lehetőséget. 

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Válassza a **hálózatkezelés**  >  **ExpressRoute**lehetőséget az alábbi ábrán látható módon:

   ![ExpressRoute-kapcsolatcsoport létrehozása](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Miután rákattintott a **ExpressRoute**elemre, megjelenik a **ExpressRoute-kör létrehozása** lap. Ha kitölti az ezen a lapon található értékeket, győződjön meg arról, hogy a megfelelő SKU-szintet (standard vagy prémium) és az adatmérési számlázási modellt (korlátlan vagy mért) használja.

   ![Az SKU-rétegek és az adatmérés konfigurálása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * A **szint** meghatározza, hogy engedélyezve van-e a ExpressRoute standard vagy a ExpressRoute Premium bővítmény. A prémium szintű bővítmény szabványos SKU-jának vagy **premiumjának** beszerzéséhez megadható a **standard** .
   * Az **adatmérés** meghatározza a számlázási típust. Megadhatja **a mért adatcsomag díjait** , és **korlátlan** számú adatcsomagot. Vegye figyelembe, hogy a számlázási típus a **mért** értékről **korlátlanra**módosítható.

     > [!IMPORTANT]
     > A típus nem módosítható **korlátlanról** **mértre**.

   * A társítás **helye** az a fizikai hely, ahol a Microsofttal való együttműködésre van szükség.

     > [!IMPORTANT]
     > A társítási hely azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol a Microsofttal való együttműködésre van szükség. Ez **nem** a "location" tulajdonsághoz van csatolva, amely arra a földrajzi helyre hivatkozik, ahol az Azure hálózati erőforrás-szolgáltató található. Noha nem kapcsolódnak egymáshoz, célszerű kiválasztania a hálózati erőforrás-szolgáltatót földrajzilag közel az áramkör egyenrangú helyéhez.

### <a name="3-view-the-circuits-and-properties"></a>3. a áramkörök és a tulajdonságok megtekintése

**Az összes áramkör megtekintése**

A bal oldali menüben az **összes erőforrás** lehetőség kiválasztásával megtekintheti az összes létrehozott áramkört.

![Áramkörök megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**A tulajdonságok megtekintése**

Az áramkör tulajdonságait a kiválasztásával tekintheti meg. Az áramkör **Áttekintés** lapján a szolgáltatási kulcs megjelenik a szolgáltatás kulcsa mezőben. A létesítési folyamat befejezéséhez át kell másolnia az áramkörhöz tartozó szolgáltatási kulcsot, és továbbítania kell azt a szolgáltatónak. Az áramköri szolgáltatás kulcsa az áramkörre jellemző.

![Tulajdonságok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. küldje el a szolgáltatási kulcsot a kapcsolat szolgáltatójának az üzembe helyezéshez

Ezen az oldalon a **szolgáltatói állapot** információkkal szolgál a kiépítés aktuális állapotáról a szolgáltatói oldalon. Az **áramkör állapota** a Microsoft oldalán található állapotot biztosítja. Az áramkör kiépítési állapotával kapcsolatos további információkért tekintse meg a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) című cikket.

Új ExpressRoute-kör létrehozásakor az áramkör a következő állapotban van:

Szolgáltató állapota: nincs kiépítve<BR>
Áramkör állapota: engedélyezve

![Kiépítési folyamat kezdeményezése](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Az áramkör a következő állapotra vált, amikor a kapcsolati szolgáltató a következő állapotban van:

Szolgáltatói állapot: kiépítés<BR>
Áramkör állapota: engedélyezve

Ahhoz, hogy használni tudja a ExpressRoute áramkört, a következő állapotban kell lennie:

Szolgáltató állapota: kiépítve<BR>
Áramkör állapota: engedélyezve

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. az áramköri kulcs állapotának és állapotának rendszeres időközönkénti keresése

A kiválasztásával megtekintheti annak az áramkörnek a tulajdonságait, amelyre kíváncsi. Ellenőrizze a **szolgáltató állapotát** , és győződjön meg arról, hogy a folytatás előtt átkerült a **kiépített** állapotba.

![Áramkör és szolgáltató állapota](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. az útválasztási konfiguráció létrehozása

Részletes útmutatásért tekintse meg az [ExpressRoute-áramkör útválasztási konfigurációját](expressroute-howto-routing-portal-resource-manager.md) ismertető cikket az áramköri társítások létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. rétegbeli kapcsolati szolgáltatásokat nyújtó szolgáltatók által létrehozott áramkörökre vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat (jellemzően IP VPN, például MPLS) kínál, a kapcsolati szolgáltatója konfigurálja és kezeli az útválasztást.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. virtuális hálózat összekapcsolása egy ExpressRoute-áramkörrel

Ezután csatoljon egy virtuális hálózatot a ExpressRoute-áramkörhöz. Ha a Resource Manager-alapú üzemi modellel dolgozik, használja a [virtuális hálózatok összekapcsolása a ExpressRoute áramkörökkel](expressroute-howto-linkvnet-arm.md) című cikket.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>ExpressRoute kör állapotának beolvasása

Az áramkör állapotát úgy tekintheti meg, ha kiválasztja, és megtekinti az Áttekintés lapot.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute áramkör módosítása

Egy ExpressRoute-kör bizonyos tulajdonságait módosíthatja a kapcsolat befolyásolása nélkül. Módosíthatja a sávszélességet, az SKU-t, a számlázási modellt, és engedélyezheti a klasszikus műveleteket a **konfiguráció** lapon. A korlátozásokkal és korlátozásokkal kapcsolatos információkért tekintse meg a [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakört.

A következő feladatok elvégzése leállás nélkül is elvégezhető:

* Engedélyezheti vagy letilthatja a ExpressRoute-áramkörhöz tartozó ExpressRoute Premium-bővítményt.
* Növelje a ExpressRoute-áramkör sávszélességét, ha rendelkezésre áll kapacitás a porton.

  > [!IMPORTANT]
  > Az áramkör sávszélességének visszaminősítése nem támogatott.

* Módosítsa a mérési tervet a *mért adatokról* a *korlátlan mennyiségű adatokra*.

  > [!IMPORTANT]
  > A mérési terv korlátlan mennyiségű és mért adatok közötti módosítása nem támogatott.

* Engedélyezheti és letilthatja a *klasszikus műveletek engedélyezését*.
  > [!IMPORTANT]
  > Előfordulhat, hogy újra létre kell hoznia a ExpressRoute áramkört, ha nincs elegendő kapacitás a meglévő porton. Az áramkör nem frissíthető, ha az adott helyen nem érhető el további kapacitás.
  >
  > Bár zökkenőmentesen frissítheti a sávszélességet, a ExpressRoute áramkör sávszélességét nem lehet megzavarás nélkül csökkenteni. A lefokozási sávszélességhez meg kell szüntetnie a ExpressRoute áramkört, majd újra kell telepítenie egy új ExpressRoute áramkört.
  >
  > A prémium szintű bővítmény műveletének letiltása sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak a standard áramkör számára engedélyezettnél.

Egy ExpressRoute-kör módosításához kattintson a **konfiguráció**elemre.

![Áramkör módosítása](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése

A **Törlés** ikonra kattintva törölheti a ExpressRoute áramkörét. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha a művelet meghiúsul, ellenőrizze, hogy a virtuális hálózatok az áramkörhöz vannak-e kapcsolva.
* Ha a ExpressRoute Circuit Service Provider kiépítési állapota kiépítés vagy **kiépítve** **van,** akkor a szolgáltatóval kell együttműködni, hogy kiépítse az áramkört a saját oldalán. Továbbra is fenntartjuk az erőforrásokat és számlázunk, amíg a szolgáltató befejezi az áramkör kiépítését, és értesítést küld nekünk.
* Ha a szolgáltató kiépítte az áramkört (a szolgáltató kiépítési állapota **nincs kiépítve**), törölheti az áramkört. Ez leállítja a kapcsolatcsoport számlázását.

## <a name="next-steps"></a>Következő lépések

Az áramkör létrehozása után folytassa a következő lépésekkel:

* [Az ExpressRoute-áramkör útválasztásának létrehozása és módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása a ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
