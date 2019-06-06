---
title: Hívása, vagy a logic apps az Azure Functions és az Azure Service Bus-trigger
description: Az Azure functions, és a logic apps az Azure Service Bus-trigger létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494974"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Hívása, vagy a logic apps által az Azure Functions és az Azure Service Bus-trigger

Használhat [Azure Functions](../azure-functions/functions-overview.md) indítható egy logikai alkalmazást, ha üzembe kell helyeznie egy hosszú ideig futó figyelő vagy feladat. Például létrehozhat egy Azure-függvényt, amely figyeli a egy [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) üzenetsort, és azonnal aktiválódik a logikai alkalmazás leküldéses eseményindítóként.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure Service Bus-névtér. Ha nem rendelkezik egy adott névtéren [először hozza létre a névteret](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Azure-függvényalkalmazás, amely az Azure functions tárolója. Ha nem rendelkezik egy függvényalkalmazást, [a függvényalkalmazás létrehozásához először](../azure-functions/functions-create-first-azure-function.md), és győződjön meg arról, hogy a futtatókörnyezeti veremként .NET kiválasztása.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ebben az esetben a függvény minden egyes logikai alkalmazást, amely a kiváltó van. Először hozzon létre egy logikai alkalmazást, amely a HTTP-kérelem típusú trigger kezdődik. A függvény meghívja az, hogy a végpont, amikor egy üzenetsor üzenetet.  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és hozzon létre üres logikai alkalmazás.

   Ha most ismerkedik a logic apps, tekintse át [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. A Keresés mezőbe írja be a "http-kérelem". Az eseményindítók listában jelölje ki az eseményindító: **HTTP-kérés fogadásakor**

   ![Trigger kiválasztása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   A kérelem eseményindítójában a igény szerint megadhat egy JSON-sémájában az üzenetsorban található üzenet használata. JSON-sémáinak segítenek a Logikaialkalmazás-Tervező ismerje meg a bemeneti adatok struktúráját, és a kimenetek könnyebben, amelyet használhat a munkafolyamatban.

1. Adjon meg egy séma, adja meg a sémának a **kérelem Kéréstörzs JSON-sémája** mezőbe, például:

   ![Adja meg a JSON-sémája](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Ha nem rendelkezik a séma, de egy hasznosadat-minta JSON formátumban van, a sémát, hogy hasznos-is létrehozhat.

   1. A kérelem eseményindítójában válassza **Mintaadat használata séma létrehozásához**.

   1. A **írja vagy illessze be a JSON hasznosadat-minta**, adja meg a hasznosadat-minta, és válassza **kész**.

      ![Adja meg a hasznosadat-minta](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Ez hasznosadat-minta ebben a sémában, amely akkor jelenik meg az eseményindító hoz létre:

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

1. Adja hozzá az üzenetsorban található üzenet fogadása után futtatni kívánt műveletek.

   Ha például egy e-mailt, az Office 365 Outlook-összekötővel küldhet.

1. Mentse a logikai alkalmazást, amely hoz létre a logikai alkalmazás a trigger visszahívási URL-CÍMÉT. Később fogja használni a visszahívási URL-Címének a kódot az Azure Service Bus-üzenetsor eseményindító.

   A visszahívási URL-cím megjelenik a **HTTP POST URL-címe** tulajdonság.

   ![Generált visszahívási URL-Címének eseményindító](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Az Azure-függvény létrehozása

Ezután hozzon létre a függvényt, amely az eseményindító funkcionál, és figyeli az üzenetsorba.

1. Az Azure Portalon nyissa meg és bontsa ki a függvényalkalmazást, ha nem már megnyitásához. 

1. Bontsa ki a függvényalkalmazás nevére, **funkciók**. Az a **funkciók** panelen válassza a **új függvény**.

   ![Bontsa ki a "Függvények", és válassza az "Új függvény"](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Válassza ki a sablon alapján, hogy létrehozott egy új függvényalkalmazást, ahol a futtatókörnyezeti veremként .NET kiválasztva, vagy meglévő függvényalkalmazással használ.

   * Új függvényalkalmazás válassza ezt a sablont: **Service Bus-üzenetsor eseményindító**

     ![Új függvényalkalmazás sablon kiválasztása](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * A meglévő függvényalkalmazással válassza ki az ezzel a sablonnal: **Service Bus-üzenetsor eseményindító-C#**

     ![A sablon létező függvényalkalmazás kiválasztása](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. A a **Azure Service Bus-üzenetsor eseményindító** panelen adja meg az eseményindító nevét, és állítsa be a **Service Bus-kapcsolat** az üzenetsorban, amely használja az Azure Service Bus SDK `OnMessageReceive()` figyelőjét, és válassza **Létrehozása**.

1. Írjon egy alapszintű függvényt hívni a korábban létrehozott logic app-végpont az üzenetsorban található üzenet eseményindítóként való használatával. Ez a példa a `application/json` üzenet tartalmának típusa, de ilyen típusú, igény szerint módosíthatja. Ha lehetséges újrafelhasználását teszi lehetővé az ügyfelek HTTP példányát. További információkért lásd: [kezelheti a kapcsolatokat az Azure Functions](../azure-functions/manage-connections.md).

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

1. A függvény teszteléséhez, adja hozzá az üzenetsori üzenet például egy olyan eszközzel a [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer).

   A logikai alkalmazások eseményindítói, a függvény az üzenet fogadása után azonnal.

## <a name="next-steps"></a>További lépések

[Hívása, aktiválása vagy beágyazása munkafolyamatok használatával a HTTP-végpontokat](../logic-apps/logic-apps-http-endpoint.md)