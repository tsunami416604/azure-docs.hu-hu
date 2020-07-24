---
title: Integrálás Time Series Insights
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan állíthatja be az esemény-útvonalakat az Azure Digital Ikrekből a Azure Time Series Insightsba.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9be44d29ab08a7404bfb14d691bb5bb339284714
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131599"
---
# <a name="integrate-digital-twins-with-azure-time-series-insights"></a>Digitális ikrek integrálása Azure Time Series Insights

Ebből a referenciából megtudhatja, hogyan integrálhatja az Azure Digital Twins-t [Azure Time Series Insights (ÁME)](../time-series-insights/overview-what-is-tsi.md)használatával. Ez a megoldás lehetővé teszi a IoT-megoldással kapcsolatos korábbi adatok összegyűjtését és elemzését. Az Azure Digital Twins kiválóan alkalmas az adatok Time Series Insightsba való takarmányozására, mivel lehetővé teszi több adatfolyam összekapcsolását és az adatok egységesítését, mielőtt elküldené azt a Time Series Insightsba. 

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi elérési úton megjelenő Time Series-bepillantást az Azure Digital Twins-ba fogja csatolni.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Az Azure-szolgáltatások egy végpontok közötti forgatókönyvben, kiemelve Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="prerequisites"></a>Előfeltételek

* Szüksége lesz egy Azure Digital Twins-példányra, amely néhány alkalommal frissítheti a Twin-adatokat, hogy megtekintse az adatokat a Time Series Insightsban. 
    * Ha még nem rendelkezik ilyennel, kövesse az Azure Digital Twins [*oktatóanyagot: kapcsolódjon egy teljes körű megoldáshoz*](./tutorial-end-to-end.md) , amely egy Azure digitális Twins-példányt és egy virtuális IoT-eszközt hoz létre kettős változtatások létrehozásához.

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Útvonal és szűrő létrehozása a kettős frissítési értesítésekhez

Az Azure Digital Twins-példányok [dupla frissítési eseményeket](how-to-interpret-event-data.md) bocsátanak ki, amikor a Twin állapot frissül. A rendszer létrehoz egy útvonalat, amely további feldolgozás céljából irányítja ezeket a frissítési eseményeket egy Event hub-ra.

Az Azure Digital Twins [*oktatóanyaga: egy végpontok közötti megoldás összekapcsolásával*](./tutorial-end-to-end.md) elsajátíthatja egy olyan forgatókönyvet, amelyben egy hőmérőt használ egy szoba Twin-hez csatolt hőmérséklet-attribútum frissítéséhez. Ez a minta a Twin-frissítéseket használja ahelyett, hogy telemetria egy IoT-eszközről, amely lehetővé teszi az alapul szolgáló adatforrás módosítását anélkül, hogy frissítenie kellene a Time Series Insights logikát.

1. Hozzon létre egy Event hub-névteret, amely az Azure digitális Twins-példányának eseményeit fogja kapni. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md).

    ```azurecli-interactive
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Eseményközpont létrehozása.

    ```azurecli-interactive
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```

3. Hozzon létre egy [engedélyezési szabályt](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) a küldési és fogadási engedélyekkel.

    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

4. Hozzon létre egy végpontot, amely összekapcsolja az Event Grid-témakört az Azure digitális Twins szolgáltatással.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace> --eventhub <twins event hub name> --eventhub-policy <twins auth rule> -n <your-Azure-Digital-Twins-instance-name>
    ```

5. Hozzon létre egy útvonalat az Azure Digital Ikrekben, hogy dupla frissítési eseményt küldjön a végpontnak. Az ebben az útvonalban lévő szűrő csak a kettős frissítési üzeneteket továbbítja a végpontnak.

    ```azurecli
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Hub-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása 

Ezután létre fog hozni egy Event Hubs által aktivált függvényt egy új Function alkalmazásban, a Function alkalmazást a teljes körű oktatóanyagban ([*oktatóanyag: teljes körű megoldás összekapcsolása*](./tutorial-end-to-end.md)). Ez a függvény átalakítja ezeket a frissítéseket az eredeti űrlapról JSON-javítási dokumentumként a csak frissített és hozzáadott értékeket tartalmazó JSON-objektumokra.

További információ a Event Hubs Azure functions használatával történő használatáról: [*azure Event Hubs trigger Azure Functionshoz*](../azure-functions/functions-bindings-event-hubs-trigger.md).

A közzétett Function alkalmazásban cserélje le a függvény kódját a következő kódra.

```C#
using Microsoft.Azure.EventHubs;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System.Threading.Tasks;
using System.Text;
using System.Collections.Generic;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoTSI
    { 
        [FunctionName("ProcessDTUpdatetoTSI")]
        public static async Task Run(
            [EventHubTrigger("twins-fx-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("alkarche-tsi-demo-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
            ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(Encoding.UTF8.GetString(myEventHubMessage.Body));
            log.LogInformation("Reading event:" + message.ToString());

            // Read values that are replaced or added
            Dictionary<string, object> tsiUpdate = new Dictionary<string, object>();
            foreach (var operation in message["patch"]) {
                if (operation["op"].ToString() == "replace" || operation["op"].ToString() == "add")
                    //Convert from JSON patch path to a flattened property for TSI
                    //Example input: /Front/Temperature
                    //        output: Front.Temperature
                    string path = operation["path"].ToString().Substring(1);                    
                    path = path.Replace("/", ".");                    
                    tsiUpdate.Add(path, operation["value"]);
            }
            //Send an update if updates exist
            if (tsiUpdate.Count>0){
                tsiUpdate.Add("$dtId", myEventHubMessage.Properties["cloudEvents:subject"]);
                await outputEvents.AddAsync(JsonConvert.SerializeObject(tsiUpdate));
            }
        }
    }
}
```

Innen a függvény ezután elküldi az általa létrehozott JSON-objektumokat egy második esemény-hubhoz, amely Time Series Insightshoz fog csatlakozni.

## <a name="send-telemetry-to-an-event-hub"></a>Telemetria küldése az Event hub-nak

Most hozzon létre egy második Event hub-t, és konfigurálja a függvényt az Event hub kimenetének továbbításához. Ez az Event hub ezután csatlakozik Time Series Insightshoz.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md).

1. Az Event hub-névtér és az erőforráscsoport nevének előkészítése a korábbi verziókból 

2. Eseményközpont létrehozása
    ```azurecli-interactive
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <TSI event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
    ```
3. [Engedélyezési szabály](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) létrehozása küldési és fogadási engedélyekkel
    ```azurecli-interactive
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <event hub name> --name <TSI auth rule>
    ```

### <a name="configure-your-function"></a>A függvény konfigurálása

Egy környezeti változót kell beállítania a Function alkalmazásban, amely az Event hub kapcsolati karakterláncát tartalmazza.

#### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Az Time Series Insights Event hub kapcsolati karakterláncának beállítása

1. Szerezze be az Time Series Insights hub felett létrehozott engedélyezési szabályok [Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md) :

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <twins event hub name> --name <twins auth rule>
    ```

2. A Function alkalmazásban hozzon létre egy alkalmazást, amely tartalmazza a kapcsolatok karakterláncát:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

#### <a name="set-the-twins-event-hub-connection-string"></a>Az ikrek Event hub kapcsolati karakterláncának beállítása

1. Szerezze be az [Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md) az ikrek hubhoz fent létrehozott engedélyezési szabályokhoz.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. A Function alkalmazásban hozzon létre egy alkalmazást, amely tartalmazza a kapcsolatok karakterláncát:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-event-hub-connection-string> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights-példány létrehozása és összekötése

Ezután állítson be egy Time Series Insights-példányt, amely a második Event hub adatait fogja fogadni. További információ erről a folyamatról [ *: oktatóanyag: Azure Time Series INSIGHTS Gen2 TB-környezet beállítása*](../time-series-insights/tutorials-set-up-tsi-environment.md)

1. A Azure Portal Time Series Insights erőforrás létrehozásának megkezdéséhez. 
    1. Válassza ki a **TB (előzetes verzió)** árképzési szintet.
    2. Ehhez a környezethez ki kell választania egy idősorozat-azonosítót. Az idősorozat-azonosító legfeljebb három olyan érték lehet, amelyet a Time Series Insightsban lévő adatok kereséséhez fog használni. Ebben az oktatóanyagban használhatja a **$dtId**. További információ az azonosító érték kiválasztásáról az [*ajánlott eljárásokban az idősorozat-azonosító kiválasztásához*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="A létrehozási portál UX Time Series Insights-környezethez. A TB (előzetes verzió) árképzési szintje be van jelölve, az idősorozat-azonosító tulajdonság neve pedig $dtId":::

2. Válassza a **Next (tovább): eseményforrás** lehetőséget, és válassza ki a Event Hubs adatokat a fenti listából. Emellett új Event Hubs fogyasztói csoportot is létre kell hoznia.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="A létrehozási portál UX Time Series Insights környezeti esemény forrásához. Az Event hub információi alapján hozza létre az eseményforrás adatait. Új fogyasztói csoportot is létrehoz.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>IoT-adatok küldésének megkezdése az Azure Digital Twinsba

Az adatok Time Series Insightsba való küldésének megkezdéséhez meg kell kezdenie a digitális Twin tulajdonságok frissítését az adatértékek módosításával. Használja az az [DT Twin Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) parancsot.

Ha a teljes körű oktatóanyagot használja a környezet beállításának támogatásához, megkezdheti a szimulált IoT-adatok küldését a projekt futtatásával `DeviceSimulator` Az Azure Digital Twins [*oktatóanyag: teljes körű megoldás összekapcsolásával*](tutorial-end-to-end.md). Az útmutató az oktatóanyag [*konfigurálása és futtatása a szimuláció*](tutorial-end-to-end.md#configure-and-run-the-simulation) szakaszban található.

## <a name="visualize-your-data-in-time-series-insights"></a>Jelenítse meg az adatait Time Series Insights

Az adatforgalom az Time Series Insights-példányba kerül, és készen áll az elemzésre. Kövesse az alábbi lépéseket a beérkező adatforgalom megismeréséhez.

1. Nyissa meg Time Series Insights-példányát a Azure Portalban. Látogasson el az áttekintésben látható Time Series Insights Explorer URL-címére.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="A Time Series Insights-környezet áttekintés lapján kattintson a Time Series Insights Explorer URL-címére":::

2. Az Explorerben a bal oldali három ikrek látható. Kattintson a **thermostat67**elemre, válassza a **patch_value**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Kattintson a * * thermostat67 * * elemre, válassza a * * hőmérséklet * * elemet, majd kattintson * * Hozzáadás * *":::

3. Ekkor látnia kell a termosztátból a kezdeti hőmérséklet-leolvasásokat az alább látható módon. Ugyanazt a hőmérséklet-olvasási értéket frissíti a rendszer a room21 és a floor1, és párhuzamosan jelenítheti meg az adatfolyamokat.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="A kezdeti hőmérsékleti adatai az ÁME Explorerben vannak ábrázolva. 68 és 85 közötti véletlenszerű értékek sora":::

4. Ha lehetővé teszi, hogy a szimuláció jóval tovább fusson, a vizualizáció a következőképpen fog kinézni:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Az egyes Twin-sorok hőmérsékleti értékeit három, különböző színű párhuzamos vonal ábrázolja.":::

## <a name="next-steps"></a>További lépések

A digitális ikreket alapértelmezés szerint a rendszer a Time Series Insightsban lévő, lapos hierarchiában tárolja, de a modell adataival és a szervezet többszintű hierarchiájának használatával gazdagíthatja őket. A folyamattal kapcsolatos további információkért olvassa el a következőt: 

* [*Oktatóanyag: modell meghatározása és alkalmazása*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Egyéni logikát írhat arra, hogy automatikusan megadja ezeket az adatokat az Azure digitális Twins-ban már tárolt modell-és gráf-adatok használatával. Az alábbi hivatkozásokat követve további információkat olvashat az ikrek gráf információinak kezelésével, frissítésével és lekérésével kapcsolatban:

* [*Útmutató: digitális kettős kezelés*](./how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](./how-to-query-graph.md)