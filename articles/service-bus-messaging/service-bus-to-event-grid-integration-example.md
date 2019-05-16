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
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b29798bb87b7c5c677e7d80e552e45e8d1290541
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65754757"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Válaszoljon az Azure Functions és az Azure Logic Apps használatával az Azure Event Grid-n keresztül fogadott Azure Service Bus-események
Ebben az oktatóanyagban elsajátíthatja, hogyan válaszol az Azure Service Bus-események Azure Event Grid-n keresztül az Azure Functions és az Azure Logic Apps által kapott. A következőket teheti:
 
- A hibakereséshez és az Event Grid-eseményfolyam kezdeti folyamat tesztelési Azure-függvény létrehozása.
- Hozzon létre egy Azure-függvényt az Event Grid-események alapján az Azure Service Bus-üzenetek fogadásához és feldolgozásához.
- Event Grid-események válaszolni a logikai alkalmazás létrehozása

Miután létrehozta a Service Bus Event Grid, Azure Functions és a Logic Apps-összetevők, hajtsa végre a következőket: 

1. Üzenetek küldése egy Service Bus-témakörbe. 
2. Győződjön meg arról, hogy az előfizetések a témakörbe beérkezett ezeket az üzeneteket
3. Győződjön meg arról, hogy a függvény vagy a logikai alkalmazás, amelyre az esemény előfizetett kapott az eseményt. 

## <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása
Ez az oktatóanyag utasításait követve: [Rövid útmutató: Az Azure portal használatával hozzon létre egy Service Bus-témakörbe, és a témakörbe az előfizetések](service-bus-quickstart-topics-subscriptions-portal.md) a következő feladatokat végezheti el:

- Hozzon létre egy **prémium** Service Bus-névteret. 
- Kérje le a kapcsolati karakterláncot. 
- Hozzon létre egy Service Bus-témakörbe.
- Hozzon létre a témakört két előfizetéssel. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Üzenetek küldése egy mintaalkalmazást előkészítése
Az üzenetek Service Bus-témakörbe történő elküldéséhez bármilyen metódust használhat. Ez az eljárás végén található mintakód feltételezi, hogy a Visual Studio 2017-et használ.

1. Klónozza az [azure-service-bus GitHub-adattárat](https://github.com/Azure/azure-service-bus/).
2. A Visual Studióban lépjen a *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration* mappába és nyissa meg az *SBEventGridIntegration.sln* fájlt.
3. Lépjen a **MessageSender** projekthez, és válassza a **Program.cs** fájlt.
4. Adja meg a Service Bus-témakör nevét és az előző lépésben kapott kapcsolati karakterláncot:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Hozhat létre, és futtassa a programot teszt üzeneteket küldeni a Service Bus-témakörbe. 

## <a name="set-up-a-test-function-on-azure"></a>Az Azure-ban tesztfüggvény beállítása 
Mielőtt a teljes forgatókönyv dolgozik, állítsa be legalább egy rövid tesztet függvény, amely segítségével hibakeresést, és figyelje meg az események átvitele. Kövesse az utasításokat a [az első függvény létrehozása az Azure Portalon](../azure-functions/functions-create-first-azure-function.md) a cikk a következő feladatokat végezheti el: 

1. Hozzon létre egy függvényalkalmazást.
2. Hozzon létre egy HTTP által aktivált függvényt. 

Ezután tegye a következőket: 


# <a name="azure-functions-v2tabv2"></a>[Az Azure Functions V2](#tab/v2)

1. Bontsa ki a **funkciók** a fanézetben megtekintheti, és válassza ki a függvényt. Cserélje le a függvény kódját a következő kódot: 

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

    ![Függvény-alkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Válassza ki **függvény URL-Címének lekérése** és jegyezze fel az URL-címet. 

    ![Függvény URL-címének beolvasása](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. Konfigurálja a használni kívánt függvény **V1** verziója: 
    1. A faszerkezetes nézetben válassza ki a függvényalkalmazást, és válassza ki **Alkalmazásbeállítások függvény**. 

        ![Függvényalkalmazás beállításai]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Válassza ki **~ 1** a **verze modulu Runtime**. 
2. Bontsa ki a **funkciók** a fanézetben megtekintheti, és válassza ki a függvényt. Cserélje le a függvény kódját a következő kódot: 

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

    ![Függvény-alkalmazás kimenete](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Válassza ki **függvény URL-Címének lekérése** és jegyezze fel az URL-címet. 

    ![Függvény URL-címének beolvasása](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>A függvény és a névtér összekapcsolása az Event Griden keresztül
Ebben a szakaszban összekapcsoljuk a függvényt, és a Service Bus-névtér az Azure portal használatával. 

Az Azure Event Grid-előfizetés létrehozásához kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen a névteréhez, és ezt követően a bal oldali ablaktáblán válassza **események**. Megjelenik a névtér ablaka, a jobb oldali panelen két Event Grid-előfizetéssel. 
    
    ![A Service Bus - események lap](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Válassza ki **+ esemény-előfizetés** az eszköztáron. 
3. Az a **esemény-előfizetés létrehozása** lapon, tegye a következőket:
    1. Adjon meg egy **neve** az előfizetéshez. 
    2. Válassza ki **Webhook** a **típusú végpont**. 

        ![A Service Bus – Event Grid-előfizetés](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Kiválasztott **válasszon végpontot**illessze be a függvény URL-CÍMÉT, és válassza ki **kijelölés megerősítéséhez**. 

        ![Függvény - válassza ki a végpontot](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Váltson a **szűrők** lapra, adja meg a nevét a **első előfizetés** a Service Bus-témakörbe létrehozott korábbi, és adja meg a **létrehozás** gombra. 

        ![Esemény-előfizetés-szűrő](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Győződjön meg arról, hogy megjelenik-e a listában az esemény-előfizetés.

    ![A listában az esemény-előfizetés](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Üzenetek küldése a Service Bus-témakörbe
1. Futtatás a .NET C# alkalmazás, amely üzeneteket küld a Service Bus-témakörbe. 

    ![Alkalmazás konzolkimenet](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Az Azure-függvényalkalmazás lapon bontsa ki a **funkciók**, bontsa ki a **függvény**, és válassza ki **figyelő**. 

    ![A figyelő függvény](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Üzenetek fogadása az Azure Functions használatával
Az előző szakaszban megtekinthetett egy egyszerű tesztelési és hibakeresési forgatókönyvet, illetve meggyőződhetett az események átvitelének megtörténtéről. 

Ez a szakasz az üzenetek fogadását és feldolgozását ismerteti az események fogadását követően.

### <a name="publish-a-function-from-visual-studio"></a>Tegyen közzé egy függvényt a Visual Studióból
1. Ugyanabban a Visual Studio-megoldásban (**SBEventGridIntegration**), amely megnyitotta, jelölje be **ReceiveMessagesOnEvent.cs** a a **SBEventGridIntegration** projekt. 
2. Adja meg a Service Bus kapcsolati karakterlánc a következő kódot:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Töltse le a **közzétételi profil** a függvény:
    1. Válassza ki a függvényalkalmazást. 
    2. Válassza ki a **áttekintése** lapon, ha még nincs kiválasztva. 
    3. Válassza ki **közzétételi profil letöltése** az eszköztáron. 

        ![Közzétételi profil a függvény lekérése](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Mentse a fájlt a projektmappa fájllistájának. 
4. A Visual Studióban kattintson a jobb gombbal az **SBEventGridIntegration** elemre, és válassza a **Közzététel** lehetőséget. 
5. Válassza ki *Start** a a **közzététel** lapot. 
6. Az a **válasszon egy közzétételi célként** lapon, válassza ki a következő lépések **profil importálása**. 

    ![A Visual Studio - profil importálása gomb](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Válassza ki a **profilfájl közzététele** korábban letöltött. 
8. Válassza ki **közzététel** a a **közzététel** lapot. 

    ![Visual Studio - Publish](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Győződjön meg arról, hogy megjelenik-e az új Azure-függvény **ReceiveMessagesOnEvent**. Ha szükséges, frissítse az oldalt. 

    ![Győződjön meg arról, hogy az új függvény létrehozása](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Az új függvény URL-címére, és jegyezze fel azt le. 

### <a name="event-grid-subscription"></a>Event Grid-előfizetés

1. Törölje a meglévő Event Grid-előfizetést:
    1. Az a **Service Bus-Namespace** lapon jelölje be **események** a bal oldali menüben. 
    2. Válassza ki a meglévő esemény-előfizetés. 
    3. Az a **esemény-előfizetés** lapon jelölje be **törlése**.
2. Kövesse az utasításokat a [a függvény és a névtér Event Griden keresztül összekapcsolása](#connect-the-function-and-namespace-via-event-grid) szakasz hozhat létre egy Event Grid-előfizetés az új függvény URL-cím használatával.
3. Kövesse az utasításokat a a [üzeneteket küldeni a Service Bus-témakörbe](#send-messages-to-the-service-bus-topic) szakasz üzenetek küldése az üzenettémához, és figyelheti a függvényt. 

## <a name="receive-messages-by-using-logic-apps"></a>Üzenetek fogadása a Logic Apps használatával
Logikai alkalmazás csatlakoztatása az Azure Service Bus és az Azure Event Grid az alábbi lépéseket:

1. Logikai alkalmazás létrehozása az Azure Portalon.
    1. Válassza ki **+ erőforrás létrehozása**válassza **integrációs**, majd válassza ki **logikai alkalmazás**. 
    2. Az a **Logikaialkalmazás - létrehozása** lap, adja meg egy **neve** a logikai alkalmazás.
    3. Jelölje ki az Azure-**előfizetést**. 
    4. Válassza ki **meglévő használata** számára a **erőforráscsoport**, és válassza ki az erőforráscsoportot, amelyet korábban létrehozott források (pl. Azure-függvény, Service Bus-névtér) használt. 
    5. Válassza ki a **hely** a logikai alkalmazás. 
    6. Válassza ki **létrehozás** a logikai alkalmazás létrehozásához. 
2. Az a **Logic Apps Designerben** lapon jelölje be **üres logikai alkalmazás** alatt **sablonok**. 
3. A tervezőben tegye a következőket:
    1. Keresse meg **Event Grid**. 
    2. Válassza ki **erőforrás-esemény bekövetkezésekor (előzetes verzió) – Azure Event Grid**. 

        ![Logic Apps Designer – Event Grid eseményindító kiválasztása](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Válassza ki **jelentkezzen be a**, adja meg Azure hitelesítő adatait, és válassza ki **hozzáférés engedélyezése**. 
5. Az a **erőforrás-esemény bekövetkezésekor** lapon, tegye a következőket:
    1. Válassza ki az Azure-előfizetését. 
    2. A **erőforrástípus**válassza **Microsoft.ServiceBus.Namespaces**. 
    3. A **erőforrásnév**, válassza ki a Service Bus-névtér. 
    4. Válassza ki **új paraméter hozzáadása**, és válassza ki **Utótagszűrő**. 
    5. A **Utótagszűrő**, adja meg a nevét, a második a Service Bus témakör-előfizetésbe. 

        ![Logic Apps Designer – esemény konfigurálása](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Válassza ki **+ új lépés** a tervezőben, és kövesse az alábbi lépéseket:
    1. Keresse meg **Service Bus**.
    2. Válassza ki **a Service Bus** a listában. 
    3. Válassza a **üzeneteket** a a **műveletek** listája. 
    4. Válassza ki **üzenetek beolvasása témafeliratkozásból (zárolás betekintésre)**. 

        ![Logic Apps Designer – get-üzenetek művelet](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Adjon meg egy **a kapcsolat neve**. Példa: **Üzenetek beolvasása a témakör-előfizetésből**, és válassza ki a Service Bus-névteret. 

        ![Logic Apps Designer – válassza ki a Service Bus-névtér](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Válassza ki **RootManageSharedAccessKey**.

        ![Logic Apps Designer – válassza ki a közös hozzáférési kulcs](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Kattintson a **Létrehozás** gombra. 
    8. Válassza ki a témakör és előfizetés. 
    
        ![Logic Apps Designer – válassza ki a Service Bus-témakör és előfizetés](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Válassza ki **+ új lépés**, és kövesse az alábbi lépéseket: 
    1. Válassza a **Service Bus** lehetőséget.
    2. Válassza ki **egy témakör-előfizetésben lévő üzenet feldolgozottnak** műveletek listájában. 
    3. Válassza ki a Service Bus **témakör**.
    4. Válassza ki a második **előfizetés** a témakörbe.
    5. A **az üzenet zárolási jogkivonata**válassza **zárolási jogkivonat** származó a **dinamikus tartalom**. 

        ![Logic Apps Designer – válassza ki a Service Bus-témakör és előfizetés](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Válassza ki **mentése** a logikai alkalmazás mentéséhez, a Logic Apps Designerben az eszköztáron. 
9. Kövesse az utasításokat a a [üzeneteket küldeni a Service Bus-témakörbe](#send-messages-to-the-service-bus-topic) szakasz a témakörbe való üzenetküldéshez. 
10. Váltson a **áttekintése** a logikai alkalmazás lapján. A logikai alkalmazás fog látni a **futtatási előzmények** küldött üzenetek.

    ![Logic Apps Designer – logikaialkalmazás-futtatások](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

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
