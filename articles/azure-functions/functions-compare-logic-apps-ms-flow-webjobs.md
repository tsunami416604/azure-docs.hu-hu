---
title: Integrációs és automatizálási platform lehetőségei az Azure-ban
description: 'Az integrációs feladatokhoz optimalizált Microsoft Cloud Services összehasonlítása: Microsoft Flow, Logic Apps, functions és webjobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: bd9f3bfe1578b632707382cfe422f19514e7ce48
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988107"
---
> [!NOTE]
> A Microsoft Flow mostantól automatizálható. További információt [ebben a blogban](https://aka.ms/flow-now-pa) talál.
> 
> A rendszer frissíti a tartalmat, hogy tükrözze a következő napokon a márkaépítési változást.
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Válassza ki a megfelelő integrációs és automatizálási szolgáltatásokat az Azure-ban

Ez a cikk összehasonlítja az alábbi Microsoft-felhőszolgáltatásokat:

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Mindegyik szolgáltatás megoldhatja az integrációs problémákat és automatizálhatja az üzleti folyamatokat. Mindegyik képes bemenet, műveletek, feltételek és kimenet meghatározására. Futtathatók ütemezve vagy eseményindítóval is. Az egyes szolgáltatások egyedi előnyökkel rendelkeznek, és ez a cikk ismerteti a különbségeket. 

Ha a Azure Functions és az egyéb Azure számítási lehetőségek közötti általánosabb összehasonlítást keres, tekintse meg az [Azure számítási szolgáltatás kiválasztásának feltételeit](/azure/architecture/guide/technology-choices/compute-comparison) és [egy Azure-beli számítási lehetőség kiválasztását a-szolgáltatásokhoz](/azure/architecture/microservices/design/compute-options).

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>A Microsoft Flow és az Azure Logic Apps összehasonlítása

A Microsoft Flow és Logic Apps mind a *Designer-First* integrációs szolgáltatások, amelyek munkafolyamatokat hozhatnak létre. Mindkét szolgáltatás integrálható különféle SaaS- és vállalati alkalmazásokkal. 

A Microsoft Flow Logic Appsra épül. Ugyanazt a munkafolyamat-tervezőt és ugyanazokat az [összekötőket](../connectors/apis-list.md)használják. 

Microsoft Flow bármely irodai feldolgozó számára lehetővé teszi az egyszerű integrációk (például a SharePoint-dokumentumtár jóváhagyási folyamatának) elvégzését anélkül, hogy a fejlesztőknek vagy az IT-nak kellene lennie. A Logic Apps a speciális integrációkat (például B2B folyamatokat) is lehetővé teheti, ahol nagyvállalati szintű Azure DevOps és biztonsági gyakorlatok szükségesek. Az üzleti munkafolyamatokra jellemző, hogy idővel egyre összetettebbé válnak. Ennek megfelelően először egy folyamat indul el, majd szükség szerint konvertálhatja egy logikai alkalmazásba.

Az alábbi táblázat segítségével meghatározhatja, hogy Microsoft Flow vagy Logic Apps a legmegfelelőbb egy adott integrációhoz:

|  | Microsoft Flow | Logic Apps |
| --- | --- | --- |
| **Felhasználók** |Irodai dolgozók, üzleti felhasználók, SharePoint-adminisztrátorok |Professzionális integrátorok és fejlesztők, informatikai szakemberek |
| **Forgatókönyvek** |Önkiszolgáló |Speciális integrációk |
| **Kialakítási eszköz** |Böngészőbeli és mobilalkalmazás, kizárólag felhasználói felület |Böngészőbeli, [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [Kódnézet](../logic-apps/logic-apps-author-definitions.md) elérhető |
| **Alkalmazások életciklus-felügyelete (ALM)** |Tervezés és tesztelés nem éles környezetekben, előléptetés éles környezetben, ha készen áll |Azure DevOps: verziókövetés, tesztelés, támogatás, automatizálás és kezelhetőség [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| **Rendszergazdai felület** |Microsoft Flow környezetek és adatvesztés-megelőzési (DLP) szabályzatok kezelése, licencelés nyomon követése: [Microsoft flow felügyeleti központ](https://admin.flow.microsoft.com) |Erőforráscsoportok, kapcsolatok, hozzáférés-kezelés és naplózás kezelése: [Azure Portal](https://portal.azure.com) |
| **Biztonság** |Office 365 biztonsági és megfelelőségi naplók, DLP, inaktív adatok [titkosítása](https://wikipedia.org/wiki/Data_at_rest#Encryption) bizalmas adatokhoz |Az Azure biztonsági garanciája: [Azure Security](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [naplók](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Az Azure Functions és az Azure Logic Apps összehasonlítása

A Functions és a Logic Apps egy Azure-szolgáltatás, melyek kiszolgáló nélküli feladatokhoz nyújtanak támogatást. Azure Functions egy kiszolgáló nélküli számítási szolgáltatás, míg a Azure Logic Apps kiszolgáló nélküli munkafolyamatokat biztosít. Mindkettő összetett előkészítéseketis létrehozhat. A vezénylés a függvények vagy lépések – a Logic Appsben ezeket *műveleteknek* nevezzük – egy olyan készlete, amelynek a végrehajtásával összetett feladatokat tud elvégezni. Ha például egy köteget szeretne feldolgozni, egy függvény több példánya is végrehajtható párhuzamosan, várjon, amíg az összes példány befejeződik, majd hajtson végre egy olyan függvényt, amely kiszámítja az Összesítés eredményét.

Az Azure Functions esetében a vezénylések fejlesztéséhez kódot kell írnia a [Durable Functions bővítmény](durable/durable-functions-overview.md) segítségével. A Logic Appsben a vezényléseket grafikus felhasználói felülettel vagy konfigurációs fájlok szerkesztésével tudja létrehozni.

Ezeket a szolgáltatásokat használhatja vegyesen a vezénylések létrehozásához, vagyis hívhat meg függvényeket a logikai alkalmazásokból, illetve hívhat meg logikai alkalmazásokat a függvényekből. A vezénylések építésének módját a szolgáltatások képességei és a személyes preferenciái szerint választhatja ki. Az alábbi táblázat a következő főbb különbségeket sorolja fel:

|  | Tartós függvények | Logic Apps |
| --- | --- | --- |
| **Fejlesztés** | Kódközpontú (imperatív) | Tervezőközpontú (deklaratív) |
| **Kapcsolatok** | [Számos beépített kötési típus](functions-triggers-bindings.md#supported-bindings), egyéni kötések létrehozása kód írásával | [Nagyszámú beépített összekötők](../connectors/apis-list.md), [Enterprise Integration Pack csomag B2B-forgatókönyvekhez](../logic-apps/logic-apps-enterprise-integration-overview.md), [egyéni összekötők létrehozása](../logic-apps/custom-connector-overview.md) |
| **Műveletek** | Minden tevékenység egy Azure-függvény; kód írásával hozhat létre tevékenységfüggvényeket |[Használatra kész műveletek széles választéka](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Monitorozás** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure monitor naplók](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| **Felügyeleti** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| **Végrehajtási környezet** | [Helyileg](functions-runtime-overview.md) vagy a felhőben is futtatható | Csak a felhőben fut|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>A Functions és a WebJobs összehasonlítása

Ugyanúgy, mint az Azure Functions, az Azure App Service WebJobs a WebJobs SDK-val is olyan integrációs szolgáltatás, ahol a *kód* a kiindulási pont, és mindkettőt fejlesztők részére tervezték. Mindkettő az [Azure App Service](../app-service/overview.md) szolgáltatásra épül, és támogatják többek között a következő funkciókat: [verziókövetés integrálása](../app-service/deploy-continuous-deployment.md), [hitelesítés](../app-service/overview-authentication-authorization.md) és [Application Insights-integrációs monitorozás](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs és WebJobs SDK

A App Service *webjobs* funkciójának használatával parancsfájlt vagy kódot futtathat egy app Service webalkalmazás környezetében. A *WebJobs SDK* egy, a WebJobshoz tervezett keretrendszer, amely leegyszerűsíti az Azure-szolgáltatások válaszadására írt kódokat. Előfordulhat például, hogy egy miniatűr rendszerkép létrehozásával válaszol egy képblob létrehozására az Azure Storage-ban. A WebJobs SDK .NET-konzolalkalmazásként fut, amelyet üzembe helyezhet egy WebJobon. 

A WebJobs és a WebJobs SDK együtt működnek a leghatékonyabban, de használhatja az egyiket a másik nélkül is. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben. A WebJobs SDK-konzolalkalmazások bárhol futtathatók, ahol konzolalkalmazások futtathatók, például a helyszíni kiszolgálókon.

### <a name="comparison-table"></a>Összehasonlító táblázat

Az Azure Functions a WebJobs SDK-ra épül, így sok, más Azure-szolgáltatásokkal közös eseményindítóval és kapcsolattal rendelkezik. Az alábbiakban néhány szempontot figyelembe kell venni, amikor a webjobs SDK-val Azure Functions és webjobs-feladatok közül választ:

|  | Functions | WebJobs WebJobs SDK-val |
| --- | --- | --- |
|[Kiszolgáló nélküli alkalmazásmodell](https://azure.microsoft.com/solutions/serverless/)[automatikus skálázással](functions-scale.md#how-the-consumption-and-premium-plans-work)|✔||
|[Fejlesztése és tesztelés böngészőben](functions-create-first-azure-function.md) |✔||
|[Használatalapú fizetés](functions-scale.md#consumption-plan)|✔||
|[Integráció a Logic Apps szolgáltatással](functions-twitter-email.md)|✔||
| Kiváltó események |[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Időzítő](functions-bindings-timer.md)<br>[Azure Storage üzenetsorok és blobok](functions-bindings-storage-blob.md)<br>[Azure Service Bus – üzenetsorok és témakörök](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Fájlrendszer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Támogatott nyelvek  |C#<br>F#<br>JavaScript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|Csomagkezelők|NPM és NuGet|NuGet<sup>2</sup>|

<sup>1</sup> a webjobs (a webjobs SDK nélkül) C#támogatja a következőt: Java, JavaScript, bash,. cmd,. bat, PowerShell, php, írógéppel, Python és sok más. Ez nem egy átfogó lista. A WebJobs képes bármilyen programot vagy szkriptet futtatni, amely képes futni az App Service tesztkörnyezetben.

<sup>2</sup> a webjobs (a webjobs SDK nélkül) támogatja a NPM és a NuGet.

### <a name="summary"></a>Összefoglalás

Azure Functions nagyobb fejlesztői hatékonyságot biztosít, mint Azure App Service webjobs. Emellett több lehetőséget is kínál a programozási nyelvek, a fejlesztői környezetek, az Azure-szolgáltatások integrálására és a díjszabásra. A legtöbb forgatókönyvhöz ez a legjobb választás.

Az alábbiakban két olyan forgatókönyvet láthat, amelyekhez a WebJobs lehet a legjobb választás:

* Az eseményekre figyelő kódok nagyobb mértékű felügyeletére van szüksége – `JobHost` objektum. A Functions korlátozott számú módszert biztosít a `JobHost`-viselkedés a [host.json](functions-host-json.md) fájlban való testreszabására. Néha olyan műveletekre lehet szüksége, amelyek nem adhatók meg egy JSON-fájlbeli sztringben. Például csak a WebJobs SDK teszi lehetővé egyéni újrapróbálkozási szabályzat konfigurálását az Azure Storage-ban.
* Rendelkezik egy App Service alkalmazással, amelyhez kódrészleteket szeretne futtatni, és együtt szeretné kezelni őket ugyanabban az Azure DevOps-környezetben.

Minden egyéb olyan forgatókönyv esetén, ahol kódrészletek futtatásával kíván Azure- vagy külső szolgáltatásokat integrálni, válassza az Azure Functionst a WebJobs és WebJobs SDK helyett.

<a name="together"></a>

## <a name="microsoft-flow-logic-apps-functions-and-webjobs-together"></a>Microsoft Flow, Logic Apps, functions és webjobs együtt

Ezen szolgáltatások közül csak egyet kell választania. Integrálva vannak egymással, valamint külső szolgáltatásokkal is.

Egy folyamat meghívhat egy logikai alkalmazást. Egy logikai alkalmazás meghívhat egy függvényt, egy függvény pedig meghívhat egy logikai alkalmazást. Példa: [Az Azure Logic Apps szolgáltatással integrálható függvények létrehozása](functions-twitter-email.md).

A Microsoft Flow, a Logic Apps és a függvények közötti integráció továbbra is az idő múlásával javul. Amit létrehoz az egyik szolgáltatásban, használhatja a többiben.

Az integrációs szolgáltatásokkal kapcsolatos további információk az alábbi hivatkozásokon keresztül olvashatók:

* [Christopher Anderson: Leveraging Azure Functions & Azure App Service for integration scenarios](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/) (Az Azure Functions és az Azure App Service használata integrációs forgatókönyvekhez)
* [Charles Lamanna: Integrations Made Simple](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/) (Integráció egyszerűen)
* [Élő webes közvetítés Logic Apps](https://aka.ms/logicappslive)
* [Microsoft Flow gyakori kérdések](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Következő lépések

Első lépésként hozza létre az első folyamatát, logikai alkalmazását vagy függvényalkalmazását. Válassza ki a következő hivatkozások bármelyikét:

* [A Microsoft Flow használatának első lépései](/power-automate/getting-started)
* [Logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)
