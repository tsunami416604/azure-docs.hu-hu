---
title: Biztonsági riasztások a Azure Security Centerban | Microsoft Docs
description: Ez a témakör ismerteti a biztonsági riasztásokat, valamint a Azure Security Centerban elérhető különböző típusokat.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 3b4b02574c028822d25d841376b127a718243b2e
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202558"
---
# <a name="security-alerts-in-azure-security-center"></a>Biztonsági riasztások az Azure Security Centerben

A Azure Security Center számos különböző típusú erőforráshoz biztosít riasztásokat. A Security Center riasztásokat hoz létre az Azure-ban üzembe helyezett erőforrásokról, valamint a helyszíni és hibrid felhőalapú környezetekben üzembe helyezett erőforrásokról is.

A speciális észlelési funkciók az Azure Security Center Standard rétegben érhetők el. Ennek létezik egy ingyenesen elérhető próbaverziója. A választott tarifacsomagról a [Biztonsági szabályzat](security-center-pricing.md) beállításainál frissíthet. A díjszabással kapcsolatos további tudnivalókért keresse fel a [Security Center oldalát](https://azure.microsoft.com/pricing/details/security-center/).

## Válaszadás a mai fenyegetésekre <a name="respond-threats"></a>

Az elmúlt 20 évben jelentős változásokat figyelhettünk meg a fenyegetések területén. Korábban a vállalatoknak általában csak a webhelyük arculatának lerombolása miatt kellett aggódniuk, mert a támadókat főleg az érdekelte, hogy kipróbálják, „mit is lehetne tenni”. A mai támadók sokkal bonyolultabban és szervezettebben cselekszenek. Gyakran konkrét pénzügyi és stratégiai célokat követnek. Ezenkívül több erőforrás áll a rendelkezésükre, mert akár az is előfordulhat, hogy egy nemzetállam vagy a szervezett bűnözés biztosítja számukra az anyagi hátteret.

Ezek a változó realitások a támadó soraiban példátlan szintű professzionalizmust eredményeztek. Már nem a webhelyek megrongálása érdekli őket. Most már érdeklik az információk, a pénzügyi fiókok és a személyes adatok ellopása, amelyek segítségével készpénzt hozhatnak a nyílt piacon, vagy egy adott üzleti, politikai vagy katonai pozíciót is kihasználhatnak. Még nagyobb aggodalomra ad okot, hogy a pénzügyi célokat követő támadókon kívül olyanokkal is számolni kell, akik azért törnek be a hálózatokba, hogy kárt okozzanak az infrastruktúrában és a felhasználóknak.

Erre reagálva a szervezetek gyakran olyan adott pontokon működő megoldásokat alkalmaznak, amelyek a vállalat peremsávját vagy végpontjait védelmezik a támadások ismert jelei után kutatva. Ezek a megoldások általában nagy mennyiségű, alacsony megbízhatósági szintű riasztást eredményeznek, és ezeket a biztonsági elemzőknek szét kell válogatniuk és ki kell vizsgálniuk. A legtöbb szervezetnek nincs ideje és megfelelő szaktudása ahhoz, hogy reagáljon ezekre a riasztásokra, ezért sok probléma megoldatlan marad.  

Emellett a támadók kifejlődték a módszereiket számos aláírás-alapú védelem kialakulásához, és [alkalmazkodnak a felhőalapú környezetekhez](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Az új módszerek a fenyegetések egyre bővülő körének gyorsabb felismerését, azonnali észlelését és kiiktatását igénylik.

## <a name="what-are-security-alerts"></a>Mik azok a biztonsági riasztások?

A riasztások a Security Center által generált értesítések, amikor észlelik a fenyegetéseket az erőforrásokon. A Security Center rangsorolja és felsorolja a riasztásokat, valamint azokat az információkat, amelyek a probléma gyors kivizsgálásához szükségesek. A Security Center javaslatokat is tartalmaz a támadások megoldásához.

## Hogyan ismeri fel Security Center a fenyegetéseket? <a name="detect-threats"> </a>

A Microsoft biztonsági kutatói folyamatosan figyelik a megjelenő fenyegetéseket. A Microsoft globális jelenléte a felhőben és a helyszínen egyaránt a telemetria expanzív készletéhez férhet hozzá. Az adatkészletek széles körű és változatos gyűjteménye lehetővé teszi, hogy az új támadási mintákat és trendeket a helyszíni fogyasztói és nagyvállalati termékekben, valamint a online szolgáltatásokon is felfedezzék. Ennek eredményeképpen a Security Center gyorsan tudja frissíteni az észlelési algoritmusait, igazodva a támadók újabb és egyre összetettebb biztonságirés-kihasználási megoldásaihoz. Ez a módszer segít lépést tartani a fenyegetések gyors ütemben növekvő körével.

A valós fenyegetések észlelése és a téves pozitív állapotok csökkentése érdekében Security Center gyűjti, elemzi és integrálja az Azure-erőforrások és a hálózat naplófájljait. Együttműködik továbbá a csatlakoztatott partneri megoldásokkal, például a tűzfal-és Endpoint Protection-megoldásokkal. Security Center elemzi ezeket az információkat, és gyakran korrelál több forrásból származó információkat a fenyegetések azonosítása érdekében.

![A Security Center adatgyűjtése és adatábrázolása](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

A Security Center olyan fejlett biztonsági elemzéseket alkalmaz, amelyek messze túlmutatnak az aláírás-alapú megközelítéseken. A big data és a [gépi tanulási](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technológiák áttörést jelentő eredményeinek felhasználásával értékelhetők ki az események a teljes felhőalapú hálóban, és így olyan fenyegetések is észlelhetők, amelyeket manuális módszerekkel lehetetlen volna azonosítani, és lehetőség van a támadások fejlődésének előrejelzésére. Ezek a biztonsági elemzések a következők:

* **Integrált veszélyforrások felderítése**: A Microsoft-termékek és-szolgáltatások, a Microsoft Digital Crimes Unit (DCU), a Microsoft Security Response Center (MSRC) és a külső hírcsatornák globális fenyegetési intelligenciát hasznosító ismert hibás szereplők.
* **Viselkedési elemzés**: Az ismert mintákat alkalmazza a kártékony viselkedés felderítésére.
* **Anomáliák észlelése**: Statisztikai profilkészítést használ egy korábbi alapterv létrehozásához. Jelzi a meghatározott alapkonfigurációktól való olyan eltéréseket, amelyek potenciális támadásként értelmezhetők.

A következő témakörök részletesebben tárgyalják ezeket az elemzéseket.

### <a name="integrated-threat-intelligence"></a>Integrált veszélyforrások felderítése

A Microsoft rendkívül nagy mennyiségű adattal rendelkezik a globális fenyegetésészlelési intelligencia keretein belül. Telemetriai adatok gyűjt több forrásból, ilyen például az Azure, az Office 365, a Microsoft CRM online, a Microsoft Dynamics AX, az outlook.com, az MSN.com, a Microsoft Digital Crimes Unit (DCU) és a Microsoft Security Response Center (MSRC). A kutatók ezenkívül olyan adatokat is kapnak a fenyegetésészleléshez, amelyeket a fő felhőszolgáltatók osztanak meg, és előfizetnek a harmadik felek fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra is. Az Azure Security Center mindezeket az adatokat fel tudja használni arra, hogy értesítse Önt az ismert kártékony elemektől eredő fenyegetésekről.

### <a name="behavioral-analytics"></a>Működés elemzése

A működés elemzése olyan módszer, amely megvizsgálja és összehasonlítja az adatokat az ismert minták gyűjteményével. Ezek a minták azonban nem csak egyszerű aláírások. Meghatározásuk hatalmas adatkészletekre alkalmazott összetett gépi tanulási algoritmusokkal történt. Ezenkívül szakértő elemzők mélyrehatóan elemezték a kártékony működést a meghatározásukhoz. Az Azure Security Center a működés elemzésével tudja azonosítani a feltört erőforrásokat a virtuális gépek naplóinak, a virtuális hálózati eszközök naplóinak, a háló naplóinak, az összeomlási memóriaképeknek és az egyéb forrásoknak az elemzésével.

Ezenkívül megvizsgálja az összefüggéseket más jelekkel, hogy alátámassza a nagy lépétkű kampányok bizonyítékait. Az összefüggések felderítése segít azonosítani a feltörés meghatározott tüneteit mutató eseményeket. 

### <a name="anomaly-detection"></a>Anomáliadetektálás

Az Azure Security Center további módszere a fenyegetések felderítésére a rendellenességek észlelése. A működés elemzésével (amely a nagy adatkészletekből kinyert ismert mintákon alapul) ellentétben a rendellenességek észlelése „testre szabottabb”, és az üzemelő példányokhoz tartozó alapkonfigurációkra összpontosít. Ez a módszer gépi tanulás alkalmazásával felméri az üzemelő példányok normál tevékenységeit, majd szabályokat hoz létre az olyan rendkívüli körülmények meghatározásához, amelyek a biztonságot érintő eseményre utalhatnak.

## <a name="continuous-monitoring-and-assessments"></a>Folyamatos monitorozás és értékelések

Azure Security Center a biztonsági kutatási és adatelemzési csapatokból származó előnyöket a Microsoftnál, akik folyamatosan figyelik a fenyegetések tájképének változásait. Ide tartoznak a következők:

* **Fenyegetési intelligencia figyelése**: A fenyegetések intelligencia a meglévő vagy újonnan felmerülő fenyegetésekkel kapcsolatos mechanizmusokat, indikátorokat, következményeket és gyakorlati tanácsokat tartalmaz. Ez az információ elérhető a biztonsági közösség számára, és a Microsoft folyamatosan figyeli a fenyegetésekre vonatkozó intelligencia belső és külső forrásból származó hírcsatornáit.
* **Jel megosztása**: A Microsoft széles köre a Felhőbeli és a helyszíni szolgáltatások, a kiszolgálók és az ügyfél végponti eszközeinek megosztására és elemzésére szolgáló biztonsági csapatokkal kapcsolatos információkat.
* **Microsoft biztonsági szakemberek**: Folyamatos együttműködés a Microsoft különböző csoportjaival, amelyek speciális biztonsági mezőkben működnek, mint például a kriminalisztika és a webes támadás észlelése.
* **Észlelés finomhangolása**: Az algoritmusok valós ügyfél-adatkészleteken futnak, és a biztonsági kutatók az ügyfelekkel együttműködve érvényesítik az eredményeket. Az igazi és a téves találatok megjelölésével pontosítják a gépi algoritmusokat.

Ezek az egyesített erőfeszítések új és hatékonyabb észleléseket eredményeznek, amelyeknek azonnal hasznát veheti anélkül, hogy bármit is tennie kellene.

## Biztonsági riasztások <a name="security-alert-types"></a> típusai

Az alábbi témakörök a különböző riasztásokat ismertetik az erőforrástípusok szerint:

* [IaaS virtuális gépek és kiszolgálók riasztásai](security-center-alerts-iaas.md)
* [Natív számítási riasztások](security-center-alerts-compute.md)
* [Adatszolgáltatási riasztások](security-center-alerts-data-services.md)

A következő témakörök azt ismertetik, hogyan használják a Security Center az Azure-infrastruktúrával való integrálás során gyűjtött különböző telemetria, hogy az Azure-ban üzembe helyezett erőforrások további védelmi rétegeit is alkalmazni lehessen:

* [Szolgáltatási réteggel kapcsolatos riasztások](security-center-alerts-service-layer.md)
* [Integráció az Azure Security-termékekkel](security-center-alerts-integration.md)

## <a name="what-are-security-incidents"></a>Mik azok a biztonsági incidensek?

A biztonsági incidensek a kapcsolódó riasztások gyűjteményei, az egyes riasztások egyenkénti listázása helyett. A Security Center a [Felhőbeli intelligens riasztások korrelációját](security-center-alerts-cloud-smart.md) használja a különböző riasztások és az alacsony megbízhatósági jelzések biztonsági incidensekre való összekapcsolásához.

Az incidensek használatával a Security Center egyetlen nézetet biztosít a támadási kampányokról és az összes kapcsolódó riasztásról. Ez a nézet lehetővé teszi, hogy gyorsan megértse, milyen műveleteket tartott a támadó, és milyen erőforrásokat érintettek. További információ: [felhőalapú intelligens riasztás korrelációja](security-center-alerts-cloud-smart.md).

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, milyen típusú riasztások érhetők el a Security Centerban. További információkért lásd:

* [Útmutató az Azure Security Center tervezéséhez és működtetéséhez](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)
* [Azure Security Center – gyakori kérdések](https://docs.microsoft.com/azure/security-center/security-center-faq)

