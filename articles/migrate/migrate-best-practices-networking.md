---
title: Gyakorlati tanácsok a számítási feladatok migrálása az Azure-alapú hálózati beállítása |} A Microsoft Docs
description: Az Azure-bA az áttelepítés után kaphat gyakorlati tanácsok az áttelepített alkalmazások és szolgáltatások hálózatának beállítása.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: raynew
ms.openlocfilehash: 1493eb6978b00771aa8ed4d8cfc28c37a9dde5b6
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139744"
---
# <a name="best-practices-to-set-up-networking-for-workloads-migrated-to-azure"></a>Ajánlott eljárásokat, és állítsa be a hálózati, számítási feladatok migrálása az Azure-bA

A tervezés és az áttelepítés mellett magát, az áttelepítés tervezése kritikus fontosságú lépés egyik megtervezését és megvalósítását az Azure-hálózatok. Ez a cikk ismerteti a hálózati ajánlott eljárások IaaS és PaaS implementációk az Azure-ba való migrálás során.

> [!IMPORTANT]
> Az ajánlott eljárásokról és a jelen cikkben ismertetett vélemények az Azure platformon alapul, és a szolgáltatás írásának időpontjában elérhető szolgáltatások. Funkciók és képességek idővel változni. Lehet, hogy nem minden javaslat alkalmazható a telepítéshez, ezért válassza, amelyek az Ön számára.


## <a name="design-virtual-networks"></a>Virtuális hálózatok megtervezése

Az Azure virtuális hálózatok (Vnetek) kínál:
- Azure-erőforrások kommunikálni privát módon, közvetlenül és biztonságosan egymás feletti virtuális hálózatok.
- A virtuális hálózatok végpont kapcsolatok konfigurálhatja a virtuális gépek és szolgáltatások, amelyek az internetes kommunikációt is igényelnek.
- Virtuális hálózat egy logikai elkülönítés az Azure felhő, amely az előfizetés van kijelölve.
- Több virtuális hálózaton belül minden egyes Azure-előfizetés és az Azure-régióban is alkalmazható.
- Minden egyes virtuális hálózat el különítve a többi virtuális hálózatok.
- Virtuális hálózatok privát és nyilvános IP-címek meghatározott tartalmazhat [RFC 1918](https://tools.ietf.org/html/rfc1918)kifejezve a CIDR-jelölésrendszerben. Nyilvános IP-címek nem érhetők el közvetlenül az internetről.
- Virtuális hálózatok egymáshoz virtuális hálózatok közötti társviszony használatával csatlakozhat. Kapcsolódó virtuális hálózatok lehetnek azonos vagy eltérő régiókban. Így egy virtuális hálózatban található erőforrások erőforrások más virtuális hálózatokhoz csatlakozhat.
- Azure irányítja a forgalmat a Vneten belül az alhálózatok közötti alapértelmezés szerint a virtuális hálózatok, a helyszíni hálózatok és az internethez csatlakoztatva.


Létezik néhány dolgot kell foglalkoznia, ha a virtuális hálózatok közötti topológia, beleértve az IP-cím elrendezése tervezési tárolóhelyek, hogyan valósíthat meg egy küllős hálózati szegmentálhatja a virtuális hálózatok alhálózatokra, állítsa be a DNS és az Azure rendelkezésre állási zónák megvalósítása.

## <a name="best-practice-plan-ip-addressing"></a>Ajánlott eljárás: megtervezése IP-címkezelés

Ha az áttelepítés részeként létrehozott virtuális hálózatok, fontos tervezze meg a virtuális hálózat IP-címtér.

- Egy címtér, amely nem nagyobb, mint egy, az egyes virtuális hálózatok /16 CIDR-tartományt kell hozzárendelni. Virtuális hálózatok 65536 IP-címek használatát és a egy kisebb, mint/16-előtagot rendel eredményez IP-címek elvesztését. Fontos nem pazarlom az IP-címek, még akkor is, ha az RFC 1918 által meghatározott privát tartományokat.
- A virtuális hálózat címtere ne átfedésben a helyszíni hálózati tartományok.
- Hálózati címfordítás (NAT) nem használható.
- Egymást átfedő címek okozhat, hálózatok, amelyek nem lehet csatlakozni, és útválasztási, amely nem működik megfelelően. Ha a hálózatok átfedésben vannak, újra kell terveznie a hálózaton, vagy hálózati címfordítás (NAT) használjon szüksége.

**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) az Azure virtuális hálózatok.
- [Olvasási](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq) a hálózat – gyakori kérdések.
- [Ismerje meg](https://docs.microsoft.com/azure/azure-subscription-service-limits?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) hálózati korlátozások.


## <a name="best-practice-implement-a-hub-spoke-network-topology"></a>Ajánlott eljárás: egy küllős hálózati topológia implementálása

Egy küllős hálózati topológia elkülöníti a számítási feladatok során például az identitás- és biztonsági szolgáltatások megosztása.
- A hub egy Azure virtuális hálózat, amely központi kapcsolódási pontként viselkedik.
- A küllők is csatlakozhat az agyi virtuális hálózat virtuális hálózatok közötti társviszony útján.
- A közös szolgáltatások üzembe helyezése az agyon történik, az egyes számítási feladatokat a küllőkön vannak. 

A következőket ajánljuk figyelmébe:
- Egy küllős topológia implementálása az Azure-ban központosítja közös szolgáltatásokat, például a helyszíni hálózatok, a tűzfalak és virtuális hálózatok közötti elkülönítés kapcsolatokat. Az agyi virtuális hálózat biztosít a kapcsolatot a helyszíni hálózatok és a hely, a gazdagép-szolgáltatások használatát a küllő virtuális hálózatok lévő üzemeltetett számítási feladatokat.
- Egy küllős konfigurációs általában a nagyobb cégek használják. Kisebb hálózatok érdemes megfontolni egy egyszerűbb tervezési menteni a költségeket és összetettséget.
- Számítási feladatok elkülönítése más küllőktől elkülönülten kezelhetőek külön-külön kezelt minden egyes küllőben a küllő virtuális hálózatok használható. Minden számítási feladat több szintet, és a több alhálózattal rendelkező Azure load Balancer terheléselosztók csatlakozó is tartalmazhat.
- Küllős topológiájú virtuális hálózatok implementálható különböző erőforráscsoportokban, és akár különböző előfizetésben található. Különböző előfizetésekben található virtuális hálózatok társviszonyba állítása akkor, amikor az előfizetések társíthatók az azonos vagy eltérő, az Azure Active Directory (AD) bérlő. Ez lehetővé teszi minden számítási feladat decentralizált felügyeletét során a szolgáltatások megosztása változatlan a központi hálózathoz.

![Változáskezelés](./media/migrate-best-practices-networking/hub-spoke.png)
*küllős topológia*

**tudj meg többet:**

- [További információ](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) egy küllős topológiában.
- A futó Azure hálózati javaslatok [Windows](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/windows-vm#network-recommendations) és [Linux](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/linux-vm#network-recommendations) virtuális gépeket.
- [Ismerje meg](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuális hálózatok közötti társviszony-létesítés.


## <a name="best-practice-design-subnets"></a>Ajánlott eljárás: alhálózatok tervezése

Elkülönítéshez egy virtuális hálózaton, szegmentálása, egy vagy több alhálózatra, és lefoglalni a VNet-címterek minden alhálózat egy része.
- Minden egyes virtuális hálózaton belüli több alhálózaton is létrehozhat.
- Alapértelmezés szerint az Azure közötti hálózati forgalom egy virtuális hálózaton lévő összes alhálózathoz.
- Az alhálózat döntéseket hozhat a technikai és szervezeti követelmények alapulnak.  
- Létrehozhat alhálózatokat a CIDR-jelölésrendszerben.
- Amikor meghatározza az alhálózatok hálózati címtartományt, fontos vegye figyelembe, hogy az Azure megőrzi minden alhálózat nem használható öt IP-címeket. Például ha hoz létre a legkisebb alhálózat/29 méretű (nyolc IP-címekkel) rendelkező, Azure megőrzi öt címet, így csak az alhálózat-gazdagépekhez rendelt három felhasználható cím.
- A legtöbb esetben a legkisebb alhálózat/28-as használata ajánlott.

### <a name="example"></a>Példa

A táblázat egy virtuális hálózat egy példa az alhálózatra, tervezett áttelepítés szegmentált 10.245.16.0/20-címterét.

**Alhálózat** | **CIDR** | **Címek** | **Használat**
--- | --- | --- | ---
DEV-FE-EUS2 | 10.245.16.0/22 | 1019 | Előtér/webes szintű virtuális gépek
DEV-ALKALMAZÁS-EUS2 | 10.245.20.0/22 | 1019 | Alkalmazás szintű virtuális gépek
DEV-DB-EUS2 | 10.245.24.0/23 | 507 | Adatbázis-beli virtuális gépek

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#segmentation) alhálózatok tervezése.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) egy fiktív vállalat (Contoso) a hálózati infrastruktúra előkészítve a migrálásra.


## <a name="best-practice-set-up-a-dns-server"></a>Ajánlott eljárás: egy DNS-kiszolgáló beállítása

Azure ad hozzá egy DNS-kiszolgáló alapértelmezés szerint, ha telepít egy virtuális hálózathoz. Ez lehetővé teszi, hogy gyorsan hozhat létre virtuális hálózatok és az erőforrások üzembe helyezése. Azonban a DNS-kiszolgáló csak szolgáltatást biztosítja az erőforrásokat, hogy a virtuális hálózaton. Ha azt szeretné, több virtuális hálózatok egymáshoz kapcsolásához, vagy egy helyszíni kiszolgálóhoz csatlakozni a virtuális hálózatok, szüksége további megoldási képességek. Ha például szüksége lehet az Active Directory DNS-nevek a virtuális hálózatok között. Ehhez az Azure-ban a saját egyéni DNS-kiszolgáló üzembe helyezése.

- Egy virtuális hálózat DNS-kiszolgálók DNS-lekérdezések is továbbítja az Azure-ban a rekurzív feloldók. Ez lehetővé teszi a gazdagépnevekhez adott virtuális hálózaton belül. Például egy Azure-ban futó tartományvezérlő is a saját tartományok DNS-lekérdezései válaszolni, és minden más lekérdezés továbbítása az Azure-bA.
- DNS-továbbítást lehetővé teszi, hogy a virtuális gép, mind a helyszíni erőforrásokhoz (keresztül a tartományvezérlő) és az Azure által biztosított állomásnevek (a továbbító használatával). A rekurzív feloldók az Azure-ban való hozzáférés biztosítja a virtuális IP-címen 168.63.129.16.
- DNS-továbbítást is lehetővé teszi a DNS-feloldási virtuális hálózatok között, és lehetővé teszi, hogy a helyszíni gépek Azure által biztosított feloldásához.
    - Egy virtuális gép állomásnevét feloldani, a DNS-kiszolgáló virtuális gép ugyanazon virtuális hálózatban kell lennie, és úgy konfigurálni, hogy előre állomás neve lekérdezések az Azure-bA.
    - A DNS-utótagja eltér az egyes virtuális hálózatok, mert a feltételes továbbítás szabályok használatával DNS-lekérdezéseket küld a megfelelő virtuális hálózat a feloldásához.
- Ha a saját DNS-kiszolgálókat használ, az egyes virtuális hálózatok több DNS-kiszolgáló is megadhat. Megadhat több DNS-kiszolgálók száma hálózati adapterenként (az Azure Resource Manager) vagy egy (esetén a klasszikus üzemi modellhez) is.
- Egy hálózati adapter vagy a felhőben szolgáltatás számára megadott DNS-kiszolgálók elsőbbséget élveznek a megadott virtuális hálózat DNS-kiszolgálók.
- Az Azure Resource Manager-alapú üzemi modellben a virtuális hálózat és a egy hálózati adapter DNS-kiszolgálók is megadhat, de az ajánlott eljárás a beállítás csak a virtuális hálózatok használatát.

    ![DNS-kiszolgálók](./media/migrate-best-practices-networking/dns2.png) *virtuális hálózathoz tartozó DNS-kiszolgálók*

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/migrate/contoso-migration-infrastructure) névfeloldás saját DNS-kiszolgáló használata esetén.
- [Ismerje meg](https://docs.microsoft.com/en-us/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-subscriptions) DNS elnevezési szabályokat és korlátozásokat.


## <a name="best-practice-set-up-availability-zones"></a>Ajánlott eljárás: állítsa be a rendelkezésre állási zónák

A rendelkezésre állási zónák a magas rendelkezésre állású alkalmazások és adatok védelme az Adatközpont meghibásodása növelése.

- A rendelkezésre állási zónák egyedi fizikai helyszínek az Azure-régióban.
- Minden zóna egy vagy több adatközpont független áramellátással, hűtéssel és hálózati található tevődik össze.
- Rugalmasság biztosítása érdekében legalább három különálló zónát, az összes engedélyezett régióban van.
- Egy adott régión belül a rendelkezésre állási zónák fizikai elválasztása adatközpont meghibásodása ellen védi az alkalmazásokat és adatokat.
- Zónaredundáns szolgáltatásokat az alkalmazások és adatok replikálása megvédeni a hibaérzékeny pontokat a rendelkezésre állási zónák között. – A rendelkezésre állási zónákban az Azure virtuális gép 99,99 %-os szolgáltatásiszint-szerződésben garantált kínál.

    ![Rendelkezésre állási zónában](./media/migrate-best-practices-networking/availability-zone.png) *rendelkezésre állási zónában*

- Tervezhet és magas rendelkezésre állás beépítése az áttelepítési architektúra számítási, tárolási, hálózati és adatforrások közös elhelyezése a zónán belül, és replikálja őket a más zónákban. Azure-szolgáltatások rendelkezésre állási zónákat támogató két kategóriába sorolhatók:
    - A zónaszintű szolgáltatások: társítania egy erőforráshoz egy adott zónához. Példa virtuális gépek esetén a felügyelt lemezek, IP-címek).
    - Zónaredundáns szolgáltatások: az erőforrás automatikus több zónában replikálja. Ha például zónaredundáns tárolás, az Azure SQL Database.
- Telepíthet egy standard Azure elosztott terhelésű az internetkapcsolattal rendelkező számítási feladatok vagy alkalmazást, a zónaszintű hibatűrő képességének biztosítása érdekében.

    ![Terheléselosztó](./media/migrate-best-practices-networking/load-balancer.png) *Load balancer*

**tudj meg többet:**
-   [Áttekintés](https://docs.microsoft.com/azure/availability-zones/az-overview) a rendelkezésre állási zónák.



## <a name="design-hybrid-cloud-networking"></a>Tervezés a hibrid felhőalapú hálózatkezelés

A sikeres áttelepítéshez fontos helyszíni vállalati hálózat csatlakoztatása az Azure. Ez létrehoz egy folyamatosan elérhető kapcsolat, néven egy hibridfelhő-alapú hálózat, ahol szolgáltatások állnak rendelkezésre az Azure felhőalapú vállalati felhasználók számára. Az ilyen típusú hálózat létrehozása két lehetőség van:

- **Helyek közötti VPN:** egy helyek közötti kapcsolat közötti a kompatibilis a helyszíni VPN-eszköz és az üzembe helyezett Azure VPN gateway a virtuális hálózat létrehozása. Minden jogosult a helyszíni erőforráshoz hozzáférhet a virtuális hálózatok. Helyek közötti kommunikáció egy titkosított csatornán keresztül továbbítódnak az interneten keresztül. 
- **Az Azure ExpressRoute:** létrehozása az Azure ExpressRoute-kapcsolat a helyszíni hálózat és az Azure között egy ExpressRoute-partneren keresztül. Ez a kapcsolat nem nyilvános, és a forgalom nem halad az interneten keresztül.

**tudj meg többet:**

- [További](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/vpn) hibridfelhő-alapú hálózati kapcsolatban.

## <a name="best-practice-implement-a-highly-available-site-to-site-vpn"></a>Ajánlott eljárás: egy magas rendelkezésre állású site-to-site VPN megvalósítása

Site-to-site VPN implementálásához, állítsa be a VPN-átjáró, az Azure-ban.
- VPN-átjáró egy adott típusú virtuális hálózati átjáró, amely egy Azure virtuális hálózat és a egy helyszíni hely közötti titkosított adatforgalmat továbbíthat a nyilvános interneten keresztül szolgál.
- VPN-átjáró használatával titkosított adatforgalmat továbbíthat az Azure virtuális hálózatok, a Microsoft hálózatán keresztül között.
- Minden egyes virtuális hálózatok csak egy VPN-átjáróval rendelkezhetnek.
- Létrehozhat több kapcsolatot a VPN-átjáróhoz. Létrehozhat több kapcsolatot, ha az összes VPN-alagút ossza meg a rendelkezésre álló átjáró sávszélességet.
- Minden egyes Azure-alapú VPN-átjáró két példányból áll, amelyek aktív-készenléti konfigurációban vannak.
    - Tervezett karbantartás vagy az aktív példányra való unplaned megszakadása, a feladatátvételt hajt végre, és a készenléti példány automatikusan átveszi, és folytatja a site-to-site vagy VNet – VNet kapcsolat. 
    - A Váltás következtében a rövid megszakítás.
    - Tervezett karbantartás esetén a kapcsolat 10-15 másodpercen belül lehet visszaállítani.
    - Nem tervezett problémák esetén a kapcsolat helyreállítása hosszabb, körülbelül egy 1.5-ig perccel lesz a legrosszabb esetben.
    - Pont – hely (P2S) VPN-ügyfél kapcsolatok az átjárónak le lesz választva, és a felhasználók az ügyfélgépek csatlakozni kell.

A site-to-site VPN beállításakor, tegye a következőket:
 - Egy virtuális hálózathoz kell ki a címtartomány nem átfedésben a helyszíni hálózattal, amelyhez a VPN-t csatlakozni fognak.
 - Egy átjáró-alhálózatot hoz létre a hálózaton.
 - VPN-átjáró létrehozása, adja meg az átjáró típusa (VPN), és hogy az átjáró házirendalapú vagy útvonalalapú. Útvonalalapú VPN ajánlott több képes és jövőjét.
 - A helyi hálózati átjáró létrehozása a helyszínen, és a helyszíni VPN-eszköz konfigurálása. 
 - Létrehozhat egy feladatátvételi site-to-site VPN-kapcsolat a VNet-átjáró és a helyszíni eszköz között. Útvonalalapú VPN használatával lehetővé teszi, hogy aktív-passzív vagy aktív – aktív kapcsolatok az Azure-bA. Útválasztó-alapú is támogatja a site-to-site (bármely olyan számítógépről) és a pont – hely (egyetlen számítógépről) kapcsolatok egyidejűleg.
 - Azt adja meg az átjáró használni kívánt Termékváltozatot. Ez a terhelési követelményeknek, termékváltozatokat, funkciók és SLA-k függ.
 - Border gateway protocol (BGP) egy olyan opcionális szolgáltatás az Azure ExpressRoute és az útvonalalapú VPN-átjárók használatával a helyszíni BGP-útvonalakat a virtuális hálózatok való propagálása.

![VPN](./media/migrate-best-practices-networking/vpn.png)
*Site-to-site VPN*
 
**tudj meg többet:**

- [Felülvizsgálat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) kompatibilis a helyszíni VPN-eszközök.
- [Áttekintés](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateway) VPN-átjárók.
- [Ismerje meg](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable) magas rendelkezésre állású VPN-kapcsolatok.
- [Ismerje meg](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design) tervezésekor és kialakításakor a VPN-átjáró.
- [Felülvizsgálat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings#gwsku) a VPN gateway beállításairól.
- [Felülvizsgálat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) átjáró-termékváltozatokat használják.
- [További információ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview) BGP beállítása az Azure VPN-átjárókkal.


### <a name="best-practice-configure-a-gateway-for-vpn-gateways"></a>Ajánlott eljárás: átjáróinak konfigurálása a VPN-átjárók

Az Azure-beli VPN-átjáró létrehozásakor meg kell használnia egy GatewaySubnet nevű speciális alhálózatra. Amikor létrehozza a alhálózati Megjegyzés az ajánlott eljárások:

- Az átjáró-alhálózat előtagjának hossza a maximális előtag hossza 29 (például 10.119.255.248/29) lehet. Az aktuális ajánljuk, egy előtaghossz 27 (például 10.119.255.224/27) használatát.
- Az átjáró-alhálózat címtartománya a határozza meg, ha a virtuális hálózat címtere a legutóbbi részét használja.
- Az Azure-átjáró használata esetén nem telepíteni olyan virtuális gépek vagy más eszközök, például az átjáró-alhálózatot az Application Gateway.
- Ez az alhálózat egy hálózati biztonsági csoport (NSG) sem rendelhet. Az átjáró működik-e nem fog okozni.

**tudj meg többet:**
- [Ezzel az eszközzel](https://gallery.technet.microsoft.com/scriptcenter/Address-prefix-calculator-a94b6eed) meghatározni az IP-címterületének.

## <a name="best-practice-implement-azure-virtual-wan-for-branch-offices"></a>Ajánlott eljárás: megvalósítása az Azure virtuális WAN fiókirodák számára

Több VPN-kapcsolatok az Azure virtuális WAN egy olyan hálózati szolgáltatás, amely optimalizált és automatizált ág ágba irányuló kapcsolatot az Azure-on keresztül biztosítja.
- A Virtual WAN segítségével ágeszközöket csatlakoztathat és konfigurálhat az Azure-ral való kommunikációra. Ez elvégezhető manuálisan, vagy egy virtuális WAN-partneren keresztül előnyben részesített szolgáltató eszközök használatával.
- Előnyben részesített szolgáltató eszközök használata lehetővé teszi az egyszerű használat, kapcsolat, és a konfigurációkezelés.
- Az Azure-WAN beépített Irányítópult segítségével azonnal képet kaphat hibaelhárítási időt, és nyomon követéséhez a nagy méretű hely – hely kapcsolat egyszerű módot biztosítanak. 

**További információ:**
[ismertetése](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about) Azure virtuális WAN.

### <a name="best-practice-implement-expressroute-for-mission-critical-connections"></a>Ajánlott eljárás: a működés szempontjából kritikus fontosságú kapcsolatokhoz megvalósítása az ExpressRoute

Az Azure ExpressRoute szolgáltatás használatával a helyszíni infrastruktúrát kiterjesztheti a Microsoft cloud Az Azure-beli virtuális adatközpont közötti privát kapcsolatok létrehozásával és a helyszíni hálózatokkal.
- Az ExpressRoute-kapcsolatok lehetnek egy – bármely (IP VPN) hálózat, pontok közötti Ethernet-hálózaton keresztül, vagy egy kapcsolatszolgáltatón keresztül. Ezek nem a nyilvános interneten haladnak át.
- Az ExpressRoute-kapcsolatok kínál a nagyobb biztonságot, a megbízhatóság és a nagyobb sebesség (legfeljebb 10 GB/s), egységes késés mellett.
- Az ExpressRoute hasznos virtuális adatközpontok, a megfelelőségi szabályok magánkapcsolatok társított előnyeit magonkénti.
- Az ExpressRoute közvetlen a kapcsolódhat közvetlenül a Microsoft útválasztói 100Gbps nagyobb sávszélesség igényei szerint.
- ExpressRoute BGP használatával útvonalakat a helyszíni hálózatok, az Azure-példányokon és a Microsoft nyilvános címek között cserél.

Az ExpressRoute-kapcsolatok általában telepítése magában foglalja egy ExpressRoute-szolgáltató révén. A rövid útmutatóban közös először használja a virtuális adatközpontok közötti kapcsolatot létesíteni a site-to-site VPN és a helyszíni erőforrásokhoz, és majd áttelepítését egy ExpressRoute-kapcsolat, ha egy fizikai összekapcsolása a szolgáltatónál akkor jön létre.

**tudj meg többet:**
- [Áttekintés](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) expressroute.
- [Ismerje meg](https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about) ExpressRoute közvetlen.

### <a name="best-practice-optimize-expressroute-routing-with-bgp-communities"></a>Ajánlott eljárás: BGP-Közösségek az ExpressRoute-útválasztás optimalizálása

Ha több ExpressRoute-kapcsolatcsoporttal rendelkezik, több útvonalon csatlakozhat a Microsofthoz. Ennek eredményeképpen optimálisnál rosszabb útválasztás történhet, és a forgalom is igénybe vehet egy hosszabb úton jut el a Microsoft és a Microsoft hálózatába. Minél hosszabb a hálózati útvonal, annál nagyobb a késés. Késés közvetlen hatással van az alkalmazás teljesítmény- és felhasználói élményt.

#### <a name="example"></a>Példa

Vegyünk egy példát.

- Van két irodája az Egyesült Államokban, egy Los Angelesben és egy New Yorkban.
- Az irodák egy WAN hálózaton csatlakoznak úgy amely lehet a saját gerinchálózata vagy a szolgáltatója IP VPN.
- Két ExpressRoute-kapcsolatcsoporttal rendelkezik, egy az USA nyugati régiójában és egy az USA keleti régiójában, amelyek szintén a WAN hálózathoz csatlakoznak. Nyilvánvaló, hogy két útvonalon keresztül csatlakozhat a Microsoft hálózatához.


 
**A probléma** most már rendelkezik Azure-telepítés (például az Azure App Service) imagine USA nyugati régiója és USA keleti régiójában is.
- Azt szeretné, hogy a felhasználók az egyes irodákban lévő érdekében a legközelebbi Azure-szolgáltatások eléréséhez.
- Ily módon szeretné csatlakoztatni a Los Angelesben lévő felhasználók az USA nyugati Azure- és a felhasználók az USA keleti Azure New York-i.
- Ez a módszer keleti parti felhasználók esetében, de nem a Nyugat-India. A probléma a következőképpen történik:
    - Mindegyik ExpressRoute-kapcsolatcsoport azt meghirdeti mindkét előtagot az USA keleti Azure (23.100.0.0/16) és az USA nyugati Azure-(13.100.0.0/16).
    - Anélkül, hogy melyik előtag melyik régióból származik az előtagok máshogy nem.
    - A WAN hálózat feltételezheti, hogy mindkét előtag közelebb az USA keleti régiója, USA nyugati régiójában, és így irányíthatja a felhasználókat a mindkét iroda az ExpressRoute-kapcsolatcsoport az USA keleti régiójában, így egy kisebb, mint az optimális működés érdekében a felhasználók számára a Los Angeles-i irodában.

![VPN](./media/migrate-best-practices-networking/bgp1.png)
*BGP-Közösségek nem optimalizált kapcsolat*

**Megoldás**

Optimalizálható az Útválasztás mindkét irodához, melyik előtag az USA nyugati Azure- és melyik az USA keleti Azure ismernie kell. Ez az információ kódolhatja a BGP-Közösség értékét.
- Egy egyedi BGP-közösségérték rendel minden egyes Azure-régióban. Például az USA keleti régiójában; 12076:51004 az USA nyugati régiójához 12076:51006.
- Most, hogy egyértelmű melyik előtag melyik Azure-régióba tartozik, konfigurálhatja az előnyben részesített ExpressRoute-kapcsolatcsoportot.
- Az útválasztási információ cseréje a BGP használja, mert meg is a BGP helyi preferenciaértékével befolyásolhatja az útválasztást.
- Ebben a példában rendeljen hozzá egy magasabb helyi preferenciaértéket a 13.100.0.0/16 az USA nyugati régiójában, mint az USA keleti régiójában, és hasonlóképpen magasabb helyi preferenciaértéket 23.100.0.0/16 az USA keleti régiójában, mint az USA nyugati régiójában. 
- Ez a konfiguráció biztosítja, hogy mind elérési út létezik, a Microsoftnak, Los Angelesben lévő felhasználók Azure USA nyugati régiója az USA nyugati kapcsolatcsoport segítségével csatlakoznak, és felhasználói New York-i csatlakozni az USA keleti Azure a kelet-kapcsolatcsoport használatával. Az útválasztás minkét oldalon optimalizálva van.

![VPN](./media/migrate-best-practices-networking/bgp2.png)
*BGP-Közösségek optimalizált kapcsolat*


**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing) útválasztás optimalizálása

## <a name="securing-vnets"></a>Virtuális hálózatok biztonságossá tétele

Virtuális hálózatok biztonságossá tétele a felelősséget, és a Microsoft között megosztott. A Microsoft számos hálózatkezelési szolgáltatásokat, valamint erőforrások biztonsága érdekében szolgáltatásokat biztosít. A virtuális hálózatok biztonsági tervezéskor számos kövesse, többek között a szegélyhálózaton megvalósítása, szűrés és biztonsági csoportok használata, IP-címek és erőforrásaihoz való hozzáférés biztonságossá tétele és támadás elleni védelem megvalósításához ajánlott eljárások.

**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices) hálózati biztonság ajánlott eljárásait.
- [Ismerje meg, hogyan](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm#security) biztonságos hálózatok megtervezése.

## <a name="best-practice-implement-an-azure-perimeter-network"></a>Ajánlott eljárás: egy Azure peremhálózat megvalósítása

Bár a Microsoft erősen fektet a felhőalapú infrastruktúra védelmét, ezenkívül védeni kell a felhőalapú szolgáltatások és -erőforráscsoportok. Egy többrétegű biztonsági megközelítés a legjobb védelmet biztosít. Fontos része annak a defense stratégia szegélyhálózaton helyezné érvényben.

- Szegélyhálózaton védi a belső hálózati erőforrásokhoz a nem megbízható hálózaton. 
- Azt, amely a legkülső, amely közvetlenül csatlakozik az internethez. Azt általában között helyezkedik el az internet és a vállalati infrastruktúra általában valamilyen védelmi mindkét oldalon az. 
- A vállalati hálózati topológiában az alapvető infrastruktúra erősen dúsítjuk régebben, a biztonsági eszközök több rétege címen. A határ az egyes rétegek eszközök és a házirend-kényszerítési pontok áll.
- Az egyes rétegek, amelyek tartalmazzák a tűzfalak, a szolgáltatásmegtagadásos (DoS) megelőzése, a behatolás észlelési/behatolás a védelmi rendszereket (IDS/IPS) és a VPN-eszközök a hálózati biztonsági megoldások kombinációját is tartalmazhatnak.
- Házirend betartatása peremhálózati tűzfal házirendeket, hozzáférés-vezérlési listák (ACL) vagy adott útválasztási használhatja.
- Amint a bejövő forgalom az internetről érkeznek, elfogni és kezeli a blokk támadások és kártékony forgalom védelmi megoldás együttes jogos kérésének engedélyezése a hálózatban.
- A bejövő forgalom közvetlenül az erőforrásokhoz a peremhálózaton is irányíthatja. A szegélyhálózat-alapú hálózati erőforráshoz és más erőforrások, a hálózati forgalom továbbítása helyezi át a hálózati ellenőrzést követően a mélyebb kommunikálhat.

Az alábbi ábra egy vállalati hálózaton, két biztonsági határokat egyetlen alhálózat szegélyhálózaton példán látható.

![VPN](./media/migrate-best-practices-networking/perimeter.png)
*szegélyhálózat-alapú hálózati üzembe helyezése*

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) üzembe helyezése az Azure és a helyszíni adatközpont közötti szegélyhálózaton.


## <a name="best-practice-filter-vnet-traffic-with-nsgs"></a>Ajánlott eljárás: szűrő virtuális hálózatok közötti forgalom NSG-k

Hálózati biztonsági csoportok (NSG) tartalmazhat több bejövő és kimenő biztonsági szabályt, amely a forgalmat az erőforrások szűréséhez. Szűrés forrás és cél IP-cím, port és protokoll szerint is lehet. 
- NSG tartalmaz szabályokat, amelyek engedélyezik vagy megtagadják a bejövő hálózati forgalom (vagy a kimenő hálózati forgalom) számos különböző Azure-erőforrások. Az egyes szabályokhoz meghatározhatja a forrást és a célt, valamint a használni kívánt portot és protokollt.
- Az NSG-szabályok információival 5 rekordos (forrás, forrásport, cél, céloldali port és protokoll) engedélyezik vagy megtagadják a forgalmat a prioritás szerint értékeli ki.
- Egy folyamatrekord jön létre a meglévő kapcsolatokhoz. A kommunikáció a folyamatrekordok kapcsolati állapota alapján lesz engedélyezve vagy tiltva.
- Egy flow-rekord lehetővé teszi, hogy egy NSG-állapot-nyilvántartó. Ha bármely címre egy kimenő biztonsági szabályt a 80-as porton keresztül ad meg, például nem válaszol a kimenő forgalmat egy bejövő biztonsági szabályt kell. Ha a kommunikáció kívülről indul, csak egy bejövő biztonsági szabályt kell meghatároznia.
- Ennek az ellenkezője is igaz. Ha egy porton engedélyezett a bejövő forgalom, nem kell egy kimenő biztonsági szabályt válaszoljanak a forgalomra, a portot adja meg.
- A meglévő kapcsolatok nem szakadnak meg, a biztonsági szabály, amely engedélyezve van a flow eltávolításakor. Adatforgalmi folyamatokat kapcsolatok le lesz állítva, és nincs adatforgalommal bármelyik irányba, legalább egy néhány perces megszakadnak.
- NSG-k létrehozásakor, hozzon létre néhány lehetséges, hanem számos szükséges.

### <a name="best-practice-secure-northsouth-and-eastwest-traffic"></a>Ajánlott eljárás: észak-dél és kelet-Nyugat forgalmának biztonságossá tétele

Virtuális hálózatok biztosításakor fontos fontolja meg a támadási vektorok.
- Csak az alhálózati NSG-k segítségével egyszerűbbé teszi a környezetet, de csak titkosítja a forgalmat az alhálózatban. Ez az úgynevezett észak-dél-forgalmat.
- Kelet-Nyugat forgalom ugyanazon az alhálózaton lévő virtuális gépek közötti forgalom néven ismert.
- Fontos használja ki mindkét formáját védelmet, így ha egy támadó hozzáfér a kívülről azok fog állítani csatolja ugyanazon az alhálózaton található gépek tett kísérlet során.

### <a name="use-service-tags-on-nsgs"></a>Szolgáltatás-címkék használata az NSG-k

Egy csoport IP-címelőtagok csoportjait jelölik. A szolgáltatás címke használata segít csökkenteni a összetettségét, az NSG-szabályok létrehozásakor.
- Szolgáltatáscímkéket használhat bizonyos IP-címek helyett, szabályok létrehozásakor.
- A Microsoft felügyeli a társított szolgáltatás címke címelőtagokat, és automatikusan frissíti a szolgáltatáscímkéket a címek módosítása esetén.
- Hozzon létre saját szolgáltatáscímkéket nem, vagy adja meg, hogy mely IP-címek egyes címkék.

Szolgáltatáscímkék a manuális tevékenység ki egy szabályt hozzárendelése csoportokhoz az Azure-szolgáltatások igénybe vehet. Például, ha szeretné engedélyezni a virtuális hálózat alhálózatához tartalmazó webes kiszolgálók elérésének az Azure SQL Database, sikerült 1433-as porton a kimenő szabály létrehozása, és használja a **Sql** szolgáltatáscímke.
- Ez **Sql** címke címelőtagjait Azure SQL Database és az Azure SQL Data Warehouse-szolgáltatását.
- Ha megad **Sql** értékeként, engedélyezett vagy tiltott forgalmat az Sql-be.
- Ha szeretné való hozzáférés engedélyezése **Sql** egy adott régióban, megadhatja az adott régióban. Például, ha azt szeretné, hogy engedélyezze a hozzáférést csak az Azure SQL Database, az USA keleti régiójában, megadhatja **Sql.EastUS** szolgáltatáscímkét.
- A címke a szolgáltatást jelöli, annak adott példányait azonban nem. Például a címke az Azure SQL Database szolgáltatást jelöli, de egy adott SQL-adatbázis vagy a kiszolgáló nem képviselik.
- A címke által jelölt címelőtagokat az **Internet** címke is jelöli.


**tudj meg többet:**

- [További információ](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview) NSG-ket.
- [Felülvizsgálat](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) a szolgáltatáscímkék NSG-ket.


## <a name="best-practice-use-application-security-groups"></a>Ajánlott eljárás: az alkalmazásbiztonsági csoportok használata

Az alkalmazásbiztonsági csoportok lehetővé teszik az alkalmazás struktúrájának természetes bővítményeként konfigurálhatja a hálózati biztonságot.

- Virtuális gépek csoportban, és meghatározhatja a hálózati biztonsági szabályokat az alkalmazásbiztonsági csoportok alapján.
- Az alkalmazásbiztonsági csoportok lehetővé teszik a biztonsági szabályokat nagy léptékben explicit IP-címek manuális karbantartása nélkül felhasználhatja.
- Alkalmazás biztonsági csoportokat kezelni a konkrét IP-címek és a szabálykészletek, összetettségétől lehetővé teszi, hogy az üzleti logikára összpontosíthat. 

### <a name="example"></a>Példa

![Alkalmazásbiztonsági csoportok](./media/migrate-best-practices-networking/asg.png)
*példa az alkalmazások biztonsági csoport*

**Hálózati illesztő** | **Alkalmazásbiztonsági csoportok**
--- | ---
NIC1 | AsgWeb
NIC2 | AsgWeb
NIC3 | AsgLogic
NIC4 | AsgDb 

- Ebben a példában csak egy alkalmazásbiztonsági csoport minden egyes hálózati adapter tartozik, de valójában több csoportot, az Azure korlátai megfelelően felületet is tartozhatnak.
- A hálózati adapterek egyetlen sem rendelkezik, egy társított NSG-t. NSG1 mindkét alhálózatok társítva, és a következő szabályokat tartalmazza.

    **Szabály neve** | **Cél** | **Részletek**
    --- | --- | ---   
    Allow-HTTP-Inbound-Internet | Engedélyezi az internetről érkező forgalmat a webkiszolgálóra. Az internetről bejövő forgalmat, nincs további szabály szükséges az AsgLogic vagy AsgDb alkalmazásbiztonsági csoportok szerint DenyAllInbound alapértelmezett biztonsági szabály nem. | Prioritás: 100<br/><br/> Forrás: internet<br/><br/> Forrásport: *<br/><br/> Cél: AsgWeb<br/><br/> Céloldali port: 80-as<br/><br/> Protokoll: TCP<br/><br/> Hozzáférés: engedélyezése.
    Deny-Database-All | AllowVNetInBound alapértelmezett biztonsági szabály lehetővé teszi, hogy az azonos virtuális hálózatban található erőforrások közötti minden kommunikáció is, ez a szabály az összes erőforrást a forgalom megtagadásához szükséges. | Prioritás: 120<br/><br/> Forrás: *<br/><br/> Forrásport: *<br/><br/> Cél: AsgDb<br/><br/> Céloldali port: 1433-as<br/><br/> Protokoll: összes<br/><br/> Hozzáférés: elutasítás.
    Allow-Database-BusinessLogic | A AsgLogic alkalmazásbiztonsági csoport forgalmat engedélyezni a AsgDb alkalmazásbiztonsági csoporthoz. Ez a szabály prioritását magasabb, mint a Megtagadás – adatbázis-minden szabály, és így a AsgLogic alkalmazásbiztonsági csoport a forgalom engedélyezve van, és minden forgalmat blokkol előtt, hogy a szabály feldolgozása. | Prioritás: 110<br/><br/> Forrás: AsgLogic<br/><br/> Forrásport: *<br/><br/> Cél: AsgDb<br/><br/> Céloldali port: 1433-as<br/><br/> Protokoll: TCP<br/><br/> Hozzáférés: engedélyezése.

- Az alkalmazásbiztonsági csoportokat forrásként vagy célként megadó szabályok csak az adott alkalmazásbiztonsági csoport tagságába tartozó hálózati adapterekre lesznek alkalmazva. Ha a hálózati adapter nem tagja az alkalmazásbiztonsági csoportnak, a szabály nem lesz az adapterre alkalmazva, még akkor sem, ha a hálózati biztonsági csoport az alhálózathoz van rendelve.

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) az alkalmazásbiztonsági csoportok.


### <a name="best-practice-secure-access-to-paas-using-vnet-service-endpoints"></a>Ajánlott eljárás: biztonságos hozzáférés a virtuális hálózati Szolgáltatásvégpontok használatával PaaS

Virtuális hálózati Szolgáltatásvégpontok a virtuális hálózat privát címterét és identitását az Azure-szolgáltatások kiterjesztésére egy közvetlen kapcsolaton keresztül.

- Végpontok lehetővé teszik a virtuális hálózatok csak a kritikus fontosságú Azure-szolgáltatási erőforrások védelme. A VNet felől az Azure-szolgáltatás felé irányuló forgalom mindig a Microsoft Azure gerinchálózatán halad át.
- Virtuális hálózat magáncímterét lehetnek egymással átfedésben lévő, és így nem használható a forgalom egy virtuális hálózatból származó egyedi azonosításához.
- A Szolgáltatásvégpontok engedélyezve vannak a virtuális hálózatban található, miután egy virtuális hálózati szabályt ad hozzá a szolgáltatási erőforrások gondoskodhat Azure-szolgáltatási erőforrások. Ez biztosítja, megnövelt biztonság teljes mértékben eltávolítja a nyilvános internetkapcsolaton keresztüli hozzáférés az erőforrásokhoz, és lehetővé teszi a forgalom csak a virtuális hálózatról.

![Szolgáltatásvégpontokat](./media/migrate-best-practices-networking/endpoint.png)
*Szolgáltatásvégpontokat*

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) virtuális hálózati Szolgáltatásvégpontok.


## <a name="best-practice-control-public-ip-addresses"></a>Ajánlott eljárás: szabályozhatja a nyilvános IP-címek

Nyilvános IP-címek az Azure-beli virtuális gépek, terheléselosztók, az application Gateway átjárók és VPN-átjárók társítható.

- Nyilvános IP-címek lehetővé teszik internetes erőforrások Azure-erőforrások bejövő kommunikációt, és az Azure-erőforrásokhoz való kommunikációhoz kimenő internetkapcsolattal.
- A kettő közötti számos alapszintű vagy standard termékváltozatú nyilvános IP-címek jönnek létre. Standard termékváltozatok bármely szolgáltatás rendelhetők, de a legtöbb általában virtuális gépek, a terheléselosztók és az application Gateway átjárók konfigurálása.
- Fontos megjegyezni, hogy egy alapszintű nyilvános IP-cím nincs-e egy NSG-t automatikusan konfigurálva. Konfigurálja a saját és a szabályok történő hozzáférés hozzárendelése kell. Standard Termékváltozat IP-címek egy NSG-t és a szabályok alapértelmezés szerint rendelkezik.
- Ajánlott eljárásként virtuális gépek ne egy nyilvános IP-címmel konfigurálva.
    - Ha megnyitott egy portot, csak kell webes szolgáltatásokhoz, például a 80-as vagy 443-as porton.
    - Standard távoli felügyeleti portokat például az SSH (22) és az RDP (3389-es) megtagadja, valamint minden más port, az NSG-k használatával kell megadni.
- A jobb eljárás a virtuális gépeket szeretne helyezni egy Azure load balancer vagy alkalmazás átjáró mögött. Ha szükség van a távoli felügyeleti portokhoz való hozzáférést, az Azure Security Center a just-in-time VM access használja.

**tudj meg többet:**

- [Ismerje meg](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) nyilvános IP-címek az Azure-ban.
- [További információ](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) just-in-time VM hozzáférés az Azure Security Centerben.


## <a name="leverage-azure-security-features-for-networking"></a>A hálózatkezelés az Azure biztonsági funkciói

Az Azure platform biztonsági funkcióval rendelkezik, amelyeket könnyen használható, és adja meg a részletes ellenintézkedések közös hálózati támadásokkal szemben. Ezek közé tartozik az Azure-tűzfal, a webalkalmazási tűzfal és a Network Watcher.

## <a name="best-practice-deploy-azure-firewall"></a>Ajánlott eljárás: Azure tűzfal üzembe helyezése

Az Azure tűzfal az egy felügyelt, felhőalapú és hálózati biztonsági szolgáltatás, amely védelmet nyújt a VNet-erőforrások. Egy teljes körűen állapot-nyilvántartó tűzfal--szolgáltatásként beépített magas rendelkezésre állás és a korlátlan felhőbeli méretezhetőséget.

![Szolgáltatásvégpontokat](./media/migrate-best-practices-networking/firewall.png)
*Azure tűzfal*

- Az Azure-tűzfal is központilag létrehozása, kényszerítése, és jelentkezzen alkalmazás és a hálózati kapcsolat házirendek az előfizetések és a virtuális hálózatok között.
- Azure-tűzfalon használja egy statikus nyilvános IP-címet a VNet-erőforrások kívül tűzfalak azonosíthatja a virtuális hálózatról származó forgalmat engedélyezi.
- Azure tűzfal és az Azure Monitor teljesen integrálva a naplózást és az elemzésre.
- Ajánlott eljárásként Azure tűzfal-szabályok létrehozása során a teljes tartománynév címkék segítségével szabályokat hozhat létre.
    - Egy teljesen minősített tartománynév-címke jól ismert Microsoft-szolgáltatásokkal kapcsolatos teljes tartománynevek csoportját jelöli.
    - Egy teljesen minősített tartománynév-címke segítségével a szükséges kimenő hálózati forgalom engedélyezése a tűzfalon keresztül.
- Például ahhoz, hogy manuálisan a Windows Update hálózati adatforgalom a tűzfalon keresztül, meg kellene több alkalmazás szabály létrehozásához. FQDN címkék használatával, létrehoz egy alkalmazás-szabályt, és tartalmazza a Windows-frissítések címke. Ez a szabály érvényben, a Microsoft Windows Update-végpontokra irányuló hálózati forgalmat a tűzfal is áthaladhat.

**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/firewall/overview) Azure tűzfal.
- [Ismerje meg](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN címkék.


## <a name="best-practice-deploy-azure-web-application-firewall-waf"></a>Ajánlott eljárás: Azure webalkalmazási tűzfal (WAF) üzembe helyezése

Webalkalmazások egyre inkább ki rosszindulatú támadásoknak, amelyek gyakran ismert biztonsági rések kihasználására. Biztonsági rések közé tartozik az SQL-injektálásos támadásokról, és többhelyes parancsfájlok futtatására. Ilyen támadások megakadályozása az alkalmazás kódjában kihívást jelenthet, és megkövetelheti szigorúan betartandó, javítást és megfigyelést az alkalmazás topológiájának több rétegén. Központosított webalkalmazási tűzfal egyszerűbbé jóval egyszerűbb, és segít a fenyegetések vagy a behatolások elleni védelme app rendszergazdák. A web app tűzfal reagálhat a biztonsági fenyegetések gyorsabb, azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett, hogy egyes webalkalmazások biztonságossá tétele. A meglévő alkalmazásátjárókat egyszerűen át lehet alakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.

Az Azure webalkalmazási tűzfal (WAF) az Azure application gateway egyik szolgáltatása.
- WAF a webes alkalmazások gyakori biztonsági rések ellen a központi védelmet nyújt.
- WAF védi a háttérkód módosítása nélkül.
- Több web apps megvédheti egyszerre egy application gateway mögött
- WAF integrálva van az Azure Security Center.
- Testre szabható a WAF-szabályok és az alkalmazások követelményeinek megfelelően.
- Ajánlott eljárásként használjon a WAF elöl más webes felé néző alkalmazások, többek között az alkalmazások Azure-beli virtuális gépeken vagy egy Azure App Service-ben.

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/application-gateway/waf-overview) WAF.
- [Felülvizsgálat](https://docs.microsoft.com/azure/application-gateway/application-gateway-waf-configuration) WAF korlátozások és kizárások.


## <a name="best-practice-implement-azure-network-watcher"></a>Ajánlott eljárás: megvalósítása az Azure Network Watcher

Az Azure Network Watcher-erőforrások és a egy Azure virtuális hálózatok közötti kommunikáció monitorozása eszközöket biztosít. Például egy virtuális Gépet és a egy végpontot, például egy másik virtuális gép vagy teljes tartománynév, erőforrások megtekintése és a egy virtuális hálózat, erőforrás-kapcsolatok közötti kommunikáció monitorozása, vagy a hálózati forgalom problémák diagnosztizálásához.

![Network Watcher](./media/migrate-best-practices-networking/network-watcher.png)
*Network Watcher*

- A Network Watcher figyelheti és diagnosztizálhatja a virtuális gépekhez való bejelentkezés nélkül hálózatkezelési problémákat.
- Riasztások beállításával csomagrögzítést aktiválhat, és a csomag szintjén teljesítményének valós idejű adatokhoz való hozzáférésre. Ha problémát észlel, részletesen megvizsgálhatja azt.
- Ajánlott eljárás az, használjon a Network Watcher NSG-Folyamatnaplók áttekintéséhez.
    - NSG-Folyamatnaplók a Network Watcher lehetővé teszik egy NSG-n keresztül bejövő és kimenő IP-forgalomra vonatkozó információk megtekintése.
    - Forgalmi naplók írt json formátumban.
    - Forgalmi naplók megjelenítése a szabályonkénti, amelyre a folyamat vonatkozik, a folyamat (forrás és cél IP-cím, forrás és a cél-port és protokoll), és hogy engedélyezett vagy tiltott a forgalom 5-ször több információt a hálózati adapter (NIC) kimenő és bejövő forgalom.

**tudj meg többet:**

- [Áttekintés](https://docs.microsoft.com/azure/network-watcher) a Network Watcher.
- [További](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) kapcsolatos NSG flow naplókat.

## <a name="use-partner-tools-in-the-azure-marketplace"></a>Partner-eszközök használata az Azure Marketplace-en

Összetettebb hálózati topológiákat biztonsági termékeket a Microsoft-partnerektől, az adott hálózati virtuális készülékek (nva-k) használatával.

- Az NVA egy virtuális Gépet, amely hálózati funkciót, például egy tűzfal, WAN-optimalizáció vagy egyéb hálózati funkciók.
- Az nva-k tovább egyengetheti a virtuális hálózat biztonsági és hálózati funkciók. A magas rendelkezésre állású tűzfalak, behatolás-megelőzési, behatolásérzékelési, webalkalmazás-tűzfalak (alsóbb), WAN-optimalizálás, útválasztást, terheléselosztást, VPN, tanúsítványkezelés, Active Directory és a többtényezős hitelesítés telepíthető.
- Számos olyan szállítókra érhető el az nva-t a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/). 
 

## <a name="best-practice-implement-firewalls-and-nvas-in-hub-networks"></a>Ajánlott eljárás: tűzfalak és az nva-k megvalósításához a hub hálózatokban

Az agyban a szegélyhálózaton (az internet-hozzáférés) megfelelően kezelik egy Azure tűzfalon, a tűzfal farm, vagy a webalkalmazás-tűzfalak (alsóbb). Vegye figyelembe a következő összehasonlítást.

**Tűzfalszabályok típusa** | **Részletek**
--- | ---
Alsóbb | Webes alkalmazások gyakori, és általában a biztonsági rések és az esetleges biztonsági rések. sorból.<br/><br/> Alsóbb úgy tervezték, hogy a támadásokat (HTTP/HTTPS), webes alkalmazások, pontosabban, mint az általános tűzfal.<br/><br/> Képest hagyomány tűzfal-technológiája, alsóbb rendelkezik adott szolgáltatások, amelyek belső webkiszolgálók védelme a fenyegetésekkel szemben.
Azure Firewall | Azure tűzfal a üzemeltetett topológiájú hálózatokat, és a helyszíni hálózatok hozzáférés-vezérlése a munkaterhelések védelme érdekében például tűzfal-farmok nva-t használja egy közös felügyeleti mechanizmus és a biztonsági szabályok.<br/><br/> Az Azure-tűzfal rendelkezik beépített méretezhetőség.
NVA tűzfalak | Azure tűzfal NVA például a tűzfal-farmok közös felügyeleti mechanizmus, és meg topológiájú hálózatokat, és a helyszíni hálózatok hozzáférés-vezérlése üzemeltetett munkaterhelések védelme érdekében a biztonsági szabályok rendelkezik.<br/><br/> NVA tűzfalak manuálisan méretezhetők egy terheléselosztó mögé.<br/><br/> Abban az esetben, ha az nva-t tűzfal van kisebb, mint a WAF speciális szoftver rendelkezik szűrését, és vizsgálja meg a bejövő és kimenő forgalmat bármilyen típusú szélesebb körű alkalmazás-hatókör.<br/><br/> Ha nva-t használni kívánt megtalálhatja azokat az Azure piactéren.

Javasoljuk, hogy egy készletét az Azure-tűzfalak (vagy az nva-k) használata az internetről érkező forgalomhoz, és a egy másik származó forgalmat a helyszíni.
- Biztonsági kockázat, csak egy tűzfalak használatával is, nincs biztonsági határ a két fajta hálózati forgalom között lehetővé teszi.
- Különálló tűzfalat rétegek használatával csökkenti az ellenőrző biztonsági szabályok összetettségét, és törölje melyik szabály vonatkozik, hogy mely bejövő hálózati kérésekre.

**tudj meg többet:**
- [Ismerje meg](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) nva-k használatával egy Azure virtuális hálózat.

## <a name="next-steps"></a>További lépések 

Tekintse át a többi ajánlott eljárásokat:

- [Ajánlott eljárások](migrate-best-practices-security-management.md) biztonsági és felügyeleti migrálás után.
- [Ajánlott eljárások](migrate-best-practices-costs.md) cost Management a migrálás után.
