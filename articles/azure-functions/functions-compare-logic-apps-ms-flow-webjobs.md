---
title: Mik azok a Microsoft Flow, Logic Apps, Functions és webjobs-feladatok? – Azure
description: 'A Microsoft cloud services integrációs feladatokhoz optimalizált összehasonlítása: Microsoft Flow, Logic Apps, Functions és webjobs-feladatok.'
services: functions, logic-apps
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: microsoft flow, folyamat, logic apps, azure functions, függvények, azure webjobs, webjobs, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: overview
ms.date: 04/09/2018
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: de83cc80d64fb1862f1fdec32b704d0948ef9fc8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820518"
---
# <a name="what-are-microsoft-flow-logic-apps-functions-and-webjobs"></a>Mik azok a Microsoft Flow, Logic Apps, Functions és webjobs-feladatok?

Ez a cikk összehasonlítja az alábbi Microsoft-felhőszolgáltatásokat:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Mindegyik szolgáltatás megoldhatja az integrációs problémákat és automatizálhatja az üzleti folyamatokat. Mindegyik képes bemenet, műveletek, feltételek és kimenet meghatározására. Futtathatók ütemezve vagy eseményindítóval is. Mindegyikük rendelkezik egyedi előnyökkel, és ez a cikk ismerteti a különbségeket.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>A Microsoft Flow és az Azure Logic Apps összehasonlítása

Microsoft Flow és Logic Apps *tervezőközpontú* integrációs szolgáltatások, amelyek munkafolyamatokat hozhat létre. Mindkét szolgáltatás integrálható különféle SaaS- és vállalati alkalmazásokkal. 

Microsoft Flow a Logic appsre épül. Oszthatnak meg ugyanazzal a munkafolyamat-tervezővel és azonos [összekötők](../connectors/apis-list.md). 

Microsoft Flow segítségével bármely irodai dolgozó képes végrehajtani egyszerű integrációkat (például egy SharePoint-dokumentumtár jóváhagyási folyamata) anélkül fejlesztőkhöz vagy informatikai. A Logic Apps is engedélyezheti a speciális integrációkat (például B2B folyamatokat), amelyekben vállalati szintű Azure DevOps és biztonsági gyakorlatok szükségesek. Az üzleti munkafolyamatokra jellemző, hogy idővel egyre összetettebbé válnak. Ennek megfelelően kezdhet egy folyamattal kezdődnie, és majd átalakíthatja logikai alkalmazás igény szerint.

Az alábbi táblázat segítségével meghatározhatja a Microsoft Flow vagy a Logic Apps-e egy adott integráció esetében ajánlott:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| Felhasználók |Irodai dolgozók, üzleti felhasználók, SharePoint-adminisztrátorok |Professzionális integrátorok és fejlesztők, informatikai szakemberek |
| Forgatókönyvek |Önkiszolgáló |Speciális integrációk |
| Tervezési eszköz |Böngészőbeli és mobilalkalmazás, kizárólag felhasználói felület |Böngészőbeli, [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Kódnézet](../logic-apps/logic-apps-author-definitions.md) elérhető |
| Alkalmazáséletciklus-kezelés (ALM) |Tervezés és tesztelés nem éles környezetekben, az előléptetés éles üzemhez, ha készen áll |Az Azure DevOps: forrás-vezérlő, a tesztelés, támogatás, automatizálási és kezelhetősége [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| A rendszergazda teendői |Microsoft Flow-környezet és adatveszteség-megelőzési (DLP) szabályzatok kezelése, licencek nyomon követése: [Microsoft Flow Admin Center](https://admin.flow.microsoft.com) |Erőforráscsoportok, kapcsolatok, hozzáférés-kezelés és naplózás kezelése: [Azure Portal](https://portal.azure.com) |
| Biztonság |Az Office 365 biztonsági és megfelelőségi auditnaplói, DLP, [titkosítás inaktív állapotban](https://wikipedia.org/wiki/Data_at_rest#Encryption) bizalmas adatok |Azure által garantált biztonság: [Az Azure security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [az Azure Security Center](https://azure.microsoft.com/services/security-center/), [naplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Az Azure Functions és az Azure Logic Apps összehasonlítása

A Functions és a Logic Apps egy Azure-szolgáltatás, melyek kiszolgáló nélküli feladatokhoz nyújtanak támogatást. Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, mivel az Azure Logic Apps biztosít a kiszolgáló nélküli munkafolyamatokat. Mindkét hozhat létre komplex *vezénylések*. A vezénylés a függvények vagy lépések – a Logic Appsben ezeket *műveleteknek* nevezzük – egy olyan készlete, amelynek a végrehajtásával összetett feladatokat tud elvégezni. Például rendelések feldolgozásához, akkor előfordulhat, hogy végre egy függvény több példányát párhuzamosan, várjon, amíg az összes példány futásának befejezését és majd végrehajthat egy függvényt, amely kiszámítja az összesített eredményt.

Az Azure Functions esetében a vezénylések fejlesztéséhez kódot kell írnia a [Durable Functions bővítmény](durable/durable-functions-concepts.md) segítségével. A Logic Appsben a vezényléseket grafikus felhasználói felülettel vagy konfigurációs fájlok szerkesztésével tudja létrehozni.

Ezeket a szolgáltatásokat használhatja vegyesen a vezénylések létrehozásához, vagyis hívhat meg függvényeket a logikai alkalmazásokból, illetve hívhat meg logikai alkalmazásokat a függvényekből. A vezénylések építésének módját a szolgáltatások képességei és a személyes preferenciái szerint választhatja ki. Az alábbi táblázatban áttekintheti a szolgáltatások közötti fő különbségeket:
 
|  | Tartós függvények | Logic Apps |
| --- | --- | --- |
| Fejlesztés | Kódközpontú (imperatív) | Tervezőközpontú (deklaratív) |
| Kapcsolatok | [Számos beépített kötési típus](functions-triggers-bindings.md#supported-bindings), egyéni kötések létrehozása kód írásával | [Nagyszámú beépített összekötők](../connectors/apis-list.md), [Enterprise Integration Pack csomag B2B-forgatókönyvekhez](../logic-apps/logic-apps-enterprise-integration-overview.md), [egyéni összekötők létrehozása](../logic-apps/custom-connector-overview.md) |
| Műveletek | Minden tevékenység egy Azure-függvény; kód írásával hozhat létre tevékenységfüggvényeket |[Használatra kész műveletek széles választéka](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Figyelés | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Az Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor-naplók](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| Kezelés | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Végrehajtási környezet | Futtatható [helyileg](functions-runtime-overview.md) vagy a felhőben | Csak a felhőben|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>A Functions és a WebJobs összehasonlítása

Ugyanúgy, mint az Azure Functions, az Azure App Service WebJobs a WebJobs SDK-val is olyan integrációs szolgáltatás, ahol a *kód* a kiindulási pont, és mindkettőt fejlesztők részére tervezték. Mindkettő az [Azure App Service](../app-service/overview.md) szolgáltatásra épül, és támogatják többek között a következő funkciókat: [verziókövetés integrálása](../app-service/deploy-continuous-deployment.md), [hitelesítés](../app-service/overview-authentication-authorization.md) és [Application Insights-integrációs monitorozás](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs és WebJobs SDK

Használhatja a *WebJobs* környezetben történő futtatásra szkriptek vagy kódok az App Service-webalkalmazás App Service szolgáltatásával. A *WebJobs SDK* egy, a WebJobshoz tervezett keretrendszer, amely leegyszerűsíti az Azure-szolgáltatások válaszadására írt kódokat. Például, előfordulhat, hogy válaszolhat az Azure Storage-ban egy kép BLOB miniatűr létrehozásával. A WebJobs SDK .NET-konzolalkalmazásként fut, amelyet üzembe helyezhet egy WebJobon. 

A WebJobs és a WebJobs SDK együtt működnek a leghatékonyabban, de használhatja az egyiket a másik nélkül is. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben. A WebJobs SDK-konzolalkalmazások bárhol futtathatók, ahol konzolalkalmazások futtathatók, például a helyszíni kiszolgálókon.

### <a name="comparison-table"></a>Összehasonlító táblázat

Az Azure Functions a WebJobs SDK-ra épül, így sok, más Azure-szolgáltatásokkal közös eseményindítóval és kapcsolattal rendelkezik. Íme néhány tényező, kiválasztásakor vegye figyelembe, hogy között az Azure Functions és WebJobs a WebJobs SDK-val:

|  | Functions | WebJobs WebJobs SDK-val |
| --- | --- | --- |
|[Kiszolgáló nélküli alkalmazásmodell](https://azure.microsoft.com/solutions/serverless/) [automatikus skálázással](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Fejlesztése és tesztelés böngészőben](functions-create-first-azure-function.md) |✔||
|[Használatalapú fizetés](functions-scale.md#consumption-plan)|✔||
|[Integráció a Logic Apps szolgáltatással](functions-twitter-email.md)|✔||
| Kiváltó események |[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Fájlrendszer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Támogatott nyelvek  |C#<br>F#<br>JavaScript<br>Java (előzetes verzió) |C#<sup>1</sup>|
|Csomagkezelők|NPM és NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (a WebJobs SDK nélkül) támogatja a C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python és több. Ez a teljes listát nem. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben.

<sup>2</sup> WebJobs (a WebJobs SDK nélkül) támogatja az NPM és NuGet.

### <a name="summary"></a>Összegzés

Az Azure Functions kínál, mint az Azure App Service WebJobs további fejlesztői hatékonyságot biztosítanak. Programozási nyelvek, a fejlesztési környezetek, az Azure-szolgáltatások integrálása és díjszabás további beállításokat is kínál. A legtöbb forgatókönyvhöz ez a legjobb választás.

Az alábbiakban két olyan forgatókönyvet láthat, amelyekhez a WebJobs lehet a legjobb választás:

* Az eseményekre figyelő kódok nagyobb mértékű felügyeletére van szüksége – `JobHost` objektum. A Functions korlátozott számú módszert biztosít a `JobHost`-viselkedés a [host.json](functions-host-json.md) fájlban való testreszabására. Néha olyan műveletekre lehet szüksége, amelyek nem adhatók meg egy JSON-fájlbeli sztringben. Például csak a WebJobs SDK teszi lehetővé egyéni újrapróbálkozási szabályzat konfigurálását az Azure Storage-ban.
* Egy App Service-alkalmazás, amelynek a kódrészleteket futtatni kívánt, és együtt kezelheti azokat ugyanabban az Azure DevOps-környezetben szeretné.

Minden egyéb olyan forgatókönyv esetén, ahol kódrészletek futtatásával kíván Azure- vagy külső szolgáltatásokat integrálni, válassza az Azure Functionst a WebJobs és WebJobs SDK helyett.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions és webjobs-feladatok együtt

Nem kell csupán egyet kiválasztania ezen szolgáltatások. Ezek integrálható egymással is, mint a külső szolgáltatások.

Egy folyamat meghívhat egy logikai alkalmazást. Egy logikai alkalmazás meghívhat egy függvényt, egy függvény pedig meghívhat egy logikai alkalmazást. Példa: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).

A Microsoft Flow, Logic Apps és Functions közötti integráció továbbra is javul. Amit létrehoz az egyik szolgáltatásban, használhatja a többiben.

Az integrációs szolgáltatások további információkat szerezhet a a következő hivatkozásokra:

* [Christopher Anderson: Leveraging Azure Functions & Azure App Service for integration scenarios](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Az Azure Functions és az Azure App Service használata integrációs forgatókönyvekhez)
* [Charles Lamanna: Integrations Made Simple](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integráció egyszerűen)
* [Logic Apps élő internetes adás](https://aka.ms/logicappslive)
* [Gyakori kérdések a Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

## <a name="next-steps"></a>További lépések

Első lépésként hozza létre az első folyamatát, logikai alkalmazását vagy függvényalkalmazását. Válassza ki a következő hivatkozások bármelyikére:

* [A Microsoft Flow használatának első lépései](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)
