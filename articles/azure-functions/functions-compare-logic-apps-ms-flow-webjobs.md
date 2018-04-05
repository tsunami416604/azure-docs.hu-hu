---
title: 'Választás a következők közül: Flow, Logic Apps, Functions és WebJobs | Microsoft Docs'
description: Hasonlítsa össze a Microsoft négy felhőintegrációs szolgáltatását, és döntse el, melyik szolgáltatást vagy szolgáltatásokat érdemes használnia.
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: wpickett
tags: ''
keywords: microsoft flow, folyamat, logic apps, azure functions, függvények, azure webjobs, webjobs, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/20/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 577031c58e95781dc97721acc71fb22114b1c606
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Választás a következők közül: Flow, Logic Apps, Functions és WebJobs

Ez a cikk az alábbi Microsoft Cloud-szolgáltatásokat hasonlítja össze:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Mindegyik szolgáltatás megoldhatja az integrációs problémákat és automatizálhatja az üzleti folyamatokat. Mindegyik képes bemenet, műveletek, feltételek és kimenet meghatározására. Futtathatók ütemezve vagy eseményindítóval is. Azonban mindegyikük rendelkezik egyedi előnyökkel, és ez a cikk ismerteti a különbségeket.

## <a name="flow-vs-logic-apps"></a>Flow és Logic Apps

A Microsoft Flow és az Azure Logic Apps szolgáltatásban is a *konfiguráció* a kiindulási pont. Mindkettő olyan munkafolyamatokat hoz létre, amelyeket integrálhat SaaS- és vállalati alkalmazásokkal. 

A Flow a Logic Appsre épül. Ugyanazzal a munkafolyamat-tervezővel és azonos [Összekötőkkel](../connectors/apis-list.md) rendelkeznek. 

A Flow segítségével bármely irodai dolgozó képes végrehajtani egyszerű integrációkat (például egy SharePoint-dokumentumtár jóváhagyási folyamata) anélkül, hogy a fejlesztőkhöz vagy informatikai szakemberhez kellene fordulnia. A Logic Apps viszont lehetővé tesz olyan haladó szintű integrációkat (például B2B folyamatokat), amelyekben vállalati szintű DevOps és biztonsági gyakorlatok szükségesek. Az üzleti munkafolyamatokra jellemző, hogy idővel egyre összetettebbé válnak. Ennek megfelelően kezdhet egy folyamattal, majd szükség szerint átalakíthatja logikai alkalmazássá.

Az alábbi táblázat segítségével eldöntheti, hogy a Flow vagy a Logic Apps felel meg Önnek jobban egy adott integráció esetében.

|  | Folyamat | Logic Apps |
| --- | --- | --- |
| Felhasználók |Irodai dolgozók, üzleti felhasználók, SharePoint-adminisztrátorok |Professzionális integrátorok és fejlesztők, informatikai szakemberek |
| Forgatókönyvek |Önkiszolgáló |Speciális integrációk |
| Tervezőeszköz |Böngészőbeli és mobilalkalmazás, kizárólag felhasználói felület |Böngészőbeli, [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Kódnézet](../logic-apps/logic-apps-author-definitions.md) elérhető |
| Alkalmazások életciklus-felügyelete |Tervezés és tesztelés nem éles környezetekben, majd ezek elkészültével előléptetés éles környezetbe. |DevOps: verziókövetés, tesztelés, támogatás, automatizálás és kezelhetőség az [Azure Resource Management](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) eszközzel |
| Rendszergazdai feladatok |A Flow-környezetek és az adatveszteség-megelőzési szabályzatok kezelése, licencek nyomon követése [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Erőforráscsoportok kezelése, kapcsolatok, hozzáférés-kezelés és naplózás [https://portal.azure.com](https://portal.azure.com) |
| Biztonság |Az Office 365 biztonsági és megfelelőségi auditnaplói, adatveszteség-megelőzési szabályzatok, érzékeny adatok [titkosítása inaktív állapotban](https://wikipedia.org/wiki/Data_at_rest#Encryption) stb. |Az Azure által garantált biztonság: [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Biztonsági Központ](https://azure.microsoft.com/services/security-center/), [naplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), stb. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions és WebJobs

Ugyanúgy, mint az Azure Functions, az Azure App Service WebJobs a WebJobs SDK-val is olyan integrációs szolgáltatás, ahol a *kód* a kiindulási pont, és mindkettőt fejlesztők részére tervezték. Mindkettő az [Azure App Service](../app-service/app-service-web-overview.md) szolgáltatásra épül, és támogatják többek között a következő funkciókat: [verziókövetés integrálása](../app-service/app-service-continuous-deployment.md), [hitelesítés](../app-service/app-service-authentication-overview.md) és [Application Insights-integrációs monitorozás](functions-monitoring.md).

### <a name="webjobs-vs-the-webjobs-sdk"></a>A WebJobs összehasonlítása a WebJobs SDK-val

Az App Service *WebJobs* szolgáltatása lehetővé teszi szkriptek vagy kódok App Service-webalkalmazások környezetében való futtatását. A *WebJobs SDK* egy, a WebJobshoz tervezett keretrendszer, amely leegyszerűsíti az Azure-szolgáltatások válaszadására írt kódokat. Egy, az Azure Storage-ban létrehozott képblobra például válaszolhat egy miniatűr létrehozásával. A WebJobs SDK .NET-konzolalkalmazásként fut, amelyet üzembe helyezhet egy WebJobon. 

A WebJobs és a WebJobs SDK együtt működnek a leghatékonyabban, de használhatja az egyiket a másik nélkül is. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben. Egy WebJobs SDK-konzolalkalmazás bárhol futtatható, ahol konzolalkalmazások futtathatók, például a helyszíni kiszolgálókon.

### <a name="comparison-table"></a>Összehasonlító táblázat

Az Azure Functions a WebJobs SDK-ra épül, így sok, más Azure-szolgáltatásokkal közös eseményindítóval és kapcsolattal rendelkezik. Íme néhány tényező, amelyet érdemes figyelembe venni, mielőtt döntene az Azure Functions vagy a WebJobs és a WebJobs SDK használata között:

|  | Functions | WebJobs WebJobs SDK-val |
| --- | --- | --- |
|[Kiszolgáló nélküli alkalmazásmodell](https://azure.microsoft.com/overview/serverless-computing/) [automatikus skálázással](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Fejlesztése és tesztelés böngészőben](functions-create-first-azure-function.md) |✔||
|[Használatalapú fizetés](functions-scale.md#consumption-plan)|✔||
|[Integráció a Logic Apps szolgáltatással](functions-twitter-email.md)|✔||
| Kiváltó események |[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Fájlrendszer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Támogatott nyelvek  |C#<br>F#<br>JavaScript<br>Java (előzetes verzió) |C#<sup>1</sup>|
|Csomagkezelők|NPM és NuGet|NuGet<sup>2</sup>|

<sup>1</sup> A WebJobs (a WebJobs SDK nélkül) a következőket támogatja: C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python stb. A lista nem teljes, a WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben.

<sup>2</sup> A WebJobs (WebJobs SDK nélkül) támogatja az NPM-et és a NuGetet.

### <a name="summary"></a>Összegzés

Az Azure Functions nagyobb fejlesztési hatékonyságot tesz lehetővé, több programozási nyelvet, több fejlesztési környezetet, több Azure szolgáltatásintegrációs lehetőséget és több árképzési lehetőséget kínál. A legtöbb forgatókönyvhöz ez a legjobb választás.

Az alábbiakban két olyan forgatókönyvet láthat, amelyekhez a WebJobs lehet a legjobb választás:

* Az eseményekre figyelő kódok nagyobb mértékű felügyeletére van szüksége – `JobHost` objektum. A Functions korlátozott számú módszert biztosít a `JobHost`-viselkedés a [host.json](functions-host-json.md) fájlban való testreszabására. Néha olyan műveletekre lehet szüksége, amelyek nem adhatók meg egy JSON-fájlbeli karakterláncban. Például csak a WebJobs SDK teszi lehetővé egyéni újrapróbálkozási szabályzat konfigurálását az Azure Storage-ban.
* Van egy App Service-alkalmazása, amelyhez kódrészleteket szeretne futtatni, és közösen szeretné őket kezelni ugyanabban a DevOps-környezetben.

Minden egyéb olyan forgatókönyv esetén, ahol kódrészletek futtatásával kíván Azure- vagy külső szolgáltatásokat integrálni, válassza az Azure Functionst a WebJobs és WebJobs SDK helyett.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>A Flow, a Logic Apps, a Functions és a WebJobs együttes használata

Nem kell csupán egyet kiválasztania ezen szolgáltatások közül, mivel ugyanúgy integrálhatók egymással, mint más külső szolgáltatásokkal.

Egy folyamat meghívhat egy logikai alkalmazást. Egy logikai alkalmazás meghívhat egy függvényt, egy függvény pedig meghívhat egy logikai alkalmazást. Példa: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).

A Flow, a Logic Apps és a Functions közötti integráció folyamatosan javul. Amit létrehoz az egyik szolgáltatásban, használhatja a többiben.

## <a name="next-steps"></a>További lépések

Első lépésként hozza létre az első folyamatát, logikai alkalmazását vagy függvényalkalmazását. Kattintson a következő hivatkozások bármelyikére:

* [A Microsoft Flow használatának első lépései](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)

Az integrációs szolgáltatások további információit a következő hivatkozások használatával érhetők el:

* [Christopher Anderson: Leveraging Azure Functions & Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Az Azure Functions és az Azure App Service használata integrációs forgatókönyvekhez)
* [Charles Lamanna: Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integráció egyszerűen)
* [Logic Apps élő webszórás](http://aka.ms/logicappslive)
* [Microsoft Flow – Gyakori kérdések](https://flow.microsoft.com/documentation/frequently-asked-questions/)
