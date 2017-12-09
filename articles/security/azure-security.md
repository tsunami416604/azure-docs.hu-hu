---
title: "Bevezetés az Azure biztonsági |} Microsoft Docs"
description: "Tudnivalók az Azure biztonsági szolgáltatásait és annak működéséről."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 68bba95e177fa8d0261b84f51b0f5285c7fb7417
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="introduction-to-azure-security"></a>Bevezetés az Azure biztonsági
## <a name="overview"></a>Áttekintés
Tudjuk, hogy biztonsági-e a feladat a felhőben, és hogy mennyire fontos, hogy található-e az Azure biztonsági pontos és időben információt. A legjobb okai az alkalmazások és szolgáltatások Azure használandó, a széles köréről a biztonsági eszközök és képességek előnyeit. Ezekkel az eszközökkel és képességek segítségével történő biztonságos megoldásokat keres a biztonságos Azure platformon. A Microsoft Azure a titkosítás, integritás és rendelkezésre állás ügyféladatok, biztosítása átlátszó elszámolási kötelezettségéről szóló biztosít.

Jobb megértése érdekében biztonsági gyűjteményének segítségével az ügyfél és a Microsoft operations perspektívából, ez a dokumentum, "Bevezetés az Azure biztonsági", a Microsoft Azure-ban végrehajtott vezérlők adjon meg egy átfogó megjelenését írni a biztonsági érhető el a Microsoft Azure-ban.

### <a name="azure-platform"></a>Az Azure Platform
Azure egy nyilvános felhőszolgáltatási platform, amely támogatja az operációs rendszerek, programozási nyelvek, keretrendszerek, eszközök, adatbázisok és eszközök széles körű kijelölés. Linux-tárolók futtatható a Docker integrációja; alkalmazások, JavaScript, Python, .NET, PHP, Java és Node.js; build vissza-végpontok iOS, Android és Windows eszközökhöz.

Azure nyilvános felhőjében services támogatja a fejlesztők milliói technológiát és informatikai szakemberek számára már alapulnak, és megbízható. Létrehozása vagy áttelepítése informatikai eszközök, egy nyilvános felhőszolgáltatóhoz meg az adott szervezet képességek az alkalmazások és a szolgáltatások és a vezérlők adatok védelme érdekében a megbízható függő biztosítanak a biztonság, a felhő alapú eszközök kezeléséhez.

Azure infrastruktúrája alkalmazások felhasználók millióit egyidejűleg üzemeltetéséhez a létesítmény készült, és egy megbízható foundation, amelyre vállalatok megfelel a biztonsági követelményeknek biztosít.

Emellett Azure biztosít konfigurálható biztonsági beállításai és képes azokat, hogy testre szabhatja a szervezet központi telepítések egyedi követelményeinek biztonsági széles köréről. Ez a dokumentum segít az Azure biztonsági eljárásainak megértését képességek segítségével ezek a követelmények teljesítéséhez.

> [!Note]
> A jelen dokumentum elsősorban ügyfélkapcsolati vezérlők, amelyekkel testre szabhatja, és az alkalmazások és szolgáltatások biztonságának növelésében.
>
> Rendszer áttekintése adatokra, de hogyan biztosítja a Microsoft az az Azure platformon, maga részletes információkért lásd: található információk a [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Absztrakt
Kezdetben nyilvános felhő áttelepítések volt által vezérelt költséghatékony és agilitást újítaniuk. Biztonsági tekinthető egy ideig, és még egy megjelenítése dugót, nyilvános felhő áttelepítési fő szempont. Azonban nyilvános felhő biztonsági átváltott a fő szempont egyik áttelepülés a felhőbe az illesztőprogramot. Ez logikája, a felső szintű képességét nagy nyilvánosfelhő-szolgáltatók-alkalmazások védelmét és az adatok felhőalapú eszközök.

Az Azure infrastruktúráját úgy tervezték, hogy képes legyen ügyfelek millióit egyidejűleg kiszolgáló alkalmazásokat üzemeltetni, és olyan megbízható alapot nyújtson, amely megfelel a vállalatok biztonsági igényeinek. Emellett Azure akkor konfigurálható biztonsági beállítások széles köréről és egyaránt tudja vezérelni őket, hogy a biztonsági, hogy megfeleljenek a vállalat informatikai tárhelyigényét egyedi követelményeinek megfelelően testre hozzáférésvezérlési házirendeket, és követi a külső szabályzat.

A dokumentum ismerteti a Microsoft megközelítést a Microsoft Azure cloud platform belül:
* A Azure-infrastruktúra, a felhasználói adatok és az alkalmazások a Microsoft által megvalósított funkciókat.
* Azure-szolgáltatások és a biztonsági funkciók érhetők el, hogy a szolgáltatások és az adatok az Azure-előfizetések belüli biztonság kezeléséhez.

## <a name="summary-azure-security-capabilities"></a>Összegző Azure biztonsági képességei
A táblázat következő biztonságos az Azure-infrastruktúra, a felhasználói adatok és a biztonságos alkalmazások a Microsoft által megvalósított funkciókat rövid leírását adhatja meg.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Biztonságossá tétele az Azure platformon végrehajtott biztonsági funkciók:
A következő felsorolt funkciók áttekintheti a megakadályozni, hogy biztonságos módon kezeli-e az Azure Platform olyan képességeit. Hivatkozások vannak-e megadva a további részletes a hogyan Microsoft címek megbízhatósági kérdéseinek négy területeken: biztonságos Platform, adatvédelmi és vezérlők, megfelelőségi és átláthatóság.


| [Biztonságos Platform](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Adatvédelem és vezérlők](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Megfelelőség](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Átláthatóság](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Biztonsági fejlesztési ciklus](https://www.microsoft.com/en-us/sdl/), belső naplózás | [Az adatok állandóan kezelése](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Adatvédelmi központ](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Hogyan biztosítja a Microsoft az ügyféladatokat, Azure-szolgáltatások](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Kötelező biztonsági képzés, hátsó ground ellenőrzése](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc) |  [Szabályozhatja az adatok helye](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Vezérlők közös hubbal](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Hogyan kezelhetik a Microsoft az Azure-szolgáltatásokat az adatok helye](http://azuredatacentermap.azurewebsites.net/)|
| [Behatolás tesztelés](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc), [behatolásérzékelési, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [naplózás & naplózás](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Adja meg az adat-hozzáférési saját igényei szerint](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [A Felhőszolgáltatások miatt gondossággal ellenőrzőlista](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[A Microsoft ki férhet hozzá az adatok milyen feltételek mellett](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [A kép datacentre állapot](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), fizikai biztonsági [biztonságos hálózati](https://docs.microsoft.com/azure/security/security-network-overview) | [Válaszol a rendészeti szervet](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Szolgáltatás, raktár és iparági megfelelősége](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Hogyan biztosítja a Microsoft az ügyféladatokat, Azure-szolgáltatások](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Biztonsági incidensmegoldási](http://aka.ms/SecurityResponsepaper), [megosztott feladata](http://aka.ms/sharedresponsibility) |[Szigorú adatvédelmi szabványok](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Tekintse át az Azure szolgáltatások esetében az átlátszóság hub hitelesítésszolgáltató](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Adatok és az alkalmazás Azure által nyújtott biztonsági szolgáltatások
Attól függően, hogy a felhőszolgáltatási modellnek nincs biztonsága érdekében az alkalmazások és szolgáltatások kezelése felelős változó felelős. Nincsenek képességek az Azure platform segítséget teljesíti-e feladatokat beépített funkciók révén, és a partneri megoldások, amelyek is telepíthető az Azure-előfizetéssel.

A beépített képességei hat (6) funkcionális területen vannak rendszerezve: műveletek, alkalmazások, tárolási, hálózati, számítási és identitás. További részletek a funkciók és képességek az Azure platformra ezen hat (6) területen elérhető szolgáltatáson keresztül összegző információkat.

## <a name="operations"></a>Műveletek
Ez a témakör a biztonsági műveletek legfontosabb funkcióira vonatkozó további információk és ezek a képességek kapcsolatos összegző információkat.

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Az Operations Management Suite biztonsági és naplózási irányítópult
A [OMS biztonsági, naplózási megoldást](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) nyújt átfogó képet kaphat a szervezet informatikai biztonságot a [beépített keresési lekérdezések](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) a jelentős figyelmet igénylő problémák. A [Security and Audit](https://technet.microsoft.com/library/mt484091.aspx) (Biztonsági és naplózás) irányítópult az OMS rendszerben a biztonsággal kapcsolatos összes tényezőt megjelenítő kezdőoldal. A számítógépek biztonsági állapotának magas szintű betekintést biztosít. Olyan funkciót is kínál, amellyel megjeleníthető az elmúlt 24 óra, 7 nap vagy bármely más egyéni időszak összes eseménye.

Emellett konfigurálhatja a OMS biztonsági és megfelelőségi való [automatikusan műveleteket hajt végre adott](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) Ha egy adott esemény észlelhető.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Az Azure Resource Manager ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) lehetővé teszi, hogy az alkalmazásában lévő erőforrásokat csoportként a megoldásban. A megoldás összes erőforrását egyetlen, koordinált műveletben telepítheti, frissítheti vagy törölheti. Használhat egy [Azure Resource Manager sablon](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) az üzembe helyezési és, hogy a sablon különböző, például tesztelési, átmeneti és üzemi környezetekben is képes működni. A Resource Manager biztonsági, naplózási és címkézési szolgáltatásokat biztosít, hogy segítsen az erőforrások kezelésében a telepítést követően.

Az Azure Resource Manager sablon-alapú központi telepítések segítségével biztonságosabbá teheti az Azure rendszerbe, mert a szabványos biztonsági beállításainak kezeléséhez, és képes megoldások szabványos sablon-alapú telepítések integrálni. Ez csökkenti a manuális telepítése során előfordulhat, hogy kerül sor biztonsági konfigurációs hibák kockázatát.

### <a name="application-insights"></a>Application Insights
[Az Application Insights](https://docs.microsoft.com/azure/application-insights/) webfejlesztőknek bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás. Az Application insights szolgáltatással az élő webalkalmazások figyeléséhez, és automatikusan észleli a teljesítményanomáliákat. Ez magában foglalja a hatékony elemzőeszközök segítségével problémák elemzéséhez és megérteni a felhasználók ténylegesen mire fel az alkalmazások. Az alkalmazás fut, tesztelés során és azt követően, hogy közzétett vagy telepítve lett folyamatosan figyeli.

Az Application Insights diagramok és is láthat, például táblák, mely napszakokban kap a legtöbb felhasználó, mennyire reagáljon az alkalmazást, és attól függ, külső szolgáltatások által kiszolgált mennyire hoz létre.

Ha összeomlik, hibák vagy teljesítményproblémák, kereshet a telemetriai adatok ellenőrizze okát részletesen keresztül. És a szolgáltatás küld Önnek e-maileket a rendelkezésre állási és az alkalmazás teljesítményével kapcsolatos módosításokat esetén. Application Insights értékes biztonsági eszköz így lesz, mert a titkosítás, integritás és rendelkezésre állás biztonsági háromrészes a segít a rendelkezésre állással.

### <a name="azure-monitor"></a>Azure Monitor
[Az Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) és biztosít a képi megjelenítés, lekérdezés, útválasztási, riasztások, automatikus méretezési, mindkét adatokon automation az Azure infrastruktúra ([tevékenységnapló](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)), és minden egyes Azure-erőforrás ([diagnosztikai Naplózza az](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Azure a figyelő riasztást küld a biztonsági események, amelyek akkor jönnek létre, az Azure naplói használhatja.

### <a name="log-analytics"></a>Log Analytics
[Naplófájl Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) része [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – informatikai felügyeleti megoldást nyújt a helyszíni és a külső felhőalapú infrastruktúrával (például AWS) Azure-erőforrások mellett. Azure figyelő adatait közvetlenül a Naplóelemzési átirányítható, így látható metrikák és a naplókat a teljes környezet egyetlen helyen.

A Naplóelemzési lehet az eszköz lehetővé teszi biztonsági bejegyzést, amelyben a Rugalmas lekérdezési megközelítés nagy mennyiségű gyorsan keresést Törvényszéki és egyéb biztonsági elemzés, fontos eszköze. Emellett a helyszíni [tűzfal és proxy naplók az Azure exportálhatók és Naplóelemzési használatával történt elemzésének elérhetővé.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Az Azure Advisor](https://docs.microsoft.com/azure/advisor/) személyre szabott felhő tanácsadó, amely segít az Azure-környezetekhez optimalizálása érdekében. A program elemzi az erőforrás-konfigurációs és -használati telemetriákat, Azt az javítása érdekében megoldások javasolja a [teljesítmény](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [biztonsági](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), és [magas rendelkezésre állású](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) lehetőségekkereséseközbenazerőforrások[csökkentheti a teljes Azure töltött](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Az Azure Advisor biztosít a biztonsági javaslatok, mely jelentős is javítja az általános biztonsági állapotát a megoldások Azure központi telepítését. Ezek a javaslatok állítják által végzett biztonsági elemzés [az Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

Emellett az Azure Security Center elősegíti a biztonsági műveletek azáltal, hogy egyetlen irányítópulton, hogy felületek riasztások és javaslatokat is kell bírálni azonnal. Gyakran javítani tudja a problémák az Azure Security Center konzolon egyetlen kattintással.
## <a name="applications"></a>Alkalmazások
A szakasz vonatkozóan nyújt további információkat az alkalmazás biztonsági és összefoglaló információt ezeket a képességeket a legfontosabb jellemzők.

### <a name="web-application-vulnerability-scanning"></a>Webes alkalmazás biztonsági rések keresése
Egyik való ismerkedés vizsgálatot a biztonsági réseket legegyszerűbb módja a [App Service alkalmazás](https://docs.microsoft.com/azure/app-service/app-service-web-overview) használata a [integráció a Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) vizsgálja meg az alkalmazást egy kattintással biztonsági rés végrehajtásához. A teszteredmények megtekintéséhez könnyen érthető jelentésben, és részletes utasításokat tartalmaz az egyes biztonsági kijavításához.

### <a name="penetration-testing"></a>Behatolástesztelés
Ha inkább a saját behatolást vagy a biztonság tesztek kerülnek végrehajtásra, vagy egy másik képolvasó suite vagy szolgáltatót szeretne használni, kövesse a [jóváhagyási folyamat tesztelése az Azure behatolás](https://security-forms.azure.com/penetration-testing/terms) és előzetes jóváhagyásának a kívánt behatolás tesztek végrehajtásához.

### <a name="web-application-firewall"></a>Webalkalmazási tűzfal
A webes alkalmazás tűzfalat (waf-ot) [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) közös web-alapú támadások, például az SQL-injektálás többhelyes parancsfájlok futtatására és munkamenet-eltérítés védelmét webes alkalmazásokhoz. Származik, előre konfigurált védelemmel által azonosított fenyegetések a [megnyitása webes alkalmazás biztonsági Project (OWASP), az első 10 közös biztonsági réseit](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service-ben
[App Service hitelesítés / engedélyezés](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) egy szolgáltatás, amely lehetővé teszi a felhasználók bejelentkezni, így nem kell a a háttéralkalmazás kódjának módosítása az alkalmazás. Az alkalmazás védelme és a felhasználói adatok segítségével egyszerűen biztosít.

### <a name="layered-security-architecture"></a>Többrétegű biztonsági architektúrája
Mivel [App Service Environment-környezetek](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) adjon meg egy helyezett izolált futtatókörnyezetben egy [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), fejlesztők hozhat létre egy többrétegű biztonsági architektúra eltérő szintű biztosítása hálózati hozzáférés minden alkalmazás szinten. A közös desire API vissza-végpontok általános Internet-hozzáférés elrejtéséhez, és csak a felsőbb rétegbeli webes alkalmazások által használt hívása API-k engedélyezése. [Hálózati biztonsági csoportokkal (NSG-k)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) nyilvános hozzáférés korlátozása API-alkalmazások Azure virtuális hálózati alhálózat App Service Environment-környezetek tartalmazó használható.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web server diagnostics és az application diagnostics
App Service web apps naplózási információkat a webkiszolgáló és a webes alkalmazás diagnosztikai funkciók biztosítanak. Ezek logikailag rétegekben [web server diagnosztika](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) és [alkalmazásdiagnosztika](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webalkalmazás-kiszolgáló két fő fejlesztéseivel felderítésére és hibaelhárítási helyek és alkalmazások magában foglalja.

Az első új szolgáltatása alkalmazáskészletek munkavégző folyamatok, webhelyek, alkalmazástartományok és futó kérések valós idejű állapotinformációkat. A második új előnyeit a rendszer részletes nyomkövetési eseményeit, hogy a teljes kérelem / válasz folyamat során a kérelmek nyomon követésére.

Ahhoz, hogy a nyomkövetési eseményeit gyűjteménye, az IIS 7 beállítható úgy, hogy automatikusan rögzítése teljes nyomkövetési naplók XML formátumban, bármely adott kérelem során eltelt idő vagy válasz hibakódok alapján.

#### <a name="web-server-diagnostics"></a>Web server diagnosztika
Engedélyezheti vagy letilthatja a naplók a következő típusú:

-   Részletes naplózás hiba – részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Ez tartalmazhat, amelyek segíthetnek meghatározni, miért a kiszolgáló a hibakódot adott vissza adatokat.

-   Sikertelen kérelmek követésének – részletes információk a sikertelen kérelmek nyomkövetési segítségével dolgozza fel a kérelmet, és minden egyes összetevő szükséges idő az IIS-összetevők többek között. Ez akkor lehet hasznos, ha a hely teljesítményének javítása vagy különítse el, mi okozza-e egy adott HTTP hiba vissza kell adni kívánt.

-   Web Server-naplózás – a W3C bővített naplófájlformátum használata HTTP tranzakciókkal kapcsolatos információkat. Ez akkor hasznos, például a kezelt kéréseket, és hogy hány kérésnek egy adott IP-címről van a teljes webhelymetrikák meghatározásakor.

#### <a name="application-diagnostics"></a>Alkalmazásdiagnosztika
[Application diagnostics](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) lehetővé teszi egy webes alkalmazás által létrehozott adatok rögzítését. ASP.NET alkalmazások használhatják a [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) osztály az alkalmazásnaplóba diagnosztikai adatok naplózására. Az Application Diagnostics nincsenek események, az alkalmazások teljesítményéről és alkalmazások hibáival kapcsolatos két fő eseménytípust. A hibák és problémák oszthatók csatlakozási, biztonsági és meghibásodási problémákra tovább. A probléma az alkalmazás kódjának hibái általában kapcsolódó.

Az Application Diagnostics felületén tekintheti meg a következő módokon csoportosíthatók események:

-   Összes (minden esemény megjelenítése)
-   Az alkalmazáshibák (a kivételesemények megjelenítése)
-   Teljesítmény (a teljesítménnyel kapcsolatos események megjelenítése)

## <a name="storage"></a>Storage
A témakör az Azure storage biztonsági és összefoglaló információt ezeket a képességeket a legfontosabb funkcióira vonatkozó további információkat.

### <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)
A storage-fiókkal, és a szerepköralapú hozzáférés-vezérlést (RBAC) biztonságát. Alapján történő hozzáférés a [tudniuk kell, hogy](https://en.wikipedia.org/wiki/Need_to_know) és [legalacsonyabb jogosultsági szint](https://en.wikipedia.org/wiki/Principle_of_least_privilege) biztonsági elveket elengedhetetlen a szervezeteknek, amelyek az adatok biztonsági házirendek kikényszerítéséhez. A következő hozzáférési jogokat kapnak a megfelelő RBAC szerepkört rendel a csoportok és alkalmazások egy adott hatókörben. Használhat [beépített RBAC-szerepkörök](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles), például a tárolási fiók közreműködői jogosultságokat hozzárendelése felhasználókhoz. A tárolási hozzáférést a kulcsok az a tárolási fiók használata a [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modell szabályozható szerepköralapú hozzáférés-vezérlést (RBAC).

### <a name="shared-access-signature"></a>Közös hozzáférésű Jogosultságkódot
A [közös hozzáférésű jogosultságkód (SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) delegált hozzáférést biztosít a tárfiókon lévő erőforrásokhoz. A biztonsági Társítások azt jelenti, hogy is meg lehet adni egy ügyfél korlátozott engedélyekkel a tárfiókban lévő objektumokhoz, adott időtartamra és meghatározott engedélyekkel vannak beállítva. Ezek korlátozott engedélyek megadásához anélkül, hogy a fiók hozzáférési kulcsait megosztásához.

### <a name="encryption-in-transit"></a>Az átvitel során titkosítás
Titkosítás az átvitel során, akkor egy eszköz adatok védelmének hálózatok közötti átvitelekor. Az Azure Storage segítségével védetté tehet adatokat:
-   [Átviteli szintű titkosítást](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), például a HTTPS vagy abból az Azure Storage-adatok átvitel során.

-   [Hozzá kell fűznie titkosítási](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), például a [SMB 3.0-titkosítás](https://docs.microsoft.com/azure/storage/storage-security-guide) a [Azure fájlmegosztások](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Ügyféloldali titkosítás, az adatok titkosítása a tároló előtt és után tárolási kivitt az adatok visszafejtéséhez.

### <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban
A legtöbb szervezet számára inaktív adatok titkosítása az adatok közös joghatóság alá, adatvédelmi és megfelelőségi felé kötelező lépés. Nincsenek három, adja meg az "Inaktív" adatok titkosítása az Azure storage biztonsági funkciók:

-   [Storage szolgáltatás titkosítási](https://docs.microsoft.com/azure/storage/storage-service-encryption) lehetővé teszi, hogy a társzolgáltatás automatikusan adatok titkosítása az Azure Storage írásakor.

-   [Ügyféloldali titkosítás](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) aktívan titkosítási funkcióval is rendelkezik.

-   [Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) lehetővé teszi az operációs rendszer és az infrastruktúra-szolgáltatási virtuális gép által használt adatok lemezek titkosításához.

### <a name="storage-analytics"></a>Storage Analytics
[Az Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) elvégzi a naplózási, és adja meg a tárfiók metrikák adatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat. Storage Analytics egy társzolgáltatás sikeres és sikertelen kérelmekkel kapcsolatos részletes információkat naplózza. Ezeket az információkat egyes kérelmeket a figyelheti és egy tárolási szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek is be vannak jelentkezve a legjobb alapul. A következő típusú hitelesített kérelmek bejelentkezett:
-   A sikeres kérelmek száma.

-   Sikertelen kérelmek, beleértve az időtúllépés, a sávszélesség-szabályozás, hálózati, engedélyezési és egyéb hibák.

-   A kérelem egy közös hozzáférésű Jogosultságkód (SAS), például és a sikertelen kérelmek használatával.

-   Az analitikai adatok kérelmeket.

### <a name="enabling-browser-based-clients-using-cors"></a>CORS használatával Fiókértékek ügyfelek engedélyezése
[Eltérő eredetű erőforrások megosztása (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) olyan mechanizmus, amely lehetővé teszi a tartományok engedélyt egymással egymás erőforrások eléréséhez. A felhasználói ügynök küld extra fejlécek annak ellenőrzéséhez, hogy engedélyezett-e a JavaScript-kódot egy bizonyos tartományból betölteni egy másik tartományban lévő erőforrások eléréséhez. Az utóbbi tartomány majd ügyfélkéréseire válaszol, a felesleges fejlécek engedélyezésére vagy megtagadására az eredeti tartomány-hozzáférés az erőforrásokhoz.

Azure storage szolgáltatások CORS mostantól támogatják az, hogy ha egyszer már megadta a CORS-szabályokat a szolgáltatás, más tartományokból a szolgáltatásra szóló megfelelően hitelesített kérelem annak meghatározásához, hogy a megadott szabályok szerint engedélyezett legyen-e kiértékelve.
## <a name="networking"></a>Hálózat
A szakasz vonatkozóan nyújt további információkat az Azure-hálózat biztonsága és összefoglaló információt ezeket a képességeket a legfontosabb jellemzők.

### <a name="network-layer-controls"></a>Hálózati réteg vezérlők
Hálózati hozzáférés-vezérlés a folyamat, korlátozza az egyes eszközök és alhálózatok érkező vagy oda irányuló kapcsolat, és jelenti. az alapvető hálózati biztonság. A hálózati hozzáférés-vezérlés célja győződjön meg arról, hogy a virtuális gépek és szolgáltatások elérhetők csak felhasználók és eszközök számára, amely szeretné őket érhető el.

#### <a name="network-security-groups"></a>Network Security Groups (Hálózati biztonsági csoportok)
A [hálózati biztonsági csoport (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) egy tűzfal, és szűrés alapszintű csomag lehetővé teszi a alapuló hozzáférés-vezérlésének egy [5 rekordos](https://www.techopedia.com/definition/28190/5-tuple). Az NSG-k alkalmazás réteg ellenőrző nem ad meg, vagy hitelesített hozzáférés-vezérlést. Egy Azure virtuális hálózatban található alhálózatok és a forgalom egy Azure virtuális hálózatra és az Internet között közötti áthelyezése forgalom vezérlésére használható.

#### <a name="route-control-and-forced-tunneling"></a>Útvonal-vezérléshez és a kényszerített bújtatás
Az Azure virtuális hálózat útválasztási viselkedés képes a kritikus hálózati biztonság és a hozzáférés képessége. Például ha azt szeretné, győződjön meg arról, hogy az Azure Virtual Network érkező vagy oda irányuló összes forgalom végighalad az adott biztonsági virtuális készüléknek, meg kell szabályozása és útválasztási viselkedés testreszabásához. Ehhez felhaszn útvonalak konfigurálása az Azure-ban.

[Felhasználó által definiált útvonalak](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) segítségével testre szabhatja a bejövő és kimenő forgalom áthelyezése útvonalak és egyedi virtuális gépeket vagy a legtöbb biztosítását alhálózatok biztonságos útvonal lehetséges. [A kényszerített bújtatás](https://www.petri.com/azure-forced-tunneling) egy mechanizmus segítségével győződjön meg arról, hogy a szolgáltatások használata nem engedélyezett a kezdeményezzen kapcsolatot az internethez csatlakozó eszközökről.

Ez eltér tud fogadni bejövő kapcsolatokat és majd válaszol rájuk. Internet gazdagépekről a kérelmek megválaszolásához szükséges előtér-webkiszolgálók, és így az Internet-forrása forgalom engedélyezve van, a webes kiszolgálókhoz történő bejövő és a webkiszolgálók válaszolhassanak.

A kényszerített bújtatás kényszerítheti a kimenő forgalom az internethez, hogy a helyi biztonsági proxyk és tűzfalak gyakran használatos.

#### <a name="virtual-network-security-appliances"></a>Virtuális hálózati biztonsági készülékek
Amíg a hálózati biztonsági csoportok, a felhasználói útvonalak és a kényszerített bújtatás biztosít, akkor a szintű biztonságot, a hálózati és a szállítási réteg a [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), előfordulhat, hogy ha azt szeretné engedélyezni a magasabb szintű biztonságot alkalommal a a verem. Egy Azure hálózati biztonság készülék partnermegoldáshoz érhető el továbbfejlesztett hálózati biztonsági funkció. Megtalálhatja a legfrissebb Azure partner hálózati biztonsági megoldások látogasson el a [Azure piactér](https://azure.microsoft.com/marketplace/) és a keresést a "biztonság" és "hálózati biztonság."

### <a name="azure-virtual-network"></a>Azure Virtual Network

Az Azure virtuális hálózat (VNet) az Ön saját hálózatát jelképezi a felhőben. A hálózat az előfizetésre kijelölt Azure-hálózat háló logikai elkülönítése is. A hálózaton belül teljes mértékben irányíthatja az IP-címblokkokat, a DNS-beállításokat, a biztonsági házirendeket és az útválasztási táblázatokat. A vnetet alhálózatokra, és helyezze el az Azure infrastruktúra-szolgáltatási virtuális gépeket (VM) és/vagy [Felhőszolgáltatásokhoz (PaaS szerepkörpéldányok)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) a Azure virtuális hálózatairól.

A virtuális hálózatot ezen felül a helyszíni hálózathoz is csatlakoztathatja az Azure-ban elérhető [kapcsolati lehetőségek](https://docs.microsoft.com/azure/vpn-gateway/) egyikével. Lényegében kiterjesztheti a hálózatot az Azure-ra, az IP-címblokkok teljes körű irányítása és a vállalati méretű Azure által nyújtott előnyök mellett.

Az Azure hálózatkezelés különböző biztonságos távoli hozzáférés szituációkat ismerteti. Ezek közé tartoznak:

-   [Az egyéni munkaállomások csatlakozni az Azure virtuális hálózat](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Csatlakozás a helyi hálózaton egy Azure virtuális hálózathoz egy VPN-](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [A helyszíni hálózat csatlakozni egy Azure virtuális hálózatot egy dedikált WAN-hivatkozás](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Egy Azure virtuális hálózatot csatlakozni egymáshoz](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Az Azure virtuális hálózat és a helyszíni hely közötti hálózati forgalom elküldése, létre kell hoznia egy VPN-átjárón az Azure virtuális hálózat. A [VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) van egy olyan virtuális hálózati átjáró által a titkosított forgalmat nyilvános kapcsolaton keresztül. VPN-átjárók használatával Azure virtuális hálózatot az Azure-hálózat hálón között forgalmat küldeni.

### <a name="express-route"></a>Express Route
A Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) van egy dedikált WAN-kapcsolaton, amely lehetővé teszi a helyszíni hálózatokhoz kiterjeszti a Microsoft cloud könnyíteni a kapcsolat szolgáltatóját egy dedikált titkos kapcsolaton keresztül.

![Express Route](./media/azure-security/azure-security-fig1.png)

Az ExpressRoute használatával kapcsolatokat létesíthet a Microsoft-felhőszolgáltatásokkal, például a Microsoft Azure-ral, az Office 365-tel és a CRM Online-nal. A kapcsolatok lehetnek: bármely elemek közötti (IP VPN) hálózat, pontok közötti Ethernet-hálózat vagy egy virtuális keresztkapcsolat egy kapcsolatszolgáltatón keresztül egy közös elhelyezési létesítményben.

Az ExpressRoute-kapcsolatok ne lépje át a nyilvános interneten, és így tekinthető biztonságosabb, mint a VPN-alapú megoldások. Így az ExpressRoute-kapcsolatok a tipikus internetes kapcsolatoknál megbízhatóbbak, gyorsabbak, gyorsabb a válaszidejük, és biztonságosabbak.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) biztosít egy [alkalmazás kézbesítési vezérlő LÉPETT](https://en.wikipedia.org/wiki/Application_delivery_controller) szolgáltatásként, kínáló lehetőségeket biztosít az alkalmazás terheléselosztási különböző réteg 7.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Lehetővé teszi a webkiszolgáló farm termelékenység optimalizálása kiürítésével a CPU intenzív SSL-lezárást az Alkalmazásátjáró (más néven "SSL-kiszervezés" vagy "SSL-hídképzés"). Más réteg 7 útválasztási lehetőségeit, köztük a ciklikus multiplexelés bejövő forgalmat, munkamenet cookie-alapú kapcsolat, URL-cím elérési út-alapú útválasztási, és csak egyetlen alkalmazás átjáró mögötti több webhely is tartalmazza. Az Azure Application Gateway egy 7. rétegbeli terheléselosztó.

Feladatátvételt és teljesítményalapú útválasztást biztosít a HTTP-kérelmek számára különböző kiszolgálók között, függetlenül attól, hogy a felhőben vagy a helyszínen vannak.

Számos alkalmazás kézbesítési vezérlő LÉPETT-funkciót, beleértve a HTTP betöltése terheléselosztási, a cookie-alapú munkamenet kapcsolat, alkalmazás maga biztosítja [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) kiszervezési, egyéni állapot-mintavételi csomagjai, támogatja a többhelyes, és sok más.

### <a name="web-application-firewall"></a>Web Application Firewall (Webalkalmazási tűzfal)
Webalkalmazási tűzfal csak a [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) szabványos alkalmazás kézbesítési vezérlő LÉPETT függvények Alkalmazásátjáró használó webalkalmazásokat védelmet biztosít. A webalkalmazási tűzfal ezt úgy éri el, hogy védelmet nyújt az alkalmazásoknak az OWASP 10 leggyakoribb webes biztonsági résének többségével szemben.

![Web Application Firewall (Webalkalmazási tűzfal)](./media/azure-security/azure-security-fig1.png)

-   SQL-injektálás elleni védelem

-   Gyakori webes támadások (például parancsinjektálás, HTTP-kéréscsempészet, HTTP-válaszfelosztás és távolifájl-beszúrásos támadás) elleni védelem

-   HTTP protokoll megsértése elleni védelem

-   HTTP protokollanomáliák (például hiányzó gazdagép-felhasználói ügynök és Accept (Elfogadás) fejlécek) elleni védelem

-   Robotprogramok, webbejárók és képolvasók elleni védelem

-   A gyakori alkalmazás konfigurációs hibák (Ez azt jelenti, hogy Apache, az IIS, stb.) észlelése


A webes támadásokkal szembeni védelmet nyújtó központi webalkalmazási tűzfal egyszerűbbé teszi a biztonságfelügyeletet, és nagyobb biztonságot ad az alkalmazásnak a behatolások jelentette veszéllyel szemben. Emellett a WAF-megoldás gyorsabban képes kezelni a biztonsági fenyegetéseket azáltal, hogy kijavítja az ismert biztonsági réseket egy központi helyen, ahelyett hogy az egyes webalkalmazások védelmét biztosítaná. A meglévő alkalmazásátjárókat egyszerűen lehet átalakítani webalkalmazási tűzfallal rendelkező alkalmazásátjárókká.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) felhasználói forgalmat a végpontok különböző adatközpontokban szabályozható. Traffic Manager által támogatott szolgáltatás végpontok közé tartoznak a Azure virtuális gépeken, a webalkalmazások és a felhőszolgáltatások. A Traffic Manager külső, nem Azure-végpontokkal együtt is használható. A TRAFFIC Manager használ a tartománynévrendszer (DNS) át tudja irányítani a legmegfelelőbb végpontra irányuló kéréseket a [forgalom-útválasztási módszer](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) és a végpontok állapotát.

A TRAFFIC Manager forgalom-útválasztási módszer különböző különböző alkalmazás igényeinek, végpont állapota megfelelő számos biztosít [figyelési](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), és automatikus feladatátvételt. A TRAFFIC Manager esetén is lehetséges legyen, beleértve a teljes Azure-régiót hibája.
### <a name="azure-load-balancer"></a>Azure Load Balancer
Az [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. A réteg 4 (TCP, UDP) terheléselosztó bejövő forgalmat egy elosztott terhelésű készlet definiált kifogástalan szolgáltatáspéldányok között ellátó. Az Azure Load Balancer beállítható úgy, hogy:

-   Egyenleg bejövő internetes forgalmat a virtuális gépek betölteni. Ez a konfiguráció az úgynevezett [internetre terheléselosztási](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Betöltési oszthatja el a forgalmat egy virtuális hálózatban lévő virtuális gépek, virtuális gépek felhőszolgáltatások közötti vagy a helyszíni számítógépek és a létesítmények közötti virtuális hálózatban lévő virtuális gépek között. Ez a konfiguráció az úgynevezett [belső terheléselosztás](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Egy adott virtuális gép a külső forgalom továbbítására

### <a name="internal-dns"></a>Belső DNS
A felügyeleti portálon, vagy a hálózati konfigurációs fájlban a Vneten belül használt DNS-kiszolgálók listáját kezelheti. Ügyfél minden vnet is hozzáadhat 12 DNS-kiszolgáló. DNS-kiszolgálók megadása esetén fontos ellenőrizni, hogy az ügyfél DNS-kiszolgálók az ügyfél környezet a megfelelő sorrendben felsorolja. DNS-kiszolgálók listáját a ciklikus multiplexelési nem működnek. Melyek a megadott sorrendben használhatók. Ha a lista első DNS-kiszolgáló nem érhető el, akkor az ügyfél a DNS-kiszolgáló, függetlenül attól, hogy a DNS-kiszolgáló működik-e megfelelő vagy nem használja. Módosítsa a felhasználói virtuális hálózat DNS-kiszolgáló sorrendjét, a DNS-kiszolgálók eltávolítása a listáról, és adja hozzá újra abban a sorrendben, hogy az ügyfél szeretne. A DNS a rendelkezésre állási szempontja, hogy a "CIA" biztonsági háromrészes támogatja.

### <a name="azure-dns"></a>Azure DNS
A [tartománynévrendszer](https://technet.microsoft.com/library/bb629410.aspx), vagy a DNS, felelős fordítása (vagy feloldása) az IP-címét egy webhely vagy szolgáltatás neve. [Az Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) üzemeltetési szolgáltatás DNS-tartományok biztosítani a névfeloldást a Microsoft Azure-infrastruktúra használatával. Ha tartományait az Azure-ban üzemelteti, DNS-rekordjait a többi Azure-szolgáltatáshoz is használt hitelesítő adatokkal, API-kkal, eszközökkel és számlázási információkkal kezelheti. A DNS a rendelkezésre állási szempontja, hogy a "CIA" biztonsági háromrészes támogatja.
### <a name="log-analytics-nsgs"></a>Napló Analytics NSG-k
Az NSG-k engedélyezéséhez a következő diagnosztikai naplófájl kategóriák:
-   Esemény: A mely NSG szabályok érvényesek virtuális gépek és a MAC-címe alapján példány szerepkörök bejegyzést tartalmaz. Ezek a szabályok állapota 60 másodpercenként gyűjti.

-   Szabályok számláló: bejegyzéseket hányszor minden NSG-szabály alkalmazásakor a forgalom engedélyezése vagy megtagadása tartalmazza.

### <a name="azure-security-center"></a>Azure Security Center
A Security Center segítséget nyújtanak a megakadályozása, észlelésében és kezelésében fenyegetések, és növeli a betekintést nyújt, és vezérlése az Azure-erőforrások biztonságának. Biztosít integrált biztonsági monitorozást és az Azure-előfizetésekkel, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik. Hálózati javaslatok center tűzfalak, a hálózati biztonsági csoportok, az konfigurálása bejövő forgalomra vonatkozó szabályokat és több.

Rendelkezésre álló hálózati ajánlások a következők:

-   [Adja hozzá az új generációs tűzfal](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) javasolja, hogy növelje a biztonsági védelmet egy Microsoft-partner adja hozzá a következő generációs tűzfal (NGFW)

-   [Irányíthatja a forgalmat keresztül NGFW csak](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) javasolja, hogy konfigurálja a hálózati biztonsági csoport (NSG) szabályok, amelyek a bejövő forgalom kényszeríti a virtuális géphez a NGFW keresztül.

-   [Hálózati biztonsági csoportok engedélyezi az alhálózatokat vagy a virtuális gépek](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) alhálózatok és virtuális gépek az NSG-k engedélyezését javasolja.

-   [Internetre irányuló végpont-en keresztüli hozzáférés korlátozása](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) azt javasolja, hogy az NSG-ket konfigurálhat a bejövő forgalomra vonatkozó szabályokat.


## <a name="compute"></a>Számítás

A szakasz vonatkozóan nyújt további információkat a legfontosabb jellemzők ezeket a képességeket a terület és összefoglaló információt.

### <a name="antimalware--antivirus"></a>Kártevőirtó & víruskereső
Azure IaaS például a Microsoft, a Symantec, Trend Micro, McAfee és Kaspersky biztonsági szállítóktól származó kártevőirtó szoftver segítségével a virtuális gépek védelmét a rosszindulatú fájlok, hirdetéseket és más fenyegetésekkel szemben. [A Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) Azure Cloud Services és a virtuális gépek esetén a védelmi képessége, amelyik segít azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek. A Microsoft Antimalware biztosít konfigurálható riasztást küld, ha ismert kártevő vagy nemkívánatos szoftverek próbálják telepíteni magukat az Azure rendszeren. A Microsoft Antimalware is telepíthető az Azure Security Centerben

### <a name="hardware-security-module"></a>Hardveres biztonsági modul
Titkosítás és hitelesítés nem biztonság fokozása érdekében kivéve, ha a kulcsok védettek. Egyszerűbbé teheti a felügyeleti és a kritikus titkos kulcsok és a kulcsok biztonsági tárolja őket a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault lehetővé teszi a hitelesített FIPS 140-2 2. szintű szabványok hardveres biztonsági modulokkal (HSM) a kulcsok tárolására. Az SQL Server titkosítási kulcsok biztonsági mentésre vagy [átlátható adattitkosítás](https://msdn.microsoft.com/library/bb934049.aspx) összes tárolhatja Key Vault kulcsok és titkos kulcsokat a alkalmazásokból. Engedélyek és a következő védett elemeknek használatával kezelhető [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Virtuális gép biztonsági mentése
[Azure biztonsági mentés](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) van olyan megoldás, amely megvédi az alkalmazásadatok nulla tőkebefektetés szükségessége, és minimális üzemeltetési költségek. Az alkalmazáshibák az adatok sérülését okozhatja, és emberi hibákat hibák vethet fel saját alkalmazásaiba hibákhoz vezethet. Az Azure Backup szolgáltatással a Windows és Linux rendszerű virtuális gépek védelmének.

### <a name="azure-site-recovery"></a>Azure Site Recovery
A szervezet fontos része [üzleti folytonossági/vészhelyreállítási (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) stratégia rendszer tudja, hogyan kell fenntartani a vállalati munkaterheléseket és alkalmazásokat használatra tervezett és nem tervezett leállások esetén. [Az Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) segít replikáció, feladatátvétel és helyreállítás munkaterhelések és alkalmazások téve, hogy azok elérhetők a másodlagos helyről, ha az elsődleges hely leáll.

### <a name="sql-vm-tde"></a>SQL VIRTUÁLIS GÉP TDE
[Az átlátható adattitkosítás (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) és oszlop a blokkszintű titkosítás (törlése) SQL server titkosítási funkciókat. A titkosítási űrlap ügyfelek kezelésére és tárolására a titkosításhoz használt kriptográfiai kulcsokkal.

Az Azure Key Vault (AKV) szolgáltatás szolgáltatást javítására készült, a biztonsági és kezelésére, ezek a kulcsok egy magas rendelkezésre állású és biztonságos helyen. Az SQL Server-összekötő lehetővé teszi, hogy ezek a kulcsok Azure Key Vault a használni kívánt SQL Server.

SQL Server helyszíni gépeken futtatja, ha nincsenek lépései az Azure Key Vault elérje a helyszíni SQL Server-számítógépen. De az SQL Server Azure virtuális gépeken, időt takaríthat meg az Azure Key Vault-integráció szolgáltatás segítségével. A néhány Azure PowerShell-parancsmagokkal a funkció engedélyezéséhez az SQL virtuális gép a kulcstároló eléréséhez szükséges konfigurációs automatizálható.

### <a name="vm-disk-encryption"></a>Virtuális gép lemeztitkosítás
[Az Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) egy új képesség, amely segít a Windows és Linux IaaS virtuális gépek lemezeit titkosításához. Az iparági szabványos BitLocker a Windows és a DM-crypt program segítségével a Linux operációs rendszer és az adatlemezek kötettitkosítást biztosít vonatkozik. A megoldás integrálva van az Azure Key Vault segítségével szabályozhatja, és a lemez-titkosítási kulcsok és titkos kulcsokat a Key Vault előfizetés kezelése. A megoldás biztosítja azt is, hogy a virtuális gép lemezeinek lévő összes adat titkosítva legyenek-e az Azure tárolás közben.

### <a name="virtual-networking"></a>Virtuális hálózat
Virtuális gépek hálózati kapcsolattal kell. Ez a követelmény kielégítése érdekében az Azure-nak egy Azure virtuális hálózatra kell csatlakoztatni a virtuális gépek. Egy Azure virtuális hálózatra egy logikai szerkezet, a fizikai Azure hálózati háló platformra épül. Minden egyes logikai [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) el különítve a többi Azure virtuális hálózatot. Elkülönítés segítségével biztosítja, hogy a hálózati forgalmat a központi telepítés nem érhető el más Microsoft Azure-ügyfelek.

### <a name="patch-updates"></a>Javítás frissítések
Javítás frissítések az alapot biztosít keresése és a lehetséges problémák kijavítása, és egyszerűbb lehet a szoftverfrissítések célirányos felügyeleti folyamata egyaránt telepítenie kell a vállalati szoftverfrissítések számának csökkentésével, és megfelelőségének figyeléséhez arra a képességére növelésével.

### <a name="security-policy-management-and-reporting"></a>Biztonsági házirendek kezelése és jelentéskészítés
[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) megakadályozása, észleli, és fenyegetéseket, és növeli a betekintést nyújt válaszol, és vezérlése az Azure-erőforrások biztonságának nyújt segítséget. Biztosít integrált biztonsági monitorozást és az Azure-előfizetésekkel, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és számos biztonsági megoldással együttműködik.

### <a name="azure-security-center"></a>Azure Security Center
A Security Center az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

## <a name="identify-and-access-management"></a>Identitások és hozzáférések kezelése

Azonosító-alapú hozzáférés-vezérlést kezdődik rendszerek, alkalmazások és adatok védelme. Microsoft üzleti termékek és szolgáltatások beépített identitások és hozzáférések felügyeleti szolgáltatások szervezeti és személyes adatainak védelme a jogosulatlan hozzáférés közben, így rendelkezésére jogos felhasználók amikor és ahol azokat szükség lenne rá.

### <a name="secure-identity"></a>Biztonságos identitás
A Microsoft termékei és szolgáltatásai között több biztonsági gyakorlatokkal és technológiák identitások és hozzáférések felügyelete használja.
-   [A multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) megköveteli a felhasználóktól több módszereket használja, a hozzáférés, a helyszíni és a felhőben. Egyszerű hitelesítési beállítások széles közben egyszerre egy egyszerű bejelentkezési folyamat rendelkező felhasználók erős hitelesítés biztosít.

-   [A Microsoft Authenticator](https://aka.ms/authenticator) felhasználóbarát multi-factor Authentication felhasználói élményt, amely együttműködik a Microsoft Azure Active Directory és a Microsoft-fiókkal, és wearables és az ujjlenyomat-alapú jóváhagyások támogatását is magában foglalja.

-   [Jelszó a házirend betartatása](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) hosszúságára és összetettségére vonatkozó követelmények, kényszerített rendszeres elforgatás és a fiók zárolása sikertelen hitelesítési kísérlet után azáltal növeli a hagyományos jelszavak biztonságát.

-   [Jogkivonat-alapú hitelesítés](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) lehetővé teszi a hitelesítés az Active Directory összevonási szolgáltatások (AD FS) keresztül vagy a külső biztonságos token rendszerekben.

-   [Szerepköralapú hozzáférés-vezérlést (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) lehetővé teszi a hozzáférést a felhasználó hozzárendelt szerepkör, így könnyen adhat a felhasználók csak olyan mértékű hozzáférést a feladat feladataik elvégzéséhez szükséges. Az RBAC a szervezet üzleti modelljéhez és kockázattűrése testre.

-   [Az Identitáskezelés (hibrid identitás) integrált](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) fenntarthatja a felhasználói hozzáférés belső adatközpontok és felhőszolgáltatások platformon, a hitelesítés és engedélyezés az összes erőforráshoz egyetlen felhasználói azonosítót létrehozása.

### <a name="secure-apps-and-data"></a>Biztonságos alkalmazások és adatok
[Az Azure Active Directory](https://azure.microsoft.com/services/active-directory/), egy átfogó identitás- és hozzáférés kezelési felhőalapú megoldás, segítségével biztonságban adatok az alkalmazások a helyen, és a felhőben, és egyszerűbbé teszi a felhasználók és csoportok. Egyesíti a címtárszolgáltatások mag, speciális identitás irányítás, biztonsági és alkalmazáshozzáférés-kezeléshez, és megkönnyíti a fejlesztők számára a csoportházirend-alapú Identitáskezelés saját alkalmazásokba. Az Azure Active Directory javítása érdekében, az Azure Active Directory Basic, a Premium P1 és a Premium P2 verziójával fizetős képességeket is hozzáadhat.

| Szabad / közös szolgáltatások     | Alapvető szolgáltatások    |Prémium P1 szolgáltatások |Prémium P2 szolgáltatások | Az Azure Active Directory Join – Windows 10 csak a kapcsolódó szolgáltatások|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Címtárobjektumok](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [felhasználó/csoport Management (hozzáadása/frissítés/törlés) / felhasználó-alapú létesítési, eszközregisztráció](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [egyszeri bejelentkezés (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [önkiszolgáló A jelszó módosítása a felhőbeli felhasználóinak](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (szinkronizálási motor, amely kiterjeszti a helyszíni címtárakat az Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [biztonsági / jelentései](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Csoportalapú hozzáférés-kezelés / létesítési](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [önkiszolgáló jelszó-változtatási a felhőbeli felhasználóinak](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [vállalati arculat (bejelentkezési oldalak/hozzáférési Panel Testreszabás)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [SLA 99,9 %-os](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Önkiszolgáló csoport és az alkalmazás felügyeleti/önkiszolgáló-üzemeltetési alkalmazás kiegészítéseket/dinamikus csoportok](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [önkiszolgáló jelszó alaphelyzetbe állítása/módosítás/zárolásának feloldása a helyszíni késleltetve visszaírt](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [multi-factor Authentication Hitelesítési (felhőalapú és helyszíni (MFA kiszolgáló))](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [MIM CAL + a MIM-kiszolgáló](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [ A jelszó automatikus csoportfiókoknak váltása](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [Azonosító adatok védelmét](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Egy eszköz csatlakoztatása az Azure ad-vel, asztali SSO, a Microsoft Passport az Azure Active Directory, a rendszergazda a Bitlocker helyreállítási](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [MDM automatikus igénylés, önkiszolgáló Bitlocker-helyreállítást, a Windows 10-eszközöket az Azure AD további helyi rendszergazda Csatlakozás](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) egy prémium szintű Azure Active Directoryban, amely lehetővé teszi a felhőalapú alkalmazások, a szervezet munkatársai által használt funkciója.

- [Az Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) egy biztonsági szolgáltatás, amellyel Azure Active Directory anomáliadetektálási az észlelési képességek a kockázati eseményekről és a potenciális biztonsági réseket, amely befolyásolhatja egyesített nézetét biztosítja a szervezet identitásait.

- [Az Azure Active Directory tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/) teszi lehetővé az Azure virtuális gépek csatlakoztatása a tartományhoz nem kell a tartományvezérlőket. Felhasználók a vállalati Active Directory hitelesítő adatok használatával jelentkezzen be a virtuális gépeket, és zavartalanul tud hozzáférni az erőforrásokhoz.

- [Az Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) egy magas rendelkezésre állású, globális identitás szolgáltatás, amely méretezhető, több száz millió identitások és mobile kapcsolódik felhasználók felé néző alkalmazásokhoz és webes platformokat. Az ügyfelek lehet bejelentkezni, használja a meglévő közösségi fiókokat testre szabható felhasználói élmény mellett az alkalmazások, vagy létrehozhat új önálló hitelesítő adatokat.

- [Az Azure Active Directory B2B együttműködés](https://aka.ms/aad-b2b-collaboration) egy biztonságos partner integrációs megoldás, amely támogatja a vállalatokon átívelő kapcsolatok hozzáférhet a vállalati alkalmazások és adatok szelektív a az önállóan felügyelt partnerek engedélyezése identitások.

- [Az Azure Active Directory csatlakozási](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) lehetővé teszi a Windows 10-eszközökre, a központi felügyelet felhő lehetőségek bővítése céljából. A felhasználók csatlakoznak a vállalati vagy szervezeti felhőalapú Azure Active Directoryn keresztül lehetővé teszi, és leegyszerűsíti az alkalmazások és erőforrások elérésére.

- [Az Azure Active Directory Alkalmazásproxyjával](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) webalkalmazások helyben tárolt egyszeri Bejelentkezéssel és biztonságos távoli hozzáférést nyújt.

## <a name="next-steps"></a>Következő lépések
- [Ismerkedés a Microsoft Azure biztonsági](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Azure-beli adatai és szolgáltatásai számára védelmet nyújtó Azure-szolgáltatások és funkciók

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Fenyegetések megelőzése, észlelése és elhárítása az Azure-erőforrások jobb láthatóságával és kézben tartásával

- [Biztonsági állapotfigyelés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

A megfigyelési lehetőségek az Azure Security Centerben a házirendek megfelelőségének figyeléséhez.
