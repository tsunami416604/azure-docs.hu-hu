---
title: 'Azure-ExpressRoute: társítás konfigurálása'
description: Ez a cikk a privát és a Microsoft-ExpressRoute létrehozásának és kiépítési lépéseinek a lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizhető az áramkör állapota, frissítése vagy törlése.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.openlocfilehash: 5fb728cccd77d0cefd10c124cb7215dc3b880fe3
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083526"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport társviszony létesítése és módosítása

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet útválasztási konfigurációt egy Azure Resource Manager (ARM) ExpressRoute-áramkörhöz a Azure Portal használatával. Ellenőrizze az állapotot, frissítési vagy törlési is, és ExpressRoute-kapcsolatcsoport társviszonyainak megszüntetése. Ha szeretne egy másik módszer használható a kapcsolatcsoport-, válassza ki a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó - privát társviszony](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó - nyilvános társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Az Azure Private-és a Microsoft-társítást egy ExpressRoute-áramkörhöz is konfigurálhatja (az Azure nyilvános társítása elavult az új áramkörök esetében). A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományokról és a társításokról: [Tudnivalók az áramkörekről és a társairól](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A társítás (ok) konfigurálásához a ExpressRoute áramkörnek kiépített és engedélyezett állapotban kell lennie. 
* Ha megosztott kulcsot vagy MD5-kivonatot szeretne használni, ügyeljen arra, hogy ezt az alagút mindkét oldalán használja, és legfeljebb 25 alfanumerikus karakterből álljon. A speciális karakterek nem támogatottak. 

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha használja a szolgáltató által felügyelt Layer 3 szolgáltatások (általában egy IPVPN, mint az MPLS), a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást Ön helyett. 

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. Ellenőrizze a szolgáltatónál, mielőtt konfigurálná a BGP társviszony-létesítéseket.
> 
> 

## <a name="msft"></a>Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Microsoft társviszony-létesítési konfigurációjának.

> [!IMPORTANT]
> Microsoft társviszony-létesítés voltak beállítva a 2017. augusztus 1. ExpressRoute-Kapcsolatcsoportok az összes szolgáltatás előtagkészletet hirdeti meg a Microsoft társviszony-létesítéshez, akkor is, ha nincsenek meghatározva útvonalszűrők fog rendelkezni. Microsoft társviszony-létesítésre vannak konfigurálva, vagy 2017. augusztus 1. után az ExpressRoute-Kapcsolatcsoportok, nem rendelkezik minden olyan előtagok mindaddig, amíg egy útvonalszűrőhöz csatolva van a kapcsolatcsoport hirdetve. További információkért lásd: [konfigurálása a Microsoft társviszony-létesítés egy útvonalszűrőhöz](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. Ellenőrizze a **szolgáltató állapotát** , és győződjön meg arról, hogy a kapcsolat szolgáltatója teljes mértékben kiépíti az áramkört a folytatás előtt.

   Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a Microsoft társviszony-létesítés, a kapcsolat szolgáltatóját. Ebben az esetben nem kell követnie a következő szakaszokban ismertetett utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa ezeket a lépéseket.

   **Áramköri szolgáltató állapota: nincs kiépítve**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Áramkör-szolgáltató állapota: kiépítve**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos hivatkozásokat kell használnia az ugyanazon VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha azt tervezi, az előtagok megadni, küldhet egy vesszővel tagolt lista. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * **Nem kötelező –** ügyfél ASN: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-szám, megadhatja az AS-számot, amelyre regisztrálva vannak.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * **Nem kötelező –** egy MD5-kivonat, ha használni kívánja.
3. Kiválaszthatja, hogy a társviszonyt szeretne beállítani, az alábbi példában látható módon. Válassza ki a Microsoft társviszony-létesítés sorát.

   [![Válassza ki a Microsoft egyenrangú sorát](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Válassza ki a Microsoft egyenrangú sorát")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurálja a Microsoft társviszony-beállítást. **Mentse** a konfigurációt, miután megadta az összes paramétert. Az alábbi képen egy példa konfiguráció látható:

   ![Konfigurálja a Microsoft társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "meghirdetett nyilvános előtagok" és "peer ASN" (vagy "Customer ASN") hozzá van-e rendelve az Internet Routing beállításjegyzékben. Ha egy másik entitásból kéri le a nyilvános előtagokat, és ha a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, akkor az automatikus ellenőrzés nem fejeződik be, és manuális ellenőrzésre van szükség. Ha az automatikus ellenőrzés meghiúsul, az "érvényesítés szükséges" üzenet jelenik meg. 
>
> Ha az "érvényesítés szükséges" üzenet jelenik meg, Gyűjtse össze azokat a dokumentumokat, amelyek megjelenítik a nyilvános előtagokat a szervezethez az útválasztási beállításjegyzékben szereplő előtagok tulajdonosának listáján felsorolt entitások alapján, és küldje el ezeket a dokumentumokat kézi ellenőrzésre a következő módon: támogatási jegy megnyitása az alább látható módon. 
>

   Ha az áramkör "érvényesítés szükséges" állapotba kerül, meg kell nyitnia egy támogatási jegyet, hogy igazolja az előtagok tulajdonjogát a támogatási csapatnak. Közvetlenül a portálról, nyithat egy támogatási jegyet, az alábbi példában látható módon:

   ![Ellenőrzés szükséges – támogatási jegy](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. A konfiguráció sikeres elfogadását követően az alábbi képhez hasonlóan fog megjelenni:

   ![Egyenrangú állapot: konfigurálva](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Egyenrangú állapot: konfigurálva")]

### <a name="getmsft"></a>A Microsoft társviszony-létesítés részleteinek megtekintése

A Microsoft-társak tulajdonságainak megtekintéséhez válassza ki a társítás sorát.

[![Microsoft-partneri tulajdonságok megtekintése](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Tulajdonságok megtekintése")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>A Microsoft társviszony-létesítés konfigurációjának frissítése

Kiválaszthatja a módosítani kívánt társítás sorát, majd módosíthatja a társítási tulajdonságokat, és mentheti a módosításokat.

![Egyenrangú sor kijelölése](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>A Microsoft társviszony-létesítés törlése

A társítási konfigurációt a Delete (Törlés) ikonra kattintva távolíthatja el, ahogy az alábbi képen is látható:

![Társítás törlése](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Az Azure privát társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. 

   Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti engedélyezése az Azure privát társviszony-létesítést, a kapcsolat szolgáltatóját. Ebben az esetben nem kell követnie a következő szakaszokban ismertetett utasításokat. Ha azonban a kapcsolat szolgáltatója nem kezeli az útválasztást, az áramkör létrehozása után folytassa a következő lépésekkel.

   **Áramköri szolgáltató állapota: nincs kiépítve**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Áramkör-szolgáltató állapota: kiépítve**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem kell minden olyan virtuális hálózatok számára lefoglalt címtér részét. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem kell minden olyan virtuális hálózatok számára lefoglalt címtér részét. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos hivatkozásokat kell használnia az ugyanazon VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társhoz az 65515 és 65520 közötti számot is használhatja, amely magában foglalja a többit.
   * Az útvonalakat a helyszíni peremhálózati útválasztóról az Azure-ba kell hirdetni a BGP-n keresztül, amikor beállítja a privát társítást.
   * **Nem kötelező –** egy MD5-kivonat, ha használni kívánja.
3. Válassza ki az Azure privát társ-összevonási sorát az alábbi példában látható módon:

   [![Válassza ki a privát társas sort](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Válassza ki a privát társas sort")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurálja a privát társviszony-létesítést. **Mentse** a konfigurációt, miután megadta az összes paramétert.

   ![Konfigurálja a privát társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Miután a konfiguráció sikeres elfogadása, megjelenik az alábbi példához hasonló:

   ![privát társak mentve](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Az Azure privát társviszony-létesítés részleteinek megtekintése

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

[![Privát egyenrangú tulajdonságok megtekintése](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Privát egyenrangú tulajdonságok megtekintése")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. A frissítés után mentse a módosításokat.

![Frissítse a privát társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Az Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a Törlés ikonra kattintva távolíthatja el az alábbi képen látható módon:

> [!WARNING]
> Biztosítania kell, hogy a virtuális hálózatok és a globális elérhetőségű ExpressRoute-kapcsolatok eltávolítása a példa futtatása előtt. 
> 
> 

![magánhálózati társviszony-létesítés törlése](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Az Azure nyilvános társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának.

> [!Note]
> Az Azure nyilvános társítása elavult az új áramköröknél. További információ: [ExpressRoute peering](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Az Azure nyilvános társviszony-létesítés részleteinek megtekintése

Tekintse meg az Azure-beli nyilvános társak tulajdonságait a társítás kiválasztásával.

### <a name="updatepublic"></a>Az Azure nyilvános társviszony-létesítés konfigurációjának frissítése

Válassza ki a társításhoz tartozó sort, majd módosítsa a társítási tulajdonságokat.

### <a name="deletepublic"></a>Az Azure nyilvános társviszony-létesítés törlése

A Törlés ikonra kattintva távolítsa el a társ-konfigurációt.

## <a name="next-steps"></a>Következő lépések

Következő lépés, [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)
* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
