---
title: Bevezetés az Azure Security |} A Microsoft Docs
description: Ismerje meg az Azure Security, a szolgáltatások és működését.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: c66f0e67b09dab15431a8c7c10db1c820038dea6
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984985"
---
# <a name="introduction-to-azure-security"></a>Bevezetés az Azure Security
## <a name="overview"></a>Áttekintés
Tudjuk, hogy biztonsági-e a feladatot a felhőben, és hogy mennyire fontos, hogy látja-e az Azure security pontos és időben információt. Használhatja az Azure-alkalmazásokat és szolgáltatásokat a legjobb okok egyike miatt, a biztonsági eszközök és funkciók széles választékának előnyeinek kihasználása érdekében. A következő eszközök és képességek segítségével biztonságos megoldásokat hozhat létre a biztonságos Azure-platformon lehetővé teszik, hogy. Microsoft Azure lehetőséget kínál a bizalmas, integritás és rendelkezésre állását a vásárlói adatokat, átlátható accountability is engedélyezése közben.

Segítségével jobban megismerheti a gyűjteményben biztonsági szemszögből a vevő és a Microsoft operations, ez a tanulmány, "Bevezetés az Azure Security", a Microsoft Azure-ban megvalósított írt átfogó képet nyújt elérhető a Microsoft Azure biztonsági.

### <a name="azure-platform"></a>Azure Platform
Az Azure egy nyilvános felhőszolgáltatás-platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles skálájából el. Azt is Linux-tárolókat futtathat Docker-integrációval; a JavaScript, Python, .NET, PHP, Java és Node.js-alkalmazások készítéséhez háttérrendszereket készíthet iOS, Android és Windows eszközökhöz.

Azure nyilvános felhőbeli szolgáltatásaival támogatja technológiákat a fejlesztők és informatikai szakemberek számára már elnyerték. Amikor épülnek, vagy át az IT-eszközeivel, nyilvános felhőszolgáltató védelme érdekében az alkalmazások és adatok a szolgáltatások és a vezérlők annak a szervezetnek képességekhez hagyatkoznia nyújtanak kezelheti a felhőalapú eszközeinek biztonságát.

Az Azure infrastruktúráját úgy alakítottuk létesítményből származó alkalmazásokat képes legyen ügyfelek millióit egyidejűleg üzemeltetni, és olyan megbízható alapot, amely megfelel a vállalatok a biztonsági követelményeket biztosít.

Emellett az Azure biztosít számos konfigurálható biztonsági beállítások és irányítása őket, hogy a szervezet üzemelő példányok egyedi követelményeinek megfelelő biztonsági testre szabhatja. Ebből a dokumentumból megismerheti az Azure biztonsági eljárásainak megértését képességek segítségével ezek a követelmények teljesítéséhez.

> [!Note]
> Ez a dokumentum elsődleges célja van a ügyfél felé irányuló szabályozza, amely segítségével testre szabhatja, és biztonságosabbá teszi az alkalmazásokat és szolgáltatásokat.
>
> Azt adja meg az egyes áttekintő információkat, de hogyan teszi biztonságossá a Microsoft az Azure platform magát a részletes információkért lásd: a információk a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Absztrakt
Kezdetben a migrálás a nyilvános felhőbe is hátterében a költségmegtakarítást és rugalmasságot az innováció. Biztonsági egy ideig, és még egy show zárjuk le, a nyilvános felhőbe való migrálást fő szempont volt tekinthető. Azonban nyilvános felhőbeli biztonsági átváltott a fő szempont az egyik hajtóereje a felhőbe való migrálást. Ez logikája az adatok a felhő alapú eszközök és alkalmazások védelmét nagy nyilvánosfelhő-szolgáltatók kiváló képesség.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Emellett az Azure konfigurálható biztonsági beállítások széles választékának rendelkező és irányítása őket, így testre szabhatja, hogy megfeleljen az informatikai RÉSZLEG az üzembe helyezést egyedi igényeinek megfelelő biztonsági házirendek szabályozzák, és külső tartunk szabályzat.

Ez a tanulmány biztonsági belül a Microsoft Azure felhőalapú platform a Microsoft megközelítést ismerteti:
* Az Azure-infrastruktúra, a vásárlói adatokat és az alkalmazások biztonságossá tételéhez a Microsoft által végrehajtott biztonsági funkciók.
* Azure-szolgáltatások és a szolgáltatások és az adatokat, amelyben az Azure-előfizetések biztonságának kezelését elérhető biztonsági funkciók.

## <a name="summary-azure-security-capabilities"></a>Összefoglalás az Azure biztonsági képességei
A táblázat következő adja meg a biztonsági funkciókat az Azure-infrastruktúra, a vásárlói adatokat és a biztonságos alkalmazások biztonságossá tételéhez a Microsoft által megvalósított rövid leírását.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Biztonsági funkciók biztonságossá tétele az Azure platformon megvalósított:
A következő felsorolt funkciók áttekintheti, hogy biztonságos módon kezeli az Azure Platform biztosítja a képességekre. Hivatkozások a további lehatolást hogyan a Microsoft adatvédelmi kérdések négy területeken címek adtak meg: Biztonságos platformot, adatvédelmi és vezérlők, megfelelőségi és az átláthatóság.


| [Biztonságos platformot](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Adatvédelem és vezérlők](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Megfelelőség](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Átláthatóság](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Biztonsági fejlesztési ciklus](https://www.microsoft.com/en-us/sdl/), belső naplózza | [Folyamatosan-adatok kezelése](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Adatvédelmi központ](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Hogyan teszi biztonságossá a Microsoft az ügyféladatokat az Azure-szolgáltatások](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Kötelező biztonsági képzés, a háttér-ellenőrzések](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Szabályozhatja az adatok helye](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Common Controls Hub](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Hogyan történik a Microsoft adatok helye az Azure-szolgáltatások kezelése](http://azuredatacentermap.azurewebsites.net/)|
| [Behatolásvizsgálat](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [behatolásérzékelési, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [eseményeket és naplózás](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Adatok hozzáférés biztosítása a saját igényei szerint](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [A Cloud Services megfelelő gondossággal ellenőrzőlista](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Milyen feltételekkel, akik a Microsoft is elérhetők az adatok](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [A legkorszerűbb adatközpont](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), fizikai biztonsági [biztonságos hálózati](https://docs.microsoft.com/azure/security/security-network-overview) | [Válaszadás a bűnüldöző hatóság](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Megfelelőség szolgáltatást, helye és iparág szerint](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Hogyan teszi biztonságossá a Microsoft az ügyféladatokat az Azure-szolgáltatások](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Biztonsági incidensmegoldási](https://aka.ms/SecurityResponsepaper), [közös felelősség](https://aka.ms/sharedresponsibility) |[Szigorú adatvédelmi normák](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Tekintse át az Azure-szolgáltatásokhoz, átláthatóság hub hitelesítő](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Adatok és alkalmazások védelmét az Azure által nyújtott biztonsági funkciók
A cloud service modelltől függően nincs változó felelősség kinek a feladata az alkalmazás vagy szolgáltatás biztonságának kezeléséhez. Nincsenek elérhető az Azure Platform, amely segítséget nyújt a teljesíti-e kötelezettségek beépített funkciók révén, és a partneri megoldások, amelyek is telepíthető az Azure-előfizetés keretében képességeket.

A beépített funkciók hat (6) funkcionális területein vannak rendszerezve: Műveletek, alkalmazások, tárolási, hálózati, számítási és identitás. További részleteket az funkciók és képességek elérhető az Azure-platform ezeknek a területeknek hat (6) a szolgáltatáson keresztül összegző információit.

## <a name="operations"></a>Műveletek
Ez a szakasz tartalmazza a legfontosabb jellemzőkkel a biztonsági műveletek kapcsolatos további információkat, és ezek a képességek kapcsolatos összegző információkat.

### <a name="security-and-audit-dashboard"></a>Biztonsági és auditálási irányítópultján
A [biztonsági és auditálási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) nyújt átfogó képet kaphat a szervezet informatikai biztonsági állapotát a [beépített keresési lekérdezések](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) , figyelmet igénylő jelentős problémákat. A [biztonsági és auditálási](https://technet.microsoft.com/library/mt484091.aspx) mindent a Log Analytics biztonsággal kapcsolatos irányítópult a kezdőképernyőn. A számítógépek biztonsági állapotát magas szintű betekintést biztosít. Olyan funkciót is kínál, amellyel megjeleníthető az elmúlt 24 óra, 7 nap vagy bármely más egyéni időszak összes eseménye.

Emellett konfigurálhatja a biztonság és megfelelőség a [automatikusan az adott műveletek végrehajtására](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) egy adott esemény észlelése esetén.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Az Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) lehetővé teszi, hogy az erőforrásokat a megoldás egy csoportként dolgozzon. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. Használhat egy [Azure Resource Manager-sablon](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) az üzembe helyezés és a sablon különböző, például tesztelési, átmeneti és éles környezetben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

Az Azure Resource Manager-sablonalapú üzembe helyezések segítheti a biztonsági megoldások az Azure-ban üzembe helyezett, mert szabványos biztonsági beállításainak kezeléséhez, és képes integrálni kell a szabványos sablonalapú központi telepítések. Ez csökkenti a biztonsági konfigurációs hibák, előfordulhat, hogy végzett, manuális telepítés során kockázatát.

### <a name="application-insights"></a>Application Insights
[Az Application Insights](https://docs.microsoft.com/azure/application-insights/) egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek. Az Application Insights az élő webalkalmazások figyelheti és automatikusan felismeri a teljesítményanomáliákat. Segítséget nyújt a problémák diagnosztizálása és megérteni, milyen felhasználók valójában hogyan használják az alkalmazások hatékony elemzőeszközöket tartalmaz. Az alkalmazás fut, tesztelés és már közzétett vagy azt követően folyamatosan figyeli.

Az Application Insights a diagramok és táblázatok, amelyek bemutatják, például, mely napszakokban kapja a legtöbb felhasználó, hogyan válaszol-e az alkalmazás, és, attól függ, külső szolgáltatások által kiszolgált arról, hogy hoz létre.

Ha összeomlik, sikertelen vagy teljesítménnyel kapcsolatos problémák, a telemetriai adatokat, részletes okát is kereshet. És a szolgáltatás elküldi Önnek e-mailek van-e a rendelkezésre állás és teljesítmény az alkalmazás a módosításokat. Application Insights olyan valuable biztonsági eszköz így lesz, mert a titkosítás, integritás és rendelkezésre állási biztonsági háromrészes a segít a rendelkezésre állással.

### <a name="azure-monitor"></a>Azure Monitor
[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) Vizualizáció, lekérdezés, útválasztás, riasztások, automatikus méretezés és mindkét adatokon automation kínál az Azure-infrastruktúra ([tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)), és minden egyes Azure-erőforrás ([diagnosztikai Naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Az Azure Monitor használatával figyelmeztet a biztonsággal kapcsolatos eseményeket, amelyek akkor jönnek létre, az Azure-naplók.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) – egy IT-felügyeleti megoldást kínál a helyszíni és külső felhő alapú infrastruktúra (például az AWS) Azure-erőforrások mellett is. Adatokat az Azure Monitor átirányíthatók közvetlenül a Log Analytics szolgáltatásba, így a teljes környezet egy helyen láthatja metrikák és naplók.

A log Analytics lehet az eszköz lehetővé teszi nagy mennyiségű, biztonsággal kapcsolatos bejegyzéseket a rugalmas lekérdezés megközelítéssel gyorsan kereshet egy hasznos eszköz a törvényszéki és egyéb biztonsági elemzés. Emellett a helyszíni [tűzfalakról és proxykról naplók exportálhatók az Azure-bA és elemzése a Log Analytics használatával elérhetővé.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Az Azure Advisor](https://docs.microsoft.com/azure/advisor/) van egy személyre szabott felhőalapú tanácsadó, amely segítséget nyújt az Azure-környezetek optimalizálására. A program elemzi az erőforrás-konfigurációs és -használati telemetriákat, Elősegítő megoldásokat javasol a [teljesítmény](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [biztonsági](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), és [magas rendelkezésre állású](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) az erőforrások keresése közben lehetőségeket a [csökkentheti a teljes Azure költségek](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Az Azure Advisor biztosít a biztonsági javaslatok, ami jelentősen javíthatja a végzi az üzembe helyezést az Azure-megoldások az általános biztonsági állapotát. Ezekkel az ajánlásokkal állítják a által végzett biztonsági elemzés [az Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Emellett az Azure Security Center segít a biztonsági műveletek azáltal, hogy egyetlen irányítópultot a Surface-eszközök riasztások és javaslatok, amelyek segítségével intézkedni azonnal. Gyakran előfordul akkor javíthatja a problémákat az Azure Security Center konzolon egyetlen kattintással.
## <a name="applications"></a>Alkalmazások
A szakasz a legfontosabb funkcióit az alkalmazás biztonsági és összefoglaló információt ezeket a képességeket kapcsolatos további információkat nyújt.

### <a name="web-application-vulnerability-scanning"></a>Webes alkalmazás biztonsági ellenőrzése
Az egyik legegyszerűbb – első lépések a biztonsági rések tesztelése a [App Service-alkalmazás](https://docs.microsoft.com/azure/app-service/overview) használata a [teljesíteni a Tinfoil Security-integráció](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) ellenőrzés az alkalmazás használatát egy biztonsági rés végrehajtásához. Tekintse meg az eredményt egy könnyen érthető jelentésben, és ismerje meg, hogyan háríthatja el az egyes biztonsági lépésről lépésre.

### <a name="penetration-testing"></a>Behatolástesztelés
Ha inkább saját behatolási teszteket végrehajtására, vagy egy másik képolvasó suite vagy szolgáltató szeretne használni, kövesse a [Azure behatolástesztelést jóváhagyási folyamat](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) és előzetes hagyassa jóvá a kívánt behatolási teszteket végrehajtásához.

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal
A webalkalmazási tűzfal (WAF) az [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) segít megvédeni a webalkalmazásokat az ismert webalapú támadásoktól, például SQL-injektálás, a többhelyes parancsfájlok futtatására és a munkamenet-eltérítés. Által azonosított fenyegetések elleni védelemre hozzárendeléskor a [nyílt Web Application Security Project (OWASP), a legfontosabb 10 gyakori sebezhetőségként](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben
[App Service-hitelesítés / engedélyezés](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) olyan szolgáltatás, amely lehetővé teszi, hogy az alkalmazás felhasználók bejelentkeztetéséhez, így nem kell az alkalmazási háttérrendszer a kódot. Az alkalmazás védelme és a felhasználói adatok egy egyszerű megoldást kínál.

### <a name="layered-security-architecture"></a>Többrétegű biztonsági architektúra
Mivel [App Service Environment-környezetek](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) adjon meg egy izolált futtatókörnyezetben helyezi üzembe egy [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), létrehozhatnak egy többrétegű biztonsági architektúra eltérő szintű megadása az egyes alkalmazásrétegek hálózati hozzáférést. Egy közös törekszik, hogy elrejtése API háttérrendszereket általános Internet-hozzáférés, és csak a felsőbb rétegbeli webes alkalmazások által meghívandó API-k engedélyezése. [Hálózati biztonsági csoportok (NSG-k)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) API-alkalmazásokból való nyilvános hozzáférés korlátozása tartalmazó App Service Environment-környezetek Azure virtuális hálózati alhálózatokon használható.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web server diagnostics és az application diagnostics
Az App Service web apps adja meg a diagnosztikai adatok naplózása a webalkalmazás-kiszolgáló és a webes alkalmazás funkciói. Ezek logikailag elkülönített [kiszolgálódiagnosztika webes](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) és [az application diagnostics](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webalkalmazás-kiszolgáló két fő tudnak és webhelyek és alkalmazások elhárítása meglehetősen tartalmazza.

Az első új funkció alkalmazáskészletek, munkavégző folyamatok, helyek, alkalmazás-tartományok és futó kérések valós idejű megszakítók állapotinformációit. A második új előnyei a részletes nyomkövetési eseményeket, amelyek nyomon követik a kérelem a kérelem / válasz teljes folyamat során.

Ahhoz, hogy ezek a nyomkövetési események gyűjtésére, az IIS 7 beállítható úgy, hogy automatikusan rögzítheti a teljes nyomkövetési naplókat, az XML-formátumnak, bármely adott kérelem során eltelt idő vagy a válasz hibakódok alapján.

#### <a name="web-server-diagnostics"></a>Webes kiszolgálódiagnosztika
Engedélyezheti vagy letilthatja a naplók a következő típusú:

-   Részletes hibanaplózás – részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Ez tartalmazhat, amelyek segíthetnek meghatározni, miért érdemes a kiszolgáló a következő hibakódot adta vissza információt.

-   Sikertelen kérelmek követésének – részletes információk a sikertelen kérelmek, beleértve a nyomkövetés feldolgozni a kérelmet, és az egyes összetevőkben ideje használja az IIS-összetevőt. Ez akkor lehet hasznos, ha próbált webhely teljesítményének növelése vagy elkülönítése, mi kell visszaadni egy adott HTTP hiba okozza.

-   A Web Server-naplózás – a W3C bővített naplófájlformátum használata HTTP-tranzakciót kapcsolatos információkat. Ez akkor hasznos, teljes webhelymetrikák például kezelt kérések, vagy hogy hány kérésnek egy adott IP-címről számának meghatározásakor.

#### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
[Az Application diagnostics](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) lehetővé teszi egy webalkalmazás által létrehozott adatok rögzítését. ASP.NET-alkalmazások használhatják a [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) osztályt az alkalmazásnaplóba diagnosztikai információk naplózása. Az Application Diagnostics, két fő típusa van események, alkalmazások teljesítményével és alkalmazások hibáival kapcsolatos. A hibák és a hibák oszthatók csatlakozási, biztonsági és meghibásodási problémákra tovább. Probléma adódott az alkalmazáskód hibái általában kapcsolódó.

Az Application Diagnostics felületén tekintheti meg a következő módokon csoportosíthatók események:

-   Az összes (minden esemény megjelenítése)
-   Az alkalmazáshibák (a kivételesemények megjelenítése)
-   Teljesítmény (a teljesítménnyel kapcsolatos események megjelenítése)

## <a name="storage"></a>Storage
A szakasz az Azure storage biztonsági és összefoglaló információt ezeket a képességeket a legfontosabb jellemzők kapcsolatos további információkat nyújt.

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A szerepköralapú hozzáférés-vezérlés (RBAC) a tárfiók gondoskodhat. A hozzáférés korlátozása alapján a [ismernie kell](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági alapelveket elengedhetetlen, szervezetek által adatok elérésére vonatkozó biztonsági szabályzatok kikényszerítéséhez. A hozzáférési jogosultságokat szerint a megfelelő RBAC-szerepkörök hozzárendelése a csoportokhoz és alkalmazásokhoz egy bizonyos hatókörben. Használhat [beépített RBAC-szerepkör](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), például a Tárfiók-közreműködő, jogosultságok hozzárendelése a felhasználókhoz. A storage-hozzáférés a kulcsok a tárfiók tárfiókkulcsait a [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modell szerepköralapú hozzáférés-vezérlés (RBAC) szabályozható.

### <a name="shared-access-signature"></a>Közös hozzáférésű Jogosultságkód
A [közös hozzáférésű jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. Az SAS, az azt jelenti, hogy Ön is az ügyfeleknek a tárfiókban lévő objektumokra vonatkozó adott ideig, és az engedélyek bizonyos készletét. Ezekkel a korlátozott engedélyekkel biztosíthat a hozzáférési kulcsainak megosztása nélkül.

### <a name="encryption-in-transit"></a>Titkosítás az átvitel során
Titkosítás az átvitel során egy mechanizmust, az adatok védelme, amikor azok elküldése hálózatokon keresztül. Az Azure Storage segítségével gondoskodhat adatok használatával:
-   [Átviteli szintű titkosítást](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), például a HTTPS vagy onnan máshová az Azure Storage-adatok átvitel során.

-   [Vezetékes titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), mint például [az SMB 3.0 titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide) a [Azure-fájlmegosztások](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Ügyféloldali titkosítás, a storage-bA továbbított adatok titkosítását, és az adatok visszafejtéséhez követően elfogyott a tárterület.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
A legtöbb szervezet számára az adatok titkosítását fel az adatok adatvédelmi, megfelelőségi és az adatok elkülönítése kötelező lépés. Nincsenek három, adja meg az "Inaktív", az adatok titkosítása az Azure storage biztonsági funkciók:

-   [A Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption) lehetővé teszi, hogy a storage szolgáltatás automatikusan adatok titkosításához, az Azure Storage írásakor.

-   [Ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) az inaktív adatok titkosítását a szolgáltatást is nyújt.

-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lehetővé teszi, hogy az operációsrendszer-lemezek és a egy IaaS virtuális gép által használt adatlemezek titkosítása.

### <a name="storage-analytics"></a>Storage Analytics
[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) naplózhatja és mérőszámadatokat biztosít egy tárfiókot. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. A Storage Analytics naplók sikeres és sikertelen kérések kapcsolatos részletes információk egy tárolási szolgáltatásba. Ezt az információt a figyelheti az egyes kérések és a egy storage szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek van bejelentkezve a képességeinkhez mérten biztosítjuk. A következő típusú hitelesített kérelmeket naplózza:
-   Sikeres kérelmeinek száma.

-   Sikertelen kérelmek, beleértve az időkorlát, a szabályozás, hálózati, engedélyezési és egyéb hibák.

-   A kérelmek egy közös hozzáférésű Jogosultságkód (SAS), beleértve a sikeres és sikertelen kérelmek használatával.

-   Elemzési adatok kérelmeket.

### <a name="enabling-browser-based-clients-using-cors"></a>Böngészőalapú ügyfeleken a CORS engedélyezése
[Eltérő eredetű erőforrások megosztása (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) olyan mechanizmus, amely lehetővé teszi a tartományok engedélyt egymással a többi összes erőforrások eléréséhez. A felhasználói ügynök küld további fejlécek annak ellenőrzéséhez, hogy engedélyezett-e egy bizonyos tartományból betöltése a JavaScript-kódot egy másik tartományban található erőforrások eléréséhez. Az utóbbi tartomány majd válaszol a további fejlécek erőforrásaira az eredeti tartomány-hozzáférés engedélyezése vagy elutasítása.

Az Azure storage szolgáltatás mostantól CORS támogatására, úgy, hogy miután beállította a szolgáltatást a CORS-szabályainak, más tartományokból a szolgáltatás társzolgáltatásokhoz megfelelően hitelesített kérelmeket meghatározni, hogy engedélyezett-e a megadott szabályok szerint abban az esetben.
## <a name="networking"></a>Hálózat
A szakasz a legfontosabb funkcióit az Azure-beli hálózati biztonsági és összefoglaló információt ezeket a képességeket kapcsolatos további információkat nyújt.

### <a name="network-layer-controls"></a>Hálózati réteg vezérlők
Hálózati hozzáférés-vezérlés, és konkrét eszközöket vagy alhálózatokról származó kapcsolat korlátozására a törvény, és az alapvető hálózati biztonság jelöli. A hálózati hozzáférés-vezérlés célja, hogy győződjön meg arról, hogy a virtuális gépek és szolgáltatások elérhetők csak a felhasználók és eszközök, amelyek szeretné őket érhető el.

#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A [hálózati biztonsági csoport (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) egy tűzfal, és szűrés alapszintű állapot-nyilvántartó csomag segítségével szabályozható a hozzáférés alapján egy [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple). Az NSG-k application layer ellenőrző nem ad meg, vagy a hitelesített hozzáférés-vezérlést. Egy Azure virtuális hálózat alhálózatainak és az Azure Virtual Network és az Internet közötti forgalom közötti áthelyezése adatforgalom vezérlésére használható.

#### <a name="route-control-and-forced-tunneling"></a>Útvonal-vezérléshez és a kényszerített bújtatás
A kritikus hálózati biztonság és a hozzáférés képessége arra, hogy az Azure Virtual Networks az útválasztási viselkedés szabályozására. Például győződjön meg arról, hogy minden forgalmat az Azure Virtual Networkhöz végighalad a virtuális biztonsági berendezés szeretne, ha szeretne tudni a vezérlőelemet, és testre szabhatja az útválasztási viselkedés. Ehhez felhasználó által megadott útvonalak konfigurálása az Azure-ban.

[Felhasználó által definiált útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) segítségével testre szabhatja a környezetbe történő áthelyezés forgalom a bejövő és kimenő útvonalai és az egyes virtuális gépek vagy az alhálózatok biztosítása érdekében a legtöbb biztonságos útvonal lehetséges. [Kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) olyan mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások nem engedélyezett az internethez csatlakozó eszközökről való kapcsolatot kezdeményez.

Ez eltér a bejövő kapcsolatok fogadására képes, és majd válaszadás a őket. Előtér-webkiszolgálók kell válaszolni a kérelmekre Internet gazdagépekről, és így internetes forráskódú forgalom engedélyezve van, a következő webes kiszolgálókhoz bejövő és a webkiszolgálók válaszolhassanak.

Kényszerített bújtatás általában arra használják, kimenő forgalom az interneten keresztül halad át a helyszíni biztonsági proxykon és tűzfalakon kényszerít ki.

#### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági berendezéseket
Bár a hálózati biztonsági csoportok, a felhasználó által megadott útvonalakat és a kényszerített bújtatás biztosítanak a hálózat és az átviteli rétegeket, a biztonsági szintet a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), előfordulhat, hogy azt szeretné, magasabb szintű biztonság engedélyezéséhez a stack. Következő nagyobb hálózati biztonsági funkciók érhetők el az egy biztonsági berendezés Azure-partneri megoldás használatával. Annak a legújabb Azure-partneri hálózati biztonsági megoldások meglátogatják a [Azure Marketplace-en](https://azure.microsoft.com/marketplace/) , és keressen a "security" és "hálózati biztonság."

### <a name="azure-virtual-network"></a>Azure Virtual Network

Az Azure virtuális hálózat (VNet) az Ön saját hálózatát jelképezi a felhőben. Egy logikai elkülönítése az Ön előfizetéséhez fenntartott Azure-beli hálózati háló. A hálózaton belül teljes mértékben irányíthatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útválasztási táblázatokat. A virtuális hálózat oszthatja alhálózatokra, és helyezze el az Azure IaaS virtuális gépeket (VM) és/vagy [Felhőszolgáltatások (PaaS szerepkörpéldányok)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) az Azure-beli virtuális hálózatok.

A virtuális hálózatot ezen felül a helyszíni hálózathoz is csatlakoztathatja az Azure-ban elérhető [kapcsolati lehetőségek](https://docs.microsoft.com/azure/vpn-gateway/) egyikével. Lényegében kiterjesztheti a hálózatot az Azure-ra, az IP-címblokkok teljes körű irányítása és a vállalati méretű Azure által nyújtott előnyök mellett.

Az Azure-hálózatok biztonságos távoli hozzáférést különböző forgatókönyveket támogatja. Ezek többek között:

-   [Egyéni munkaállomás csatlakozni egy Azure virtuális hálózaton](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [A helyszíni hálózat csatlakoztatása az Azure Virtual Network VPN-nel](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [A helyszíni hálózat csatlakoztatása egy Azure virtuális hálózat egy dedikált WAN-kapcsolaton](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Azure virtuális hálózatokhoz csatlakozni egymáshoz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Az Azure virtuális hálózat és a helyszíni hely közötti hálózati adatforgalom elküldésére, az Azure Virtual Network VPN-átjárót kell létrehoznia. A [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) olyan típusú virtuális hálózati átjáró, amely titkosított adatforgalmat továbbít nyilvános kapcsolaton keresztül. VPN átjárók használatával továbbá adatforgalmat továbbíthat az Azure Virtual Networks keresztül az Azure-beli hálózati háló között.

### <a name="express-route"></a>Express Route
A Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) egy dedikált WAN hivatkozás, amely lehetővé teszi a kiterjesztheti helyszíni hálózatait a Microsoft-felhőben, amelyet egy kapcsolatszolgáltató biztosít egy dedikált privát kapcsolaton keresztül.

![Express Route](./media/azure-security/azure-security-fig1.png)

Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

Az ExpressRoute-kapcsolatok nem a nyilvános interneten haladnak át, és így lehessen venni biztonságosabb, mint a VPN-alapú megoldásokat. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.


### <a name="application-gateway"></a>Application Gateway
A Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) biztosít egy [alkalmazás Vezérlőszolgáltatást (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) szolgáltatás, amely számos 7. rétegbeli terheléselosztási lehetőséget nyújt alkalmazásának.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Ez lehetővé teszi, hogy optimalizálhatják a webfarmok termelékenységét a processzorigényes SSL-lezárások az Alkalmazásátjáró (más néven "SSL alapú kiszervezést" vagy "SSL-hídképzés") történő kiszervezésével. Egyéb 7. rétegbeli útválasztási lehetőségeket, beleértve a bejövő forgalmat, cookie-alapú munkamenet-affinitást, URL-cím-alapú útválasztás és egyetlen Application Gateway mögött több webhelyet is üzemeltethet Ciklikus időszeleteléses elosztását is tartalmazza. Az Azure Application Gateway egy 7. rétegbeli terheléselosztó.

Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak.

Alkalmazás maga biztosítja, hogy számos olyan alkalmazás Vezérlőszolgáltatást (ADC) szolgáltatást, beleértve a HTTP betölteni a terheléselosztást, cookie-alapú munkamenet-affinitást, [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) kiszervezési, egyéni állapotmintákkal, támogatja a többhelyes, és sok más.

### <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)
A webalkalmazási tűzfal az egyik funkciója [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) , amely a webes alkalmazásokhoz, amelyek használják az application gateway alkalmazás kézbesítési vezérlő (ADC) szabványos függvényekben védelmet biztosít. A webalkalmazási tűzfal ezt úgy éri el, hogy védelmet nyújt az alkalmazásoknak az OWASP 10 leggyakoribb webes biztonsági résének többségével szemben.

![Web Application Firewall (Webalkalmazási tűzfal)](./media/azure-security/azure-security-fig1.png)

-   SQL-injektálás elleni védelem

-   Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

-   HTTP protokoll megsértése elleni védelem

-   HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

-   Robotprogramok, webbejárók és képolvasók elleni védelem

-   Gyakori alkalmazások konfigurációs hibáinak észlelése (vagyis Apache, IIS stb.)


A webes támadásokkal szembeni védelmet nyújtó központi webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és nagyobb biztonságot ad az alkalmazásnak a behatolások jelentette veszéllyel szemben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen lehet átalakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.
### <a name="traffic-manager"></a>Traffic Manager
A Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) szabályozhatja a különböző adatközpontokban szolgáltatásvégpontokra érkező felhasználói forgalom elosztása. A Traffic Manager által támogatott szolgáltatóvégpontok közé tartozik az Azure virtuális gépek, a Web Apps és a Cloud services. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható. A TRAFFIC Manager a tartománynévrendszer (DNS) használatával a leginkább megfelelő végpontra alapján az ügyfélkéréseket a [forgalom-útválasztási módszer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) és a végpontok állapotát.

A TRAFFIC Manager egy sor különböző alkalmazás igényeinek megfelelően, a végpontonkénti állapotot forgalom-útválasztási módszert biztosít [figyelési](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), és automatikus feladatátvételt. A Traffic Manager ellenáll a meghibásodásoknak, beleértve akár egy egész Azure-régió meghibásodását is.
### <a name="azure-load-balancer"></a>Azure Load Balancer
Az [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. 4. réteg (TCP, UDP) terheléselosztó bejövő forgalmat egy elosztott terhelésű készlet definiált szolgáltatások kifogástalan példányai között osztja el. Az Azure Load Balancer konfigurálható:

-   Töltse be a bejövő internetes forgalom terheléselosztása virtuális gépekhez. Ez a konfiguráció az úgynevezett [internetre irányuló terheléselosztási](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Forgalom terheléselosztása virtuális hálózatban lévő virtuális gépek közötti, virtuális gépek, cloud Services vagy a helyszíni számítógépek és létesítmények közötti virtuális hálózatban lévő virtuális gépek között. Ez a konfiguráció az úgynevezett [belső terheléselosztás](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Egy adott virtuális gép külső forgalom továbbítása

### <a name="internal-dns"></a>Internal DNS
A felügyeleti portálon, vagy a hálózati konfigurációs fájlt a vnetben használt DNS-kiszolgálók listáját kezelheti. Ügyfél legfeljebb 12 DNS-kiszolgálókat adhat hozzá, az egyes virtuális hálózatok. DNS-kiszolgálók megadása esetén fontos ellenőrizni, hogy az ügyfél DNS-kiszolgálók az ügyfél-környezetben a megfelelő sorrendben listázza. Ciklikus időszeletelési DNS-kiszolgáló listákat nem működik. Ezek szolgálnak, melyek a megadott sorrendben. Ha a lista első DNS-kiszolgáló érhető el, akkor az ügyfél a DNS-kiszolgálónak, függetlenül attól, hogy a DNS-kiszolgáló működik-e megfelelő vagy nem használja. A DNS-kiszolgáló az ügyfél virtuális hálózatán sorrendjének módosítása, a DNS-kiszolgálók eltávolítása a listából, és hozzáadhatja őket vissza a sorrendet, hogy az ügyfél szeretné. A DNS támogatja a rendelkezésre állási szempontból a "CIA" biztonsági háromrészes.

### <a name="azure-dns"></a>Azure DNS
A [Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), vagy a DNS-beli felelős fordítása (vagy feloldása) az IP-címét a webhely vagy szolgáltatás nevét. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) egy üzemeltetési szolgáltatás DNS-tartományok biztosítani a névfeloldást a Microsoft Azure infrastruktúráját használja. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. A DNS támogatja a rendelkezésre állási szempontból a "CIA" biztonsági háromrészes.
### <a name="log-analytics-nsgs"></a>Log Analytics NSG-k
A következő diagnosztikai napló kategóriák engedélyezheti a hálózati biztonsági csoportok:
-   Esemény: Melyik NSG szabályok érvényesek virtuális gépeket és példányszerepköröket MAC-cím alapján bejegyzést tartalmaz. Ezek a szabályok állapota gyűjtött minden 60 másodpercben.

-   Szabályok számláló: Az egyes NSG-szabályokat alkalmaznak-forgalom engedélyezése vagy megtagadása hány alkalommal bejegyzést tartalmaz.

### <a name="azure-security-center"></a>Azure Security Center
A Security Center segít megelőzését, észlelését és az azokra való reagálásban, és növeli a betekintést nyújt, és szabályozhatóbbá, az Azure-erőforrások biztonságát. Az Azure-előfizetések integrált biztonsági monitorozást és felügyeletet biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és a biztonsági megoldások széles ökoszisztémájával képes együttműködni. Hálózati javaslatok center tűzfalak, a hálózati biztonsági csoportok, konfigurálását, bejövő forgalomra vonatkozó szabályokat és további körül.

Rendelkezésre álló hálózati javaslatok a következők:

-   [Újgenerációs tűzfal hozzáadása](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) javasolja, hogy egy Microsoft-partner a biztonsági megoldásokat növelése érdekében adjon hozzá egy Next Generation Firewall (NGFW)

-   [Irányíthatja a forgalmat Újgenerációs tűzfalon keresztül csak](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) javasolni a felhasználóknak, Ön által konfigurált hálózati biztonsági csoport (NSG) szabályok, amelyek a bejövő forgalom kényszeríti a virtuális géphez az Újgenerációs tűzfalon keresztül.

-   [Hálózati biztonsági csoportok engedélyezése az alhálózatokra vagy virtuális gépekre](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) NSG-ket alhálózatokhoz vagy virtuális gépeken engedélyezését javasolja.

-   [Internetről elérhető végponton keresztüli hozzáférés korlátozása](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) javasolja, hogy a bejövő forgalomra vonatkozó szabályokat az NSG-k konfigurálása.


## <a name="compute"></a>Compute

A szakasz a legfontosabb jellemzőkkel a ezeket a képességeket a terület és összefoglaló információt kapcsolatos további információkat nyújt.

### <a name="antimalware--antivirus"></a>Kártevőirtó- és víruskereső
Az Azure IaaS-használhatja például a Microsoft, a Symantec, a Trend Micro, a McAfee és a Kaspersky biztonsági szállítóktól származó kártevőirtó szoftver kártékony fájlokkal, hirdetőprogramokkal és egyéb veszélyforrásokkal szembeni a virtuális gépek. [A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) az Azure Cloud Services és Virtual Machines a vírusok, kémprogramok és más kártevők azonosításában és segít védelmi funkcióval. Microsoft Antimalware-t biztosít, konfigurálható riasztást, ha ismert kártevő vagy nemkívánatos szoftver megkísérli a telepítést, vagy az Azure rendszeren futtassa. A Microsoft Antimalware is üzembe helyezhetők az Azure Security Center használatával

### <a name="hardware-security-module"></a>Hardveres biztonsági modul
Titkosítási és hitelesítési nem biztonság növelése, ha a kulcsok védettek. Egyszerűsítheti a kezelését és a létfontosságú kulcsok és titkok biztonságos tárolása a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault lehetővé teszi, hogy a kulcsokat hardveres biztonsági modulokban (HSM) certified FIPS 140-2 2. szintű szabványoknak. Az SQL Server titkosítási kulcsok biztonsági mentése vagy [transzparens adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx) összes tárolható a Key Vault- és az alkalmazások titkos. Engedélyek és a hozzáférést ezekhez a védett elemekhez kezelhető [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
[Az Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) van olyan megoldás, amely védelmet biztosít az alkalmazásadatok nulla tőkebefektetés és minimális üzemeltetési költségek. Az alkalmazáshibák adatai sérülését okozhatják, és az emberi hibák meghibásodásához vezethetnek, amely biztonsági problémákat okozhat az alkalmazások. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védett.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Fontos része annak a szervezet [üzleti folytonosság és vészhelyreállítás (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) stratégia van foglalkozhatunk azzal, hogyan bízhatja vállalati munkaterheléseket és alkalmazásokat tervezett és nem tervezett leállások esetén. [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) segítségével koordinálhatja replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások, hogy elérhetők egy másodlagos helyre, ha az elsődleges hely leáll.

### <a name="sql-vm-tde"></a>AZ SQL VIRTUÁLIS GÉP TDE
[Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) és oszlop a blokkszintű titkosítás (CLE) az SQL server titkosítási funkciókat. Az ilyen típusú titkosítás felügyeletét, és a titkosításhoz használt kriptográfiai kulcsok tárolásához szükséges.

Az Azure Key Vault (AKV) szolgáltatás célja a biztonsági és a egy biztonságos és magas rendelkezésre állású helyen ezek a kulcsok kezelését. Az SQL Server-összekötő lehetővé teszi, hogy az SQL Server-e az Azure Key Vault-kulcsok használata.

A helyszíni gépek az SQL Server futtatja, van-e a lépéseket követheti a helyszíni SQL Server-gép az Azure Key Vault elérése érdekében. Azonban az SQL Server Azure virtuális gépeken, időt takaríthat az Azure Key Vault-integráció funkcióval. Néhány Azure PowerShell-parancsmagokkal a funkció engedélyezéséhez automatizálhatja a konfiguráció szükséges az SQL virtuális gép hozzáférjen a kulcstartóhoz.

### <a name="vm-disk-encryption"></a>Virtuális gép lemeztitkosítás
[Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) egy olyan új képesség, amely segítséget nyújt a Windows és Linux rendszerű IaaS virtuális gépek lemezeinek titkosításához. Az iparági szabványos BitLocker funkcióját Windows és az operációs rendszer és az adatlemezek kötettitkosítását biztosít Linux DM-Crypt funkcióját vonatkozik. A megoldás integrálva van az Azure Key Vault segítségével vezérelheti és felügyelheti a lemeztitkosítási kulcsokat és titkos kulcsokat a Key Vault-előfizetésből. A megoldás emellett biztosítja, hogy a virtuálisgép-lemezeken lévő összes adatot is titkosítása az Azure storage-ban.

### <a name="virtual-networking"></a>Virtuális hálózat
Virtuális gépek hálózati kapcsolat szükséges. Ezt a követelményt támogatása érdekében az Azure-beli virtuális hálózathoz csatlakoztatni kívánt virtuális gépek van szüksége. Egy Azure virtuális hálózaton egy logikai szerkezet, a fizikai az Azure hálózati háló épülnek. Minden egyes logikai [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) el különítve a többi Azure virtuális hálózatok. Elkülönítés segítségével biztosítja, hogy a központi telepítések a hálózati forgalom nem érhető el a más Microsoft Azure-ügyfelek.

### <a name="patch-updates"></a>Patch-frissítések
Javítás frissítések adja meg a alapját megtalálásához, és lehetséges problémák kijavítása, és egyszerűsítheti a szoftverfrissítések célirányos felügyeleti folyamata egyaránt telepítenie kell a vállalati szoftverfrissítések számának csökkentésével, és növelje a megítélnie való megfelelés ellenőrzésére.

### <a name="security-policy-management-and-reporting"></a>Biztonsági házirend kezelése és jelentéskészítés
[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) segít megelőzése, észlelése, és az észlelt, és növeli a betekintést nyújt válaszolni, és szabályozhatóbbá, az Azure-erőforrások biztonságát. Az Azure-előfizetések integrált biztonsági monitorozást és felügyeletet biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és a biztonsági megoldások széles ökoszisztémájával képes együttműködni.

### <a name="azure-security-center"></a>Azure Security Center
A Security Center az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

## <a name="identity-and-access-management"></a>Identitás- és hozzáférés-kezelés

Identitásalapú hozzáférés-vezérlés kezdődik rendszerek, alkalmazások és adatok védelme. Az identitás- és hozzáférés-felügyeleti funkciókat Microsoft üzleti termékeit és szolgáltatásait beépített segít megvédeni a munkahelyi és személyes adatokat a jogosulatlan hozzáféréstől, miközben megkönnyíti elérhető a jogosult felhasználók bárhol és bármikor, szükség van rá.

### <a name="secure-identity"></a>Biztonságos identitás
A Microsoft termékei és szolgáltatásai között több ajánlott biztonsági eljárások és technológiák identitások és hozzáférések felügyelete használja.
-   [A multi-factor Authentication szolgáltatás](https://azure.microsoft.com/services/multi-factor-authentication/) megköveteli a felhasználóktól, több módszer használatához hozzáférés, a helyszíni és a felhőben. Együttműködésre a felhasználók egy egyszerű bejelentkezési folyamat során több egyszerű ellenőrzési lehetőség, erős hitelesítést biztosít.

-   [A Microsoft Authenticator](https://aka.ms/authenticator) , amely együttműködik a Microsoft Azure Active Directory és a Microsoft-fiókok, és támogatja a wearables és az ujjlenyomat-alapú jóváhagyások felhasználóbarát multi-factor Authentication élményt nyújt.

-   [Jelszó házirend betartatása](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) hosszúságára és összetettségére vonatkozó követelményeket, kényszerített rendszeres rotálását és a fiók zárolása sikertelen hitelesítési kísérlet után azáltal növeli a hagyományos jelszavakat biztonságát.

-   [Jogkivonat-alapú hitelesítés](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) lehetővé teszi a hitelesítést az Azure Active Directory segítségével.

-   [Szerepköralapú hozzáférés-vezérlés (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) lehetővé teszi, hogy a felhasználó hozzárendelt szerepkör, így könnyen engedélyezheti a felhasználók csak olyan mértékű hozzáférést a feladat feladataik elvégzéséhez szükséges hozzáférést. Testre szabhatja, hogy az RBAC a szervezet üzleti modell és a szervezet kockázattűrési határát.

-   [Identitáskezelés (hibrid identitás) integrált](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) lehetővé teszi a felhasználói hozzáférés feletti belső adatközpontokra és felhőalapú platformon, hitelesítés és engedélyezés az összes erőforráshoz egyetlen felhasználói identitást létrehozása.

### <a name="secure-apps-and-data"></a>Biztonságos alkalmazások és adatok
[Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/), egy átfogó identitás- és hozzáférés kezelési felhőalapú megoldás, biztonságosabbá teszi a hozzáférést a helyszíni alkalmazások és a felhőbeli adatok, és egyszerűbbé teszi a felhasználók és csoportok kezelését. Ez a egyesíti az alapvető címtárszolgáltatásokat, identitáskezelést, biztonsági és alkalmazáshozzáférés-felügyeletet, speciális, és megkönnyíti a fejlesztők számára a házirend-alapú Identitáskezelés alkalmazásokba. Az Azure Active Directoryt kiegészítheti fizetős képességekkel is, az Azure Active Directory Alapszintű, Prémium P1 és Prémium P2 kiadásával.

| Ingyenes / közös funkciók     | Alapszintű funkciók    |Prémium P1 szintű szolgáltatások |Prémium P2 szintű szolgáltatások | Az Azure Active Directory Join – Windows 10-es csak kapcsolódó szolgáltatások|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Címtárobjektumok](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [felhasználók/csoportok kezelése (hozzáadása/frissítése/törlése) / felhasználóalapú kiépítés, eszközregisztráció](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [egyszeri bejelentkezéses (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [önkiszolgáló jelszó Módosítsa a felhőbeli felhasználóinak](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect (szinkronizációs motor, amely kiterjeszti a helyszíni címtárakat az Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [biztonsági / használati jelentések](https://docs.microsoft.com/azure/active-directory/active-directory-editions)       |     [Csoportalapú hozzáférés-kezelés / kiépítési](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [önkiszolgáló Jelszóátállítás felhőfelhasználók számára](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [vállalati arculat megjelenítése (bejelentkezési lapok/hozzáférési Panel testreszabása)](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [ Az alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [99.9%-os SLA](https://docs.microsoft.com/azure/active-directory/active-directory-editions) |  [Önkiszolgáló csoport- és Alkalmazáskezelés/önkiszolgáló alkalmazás-Hozzáadás/dinamikus csoportok](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [önkiszolgáló jelszó alaphelyzetbe állítása/módosítása /-Zárolásfeloldás helyszíni késleltetve visszaírt](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [multi-factor Authentication Authentication (Felhőbeli és helyszíni (MFA-kiszolgáló))](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MIM CAL + a MIM-kiszolgáló](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [a Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [Automatikus jelszóváltás csoportfiók esetén](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|  [Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Eszköz csatlakoztatása az Azure ad-hez, asztali SSO, a Microsoft Passport for Azure AD-ben rendszergazdai BitLocker-alapú helyreállítási](https://docs.microsoft.com/azure/active-directory/active-directory-editions), [MDM automatikus regisztráció, önkiszolgáló BitLocker-alapú helyreállítás, további helyi rendszergazdák a Windows 10-es eszközökhöz az Azure AD-n keresztül Csatlakozás](https://docs.microsoft.com/azure/active-directory/active-directory-editions)|


- [A cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) egy prémium szintű funkció az Azure Active Directory, amely lehetővé teszi, hogy az alkalmazottak a szervezet által használt felhőalkalmazások azonosításához.

- [Az Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) biztonsági szolgáltatás, amely az Azure Active Directory anomáliadetektálási észlelési funkciót használ a kockázati eseményekről és a lehetséges biztonsági résekről, amelyek hatással lehetnek egy összesített nézetet jelenít meg a a szervezet identitásait.

- [Az Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) lehetővé teszi, hogy az Azure virtuális gépek csatlakoztatása egy tartományhoz tartományvezérlők üzembe helyezése nélkül. Felhasználók a vállalati Active Directory hitelesítő adatok használatával jelentkezzen be ezeket a virtuális gépeket, és hozzáférhessenek az erőforrásokhoz.

- [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású, globális identitáskezelő szolgáltatást nyújt, amely több százmillió identitás kezelésére méretezhető és integrálni lehet a mobil felhasználók felé néző alkalmazásokhoz és webes platformokra. Az ügyfelek keresztül személyre szabható megoldásokat, meglévő közösségi hálózati fiókjaik használó összes alkalmazás jelentkezhetnek be, vagy létrehozhat új önálló hitelesítő adatokat.

- [Az Azure Active Directory B2B együttműködés](https://aka.ms/aad-b2b-collaboration) egy biztonságos partner integrációs megoldás, amely támogatja a átívelő kapcsolatok azzal, elérni a vállalati alkalmazások és adatok külön-külön az önállóan felügyelt használatával identitások.

- [Az Azure Active Directory-csatlakozás](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) lehetővé teszi, hogy a központi felügyelet a Windows 10 rendszerű eszközökön a felhőalapú képességek kiterjesztése. Ez lehetővé teszi a felhasználók számára a vállalati vagy szervezeti felhőalapú Azure Active Directoryn keresztül csatlakozhat, és egyszerűbbé teszi az alkalmazásokhoz és erőforrásokhoz való hozzáférés.

- [Az Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) egyszeri Bejelentkezéssel és biztonságos távoli hozzáférést biztosít a helyszínen üzemeltetett webalkalmazásokhoz.

## <a name="next-steps"></a>További lépések
- [A Microsoft Azure Security használatának első lépései](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Azure-beli adatai és szolgáltatásai számára védelmet nyújtó Azure-szolgáltatások és funkciók

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Fenyegetések megelőzése, észlelése és elhárítása az Azure-erőforrások jobb láthatóságával és kézben tartásával

- [Biztonsági állapot monitorozása az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Az Azure Security Center figyelési funkcióinak szabályzatainak való megfelelés figyelése.
