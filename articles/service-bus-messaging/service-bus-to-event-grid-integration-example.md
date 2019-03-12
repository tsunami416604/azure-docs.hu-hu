---
title: Példák Azure Service Bus – Event Grid integrációra | Microsoft Docs
description: Ez a cikk példákat tartalmaz a Service Bus-üzenetkezelés és az Event Grid integrációjára.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: spelluru
ms.openlocfilehash: 7c38de9c1dbb5e8a286fa1b72d0461dd74ed6f25
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770527"
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Példák Azure Service Bus – Azure Event Grid integrációra

Ebben a cikkben megtudhatja, hogyan állíthat be egy Azure-függvényt és egy logikai alkalmazást, amelyek mindegyike egy Azure Event Grid-esemény fogadása révén fogad üzeneteket. A következőket fogja elvégezni:
 
* Hozzon létre egy egyszerű tesztelési Azure-függvényt a hibakereséshez és az Event Grid-eseményfolyam kezdeti folyamat. Ezt a lépést végezze el attól függetlenül, hogy a többi lépést elvégzi-e.
* Hozzon létre egy Azure-függvényt az Event Grid-események alapján az Azure Service Bus-üzenetek fogadásához és feldolgozásához.
* Az Azure App Service Logic Apps funkcióját használják.

Az Ön által létrehozott példa feltételezi, hogy a Service Bus-témakörnek két előfizetése van. Emellett feltételezi, hogy az Event Grid-előfizetés úgy lett létrehozva, hogy csak egy Service Bus-előfizetés számára küldjön eseményeket. 

A példában üzeneteket fog küldeni a Service Bus-témakörbe, és ellenőrizni fogja, hogy a Service Bus-előfizetéshez tartozó esemény létrejött-e. A függvény vagy a logikai alkalmazás fogadja az üzeneteket ebből a Service Bus-előfizetésből, és befejezi a feldolgozásukat.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzákezd, mindenképp végezze el a következő két szakasz lépéseit.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

Hozzon létre egy prémium szintű Service Bus-névteret és egy Service Bus-témakört, amely két előfizetéssel rendelkezik.

### <a name="send-a-message-to-the-service-bus-topic"></a>Üzenet küldése a Service Bus-témakörbe

Az üzenetek Service Bus-témakörbe történő elküldéséhez bármilyen metódust használhat. Az eljárás végén található mintakód feltételezi, hogy Visual Studio 2017 programot használ.

1. Klónozza az [azure-service-bus GitHub-adattárat](https://github.com/Azure/azure-service-bus/).

1. A Visual Studióban lépjen a *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* mappába és nyissa meg az *SBEventGridIntegration.sln* fájlt.

1. Lépjen a **MessageSender** projekthez, és válassza a **Program.cs** fájlt.

   ![8][]

1. Adja meg a témakör nevét és a kapcsolati sztringet, majd futtassa a következő konzolalkalmazás-kódot:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Tesztfüggvény beállítása

A teljes forgatókönyv végrehajtása előtt hozzon létre legalább egy kisebb tesztfüggvényt a hibakereséshez és az eseményfolyam megtekintéséhez.

1. Az Azure Portalon hozzon létre egy új Azure Functions-alkalmazást. Az Azure Functions alapismereteivel kapcsolatban tekintse meg az [Azure Functions dokumentációját](https://docs.microsoft.com/azure/azure-functions/).

1. Az újonnan létrehozott függvényben kattintson a pluszjelre (+) egy HTTP által aktivált függvény hozzáadásához:

    ![2][]
    
    Megjelenik az **Első lépések egy előre elkészített függvénnyel** ablak.

    ![3][]

1. Válassza a **Webhook + API**, a **CSharp**, majd a **Függvény létrehozása** lehetőségeket.
 
1. Illessze be a függvénybe a következő kódot:

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

1. Kattintson a **Mentés és futtatás** elemre.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>A függvény és a névtér összekapcsolása az Event Griden keresztül

Ebben a szakaszban összekapcsoljuk a függvényt és a Service Bus-névteret. Ebben a példában az Azure Portalt használjuk. Ha az eljárást a PowerShell vagy az Azure CLI segítségével kívánja elvégezni, olvassa el a következő cikket: [Az Azure Service Bus – Azure Event Grid integráció áttekintése](service-bus-to-event-grid-integration-concept.md).

Egy új Azure Event Grid-előfizetés létrehozásához tegye a következőket:
1. Az Azure Portalon lépjen a névteréhez, majd a bal oldali panelen válassza az **Event Grid** elemet.  
    Megjelenik a névtér ablaka, a jobb oldali panelen két Event Grid-előfizetéssel.

    ![20][]

1. Válassza az **Esemény-előfizetés** lehetőséget.  
    Megnyílik az **Esemény-előfizetés** ablak. Az alábbi képen egy űrlap látható az Azure-függvényekre vagy -webhookokra való előfizetéshez szűrők alkalmazása nélkül.

    ![21][]

1. Töltse ki az űrlapot a képen látható módon, és az **Utótagszűrő** mezőben adja meg a megfelelő szűrőt.

1. Kattintson a **Létrehozás** gombra.

1. Küldjön üzenetet a Service Bus-témakörbe az „Előfeltételek” szakaszban ismertetett módon, majd az Azure Functions monitorozási funkciójával ellenőrizze, hogy megkezdődött-e az események átvitele.

A következő lépés a függvény és a Service Bus-névtér összekapcsolása. Ebben a példában az Azure Portalt használjuk. Ha ezt a lépést a PowerShell vagy az Azure CLI segítségével kívánja elvégezni, olvassa el a következő cikket: [Az Azure Service Bus – Azure Event Grid integráció áttekintése](service-bus-to-event-grid-integration-concept.md).

![9][]

### <a name="receive-messages-by-using-azure-functions"></a>Üzenetek fogadása az Azure Functions használatával

Az előző szakaszban megtekinthetett egy egyszerű tesztelési és hibakeresési forgatókönyvet, illetve meggyőződhetett az események átvitelének megtörténtéről. 

Ez a szakasz az üzenetek fogadását és feldolgozását ismerteti az események fogadását követően.

Hozzá kell adni egy Azure-függvényt, ahogy az a következő példában is látható, mert az Azure Functions Service Bus-függvényei még nem támogatják natív módon az új Event Grid-integrációt.

1. Ugyanabban a Visual Studio-megoldásban, amelyben az előfeltételeket megnyitotta, jelölje ki a **ReceiveMessagesOnEvent.cs** fájlt. 

    ![10][]

1. Adja meg a kapcsolati sztringet a következő kódban:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

1. Nyissa meg az Azure Portalt, és töltse le a „Tesztfüggvény beállítása” szakaszban létrehozott Azure-függvényhez tartozó közzétételi profilt.

    ![11][]

1. A Visual Studióban kattintson a jobb gombbal az **SBEventGridIntegration** elemre, és válassza a **Közzététel** lehetőséget. 

1. A korábban letöltött közzétételi profil **Közzététel** paneljén válassza a **Profil importálása** lehetőséget, majd kattintson a **Közzététel** lehetőségre.

    ![12][]

1. Az új Azure-függvény közzétételét követően hozzon létre egy olyan új Azure Event Grid-előfizetést, amely erre az új függvényre mutat.  
    Ügyeljen arra, hogy a **Vége** mezőben a helyes szűrőt alkalmazza, amelynek a Service Bus-előfizetés nevének kell lennie.

1. Küldjön üzenetet a korábban létrehozott Azure Service Bus-témakörbe, majd az Azure Portalon az Azure Functions naplójában ellenőrizze, hogy megkezdődött-e az események átvitele és fogadása.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Üzenetek fogadása a Logic Apps használatával

Az alábbi utasítások végrehajtásával kapcsolhat össze egy logikai alkalmazást az Azure Service Bus és az Azure Event Grid szolgáltatással:

1. Hozzon létre egy új logikai alkalmazást az Azure Portalon, majd kezdő műveletként válassza ki az **Event Grid** szolgáltatást.

    ![13][]

    Megnyílik a Logic Apps Designer ablaka.

    ![14][]

1. Adja meg az adatait a következő módon:

    a. Az **Erőforrás neve** mezőben adja meg a saját névtere nevét. 

    b. A **Speciális beállítások** terület **Utótagszűrő** mezőjében adja meg az előfizetéséhez tartozó szűrőt.

1. Adjon hozzá egy Service Bus-műveletet a témakör-előfizetésből történő üzenetfogadáshoz.  
    Az utolsó műveletet a következő kép mutatja be:

    ![15][]

1. Adjon hozzá egy teljes eseményt a következő képen látható módon:

    ![16][]

1. Mentse a logikai alkalmazást, és küldjön üzenetet a Service Bus-témakörbe az „Előfeltételek” szakaszban ismertetett módon.  
    Figyelje meg a logikai alkalmazás végrehajtását. A végrehajtás részleteinek megtekintéséhez válassza az **Áttekintés** lehetőséget, majd tekintse meg az adatokat a **Futtatási előzmények** területen.

    ![17][]

    ![18][]

## <a name="next-steps"></a>További lépések

* További tudnivalók az [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) szolgáltatásról.
* További tudnivalók az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) szolgáltatásról.
* További tudnivalók az [Azure App Service szolgáltatás Logic Apps funkciójáról](https://docs.microsoft.com/azure/logic-apps/).
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
