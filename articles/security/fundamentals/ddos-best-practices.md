---
title: Rugalmas megoldások tervezése az Azure DDoS Protection segítségével
description: Ismerje meg, hogyan használhatja a naplózási adatokat, hogy mély betekintést nyerjen az alkalmazásba.
services: security
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2018
ms.author: barclayn
ms.openlocfilehash: 8d3fc809999508bf3d49c3765c90017e89e80fa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624056"
---
# <a name="azure-ddos-protection---designing-resilient-solutions"></a>Azure DDoS Protection – Rugalmas megoldások tervezése

Ez a cikk informatikai döntéshozóknak és biztonsági személyzetnek szól. Azt várja, hogy ismeri az Azure-t, a hálózatépítést és a biztonságot.
DDoS egy olyan típusú támadás, amely megpróbálja kimeríteni az alkalmazás erőforrásait. A cél az, hogy befolyásolja az alkalmazás rendelkezésre állását és a jogos kérések kezelésére való képességét. A támadások egyre kifinomultabbak és nagyobb a méreteés a becsapódás. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak. Az elosztott szolgáltatásmegtagadási (DDoS) rugalmasság tervezése számos hibamód tervezését és tervezését igényli. Az Azure folyamatos védelmet nyújt a DDoS-támadások ellen. Ez a védelem alapértelmezés szerint integrálva van az Azure platformba, és nem jár többletköltséggel.

A platform alapvető DDoS-védelme mellett az [Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/) speciális DDoS-kockázatcsökkentési képességeket is biztosít a hálózati támadások ellen. A rendszer automatikusan bevan hangolva az adott Azure-erőforrások védelme érdekében. Védelem egyszerű lehetővé teszi az új virtuális hálózatok létrehozása során. A létrehozás után is elvégezhető, és nem igényel alkalmazás- vagy erőforrás-módosításokat.

![Az Azure DDoS Protection szerepe az ügyfelek és a virtuális hálózat támadókkal szembeni védelmében](./media/ddos-best-practices/image1.png)


## <a name="fundamental-best-practices"></a>Alapvető bevált gyakorlatok

A következő szakaszok előíró útmutatást nyújtanak a DDoS-t rugalmas azure-beli szolgáltatások létrehozásához.

### <a name="design-for-security"></a>Biztonságot szem előtt tartó tervezés

Győződjön meg arról, hogy a biztonság prioritást élvez az alkalmazás teljes életciklusa során, a tervezéstől és a megvalósítástól az üzembe helyezésig és a műveletekig. Az alkalmazások olyan hibákat tartalmaznak, amelyek lehetővé teszik, hogy viszonylag kis mennyiségű kérelem használjon túlzott mennyiségű erőforrást, ami szolgáltatáskimaradást eredményez. 

A Microsoft Azure-on futó szolgáltatások védelme érdekében jól meg kell ismernie az alkalmazásarchitektúrát, és a [szoftverminőség öt pillérére kell összpontosítania.](/azure/architecture/guide/pillars)
Ismernie kell a tipikus forgalmi kötetek, az alkalmazás és más alkalmazások közötti kapcsolati modell, valamint a szolgáltatás végpontok, amelyek ki vannak téve a nyilvános interneten.

Annak biztosítása, hogy egy alkalmazás elég rugalmas ahhoz, hogy kezelje a szolgáltatásmegtagadás, amely az alkalmazás maga a legfontosabb. A biztonság és az adatvédelem be van építve az Azure platformba, kezdve a [biztonsági fejlesztési életciklussal (SDL).](https://www.microsoft.com/sdl/default.aspx) Az SDL minden fejlesztési fázisban kezeli a biztonságot, és biztosítja, hogy az Azure folyamatosan frissüljön, hogy még biztonságosabb legyen.

### <a name="design-for-scalability"></a>Tervezés a méretezhetőség érdekében

A méretezhetőség az, hogy a rendszer mennyire képes kezelni a megnövekedett terhelést. Tervezze meg alkalmazásait [vízszintesen,](/azure/architecture/guide/design-principles/scale-out) hogy megfeleljen a felerősített terhelés igényeinek, különösen DDoS-támadás esetén. Ha az alkalmazás egy szolgáltatás egyetlen példányától függ, egyetlen meghibásodási pontot hoz létre. Több példány kiépítése rugalmasabbá és méretezhetőbbé teszi a rendszert.

Az [Azure App Service](/azure/app-service/app-service-value-prop-what-is)szolgáltatás, válasszon egy App [Service-csomag,](/azure/app-service/overview-hosting-plans) amely több példányt kínál. Az Azure Cloud Services esetében konfigurálja az egyes szerepköröket úgy, hogy [több példányt használjanak.](/azure/cloud-services/cloud-services-choose-me) [Az Azure virtuális gépek,](/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)győződjön meg arról, hogy a virtuális gép (VM) architektúra több virtuális gépet tartalmaz, és hogy minden virtuális gép szerepel egy [rendelkezésre állási készlet.](/azure/virtual-machines/virtual-machines-windows-manage-availability) Azt javasoljuk, [hogy a virtuális gép méretezési készletek](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-overview) automatikus skálázási képességek.

### <a name="defense-in-depth"></a>Mélységi védelem

Az ötlet mögött védelmi mélységben, hogy kezelje a kockázatot segítségével különböző védekező stratégiákat. A biztonsági védelem rétegezése egy alkalmazásban csökkenti a sikeres támadás esélyét. Azt javasoljuk, hogy az Azure platform beépített képességeinek használatával valósítson meg biztonságos terveket az alkalmazásaihoz.

Például a támadás kockázata az alkalmazás méretével (*felületével)* nő. Csökkentheti a felületet az engedélyezési lista használatával a terheléselosztók[(Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) és [Azure Application Gateway)](/azure/application-gateway/application-gateway-create-probe-portal)által nem szükséges IP-címterület és figyelőportok bezárásához. [A hálózati biztonsági csoportok (NSG-k)](/azure/virtual-network/security-overview) egy másik módja a támadási felület csökkentésének.
[A szolgáltatáscímkék](/azure/virtual-network/security-overview#service-tags) és [az alkalmazásbiztonsági csoportok](/azure/virtual-network/security-overview#application-security-groups) segítségével minimalizálhatja a biztonsági szabályok létrehozásának összetettségét és konfigurálhatja a hálózati biztonságot, mint az alkalmazás szerkezetének természetes kiterjesztését.

Amikor csak lehetséges, üzembe kell helyeznie az Azure-szolgáltatásokat egy [virtuális hálózatban.](/azure/virtual-network/virtual-networks-overview) Ez a gyakorlat lehetővé teszi, hogy a szolgáltatás erőforrások privát IP-címeken keresztül kommunikáljon. A virtuális hálózatról származó Azure-szolgáltatásforgalom alapértelmezés szerint nyilvános IP-címeket használ forrás IP-címként. A [szolgáltatásvégpontok](/azure/virtual-network/virtual-network-service-endpoints-overview) használatával a szolgáltatásforgalom a virtuális hálózati magáncímek használata a virtuális hálózati IP-címek, amikor az Azure-szolgáltatás elérése egy virtuális hálózatról.

Gyakran látjuk, hogy az ügyfelek helyszíni erőforrásait az Azure-beli erőforrásokkal együtt támadják meg. Ha helyszíni környezetet csatlakoztat az Azure-hoz, azt javasoljuk, hogy minimalizálja a helyszíni erőforrások nyilvános internet-expozícióját. Használhatja a méretezési és a fejlett DDoS védelmi képességek az Azure-ban a jól ismert nyilvános entitások az Azure-ban üzembe helyezésével. Mivel ezek a nyilvánosan elérhető entitások gyakran ddos-támadások célpontjai, az Azure-ban való elhelyezésük csökkenti a helyszíni erőforrásokra gyakorolt hatást.

## <a name="azure-offerings-for-ddos-protection"></a>Azure-ajánlatok a DDoS-védelemhez

Az Azure két DDoS-szolgáltatásajánlattal rendelkezik, amelyek védelmet nyújtanak a hálózati támadásokkal szemben (3. és 4. réteg): DDoS Protection Basic és DDoS Protection Standard. 

### <a name="ddos-protection-basic"></a>DDoS védelem alap

Az alapszintű védelem alapértelmezés szerint további költségek nélkül integrálódik az Azure-ba. A globálisan üzembe helyezett Azure-hálózat méretezése és kapacitása védelmet nyújt a gyakori hálózati szintű támadások ellen a forgalom folyamatos figyelése és a valós idejű megoldások révén. A DDoS Protection Basic nem igényel felhasználói konfigurációt vagy alkalmazásmódosítást. A DDoS Protection Basic segít megvédeni az összes Azure-szolgáltatást, beleértve a PaaS-szolgáltatásokat, például az Azure DNS-t is.

![Az Azure-hálózat leképezési ábrázolása a "Globális DDoS-kockázatcsökkentési jelenlét" és a "Vezető DDoS-kockázatcsökkentési kapacitás" szöveggel](./media/ddos-best-practices/image3.png)

Az Azure-ban az alapvető DDoS-védelem szoftver- és hardverösszetevőkből áll. A szoftvervezérlő sík dönti el, hogy mikor, hol és milyen típusú forgalmat kell irányítani a támadási forgalmat elemző és eltávolító hardverkészülékeken keresztül. A vezérlősík ezt a döntést egy infrastruktúra-szintű DDoS-védelmi *házirend*alapján hozza meg. Ez a szabályzat statikusan van beállítva, és általánosan alkalmazzák az összes Azure-ügyfelek számára.

A DDoS-védelem házirendpéldául meghatározza, hogy a védelmet milyen forgalomban kell *aktiválni.* (Ez azt illeti, a bérlő forgalmát a súrolóberendezéseken keresztül kell irányítani.) A szabályzat ezután meghatározza, hogy a súrolóberendezések hogyan *kell enyhíteni* a támadást.

Az Azure DDoS Protection Basic szolgáltatás az infrastruktúra és az Azure platform védelmére irányul. Csökkenti a forgalmat, ha túllépi a sebesség, amely olyan jelentős, hogy hatással lehet több ügyfél egy több-bérlős környezetben. Nem biztosít riasztási vagy ügyfélenkénti testreszabott szabályzatokat.

### <a name="ddos-protection-standard"></a>DDoS védelmi szabvány

A szabványos védelem továbbfejlesztett DDoS-kockázatcsökkentési funkciókat biztosít. A rendszer automatikusan úgy hangolja be, hogy a virtuális hálózatban lévő adott Azure-erőforrások védelme érdekében. A védelem egyszerűen engedélyezhetővé minden új vagy meglévő virtuális hálózaton, és nem igényel alkalmazás- vagy erőforrás-módosításokat. Számos előnye van az alapvető szolgáltatás, beleértve a naplózás, riasztási és telemetriai adatok. A következő szakaszok ismertetik az Azure DDoS Protection Standard szolgáltatás főbb funkcióit.

#### <a name="adaptive-real-time-tuning"></a>Adaptív, valós idejű hangolás

Az Azure DDoS Protection Basic szolgáltatás segít megvédeni az ügyfeleket, és megakadályozza a más ügyfelekre gyakorolt hatásokat. Ha például egy szolgáltatás ki van építve egy tipikus bejövő forgalom egy tipikus kötetéhez, amely kisebb, mint az infrastruktúra-szintű DDoS-védelem házirend *eseményindító sebessége,* az ügyfél erőforrásait ért DDoS-támadás észrevétlen maradhat. Általánosabban, a legutóbbi támadások összetettsége (például többvektor DDoS) és a bérlők alkalmazásspecifikus viselkedése ügyfélenkénti, testreszabott védelmi házirendeket tesz szükségessé. A szolgáltatás ezt a testreszabást két elemzési információ használatával valósítja meg:

- Az ügyfélenkénti (IP-alapú) forgalmi minták automatikus tanulása a 3.

- A hamis pozitív értékek minimalizálása, figyelembe véve, hogy az Azure-skála lehetővé teszi, hogy jelentős mennyiségű forgalmat.

![A DDoS Protection Standard működésének diagramja, bekarikázva a "Policy Generation"](./media/ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS Protection telemetria, figyelés és riasztás

A DDoS Protection Standard gazdag telemetriai adatokat tesz elérhetővé az [Azure Monitoron](/azure/azure-monitor/overview) keresztül egy DDoS-támadás időtartama alatt. Riasztások konfigurálása az Azure Monitor metrikák, amelyek DDoS Protection használ. A naplózást integrálhatja a Splunk (Azure Event Hubs), az Azure Monitor-naplók és az Azure Storage segítségével az Azure Monitor diagnosztikai felületén keresztül.

##### <a name="ddos-mitigation-policies"></a>DDoS-kockázatcsökkentési házirendek

Az Azure Portalon válassza a > **Metrikák** **figyelése**lehetőséget. A **Metrikák** ablaktáblán válassza ki az erőforráscsoportot, válassza ki a **nyilvános IP-cím**erőforrástípusát, és válassza ki az Azure nyilvános IP-címét. A DDoS-metrikák az **Elérhető metrikák** ablaktáblában láthatók.

A DDoS Protection Standard három autotuned mitigation házirendet (TCP SYN, TCP és UDP) alkalmaz a védett erőforrás minden nyilvános IP-címére, a DDoS-t engedélyező virtuális hálózatban. A házirend-küszöbértékeket a Bejövő csomagok metrika kiválasztásával tekintheti meg **a DDoS-kockázatcsökkentés elindításához.**

![Elérhető mérőszámok és mérőszámok diagramja](./media/ddos-best-practices/image7.png)

A házirend-küszöbértékek automatikusan konfigurálva gépi tanuláson alapuló hálózati forgalom profilozás. DDoS-kockázatcsökkentés csak akkor történik meg támadás alatt lévő IP-cím esetén, ha túllépi a házirend-küszöbértéket.

##### <a name="metric-for-an-ip-address-under-ddos-attack"></a>DDoS-támadás alatti IP-cím metrikája

Ha a nyilvános IP-cím támadás alatt áll, a **DDoS-támadás alatti** metrika értéke, vagy nem 1-re változik, mivel a DDoS-védelem enyhíti a támadási forgalmat.

!["A DDoS támadás vagy nem" metrikus és diagram](./media/ddos-best-practices/image8.png)

Azt javasoljuk, hogy konfigurálja a riasztást ezen a metrikán. Ezután értesítést kap, ha a nyilvános IP-címén aktív DDoS-kockázatcsökkentés történik.

További információ: [Manage Azure DDoS Protection Standard using the Azure Portal](/azure/virtual-network/ddos-protection-manage-portal).

#### <a name="web-application-firewall-for-resource-attacks"></a>Webalkalmazás tűzfala erőforrás-támadásokhoz

Az alkalmazásrétegen elkövetett erőforrás-támadásokra jellemzően konfigurálnia kell egy webalkalmazás-tűzfalat (WAF) a webalkalmazások biztonságossá tétele érdekében. A WAF megvizsgálja a bejövő webes forgalmat az SQL-befecskendezések, a helyek közötti parancsfájlok, a DDoS és más Layer 7 támadások blokkolása érdekében. Az Azure a [WAF-ot az Application Gateway szolgáltatásaként](/azure/application-gateway/application-gateway-web-application-firewall-overview) biztosítja a webalkalmazások közös biztonsági rések és biztonsági rések elleni központosított védelméhez. Az Azure-partnerek től más WAF-ajánlatok is elérhetők, amelyek az [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1)keresztül jobban megfelelhetnek az Ön igényeinek.

Még a webalkalmazás tűzfalak is ki vannak téve a volumetrikus és az állami kimerültség támadások. Javasoljuk, hogy engedélyezze a DDoS Protection Standard a WAF virtuális hálózaton, hogy megvédje a volumetrikus és protokoll támadások. További információt a [DDoS Protection referenciaarchitektúrák](#ddos-protection-reference-architectures) című szakaszában talál.

### <a name="protection-planning"></a>Védelmi tervezés

Tervezés és előkészítés elengedhetetlen annak megértéséhez, hogy a rendszer hogyan fog teljesíteni a DDoS-támadás során. Az incidenskezelési választerv tervezése ennek az erőfeszítésnek a része.

Ha ddos védelmi szabvány, győződjön meg arról, hogy engedélyezve van a virtuális hálózat az internet felé néző végpontok. A DDoS-riasztások konfigurálásával folyamatosan figyelheti az infrastruktúra esetleges támadásait. 

Az alkalmazások egymástól függetlenfigyelemmel figyelhető konkretmán. Ismerje meg az alkalmazás normális viselkedését. Készüljön fel a cselekvésre, ha az alkalmazás nem a várt módon viselkedik a DDoS-támadás során.

#### <a name="testing-through-simulations"></a>Tesztelés szimulációkon keresztül

Ez egy jó gyakorlat, hogy tesztelje a feltételezéseket arról, hogy a szolgáltatások hogyan reagálnak a támadás időszakos szimulációk végrehajtásával. A tesztelés során ellenőrizze, hogy a szolgáltatások vagy alkalmazások továbbra is a várt módon működnek-e, és nincs-e a felhasználói élmény megszakítása. Azonosítsa a hiányosságokat mind a technológia, mind a folyamat szempontjából, és beépítse azokat a DDoS-válaszstratégiába. Azt javasoljuk, hogy az ilyen vizsgálatokat átmeneti környezetben vagy nem csúcsidőben hajtsa végre az éles környezetre gyakorolt hatás minimalizálása érdekében.

A [BreakingPoint Cloud-tal](https://www.ixiacom.com/products/breakingpoint-cloud) együttműködve olyan felületet hoztunk létre, ahol az Azure-ügyfelek forgalmat generálhatnak a DDoS Protection-kompatibilis nyilvános végpontok ellen szimulációkhoz. A [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) szimulációval a következőkre használhatja:

- Ellenőrizze, hogy az Azure DDoS-védelem hogyan védi az Azure-erőforrásokat a DDoS-támadásoktól.

- Optimalizálja az incidensválasz-folyamatot a DDoS-támadás alatt.

- Dokumentum DDoS-megfelelőség.

- A hálózati biztonsági csapatok betanítása.

A kiberbiztonság folyamatos innovációt igényel a védelem terén. Az Azure DDoS Standard védelem egy korszerű ajánlat, amely hatékony megoldást kínál az egyre összetettebb DDoS-támadások csökkentésére.

## <a name="components-of-a-ddos-response-strategy"></a>A DDoS-válaszstratégia összetevői

Az Azure-erőforrásokat megcélozó DDoS-támadás általában minimális beavatkozást igényel felhasználói szempontból. Mégis, a DDoS-kockázatcsökkentés beépítése az incidens-reagálási stratégia részeként segít minimalizálni az üzletmenet folytonosságára gyakorolt hatást.

### <a name="microsoft-threat-intelligence"></a>A Microsoft fenyegetésekkel kapcsolatos intelligenciája

A Microsoft kiterjedt fenyegetésfelderítési hálózattal rendelkezik. Ez a hálózat egy olyan kiterjesztett biztonsági közösség kollektív ismereteit használja, amely támogatja a Microsoft online szolgáltatásait, a Microsoft-partnereket és az internetes biztonsági közösségen belüli kapcsolatokat. 

Kritikus infrastruktúra-szolgáltatóként a Microsoft korai figyelmeztetéseket kap a fenyegetésekről. A Microsoft online szolgáltatásaiból és globális ügyfélköréből gyűjti a fenyegetési információkat. A Microsoft az azure DDoS Protection-termékekbe visszaépíti ezt a fenyegetésfelderítési intelligenciát.

Emellett a Microsoft Digitális Bűncselekmények Egysége (DCU) sértő stratégiákat hajt végre a botnetek ellen. A botnetek a DDoS-támadások gyakori parancsnoklási és vezérlési forrásai.

### <a name="risk-evaluation-of-your-azure-resources"></a>Az Azure-erőforrások kockázatértékelése

Rendkívül fontos, hogy megértsék a körét a kockázatot a DDoS támadás folyamatosan. Rendszeresen kérdezd meg magadtól:

- Milyen új, nyilvánosan elérhető Azure-erőforrásokat kell védelemmel elkezdeni?

- Van egyetlen hibapont a szolgáltatásban? 

- Hogyan lehet elkülöníteni a szolgáltatásokat a támadások hatásának korlátozása érdekében, miközben továbbra is elérhetővé teszik a szolgáltatásokat az érvényes ügyfelek számára?

- Vannak olyan virtuális hálózatok, ahol a DDoS Protection Standard-ot engedélyezni kell, de nem? 

- A szolgáltatásaim aktívak/aktívak több régióban a feladatátvételsel?

### <a name="customer-ddos-response-team"></a>Ügyfél DDoS válaszcsapat

A DDoS-válaszcsapat létrehozása kulcsfontosságú lépés a gyors és hatékony támadásra való reagálásban. Azonosítsa a szervezeten belüli azon kapcsolattartókat, akik mind a tervezést, mind a végrehajtást felügyelik. Ez a DDoS-válaszcsapat alaposan meg kell ismernie az Azure DDoS Protection Standard szolgáltatás. Győződjön meg arról, hogy a csapat azonosítani és enyhíteni a támadást a belső és külső ügyfelekkel, köztük a Microsoft támogatási csapatával való egyeztetéssel.

A DDoS-válaszcsapat számára azt javasoljuk, hogy a szolgáltatás rendelkezésre állásának és folytonosságának megtervezéséhez használjon szimulációs gyakorlatokat. Ezeknek a gyakorlatoknak tartalmazniuk kell a skálatesztelést.

### <a name="alerts-during-an-attack"></a>Riasztások támadás közben

Az Azure DDoS Protection Standard azonosítja és csökkenti a DDoS-támadások felhasználói beavatkozás nélkül. Ha értesítést szeretne kapni, ha egy védett nyilvános IP-cím aktív kockázatcsökkentést kap, [konfigurálhat egy riasztást](/azure/virtual-network/ddos-protection-manage-portal) a **DDoS-támadás alatti**metrika alapján, vagy sem. Választhat, hogy riasztásokat hozzon létre a többi DDoS-metrikák a támadás méretének megértéséhez, a forgalom eldobása és egyéb részletek.

#### <a name="when-to-contact-microsoft-support"></a>Mikor kell kapcsolatba lépni a Microsoft támogatási szolgálatával?

- DDoS-támadás során azt találja, hogy a védett erőforrás teljesítménye súlyosan leromlott, vagy az erőforrás nem érhető el.

- Úgy gondolja, hogy a DDoS-védelem szolgáltatás nem a várt módon viselkedik. 

  A DDoS Protection szolgáltatás csak akkor indítja el a kockázatcsökkentést, ha a **DDoS-kockázatcsökkentést kiváltó metrikaérték-házirend (TCP/TCP SYN/UDP)** alacsonyabb, mint a védett nyilvános IP-erőforráson kapott forgalom.

- Olyan vírusos eseményt tervez, amely jelentősen növeli a hálózati forgalmat.

- Egy szereplő azzal fenyegetőzött, hogy ddos-támadást indít az erőforrásai ellen.

- Ha engedélyeznie kell egy IP-vagy IP-tartomány t az Azure DDoS Protection Standard. Gyakori forgatókönyv, hogy engedélyezi a lista IP-cím, ha a forgalom egy külső felhőwaf-ből az Azure-ba. 

A kritikus üzleti hatással lévő támadások esetén hozzon létre egy súlyosság-A [támogatási jegyet.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="post-attack-steps"></a>Támadás utáni lépések

Ez mindig egy jó stratégia, hogy csinál egy postmortem támadás után, és állítsa be a DDoS válasz stratégia szükség szerint. Megfontolandó dolgok:

- Volt-e zavar a szolgáltatás vagy a felhasználói élmény hiánya miatt a skálázható architektúra?

- Mely alkalmazások vagy szolgáltatások szenvedtek a legtöbbet?

- Mennyire volt hatékony a DDoS válaszstratégia, és hogyan lehetne javítani?

Ha azt gyanítja, hogy DDoS-támadás alatt áll, a szokásos Azure-támogatási csatornákon keresztül.

## <a name="ddos-protection-reference-architectures"></a>DDoS Protection referenciaarchitektúrák

A DDoS Protection Standard [olyan szolgáltatásokhoz készült, amelyek virtuális hálózatban vannak telepítve.](/azure/virtual-network/virtual-network-for-azure-services) Más szolgáltatások esetén az alapértelmezett DDoS Protection Basic szolgáltatás érvényes. A következő referenciaarchitektúrák forgatókönyvek szerint vannak elrendezve, az architektúraminták at csoportosítva.

### <a name="virtual-machine-windowslinux-workloads"></a>Virtuális gép (Windows/Linux) munkaterhelései

#### <a name="application-running-on-load-balanced-vms"></a>Terheléselosztásos virtuális gépeken futó alkalmazás

Ez a referenciaarchitektúra egy bevált gyakorlatot mutat be több Windows virtuális gép futtatásához egy terheléselosztó mögötti méretezési csoportban a rendelkezésre állás és a méretezhetőség javítása érdekében. Ez az architektúra használható bármilyen állapot nélküli számítási feladatok, például egy webkiszolgáló.

![A terheléselosztásos virtuális gépeken futó alkalmazás referenciaarchitektúrájának diagramja](./media/ddos-best-practices/image9.png)

Ebben az architektúrában a számítási feladatok több virtuálisgép-példányon oszlanak el. Egyetlen nyilvános IP-cím van, és az internetes forgalom a virtuális gépek egy terheléselosztón keresztül oszlik meg. A DDoS Protection Standard engedélyezve van az Azure (internet) terheléselosztó virtuális hálózatán, amelyhez a nyilvános IP-cím társítva van.

A terheléselosztó a bejövő internetes kérelmeket a virtuálisgép-példányok között osztja el. A virtuálisgép-méretezési készletek lehetővé teszik a virtuális gépek manuális vagy manuális méretezését, vagy automatikusan az előre meghatározott szabályok alapján. Ez akkor fontos, ha az erőforrás DDoS-támadás alatt áll. Erről a referenciaarchitektúráról ebben a cikkben olvashat [bővebben.](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)

#### <a name="application-running-on-windows-n-tier"></a>Windows N-szinten futó alkalmazás

Egy N szintű architektúra számos módon implementálható. Az alábbi ábrán egy tipikus háromrétegű webalkalmazás látható. Ez az architektúra a [méretezhetőség és a rendelkezésre állás érdekében a terheléselosztásos virtuális gépek futtatása](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)című cikkre épül. A webes és üzleti szintek elosztott terhelésű virtuális gépeket használnak.

![A Windows N-rétegen futó alkalmazások referenciaarchitektúrájának diagramja](./media/ddos-best-practices/image10.png)

Ebben az architektúrában a DDoS Protection Standard engedélyezve van a virtuális hálózaton. A virtuális hálózat összes nyilvános IP-kiszolgálója DDoS-védelmet kap a 3. A Layer 7 védelem hez telepítse az Application Gateway alkalmazást a WAF termékváltozatban. Erről a referenciaarchitektúráról ebben a cikkben olvashat [bővebben.](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)

#### <a name="paas-web-application"></a>PaaS webalkalmazás

Ez a referenciaarchitektúra egy Azure App Service-alkalmazás egyetlen régióban futtatását mutatja. Ez az architektúra az Azure App [Service-t](https://azure.microsoft.com/documentation/services/app-service/) és az [Azure SQL Database-t](https://azure.microsoft.com/documentation/services/sql-database/)használó webalkalmazások bevált gyakorlatait jeleníti meg.
Feladatátvételi forgatókönyvekhez készenléti terület van beállítva.

![PaaS-webalkalmazás referenciaarchitektúrájának diagramja](./media/ddos-best-practices/image11.png)

Az Azure Traffic Manager a bejövő kérelmeket az egyik régióban irányítja az Application Gateway-hez. Normál műveletek során az aktív régióban lévő Application Gateway-hez irányítja a kérelmeket. Ha ez a régió elérhetetlenné válik, a Traffic Manager átadja a rendszera készenléti régióban az Application Gateway-nek.

Az internetről a webalkalmazásba irányuló összes forgalom az [Application Gateway nyilvános IP-címére](/azure/application-gateway/application-gateway-web-app-overview) kerül a Traffic Manageren keresztül. Ebben a forgatókönyvben az alkalmazásszolgáltatás (webapp) maga nem közvetlenül külsőleg, és az Application Gateway által védett. 

Azt javasoljuk, hogy konfigurálja az Application Gateway WAF Termékváltozat (prevent mód) a Layer 7 (HTTP/HTTPS/WebSocket) támadások elleni védelem. Emellett a webalkalmazások úgy vannak konfigurálva, hogy csak az Application Gateway IP-címéről [érkező forgalmat fogadják el.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)

Erről a referenciaarchitektúráról ebben a cikkben olvashat [bővebben.](/azure/architecture/reference-architectures/app-service-web-app/multi-region)

### <a name="mitigation-for-non-web-paas-services"></a>A nem webes PasaS-szolgáltatások enyhítése

#### <a name="hdinsight-on-azure"></a>HDInsight az Azure-ban

Ez a referenciaarchitektúra a DDoS Protection Standard konfigurálását mutatja be egy [Azure HDInsight-fürthöz.](/azure/hdinsight/) Győződjön meg arról, hogy a HDInsight-fürt egy virtuális hálózathoz van csatolva, és hogy a DDoS Protection engedélyezve van a virtuális hálózaton.

!["HDInsight" és "Speciális beállítások" ablaktáblák, virtuális hálózati beállításokkal](./media/ddos-best-practices/image12.png)

![A DDoS-védelem engedélyezéséhez kijelölt kijelölés](./media/ddos-best-practices/image13.png)

Ebben az architektúrában a HDInsight-fürtre irányuló, az internetről érkező forgalmat a HDInsight átjáró terheléselosztóhoz tartozó nyilvános IP-címhez irányítja. Az átjáró terheléselosztó ezután elküldi a forgalmat a fő csomópontok vagy a feldolgozó csomópontok közvetlenül. Mivel a DDoS Protection Standard engedélyezve van a HDInsight virtuális hálózaton, a virtuális hálózat összes nyilvános IP-kiszolgálója DDoS-védelmet kap a 3. Ez a referenciaarchitektúra kombinálható az N-réteg és a többrégiós referenciaarchitektúrákkal.

A referenciaarchitektúráról további információt az Azure HDInsight kiterjesztése az Azure virtuális hálózati dokumentáció [használatával című témakörben talál.](/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json)


> [!NOTE]
> Az Azure App Service Environment for PowerApps vagy az API-kezelés nyilvános IP-című virtuális hálózatokban nem natív módon támogatott.

## <a name="next-steps"></a>További lépések

* [Megosztott feladatkörök a felhőben](shared-responsibility.md)

* [Az Azure DDoS Protection termékoldala](https://azure.microsoft.com/services/ddos-protection/)

* [Az Azure DDoS Protection dokumentációja](/azure/virtual-network/ddos-protection-overview)
