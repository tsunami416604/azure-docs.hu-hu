---
title: "Választás a következők közül: Flow, Logic Apps, Functions és WebJobs | Microsoft Docs"
description: "Hasonlítsa össze a Microsoft négy felhőintegrációs szolgáltatását, és döntse el, melyik szolgáltatást vagy szolgáltatásokat érdemes használnia."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "microsoft flow, folyamat, logic apps, azure functions, függvények, azure webjobs, webjobs, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 03246846484878f7155449ad11b009aeffe8a576
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2017
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Választás a következők közül: Flow, Logic Apps, Functions és WebJobs
Ez a cikk az integrációs problémák megoldására és üzleti folyamatok automatizálására alkalmas alábbi Microsoft Cloud-szolgáltatásokat hasonlítja össze:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Az itt felsorolt összes szolgáltatás segíthet a különálló rendszerek „összeragasztásában”. Mindegyik képes bemenet, műveletek, feltételek és kimenet meghatározására. Futtathatók ütemezve vagy eseményindítóval is. Azonban mindegyikük rendelkezik egyedi előnyökkel, így az összehasonlításnak nem célja eldönteni, melyik szolgáltatás a legjobb; ehelyett információt nyújt arról, melyik szolgáltatás a legmegfelelőbb az adott szituációban. Gyakran e szolgáltatások kombinációja a legjobb mód egy skálázható, teljes körű integrációs megoldás gyors felépítésére.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow és Logic Apps
A Microsoft Flow és az Azure Logic Apps szolgáltatásokat áttekinthetjük párhuzamosan, mivel mindkét integrációs szolgáltatásban a *konfiguráció* a kiindulási pont. Megkönnyítik a folyamatok és munkafolyamatok felépítését, valamint a különböző SaaS- és vállalati alkalmazásokkal történő integrációt. 

* A Flow a Logic Appsre épül
* Ugyanazzal a munkafolyamat-tervezővel rendelkeznek
* Az egyik szolgáltatásban működő [összekötők](../connectors/apis-list.md) működhetnek a másikban is

A Flow segítségével bármely irodai dolgozó képes végrehajtani egyszerű integrációkat (például SMS üzenet küldése mobiltelefonra fontos e-mail érkezésekor) anélkül, hogy a fejlesztőkhöz vagy informatikai szakemberhez kellene fordulnia. A Logic Apps viszont ehetővé tesz olyan haladó szintű vagy alapvető fontosságú integrációkat (például B2B folyamatokat), amelyekben vállalati szintű DevOps és biztonsági gyakorlatok szükségesek. Az üzleti munkafolyamatokra jellemző, hogy idővel egyre összetettebbé válnak. Ennek megfelelően kezdhet egy folyamattal, majd szükség szerint átalakíthatja logikai alkalmazássá.

Az alábbi táblázat segítségével eldöntheti, hogy a Flow vagy a Logic Apps felel meg Önnek jobban egy adott integráció esetében.

|  | Folyamat | Logic Apps |
| --- | --- | --- |
| Célközönség |Irodai dolgozók, üzleti felhasználók |Informatikai szakemberek, fejlesztők |
| Forgatókönyvek |Önkiszolgáló |Alapvető fontosságú |
| Tervezőeszköz |Böngészőbeli és mobilalkalmazás, kizárólag felhasználói felület |Böngészőbeli, [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Kódnézet](../logic-apps/logic-apps-author-definitions.md) elérhető |
| DevOps |Ad hoc, fejlesztés éles környezetben |verziókövetés, tesztelés, támogatás, automatizálás és kezelhetőség az [Azure Resource Management](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) eszközzel |
| Rendszergazdai feladatok |[https://flow.microsoft.com](https://flow.microsoft.com) |[https://portal.azure.com](https://portal.azure.com) |
| Biztonság |Általános gyakorlatok: [adatszuverenitás](https://wikipedia.org/wiki/Technological_Sovereignty), bizalmas adatok [titkosítása inaktív állapotban](https://wikipedia.org/wiki/Data_at_rest#Encryption), stb. |Az Azure által garantált biztonság: [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Biztonsági Központ](https://azure.microsoft.com/services/security-center/), [naplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), stb. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions és WebJobs
Az Azure Functions és az Azure App Service szolgáltatásokat áttekinthetjük párhuzamosan, mivel mindkét integrációs szolgáltatásban a *kód* a kiindulási pont, és mindkettőt fejlesztők részére tervezték. Segítségükkel szkripteket vagy kódrészleteket futtathat különféle eseményekre reagálva (pl. [új tárolóblob](functions-bindings-storage.md) vagy egy [webhook-kérelem](functions-bindings-http-webhook.md)). Hasonlóságok: 

* Mindkettő az [Azure App Service](../app-service/app-service-web-overview.md) szolgáltatásra épül, és rendelkezik többek között a következő funkciókkal: [verziókövetés](../app-service/app-service-continuous-deployment.md), [hitelesítés](../app-service/app-service-authentication-overview.md) és [monitorozás](../app-service/web-sites-monitor.md).
* Mindkét szolgáltatás fejlesztőközpontú.
* Mindkettő támogatja a standard szkripteket és programozási nyelveket.
* Mindkettő rendelkezik NuGet és NPM támogatással.

A Functions a WebJobs természetes továbbfejlődése; annak legjobb tulajdonságait emeli még magasabb szintre. A fejlesztések az alábbiak: 

* [Kiszolgáló nélküli](https://azure.microsoft.com/overview/serverless-computing/) alkalmazásmodell.
* Kódok zökkenőmentes fejlesztése, tesztelése és futtatása, közvetlenül a böngészőben.
* Beépített integráció több Azure-szolgáltatással és harmadik féltől származó szolgáltatásokkal (például a [GitHub WebHooks](https://developer.github.com/webhooks/creating/)).
* Használatalapú fizetés, [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) vásárlása nem szükséges.
* Automatikus, [dinamikus méretezés](functions-scale.md).
* Az App Service meglévő ügyfelei számára az App Service-csomaggal történő futtatás továbbra is lehetséges (a kevésbé használt erőforrások kihasználása érdekében).
* Integráció a Logic Apps szolgáltatással

Az alábbi táblázat a Functions és a WebJobs közötti különbségeket foglalja össze:

|  | Functions | WebJobs |
| --- | --- | --- |
| Méretezés |Konfiguráció nélküli méretezés |Méretezés App Service-csomaggal |
| Díjszabás |Használatalapú fizetés vagy az App Service-csomag része |Az App Service-csomag része |
| Futtatási típus |Aktivált, ütemezett (időzítő eseményindító által) |Aktivált, folyamatos, ütemezett |
| Kiváltó események |[Időzítő](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-documentdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md), [Azure Service Bus üzenetsorok és témakörök](functions-bindings-service-bus.md) |[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md), [Azure Service Bus üzenetsorok és témakörök](functions-bindings-service-bus.md) |
| Fejlesztés böngészőben |Támogatott |Nem támogatott |
| C# |Támogatott |Támogatott |
| F# |Támogatott |Nem támogatott |
| JavaScript |Támogatott |Támogatott |
| Java |Előzetes verzió | Nem támogatott |
| Bash |Kísérleti |Támogatott |
| Windows szkriptek (.cmd, .bat) |Kísérleti |Támogatott |
| PowerShell |Kísérleti |Támogatott |
| PHP |Kísérleti |Támogatott |
| Python |Kísérleti |Támogatott |
| TypeScript |Kísérleti |Nem támogatott |

A Functions és a WebJobs közötti választás attól függ, eddig mire használta az App Service szolgáltatást. Ha van egy App Service alkalmazása, amellyel kódrészleteket szeretne futtatni, és közösen szeretné őket kezelni ugyanabban a DevOps-környezetben, használja a WebJobs szolgáltatást. Az alábbi esetekben használja a Functions szolgáltatást.

* Kódrészleteket szeretne futtatni más Azure-szolgáltatások vagy harmadik féltől származó alkalmazások esetében.
* Integrációs kódját külön szeretné kezelni App Service-alkalmazásaitól.
* Egy logikai alkalmazásból szeretné meghívni kódrészleteit. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps, és Functions együtt
Mint azt korábban említettük, az adott helyzettől függ, melyik a legmegfelelőbb szolgáltatás az Ön számára. 

* Egyszerű vállalati optimalizáláshoz használja a Flow szolgáltatást.
* Ha integrációs forgatókönyve túl összetett a Flow számára, vagy a DevOps-környezet funkcióira és biztonsági megfelelőségre van szüksége, használja a Logic Apps szolgáltatást.
* Ha az integrációs forgatókönyvének egy lépéséhez nagyfokú egyéni átalakításra vagy specializált kódra van szükség, írjon egy függvényt, és aktiválja műveletként a logikai alkalmazásban.

Meghívhat egy logikai alkalmazást egy folyamatban. Függvényt is meghívhat egy logikai alkalmazásban, és logikai alkalmazást is egy függvényben. A Flow, a Logic Apps és a Functions közötti integráció folyamatosan javul. Amit létrehoz az egyik szolgáltatásban, használhatja a többiben. Ezért a három technológia bármelyikébe megéri befektetni.

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg az egyes szolgáltatásokkal az első folyamat, logikai alkalmazás, függvényalkalmazás vagy WebJob létrehozása révén. Kattintson a következő hivatkozások bármelyikére:

* [A Microsoft Flow használatának első lépései](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Logikai alkalmazás létrehozása](../logic-apps/logic-apps-create-a-logic-app.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)
* [WebJobs üzembe helyezése Visual Studióval](../app-service/websites-dotnet-deploy-webjobs.md)

Az integrációs szolgáltatások további információit a következő hivatkozások használatával érhetők el:

* [Christopher Anderson: Leveraging Azure Functions & Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Az Azure Functions és az Azure App Service használata integrációs forgatókönyvekhez)
* [Charles Lamanna: Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integráció egyszerűen)
* [Logic Apps élő webszórás](http://aka.ms/logicappslive)
* [Microsoft Flow – Gyakori kérdések](https://flow.microsoft.com/documentation/frequently-asked-questions/)

