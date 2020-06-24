---
title: Telemetria betöltése az IoT Hubról
titleSuffix: Azure Digital Twins
description: 'Lásd: az eszköz telemetria üzeneteinek betöltése IoT Hubról.'
author: cschormann
ms.author: cschorm
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 4d2b849df7371815a120e59cfff3d8bc7596b1ed
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725851"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>IoT Hub telemetria betöltése az Azure digitális Twinsba

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Az Azure digitális Twins a IoT-eszközökről és más forrásokból származó adatokon alapul. Az Azure Digital Twins szolgáltatásban használható eszköz-adatforrások közös forrása [IoT hub](../iot-hub/about-iot-hub.md).

Az előzetes verzióban az adatok Azure digitális Ikrekbe való betöltésének folyamata egy külső számítási erőforrás, például egy [Azure-függvény](../azure-functions/functions-overview.md)beállítása, amely fogadja az adatok fogadását, és a [DigitalTwins API](how-to-use-apis-sdks.md) -kkal állítja be a Tulajdonságok vagy a telemetria események [digitális ikrekre](concepts-twins-graph.md) való beállítását. 

Ez a dokumentum végigvezeti egy olyan Azure-függvény írására szolgáló folyamaton, amely képes a telemetria betöltésére IoT Hub.

## <a name="example-telemetry-scenario"></a>Példa telemetria forgatókönyvre

Ez a útmutató ismerteti, hogyan küldhet üzeneteket IoT Hubról Azure digitális Twins-ra egy Azure-függvény használatával. Számos lehetséges konfiguráció és megfelelő stratégia használható ehhez, de a cikk példája a következő részeket tartalmazza:
* Egy IoT Hub hőmérő eszköz, egy ismert eszköz azonosítójával.
* Egy digitális Twin, amely az eszközt a megfelelő AZONOSÍTÓval jelöli
* Egy szobát jelölő digitális Twin

> [!NOTE]
> Ez a példa egy egyszerű azonosító egyezést használ az eszköz azonosítója és a hozzá tartozó digitális Twin azonosító között, de az eszközről a Twin (például egy leképezési táblával) kifinomultabb leképezések is megadhatók.

Amikor a hőmérő eszköz elküld egy hőmérsékleti telemetria eseményt, a Twin *Room* *hőmérséklet* tulajdonságának frissítenie kell. Ennek elvégzéséhez egy eszközön egy telemetria-eseményről kell leképezni egy, a digitális Twin-ben lévő tulajdonságot. A Twin [gráf](concepts-twins-graph.md) topológiával kapcsolatos információkat fog használni, hogy megkeresse a Twin *Room* -t, majd megadhatja a Twin tulajdonságot. Más esetekben előfordulhat, hogy a felhasználók a megfelelő twin (ebben a példában az *123*-es azonosítójú Twin) tulajdonságot szeretnék beállítani. Az Azure Digital Twins sok rugalmasságot biztosít annak eldöntéséhez, hogy hogyan telemetria az adatleképezések az ikrekbe. 

Ezt a forgatókönyvet az alábbi ábrán ismertetjük:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Az IoT Hub-eszközök hőmérséklet-telemetria küldenek IoT Hub, Event Grid vagy rendszertémakörök között egy Azure-függvénynek, amely frissíti a hőmérséklet-tulajdonságot az ikrekben az Azure digitális Twins-ban." border="false":::

## <a name="prerequisites"></a>Előfeltételek

A példa folytatása előtt végre kell hajtania a következő előfeltételeket.
1. Hozzon létre egy IoT hubot. Útmutatásért tekintse meg a [IoT hub](../iot-hub/quickstart-send-telemetry-cli.md) útmutató *IoT hub létrehozása* című szakaszát.
2. Hozzon létre legalább egy Azure-függvényt az események IoT Hubból való feldolgozásához. [Útmutató: Azure-függvény beállítása az adatok feldolgozásához](how-to-create-azure-function.md) egy olyan alapszintű Azure-függvény létrehozásához, amely csatlakozhat az Azure Digital Twins-hoz, és meghívja az Azure digitális Twins API-funkcióit. Ennek a funkciónak a többi része fog kiépíteni.
3. Esemény célhelyének beállítása a hub-adatként. A [Azure Portal](https://portal.azure.com/)navigáljon a IoT hub-példányhoz. Az *események*területen hozzon létre egy előfizetést az Azure-függvényhez. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Azure Portal: esemény-előfizetés hozzáadása":::

4. Az *esemény-előfizetés létrehozása* lapon töltse ki a mezőket a következőképpen:
   * Az *esemény-előfizetés részletei*területen nevezze el az előfizetést, amire szeretné
   * Az *eseménytípus*területen válassza ki az *eszköz telemetria* elemet a szűrni kívánt esemény típusaként.
      - Ha szeretné, adja hozzá a szűrőket más típusú eseményekhez.
   * A *VÉGPONT részletei*területen válassza ki az Azure-függvényt végpontként

## <a name="create-an-azure-function-in-visual-studio"></a>Azure-függvény létrehozása a Visual Studióban

Ez a szakasz ugyanazokat a Visual Studio-indítási lépéseket és az Azure Function csontvázát használja a [útmutató: Azure-függvény beállítása az adatok feldolgozásához](how-to-create-azure-function.md). A csontváz kezeli a hitelesítést, és létrehoz egy szolgáltatási ügyfelet, amely készen áll arra, hogy feldolgozza az adatfeldolgozást, és az Azure Digital Twins API-kat hívja 

A csontváz függvény szíve a következő:

```csharp
namespace FunctionSample
{
    public static class FooFunction
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("Foo")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            DigitalTwinsClient client = null;
            try
            {
                ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
                DigitalTwinsClientOptions opts = 
                    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
                client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
                                                
                log.LogInformation($"ADT service client connection created.");
            }
            catch (Exception e)
            {
                log.LogError($"ADT service client connection failed. " + e.ToString());
                return;
            }
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Az alábbi lépéseket követve hozzá kell adnia egy adott kódot a IoT telemetria-események feldolgozásához IoT Hub.  

## <a name="add-telemetry-processing"></a>Telemetria-feldolgozás hozzáadása

A telemetria események üzenetek formájában érkeznek az eszközről. A telemetria-feldolgozási kód hozzáadásának első lépéseként kinyeri az üzenet megfelelő részét az Event Grid eseményből. 

A különböző eszközök eltérő módon strukturálják az üzeneteiket, így a lépéshez tartozó kód a csatlakoztatott eszköztől függ. 

A következő kód példát mutat be egy egyszerű eszközre, amely a telemetria JSON-ként küldi el. A minta kibontja az üzenetet küldő eszköz AZONOSÍTÓját, valamint a hőmérséklet értékét.

```csharp
JObject job = eventGridEvent.Data as JObject;
string devid = (string)job["systemProperties"].ToObject<JObject>().Property("IoT-hub-connection-device-ID").Value;
double temp = (double)job["body"].ToObject<JObject>().Property("temperature").Value;
```

Ne felejtse el, hogy a gyakorlat célja egy *helyiség* hőmérsékletének frissítése a Twin gráfon belül. Ez azt jelenti, hogy az üzenet célja nem az ehhez az eszközhöz társított digitális Twin, de a Twin *szoba* , amely a szülője. A szülő IKeret az telemetria-üzenetből a fenti kóddal kinyert eszköz-azonosító érték használatával találja.

Ehhez használja az Azure Digital Twins API-kat, hogy hozzáférjenek az eszközhöz tartozó "Twin" kapcsolatokhoz (amelyek ebben az esetben ugyanazzal az AZONOSÍTÓval rendelkeznek). A bejövő kapcsolatból megkeresheti a szülő AZONOSÍTÓját az alábbi kódrészlettel.

Az alábbi kódrészlet azt mutatja be, hogyan lehet lekérdezni a kettős kapcsolatok bejövő kapcsolatait:

```csharp
AsyncPageable<IncomingRelationship> res = client.GetIncomingRelationshipsAsync(twin_id);
await foreach (IncomingRelationship irel in res)
{
    Log.Ok($"Relationship: {irel.RelationshipName} from {irel.SourceId} | {irel.RelationshipId}");
}
```

A Twin szülője a kapcsolat *SourceId forrásazonosító* tulajdonságában található.

Meglehetősen gyakori, ha egy különálló modell egy olyan eszközt jelöl, amely csak egyetlen bejövő kapcsolattal rendelkezik. Ebben az esetben választhatja az első (és csak a) kapcsolat eredményét. Ha a modellek több típusú kapcsolatot is lehetővé tesznek ehhez a Twin típushoz, előfordulhat, hogy további, több bejövő kapcsolat közül kell megadnia. Ennek egyik leggyakoribb módja a kapcsolat kiválasztása `RelationshipName` . 

Ha már rendelkezik a *szobát*jelképező szülő-iker azonosítóval, akkor a "javítás" (a frissítés a következőre) a Twin. Ehhez használja a következő kódot:

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", temp);
try
{
    await client.UpdateDigitalTwinAsync(twin_id, uou.Serialize());
    Log.Ok($"Twin '{twin_id}' updated successfully!");
}
...
```

### <a name="full-azure-function-code"></a>Teljes Azure-függvény kódja

A korábbi mintákból származó kód használatával itt látható a teljes Azure-függvény a kontextusban:

```csharp
[FunctionName("ProcessHubToDTEvents")]
public async void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
{
    // After this is deployed, in order for this function to be authorized on Azure Digital Twins APIs,
    // you'll need to turn the Managed Identity Status to "On", 
    // grab the Object ID of the function, and assign the "Azure Digital Twins Owner (Preview)" role to this function identity.

    DigitalTwinsClient client = null;
    //log.LogInformation(eventGridEvent.Data.ToString());
    // Authenticate on Azure Digital Twins APIs
    try
    {
        
        ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
        client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
        log.LogInformation($"ADT service client connection created.");
    }
    catch (Exception e)
    {
        log.LogError($"ADT service client connection failed. " + e.ToString());
        return;
    }

    if (client != null)
    {
        try
        {
            if (eventGridEvent != null && eventGridEvent.Data != null)
            {
                #region Open this region for message format information
                // Telemetry message format
                //{
                //  "properties": { },
                //  "systemProperties": 
                // {
                //    "iothub-connection-device-id": "thermostat1",
                //    "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
                //    "iothub-connection-auth-generation-id": "637199981642612179",
                //    "iothub-enqueuedtime": "2020-03-18T18:35:08.269Z",
                //    "iothub-message-source": "Telemetry"
                //  },
                //  "body": "eyJUZW1wZXJhdHVyZSI6NzAuOTI3MjM0MDg3MTA1NDg5fQ=="
                //}
                #endregion

                // Reading deviceId from message headers
                log.LogInformation(eventGridEvent.Data.ToString());
                JObject job = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                string deviceId = (string)job["systemProperties"]["iothub-connection-device-id"];
                log.LogInformation($"Found device: {deviceId}");

                // Extracting temperature from device telemetry
                byte[] body = System.Convert.FromBase64String(job["body"].ToString());
                var value = System.Text.ASCIIEncoding.ASCII.GetString(body);
                var bodyProperty = (JObject)JsonConvert.DeserializeObject(value);
                var temperature = bodyProperty["Temperature"];
                log.LogInformation($"Device Temperature is:{temperature}");

                // Update device Temperature property
                await AdtUtilities.UpdateTwinProperty(client, deviceId, "/Temperature", temperature, log);

                // Find parent using incoming relationships
                string parentId = await AdtUtilities.FindParent(client, deviceId, "contains", log);
                if (parentId != null)
                {
                    await AdtUtilities.UpdateTwinProperty(client, parentId, "/Temperature", temperature, log);
                }

            }
        }
        catch (Exception e)
        {
            log.LogError($"Error in ingest function: {e.Message}");
        }
    }
}
```

A (z) funkció a bejövő kapcsolatok kereséséhez:
```csharp
public static async Task<string> FindParent(DigitalTwinsClient client, string child, string relname, ILogger log)
{
    // Find parent using incoming relationships
    try
    {
        AsyncPageable<IncomingRelationship> rels = client.GetIncomingRelationshipsAsync(child);

        await foreach (IncomingRelationship ie in rels)
        {
            if (ie.RelationshipName == relname)
                return (ie.SourceId);
        }
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error in retrieving parent:{exc.Status}:{exc.Message}");
    }
    return null;
}
```

És a Utility függvény a Twin javításához:
```csharp
public static async Task UpdateTwinProperty(DigitalTwinsClient client, string twinId, string propertyPath, object value, ILogger log)
{
    // If the twin does not exist, this will log an error
    try
    {
        // Update twin property
        UpdateOperationsUtility uou = new UpdateOperationsUtility();
        uou.AppendAddOp(propertyPath, value);
        await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
    }
    catch (RequestFailedException exc)
    {
        log.LogInformation($"*** Error:{exc.Status}/{exc.Message}");
    }
}
```

Most már rendelkezik egy Azure-függvénnyel, amely a IoT Hubból érkező forgatókönyv-adatok olvasására és értelmezésére van felszerelve.

## <a name="debug-azure-function-apps-locally"></a>Az Azure Function apps helyi hibakeresése

Az Azure functions hibakeresése helyileg Event Grid triggerrel lehetséges. További információ erről: [Event Grid trigger helyi hibakeresése](../azure-functions/functions-debug-event-grid-trigger-local.md).

## <a name="next-steps"></a>További lépések

További információ az Azure Digital Twins szolgáltatással való bejövő és kimenő adatforgalomról:
* [Fogalmak: integráció más szolgáltatásokkal](concepts-integration.md)
