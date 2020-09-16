---
title: Eszközök automatikus felügyelete a DPS használatával
titleSuffix: Azure Digital Twins
description: A Device kiépítési szolgáltatás (DPS) használatával megtudhatja, hogyan állíthat be egy automatizált folyamatot a IoT-eszközök kiépítéséhez és kivonásához az Azure Digital Twins-ban.
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efc507cb69b3368a2102b6de0b905657d5806ef2
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90561431"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Eszközök automatikus felügyelete az Azure Digital Twins-ben a Device kiépítési szolgáltatás (DPS) használatával

Ebből a cikkből megtudhatja, hogyan integrálhatja az Azure Digital ikreket a [Device kiépítési szolgáltatással (DPS)](../iot-dps/about-iot-dps.md).

A cikkben ismertetett megoldás lehetővé teszi, hogy automatizálja IoT Hub eszközök Azure digitális Twins **_-ban_** való kiépítésének **_és_** kivonásának folyamatát az eszköz kiépítési szolgáltatásának használatával. 

A _kiépítési_ és kivonási szakaszokkal kapcsolatos további információkért _, valamint az_ összes vállalati IoT-projekthez közös általános eszközkezelés szakaszának jobb megismeréséhez tekintse meg a IoT hub eszközkezelés dokumentációjának [ *eszközök életciklusa* című szakaszát](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) .

## <a name="prerequisites"></a>Előfeltételek

A kiépítés beállítása előtt rendelkeznie kell egy, a modelleket és az ikreket tartalmazó **Azure digitális Twins-példánnyal** . Ezt a példányt úgy is be kell állítani, hogy az adatok alapján frissítse a digitális Twin-információkat. 

Ha még nem rendelkezik ezzel a beállítással, létrehozhatja azt az Azure Digital Twins [*oktatóanyagának használatával: Kapcsolódás végpontok közötti megoldáshoz*](tutorial-end-to-end.md). Az oktatóanyag végigvezeti egy Azure digitális Twins-példány beállításán, amely modellekkel és ikrekkel, csatlakoztatott Azure- [IoT Hubokkal](../iot-hub/about-iot-hub.md)és számos [Azure-funkcióval](../azure-functions/functions-overview.md) rendelkezik az adatfolyamok propagálásához.

A példány beállításakor az alábbi értékekre lesz szüksége a cikk későbbi részében. Ha újra össze kell gyűjtenie ezeket az értékeket, az alábbi hivatkozásokat követve adhat meg útmutatást.
* Azure digitális Twins-példány **_állomásneve_** ([Keresés a portálon](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Az Azure Event Hubs a kapcsolatok karakterláncának **_összekapcsolási karakterlánca_** ([Keresés a portálon](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

Ez a minta egy **eszköz-szimulátort** is használ, amely az eszközök kiépítési szolgáltatásával történő telepítést is magában foglalja. Az eszköz-szimulátor itt található: [Azure digitális Twins és IoT hub integrációs minta](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Szerezze be a minta projektet a gépen a minta hivatkozásra kattintva, majd a cím alatt található *zip letöltése* gombra kattintva. Bontsa ki a letöltött mappát.

Az eszköz-szimulátor **Node.js**10.0. x vagy újabb verzión alapul. [*A fejlesztési környezet előkészítése*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) leírja, hogyan telepítheti a Windows vagy Linux rendszerhez készült Node.js az oktatóanyaghoz.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi ábra a megoldás architektúráját mutatja be az Azure Digital Twins és a Device kiépítési szolgáltatás használatával. Az eszköz kiépítése és a kivonási folyamat is megjelenik.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Egy eszköz és számos Azure-szolgáltatás áttekintése egy végpontok közötti forgatókönyvben. Az adatforgalom a termosztátos eszköz és a DPS közötti kapcsolaton keresztül zajlik. Az adatok a DPS-ből a IoT Hubba és az Azure digitális Twins-ba is áramlanak a kiosztás címkével ellátott Azure-függvény használatával. A manuális eszköz törlése műveletből származó adatok IoT Hub > Event Hubs > Azure Functions > Azure digitális Ikreken keresztül áramlanak.":::

Ez a cikk két szakaszra oszlik:
* [*Eszköz automatikus kiépítése az eszköz kiépítési szolgáltatásával*](#auto-provision-device-using-device-provisioning-service)
* [*Eszköz automatikus kivonása IoT Hub életciklus-események használatával*](#auto-retire-device-using-iot-hub-lifecycle-events)

Az architektúra egyes lépéseinek mélyebb magyarázatához tekintse meg az egyes szakaszokat a cikk későbbi részében.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Eszköz automatikus kiépítése az eszköz kiépítési szolgáltatásával

Ebben a szakaszban az eszköz kiépítési szolgáltatását az Azure digitális Twins-hoz csatlakoztatja, hogy automatikusan kiépítse az eszközöket az alábbi elérési úton. Ez a [korábban](#solution-architecture)bemutatott teljes architektúra kivonata.

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Flow kiépítése – a megoldás architektúra-diagramjának kivonata, amely a folyamat egyes részeit címkézi. Az adatforgalom egy termosztátos eszköz és a DPS (1 for Device > DPS és 5 a DPS > eszköz esetében) között zajlik. Az adatok a DPS-ből a IoT Hub (4) és az Azure digitális Twins (3) rendszerbe is áramlanak a kiosztás (2) címkével ellátott Azure-függvény használatával.":::

Itt látható a folyamat folyamatábrája:
1. Az eszköz kapcsolatba lép a DPS-végponttal, és azonosítja az azonosító adatokat, hogy igazolja az identitását.
2. A DPS érvényesíti az eszköz identitását a regisztrációs azonosító és a kulcs érvényesítésével a beléptetési listán, és egy [Azure-függvényt](../azure-functions/functions-overview.md) hív meg a foglalás elvégzéséhez.
3. Az Azure-függvény létrehoz egy új [Twin](concepts-twins-graph.md) -et az Azure digitális Twins szolgáltatásban az eszközhöz.
4. A DPS regisztrálja az eszközt egy IoT hub-ban, és feltölti az eszközt a kívánt Twin állapotba.
5. Az IoT hub az eszköz AZONOSÍTÓjának adatait és az IoT hub kapcsolódási adatait adja vissza az eszközhöz. Az eszköz mostantól képes csatlakozni az IoT hubhoz.

Az alábbi szakaszokban ismertetjük az automatikus kiépítésű eszköz folyamatának lépéseit.

### <a name="create-a-device-provisioning-service"></a>Eszköz-kiépítési szolgáltatás létrehozása

Ha egy új eszközt kiépítenek az eszköz kiépítési szolgáltatásával, az adott eszközhöz egy új Twin is létrehozható az Azure digitális Twins-ban.

Hozzon létre egy eszköz-kiépítési szolgáltatási példányt, amely a IoT-eszközök kiépítésére szolgál majd. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: gyors útmutató [*: a IoT hub Device Provisioning Service beállítása a Azure Portal*](../iot-dps/quick-setup-auto-provision.md).

A következő Azure CLI-parancs létrehoz egy eszköz-kiépítési szolgáltatást. Meg kell adnia egy nevet, egy erőforráscsoportot és egy régiót. A parancs [Cloud Shell](https://shell.azure.com)vagy helyileg is futtatható, ha telepítve van az Azure CLI a [gépen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

```azurecli
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ezután létre fog hozni egy HTTP-kérelem által aktivált függvényt egy Function alkalmazáson belül. Használhatja a teljes körű oktatóanyagban létrehozott Function alkalmazást ([*oktatóanyag: végpontok közötti megoldás összekapcsolását*](tutorial-end-to-end.md)) vagy a sajátját.

Ezt a függvényt az eszköz kiépítési szolgáltatása fogja használni egy új eszköz [kiosztására szolgáló egyéni foglalási szabályzatban](../iot-dps/how-to-use-custom-allocation-policies.md) . További információ a HTTP-kérelmek Azure functions használatával történő használatáról: [*Azure HTTP-kérelem trigger Azure functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

A Function app-projekten belül adjon hozzá egy új függvényt. Továbbá adjon hozzá egy új NuGet-csomagot a projekthez: `Microsoft.Azure.Devices.Provisioning.Service` .

Az újonnan létrehozott függvény kódlapja fájlban illessze be a következő kódot.

```C#
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Devices.Shared;
using Microsoft.Azure.Devices.Provisioning.Service;
using System.Net.Http;
using Azure.Identity;
using Azure.DigitalTwins.Core;
using Azure.Core.Pipeline;
using Azure;
using System.Collections.Generic;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DpsAdtAllocationFunc
    {
        const string adtAppId = "https://digitaltwins.azure.net";
        private static string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DpsAdtAllocationFunc")]
        public static async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger log)
        {
            // Get request body
            string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
            log.LogDebug($"Request.Body: {requestBody}");
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Get registration ID of the device
            string regId = data?.deviceRuntimeContext?.registrationId;

            bool fail = false;
            string message = "Uncaught error";
            ResponseObj obj = new ResponseObj();

            // Must have unique registration ID on DPS request 
            if (regId == null)
            {
                message = "Registration ID not provided for the device.";
                log.LogInformation("Registration ID: NULL");
                fail = true;
            }
            else
            {
                string[] hubs = data?.linkedHubs.ToObject<string[]>();

                // Must have hubs selected on the enrollment
                if (hubs == null)
                {
                    message = "No hub group defined for the enrollment.";
                    log.LogInformation("linkedHubs: NULL");
                    fail = true;
                }
                else
                {
                    // Find or create twin based on the provided registration ID and model ID
                    dynamic payloadContext = data?.deviceRuntimeContext?.payload;
                    string dtmi = payloadContext.modelId;
                    log.LogDebug($"payload.modelId: {dtmi}");
                    string dtId = await FindOrCreateTwin(dtmi, regId, log);

                    // Get first linked hub (TODO: select one of the linked hubs based on policy)
                    obj.iotHubHostName = hubs[0];

                    // Specify the initial tags for the device.
                    TwinCollection tags = new TwinCollection();
                    tags["dtmi"] = dtmi;
                    tags["dtId"] = dtId;

                    // Specify the initial desired properties for the device.
                    TwinCollection properties = new TwinCollection();

                    // Add the initial twin state to the response.
                    TwinState twinState = new TwinState(tags, properties);
                    obj.initialTwin = twinState;
                }
            }

            log.LogDebug("Response: " + ((obj.iotHubHostName != null) ? JsonConvert.SerializeObject(obj) : message));

            return (fail)
                ? new BadRequestObjectResult(message)
                : (ActionResult)new OkObjectResult(obj);
        }

        public static async Task<string> FindOrCreateTwin(string dtmi, string regId, ILogger log)
        {
            // Create Digital Twins client
            var cred = new ManagedIdentityCredential(adtAppId);
            var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

            // Find existing twin with registration ID
            string dtId;
            string query = $"SELECT * FROM DigitalTwins T WHERE $dtId = '{regId}' AND IS_OF_MODEL('{dtmi}')";
            AsyncPageable<string> twins = client.QueryAsync(query);
            
            await foreach (string twinJson in twins)
            {
                // Get DT ID from the Twin
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' with Registration ID '{regId}' found in DT");
                return dtId;
            }

            // Not found, so create new twin
            log.LogInformation($"Twin ID not found, setting DT ID to regID");
            dtId = regId; // use the Registration ID as the DT ID

            // Define the model type for the twin to be created
            Dictionary<string, object> meta = new Dictionary<string, object>()
            {
                { "$model", dtmi }
            };
            // Initialize the twin properties
            Dictionary<string, object> twinProps = new Dictionary<string, object>()
            {
                { "$metadata", meta }
            };
            twinProps.Add("Temperature", 0.0);
            await client.CreateDigitalTwinAsync(dtId, System.Text.Json.JsonSerializer.Serialize<Dictionary<string, object>>(twinProps));
            log.LogInformation($"Twin '{dtId}' created in DT");

            return dtId;
        }
    }

    public class ResponseObj
    {
        public string iotHubHostName { get; set; }
        public TwinState initialTwin { get; set; }
    }
}
```

Mentse a fájlt, majd tegye közzé újra a Function alkalmazást. A Function alkalmazás közzétételével kapcsolatos utasításokért tekintse meg a teljes körű oktatóanyag [*alkalmazás közzététele*](tutorial-end-to-end.md#publish-the-app) című szakaszát.

### <a name="configure-your-function"></a>A függvény konfigurálása

A következő lépésben környezeti változókat kell beállítania a Function alkalmazásban, amely tartalmazza a létrehozott Azure digitális Twins-példányra mutató hivatkozást. Ha a teljes körű oktatóanyagot használta ([*oktatóanyag: végpontok közötti megoldás összekötése*](tutorial-end-to-end.md)), a beállítás már konfigurálva lesz.

Adja hozzá a beállítást az alábbi Azure CLI-paranccsal:

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Győződjön meg arról, hogy az engedélyek és a felügyelt identitás szerepkör-hozzárendelés helyesen van konfigurálva a Function alkalmazáshoz a teljes körű oktatóanyagban az [*engedélyek hozzárendelése a Function alkalmazáshoz*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) című szakaszban leírtak szerint.

<!-- 
* Azure AD app registration **_Application (client) ID_** ([find in portal](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))

```azurecli
az functionapp config appsettings set --settings "AdtAppId=<Application (client)" ID> -g <resource group> -n <your App Service (function app) name> 
``` -->

### <a name="create-device-provisioning-enrollment"></a>Eszköz kiépítési regisztrációjának létrehozása

Ezután létre kell hoznia egy regisztrációt az eszköz kiépítési szolgáltatásában egy **Egyéni foglalási függvénnyel**. Az egyéni kiosztási szabályzatokról szóló cikk az eszköz üzembe helyezése [*és az*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) [*egyedi eszközök kulcsai*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) szakasza című cikkben ismertetett útmutatást követve végezze el az alábbi utasításokat.

A folyamat során a rendszer az imént létrehozott függvényhez csatolja a beléptetést, ha kiválasztja a függvényt a lépés során, hogy **kiválassza, hogyan kívánja hozzárendelni az eszközöket a hubokhoz**. A regisztráció létrehozása után a rendszer később a beléptetési nevet és az elsődleges vagy másodlagos SAS-kulcsot fogja használni az eszköz szimulátorjának konfigurálásához ehhez a cikkhez.

### <a name="set-up-the-device-simulator"></a>Az eszköz-szimulátor beállítása

Ez a minta egy eszköz-szimulátort használ, amely az eszközök kiépítési szolgáltatásával történő üzembe helyezést tartalmaz. Az eszköz-szimulátor itt található: [Azure digitális Twins és IoT hub integrációs minta](https://docs.microsoft.com/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Ha még nem töltötte le a mintát, most lépjen a minta hivatkozásra, és válassza a cím alatt a *zip letöltése* gombot. Bontsa ki a letöltött mappát.

Nyisson meg egy parancssorablakot, és navigáljon a letöltött mappába, majd az *eszköz-szimulátor* könyvtárba. Telepítse a projekt függőségeit a következő parancs használatával:

```cmd
npm install
```

Ezután másolja a *. env. template* fájlt egy új, *. env*nevű fájlba, és töltse ki az alábbi beállításokat:

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Mentse és zárja be a fájlt.

### <a name="start-running-the-device-simulator"></a>Az eszköz-szimulátor futtatásának megkezdése

A parancssorablakban még mindig a *Device-Simulator* könyvtárban indítsa el az eszközt a következő parancs használatával:

```cmd
node .\adt_custom_register.js
```

Látnia kell az eszközt, amely regisztrálva van, és csatlakoztatva van IoT Hubhoz, majd megkezdi az üzenetek küldését.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Az eszközök regisztrálását és az üzenetek küldését Parancsablak bemutató":::

### <a name="validate"></a>Érvényesítés

A cikkben beállított folyamat eredményeképpen az eszköz automatikusan regisztrálva lesz az Azure digitális Twins-ban. A következő [Azure digitális Twins CLI](how-to-use-cli.md) -paranccsal megkeresheti az eszköz két részét a létrehozott Azure digitális Twins-példányban.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Meg kell jelennie az Azure Digital Twins-példányban található eszköz két részének.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Újonnan létrehozott Twin Parancsablak":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Eszköz automatikus kivonása IoT Hub életciklus-események használatával

Ebben a szakaszban IoT Hub életciklus-eseményeket csatlakoztat az Azure digitális Twins-hoz, hogy az alábbi elérési úton automatikusan kivonja az eszközöket. Ez a [korábban](#solution-architecture)bemutatott teljes architektúra kivonata.

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Az eszköz kivonása – a megoldás architektúra diagramjának kivonata, amely a folyamat egyes részeit címkézi. A termosztátos eszköz a diagramon az Azure-szolgáltatásokhoz való csatlakozás nélkül jelenik meg. A manuális eszköz törlése műveletből származó adatok IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure digitális Twins (3) használatával áramlanak.":::

Itt látható a folyamat folyamatábrája:
1. Egy külső vagy manuális folyamat elindít egy eszköz törlését IoT Hubban.
2. IoT Hub törli az eszközt, és létrehoz egy [eszköz életciklusa](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) eseményt, amely az [Event hub](../event-hubs/event-hubs-about.md)-ba lesz irányítva.
3. Egy Azure-függvény törli az eszköz két részét az Azure digitális Twins-ban.

Az alábbi szakasz végigvezeti az automatikus kivonási folyamat beállításának lépésein.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Most létre kell hoznia egy Azure [Event hub](../event-hubs/event-hubs-about.md)-t, amely a IoT hub életciklus eseményeinek fogadására szolgál majd. 

Az alábbi információk segítségével hajtsa végre az [*Event hub létrehozása*](../event-hubs/event-hubs-create.md) rövid útmutatójában ismertetett lépéseket:
* Ha a teljes körű oktatóanyagot használja ([*oktatóanyag: végpontok közötti megoldás összekötése*](tutorial-end-to-end.md)), akkor újra felhasználhatja a végpontok közötti oktatóanyaghoz létrehozott erőforráscsoportot.
* Nevezze el az Event hub- *lifecycleevents*, vagy válasszon ki egy másikat, és jegyezze fel a létrehozott névteret. Ezeket akkor fogja használni, amikor beállítja az életciklus függvényt, és IoT Hub útvonalat a következő szakaszokban.

### <a name="create-an-azure-function"></a>Azure-függvény létrehozása

Ezután létre fog hozni egy Event Hubs által aktivált függvényt egy Function alkalmazásban. Használhatja a teljes körű oktatóanyagban létrehozott Function alkalmazást ([*oktatóanyag: végpontok közötti megoldás összekapcsolását*](tutorial-end-to-end.md)) vagy a sajátját. 

Nevezze el az Event hub-eseményindítót *lifecycleevents*, és kapcsolódjon az Event hub-eseményindítóhoz az előző lépésben létrehozott Event hubhoz. Ha más Event hub-nevet használt, módosítsa úgy, hogy az az alábbi eseményindító nevével egyezzen.

Ez a függvény a IoT Hub eszköz életciklusa eseményt fogja használni egy meglévő eszköz kivonásához. Az életciklus eseményeivel kapcsolatos további információkért lásd: [*IoT hub nem telemetria események*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). További információ a Event Hubs Azure functions használatával történő használatáról: [*azure Event Hubs trigger Azure Functionshoz*](../azure-functions/functions-bindings-event-hubs-trigger.md).

A közzétett Function alkalmazásban vegyen fel egy *Event hub eseményindító*típusú új függvényt, és illessze be az alábbi kódot.

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Threading.Tasks;
using Azure;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Samples.AdtIothub
{
    public static class DeleteDeviceInTwinFunc
    {
        private static string adtAppId = "https://digitaltwins.azure.net";
        private static readonly string adtInstanceUrl = System.Environment.GetEnvironmentVariable("ADT_SERVICE_URL", EnvironmentVariableTarget.Process);
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("DeleteDeviceInTwinFunc")]
        public static async Task Run(
            [EventHubTrigger("lifecycleevents", Connection = "EVENTHUB_CONNECTIONSTRING")] EventData[] events, ILogger log)
        {
            var exceptions = new List<Exception>();

            foreach (EventData eventData in events)
            {
                try
                {
                    //log.LogDebug($"EventData: {System.Text.Json.JsonSerializer.Serialize(eventData)}");

                    string opType = eventData.Properties["opType"] as string;
                    if (opType == "deleteDeviceIdentity")
                    {
                        string deviceId = eventData.Properties["deviceId"] as string;
                        
                        // Create Digital Twin client
                        var cred = new ManagedIdentityCredential(adtAppId);
                        var client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });

                        // Find twin based on the original Registration ID
                        string regID = deviceId; // simple mapping
                        string dtId = await GetTwinId(client, regID, log);
                        if (dtId != null)
                        {
                            await DeleteRelationships(client, dtId, log);

                            // Delete twin
                            await client.DeleteDigitalTwinAsync(dtId);
                            log.LogInformation($"Twin '{dtId}' deleted in DT");
                        }
                    }
                }
                catch (Exception e)
                {
                    // We need to keep processing the rest of the batch - capture this exception and continue.
                    exceptions.Add(e);
                }
            }

            if (exceptions.Count > 1)
                throw new AggregateException(exceptions);

            if (exceptions.Count == 1)
                throw exceptions.Single();
        }


        public static async Task<string> GetTwinId(DigitalTwinsClient client, string regId, ILogger log)
        {
            string query = $"SELECT * FROM DigitalTwins T WHERE T.$dtId = '{regId}'";
            AsyncPageable<string> twins = client.QueryAsync(query);
            await foreach (string twinJson in twins)
            {
                JObject twin = (JObject)JsonConvert.DeserializeObject(twinJson);
                string dtId = (string)twin["$dtId"];
                log.LogInformation($"Twin '{dtId}' found in DT");
                return dtId;
            }

            return null;
        }

        public static async Task DeleteRelationships(DigitalTwinsClient client, string dtId, ILogger log)
        {
            var relationshipIds = new List<string>();

            AsyncPageable<string> relationships = client.GetRelationshipsAsync(dtId);
            await foreach (var relationshipJson in relationships)
            {
                BasicRelationship relationship = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relationshipJson);
                relationshipIds.Add(relationship.Id);
            }

            foreach (var relationshipId in relationshipIds)
            {
                client.DeleteRelationship(dtId, relationshipId);
                log.LogInformation($"Twin '{dtId}' relationship '{relationshipId}' deleted in DT");
            }
        }
    }
}
```

Mentse a projektet, majd tegye közzé újra a Function alkalmazást. A Function alkalmazás közzétételével kapcsolatos utasításokért tekintse meg a teljes körű oktatóanyag [*alkalmazás közzététele*](tutorial-end-to-end.md#publish-the-app) című szakaszát.

### <a name="configure-your-function"></a>A függvény konfigurálása

A következő lépésben környezeti változókat kell beállítania a Function alkalmazásban, amely tartalmazza a létrehozott Azure digitális Twins-példányra és az Event hub-ra mutató hivatkozást. Ha a teljes körű oktatóanyagot használta ([*oktatóanyag: végpontok közötti megoldás összekötése*](./tutorial-end-to-end.md)), az első beállítás már konfigurálva lesz.

Adja hozzá a beállítást az Azure CLI-paranccsal. A parancs [Cloud Shell](https://shell.azure.com)vagy helyileg is futtatható, ha telepítve van az Azure CLI a [gépen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

```azurecli
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Ezután konfigurálnia kell a Function környezeti változót az újonnan létrehozott Event hubhoz való csatlakozáshoz.

```azurecli
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Győződjön meg arról, hogy az engedélyek és a felügyelt identitás szerepkör-hozzárendelés helyesen van konfigurálva a Function alkalmazáshoz a teljes körű oktatóanyagban az [*engedélyek hozzárendelése a Function alkalmazáshoz*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) című szakaszban leírtak szerint.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>IoT Hub útvonal létrehozása életciklus-események esetén

Most be kell állítania egy IoT Hub útvonalat, hogy átirányítsa az eszköz életciklusának eseményeit. Ebben az esetben kifejezetten a által azonosított eszköz-törlési eseményeket fogja figyelni `if (opType == "deleteDeviceIdentity")` . Ez elindítja a digitális kettős elem törlését, az eszköz kivonásának véglegesítését és a digitális ikerét.

A IoT Hub útvonal létrehozásával kapcsolatos útmutatást a cikk ismerteti: [*IoT hub üzenet-útválasztás használata az eszközről a felhőbe irányuló üzenetek küldéséhez a különböző végpontokra*](../iot-hub/iot-hub-devguide-messages-d2c.md). A *nem telemetria események* szakasza azt mutatja be, hogy az **eszköz életciklusával kapcsolatos események** az útvonal adatforrásaként használhatók.

A telepítéshez szükséges lépések a következők:
1. Hozzon létre egy egyéni IoT Hub Event hub-végpontot. A végpontnak az [*Event hub létrehozása*](#create-an-event-hub) szakaszban létrehozott Event hub-t kell megcéloznia.
2. Adjon hozzá egy *eszköz életciklus-események* útvonalát. Használja az előző lépésben létrehozott végpontot. Az eszköz életciklus-eseményeinek korlátozásával csak az útválasztási lekérdezés hozzáadásával lehet elküldeni a DELETE eseményeket `opType='deleteDeviceIdentity'` .
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Útvonal hozzáadása":::

Miután elvégezte a folyamatot, minden úgy van beállítva, hogy teljes körűen kivonja az eszközöket.

### <a name="validate"></a>Érvényesítés

A kivonási folyamat elindításához kézzel kell törölnie az eszközt IoT Hubról.

A [cikk első felében](#auto-provision-device-using-device-provisioning-service)létrehozott egy eszközt IoT hub és egy megfelelő digitális Twin-ben. 

Most nyissa meg a IoT Hub és törölje az eszközt (ezt megteheti egy [Azure CLI-paranccsal](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) vagy a [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

Az eszköz automatikusan el lesz távolítva az Azure digitális Twins-ból. 

A következő [Azure digitális Twins CLI](how-to-use-cli.md) -paranccsal ellenőrizheti, hogy az eszköz két példánya az Azure Digital Twins-példánnyal lett-e törölve.

```azurecli
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Látnia kell, hogy az eszköz két része nem található az Azure Digital Twins-példányban.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Parancsablak a Twin nem található":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az ebben a cikkben létrehozott erőforrásokra, a következő lépésekkel törölheti őket.

Az Azure Cloud Shell vagy a helyi Azure CLI használatával törölheti az erőforráscsoport összes Azure-erőforrását az az [Group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal. Ezzel eltávolítja az erőforráscsoportot; Az Azure Digital Twins-példány; az IoT hub és a hub-eszköz regisztrációja; az Event Grid-témakör és a hozzá tartozó előfizetések; az Event hub-névtér és mindkét Azure Functions alkalmazás, beleértve a kapcsolódó erőforrásokat, például a tárolást.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. 

```azurecli
az group delete --name <your-resource-group>
```
<!-- 
Next, delete the Azure AD app registration you created for your client app with this command:

```azurecli
az ad app delete --id <your-application-ID>
``` -->

Ezután törölje a helyi gépről letöltött Project Sample mappát.

## <a name="next-steps"></a>További lépések

Az eszközökhöz létrehozott digitális Twins-ket az Azure Digital Twins szolgáltatásban tárolja a rendszer, de a modell adataival és a szervezet többszintű hierarchiájának használatával gazdagíthatja őket. Ha többet szeretne megtudni erről a fogalomról, olvassa el a következőt:

* [*Fogalmak: digitális ikrek és a Twin gráf*](concepts-twins-graph.md)

Egyéni logikát írhat arra, hogy automatikusan megadja ezeket az adatokat az Azure digitális Twins-ban már tárolt modell-és gráf-adatok használatával. Az alábbi témakörben olvashat bővebben arról, hogyan kezelheti, frissítheti és beolvashatja az adatokat az Twins-gráfból:

* [*Útmutató: digitális kettős kezelés*](how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)
