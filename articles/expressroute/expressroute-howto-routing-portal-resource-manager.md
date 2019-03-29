---
title: 'Adja meg az ExpressRoute - kapcsolatcsoport társviszony-létesítést: Azure | Microsoft Docs'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6d1ce56ef66d224b89f49a00c2883ebbf22a5745
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622025"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>ExpressRoute-kapcsolatcsoport társviszony létesítése és módosítása

Ez a cikk segítséget nyújt a létrehozása, és a Resource Manager üzemi modellben az Azure portal használatával egy ExpressRoute-Kapcsolatcsoportok útválasztási konfigurációjának. Ellenőrizze az állapotot, frissítési vagy törlési is, és ExpressRoute-kapcsolatcsoport társviszonyainak megszüntetése. Ha szeretne egy másik módszer használható a kapcsolatcsoport-, válassza ki a cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Videó - privát társviszony](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Videó - nyilvános társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – Microsoft társviszony-létesítés](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. Útválasztási tartományok és a társviszony-létesítéseket kapcsolatos további információkért lásd: [Kapcsolatcsoportok és társviszony-létesítéseket](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport kiosztott és engedélyezett állapotban kell tudni futtassa a parancsmagokat a következő szakaszokban kell lennie.
* Ha azt tervezi, a megosztott kulcs/MD5-kivonatot használja, mindenképpen használja ezt az alagutat mindkét oldalán, és legfeljebb 25 alfanumerikus karakterek száma korlátozza. Speciális karakterek használata nem támogatott. 

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

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti a Microsoft társviszony-létesítés, a kapcsolat szolgáltatóját. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolatszolgáltató felügyeli az útválasztást, a kapcsolatcsoport létrehozását követően továbbra is a konfiguráció a következő lépéseket.

   ![Microsoft társviszony-létesítés listázása](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos hivatkozásokat kell használnia az ugyanazon VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha azt tervezi, az előtagok megadni, küldhet egy vesszővel tagolt lista. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
   * **Nem kötelező –** ügyfél ASN-ként: Ha olyan előtagokat hirdet meg, amelyek nem a társviszony-létesítési AS-szám, megadhatja az AS-számot, amelyre regisztrálva vannak.
   * Útválasztási jegyzék neve: Megadhatja az RIR / IRR jegyzék nevét, amely az AS-szám és az előtagok regisztrálva vannak.
   * **Nem kötelező –** egy MD5-kivonat, ha használni kívánja.
3. Kiválaszthatja, hogy a társviszonyt szeretne beállítani, az alábbi példában látható módon. Válassza ki a Microsoft társviszony-létesítés sorát.

   ![Válassza ki a Microsoft társviszony-létesítés sort](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Konfigurálja a Microsoft társviszony-beállítást. Az alábbi kép egy példakonfigurációt mutat be:

   ![Konfigurálja a Microsoft társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Mentse a konfigurációt, miután megadta az összes paramétert.

   Ha a kapcsolatcsoport lekérdezi a "Érvényesítését szükséges" állapotba (ahogy az ábrán látható), meg kell nyitnia egy támogatási jegyet az ügyfélszolgálati csapatunkhoz az előtagok tulajdonjogának megvalósíthatósági megjelenítéséhez.

   ![A Microsoft társviszony-létesítési konfigurációjának mentése](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

   Közvetlenül a portálról, nyithat egy támogatási jegyet, az alábbi példában látható módon:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Miután a konfiguráció sikeres elfogadása, hasonló az alábbi képen láthatja:

   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>A Microsoft társviszony-létesítés részleteinek megtekintése

Az Azure nyilvános társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>A Microsoft társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>A Microsoft társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a Törlés ikonra kattintva távolíthatja el az alábbi képen látható módon:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Az Azure privát társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Azure privát társviszony-létesítési konfigurációjának.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti engedélyezése az Azure privát társviszony-létesítést, a kapcsolat szolgáltatóját. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolatszolgáltató felügyeli az útválasztást, a kapcsolatcsoport létrehozását követően továbbra is a konfiguráció a következő lépéseket.

   ![lista](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem kell minden olyan virtuális hálózatok számára lefoglalt címtér részét. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem kell minden olyan virtuális hálózatok számára lefoglalt címtér részét. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos hivatkozásokat kell használnia az ugyanazon VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Használhat privát AS-szám a társviszony a 65515 – 65520, szám kivételével szélsőértékeket is beleértve.
   * **Nem kötelező –** egy MD5-kivonat, ha használni kívánja.
3. Válassza ki az Azure-beli privát társviszony-létesítés sort, az alábbi példában látható módon:

   ![privát](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Konfigurálja a privát társviszony-létesítést. Az alábbi kép egy példakonfigurációt mutat be:

   ![Konfigurálja a privát társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Mentse a konfigurációt, miután megadta az összes paramétert. Miután a konfiguráció sikeres elfogadása, megjelenik az alábbi példához hasonló:

   ![Mentse a privát társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Az Azure privát társviszony-létesítés részleteinek megtekintése

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![magánhálózati társviszony-létesítés megtekintése](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Azure privát társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait.

![Frissítse a privát társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Az Azure privát társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a Törlés ikonra kattintva távolíthatja el az alábbi képen látható módon:

> [!WARNING]
> Biztosítania kell, hogy a virtuális hálózatok és a globális elérhetőségű ExpressRoute-kapcsolatok eltávolítása a példa futtatása előtt. 
> 
> 

![magánhálózati társviszony-létesítés törlése](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Az Azure nyilvános társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissíteni és törölni az ExpressRoute-kapcsolatcsoport Azure nyilvános társviszony-létesítési konfigurációjának.

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. Ha kapcsolatszolgáltatója felügyelt 3. rétegbeli szolgáltatásokat kínál, megkérheti engedélyezése az Azure nyilvános társviszony-létesítést, a kapcsolat szolgáltatóját. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolatszolgáltató felügyeli az útválasztást, a kapcsolatcsoport létrehozását követően továbbra is a konfiguráció a következő lépéseket.

   ![nyilvános társviszony-létesítés listázása](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

   * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó. 
   * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie. Ez az alhálózat a rendel az első gyakorlatot IP-címet az útválasztó, a Microsoft használ a második gyakorlatot IP-címet az útválasztó.
   * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót. Az elsődleges és a másodlagos hivatkozásokat kell használnia az ugyanazon VLAN-azonosítót.
   * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
   * **Nem kötelező –** egy MD5-kivonat, ha használni kívánja.
3. Válassza ki az Azure nyilvános társviszony-létesítés sort, az alábbi képen látható módon:

   ![Válassza ki a nyilvános társviszony-létesítés sort](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Konfigurálja a nyilvános társviszony-létesítést. Az alábbi kép egy példakonfigurációt mutat be:

   ![Konfigurálja a nyilvános társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Mentse a konfigurációt, miután megadta az összes paramétert. Miután a konfiguráció sikeres elfogadása, megjelenik az alábbi példához hasonló:

   ![Nyilvános társviszony-létesítési konfigurációjának mentése](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Az Azure nyilvános társviszony-létesítés részleteinek megtekintése

Az Azure nyilvános társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![nyilvános társviszony-létesítés tulajdonságainak megtekintése](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Az Azure nyilvános társviszony-létesítés konfigurációjának frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait.

![Válassza ki a nyilvános társviszony-létesítés sort](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Az Azure nyilvános társviszony-létesítés törlése

A társviszony-létesítés konfigurációját a Törlés ikonra kattintva távolíthatja el az alábbi példában látható módon:

![nyilvános társviszony-létesítés törlése](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>További lépések

Következő lépés, [virtuális hálózat összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-portal-resource-manager.md)
* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
