<properties 
   pageTitle="Az ExpressRoute bemutatása | Microsoft Azure"
   description="Ez az oldal egy áttekintést nyújt az ExpressRoute szolgáltatásról, beleértve az ExpressRoute-kapcsolatok működését."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>


# Az ExpressRoute technikai áttekintése

A Microsoft Azure ExpressRoute használatával kiterjesztheti helyszíni hálózatait a Microsoft-felhőbe egy dedikált privát kapcsolaton keresztül, amelyet egy kapcsolatszolgáltató biztosít. Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben. Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.

![](./media/expressroute-introduction/expressroute-basic.png)

**A következő fő előnyöket nyújtják:**

- 3. rétegbeli kapcsolatok a helyszíni hálózat és a Microsoft Cloud közt egy kapcsolatszolgáltatón keresztül. A kapcsolatok lehetnek: bármely elemek közötti (IPVPN) hálózat, pontok közötti Ethernet-kapcsolat vagy egy virtuális keresztkapcsolat egy Ethernet-adatcserélőn keresztül.
- Kapcsolódás a Microsoft-felhőszolgáltatásokhoz az adott geopolitikai régió minden régiójában.
- Globális kapcsolódás a Microsoft-szolgáltatásokhoz az összes régióban az ExpressRoute prémium bővítmény használatával.
- Dinamikus útválasztás a hálózata és a Microsoft közt iparági szabványnak megfelelő protokollokon (BGP) keresztül.
- Beépített redundancia minden társviszony-létesítési helyszínen a nagyobb megbízhatóság érdekében.
- Kapcsolat-üzemidőre vonatkozó [SLA](https://azure.microsoft.com/support/legal/sla/).
- QoS és támogatás speciális alkalmazások (például Skype Vállalati verzió) több szolgáltatási osztályához.

További részletek: [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – gyakori kérdések).

## <a name="howtoconnect"></a>Hogyan csatlakoztathatom a hálózatomat a Microsofthoz az ExpressRoute használatával?

A helyszíni hálózata és a Microsoft-felhő között háromféleképpen hozhat létre kapcsolatot:

### Közös elhelyezés felhőalapú adatcsere keretében

Ha közösen van elhelyezve egy felhőalapú adatcserével rendelkező létesítményben, virtuális keresztkapcsolatokat rendelhet a Microsoft-felhőhöz a közös környezet szolgáltatójának Ethernet-adatcserélőjén. A közöskörnyezet-szolgáltatók 2. rétegbeli keresztkapcsolatokat vagy felügyelt, 3. rétegbeli keresztkapcsolatokat kínálnak a közös elhelyezési létesítményben lévő infrastruktúra és a Microsoft-felhő között.

### Pontok közti Ethernet-kapcsolatok 

Helyszíni adatközpontjait/irodáit pontok közötti Ethernet-hivatkozásokon keresztül is csatlakoztathatja a Microsoft Cloudhoz. A pontok közötti Ethernet-szolgáltatók vagy 2. rétegbeli kapcsolatokat vagy felügyelt 3. rétegbeli kapcsolatokat kínálnak a hely és a Microsoft Cloud között.

### Bármely elemek közötti (IPVPN-) hálózat

WAN hálózatát integrálhatja a Microsoft Clouddal. Az IPVPN-szolgáltatók (jellemzően MPLS VPN) bármilyen elemek közötti kapcsolódást kínálnak a fiókirodák és az adatközpontok közti kapcsolatokhoz. A Microsoft Cloud összekapcsolható a WAN hálózatával, így ugyanúgy jelenik meg, mint bármely másik fiókiroda. A WAN-szolgáltatók jellemzően felügyelt 3. rétegbeli kapcsolatokat kínálnak. Az ExpressRoute képességi és szolgáltatásai a fenti kapcsolódási modellek mindegyikében megegyeznek. 

A kapcsolatszolgáltatók egy vagy több kapcsolódási modellt kínálhatnak. Kapcsolatszolgáltatójával együttműködve választhatja ki az Ön számára optimális modellt.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## Az ExpressRoute szolgáltatásai

Az ExpressRoute a következő szolgáltatásokat és képességeket támogatja: 

### 3. rétegbeli kapcsolatok

A Microsoft iparági szabványnak megfelelő dinamikus útválasztási protokollt (BGP) alkalmaz az útvonalak cseréjéhez a helyszíni hálózat, az Azure-ban lévő példányai és a Microsoft nyilvános címek között.  Több BGP-munkamenetet létesítünk a hálózattal, különböző forgalomprofilokkal. További részletek az [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (ExpressRoute-kapcsolatcsoportok és útválasztási tartományok) című cikkben találhatók.

### Redundancia

Minden egyes ExpressRoute-kapcsolatcsoport két kapcsolattal rendelkezik két Microsoft Enterprise peremhálózati útválasztóhoz (MSEE) a kapcsolatszolgáltatótól / a hálózata peremétől. A Microsoft kettős BGP-kapcsolatot igényel a kapcsolatszolgáltatótól / az Ön oldaláról – egyet-egyet mindkét MSEE-hez. Dönthet úgy, hogy nem helyez üzembe redundáns eszközöket / Ethernet-kapcsolatcsoportokat az Ön oldalán. A kapcsolatszolgáltatók azonban redundáns eszközöket használnak annak biztosítása érdekében, hogy az Ön kapcsolatai redundáns módon jussanak el a Microsofthoz. Az [SLA](https://azure.microsoft.com/support/legal/sla/) érvényességének előfeltétele a redundáns 3. rétegbeli kapcsolódási konfiguráció. 

### Kapcsolódás a Microsoft-felhőszolgáltatásokhoz

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Az ExpressRoute-kapcsolatok a következő szolgáltatásokhoz biztosítanak hozzáférést:

- Microsoft Azure-szolgáltatások
- Microsoft Office 365-szolgáltatások
- Microsoft CRM Online-szolgáltatások 
 
Az ExpressRoute-on keresztül támogatott szolgáltatások részletes listáját az [ExpressRoute – Gyakori kérdések](expressroute-faqs.md) oldalon tekintheti meg.

### Kapcsolódás az összes régióhoz egy geopolitikai régión belül

Csatlakozhat a Microsofthoz a [társviszony-létesítési helyszínek](expressroute-locations.md) egyikén, és elérheti az összes régiót a geopolitikai régióban. 

Ha például Amsterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, az Észak-Európában és Nyugat-Európában üzemeltetett összes Microsoft -felhőszolgáltatáshoz hozzáférhet. A geopolitikai régiók, a hozzájuk rendelt Microsoft felhő-régiók és a megfelelő ExpressRoute társviszony-létesítési helyszínek áttekintését az [ExpressRoute partnerek és társviszony-létesítési helyszínek](expressroute-locations.md) oldalon találja.

### Globális kapcsolódás az ExpressRoute prémium bővítmény használatával

Az ExpressRoute prémium bővítménnyel kiterjesztheti a kapcsolódási lehetőségeket a geopolitikai határokon túl. Ha például Amsterdamban csatlakozik a Microsofthoz az ExpressRoute-on keresztül, a világszerte az összes régióban (az országos felhők kivételével) üzemeltetett összes Microsoft-felhőszolgáltatáshoz hozzáféréssel fog rendelkezni. A Dél-Amerikában vagy Ausztráliában üzemeltetett szolgáltatásokat ugyanúgy eléri majd, mint az észak- vagy nyugat-európai régióban lévőket.

### A kapcsolati partnerek gazdag ökoszisztémája

Az ExpressRoute kapcsolati és SI-partnerek egyre bővülő ökoszisztémájával rendelkezik. A legfrissebb információkért lásd az [ExpressRoute-szolgáltatókat és helyeket](expressroute-locations.md) ismertető cikket.

### Kapcsolódás országos felhőkhöz

A Microsoft elkülönített felhőkörnyezeteket tart fenn speciális geopolitikai régiók és ügyfélszegmensek számára. Az országos felhők és szolgáltatók listájáért lásd az [ExpressRoute-szolgáltatókat és -helyeket](expressroute-locations.md) ismertető lapot.

### Támogatott sávszélesség-lehetőségek

A sávszélességek széles választékához vásárolhat ExpressRoute-kapcsolatcsoportokat. A támogatott sávszélességeket az alábbi lista tartalmazza. Mindenképp egyeztessen kapcsolatszolgáltatójával a támogatott sávszélességekről.

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 Gbps
- 2 Gbps
- 5 Gbps
- 10 Gbps

### Dinamikus sávszélesség-méretezés

Az ExpressRoute-kapcsolatcsoport sávszélességét (az elérhető legjobb lehetőség alapján) anélkül növelheti, hogy le kellene bontania a kapcsolatokat. 

### Rugalmas számlázási modellek

Kiválaszthatja az Ön számára optimális számlázási modellt. Az alábbi számlázási modellek közül választhat. További részletekért lásd az [ExpressRoute FAQ](expressroute-faqs.md) (ExpressRoute – Gyakori kérdések) oldalt. 

- **Korlátlan adatforgalom**. Az ExpressRoute-kapcsolatcsoport számlázása havi díj alapján történik, amely az összes bejövő és kimenő adatátvitelt ingyenesen tartalmazza. 
- **Forgalmi díjas adatforgalom**. Az ExpressRoute-kapcsolatcsoport számlázása havi díj alapján történik. Az összes bejövő adatátvitel ingyenes. A kimenő adatátvitel számlázása az átvitt adatok GB-jai alapján történik. Az adatátviteli árak régiónként eltérnek.
- **ExpressRoute prémium bővítmény**. Az ExpressRoute prémium az ExpressRoute-kapcsolatcsoport bővítménye. Az ExpressRoute prémium bővítmény az alábbi képességeket biztosítja: 
    - Az Azure nyilvános és az Azure privát társviszony-létesítés útvonalkorlátja 4000 útvonalról 10 000 útvonalra nő.
    - Globális kapcsolódás a szolgáltatásokhoz. A bármely régióban (az országos felhők kivételével) létrehozott ExpressRoute-kapcsolatcsoportok a világ bármely más régiójában elérik az erőforrásokat. Egy Nyugat-Európában létrehozott virtuális hálózat például elérhető egy, a Szilícium-völgyben kiosztott ExpressRoute-kapcsolatcsoportról is.
    - A VNet-hivatkozások száma ExpressRoute-kapcsolatcsoportonként 10-ről egy magasabb korlátra nő a kör sávszélességének függvényében.

## Következő lépések

- Ismerje meg az ExpressRoute-kapcsolatokat és útválasztási tartományokat. Lásd: [ExpressRoute-kapcsolatcsoportok és útválasztási tartományok](expressroute-circuit-peerings.md).
- Találjon egy szolgáltatót. Lásd: [ExpressRoute-partnerek és társviszony-létesítési helyszínek](expressroute-locations.md).
- Ellenőrizze, hogy minden előfeltétel teljesül-e. Lásd: [ExpressRoute-előfeltételek](expressroute-prerequisites.md).
- Tekintse meg az [Útválasztás](expressroute-routing.md), a [NAT](expressroute-nat.md) és a [QoS](expressroute-qos.md) követelményeit.
- Az ExpressRoute-kapcsolat konfigurálása.
    - [ExpressRoute-kapcsolatcsoport létrehozása](expressroute-howto-circuit-classic.md)
    - [Útválasztás konfigurálása](expressroute-howto-routing-classic.md)
    - [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-classic.md)



<!--HONumber=Oct16_HO3-->


