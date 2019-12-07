---
title: Az Azure IoT Central kiterjesztése egyéni szabályokkal és értesítésekkel | Microsoft Docs
description: Megoldás fejlesztőként konfiguráljon egy IoT Central alkalmazást e-mail-értesítések küldéséhez, amikor egy eszköz leállítja a telemetria küldését. Ez a megoldás Azure Stream Analytics, Azure Functions és SendGrid használ.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: bdaa08e8c3b104c7269c1fb4169779d98b4e0880
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895730"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid-preview-features"></a>Az Azure IoT Central kiterjesztése egyéni szabályokkal Stream Analytics, Azure Functions és SendGrid használatával (előzetes verziójú szolgáltatások)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ez az útmutató bemutatja, hogyan bővítheti IoT Central alkalmazását egyéni szabályokkal és értesítésekkel. A példa egy értesítés küldését mutatja be egy operátornak, amikor egy eszköz leállítja a telemetria küldését. A megoldás egy [Azure stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) lekérdezést használ annak észlelésére, hogy egy eszköz leállította-e a telemetria küldését. A Stream Analytics-feladatokhoz [Azure functions](https://docs.microsoft.com/azure/azure-functions/) kell küldenie az értesítő e-maileket a [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)használatával.

Ez a útmutató bemutatja, hogyan bővítheti IoT Central a beépített szabályokkal és műveletekkel, hogy mit tehet.

Ebben a útmutatóban a következőket sajátíthatja el:

* Stream telemetria egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Stream Analytics-lekérdezést, amely észleli, ha egy eszköz leállította az adatküldést.
* Értesítés küldése e-mailben a Azure Functions és a SendGrid Services használatával.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre egy IoT Central alkalmazást az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén a következő beállításokkal:

| Beállítás | Value (Díj) |
| ------- | ----- |
| Fizetési terv | Utólagos, használatalapú fizetés |
| Alkalmazássablon | Áruházbeli elemzés – állapot figyelése |
| Alkalmazásnév | Fogadja el az alapértelmezett értéket, vagy válassza ki a saját nevét |
| URL-cím | Fogadja el az alapértelmezett értéket, vagy válassza ki a saját egyedi URL-előtagját |
| Könyvtár | Azure Active Directory bérlő |
| Azure-előfizetés | Az Azure-előfizetése |
| Region (Régió) | A legközelebbi régió |

A cikkben szereplő példák és Képernyőképek a **Egyesült Államok** régiót használják. Válasszon egy helyet az Ön számára, és győződjön meg róla, hogy az összes erőforrást ugyanabban a régióban hozza létre.

Ez az alkalmazás sablon két szimulált termosztátos eszközt tartalmaz, amelyek telemetria küldenek.

### <a name="resource-group"></a>Erőforráscsoport

A [Azure Portal használatával hozzon létre egy](https://portal.azure.com/#create/Microsoft.ResourceGroup) **DetectStoppedDevices** nevű erőforráscsoportot a létrehozott egyéb erőforrások tárolására. Hozza létre az Azure-erőforrásokat a IoT Central alkalmazással megegyező helyen.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

A [Azure Portal használatával hozzon létre egy Event Hubs névteret](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Value (Díj) |
| ------- | ----- |
| Név    | Adja meg a névtér nevét |
| Díjcsomag | Basic |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Földrajzi egység | USA keleti régiója |
| Adatkapacitás-egységek | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-feladat

A [Azure Portal használatával hozzon létre egy stream Analytics feladatot](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) a következő beállításokkal:

| Beállítás | Value (Díj) |
| ------- | ----- |
| Név    | Válassza ki a feladatokhoz tartozó nevet |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Földrajzi egység | USA keleti régiója |
| Üzemeltetési környezet | Felhőbeli |
| Folyamatos átviteli egységek | 3 |

### <a name="function-app"></a>Függvényalkalmazás

A [Azure Portal használatával hozzon létre egy Function alkalmazást](https://portal.azure.com/#create/Microsoft.FunctionApp) a következő beállításokkal:

| Beállítás | Value (Díj) |
| ------- | ----- |
| App neve    | Válassza ki a Function alkalmazás nevét |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Operációs rendszer | Windows |
| Szolgáltatási csomag | Használatalapú csomag |
| Földrajzi egység | USA keleti régiója |
| Futtatókörnyezet verme | .NET |
| Adattárolás | Új létrehozása |

### <a name="sendgrid-account"></a>SendGrid-fiók

A [Azure Portal használatával hozzon létre egy SendGrid-fiókot](https://portal.azure.com/#create/Sendgrid.sendgrid) a következő beállításokkal:

| Beállítás | Value (Díj) |
| ------- | ----- |
| Név    | Válassza ki a SendGrid-fiók nevét |
| Jelszó | Jelszó létrehozása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Díjcsomag | F1 – Ingyenes |
| Kapcsolattartási adatok | Írja be a szükséges adatokat |

Ha létrehozta az összes szükséges erőforrást, a **DetectStoppedDevices** -erőforráscsoport a következő képernyőképhez hasonlóan néz ki:

![Leállított eszközök erőforráscsoport észlelése](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

IoT Central alkalmazást úgy konfigurálhatja, hogy folyamatosan exportálja a telemetria egy Event hubhoz. Ebben a szakaszban egy Event hub-t hoz létre, amely telemetria fogad a IoT Central alkalmazásból. Az Event hub a telemetria az Stream Analytics-feladatokhoz továbbítja a feldolgozáshoz.

1. A Azure Portal navigáljon a Event Hubs névtérhez, és válassza a **+ Event hub**elemet.
1. Nevezze el az Event hub- **centralexport**, majd válassza a **Létrehozás**lehetőséget.

A Event Hubs névtere a következő képernyőképre hasonlít:

![Event Hubs-névtér](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API-kulcs beolvasása

Az e-mailek küldéséhez a Function alkalmazásnak szüksége van egy SendGrid API-kulcsra. SendGrid API-kulcs létrehozása:

1. A Azure Portal navigáljon a SendGrid-fiókjához. Ezután válassza a **kezelés** lehetőséget a SendGrid-fiók eléréséhez.
1. A SendGrid-fiókban válassza a **Beállítások**, majd az **API-kulcsok**elemet. Válassza az **API-kulcs létrehozása**elemet:

    ![SendGrid API-kulcs létrehozása](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Az **API-kulcs létrehozása** lapon hozzon létre egy **AzureFunctionAccess** nevű kulcsot **teljes hozzáférési** engedélyekkel.
1. Jegyezze fel az API-kulcsot, szüksége lesz rá, amikor konfigurálja a Function alkalmazást.

## <a name="define-the-function"></a>A függvény megadása

Ez a megoldás egy Azure Functions alkalmazást használ e-mail-értesítés küldéséhez, ha a Stream Analytics feladatokban leállított eszköz észlelve. A Function-alkalmazás létrehozása:

1. A Azure Portal navigáljon a **DetectStoppedDevices** erőforráscsoport **app Service** példányához.
1. Új függvény létrehozásához válassza a **+** lehetőséget.
1. A **fejlesztési környezet kiválasztása** lapon válassza **a portálon** , majd a **Folytatás**lehetőséget.
1. A **függvény létrehozása** lapon válassza a **WEBhook + API** elemet, majd kattintson a **Létrehozás**gombra.

A portál létrehoz egy **HttpTrigger1**nevű alapértelmezett függvényt:

![Alapértelmezett HTTP-trigger függvény](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Függvények kötésének konfigurálása

Az e-mailek SendGrid való elküldéséhez a következő módon kell konfigurálnia a függvény kötéseit:

1. Válassza az **integráció**lehetőséget, válassza ki a kimeneti http-t **($Return)** , majd válassza a **Törlés**lehetőséget.
1. Válassza az **+ új kimenet**, majd a **SendGrid**lehetőséget, majd a **kiválasztás**lehetőséget. A SendGrid-bővítmény telepítéséhez válassza a **telepítés** lehetőséget.
1. Ha a telepítés befejeződött, válassza a **függvény visszatérési értékének használata**lehetőséget. Adjon meg egy érvényes **címet** az e-mail-értesítések fogadásához.  Adjon meg egy érvényes **címet** az e-mail feladóként való használathoz.
1. Válassza a **SENDGRID API-kulcs alkalmazás**melletti **új** lehetőséget. Adja meg a **SendGridAPIKey** kulcsot, és a korábban feljegyzett SendGrid API-kulcsot értékként. Ezután kattintson a **Létrehozás** elemre.
1. A **Save (Mentés** ) gombra kattintva mentse a függvény SendGrid-kötéseit.

Az integrálási beállítások a következő képernyőképhez hasonlóan néznek ki:

![Function app-integrációk](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>A függvény kódjának hozzáadása

A függvény megvalósításához adja hozzá a C# kódot a bejövő HTTP-kérelem elemzéséhez, és küldje el az e-maileket a következőképpen:

1. Válassza ki a **HttpTrigger1** függvényt a Function alkalmazásban, C# és cserélje le a kódot a következő kódra:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    Előfordulhat, hogy az új kód mentésekor hibaüzenet jelenik meg.

1. A függvény mentéséhez válassza a **Mentés** lehetőséget.

### <a name="test-the-function-works"></a>A függvény működésének tesztelése

A függvénynek a portálon való teszteléséhez először a Kódszerkesztő alján válassza a **naplók** lehetőséget. Ezután válassza a **teszt** elemet a Kódszerkesztő jobb oldalán. A következő JSON-t használja a **kérelem törzse**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

A függvények naplójának üzenetei a **naplók** panelen jelennek meg:

![Függvény naplójának kimenete](media/howto-create-custom-rules/function-app-logs.png)

Néhány perc elteltével az e- **mail-címe** megkapja az alábbi tartalommal ellátott e-mailt:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics lekérdezés hozzáadása

Ez a megoldás egy Stream Analytics lekérdezést használ annak észlelésére, hogy egy eszköz nem küld-e telemetria több mint 120 másodpercig. A lekérdezés az Event hub telemetria használja bemenetként. A feladat elküldi a lekérdezés eredményeit a Function alkalmazásnak. Ebben a szakaszban a Stream Analytics feladatot konfigurálja:

1. A Azure Portal navigáljon a Stream Analytics feladathoz a **feladatok topológia** területen válassza a **bemenetek**lehetőséget, válassza a **+ stream-bemenet hozzáadása**lehetőséget, majd válassza az **Event hub**elemet.
1. A következő táblázatban található információk segítségével konfigurálja a bemenetet a korábban létrehozott Event hub használatával, majd válassza a **Mentés**lehetőséget:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Bemeneti alias | centraltelemetry |
    | Előfizetés | Az Ön előfizetése |
    | Event Hubs-névtér | Az Event hub-névtér |
    | Eseményközpont neve | Meglévő **centralexport** használata |

1. A **feladatok topológia**területen válassza a **kimenetek**, majd a **+ Hozzáadás**, majd az **Azure-függvény**lehetőséget.
1. A következő táblázatban található információk segítségével konfigurálja a kimenetet, majd válassza a **Mentés**lehetőséget:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Kimeneti alias | emailnotification |
    | Előfizetés | Az Ön előfizetése |
    | Függvényalkalmazás | A Function alkalmazás |
    | Függvény  | HttpTrigger1 |

1. A **feladatok topológia**területen válassza a **lekérdezés** lehetőséget, és cserélje le a meglévő lekérdezést a következő SQL-re:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Kattintson a **Mentés** gombra.
1. A Stream Analytics-feladatok elindításához válassza az **Áttekintés**, majd a **Start** **, majd a**Start lehetőséget, majd **indítsa**el a következőt:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Az Exportálás konfigurálása IoT Central

Az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén navigáljon a contoso-sablonból létrehozott IoT Central alkalmazáshoz. Ebben a szakaszban úgy konfigurálja az alkalmazást, hogy a szimulált eszközökről az telemetria továbbítsa az alkalmazást. Az Exportálás konfigurálása:

1. Navigáljon az **adatexportálás** lapra, válassza az **+ új**, majd az **Azure Event Hubs**elemet.
1. Az Exportálás konfigurálásához használja a következő beállításokat, majd válassza a **Mentés**lehetőséget:

    | Beállítás | Value (Díj) |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás Event Hubsba |
    | Engedélyezve | Be |
    | Event Hubs-névtér | Az Event Hubs névtér neve |
    | Eseményközpont | centralexport |
    | Mérések | Be |
    | Eszközök | Ki |
    | Eszközsablonok | Ki |

![Folyamatos adatexportálási konfiguráció](media/howto-create-custom-rules/cde-configuration.png)

A folytatás előtt várjon, amíg az Exportálás állapota **fut** .

## <a name="test"></a>Tesztelés

A megoldás teszteléséhez letilthatja a folyamatos adatexportálást IoT Centralról szimulált leállított eszközökre:

1. A IoT Central alkalmazásban navigáljon az **adatexportálás** lapra, és válassza az **Exportálás Event Hubs** exportálási konfigurációt.
1. Válassza **ki** az **engedélyezve** lehetőséget, majd kattintson a **Mentés**gombra.
1. Legalább két perc elteltével az e-mail-cím egy vagy **több olyan e** -mailt kap, amely az alábbi példához hasonlóan néz ki:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Kitakarítás

A fenti útmutató és a szükségtelen költségek elkerülése érdekében törölje a **DetectStoppedDevices** erőforráscsoportot a Azure Portal.

A IoT Central alkalmazást a **felügyeleti** lapról törölheti az alkalmazáson belül.

## <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan végezheti el a következőket:

* Stream telemetria egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Stream Analytics-lekérdezést, amely észleli, ha egy eszköz leállította az adatküldést.
* Értesítés küldése e-mailben a Azure Functions és a SendGrid Services használatával.

Most, hogy már tudja, hogyan hozhat létre egyéni szabályokat és értesítéseket, a javasolt következő lépés az [Azure-IoT Central egyéni elemzésekkel való bővítésének](howto-create-custom-analytics.md)megismerése.
