---
title: Logikai alkalmazások meghívása Azure Functions-Azure Logic Apps
description: Logikai alkalmazásokat hívó vagy indító Azure functions létrehozása a Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 2ab6ace7c30c3dd385928b6b0ae8000485d5f495
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680142"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Logikai alkalmazások meghívása vagy elindítása Azure Functions és Azure Service Bus használatával

A [Azure functions](../azure-functions/functions-overview.md) használatával aktiválhat egy logikai alkalmazást, ha hosszú ideig futó figyelőt vagy feladatot kell telepítenie. Létrehozhat például egy olyan Azure-függvényt, amely egy [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) várólistán figyel, és azonnal leküldéses eseményindítóként egy logikai alkalmazást indít el.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure Service Bus névtér. Ha nem rendelkezik névtérrel, [először hozza létre a névteret](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Egy Azure Function-alkalmazás, amely az Azure functions tárolója. Ha nem rendelkezik Function alkalmazással, [először hozza létre a Function alkalmazást](../azure-functions/functions-create-first-azure-function.md), és győződjön meg arról, hogy a .NET-et futtatókörnyezeti veremként választja.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a forgatókönyvben egy olyan függvény fut, amely minden olyan logikai alkalmazást futtat, amelyet el szeretne indítani. Először hozzon létre egy logikai alkalmazást, amely egy HTTP-kérelem triggerével kezdődik. A függvény meghívja ezt a végpontot, amikor üzenetsor-üzenetet fogad.  

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és hozzon létre üres logikai alkalmazást.

   Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című útmutatót.

1. A keresőmezőbe írja be a "http-kérelem" kifejezést. Az eseményindítók listából válassza ki ezt az eseményindítót: **http-kérés fogadásakor**

   ![Trigger kiválasztása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   A kérelem triggerrel megadhat egy JSON-sémát, amelyet az üzenetsor-üzenethez használhat. A JSON-sémák segítenek a Logic app designernek megérteni a bemeneti adatok szerkezetét, és megkönnyíti a kimenetek használatát a munkafolyamatban.

1. Séma megadásához írja be a sémát a **kérelem törzse JSON-sémája** mezőbe, például:

   ![JSON-séma meghatározása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Ha nem rendelkezik sémával, de JSON formátumú minta-adattartalommal rendelkezik, létrehozhat egy sémát ebből a hasznos adatból.

   1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához**lehetőséget.

   1. Az **írja be vagy illessze be a minta JSON-adattartalmat**területen adja meg a minta hasznos adatait, majd válassza a **kész**lehetőséget.

      ![Adja meg a minta hasznos adatait](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Ez a minta hasznos adatokat generáló séma az triggerben jelenik meg:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Adja meg az üzenetsor-üzenet fogadása után futtatni kívánt egyéb műveleteket.

   Küldhet például egy e-mailt az Office 365 Outlook Connector használatával.

1. Mentse a logikai alkalmazást, amely létrehozza az trigger visszahívási URL-címét a logikai alkalmazásban. Később ezt a visszahívási URL-címet használja a Azure Service Bus üzenetsor-trigger kódjában.

   A visszahívási URL-cím a **http post URL-cím** tulajdonságában jelenik meg.

   ![Generált visszahívási URL-cím triggerhez](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure-függvény létrehozása

Ezután hozza létre az triggerként viselkedő függvényt, és figyelje a várólistát.

1. A Azure Portal nyissa meg és bontsa ki a Function alkalmazást, ha még nincs megnyitva. 

1. A Function alkalmazás neve alatt bontsa ki a **függvények**elemet. A **függvények** ablaktáblán válassza az **új függvény**lehetőséget.

   ![Bontsa ki a "függvények" elemet, és válassza az "új függvény" lehetőséget.](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Válassza ki ezt a sablont attól függően, hogy létrehozott-e egy új Function alkalmazást, ahol a .NET-et futtatókörnyezeti veremként választotta, vagy egy meglévő Function alkalmazást használ.

   * Az új Function apps esetében válassza a következő sablont: **Service Bus üzenetsor-trigger**

     ![Új Function-alkalmazás sablonjának kiválasztása](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Meglévő Function alkalmazás esetén válassza a következő sablont: **Service Bus üzenetsor-trigger- C#**

     ![Sablon kiválasztása meglévő Function-alkalmazáshoz](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Az **Azure Service Bus várólista-trigger** ablaktáblán adja meg az trigger nevét, majd állítsa be a **Service Bus-kapcsolatokat** a várólista számára, amely az Azure Service Bus SDK `OnMessageReceive()`-figyelőt használja, majd válassza a **Létrehozás**lehetőséget.

1. Írjon egy alapszintű függvényt a korábban létrehozott Logic app-végpont meghívásához az üzenetsor-üzenet triggerként való használatával. Ez a példa a `application/json` üzenet tartalmának típusát használja, de szükség szerint módosíthatja ezt a típust. Ha lehetséges, használja újra a HTTP-ügyfelek példányát. További információ: [kapcsolatok kezelése Azure Functionsban](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. A függvény teszteléséhez adjon hozzá egy üzenetsor-üzenetet egy olyan eszköz használatával, mint például a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

   A logikai alkalmazás azonnal elindul, miután a függvény megkapja az üzenetet.

## <a name="next-steps"></a>Következő lépések

[Munkafolyamatok hívása, elindítása vagy beágyazása HTTP-végpontok használatával](../logic-apps/logic-apps-http-endpoint.md)