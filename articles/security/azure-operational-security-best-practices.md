---
title: Az Azure Operational bevált gyakorlatát |} Microsoft Docs
description: Ez a cikk számos gyakorlati tanácsok a működési biztonság Azure.
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: bb7e0df046ecc2ffcd3fa59ac53edf36095933c7
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-operational-security-best-practices"></a>Az Azure Operational biztonsági gyakorlati tanácsok
Az Azure Operational biztonsági hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelmére az adatok, alkalmazások és egyéb eszközök a Microsoft Azure-ban. Az Azure Operational biztonsági egy keretrendszer, amely magában foglalja a különböző képességeket, amelyek a Microsoftnak, beleértve a Microsoft biztonsági fejlesztési életciklus (SDL), a Microsoft Security Response Center program egyedi keresztül tapasztalatok épül és részletes tájékoztatást nyújthatnak a a számítógépes fenyegetésekről alkotott képet.

Ez a cikk arról lesz szó Azure-adatbázishoz ajánlott biztonsági eljárások gyűjteménye. Az alábbi gyakorlati tanácsok az Azure-adatbázis biztonsági származtatja tapasztalatunk, és az ügyfelek feladatokat, például saját maga.

A minden ajánlott eljárás az hogy ismertetik:
-   Mi az az ajánlott eljárás szerint
-   Miért érdemes, hogy a legjobb engedélyezése
-   Milyen lehet az eredmény, ha nem engedélyezi az ajánlott eljárás szerint
- Hogyan megismerheti az ajánlott eljárás engedélyezése

Azure biztonsági gyakorlati tanácsok az üzemeltetéshez cikkben az együttműködési véleményét, és az Azure platform olyan képességeit és a szolgáltatáskészletek, alapján ez a cikk írásának időpontjában léteznek. Vélemények és technológiák változnak az idők, és ez a cikk a változások követése érdekében rendszeresen frissül.

Az Azure Operational ajánlott biztonsági eljárások cikkben említett a következők:

-   Figyeléséhez, kezeléséhez és felhő-infrastruktúra védelme
-   Identitás kezelése és az egyszeri bejelentkezés (SSO) végrehajtása
-   Kérelmek nyomon követése, elemezheti a használati trendeket és eseményadatokat
-   A központi felügyeleti megoldás-szolgáltatások figyelése
-   Megakadályozása, észlelésében és kezelésében fenyegetések
-   Forgatókönyv-alapú hálózati végpont figyelő
-   A telepítés biztonságának bevált DevOps eszközökkel

## <a name="monitor-manage-and-protect-cloud-infrastructure"></a>Figyeléséhez, kezeléséhez és felhő-infrastruktúra védelme
IT-üzemeltetők adatközpont infrastruktúrájában, alkalmazások és adatok, beleértve a stabilitás és az ilyen rendszerek biztonsági felügyeletéért felelős. Biztonság és ellenőrzött való növelésével gyakran összetett informatikai környezetben különböző azonban van szükség a szervezetek cobble egyszerre több biztonsági és felügyeleti rendszerek adatait.

[A Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) a Microsoft felhőalapú informatikai felügyeleti megoldás, amely segít a kezelése és védelme a helyszíni és felhőalapú infrastruktúra.

[OMS biztonsági, naplózási megoldást](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) informatikai aktív figyelését az összes erőforrást, amely segítségével a biztonsági események gyakorolt hatásának minimalizálása érdekében. OMS biztonsági és naplózási rendelkezik biztonsági tartományok erőforrások figyeléséhez használható.

Az OMS termékkel kapcsolatos további információkért tekintse meg az [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx) című cikket.

Segítséget nyújtanak a megakadályozása, észlelésében és kezelésében fenyegetések, [Operations Management Suite (OMS) biztonsági és naplózási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) gyűjt, és feldolgozza az erőforrások adatait, amely tartalmazza:

-   Biztonsági eseménynapló
-   A Windows esemény-nyomkövetés (ETW) eseményei
-   AppLocker naplózási események
-   A Windows tűzfal naplója
-   Advanced Threat Analytics-események
-   A biztonsági alapkonfiguráció értékelése
-   Kártevőirtó-felmérés eredménye
-   Frissítések/javítások felmérésének eredménye
-   Syslog adatfolyamok kifejezetten engedélyezett az ügynökön


## <a name="manage-identity-and-implement-single-sign-on"></a>Identitás kezelése és az egyszeri bejelentkezés megvalósítása
[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) a Microsoft több-bérlős felhőalapú címtár, és az identity management szolgáltatás.

[Az Azure AD](https://azure.microsoft.com/services/active-directory/) is tartalmazó teljes csomag [Identitáskezelés](https://docs.microsoft.com/azure/security/security-identity-management-overview) képességeket telepít, például [a multi-factor authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication), eszközregisztráció, önkiszolgáló jelszókezelés önkiszolgáló csoportfelügyelet, rendszerjogosultságú fiókok kezelése, szerepköralapú hozzáférés-vezérlés, alkalmazás-használat, figyelés, a részletes naplózást, és biztonsági figyelést és riasztást.

Az alábbi képességeket is felhőalapú alkalmazások védelméhez, IT-folyamatok egyszerűsítésére, költségeket és érdekében győződjön meg arról, hogy teljesíti-e a vállalati megfelelőség célok:

-   Identitás- és hozzáférés-kezelés a felhőben
-   Egyszerűbb hozzáférés bármely felhőalkalmazáshoz
-   Bizalmas jellegű adatok és alkalmazások védelme
-   Önkiszolgáló lehetőségek engedélyezése az alkalmazottaknak
-   Integráció az Azure Active Directoryval

### <a name="identity-and-access-management-for-the-cloud"></a>Identitás- és hozzáférés-kezelés a felhőben
Azure Active Directory (Azure AD) egy átfogó [identitás- és hozzáférés kezelési felhőalapú megoldás](https://www.microsoft.com/cloud-platform/identity-management), amely lehetővé teszi egy robusztus szolgáltatásaira felhasználók és csoportok kezelése. Ennek segítségével biztonságos hozzáférés a helyszíni és felhőalapú alkalmazások, beleértve a Microsoft web szolgáltatást például Office 365 és sok nem Microsofttól származó szoftverek egy szolgáltatott szoftverként (SaaS) alkalmazások is.
Az Azure AD identity protection engedélyezéséről, kapcsolatban további [engedélyezése Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-enable).

### <a name="simplify-user-access-to-any-cloud-app"></a>Egyszerűbb hozzáférés bármely felhőalkalmazáshoz
[Egyszeri bejelentkezés engedélyezése](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps) egyszerűbbé teheti a felhasználói hozzáférést több ezer felhőalapú alkalmazások Windows, Mac, Android és iOS-eszközökön. Felhasználók személyre szabott webes hozzáférési panel vagy a vállalati hitelesítő adataival mobilalkalmazás alkalmazások is elindíthatja. Az Azure AD alkalmazásproxy-modul segítségével túlmutató SaaS-alkalmazásokhoz, és rendkívül biztonságos távoli hozzáférést és egyszeri bejelentkezést a helyszíni webalkalmazások közzétételéhez.

### <a name="protect-sensitive-data-and-applications"></a>Bizalmas jellegű adatok és alkalmazások védelme
Engedélyezése [Azure multi-factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) jogosulatlan hozzáférés elkerülése érdekében a helyszíni és felhőalapú alkalmazásokhoz, adja meg a egy további hitelesítési szintet. Védje vállalkozását és csökkentse az esetleges fenyegetések kockázatát biztonsági monitoringgal és értesítésekkel, illetve a szokásostól eltérő bejelentkezéseket felismerő, gépi tanuláson alapuló jelentésekkel.

### <a name="enable-self-service-for-your-employees"></a>Önkiszolgáló lehetőségek engedélyezése az alkalmazottaknak
A fontos feladatokat – mint például az új jelszavak létrehozása, valamint a csoportok létrehozása és kezelése – delegálhatja alkalmazottainak. [Engedélyezi az önkiszolgáló jelszóváltoztatáshoz](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password), alaphelyzetbe állítása és az önkiszolgáló és az Azure AD felügyeleti csoportban.

### <a name="integrate-with-azure-active-directory"></a>Integráció az Azure Active Directoryval
Kiterjesztése [Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate) és más a helyszíni címtárakat az Azure AD-egyszeri bejelentkezés az összes felhőalapú alkalmazások engedélyezéséhez. A bármilyen helyi címtárból származó felhasználói attribútumok automatikusan szinkronizálhatók a felhőalapú címtárral.

Integráció az Azure Active Directory és az ahhoz, hogyan kapcsolatos további tudnivalókért olvassa el a cikk [integrálása a helyszíni címtárakat az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="trace-requests-analyze-usage-trends-and-diagnose-issues"></a>Kérelmek nyomon követése, elemezheti a használati trendeket és eseményadatokat
[Az Azure Storage Analytics](https://docs.microsoft.com/azure/storage/storage-analytics) elvégzi a naplózási, és adja meg a tárfiók metrikák adatait. Ezekkel az adatokkal kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálni lehet a tárfiókokkal kapcsolatos problémákat.

Új tárfiókok alapértelmezés szerint engedélyezve vannak a tárolási Analytics metrikákat. Naplózás engedélyezése és a metrikák és az Azure portálon; naplózás konfigurálása További információkért lásd: [figyelése az Azure portálon tárfiók](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Tárolási analitika programozott módon a REST API vagy ügyfélkódtár keresztül is engedélyezheti. A szolgáltatás tulajdonságainak beállítása művelet segítségével tárolási analitika külön-külön engedélyezni az egyes szolgáltatásokhoz.

A részletes útmutatót a tárolási analitika és más eszközök segítségével azonosíthatja, diagnosztizálása és Azure tárolással kapcsolatos problémák elhárításához, lásd: [figyelése, diagnosztizálása és elhárítása a Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Integráció az Azure Active Directory és az ahhoz, hogyan kapcsolatos további tudnivalókért olvassa el a cikk [engedélyezése és konfigurálása tárolási analitika](https://docs.microsoft.com/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics?redirectedfrom=MSDN).

## <a name="monitoring-services"></a>Szolgáltatások figyelése
Sok áthelyezése alkotórészek összetettek a felhőalapú alkalmazásokhoz. Győződjön meg arról, hogy az alkalmazás marad be adatokat, és megfelelő állapotban fut figyelés nyújt. Emellett segít, hogy ki a lehetséges problémák stave és a múltbeli kiépítettektől eltérő hibakeresést. Figyelési adatok segítségével emellett az alkalmazással kapcsolatos átfogó megismerésében. Ezt az információt nyújt segítséget az alkalmazások teljesítményének vagy karbantartási követelmények, vagy, amelyek egyébként kézi beavatkozás műveletek automatizálására.

### <a name="monitor-azure-resources"></a>Azure-erőforrások figyelése
[Az Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) a platform szolgáltatás, amely az Azure-erőforrások figyelése egyetlen helyről biztosít. Az Azure Monitorral az egyes erőforrásoktól az Azure-ba érkező metrikákat és naplókat láthatóvá teheti, lekérdezheti, átirányíthatja, archiválhatja, illetve ezekkel különböző műveleteket is végezhet. Ezekkel az adatokkal a Monitor portálpaneljén, valamint a [Monitor PowerShell-parancsmagok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples), a [platformfüggetlen parancssori felület](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples) vagy az [Azure Monitor REST API-k](https://msdn.microsoft.com/library/dn931943.aspx) használatával dolgozhat.

### <a name="enable-autoscale-with-azure-monitor"></a>Engedélyezze az automatikus skálázási Azure megfigyelővel
Engedélyezése [Azure figyelő automatikus skálázás](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started) csak a virtuálisgép-méretezési csoportok (VMSS), a cloud services csomag, a app service-csomagokról és az app service Environment-környezetek vonatkozik.

### <a name="manage-roles-permissions-and-security"></a>Szerepkörök kezelése engedélyekkel és biztonság
Sok csapatok kell feltétlenül [figyelési való hozzáférés szabályozásának](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security) adatot és beállítást. Például ha vannak olyan dolgozó kizárólag figyelése (a támogatási szakértők, devops mérnökök) csoport tagjai, vagy ha egy felügyelt szolgáltató használja, érdemes lehet hozzáférést azokat csak figyelési adatok korlátozásával létrehozása, módosítása, vagy törli az erőforrást.

Ez mutatja be gyorsan figyelési beépített RBAC szerepkör alkalmazni a felhasználók az Azure-ban vagy a saját egyéni szerepkör a felhasználókat, akiknek korlátozott felügyeleti engedélyekkel. A cikk ismerteti az Azure-figyelő kapcsolódó erőforrások és hogyan korlátozhatja az adatok tartalmazzák a hozzáférést a biztonsági szempontok majd.

## <a name="prevent-detect-and-respond-to-threats"></a>Megakadályozása, észlelésében és kezelésében fenyegetések
A Security Center fenyegetésészlelés automatikusan biztonsági információk begyűjtése Azure-erőforrások, a hálózati és az összekapcsolt partnermegoldások működik. Ezt az információt, gyakran használatával történik a fenyegetések azonosítására, több forrásból származó adatokat, elemzi. A Security Center prioritás szerinti sorolja be a biztonsági riasztásokat, és javaslatokat tesz a fenyegetés kiiktatására.

-   [Biztonsági házirend konfigurálása](https://docs.microsoft.com/azure/security-center/security-center-policies) Azure-előfizetése.
-   Használja a [Security Center javaslatait](https://docs.microsoft.com/azure/security-center/security-center-recommendations) védelméhez az Azure-erőforrások.
-   Tekintse át, és kezelheti a jelenlegi [biztonsági riasztások](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).

[Az Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) az Azure-erőforrások biztonsági felügyeletének átláthatóbbá és szabályozhatóbbá tételével megkönnyíti a fenyegetések megelőzését, észlelését és elhárítását. Az ügyfél összes előfizetésére kiterjedő, integrált biztonsági monitorozást és szabályzatkezelést biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását, és számos biztonsági megoldással együttműködik.

A Security Center az Azure portál beépített funkcióiként biztosítja a fenyegetések egyszerű és hatékony megelőzését, észlelését és az azokra való reagálást. A főbb funkciók a következők:

-   Biztonsági felhőállapot ismertetése
-   Ellenőrzött felhőbiztonság
-   Könnyen telepíthető integrált felhőbeli biztonsági megoldások
-   Gyors reagálás az észlelt fenyegetésekre

### <a name="understand-cloud-security-state"></a>Biztonsági felhőállapot ismertetése
Az Azure Security Center egy helyen jeleníti meg minden Azure-erőforrás biztonsági állapotát. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági vezérlőket teljesülnek-e, és megfelelően konfigurálva, és gyorsan azonosíthatja a erőforrásokat, amelyek beavatkozást igényelnek.

### <a name="take-control-of-cloud-security"></a>Ellenőrzött felhőbiztonság
Adja meg [biztonsági házirendek](https://docs.microsoft.com/azure/security-center/security-center-policies) az Azure-előfizetések azokat a vállalat felhőalapú biztonsági igényeinek megfelelően, igazított az alkalmazások típusának vagy az egyes előfizetések adatainak érzékenysége alapján. Szabályzatokkal vezérelt ajánlásokkal vezetheti végig az erőforrás-tulajdonosokat a szükséges ellenőrzések bevezetésének lépésein, így kiküszöbölheti a találgatást a felhő biztonsági tényezői közül.

### <a name="easily-deploy-integrated-cloud-security-solutions"></a>Könnyen telepíthető integrált felhőbeli biztonsági megoldások
[Biztonsági megoldások](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) a Microsoft és a partnerek számára, beleértve az iparágvezető tűzfalak és kártevőirtó. A leegyszerűsített telepítéssel könnyen használatba veheti a biztonsági megoldásokat – a rendszer még a hálózati változásokat is konfigurálja Önnek. Emellett a partnerek megoldásaiból származó biztonsági eseményeket is összegyűjti elemzés és riasztás céljából.

### <a name="detect-threats-and-respond-fast"></a>Gyors reagálás az észlelt fenyegetésekre
Az aktuális és újonnan felbukkanó felhőbeli fenyegetések megelőzéséhez integrált, elemzéssel támogatott megközelítésre van szükség. A Microsoft globális kombinálásával [eszközintelligencia fenyegetés](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities) és a felhő biztonsági események szakértelmét, vállalati ügyei elemzéseit az Azure központi telepítések egységességét, a Security Center segítségével korai a tényleges fenyegetések észlelése és a vakriasztások számának csökkentése érdekében. Felhőalapú biztonsági riasztások biztosítanak a támadás kampányt, beleértve a kapcsolódó események és az érintett erőforrásokról betekintést, és felajánlja a problémák megoldásához, és gyors helyreállítása módszereket.

## <a name="end-to-end-scenario-based-network-monitoring"></a>Forgatókönyv-alapú hálózati végpont figyelő
Az ügyfelek egy Azure-végpontok közötti hálózati koordinálása, és különböző egyes hálózati erőforrások, például virtuális hálózaton, ExpressRoute, Alkalmazásátjáró, terheléselosztók, és több összeállítása hozhat létre. Figyelési érhető el minden olyan a hálózati erőforrásokhoz.

[Hálózati figyelő](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) van egy regionális szolgáltatás, amely lehetővé teszi, hogy figyelése és diagnosztizálása egy hálózati forgatókönyv feltételek szintjén lévő, a, és az Azure-ból. Hálózati diagnosztika és a képi megjelenítés eszközök is elérhetők a hálózati figyelőt segítenek megérteni, diagnosztizálása és információt kaphat a hálózathoz, az Azure-ban.

### <a name="automate-remote-network-monitoring-with-packet-capture"></a>Távoli hálózatok automatikus figyelése csomagrögzítéssel
A Network Watcherrel hálózati problémákat monitorozhat és diagnosztizálhat a virtuális hálózatokba való bejelentkezés nélkül. Eseményindító [csomagrögzítéssel](https://docs.microsoft.com/azure/network-watcher/network-watcher-alert-triggered-packet-capture) az-riasztások beállítása és a csomag szintjén teljesítményének valós idejű adatokhoz való hozzáférésre. Ha problémát észlel, a jobb diagnosztizálás érdekében részletes vizsgálatot végezhet.

### <a name="gain-insight-into-your-network-traffic-using-flow-logs"></a>Információszerzés a hálózati forgalomról folyamatnaplók használatával
Bemutatják a hálózati forgalom mintát használ, létre [hálózati biztonsági csoport folyamata naplók](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Folyamat naplók által biztosított információk segítik a megfelelés, a naplózás és figyelés a hálózati biztonsági profil adatokat gyűjt.

### <a name="diagnose-vpn-connectivity-issues"></a>VPN-kapcsolódási problémák diagnosztizálása
Hálózati figyelő lehetővé teszi a [a VPN-átjáró és a kapcsolatok kapcsolatos leggyakoribb problémák diagnosztizálásához](https://docs.microsoft.com/azure/network-watcher/network-watcher-diagnose-on-premises-connectivity). Így nemcsak a probléma azonosításához, hanem a részletes naplókat létrehozásával elősegítheti a további vizsgálathoz használatára.

Konfigurálja a hálózati figyelőt és az engedélyezéshez kapcsolatos további tudnivalókért olvassa el a cikk [konfigurálja a hálózati figyelőt](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="secure-deployment-using-proven-devops-tools"></a>A telepítés biztonságának bevált DevOps eszközökkel
Ezek közé tartoznak a a lista az Azure DevOps gyakorlatnak része a Microsoft Cloud tárhelyen, ami hatékonyabbá teszi a vállalatok és a csoportokat.

-   **Szolgáltatott infrastruktúra (IaC) kód:** kódú infrastruktúra olyan technológiák és eljárások, amelyek segítségével az informatikusok, távolítsa el a napi build és moduláris infrastruktúra felügyeleti terheket. Lehetővé teszi az informatikai szakemberek hozza létre és kezelheti a modern server környezetet oly módon, hogy hogyan szoftverfejlesztők létrehozása és karbantartása alkalmazáskód hasonlít. Az Azure-ba, tudunk [Azure Resource Manager]( https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) lehetővé teszi az alkalmazások olyan deklaratív sablonok segítségével kiosztását. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra, az alkalmazás életciklusának minden fázisában felhasználhatja az alkalmazás üzembe helyezéséhez.
-   **Folyamatos integrációt és telepítést:** konfigurálhatja a Visual Studio Team Services (VSTS) csapatprojektek való [automatikusan és üzembe](https://www.visualstudio.com/docs/build/overview) Azure-webalkalmazásokban vagy felhőszolgáltatások. VSTS automatikusan telepíti a bináris fájlok ezután build Azure minden kód beadása után. Az itt leírt csomag felépítési folyamat megegyezik a Visual Studio csomag parancsot, és a közzétételi lépések a Visual Studio Közzététel parancs egyenértékűek.
-   **A Kiadáskezelés:** Visual Studio [kiadási felügyeleti](https://msdn.microsoft.com/library/vs/alm/release/overview) kiválóan többlépcsős telepítés automatizálásáról és a kiadási folyamat kezelése. Hozzon létre felügyelt folyamatos üzembe helyezés adatcsatornák gyors, egyszerű és gyakran felszabadítása. A kiadási felügyeleti azt sokkal menet közben automatizálhatja a kiadási folyamat, és azt is előre meghatározott jóváhagyási munkafolyamatok. Telepítse a helyszíni kiterjesztése a felhőbe, és szükség szerint testre szabhatja.
-   **Alkalmazás teljesítményének figyelése:** problémák észlelése, a problémák megoldására, és folyamatosan fejleszteni az alkalmazások. Gyorsan diagnosztizálhat bármilyen problémát az élő alkalmazással. Megismerheti, hogyan használják a felhasználók az alkalmazást. Konfigurációs könnyen függetlenül attól, hogy JS kódot és egy webconfig bejegyzés hozzáadása, és dokumentumkönyvtárából jelennek meg a részleteket a portálon percen belül. [App insights](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) segítséget nyújt a vállalatok számára a problémák & szervizelésének gyorsabb észleléséhez.
-   **Tesztelés & automatikus skálázás betölteni:** az alkalmazás központi telepítési minőségének javítására is találtunk teljesítménybeli problémákat, és ellenőrizze, hogy az alkalmazás mindig naprakészek vagy elérhető elemeket, az üzleti kell. Győződjön meg arról, hogy az alkalmazás kezelik a forgalmat a következő indítási vagy marketing kampány. Elindultak, felhőalapú [tesztek betöltése](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) szinte nem VSTS idejű.

## <a name="next-steps"></a>További lépések
- További információ [Azure működési biztonság](https://docs.microsoft.com/azure/security/azure-operational-security).
- További [Operations Management Suite |} Biztonsági és megfelelőségi](https://www.microsoft.com/cloud-platform/security-and-compliance).
- [Ismerkedés az Operations Management Suite biztonsági és naplózási megoldás](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started).
