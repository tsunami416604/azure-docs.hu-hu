---
title: Azure Maps beltéri Térkép frissítése az Azure digitális Twins használatával
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan hozhat létre olyan Azure-függvényt, amely a Twin Graph és az Azure digitális Twins értesítései segítségével frissíti a Azure Mapsban megjelenő információkat.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.reviewer: baanders
ms.openlocfilehash: 50fade5179d5323a332d9b103f2fac17d402aa3f
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362849"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Azure Maps beltéri Térkép frissítése az Azure digitális Twins használatával

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Ez a cikk végigvezeti az Azure Digital Twins-adatok használatához szükséges lépéseken, hogy a [Azure Maps](../azure-maps/about-azure-maps.md)használatával egy *fedett térképen* megjelenített információkat frissítsen. Az Azure Digital Twins egy gráfot tárol a IoT, és a különböző végpontokra irányítja a telemetria, így tökéletes szolgáltatásként frissítheti a térképeken található információs átfedéseket.

Ez a útmutató a következő módon fog kiterjedni:

1. Az Azure Digital Twins-példány konfigurálása két frissítési esemény küldésére a [Azure functions](../azure-functions/functions-overview.md)függvényében.
2. Azure-függvény létrehozása Azure Maps Indoor Maps szolgáltatás stateset frissítéséhez.
3. A Maps ID és a Feature stateset AZONOSÍTÓjának tárolása az Azure Digital Twins gráfban.

### <a name="prerequisites"></a>Előfeltételek

* Kövesse az Azure digitális Twins [oktatóanyagát: teljes körű megoldás összekapcsolását](./tutorial-end-to-end.md).
    * Ezt a Twin-t egy további végponttal és útvonallal kell kiterjeszteni. Az oktatóanyagból egy másik függvényt is hozzáadhat a Function alkalmazáshoz. 
* Kövesse a Azure Maps [oktatóanyagot: a Azure Maps Creator használatával beltéri térképeket hozhat](../azure-maps/tutorial-creator-indoor-maps.md) létre, amelyekkel Azure Maps fedett térképet hozhat létre a *szolgáltatás stateset*.
    * A [statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) olyan dinamikus tulajdonságok (állapotok) gyűjteményei, amelyek adatkészlet-funkciókhoz, például helyiségekhez vagy berendezésekhez vannak rendelve. A fenti Azure Maps oktatóanyagban a funkció stateset a térképen megjelenített szoba állapotát tárolja.
    * Szüksége lesz a szolgáltatás *STATESET azonosítóra* és Azure Maps *előfizetés-azonosítóra*.

### <a name="topology"></a>Topológia

Az alábbi képen látható, hogy az oktatóanyag Indoor Maps integrációs elemei hogyan illeszkednek egy nagyobb, végpontok közötti Azure digitális Twins-forgatókönyvhöz.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Az Azure-szolgáltatások egy teljes körű forgatókönyvből álló nézete, amely kiemeli a beltéri térképek integrációs részletét" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Függvény létrehozása Térkép frissítéséhez az ikrek frissítésekor

Először létre kell hoznia egy útvonalat az Azure Digital Ikrekben, hogy az összes, az Event Grid-témakörbe tartozó eseményt továbbítsa. Ezután egy Azure-függvény használatával olvassa be ezeket a frissítési üzeneteket, és frissítse a szolgáltatás stateset a Azure Mapsban. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Útvonal és szűrő létrehozása a kettős frissítési értesítésekhez

Az Azure Digital Twins-példányok dupla frissítési eseményeket bocsátanak ki, amikor a Twin állapot frissül. Az [Azure Digital Twins oktatóanyaga: a fenti lépésekhez kapcsolódó végpontok közötti megoldás összekapcsolása](./tutorial-end-to-end.md) egy olyan forgatókönyvvel, amelyben egy hőmérőt használ egy szoba Twin-hez csatolt hőmérséklet-attribútum frissítéséhez. Ezt a megoldást kiterjesztheti az ikrek frissítési értesítéseire való feliratkozással, és ezekkel az információkkal frissítheti a térképeket.

Ez a minta közvetlenül a IoT-eszköz helyett a Twin szobaból olvassa be, ami rugalmasan változtatja meg az alapul szolgáló adatforrást a hőmérsékleten anélkül, hogy frissítenie kellene a leképezési logikát. Például több hőmérőt is hozzáadhat, vagy beállíthatja, hogy egy hőmérőt egy másik helyiséggel osszanak meg, anélkül, hogy a Térkép logikáját kellene frissítenie.

1. Hozzon létre egy Event Grid-témakört, amely az Azure Digital Twins-példánnyal származó eseményeket fogja kapni.
    ```azurecli
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Hozzon létre egy végpontot, amely összekapcsolja az Event Grid-témakört az Azure digitális Twins szolgáltatással.
    ```azurecli
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Hozzon létre egy útvonalat az Azure Digital Ikrekben, hogy dupla frissítési eseményt küldjön a végpontnak.
    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "{ "endpointId": "<endpoint-ID>","filter": "type = 'Microsoft.DigitalTwins.Twin.Update'"}"
    ```

## <a name="create-an-azure-function-to-update-maps"></a>Azure-függvény létrehozása a Maps frissítéséhez

Egy Event Grid által aktivált függvényt fog létrehozni a Function alkalmazásban a [végpontok közötti oktatóanyagban](./tutorial-end-to-end.md). Ez a függvény kicsomagolja ezeket az értesítéseket, és frissítéseket küld egy Azure Maps szolgáltatás stateset egy szoba hőmérsékletének frissítéséhez. 

Tekintse meg a következő dokumentumot a hivatkozási információkhoz: [Azure Event Grid trigger Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger).

Cserélje le a függvény kódját a következő kódra. Csak a Space ikrek frissítéseit szűri, a frissített hőmérsékletet olvassa be, majd elküldi ezeket az adatokat a Azure Mapsnak.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in our map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

A Function alkalmazásban két környezeti változót kell beállítania. Az egyik a [Azure Maps elsődleges előfizetési kulcs](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account), és az egyik a [Azure Maps stateset azonosítója](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-ID> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### <a name="view-live-updates-on-your-map"></a>Élő frissítések megtekintése a térképen

Az élő frissítés hőmérsékletének megtekintéséhez kövesse az alábbi lépéseket:

1. Szimulált IoT-adatok küldésének megkezdése a **DeviceSimulator** -projekt futtatásával az Azure Digital Twins [oktatóanyagból: Kapcsolódás végpontok közötti megoldáshoz](tutorial-end-to-end.md). Az ehhez tartozó útmutatást a a [*configure és a Run The szimuláció*](././tutorial-end-to-end.md#configure-and-run-the-simulation) szakaszban találja.
2. [A **Azure Maps beltéri** modul](../azure-maps/how-to-use-indoor-module.md) használatával jelenítheti meg a Azure Maps creatorben létrehozott beltéri térképeket.
    1. Másolja a HTML-t a [*példából: használja*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) a beltéri térképek című oktatóanyag beltéri térképek modulját [: használja a Azure Maps beltéri térképek modult](../azure-maps/how-to-use-indoor-module.md) egy helyi fájlba.
    1. Cserélje le a *tilesetId* és a *STATESETID* a helyi HTML-fájlba az értékekkel.
    1. Nyissa meg a fájlt a böngészőben.

Mindkét minta egy kompatibilis tartományba küldi a hőmérsékletet, ezért 30 másodpercenként a térképen a 121-es frissítés színét kell látnia.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Egy Office-Térkép, amely színes narancssárga termet 121":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>A Maps-információk tárolása az Azure Digital Twinsban

Most, hogy már rendelkezik hardcoded-megoldással a Maps-információk frissítéséhez, az Azure digitális Twins gráf használatával tárolhatja a belső Térkép frissítéséhez szükséges összes információt. Ez magában foglalja a stateset AZONOSÍTÓját, a Maps előfizetés AZONOSÍTÓját és az egyes térképekhez és helyekhez tartozó szolgáltatás AZONOSÍTÓját. 

Az adott példához tartozó megoldás a legfelső szintű helyek frissítését is magában foglalja a stateset-azonosító és a Maps előfizetés-azonosító attribútum használatával, valamint az egyes helyiségek frissítését, hogy azok rendelkezzenek a szolgáltatás azonosítójával. Ezeket az értékeket egyszer kell megadnia a Twin gráf inicializálásakor, majd le kell kérdezni ezeket az értékeket az egyes dupla frissítési eseményekhez.

A topológia konfigurációjától függően a három attribútumot a Térkép részletességéhez képest különböző szinteken fogja tudni tárolni.

## <a name="next-steps"></a>További lépések

Az alábbi hivatkozásokat követve további információkat olvashat az ikrek gráf információinak kezelésével, frissítésével és lekérésével kapcsolatban:

* [Útmutató: digitális ikrek kezelése](./how-to-manage-twin.md)
* [Útmutató: a Twin gráf lekérdezése](./how-to-query-graph.md)