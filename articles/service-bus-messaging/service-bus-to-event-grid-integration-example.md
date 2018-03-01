---
title: "Példák Azure Service Bus – Event Grid integrációra | Microsoft Docs"
description: "Példák a Service Bus-üzenetkezelés és az Event Grid integrációjára"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 2a4d17673340d145de9a3514f920c74f7eebf6b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Példák Azure Service Bus – Azure Event Grid integrációra

Ebben a dokumentumban az Azure Functions és egy olyan logikai alkalmazás beállításával fog megismerkedni, amely egy Event Grid-esemény fogadása révén fogad üzeneteket. A példa egy két előfizetéssel rendelkező Service Bus-témakör meglétét és azt feltételezi, hogy az Event Grid-előfizetés úgy lett létrehozva, hogy csak egy Service Bus-előfizetés számára küldjön eseményeket. Ezt követően üzeneteket fog küldeni a Service Bus-témakörbe, és ellenőrizni fogja, hogy a Service Bus-előfizetéshez tartozó esemény létrejött-e, illetve hogy a függvény vagy a logikai alkalmazás fogadja-e az üzeneteket ebből a Service Bus-előfizetésből, és befejezi-e a feldolgozásukat.

* Mindenekelőtt győződjön meg arról, hogy minden [Előfeltétel](#prerequisites) teljesül.
* A hibakereséshez és a kezdeti Event Grid-eseményfolyam megtekintéséhez hozzon létre egy [egyszerű Azure-tesztfüggvényt](#test-function-setup).  **Ezt a lépést mindenképpen végre kell hajtani, függetlenül attól, hogy elvégzi-e a 3. vagy a 4. részt.**
* Event Grid-események alapján hozzon létre egy [Azure-függvényt a Service Bus-üzenetek fogadásához és feldolgozásához](#receive-messages-using-azure-function).
* Használja a [Logic Apps](#receive-messages-using-azure-logic-app) alkalmazást.

## <a name="prerequisites"></a>Előfeltételek

### <a name="service-bus-namespace"></a>Service Bus-névtér

Hozzon létre egy prémium szintű Service Bus-névteret. Hozzon létre egy Service Bus-témakört két előfizetéssel.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Kód az üzenet Service Bus-témakörbe való elküldéséhez

Az üzenetek Service Bus-témakörbe történő elküldéséhez bármilyen metódust használhat. Másik lehetőségként használhatja az alábbi mintát is. A mintakód feltételezi, hogy Visual Studio 2017 programot használ.

Klónozza [ezt a GitHub-adattárat](https://github.com/Azure/azure-service-bus/).

Keresse meg a

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration mappát, és nyissa meg a SBEventGridIntegration.sln fájlt.

Ezután keresse meg a MessageSender projektet, és nyissa meg a Program.cs fájlt.

![8][]

Adja meg a témakör nevét és a kapcsolati karakterláncot, majd futtassa a konzolalkalmazást:

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Függvénybeállítások tesztelése

A teljes, végpontok közötti forgatókönyv végrehajtása előtt minden bizonnyal futtatni kíván legalább egy kisebb tesztfüggvényt a hibakereséshez és az eseményfolyam megtekintéséhez.

A portálon hozzon létre egy új Azure-függvényalkalmazást. Kövesse ezt a [hivatkozást](https://docs.microsoft.com/en-us/azure/azure-functions/) az Azure Functions alapismereteinek elsajátításához.

Az újonnan létrehozott függvényben kattintson a kis plusz jelre egy HTTP által aktivált függvény hozzáadásához:

![2][]

![3][]

Ezt követően másolja a következő kódot a függvénybe:

```CSharp
#r "Newtonsoft.Json"
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // parse query parameter
    var content = req.Content;

    string jsonContent = await content.ReadAsStringAsync(); 
    log.Info($"Received Event with payload: {jsonContent}");

IEnumerable<string> headerValues;
if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
{
var validationHeaderValue = headerValues.FirstOrDefault();
if(validationHeaderValue == "SubscriptionValidation")
{
var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
     var code = events[0].Data["validationCode"];
     return req.CreateResponse(HttpStatusCode.OK,
     new { validationResponse = code });
}
}

    return jsonContent == null
    ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
    : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
}

public class GridEvent
{
    public string Id { get; set; }
    public string EventType { get; set; }
    public string Subject { get; set; }
    public DateTime EventTime { get; set; }
    public Dictionary<string, string> Data { get; set; }
    public string Topic { get; set; }
}
```

Kattintson a Mentés és futtatás gombra.

## <a name="connect-function-and-namespace-via-event-grid"></a>A függvény és a névtér összekapcsolása az Event Griden keresztül

A következő lépés a függvény és a Service Bus-névtér összekapcsolása. Ebben a példában az Azure Portalt használjuk. A [concepts](service-bus-to-event-grid-integration-concept.md oldalon megtudhatja, hogyan használható a PowerShell vagy az Azure CLI ugyanennek a műveletnek a végrehajtására.

Egy új Azure Event Grid-előfizetés létrehozásához keresse meg a saját névterét az Azure Portalon, majd válassza ki az Event Grid panelt. Kattintson a „+ Esemény-előfizetés” lehetőségre.

Az alábbi képernyőképen egy olyan névtér látható, amely már rendelkezik néhány Event Grid-előfizetéssel.

![20][]

Az alábbi képernyőkép az Azure-függvényekre vagy -webhookokra való szűrés nélküli előfizetés módját mutatja be. Ne feledje megadni a Service Bus-előfizetéshez tartozó szűrőt (például „Utótagszűrő”):

![21][]

Küldjön üzenetet a Service Bus-témakörbe az előfeltételek között ismertetett módon, majd az Azure-függvény monitorozási funkciójával ellenőrizze, hogy megkezdődött-e az események átvitele.

![9][]

### <a name="receive-messages-using-azure-function"></a>Üzenetek fogadása Azure-függvények használatával

Az előző szakaszban megtekinthetett egy egyszerű tesztelési és hibakeresési forgatókönyvet, illetve meggyőződhetett az események átvitelének megtörténtéről. A dokumentáció jelen részében az üzenetek fogadását és feldolgozását ismertetjük az események fogadásakor.

Az Azure-függvényeket azért így adjuk hozzá, mert az Azure Functions Service Bus-függvényei még nem támogatják natív módon az új Event Grid-integrációt.

Ugyanabban a Visual Studio-megoldásban, amelyben az előfeltételeket megnyitotta, jelölje ki a ReceiveMessagesOnEvent.cs fájlt. Adja meg a kapcsolati karakterláncot a kódban:

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Ezt követően nyissa meg az Azure Portalt, és keresse meg az ahhoz az Azure-függvényhez tartozó közzétételi profilt, amelyet még a [2. Tesztfüggvény beállítása](#2-test-function-setup) részben hozott létre.

![11][]

Ezután a Visual Studióban kattintson a jobb gombbal az SBEventGridIntegration elemre, és válassza a Közzététel lehetőséget. Használja az előzőleg letöltött közzétételi profilt, válassza a Profil importálása lehetőséget, majd kattintson a Közzététel lehetőségre.

![12][]

Az új Azure-függvény közzétételét követően hozzon létre egy olyan új Azure Event Grid-előfizetést, amely erre az új függvényre mutat. Győződjön meg arról, hogy a helyes „Vége” szűrőt alkalmazza, amely a Service Bus-előfizetés neve lesz.

Ezt követően küldjön üzenetet a korábban létrehozott Service Bus-témakörbe, majd az Azure-függvény naplójában ellenőrizze a portálon, hogy megkezdődött-e az események átvitele és fogadása.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Üzenetek fogadása az Azure Logic App használatával

Az alábbi utasítások végrehajtásával kapcsolhat össze egy Azure Logic App-alkalmazást az Azure Service Bus és az Azure Event Grid szolgáltatással:

Először hozzon létre egy új logikai alkalmazást az Azure Portalon, majd kezdő műveletként válassza ki az Event Grid szolgáltatást.

![13][]

A Logic Apps Designer kezdő nézete az alábbi képernyőképhez lesz hasonló, ahol az „Erőforrás neve” kifejezés helyett a saját névterének nevét kell beírnia. Mindenképpen bontsa ki a Speciális beállítások részt, és adja hozzá az előfizetéshez az utótagszűrőt:

![14][]

Ezt követően adjon hozzá egy Fogadás Service Bus-műveletet a témakör-előfizetésből történő üzenetfogadáshoz. Az utolsó műveletnek a következő képernyőképhez hasonlóan kell kinéznie.

![15][]

Ezt követően vegyen fel egy teljes eseményt, amelynek az alábbiak szerint kell kinéznie.

![16][]

Mentse a logikai alkalmazást, és küldjön üzenetet a Service Bus-témakörbe az előfeltételek között ismertetett módon. Ezután kövesse a logikai alkalmazások futtatását. Az „Áttekintés”, majd a „Futtatási előzmények” lehetőségre kattintva további adatokat is megtekinthet a futtatásról.

![17][]

![18][]

## <a name="next-steps"></a>További lépések

* További tudnivalók az [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) szolgáltatásról.
* További tudnivalók az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) szolgáltatásról.
* További tudnivalók az [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) szolgáltatásról.
* További tudnivalók az [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/) szolgáltatásról.

[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
