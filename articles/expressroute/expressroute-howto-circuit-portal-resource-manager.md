---
title: Oktatóanyag – Áramkör létrehozása és módosítása az ExpressRoute segítségével
description: Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre, létesíthet, ellenőrizhet, frissíthet, törölhet és megszüntethet egy ExpressRoute-áramkört.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/20/2018
ms.author: cherylmc
ms.openlocfilehash: 686ac8013879eff8adc4476d56119bbb4a169900
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74813141"
---
# <a name="tutorial-create-and-modify-an-expressroute-circuit"></a>Oktatóanyag: ExpressRoute-áramkör létrehozása és módosítása

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-circuit-portal-resource-manager.md)
> * [Powershell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Azure Resource Manager-sablon](expressroute-howto-circuit-resource-manager-template.md)
> * [Videó – Azure-portál](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk segít egy ExpressRoute-kapcsolatcsoport létrehozásában az Azure Portalon és az Azure Resource Manager telepítési modell használatával. Azt is ellenőrizheti az állapotát, frissítése, törlése vagy megszüntetése egy kapcsolatcsoport.

## <a name="before-you-begin"></a>Előkészületek

* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](expressroute-prerequisites.md) és [a munkafolyamatokat.](expressroute-workflows.md)
* Győződjön meg arról, hogy rendelkezik-e hozzáféréssel az [Azure Portalhoz.](https://portal.azure.com)
* Győződjön meg arról, hogy rendelkezik engedéllyel új hálózati erőforrások létrehozásához. Ha nem rendelkezik a megfelelő engedélyekkel, forduljon a fiók rendszergazdájához.
* A lépések jobb megértése érdekében a videót a kezdés előtt [megtekintheti.](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>ExpressRoute-kapcsolat létrehozása és kiépítése

### <a name="1-sign-in-to-the-azure-portal"></a>1. Bejelentkezés az Azure Portalra

Egy böngészőből lépjen az [Azure Portalra](https://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Új ExpressRoute-kapcsolat létrehozása

> [!IMPORTANT]
> Az ExpressRoute-kapcsolat számlázása a szolgáltatáskulcs kiadásának pillanatától kezdve történik. Győződjön meg arról, hogy ezt a műveletet akkor hajtja végre, amikor a kapcsolatszolgáltató készen áll a kapcsolat létesítésére.

ExpressRoute-kapcsolatcsoportot úgy hozhat létre, hogy új erőforrást hozhat létre. 

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet. Válassza **a Networking** > **ExpressRoute**lehetőséget az alábbi képen látható módon:

   ![ExpressRoute-kapcsolatcsoport létrehozása](./media/expressroute-howto-circuit-portal-resource-manager/create-an-expressroute-circuit.png)

2. Miután az **ExpressRoute**elemre kattintott, megjelenik az **ExpressRoute-kapcsolat létrehozása** lap. Az ezen az oldalon található értékek kitöltésekor győződjön meg arról, hogy megadja a megfelelő Termékváltozat-szintet (Standard vagy Prémium) és az adatmérési számlázási modellt (korlátlan vagy forgalmi díjas).

   ![A Termékváltozat-szint és az adatmérés konfigurálása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

   * **A szint** határozza meg, hogy egy ExpressRoute-szabvány vagy egy ExpressRoute-prémium szintű bővítmény engedélyezve van-e. Megadhatja **a Standard** a prémium bővítmény standard termékváltozatának vagy **prémium** ának lekérni.
   * **Az adatmérés** határozza meg a számlázás típusát. Megadhatja **forgalmi díjas** adatcsomag és **korlátlan** korlátlan adatcsomag. Ne feledje, hogy a számlázási típus forgalmi **díjasról** **korlátlanra**módosítható.

     > [!IMPORTANT]
     > A típus nem módosítható **Korlátlan** ról **Forgalmi díjasra.**

   * **A társviszony-létesítési hely** az a fizikai hely, ahol a Microsofttal társviszony-létesít.

     > [!IMPORTANT]
     > A társviszony-létesítési hely azt a [fizikai helyet](expressroute-locations.md) jelzi, ahol a Microsofttal társviszonyban áll. Ez **nem** kapcsolódik a "Location" tulajdonsághoz, amely az Azure hálózati erőforrás-szolgáltató földrajzi helyére hivatkozik. Bár nem kapcsolódnak egymáshoz, célszerű a hálózati erőforrás-szolgáltatót földrajzilag a kapcsolatlétesítési hely közelében választani.

### <a name="3-view-the-circuits-and-properties"></a>3. Tekintse meg az áramköröket és a tulajdonságokat

**Az összes áramkör megtekintése**

A bal oldali menü **Minden erőforrás** parancsára kattintva megtekintheti az összes létrehozott áramkört.

![Áramkörök megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**A tulajdonságok megtekintése**

Az áramkör tulajdonságait a beállításával tekintheti meg. A **kapcsolatcsoport Áttekintés lapján** a szolgáltatáskulcs megjelenik a szolgáltatáskulcs mezőben. A kiépítési folyamat befejezéséhez másolja a szolgáltatáskulcsot a kapcsolatcsoporthoz, és adja át azt a szolgáltatónak. Az áramköri szolgáltatás kulcsa az adott áramkörre jellemző.

![Tulajdonságok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Küldje el a szolgáltatáskulcsot a kapcsolódási szolgáltatónak a kiépítéshez

Ezen a lapon **a Szolgáltató állapota** a szolgáltatói oldalon a kiépítés aktuális állapotáról nyújt tájékoztatást. **A kapcsolati kapcsolat állapota** biztosítja a Microsoft oldalán lévő állapotot. A kapcsolatlétesítési állapotokról a Munkafolyamatok című cikkben olvashat [bővebben.](expressroute-workflows.md#expressroute-circuit-provisioning-states)

Új ExpressRoute-kapcsolat létrehozásakor az áramkör a következő állapotban van:

Szolgáltató állapota: Nincs kiépítve<BR>
Áramkör állapota: Engedélyezve

![Kiépítési folyamat kezdeményezése](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

Az áramkör a következő állapotra változik, amikor a kapcsolatszolgáltató az Ön számára engedélyezi azt:

Szolgáltató állapota: Kiépítés<BR>
Áramkör állapota: Engedélyezve

Ahhoz, hogy egy ExpressRoute-áramkört használhasson, a következő állapotban kell lennie:

Szolgáltató állapota: Kiépítve<BR>
Áramkör állapota: Engedélyezve

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Rendszeresen ellenőrizze az áramköri kulcs állapotát és állapotát

Az önt érdeklő áramkör tulajdonságait a kijelölésével tekintheti meg. Ellenőrizze a **szolgáltató állapotát,** és győződjön meg arról, hogy a folytatás előtt átkerült **a Kiépített** helyre.

![Áramkör és szolgáltató állapota](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Az útválasztási konfiguráció létrehozása

Lépésenkénti útmutatást az [ExpressRoute-kapcsolati útválasztó konfigurációs](expressroute-howto-routing-portal-resource-manager.md) cikkben talál a kapcsolatlétesítések létrehozásához és módosításához.

> [!IMPORTANT]
> Ezek az utasítások csak a 2. Ha olyan szolgáltatót használ, amely 3.

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Virtuális hálózat összekapcsolása ExpressRoute-áramkörrel

Ezután kapcsolja össze a virtuális hálózatot az ExpressRoute-kapcsolattal. Használja a [virtuális hálózatok összekapcsolása ExpressRoute-áramkörök](expressroute-howto-linkvnet-arm.md) cikket, amikor az Erőforrás-kezelő telepítési modell.

## <a name="getting-the-status-of-an-expressroute-circuit"></a><a name="status"></a>ExpressRoute-kapcsolat állapotának beszerzése

Az áramkör állapotát úgy tekintheti meg, hogy kiválasztja, és megtekinti az Áttekintő lapot.

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>ExpressRoute-kapcsolat módosítása

Az ExpressRoute-áramkör bizonyos tulajdonságait a kapcsolat befolyásolása nélkül módosíthatja. Módosíthatja a sávszélességet, a termékváltozatot, a számlázási modellt, és engedélyezheti a klasszikus műveleteket a **Konfiguráció lapon.** A korlátozásokról és korlátozásokról az [ExpressRoute gyakori kérdések](expressroute-faqs.md)című témakörben talál további információt.

A következő feladatokat teljesítménynélkül hajthatja végre:

* ExpressRoute Premium-bővítmény engedélyezése vagy letiltása az ExpressRoute-kapcsolaton.
* Növelje az ExpressRoute-kapcsolat sávszélességét, feltéve, hogy a porton rendelkezésre áll kapacitás.

  > [!IMPORTANT]
  > Az áramkör sávszélességének visszaminősítése nem támogatott.

* Módosítsa a mérési tervet *forgalmi díjas adatokról* *korlátlan adatra.*

  > [!IMPORTANT]
  > A mérési terv módosítása korlátlan adatról forgalmi díjas adatokra nem támogatott.

* Engedélyezheti és letilthatja *a Klasszikus műveletek engedélyezése .*
  > [!IMPORTANT]
  > Előfordulhat, hogy újra létre kell hoznia az ExpressRoute-áramkört, ha a meglévő porton nincs megfelelő kapacitás. Nem frissítheti a kapcsolatcsoport, ha nincs további kapacitás áll rendelkezésre az adott helyen.
  >
  > Bár zökkenőmentesen frissítheti a sávszélességet, nem csökkentheti az ExpressRoute-áramkör sávszélességét megszakítás nélkül. A sávszélesség visszaminősítéséhez az ExpressRoute-kapcsolat bontásához, majd egy új ExpressRoute-kapcsolat újbóli kiépítéséhez szükséges.
  >
  > A prémium szintű bővítmény művelet letiltása sikertelen lehet, ha olyan erőforrásokat használ, amelyek nagyobbak, mint a normál kapcsolatcsoport engedélyezett.

ExpressRoute-kapcsolat módosításához kattintson a **Konfiguráció**gombra.

![Áramkör módosítása](./media/expressroute-howto-circuit-portal-resource-manager/modify-circuit-configuration.png)

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>ExpressRoute-kapcsolatcsoport megszüntetése és törlése

Az ExpressRoute-kapcsolat a **törlés** ikon kiválasztásával törölheti az ExpressRoute-áramkört. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze, hogy vannak-e virtuális hálózatok a kapcsolatcsoporthoz.
* Ha az ExpressRoute-kapcsolatszolgáltató létesítési állapota **Kiépítés** vagy **Kiépítve,** együtt kell működnie a szolgáltatóval az adott kapcsolat i. Továbbra is fenntartjuk az erőforrásokat, és kiszámlázunk Önnek, amíg a szolgáltató be nem fejezi a kapcsolatcsoport kiürítését, és nem értesíti kedélyünket.
* Ha a szolgáltató megszüntette a körcsoportot (a szolgáltató létesítési állapota **Nincs kiépítve)** értékre van állítva, törölheti a csoportot. Ez leállítja a kapcsolatcsoport számlázását.

## <a name="next-steps"></a>További lépések

A kapcsolatcsoport létrehozása után folytassa a következő lépésekkel:

* [Útválasztás létrehozása és módosítása az ExpressRoute-kapcsolathoz](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-áramkörrel](expressroute-howto-linkvnet-arm.md)
