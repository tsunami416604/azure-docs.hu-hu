---
title: 'Oktatóanyag: Azure Service Bus Event Grid integrációs példák'
description: 'Oktatóanyag: Ez a cikk Service Bus üzenetkezelési és Event Grid integrációs példákat tartalmaz.'
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
ms.openlocfilehash: d3f523bbc0236536734f21870474492a61532ada
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719023"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Oktatóanyag: válaszadás a Azure Event Grid által a Azure Functions és a Azure Logic Apps használatával kapott Azure Service Bus eseményekre
Ebből az oktatóanyagból megtudhatja, hogyan válaszolhat a Azure Event Grid használatával fogadott Azure Service Bus eseményekre Azure Functions és Azure Logic Apps használatával. A következő lépéseket kell végrehajtania:
 
- Hozzon létre egy teszt Azure-függvényt a hibakereséshez, és tekintse meg az események kezdeti áramlását a Event Grid.
- Hozzon létre egy Azure-függvényt Azure Service Bus üzenetek fogadásához és feldolgozásához Event Grid események alapján.
- Logikai alkalmazás létrehozása Event Grid eseményekre való válaszadáshoz

A Service Bus, Event Grid, Azure Functions és Logic Apps összetevők létrehozása után a következő műveleteket hajtja végre: 

1. Üzenetek küldése egy Service Bus témakörnek. 
2. Ellenőrizze, hogy a témakör előfizetései fogadták-e az üzeneteket
3. Ellenőrizze, hogy az eseményre előfizetett függvény vagy logikai alkalmazás fogadta-e az eseményt. 

## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása
Kövesse az oktatóanyag lépéseit: gyors útmutató [: a Azure Portal használatával hozzon létre egy Service Bus témakört és előfizetéseket a témakörbe](service-bus-quickstart-topics-subscriptions-portal.md) a következő feladatok elvégzéséhez:

- Hozzon létre egy **prémium** szintű Service Bus névteret. 
- A kapcsolatok karakterláncának beolvasása. 
- Hozzon létre egy Service Bus témakört.
- Hozzon létre két előfizetést a témakörben. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Minta alkalmazás előkészítése üzenetek küldéséhez
Az üzenetek Service Bus-témakörbe történő elküldéséhez bármilyen metódust használhat. Az eljárás végén található mintakód azt feltételezi, hogy a Visual Studio 2017-et használja.

1. Klónozza az [azure-service-bus GitHub-adattárat](https://github.com/Azure/azure-service-bus/).
2. A Visual Studióban lépjen a *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* mappába és nyissa meg az *SBEventGridIntegration.sln* fájlt.
3. Lépjen a **MessageSender** projekthez, és válassza a **Program.cs** fájlt.
4. Adja meg a Service Bus témakör nevét és az előző lépésből kapott, a következőhöz tartozó kapcsolatok karakterláncot:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Hozza létre és futtassa a programot, hogy tesztüzenet küldjön a Service Bus témakörbe. 

## <a name="set-up-a-test-function-on-azure"></a>Tesztelési függvény beállítása az Azure-ban 
Mielőtt elkezdené a teljes forgatókönyvet, állítson be legalább egy kis teszt funkciót, amellyel hibakeresést végezhet, és megfigyelheti a folyó eseményeket. Kövesse az [első függvény létrehozása a Azure Portal](../azure-functions/functions-create-first-azure-function.md) cikkben található utasításokat a következő feladatok elvégzéséhez: 

1. Hozzon létre egy Function alkalmazást.
2. Hozzon létre egy HTTP által aktivált függvényt. 

Ezután hajtsa végre a következő lépéseket: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions v2](#tab/v2)

1. Bontsa ki a **függvények** csomópontot a fanézetben, majd válassza ki a függvényt. Cserélje le a függvény kódját a következő kódra: 

    ```CSharp
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

    ![Függvény alkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Válassza a **függvény URL-címének lekérése** és az URL-cím lekérése 

    ![Függvény URL-címének beolvasása](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions v1](#tab/v1)

1. Konfigurálja a függvényt a **v1** verzió használatára: 
    1. Válassza ki a Function alkalmazást a fanézetben, és válassza a **Function app Settings**elemet. 

        ![A függvényalkalmazás beállításai]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Válassza a **~ 1** lehetőséget a **futtatókörnyezet verziójának**kiválasztásához. 
2. Bontsa ki a **függvények** csomópontot a fanézetben, majd válassza ki a függvényt. Cserélje le a függvény kódját a következő kódra: 

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
4. Kattintson a **Mentés és futtatás** elemre.

    ![Függvény alkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Válassza a **függvény URL-címének lekérése** és az URL-cím lekérése 

    ![Függvény URL-címének beolvasása](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>A függvény és a névtér összekapcsolása az Event Griden keresztül
Ebben a szakaszban a függvényt és a Service Bus névteret a Azure Portal használatával köti össze. 

Azure Event Grid előfizetés létrehozásához kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a névteret, majd a bal oldali panelen válassza az **események**lehetőséget. Megjelenik a névtér ablaka, a jobb oldali panelen két Event Grid-előfizetéssel. 
    
    ![Service Bus – események lap](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Válassza az **+ esemény-előfizetés** lehetőséget az eszköztáron. 
3. Az **esemény-előfizetés létrehozása** oldalon hajtsa végre a következő lépéseket:
    1. Adja meg az előfizetés **nevét** . 
    2. Válassza a **webes Hook** lehetőséget a **végpont típusához**. 

        ![Service Bus – Event Grid előfizetés](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. **Válasszon ki egy végpontot**, illessze be a függvény URL-címét, majd válassza a **kijelölés megerősítése**lehetőséget. 

        ![Függvény – a végpont kiválasztása](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Váltson a **szűrők** lapra, adja meg az **első előfizetés** nevét a korábban létrehozott Service Bus témakörhöz, majd kattintson a **Létrehozás** gombra. 

        ![Esemény-előfizetés szűrője](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Ellenőrizze, hogy megjelenik-e az esemény-előfizetés a listában.

    ![Esemény-előfizetés a listában](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Üzenetek küldése a Service Bus témakörnek
1. Futtassa a .NET C# -alkalmazást, amely üzeneteket küld a Service Bus témakörnek. 

    ![Konzolos alkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Az Azure Function alkalmazás lapján bontsa ki a **függvények**, majd a **függvény**csomópontot, és kattintson a **figyelés**elemre. 

    ![Függvény figyelése](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Üzenetek fogadása az Azure Functions használatával
Az előző szakaszban megtekinthetett egy egyszerű tesztelési és hibakeresési forgatókönyvet, illetve meggyőződhetett az események átvitelének megtörténtéről. 

Ez a szakasz az üzenetek fogadását és feldolgozását ismerteti az események fogadását követően.

### <a name="publish-a-function-from-visual-studio"></a>Függvény közzététele a Visual studióból
1. Ugyanebben a Visual Studio-megoldásban (**SBEventGridIntegration**) válassza a **ReceiveMessagesOnEvent.cs** lehetőséget a **SBEventGridIntegration** projektben. 
2. Adja meg a Service Bus-kapcsolatok karakterláncát a következő kódban:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Töltse le a következő függvény **közzétételi profilját** :
    1. Válassza ki a Function alkalmazást. 
    2. Ha még nincs kiválasztva, válassza az **Áttekintés** lapot. 
    3. Válassza a **közzétételi profil beolvasása** lehetőséget az eszköztáron. 

        ![A függvény közzétételi profiljának beolvasása](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Mentse a fájlt a projekt mappájába. 
4. A Visual Studióban kattintson a jobb gombbal az **SBEventGridIntegration** elemre, és válassza a **Közzététel** lehetőséget. 
5. Kattintson a **Start gombra** a **közzétételi** lapon. 
6. A **Közzététel célhelyének** kiválasztása lapon hajtsa végre a következő lépéseket, majd válassza a **profil importálása**lehetőséget. 

    ![Visual Studio – profil importálása gomb](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Válassza ki a korábban letöltött **közzétételi profil fájlt** . 
8. Válassza a **Közzététel** elemet a **közzétételi** oldalon. 

    ![Visual Studio – közzététel](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Ellenőrizze, hogy megjelenik-e az új Azure Function **ReceiveMessagesOnEvent**. Szükség esetén frissítse az oldalt. 

    ![Győződjön meg arról, hogy az új függvény létrejött](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Töltse le az új függvény URL-címét, és jegyezze fel. 

### <a name="event-grid-subscription"></a>Előfizetés Event Grid

1. Törölje a meglévő Event Grid-előfizetést:
    1. A **Service Bus névtér** lapon válassza a bal oldali menü **események** elemét. 
    2. Válassza ki a meglévő esemény-előfizetést. 
    3. Az **esemény-előfizetés** lapon válassza a **Törlés**lehetőséget.
2. Az új függvény URL-címével hozzon létre egy Event Grid-előfizetést a következő témakörben ismertetett utasítások alapján: a [függvény és a névtér Összekapcsolásának Event Grid](#connect-the-function-and-namespace-via-event-grid)
3. Az üzenetek [küldése a Service Bus témakörbe](#send-messages-to-the-service-bus-topic) című részben található utasításokat követve üzeneteket küldhet a témakörnek, és figyelheti a függvényt. 

## <a name="receive-messages-by-using-logic-apps"></a>Üzenetek fogadása a Logic Apps használatával
Az alábbi lépéseket követve összekapcsolhatók a logikai alkalmazások Azure Service Bus és Azure Event Gridkal:

1. Hozzon létre egy logikai alkalmazást a Azure Portal.
    1. Válassza az **+ erőforrás létrehozása**lehetőséget, válassza az **integráció**, majd a **logikai alkalmazás**lehetőséget. 
    2. A **logikai alkalmazás létrehozása** lapon adja meg a logikai alkalmazás **nevét** .
    3. Jelölje ki az Azure-**előfizetést**. 
    4. Válassza az **erőforráscsoport** **meglévő használata** lehetőséget, majd válassza ki azt az erőforráscsoportot, amelyet a korábban létrehozott más erőforrásokhoz (például az Azure-függvényhez, Service Bus névtérhez) használt. 
    5. Válassza ki a logikai alkalmazás **helyét** . 
    6. Válassza a **Létrehozás** lehetőséget a logikai alkalmazás létrehozásához. 
2. A **Logic apps Designer** lapon válassza a **sablon**alatt az **üres logikai alkalmazás** lehetőséget. 
3. A tervezőben hajtsa végre a következő lépéseket:
    1. **Event Grid**keresése. 
    2. Válassza ki **az erőforrás-esemény bekövetkezésekor (előzetes verzió) – Azure Event Grid**. 

        ![Logic Apps Designer – Event Grid trigger kiválasztása](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Válassza a **Bejelentkezés**lehetőséget, adja meg az Azure-beli hitelesítő adatait, majd válassza a **hozzáférés engedélyezése**lehetőséget. 
5. Az **erőforrás-esemény bekövetkeztekor** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki az Azure-előfizetését. 
    2. Az **erőforrástípus**mezőben válassza a **Microsoft. ServiceBus. névterek**lehetőséget. 
    3. Az **Erőforrás neve**mezőben válassza ki a Service Bus névteret. 
    4. Válassza az **új paraméter hozzáadása**lehetőséget, majd válassza az **utótag-szűrő**elemet. 
    5. Az **utótag-szűrő**mezőben adja meg a második Service Bus témakör-előfizetés nevét. 
        ![Logic Apps Designer – esemény konfigurálása](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Válassza az **+ új lépés** lehetőséget a tervezőben, és hajtsa végre a következő lépéseket:
    1. **Service Bus**keresése.
    2. Válassza ki **Service Bus** a listában. 
    3. Válassza az **üzenetek beolvasása** lehetőséget a **műveletek** listában. 
    4. Válassza az **üzenetek beolvasása egy témakör-előfizetésből (betekintési zárolás)** lehetőséget. 

        ![Logic Apps Designer – üzenetek beolvasása művelet](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Adja meg **a kapcsolatok nevét**. Például: **üzenetek beolvasása az előfizetés témakörből**, majd válassza ki a Service Bus névteret. 

        ![Logic Apps Designer – válassza ki a Service Bus névteret](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Válassza a **RootManageSharedAccessKey**lehetőséget.

        ![Logic Apps Designer – válassza ki a megosztott elérési kulcsot](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Kattintson a **Létrehozás** gombra. 
    8. Válassza ki a témakört és az előfizetést. 
    
        ![Logic Apps Designer – válassza ki Service Bus témakört és előfizetést](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Válassza az **+ új lépés**lehetőséget, majd hajtsa végre a következő lépéseket: 
    1. Válassza a **Service Bus** lehetőséget.
    2. Válassza ki **az üzenet végrehajtása témakör-előfizetésben** a műveletek listájában. 
    3. Válassza ki a Service Bus **témakört**.
    4. Válassza ki a második **előfizetést** a témakörhöz.
    5. **Az üzenet zárolási jogkivonatának**kiválasztásához válassza a **jogkivonat zárolása** elemet a **dinamikus tartalomban**. 

        ![Logic Apps Designer – válassza ki Service Bus témakört és előfizetést](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. A logikai alkalmazás mentéséhez kattintson a **Mentés** gombra a Logic apps Designer eszköztárán. 
9. Az üzenetek [küldése a Service Bus témakörbe](#send-messages-to-the-service-bus-topic) című rész útmutatását követve üzeneteket küldhet a témakörbe. 
10. Váltson a logikai alkalmazás **Áttekintés** lapjára. Megjelenik a logikai alkalmazás futtatása a **futtatási előzményekben** az elküldött üzeneteknél.

    ![Logic Apps Designer – logikai alkalmazás futtatása](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
