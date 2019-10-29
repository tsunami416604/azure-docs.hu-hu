---
title: Példák & gyakori forgatókönyvekre – Azure Logic Apps
description: Példákat, gyakori forgatókönyveket, oktatóanyagokat és útmutatókat talál a Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: 9e245d1a0054d9de5b8b524416c8c92eb02353a7
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73025523"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Gyakori forgatókönyvek, példák, oktatóanyagok és útmutatók Azure Logic Apps

[Azure Logic apps](../logic-apps/logic-apps-overview.md) a különböző szolgáltatások összehangolása és integrálása révén [több száz használatra kész összekötőt](../connectors/apis-list.md)biztosít, amelyek a helyszíni SQL Server vagy az SAP és az Azure Cognitive Services között állnak rendelkezésre. A Logic Apps szolgáltatás "kiszolgáló nélküli", így nem kell aggódnia a méretezéssel vagy a példányokkal kapcsolatban. Mindössze annyit kell tennie, hogy meghatározza a munkafolyamatot egy triggerrel, valamint a munkafolyamat által végrehajtott műveleteket. Az alapul szolgáló platform kezeli a skálázást, a rendelkezésre állást és a teljesítményt. Logic Apps különösen hasznos olyan használati esetekhez és forgatókönyvekhez, ahol több műveletet kell összehangolni több rendszeren.

Ha többet szeretne megtudni a Azure Logic Apps által támogatott számos mintával és képességgel kapcsolatban, tekintse meg a gyakori példákat és forgatókönyveket.

## <a name="popular-starting-points-for-logic-app-workflows"></a>Népszerű kiindulási pontok a Logic app-munkafolyamatokhoz

Minden logikai alkalmazás egy [*triggerrel*](../logic-apps/logic-apps-overview.md#logic-app-concepts)kezdődik, és csak egy triggert indít el, amely elindítja a logikai alkalmazás munkafolyamatát, és az adott trigger részeként továbbítja az összes adatmennyiséget. Egyes összekötők eseményindítókat biztosítanak, amelyek a következő típusokból származnak:

* *Lekérdezési eseményindítók*: rendszeresen ellenőrzi a szolgáltatási végpontot az új adatértékekhez. Ha új adatok állnak rendelkezésre, az trigger létrehoz és futtat egy új munkafolyamat-példányt, amely az adatokat bemenetként adja meg.

* *Leküldéses eseményindítók*: figyeli az adatszolgáltatási végponton tárolt adatkéréseket, és megvárja, amíg egy adott esemény bekövetkezik. Ha az esemény történik, az eseményindító azonnal elindul, és egy új munkafolyamat-példányt hoz létre és futtat, amely bármely elérhető adatot használ bemenetként.

Íme néhány népszerű kiváltó példa:

* Lekérdezési

  * Az [ **ismétlődési** eseményindítóval](../connectors/connectors-native-recurrence.md) megadható a kezdő dátum és idő, valamint a logikai alkalmazás égetésének ismétlődése. Kiválaszthatja például a hét napjait és a nap időpontját a logikai alkalmazás aktiválásához. További információkért tekintse meg a következő témaköröket:

    * [Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezett és futtatása Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Oktatóanyag: az ütemezett forgalom ellenőrzését Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Az "e-mailek fogadása" trigger lehetővé teszi, hogy a logikai alkalmazás új e-maileket keressen a Logic Apps által támogatott bármely e-mail-szolgáltatótól, például az [Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md), a [Gmail](https://docs.microsoft.com/connectors/gmail/), a [Outlook.com](https://docs.microsoft.com/connectors/outlook/)stb. További információkért tekintse meg a következő témaköröket: 

    * [Oktatóanyag: levelezőlista-kérelmek kezelése Azure Logic Apps](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Oktatóanyag: az e-mailek és mellékletek kezelése a Azure Logic Appssal](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * A [ **http** -trigger](../connectors/connectors-native-http.md) lehetővé teszi, hogy a logikai alkalmazás a HTTP protokollon keresztül kommunikálva egy adott szolgáltatási végpontot keressen.
  
* Nyomja

  * A [ **kérelem** -trigger](../connectors/connectors-native-reqres.md) lehetővé teszi, hogy a logikai alkalmazás http-kérelmeket fogadjon, és valós időben válaszoljon az eseményekre valamilyen módon.

  * A [ **http-webhook** -trigger](../connectors/connectors-native-webhook.md) a szolgáltatáshoz tartozó *visszahívási URL-cím* regisztrálásával előfizet egy szolgáltatási végpontra. Így a szolgáltatás csak akkor értesíti az eseményindítót, ha a megadott esemény történik, így az eseményindítónak nem kell lekérdezni a szolgáltatást.

Miután értesítést kapott az új vagy egy eseményről, az eseményindító elindul, létrehoz egy új logikai alkalmazás-munkafolyamati példányt, és futtatja a munkafolyamatban lévő műveleteket. Az trigger összes adatait elérheti a munkafolyamaton belül. Az "on a New Tweet" trigger például átadja a tweet tartalmát a logikai alkalmazás futtatásának. A Azure Logic Apps használatának megkezdéséhez próbálja ki a következő rövid útmutatót:

* [Rövid útmutató: az első automatizált munkafolyamat létrehozása a Azure Logic Apps a Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Gyors útmutató: automatizált feladatok, folyamatok és munkafolyamatok létrehozása a Visual Studio használatával Azure Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gyors útmutató: automatizált logikai alkalmazások munkafolyamatainak létrehozása és kezelése a Visual Studio Code használatával](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="respond-to-triggers-and-extend-actions"></a>Reagálás eseményindítók és kibővítési műveletek

A nem közzétett összekötőket tartalmazó rendszerekhez és szolgáltatásokhoz a logikai alkalmazásokat is kiterjesztheti.

* [Egyéni eseményindítók vagy műveletek létrehozása](../logic-apps/logic-apps-create-api-app.md)
* [A hosszú ideig futó műveletek beállítása a munkafolyamat-futtatásokhoz](../logic-apps/logic-apps-create-api-app.md)
* [Válaszadás külső eseményekre és műveletekre webhookok használatával](../logic-apps/logic-apps-create-api-app.md)
* [Munkafolyamatok hívása, elindítása vagy beágyazása szinkron válaszokkal HTTP-kérelmekre](../logic-apps/logic-apps-http-endpoint.md)
* [Oktatóanyag: a mesterséges intelligenciát használó közösségi irányítópultok létrehozása percek alatt Logic Apps és Power BI](https://aka.ms/logicappsdemo)
* [Videó: válaszadás a Twilio SMS webhookokra és szöveges válasz küldése](https://channel9.msdn.com/Blogs/Windows-Azure/Azure-Logic-Apps-Walkthrough-Webhook-Functions-and-an-SMS-Bot)

## <a name="control-flow-error-handling-and-logging-capabilities"></a>Vezérlési folyamat, hibakezelés és naplózási képességek

A Logic apps többek között a speciális vezérlési folyamat, például a feltételek, a kapcsolók, a hurkok és a hatókörök széles körű funkcióit tartalmazza. A rugalmas megoldások biztosításához a munkafolyamatokban a hibák és kivételek kezelését is megvalósíthatja. A munkafolyamat-futtatási állapottal kapcsolatos értesítési és diagnosztikai naplók esetében a Azure Logic Apps figyelést és riasztásokat is biztosít.

* Különböző műveletek végrehajtása [feltételes utasítások](../logic-apps/logic-apps-control-flow-conditional-statement.md) és [switch utasítások](../logic-apps/logic-apps-control-flow-switch-statement.md) alapján
* [Tömbökben és gyűjteményekben lévő elemek ismétlése vagy feldolgozása hurkokkal](../logic-apps/logic-apps-control-flow-loops.md)
* [Műveletek csoportosítása hatókörökkel együtt](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Szerzői hibák és kivételek feldolgozása egy munkafolyamatban](../logic-apps/logic-apps-exception-handling.md)
* [Használati eset: hogyan használja az egészségügyi vállalat a Logic app-kivételeket a HL7 FHIR-munkafolyamatok kezeléséhez](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Meglévő logikai alkalmazások figyelésének, naplózásának és riasztásának bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [A monitorozási és diagnosztikai naplózás bekapcsolása logikai alkalmazások létrehozásakor](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)

## <a name="deploy-and-manage-logic-apps"></a>Logikai alkalmazások üzembe helyezése és kezelése

A Visual Studióval, az Azure DevOps vagy bármely más verziókövetés és automatizált Build eszközzel teljes mértékben fejlesztheti és telepítheti a Logic apps-alkalmazásokat. Az erőforrás-sablonban a munkafolyamatok és a függő kapcsolatok üzembe helyezésének támogatásához a Logic apps Azure erőforrás-telepítési sablonokat használ. A Visual Studio-eszközök automatikusan létrehozzák ezeket a sablonokat, amelyek bejelentkezhetnek a verziókövetés verziókövetésba.

* [Logikai alkalmazások létrehozása és üzembe helyezése a Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Meglévő logikai alkalmazások figyelésének, naplózásának és riasztásának bekapcsolása](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [A logikai alkalmazások telepítésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Minta: Kapcsolódás Azure Service Bus várólistákhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Kapcsolódás Azure Storage-fiókokhoz Azure Logic Apps és üzembe helyezés az Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Function app-művelet beállítása az Azure DevOps Azure-folyamatokkal való üzembe helyezéséhez és üzembe helyezéséhez Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: csatlakozás egy integrációs fiókhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

## <a name="content-types-conversions-and-transformations-within-a-run"></a>Egy futtatáson belüli tartalomtípusok, konverziók és átalakítások

Több tartalomtípus elérését, átalakítását és átalakítását a Azure Logic Apps [munkafolyamat-definíciós nyelv](https://aka.ms/logicappsdocs)számos funkciója segítségével végezheti el. A karakterlánc, a JSON és az XML között például a `@json()` és az `@xml()` munkafolyamat-kifejezéseket lehet konvertálni. A Logic Apps motor megőrzi a tartalomtípusokat, hogy a szolgáltatások között veszteségmentes módon támogassa a tartalom átvitelét.

* [A munkafolyamat-kifejezések működése a Logic Appsben](../logic-apps/logic-apps-author-definitions.md)
* [Nem JSON típusú tartalomtípusok](../logic-apps/logic-apps-content-type.md), például `application/xml`, `application/octet-stream`és `multipart/formdata` kezelése
* [Munkafolyamat-definíció nyelvi sémája Azure Logic Apps](https://aka.ms/logicappsdocs)

## <a name="other-integrations-and-capabilities"></a>Egyéb integrációk és képességek

A Logic apps számos szolgáltatással, például a Azure Functionsokkal, az Azure API Managementekkel, az Azure App Servicesekkel és az egyéni HTTP-végpontokkal, például a REST és a SZAPPANos integrációt is kínál.

* [Valós idejű közösségi irányítópult létrehozása az Azure kiszolgáló nélküli szolgáltatással](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Azure Functions meghívása a Logic appsből](../logic-apps/logic-apps-azure-functions.md)
* [Oktatóanyag: logikai alkalmazások elindítása Azure Functions](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Oktatóanyag: a virtuális gépek változásainak figyelése Azure Event Grid és Logic Apps](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Oktatóanyag: az Azure Logic Apps és az Azure Cognitive Services integrációs funkciójának létrehozása a Twitter utáni érzelmek elemzéséhez](../azure-functions/functions-twitter-email.md)
* [Oktatóanyag: a IoT távoli figyelése és értesítései Azure Logic Apps az IoT hub és a postaláda csatlakoztatásával](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: SOAP-végpontok hívása a Logic appsből](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Végpontok közötti forgatókönyvek

* [Tanulmány: az Azure-szolgáltatásokkal való teljes körű ügyviteli integráció, például Logic Apps](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Ügyfélbeszámolók

Ismerje meg, hogy a Azure Logic Apps, valamint más Azure-szolgáltatások és Microsoft-termékek mellett a vállalatok az összetett folyamatok egyszerűsítésével, rendszerezésével, automatizálásával és előkészítésével javítsák a [vállalatuk](https://aka.ms/logic-apps-customer-stories) rugalmasságát és az alapvető üzleti tevékenységekre koncentrálva.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg [Logic apps összekötői](../connectors/apis-list.md)
* Ismerkedjen meg a [B2B vállalati integrációs forgatókönyvekkel Azure Logic apps](../logic-apps/logic-apps-enterprise-integration-overview.md)
