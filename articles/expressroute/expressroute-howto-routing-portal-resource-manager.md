---
title: 'Azure-ExpressRoute: társítás konfigurálása'
description: Ez a cikk a privát és a Microsoft-ExpressRoute létrehozásának és kiépítési lépéseinek a lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizhető az áramkör állapota, frissítése vagy törlése.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/13/2019
ms.author: duau
ms.openlocfilehash: 8ac38c25821a9f636ac98903599ee7d3acfa5e17
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395723"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet útválasztási konfigurációt egy Azure Resource Manager (ARM) ExpressRoute-áramkörhöz a Azure Portal használatával. A ExpressRoute-áramkör állapota, frissítése vagy törlése és megszüntetése is ellenőrizhető. Ha más módszert szeretne használni az áramkörrel való munkavégzéshez, válasszon egy cikket a következő listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társak](about-public-peering.md)
> * [Videó – privát peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

A ExpressRoute-áramkörhöz privát és Microsoft-társítást is beállíthat (az Azure nyilvános társítása elavult az új áramkörök esetében). A társításokat tetszőleges sorrendben lehet konfigurálni. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományokról és a társításokról: [ExpressRoute-útválasztási tartományok](expressroute-circuit-peerings.md). A nyilvános hálózattal kapcsolatos további információkért lásd: [nyilvános ExpressRoute](about-public-peering.md).

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A társítás (ok) konfigurálásához a ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie. 
* Ha megosztott kulcsot vagy MD5-kivonatot szeretne használni, ügyeljen arra, hogy ezt az alagút mindkét oldalán használja, és legfeljebb 25 alfanumerikus karakterből álljon. A speciális karakterek nem támogatottak. 

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt 3. rétegbeli szolgáltatásokat kínál (jellemzően IPVPN, például MPLS), a kapcsolati szolgáltató konfigurálja és kezeli az útválasztást. 

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP-társítások konfigurálása előtt érdeklődjön a szolgáltatónál.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoftos társviszony

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Microsoft-társi konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

> [!IMPORTANT]
> Az 2017. augusztus 1. előtt konfigurált ExpressRoute-áramkörök Microsoft-összevonása a Microsoft-társon keresztül meghirdetett összes szolgáltatás-előtagokkal rendelkezik, még akkor is, ha az útvonal-szűrők nincsenek meghatározva. A 2017 augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társítása nem rendelkezik olyan előtagokkal, amelyek csak akkor lesznek meghirdetve, ha az áramkörhöz hozzá van rendelve egy útvonal-szűrő. További információ: [útválasztási szűrő beállítása a Microsoft-társak](how-to-routefilter-powershell.md)számára.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. Ellenőrizze a **szolgáltató állapotát** , és győződjön meg arról, hogy a kapcsolat szolgáltatója teljes mértékben kiépíti az áramkört a folytatás előtt.

   Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolat szolgáltatóját, hogy engedélyezze a Microsoft-partneri kapcsolatot. Ebben az esetben nem kell követnie a következő szakaszokban ismertetett utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa ezeket a lépéseket.

   **Áramköri szolgáltató állapota: nincs kiépítve**

   [![Képernyőfelvétel: a ExpressRoute bemutató lapja, amelynek a szolgáltatói állapotát a "nincs kiépítve" értékre kiemelő piros mező mutatja.](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Áramkör-szolgáltató állapota: kiépítve**

   [![Képernyőfelvétel: a ExpressRoute bemutató lapja, amely a szolgáltatói állapotot jelöli, amely a "kiépítve" értékre van állítva.](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet hozzá kell rendelni az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet hozzá kell rendelni az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos kapcsolatok esetében ugyanazt a VLAN-azonosítót kell használnia.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagokat szeretne elküldeni, vesszővel tagolt listát is küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * Nem **kötelező –** Ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem szerepelnek a társítás AS számában, megadhatja az AS-számot, amelyre regisztrálva vannak.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.
3. Kiválaszthatja a konfigurálni kívánt társítást, ahogy az az alábbi példában is látható. Válassza ki a Microsoft társviszony-létesítés sorát.

   [![Válassza ki a Microsoft egyenrangú sorát](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Válassza ki a Microsoft egyenrangú sorát")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurálja a Microsoft társviszony-beállítást. **Mentse** a konfigurációt, miután megadta az összes paramétert. Az alábbi képen egy példa konfiguráció látható:

   ![A Microsoft-társviszony konfigurálása](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés meghiúsul, az "érvényesítés szükséges" üzenet jelenik meg. 
>
> Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosának listáján, majd a dokumentumok manuális ellenőrzéshez való beküldéséhez az alább látható módon nyisson meg egy támogatási jegyet. 
>

   Ha az áramkör "érvényesítés szükséges" állapotba kerül, meg kell nyitnia egy támogatási jegyet, hogy igazolja az előtagok tulajdonjogát a támogatási csapatnak. Egy támogatási jegyet közvetlenül a portálról nyithat meg, ahogy az alábbi példában is látható:

   ![Ellenőrzés szükséges – támogatási jegy](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. A konfiguráció sikeres elfogadását követően az alábbi képhez hasonlóan fog megjelenni:

   ![Egyenrangú állapot: konfigurálva](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Egyenrangú állapot: konfigurálva")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A Microsoft-társak tulajdonságainak megtekintéséhez válassza ki a társítás sorát.

[![Microsoft-partneri tulajdonságok megtekintése](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Tulajdonságok megtekintése")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

Kiválaszthatja a módosítani kívánt társítás sorát, majd módosíthatja a társítási tulajdonságokat, és mentheti a módosításokat.

![Egyenrangú sor kijelölése](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A társítási konfigurációt a Delete (Törlés) ikonra kattintva távolíthatja el, ahogy az alábbi képen is látható:

![Társítás törlése](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Azure-beli privát társviszony

Ez a szakasz segítséget nyújt egy ExpressRoute-áramkör Azure-beli privát társ-összevonási konfigurációjának létrehozásához, lekéréséhez, frissítéséhez és törléséhez.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. 

   Ha a kapcsolat szolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a kapcsolati szolgáltatót, hogy engedélyezze az Azure-beli privát kapcsolatot. Ebben az esetben nem kell követnie a következő szakaszokban ismertetett utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a következő lépésekkel.

   **Áramköri szolgáltató állapota: nincs kiépítve**

   [![Képernyőfelvétel: a ExpressRoute bemutató lapja, amelynek a szolgáltatói állapotát a "nincs kiépítve" értékre kiemelő piros mező mutatja.](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Áramkör-szolgáltató állapota: kiépítve**

   [![Képernyőfelvétel: a ExpressRoute bemutató lapja, amely a szolgáltatói állapotot jelöli, amely a "kiépítve" értékre van állítva.](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem lehet a virtuális hálózatok számára fenntartott címterület része. Ebből az alhálózatból az első használható IP-címet hozzá kell rendelni az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem lehet a virtuális hálózatok számára fenntartott címterület része. Ebből az alhálózatból az első használható IP-címet hozzá kell rendelni az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos kapcsolatok esetében ugyanazt a VLAN-azonosítót kell használnia.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társhoz az 65515 és 65520 közötti számot is használhatja, amely magában foglalja a többit.
   * Az útvonalakat a helyszíni peremhálózati útválasztóról az Azure-ba kell hirdetni a BGP-n keresztül, amikor beállítja a privát társítást.
   * Nem **kötelező –** Egy MD5-kivonat, ha úgy dönt, hogy használ egyet.
3. Válassza ki az Azure privát társ-összevonási sorát az alábbi példában látható módon:

   [![Válassza ki a privát társas sort](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Válassza ki a privát társas sort")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurálja a privát társviszony-létesítést. **Mentse** a konfigurációt, miután megadta az összes paramétert.

   ![privát társak konfigurálása](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. A konfiguráció sikeres elfogadását követően az alábbi példához hasonló módon jelenik meg:

   ![privát társak mentve](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure privát társviszony-létesítés részleteinek megtekintése

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

[![Privát egyenrangú tulajdonságok megtekintése](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Privát egyenrangú tulajdonságok megtekintése")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. A frissítés után mentse a módosításokat.

![privát társak frissítése](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A társítási konfigurációt a Delete (Törlés) ikon kiválasztásával távolíthatja el, ahogy az alábbi képen is látható:

> [!WARNING]
> A példa futtatása előtt győződjön meg arról, hogy a virtuális hálózatok és a ExpressRoute összes Global Reach kapcsolata el lesz távolítva. 
> 
> 

![privát társak törlése](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>Következő lépések

Következő lépés, [VNet csatolása egy ExpressRoute-áramkörhöz](expressroute-howto-linkvnet-portal-resource-manager.md)
* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
