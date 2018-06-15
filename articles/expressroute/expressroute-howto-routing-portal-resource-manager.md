---
title: 'Útválasztási (társviszony-létesítés) a egy ExpressRoute-áramkör konfigurálása: erőforrás-kezelő: Azure |} Microsoft Docs'
description: A cikk az ExpressRoute-kapcsolatcsoportok privát, nyilvános és Microsoft társviszony-létesítéses létrehozásának és kiépítésének lépéseit ismerteti. A cikk azt is bemutatja, hogyan ellenőrizheti a kapcsolatcsoport társviszonyainak állapotát, illetve hogyan frissítheti vagy törölheti őket.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2018
ms.author: cherylmc
ms.openlocfilehash: f0f0a31abc4e2d3114d71729c6c447c569295290
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
ms.locfileid: "30314030"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Létrehozásához és módosításához az ExpressRoute-kör társviszony

Ez a cikk segít hozhat létre és kezelhet a Resource Manager üzembe helyezési modellel, az Azure portál használatával ExpressRoute-kapcsolatcsoportot útválasztási konfigurációja. Ellenőrizze az állapot, frissítési vagy törlési is, és az ExpressRoute-kör társviszony kiosztásának megszüntetése. Ha más módszert használja a kapcsolatcsoport dolgozni szeretne, válassza ki egy cikk az alábbi listából:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privát társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - nyilvános társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Videó – a Microsoft társviszony-létesítés](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasszikus)](expressroute-howto-routing-classic.md)
> 


## <a name="configuration-prerequisites"></a>Konfigurációs előfeltételek

* A konfigurálás megkezdése előtt mindenképp tekintse át az [előfeltételek](expressroute-prerequisites.md), az [útválasztási követelmények](expressroute-routing.md) és a [munkafolyamatok](expressroute-workflows.md) lapot.
* Egy aktív ExpressRoute-kapcsolatcsoportra lesz szüksége. Kövesse az [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md) részben foglalt lépéseket, és engedélyeztesse a kapcsolatcsoportot kapcsolatszolgáltatójával, mielőtt továbblépne. Az ExpressRoute-kapcsolatcsoport ahhoz, hogy tud-parancsmagok futtatásához a következő szakaszokban lévő kiépített és engedélyezett állapotban kell lennie.
* Ha egy megosztott kulcsot/MD5 kivonatoló használja, ügyeljen arra, hogy az használja ezt a-alagút mindkét oldalon, és legfeljebb 25 karakterek számát.

Az utasítások csak 2. rétegbeli kapcsolatszolgáltatásokat kínáló szolgáltatóknál létrehozott kapcsolatcsoportokra vonatkoznak. A szolgáltató által kezelt használata réteg (általában egy IPVPN, például az MPLS) 3 szolgáltatások, a kapcsolat szolgáltatójánál konfigurálja és kezeli az Ön útválasztást. 

> [!IMPORTANT]
> A szolgáltatásfelügyeleti portálon jelenleg nem hirdetünk szolgáltatók által konfigurált társviszony-létesítéseket. Dolgozunk azon, hogy hamarosan bevezethessük ezt a képességet. A BGP társviszony konfigurálása előtt ellenőrizze a szolgáltató.
> 
> 

Egy, két vagy akár mindhárom társviszony-létesítést (Azure privát, Azure nyilvános és Microsoft) is konfigurálhatja egy adott ExpressRoute-kapcsolatcsoportban. A társviszony-létesítéseket tetszőleges sorrendben konfigurálhatja. Az egyes társviszony-létesítéseket azonban mindenképp egyenként kell végrehajtania. További információ az útválasztási tartományok és esetében: [ExpressRoute útválasztási tartományok](expressroute-circuit-peerings.md).

## <a name="msft"></a>A Microsoft társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissítése és a Microsoft társviszony-létesítési konfiguráció az ExpressRoute-kapcsolatcsoportot törli.

> [!IMPORTANT]
> A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok 2017. augusztus 1. előtt konfigurált meghirdetett keresztül a Microsoft társviszony-létesítést, még akkor is, ha az útvonal-szűrők nem definiált összes szolgáltatás előtagok fog rendelkezni. A Microsoft társviszony-létesítést az ExpressRoute-Kapcsolatcsoportok vannak konfigurálva, vagy azt követően 2017. augusztus 1. nem rendelkezik a előtagokat amíg útvonal szűrő nem csatlakoztatja a kapcsolatcsoport hirdetve. További információkért lásd: [konfigurálása a Microsoft társviszony-létesítéshez útvonal szűrő](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Microsoft társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. Ha a kapcsolat szolgáltatójánál felügyelt 3. rétegbeli szolgáltatásokat nyújt, kérje meg a kapcsolat szolgáltatójánál engedélyezése a Microsoft társviszony-létesítés meg. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolat szolgáltatójánál nem kezeli az útválasztást, a kapcsolat létrehozása után továbbra is a konfiguráció a következő lépéseket.

  ![a Microsoft társviszony-létesítés felsorolása](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurálja a Microsoft társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná, ellenőrizze az alábbi információk meglétét.

  * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie, amely az Ön tulajdonában van, és regisztrálva van egy RIR-/IRR-jegyzékben.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
  * Meghirdetett előtagok: Meg kell adnia a BGP-munkamenetben meghirdetni kívánt összes előtag listáját. A rendszer kizárólag a nyilvános IP-cím-előtagokat fogadja el. Ha szeretne elküldhető a előtagokat, elküldheti egy vesszővel elválasztott listában. Az előtagoknak egy RIR/IRR jegyzékben regisztrálva kell lenniük az Ön neve alatt.
  * **Választható -** ügyfél ASN: Ha nincs regisztrálva a társviszony-létesítés SZÁMOT hirdetési előtagok, megadhatja a AS számot, amelyhez regisztrálja azokat a rendszer.
  * Útválasztási jegyzék neve: Megadhatja az RIR/IRR jegyzék nevét, amelyben az AS-szám és az előtagok regisztrálva vannak.
  * **Választható -** az MD5 kivonatoló, ha úgy dönt, hogy egyetlen módszer alkalmazása.
3. Kiválaszthatja a társviszony-létesítés szeretne beállítani, a következő példában látható módon. Válassza ki a Microsoft társviszony-létesítés sorát.

  ![Válassza ki a Microsoft társviszony-létesítési sor](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Konfigurálja a Microsoft társviszony-beállítást. Az alábbi képen a konfigurációs példát mutat be:

  ![Konfigurálja a Microsoft társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Mentse a konfigurációt, miután megadta az összes paramétert.

  Ha a kapcsolatcsoport kap-e a "Validation szükséges" állapotba kerül (mivel az ábrán látható), meg kell nyitnia egy támogatási jegy megjelenítése a támogatási csapat az előtagok tulajdonjogát igazolása.

  ![A Microsoft társviszony-létesítési konfiguráció mentése](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

  Közvetlenül a portálról, egy támogatási jegy nyithatja meg a következő példában látható módon:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Miután a konfiguráció sikeresen elfogadva, az alábbi képen hasonló látja:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="getmsft"></a>A Microsoft társviszony-létesítési részleteinek megtekintése

Az Azure nyilvános társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="updatemsft"></a>A Microsoft társviszony-létesítési konfiguráció frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="deletemsft"></a>A Microsoft társviszony törlése

Eltávolíthatja a társviszony-létesítési konfiguráció; ehhez válassza a Törlés ikonra, a következő ábrán látható módon:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="private"></a>Az Azure magánhálózati társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissítése és törölni az Azure magánhálózati társviszony-létesítési ExpressRoute-kapcsolatcsoportot.

### <a name="to-create-azure-private-peering"></a>Azure privát társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. Ha a kapcsolat szolgáltatójánál felügyelt 3. rétegbeli szolgáltatásokat nyújt, kérje meg a kapcsolat szolgáltatójánál engedélyezése az Azure magánhálózati társviszony-létesítés meg. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolat szolgáltatójánál nem kezeli az útválasztást, a kapcsolat létrehozása után továbbra is a konfiguráció a következő lépéseket.

  ![lista](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurálja az Azure privát társviszony-létesítést a kapcsolatcsoport számára. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

  * Egy /30 alhálózat az elsődleges kapcsolat számára. Az alhálózat nem virtuális hálózatok számára fenntartott címtartomány részének kell lennie.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Az alhálózat nem virtuális hálózatok számára fenntartott címtartomány részének kell lennie.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat. Ehhez a társviszony-létesítéshez használhat privát AS-számokat is. Ne használja a 65515 számot.
  * **Választható -** az MD5 kivonatoló, ha úgy dönt, hogy egyetlen módszer alkalmazása.
3. Válassza ki az Azure magánhálózati társviszony-létesítési sorban, a következő példában látható módon:

  ![Magánfelhő](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Konfigurálja a privát társviszony-létesítést. Az alábbi képen a konfigurációs példát mutat be:

  ![Konfigurálja a magánhálózati társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Mentse a konfigurációt, miután megadta az összes paramétert. Miután a konfiguráció sikeresen elfogadva, valami a következőhöz hasonló:

  ![Mentse a magánhálózati társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="getprivate"></a>Az Azure a magánhálózati társviszony-létesítési részletek megtekintéséhez

Az Azure privát társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![magánhálózati társviszony-létesítés megtekintése](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="updateprivate"></a>Az Azure magánhálózati társviszony-létesítési konfiguráció frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait.

![Frissítse a magánhálózati társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="deleteprivate"></a>Az Azure magánhálózati társviszony-létesítés törlése

Eltávolíthatja a társviszony-létesítési konfiguráció; ehhez válassza a Törlés ikonra, a következő ábrán látható módon:

![Törölje a magánhálózati társviszony-létesítés](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="public"></a>Az Azure nyilvános társviszony-létesítés

Ez a szakasz segítséget nyújt a létrehozása, beolvasása, frissítése és törlése az Azure nyilvános társviszony-létesítési konfiguráció ExpressRoute-kör.

### <a name="to-create-azure-public-peering"></a>Azure nyilvános társviszony-létesítés létrehozása

1. Konfigurálja az ExpressRoute-kapcsolatcsoportot. A folytatás előtt győződjön meg róla, hogy a kapcsolatszolgáltató teljesen kiépítette a kapcsolatcsoportot. Ha a kapcsolat szolgáltatójánál felügyelt 3. rétegbeli szolgáltatásokat nyújt, kérje meg a kapcsolat szolgáltatójánál engedélyezése az Azure nyilvános társviszony-létesítés meg. Ebben az esetben nem szükséges a következő szakaszokban foglalt lépéseket végrehajtania. Azonban ha a kapcsolat szolgáltatójánál nem kezeli az útválasztást, a kapcsolat létrehozása után továbbra is a konfiguráció a következő lépéseket.

  ![nyilvános társviszony felsorolása](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Konfigurálja az Azure nyilvános társviszony-létesítést a kapcsolatcsoporthoz. Mielőtt folytatná a következő lépésekkel, ellenőrizze az alábbi elemek meglétét:

  * Egy /30 alhálózat az elsődleges kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
  * Egy /30 alhálózat a másodlagos kapcsolat számára. Ennek egy érvényes nyilvános IPv4-előtagnak kell lennie.
  * Egy érvényes VLAN-azonosító a tárviszony-létesítés létrehozásához. Győződjön meg róla, hogy a kapcsolatcsoporton egy másik társviszony-létesítés sem használja ugyanezt a VLAN-azonosítót.
  * Egy AS-szám a társviszony-létesítéshez. 2 és 4 bájtos AS-számokat is használhat.
  * **Választható -** az MD5 kivonatoló, ha úgy dönt, hogy egyetlen módszer alkalmazása.
3. Válassza ki az Azure nyilvános társviszony-létesítési sorban, a következő ábrán látható módon:

  ![Válassza ki a nyilvános társviszony-létesítési sor](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Konfigurálja a nyilvános társviszony-létesítést. Az alábbi képen a konfigurációs példát mutat be:

  ![Nyilvános társviszony konfigurálása](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Mentse a konfigurációt, miután megadta az összes paramétert. Miután a konfiguráció sikeresen elfogadva, valami a következőhöz hasonló:

  ![Nyilvános társviszony-létesítési konfiguráció mentése](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="getpublic"></a>Az Azure nyilvános társviszony-létesítési részleteinek megtekintése

Az Azure nyilvános társviszony-létesítés tulajdonságainak megtekintéséhez válassza ki a társviszony-létesítést.

![nyilvános társviszony-létesítési tulajdonságainak megtekintése](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="updatepublic"></a>Az Azure nyilvános társviszony-létesítési konfiguráció frissítése

A társviszony-létesítés sorának kijelölésével módosíthatja a társviszony-létesítés tulajdonságait.

![Válassza ki a nyilvános társviszony-létesítési sor](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="deletepublic"></a>Az Azure nyilvános társviszony törlése

Eltávolíthatja a társviszony-létesítési konfiguráció; ehhez válassza a Törlés ikonra, a következő példában látható módon:

![nyilvános társviszony törlése](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="next-steps"></a>További lépések

Következő lépés, [egy virtuális hálózat csatolása ExpressRoute-kapcsolatcsoportot](expressroute-howto-linkvnet-portal-resource-manager.md)
* Az ExpressRoute-munkafolyamatokkal kapcsolatos további információkért lásd: [ExpressRoute workflows](expressroute-workflows.md) (ExpressRoute-munkafolyamatok).
* A kapcsolatcsoportok társviszony-létesítéseivel kapcsolatos további információkért lásd: [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok).
* További információkért a virtuális hálózatok használatáról lásd: [Virtual network overview](../virtual-network/virtual-networks-overview.md) (Virtuális hálózatok áttekintése).
