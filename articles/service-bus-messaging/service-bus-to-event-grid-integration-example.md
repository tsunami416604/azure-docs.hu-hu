---
title: 'Oktatóanyag: Az Azure Service Bus az Event Grid-integrációs példák'
description: 'Oktatóanyag: Ez a cikk példákat tartalmaz a Service Bus üzenetküldési és Event Grid-integráció.'
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
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067766"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Oktatóanyag: Az Azure Event Griden keresztül fogadott Azure Service Bus-események rekedése az Azure Functions és az Azure Logic Apps használatával
Ebben az oktatóanyagban megtudhatja, hogyan reagálhat az Azure Event Griden keresztül az Azure Functions és az Azure Logic Apps használatával fogadott Azure Service Bus-eseményekre. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Service Bus-névtér létrehozása
> * Mintaalkalmazás előkészítése üzenetek küldéséhez
> * Tesztfunkció beállítása az Azure-ban
> * A függvény és a névtér összekapcsolása az Event Griden keresztül
> * Üzenetek küldése a Service Bus témakörbe
> * Üzenetek fogadása az Azure Functions használatával
> * Üzenetek fogadása a Logic Apps használatával

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:

- [Visual Studio 2017 3-as frissítés (verziószám: 15.3, 26730.01)](https://www.visualstudio.com/vs) vagy újabb.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), 2.0-s vagy újabb verzió.

## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása
Kövesse az oktatóanyag utasításait: [Rövid útmutató: Az Azure Portal használatával hozzon létre egy Service Bus-témakört, és a témakörelőfizetéseit](service-bus-quickstart-topics-subscriptions-portal.md) a következő feladatok elvégzéséhez hajtsa végre:

- Hozzon létre egy **prémium** service bus névteret. 
- Szerezd meg a kapcsolati karakterláncot. 
- Hozzon létre egy Service Bus témakört.
- Hozzon létre két előfizetést a témához. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Mintaalkalmazás előkészítése üzenetek küldéséhez
Az üzenetek Service Bus-témakörbe történő elküldéséhez bármilyen metódust használhat. Az eljárás végén található mintakód feltételezi, hogy a Visual Studio 2017-et használja.

1. Klónozza az [azure-service-bus GitHub-adattárat](https://github.com/Azure/azure-service-bus/).
2. A Visual Studióban lépjen a *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* mappába és nyissa meg az *SBEventGridIntegration.sln* fájlt.
3. Lépjen a **MessageSender** projekthez, és válassza a **Program.cs** fájlt.
4. Töltse ki a Service Bus témakör nevét és az előző lépésből kapott kapcsolati karakterláncot:

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. A program létrehozása és futtatása tesztüzenetek küldéséhez a Service Bus témakörbe. 

## <a name="set-up-a-test-function-on-azure"></a>Tesztfunkció beállítása az Azure-ban 
Mielőtt a teljes forgatókönyvet végigdolgozná, állítson be legalább egy kis tesztfunkciót, amellyel hibakeresést végezhet, és megfigyelheti az eseményeket, amelyek áramlanak. Kövesse az [első függvény létrehozása az Azure Portal cikkben](../azure-functions/functions-create-first-azure-function.md) a következő feladatok elvégzéséhez című útmutatóutasításait: 

1. Hozzon létre egy függvényalkalmazást.
2. HTTP-aktivált függvény létrehozása. 

Ezután tegye a következő lépéseket: 


# <a name="azure-functions-v2"></a>[Azure Functions V2](#tab/v2)

1. Bontsa ki a **Függvények** elemet a fanézetben, és válassza ki a funkciót. Cserélje le a függvény kódját a következő kódra: 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
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
2. Kattintson a **Mentés és futtatás** elemre.

    ![Függvényalkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Válassza **a Függvény URL-címének bekerülése** lehetőséget, és jegyezze fel az URL-címet. 

    ![Függvény URL-címének lekérése](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Azure Functions V1](#tab/v1)

1. Konfigurálja a funkciót a **V1** verzió használatára: 
    1. Válassza ki a függvényalkalmazást a fanézetben, és válassza a **Függvényalkalmazás beállításai**lehetőséget. 

        ![A függvényalkalmazás beállításai]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Válassza a **~1** lehetőséget a **Runtime verzióhoz.** 
2. Bontsa ki a **Függvények** elemet a fanézetben, és válassza ki a funkciót. Cserélje le a függvény kódját a következő kódra: 

    ```csharp
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
4. Kattintson a **Mentés és futtatás** elemre.

    ![Függvényalkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Válassza **a Függvény URL-címének bekerülése** lehetőséget, és jegyezze fel az URL-címet. 

    ![Függvény URL-címének lekérése](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>A függvény és a névtér összekapcsolása az Event Griden keresztül
Ebben a szakaszban a függvényt és a Service Bus-névteret az Azure Portal használatával köti össze. 

Azure Event Grid-előfizetés létrehozásához kövesse az alábbi lépéseket:

1. Az Azure Portalon nyissa meg a névteret, majd a bal oldali ablaktáblában válassza az **Események**lehetőséget. Megjelenik a névtér ablaka, a jobb oldali panelen két Event Grid-előfizetéssel. 
    
    ![Service Bus - események lap](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Válassza **a + Eseményelőfizetés lehetőséget** az eszköztáron. 
3. Az **Esemény-előfizetés létrehozása** lapon tegye a következő lépéseket:
    1. Adja meg az előfizetés **nevét.** 
    2. Válassza a **Web Hook** lehetőséget a **végponttípushoz**. 

        ![Service Bus – Event Grid-előfizetés](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Válassza **a Végpont kiválasztása**lehetőséget, illessze be a függvény URL-címét, majd válassza a Kijelölés **megerősítése**lehetőséget. 

        ![Függvény - válassza ki a végpontot](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Váltson a **Szűrők** lapra, írja be a korábban létrehozott Service Bus-témakör **első előfizetésének** nevét, majd kattintson a **Létrehozás gombra.** 

        ![Esemény-előfizetésszűrő](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Erősítse meg, hogy megjelenik az esemény-előfizetés a listában.

    ![Esemény-előfizetés a listában](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Üzenetek küldése a Service Bus témakörbe
1. Futtassa a .NET C# alkalmazást, amely üzeneteket küld a Service Bus témakörnek. 

    ![Konzolalkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Az Azure függvényalkalmazás lapján bontsa ki a **Functions csomópontot,** bontsa ki a **funkciót,** és válassza a **Figyelő**lehetőséget. 

    ![Monitor függvény](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Üzenetek fogadása az Azure Functions használatával
Az előző szakaszban megtekinthetett egy egyszerű tesztelési és hibakeresési forgatókönyvet, illetve meggyőződhetett az események átvitelének megtörténtéről. 

Ez a szakasz az üzenetek fogadását és feldolgozását ismerteti az események fogadását követően.

### <a name="publish-a-function-from-visual-studio"></a>Függvény közzététele a Visual Studióból
1. Ugyanabban a Visual Studio-megoldásban **(SBEventGridIntegration),** amelyet megnyitott, válassza **a ReceiveMessagesOnEvent.cs** lehetőséget az **SBEventGridIntegration** projektben. 
2. Írja be a Service Bus kapcsolati karakterláncát a következő kódba:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Töltse le a **szolgáltatás közzétételi profilját:**
    1. Válassza ki a függvényalkalmazást. 
    2. Válassza az **Áttekintés** lapot, ha még nincs kijelölve. 
    3. Az eszköztáron válassza a **Közzétételi profil beszereznie** lehetőséget. 

        ![Közzétételi profil beszereznie a függvényt](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Mentse a fájlt a projekt mappájába. 
4. A Visual Studióban kattintson a jobb gombbal az **SBEventGridIntegration** elemre, és válassza a **Közzététel** lehetőséget. 
5. Válassza a **Közzététel** lap **Indítás** a lapján. 
6. A **Közzétételi céllap kiválasztása** lapon tegye a következő lépéseket, és válassza **a Profil importálása**lehetőséget. 

    ![Visual Studio – Profil importálása gomb](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Jelölje ki a korábban letöltött **közzétételi profilfájlt.** 
8. A **Közzététel** lapon válassza a **Közzététel** lehetőséget. 

    ![Visual Studio - Közzététel](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Ellenőrizze, hogy megjelenik-e az új **Azure-függvény, a ReceiveMessagesOnEvent**. Szükség esetén frissítse a lapot. 

    ![Az új függvény létrehozásának ellenőrzése](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Az URL-cím az új függvényhez, és jegyezze fel. 

### <a name="event-grid-subscription"></a>Event Grid-előfizetés

1. A meglévő Event Grid-előfizetés törlése:
    1. A **Service Bus névtér** lapon válassza az **Események** lehetőséget a bal oldali menüben. 
    2. Válassza ki a meglévő esemény-előfizetést. 
    3. Az **Esemény-előfizetés** lapon válassza a **Törlés**lehetőséget.
2. Kövesse a [Függvény és a névtér összekapcsolása az Event Grid en keresztül](#connect-the-function-and-namespace-via-event-grid) szakasz utasításait, és hozzon létre egy Event Grid-előfizetést az új függvény URL-címével.
3. Kövesse az [Üzenetek küldése a Service Bus témakörben](#send-messages-to-the-service-bus-topic) található utasításokat, és küldjön üzeneteket a témakörnek, és figyelje a funkciót. 

## <a name="receive-messages-by-using-logic-apps"></a>Üzenetek fogadása a Logic Apps használatával
Az alábbi lépésekkel csatlakoztasson egy logikai alkalmazást az Azure Service Bus és az Azure Event Grid alkalmazással:

1. Hozzon létre egy logikai alkalmazást az Azure Portalon.
    1. Válassza **a + Erőforrás létrehozása**lehetőséget, válassza az **Integráció**lehetőséget, majd a **Logikai alkalmazás**lehetőséget. 
    2. A **Logikai alkalmazás – Létrehozás** lapon adja meg a logikai alkalmazás **nevét.**
    3. Válassza ki **Azure-előfizetését.** 
    4. Válassza a **Meglévő használata** az **erőforráscsoporthoz**lehetőséget, és válassza ki a korábban létrehozott más erőforrásokhoz (például az Azure függvény, a Service Bus-névtér) használt erőforráscsoportot. 
    5. Válassza ki a logikai alkalmazás **helyét.** 
    6. A logikai alkalmazás létrehozásához válassza a **Létrehozás** gombot. 
2. A **Logic Apps Designer** lapon válassza az **Üres logikai alkalmazás** lehetőséget a Sablonok **csoportban.** 
3. A tervezőn tegye a következő lépéseket:
    1. Keresse meg az **Eseményrácsot**. 
    2. Válassza az **Or Resource event bekövetkezésekor lehetőséget (előzetes verzió) – Azure Event Grid**. 

        ![Logic Apps Designer – válassza az Eseményrács eseményindítójának jelölését](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Válassza **a Bejelentkezés**lehetőséget, adja meg az Azure-hitelesítő adatait, és válassza az Access **engedélyezése**lehetőséget. 
5. Az **Erőforrás-esemény bekövetkeztekor** lapon tegye a következő lépéseket:
    1. Válassza ki az Azure-előfizetését. 
    2. Az **Erőforrástípus mezőben**válassza a **Microsoft.ServiceBus.Namespaces**lehetőséget. 
    3. Az **Erőforrás neve**területen válassza ki a Service Bus névteret. 
    4. Válassza **az Új paraméter hozzáadása**lehetőséget, majd az **Utótagszűrő lehetőséget.** 
    5. **Utótagszűrő esetén**adja meg a második Service Bus-témakör-előfizetés nevét. 
        ![Logic Apps Designer - esemény konfigurálása](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Válassza a **+ Új lépés lehetőséget** a tervezőben, és tegye a következő lépéseket:
    1. Keressen a **Service Bus**.
    2. Válassza a **Service Bus** elemet a listában. 
    3. Jelölje be az **Üzenetek beküldése** a **Műveletek** listában jelölőnégyzetet. 
    4. Válassza **az Üzenetek beküldése témakör-előfizetésből (betekintés-zárolás)** lehetőséget. 

        ![Logic Apps Designer - üzenetek beküldése művelet](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Adja meg **a kapcsolat nevét.** Például: **Üzenetek beszerezni a témakör-előfizetés**, és válassza ki a Service Bus névtér. 

        ![Logic Apps Designer – válassza ki a Service Bus névteret](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Válassza **a RootManageSharedAccessKey lehetőséget.**

        ![Logic Apps Designer – a megosztott hozzáférési kulcs kiválasztása](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Kattintson a **Létrehozás** gombra. 
    8. Válassza ki a témát és az előfizetést. 
    
        ![Logic Apps Designer – válassza ki a Service Bus témakörét és előfizetését](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Válassza a **+ Új lépés**lehetőséget , és tegye a következő lépéseket: 
    1. Válassza a **Service Bus** lehetőséget.
    2. Válassza **az Üzenet befejezése egy témakör-előfizetésben** lehetőséget a műveletek listájából. 
    3. Válassza ki a Service Bus **témakört.**
    4. Válassza ki a témakör második **előfizetését.**
    5. Az **üzenet tokenjének zárolása**mezőben válassza a **Token zárolása lehetőséget** a dinamikus **tartalomból**. 

        ![Logic Apps Designer – válassza ki a Service Bus témakörét és előfizetését](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. A logic app mentéséhez válassza a **Mentés** gombot a Logic Apps Designer eszköztárán. 
9. Kövesse az [Üzenetek küldése a Service Bus témakörben](#send-messages-to-the-service-bus-topic) található utasításokat, és küldjön üzeneteket a témakörnek. 
10. Váltson át a logikai alkalmazás **Áttekintés** lapjára. Láthatja, hogy a logikai alkalmazás fut a **Futtatások előzmények** az elküldött üzenetek.

    ![Logic Apps Designer – a logikai alkalmazás fut](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
