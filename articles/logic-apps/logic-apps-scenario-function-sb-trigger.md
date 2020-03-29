---
title: Logikai alkalmazások meghívása Azure Functions-függvényekkel
description: Olyan Azure-függvények létrehozása, amelyek az Azure Service Bus hallgatásával szólítják vagy aktiválják a logikai alkalmazásokat
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428711"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Logikai alkalmazások hívása vagy aktiválása az Azure Functions és az Azure Service Bus használatával

Az [Azure Functions](../azure-functions/functions-overview.md) segítségével egy logikai alkalmazást aktiválhat, ha egy hosszú ideig futó figyelőt vagy feladatot kell üzembe helyeznie. Létrehozhat például egy Azure-függvényt, amely figyel egy Azure Service Bus-várólistán, és azonnal elindítja a logikai alkalmazást leküldéses eseményindítóként. [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Azure Service Bus névtér. Ha nem rendelkezik névtérrel, [először hozza létre a névteret.](../service-bus-messaging/service-bus-create-namespace-portal.md)

* Egy Azure-függvény alkalmazás, amely egy tároló az Azure-függvények. Ha nem rendelkezik függvényalkalmazással, [először hozza létre a függvényalkalmazást,](../azure-functions/functions-create-first-azure-function.md)és győződjön meg arról, hogy a .NET-et választja futásidejű veremként.

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a forgatókönyvben egy függvény tfut minden logikai alkalmazást, amely et el szeretne indítani. Először hozzon létre egy logikai alkalmazást, amely egy HTTP-kérelem eseményindítóval kezdődik. A függvény meghívja ezt a végpontot, amikor üzenetsorüzenet érkezik.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)és hozzon létre üres logikai alkalmazást.

   Ha most kezdi meg a logikai alkalmazásokat, tekintse át [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című.

1. A keresőmezőbe írja be a `http request` kifejezést. Az eseményindítók listájában válassza a **HTTP-kérelem fogadásának kori** eseményindítóját.

   ![Eseményindító kiválasztása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   A kérelem eseményindító, opcionálisan adja meg a JSON-séma a várólista-üzenet használatával. A JSON-sémák segítségével a Logic App Designer megismerheti a bemeneti adatok szerkezetét, és megkönnyíti a kimenetek használatát a munkafolyamatban.

1. Séma megadásához írja be a sémát a **Kérelem törzse JSON-séma** mezőbe, például:

   ![JSON-séma megadása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Ha nem rendelkezik sémával, de json formátumú minta hasznos adattal rendelkezik, ebből a hasznos adatból létrehozhat egy sémát.

   1. A kérelem eseményindító, válassza **a Minta hasznos adat létrehozása séma.**

   1. Az **Enter (JSON-tartalom) megadása vagy beillesztése**csoportban adja meg a mintahasznos értéket, és válassza a **Kész**lehetőséget.

      ![Mintahasznos adat megadása](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Ez a minta hasznos adat generálja ezt a sémát, amely megjelenik az eseményindítóban:

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

1. Adja hozzá a várólista-üzenet fogadása után futtatni kívánt egyéb műveleteket.

   Küldhet például egy e-mailt az Office 365 Outlook-összekötővel.

1. Mentse el a logikai alkalmazást, amely létrehozza a visszahívási URL-t az eseményindító ebben a logikai alkalmazásban. Később használja ezt a visszahívási URL-címet az Azure Service Bus-várólista-eseményindító kódjában.

   A visszahívási URL megjelenik a **HTTP POST URL-cím** tulajdonságában.

   ![Generált visszahívási URL-cím az eseményindítóhoz](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure-függvény létrehozása

Ezután hozza létre azt a függvényt, amely eseményindítóként működik, és megfigyeli a várólistát.

1. Az Azure Portalon nyissa meg és bontsa ki a függvényalkalmazást, ha még nem nyitott. 

1. A függvényalkalmazás neve alatt bontsa ki a **Functions csomópontot.** A **Funkciók** ablaktáblán válassza az **Új függvény**lehetőséget.

   ![Bontsa ki a "Funkciók" lehetőséget, és válassza az "Új függvény" lehetőséget](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Válassza ki ezt a sablont attól függően, hogy létrehozott-e egy új függvényalkalmazást, ahol a .NET-et választotta futásidejű veremként, vagy egy meglévő függvényalkalmazást használ.

   * Új függvényalkalmazások esetén válassza ezt a sablont: **Service Bus Queue trigger**

     ![Sablon kiválasztása az új függvényalkalmazáshoz](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Meglévő függvényalkalmazás esetén válassza ki ezt a sablont: **Service Bus Queue trigger - C#**

     ![Sablon kiválasztása a meglévő függvényalkalmazáshoz](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Az **Azure Service Bus-várólista eseményindító** ablaktábláján adja meg az eseményindító nevét, és állítsa be `OnMessageReceive()` a **Service Bus-kapcsolatot** a várólistához, amely az Azure Service Service SDK-figyelőt használja, és válassza a **Létrehozás lehetőséget.**

1. Írjon egy alapvető függvényt a korábban létrehozott logikai alkalmazás végpontjának meghívásához a várólistaüzenet eseményindítóként való használatával. A funkció megírása előtt tekintse át az alábbi szempontokat:

   * Ez a `application/json` példa az üzenet tartalomtípusát használja, de szükség szerint módosíthatja ezt a típust.
   
   * A lehetséges egyidejűleg futó függvények, nagy mennyiségek vagy nagy terhelések miatt `using` ne hozza létre a [HTTPClient osztályt](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) az utasítással, és ne hozzon létre kérésenként HTTPClient-példányokat. További információ: [Use HttpClientFactory to implement resilient HTTP requests](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Ha lehetséges, használja fel újra a HTTP-ügyfelek példányát. További információt a Kapcsolatok kezelése az Azure Functionsben című [témakörben talál.](../azure-functions/manage-connections.md)

   Ez a [ `Task.Run` példa](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run) a metódust [aszinkron](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async) módban használja. További információ: [Aszinkron programozás async és várja](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. A függvény teszteléséhez adjon hozzá egy üzenetsor-üzenetet egy eszközzel, például a [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)segítségével.

   A logikai alkalmazás azonnal aktiválódik, miután a függvény megkapja az üzenetet.

## <a name="next-steps"></a>További lépések

* [Munkafolyamatok hívása, aktiválása vagy beágyazása HTTP-végpontok használatával](../logic-apps/logic-apps-http-endpoint.md)
