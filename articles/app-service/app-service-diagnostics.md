---
title: Az Azure App Service diagnosztika áttekintése |} Microsoft Docs
description: Ismerje meg, hogyan háríthatóak el a problémák a webalkalmazással App Service diagnosztika.
keywords: az App service, azure app service, diagnosztika, támogatási, webalkalmazás, hibaelhárítási segítséget
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
ms.openlocfilehash: 50e0e9f5edc18aac42ee80e232f70e09736124bc
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Az Azure App Service diagnosztika áttekintése 

Miután webalkalmazást futtatja, elő kell készíteni a 500 hibák felszólító a felhasználóknak, hogy a hely nem működik az esetleg felmerülő problémákkal szeretné. App Service diagnosztika segítséget nyújthatnak a webalkalmazás nem szükséges konfiguráció az intelligens és interaktív élményt. A webalkalmazással problémákat tapasztal, amikor az App Service diagnosztika mutat, a nem megfelelő, hogy végigvezesse a megfelelő információk könnyen és gyorsan elhárításához és hárítsa el a problémát. 
 
Bár ez a felület akkor hasznos, ha tapasztal problémákat a webalkalmazással az elmúlt 24 órában, az diagnosztikai grafikon elemzése mindig elérhető lesz. További hibaelhárítási eszközök és hasznos dokumentáció és fórumok mutató hivatkozásokat tartalmaz a jobb oldali oszlopban található.

App Service diagnosztika működéséről nem csak a Windows alkalmazás, de is alkalmazások [Linux/tárolók](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro), [App Service Environment-környezet](https://docs.microsoft.com/en-us/azure/app-service/environment/intro), és [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-overview). 

## <a name="open-app-service-diagnostics"></a>Nyissa meg az App Service-diagnosztika

App Service diagnosztika szeretne használni, keresse meg az App Service-alkalmazást vagy az App Service-környezet a [Azure-portálon](https://portal.azure.com). A bal oldali navigációs, kattintson a **Diagnosztizálás és problémák megoldására**. 

Az Azure Functions, nyissa meg a függvény alkalmazást, és a felső navigációs, kattintson a **Platform funkciói** válassza **Diagnosztizálás és problémák megoldására** a a **figyelés**szakasz. 

![Kezdőlap](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Állapotfigyelő checkup

Ha nem tudja, milyen probléma vele a webalkalmazás, vagy nem tudja, hol kell elkezdeni a problémák elhárításához, az egészségügyi checkup egy remek kezdőpont. Az állapotfigyelő checkup elemzi a webalkalmazások gyors és interaktív megismerésében, amely kifogástalan és hibát, vizsgálja meg a probléma hol felszólító mutat. Az intelligens és interaktív felületet nyújt útmutatást a hibaelhárítási folyamat során.  

![Állapotfigyelő checkup](./media/app-service-diagnostics/HealthCheckup2.png)

Ha problémát észlel egy adott probléma kategória az elmúlt 24 órában, megtekintheti a teljes diagnosztikai jelentést, és az App Service diagnosztika kérheti a további hibaelhárítási tanácsokra és több elősegítheti a következő lépéseket.

![Hibaelhárítási és a következő lépések](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Mozaik parancsikonok

Ha tudja, hogy pontosan milyen hibaelhárítási információkat keres, a csempe parancsikonok léphet vissza közvetlenül a probléma kategória érdekli, a teljes diagnosztikai jelentés. A health checkup képest, a csempe parancsikonok közvetlenül a további, de kisebb interaktív módon történő eléréséhez a diagnosztikai metrikákat. A csempe parancsikonok részeként, ez történik akkor is talál a **diagnosztikai eszközök** amely több speciális eszközök, amelyek segítségével vizsgálja meg az alkalmazás kódja hibák, lassúsága, kapcsolati karakterláncok és több kapcsolatos problémákat. 

![Mozaik parancsikonok](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnosztikai jelentés

E futtatása után további információt szeretne a [állapotfigyelő checkup](#health-checkup) vagy az egyik kattintott a [parancsikonok csempe](#tile-shortcuts), a teljes diagnosztikai jelentés bemutatja az elmúlt 24 órában a megfelelő grafikon metrikákat. Ha az alkalmazás állásidő, az ütemterv alatt megjelenő narancssárga sáv jelzi. Jelölje be az állásidő megtekintéséhez, hogy az állásidő és a javasolt hibaelhárítási lépéseket kapcsolatos megfigyelések narancssárga sávok közül választhat. 

![Diagnosztikai jelentés](./media/app-service-diagnostics/DiagnosticReport5.png)


## <a name="investigating-application-code-issues"></a>Alkalmazás kód problémák kivizsgálása

Számos alkalmazás problémákkal kapcsolatos problémákat, az alkalmazás kódjában, mert az App Service diagnosztika integrálható [Application Insights](https://azure.microsoft.com/services/application-insights/) kiemeléséhez kivételeket és függőségi problémák és a kijelölt állásidő összefüggéseket. Az Application Insights külön-külön engedélyezni kell. 

Az Application Insights kivételeket és a függőségek megtekintéséhez jelölje ki a **Web App le** vagy **Web App lassú** parancsikonok csempére. 

![Application Insights](./media/app-service-diagnostics/AppInsights6.png)

