---
title: Forgatókönyv – eseményindító logic Apps alkalmazások az Azure Functions és az Azure Service Bus |} Microsoft Docs
description: Egy logikai alkalmazást az Azure Functions és az Azure Service Bus használatával való függvény létrehozása
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: jeconnoc
editor: ''
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 6bc845e4ec329d308ed87770d0dec6a7d5e447c7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030953"
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Forgatókönyv: Egy logikai alkalmazást az Azure Functions és az Azure Service Bus indítás

Az Azure Functions segítségével a létrehozott logikai alkalmazás során kell telepítenie a hosszan futó figyelő vagy feladat. Például hozzon létre egy függvényt, amely figyeli várólistát, és azonnal érvényesítést leküldéses kiindulópontként logikai alkalmazás.

## <a name="build-the-logic-app"></a>A logikai alkalmazás létrehozása
Ebben a példában, hogy a funkciót, az egyes logikai alkalmazás, amelyet a elindítható futtatását. Először hozzon létre egy logikai alkalmazás, amely egy HTTP-kérelem eseményindító tartozik. A függvény meghívja az adott végpontra, ha egy üzenetsor-üzenetet kap.  

1. Logikai alkalmazás létrehozása.
2. Válassza ki a **manuális - HTTP-kérelem fogadásakor** eseményindító.
   Másik lehetőségként megadhat egy JSON-séma segítségével egy eszköz, például az üzenetsorban lévő üzenetet használandó [jsonschema.net](http://jsonschema.net). Illessze be a séma az eseményindító. Sémák a tervezőben, az adatok és a folyamat tulajdonságainak könnyebben a munkafolyamaton keresztül az alakzat megismerése érdekében.
2. Adjon hozzá egy várólista üzenet fogadása után végrehajtandó további lépésekről. Például az Office 365 keresztül e-mail küldése.  
3. Mentse a logikai alkalmazás létrehozása a logikai alkalmazást az eseményindító visszahívási URL-CÍMÉT. Az URL-cím jelenik meg az eseményindító kártya.

![A visszahívási URL-cím jelenik meg az eseményindító kártya][1]

## <a name="build-the-function"></a>A függvény létrehozása
A következő hozzon létre egy függvényt, amely az eseményindító funkcionál, és figyeli a várólistára.

1. Az a [Azure Functions portálon](https://functions.azure.com/), jelölje be **új függvény**, majd válassza ki a **ServiceBusQueueTrigger - C#** sablon.
   
    ![Az Azure Functions portálra][2]
2. Állítsa be a kapcsolatot a Service Bus-üzenetsorba, amely használja az Azure Service Bus SDK `OnMessageReceive()` figyelő.
3. Egy alapszintű függvény hívása a logic app végpont (korábban létrehozott) segítségével az üzenetsorban lévő üzenetet egy eseményindító írni. Íme egy teljes példa egy olyan függvényt. A példa egy `application/json` üzenet tartalomtípusa, de a típusát, szükség esetén módosíthatja.
   
   ```
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

Teszteléséhez hozzáadása egy üzenetsor hasonló eszköz használatával [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Tekintse meg a logikai alkalmazást, a függvény a üzenet után azonnal érvényesítést.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
