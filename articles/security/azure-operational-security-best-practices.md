---
title: Az Azure Operational ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ez a cikk az Azure Operational Security ajánlott eljárásokat biztosít.
services: security
documentationcenter: na
author: unifycloud
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: d8f6ad48c62ff2021c91e593cee44dd6f5551247
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44297908"
---
# <a name="azure-operational-security-best-practices"></a>Azure Operational Security – ajánlott eljárások
Az Azure Operational Security hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök, Microsoft Azure-ban. Az Azure Operational Security olyan keretrendszer, amely magában foglalja a különböző képességeket, amelyek a egyedülálló rendszereiből, például a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program keresztül szerzett ismeretek épül és a kiberbiztonsági fenyegetések világának.

Ebben a cikkben bemutatjuk, Azure database ajánlott biztonsági eljárások gyűjteménye. Ajánlott eljárások az Azure adatbázis biztonsági származnak az funkciót, és az ügyfelek a funkciókat, például saját magának.

A minden egyes ajánlott eljárás az hogy ismertetik:
-   Mi az az ajánlott eljárás szerint
-   Miért ajánlott eljárás, hogy engedélyezni szeretné
-   Mi lehet az eredmény, ha Ön nem engedélyezi az ajánlott eljárás szerint
- Hogyan tudhat meg az ajánlott eljárás engedélyezése

Ez a cikk az Azure Operational ajánlott biztonsági eljárások az egy konszenzus véleményét, és az Azure platform képességeit és szolgáltatáskészleteket, alapján ez a cikk írásának időpontjában léteznek. Vélemények és technológiák időbeli változásait, és ez a cikk a változások követése érdekében rendszeresen frissül.

Ebben a cikkben tárgyalt ajánlott eljárások Azure működési biztonság:

-   Figyelése, kezelése és védelme, felhő-infrastruktúra
-   Identitás kezelése és egyszeri bejelentkezés (SSO) végrehajtása
-   Kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálhatja a problémákat
-   Szolgáltatások egy központi figyelési megoldást az
-   Megelőzését, észlelését és fenyegetések
-   Végpontok közötti forgatókönyv-alapú hálózatfigyelési
-   A telepítés biztonságának bevált DevOps-eszközök használatával

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Figyelése, kezelése és védelme, felhő-infrastruktúra
IT-üzemeltetési felelős az Adatközpont-infrastruktúrába, alkalmazások és adatok, beleértve a stabilitás és ezek a rendszerek biztonságát kezelése. Biztonsági következtetéseket között összetett IT-környezetek gyakran növelése azonban szükség van a szervezetek számára, hogy cobble együtt több biztonsági és felügyeleti rendszerek adatait.

[A Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) van a Microsoft felhőalapú informatikai felügyeleti megoldása, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúrára.

[Az OMS biztonsági és auditálási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) IT-részleg aktívan figyeljük az összes erőforrást, amely megkönnyíti a biztonsági incidens hatásának minimalizálása érdekében. Az OMS biztonsági és auditálási rendelkezik az erőforrások figyeléséhez használt biztonsági tartományok.

Az OMS termékkel kapcsolatos további információkért tekintse meg az [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) című cikket.

Segítséget megelőzését, észlelését és az azokra való reagálásban, [Operations Management Suite (OMS) biztonsági és auditálási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) gyűjt és dolgoz fel az erőforrások adatait, amely tartalmazza:

-   Biztonsági eseménynapló
-   A Windows esemény-nyomkövetés (ETW) eseményei
-   AppLocker naplózási események
-   A Windows tűzfal naplója
-   Advanced Threat Analytics-események
-   A biztonsági alapkonfiguráció értékelése
-   Kártevőirtó-felmérés eredménye
-   Frissítések/javítások felmérésének eredménye
-   Az ügynökön kifejezetten engedélyezett Syslog-adatfolyamok


## <a name="manage-identity-and-implement-single-sign-on"></a>Identitás kezelése és az egyszeri bejelentkezés megvalósítása
[Az Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) van a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatása.

[Az Azure AD](https://azure.microsoft.com/services/active-directory/) is magában foglalja a teljes körű [Identitáskezelés](https://docs.microsoft.com/azure/security/security-identity-management-overview) funkciókat, mint például [a multi-factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), az eszköz regisztrálása, önkiszolgáló jelszókezelés önkiszolgáló csoportkezelés, kiemelt jogosultságú fiókok kezelése, szerepköralapú hozzáférés-vezérlés, az alkalmazás használatának részletes naplózást, és biztonsági monitorozási és riasztási figyelése.

Az alábbi képességeket segítséget nyújtanak a felhőbeli alkalmazások biztonságos, leegyszerűsítheti az IT-folyamatok, csökkentheti a költségeket és győződjön meg arról, hogy vállalati előírásoknak való megfelelés miatt:

-   Identitás- és hozzáférés-kezelés a felhőben
-   Egyszerűbb hozzáférés bármely felhőalkalmazáshoz
-   Bizalmas jellegű adatok és alkalmazások védelme
-   Önkiszolgáló lehetőségek engedélyezése az alkalmazottaknak
-   Integráció az Azure Active Directoryval

### <a name="identity-and-access-management-for-the-cloud"></a>Identitás- és hozzáférés-kezelés a felhőben
Az Azure Active Directory (Azure AD) egy átfogó [identitás- és hozzáférés kezelési felhőmegoldást](https://www.microsoft.com/cloud-platform/identity-management), amely lehetővé teszi a felhasználók és csoportok felügyeletére funkciók robusztus készletét. És biztonságosabbá teszi a hozzáférést a helyszíni és felhőalapú alkalmazásokhoz, többek között a Microsoft webszolgáltatásaihoz, például az Office 365 és a sok nem Microsofttól származó mint a szoftverszolgáltatások (SaaS) alkalmazások.
Ha többet az Azure AD identity protection használatának engedélyezése, lásd: [engedélyezése az Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Egyszerűbb hozzáférés bármely felhőalkalmazáshoz
[Egyszeri bejelentkezés engedélyezése](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) a felhasználók egyszerűbben érhessenek el több ezer felhőalapú alkalmazások Windows, Mac, Android és IOS rendszerű eszközökről. Felhasználók alkalmazásokat egy személyre szabott webes hozzáférési panelről vagy mobilalkalmazás segítségével céges hitelesítő adataikat használva indíthatják el. Az Azure AD-alkalmazásproxy-modul segítségével nemcsak az SaaS-alkalmazásokhoz, és rendkívül biztonságos távoli hozzáférést és egyszeri bejelentkezést a helyszíni webalkalmazások közzétételéhez.

### <a name="protect-sensitive-data-and-applications"></a>Bizalmas jellegű adatok és alkalmazások védelme
Engedélyezése [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) jogosulatlan hozzáférés elkerülése érdekében a helyszíni és felhőalapú alkalmazások egy további hitelesítési szint beiktatásával. Védje vállalkozását és csökkentse az esetleges fenyegetések kockázatát biztonsági monitoringgal és értesítésekkel, illetve a szokásostól eltérő bejelentkezéseket felismerő, gépi tanuláson alapuló jelentésekkel.

### <a name="enable-self-service-for-your-employees"></a>Önkiszolgáló lehetőségek engedélyezése az alkalmazottaknak
A fontos feladatokat – mint például az új jelszavak létrehozása, valamint a csoportok létrehozása és kezelése – delegálhatja alkalmazottainak. [Önkiszolgáló jelszómódosítás engedélyezése](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password)alaphelyzetbe állítása és az önkiszolgáló csoportkezelés az Azure ad-ben.

### <a name="integrate-with-azure-active-directory"></a>Integráció az Azure Active Directoryval
Kiterjesztheti [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) és bármely más a helyszíni címtárakat az Azure AD egyszeri bejelentkezés az összes felhőalapú alkalmazáshoz engedélyezéséhez. A bármilyen helyi címtárból származó felhasználói attribútumok automatikusan szinkronizálhatók a felhőalapú címtárral.

Engedélyezését és az Azure Active Directory-integrációval kapcsolatos további tudnivalókért olvassa el a cikket [a helyszíni címtárak integrálása az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálhatja a problémákat
[Az Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) naplózhatja és mérőszámadatokat biztosít egy tárfiókot. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat.

Új storage-tárfiókok alapértelmezés szerint engedélyezve vannak a Storage Analytics mérőszámainak áttekintését. Naplózás engedélyezése és a metrikák és naplózás az Azure Portalon; konfigurálása További információkért lásd: [monitorozása az Azure Portal tárfiók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. A szolgáltatás tulajdonságainak beállítása műveletet használja ahhoz, hogy a Storage Analytics külön-külön az egyes szolgáltatások.

A Storage Analytics és más eszközök használatával azonosítsa, diagnosztizálása és hibaelhárítása az Azure Storage szolgáltatással kapcsolatos problémák a részletes útmutatót lásd: [figyelése, diagnosztizálása és hibaelhárítása a Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Engedélyezését és az Azure Active Directory-integrációval kapcsolatos további tudnivalókért olvassa el a cikket [engedélyezése és konfigurálása a Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Szolgáltatások figyelése
A felhőalapú alkalmazások összetettek a részek. Győződjön meg arról, hogy az alkalmazás mindig elérhető fel az adatokat és kifogástalan állapotban fut figyelést biztosít. Segít, hogy ki a lehetséges problémák stave vagy korábbi kiépítettektől hibaelhárítása. Monitorozási adatok segítségével emellett részletes elemzéseket kaphat az alkalmazásról. A Tudásbázis segítségével javíthatja az alkalmazás teljesítménye vagy Karbantarthatóság, vagy, amelyek egyébként manuális beavatkozásra műveletek automatizálása.

### <a name="monitor-azure-resources"></a>Azure-erőforrások figyelése
[Az Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) a platformszolgáltatás, amely egyetlen adatforrás biztosít az Azure-erőforrások figyeléséhez. Az Azure Monitorral az egyes erőforrásoktól az Azure-ba érkező metrikákat és naplókat láthatóvá teheti, lekérdezheti, átirányíthatja, archiválhatja, illetve ezekkel különböző műveleteket is végezhet. Ezekkel az adatokkal a Monitor portálpaneljén, valamint a [Monitor PowerShell-parancsmagok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), a [platformfüggetlen parancssori felület](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) vagy az [Azure Monitor REST API-k](https://msdn.microsoft.com/library/dn931943.aspx) használatával dolgozhat.

### <a name="enable-autoscale-with-azure-monitor"></a>Automatikus skálázás engedélyezése az Azure monitorral
Engedélyezése [Azure Monitor automatikus skálázása](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) csak virtuálisgép-méretezési csoportok (VMSS), a cloud services, app service-csomagot és app service Environment-környezetek vonatkozik.

### <a name="manage-roles-permissions-and-security"></a>Szerepkörök kezelése engedélyek és biztonság
Számos csapat kell feltétlenül [figyelési való hozzáférés szabályozásának](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) adatot és beállítást. Például, ha kizárólag a figyelést (a támogatási szakértők, fejlesztő és üzemeltető mérnököknek) dolgozó csapat tagjai rendelkezik, vagy ha egy felügyelt szolgáltató használ, érdemes hozzáférést biztosít nekik a csak figyelési adatok korlátozásával hozhat létre, módosít, vagy erőforrások törlése.

Ez bemutatja, hogyan gyorsan egy beépített monitorozási RBAC-szerepkör hajtja végre egy felhasználó az Azure-ban, vagy egy felhasználóhoz, aki csak korlátozott felügyeleti engedélyekre van szüksége a saját egyéni szerepkör létrehozása. Majd az Azure Monitor-kapcsolódó erőforrások és a bennük adatokhoz való hozzáférés korlátozásáról vonatkozó biztonsági szempontokat ismerteti.

## <a name="prevent-detect-and-respond-to-threats"></a>Megelőzését, észlelését és fenyegetések
Security Center fenyegetésészlelése úgy, hogy automatikusan összegyűjti a biztonsági információkat az Azure-erőforrások, a hálózat és a csatlakoztatott partnermegoldások működik. Ezt az információt, gyakran naplókezelője azonosítani a fenyegetéseket, több forrásból származó adatokat elemzi azt. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

-   [Hozzon létre egy biztonsági szabályzatot](https://docs.microsoft.com/azure/security-center/security-center-policies) Azure-előfizetéséhez.
-   Használja a [a Security Center javaslatokat](https://docs.microsoft.com/azure/security-center/security-center-recommendations) az Azure-erőforrások védelmére.
-   Tekintse át és kezelheti az aktuális [biztonsági riasztások](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center az Azure portál beépített funkcióiként biztosítja a fenyegetések egyszerű és hatékony megelőzését, észlelését és az azokra való reagálást. A főbb funkciók a következők:

-   Felhőbeli biztonsági állapot megjelenítése
-   Ellenőrzött felhőbiztonság
-   Könnyen telepíthető integrált felhőbeli biztonsági megoldások
-   Gyors reagálás az észlelt fenyegetésekre

### <a name="understand-cloud-security-state"></a>Felhőbeli biztonsági állapot megjelenítése
Az Azure Security Center egy helyen jeleníti meg minden Azure-erőforrás biztonsági állapotát. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági ellenőrzések teljesülnek-e, és megfelelően konfigurálva, és gyorsan azonosíthatja a minden olyan erőforrásokat, amelyek beavatkozást igényelnek.

### <a name="take-control-of-cloud-security"></a>Ellenőrzött felhőbiztonság
Definiálása [biztonsági házirendek](https://docs.microsoft.com/azure/security-center/security-center-policies) az Azure-előfizetéseihez a vállalat felhőbeli biztonsági igényeinek megfelelő, amelyeket előfizetésenként az alkalmazások típusához vagy az egyes előfizetések adatainak érzékenysége. Szabályzatokkal vezérelt ajánlásokkal vezetheti végig az erőforrás-tulajdonosokat a szükséges ellenőrzések bevezetésének lépésein, így kiküszöbölheti a találgatást a felhő biztonsági tényezői közül.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Könnyen telepíthető integrált felhőbeli biztonsági megoldások
[Biztonsági megoldások engedélyezése](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) a Microsoft és partnerei, köztük az iparág legjobb tűzfalait, kártevőirtó szoftvereit. A leegyszerűsített telepítéssel könnyen használatba veheti a biztonsági megoldásokat – a rendszer még a hálózati változásokat is konfigurálja Önnek. Emellett a partnerek megoldásaiból származó biztonsági eseményeket is összegyűjti elemzés és riasztás céljából.

### <a name="detect-threats-and-respond-fast"></a>Gyors reagálás az észlelt fenyegetésekre
Az aktuális és újonnan felbukkanó felhőbeli fenyegetések megelőzéséhez integrált, elemzéssel támogatott megközelítésre van szükség. A Microsoft globális kombinálásával [fenyegetésfelderítés](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) és szakértelmet ügyei elemzéseit felhőbeli biztonsági események között az Azure-környezetek, a Security Center segít a tényleges fenyegetések korai észlelését, és csökkenti a vakriasztások. Felhőbeli biztonsági riasztások szolgáltatnak az összehangolt támadásokról és az érintett erőforrásokról támadássorozatot és módszereket javasolnak problémák elhárítására és a gyors helyreállításra.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Végpontok közötti forgatókönyv-alapú hálózatfigyelési
Ügyfelek előkészítését és különböző egyes hálózati erőforrások, például a virtuális hálózaton, ExpressRoute, az Application Gateway, terheléselosztók és több összeállítása az Azure-ban egy végpontok közötti hálózati készítését. Figyelés a hálózati erőforrások mindegyike érhető el.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) van egy regionális szolgáltatás, amely lehetővé teszi a figyelési és diagnosztizálási tevékenységet végezhet hálózati forgatókönyv: szintjét, a, és az Azure-ból. A hálózati diagnosztikai és vizualizációs eszközök a Network Watcherrel elérhető segítenek megérteni, diagnosztizálása és betekintést nyerhet az Azure-ban a hálózati.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Távoli hálózatok automatikus figyelése csomagrögzítéssel
A Network Watcherrel hálózati problémákat monitorozhat és diagnosztizálhat a virtuális hálózatokba való bejelentkezés nélkül. Az eseményindító [csomagrögzítés](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) a riasztások beállítása és csomagszinten teljesítményének valós idejű adatokhoz való hozzáférésre. Ha problémát észlel, a jobb diagnosztizálás érdekében részletes vizsgálatot végezhet.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Információszerzés a hálózati forgalomról folyamatnaplók használatával
A hálózati forgalom minta használatával részletesebben felderíthetőek [hálózati biztonsági csoportok folyamatnaplóit](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Forgalmi naplók által biztosított segít a szükséges megfelelőségéhez, auditálásához és a hálózati biztonsági profil figyelési adatok gyűjtését végzi.

### <a name="diagnose-vpn-connectivity-issues"></a>VPN-kapcsolódási problémák diagnosztizálása
A Network Watcher lehetővé teszi a [a VPN Gateway és kapcsolatok leggyakoribb problémák diagnosztizálásához](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Így nemcsak a probléma azonosításához, hanem használata segít a további vizsgálat céljából a részletes naplókat.

A Network watcher konfigurálása és engedélyezését a kapcsolatos további tudnivalókért olvassa el a cikket [Network watcher konfigurálása](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>A telepítés biztonságának bevált DevOps-eszközök használatával
A rendszer többek között a lista az Azure DevOps-eljárások a Microsoft Cloud helyre, ami hatékonyabbá teszi a vállalatok és a csapatok.

-   **Infrastruktúra-Code (IaC):** infrastruktúra mint kód olyan technológiák és eljárások, amelyek az informatikai szakembereknek nyújtanak segítséget, távolítsa el a társított a napi build és a moduláris infrastruktúra felügyeleti terheket. Lehetővé teszi a hozhat létre és kezelhet modern kiszolgáló környezetükben úgy, hogy hogyan hozhat létre és alkalmazáskód karbantartását ugyanúgy történik, az informatikai szakemberek számára. Az Azure-hoz, hogy rendelkezik [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) lehetővé teszi, hogy alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.
-   **Folyamatos integráció és üzembe helyezés:** konfigurálhatja az Azure DevOps projects, [automatikus létrehozása és üzembe helyezése](https://www.visualstudio.com/docs/build/overview) Azure-webalkalmazások vagy felhőszolgáltatások. Az Azure DevOps ezután egy Azure-ban minden kód bejelentkezés után automatikusan üzembe helyezi a bináris fájlokat. Az itt leírtak szerint, a csomag buildelési folyamat megegyezik a csomag parancsot a Visual Studióban, és a közzétételi lépéseket egyenértékűek a Közzététel parancsot a Visual Studióban.
-   **A Kiadáskezelés:** Visual Studio [kiadási felügyeleti](https://msdn.microsoft.com/library/vs/alm/release/overview) többfázisú üzembe helyezésének automatizálása, és a kibocsátási folyamat kezeléséhez egy nagyszerű megoldás. Gyors, könnyen és gyakran felszabadítása felügyelt folyamatos üzembe helyezés folyamatokat hozhat létre. A kiadási felügyeleti sokkal automatizálható, hogy a kibocsátási folyamat, és azt is előre meghatározott jóváhagyási munkafolyamatokat. Helyszíni üzembe helyezése és kiterjesztése a felhőbe, és szükség szerint testre szabhatja.
-   **Alkalmazás teljesítményének figyelése:** a hibák észlelése, problémák megoldásában, és folyamatosan fejlesztheti alkalmazásait. Gyorsan diagnosztizálhat bármilyen problémát az élő alkalmazással. Megismerheti, hogyan használják a felhasználók az alkalmazást. Konfigurációs JS-kód és a egy webconfig-bejegyzés hozzáadása egyszerűen kérdését, és megjelenik a eredmények percek alatt a portálon az adataival. [App insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) & szervizelési problémák gyorsabb felismerése a vállalatoknak nyújt segítséget.
-   **Load Testing & automatikus skálázási:** teljesítményproblémákat okozhat az alkalmazás központi telepítési minőségének javítására is találhatók, és ellenőrizze, hogy az alkalmazás mindig elérhető, az üzleti méretformátumok figyelembe vétele vagy a kell. Ellenőrizze, hogy az alkalmazás képes kezelni az adatforgalom a következő indítási vagy marketing kampány. Kezdjen programokat futtatni a felhő alapú [terheléses teszteket](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) szinte idő alatt az Azure DevOps.

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [Azure működési biztonság](https://docs.microsoft.com/azure/security/azure-operational-security).
- További [Operations Management Suite |} A biztonság és megfelelőség](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Ismerkedés az Operations Management Suite biztonsági és auditálási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
