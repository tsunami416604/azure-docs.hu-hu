---
title: Integrálás az Azure Time Series Insights szolgáltatással
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan állíthatja be az esemény-útvonalakat az Azure Digital Ikrekből a Azure Time Series Insightsba.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 7/14/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c6c5c9b00ec3309638a7c5618e5995c8c5f07b11
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2020
ms.locfileid: "90564368"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Az Azure Digital Twins integrálása Azure Time Series Insights

Ebből a cikkből megtudhatja, hogyan integrálhatja az Azure Digital Twins-t [Azure Time Series Insights (ÁME)](../time-series-insights/overview-what-is-tsi.md)használatával.

A cikkben ismertetett megoldás lehetővé teszi a IoT-megoldással kapcsolatos korábbi adatok összegyűjtését és elemzését. Az Azure Digital Twins kiválóan alkalmas az adatok Time Series Insightsba való takarmányozására, mivel lehetővé teszi több adatfolyam összekapcsolását és az adatok egységesítését, mielőtt elküldené azt a Time Series Insightsba. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt kapcsolatot hozna létre Time Series Insightsval, rendelkeznie kell egy **Azure digitális Twins-példánnyal**. Ezt a példányt úgy kell beállítani, hogy az adatok alapján frissítse a digitális Twin-adatokat, mivel a két adatot frissíteni kell, hogy az adatok nyomon kövessék a Time Series Insights. 

Ha még nem rendelkezik ezzel a beállítással, létrehozhatja azt az Azure Digital Twins [*oktatóanyagának használatával: Kapcsolódás végpontok közötti megoldáshoz*](./tutorial-end-to-end.md). Az oktatóanyag végigvezeti egy olyan Azure Digital Twins-példány beállításán, amely egy virtuális IoT-eszközzel működik a digitális dupla frissítések elindításához.

## <a name="solution-architecture"></a>Megoldásarchitektúra

Az alábbi elérési úton az Azure digitális Twins-hoz Time Series Insights fog kapcsolódni.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Az Azure-szolgáltatások egy végpontok közötti forgatókönyvben, kiemelve Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Útvonal és szűrő létrehozása a kettős frissítési értesítésekhez

Az Azure Digital Twins-példányok [dupla frissítési eseményeket](how-to-interpret-event-data.md) bocsátanak ki, amikor a Twin állapot frissül. Ebben a szakaszban egy Azure digitális Twins- [**esemény útvonalát**](concepts-route-events.md) fogja létrehozni, amely további feldolgozás céljából irányítja ezeket a frissítési eseményeket az Azure [Event Hubsba](../event-hubs/event-hubs-about.md) .

Az Azure Digital Twins [*oktatóanyaga: egy végpontok közötti megoldás összekapcsolása*](./tutorial-end-to-end.md) egy olyan forgatókönyv segítségével, amelyben egy hőmérőt használ egy hőmérséklet-attribútum frissítésére egy olyan digitális ikeren, amely egy helyet jelöl. Ez a minta a Twin-frissítésekre támaszkodik, és nem továbbítja a telemetria egy IoT-eszközről, így rugalmasan módosíthatja az alapul szolgáló adatforrást anélkül, hogy frissítenie kellene a Time Series Insights logikát.

1. Először hozzon létre egy Event hub-névteret, amely az Azure digitális Twins-példányának eseményeit fogja kapni. Használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md).

    ```azurecli
    # Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
    ```

2. Hozzon létre egy Event hubot a névtéren belül.

    ```azurecli
    # Create an event hub to receive twin change events. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Hozzon létre egy [engedélyezési szabályt](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) a küldési és fogadási engedélyekkel.

    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Hozzon létre egy Azure digitális Twins- [végpontot](concepts-route-events.md#create-an-endpoint) , amely az Event Grid-témakört az Azure Digital Twins-példánnyal csatolja.

    ```azurecli
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Hozzon létre egy [útvonalat](concepts-route-events.md#create-an-event-route) az Azure Digital ikrekben, hogy dupla frissítési eseményt küldjön a végpontnak. Az ebben az útvonalban lévő szűrő csak a kettős frissítési üzeneteket továbbítja a végpontnak.

    >[!NOTE]
    >Jelenleg egy **ismert probléma** van a Cloud shellt érintő következő parancsokkal: `az dt route` , `az dt model` , `az dt twin` .
    >
    >A probléma megoldásához futtassa a `az login` parancsot Cloud Shell a parancs futtatása előtt, vagy használja a [helyi](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) parancssori felületet Cloud Shell helyett. Erről további részleteket a [*Hibaelhárítás: az Azure digitális Twins ismert problémái*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell)című témakörben talál.

    ```azurecli
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

A továbblépés előtt jegyezze fel a *Event Hubs névteret* és az *erőforráscsoportot*, ahogy azt újra használni fogja a cikk későbbi részében újabb Event hub létrehozásához.

## <a name="create-an-azure-function"></a>Azure-függvény létrehozása 

Ezután létre fog hozni egy Event Hubs által aktivált függvényt egy Function alkalmazásban. Használhatja a teljes körű oktatóanyagban létrehozott Function alkalmazást ([*oktatóanyag: végpontok közötti megoldás összekapcsolását*](./tutorial-end-to-end.md)) vagy a sajátját. 

Ez a függvény átalakítja ezeket a kettős frissítési eseményeket az eredeti űrlapról JSON-javításként szolgáló dokumentumként a JSON-objektumokba, amelyek csak a frissített és hozzáadott értékeket tartalmazzák az ikrektől.

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
            [EventHubTrigger("twins-event-hub", Connection = "EventHubAppSetting-Twins")]EventData myEventHubMessage, 
            [EventHub("tsi-event-hub", Connection = "EventHubAppSetting-TSI")]IAsyncCollector<string> outputEvents, 
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

Később olyan környezeti változók is megadhatók, amelyeket ez a függvény a saját Event hubokhoz való kapcsolódáshoz használ majd.

## <a name="send-telemetry-to-an-event-hub"></a>Telemetria küldése az Event hub-nak

Most hozzon létre egy második Event hub-t, és konfigurálja a függvényt, hogy a kimenetét továbbítsa az Event hub-nak. Ez az Event hub ezután csatlakozik Time Series Insightshoz.

### <a name="create-an-event-hub"></a>Eseményközpont létrehozása

A második Event hub létrehozásához használhatja az alábbi Azure CLI-utasításokat, vagy használhatja a Azure Portal: rövid útmutató [*: Event hub létrehozása Azure Portal használatával*](../event-hubs/event-hubs-create.md).

1. Készítse elő a *Event Hubs névteret* és az *erőforráscsoport* nevét a jelen cikk korábbi részében

2. Új Event hub létrehozása
    ```azurecli
    # Create an event hub. Specify a name for the event hub. 
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. [Engedélyezési szabály](https://docs.microsoft.com/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest#az-eventhubs-eventhub-authorization-rule-create) létrehozása küldési és fogadási engedélyekkel
    ```azurecli
    # Create an authorization rule. Specify a name for the rule.
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>A függvény konfigurálása

A következő lépésben környezeti változókat kell beállítania a Function alkalmazásban, amely tartalmazza a létrehozott Event hubok kapcsolati karakterláncait.

### <a name="set-the-twins-event-hub-connection-string"></a>Az ikrek Event hub kapcsolati karakterláncának beállítása

1. Szerezze be az ikrek [Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md)a fent létrehozott engedélyezési szabályok alapján az ikrek hubhoz.

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Használja azt a kapcsolódási karakterláncot, amelyet a függvény alkalmazásban a kapcsolódási sztringet tartalmazó alkalmazás-beállítás létrehozásához fog használni:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Az Time Series Insights Event hub kapcsolati karakterláncának beállítása

1. Szerezze be az ÁME [Event hub kapcsolati karakterláncát](../event-hubs/event-hubs-get-connection-string.md)az Time Series Insights hub felett létrehozott engedélyezési szabályok használatával:

    ```azurecli
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. A Function alkalmazásban hozzon létre egy alkalmazást, amely tartalmazza a kapcsolatok karakterláncát:

    ```azurecli
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string> -g <resource group> -n <your App Service (function app) name>"
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Time Series Insights-példány létrehozása és összekötése

Ezután állítson be egy Time Series Insights-példányt, amely a második Event hub adatait fogja fogadni. Kövesse az alábbi lépéseket, és a folyamattal kapcsolatos további információkért tekintse [*meg az oktatóanyag: Azure Time Series Insights GEN2 TB-környezet beállítása*](../time-series-insights/tutorials-set-up-tsi-environment.md)című témakört.

1. A Azure Portal Time Series Insights erőforrás létrehozásának megkezdéséhez. 
    1. Válassza ki a **TB (előzetes verzió)** árképzési szintet.
    2. Ehhez a környezethez ki kell választania egy **idősorozat-azonosítót** . Az idősorozat-azonosító legfeljebb három olyan érték lehet, amelyet a Time Series Insightsban lévő adatok kereséséhez fog használni. Ebben az oktatóanyagban használhatja a **$dtId**. További információ az azonosító érték kiválasztásáról az [*ajánlott eljárásokban az idősorozat-azonosító kiválasztásához*](https://docs.microsoft.com/azure/time-series-insights/how-to-select-tsid).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="A létrehozási portál UX Time Series Insights-környezethez. A TB (előzetes verzió) árképzési szintje be van jelölve, az idősorozat-azonosító tulajdonság neve pedig $dtId":::

2. Válassza a **Next (tovább): eseményforrás** lehetőséget, és válassza ki a Event Hubs adatokat a fenti listából. Emellett új Event Hubs fogyasztói csoportot is létre kell hoznia.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="A létrehozási portál UX Time Series Insights környezeti esemény forrásához. Az Event hub információi alapján hozza létre az eseményforrás adatait. Új fogyasztói csoportot is létrehoz.":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>IoT-adatok küldésének megkezdése az Azure Digital Twinsba

Az adatok Time Series Insightsba való küldésének megkezdéséhez meg kell kezdenie a digitális Twin-tulajdonságok frissítését az Azure Digital Twins-ban az adatértékek módosításával. Használja az az [DT Twin Update](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest#ext-azure-iot-az-dt-twin-update) parancsot.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Ha teljes körű oktatóanyagot használ ([*oktatóanyag: végpontok közötti megoldás összekapcsolásával*](tutorial-end-to-end.md)) a környezet beállításának elősegítése érdekében, megkezdheti a szimulált IoT-adatok küldését a minta *DeviceSimulator* -projekt futtatásával. Az útmutató az oktatóanyag [*konfigurálása és futtatása a szimuláció*](tutorial-end-to-end.md#configure-and-run-the-simulation) szakaszban található.

## <a name="visualize-your-data-in-time-series-insights"></a>Jelenítse meg az adatait Time Series Insights

Az adatforgalom az Time Series Insights-példányba kerül, és készen áll az elemzésre. Kövesse az alábbi lépéseket a beérkező adatforgalom megismeréséhez.

1. Nyissa meg Time Series Insights-példányát a [Azure Portalban](https://portal.azure.com) (a példány nevét megkeresheti a portál keresési sávján). Látogasson el a példány áttekintésében látható *Time Series Insights Explorer URL-címére* .
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="A Time Series Insights-környezet áttekintés lapján válassza ki a Time Series Insights Explorer URL-címét":::

2. Az Explorerben megjelenik a három ikrek a bal oldalon látható Azure digitális Ikrekből. Válassza a _**thermostat67**_ lehetőséget, válassza a **hőmérséklet**lehetőséget, és kattintson a **Hozzáadás gombra**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Válassza a * * thermostat67 * * elemet, válassza a * * hőmérséklet * * elemet, és a * * Hozzáadás * *":::

3. Ekkor látnia kell a termosztátból a kezdeti hőmérséklet-leolvasásokat az alább látható módon. Ugyanazt a hőmérséklet-olvasási értéket frissíti a rendszer a *room21* és a *floor1*, és párhuzamosan jelenítheti meg az adatfolyamokat.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="A kezdeti hőmérsékleti adatai az ÁME Explorerben vannak ábrázolva. 68 és 85 közötti véletlenszerű értékek sora":::

4. Ha lehetővé teszi, hogy a szimuláció jóval tovább fusson, a vizualizáció a következőképpen fog kinézni:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Az egyes Twin-sorok hőmérsékleti értékeit három, különböző színű párhuzamos vonal ábrázolja.":::

## <a name="next-steps"></a>További lépések

A digitális ikreket alapértelmezés szerint a rendszer a Time Series Insightsban lévő, lapos hierarchiában tárolja, de a modell adataival és a szervezet többszintű hierarchiájának használatával gazdagíthatja őket. A folyamattal kapcsolatos további információkért olvassa el a következőt: 

* [*Oktatóanyag: modell meghatározása és alkalmazása*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Egyéni logikát írhat arra, hogy automatikusan megadja ezeket az adatokat az Azure digitális Twins-ban már tárolt modell-és gráf-adatok használatával. Az alábbi hivatkozásokat követve további információkat olvashat az ikrek gráf információinak kezelésével, frissítésével és lekérésével kapcsolatban:

* [*Útmutató: digitális kettős kezelés*](./how-to-manage-twin.md)
* [*Útmutató: a Twin gráf lekérdezése*](./how-to-query-graph.md)