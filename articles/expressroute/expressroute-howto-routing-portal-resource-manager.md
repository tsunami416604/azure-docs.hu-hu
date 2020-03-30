---
title: 'Azure ExpressRoute: Társviszony-létesítés konfigurálása'
description: Ez a cikk az ExpressRoute privát és microsoftos társviszony-létesítésének lépéseit dokumentálja. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az állapotát, frissítését vagy törlését egy kapcsolatcsoportban.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: mialdrid
ms.openlocfilehash: 18d2db18e9880028c60b4b545c3628f4a9cb4703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264790"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport társviszonyának létrehozása és módosítása

Ez a cikk segít létrehozni és kezelni az Azure Resource Manager (ARM) ExpressRoute-kapcsolaton, az Azure Portal használatával. Egy ExpressRoute-kapcsolatkapcsolat állapotát, frissítését vagy törlését és megszüntetését is ellenőrizheti. Ha más módszert szeretne használni a kapcsolatcsoport használatához, válasszon ki egy cikket az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure-portál](expressroute-howto-routing-portal-resource-manager.md)
> * [Powershell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Nyilvános társviszony-létesítés](about-public-peering.md)
> * [Videó - Privát társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft-társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Konfigurálhatja a privát társviszony-létesítésés és a Microsoft-társviszony-létesítés egy ExpressRoute-kapcsolati kapcsolat (Az Azure nyilvános társviszony-létesítés elavult az új áramkörök). A társviszony-létesítés tetszőleges sorrendben konfigurálható. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. A tartományok és társviszony-létesítések útválasztási tartományairól további információt az [ExpressRoute útválasztási tartományok című témakörben talál.](expressroute-circuit-peerings.md) A nyilvános társviszony-létesítésről az [ExpressRoute nyilvános társviszony-létesítéscímű témakörben](about-public-peering.md)talál.

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. A társviszony-létesítés(ek) konfigurálásához az ExpressRoute-kapcsolati kapcsolatnak kiépített és engedélyezett állapotban kell lennie. 
* Ha megosztott kulcsot/MD5-kivonatot kíván használni, győződjön meg róla, hogy ezt az alagút mindkét oldalán használja, és az alfanumerikus karakterek számát legfeljebb 25-re korlátozza. A speciális karakterek nem támogatottak. 

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. Ha olyan szolgáltatót használ, amely felügyelt Layer 3 szolgáltatásokat (általában IPVPN-t, például MPLS-t) kínál, a kapcsolatszolgáltató konfigurálja és kezeli az útválasztást. 

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP-társviszony-létesítések konfigurálása előtt érdeklődjön a szolgáltatónál.
> 
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt az ExpressRoute-kapcsolati kapcsolatbeállításainak létrehozásában, behírlése, frissítésében és törlésében.

> [!IMPORTANT]
> 2017. augusztus 1-je előtt konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése a Microsoft társviszony-létesítésével a Microsoft társviszony-létesítésén keresztül lesz meghirdetve, még akkor is, ha nincsenek megadva útvonalszűrők. 2017. augusztus 1-jén vagy azt követően konfigurált ExpressRoute-áramkörök Microsoft-társviszony-létesítése nem rendelkezik előtagokkal, amíg egy útvonalszűrő nem csatlakozik az áramkörhöz. További információt a [Microsoft-társviszony-létesítés útvonalszűrőjének konfigurálása](how-to-routefilter-powershell.md)című témakörben talál.
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. Ellenőrizze a **szolgáltató állapotát,** és győződjön meg arról, hogy a kapcsolatszolgáltató teljes mértékben ki van építve, mielőtt tovább haladna.

   Ha a kapcsolódási szolgáltató felügyelt Layer 3 szolgáltatásokat kínál, megkérheti a kapcsolódási szolgáltatót, hogy engedélyezze a Microsoft-társviszony-létesítést. Ebben az esetben nem kell követnie a következő szakaszokban felsorolt utasításokat. Ha azonban a kapcsolatszolgáltató nem kezeli az útválasztást, a kapcsolatcsoport létrehozása után folytassa ezeket a lépéseket.

   **Circuit - Szolgáltató állapota: Nincs kiépítve**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Provider status: Not provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuit - Szolgáltató állapota: Kiépítve**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Provider status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet rendeli az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ebből az alhálózatból az első használható IP-címet rendeli az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Mind az elsődleges, mind a másodlagos hivatkozásokhoz ugyanazt a VLAN-azonosítót kell használnia.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha előtagok készletét tervezi elküldeni, vesszővel tagolt listát küldhet. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * **Nem kötelező -** Ügyfél ASN: Ha olyan előtagokat hirdet, amelyek nincsenek regisztrálva a társviszony-létesítő AS-számhoz, megadhatja azt az AS-számot, amelyre regisztrálva vannak.
   * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
   * **Nem kötelező -** MD5-kivonat, ha úgy dönt, hogy használ egyet.
3. Kiválaszthatja a konfigurálni kívánt társviszony-létesítést, ahogy az a következő példában is látható. Válassza ki a Microsoft társviszony-létesítés sorát.

   [![A Microsoft társviszony-létesítési sorának kijelölése](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "A Microsoft társviszony-létesítési sorának kijelölése")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurálja a Microsoft társviszony-beállítást. **Mentse** a konfigurációt, miután megadta az összes paramétert. Az alábbi képen egy példakonfiguráció látható:

   ![A Microsoft-társviszony konfigurálása](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> A Microsoft ellenőrzi, hogy a megadott "Hirdetett nyilvános előtagok" és a "Társ ASN" (vagy "Customer ASN") hozzá vannak-e rendelve az Internetes útválasztási beállításjegyzékben. Ha a nyilvános előtagokat egy másik entitástól kapja, és a hozzárendelés nincs rögzítve az útválasztási beállításjegyzékben, az automatikus érvényesítés nem fejeződik be, és manuális érvényesítést igényel. Ha az automatikus ellenőrzés sikertelen, az "Érvényesítés szükséges" üzenet jelenik meg. 
>
> Ha az "Érvényesítés szükséges" üzenet jelenik meg, gyűjtse össze azokat a dokumentum(okait), amelyek(ek)et az útválasztási jegyzékben az előtagok tulajdonosaként felsorolt entitás a szervezethez rendeli,(/a) gyűjtse össze azokat a dokumentum(okat), amelyek (2) bekezdéssel megegyezésre vannak. támogatási jegy megnyitását az alábbiak szerint. 
>

   Ha az áramkör "Érvényesítés szükséges" állapotba kerül, meg kell nyitnia egy támogatási jegyet, hogy bizonyítsa az előtagok tulajdonjogát a támogatási csapatunk számára. A támogatási jegyet közvetlenül a portálról is megnyithatja, ahogy az a következő példában látható:

   ![Érvényesítés szükséges - támogatási jegy](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. A konfiguráció sikeres elfogadása után az alábbihoz hasonló kép jelenik meg:

   ![Társviszony-létesítésállapota: Konfigurálva](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Társviszony-létesítésállapota: Konfigurálva")]

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Microsoft társviszony-létesítés részleteinek megtekintése

A Microsoft társviszony-létesítési tulajdonságait a társviszony-létesítés sorának kiválasztásával tekintheti meg.

[![A Microsoft társviszony-létesítési tulajdonságainak megtekintése](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Tulajdonságok megtekintése")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Microsoft társviszony-létesítés konfigurációjának frissítése

Kiválaszthatja a módosítani kívánt társviszony-létesítés i sorát, majd módosíthatja a társviszony-létesítési tulajdonságokat, és mentheti a módosításokat.

![Társviszony-létesítési sor kiválasztása](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Microsoft társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a törlés ikonra kattintva távolíthatja el, ahogy az az alábbi képen látható:

![Társviszony törlése](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Azure privát társviszony-létesítés

Ez a szakasz segít létrehozni, beszerezni, frissíteni és törölni az Azure privát társviszony-létesítési konfiguráció egy ExpressRoute-kapcsolati kapcsolat.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. 

   Ha a kapcsolódási szolgáltató felügyelt Layer 3 szolgáltatásokat kínál, kérheti meg a kapcsolatszolgáltatótól, hogy engedélyezze az Azure privát társviszony-létesítését. Ebben az esetben nem kell követnie a következő szakaszokban felsorolt utasításokat. Ha azonban a kapcsolatszolgáltató nem kezeli az útválasztást, a kapcsolatcsoport létrehozása után folytassa a következő lépésekkel.

   **Circuit - Szolgáltató állapota: Nincs kiépítve**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Provider status = Not Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuit - Szolgáltató állapota: Kiépítve**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Provider Status = Provisioned")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem lehet része a virtuális hálózatok számára fenntartott címtérnek. Ebből az alhálózatból az első használható IP-címet rendeli az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem lehet része a virtuális hálózatok számára fenntartott címtérnek. Ebből az alhálózatból az első használható IP-címet rendeli az útválasztóhoz, mivel a Microsoft a második használható IP-címet használja az útválasztóhoz.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Mind az elsődleges, mind a másodlagos hivatkozásokhoz ugyanazt a VLAN-azonosítót kell használnia.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számot, kivéve a 65515 és 65520 közötti számot, beleértve.
   * A privát társviszony-létesítés beállításakor a helyszíni Edge-útválasztóról az Azure-ba a BGP-n keresztül kell hirdetnie az útvonalakat.
   * **Nem kötelező -** MD5-kivonat, ha úgy dönt, hogy használ egyet.
3. Válassza ki az Azure privát társviszony-létesítési sor, ahogy az a következő példában látható:

   [![A privát társviszony-létesítési sor kijelölése](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "A privát társviszony-létesítési sor kijelölése")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurálja a privát társviszony-létesítést. **Mentse** a konfigurációt, miután megadta az összes paramétert.

   ![privát társviszony-létesítés konfigurálása](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. A konfiguráció sikeres elfogadása után a következő példához hasonló tanév jelenik meg:

   ![mentett privát társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Azure privát társviszony-létesítés részleteinek megtekintése

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

[![Privát társviszony-létesítési tulajdonságok megtekintése](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Privát társviszony-létesítési tulajdonságok megtekintése")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait. A frissítés után mentse a módosításokat.

![privát társviszony-létesítés frissítése](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Azure privát társviszony-létesítés törlése

A társviszony-létesítési konfigurációt a törlés ikon kiválasztásával távolíthatja el, ahogy az az alábbi képen látható:

> [!WARNING]
> A példa futtatása előtt gondoskodnia kell arról, hogy az összes virtuális hálózat és az ExpressRoute globális elérési kapcsolat törlődjön. 
> 
> 

![privát társviszony-létesítés törlése](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)


## <a name="next-steps"></a>További lépések

Következő lépés: [Virtuális hálózat csatolása ExpressRoute-kapcsolatra](expressroute-howto-linkvnet-portal-resource-manager.md)
* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
