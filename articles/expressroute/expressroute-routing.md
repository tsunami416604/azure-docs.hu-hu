<properties
   pageTitle="Az ExpressRoute útválasztási követelményei | Microsoft Azure"
   description="Ez az oldal ExpressRoute-kapcsolatcsoportok útválasztási konfigurálásának és kezelésének részletes követelményeit ismerteti."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="rossort"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2016"
   ms.author="ganesr"/>



# Az ExpressRoute útválasztási követelményei  

Ahhoz, hogy az ExpressRoute-tal tudjon csatlakozni a Microsoft-felhőszolgáltatásokhoz, be kell állítania és kezelnie kell az útválasztást. Egyes kapcsolatszolgáltatók az útválasztás beállítását és kezelését felügyelt szolgáltatásként kínálják. Ellenőrizze kapcsolatszolgáltatójánál, hogy kínálja-e ezt a szolgáltatást. Ha nem, akkor meg kell felelnie az alább ismertetett követelményeknek. 

A kapcsolat elősegítésének érdekében beállítandó útválasztási munkamenetek leírását a [kapcsolatcsoportokat és útválasztási tartományokat](expressroute-circuit-peerings.md) ismertető cikkben találja.

**Megjegyzés:** A Microsoft nem támogat semmilyen útválasztó-redundancia protokollt (pl. HSRP, VRRP) a magas rendelkezésre állású konfigurációkhoz. A magas rendelkezésre állás érdekében csak a társviszonyonként meglévő BGP-munkamenetek redundáns párjára támaszkodunk.

## IP-címek társviszony-létesítésekhez

Fenn kell tartania néhány IP-címblokkot az Ön hálózata és a Microsoft vállalati peremhálózati (MSEE) útválasztói közötti útválasztás konfigurálásához. Ez a szakasz a követelmények listáját tartalmazza, és ismerteti ezen IP-címek beszerzésére és használatára vonatkozó szabályokat.

### IP-címek Azure privát társviszony-létesítéshez

A társviszony-létesítések konfigurálásához privát IP-címeket vagy nyilvános IP-címeket használhat. Az útvonalak konfigurálásához használt címtartományok nem lehetnek átfedésben a virtuális hálózatok az Azure-ban való létrehozásához használt címtartományokkal. 

 - Egy /29 vagy két /30 alhálózatot le kell foglalnia az útválasztási felületek számára.
 - Az útválasztáshoz használt alhálózatok privát IP-címeket vagy nyilvános IP-címeket használhatnak.
 - Az alhálózatok nem ütközhetnek az ügyfél által a Microsoft Cloud használatához lefoglalt tartománnyal.
 - Ha egy /29 alhálózatot használ, az két /30 alhálózatra lesz felosztva. 
     - Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz fogja használni a rendszer.
     - Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft fogja használni egy BGP-munkamenet beállításához.
     - Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.  

#### Példa a privát társviszony-létesítésre

Ha az a.b.c.d/29 alhálózatot választja a társviszony-létesítés beállítására, az két /30 alhálózatra lesz felosztva. Az alábbi példában áttekintjük, hogyan használható az a.b.c.d/29 alhálózat. 

Az a.b.c.d/29 alhálózat fel lesz osztva az a.b.c.d/30 és az a.b.c.d+4/30 alhálózatra, és továbbítva lesz a Microsoftnak a kiépítési API-kon keresztül. Ön az a.b.c.d+1 címet fogja használni az elsődleges PE VRF IP-címeként, a Microsoft pedig az a.b.c.d+2 címet használja az elsődleges MSEE VRF IP-címeként. Ön az a.b.c.d+5 címet fogja használni a másodlagos PE VRF IP-címeként, a Microsoft pedig az a.b.c.d+6 címet használja a másodlagos MSEE VRF IP-címeként.

Vegyük azt az esetet, amelyben a 192.168.100.128/29 címet választja a privát társviszony-létesítés beállításához. Az 192.168.100.128/29 a 192.168.100.128–192.168.100.135 tartományból tartalmaz címeket, amelyek közül:

- az 192.168.100.128/30 az 1. kapcsolathoz lesz hozzárendelve, amelyből a szolgáltató a 192.168.100.129 címet, a Microsoft pedig a 192.168.100.130 címet használja.
- az 192.168.100.132/30 a 2. kapcsolathoz lesz hozzárendelve, amelyből a szolgáltató a 192.168.100.133 címet, a Microsoft pedig a 192.168.100.134 címet használja.

### IP-címek Azure nyilvános és Microsoft társviszony-létesítéshez

A BGP-munkamenetek beállításához az Ön birtokában lévő nyilvános IP-címeket kell használnia. A Microsoftnak képesnek kell lennie ellenőrizni az IP-címek tulajdonjogát egy regionális útválasztási internetes jegyzékben vagy egy internetes útválasztási jegyzékben. 

- ExpressRoute-kapcsolatcsoportonként minden társviszonyhoz egy egyedi /29 alhálózatot vagy két /30 alhálózatot kell használnia a BGP társviszony-létesítés beállításához (ha többel rendelkezik). 
- Ha egy /29 alhálózatot használ, az két /30 alhálózatra lesz felosztva. 
    - Az első /30 alhálózatot az elsődleges kapcsolathoz, a második /30 alhálózatot pedig a másodlagos kapcsolathoz fogja használni a rendszer.
    - Mindkét /30 alhálózathoz a /30 alhálózat IP-címét kell használnia az útválasztón. A /30 alhálózat második IP-címét a Microsoft fogja használni egy BGP-munkamenet beállításához.
    - Mindkét BGP-munkamenetet a [rendelkezésre állási SLA szerint](https://azure.microsoft.com/support/legal/sla/) kell beállítania, hogy érvényesek legyenek.

Győződjön meg róla, hogy az IP-címek és AS-számok regisztrálva vannak az alábbi nyilvántartások egyikében.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)


## Dinamikus útvonalcsere

Az útválasztás cseréje az eBGP protokollon keresztül történik. Az EBGP-munkamenetek az MSEE-k és az Ön útválasztója között jönnek létre. A BGP-munkamenetek hitelesítése nem szükséges. Szükség esetén konfigurálható egy MD5-kivonat. A BGP-munkamenetek konfigurálásával kapcsolatban lásd az [útválasztás konfigurálását](expressroute-howto-routing-classic.md) és a [kapcsolatcsoport-kiépítési munkafolyamatokat és a kapcsolatcsoportok állapotait](expressroute-workflows.md) ismertető témaköröket.

## Autonóm rendszerek számai

A Microsoft az AS 12076 számot használja az Azure nyilvános, az Azure privát és a Microsoft társviszony-létesítéshez. Az 65515–65520 AS-számok belső használatra vannak fenntartva. A 16 és a 32 bites AS-számok is támogatottak. A társoldalon (ügyfél vagy szolgáltató) az AS lehet nyilvános ASN (ha igazolható, hogy Ön a tulajdonosa), illetve személyes ASN-szám.

Az adatátvitel szimmetriájára nem vonatkoznak követelmények az adott kapcsolatcsoportok elsődleges és másodlagos elérési útjain. Az előre és visszafelé haladó útvonalak különböző útválasztópárokon haladhatnak keresztül. Az azonos útvonalakat az Önhöz tartozó kapcsolatcsoport-párok elsődleges vagy másodlagos oldaláról is meg kell hirdetni. Az útvonalmetrikáknak nem kell megegyezniük.

## Útvonal-összevonások és előtagkorlátozások

Legfeljebb 4000 előtag számunkra történő meghirdetését támogatjuk az Azure privát társviszony-létesítésen keresztül. Ez legfeljebb 10 000 előtagra növelhető, ha az ExpressRoute prémium bővítmény engedélyezve van. BGP-munkamenetenként legfeljebb 200 előtagot fogadunk el az Azure nyilvános és a Microsoft társviszony-létesítés esetében. 

A BGP-munkamenetek el lesznek dobva, ha az előtagok száma meghaladja a korlátot. Csak a privát társviszony-létesítési kapcsolaton fogadjuk el az alapértelmezett útvonalakat. A szolgáltatónak vagy az ügyfélnek ki kell szűrnie az alapértelmezett útvonalakat és a privát IP-címeket (RFC 1918) a nyilvános Azure- és a Microsoft társviszony-létesítési útvonalakon található BGP-hirdetésekből. 

## Tranzit útválasztás és régiók közötti útválasztás

Az ExpressRoute nem konfigurálható tranzit útválasztóként. A tranzit útválasztási szolgáltatások tekintetében a kapcsolatszolgáltatójára kell támaszkodnia.

## Alapértelmezett útvonalak meghirdetése

Az alapértelmezett útvonalak használata csak az Azure privát társviszony-létesítési munkamenetek esetében engedélyezett. Ilyen esetben a társított virtuális hálózatból származó minden forgalmat az Ön hálózatára irányítunk át. Az alapértelmezett útvonalak privát társviszony-létesítésbe történő meghirdetése az Azure-ból származó internetes útvonalak blokkolását eredményezi. Az internetről származó és az arra irányuló forgalom átirányításához az Azure-ban üzemeltetett szolgáltatások esetén a vállalati peremhálózatra kell támaszkodnia. 

 A más Azure-szolgáltatások és infrastruktúra-szolgáltatások csatlakozásának engedélyezéséhez biztosítania kell az alábbi elemek egyikének meglétét.

 - Az Azure nyilvános társviszony-létesítés engedélyezve van a forgalom nyilvános végpontokra való átirányításához
 - Felhasználó által meghatározott útválasztást használ az internetkapcsolatot igénylő összes alhálózat internetkapcsolatának engedélyezéséhez.

**Megjegyzés:** Az alapértelmezett útvonalak meghirdetése megszakítja a Windows- és az egyéb virtuálisgép-licencek aktiválását. Megkerülő megoldás alkalmazásához kövesse az [itt](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) található utasításokat.

## BGP-közösségek támogatása (előzetes kiadás)


Ez a szakasz áttekinti, hogyan használhatók a BGP-közösségek az ExpressRoute-tal. A Microsoft a megfelelő közösségértékekkel címkézett útvonalakkal hirdeti meg az útvonalakat a nyilvános és a Microsoft társviszony-létesítésekben. Ennek az eljárásnak az indoklását és a közösségértékek részleteit alább olvashatja. A Microsoft azonban nem fogadja el a Microsoft számára meghirdetett útvonalakhoz rendelt közösségértékeket.

Ha az ExpressRoute-on keresztül csatlakozik a Microsofthoz egy geopolitikai régión belül lévő bármelyik társviszony-létesítési helyszínen, az összes Microsoft-felhőszolgáltatáshoz hozzáférést kap a geopolitikai határon belüli összes régióban. 

Ha például Amsterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, az Észak-Európában és Nyugat-Európában üzemeltetett összes Microsoft-felhőszolgáltatáshoz hozzáféréssel fog rendelkezni. 

A geopolitikai régiók, a hozzájuk rendelt Azure-régiók és a megfelelő ExpressRoute társviszony-létesítési helyszínek részletes listáját az [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md) oldalon találja.

Geopolitikai régiónként több ExpressRoute-kapcsolatcsoportot is vásárolhat. Több kapcsolattal jelentős előnyöket szerezhet a magas rendelkezésre állás és a földrajzi alapú redundancia területén. Ha több ExpressRoute-kapcsolatcsoporttal rendelkezik, a Microsoft ugyanazt az előtagkészletet hirdeti meg a nyilvános társviszony-létesítési és a Microsoft társviszony-létesítési útvonalakon. Ez azt jelenti, hogy a hálózatából több útvonal fog irányulni a Microsoft felé. Emiatt előfordulhat, hogy a hálózaton belüli útvonalválasztási döntések nem lesznek optimálisak. Ez az optimálisnál rosszabb csatlakozási élményt okozhat a különböző szolgáltatások esetében. 

A Microsoft a megfelelő BGP-közösség értékét tartalmazó címkével látja el a nyilvános társviszony-létesítésen és a Microsoft társviszony-létesítésen keresztül meghirdetett előtagokat, amelyek jelzik, hogy az előtagok melyik régióban vannak üzemeltetve. A közösségértékek alapján megfelelő útválasztási döntéseket hozhat, amelyekkel [optimális útválasztást kínálhat az ügyfeleknek](expressroute-optimize-routing.md).

| **Geopolitikai régió** | **Microsoft Azure-régió** | **BGP-közösségérték** |
|---|---|---|
| **Észak-Amerika** |    |  |
|    | USA keleti régiója | 12076:51004 |
|    | USA 2. keleti régiója | 12076:51005 |
|    | USA nyugati régiója | 12076:51006 |
|    | USA nyugati régiója, 2. | 12076:51026 |
|    | USA nyugati középső régiója | 12076:51027 |
|    | USA északi középső régiója | 12076:51007 |
|    | USA déli középső régiója | 12076:51008 |
|    | USA középső régiója | 12076:51009 |
|    | Közép-Kanada | 12076:51020 |
|    | Kelet-Kanada | 12076:51021 |
| **Dél-Amerika** |  |  |
|    | Dél-Brazília | 12076:51014 |
| **Európa** |    |  |
|    | Észak-Európa | 12076:51003 |
|    | Nyugat-Európa | 12076:51002 |
| **Ázsia és a Csendes-óceáni térség** |    |   |
|    | Kelet-Ázsia | 12076:51010 |
|    | Délkelet-Ázsia | 12076:51011 |
| **Japán** |     |   |
|    | Kelet-Japán | 12076:51012 |
|    | Nyugat-Japán | 12076:51013 |
| **Ausztrália** |    |   | 
|    | Kelet-Ausztrália | 12076:51015 |
|    | Délkelet-Ausztrália | 12076:51016 |
| **India** |    |   |
|    | Dél-India | 12076:51019 |
|    | Nyugat-India | 12076:51018 |
|    | Közép-India | 12076:51017 |

A Microsoft által hirdetett összes útvonal a megfelelő közösségértéket tartalmazó címkével lesz ellátva. 

>[AZURE.IMPORTANT] A globális előtagok egy megfelelő közösségértéket tartalmazó címkével rendelkeznek, és csak akkor lesznek meghirdetve, ha az ExpressRoute prémium bővítmény engedélyezve van.


A fentiek mellett a Microsoft a kapcsolódó szolgáltatások alapján is címkével látja el az előtagokat. Ez csak a Microsoft társviszony-létesítésre vonatkozik. Az alábbi táblázat a szolgáltatások BGP-közösségértékre való leképezéseit tartalmazza.

| **Szolgáltatás** | **BGP-közösségérték** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype Vállalati verzió** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Egyéb Office 365-szolgáltatások** | 12076:5100 |

>[AZURE.NOTE] A Microsoft nem fogadja el a Microsoft számára meghirdetett útvonalakon beállított BGP-közösségértékeket.

## Következő lépések

- Az ExpressRoute-kapcsolat konfigurálása.

    - [ExpressRoute-kapcsolatcsoport létrehozása a klasszikus üzemi modellhez](expressroute-howto-circuit-classic.md) vagy [ExpressRoute-kapcsolatcsoport létrehozása vagy módosítása az Azure Resource Managerrel](expressroute-howto-circuit-arm.md)
    - [Útválasztás konfigurálása a klasszikus üzemi modellhez](expressroute-howto-routing-classic.md) vagy [Útválasztás konfigurálása a Resource Manager-alapú üzemi modellhez](expressroute-howto-routing-arm.md)
    - [Klasszikus VNet összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-classic.md) vagy [Resource Manager VNet összekapcsolása egy ExpressRoute-kapcsolatcsoporttal](expressroute-howto-linkvnet-arm.md)





<!--HONumber=Sep16_HO4-->


