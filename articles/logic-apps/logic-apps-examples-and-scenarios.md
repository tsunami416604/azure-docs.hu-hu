---
title: Példák & gyakori forgatókönyvek
description: Példák, gyakori forgatókönyvek, oktatóanyagok és forgatókönyvek keresése az Azure Logic Apps alkalmazáshoz
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: c64483f18b2deb061704a4eb7171e575fdd561e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164627"
---
# <a name="common-scenarios-examples-tutorials-and-walkthroughs-for-azure-logic-apps"></a>Gyakori forgatókönyvek, példák, oktatóanyagok és forgatókönyvek az Azure Logic Apps

[Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) segít a különböző szolgáltatások vezénylésében és integrálásában [azáltal, hogy több száz használatra kész összekötőt](../connectors/apis-list.md)biztosít, a helyszíni SQL Servertől vagy az SAP-tól az Azure Cognitive Services-ig. A Logic Apps szolgáltatás "kiszolgáló nélküli", így nem kell aggódnia a méretezés vagy a példányok. Mindössze annyit kell tennie, hogy meghatározza a munkafolyamatot egy eseményindítóval és a munkafolyamat által végrehajtott műveletekkel. Az alapul szolgáló platform kezeli a skálázást, a rendelkezésre állást és a teljesítményt. A Logic Apps különösen hasznos olyan esetek és forgatókönyvek esetében, ahol több rendszer és szolgáltatás közötti műveleteket kell koordinálnia.

Az Azure Logic Apps által támogatott képességek és minták megismeréséhez ez a cikk ismerteti a gyakori kiindulási pontokat, példákat és forgatókönyveket.

## <a name="common-starting-points-for-logic-app-workflows"></a>A logikai alkalmazás-munkafolyamatok közös kiindulópontjai

Minden logikai alkalmazás egy [*eseményindítóval*](../logic-apps/logic-apps-overview.md#logic-app-concepts)kezdődik, és csak egy eseményindítóval, amely elindítja a logikai alkalmazás munkafolyamatát, és az eseményindító részeként bármely adatot átad. Egyes összekötők az ilyen típusú eseményindítókat biztosítanak:

* *Lekérdezési eseményindítók*: Rendszeresen ellenőrzi a szolgáltatás végpontját az új adatok. Ha új adatok léteznek, az eseményindító létrehoz és futtat egy új munkafolyamat-példányt az adatokkal bemenetként.

* *Leküldéses eseményindítók:* Figyeli az adatokat a szolgáltatás végpontja, és megvárja, amíg egy adott esemény történik. Amikor az esemény bekövetkezik, az eseményindító azonnal aktiválódik, és egy új munkafolyamat-példányt hoz létre, amely a rendelkezésre álló adatokat használja bemenetként.

Íme néhány példa, amelyek leírják a gyakran használt eseményindítók:

* *Lekérdezési* eseményindítók:

  * [ **Az ismétlődési** eseményindító](../connectors/connectors-native-recurrence.md) lehetővé teszi a kezdési dátum és az idő, valamint a logikai alkalmazás indításának ismétlődését. Például kiválaszthatja a hét napjait és a napszakokat a logikai alkalmazás aktiválásához. További információt az alábbi témakörökben talál:<p>

    * [Ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezése és futtatása az Azure Logic Apps használatával](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
    * [Oktatóanyag: Automatizált, ütemezésalapú ismétlődő munkafolyamatok létrehozása az Azure Logic Apps használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)

  * Az **E-mail érkezésekor** eseményindító lehetővé teszi, hogy a logikai alkalmazás ellenőrizze az új e-maileket bármely, a Logic Apps által támogatott levelezési szolgáltatótól, például az [Office 365 Outlook,](../connectors/connectors-create-api-office365-outlook.md) [a Gmail,](https://docs.microsoft.com/connectors/gmail/) [a Outlook.com](https://docs.microsoft.com/connectors/outlook/)és így tovább. További információt az alábbi témakörökben talál:<p>

    * [Oktatóanyag: Automatikus, jóváhagyásalapú munkafolyamatok létrehozása az Azure Logic Apps használatával](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
    * [Oktatóanyag: Az Azure Logic Apps, az Azure Functions és az Azure Storage használatával az e-mailek feldolgozására irányuló feladatok automatizálása](../logic-apps/tutorial-process-email-attachments-workflow.md)

  * A [ **HTTP-eseményindító** ](../connectors/connectors-native-http.md) http-n vagy HTTPS-en keresztül hívhat meg egy szolgáltatásvégpontot. További információt a [Munkafolyamatok hívása, aktiválása vagy beágyazása HTTP-végpontok használatával](../logic-apps/logic-apps-http-endpoint.md)című témakörben talál.

* *Leküldéses* eseményindítók:

  * A [ **kérelem** eseményindító](../connectors/connectors-native-reqres.md) fogadhat bejövő HTTPS-kérelmeket.

  * A [ **HTTP Webhook-eseményindító** ](../connectors/connectors-native-webhook.md) előfizet egy szolgáltatásvégpontra egy *visszahívási URL-cím* regisztrálásával az adott szolgáltatással. Így a szolgáltatás csak értesíti az eseményindítót, amikor a megadott esemény történik, így az eseményindító nem kell lehallgatni a szolgáltatást.

A megadott esemény bekövetkezése után az eseményindító aktiválódik, amely létrehoz egy új logikai alkalmazás munkafolyamat-példányt, és futtatja a munkafolyamatban végrehajtott műveleteket. Az eseményindítóból származó adatok at a munkafolyamat során elérheti. Például a Twitter **egy új tweet** trigger halad a tweet tartalmát a logikai alkalmazás fut. Az Azure Logic Apps első lépéseihez próbálkozzon az alábbi rövid útmutatókkal:

* [Rövid útmutató: Az első automatizált munkafolyamat létrehozása az Azure Logic Apps használatával – Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Rövid útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps használatával – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Gyorsútmutató: Automatizált logikai alkalmazás-munkafolyamatok létrehozása és kezelése a Visual Studio-kód használatával](../logic-apps/quickstart-create-logic-apps-visual-studio-code.md)

## <a name="control-flow-and-error-handling-capabilities"></a>A folyamat- és hibakezelési képességek szabályozása

A logikai alkalmazások gazdag képességeket tartalmaznak a fejlett vezérlési folyamathoz, például feltételekhez, kapcsolókhoz, hurkokhoz és hatókörökhöz. A rugalmas megoldások biztosítása érdekében hiba- és kivételkezelést is megvalósíthat a munkafolyamatokban.

* Feltételes utasítások on és [kapcsolóutasításokon](../logic-apps/logic-apps-control-flow-conditional-statement.md) alapuló különböző műveletek végrehajtása [switch statements](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Lépések ismétlése vagy elemek feldolgozása tömbökben és gyűjtemények ciklusokkal](../logic-apps/logic-apps-control-flow-loops.md)
* [Csoportos műveletek és hatókörök](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* [Hiba- és kivételkezelés hozzáadása munkafolyamathoz](../logic-apps/logic-apps-exception-handling.md)
* [Használati eset: Hogyan használja egy egészségügyi vállalat a logikai alkalmazások kivételkezelését a HL7 FHIR munkafolyamatokhoz?](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)

## <a name="create-custom-apis-and-connectors"></a>Egyéni API-k és összekötők létrehozása

A rendszerek és szolgáltatások, amelyek nem rendelkeznek közzétett összekötők, kiterjesztheti a logikai alkalmazásokat is.

* [Egyéni API-k létrehozása az Azure Logic Apps szolgáltatásból hívandó](../logic-apps/logic-apps-create-api-app.md)
* [Új adatok vagy események rendszeres ellenőrzése a lekérdezési eseményindító mintájának használatával](../logic-apps/logic-apps-create-api-app.md#polling-triggers)
* [Várjon és hallgassa az új adatokat vagy eseményeket a webhook-eseményindító minta használatával](../logic-apps/logic-apps-create-api-app.md#webhook-triggers)
* [Hosszú ideig futó feladatok végrehajtása a lekérdezési művelet mintájával](../logic-apps/logic-apps-create-api-app.md#async-pattern)
* [Hosszú ideig futó feladatok végrehajtása a webhook műveletmintával](../logic-apps/logic-apps-create-api-app.md#webhook-actions)
* [Egyéni összekötők az Azure Logic-alkalmazásokban](../logic-apps/custom-connector-overview.md)

## <a name="build-business-to-business-b2b-solutions"></a>Üzleti megoldások (B2B) megoldások készítése

A vállalati integrációs megoldások és a szervezetek közötti zökkenőmentes kommunikáció érdekében automatikus méretezhető munkafolyamatokat hozhat létre ezekhez a forgatókönyvekhez az Azure Logic Apps vállalati integrációs csomag (EIP) használatával. Bár a szervezetek különböző protokollokat és formátumokat használnak, elektronikus úton is válthatnak üzeneteket. Az EIP a különböző formátumokat olyan formátumba alakítja át, amelyet a szervezetek rendszerei fel dolgozhatnak, és támogatja az iparági szabványnak megfelelő protokollokat, például az AS2, X12, EDIFACT és RosettaNet protokollokat. Ezeka megoldások létrehozásához hozzon létre egy integrációs fiókot, amely egy külön Azure-erőforrás, amely biztonságos, méretezhető és kezelhető tárolót biztosít a logikai alkalmazás munkafolyamataihoz definiált és használt összetevők számára. Összetevők közé tartoznak például a kereskedelmi partnerek, megállapodások, térképek, sémák, tanúsítványok és kötegkonfigurációk.

* [Áttekintés: B2B vállalati integrációs megoldások az Azure Logic Alkalmazásokkal és az Enterprise Integration Pack csomaggal](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Integrációs fiókok létrehozása és kezelése a B2B vállalati integrációkhoz az Azure Logic Apps alkalmazásban](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

## <a name="access-azure-virtual-network-resources"></a>Az Azure virtuális hálózati erőforrásainak elérése

Néha a logikai alkalmazások és integrációs fiókok hozzáféréssel kell férniük a biztonságos erőforrásokhoz, például a virtuális gépekhez (VM-ek) és más rendszerekhez vagy szolgáltatásokhoz, amelyek egy Azure virtuális hálózatban vannak. A hozzáférés beállításához létrehozhat egy integrációs szolgáltatási környezetet (ISE), ahol a logikai alkalmazások at hozhat létre és futtathatja. Az ISE a Logic Apps szolgáltatás privát és elkülönített példánya, amely dedikált erőforrásokat, például tárolást használ, és a nyilvános, "globális" több-bérlős Logic Apps szolgáltatástól elkülönítve fut. Az elkülönített privát példány és a nyilvános globális példány elkülönítése is segít csökkenteni a más Azure-bérlők által az alkalmazások teljesítményére gyakorolt hatását, amelyet "zajos szomszédok" hatásnak is neveznek.

* [Áttekintés: Hozzáférés az Azure virtuális hálózati erőforrásaihoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Csatlakozás az Azure-alapú virtuális hálózatokhoz az Azure Logic Apps alkalmazásból](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="deploy-manage-and-monitor-logic-apps"></a>Logikai alkalmazások telepítése, kezelése és figyelése

A Visual Studio, az Azure DevOps vagy bármely más forrásvezérlő és automatizált buildeszközök segítségével teljes mértékben fejleszthet és üzembe helyezhet logikai alkalmazásokat. A munkafolyamatok és a függő kapcsolatok erőforrássablonban történő üzembe helyezésének támogatásához a logikai alkalmazások Azure-erőforrás-telepítési sablonokat használnak. A Visual Studio eszközei automatikusan generálják ezeket a sablonokat, amelyeket beadhat a verziószámozásforrás-vezérlőbe. A munkafolyamat-futtatási állapot értesítési és diagnosztikai naplók hoz, az Azure Logic Apps is biztosít figyelésésés riasztások.

### <a name="deploy"></a>Üzembe helyezés

* [Rövid útmutató: Automatizált feladatok, folyamatok és munkafolyamatok létrehozása az Azure Logic Apps használatával – Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Áttekintés: A logikai alkalmazások telepítésének automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Azure Resource Manager-sablonok létrehozása az Azure Logic Apps üzembe helyezésének automatizálásához](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
* [Azure Resource Manager-sablonok üzembe helyezése az Azure Logic Apps alkalmazásokhoz](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
* [Példa: Csatlakozás az Azure Service Bus-várólistákhoz az Azure Logic Apps alkalmazásból, és üzembe helyezés az Azure-folyamatok használatával az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Példa: Csatlakozás Azure Storage-fiókokhoz az Azure Logic Apps alkalmazásból, és üzembe helyezés az Azure-folyamatok használatával az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Példa: Hozzon létre egy függvényalkalmazás-műveletet az Azure Logic Apps alkalmazáshoz, és telepítse az Azure-folyamatokat az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Csatlakozzon egy integrációs fiókhoz az Azure Logic Apps alkalmazásból, és telepítse az Azure-folyamatokat az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Minta: Az Azure-folyamatok koordinálása az Azure Logic Apps használatával](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

### <a name="manage"></a>Kezelés

* [Logikai alkalmazások kezelése a Visual Studio használatával](../logic-apps/manage-logic-apps-with-visual-studio.md)
* [Integrációs fiókok létrehozása és kezelése a B2B vállalati integrációkhoz](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)
* [Integrációs szolgáltatási környezet (ISE) kezelése az Azure Logic Apps alkalmazásban](../logic-apps/ise-manage-integration-service-environment.md)

### <a name="monitor"></a>Figyelés

* [Futásállapotának figyelése, az eseményindítók előzményeinek áttekintése és riasztások beállítása az Azure Logic Apps alkalmazásokhoz](../logic-apps/monitor-logic-apps.md)
* [Az Azure Monitor naplóinak beállítása és diagnosztikai adatok gyűjtése az Azure Logic Apps alkalmazásokhoz](../logic-apps/monitor-logic-apps-log-analytics.md)
* [Az Azure Monitor naplóinak beállítása és diagnosztikai adatok gyűjtése a B2B-üzenetekhez az Azure Logic Apps alkalmazásban](../logic-apps/monitor-b2b-messages-log-analytics.md)
* [Az Azure Logic Apps figyelőnaplóiban figyelési és nyomon követési lekérdezések megtekintése és létrehozása](../logic-apps/create-monitoring-tracking-queries.md)

## <a name="handle-content-types-conversions-and-transformations"></a>Tartalomtípusok, konverziók és átalakítások kezelése

Az Azure Logic Apps [munkafolyamat-definíciós nyelvszámos](https://aka.ms/logicappsdocs)funkciójával több tartalomtípust is elérhet, konvertálhat és konvertálhat. Konvertálhat például egy karakterlánc, JSON és XML `@json()` `@xml()` között a és a munkafolyamat-kifejezésekkel. A Logic Apps motor megőrzi a tartalomtípusokat a tartalomátvitel támogatásához veszteségmentes módon a szolgáltatások között.

* [Tartalomtípusok kezelése az Azure Logic Apps alkalmazásban,](../logic-apps/logic-apps-content-type.md)például `application/`a , `application/octet-stream`és`multipart/formdata`
* [Útmutató a függvények használatához az Azure Logic Apps és a Power Automate kifejezésekben](../logic-apps/workflow-definition-language-functions-reference.md)
* [Munkafolyamat-definíciós nyelvi séma az Azure Logic Apps alkalmazáshoz](../logic-apps/logic-apps-workflow-definition-language.md)

## <a name="other-integrations-and-capabilities"></a>Egyéb integrációk és képességek

Az Azure Logic Apps számos szolgáltatással integrálható, például az Azure Functions, az Azure API Management, az Azure App Service és az egyéni HTTP-végpontok, például a REST és a SOAP szolgáltatással.

* [Az Azure-függvények hívása az Azure Logic Apps alkalmazásból](../logic-apps/logic-apps-azure-functions.md)
* [Oktatóanyag: Az Azure Functions és az Azure Service Bus használatával logikai alkalmazások hívása vagy aktiválása](../logic-apps/logic-apps-scenario-function-sb-trigger.md)
* [Oktatóanyag: Hozzon létre egy streamelési ügyfélelemzési irányítópultot az Azure Logic Apps és az Azure Functions segítségével](../logic-apps/logic-apps-scenario-social-serverless.md)
* [Oktatóanyag: Hozzon létre egy olyan funkciót, amely integrálható az Azure Logic Apps és az Azure Cognitive Services elemzéséhez Twitter post hangulat](../azure-functions/functions-twitter-email.md)
* [Oktatóanyag: AI-alapú közösségi irányítópult létrehozása a Power BI és az Azure Logic Apps használatával](https://aka.ms/logicappsdemo)
* [Oktatóanyag: A virtuális gépek változásainak figyelése az Azure Event Grid és a Logic Apps használatával](../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md)
* [Oktatóanyag: IoT-alapú távoli figyelés és értesítések az IoT-központés a postaláda összekötő Azure Logic Apps alkalmazásokkal](../iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps.md)
* [Blog: Soap-szolgáltatások hívása az Azure Logic Apps használatával](https://blogs.msdn.microsoft.com/logicapps/2016/04/07/using-soap-services-with-logic-apps/)

## <a name="end-to-end-scenarios"></a>Végpontok közötti forgatókönyvek

* [Tanulmány: Végpontok közötti esetkezelési integráció az Azure-szolgáltatásokkal, például a Logic Apps alkalmazásokkal](https://aka.ms/enterprise-integration-e2e-case-management-utilities-logic-apps)

## <a name="customer-stories"></a>Ügyfelek történetei

Ismerje meg, hogy az Azure Logic Apps más Azure-szolgáltatásokkal és Microsoft-termékekkel együtt hogyan segítette [ezeket a vállalatokat](https://aka.ms/logic-apps-customer-stories) a mozgékonyságuk javításában, és hogyan összpontosíthat az alapvető üzleti tevékenységükre az összetett folyamatok egyszerűsítésével, rendszerezésével, automatizálásával és vezénylésével.

## <a name="next-steps"></a>További lépések

* [Tudnivalók a Logic Apps összekötőiről](../connectors/apis-list.md)
* Ismerje meg a [B2B vállalati integrációs forgatókönyveit az Azure Logic Apps alkalmazásokkal](../logic-apps/logic-apps-enterprise-integration-overview.md)
