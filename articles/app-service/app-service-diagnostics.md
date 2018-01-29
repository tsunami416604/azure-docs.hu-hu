---
title: "Az Azure App Service diagnosztika áttekintése |} Microsoft Docs"
description: "Ismerje meg, hogyan háríthatóak el a problémák a webalkalmazással App Service diagnosztika."
keywords: "az App service, azure app service, diagnosztika, támogatási, webalkalmazás, hibaelhárítási segítséget"
services: app-service
documentationcenter: 
author: jen7714
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: jennile
ms.openlocfilehash: 9526817ce7969edcd5e9c56ec153bb4e3ebaa501
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
---
# <a name="azure-app-service-diagnostics-overview"></a>Az Azure App Service diagnosztika áttekintése 

Miután webalkalmazást futtatja, elő kell készíteni a 500 hibák felszólító a felhasználóknak, hogy a hely nem működik az esetleg felmerülő problémákkal szeretné. App Service diagnosztika segítséget nyújthatnak a webalkalmazás nem szükséges konfiguráció az intelligens és interaktív élményt. A webalkalmazással problémákat tapasztal, amikor az App Service diagnosztika mutat, a nem megfelelő, hogy végigvezesse a megfelelő információk könnyen és gyorsan elhárításához és hárítsa el a problémát. 
 
Bár ez a felület akkor hasznos, ha tapasztal problémákat a webalkalmazással az elmúlt 24 órában, az diagnosztikai grafikon elemzése mindig elérhető lesz. További hibaelhárítási eszközök és hasznos dokumentáció és fórumok mutató hivatkozásokat tartalmaz a jobb oldali oszlopban található.

## <a name="open-app-service-diagnostics"></a>Nyissa meg az App Service-diagnosztika

App Service diagnostics elérésére az App Service webalkalmazás navigáljon a [Azure-portálon](https://portal.azure.com). 

A bal oldali navigációs, kattintson a **Diagnosztizálás és problémák megoldására**.

![Kezdőlap](./media/app-service-diagnostics/Homepage1.png)

## <a name="health-checkup"></a>Állapotfigyelő checkup

Ha nem tudja, milyen probléma vele a webalkalmazás, vagy nem tudja, hol kell elkezdeni a problémák elhárításához, az egészségügyi checkup egy remek kezdőpont. Az állapotfigyelő checkup elemzi a webalkalmazások gyors és interaktív megismerésében, amely kifogástalan és hibát, vizsgálja meg a probléma hol felszólító mutat. Az intelligens és interaktív felületet nyújt útmutatást a hibaelhárítási folyamat során.  

![Állapotfigyelő checkup](./media/app-service-diagnostics/HealthCheckup2.png)

Ha problémát észlel egy adott probléma kategória az elmúlt 24 órában, megtekintheti a teljes diagnosztikai jelentést, és az App Service diagnosztika kérheti a további hibaelhárítási tanácsokra és több elősegítheti a következő lépéseket.

![Hibaelhárítási és a következő lépések](./media/app-service-diagnostics/Troubleshooting3.png)

## <a name="tile-shortcuts"></a>Mozaik parancsikonok

Ha tudja, hogy pontosan milyen hibaelhárítási információkat keres, a csempe parancsikonok léphet vissza közvetlenül a probléma kategória érdekli, a teljes diagnosztikai jelentés. A health checkup képest, a csempe parancsikonok közvetlenül a további, de kisebb interaktív módon történő eléréséhez a diagnosztikai metrikákat.  

![Mozaik parancsikonok](./media/app-service-diagnostics/TileShortcuts4.png)

## <a name="diagnostic-report"></a>Diagnosztikai jelentés

E futtatása után további információt szeretne a [állapotfigyelő checkup](#health-checkup) vagy az egyik kattintott a [parancsikonok csempe](#tile-shortcuts), a teljes diagnosztikai jelentés bemutatja az elmúlt 24 órában a megfelelő grafikon metrikákat. Ha az alkalmazás állásidő, az ütemterv alatt megjelenő narancssárga sáv jelzi. Az állásidő és a javasolt megoldásokról elemzett megfigyelések beolvasandó állásidőt közül választhat. 

![Diagnosztikai jelentés](./media/app-service-diagnostics/DiagnosticReport5.png)

