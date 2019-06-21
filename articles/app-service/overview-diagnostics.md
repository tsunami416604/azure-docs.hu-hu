---
title: Az Azure App Service – diagnosztika áttekintése |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatóak el a problémákat az alkalmazását az App Service-diagnosztikával.
keywords: App Service-ben, az azure app Service-ben, diagnosztika, támogatási, webalkalmazás, hibaelhárítás, önsegítő anyagok
services: app-service
documentationcenter: ''
author: jen7714
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.custom: seodec18
ms.openlocfilehash: f2f798be85e9c3aeb8d4b54cba89d8be059427e0
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147340"
---
# <a name="azure-app-service-diagnostics-overview"></a>Az Azure App Service – diagnosztika áttekintése

Amikor egy webalkalmazás rendszert használ, érdemes elő kell készíteni a 500-as hibák a felhasználók számára arról tájékoztat, hogy webhelye nem működik az esetleg felmerülő problémákat. Az App Service diagnosztikái egy intelligens és interaktív élmény szükséges konfiguráció nélkül az alkalmazás háríthatja el. Ha problémákat tapasztal az alkalmazással, az App Service-diagnosztikával mutat meg, mi okozza a végigvezeti Önt a megfelelő információk könnyen és gyorsan elhárítása és a probléma megoldásához.

Bár ez a tapasztalat akkor hasznos, ha problémákat tapasztal az alkalmazással az elmúlt 24 órában, a diagnosztikai grafikonok érhetők el mindig elemezhet.

Nem csak a Windows-alkalmazás, de az is alkalmazások működik az App Service diagnosztikái [Linux-tárolók](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/environment/intro), és [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).

## <a name="open-app-service-diagnostics"></a>Nyissa meg az App Service diagnosztikái

Az App Service diagnosztikái eléréséhez keresse meg az App Service-webalkalmazás vagy az App Service-környezet a [az Azure portal](https://portal.azure.com). A bal oldali navigációs sávján kattintson a **diagnosztizálása és a problémák megoldásához**.

Az Azure Functions, nyissa meg a függvényalkalmazást, és a felső navigációs, kattintson a **platformfunkciók**, és válassza ki **diagnosztizálása és a problémák megoldásához** a a **erőforrás-kezelés** szakaszban.

Az App Service-ben diagnosztikai kezdőlapjának választhatja ki a kategóriát, amely a legjobban ismerteti a problémát, az alkalmazás kezdőlapjára csempéket a kulcsszavak használatával. Ez az oldal is, ahol megtalálja **diagnosztikai eszközök** Windows-alkalmazások. Lásd: [diagnosztikai eszközök (csak a Windows-alkalmazás)](#diagnostic-tools-only-for-windows-app).

![Homepage](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

## <a name="interactive-interface"></a>Interaktív felület

Miután kiválasztotta a kezdőlap kategória, amely leginkább megfelel az alkalmazás a probléma, az App Service diagnosztikái interaktív felületét, Genie, is nyújt útmutatást a diagnosztizálásához és az alkalmazást a probléma megoldásához. A csempe parancsikonok Genie által biztosított használhatja a teljes diagnosztikai jelentés az Önt érdeklő a probléma kategória megtekintéséhez. A csempe parancsikonok férnek hozzá a diagnosztikai metrikák közvetlen lehetőséget biztosíthat.

![Csempe parancsikonok](./media/app-service-diagnostics/tile-shortcuts-2.png)

Után ezek a csempék kattint, a csempe ismertetett a problémához kapcsolódó témakörök listáját láthatja. Ezek a témakörök a teljes jelentés jelentős adatainak kódrészletek. Kattintson a következő témakörökhöz, továbbá a problémák vizsgálatára. Emellett, kattintson a **teljes jelentés megtekintése** fedezheti fel az összes téma egy oldalon.

![Témakörök](./media/app-service-diagnostics/application-logs-insights-3.png)

![Teljes jelentés megtekintése](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnosztikai jelentés

Miután a probléma további vizsgálata témakörökre kattintva választja, megtekintheti további információt a témakör grafikonokkal és markdowns gyakran kiegészíteni. Diagnosztikai jelentés hatékony eszköz a felügyelő a probléma merült fel az alkalmazás lehet.

![Diagnosztikai jelentés](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Állapotfelülvizsgálatot

Ha nem tudja, mi okozza az alkalmazással, vagy nem tudja, hol kell elkezdeni az kapcsolatos hibák elhárítása, a állapotfelülvizsgálatot egy nagyszerű hely az induláshoz. A állapotfelülvizsgálatot elemzi az alkalmazások, hogy adjon egy rövid, interaktív áttekintésben, amely mutat meg, mi a kifogástalan állapotú, és mi okozza, amely közli, hova kell néznie a probléma kivizsgálására. Az intelligens és interaktív felületen keresztül a hibaelhárítási folyamat útmutatást biztosít. Állapotfelülvizsgálatot integrálva van a Genie élmény a Windows-alkalmazások és a web app lefelé diagnosztikai jelentés Linux-alkalmazások.

### <a name="health-checkup-graphs"></a>Egészségügyi felülvizsgálata diagramok

A állapotfelülvizsgálatot négy különböző diagramok találhatók.

- **kérelmek és hibák:** Diagramját, amelyek az elmúlt 24 órában HTTP Kiszolgálóhibák együtt végrehajtott kérelmek száma.
- **alkalmazások teljesítményét:** Válaszidő az elmúlt 24 órában különböző PERCENTILIS csoportok keresztül megjelenítő grafikon.
- **CPU-használat:** A példányonként átfogó készültségi CPU-használat az elmúlt 24 órában megjelenítő grafikon.  
- **memóriahasználat:** A példányonként átfogó készültségi fizikaimemória-használat az elmúlt 24 órában megjelenítő grafikon.

![Állapotfelülvizsgálatot](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>(Csak a Windows-alkalmazás) application code kapcsolatos problémák kivizsgálása

Számos alkalmazás probléma kapcsolatos problémákat, az alkalmazás kódjában, mert az App Service diagnosztikái integrálható [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) kivételeket és függőségi problémák korrelációját, ha a kiválasztott állásidővel. Az Application Insights rendelkezik külön-külön engedélyezni kell.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Az Application Insights-kivételek és függőségek megtekintéséhez jelölje ki a **webalkalmazás le** vagy **lassú webalkalmazás** parancsikonok csempére.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Hibaelhárítási lépéseit (csak Windows-alkalmazás)

Ha problémát észlel az egy adott probléma kategóriája az elmúlt 24 órában, megtekintheti a teljes diagnosztikai jelentést, és az App Service diagnosztikái késztethetik, további hibaelhárítási tanácsokat és lépések több interaktív folyamat megtekintéséhez.

![Az Application Insights és a hibaelhárítási és a következő lépések](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools-only-for-windows-app"></a>Diagnosztikai eszközök (csak a Windows-alkalmazás)

Diagnosztikai eszközök közé tartozik, hogy keresse meg a alkalmazást súgó code problémák, lassúsága, kapcsolati karakterláncok és további speciális diagnosztikai eszközök. eszközök, amelyek segítségével proaktív problémák és a CPU-használat, a kérelmektől és a memória.

### <a name="proactive-cpu-monitoring"></a>Proaktív CPU-figyelés

Proaktív CPU figyelését teszi lehetővé egy egyszerű és proaktív módon elvégzendő művelet az alkalmazás- és gyermekhelyek folyamat az alkalmazás használ magas Processzor-erőforrások. Beállíthatja a saját CPU küszöbértékszabályainak ideiglenesen elhárításukra a magas CPU mindaddig, amíg a valódi oka a nem várt hiba található.

![Proaktív CPU-figyelés](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="proactive-auto-healing"></a>Proaktív automatikus javítás

Proaktív CPU figyelését, például proaktív automatikus javítás egy egyszerű, proaktív megközelítést kínál az csökkentése az alkalmazás nem várt viselkedést. Beállíthatja a saját kérések száma, a lassú lekéréshez, a memória felső korlátja és a kockázatcsökkentési műveleteket aktiválhat a HTTP-állapotkód: alapuló szabály. Ezzel az eszközzel használható ideiglenesen csökkentése érdekében egy nem várt viselkedést, amíg nem található a valódi a probléma okát. A proaktív automatikus javítás további információért látogasson el [megjelent az új automatikus, az app service diagnostics élményt javító](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html).

![Proaktív automatikus javítás](./media/app-service-diagnostics/proactive-auto-healing-10.png)

## <a name="change-analysis-only-for-windows-app"></a>Elemzés (csak a Windows-alkalmazás) módosítása

Ritmusú fejlesztői környezetben néha nehéz lehet, nyomon követheti az alkalmazás végzett összes módosítást, és lehetővé teszik önálló pinpoint a nem megfelelő viselkedésének okozó módosítása. Módosítás elemzés segíthet leszűkíteni a hibaelhárítással élmény megkönnyítése érdekében az alkalmazás végzett módosításokat. Változás elemzési található **alkalmazások módosítására** is ágyazott például diagnosztikai jelentés **alkalmazás összeomlik** így használhatja más metrikákkal már létesítve lett.

Változás elemzésnek engedélyezni kell a funkció használata előtt. Változás-elemzési további információért látogasson el [bejelentése az új módosítás elemzési az App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html).

![Változás elemzési alapértelmezett lap](./media/app-service-diagnostics/change-analysis-default-page-11.png)

![A diff megtekintése](./media/app-service-diagnostics/diff-view-12.png)