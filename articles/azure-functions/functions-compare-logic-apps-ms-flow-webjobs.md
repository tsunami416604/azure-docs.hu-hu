---
title: Integrációs és automatizálási platformlehetőségek az Azure-ban
description: 'Hasonlítsa össze az integrációs feladatokra optimalizált Microsoft felhőszolgáltatásokat: Microsoft Flow, Logic Apps, Functions és WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: bd9f3bfe1578b632707382cfe422f19514e7ce48
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241317"
---
> [!NOTE]
> A Microsoft Flow új neve Power Automate. További információt [ebben a blogban](https://aka.ms/flow-now-pa) talál.
> 
> A rendszer az elkövetkező napokban frissíti a tartalmat a védjegyezési változásnak megfelelően.
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Válassza ki a megfelelő integrációs és automatizálási szolgáltatásokat az Azure-ban

Ez a cikk összehasonlítja az alábbi Microsoft-felhőszolgáltatásokat:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Mindegyik szolgáltatás megoldhatja az integrációs problémákat és automatizálhatja az üzleti folyamatokat. Mindegyik képes bemenet, műveletek, feltételek és kimenet meghatározására. Futtathatók ütemezve vagy eseményindítóval is. Minden szolgáltatásnak egyedi előnyei vannak, és ez a cikk ismerteti a különbségeket. 

Ha általánosabb összehasonlítást szeretne végezni az Azure Functions és más Azure-számítási lehetőségek között, olvassa el [az Azure számítási szolgáltatás kiválasztásának feltételei](/azure/architecture/guide/technology-choices/compute-comparison) és az Azure számítási lehetőség kiválasztása [mikroszolgáltatásokhoz](/azure/architecture/microservices/design/compute-options)lehetőséget.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>A Microsoft Flow és az Azure Logic Apps összehasonlítása

A Microsoft Flow és a Logic Apps egyaránt *tervező-első* integrációs szolgáltatások, amelyek munkafolyamatokat hozhatnak létre. Mindkét szolgáltatás integrálható különféle SaaS- és vállalati alkalmazásokkal. 

A Microsoft Flow a Logic Apps-re épül. Ugyanaz a munkafolyamat-tervező és ugyanazok az [összekötők.](../connectors/apis-list.md) 

A Microsoft Flow minden irodai dolgozót képessé tesz egyszerű integrációk (például egy SharePoint-dokumentumtár jóváhagyási folyamata) végrehajtására anélkül, hogy fejlesztőkön vagy informatikai rendszereken keresztül haladna. A Logic Apps speciális integrációkat is engedélyezhet (például B2B-folyamatokat), ahol nagyvállalati szintű Azure DevOps-ra és biztonsági eljárásokra van szükség. Az üzleti munkafolyamatokra jellemző, hogy idővel egyre összetettebbé válnak. Ennek megfelelően először egy folyamattal kezdheti, majd szükség szerint átalakíthatja egy logikai alkalmazássá.

Az alábbi táblázat segítségével meghatározhatja, hogy a Microsoft Flow vagy a Logic Apps a legjobb-e egy adott integrációhoz:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| **Felhasználók** |Irodai dolgozók, üzleti felhasználók, SharePoint-adminisztrátorok |Professzionális integrátorok és fejlesztők, informatikai szakemberek |
| **Forgatókönyvek** |Önkiszolgáló |Speciális integrációk |
| **Tervező eszköz** |Böngészőbeli és mobilalkalmazás, kizárólag felhasználói felület |Böngészőbeli, [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Kódnézet](../logic-apps/logic-apps-author-definitions.md) elérhető |
| **Alkalmazás életciklus-kezelése (ALM)** |Tervezés és tesztelés nem éles környezetben, előmozdítás a termeléshez, ha készen áll |Azure DevOps: forrásvezérlés, tesztelés, támogatás, automatizálás és kezelhetőség az [Azure Resource Managerben](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| **Rendszergazdai élmény** |A Microsoft Flow-környezetek és az adatveszteség-megelőzési (DLP) házirendek kezelése, nyomon követése licencelés: [Microsoft Flow Felügyeleti központ](https://admin.flow.microsoft.com) |Erőforráscsoportok, kapcsolatok, hozzáférés-kezelés és naplózás kezelése: [Azure Portal](https://portal.azure.com) |
| **Biztonság** |Office 365 biztonsági és megfelelőségi naplózási naplói, DLP, [inaktív titkosítás](https://wikipedia.org/wiki/Data_at_rest#Encryption) bizalmas adatok esetén |Az Azure biztonsági garanciája: [Azure security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), Azure [Security Center](https://azure.microsoft.com/services/security-center/), [naplónaplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Az Azure Functions és az Azure Logic Apps összehasonlítása

A Functions és a Logic Apps egy Azure-szolgáltatás, melyek kiszolgáló nélküli feladatokhoz nyújtanak támogatást. Az Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, míg az Azure Logic Apps kiszolgáló nélküli munkafolyamatokat biztosít. Mindkettő összetett *vezényléseket*hozhat létre. A vezénylés a függvények vagy lépések – a Logic Appsben ezeket *műveleteknek* nevezzük – egy olyan készlete, amelynek a végrehajtásával összetett feladatokat tud elvégezni. Például a rendelések kötegének feldolgozásához egy függvény több példányát is végrehajthatja párhuzamosan, megvárhatja az összes példány befejezését, majd végrehajthat egy függvényt, amely kiszámítja az eredményt az összesítésen.

Az Azure Functions esetében a vezénylések fejlesztéséhez kódot kell írnia a [Durable Functions bővítmény](durable/durable-functions-overview.md) segítségével. A Logic Appsben a vezényléseket grafikus felhasználói felülettel vagy konfigurációs fájlok szerkesztésével tudja létrehozni.

Ezeket a szolgáltatásokat használhatja vegyesen a vezénylések létrehozásához, vagyis hívhat meg függvényeket a logikai alkalmazásokból, illetve hívhat meg logikai alkalmazásokat a függvényekből. A vezénylések építésének módját a szolgáltatások képességei és a személyes preferenciái szerint választhatja ki. Az alábbi táblázat a legfontosabb különbségeket sorolja fel:

|  | Tartós függvények | Logic Apps |
| --- | --- | --- |
| **Fejlesztés** | Kódközpontú (imperatív) | Tervezőközpontú (deklaratív) |
| **Kapcsolat** | [Számos beépített kötési típus](functions-triggers-bindings.md#supported-bindings), egyéni kötések létrehozása kód írásával | [Nagyszámú beépített összekötők](../connectors/apis-list.md), [Enterprise Integration Pack csomag B2B-forgatókönyvekhez](../logic-apps/logic-apps-enterprise-integration-overview.md), [egyéni összekötők létrehozása](../logic-apps/custom-connector-overview.md) |
| **Műveletek** | Minden tevékenység egy Azure-függvény; kód írásával hozhat létre tevékenységfüggvényeket |[Használatra kész műveletek széles választéka](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Megfigyelő** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor naplók](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| **Felügyelet** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| **Végrehajtási környezet** | Futtatható [helyileg](functions-runtime-overview.md) vagy a felhőben | Csak felhőben futtatható|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>A Functions és a WebJobs összehasonlítása

Ugyanúgy, mint az Azure Functions, az Azure App Service WebJobs a WebJobs SDK-val is olyan integrációs szolgáltatás, ahol a *kód* a kiindulási pont, és mindkettőt fejlesztők részére tervezték. Mindkettő az [Azure App Service](../app-service/overview.md) szolgáltatásra épül, és támogatják többek között a következő funkciókat: [verziókövetés integrálása](../app-service/deploy-continuous-deployment.md), [hitelesítés](../app-service/overview-authentication-authorization.md) és [Application Insights-integrációs monitorozás](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs és WebJobs SDK

Az App Service *WebJobs* szolgáltatásával parancsfájlt vagy kódot futtathat egy App Service-webalkalmazás környezetében. A *WebJobs SDK* egy, a WebJobshoz tervezett keretrendszer, amely leegyszerűsíti az Azure-szolgáltatások válaszadására írt kódokat. Például előfordulhat, hogy egy képblob létrehozása az Azure Storage-ban egy miniatűr kép létrehozásával válaszolhat. A WebJobs SDK .NET-konzolalkalmazásként fut, amelyet üzembe helyezhet egy WebJobon. 

A WebJobs és a WebJobs SDK együtt működnek a leghatékonyabban, de használhatja az egyiket a másik nélkül is. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben. A WebJobs SDK-konzolalkalmazások bárhol futtathatók, ahol konzolalkalmazások futtathatók, például a helyszíni kiszolgálókon.

### <a name="comparison-table"></a>Összehasonlító táblázat

Az Azure Functions a WebJobs SDK-ra épül, így sok, más Azure-szolgáltatásokkal közös eseményindítóval és kapcsolattal rendelkezik. Az alábbiakban néhány tényezőt kell figyelembe venni, amikor az Azure Functions és a WebJobs sdk-vel rendelkező WebJobs-feladatok közül választ:

|  | Functions | WebJobs WebJobs SDK-val |
| --- | --- | --- |
|[Kiszolgáló nélküli alkalmazásmodell](https://azure.microsoft.com/solutions/serverless/)[automatikus skálázással](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Fejlesztése és tesztelés böngészőben](functions-create-first-azure-function.md) |✔||
|[Használatalapú fizetés](functions-scale.md#consumption-plan)|✔||
|[Integráció a Logic Apps szolgáltatással](functions-twitter-email.md)|✔||
| Kiváltó események |[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Fájlrendszer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Támogatott nyelvek  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|Csomagkezelők|NPM és NuGet|NuGet<sup>2</sup>|

<sup>1</sup> A WebJobs (a WebJobs SDK nélkül) támogatja a C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python és így tovább. Ez nem egy átfogó lista. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben.

<sup>2</sup> A WebJobs (a WebJobs SDK nélkül) támogatja az NPM-et és a NuGet-et.

### <a name="summary"></a>Összefoglalás

Az Azure Functions nagyobb fejlesztői hatékonyságot kínál, mint az Azure App Service WebJobs. Emellett további lehetőségeket kínál a programozási nyelvekhez, a fejlesztői környezetekhez, az Azure-szolgáltatások integrációjához és a díjszabáshoz. A legtöbb forgatókönyvhöz ez a legjobb választás.

Az alábbiakban két olyan forgatókönyvet láthat, amelyekhez a WebJobs lehet a legjobb választás:

* Az eseményekre figyelő kódok nagyobb mértékű felügyeletére van szüksége – `JobHost` objektum. A Functions korlátozott számú módszert biztosít a `JobHost`-viselkedés a [host.json](functions-host-json.md) fájlban való testreszabására. Néha olyan műveletekre lehet szüksége, amelyek nem adhatók meg egy JSON-fájlbeli sztringben. Például csak a WebJobs SDK teszi lehetővé egyéni újrapróbálkozási szabályzat konfigurálását az Azure Storage-ban.
* Van egy App Service-alkalmazás, amelyhez kódrészleteket szeretne futtatni, és szeretné kezelni őket együtt ugyanabban az Azure DevOps-környezetben.

Minden egyéb olyan forgatókönyv esetén, ahol kódrészletek futtatásával kíván Azure- vagy külső szolgáltatásokat integrálni, válassza az Azure Functionst a WebJobs és WebJobs SDK helyett.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, Functions és WebJobs együttesen

Nem kell választani csak egy ilyen szolgáltatást. Integrálódnak egymással, valamint a külső szolgáltatásokkal.

Egy folyamat meghívhat egy logikai alkalmazást. Egy logikai alkalmazás meghívhat egy függvényt, egy függvény pedig meghívhat egy logikai alkalmazást. Példa: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).

A Microsoft Flow, a Logic Apps és a Functions integrációja az idő múlásával tovább javul. Amit létrehoz az egyik szolgáltatásban, használhatja a többiben.

Az integrációs szolgáltatásokról az alábbi hivatkozások használatával kaphat további információkat:

* [Christopher Anderson: Leveraging Azure Functions & Azure App Service for integration scenarios](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Az Azure Functions és az Azure App Service használata integrációs forgatókönyvekhez)
* [Charles Lamanna: Integrations Made Simple](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integráció egyszerűen)
* [Logic Apps élő adás](https://aka.ms/logicappslive)
* [Gyakori kérdések a Microsoft Flow-val kapcsolatban](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>További lépések

Első lépésként hozza létre az első folyamatát, logikai alkalmazását vagy függvényalkalmazását. Válasszon az alábbi hivatkozások közül:

* [A Microsoft Flow használatának első lépései](/power-automate/getting-started)
* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)
