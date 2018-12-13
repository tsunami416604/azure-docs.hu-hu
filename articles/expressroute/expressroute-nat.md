---
title: '– Az ExpressRoute-Kapcsolatcsoportok NAT-követelmények: Azure |} A Microsoft Docs'
description: Ez az oldal ExpressRoute-kapcsolatcsoportok NAT-konfigurálásának és -kezelésének részletes követelményeit ismerteti.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 4aa1b170b0c0c515faa67118772ad14d34c7276b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278253"
---
# <a name="expressroute-nat-requirements"></a>Az ExpressRoute NAT-követelményei
Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, NAT-okat kell beállítania és kezelnie. Egyes kapcsolatszolgáltatók felügyelt szolgáltatásként kínálják a NAT beállítását és kezelését. Ellenőrizze kapcsolatszolgáltatójánál, hogy kínál-e ilyen szolgáltatást. Ha nem, akkor meg kell felelnie az alább ismertetett követelményeknek. 

A különböző útválasztási tartományok áttekintését az [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md) oldalon tekintheti meg. Az Azure nyilvános és a Microsoft társviszony-létesítés nyilvános IP-cím követelményeinek való megfeleléshez javasoljuk, hogy állítsa be a NAT-ot a hálózata és a Microsoft között. Ez a szakasz a beállítandó NAT-infrastruktúra részletes leírását tartalmazza.

## <a name="nat-requirements-for-microsoft-peering"></a>A Microsoft társviszony-létesítésre vonatkozó NAT-követelmények
A Microsoft társviszony-létesítési útvonal lehetővé teszi, hogy az Azure nyilvános társviszony-létesítési útvonalon keresztül nem támogatott Microsoft-felhőszolgáltatásokhoz csatlakozzon. A szolgáltatások listájába beletartoznak az Office 365 szolgáltatásai, például az Exchange Online, a SharePoint Online, a Skype Vállalati verzió és a Dynamics 365. A Microsoft tervezi a kétirányú kapcsolatok támogatását a Microsoft társviszony-létesítésen keresztül. A Microsoft-felhőszolgáltatások felé irányuló forgalmat forrásoldali hálózati címfordítással érvényes nyilvános IPv4-címekké kell alakítani, mielőtt belép a Microsoft hálózatába. A Microsoft felhőszolgáltatásai felől az Ön hálózata felé irányuló forgalmon forrásoldali hálózati címfordítást kell végrehajtani az internetes peremhálózaton az [aszimmetrikus útválasztás](expressroute-asymmetric-routing.md) megelőzése érdekében. Az alábbi ábrán annak magas szintű ábrázolása látható, hogy hogyan kell beállítani a NAT-ot a Microsoft társviszony-létesítéshez.

![](./media/expressroute-nat/expressroute-nat-microsoft.png) 

### <a name="traffic-originating-from-your-network-destined-to-microsoft"></a>Az Ön hálózatából eredő, a Microsoft felé irányuló forgalom
* Biztosítania kell, hogy a forgalom a Microsoft társviszony-létesítési útvonalra érvényes nyilvános IPv4-címmel lépjen be. A Microsoftnak képesnek kell lennie ellenőriznie az IPv4 NAT-címkészlet tulajdonosát egy regionális útválasztási internetes jegyzékben (RIR) vagy egy internetes útválasztási jegyzékben (RIR) A rendszer a NAT-tal társviszonyban lévő AS-szám és az ahhoz használt IP-címek alapján ellenőrzést hajt végre. Az útválasztási nyilvántartásokkal kapcsolatos információkért tekintse meg [az ExpressRoute útválasztási követelményeit](expressroute-routing.md) ismertető oldalt.
* Az Azure nyilvános társviszony-létesítési beállításhoz és az egyéb ExpressRoute-kapcsolatcsoportokhoz használt IP-címek nem hirdethetők a Microsoft felé a BGP-munkameneten keresztül. A társviszony-létesítésen keresztül hirdetett NAT IP-előtag hosszára nem vonatkoznak korlátozások.
  
  > [!IMPORTANT]
  > A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.
  > 
  > 

### <a name="traffic-originating-from-microsoft-destined-to-your-network"></a>A Microsofttól eredő, az Ön hálózata felé irányuló forgalom
* Bizonyos forgatókönyvek megkövetelik, hogy a Microsoft kapcsolatot kezdeményezzen az Ön hálózatán üzemeltetett szolgáltatásvégpontokkal. A forgatókönyvre jellemző példa az Ön hálózatában üzemeltetett ADFS-kiszolgálókhoz való csatlakozás az Office 365-ből. Ilyen esetekben a megfelelő előtagokat ki kell szivárogtatnia a hálózatából a Microsoft-társviszonyba. 
* Az [aszimmetrikus útválasztás](expressroute-asymmetric-routing.md) kiküszöbölése érdekében a Microsoft-adatforgalmon forrásoldali hálózati címfordítást kell végrehajtani az internetes peremhálózaton, a hálózaton belüli szolgáltatásvégpontok számára. Az ExpressRoute-on keresztül kapott útvonallal megegyező cél IP-címmel ellátott kérelmeket **és válaszokat** a rendszer minden esetben ExpressRoute-on keresztül küldi el. Aszimmetrikus útválasztás akkor jön létre, ha a kérelem interneten keresztül érkezik, a választ viszont ExpressRoute-on keresztül küldjük el. A bejövő Microsoft-adatforgalom internetes peremhálózaton történő forrásoldali hálózati címfordítása úgy küszöböli ki ezt a problémát, hogy a válasz adatforgalmát újra az internetes peremhálózatra kényszeríti.

![Aszimmetrikus útválasztás az ExpressRoute-tal](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="nat-requirements-for-azure-public-peering"></a>Az Azure nyilvános társviszony-létesítés NAT-követelményei
Az Azure nyilvános társviszony-létesítési útvonal használatával az Azure-ban üzemeltetett összes szolgáltatáshoz csatlakozhat a nyilvános IP-címeiken keresztül. Ezen szolgáltatások közé tartoznak az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) című dokumentumban felsorolt szolgáltatások, valamint az ISV-k által a Microsoft Azure-ban üzemeltetett szolgáltatások. 

> [!IMPORTANT]
> A Microsoft Azure-szolgáltatásokhoz a nyilvános társviszony-létesítéssel létrehozott kapcsolatokat mindig az Ön hálózata kezdeményezi a Microsoft hálózata felé. Ezért a Microsoft Azure-szolgáltatásokból nem indíthatók munkamenetek az Ön hálózatába az ExpressRoute-on keresztül. Ilyen próbálkozás esetén a meghirdetett IP-címekre küldött csomagok az ExpressRoute helyett az internetet használják.
> 

A Microsoft Azure felé a nyilvános társviszony-létesítésen keresztül irányuló forgalmat forrásoldali hálózati címfordítással érvényes nyilvános IPv4-címekké kell alakítani, mielőtt belép a Microsoft hálózatába. Az alábbi ábrán annak magas szintű ábrázolása látható, hogy hogyan kell beállítani a NAT-ot a fenti követelmények teljesítéséhez.

![](./media/expressroute-nat/expressroute-nat-azure-public.png) 

### <a name="nat-ip-pool-and-route-advertisements"></a>NAT IP-készlet és útvonalhirdetések
Biztosítania kell, hogy a forgalom az Azure nyilvános társviszony-létesítési útvonalra érvényes nyilvános IPv4-címmel lépjen be. A Microsoftnak képesnek kell lennie ellenőriznie az IPv4 NAT-címkészlet tulajdonjogát egy regionális útválasztási internetes jegyzékben (RIR) vagy egy internetes útválasztási jegyzékben (RIR) A rendszer a NAT-tal társviszonyban lévő AS-szám és az ahhoz használt IP-címek alapján ellenőrzést hajt végre. Az útválasztási nyilvántartásokkal kapcsolatos információkért tekintse meg [az ExpressRoute útválasztási követelményeit](expressroute-routing.md) ismertető oldalt.

A társviszony-létesítésen keresztül hirdetett NAT IP-előtag hosszára nem vonatkoznak korlátozások. Meg kell figyelnie a NAT-készletet, és biztosítania kell, hogy nem fogy ki a NAT-munkamenetekből.

> [!IMPORTANT]
> A Microsoft számára hirdetett NAT IP-készlet nem hirdethető meg az interneten. Ez megszakítja a más Microsoft-szolgáltatások kapcsolatait.
> 
> 

## <a name="next-steps"></a>További lépések
* Tekintse meg az [Útválasztás](expressroute-routing.md) és a [QoS](expressroute-qos.md) követelményeit.
* További információkért lásd: [ExpressRoute-kapcsolatcsoportok kiépítési munkafolyamatai és kapcsolatcsoport-állapotok](expressroute-workflows.md).
* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-portal-resource-manager.md)
  * [Útválasztás konfigurálása](expressroute-howto-routing-portal-resource-manager.md)
  * [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-portal-resource-manager.md)

