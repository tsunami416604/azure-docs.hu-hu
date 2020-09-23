---
title: Telemetria betöltése az IoT Hubról
titleSuffix: Azure Digital Twins
description: 'Lásd: az eszköz telemetria üzeneteinek betöltése IoT Hubról.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9fa3c27f9cc35b31fc78b2a09bea725934093e63
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983357"
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

Ez a útmutató ismerteti, hogyan küldhet üzeneteket IoT Hubról Azure digitális Twins-ra egy Azure-függvény használatával. Számos lehetséges konfiguráció és megfelelő stratégia használható az üzenetek küldéséhez, de a cikk példája a következő részeket tartalmazza:
* Egy IoT Hub hőmérő eszköz, egy ismert eszköz azonosítójával
* Egy digitális Twin, amely az eszközt a megfelelő AZONOSÍTÓval jelöli

> [!NOTE]
> Ez a példa egy egyszerű azonosító egyezést használ az eszköz azonosítója és a hozzá tartozó digitális Twin azonosító között, de az eszközről a Twin (például egy leképezési táblával) kifinomultabb leképezések is megadhatók.

Ha a termosztátos eszköz egy hőmérséklet-telemetria eseményt küld, egy Azure-függvény dolgozza fel a telemetria és a digitális Twin *hőmérséklet* tulajdonságát. Ezt a forgatókönyvet az alábbi ábrán ismertetjük:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Egy folyamatábrát ábrázoló diagram. A diagramon egy IoT Hub eszköz hőmérséklet-telemetria küld a IoT Hubon keresztül egy Azure-függvénynek, amely egy, az Azure-beli digitális Ikrekben található Twin értékre frissíti a hőmérséklet-tulajdonságot." border="false":::

## <a name="add-a-model-and-twin"></a>Modell hozzáadása és Twin

Hozzáadhat/feltölthet egy modellt az alábbi CLI-paranccsal, majd létrehozhat egy IKeret a modell használatával, amely a IoT Hubból származó információkkal lesz frissítve.

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

Most, hogy megértette a korábbi minták kódját, nyissa meg az Azure-függvényt a Visual Studio [*Előfeltételek*](https://docs.microsoft.com/azure/digital-twins/how-to-ingest-iot-hub-data#prerequisites) szakaszában. (Ha még nem rendelkezik Azure-függvénysel, az előfeltételekben található hivatkozásra kattintva hozzon létre egyet most).

Cserélje le az Azure Function kódját ezzel a mintakód.

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
Mentse a függvény kódját, és tegye közzé a Function alkalmazást az Azure-ban. Ezt úgy teheti meg, hogy [*közzéteszi a*](https://docs.microsoft.com/azure/digital-twins/how-to-create-azure-function#publish-the-function-app-to-azure) " [*útmutató: Azure-függvény beállítása az adatfeldolgozáshoz*](how-to-create-azure-function.md)" című függvényalkalmazás szakaszát.

Sikeres közzététel után az alábbi ábrán látható kimenet jelenik meg a Visual Studio-parancsablakban:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
A közzétételi folyamat állapotát a [Azure Portal](https://portal.azure.com/)is ellenőrizheti. Keresse meg az _erőforráscsoportot_ , és navigáljon a _tevékenység naplóhoz_ , és keresse meg a _Get Web App Publishing profilt_ a listából, és ellenőrizze, hogy az állapot sikeres-e.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="A közzétételi folyamat állapotát megjelenítő Azure Portal képernyőképe.":::

## <a name="connect-your-function-to-iot-hub"></a>A függvény összekapcsolásával IoT Hub

Esemény célhelyének beállítása a hub-adatként.
A [Azure Portal](https://portal.azure.com/)navigáljon az [*előfeltételek*](https://docs.microsoft.com/azure/digital-twins/how-to-ingest-iot-hub-data#prerequisites) szakaszban létrehozott IoT hub-példányhoz. Az **események**területen hozzon létre egy előfizetést az Azure-függvényhez.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Képernyőkép az esemény-előfizetés hozzáadását bemutató Azure Portalról.":::

Az **esemény-előfizetés létrehozása** lapon töltse ki a mezőket a következőképpen:
  1. A **név**mezőben adja meg az előfizetés nevét.
  2. Az **esemény sémája**területen válassza ki _Event Grid sémát_.
  3. Az **eseménytípus**területen válassza az _eszköz telemetria_ jelölőnégyzetet, és törölje a többi eseménytípus jelölését.
  4. A **végpont típusa**területen válassza az _Azure-függvény_lehetőséget.
  5. A **végpont területen**válassza _a végpont kiválasztása_ lehetőséget a végpont létrehozásához.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Képernyőkép a Azure Portalról az esemény-előfizetés részleteinek létrehozásához":::

A megnyíló _Azure-függvény kiválasztása_ oldalon ellenőrizze az alábbi adatokat.
 1. **Előfizetés**: az Azure-előfizetése
 2. **Erőforráscsoport**: az erőforráscsoport
 3. **Function alkalmazás**: a Function alkalmazás neve
 4. **Tárolóhely**: _éles üzem_
 5. **Függvény**: válassza ki az Azure-függvényt a legördülő listából.

Mentse a részleteket a _kijelölés megerősítése_ gombra kattintva.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="A Azure Portal képernyőképe az Azure-függvény kiválasztásához":::

Az esemény-előfizetés létrehozásához kattintson a _Létrehozás_ gombra.

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

## <a name="next-steps"></a>Következő lépések

További információ az Azure Digital Twins szolgáltatással való bejövő és kimenő adatforgalomról:
* [*Fogalmak: integráció más szolgáltatásokkal*](concepts-integration.md)