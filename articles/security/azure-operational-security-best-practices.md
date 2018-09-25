---
title: Az Azure Operational ajánlott biztonsági eljárások |} A Microsoft Docs
description: Ez a cikk az Azure Operational Security ajánlott eljárásokat biztosít.
services: security
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: d005dd01de0dff0136c0a4e9775001dbe018228f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035283"
---
# <a name="azure-operational-security-best-practices"></a>Azure Operational Security – ajánlott eljárások
Az Azure operational security hivatkozik a szolgáltatások, a vezérlők és a felhasználók számára elérhető szolgáltatások védelme érdekében az adatokat, alkalmazásokat és egyéb eszközök az Azure-ban. Az Azure operational security olyan keretrendszer, amely magában foglalja a Microsoft, egyedi képességek vizsgálatából épül, többek között a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/sdl), a [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) programot, és a kiberbiztonsági fenyegetések világának.

Ebben a cikkben bemutatjuk, ajánlott biztonsági eljárások gyűjteménye. Ajánlott eljárások az Azure adatbázis biztonsági származnak az funkciót, és az ügyfelek a funkciókat, például saját magának.

A minden egyes ajánlott eljárás az hogy ismertetik:
-   Mi az az ajánlott eljárás szerint
-   Miért ajánlott eljárás, hogy engedélyezni szeretné
-   Mi lehet az eredmény, ha Ön nem engedélyezi az ajánlott eljárás szerint
- Hogyan tudhat meg az ajánlott eljárás engedélyezése

Ez a cikk az Azure Operational ajánlott biztonsági eljárások az egy konszenzus véleményét, és az Azure platform képességeit és szolgáltatáskészleteket, alapján ez a cikk írásának időpontjában léteznek. Vélemények és technológiák időbeli változásait, és ez a cikk a változások követése érdekében rendszeresen frissül.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Viselkedés a váratlan módosítások tárolási szolgáltatások monitorozása
Diagnosztizálás és a egy felhőkörnyezetben található elosztott alkalmazásban kapcsolatos hibák elhárítása lehet bonyolultabb, mint a hagyományos környezetekben. PaaS vagy IaaS infrastruktúra, a helyszínen, egy mobileszközön, vagy valamilyen kombinációját ezekben a környezetekben is telepíthető alkalmazások. Az alkalmazás hálózati forgalom előfordulhat, hogy haladnak át a nyilvános és privát, és az alkalmazás használhat több tárolási technológiát.

A tárolási szolgáltatások (például a válaszidők lassabb) viselkedését a váratlan módosítások az alkalmazás használó, folyamatosan figyelje. Naplózás használata a részletesebb adatok gyűjtésére és a egy probléma mélyebben elemezheti. A megfigyelés és naplózás származó diagnosztikai információkat segít, hogy az alkalmazás észlelt probléma kiváltó okának meghatározásához. Ezután a probléma elhárításában, és határozza meg a megfelelő lépéseket annak megoldásáról.

[Az Azure Storage Analytics](../storage/storage-analytics.md) funkciói naplózást végeznek, és mérőszámadatokat biztosít az Azure storage-fiók. Azt javasoljuk, hogy használja-e az adatok kérések követhetők nyomon, elemezhetők a használati trendek és diagnosztizálhatja a problémákat a tárfiókkal.

## <a name="prevent-detect-and-respond-to-threats"></a>Megelőzését, észlelését és fenyegetések
[Az Azure Security Center](../security-center/security-center-intro.md) megelőzése, észlelése, és háríthatja el a fenyegetéseket átláthatóbbá és (és szabályozhatóbbá) segít az Azure-erőforrások biztonságát. Az Azure-előfizetések integrált biztonsági monitorozást és felügyeletet biztosít, megkönnyíti a nehezen észlelhető fenyegetések azonosítását szabályzatkezelést, és a különböző biztonsági megoldások együttműködik.

A Security Center ingyenes csomag az Azure-erőforrások csak korlátozott biztonságot nyújt. A Standard szint kiterjeszti ezeket a képességeket a helyszíni és egyéb felhőkben. A Security Center segítségével megtalálhatja és kijavíthatja a biztonsági réseket, hozzáférés- és alkalmazásvezérlőket alkalmazhat a kártékony tevékenységek blokkolásához, észlelheti a fenyegetéseket az analitika és az intelligencia használatával, valamint gyorsan reagálhat, ha a rendszer támadás alatt áll. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját. Azt javasoljuk, hogy Ön [előkészítése az Azure-előfizetés a Security Center Standard](../security-center/security-center-get-started.md).

A Security Center segítségével egy helyen jeleníti meg az összes az Azure-erőforrások biztonsági állapotát. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági ellenőrzések teljesülnek, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Végpontok közötti forgatókönyv-alapú hálózatfigyelési figyelése
Az ügyfelek hozhat létre az Azure-ban egy végpontok közötti hálózati egyesítésével a hálózati erőforrások, például egy virtuális hálózatot, ExpressRoute, Application Gateway, és a terheléselosztók. Figyelés a hálózati erőforrások mindegyike érhető el.

[Az Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) egy regionális szolgáltatás. A diagnosztikai és vizualizációs eszközök használatával figyelheti és diagnosztizálhatja a feltételeket a hálózati forgatókönyvek szintjén lévő, a és az Azure-ból.

Az alábbiakban ajánlott eljárások a hálózati figyelési és a rendelkezésre álló eszközöket.

**Ajánlott eljárás**: távoli hálózatok automatikus figyelése csomagrögzítéssel.   
**Részletes**: figyelése és a hálózati problémák diagnosztizálása a Network Watcher használatával, a virtuális gépek való bejelentkezés nélkül. Az eseményindító [csomagrögzítés](../network-watcher/network-watcher-alert-triggered-packet-capture.md) a riasztások beállítása és csomagszinten teljesítményének valós idejű adatokhoz való hozzáférésre. Ha problémát észlel, a jobb diagnosztizálás érdekében részletes vizsgálatot végezhet.

**Ajánlott eljárás**: betekintést nyerhet a hálózati forgalomról Folyamatnaplók használatával.   
**Részletes**: részletesebben Felderíthetőek a hálózati forgalmi minták használatával [hálózati biztonsági csoport folyamatnaplóit](../network-watcher/network-watcher-nsg-flow-logging-overview.md). Forgalmi naplók található információk megkönnyítik a megfelelés, a naplózás és a hálózati biztonsági profil figyelési adatok gyűjtéséhez.

**Ajánlott eljárás**: diagnosztizálhatja a VPN-kapcsolati hibák.   
**Részletes**: használja a Network Watcher való [a leggyakrabban használt VPN-átjáró és a kapcsolódási problémák diagnosztizálása](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Nem csak a probléma azonosításához, de is használhatja a részletes naplók további vizsgálat céljából.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>A telepítés biztonságának bevált DevOps-eszközök használatával
Az alábbi fejlesztési és üzemeltetési eljárások használatával győződjön meg arról, hogy a vállalati és a csapatok hatékonyabbá.

**Ajánlott eljárás**: a build és a szolgáltatások üzembe helyezésének automatizálása.   
**Részletes**: [infrastruktúra mint kód](https://en.wikipedia.org/wiki/Infrastructure_as_Code) technikák készlete, és gyakorlatokat, amelyek az informatikai szakembereknek nyújtanak segítséget távolítsa el a napi build terhe és moduláris infrastruktúra felügyeletét. Lehetővé teszi az informatikai szakemberek hozhat létre és kezelhet modern kiszolgáló környezetükben úgy, hogy hogyan hozhat létre és alkalmazáskód karbantartását hasonlít.

Használhat [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) az alkalmazásait egy deklaratív sablon használatával helyezze üzembe. Egyetlen sablonnal több szolgáltatást is üzembe helyezhet azok függőségeivel együtt. Ugyanazt a sablont újra és újra telepíteni az alkalmazást, az alkalmazás életciklusának minden szakaszában használhatja.

**Ajánlott eljárás**: automatikusan hozhat létre és üzembe helyezése az Azure web Apps és a felhőszolgáltatásokra.   
**Részletes**: beállíthatja a Visual Studio Team Services (VSTS) csapatprojektek való [automatikus létrehozása és üzembe helyezése](https://www.visualstudio.com/docs/build/overview) Azure-webalkalmazások vagy felhőszolgáltatások. VSTS ezután egy Azure-ban minden kód bejelentkezés után automatikusan üzembe helyezi a bináris fájlokat. A csomag buildelési folyamat megegyezik a csomag parancsot a Visual Studióban, és a közzétételi lépéseket egyenértékűek a Közzététel parancsot a Visual Studióban.

**Ajánlott eljárás**: kiadás automatizálása felügyeleti.   
**Részletes**: a Visual Studio [kiadási felügyeleti](https://msdn.microsoft.com/library/vs/alm/release/overview) több lépésből álló üzembe helyezésének automatizálása, és a kibocsátási folyamat kezeléséhez egy megoldás. Gyors, könnyen és gyakran felszabadítása felügyelt folyamatos üzembe helyezés folyamatokat hozhat létre. A kiadási felügyeleti a kibocsátási folyamat automatizálható, és képes jóváhagyási munkafolyamatokat előre. Helyszíni üzembe helyezése és kiterjesztése a felhőbe, és szükség szerint testre szabhatja.

**Ajánlott eljárás**: Ellenőrizze, hogy az alkalmazás teljesítményében előtt indítsa el az eszközt, vagy az éles környezetben telepítheti a frissítéseket.   
**Részletes**: futtassa a felhő alapú [terheléses teszteket](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) a VSTS használatával:

- Keresse meg a teljesítményproblémákat okozhat az alkalmazásban.
- Üzembe helyezés minőségének javítására.
- Győződjön meg arról, hogy az alkalmazás mindig elérhető.
- Győződjön meg arról, hogy az alkalmazás képes kezelni a következő indítási vagy marketing kampány forgalmát.

**Ajánlott eljárás**: alkalmazásteljesítmény Monitorozásához.   
**Részletes**: [Azure Application Insights](../application-insights/app-insights-overview.md) van egy bővíthető alkalmazásteljesítmény-felügyeleti (APM) szolgáltatás webfejlesztőknek, több platformon. Az Application Insights segítségével figyelheti az élő webalkalmazását. Automatikusan felismeri a teljesítményanomáliákat. Ez magában foglalja az analitikai eszközök segítségével diagnosztizálhatja a problémákat, és megismerheti, mit felhasználók valójában hogyan használják az alkalmazását. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="mitigate-and-protect-against-ddos"></a>Csökkentése és a DDoS elleni védelem
Az elosztott szolgáltatásmegtagadásos (DDoS-) olyan támadás, amely megkísérli az alkalmazás-erőforrások felhasználta rendelkezésére. A cél, hogy az alkalmazás rendelkezésre állásának és történő jogos kérelmek kezelése hatással van. Ezeket a támadásokat egyre több kifinomult és a méretét és a hatása nagyobb. Bármely, amely az interneten keresztül nyilvánosan elérhető végponton célozhatják.

Megtervezéséért és építéséért DDoS elleni rugalmasság szükséges tervezésekor és kialakításakor hibaállapot különböző.

Az alábbiakban ajánlott eljárások az Azure rugalmas DDoS szolgáltatások létrehozásához.

**Ajánlott eljárás**: Gondoskodjon arról, hogy biztonsági prioritást az alkalmazás, a tervezéstől és implementálástól a központi telepítés és a műveletek teljes életciklusa során. Alkalmazások is rendelkezhetnek, amelyek lehetővé teszik egy viszonylag kis mennyiségű kérést használandó rengeteg erőforrás, szolgáltatás-kimaradás eredményez hibákat.    
**Részletes**: Microsoft Azure-on futó szolgáltatás védelme érdekében kell az alkalmazásarchitektúra alapos ismerete és összpontosítson a [szoftverminőség következő öt alappillérére](https://docs.microsoft.com/azure/architecture/guide/pillars). Ha tisztában van tipikus forgalommal, a kapcsolódási modellt az alkalmazás és más alkalmazások és a Szolgáltatásvégpontok, amelyek ki vannak téve a nyilvános internethez.

Annak biztosítása, hogy egy alkalmazás elég rugalmas, maga az alkalmazás osztályra irányuló szolgáltatásmegtagadási kezelésére legfontosabb. Az Azure platformba, kezdve beépített biztonság és adatvédelem a [biztonságos fejlesztési Életciklussal (SDL)](https://www.microsoft.com/en-us/sdl). Az SDL-ből dobásig minden fejlesztési fázist, és biztosítja, hogy Azure-t folyamatosan frissítjük, hogy még biztonságosabb.

**Ajánlott eljárás**: megterveznie az alkalmazásokat, [horizontális skálázásra](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) az igény egy felerősített terhelés, kifejezetten a DDoS-támadások esetén. Ha az alkalmazás egy szolgáltatás egyetlen példánya függ, a meghibásodási pontot hoz létre. Rugalmasabb és skálázhatóbb üzembe helyezett példányban több lehetővé teszi a rendszer.   
**Részletes**: A [Azure App Service](../app-service/app-service-value-prop-what-is.md), jelölje be egy [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) , amelynek keretében több példányt.

Az Azure Cloud Services, konfigurálja az összes, használja a szerepkörök [több példány](../cloud-services/cloud-services-choose-me.md).

A [Azure Virtual Machines](../virtual-machines/windows/overview.md), győződjön meg arról, hogy a virtuális gép architektúra tartalmaz-e egynél több virtuális gép és minden virtuális gép szerepel egy [rendelkezésre állási csoport](../virtual-machines/virtual-machines-windows-manage-availability.md). Azt javasoljuk, hogy a virtuális gép méretezési csoportokat használ az automatikus méretezési képességeivel.

**Ajánlott eljárás**: az alkalmazások biztonsági védelmekkel rétegez csökkenti annak az esélyét, a sikeres támadás. Az alkalmazások biztonságos minták megvalósítása az Azure platform beépített funkcióinak használatával.   
**Részletes**: az alkalmazás méretének (terület) növeli a támadások veszélye. Csökkentheti a támadási engedélyezési használatával gombra kattintva zárja be a közzétett IP-címtér le, és nem szükséges a terheléselosztókat a portokat figyeli ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) és [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)).

[Hálózati biztonsági csoportok](../virtual-network/security-overview.md) egy másik módja a támadási felület csökkentése érdekében. Használhat [szolgáltatáscímkéket](../virtual-network/security-overview.md#service-tags) és [az alkalmazásbiztonsági csoportok](../virtual-network/security-overview.md#application-security-groups) minimalizálása érdekében a biztonsági szabályok létrehozása és konfigurálása a hálózati biztonság, az alkalmazás struktúrájának természetes bővítményeként összetettségét.

Azure-szolgáltatások üzembe kell helyeznie egy [virtuális hálózat](../virtual-network/virtual-networks-overview.md) , amikor csak lehetséges. Ez az eljárás lehetővé teszi, hogy a szolgáltatási erőforrások a magánhálózati IP-címeken keresztül kommunikáljanak. Egy virtuális hálózatot az Azure-szolgáltatások forgalmára alapértelmezés szerint nyilvános IP-címeket használ forrás IP-ként.

Használatával [szolgáltatásvégpontokat](../virtual-network/virtual-network-service-endpoints-overview.md) kapcsolók szolgáltatás forgalmát használják a virtuális hálózat privát címeire, a forrás IP-címek, amikor a virtuális hálózatról az Azure-szolgáltatás fér hozzá.

Gyakran láthatjuk az ügyfelek a helyi és azok az Azure-erőforrások lekérdezése megtámadott erőforrások. Ha csatlakoztatja a helyszíni környezet az Azure-ba, minimalizálja a helyszíni erőforrásokhoz kitéve a nyilvános interneten.

Az Azure rendelkezik két DDoS [szolgáltatásajánlatok](../virtual-network/ddos-protection-overview.md) , amely a hálózati támadásoktól védelmet biztosít:

- Alapvető védelmet további költségek nélkül alapértelmezés szerint integrálva van az Azure-bA. A méretezési csoport és a kapacitás a globálisan üzembe helyezett Azure-hálózat biztosít – a forgalom figyelési és valós idejű megoldás révén közös hálózati rétegből támadások elleni védelmet. Alapszintű nem kell felhasználói konfiguráció vagy az alkalmazás módosítani, és segít megvédeni az összes Azure-szolgáltatások, beleértve a PaaS-szolgáltatások, például az Azure DNS-ben.
- Standard szintű védelmet biztosít a fejlett funkciói hálózati támadásokkal szemben. Automatikusan hangolt az adott Azure-erőforrások védelme. A védelem az egyszerű engedélyezése a virtuális hálózatok létrehozása során. Ez a létrehozása után is elvégezhető, és nem kell application vagy az erőforrás módosítani.

## <a name="next-steps"></a>További lépések
Lásd: [Azure ajánlott biztonsági eljárások és minták](security-best-practices-and-patterns.md) további ajánlott biztonsági eljárások szeretne használni, amikor a tervezése, telepítése, és a felhőalapú megoldások kezelése az Azure használatával.

Az alábbi forrásanyagokat biztosít az Azure biztonsági és a kapcsolódó Microsoft-szolgáltatások kapcsolatos általános információk érhetők el:
* [Az Azure Security csapat blogja](https://blogs.msdn.microsoft.com/azuresecurity/) – az Azure Security legújabb naprakész információk
* [A Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – Ha a jelenteni lehet a Microsoft biztonsági réseket, beleértve a problémák az Azure-ral, vagy az e-mailen keresztül secure@microsoft.com
