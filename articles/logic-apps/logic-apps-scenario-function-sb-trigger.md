---
title: Forgatókönyv - eseményindító logic apps az Azure Functions és az Azure Service Bus |} A Microsoft Docs
description: A logic apps-trigger által az Azure Functions és az Azure Service Bus függvények létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 69a4e4c59038599a7375466c46878bdd017582fa
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231610"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Forgatókönyv: A logic apps az Azure Functions és az Azure Service Bus-Trigger

Az Azure Functions segítségével egy eseményindítót egy logikai alkalmazást létrehozni, amikor üzembe kell helyeznie egy hosszú ideig futó figyelő vagy feladat. Például hozzon létre egy függvényt, amely figyeli üzenetsorba, és azonnal aktiválódik a logikai alkalmazás leküldéses eseményindítóként.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Mielőtt létrehozhatna egy Azure-függvényt, [hozzon létre egy függvényalkalmazást](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Logikai alkalmazás létrehozása

Ebben a példában minden egyes logikai alkalmazás aktiválódik, akit egy függvény rendelkezik. Először hozzon létre egy logikai alkalmazást, amely a HTTP kérés eseményindító tartozik. A függvény meghívja az, hogy a végpont, amikor egy üzenetsor üzenetet.  

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és hozzon létre üres logikai alkalmazás. 

   Ha most ismerkedik a logic apps, tekintse át [a rövid útmutató: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. A Keresés mezőbe írja be a "http-kérelem". Eseményindítók listájában válassza a következő eseményindítót: **amikor egy HTTP-kérés fogadásakor.**

   ![Trigger kiválasztása](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Az a **kérelem** eseményindító, igény szerint megadhat egy JSON-sémájában az üzenetsorban található üzenet való használatra. JSON-sémáinak segítenek a Logikaialkalmazás-tervezőben válassza ki a munkafolyamat során könnyebben bemeneti adatokat és teszi kimenetek értelmezésekor. 

   Adjon meg egy séma, adja meg a sémának a **kérelem Kéréstörzs JSON-sémája** mezőbe, például: 

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

1. Adjon hozzá semmilyen más műveleteket, amelyeket az üzenetsorban található üzenet fogadása után. 

   Ha például egy e-mailt, az Office 365 Outlook-összekötővel küldhet.

1. Mentse a logikai alkalmazást, amely hoz létre a logikai alkalmazás a trigger visszahívási URL-CÍMÉT. Az URL-cím jelenik meg a **HTTP POST URL-címe** tulajdonság.

   ![Generált visszahívási URL-Címének eseményindító](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Az Azure-függvény létrehozása

Ezután hozzon létre a függvényt, amely az eseményindító funkcionál, és figyeli az üzenetsorba. 

1. Az Azure Portalon nyissa meg és bontsa ki a függvényalkalmazást, ha nem már megnyitásához. 

1. Bontsa ki a függvényalkalmazás nevére, **funkciók**. Az a **funkciók** panelen válassza a **új függvény**. Válassza ki az ezzel a sablonnal: **Service Bus-üzenetsor eseményindító – C#**
   
   ![Válassza ki az Azure Functions portálra](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Adja meg az eseményindító nevét, és konfigurálja a kapcsolatot a Service Bus-üzenetsorba, amely használja az Azure Service Bus SDK `OnMessageReceive()` figyelő.

1. Írjon egy alapszintű függvényt hívni a korábban létrehozott logic app-végpont használatával az üzenetsorban található üzenet eseményindítóként, például: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

   Ez a példa a `application/json` üzenet tartalmának típusa, de ilyen típusú, igény szerint módosíthatja.

1. A függvény teszteléséhez, adja hozzá az üzenetsori üzenet például egy olyan eszközzel a [Service Bus Explorerrel](https://github.com/paolosalvatori/ServiceBusExplorer). 

   A logikai alkalmazások eseményindítói, a függvény az üzenet fogadása után azonnal.

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

