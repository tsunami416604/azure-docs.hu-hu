---
title: 'Létrehozása és módosítása egy ExpressRoute-kapcsolatcsoport - portálon: Azure | Microsoft Docs'
description: Hozzon létre, üzembe helyezése, győződjön meg arról, frissítése, törlése és ExpressRoute-kapcsolatcsoport megszüntetése.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 10/20/2018
ms.author: cherylmc;ganesr
ms.custom: seodec18
ms.openlocfilehash: 3abdeff3c3f1a4069130ed7c8d49d485feea4093
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55894717"
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Létrehozása és módosítása egy ExpressRoute-kapcsolatcsoporttal
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [Azure CLI](howto-circuit-cli.md)
> * [Videó – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-circuit-classic.md)
>

Ez a cikk segít az Azure portal és az Azure Resource Manager üzemi modell használatával egy ExpressRoute-kapcsolatcsoportot létrehozni. Azt is ellenőrizze az állapotát, frissítése, törlése, vagy egy kapcsolatcsoport megszüntetése.


## <a name="before-you-begin"></a>Előkészületek
* Tekintse át a [Előfeltételek](expressroute-prerequisites.md) és [munkafolyamatok](expressroute-workflows.md) konfigurálás megkezdése előtt.
* Gondoskodjon arról, hogy van-e a hozzáférést a [az Azure portal](https://portal.azure.com).
* Győződjön meg arról, hogy új hálózati erőforrások létrehozásához szükséges engedélyek. Ha nem rendelkezik a megfelelő engedélyekkel, lépjen kapcsolatba a fiókadminisztrátorral.
* Is [videó](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) megtudhatja, a lépések megkezdése előtt.

## <a name="create"></a>Létrehozása és kiépítése az ExpressRoute-kapcsolatcsoport
### <a name="1-sign-in-to-the-azure-portal"></a>1. Jelentkezzen be az Azure Portalra
Egy böngészőből lépjen az [Azure Portalra](http://portal.azure.com), majd jelentkezzen be az Azure-fiókjával.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Egy új ExpressRoute-kapcsolatcsoport létrehozása
> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport számlázása a szolgáltatáskulcs pillanatától kezdve. Győződjön meg arról, hogy ha a kapcsolatszolgáltató üzembe helyezi a kapcsolatcsoportot készen áll a művelet végrehajtása.
> 
> 

1. Egy ExpressRoute-kapcsolatcsoportot is létrehozhat egy új erőforrás létrehozásának lehetősége kiválasztásával. Kattintson a **erőforrás létrehozása** > **hálózatkezelés** > **ExpressRoute**, ahogy az alábbi képen látható:

  ![ExpressRoute-kapcsolatcsoport létrehozása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Miután rákattintott **ExpressRoute**, látni fogja a **létrehozása az ExpressRoute-kapcsolatcsoport** lap. Ha Ön az értékeket, ezen a lapon megadott információkat, győződjön meg arról, adja meg a megfelelő Termékváltozat-szint (Standard vagy prémium) és a számlázási modellt (korlátlan és forgalmi díjas) mérési adatokat.

  ![A Termékváltozat-szint és az adatforgalom-mérést konfigurálása](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit.png)

  * **Réteg** határozza meg, hogy egy ExpressRoute-standard vagy az ExpressRoute prémium bővítmény engedélyezve van. Megadhat **Standard** beolvasni a standard Termékváltozat vagy **prémium** for a premium bővítményt.
  * **Az adatforgalom-mérést** határozza meg a számlázási típusát. Megadhat **forgalmi díjas** forgalmi díjas csomag, és **korlátlan** a korlátlan adatforgalmú. Vegye figyelembe, hogy a számlázási típust módosíthatja **forgalmi díjas** való **korlátlan**.
    > [!IMPORTANT] Nem módosíthatja a típust **korlátlan** való **forgalmi díjas**.
  * **Társviszony-létesítési helyszínen** a fizikai hely, ahol van Microsoft-társviszony van.

    > [!IMPORTANT]
    > Azt jelzi, hogy a társviszony-létesítési helye a [fizikai helyének](expressroute-locations.md) hol vannak a Microsoft társviszony. Ez a **nem** "Hely" tulajdonság, amely hivatkozik a földrajzi hely, ahol az Azure hálózati erőforrás-szolgáltató csatolva. Amíg nem áll(nak), tanácsos válassza ki a hálózati erőforrás-szolgáltató földrajzilag közeli, a kapcsolatcsoport társviszony-létesítési helye.
    >
    >

### <a name="3-view-the-circuits-and-properties"></a>3. A Kapcsolatcsoportok és a tulajdonságok megtekintéséhez
**Minden kapcsolatcsoportra megtekintése**

Megtekintheti a Kapcsolatcsoportok kiválasztásával létrehozott **összes erőforrás** a bal oldali menüben.

![Kapcsolatok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**A tulajdonságok megtekintéséhez**

A kapcsolatcsoport tulajdonságainak megtekintéséhez jelölje ki. Az a **áttekintése** a kapcsolatcsoport lapon, a kulcs szolgáltatás key mezőben jelennek meg. Másolja a kulcsot a kapcsolatcsoport kell, és adja át azt le a service provider, a kiépítési folyamat befejezéséhez. Kör szolgáltatáskulcsának csak a kapcsolatcsoporthoz.

![Tulajdonságok megtekintése](./media/expressroute-howto-circuit-portal-resource-manager/servicekey1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. A kulcs küldése a kapcsolatszolgáltató a kiépítés
Ezen a lapon **szolgáltató állapota** információt nyújt a szolgáltatói oldalon kiépítés aktuális állapotát. **Kapcsolatcsoport állapota** a állapotot biztosít a Microsoft oldalán. A kapcsolatcsoport kiépítési állapotok kapcsolatos további információkért lásd: a [munkafolyamatok](expressroute-workflows.md#expressroute-circuit-provisioning-states) cikk.

Amikor létrehoz egy új ExpressRoute-kapcsolatcsoportot, a kapcsolatcsoport a következő állapotban van:

Szolgáltató állapota: Nincs kiépítve<BR>
Kapcsolatcsoport állapota: Engedélyezve

![Üzembe helyezési folyamatának elindításához](./media/expressroute-howto-circuit-portal-resource-manager/status.png)

A kapcsolatcsoport változik a következő állapotot, amikor a kapcsolatszolgáltató van folyamatban, amely lehetővé teszi az Ön számára:

Szolgáltató állapota: Kiépítés<BR>
Kapcsolatcsoport állapota: Engedélyezve

ExpressRoute-kapcsolatcsoport segítségével tudja meg a következő állapotban kell lennie:

Szolgáltató állapota: Telepítve<BR>
Kapcsolatcsoport állapota: Engedélyezve

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Rendszeresen ellenőrizze a kapcsolatcsoport kulcs állapotát és az állapot
Megtekintheti, hogy az Önt érdeklő, ha kiválasztja a kapcsolatcsoport tulajdonságainak. Ellenőrizze a **szolgáltató állapota** , és győződjön meg arról, hogy átkerült az **kiépített** a folytatás előtt.

![Kapcsolatcsoport és a szolgáltató állapota](./media/expressroute-howto-circuit-portal-resource-manager/provisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Az útválasztási konfiguráció létrehozása
Részletes útmutatásért tekintse meg a [ExpressRoute-Kapcsolatcsoportok útválasztási konfigurációja](expressroute-howto-routing-portal-resource-manager.md) cikk létrehozásához és módosításához a kapcsolatcsoport társviszony-létesítéseket.

> [!IMPORTANT]
> Ezek az utasítások csak 2 réteg szolgáltatás kínáló szolgáltatóknál létrehozott Kapcsolatcsoportok vonatkoznak. Ha használja a szolgáltató által kínált felügyelt réteg (általában egy IP VPN, mint az MPLS) 3 szolgáltatások, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást Ön helyett.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal
Ezután egy virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoportot. Használja a [virtuális hálózatok összekapcsolása az ExpressRoute-Kapcsolatcsoportok](expressroute-howto-linkvnet-arm.md) című cikket, amikor a Resource Manager üzemi modellel dolgozik.

## <a name="status"></a>Az ExpressRoute-Kapcsolatcsoportok állapotának beolvasása
Megtekintheti a kapcsolatcsoport állapotát, jelölje ki, és az Áttekintés oldal megtekintése. 

## <a name="modify"></a>Az ExpressRoute-Kapcsolatcsoportok módosítása
Egyes ExpressRoute-kapcsolatcsoport tulajdonságainak kapcsolat befolyásolása nélkül módosíthatja. A sávszélesség, Termékváltozat, számlázási modellt módosíthatja, és a klasszikus működés engedélyezése a **konfigurációs** lapot. A korlátok és korlátozások további információkért lásd: a [ExpressRoute – gyakori kérdések](expressroute-faqs.md). 

Leállás nélkül a következő feladatokat hajthatja végre:

* Engedélyezi vagy letiltja az ExpressRoute Premium az ExpressRoute-kapcsolatcsoport bővítménye.
* Növelje az ExpressRoute-kapcsolatcsoport sávszélességét, feltéve, hogy kapacitás érhető el a porton.
  > [!IMPORTANT] A sávszélesség csökkentése azonban a kapcsolat nem támogatott. 
* Módosítsa a mérési a csomagot az *díjas* való *korlátlan*.
  > [!IMPORTANT] Mérési csomag módosítása a korlátlan, a forgalmi díjas adatok nem támogatott.
* Engedélyezheti és letilthatja az *klasszikus működés engedélyezése*.
> [!IMPORTANT]
> Az ExpressRoute-kapcsolatcsoport hozza létre újra, ha nincs elegendő kapacitás a meglévő porton is. A kapcsolatcsoport nem frissíthető, ha nincsenek további kapacitás érhető el az adott helyhez.
>
> Bár a zökkenőmentes frissítés a sávszélesség, megszakítás nélküli ExpressRoute-kapcsolatcsoport sávszélességét nem csökkenthető. Alacsonyabb verziójúra változtatása sávszélesség megköveteli, hogy az ExpressRoute-kapcsolatcsoport megszüntetése, és ezután építse ki újra a egy új ExpressRoute-kapcsolatcsoportot.
> 
> A Premium bővítmény művelet letiltása meghiúsulhat, ha az erőforrást, amely nagyobb, mint a megengedett a standard szintű kapcsolatcsoportot használ.
> 
> 

ExpressRoute-kapcsolatcsoport módosításához kattintson **konfigurációs**.

![Kapcsolatcsoport módosítása](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)




## <a name="delete"></a>A megszüntetés és a egy ExpressRoute-kapcsolatcsoport törlése
Az ExpressRoute-kapcsolatcsoport kiválasztásával törölheti a **törlése** ikonra. Tekintse meg az alábbi információkat:

* Az összes virtuális hálózatot le kell választania az ExpressRoute-kapcsolatcsoportról. Ha ez a művelet sikertelen, ellenőrizze-e minden olyan virtuális hálózatok kapcsolódnak-e a kapcsolatcsoportot.
* Ha az ExpressRoute kapcsolatcsoport szolgáltató üzembe helyezési állapota **kiépítési** vagy **kiépített** -e, hogy azok oldalán a kapcsolatcsoport megszüntetése a szolgáltató. Továbbra is erőforrásokat tartalékolnia, és addig, amíg a szolgáltató befejeződött, a kapcsolatcsoport megszüntetése, és értesítést küld nekünk fel díjat.
* Ha a szolgáltató eltávolította a kapcsolatcsoportot (a kiépítési állapota szolgáltató beállítása **nincs kiépítve**), törölheti a kapcsolatcsoportot. Ez leállítja a kapcsolatcsoport számlázását.

## <a name="next-steps"></a>További lépések
Miután létrehozta a kapcsolatcsoportot, folytassa a következő lépések:

* [Útválasztás az ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása](expressroute-howto-routing-portal-resource-manager.md)
* [A virtuális hálózat összekapcsolása az ExpressRoute-kapcsolatcsoport](expressroute-howto-linkvnet-arm.md)
