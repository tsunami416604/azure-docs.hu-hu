---
title: Biztonsági riasztások az Azure Security Centerben | Microsoft dokumentumok
description: Ez a témakör ismerteti, hogy mik a biztonsági riasztások, és milyen különböző típusú elérhető az Azure Security Centerben.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 697c038a2fefdde8e488dad23a4e38e0b2b7b288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415850"
---
# <a name="security-alerts-in-azure-security-center"></a>Biztonsági riasztások az Azure Security Centerben

Az Azure Security Center számos különböző erőforrástípushoz számos riasztást. A Security Center riasztásokat hoz létre az Azure-ban üzembe helyezett erőforrásokhoz, valamint a helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokhoz.

A biztonsági riasztásokat speciális észlelések váltják ki, és csak az Azure Security Center standard szintű szintjein érhetők el. Ennek létezik egy ingyenesen elérhető próbaverziója. A választott tarifacsomagról a [Biztonsági szabályzat](security-center-pricing.md) beállításainál frissíthet. A díjszabással kapcsolatos további tudnivalókért keresse fel a [Security Center oldalát](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="responding-to-todays-threats"></a>Válasz a mai fenyegetésekre <a name="respond-threats"> </a>

Az elmúlt 20 évben jelentős változásokat figyelhettünk meg a fenyegetések területén. A múltban a vállalatoknak jellemzően csak a weboldal defacement miatt kellett aggódniuk az egyes támadók által, akiket leginkább az érdekelt, hogy "mit tehetnének". A mai támadók sokkal kifinomultabbak és szervezettebbek. Gyakran konkrét pénzügyi és stratégiai célokat követnek. Ezenkívül több erőforrás áll a rendelkezésükre, mert akár az is előfordulhat, hogy egy nemzetállam vagy a szervezett bűnözés biztosítja számukra az anyagi hátteret.

Ezek a változó valóságok vezettek példátlan szintű professzionalizmus a támadó soraiban. Már nem a webhelyek megrongálása érdekli őket. Most már az információk, pénzügyi számlák és személyes adatok ellopása érdekli őket – amelyek mindegyikét felhasználhatják készpénz előállítására a nyílt piacon, vagy egy adott üzleti, politikai vagy katonai pozíció kihasználására. Még nagyobb aggodalomra ad okot, hogy a pénzügyi célokat követő támadókon kívül olyanokkal is számolni kell, akik azért törnek be a hálózatokba, hogy kárt okozzanak az infrastruktúrában és a felhasználóknak.

Erre reagálva a szervezetek gyakran olyan adott pontokon működő megoldásokat alkalmaznak, amelyek a vállalat peremsávját vagy végpontjait védelmezik a támadások ismert jelei után kutatva. Ezek a megoldások általában nagy mennyiségű, alacsony megbízhatósági szintű riasztást eredményeznek, és ezeket a biztonsági elemzőknek szét kell válogatniuk és ki kell vizsgálniuk. A legtöbb szervezetnek nincs ideje és megfelelő szaktudása ahhoz, hogy reagáljon ezekre a riasztásokra, ezért sok probléma megoldatlan marad.  

Emellett a támadók kidolgozták módszereiket, hogy aláaknázzanak számos aláírás-alapú védelmet, és [alkalmazkodjanak a felhőalapú környezetekhez.](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/) Az új módszerek a fenyegetések egyre bővülő körének gyorsabb felismerését, azonnali észlelését és kiiktatását igénylik.

## <a name="what-are-security-alerts-and-security-incidents"></a>Mik azok a biztonsági riasztások és biztonsági incidensek? 

**A riasztások** azok az értesítések, amelyeket a Security Center akkor hoz létre, amikor az erőforrásokat fenyegető veszélyeket észlel. A Security Center rangsorolja és felsorolja a riasztásokat, valamint a probléma gyors kivizsgálásához szükséges információkat. A Security Center javaslatokat is felkínál a támadás elhárítására.

**A biztonsági incidens** a kapcsolódó riasztások gyűjteménye, ahelyett, hogy minden egyes riasztást külön-külön listázna. A Security Center [a Cloud Smart Alert Korreláció](security-center-alerts-cloud-smart.md) segítségével korrelálja a különböző riasztásokat és az alacsony hűségű jeleket a biztonsági incidensekben.

Az incidensek használatával a Security Center egyetlen nézetet biztosít a támadási kampányról és az összes kapcsolódó riasztásról. Ez a nézet lehetővé teszi, hogy gyorsan megértse, milyen műveleteket végzett a támadó, és milyen erőforrásokat érintett. További információ: [Cloud smart alert korreláció](security-center-alerts-cloud-smart.md).



## <a name="how-does-security-center-detect-threats"></a>Hogyan észleli a Security Center a fenyegetéseket? <a name="detect-threats"> </a>

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. A Microsoft globális jelenléte a felhőben és a helyszíni, hozzáférhetnek egy kiterjedt telemetriai készlet. Az adatkészletek széles körű és változatos gyűjteménye lehetővé teszi az új támadási minták és trendek felfedezését a helyszíni fogyasztói és vállalati termékekben, valamint az online szolgáltatásokban. Ennek eredményeképpen a Security Center gyorsan tudja frissíteni az észlelési algoritmusait, igazodva a támadók újabb és egyre összetettebb biztonságirés-kihasználási megoldásaihoz. Ez a módszer segít lépést tartani a fenyegetések gyors ütemben növekvő körével.

A valós fenyegetések észlelése és a hamis pozitív eredmények csökkentése érdekében a Security Center összegyűjti, elemzi és integrálja az Azure-erőforrásokból és a hálózatból származó naplóadatokat. Kapcsolódó partnermegoldásokkal is működik, például tűzfal- és végpontvédelmi megoldásokkal. A Security Center elemzi ezeket az információkat, gyakran több forrásból származó információkat összevetve, hogy azonosítsa a fenyegetéseket.

![A Security Center adatgyűjtése és adatábrázolása](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák áttörést jelentő eredményeinek felhasználásával értékelhetők ki az események a teljes felhőalapú hálóban, és így olyan fenyegetések is észlelhetők, amelyeket manuális módszerekkel lehetetlen volna azonosítani, és lehetőség van a támadások fejlődésének előrejelzésére. Ezek a biztonsági elemzések a következők:

* **Integrált fenyegetésfelderítés:** A Microsoft hatalmas mennyiségű globális fenyegetésfelderítéssel rendelkezik. A telemetria több forrásból, például az Azure-ból, az Office 365-ből, a Microsoft CRM online-ból, a Microsoft Dynamics AX-ből, a outlook.com-ból, a MSN.com-ból, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Centerből (MSRC) áramlik be. A kutatók olyan fenyegetésfelderítési információkat is kapnak, amelyeket megosztanak a főbb felhőszolgáltatók és más harmadik felek hírcsatornái között. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről.

* **Viselkedési elemzés:** A viselkedéselemzés olyan technika, amely elemzi és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt. Ezenkívül szakértő elemzők mélyrehatóan elemezték a kártékony működést a meghatározásukhoz. Az Azure Security Center viselkedéselemzéssel azonosíthatja a feltört erőforrásokat a virtuálisgép-naplók, a virtuális hálózati eszköznaplók, a hálónaplók, az összeomlási memóriaképek és más források elemzése alapján.

* **Anomáliadetektálás:** Az Azure Security Center anomáliadetektálást is használ a fenyegetések azonosítására. A viselkedéselemzéssel ellentétben (amely a nagy adatkészletekből származó ismert mintáktól függ), az anomáliadetektálás "személyre szabottabb", és az üzembe helyezésekhez jellemző alapkonfigurációkra összpontosít. Ez a módszer gépi tanulás alkalmazásával felméri az üzemelő példányok normál tevékenységeit, majd szabályokat hoz létre az olyan rendkívüli körülmények meghatározásához, amelyek a biztonságot érintő eseményre utalhatnak.

## <a name="how-are-alerts-classified"></a>Hogyan osztályozzák a riasztásokat?

A Security Center súlyossági szintet rendel a riasztásokhoz, hogy segítsen rangsorolni az egyes riasztások hozadékát, így ha egy erőforrás biztonsága veszélybe kerül, azonnal hozzáérhet. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

> [!NOTE]
> A riasztás súlyossága eltérően jelenik meg a portálon és a REST API 01-01-2019 előtti verzióiban. Ha az API egy régebbi verzióját használja, frissítse ni az alábbiakban ismertetett konzisztens élményhez.

- **Magas:** Nagy a valószínűsége annak, hogy az erőforrás sérült. Azonnal utána kellene nézned. A Security Center nagy bizalommal rendelkezik mind a rosszindulatú szándék, mind a riasztás kiadásához használt megállapítások iránt. Például egy riasztást, amely észleli egy ismert rosszindulatú eszköz, például a Mimikatz, a hitelesítő adatok ellopásával használt közös eszköz végrehajtását.
- **Közepes:** Ez valószínűleg egy gyanús tevékenység azt jelezheti, hogy egy erőforrás biztonsága sérül.
Security Center bizalmát az analitikus vagy megállapítás közepes és a bizalom a rosszindulatú szándék közepes és magas. Ezek általában gépi tanulás vagy anomália alapú észlelések. Például egy bejelentkezési kísérlet egy rendellenes helyről.
- **Alacsony:** Ez lehet egy jóindulatú pozitív vagy blokkolt támadás.
   * A Security Center nem elég magabiztos abban, hogy a szándék rosszindulatú, és a tevékenység ártatlan lehet. A napló törléspéldául olyan művelet, amely akkor fordulhat elő, amikor a támadó megpróbálja elrejteni a sávjait, de sok esetben a rendszergazdák által végrehajtott rutinművelet.
   * A Biztonsági Központ általában nem mondja meg, hogy mikor blokkolták a támadásokat, kivéve, ha ez egy érdekes eset, amelyet javasoljuk, hogy nézzen utána. 
- **Tájékoztató:** Csak akkor jelennek meg tájékoztató riasztások, ha egy biztonsági incidens, vagy ha a REST API-t egy adott riasztási azonosítóval használja. Az incidens általában több riasztásból áll, amelyek közül néhány önmagában csak tájékoztató jellegűnek tűnhet, de a többi figyelmeztető jelzés összefüggésében érdemes lehet közelebbről megvizsgálni. 

## <a name="continuous-monitoring-and-assessments"></a>Folyamatos nyomon követés és értékelések

Az Azure Security Center számára előnyös, hogy a Microsoft biztonsági kutatási és adatelemzési csapatai folyamatosan figyelik a fenyegetési környezet változásait. Ide tartoznak a következők:

* **Fenyegetésfelderítésfigyelés:** A fenyegetésfelderítés mechanizmusokat, mutatókat, következményeket és a meglévő vagy újonnan megjelenő fenyegetésekre vonatkozó végrehajtható tanácsokat tartalmaz. Ez az információ elérhető a biztonsági közösség számára, és a Microsoft folyamatosan figyeli a fenyegetésekre vonatkozó intelligencia belső és külső forrásból származó hírcsatornáit.
* **Jelmegosztás:** A microsoftfelhői és helyszíni szolgáltatások, kiszolgálók és ügyfélvégpont-eszközök széles portfóliójában a biztonsági csapatok tól származó elemzéseket megosztják és elemzik.
* **A Microsoft biztonsági szakértői**: folyamatos kapcsolatot tartunk a Microsoft csapataival, amelyek tagjai meghatározott biztonsági szakterületekkel foglalkoznak, például igazságügyi szakértői tevékenységekkel és a webes támadások észlelésével.
* **Az észlelés finomhangolása**: a biztonsági szakértők az ügyfelek valós adatkészletein futtatják az algoritmusokat, és az ügyfelekkel együttműködve ellenőrzik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az együttes erőfeszítések új és továbbfejlesztett észlelésekben csúcsosodnak ki, amelyek azonnal profitálhatnak belőle – nincs mit tennie.


## <a name="next-steps"></a>További lépések

Ebben a cikkben a Security Centerben elérhető különböző típusú riasztásokról szerzett tudomást. További információkért lásd:

* [Fenyegetésvédelem az Azure Security Centerben](threat-protection.md) – Az Azure Security Center által megjelenített biztonsági riasztások forrásainak rövid ismertetője 
* **Biztonsági riasztások az Azure-tevékenységnaplóban** – Amellett, hogy elérhető az Azure Portalon vagy programozott módon, a biztonsági riasztások és incidensek eseményekként vannak naplózva az [Azure-tevékenységnaplóban.](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) Az eseménysémáról további információt a [Biztonsági riasztások az Azure-tevékenységnaplóban című témakörben](https://go.microsoft.com/fwlink/?linkid=2114113) talál.

