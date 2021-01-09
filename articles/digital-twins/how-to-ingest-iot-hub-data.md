---
title: Telemetria betöltése az IoT Hubról
titleSuffix: Azure Digital Twins
description: 'Lásd: az eszköz telemetria üzeneteinek betöltése IoT Hubról.'
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051088"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetria betöltése az Azure digitális Twinsba

Az Azure digitális Twins a IoT-eszközökről és más forrásokból származó adatokon alapul. Az Azure Digital Twins szolgáltatásban használható eszköz-adatforrások közös forrása [IoT hub](../iot-hub/about-iot-hub.md).

Az adatok Azure digitális Ikrekbe való betöltésének folyamata egy külső számítási erőforrás, például egy [Azure functions](../azure-functions/functions-overview.md)használatával végzett függvény beállítása. A függvény fogadja az összes adatát, és a [DigitalTwins API](/rest/api/digital-twins/dataplane/twins) -kkal állítja be a tulajdonságokat vagy a telemetria eseményeket a [digitális ikreknek](concepts-twins-graph.md) megfelelően. 

Ez a dokumentum végigvezeti a telemetria IoT Hub-ből betölthető függvény írásához használt folyamaton.

## <a name="prerequisites"></a>Előfeltételek

A példának való továbblépés előtt a következő erőforrásokat kell beállítania előfeltételekként:
* **Egy IoT hub**. Útmutatásért tekintse meg a [IoT hub](../iot-hub/quickstart-send-telemetry-cli.md)rövid útmutató *IoT hub létrehozása* című szakaszát.
* A digitális kettős példány meghívásához megfelelő engedélyekkel rendelkező **függvény** . Útmutatásért tekintse meg a következő témakört [*: útmutató: funkció beállítása az Azure-ban az adatfeldolgozáshoz*](how-to-create-azure-function.md). 
* **Egy Azure digitális Twins-példány** , amely az eszköz telemetria fogja fogadni. Útmutatásért lásd [*: útmutató: Azure digitális Twins-példány és-hitelesítés beállítása*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Példa telemetria forgatókönyvre

Ez a útmutató ismerteti, hogyan küldhet üzeneteket IoT Hubról Azure digitális Twins-ra az Azure-függvény használatával. Számos lehetséges konfiguráció és megfelelő stratégia használható az üzenetek küldéséhez, de a cikk példája a következő részeket tartalmazza:
* Egy IoT Hub hőmérő eszköz, egy ismert eszköz azonosítójával
* Egy digitális Twin, amely az eszközt a megfelelő AZONOSÍTÓval jelöli

> [!NOTE]
> Ez a példa egy egyszerű azonosító egyezést használ az eszköz azonosítója és a hozzá tartozó digitális Twin azonosító között, de az eszközről a Twin (például egy leképezési táblával) kifinomultabb leképezések is megadhatók.

Ha a termosztátos eszköz egy hőmérséklet-telemetria eseményt küld, a függvény feldolgozza a telemetria és a digitális Twin *hőmérséklet* tulajdonságát. Ezt a forgatókönyvet az alábbi ábrán ismertetjük:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Egy folyamatábrát ábrázoló diagram. A diagramon egy IoT Hub eszköz hőmérséklet-telemetria küld a IoT Hub az Azure-ban egy függvénynek, amely egy, az Azure-beli digitális Ikrekben lévő Twin értékre frissíti a hőmérséklet-tulajdonságot." border="false":::

## <a name="add-a-model-and-twin"></a>Modell hozzáadása és Twin

Hozzáadhat/feltölthet egy modellt az alábbi CLI-paranccsal, majd létrehozhat egy IKeret a modell használatával, amely a IoT Hubból származó információkkal lesz frissítve.

A modell így néz ki:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

A **modell az ikrek-példányba való feltöltéséhez** nyissa meg az Azure CLI-t, és futtassa a következő parancsot:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Ezután **létre kell hoznia egy IKeret a modell használatával**. A következő parancs használatával hozzon létre egy dupla értéket, és állítsa be a 0,0 kezdeti hőmérsékleti értékként.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

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

## <a name="create-a-function"></a>Függvény létrehozása

Ez a szakasz ugyanazokat a Visual Studio indítási lépéseket és függvény-csontvázat használja, mint a következő [*: függvény beállítása az adatok feldolgozásához*](how-to-create-azure-function.md). A csontváz kezeli a hitelesítést, és létrehoz egy szolgáltatási ügyfelet, amely készen áll arra, hogy feldolgozza az adatfeldolgozást, és az Azure Digital Twins API-kat hívja 

Az alábbi lépéseket követve hozzá kell adnia egy adott kódot a IoT telemetria-események feldolgozásához IoT Hub.  

### <a name="add-telemetry-processing"></a>Telemetria-feldolgozás hozzáadása
    
A telemetria események üzenetek formájában érkeznek az eszközről. A telemetria-feldolgozási kód hozzáadásának első lépéseként kinyeri az üzenet megfelelő részét az Event Grid eseményből. 

A különböző eszközök eltérő módon strukturálják az üzeneteiket, így a lépéshez tartozó kód a **csatlakoztatott eszköztől függ.** 

A következő kód példát mutat be egy egyszerű eszközre, amely a telemetria JSON-ként küldi el. Ez a minta teljes körűen fel van derítve az [*oktatóanyagban: végpontok közötti megoldás összekötése*](./tutorial-end-to-end.md). A következő kód megkeresi az üzenetet elküldő eszköz AZONOSÍTÓját, valamint a hőmérséklet értékét.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

A következő mintakód az azonosító és a hőmérséklet értékét veszi fel, és a "javítás" (a frissítések elvégzése) elemre használja a Twin.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>A függvény kódjának frissítése

Most, hogy megértette a korábbi minták kódját, nyissa meg a függvényt a Visual Studio [*Előfeltételek*](#prerequisites) szakaszában. (Ha nem rendelkezik az Azure-ban létrehozott függvénnyel, az előfeltételekben található hivatkozásra kattintva hozzon létre egyet most).

Cserélje le a függvény kódját ezzel a mintakód.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Mentse a függvény kódját, és tegye közzé a Function alkalmazást az Azure-ban. Ebből a cikkből megtudhatja, hogyan hozhat létre egy függvényt az Azure [*-ban az*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) [*adatfeldolgozáshoz*](how-to-create-azure-function.md).

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
A [Azure Portal](https://portal.azure.com/)navigáljon az [*előfeltételek*](#prerequisites) szakaszban létrehozott IoT hub-példányhoz. Az **események** területen hozzon létre egy előfizetést a függvényhez.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Képernyőkép az esemény-előfizetés hozzáadását bemutató Azure Portalról.":::

Az **esemény-előfizetés létrehozása** lapon töltse ki a mezőket a következőképpen:
  1. A **név** mezőben adja meg az előfizetés nevét.
  2. Az **esemény sémája** területen válassza ki _Event Grid sémát_.
  3. Az **eseménytípus** területen válassza az _eszköz telemetria_ jelölőnégyzetet, és törölje a többi eseménytípus jelölését.
  4. A **végpont típusa** területen válassza az _Azure-függvény_ lehetőséget.
  5. A **végpont területen** válassza _a végpont kiválasztása_ lehetőséget a végpont létrehozásához.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Képernyőkép a Azure Portalról az esemény-előfizetés részleteinek létrehozásához":::

A megnyíló _Azure-függvény kiválasztása_ oldalon ellenőrizze az alábbi adatokat.
 1. **Előfizetés**: Az Ön Azure-előfizetése
 2. **Erőforráscsoport**: az erőforráscsoport
 3. **Function alkalmazás**: a Function alkalmazás neve
 4. **Tárolóhely**: _éles üzem_
 5. **Függvény**: válassza ki a függvényt a legördülő listából.

Mentse a részleteket a _kijelölés megerősítése_ gombra kattintva.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Képernyőkép a Azure Portalről a függvény kiválasztásához.":::

Az esemény-előfizetés létrehozásához kattintson a _Létrehozás_ gombra.

## <a name="send-simulated-iot-data"></a>Szimulált IoT-adatgyűjtés küldése

Az új beáramlási funkció teszteléséhez használja az eszköz-szimulátort az [*oktatóanyag: végpontok közötti megoldás csatlakoztatása*](./tutorial-end-to-end.md). Ezt az oktatóanyagot C# nyelven írt minta projekt vezérli. A mintakód itt található: [Azure digitális Twins végpontok közötti minták](/samples/azure-samples/digital-twins-samples/digital-twins-samples). A **DeviceSimulator** projektet a tárházban fogja használni.

A végpontok közötti oktatóanyagban hajtsa végre a következő lépéseket:
1. [*A szimulált eszköz regisztrálása IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*A szimuláció konfigurálása és futtatása*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Az eredmények ellenőrzése

A fenti eszköz-szimulátor futtatásakor a rendszer megváltoztatja a digitális iker hőmérséklet-értékét. Az Azure CLI-ben futtassa a következő parancsot a hőmérséklet értékének megtekintéséhez.

```azurecli-interactive
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
