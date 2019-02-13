---
title: Ajánlott eljárások az Azure DDoS Protection és a referenciaarchitektúrákat |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja fel a naplózási adatok révén mélyrehatóan elemezheti az alkalmazással kapcsolatos információk.
services: security
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: barclayn
ms.openlocfilehash: 9f837a5dbea15b159a226b12c034add1dcf69834
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117850"
---
# <a name="azure-ddos-protection-best-practices-and-reference-architectures"></a>Az Azure DDoS Protection: Ajánlott eljárások és referenciaarchitektúrák

Ez a cikk olyan informatikai döntéshozókat, és a biztonsági csoporthoz. Azt várja, hogy ismeri, az Azure-hálózatot és a biztonságot.

Az elosztott szolgáltatásmegtagadásos (DDoS-) tervezése rugalmasság szükséges tervezésekor és kialakításakor hibaállapot különböző. A cikk ismerteti az ajánlott eljárások az alkalmazások tervezése az Azure-ban az DDoS-támadásokkal szembeni ellenálló-képesség.

## <a name="types-of-attacks"></a>Típusú támadások

DDoS olyan támadás, amely megkísérli az alkalmazás-erőforrások felhasználta rendelkezésére. A cél, hogy az alkalmazás rendelkezésre állásának és történő jogos kérelmek kezelése hatással van. Kifinomult, és a méretét és a hatása nagyobb támadások mutat. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure DDoS-támadásokkal szembeni folyamatos védelmet biztosít. Ez a védelem integrálva van az Azure platform alapértelmezés szerint, és nélkül többletköltség. 

A DDoS protection a platform legfontosabb mellett [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) biztosít a fejlett funkciói hálózati támadásokkal szemben. Automatikusan hangolt az adott Azure-erőforrások védelme. A védelem az egyszerű ahhoz, hogy új virtuális hálózat létrehozása során. Ez a létrehozása után is elvégezhető, és nem kell application vagy az erőforrás módosítani.

![Azure DDoS Protection szerepe az ügyfelek és a egy virtuális hálózatot egy támadó elleni védelme](media/azure-ddos-best-practices/image1.png)

DDoS-támadások három kategóriába sorolható:-es, protokoll és az erőforrás.

### <a name="volumetric-attacks"></a>-Es támadások

-Es támadások a DDoS-támadások leggyakoribb típusát. -Es támadások találgatásos támadások, a hálózati és az átviteli réteg célzó. Felhasználta a rendelkezésére erőforrásokhoz, mint a hálózati kapcsolatok próbálják. 

Ezeket a támadásokat gyakran több fertőzött rendszer használnak kéréssekkel túlterhelheti a hálózati rétegek látszólag jogos forgalmat. Hálózati szintű protokollok, például az Internet Control Message Protocol (ICMP), a User Datagram Protocol (UDP) és a Transmission Control Protocol (TCP) használnak.

A leggyakrabban használt hálózati rétegből DDoS-támadások elárasztás ICMP echo-elárasztás, DNS, UDP, TCP SZIN és NTP erősítési támadások. Ilyen típusú támadások segítségével nem csak megszakadhat a szolgáltatás, hanem az, egy smokescreen vonatkozó adatgyűjteményével, és célzott hálózati behatolások. A legutóbbi-es támadások például a [Memcached biztonsági rés kiaknázása elleni](https://www.wired.com/story/github-ddos-memcached/) GitHub érintette. A támadás UDP-port 11211 célzott és létrehozott 1.35 Tb/s támadás a kötet.

### <a name="protocol-attacks"></a>Protokoll támadások

Protokoll cél alkalmazásprotokollok támadások. Próbálják használni a rendelkezésre álló erőforrásokat, az infrastruktúra eszközeit, például egy tűzfal, az alkalmazáskiszolgálókat, és terheléselosztókat. Protokoll támadások használja a csomagot, amely helytelen formátumú vagy protokoll rendellenességeket tartalmaz. Ezeket a támadásokat működnek nagy számú nyitott kérelmeket, hogy a kiszolgálók és egyéb kommunikációt eszközök választ, és várjon, amíg csomagot választ küldött. A cél a nyitott kérelmei, ami végül az összeomlás, a rendszer megkísérli.

A protokoll-alapú DDoS-támadás leggyakoribb példája TCP SZIN áramlik. A támadás egy TCP SZIN kérelem egymás után megpróbál terhelje túl a cél. Az célunk, hogy a cél nem válaszol. A 2016 DIN szolgáltatáskimaradás TCP SZIN állt alkalmazásréteg támadás alatt szereplőkkel elárasztja, hogy célzott 53-as port DIN a DNS-kiszolgálók.

### <a name="resource-attacks"></a>Erőforrás-támadások

Erőforrás-támadások a céloznia az alkalmazásrétegre. Háttérbeli folyamatok annak érdekében, terhelje túl a rendszer aktiválása azokat. Erőforrás a normál megjelenését, de ez végzi a CPU-igényes lekérdezi a kiszolgálóhoz való visszaélés forgalom támadások. A szükséges erőforrások lefoglalhat forgalom mennyisége az alacsonyabb, mint a más típusú támadásokkal szemben. A forgalom erőforrás támadás megkülönböztetni jogos forgalmat, így nehéz észlelni. A leggyakrabban használt erőforrás támadásról van HTTP/HTTPS- és DNS szolgáltatások.

## <a name="shared-responsibility-in-the-cloud"></a>A felhőbeli közös felelősség

Egy védelmi-stratégia segít a növekvő különböző és kifinomultabbak támadások elleni. Egy, az ügyfél és a Microsoft között megosztott felelősségre. A Microsoft meghívja ezt egy [közös felelősség modell](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/). A következő ábrán látható a felosztás felelős:

![Az Azure és az ügyfél feladatkörei](media/azure-ddos-best-practices/image2.png)

Az Azure-ügyfelek kihasználhatják a Microsoft ajánlott eljárások áttekintése és globálisan elosztott alkalmazások, amelyek a tervezett és tesztelt hiba létrehozása.

## <a name="fundamental-best-practices"></a>Alapvető ajánlott eljárások

Az alábbi szakaszok ad részletes útmutatást rugalmas DDoS szolgáltatások létrehozásához az Azure-ban.

### <a name="design-for-security"></a>Biztonság – tervezés

Győződjön meg arról, hogy biztonsági prioritást az alkalmazás, a tervezéstől és implementálástól a központi telepítés és a műveletek teljes életciklusa során. Alkalmazások, amelyek lehetővé teszik egy viszonylag kis mennyiségű kérést egy erőforrás, szolgáltatás-kimaradás eredményez túlságosan mennyisége használandó hibák is rendelkezhetnek. 

A Microsoft Azure-on futó szolgáltatás védelme érdekében kell az alkalmazásarchitektúra alapos ismerete és összpontosítson a [szoftverminőség következő öt alappillérére](https://docs.microsoft.com/azure/architecture/guide/pillars).
Ha tisztában van tipikus forgalommal, a kapcsolódási modellt az alkalmazás és más alkalmazások és a Szolgáltatásvégpontok, amelyek ki vannak téve a nyilvános internethez.

Annak biztosítása, hogy egy alkalmazás elég rugalmas, maga az alkalmazás osztályra irányuló szolgáltatásmegtagadási kezelésére legfontosabb. Az Azure platformba, kezdve beépített biztonság és adatvédelem a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl/default.aspx). Az SDL-ből dobásig minden fejlesztési fázist, és biztosítja, hogy Azure-t folyamatosan frissítjük, hogy még biztonságosabb.

### <a name="design-for-scalability"></a>Méretezhetőség tervezése

Méretezhetőség azt arról, hogy a rendszer megnövekedett terhelés kezelésére. Kell megterveznie az alkalmazásokat, [horizontális skálázásra](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) az igény egy felerősített terhelés, kifejezetten a DDoS-támadások esetén. Ha az alkalmazás egy szolgáltatás egyetlen példánya függ, a meghibásodási pontot hoz létre. Rugalmasabb és skálázhatóbb üzembe helyezett példányban több lehetővé teszi a rendszer.

A [Azure App Service](../app-service/app-service-value-prop-what-is.md), jelölje be egy [App Service-csomag](../app-service/overview-hosting-plans.md) , amelynek keretében több példányt. Az Azure Cloud Services, konfigurálja az összes, használja a szerepkörök [több példány](../cloud-services/cloud-services-choose-me.md). A [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), győződjön meg arról, hogy a virtuális gép (VM) architektúra tartalmaz-e egynél több virtuális gép és minden virtuális gép szerepel egy [rendelkezésre állási csoport](../virtual-machines/virtual-machines-windows-manage-availability.md). Azt javasoljuk, [a virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) az automatikus méretezési képességeivel.

### <a name="defense-in-depth"></a>Mélységi védelemmel is

A működési mélységi védelemmel is, hogy különböző védelmi vonalként stratégiák használatával kezelni a kockázatokat. Az alkalmazások biztonsági védelmekkel rétegez csökkenti annak az esélyét, a sikeres támadás. Azt javasoljuk, hogy meg, hogy biztonságos tervek alkalmazásai számára az Azure platform beépített funkcióinak használatával.

Például támadások veszélye növeli a méretét (*felülettel*) az alkalmazás. Csökkentheti a támadási engedélyezési használatával gombra kattintva zárja be a közzétett IP-címtér le, és nem szükséges a terheléselosztókat a portokat figyeli ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) és [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)). [Hálózati biztonsági csoportok (NSG-k)](../virtual-network/security-overview.md) egy másik módja a támadási felület csökkentése érdekében.
Használhat [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags) és [az alkalmazásbiztonsági csoportok](../virtual-network/security-overview.md#application-security-groups) minimalizálása érdekében a biztonsági szabályok létrehozása és konfigurálása a hálózati biztonság, az alkalmazás struktúrájának természetes bővítményeként összetettségét.

Azure-szolgáltatások üzembe kell helyeznie egy [virtuális hálózat](../virtual-network/virtual-networks-overview.md) , amikor csak lehetséges. Ez az eljárás lehetővé teszi, hogy a szolgáltatási erőforrások a magánhálózati IP-címeken keresztül kommunikáljanak. Egy virtuális hálózatot az Azure-szolgáltatások forgalmára alapértelmezés szerint nyilvános IP-címeket használ forrás IP-ként. Használatával [szolgáltatásvégpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) átvált a szolgáltatás forgalmát használják a virtuális hálózat privát címeire, a forrás IP-címek, amikor a virtuális hálózatról az Azure-szolgáltatás fér hozzá.

Gyakran láthatjuk az ügyfelek a helyi és azok az Azure-erőforrások lekérdezése megtámadott erőforrások. Ha csatlakoztatja a helyszíni környezet az Azure-ba, javasoljuk, hogy minimálisra csökkenthető a helyszíni erőforrások kitéve a nyilvános interneten. Használhatja a méretezési csoport és az Azure fejlett DDoS protection funkcióinak a jól ismert nyilvános entitásokat az Azure-beli üzembe helyezésével. Mivel ezek nyilvánosan elérhető-e az entitások gyakran céljának DDoS-támadások, a helyszíni erőforrások gyakorolt helyezné azokat az Azure-ban csökkenti.

## <a name="azure-offerings-for-ddos-protection"></a>A DDoS protection az Azure-ajánlatok

Az Azure két DDoS szolgáltatásajánlatok, (3. és 4. réteg) hálózati támadások ellen védelmet biztosító rendelkezik: A DDoS Protection alapszintű és a DDoS Protection-szabvány. 

### <a name="ddos-protection-basic"></a>A DDoS Protection alapszintű

Alapvető védelmet további költségek nélkül alapértelmezés szerint integrálva van az Azure-bA. A méretezési csoport és a kapacitás a globálisan üzembe helyezett Azure-hálózat biztosít – a forgalom figyelési és valós idejű megoldás révén közös hálózati rétegből támadások elleni védelmet. A DDoS Protection alapszintű nem kell felhasználói konfiguráció vagy az alkalmazás módosítani. A DDoS Protection alapszintű segít megvédeni az összes Azure-szolgáltatások, beleértve a PaaS-szolgáltatások, például az Azure DNS-ben.

![Térkép ábrázolása az Azure-hálózatot, a szöveg "globális DDoS kockázatcsökkentési jelenlét" és "Vezető kockázatcsökkentési képességet nyújt"](media/azure-ddos-best-practices/image3.png)

Az Azure alapszintű DDoS protection a szoftver- és Hardverleltár összetevőből áll. A szoftver vezérlősík úgy dönt, hogy mikor, hol, és milyen típusú forgalom kell kormányzott, elemzése, és távolítsa el a forgalmat a támadás hardveres berendezések keresztül. A vezérlősík gondoskodik az infrastruktúra-szintű DDoS Protection alapján ezt a döntést *házirend*. Ez a szabályzat statikusan állítsa be, és a felsővezetőknek, akik a alkalmazni az összes Azure-ügyfeleknek.

Például a DDoS Protection-szabályzat meghatározza, milyen forgalom adatmennyiségét, a védelem *aktiválódik.* (Azaz a bérlői forgalom kell átirányítani Tisztítja a berendezések keresztül.) A házirend határozza meg majd, hogyan kell a tisztítási készülékek *csökkentése* a támadás.

Az Azure DDoS Protection alapszintű szolgáltatási célja az infrastruktúra védelme és az Azure platform védelmet. Megszünteti a forgalmat, amikor az meghaladja gyakoriságát, hogy a lényeges, hogy az hatással lehet egy több-bérlős környezetben több ügyfelet. Nem biztosít, riasztások, vagy ügyfél testreszabott házirendek.

### <a name="ddos-protection-standard"></a>DDoS Protection Standard

Standard szintű védelmet továbbfejlesztett DDoS kockázatcsökkentési szolgáltatásokat biztosítja. Automatikusan az adott Azure-erőforrások virtuális hálózat védelme érdekében van beállítva. A védelem az egyszerű ahhoz, hogy minden olyan új vagy meglévő virtuális hálózaton, és nem igényel, alkalmazás vagy erőforrás módosítása nélkül. Több előnye is van az alapszintű szolgáltatásban, beleértve a naplózást, a riasztás és a telemetriai adatokat tartalmaz. Az alábbiakban azoknak az Azure DDoS Protection Standard szolgáltatás legfontosabb funkcióit.

#### <a name="adaptive-real-time-tuning"></a>Valós idejű adaptív hangolás

Az Azure DDoS Protection alapszintű szolgáltatásnak köszönhetően az ügyfelek adatai, és megelőzheti a hatások más ügyfelek számára. Ha a szolgáltatást annak üzembe helyezése egy tipikus kötet jogos bejövő forgalom, amely kisebb, mint például a *triggerek sebessége* a infrastruktúra szintű DDoS Protection-szabályzatot, az általuk erőforrásokon DDoS-támadás mehet maradna. Legutóbbi támadások (például több vektor DDoS-) bonyolultságát és az alkalmazás-specifikus viselkedéseket a bérlők általában hívja fel az /-ügyfél, testre szabott alkalmazásvédelmi szabályzatokat. A szolgáltatás két insights segítségével éri el a testre:

- Automatikus tanulás az per-ügyfél (-IP) forgalom minták a 3. rétegbeli és a 4.

- Minimalizálja a téves, a mérlegeli, hogy a méretezési csoport az Azure lehetővé teszi, hogy jelentős mennyiségű forgalom számára.

![A DDoS Protection Standard működése, a "Házirend létrehozása" bekarikázott ábrája](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>A DDoS Protection telemetriát, figyelés és riasztások

A DDoS Protection Standard tesz elérhetővé részletes telemetria keresztül [Azure Monitor](../azure-monitor/overview.md) DDoS-támadás idejére. A DDoS Protection használó Azure Monitor-metrikák riasztásokat lehet konfigurálni. Naplózás integrálhatók a Splunk (az Azure Event Hubs), az Azure Log Analytics és az Azure Storage, a speciális elemzés a Azure Monitor Diagnostics felületéről.

##### <a name="ddos-mitigation-policies"></a>DDoS-kockázatcsökkentést házirendek

Az Azure Portalon válassza ki a **figyelő** > **metrikák**. Az a **metrikák** ablaktáblán válassza ki az erőforráscsoportot, válasszon ki egy erőforrást **nyilvános IP-cím**, és válassza ki az Azure nyilvános IP-címet. A DDoS-metrikák láthatók a **rendelkezésre álló metrikák** ablaktáblán.

A DDoS Protection Standard érvényes három autotuned kockázatcsökkentési házirendek (TCP külön, a TCP és UDP) minden egyes nyilvános IP-címek, a védett erőforrások, a virtuális hálózat, amelyen engedélyezve van a DDoS. A metrika kiválasztásával is megtekintheti a szabályzat küszöbértékek **bejövő csomagok DDoS elleni védelem aktiválásához**.

![Rendelkezésre álló metrikák és a teljesítménymetrikák diagramja](media/azure-ddos-best-practices/image7.png)

A szabályzat küszöbértékek automatikusan konfigurált, machine learning-alapú hálózati forgalom profilkészítés keresztül. DDoS-támadás kockázatcsökkentése bekövetkezik támadás alatt lévő IP-cím csak akkor, ha a házirend küszöbérték túllépésre kerül.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>A metrika az IP-címet a DDoS-támadás alatt áll

Ha nyilvános IP-cím a támadásokkal szemben, a metrika értékének **alatt DDoS támadásokkal szemben, vagy nem** 1-re módosítja, a DDoS Protection kockázatcsökkentési hajt végre a támadás forgalmat.

!["A DDoS elleni támadás vagy a nem" metrika- és a diagram](media/azure-ddos-best-practices/image8.png)

Azt javasoljuk, hogy ez a mérőszám a riasztás konfigurálása. Ezután értesítést is, ha egy aktív DDoS-támadás kockázatcsökkentése, a nyilvános IP-cím végrehajtani.

További információkért lásd: [kezelése Azure DDoS Protection Standard az Azure portal használatával](../virtual-network/ddos-protection-manage-portal.md).

#### <a name="web-application-firewall-for-resource-attacks"></a>Az erőforrás-támadások webalkalmazási tűzfal

Adott erőforrás támadásokkal szemben az alkalmazásréteg szintjén, konfigurálnia kell egy webalkalmazási tűzfal (WAF) segítségével biztonságos webalkalmazásokat. A WAF megvizsgálja a bejövő webes forgalmat blokkolja az SQL-injektálásokat, a többhelyes parancsfájlok, DDoS, és egyéb 7. rétegbeli támadásokat. Az Azure biztosít [szolgáltatásként az Application Gateway WAF](../application-gateway/application-gateway-web-application-firewall-overview.md) a webalkalmazásoknak a gyakori támadások és biztonsági rések központi védelméhez. Nincsenek elérhető az Azure-partnerek, amely segítségével az igényeinek megfelelő lehet egyéb WAF-megoldások a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1).

Akár webalkalmazás-tűzfalak fogékonyak-es és állami Erőforrásfogyás támadásokkal szemben. Feltétlenül ajánlott engedélyezni a DDoS Protection Standard a WAF-es védelmének elősegítésére virtuális hálózaton, és a protokoll támadásokat. További információkért lásd: a [DDoS Protection referenciaarchitektúrák](#ddos-protection-reference-architectures) szakaszban.

### <a name="protection-planning"></a>Védelem megtervezése

Tervezése és előkészítése is fontos tudni, hogyan hajthat végre a rendszer a DDoS-támadások során. Az incidenskezelés válaszadási terv tervezése ebből a törekvésből részét képezi.

Ha van a DDoS Protection standard szintű, ügyeljen arra, hogy a virtuális hálózaton az internet felé néző végpontok engedélyezve van. A DDoS-riasztások konfigurálása segítségével folyamatosan figyelje a infrastruktúra minden potenciális támadások. 

Az alkalmazások egymástól függetlenül kell figyelni. Ismerje meg az alkalmazás normál viselkedését. Készítse elő, ha az alkalmazás viselkedik DDoS-támadások során a várt módon működni.

#### <a name="testing-through-simulations"></a>Szimuláció keresztül tesztelése

Tanácsos a módját a szolgáltatások által rendszeres szimulációk végzése válaszol a támadás feltételezéseket teszteléséhez. Tesztelés során ellenőrizze, hogy a szolgáltatások vagy alkalmazások továbbra is a várt módon működik, és nem a felhasználói élmény szolgáltatáskimaradás van. Azonosítsa a technológia és a folyamat szempontból hézagok, és belefoglalhatják őket a DDoS-válasz stratégia. Azt javasoljuk, hogy az átmeneti környezetek vagy során az éles környezetbe minimalizálása érdekében csúcsidőn kívül végre ilyen vizsgálat.

A Microsoft közreműködésével [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felület, ahol az Azure-ügyfelek hozhat létre nyilvános végpontokat a DDoS Protection-kompatibilis szimulációkhoz forgalmát. Használhatja a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) szimulációt:

- Ellenőrizze, hogy hogyan segít az Azure DDoS Protection az Azure-erőforrások védelme a DDoS-támadások ellen.

- Optimalizálhatja az incidensmegoldási folyamatba, a DDoS-támadások.

- A dokumentum DDoS megfelelőségi.

- A hálózati biztonsági csoportok betanítása.

Kiberbiztonsági védelmi állandó innovációt van szükség. Az Azure DDoS Standard protection egy állapot-az-a legújabb az egyre összetettebb DDoS-támadások számának csökkentése érdekében egy költséghatékony megoldást kínál az.

## <a name="components-of-a-ddos-response-strategy"></a>DDoS válasz stratégia összetevői

DDoS-támadás, amely általában célozza az Azure-erőforrások felhasználói szempontból minimális beavatkozásra van szükség. Továbbra is DDoS beépítése az incidensmegoldási stratégia részeként kockázatcsökkentési minimálisra üzletmenetre gyakorolt hatása.

### <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

A Microsoft rendelkezik egy kiterjedt threat intelligence hálózati. Ez a hálózat a közösségi ismeretek egy kiegészítő biztonsági Közösség, amely támogatja a Microsoft online szolgáltatások, Microsoft-partnerek és az internetes biztonság Közösségen belül kapcsolatot használ. 

Kritikus fontosságú infrastruktúrát-szolgáltatóként a Microsoft fenyegetésekkel kapcsolatos korai figyelmeztetéseket kap. A Microsoft online services és a globális rátába a gyűjti össze a fenyegetésészlelési intelligencia. A Microsoft magában foglalja az összes ez fenyegetésfelderítést vissza az Azure DDoS Protection termékekbe.

Ezenkívül a Microsoft Digital Crimes Unit (DCU) hajt végre sértő stratégiák botnetek ellen. Botnetek olyan parancs- és DDoS-támadások gyakori forrását.

### <a name="risk-evaluation-of-your-azure-resources"></a>Az Azure-erőforrások kockázat kiértékelésekor

Rendkívül fontos tudni, hogy a kockázat egy DDoS támadástól folyamatosan hatókörén. Rendszeres időközönként megválaszolnia: 
- Milyen új nyilvánosan elérhető Azure-erőforrások védelmére van szükség?

- Van olyan hibaérzékeny pont a szolgáltatásban? 

- Hogyan szolgáltatások el lehet különíteni a támadás során továbbra is szolgáltatások elérhetővé tétele az ügyfeleknek érvényes hatásának korlátozása?

- Ahol a DDoS Protection Standard engedélyezni kell a virtuális hálózatok vannak, de nem? 

- Azok a szolgáltatások aktív/aktív feladatátvétellel több régióban?

### <a name="customer-ddos-response-team"></a>Ügyfél DDoS válasz csapata

DDoS válasz csapat egy kulcsfontosságú lépéseként támadás gyors és hatékony. Azonosítsa a névjegyeit, állítsa be a szervezetben, akik megtervezése és végrehajtása rendszer felügyeli. A DDoS-válasz csapat alaposan megismerje az Azure DDoS Protection standard szintű szolgáltatás. Győződjön meg arról, hogy a csapat azonosítása, és csökkentheti a támadás a belső és külső ügyfeleinek, beleértve a Microsoft támogatási csapatának.

DDoS-válasz csapata azt javasoljuk, hogy használja-e szimulációs gyakorlatok a szolgáltatás rendelkezésre állása és folytonosságának megtervezése normál részeként. Ezek a gyakorlatok mérettesztelés kell tartalmaznia.

### <a name="alerts-during-an-attack"></a>A támadás során riasztások

Az Azure DDoS Protection Standard azonosítja és elhárítja a DDoS-támadások felhasználói beavatkozás nélkül. Értesítés küldése, ha van egy aktív kockázatcsökkentési egy védett nyilvános IP-címek, meg szeretnénk [olyan riasztást konfigurálhat](../virtual-network/ddos-protection-manage-portal.md) a mérőszám a **alatt DDoS támadásokkal szemben, vagy nem**. Ha szeretné tudni, hogy a támadást, eldobott forgalom és egyéb részletek a méretezési csoport más DDoS metrikákhoz riasztásokat hozhat létre.

#### <a name="when-to-contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatához

- A DDoS-támadások során látja, hogy a védett erőforrás teljesítménye jelentősen csökkentett teljesítményű, vagy az erőforrás nem érhető el.

- Úgy gondolja, hogy a DDoS Protection szolgáltatás nem várt módon viselkedik. 

  A DDoS Protection szolgáltatás indítása a megoldás, ha csak a metrikaérték **DDoS elleni védelem (TCP/TCP SZIN/UDP) aktiválásához házirend** alacsonyabb, mint a védett nyilvános IP-erőforrásból fogadott forgalmat.

- A vírusos esemény, amely jelentősen növeli a hálózati forgalom tervezi.

- Egy aktor van veszélyeztetett az erőforrásokon, DDoS-támadások indítására.

Amelyek kritikus fontosságú üzleti hatással van, hozzon létre egy súlyossági-A [támogatási jegyet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

### <a name="post-attack-steps"></a>A támadás utáni lépések

Mindig egy futás utáni tegye a támadás után, és szükség szerint módosítsa a DDoS-válasz stratégia stratégiát. Dolgot figyelembe venni:

- Volt hiba fennakadást a szolgáltatás vagy a felhasználói élmény méretezhető architektúra hiánya miatt?

- Mely alkalmazások vagy szolgáltatások küldését a legtöbbet?

- Hogyan hatékony volt a DDoS-válasz stratégiát, és hogyan fejleszthető?

Ha azt gyanítja, hogy Ön egy DDoS-támadás alatt áll, eszkalálása a normál Azure-támogatási csatornákon keresztül.

## <a name="ddos-protection-reference-architectures"></a>A DDoS Protection referenciaarchitektúrák

A DDoS Protection Standard célja [egy virtuális hálózaton üzembe helyezett szolgáltatásokhoz](../virtual-network/virtual-network-for-azure-services.md). Egyéb szolgáltatások az alapértelmezett a DDoS Protection egyszerű szolgáltatás érvényes. A következő referenciaarchitektúrákat a csoportosított felhőarchitektúra-sémák, forgatókönyvek szerint vannak elrendezve.

### <a name="virtual-machine-windowslinux-workloads"></a>(Windows/Linux) virtuális gépek számítási feladataihoz

#### <a name="application-running-on-load-balanced-vms"></a>Elosztott terhelésű virtuális gépeken futó alkalmazás

Ez a referenciaarchitektúra rendelkezésre állás és méretezhetőség javítása érdekében egy terheléselosztó mögötti méretezési csoportban lévő több Windows virtuális gépek futtatásához kapcsolódó bevált eljárásokat mutat be. Ez az architektúra bármilyen állapot nélküli számítási feladat, például a webkiszolgálón is használható.

![Az elosztott terhelésű virtuális gépeken futó alkalmazások a referencia-architektúra ábrája](media/azure-ddos-best-practices/image9.png)

Ebben az architektúrában a számítási feladatok több virtuálisgép-példányon oszlanak el. Egy nyilvános IP-címet, és ossza el a virtuális gépek egy terheléselosztón keresztül internetes forgalmat. A DDoS Protection Standard engedélyezve van a (internet) az Azure-terheléselosztó, amely a nyilvános IP-cím társítva van a virtuális hálózaton.

A terheléselosztó elosztja a beérkező internetes kérelmeket a Virtuálisgép-példányok. A Virtual machine scale sets engedélyezése előre meghatározott szabályok alapján manuálisan vagy automatikusan skálázhatók fel vagy ki a virtuális gépek számát. Ez azért fontos, ha az erőforrás a DDoS-támadás alatt áll. Ez a referenciaarchitektúra a további információkért lásd: [Ez a cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

#### <a name="application-running-on-windows-n-tier"></a>Futó Windows N szintű alkalmazás

Egy N szintű architektúra számos módon implementálható. Az alábbi ábrán egy tipikus háromrétegű webalkalmazást. Ez az architektúra épül, amely a cikk [elosztott terhelésű virtuális gépek a méretezhetőség és a rendelkezésre állási](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). A webes és üzleti szintek elosztott terhelésű virtuális gépeket használnak.

![A futó Windows N szintű alkalmazáshoz a referencia-architektúra ábrája](media/azure-ddos-best-practices/image10.png)

Ebben az architektúrában a DDoS Protection Standard engedélyezve van a virtuális hálózaton. A virtuális hálózat összes nyilvános IP-címek lekérése a DDoS protection a 3. rétegbeli és 4. 7. rétegbeli védelemre helyezze üzembe az Application Gateway WAF termékváltozatban. Ez a referenciaarchitektúra a további információkért lásd: [Ez a cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

#### <a name="paas-web-application"></a>PaaS webalkalmazás

Ez a referenciaarchitektúra bemutatja, hogy egyetlen régión belüli Azure App Service alkalmazások futtatásához. Ezt az architektúrát mutat be bevált eljárásokat használó webalkalmazás [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) és [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
Feladatátvételi forgatókönyvek esetén a készenléti régió van beállítva.

![A PaaS-webalkalmazás számára a referencia-architektúra ábrája](media/azure-ddos-best-practices/image11.png)

Az Azure Traffic Manager egy régiót az Application Gateway felé irányítja a kérelmeket. A normál működés során az Application Gateway az aktív régióban irányítja a kérelmeket. Ha az adott régió elérhetetlenné válik, a Traffic Manager átadja a feladatokat az Application Gateway a készenléti régió.

Az összes, a webalkalmazás felé irányuló internetes adatforgalmat a [Application Gateway nyilvános IP-cím](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview) Traffic Manageren keresztül. Ebben a forgatókönyvben az app service (webalkalmazás) önmagában nem közvetlenül kifelé irányuló, és az Application Gateway által védett. 

Azt javasoljuk, hogy konfigurálja az Application Gateway WAF Termékváltozata (akadályozza) 7. rétegbeli (HTTP/HTTPS/WebSocket) támadások elleni védelme érdekében. Ezenkívül webalkalmazások vannak konfigurálva [csak az Alkalmazásátjáróra érkező forgalmat fogad](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) IP-címet.

Ez a referenciaarchitektúra kapcsolatos további információkért lásd: [Ez a cikk](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

### <a name="mitigation-for-non-web-paas-services"></a>A PaaS-szolgáltatások nem webes megoldás

#### <a name="hdinsight-on-azure"></a>Az Azure-on HDInsight

Ez a referenciaarchitektúra azt mutatja be a DDoS Protection szabvány egy [Azure HDInsight-fürt](https://docs.microsoft.com/azure/hdinsight/). Győződjön meg arról, hogy a HDInsight-fürt virtuális hálózaton van csatolva, és, hogy a DDoS Protection engedélyezve van-e a virtuális hálózaton.

!["HDInsight" és "Speciális beállítások" csomópontoknak, a virtuális hálózati beállítások](media/azure-ddos-best-practices/image12.png)

![DDoS elleni védelem kiválasztása](media/azure-ddos-best-practices/image13.png)

Ebben az architektúrában a HDInsight-fürthöz az internetről felé haladó adatforgalmat a HDInsight-átjárón terheléselosztóhoz társított nyilvános IP-címhez. Az átjáró terheléselosztó ezután elküldi a forgalmat az átjárócsomópontokhoz vagy a feldolgozó csomópontok közvetlenül. A DDoS Protection Standard engedélyezve van a HDInsight virtuális hálózaton, mert a virtuális hálózat összes nyilvános IP-címek lekérése a DDoS protection a 3. rétegbeli és 4. Ez a referenciaarchitektúra az N szintű és többrégiós referenciaarchitektúrák kombinálható.

Ez a referenciaarchitektúra a további információkért lásd: a [kiterjesztése az Azure HDInsight használata az Azure Virtual Network](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json) dokumentációját.


> [!NOTE]
> Az Azure App Service-környezet a PowerApps- vagy API Management a virtuális hálózatban egy nyilvános IP-Címmel rendelkező nem natív módon támogatottak.

## <a name="next-steps"></a>További lépések

* [Az Azure DDoS Protection termékoldala](https://azure.microsoft.com/services/ddos-protection/)

* [Az Azure DDoS Protection blogja](https://aka.ms/ddosblog)

* [Az Azure DDoS Protection dokumentációja](../virtual-network/ddos-protection-overview.md)
