---
title: NAT az Azure ExpressRoute-hoz | Microsoft Docs
description: "Ez az oldal ExpressRoute-kapcsolatcsoportok útválasztási konfigurálásának és kezelésének részletes követelményeit ismerteti."
documentationcenter: na
services: expressroute
author: osamazia
manager: ganesr
editor: 
ms.assetid: eaaf0393-d384-4496-9a5c-328e94c262a7
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2017
ms.author: osamam
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 5c039a80b24feda61da0793fa64b48cb4783c3f1
ms.contentlocale: hu-hu
ms.lasthandoff: 05/15/2017


---
<a id="nat-for-expressroute" class="xliff"></a>

# NAT az ExpressRoute-hoz

Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, be kell állítania és kezelnie kell az útválasztást. Egyes kapcsolatszolgáltatók az útválasztás beállítását és kezelését felügyelt szolgáltatásként kínálják. Ellenőrizze kapcsolatszolgáltatójánál, hogy kínálja-e ezt a szolgáltatást. Ha nem, akkor meg kell felelnie az alábbi követelményeknek. 

A kapcsolat elősegítésének érdekében beállítandó útválasztási munkamenetek leírását a [kapcsolatcsoportokat és útválasztási tartományokat](expressroute-circuit-peerings.md) ismertető cikkben találja.

> [!NOTE]
> A Microsoft nem támogat semmilyen útválasztó-redundancia protokollt (pl. HSRP, VRRP) a magas rendelkezésre állású konfigurációkhoz. A magas rendelkezésre állás érdekében csak a társviszonyonként meglévő BGP-munkamenetek redundáns párjára támaszkodunk.
> 
> 

<a id="ip-addresses-used-for-peerings" class="xliff"></a>

## Társviszony-létesítéshez használt IP-címek

Fenn kell tartania néhány IP-címblokkot az Ön hálózata és a Microsoft vállalati peremhálózati (MSEE) útválasztói közötti útválasztás konfigurálásához. Ez a szakasz a követelmények listáját tartalmazza, és ismerteti ezen IP-címek beszerzésére és használatára vonatkozó szabályokat.

<a id="ip-addresses-used-for-azure-private-peering" class="xliff"></a>

### Magánhálózati Azure-társviszony-létesítéshez használt IP-címek

A társviszony-létesítések konfigurálásához privát IP-címeket vagy nyilvános IP-címeket használhat. Az útvonalak konfigurálásához használt címtartományok nem lehetnek átfedésben a virtuális hálózatok az Azure-ban való létrehozásához használt címtartományokkal. 

* Egy /29 vagy két /30 alhálózatot le kell foglalnia az útválasztási felületek számára.
* Az útválasztáshoz használt alhálózatok privát IP-címeket vagy nyilvános IP-címeket használhatnak.
* Az alhálózatok nem ütközhetnek az ügyfél által a Microsoft Cloud használatához lefoglalt tartománnyal.
* Ha egy /29 alhálózatot használ, az két /30 alhálózatra lesz felosztva. 
  * Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz fogja használni a rendszer.
  * Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft fogja használni egy BGP-munkamenet beállításához.
  * Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.  

<a id="example-for-private-peering" class="xliff"></a>

#### Példa a privát társviszony-létesítésre

Ha az a.b.c.d/29 alhálózatot választja a társviszony-létesítés beállítására, az két /30 alhálózatra lesz felosztva. Az alábbi példában áttekintjük, hogyan használható az a.b.c.d/29 alhálózat. 

Az a.b.c.d/29 alhálózat fel lesz osztva az a.b.c.d/30 és az a.b.c.d+4/30 alhálózatra, és továbbítva lesz a Microsoftnak a kiépítési API-kon keresztül. Ön az a.b.c.d+1 címet fogja használni az elsődleges PE VRF IP-címeként, a Microsoft pedig az a.b.c.d+2 címet használja az elsődleges MSEE VRF IP-címeként. Ön az a.b.c.d+5 címet fogja használni a másodlagos PE VRF IP-címeként, a Microsoft pedig az a.b.c.d+6 címet használja a másodlagos MSEE VRF IP-címeként.

Vegyük azt az esetet, amelyben a 192.168.100.128/29 címet választja a privát társviszony-létesítés beállításához. Az 192.168.100.128/29 a 192.168.100.128–192.168.100.135 tartományból tartalmaz címeket, amelyek közül:

* az 192.168.100.128/30 az 1. kapcsolathoz lesz hozzárendelve, amelyből a szolgáltató a 192.168.100.129 címet, a Microsoft pedig a 192.168.100.130 címet használja.
* az 192.168.100.132/30 a 2. kapcsolathoz lesz hozzárendelve, amelyből a szolgáltató a 192.168.100.133 címet, a Microsoft pedig a 192.168.100.134 címet használja.

<a id="ip-addresses-used-for-azure-public-and-microsoft-peering" class="xliff"></a>

### Nyilvános Azure- és Microsoft-társviszony-létesítéshez használt IP-címek

A BGP-munkamenetek beállításához az Ön birtokában lévő nyilvános IP-címeket kell használnia. A Microsoftnak képesnek kell lennie ellenőrizni az IP-címek tulajdonjogát egy regionális útválasztási internetes jegyzékben vagy egy internetes útválasztási jegyzékben. 

* ExpressRoute-kapcsolatcsoportonként minden társviszonyhoz egy egyedi /29 alhálózatot vagy két /30 alhálózatot kell használnia a BGP társviszony-létesítés beállításához (ha többel rendelkezik). 
* Ha egy /29 alhálózatot használ, az két /30 alhálózatra lesz felosztva. 
  * Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz fogja használni a rendszer.
  * Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft fogja használni egy BGP-munkamenet beállításához.
  * Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.

<a id="public-ip-address-requirement" class="xliff"></a>

## Nyilvános IP-cím-követelmények

<a id="private-peering" class="xliff"></a>

### Magánhálózati társviszony-létesítés

Eldöntheti, hogy nyilvános vagy magánhálózati IPv4-címeket szeretne-e használni a magánhálózati társviszony-létesítéshez. Mi biztosítjuk a forgalom végpontok közötti elkülönítését, így elkerülhető, hogy a címek átfedésben legyenek más ügyfelekkel magánhálózati társviszony-létesítés esetén. Ezek a címek nincsenek meghirdetve az interneten. 

<a id="public-peering" class="xliff"></a>

### Nyilvános társviszony-létesítés

Az Azure nyilvános társviszony-létesítési útvonal használatával az Azure-ban üzemeltetett összes szolgáltatáshoz csatlakozhat a nyilvános IP-címeiken keresztül. Ezen szolgáltatások közé tartoznak az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) című dokumentumban felsorolt szolgáltatások, valamint az ISV-k által a Microsoft Azure-ban üzemeltetett szolgáltatások. A Microsoft Azure-szolgáltatásokhoz a nyilvános társviszony-létesítéssel létrehozott kapcsolatokat mindig az Ön hálózata kezdeményezi a Microsoft hálózata felé. A Microsoft hálózatához tervezett forgalomhoz nyilvános IP-címeket kell használnia.

<a id="microsoft-peering" class="xliff"></a>

### Microsoft-társviszony-létesítés

A Microsoft társviszony-létesítési útvonal lehetővé teszi, hogy az Azure nyilvános társviszony-létesítési útvonalon keresztül nem támogatott Microsoft-felhőszolgáltatásokhoz csatlakozzon. A szolgáltatások listájába beletartoznak az Office 365 szolgáltatásai, például az Exchange Online, a SharePoint Online, a Skype Vállalati verzió és a Dynamics 365. A Microsoft támogatja a kétirányú kapcsolatokat a Microsoft-társviszony-létesítésen keresztül. A Microsoft-felhőszolgáltatások felé irányuló forgalomnak érvényes nyilvános IPv4-címeket kell használnia, mielőtt belép a Microsoft hálózatába.

Győződjön meg róla, hogy az IP-címek és AS-számok regisztrálva vannak az alábbi nyilvántartások egyikében.

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

> [!IMPORTANT]
> A Microsoft számára ExpressRoute-on hirdetett nyilvános IP-címek nem hirdethetők meg az interneten. Ez megszakíthatja a más Microsoft-szolgáltatások kapcsolatait. Azonban azok a nyilvános IP-címek, amelyeket a hálózatban található kiszolgálók használnak, amelyek O365-végpontokkal kommunikálnak a Microsofton belül, meg lehetnek hirdetve az ExpressRoute-on. 
> 
> 

<a id="dynamic-route-exchange" class="xliff"></a>

## Dinamikus útvonalcsere

Az útválasztás cseréje az eBGP protokollon keresztül történik. Az EBGP-munkamenetek az MSEE-k és az Ön útválasztója között jönnek létre. A BGP-munkamenetek hitelesítése nem szükséges. Szükség esetén konfigurálható egy MD5-kivonat. A BGP-munkamenetek konfigurálásával kapcsolatban lásd az [útválasztás konfigurálását](expressroute-howto-routing-classic.md) és a [kapcsolatcsoport-kiépítési munkafolyamatokat és a kapcsolatcsoportok állapotait](expressroute-workflows.md) ismertető témaköröket.

<a id="autonomous-system-numbers" class="xliff"></a>

## Autonóm rendszerek számai

A Microsoft az AS 12076 számot használja az Azure nyilvános, az Azure privát és a Microsoft társviszony-létesítéshez. Az 65515–65520 AS-számok belső használatra vannak fenntartva. A 16 és a 32 bites AS-számok is támogatottak.

Az adatátvitel szimmetriájára nem vonatkoznak követelmények. Az előre és visszafelé haladó útvonalak különböző útválasztópárokon haladhatnak keresztül. Az azonos útvonalakat az Önhöz tartozó kapcsolatcsoport-párokon mindkét oldalról meg kell hirdetni. Az útvonalmetrikáknak nem kell megegyezniük.

<a id="route-aggregation-and-prefix-limits" class="xliff"></a>

## Útvonal-összevonások és előtagkorlátozások

Legfeljebb 4000 előtag számunkra történő meghirdetését támogatjuk az Azure privát társviszony-létesítésen keresztül. Ez legfeljebb 10 000 előtagra növelhető, ha az ExpressRoute prémium bővítmény engedélyezve van. BGP-munkamenetenként legfeljebb 200 előtagot fogadunk el az Azure nyilvános és a Microsoft társviszony-létesítés esetében. 

A BGP-munkamenetek el lesznek dobva, ha az előtagok száma meghaladja a korlátot. Csak a privát társviszony-létesítési kapcsolaton fogadjuk el az alapértelmezett útvonalakat. A szolgáltatónak ki kell szűrnie az alapértelmezett útvonalakat és a privát IP-címeket (RFC 1918) az Azure nyilvános és a Microsoft társviszony-létesítési útvonalakból. 

<a id="transit-routing-and-cross-region-routing" class="xliff"></a>

## Tranzit útválasztás és régiók közötti útválasztás

Az ExpressRoute nem konfigurálható tranzit útválasztóként. A tranzit útválasztási szolgáltatások tekintetében a kapcsolatszolgáltatójára kell támaszkodnia.

<a id="advertising-default-routes" class="xliff"></a>

## Alapértelmezett útvonalak meghirdetése

Az alapértelmezett útvonalak használata csak az Azure privát társviszony-létesítési munkamenetek esetében engedélyezett. Ilyen esetben a társított virtuális hálózatból származó minden forgalmat az Ön hálózatára irányítunk át. Az alapértelmezett útvonalak privát társviszony-létesítésbe történő meghirdetése az Azure-ból származó internetes útvonalak blokkolását eredményezi. Az internetről származó és az arra irányuló forgalom átirányításához az Azure-ban üzemeltetett szolgáltatások esetén a vállalati peremhálózatra kell támaszkodnia. 

 A más Azure-szolgáltatások és infrastruktúra-szolgáltatások csatlakozásának engedélyezéséhez biztosítania kell az alábbi elemek egyikének meglétét.

* Az Azure nyilvános társviszony-létesítés engedélyezve van a forgalom nyilvános végpontokra való átirányításához
* Felhasználó által meghatározott útválasztást használ az internetkapcsolatot igénylő összes alhálózat internetkapcsolatának engedélyezéséhez.

> [!NOTE]
> Az alapértelmezett útvonalak meghirdetése megszakítja a Windows- és az egyéb virtuálisgép-licencek aktiválását. Megkerülő megoldás alkalmazásához kövesse az [itt](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) található utasításokat.
> 
> 

<a id="support-for-bgp-communities-preview" class="xliff"></a>

## BGP-közösségek támogatása (előzetes kiadás)

Ez a szakasz áttekinti, hogyan használhatók a BGP-közösségek az ExpressRoute-tal. A Microsoft a megfelelő közösségértékekkel címkézett útvonalakkal hirdeti meg az útvonalakat a nyilvános és a Microsoft társviszony-létesítésekben. Ennek az eljárásnak az indoklását és a közösségértékek részleteit alább olvashatja. A Microsoft azonban nem fogadja el a Microsoft számára meghirdetett útvonalakhoz rendelt közösségértékeket.

Ha az ExpressRoute-on keresztül csatlakozik a Microsofthoz egy geopolitikai régión belül lévő bármelyik társviszony-létesítési helyszínen, az összes Microsoft-felhőszolgáltatáshoz hozzáférést kap a geopolitikai határon belüli összes régióban. 

Ha például Amsterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, az Észak-Európában és Nyugat-Európában üzemeltetett összes Microsoft-felhőszolgáltatáshoz hozzáféréssel fog rendelkezni. 

A geopolitikai régiók, a hozzájuk rendelt Azure-régiók és a megfelelő ExpressRoute társviszony-létesítési helyszínek részletes listáját az [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md) oldalon találja.

Geopolitikai régiónként több ExpressRoute-kapcsolatcsoportot is vásárolhat. Több kapcsolattal jelentős előnyöket szerezhet a magas rendelkezésre állás és a földrajzi alapú redundancia területén. Ha több ExpressRoute-kapcsolatcsoporttal rendelkezik, a Microsoft ugyanazt az előtagkészletet hirdeti meg a nyilvános társviszony-létesítési és a Microsoft társviszony-létesítési útvonalakon. Ez azt jelenti, hogy a hálózatából több útvonal fog irányulni a Microsoft felé. Emiatt előfordulhat, hogy a hálózaton belüli útvonalválasztási döntések nem lesznek optimálisak. Ez az optimálisnál rosszabb csatlakozási élményt okozhat a különböző szolgáltatások esetében. 

A Microsoft a megfelelő BGP-közösség értékét tartalmazó címkével látja el a nyilvános társviszony-létesítésen és a Microsoft társviszony-létesítésen keresztül meghirdetett előtagokat, amelyek jelzik, hogy az előtagok melyik régióban vannak üzemeltetve. A közösségértékek alapján megfelelő útválasztási döntéseket hozhat, amelyekkel [optimális útválasztást kínálhat az ügyfeleknek](expressroute-optimize-routing.md).

| **Geopolitikai régió** | **Microsoft Azure-régió** | **BGP-közösségérték** |
| --- | --- | --- |
| **Észak-Amerika** | | |
| USA keleti régiója |12076:51004 | |
| USA 2. keleti régiója |12076:51005 | |
| USA nyugati régiója |12076:51006 | |
| USA nyugati régiója, 2. |12076:51026 | |
| USA nyugati középső régiója |12076:51027 | |
| USA északi középső régiója |12076:51007 | |
| USA déli középső régiója |12076:51008 | |
| USA középső régiója |12076:51009 | |
| Közép-Kanada |12076:51020 | |
| Kelet-Kanada |12076:51021 | |
| **Dél-Amerika** | | |
| Dél-Brazília |12076:51014 | |
| **Európa** | | |
| Észak-Európa |12076:51003 | |
| Nyugat-Európa |12076:51002 | |
| **Ázsia és a Csendes-óceáni térség** | | |
| Kelet-Ázsia |12076:51010 | |
| Délkelet-Ázsia |12076:51011 | |
| **Japán** | | |
| Kelet-Japán |12076:51012 | |
| Nyugat-Japán |12076:51013 | |
| **Ausztrália** | | |
| Kelet-Ausztrália |12076:51015 | |
| Délkelet-Ausztrália |12076:51016 | |
| **India** | | |
| Dél-India |12076:51019 | |
| Nyugat-India |12076:51018 | |
| Közép-India |12076:51017 | |

A Microsoft által hirdetett összes útvonal a megfelelő közösségértéket tartalmazó címkével lesz ellátva. 

> [!IMPORTANT]
> A globális előtagok egy megfelelő közösségértéket tartalmazó címkével rendelkeznek, és csak akkor lesznek meghirdetve, ha az ExpressRoute prémium bővítmény engedélyezve van.
> 
> 

A fentiek mellett a Microsoft a kapcsolódó szolgáltatások alapján is címkével látja el az előtagokat. Ez csak a Microsoft társviszony-létesítésre vonatkozik. Az alábbi táblázat a szolgáltatások BGP-közösségértékre való leképezéseit tartalmazza.

| **Szolgáltatás** | **BGP-közösségérték** |
| --- | --- |
| **Exchange** |12076:5010 |
| **SharePoint** |12076:5020 |
| **Skype Vállalati verzió** |12076:5030 |
| **Dynamics 365** |12076:5040 |
| **Egyéb Office 365-szolgáltatások** |12076:5100 |

> [!NOTE]
> A Microsoft nem fogadja el a Microsoft számára meghirdetett útvonalakon beállított BGP-közösségértékeket.
> 
> 

<a id="next-steps" class="xliff"></a>

## Következő lépések

* Az ExpressRoute-kapcsolat konfigurálása.
  
  * [ExpressRoute-kapcsolatcsoport létrehozása a klasszikus üzemi modellhez](expressroute-howto-circuit-classic.md) vagy [ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása az Azure Resource Managerrel](expressroute-howto-circuit-arm.md)
  * [Útválasztás konfigurálása a klasszikus üzemi modellhez](expressroute-howto-routing-classic.md) vagy [Útválasztás konfigurálása a Resource Manager-alapú üzemi modellhez](expressroute-howto-routing-arm.md)
  * [Klasszikus VNet összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-classic.md) vagy [Resource Manager VNet összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-arm.md)


