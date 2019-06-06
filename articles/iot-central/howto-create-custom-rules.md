---
title: Az egyéni szabályok és értesítések kiterjesztése az Azure IoT Central |} A Microsoft Docs
description: Megoldás a fejlesztők konfigurálása e-mail-értesítések küldése, ha egy eszköz leállítja a telemetriát küldő IoT Central alkalmazáshoz. Ez a megoldás az Azure Stream Analytics és az Azure Functions használ.
author: dominicbetts
ms.author: dobett
ms.date: 05/14/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5248b9546ffe931b72123778d0d23574e5238405
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742411"
---
# <a name="extend-azure-iot-central-with-custom-rules-that-send-notifications"></a>Egyéni szabályok, amelyek értesítéseket küldenek az Azure IoT Central kiterjesztése

Ez az útmutató bemutatja, hogy megoldás a fejlesztők az IoT-központ alkalmazás az egyéni szabályok és értesítések bővítése. A példa bemutatja, hogy értesítést küldhet az operátornak, ha egy eszköz leállítja a telemetriai adatokat küldenek. A megoldás egy [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) lekérdezést, amely észleli, ha egy eszköz leállt, telemetriai adatokat küldenek. A Stream Analytics-feladat használ [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) értesítés küldése e-mailt küld használatával [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

Ez az útmutató bemutatja, hogyan túlnyúlnak az IoT-központ már mire képes beépített szabályokat és műveleteket.

Ez az útmutató, megtudhatja, hogyan lehet:

* Egy IoT-központ alkalmazást a származó telemetriai adatok Stream *folyamatos adatexportálás*.
* Hozzon létre egy Stream Analytics-lekérdezés, amely észleli, ha egy eszköz már le van állítva az adatok küldésének.
* Az Azure Functions és a SendGrid szolgáltatások használatával e-mail értesítés küldése.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató a lépések elvégzéséhez, aktív Azure-előfizetés szükséges.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

### <a name="iot-central-application"></a>IoT Central alkalmazáshoz

Hozzon létre egy IoT-központ alkalmazás az [Azure IoT Central - alkalmazásaimat](https://aka.ms/iotcentral) lap a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Fizetési lehetőség | Utólagos, használatalapú fizetés |
| Alkalmazássablon | Minta: Contoso |
| Alkalmazásnév | Fogadja el az alapértelmezett, vagy válassza ki a saját nevét |
| URL-cím | Fogadja el az alapértelmezett, vagy válassza ki a saját egyedi URL-előtag |
| Címtár | Az Azure Active Directory-bérlő |
| Azure-előfizetés | Az Azure-előfizetése |
| Régió | USA keleti régiója |

A példák és képernyőképek a jelen cikk használja a **USA keleti Régiójában** régióban. Válassza az Önhöz helyet, és ellenőrizze, hogy ugyanabban a régióban létrehozott összes erőforrást.

### <a name="resource-group"></a>Erőforráscsoport

Használja a [hozzon létre egy erőforráscsoportot az Azure Portalon](https://portal.azure.com/#create/Microsoft.ResourceGroup) nevű **DetectStoppedDevices** hoz létre a többi erőforrást tartalmaz. Az Azure-erőforrások létrehozása az IoT Central alkalmazásnak ugyanazon a helyen.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

Használja a [Event Hubs-névtér létrehozása az Azure portal](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Name (Név)    | A névtér nevének kiválasztása |
| Tarifacsomag | Alapszintű |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Location egység | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-feladat

Használja a [a Stream Analytics-feladat létrehozása az Azure portal](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Name (Név)    | Válassza ki a feladat neve |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Location egység | USA keleti régiója |
| Üzemeltetési környezet | Felhő |
| Streamelési egységek | 3 |

### <a name="function-app"></a>Függvényalkalmazás

Használja a [egy függvényalkalmazás létrehozásához az Azure portal](https://portal.azure.com/#create/Microsoft.FunctionApp) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| App neve    | Válassza ki a függvényalkalmazás neve |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Operációs rendszer | Windows |
| Szolgáltatási csomag | Használatalapú csomag |
| Location egység | USA keleti régiója |
| Futtatókörnyezeti verem | .NET |
| Storage | Új létrehozása |

### <a name="sendgrid-account"></a>SendGrid-fiók

Használja a [SendGrid-fiók létrehozása az Azure portal](https://portal.azure.com/#create/Sendgrid.sendgrid) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Name (Név)    | Válassza ki a SendGrid-fiók neve |
| Jelszó | Jelszó létrehozása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | DetectStoppedDevices |
| Tarifacsomag | F1 – Ingyenes |
| Kapcsolattartási adatok | Írja be a szükséges adatokat |

Ha az összes szükséges erőforrást, hogy létrehozta a **DetectStoppedDevices** erőforráscsoportot a következő képernyőképhez hasonlóan néz ki:

![Leállított eszközök erőforráscsoport észlelése](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Konfigurálhatja az IoT-központ alkalmazás folyamatosan telemetria exportálása az eseményközpontba. Ebben a szakaszban a telemetriai adatok fogadása az IoT-központ alkalmazásból event hub létrehozása. Az event hubs kézbesíti a telemetriát a Stream Analytics-feladatot a feldolgozáshoz.

1. Az Azure Portalon keresse meg az Event Hubs-névtér, és válassza **+ Event Hub**.
1. Az eseményközpont neve **centralexport**, és válassza ki **létrehozás**.

Az Event Hubs-névtér a következő képernyőképhez hasonlóan néz ki:

![Event Hubs-névtér](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>A SendGrid API-kulcs beszerzése

A függvényalkalmazás egy e-mailek küldése a SendGrid API-kulcs szükséges. A SendGrid API-kulcs létrehozása:

1. Az Azure Portalon lépjen a SendGrid-fiókjából. Válassza a **kezelés** a SendGrid-fiók eléréséhez.
1. A SendGrid-fiókjából válassza **beállítások**, majd **API-kulcsok**. Válasszon **API-kulcs létrehozása**:

    ![A SendGrid API-kulcs létrehozása](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Az a **API-kulcs létrehozása** hozza létre a nevű kulcs **AzureFunctionAccess** a **teljes hozzáférést** engedélyeket.
1. Jegyezze fel az API-kulcsot, szüksége lesz rá a függvényalkalmazás konfigurálásakor.

## <a name="define-the-function"></a>A függvény meghatározása

Ez a megoldás az Azure Functions-alkalmazás használatával e-mail-értesítés küldése, amikor a Stream Analytics-feladat leállítva eszközt észlel. A függvényalkalmazás létrehozásához:

1. Az Azure Portalon lépjen a **App Service-ben** példány a **DetectStoppedDevices** erőforráscsoportot.
1. Válassza ki **+** új függvény létrehozásához.
1. Az a **válassza ki A fejlesztési környezet** lapon a **a portálon** majd **Folytatás**.
1. A a **CREATE A FUNCTION** lapon a **Webhook + API** majd **létrehozás**.

A portál létrehoz egy alapértelmezett függvény nevű **HttpTrigger1**:

![Alapértelmezett HTTP által aktivált függvény](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Függvénykötésnek konfigurálása

A sendgrid használatával e-mailek küldése, kell tennie a kötéseket a függvény a következőképpen konfigurálja:

1. Válassza ki **integráció**, válassza ki a kimeneti **HTTP ($return)** , majd válassza ki **törlése**.
1. Válassza a **+ új kimenet**, majd válassza ki **SendGrid**, és válassza a **kiválasztása**. Válasszon **telepítése** a SendGrid-bővítmény telepítéséhez.
1. Amikor a telepítés befejeződött, válassza ki a **függvény visszaadott értékének használata**. Adjon hozzá egy érvényes **címre** e-mail értesítések fogadásához.  Adjon hozzá egy érvényes **címről** használata az e-mailek feladójaként.
1. Válassza ki **új** melletti **SendGrid API-kulcs Alkalmazásbeállítása**. Adja meg **SendGridAPIKey** a kulcsot, valamint a korábban feljegyzett értéket a SendGrid API-kulcsot. Ezután kattintson a **Létrehozás** elemre.
1. Válasszon **mentése** menteni a SendGrid-kötések a függvény.

Az integráció beállítások az alábbi képernyőképen látható:

![Függvény alkalmazás Integrációk](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>A függvénykód hozzáadása

A függvény végrehajtásához adja hozzá a C# elemezheti a bejövő HTTP-kérelem, és az e-mailek küldése a következő kódot:

1. Válassza ki a **HttpTrigger1** függvény a függvényalkalmazásban, és cserélje le a C# kódot az alábbira:

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

    Egy hiba üzenet jelenhet meg, amíg nem menti az új kódot.

1. Válassza ki **mentése** , mentse a függvényt.

### <a name="test-the-function-works"></a>A függvény működését tesztelése

A függvény tesztelése a portálon, először válassza **naplók** Kódszerkesztő alján. Válassza a **teszt** jobb oldalán a Kódszerkesztő. Használja a következő JSON, mint a **kérelem törzse**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

A függvény naplója üzenetek megjelennek a **naplók** panelen:

![Függvény-kimenet](media/howto-create-custom-rules/function-app-logs.png)

Néhány perc múlva a **való** e-mail cím kap egy e-mailt a következő tartalommal:

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics-lekérdezés hozzáadása

Ez a megoldás egy Stream Analytics-lekérdezés használatával észleli, ha egy eszköz leállítja a telemetriai adatokat küldenek a több mint 120 másodperc. A lekérdezés az eseményközpontból érkező telemetriai adatok használja bemenetként. A feladat küld a függvényalkalmazás a lekérdezés eredményeit. Ebben a szakaszban a Stream Analytics-feladat konfigurálása:

1. Az Azure Portalon keresse meg a Stream analytics-feladatot, a **feladatok topológia** válassza **bemenetek**, válassza a **+ streambemenet hozzáadása**, és válassza a **esemény Hub**.
1. Az alábbi táblázat az információk segítségével konfigurálhatja a bemenetet a korábban létrehozott event hubs használatával, majd kattintson a **mentése**:

    | Beállítás | Érték |
    | ------- | ----- |
    | Bemeneti alias | centraltelemetry |
    | Előfizetés | Az Ön előfizetése |
    | Event Hubs-névtér | Az Event Hubs-névtér |
    | Eseményközpont neve | Meglévő - **centralexport** |

1. Alatt **feladatok topológia**válassza **kimenetek**, válassza a **+ Hozzáadás**, és válassza a **Azure-függvény**.
1. Az alábbi táblázatban az információk segítségével konfigurálhatja a kimenetet, majd kattintson a **mentése**:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kimeneti alias | EmailNotification |
    | Előfizetés | Az Ön előfizetése |
    | Függvényalkalmazás | A függvényalkalmazás |
    | Függvény  | HttpTrigger1 |

1. Alatt **feladatok topológia**válassza **lekérdezés** , és cserélje le a meglévő lekérdezés a következő SQL-lel:

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
1. A Stream Analytics-feladat indításához válassza **áttekintése**, majd **Start**, majd **most**, majd **Start**:

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Exportálás konfigurálása az IoT-központ

Keresse meg a [IoT Central alkalmazásnak](https://aka.ms/iotcentral) a Contoso-sablon alapján létrehozott. Ebben a szakaszban konfigurálja az alkalmazás segítségével a telemetriát a szimulált eszközökről az eseményközpontba. Az Exportálás konfigurálása:

1. Keresse meg a **folyamatos adatexportálás** lapon jelölje be **+ új**, majd **Azure Event Hubs**.
1. Az Exportálás konfigurálja, majd válassza ki a következő beállítások használatával **mentése**:

    | Beállítás | Érték |
    | ------- | ----- |
    | Megjelenített név | Exportálás az Event hubs szolgáltatásba |
    | Enabled | Bekapcsolva |
    | Event Hubs-névtér | Az Event Hubs-névtér neve |
    | Eseményközpont | centralexport |
    | Mérések | Bekapcsolva |
    | Eszközök | Ki |
    | Eszköz-sablonok | Ki |

![Folyamatos exportálási konfiguráció](media/howto-create-custom-rules/cde-configuration.png)

Várjon, amíg az Exportálás állapota **futó** a folytatás előtt.

## <a name="test"></a>Tesztelés

A megoldás teszteléséhez, letilthatja a folyamatos exportálás IoT-központ leállt a szimulált eszközök:

1. Az IoT-központ alkalmazásban, keresse meg a **folyamatos adatexportálás** lapon, és válassza a **az Event hubs szolgáltatásba exportálása** konfigurációjának exportálása.
1. Állítsa be **engedélyezve** való **ki** válassza **mentése**.
1. Legalább két perc elteltével a **való** e-mail cím kap egy vagy több e-maileket, amelyek a következő példához hasonló tartalom:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID   Time
    7b169aee-c843-4d41-9f25-7a02671ee659    2019-05-09T14:28:59.954Z
    ```

## <a name="tidy-up"></a>Elágazásából

Ebben az útmutatóban után elágazásából és a felesleges költségek elkerülése érdekében törölje a **DetectStoppedDevices** erőforráscsoportot az Azure Portalon.

Az IoT Central alkalmazáshoz törölheti a **felügyeleti** lap az alkalmazásban.

## <a name="next-steps"></a>További lépések

Ez az útmutató, megtanulta, hogyan lehet:

* Egy IoT-központ alkalmazást a származó telemetriai adatok Stream *folyamatos adatexportálás*.
* Hozzon létre egy Stream Analytics-lekérdezés, amely észleli, ha egy eszköz már le van állítva az adatok küldésének.
* Az Azure Functions és a SendGrid szolgáltatások használatával e-mail értesítés küldése.

Most, hogy tudja, hogyan hozhat létre egyéni szabályok és értesítések, a javasolt következő lépésre megtudhatja, hogyan [kiterjesztése az Azure IoT Central egyéni Analytics](howto-create-custom-analytics.md).
