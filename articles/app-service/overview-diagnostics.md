---
title: Diagnosztika és megoldás i
description: Ismerje meg, hogyan háríthatja el az alkalmazással kapcsolatos problémákat az Azure App Service-ben a diagnosztikával, és hogyan oldhatja meg az eszközt az Azure Portalon.
keywords: app service, azure app service, diagnosztika, támogatás, web app, hibaelhárítás, önsegítő
author: jen7714
ms.topic: article
ms.date: 10/18/2019
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: df8f68e47776f46ae2d1331f85bd76d6cfe17b80
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869946"
---
# <a name="azure-app-service-diagnostics-overview"></a>Az Azure App Service diagnosztikája – áttekintés

Amikor egy webalkalmazást futtat, fel szeretne készülni az esetlegesen felmerülő problémákra, az 500 hibából a felhasználóknak, amelyek arról szólnak, hogy a webhely nem működik. Az App Service diagnosztika egy intelligens és interaktív élmény, amely segít az alkalmazás konfigurálás nélküli hibaelhárításában. Amikor problémákba ütközik az alkalmazással kapcsolatban, az App Service diagnosztikája rámutat arra, hogy mi a hiba, amely a megfelelő információkhoz vezeti a problémát, hogy könnyebben és gyorsabban elháríthassa és megoldhassa a problémát.

Bár ez az élmény akkor a leghasznosabb, ha az elmúlt 24 órában problémákat tapasztal az alkalmazással, az összes diagnosztikai grafikon mindig elérhető az elemzéshez.

Az App Service diagnosztika nem csak a Windows alkalmazás, hanem a [Linux/containers,](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro) [az App Service Environment](https://docs.microsoft.com/azure/app-service/environment/intro)és az Azure [Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)alkalmazások esetében is működik.

## <a name="open-app-service-diagnostics"></a>Az App Service diagnosztikájának megnyitása

Az App Service-diagnosztika eléréséhez keresse meg az App Service webappját vagy az App Service-környezetet az [Azure Portalon.](https://portal.azure.com) A bal oldali navigációs, kattintson **a diagnosztizálása és a problémák megoldása**.

Az Azure Functions, keresse meg a függvényalkalmazást, és a felső navigációs, kattintson a **Platform funkciók**, és válassza a problémák diagnosztizálása és megoldása az **Erőforrás-kezelés** szakaszban válassza ki a **problémák diagnosztizálása és megoldása.**

Az App Service diagnosztikai kezdőlapján kiválaszthatja azt a kategóriát, amely a legjobban leírja az alkalmazással kapcsolatos problémát az egyes kezdőlap-csempék kulcsszavainak használatával. Ezen a lapon találhatók a **Windows-alkalmazások diagnosztikai eszközei** is. Lásd: [Diagnosztikai eszközök (csak Windows alkalmazáshoz)](#diagnostic-tools-only-for-windows-app).

![Kezdőlap](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Ha az alkalmazás nem vagy lassan teljesít, [profilkészítési nyomkövetést gyűjthet](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html) a probléma kiváltó okának azonosításához. A profilkészítés könnyű, és éles forgatókönyvekhez készült.
>

## <a name="interactive-interface"></a>Interaktív felület

Miután kiválasztotta az alkalmazás problémájához legjobban illeszkedő kezdőlap-kategóriát, az App Service diagnosztika interaktív felülete, a Genie végigvezeti Önt az alkalmazással kapcsolatos problémák diagnosztizálásán és megoldásán. A Genie által biztosított csempeparancsikonok segítségével megtekintheti az Önt érdeklő problémakategória teljes diagnosztikai jelentését. A csempe parancsikonok közvetlen hozzáférést biztosít a diagnosztikai metrikák.

![Mozaikbillentyűk](./media/app-service-diagnostics/tile-shortcuts-2.png)

Miután rákattintott ezekre a csempékre, megtekintheti a csempén leírt problémával kapcsolatos témakörök listáját. Ezek a témakörök a teljes jelentésből származó jelentős információk töredékeit tartalmazza. A problémák további kivizsgálásához kattintson a témakörök bármelyikére. Emellett a Teljes **jelentés megtekintése** gombra kattintva egyetlen oldalon találhatja meg az összes témakört.

![Témakörök](./media/app-service-diagnostics/application-logs-insights-3.png)

![Teljes jelentés megtekintése](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnosztikai jelentés

Miután úgy döntött, hogy vizsgálja meg a problémát tovább kattintva egy témát, megtekintheti további részleteket a témáról gyakran kiegészítve grafikonok és markdowns. A diagnosztikai jelentés hatékony eszköz lehet az alkalmazással kapcsolatos probléma azonosításához.

![Diagnosztikai jelentés](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Állapot-ellenőrzés

Ha nem tudja, mi a baj az alkalmazással, vagy nem tudja, hol kezdje el a problémák elhárítását, az állapotfelmérés jó kiindulópont. Az állapotfelmérés elemzi az alkalmazásokat, hogy gyors, interaktív áttekintést nyújtson, amely rámutat arra, hogy mi az egészséges és mi a baj, és megmondja, hogy hol keresse a problémát. Intelligens és interaktív felülete útmutatást nyújt a hibaelhárítási folyamat során. Az állapotalapú szűrés integrálva van a Genie szolgáltatással a Windows-alkalmazásokhoz és a webalkalmazás-le diagnosztikai jelentésa Linux-alkalmazásokhoz.

### <a name="health-checkup-graphs"></a>Állapot-ellenőrzési grafikonok

Négy különböző grafikonok az állapotfelmérés.

- **kérések és hibák:** Egy grafikon, amely az elmúlt 24 órában a HTTP-kiszolgáló hibáival együtt végrehajtott kérelmek számát mutatja.
- **alkalmazás teljesítménye:** Egy grafikon, amely különböző percentilis csoportok válaszidejét mutatja az elmúlt 24 órában.
- **CPU-használat:** Egy grafikon, amely megmutatja a teljes százalékos CPU-használat példányonként az elmúlt 24 órában.  
- **memóriahasználat:** Egy grafikon, amely az elmúlt 24 órában példányonkénti fizikai memóriahasználat teljes százalékát mutatja.

![Állapot-ellenőrzés](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Alkalmazáskóddal kapcsolatos problémák vizsgálata (csak Windows alkalmazásesetén)

Mivel számos alkalmazásprobléma kapcsolódik az alkalmazáskódban lévő problémákhoz, az App Service-diagnosztika integrálódik az [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) szolgáltatással, hogy kiemelje a kivételeket és a függőségi problémákat a kiválasztott állásidővel. Az Application Insights külön-külön engedélyezve kell lennie.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Az Application Insights-kivételek és -függőségek megtekintéséhez válassza a **webalkalmazást lefelé** vagy a **webalkalmazás lassú** csempebillentyűparancsait.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Hibaelhárítási lépések (csak Windows alkalmazásesetén)

Ha az elmúlt 24 órában egy adott problémakategóriával kapcsolatban problémát észlel, megtekintheti a teljes diagnosztikai jelentést, és az App Service diagnosztikája további hibaelhárítási tanácsok és a következő lépések megtekintését kérheti az irányított abbéli élmény érdekében.

![Az Application Insights és a hibaelhárítás, valamint a következő lépések](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnosztikai eszközök (csak Windows alkalmazáshoz)

A diagnosztikai eszközök fejlettebb diagnosztikai eszközöket tartalmaznak, amelyek segítenek az alkalmazáskódokkal kapcsolatos problémák, a lassúság, a kapcsolati karakterláncok és egyebek vizsgálatában. és proaktív eszközök, amelyek segítenek enyhíteni a processzorhasználattal, kérésekkel és memóriával kapcsolatos problémákat.

### <a name="proactive-cpu-monitoring"></a>Proaktív CPU-figyelés

A proaktív CPU-figyelés egyszerű és proaktív módot kínál arra, hogy végrehajtsa a műveletet, ha az alkalmazás vagy az alkalmazás gyermekfeldolgozása magas CPU-erőforrásokat fogyaszt. Beállíthatja a saját CPU-küszöbérték-szabályok ideiglenesen enyhíteni a magas CPU-probléma, amíg a nem várt probléma valódi oka található. További információ: [A processzorproblémák csökkentése azok bekövetkezése előtt.](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html)

![Proaktív CPU-figyelés](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing-and-proactive-auto-healing"></a>Auto-gyógyító és proaktív auto-gyógyító

Az automatikus javítás olyan kockázatcsökkentési művelet, amelyet akkor tehet meg, ha az alkalmazás nem várt módon viselkedik. Beállíthatja a saját szabályok alapján kérelmek száma, lassú kérelem, memóriakorlát és a HTTP-állapotkód kiváltó kockázatcsökkentő műveleteket. Az eszköz segítségével ideiglenesen mérsékelheti a váratlan viselkedést, amíg meg nem találja a kiváltó okot. További információ: [Az új automatikus gyógyulási élmény bejelentése az alkalmazásszolgáltatás diagnosztikájában](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html)című témakörben talál.

![Auto-gyógyulás](./media/app-service-diagnostics/auto-healing-10.png)

A proaktív CPU-figyeléshez hasonlóan a proaktív automatikus javítás is kulcsrakész megoldás az alkalmazás váratlan viselkedésének enyhítésére. A proaktív automatikus javítás újraindítja az alkalmazást, ha az App Service megállapítja, hogy az alkalmazás helyreállíthatatlan állapotban van. További információ: [Introducing Proactive Auto Heal](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigátor és módosításelemzés (csak Windows alkalmazáshoz)

A folyamatos integrációval rendelkező nagy csapatban, ahol az alkalmazás számos függőséget tartalmaz, nehéz lehet pontosan meghatározni az adott változást, amely nem megfelelő viselkedést okoz. A Navigátor segít az alkalmazás topológiájának láthatóságát azáltal, hogy automatikusan leteszi az alkalmazás és az ugyanazon előfizetés összes erőforrásának függőségi térképét. A Navigátor lehetővé teszi az alkalmazás által végrehajtott módosítások és függőségei összesített listájának megtekintését, valamint a nem megfelelő működést okozó módosítások szűkítését. Ez elérhető a honlapon csempe **Navigator,** és engedélyezni kell, mielőtt használja az első alkalommal. További információ: [Az alkalmazás függőségei megismerése a Navigátorral](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html)című témakörben található.

![A Navigátor alapértelmezett lapja](./media/app-service-diagnostics/navigator-default-page-11.png)

![Különbözet nézet](./media/app-service-diagnostics/diff-view-12.png)

Az alkalmazásmódosítások változáselemzése csempeparancsikonokon, **alkalmazásmódosításokon** és **alkalmazásösszeomlásokon** keresztül érhető el **a rendelkezésre állás és a teljesítmény területén,** így más mérőszámokkal egyidejűleg is használhatja. A szolgáltatás használata előtt először engedélyeznie kell azt. További információ: [Az Új változáselemzési élmény bejelentése az App Service diagnosztikában](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html)című témakörben talál.

Tegye fel kérdéseit vagy visszajelzését a [UserVoice-on](https://feedback.azure.com/forums/169385-web-apps) a "[Diag]" hozzáadásával a címben.
