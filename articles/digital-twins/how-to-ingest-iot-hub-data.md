---
title: Telemetria betöltése az IoT Hubról
titleSuffix: Azure Digital Twins
description: 'Lásd: az eszköz telemetria üzeneteinek betöltése IoT Hubról.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7e6c200f0bec90fb73122e50885f2e6ad7420aeb
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564389"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetria betöltése az Azure digitális Twinsba

Az Azure digitális Twins a IoT-eszközökről és más forrásokból származó adatokon alapul. Az Azure Digital Twins szolgáltatásban használható eszköz-adatforrások közös forrása [IoT hub](../iot-hub/about-iot-hub.md).

Az adatok Azure digitális Ikrekbe való betöltésének folyamata egy külső számítási erőforrás, például egy [Azure-függvény](../azure-functions/functions-overview.md)beállítása, amely fogadja az adatok fogadását, és a [DigitalTwins API](how-to-use-apis-sdks.md) -kkal állítja be a Tulajdonságok vagy a telemetria események [digitális ikrekre](concepts-twins-graph.md) való beállítását. 

Ez a dokumentum végigvezeti egy olyan Azure-függvény írására szolgáló folyamaton, amely képes a telemetria betöltésére IoT Hub.

## <a name="prerequisites"></a>Előfeltételek

A példának való továbblépés előtt a következő erőforrásokat kell beállítania előfeltételekként:
* **Egy IoT hub**. Útmutatásért tekintse meg a [IoT hub](../iot-hub/quickstart-send-telemetry-cli.md)rövid útmutató *IoT hub létrehozása* című szakaszát.
* **Egy Azure-függvény** , amely megfelelő engedélyekkel rendelkezik a digitális kettős példány meghívásához. Útmutatásért lásd [*: útmutató: Azure-függvény beállítása az adatfeldolgozáshoz*](how-to-create-azure-function.md). 
* **Egy Azure digitális Twins-példány** , amely az eszköz telemetria fogja fogadni. Útmutatásért lásd [*: útmutató: Azure digitális Twins-példány és-hitelesítés beállítása*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Példa telemetria forgatókönyvre

Ez a útmutató ismerteti, hogyan küldhet üzeneteket IoT Hubról Azure digitális Twins-ra egy Azure-függvény használatával. Számos lehetséges konfiguráció és megfelelő stratégia használható ehhez, de a cikk példája a következő részeket tartalmazza:
* Egy IoT Hub hőmérő eszköz, egy ismert eszköz azonosítójával.
* Egy digitális Twin, amely az eszközt a megfelelő AZONOSÍTÓval jelöli

> [!NOTE]
> Ez a példa egy egyszerű azonosító egyezést használ az eszköz azonosítója és a hozzá tartozó digitális Twin azonosító között, de az eszközről a Twin (például egy leképezési táblával) kifinomultabb leképezések is megadhatók.

Ha a hőmérő eszköz egy hőmérséklet-telemetria eseményt küld, a digitális iker *hőmérséklet* tulajdonságát frissíteni kell. Ezt a forgatókönyvet az alábbi ábrán ismertetjük:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Egy folyamatábrát ábrázoló diagram. A diagramon egy IoT Hub eszköz hőmérséklet-telemetria küld a IoT Hub egy Azure-függvénynek, amely egy, az Azure-beli digitális Ikrekben található Twin értékre frissíti a hőmérséklet-tulajdonságot." border="false":::

## <a name="add-a-model-and-twin"></a>Modell hozzáadása és Twin

Az IoT hub-információkkal együtt kell frissítenie.

A modell így néz ki:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

A **modell az ikrek-példányba való feltöltéséhez**nyissa meg az Azure CLI-t, és futtassa a következő parancsot:

```azurecli
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Ezután **létre kell hoznia egy IKeret a modell használatával**. A következő parancs használatával hozzon létre egy dupla értéket, és állítsa be a 0,0 kezdeti hőmérsékleti értékként.

```azurecli
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

A sikeres Twin Create parancs kimenetének a következőhöz hasonlóan kell kinéznie:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ez a szakasz ugyanazokat a Visual Studio-indítási lépéseket és az Azure Function csontvázát használja a [*útmutató: Azure-függvény beállítása az adatok feldolgozásához*](how-to-create-azure-function.md). A csontváz kezeli a hitelesítést, és létrehoz egy szolgáltatási ügyfelet, amely készen áll arra, hogy feldolgozza az adatfeldolgozást, és az Azure Digital Twins API-kat hívja 

Az alábbi lépéseket követve hozzá kell adnia egy adott kódot a IoT telemetria-események feldolgozásához IoT Hub.  

### <a name="add-telemetry-processing"></a>Telemetria-feldolgozás hozzáadása
    
A telemetria események üzenetek formájában érkeznek az eszközről. A telemetria-feldolgozási kód hozzáadásának első lépéseként kinyeri az üzenet megfelelő részét az Event Grid eseményből. 

A különböző eszközök eltérő módon strukturálják az üzeneteiket, így a lépéshez tartozó kód a **csatlakoztatott eszköztől függ.** 

A következő kód példát mutat be egy egyszerű eszközre, amely a telemetria JSON-ként küldi el. Ez a minta teljes körűen fel van derítve az [*oktatóanyagban: végpontok közötti megoldás összekötése*](./tutorial-end-to-end.md). A következő kód megkeresi az üzenetet elküldő eszköz AZONOSÍTÓját, valamint a hőmérséklet értékét.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

A következő mintakód az azonosító és a hőmérséklet értékét veszi fel, és a "javítás" (a frissítések elvégzése) elemre használja a Twin.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Azure-függvény kódjának frissítése

Most, hogy megértette a kódot a korábbi mintákból, nyissa meg a Visual studiót, és cserélje le az Azure Function kódját ezzel a mintakód-kódra.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>A függvény összekapcsolásával IoT Hub

1. Esemény célhelyének beállítása a hub-adatként. A [Azure Portal](https://portal.azure.com/)navigáljon a IoT hub-példányhoz. Az **események**területen hozzon létre egy előfizetést az Azure-függvényhez. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Képernyőkép az esemény-előfizetés hozzáadását bemutató Azure Portalról.":::

2. Az **esemény-előfizetés létrehozása** lapon töltse ki a mezőket a következőképpen:
    1. A **név**mezőben adja meg az előfizetés nevét.
    2. Az **esemény sémája**területen válassza ki **Event Grid sémát**.
    3. A **Rendszertéma neve**területen válasszon egy egyedi nevet.
    4. Az **eseménytípus**területen válassza az **eszköz telemetria** lehetőséget a szűrendő esemény típusaként.
    5. A **végpont részletei**területen válassza ki az Azure-függvényt végpontként.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Az esemény-előfizetés részleteit megjelenítő Azure Portal képernyőképe":::

## <a name="send-simulated-iot-data"></a>Szimulált IoT-adatgyűjtés küldése

Az új beáramlási funkció teszteléséhez használja az eszköz-szimulátort az [*oktatóanyag: végpontok közötti megoldás csatlakoztatása*](./tutorial-end-to-end.md). Ezt az oktatóanyagot C# nyelven írt minta projekt vezérli. A mintakód itt található: [Azure digitális Twins-minták](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). A **DeviceSimulator** projektet a tárházban fogja használni.

A végpontok közötti oktatóanyagban hajtsa végre a következő lépéseket:
1. [*A szimulált eszköz regisztrálása IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*A szimuláció konfigurálása és futtatása*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Az eredmények ellenőrzése

A fenti eszköz-szimulátor futtatásakor a rendszer megváltoztatja a digitális iker hőmérséklet-értékét. Az Azure CLI-ben futtassa a következő parancsot a hőmérséklet értékének megtekintéséhez.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

A kimenetnek az alábbihoz hasonló hőmérsékleti értéket kell tartalmaznia:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Az érték változásának megtekintéséhez futtassa többször a fenti lekérdezési parancsot.

## <a name="next-steps"></a>További lépések

További információ az Azure Digital Twins szolgáltatással való bejövő és kimenő adatforgalomról:
* [*Fogalmak: integráció más szolgáltatásokkal*](concepts-integration.md)
