---
title: Ajánlott biztonsági eljárások az védelme az eszközök – Microsoft Azure
description: Ez a cikk az adatok, alkalmazások és más Azure-beli eszközök védelmére gyakorlati tanácsok az üzemeltetéshez ismertet.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4a4677b5db730001df75d201d8e6d3149cb928e6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409846"
---
# <a name="azure-operational-security-best-practices"></a>Azure Operational Security – ajánlott eljárások
Ez a cikk az adatok, alkalmazások és más Azure-beli eszközök védelmére gyakorlati tanácsok az üzemeltetéshez ismertet.

Az ajánlott eljárások a vélemény konszenzus alapulnak, és dolgozhat az aktuális Azure platform olyan képességeit, és a szolgáltatáskészletek. Vélemények és technológiák az idő előrehaladtával változik, és ez a cikk a változások követése érdekében rendszeresen frissül.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Meghatározhatja, és erős operatív biztonsági eljárások
Az Azure operational security hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök az Azure-ban. Az Azure operational security olyan keretrendszer, amely magában foglalja a Microsoft, egyedi képességek vizsgálatából épül, többek között a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl), a [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programot, és a kiberbiztonsági fenyegetések világának.

## <a name="manage-and-monitor-user-passwords"></a>Felügyelheti és figyelheti a felhasználói jelszavak
A következő táblázat felsorolja a felhasználói jelszavak felügyeletével kapcsolatos ajánlott eljárásokat:

**Ajánlott eljárás**: Győződjön meg arról, hogy a megfelelő szintű jelszavas védelmet a felhőben.   
**Részletes**: Kövesse az útmutató [Microsoft tájékoztató a jelszavakról](https://www.microsoft.com/research/publication/password-guidance/), amely Microsoft identitás-platformot (Azure Active Directory, az Active Directory és a Microsoft-fiók) felhasználók hatókörét.

**Ajánlott eljárás**: Ez a figyelő a felhasználói fiókokhoz kapcsolódó gyanús tevékenységek.   
**Részletes**: Figyeli, hogy [veszélyeztetett felhasználók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) és [kockázatos bejelentkezések](../active-directory/reports-monitoring/concept-risk-events.md) az Azure AD biztonsági jelentések.

**Ajánlott eljárás**: Automatikus észlelése és javítása a magas kockázatú jelszavakat.   
**Részletes**: [Az Azure AD Identity Protection](../active-directory/identity-protection/overview.md) funkció az Azure AD Premium P2 kiadás, amely lehetővé teszi, hogy:

- A szervezet identitásait érintő esetleges biztonsági rések észlelését
- Automatikus válaszok észlelt gyanús tevékenységeket, a szervezet identitásait kapcsolódó konfigurálása
- Gyanús incidensek kivizsgálásában, és azok megoldását megfelelő műveletek végrehajtása

## <a name="receive-incident-notifications-from-microsoft"></a>Incidens értesítéseket kapni a Microsofttól
Győződjön meg arról, hogy a biztonsági műveletek csapat Azure incidens értesítéseket fogad a Microsoft. Egy incidens értesítési lehetővé teszi, a biztonsági csapat ismeri az Azure-erőforrások rendelkezik biztonsága sérült, így gyorsan válaszolni és javíthatja az esetleges biztonsági kockázatokat.

Az Azure-regisztrációjához portálon biztosítható a rendszergazda kapcsolattartási adatait tartalmazza, amelyek értesítik a biztonsági műveletek részleteit. Kapcsolattartási adatok, e-mail címét és telefonszámát szám.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Az Azure-előfizetések felügyeleti csoportokba való rendszerezése
Ha a szervezet több előfizetéssel rendelkezik, szüksége lehet hatékonyan kezelheti a hozzáférést, a házirendek és a megfelelőségi ezen előfizetések esetében. [Az Azure felügyeleti csoportok](../governance/management-groups/create.md) biztosítanak, amely meghaladja az előfizetések. Felügyeleti csoportok nevű tárolókba előfizetések rendszerezése és a szabályozási feltételek vonatkoznak a felügyeleti csoportok. A felügyeleti csoporton belüli összes előfizetés automatikusan örökli a felügyeleti csoportra alkalmazott feltételeket.

Felügyeleti csoportok és a egy könyvtárba előfizetések rugalmas szerkezete hozhat létre. Minden könyvtár egy legfelső szintű felügyeleti csoport gyökérszintű felügyeleti csoport neve van megadva. Ez a gyökérszintű felügyeleti csoport úgy épül be a hierarchiába, hogy minden felügyeleti csoport és előfizetés fölött legyen. A gyökérszintű felügyeleti csoport lehetővé teszi a globális szabályzatok és az RBAC-hozzárendeléseket a könyvtár szintjén alkalmazható.

Az alábbiakban néhány gyakorlati tanácsot a felügyeleti csoportok:

**Ajánlott eljárás**: Győződjön meg arról, hogy az új előfizetések a cégirányítási elemeket, például a szabályzatok és engedélyek vonatkoznak hozzáadásuk.   
**Részletes**: A gyökérszintű felügyeleti csoport használatával rendelje hozzá a vállalati szintű biztonsági elemek, amelyek érvényesek az összes Azure-objektumok. Szabályzatok és engedélyek elemek példák.

**Ajánlott eljárás**: A legfelső szintű felügyeleti csoportok Szegmentálás stratégiát egy pontot biztosít minden szegmensben vezérlő és a házirend konzisztencia igazítása   
**Részletes**: Hozzon létre egy felügyeleti csoport minden egyes szegmens a legfelső szintű felügyeleti csoportban. Más felügyeleti csoportok a legfelső szintű csoportban ne hozzon létre.

**Ajánlott eljárás**: Korlátozza a felügyeleti csoport mélysége, műveleteket és a biztonsági gátolja félreértések elkerülése érdekében.   
**Részletes**: A hierarchiában három szintre, beleértve a legfelső szintű korlátozza.

**Ajánlott eljárás**: A legfelső szintű felügyeleti csoporttal, a teljes nagyvállalati alkalmazása elemek gondosan kiválasztásához.   
**Részletes**: Győződjön meg arról felügyeleti csoport gyökérelemmel rendelkezik egy tiszta, minden erőforrás között alkalmazni kell, hogy azok kis hatás.

Tudta kellőképpen deduplikálni a következők:

- Szabályozási követelményeknek, amelyek egy egyértelmű hatás az üzletmenetre (például az adatok elkülönítése kapcsolatos korlátozások)
- Követelményekhez csaknem teljes esetleges negatív hatással a műveletek, például a naplózási vagy RBAC házirend gondosan ellenőrzöttek engedély-hozzárendeléseket

**Ajánlott eljárás**: Gondosan tervezze, és a gyökérszintű felügyeleti csoport összes vállalati szintű módosítások teszteléséhez mielőtt alkalmazná őket (házirend, RBAC-modellben, és így tovább).   
**Részletes**: A gyökérszintű felügyeleti csoport is hatással vannak az Azure-ban minden erőforrás. Hatékonyan biztosítani a konzisztenciát a vállalaton belül biztosítanak, miközben hibák vagy a helytelen használat negatív hatással lehet éles műveleteket. Tesztelje a gyökérszintű felügyeleti csoport összes módosítását egy tesztelési labor- vagy éles kísérleti.

## <a name="streamline-environment-creation-with-blueprints"></a>Egyszerűsíthetők a tervek-környezet létrehozása
[Az Azure-tervek](../governance/blueprints/overview.md) szolgáltatás lehetővé teszi a felhőbeli tervezők és a központi technológia csoportok segítségével meghatározhatja egy megismételhető az Azure-erőforrás, amely megvalósítja és betartja a szervezet szabványok, minták és követelményeket. Azure tervezetek lehetővé teszi a fejlesztői csapatok gyorsan hozhat létre, és a beépített összetevők és magabiztosan, hogy belüli szervezeti megfelelőségi szkriptjét hoznak létre egy új környezetek üzembe.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Viselkedés a váratlan módosítások tárolási szolgáltatások monitorozása
Diagnosztizálás és a egy felhőkörnyezetben található elosztott alkalmazásban kapcsolatos hibák elhárítása lehet bonyolultabb, mint a hagyományos környezetekben. PaaS vagy IaaS infrastruktúra, a helyszínen, egy mobileszközön, vagy valamilyen kombinációját ezekben a környezetekben is telepíthető alkalmazások. Az alkalmazás hálózati forgalom előfordulhat, hogy haladnak át a nyilvános és privát, és az alkalmazás használhat több tárolási technológiát.

A tárolási szolgáltatások (például a válaszidők lassabb) viselkedését a váratlan módosítások az alkalmazás használó, folyamatosan figyelje. Naplózás használata a részletesebb adatok gyűjtésére és a egy probléma mélyebben elemezheti. A megfigyelés és naplózás származó diagnosztikai információkat segít, hogy az alkalmazás észlelt probléma kiváltó okának meghatározásához. Ezután a probléma elhárításában, és határozza meg a megfelelő lépéseket annak megoldásáról.

[Az Azure Storage Analytics](../storage/storage-analytics.md) funkciói naplózást végeznek, és mérőszámadatokat biztosít az Azure storage-fiók. Azt javasoljuk, hogy használja-e az adatok kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálhatja a problémákat a tárfiókkal.

## <a name="prevent-detect-and-respond-to-threats"></a>Megelőzését, észlelését és fenyegetések
[Az Azure Security Center](../security-center/security-center-intro.md) megelőzése, észlelése, és háríthatja el a fenyegetéseket azáltal, hogy átláthatóbbá (és szabályozhatóbbá) segít az Azure-erőforrások biztonságát. Az Azure-előfizetések integrált biztonsági monitorozást és felügyeletet biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és a különböző biztonsági megoldások együttműködik.

A Security Center ingyenes szintjéhez csak az Azure-erőforrások számára korlátozott biztonságot nyújt. A Standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkben. A Security Center segít a keresése, és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket kártékony tevékenységek blokkolásához, észlelheti a fenyegetéseket analytics és az intelligencia használatával a alkalmazni és gyorsan reagálhat a támadás alatt. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját. Azt javasoljuk, hogy Ön [frissítése az Azure-előfizetés a Security Center Standard](../security-center/security-center-get-started.md).

A Security Center segítségével egy helyen jeleníti meg az Azure-erőforrások biztonsági állapotát. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági ellenőrzések teljesülnek, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.

A Security Center emellett integrálható [Windows Defender komplex veszélyforrások elleni védelem (ATP)](../security-center/security-center-wdatp.md), átfogó végpont észlelés és válasz (EDR) képességeket biztosít, amely. A Windows Defender ATP-integráció felismerése rendellenességeket. Is észlelni és válaszadás a Security Center által figyelt kiszolgálói végpontot a speciális támadások.

Szinte az összes vállalati szervezetek rendelkezik egy biztonsági biztonságiadat- és eseménykezelés (SIEM) felügyeleti rendszer azonosíthatja a különféle jel gyűjtése eszközökről naplóadatok konszolidálásával újonnan felbukkanó fenyegetésekkel szemben. A naplók segítségével azonosíthatja a "érdekes", a háttérzaj, amely az összes naplófájl összegyűjtése és elemzési megoldások elkerülhetetlen az adatok elemzési rendszerek által majd elemzi.

[Az Azure Sentinel](../sentinel/overview.md) egy skálázható, felhő-natív, biztonsági információk és az esemény kezelése (SIEM) és a biztonsági automatizált vezénylési válasz (MEGUGRIK) megoldás. Az Azure Sentinel intelligens biztonsági elemzés és veszélyészlelés céljából információs riasztás észlelési, veszélyforrások elleni látható-e, proaktív vadászat és automatizált reagáláshoz keresztül biztosít.

Az alábbiakban néhány gyakorlati tanácsot megelőzését, észlelését és a fenyegetésekkel szembeni:

**Ajánlott eljárás**: Növelheti a sebességet és méretezhetőséget biztosít, a SIEM-megoldás egy felhőalapú SIEM használatával.   
**Részletes**: A funkciókat és képességeket, [Azure Sentinel-](../sentinel/overview.md) és összevetheti azokat, amit Ön jelenleg lehetőségeinek használatával a helyszínen. Vegye figyelembe, hogy bevezetése az Azure-Sentinel, ha megfelel a szervezet SIEM követelményeinek.

**Ajánlott eljárás**: Keresse meg a legsúlyosabb biztonsági réseket, így rangsorolhatja a vizsgálatot.   
**Részletes**: Tekintse át a [Azure biztonságos pontszám](../security-center/security-center-secure-score.md) a javaslatok a szabályzatok az Azure és az Azure Security Center épített kezdeményezések megtekintéséhez. Ezek a javaslatok segítenek cím felső kockázatok, például a biztonsági frissítéseket, az endpoint protection, titkosítás, biztonsági konfigurációkat, hiányzó WAF, internetkapcsolattal rendelkező virtuális gépek és sok más.

A biztonságos pontszám, alapuló Center internetes biztonsági (CIS) vezérlők, lehetővé teszi a szervezet az Azure security külső források becslésére. Külső érvényesítése segít érvényesíteni, és a csapat biztonsági stratégia bővítheti.

**Ajánlott eljárás**: Figyelheti a gépek, hálózatok, tárolási és adatszolgáltatások, és Fedezze fel, és rangsorolhatja a potenciális biztonsági problémákat alkalmazások biztonsági állapotát.  
**Részletes**: Kövesse a [biztonsági javaslatok](../security-center/security-center-recommendations.md) a Security Center a legmagasabb prioritású elemek kezdve.

**Ajánlott eljárás**: A Security Center riasztásainak integrálása (a biztonsági biztonságiadat- és eseménykezelés SIEM) megoldást.   
**Részletes**: A legtöbb szervezet a siem-mel használhatjuk a központi fórum, biztonsági elemző választ igénylő riasztásokat. A Security Center által feldolgozott események az Azure tevékenységnapló-, egyet az Azure monitoron keresztül elérhető naplók vannak közzétéve. Az Azure Monitor egy konszolidált folyamatot a figyelési adatokat egy SIEM-eszközével történő útválasztáshoz kínál. Lásd: [a Security Center biztonsági megoldások integrálása](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) útmutatást. Ha az Azure-Sentinel használ, tekintse meg [csatlakozás az Azure Security Center](../sentinel/connect-azure-security-center.md).

**Ajánlott eljárás**: Azure integrálása a SIEM-naplókat.   
**Részletes**: Használat [gyűjthet, és az adatok exportálása az Azure Monitor](../azure-monitor/overview.md#integrate-and-export-data). Ez az eljárás kritikus fontosságú biztonsági incidens vizsgálat engedélyezésére, és online naplófájlok megőrzése korlátozva. Ha az Azure-Sentinel használ, tekintse meg [adatforrások csatlakoztatása](../sentinel/connect-data-sources.md).

**Ajánlott eljárás**: A vizsgálati és vadászat folyamatok felgyorsítása és csökkenti a vakriasztások a támadás vizsgálat az Endpoint észlelés és válasz (EDR) képességeket integrálásával.   
**Részletes**: [A Windows Defender ATP-integráció engedélyezése](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) a Security Center biztonsági házirendjében. Fontolja meg az Azure-Sentinel threat vadászat és incidensmegoldás.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Végpontok közötti forgatókönyv-alapú hálózatfigyelési figyelése
Az ügyfelek hozhat létre az Azure-ban egy végpontok közötti hálózati egyesítésével a hálózati erőforrások, például egy virtuális hálózatot, ExpressRoute, Application Gateway, és a terheléselosztók. Figyelés a hálózati erőforrások mindegyike érhető el.

[Az Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás. A diagnosztikai és vizualizációs eszközök használatával figyelheti és diagnosztizálhatja a feltételeket a hálózati forgatókönyvek szintjén lévő, a és az Azure-ból.

Az alábbiakban ajánlott eljárások a hálózati figyelési és a rendelkezésre álló eszközöket.

**Ajánlott eljárás**: Távoli hálózatok automatikus figyelése csomagrögzítéssel.  
**Részletes**: Figyelheti, és a hálózati problémák diagnosztizálása a Network Watcher használatával, a virtuális gépek való bejelentkezés nélkül. Az eseményindító [csomagrögzítés](../network-watcher/network-watcher-alert-triggered-packet-capture.md) a riasztások beállítása és csomagszinten teljesítményének valós idejű adatokhoz való hozzáférésre. Ha problémát észlel, a jobb diagnosztizálás érdekében részletes vizsgálatot végezhet.

**Ajánlott eljárás**: Információszerzés a hálózati forgalomról Folyamatnaplók használatával.  
**Részletes**: Részletesebben felderíthetőek a hálózati forgalmi minták használatával [hálózati biztonsági csoport folyamatnaplóit](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Forgalmi naplók található információk megkönnyítik a megfelelés, a naplózás és a hálózati biztonsági profil figyelési adatok gyűjtéséhez.

**Ajánlott eljárás**: VPN-kapcsolati hibák diagnosztizálása.  
**Részletes**: A Network Watcher használatát [a leggyakrabban használt VPN-átjáró és a kapcsolódási problémák diagnosztizálása](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Nem csak a probléma azonosításához, de is használhatja a részletes naplók további vizsgálat céljából.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>A telepítés biztonságának bevált DevOps-eszközök használatával
Az alábbi fejlesztési és üzemeltetési eljárások használatával győződjön meg arról, hogy a vállalati és a csapatok hatékonyabbá.

**Ajánlott eljárás**: A build és a szolgáltatások üzembe helyezésének automatizálása.  
**Részletes**: [Infrastruktúra mint kód](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) technikák készlete, és gyakorlatokat, amelyek az informatikai szakembereknek nyújtanak segítséget távolítsa el a napi build terhe és moduláris infrastruktúra felügyeletét. Lehetővé teszi az informatikai szakemberek hozhat létre és kezelhet modern kiszolgáló környezetükben úgy, hogy hogyan hozhat létre és alkalmazáskód karbantartását hasonlít.

Használhat [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) az alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra telepíteni az alkalmazást, az alkalmazás életciklusának minden szakaszában használhatja.

**Ajánlott eljárás**: Automatikusan létrehozhat és üzembe helyezése az Azure web Apps, vagy felhőszolgáltatások.  
**Részletes**: Konfigurálhatja az Azure DevOps Projects, [automatikus létrehozása és üzembe helyezése](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) Azure-webalkalmazások vagy felhőszolgáltatások. Az Azure DevOps ezután egy Azure-ban minden kód bejelentkezés után automatikusan üzembe helyezi a bináris fájlokat. A csomag buildelési folyamat megegyezik a csomag parancsot a Visual Studióban, és a közzétételi lépéseket egyenértékűek a Közzététel parancsot a Visual Studióban.

**Ajánlott eljárás**: A kiadáskezelés automatizálhatja.  
**Részletes**: [Az Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) több lépésből álló üzembe helyezésének automatizálása, és a kibocsátási folyamat kezeléséhez egy megoldás. Gyors, könnyen és gyakran felszabadítása felügyelt folyamatos üzembe helyezés folyamatokat hozhat létre. Az Azure-folyamatok a kibocsátási folyamat automatizálható, és képes jóváhagyási munkafolyamatokat előre. Helyszíni üzembe helyezése és kiterjesztése a felhőbe, és szükség szerint testre szabhatja.

**Ajánlott eljárás**: Ellenőrizze, hogy az alkalmazás teljesítményében előtt indítsa el az eszközt, vagy az éles környezetben telepítheti a frissítéseket.  
**Részletes**: Futtassa a felhő alapú [terheléses teszteket](https://docs.microsoft.com/azure/devops/test/load-test/overview.md?view=azure-devops#alternatives) való:

- Keresse meg a teljesítményproblémákat okozhat az alkalmazásban.
- Üzembe helyezés minőségének javítására.
- Győződjön meg arról, hogy az alkalmazás mindig elérhető.
- Győződjön meg arról, hogy az alkalmazás képes kezelni a következő indítási vagy marketing kampány forgalmát.

[Az Apache JMeter](https://jmeter.apache.org/) egy ingyenes, népszerű nyílt forráskódú eszköz, egy erős biztonsági Közösséggel.

**Ajánlott eljárás**: Alkalmazások teljesítményének figyelése.  
**Részletes**: [Az Azure Application Insights](../azure-monitor/app/app-insights-overview.md) van egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az Application Insights segítségével figyelheti az élő webalkalmazását. Automatikusan felismeri a teljesítményanomáliákat. Ez magában foglalja az analitikai eszközök segítségével diagnosztizálhatja a problémákat, és megismerheti, mit felhasználók valójában hogyan használják az alkalmazását. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="mitigate-and-protect-against-ddos"></a>Csökkentése és a DDoS elleni védelem
Az elosztott szolgáltatásmegtagadásos (DDoS-) olyan támadás, amely megkísérli az alkalmazás-erőforrások felhasználta rendelkezésére. A cél, hogy az alkalmazás rendelkezésre állásának és történő jogos kérelmek kezelése hatással van. Ezeket a támadásokat egyre több kifinomult és a méretét és a hatása nagyobb. Bármely, amely az interneten keresztül nyilvánosan elérhető végponton célozhatják.

Megtervezéséért és építéséért DDoS elleni rugalmasság szükséges tervezésekor és kialakításakor hibaállapot különböző. Az alábbiakban ajánlott eljárások az Azure rugalmas DDoS szolgáltatások létrehozásához.

**Ajánlott eljárás**: Győződjön meg arról, hogy biztonsági prioritást az alkalmazás, a tervezéstől és implementálástól a központi telepítés és a műveletek teljes életciklusa során. Alkalmazások is rendelkezhetnek, amelyek lehetővé teszik egy viszonylag kis mennyiségű kérést használandó rengeteg erőforrás, szolgáltatás-kimaradás eredményez hibákat.  
**Részletes**: A Microsoft Azure-on futó szolgáltatás védelme érdekében kell az alkalmazásarchitektúra alapos ismerete és összpontosítson a [szoftverminőség következő öt alappillérére](https://docs.microsoft.com/azure/architecture/guide/pillars). Ha tisztában van tipikus forgalommal, a kapcsolódási modellt az alkalmazás és más alkalmazások és a Szolgáltatásvégpontok, amelyek ki vannak téve a nyilvános internethez.

Annak biztosítása, hogy egy alkalmazás elég rugalmas, maga az alkalmazás osztályra irányuló szolgáltatásmegtagadási kezelésére legfontosabb. Az Azure platformba, kezdve beépített biztonság és adatvédelem a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl). Az SDL-ből dobásig minden fejlesztési fázist, és biztosítja, hogy Azure-t folyamatosan frissítjük, hogy még biztonságosabb.

**Ajánlott eljárás**: Az alkalmazások tervezési [horizontális skálázásra](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) az igény egy felerősített terhelés, kifejezetten a DDoS-támadások esetén. Ha az alkalmazás egy szolgáltatás egyetlen példánya függ, a meghibásodási pontot hoz létre. Rugalmasabb és skálázhatóbb üzembe helyezett példányban több lehetővé teszi a rendszer.  
**Részletes**: A [Azure App Service](../app-service/app-service-value-prop-what-is.md), jelölje be egy [App Service-csomag](../app-service/overview-hosting-plans.md) , amelynek keretében több példányt.

Az Azure Cloud Services, konfigurálja az összes, használja a szerepkörök [több példány](../cloud-services/cloud-services-choose-me.md).

A [Azure Virtual Machines](../virtual-machines/windows/overview.md), győződjön meg arról, hogy a virtuális gép architektúra tartalmaz-e egynél több virtuális gép és minden virtuális gép szerepel egy [rendelkezésre állási csoport](../virtual-machines/virtual-machines-windows-manage-availability.md). Azt javasoljuk, hogy a virtuális gép méretezési csoportokat használ az automatikus méretezési képességeivel.

**Ajánlott eljárás**: Az alkalmazások biztonsági védelmekkel rétegez csökkenti annak az esélyét, a sikeres támadás. Az alkalmazások biztonságos minták megvalósítása az Azure platform beépített funkcióinak használatával.  
**Részletes**: A támadás kockázatát az alkalmazás mérete (terület) egyenes arányban növekszik. Csökkentheti a támadási engedélyezési használatával gombra kattintva zárja be a közzétett IP-címtér le, és nem szükséges a terheléselosztókat a portokat figyeli ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) és [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

[Hálózati biztonsági csoportok](../virtual-network/security-overview.md) egy másik módja a támadási felület csökkentése érdekében. Használhat [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags) és [az alkalmazásbiztonsági csoportok](../virtual-network/security-overview.md#application-security-groups) minimalizálása érdekében a biztonsági szabályok létrehozása és konfigurálása a hálózati biztonság, az alkalmazás struktúrájának természetes bővítményeként összetettségét.

Azure-szolgáltatások üzembe kell helyeznie egy [virtuális hálózat](../virtual-network/virtual-networks-overview.md) , amikor csak lehetséges. Ez az eljárás lehetővé teszi, hogy a szolgáltatási erőforrások a magánhálózati IP-címeken keresztül kommunikáljanak. Egy virtuális hálózatot az Azure-szolgáltatások forgalmára alapértelmezés szerint nyilvános IP-címeket használ forrás IP-ként.

Használatával [szolgáltatásvégpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) kapcsolók szolgáltatás forgalmát használják a virtuális hálózat privát címeire, a forrás IP-címek, amikor a virtuális hálózatról az Azure-szolgáltatás fér hozzá.

Gyakran láthatjuk az ügyfelek a helyi és azok az Azure-erőforrások lekérdezése megtámadott erőforrások. Ha csatlakoztatja a helyszíni környezet az Azure-ba, minimalizálja a helyszíni erőforrásokhoz kitéve a nyilvános interneten.

Az Azure rendelkezik két DDoS [szolgáltatásajánlatok](../virtual-network/ddos-protection-overview.md) , amely a hálózati támadásoktól védelmet biztosít:

- Alapvető védelmet további költségek nélkül alapértelmezés szerint integrálva van az Azure-bA. A méretezési csoport és a kapacitás a globálisan üzembe helyezett Azure-hálózat biztosít – a forgalom figyelési és valós idejű megoldás révén közös hálózati rétegből támadások elleni védelmet. Alapszintű nem kell felhasználói konfiguráció vagy az alkalmazás módosítani, és segít megvédeni az összes Azure-szolgáltatások, beleértve a PaaS-szolgáltatások, például az Azure DNS-ben.
- Standard szintű védelmet biztosít a fejlett funkciói hálózati támadásokkal szemben. Automatikusan hangolt az adott Azure-erőforrások védelme. A védelem az egyszerű engedélyezése a virtuális hálózatok létrehozása során. Ez a létrehozása után is elvégezhető, és nem kell application vagy az erőforrás módosítani.

## <a name="enable-azure-policy"></a>Az Azure Policy engedélyezése
[Az Azure Policy](../governance/policy/overview.md) egy szolgáltatás, amellyel létrehozása, hozzárendelése és szabályzatok kezelése Azure-ban. Ezek a szabályzatok szabályokat és kényszerítenek hatások az erőforrásokat, így azok megfeleljenek a vállalati szabványoknak és szolgáltatói szerződéseket. Az Azure Policy megfelel ennek az igénynek az erőforrások megfelel-e a hozzárendelt szabályzatok kiértékelik.

Azure Policy ellenőrizni és érvényesíteni a szervezet írásos házirend engedélyezése. A hibrid felhőbeli számítási feladatokban biztonsági szabályzatainak központi kezelésével által vagy hatósági követelményeknek való megfelelést a céges tud biztosítani. Ismerje meg, hogyan [kényszeríteni a megfelelőségi szabályzatok létrehozása és kezelése](../governance/policy/tutorials/create-and-manage.md). Lásd: [Azure szabályzatdefiníciók struktúrája](../governance/policy/concepts/definition-structure.md) házirend elemei áttekintését.

Az alábbiakban néhány biztonsági vonatkozó ajánlott eljárásokat, az Azure Policy elfogadása után:

**Ajánlott eljárás**: A házirend számos különböző típusú hatások támogatja. Itt olvashat őket [Azure szabályzatdefiníciók struktúrája](../governance/policy/concepts/definition-structure.md#policy-rule). Üzleti műveletek negatív hatással lehet a **megtagadása** hatás és a **szervizelése** életbe lépjenek, így kezdje a **naplózási** és a kockázatok a negatív hatás effektus a házirend.   
**Részletes**: [Indítsa el a házirend-központitelepítések rendszervizsgálati módban](../governance/policy/concepts/definition-structure.md#policy-rule) , majd később halad a **megtagadása** vagy **szervizelése**. Tesztelje, és tekintse át az eredményeket a naplózási hatás, mielőtt továbblép **megtagadása** vagy **szervizelése**.

További információkért lásd: [létrehozása és a megfelelőség kikényszerítése céljából házirendek kezelése](../governance/policy/tutorials/create-and-manage.md).

**Ajánlott eljárás**: Azonosítsa a szabálymegsértéseknek monitorozásának és biztosítva a megfelelő javítási műveletet gyorsan felelős szerepkörök.   
**Részletes**: A hozzárendelt szerepkör figyelő megfelelőségvizsgálat rendelkezik a [az Azure portal](../governance/policy/how-to/get-compliance-data.md#portal) vagy a [parancssori](../governance/policy/how-to/get-compliance-data.md#command-line).

**Ajánlott eljárás**: Az Azure Policy a szervezet írásos házirendek műszaki reprezentációját. Képezze le az összes Azure-szabályzatok a szervezeti irányelvek keveredési csökkentheti és növelheti a konzisztencia.   
**Részletes**: A szervezet dokumentációban vagy az Azure policy magát egy hivatkozást a szervezeti házirend hozzáadásával az Azure-ban lévő dokumentum leképezés [szabályzat leírása](../governance/policy/concepts/definition-structure.md#display-name-and-description) vagy az Azure policy [kezdeményezés](../governance/policy/concepts/definition-structure.md#initiatives) leírása.

## <a name="monitor-azure-ad-risk-reports"></a>A figyelő az Azure AD szóló jelentések
Biztonsági rések túlnyomó többsége kerül sor, amikor a támadók próbál a jeggyel hozzáférést egy környezethez lopásának megjelölése a felhasználó identitását. Feltört identitásokat felderítése nem könnyű feladat. Azure ad-ben az adaptív gépi tanulási algoritmusok és heurisztika segítségével észleli a felhasználói fiókokhoz kapcsolódó gyanús tevékenységeket. Minden észlelt gyanús művelet nevű rekordot tárolja egy [kockázati esemény](../active-directory/reports-monitoring/concept-risk-events.md). Kockázati események tárolja, amely az Azure AD biztonsági jelentések. További információkért olvassa el a [felhasználókról szóló biztonsági jelentés](../active-directory/reports-monitoring/concept-user-at-risk.md) és a [kockázatos bejelentkezés biztonsági jelentés](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>További lépések
Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.

Az alábbi forrásanyagokat biztosít az Azure biztonsági és a kapcsolódó Microsoft-szolgáltatások kapcsolatos általános információk érhetők el:
* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – az Azure Security legújabb naprakész információk
* [A Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – Ha a jelenteni lehet a Microsoft biztonsági réseket, beleértve a problémák az Azure-ral, vagy az e-mailen keresztül secure@microsoft.com
