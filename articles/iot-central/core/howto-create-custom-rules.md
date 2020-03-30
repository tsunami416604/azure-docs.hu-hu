---
title: Az Azure IoT Central kiterjesztése egyéni szabályokkal és értesítésekkel | Microsoft dokumentumok
description: Megoldásfejlesztőként konfiguráljon egy IoT Central-alkalmazást e-mail értesítések küldésére, ha egy eszköz leállítja a telemetriai adatok küldését. Ez a megoldás az Azure Stream Analytics, az Azure Functions és a SendGrid függvényeket használja.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 0e161cf83662df671b8cfb100ddc12c3b3e7359f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158146"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Az Azure IoT Central kiterjesztése egyéni szabályokkal a Stream Analytics, az Azure Functions és a SendGrid használatával



Ez az útmutató bemutatja, hogy megoldásfejlesztőként hogyan bővítheti az IoT Central-alkalmazást egyéni szabályokkal és értesítésekkel. A példa bemutatja egy értesítést küld egy operátor, ha egy eszköz leállítja a telemetriai adatok küldését. A megoldás egy [Azure Stream Analytics-lekérdezést](https://docs.microsoft.com/azure/stream-analytics/) használ annak észlelésére, ha egy eszköz leállította a telemetriai adatok küldését. A Stream Analytics-feladat az [Azure Functions segítségével](https://docs.microsoft.com/azure/azure-functions/) küld küld küldjön értesítési e-maileket a [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)használatával.

Ez az útmutató bemutatja, hogyan terjesztheti ki az IoT Central túl azon túl, amit a beépített szabályokkal és műveletekkel már megtehet.

Ebben az útmutatóútmutatóban megtudhatja, hogyan:

* Telemetriai adatok streamelése egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Stream Analytics-lekérdezést, amely észleli, ha egy eszköz leállította az adatok küldését.
* Küldjön egy e-mail értesítést az Azure Functions és sendgrid szolgáltatások használatával.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépései végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre egy IoT Central alkalmazást az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Árképzési terv | Standard |
| Alkalmazássablon | Üzleten belüli elemzés – állapotfigyelés |
| Alkalmazásnév | Fogadja el az alapértelmezettet, vagy válassza ki a saját nevét |
| URL-cím | Az alapértelmezett érték elfogadása vagy saját egyedi URL-előtag kiválasztása |
| Címtár | Az Azure Active Directory bérlője |
| Azure-előfizetés | Az Azure-előfizetése |
| Régió | A legközelebbi régió |

A jelen cikkben szereplő példák és képernyőképek az **Egyesült Államok régióját** használják. Válasszon egy önhöz közeli helyet, és győződjön meg arról, hogy az összes erőforrást ugyanabban a régióban hozza létre.

Ez az alkalmazássablon két szimulált termosztáteszközt tartalmaz, amelyek telemetriai adatokat küldenek.

### <a name="resource-group"></a>Erőforráscsoport

Az Azure Portal használatával [hozzon létre egy](https://portal.azure.com/#create/Microsoft.ResourceGroup) **detectStoppedDevices** nevű erőforráscsoportot a többi létrehozott erőforrás tárolására. Hozzon létre az Azure-erőforrások ugyanazon a helyen, mint az IoT Central alkalmazás.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

Az Azure Portal használatával [hozzon létre egy Event Hubs névteret](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A névtér nevének kiválasztása |
| Tarifacsomag | Basic |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Megállított eszközök észlelése) |
| Hely | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-feladat

Az [Azure Portal használatával streamelési feladatot hozhat létre](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A feladat nevének kiválasztása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Megállított eszközök észlelése) |
| Hely | USA keleti régiója |
| Üzemeltetési környezet | Felhő |
| Streamelési egységek | 3 |

### <a name="function-app"></a>Függvényalkalmazás

Az Azure Portal használatával [hozzon létre egy függvényalkalmazást](https://portal.azure.com/#create/Microsoft.FunctionApp) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| App neve    | A függvényalkalmazás nevének kiválasztása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Megállított eszközök észlelése) |
| Operációs rendszer | Windows |
| Szolgáltatási csomag | Használatalapú csomag |
| Hely | USA keleti régiója |
| Futtatókörnyezet verme | .NET |
| Storage | Új létrehozása |

### <a name="sendgrid-account"></a>SendGrid-fiók

Az Azure Portal on [hozzon létre egy SendGrid-fiókot](https://portal.azure.com/#create/Sendgrid.sendgrid) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A SendGrid-fiók nevének kiválasztása |
| Jelszó | Jelszó létrehozása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices (Megállított eszközök észlelése) |
| Tarifacsomag | F1 – Ingyenes |
| Kapcsolattartási adatok | A szükséges adatok kitöltése |

Miután létrehozta az összes szükséges erőforrást, a **DetectStoppedDevices** erőforráscsoport a következő képernyőképen jelenik meg:

![Leállított eszközök erőforráscsoport észlelése](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Konfigurálhatja az IoT Central-alkalmazást, hogy folyamatosan exportálja a telemetriai adatokat egy eseményközpontba. Ebben a szakaszban hozzon létre egy eseményközpontot az IoT Central-alkalmazás telemetriai adatainak fogadásához. Az eseményközpont biztosítja a telemetriai adatokat a Stream Analytics-feladat feldolgozásra.

1. Az Azure Portalon keresse meg az Event Hubs névterét, és válassza a **+ Event Hub**lehetőséget.
1. Nevezze el az eseményközpontot **centralexportnak,** és válassza a **Létrehozás gombot.**

Az Event Hubs névtere a következő képernyőképen jelenik meg:

![Event Hubs-névtér](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API-kulcs beszerezni

A függvényalkalmazásnak sendgrid API-kulcsra van szüksége az e-mailek küldéséhez. SendGrid API-kulcs létrehozása:

1. Az Azure Portalon keresse meg a SendGrid-fiókját. Ezután válassza a **Kezelés** lehetőséget a SendGrid-fiók eléréséhez.
1. A SendGrid-fiókban válassza a **Beállítások**lehetőséget, majd **az API-kulcsok lehetőséget.** Válassza **az API-kulcs létrehozása**lehetőséget:

    ![SendGrid API-kulcs létrehozása](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Az **API-kulcs létrehozása** lapon hozzon létre egy **AzureFunctionAccess** nevű kulcsot **teljes hozzáférési** engedélyekkel.
1. Jegyezze fel az API-kulcsot, szüksége van rá a függvényalkalmazás konfigurálásakor.

## <a name="define-the-function"></a>A függvény meghatározása

Ez a megoldás egy Azure Functions alkalmazást használ, hogy e-mail értesítést küldjön, amikor a Stream Analytics-feladat észleli a leállított eszközt. A függvényalkalmazás létrehozása:

1. Az Azure Portalon keresse meg az **App Service-példányt** a **DetectStoppedDevices** erőforráscsoportban.
1. Új **+** függvény létrehozásához jelölje be.
1. A **FEJLESZTŐI KÖRNYEZET KIVÁLASZTÁSA** lapon válassza **a Portálon** lehetőséget, majd a **Continue (Folytatás)** lehetőséget.
1. A **FÜGGVÉNY LÉTREHOZÁSA** lapon válassza a **Webhook + API,** majd a **Create (Létrehozás) lehetőséget.**

A portál létrehoz egy HttpTrigger1 nevű alapértelmezett **függvényt:**

![Alapértelmezett HTTP-eseményindító függvény](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Függvénykötések konfigurálása

Ha e-maileket szeretne küldeni a SendGriddel, a függvénykötéseit az alábbiak szerint kell konfigurálnia:

1. Válassza **az Integráció**lehetőséget, válassza ki a kimeneti HTTP **-t ($return),** majd válassza a **törlés**lehetőséget.
1. Válassza **a + Új kimenet**lehetőséget, majd a **Küldőrács**lehetőséget, majd a **Kijelölés**lehetőséget. A SendGrid bővítmény telepítéséhez válassza a **Telepítés** gombot.
1. Amikor a telepítés befejeződött, válassza **a Függvényvisszatérési érték használata**lehetőséget. Adjon hozzá egy érvényes **Címzett címet** az e-mailértesítések fogadásához.  Adjon meg egy érvényes **Feladó címet** az e-mail feladójaként való használatra.
1. Válassza az **új** lehetőséget a **SendGrid API kulcsalkalmazás-beállítása mellett.** Írja be **a SendGridAPIKey kulcsot** kulcsként, és a korábban értékként megjelölt SendGrid API-kulcs. Ezután válassza **a Létrehozás lehetőséget.**
1. A **Mentés gombra** a sendgrid-kötések mentéséhez válassza a függvényt.

Az integrációs beállítások a következő képernyőképhez hasonlóan néznek ki:

![Függvényalkalmazás-integrációk](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>A függvénykód hozzáadása

A funkció megvalósításához adja hozzá a C# kódot a bejövő HTTP-kérelem elemzéséhez, és küldje el az e-maileket az alábbiak szerint:

1. Válassza ki a **HttpTrigger1** függvényt a függvényalkalmazásban, és cserélje le a C# kódot a következő kódra:

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

    Az új kód mentéséig hibaüzenet jelenhet meg.

1. A funkció mentéséhez válassza a **Mentés** gombot.

### <a name="test-the-function-works"></a>A függvény működésének tesztelése

A függvény a portálon, először válassza **naplók** alján a kódszerkesztő. Ezután válassza a **Teszt** lehetőséget a kódszerkesztő jobb oldalán. Használja a következő **JSON-t a kérelem törzseként:**

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

A függvénynapló-üzenetek a **Naplók** panelen jelennek meg:

![Függvénynapló kimenete](media/howto-create-custom-rules/function-app-logs.png)

Néhány perc múlva a **Címzett** e-mail cím e-mailt kap a következő tartalommal:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics-lekérdezés hozzáadása

Ez a megoldás egy Stream Analytics-lekérdezést használ annak észlelésére, ha egy eszköz 120 másodpercnél tovább leállítja a telemetriai adatok küldését. A lekérdezés az eseményközpontthasználó telemetriai adatokat használja bemenetként. A feladat elküldi a lekérdezés eredményeit a függvényalkalmazásba. Ebben a szakaszban konfigurálja a Stream Analytics-feladatot:

1. Az Azure Portalon keresse meg a Stream Analytics-feladat, **a Feladatok topológiája** csoportban válassza **a Bemenetek**, válassza **a + Stream bemenet hozzáadása**lehetőséget, majd az **Event Hub**lehetőséget.
1. Az alábbi táblázatban található információk segítségével konfigurálhatja a bemenetet a korábban létrehozott eseményközpont segítségével, majd válassza a **Mentés gombot:**

    | Beállítás | Érték |
    | ------- | ----- |
    | Bemeneti alias | központitelemetria |
    | Előfizetés | Az Ön előfizetése |
    | Event Hubs-névtér | Az Event Hub névtere |
    | Eseményközpont neve | Meglévő - **centralexport** használata |

1. A **Feladatok topológiában**válassza **a Kimenetek**lehetőséget, válassza **a + Hozzáadás**lehetőséget, majd válassza az **Azure függvény lehetőséget.**
1. Az alábbi táblázatban található információk segítségével konfigurálhatja a kimenetet, majd válassza a **Mentés gombot:**

    | Beállítás | Érték |
    | ------- | ----- |
    | Kimeneti alias | e-mail értesítés |
    | Előfizetés | Az Ön előfizetése |
    | Függvényalkalmazás | A függvényalkalmazás |
    | Függvény  | httpTrigger1 |

1. A **Feladatok topológiában**válassza a **Lekérdezés** lehetőséget, és cserélje le a meglévő lekérdezést a következő SQL-re:

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
1. A Stream Analytics-feladat elindításához válassza az **Áttekintés**lehetőséget, majd a **Start**, majd **a Now**, majd a **Start lehetőséget:**

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Exportálás konfigurálása az IoT Centralban

Az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén keresse meg a Contoso-sablonból létrehozott IoT Central alkalmazást. Ebben a szakaszban konfigurálja az alkalmazást a telemetriai adatok streamelése a szimulált eszközökről az eseményközpontba. Az exportálás konfigurálása:

1. Nyissa meg az **Adatexportálás** lapot, válassza a **+ Új**, majd **az Azure Event Hubs lehetőséget.**
1. Az exportálás konfigurálásához használja az alábbi beállításokat, majd válassza a **Mentés**lehetőséget:

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás eseményközpontokba |
    | Engedélyezve | Bekapcsolva |
    | Event Hubs-névtér | Az Event Hubs névtér ének neve |
    | Eseményközpont | központi export |
    | Mérések | Bekapcsolva |
    | Eszközök | Ki |
    | Eszközsablonok | Ki |

![Folyamatos adatexportálási konfiguráció](media/howto-create-custom-rules/cde-configuration.png)

A folytatás előtt várja meg, amíg az exportálási állapot **fut.**

## <a name="test"></a>Test

A megoldás teszteléséhez letilthatja a folyamatos adatexportálást az IoT Central-ból a szimulált leállított eszközökre:

1. Az IoT Central alkalmazásban keresse meg az **Adatexportálás** lapot, és válassza az **Exportálás az eseményközpontokba** exportálási konfigurációt.
1. Állítsa **az Engedélyezve lehetőséget** **Ki** értékre, és válassza a **Mentés gombot.**
1. Legalább két perc elteltével a **Címzett** e-mail cím egy vagy több e-mailt kap, amelyek a következő példatartalomhoz hasonlóan jelennek meg:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Rendet rak

Az útmutató után a rendrakáshoz és a szükségtelen költségek elkerüléséhez törölje a **DetectStoppedDevices** erőforráscsoportot az Azure Portalon.

Törölheti az IoT Central alkalmazást az alkalmazás on belül **a Kezelés** lapról.

## <a name="next-steps"></a>További lépések

Ebben az útmutatóútmutatóban megtanulta, hogyan:

* Telemetriai adatok streamelése egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Stream Analytics-lekérdezést, amely észleli, ha egy eszköz leállította az adatok küldését.
* Küldjön egy e-mail értesítést az Azure Functions és sendgrid szolgáltatások használatával.

Most, hogy már tudja, hogyan hozhat létre egyéni szabályokat és értesítéseket, a javasolt következő lépés az [Azure IoT Central egyéni elemzéssel való kiterjesztése.](howto-create-custom-analytics.md)
