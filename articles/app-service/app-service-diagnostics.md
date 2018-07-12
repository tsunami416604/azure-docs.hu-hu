---
title: Az Azure App Service – diagnosztika áttekintése |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatóak el a problémákat az App Service-diagnosztikával webalkalmazással együtt.
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
ms.openlocfilehash: 7ad205c75a02b496abe2cb910c7eb459cdb16c97
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969238"
---
# <a name="azure-app-service-diagnostics-overview"></a>Az Azure App Service – diagnosztika áttekintése 

Amikor egy webalkalmazás rendszert használ, érdemes elő kell készíteni a 500-as hibák a felhasználók számára arról tájékoztat, hogy webhelye nem működik az esetleg felmerülő problémákat. Az App Service diagnosztikái egy intelligens és interaktív élmény szükséges konfiguráció nélkül a webalkalmazás háríthatja el. Ha problémákat tapasztal a webalkalmazással, az App Service diagnosztikái meg, mi okozza a végigvezeti Önt a megfelelő információk könnyen és gyorsan elhárítása és a probléma megoldásához fog mutatni. 
 
Bár ez a tapasztalat akkor hasznos, ha problémákat tapasztal a webalkalmazással az elmúlt 24 órában, a diagnosztikai grafikonok elemezhet, mindig elérhető lesz. További hibaelhárítási eszközei és hasznos dokumentáció és fórumok mutató hivatkozásokat tartalmaz a jobb oldali oszlopban található.

Nem csak a Windows-alkalmazás, de az is alkalmazások működik az App Service diagnosztikái [Linux-tárolók](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro), [App Service Environment-környezet](https://docs.microsoft.com/azure/app-service/environment/intro), és [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Nyissa meg az App Service diagnosztikái

Az App Service diagnosztikái eléréséhez keresse meg az App Service-alkalmazás vagy az App Service-környezet a [az Azure portal](https://portal.azure.com). A bal oldali navigációs sávján kattintson a **diagnosztizálása és a problémák megoldásához**. 

Az Azure Functions, nyissa meg a függvényalkalmazást, és a felső navigációs, kattintson a **platformfunkciók** válassza **diagnosztizálása és a problémák megoldásában** a a **figyelés**szakaszban. 

![Kezdőlap](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Állapotfelülvizsgálatot

Ha nem tudja, mi okozza a webalkalmazással, vagy nem tudja, hol kell elkezdeni az kapcsolatos hibák elhárítása, a állapotfelülvizsgálatot egy nagyszerű hely az induláshoz. A állapotfelülvizsgálatot elemzi a webes alkalmazások, hogy adjon egy rövid, interaktív áttekintésben, amely mutat meg, mi a kifogástalan állapotú, és mi okozza, amely közli, hova kell néznie a probléma kivizsgálására. Az intelligens és interaktív felületen keresztül a hibaelhárítási folyamat útmutatást biztosít.  

![Állapotfelülvizsgálatot](./media/app-service-diagnostics/HealthCheckup2.png)

Ha problémát észlel az egy adott probléma kategóriája az elmúlt 24 órában, megtekintheti a teljes diagnosztikai jelentés, és az App Service diagnosztikái késztethetik, további hibaelhárítási tanácsokat és lépések több interaktív folyamat megtekintéséhez.

![Hibaelhárítás és a következő lépések](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Csempe parancsikonok

Ha tudja, hogy pontosan milyen hibaelhárítási információkat keres, a csempe parancsikonok léphet közvetlenül a teljes diagnosztikai jelentés az Önt érdeklő probléma kategóriába tartozó. A állapotfelülvizsgálatot képest, a csempe parancsikonok közvetlenül a további, de kisebb interaktív módon férnek hozzá a diagnosztikai metrikák. Csempe parancsikonok részeként, ez történik akkor is talál a **diagnosztikai eszközök** amely több speciális eszközöket, amelyekkel az alkalmazás kódja hibák, lassúsága, kapcsolati karakterláncok és egyéb kapcsolódó problémák kivizsgálásában. 

![Csempe parancsikonok](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnosztikai jelentés

E Futtatás után további információra van szüksége egy [állapotfelülvizsgálatot](#health-checkup) vagy az egyik lehetőséget választotta a [parancsikonok csempére](#tile-shortcuts), a teljes diagnosztikai jelentés megjelenik az elmúlt 24 órában megfelelő ábrázolt metrikáit. Ha az alkalmazás állásidő, azt egy narancssárga sáv alul az ütemterv képviseli. A leállás ideje, hogy az állásidő és a javasolt hibaelhárítási lépések kapcsolatos megfigyelések megtekintéséhez válassza a narancssárga sávok közül választhat. 

![Diagnosztikai jelentés](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Alkalmazás kód kapcsolatos problémák kivizsgálása

Számos alkalmazás probléma kapcsolatos problémákat, az alkalmazás kódjában, mert az App Service diagnosztikái integrálható [Application Insights](https://azure.microsoft.com/services/application-insights/) kivételeket és függőségi problémák korrelációját, ha a kiválasztott állásidővel. Az Application Insights külön-külön engedélyezni kell. 

Az Application Insights-kivételek és függőségek megtekintéséhez jelölje ki a **webalkalmazás nem érhető** vagy **webes alkalmazás lassú** parancsikonok csempére. 

![Application Insights](./media/app-service-diagnostics/AppInsights6.png)

