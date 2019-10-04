---
title: Azure Functions Event Grid trigger
description: Ismerje meg, hogyan kezelheti Event Grid eseményeit Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: reference
ms.date: 09/04/2018
ms.author: cshoe
ms.openlocfilehash: d2e15c63ac6d93824aeab3f251c2860b7ea114d6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086827"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Azure Functions Event Grid trigger

Ez a cikk bemutatja, hogyan kezelheti [Event Grid](../event-grid/overview.md) eseményeit Azure Functionsban.

Event Grid egy olyan Azure-szolgáltatás, amely HTTP-kéréseket küld, hogy értesítsea kiadói eseményekről. A közzétevő az eseményt kezdeményező szolgáltatás vagy erőforrás. Egy Azure Blob Storage-fiók például közzétevő, [a blob feltöltése vagy törlése pedig egy esemény](../storage/blobs/storage-blob-event-overview.md). Egyes [Azure-szolgáltatások beépített támogatást biztosítanak az események Event Grid való közzétételéhez](../event-grid/overview.md#event-sources).

Az eseménykezelők fogadják és dolgozzák fel az eseményeket. A Azure Functions számos olyan [Azure-szolgáltatás egyike, amelyek beépített támogatást biztosítanak a Event Grid események kezeléséhez](../event-grid/overview.md#event-handlers). Ebből a cikkből megtudhatja, hogyan használhat egy Event Grid eseményindítót egy függvény meghívásához, amikor egy esemény érkezik a Event Gridból.

Ha szeretné, használhat egy HTTP-triggert Event Grid események kezelésére; Lásd: [http-trigger használata Event Grid triggerként](#use-an-http-trigger-as-an-event-grid-trigger) a cikk későbbi részében. Jelenleg nem használhat Event Grid eseményindítót egy Azure Functions alkalmazáshoz, ha az esemény a [CloudEvents](../event-grid/cloudevents-schema.md)-sémában kerül kézbesítésre. Ehelyett használjon HTTP-triggert.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

A Event Grid triggert a [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet csomagban, 2. x verzióban kell megadnia. A csomag forráskódja az [Azure-functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub-tárházban található.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

A Event Grid triggert a [Microsoft. Azure. webjobs. Extensions. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet csomagban, 1. x verzióban kell megadnia. A csomag forráskódja az [Azure-functions-eventgrid-Extension](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="example"></a>Példa

Tekintse meg a Event Grid trigger nyelvspecifikus példáját:

* C#
* [C# script (.csx)](#c-script-example)
* [Java](#trigger---java-examples)
* [JavaScript](#javascript-example)
* [Python](#python-example)

A HTTP-triggerek példáját lásd: a [http-trigger használata](#use-an-http-trigger-as-an-event-grid-trigger) a cikk későbbi részében.

### <a name="c-2x"></a>C# (2.x)

Az alábbi példa egy 2. x [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely a `EventGridEvent`következőhöz kötődik:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

További információ: csomagok, [attribútumok](#attributes), [konfiguráció](#configuration)és [használat](#usage).

### <a name="c-version-1x"></a>C#(1. x verzió)

Az alábbi példa egy 1. x [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely a `JObject`következőhöz kötődik:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

### <a name="c-script-example"></a>C#parancsfájl – példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl](functions-reference-csharp.md) -függvényt, amely a kötést használja.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-script-version-2x"></a>C#parancsfájl (2. x verzió)

Az itt található functions 2 C# . x szkript kódja a `EventGridEvent`következőhöz kötődik:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

További információ: csomagok, [attribútumok](#attributes), [konfiguráció](#configuration)és [használat](#usage).

#### <a name="c-script-version-1x"></a>C#parancsfájl (1. x verzió)

Az itt található functions 1 C# . x szkript kódja a `JObject`következőhöz kötődik:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

### <a name="javascript-example"></a>JavaScript-példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript](functions-reference-node.md) -függvényt, amely a kötést használja.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A következő JavaScript-kódot:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

### <a name="python-example"></a>Python-példa

Az alábbi példa egy trigger-kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [Python](functions-reference-python.md) -függvényt, amely a kötést használja.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(event: func.EventGridEvent):
    logging.info("Python Event Grid function processed a request.")
    logging.info("  Subject: %s", event.subject)
    logging.info("  Time: %s", event.event_time)
    logging.info("  Data: %s", event.get_json())
```

### <a name="trigger---java-examples"></a>Trigger – Java-példák

Ez a szakasz tartalmazza az alábbi példák:

* [Event Grid trigger, karakterlánc-paraméter](#event-grid-trigger-string-parameter-java)
* [Event Grid trigger, POJO paraméter](#event-grid-trigger-pojo-parameter-java)

Az alábbi példákban egy *function. JSON* -fájlban és a kötést használó [Java-függvények](functions-reference-java.md) , valamint egy esemény kinyomtatásakor a rendszer eseményindító-kötést mutat be, majd először ```String``` a POJO fogadja az eseményt.

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

#### <a name="event-grid-trigger-string-parameter-java"></a>Event Grid trigger, string paraméter (Java)

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: " + content);      
  }
```

#### <a name="event-grid-trigger-pojo-parameter-java"></a>Event Grid trigger, POJO paraméter (Java)

Ez a példa a következő POJO használja, amely egy Event Grid esemény legfelső szintű tulajdonságait jelöli:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Érkezéskor az esemény JSON-tartalma deszerializálható a ```EventSchema``` POJO a függvény általi használatra. Ez lehetővé teszi, hogy a függvény objektumorientált módon hozzáférhessen az esemény tulajdonságaihoz.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      // log 
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja `EventGridTrigger` a jegyzeteket azon paramétereknél, amelyek értéke a EventGrid származik. Az ezekkel a megjegyzésekkel rendelkező paraméterek a függvény futását okozzák, amikor egy esemény érkezik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>`.

## <a name="attributes"></a>Attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) attribútumot.

Itt egy `EventGridTrigger` attribútum szerepel a metódus-aláírásban:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Teljes példa: C# példa.

## <a name="configuration"></a>Konfiguráció

A következő táblázat a *function. JSON* fájlban beállított kötési konfigurációs tulajdonságokat ismerteti. Nincsenek beállítva konstruktor-paraméterek vagy tulajdonságok az `EventGridTrigger` attribútumban.

|Function.JSON tulajdonság |Leírás|
|---------|---------|
| **type** | Kötelező – a `eventGridTrigger`következőre kell beállítani:. |
| **direction** | Kötelező – a `in`következőre kell beállítani:. |
| **name** | Kötelező – a függvény kódjában használt változó neve az esemény-adatfogadási paraméterhez. |

## <a name="usage"></a>Használat

A (z) Azure Functions 1. x esetében C# a következő paramétereket használhatja a Event Grid triggerhez: F#

* `JObject`
* `string`

A C# ( F# z) Azure functions 2. x verzióban és a függvényekben a következő paraméter-típust is használhatja a Event Grid triggerhez:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Az összes eseménytípus közös mezőinek tulajdonságait határozza meg.

> [!NOTE]
> Ha a functions v1-re próbál kötni `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, a fordító egy "elavult" üzenetet jelenít meg, és azt tanácsolja, hogy `Microsoft.Azure.EventGrid.Models.EventGridEvent` használja helyette. Az újabb típus használatához hivatkozzon a [Microsoft. Azure. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet-csomagra, és teljes mértékben `EventGridEvent` minősítse a típus nevét a következővel: `Microsoft.Azure.EventGrid.Models`. További információ a NuGet-csomagok C# parancsfájl-függvényekben való hivatkozásáról: [NuGet-csomagok használata](functions-reference-csharp.md#using-nuget-packages)

JavaScript-függvények esetén a *function. JSON* `name` tulajdonság által megnevezett paraméter az Event objektumra mutató hivatkozást tartalmaz.

## <a name="event-schema"></a>Eseményséma

Egy Event Grid eseményhez tartozó adatmennyiség egy HTTP-kérelem törzsében JSON-objektumként érkezik. A JSON a következő példához hasonlóan néz ki:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

A megjelenített példa egy elem tömbje. Event Grid mindig egy tömböt küld, és több eseményt is küldhet a tömbben. A futtatókörnyezet minden egyes tömb elemnél egyszer meghívja a függvényt.

Az Event JSON-adattípusok legfelső szintű tulajdonságai megegyeznek az összes eseménytípus között, míg a `data` tulajdonság tartalma minden eseménytípus esetében egyedi. A példa egy blob Storage-eseményre mutat.

A Common és az Event-specifikus tulajdonságok magyarázatát az Event Grid [](../event-grid/event-schema.md#event-properties) dokumentációjában találja.

A `EventGridEvent` típus csak a legfelső szintű tulajdonságokat definiálja, `Data` a tulajdonság `JObject`a.

## <a name="create-a-subscription"></a>Előfizetés létrehozása

Event Grid HTTP-kérelmek fogadásának megkezdéséhez hozzon létre egy Event Grid-előfizetést, amely megadja a függvényt meghívó végpont URL-címét.

### <a name="azure-portal"></a>Azure Portal

Az Event Grid triggerrel Azure Portalban fejlesztett függvények esetében válassza az **Event Grid előfizetés hozzáadása**lehetőséget.

![Előfizetés létrehozása a portálon](media/functions-bindings-event-grid/portal-sub-create.png)

Ha ezt a hivatkozást választja, a portál megnyitja az **esemény-előfizetés létrehozása** lapot az előtöltött végpont URL-címével.

![Végpont URL-címe előtöltve](media/functions-bindings-event-grid/endpoint-url.png)

Az előfizetések Azure Portal használatával történő létrehozásával kapcsolatos további információkért tekintse meg az [egyéni esemény-Azure Portal létrehozása](../event-grid/custom-event-quickstart-portal.md) a Event Grid dokumentációjában.

### <a name="azure-cli"></a>Azure CLI

Ha [Az Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)használatával szeretne előfizetést létrehozni, használja az az [eventgrid Event-előfizetés Create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) parancsot.

A parancshoz meg kell adni a végpont URL-címét, amely meghívja a függvényt. A következő példa a verzióra jellemző URL-mintát mutatja:

#### <a name="version-2x-runtime"></a>2-es verzió. x futtatókörnyezet

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>1\. x verzió futtatókörnyezete

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A rendszerkulcs egy olyan engedélyezési kulcs, amely egy Event Grid trigger végponti URL-címében szerepelnie kell. A következő szakasz ismerteti, hogyan kérheti le a rendszerkulcsot.

Az alábbi példa egy blob Storage-fiókra való előfizetést mutat be (a rendszerkulcs helyőrzője):

#### <a name="version-2x-runtime"></a>2-es verzió. x futtatókörnyezet

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>1\. x verzió futtatókörnyezete

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Az előfizetés létrehozásával kapcsolatos további információkért tekintse meg [a blob Storage](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) rövid útmutatóját vagy a többi Event Grid rövid útmutatót.

### <a name="get-the-system-key"></a>A rendszerkulcs beszerzése

A rendszerkulcs a következő API-val (HTTP GET) szerezhető be:

#### <a name="version-2x-runtime"></a>2-es verzió. x futtatókörnyezet

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>1\. x verzió futtatókörnyezete

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Ez egy felügyeleti API, ezért a Function app főkulcsát [](functions-bindings-http-webhook.md#authorization-keys)igényli. Ne tévesszük össze a rendszerkulcsot (egy Event Grid trigger függvény meghívásához) a főkulccsal (a Function alkalmazás felügyeleti feladatainak végrehajtásához). Amikor előfizet egy Event Grid témakörre, ügyeljen arra, hogy a rendszerkulcsot használja.

Íme egy példa a rendszerkulcsot biztosító válaszra:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

A Function alkalmazás főkulcsát a portál **Function app Settings** lapján szerezheti be.

> [!IMPORTANT]
> A főkulcs rendszergazdai hozzáférést biztosít a Function alkalmazáshoz. Ne ossza meg ezt a kulcsot harmadik felekkel, vagy terjessze azt natív ügyfélalkalmazásokba.

További információ: [engedélyezési kulcsok](functions-bindings-http-webhook.md#authorization-keys) a http-trigger dokumentációjában.

Azt is megteheti, hogy HTTP-PUT-t küld a kulcs értékének megadásához.

## <a name="local-testing-with-viewer-web-app"></a>Helyi tesztelés a megjelenítői webalkalmazással

Event Grid-trigger helyi teszteléséhez be kell szereznie Event Grid HTTP-kéréseket a felhőből a helyi gépre. Ennek egyik módja a kérelmek online rögzítésének és manuális újraküldése a helyi gépen:

1. [Hozzon létre egy](#create-a-viewer-web-app) megjelenítői webalkalmazást, amely rögzíti az események üzeneteit.
1. [Hozzon létre egy Event Grid](#create-an-event-grid-subscription) -előfizetést, amely eseményeket küld a megjelenítői alkalmazásnak.
1. [Kérelem létrehozása](#generate-a-request) és a kérelem törzsének másolása a megjelenítői alkalmazásból.
1. [Manuálisan tegye közzé a kérést](#manually-post-the-request) a Event Grid trigger függvény localhost URL-címére.

Ha végzett a teszteléssel, a végpont frissítésével ugyanazt az előfizetést használhatja éles környezethez. Használja az az [eventgrid Event-előfizetés Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI parancsot.

### <a name="create-a-viewer-web-app"></a>Megjelenítői Webalkalmazás létrehozása

Az üzenetek rögzítésének egyszerűbbé tétele érdekében üzembe helyezhet egy [előre elkészített](https://github.com/Azure-Samples/azure-event-grid-viewer) webalkalmazást, amely megjeleníti az esemény üzeneteit. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A hely látható, de még nem lett közzétéve esemény.

![Új hely megtekintése](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre egy Event Grid-előfizetést a tesztelni kívánt típushoz, és adja meg a webalkalmazás URL-címét az eseményekről szóló értesítés végpontja. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia. Tehát a teljes URL-cím`https://<your-site-name>.azurewebsites.net/api/updates`

További információ az előfizetések létrehozásáról a Azure Portal használatával: [Egyéni Event-Azure Portal létrehozása](../event-grid/custom-event-quickstart-portal.md) a Event Grid dokumentációjában.

### <a name="generate-a-request"></a>Kérelem létrehozása

Indítson el egy eseményt, amely a webalkalmazás-végpontra HTTP-forgalmat fog előállítani.  Ha például létrehozta a blob Storage-előfizetést, feltölt vagy töröl egy blobot. Ha egy kérelem megjelenik a webalkalmazásban, másolja a kérelem törzsét.

A rendszer először az előfizetés-ellenőrzési kérelmet kapja meg; hagyja figyelmen kívül az érvényesítési kérelmeket, és másolja az eseményre vonatkozó kérelmet.

![Kérelem törzsének másolása a webalkalmazásból](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>A kérelem manuális közzététele

Futtassa helyileg a Event Grid függvényt.

HTTP POST-kérelem létrehozásához használjon olyan eszközt, mint például a [Poster](https://www.getpostman.com/) vagy a [curl](https://curl.haxx.se/docs/httpscripting.html) :

* Adja meg `Content-Type: application/json` a fejlécet.
* Adja meg `aeg-event-type: Notification` a fejlécet.
* Illessze be a RequestBin a kérelem törzsébe.
* Tegye közzé a Event Grid trigger függvény URL-címét.
  * A 2. x esetében használja a következő mintát:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1\. x esetén használja a következőt:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

A `functionName` paraméternek az `FunctionName` attribútumban megadott névnek kell lennie.

A következő Képernyőképek a Poster fejléceit és a kérelem törzsét mutatják be:

![Fejlécek a Poster-ban](media/functions-bindings-event-grid/postman2.png)

![Kérelem törzse a Poster-ban](media/functions-bindings-event-grid/postman.png)

A Event Grid trigger függvény végrehajtja és megjeleníti a következő példához hasonló naplókat:

![Minta Event Grid trigger-függvények naplói](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Helyi tesztelés a ngrok

Egy Event Grid trigger helyi tesztelésének másik módja az Internet és a fejlesztői számítógép közötti HTTP-kapcsolat automatizálása. Ezt egy olyan eszközzel teheti meg, mint a [ngrok](https://ngrok.com/):

1. [Hozzon létre egy ngrok](#create-an-ngrok-endpoint)-végpontot.
1. [Futtassa a Event Grid trigger függvényt](#run-the-event-grid-trigger-function).
1. [Hozzon létre egy Event Grid](#create-a-subscription) -előfizetést, amely eseményeket küld a ngrok-végpontnak.
1. [Esemény elindítása](#trigger-an-event).

Ha végzett a teszteléssel, a végpont frissítésével ugyanazt az előfizetést használhatja éles környezethez. Használja az az [eventgrid Event-előfizetés Update](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI parancsot.

### <a name="create-an-ngrok-endpoint"></a>Ngrok-végpont létrehozása

Töltse le a *ngrok. exe fájlt* a [ngrok](https://ngrok.com/)-ből, és futtassa a parancsot a következő paranccsal:

```
ngrok http -host-header=localhost 7071
```

A-Host-header paraméterre azért van szükség, mert a functions Runtime a localhost-on futtatott gazdagépről érkező kéréseket vár. a 7071 az alapértelmezett portszám, ha a futtatókörnyezet helyileg fut.

A parancs a következőhöz hasonló kimenetet hoz létre:

```
Session Status                online
Version                       2.2.8
Region                        United States (us)
Web Interface                 http://127.0.0.1:4040
Forwarding                    http://263db807.ngrok.io -> localhost:7071
Forwarding                    https://263db807.ngrok.io -> localhost:7071

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

A `https://{subdomain}.ngrok.io` Event Grid-előfizetés URL-címét fogja használni.

### <a name="run-the-event-grid-trigger-function"></a>A Event Grid trigger függvény futtatása

A ngrok URL-cím nem kap speciális kezelést Event Grid alapján, így a függvénynek helyileg kell futnia az előfizetés létrehozásakor. Ha nem, az érvényesítési válasz nem lesz elküldve, és az előfizetés létrehozása sikertelen lesz.

### <a name="create-a-subscription"></a>Előfizetés létrehozása

Hozzon létre egy Event Grid-előfizetést a tesztelni kívánt típushoz, és adja meg a ngrok-végpontot.

Használja ezt a végponti mintát a 2. x függvényhez:

```
https://{SUBDOMAIN}.ngrok.io/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
```

Használja ezt a végponti mintát az 1. x függvényeknél:

```
https://{SUBDOMAIN}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
```

A `{FUNCTION_NAME}` paraméternek az `FunctionName` attribútumban megadott névnek kell lennie.

Íme egy példa az Azure CLI használatával:

```azurecli
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/runtime/webhooks/eventgrid?functionName=EventGridTrigger
```

További információ az előfizetés létrehozásáról: [előfizetés létrehozása](#create-a-subscription) a cikk korábbi részében.

### <a name="trigger-an-event"></a>Események aktiválása

Indítson el egy eseményt, amely a ngrok-végpontra HTTP-forgalmat fog előállítani.  Ha például létrehozta a blob Storage-előfizetést, feltölt vagy töröl egy blobot.

A Event Grid trigger függvény végrehajtja és megjeleníti a következő példához hasonló naplókat:

![Minta Event Grid trigger-függvények naplói](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>HTTP-trigger használata Event Grid triggerként

Event Grid események fogadása HTTP-kérésként történik, így az eseményeket egy Event Grid trigger helyett HTTP-trigger használatával kezelheti. Ennek egyik lehetséges oka, hogy jobban kézben tarthatja a függvényt meghívó végponti URL-címet. Egy másik ok az, amikor eseményeket kell fogadnia a [CloudEvents sémában](../event-grid/cloudevents-schema.md). A Event Grid trigger jelenleg nem támogatja a CloudEvents sémát. Az ebben a szakaszban szereplő példák a Event Grid séma és a CloudEvents séma megoldásait mutatják be.

Ha HTTP-triggert használ, kódot kell írnia ahhoz, hogy mit tesz a Event Grid trigger automatikusan:

* Érvényesítési választ küld egy [előfizetés-ellenőrzési kérelemre](../event-grid/security-authentication.md#webhook-event-delivery).
* Egyszer meghívja a függvényt a kérelem törzsében található Event tömbben.

További információ a függvény helyi meghívásához vagy az Azure-ban való futtatásához használt URL-címről: [http-trigger kötési útmutatója – dokumentáció](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Event Grid-séma

A következő mintakód C# egy http-triggerhez szimulálja Event Grid trigger viselkedését. Ezt a példát a Event Grid sémában szállított eseményekhez használhatja.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.LogInformation("Validate request received");
        return req.CreateResponse<object>(new
        {
            validationResponse = messages[0]["data"]["validationCode"]
        });
    }

    // The request is not for subscription validation, so it's for one or more events.
    foreach (JObject message in messages)
    {
        // Handle one event.
        EventGridEvent eventGridEvent = message.ToObject<EventGridEvent>();
        log.LogInformation($"Subject: {eventGridEvent.Subject}");
        log.LogInformation($"Time: {eventGridEvent.EventTime}");
        log.LogInformation($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A következő minta JavaScript-kód a HTTP-triggerekhez szimulálja Event Grid trigger viselkedését. Ezt a példát a Event Grid sémában szállított eseményekhez használhatja.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = messages[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
        // Event Grid schema delivers events in an array.
        for (var i = 0; i < messages.length; i++) {
            // Handle one event.
            var message = messages[i];
            context.log('Subject: ' + message.subject);
            context.log('Time: ' + message.eventTime);
            context.log('Data: ' + JSON.stringify(message.data));
        }
    }
    context.done();
};
```

Az eseménykezelő kód a `messages` tömbön belülről megy át a hurokban.

### <a name="cloudevents-schema"></a>CloudEvents séma

A következő mintakód C# egy http-triggerhez szimulálja Event Grid trigger viselkedését.  Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequestMessage req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    var requestmessage = await req.Content.ReadAsStringAsync();
    var message = JToken.Parse(requestmessage);

    if (message.Type == JTokenType.Array)
    {
        // If the request is for subscription validation, send back the validation code.
        if (string.Equals((string)message[0]["eventType"],
        "Microsoft.EventGrid.SubscriptionValidationEvent",
        System.StringComparison.OrdinalIgnoreCase))
        {
            log.LogInformation("Validate request received");
            return req.CreateResponse<object>(new
            {
                validationResponse = message[0]["data"]["validationCode"]
            });
        }
    }
    else
    {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        log.LogInformation($"Source: {message["source"]}");
        log.LogInformation($"Time: {message["eventTime"]}");
        log.LogInformation($"Event data: {message["data"].ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A következő minta JavaScript-kód a HTTP-triggerekhez szimulálja Event Grid trigger viselkedését. Ezt a példát a CloudEvents sémában elküldött eseményekhez használhatja.

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var message = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (message.length > 0 && message[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        var code = message[0].data.validationCode;
        context.res = { status: 200, body: { "ValidationResponse": code } };
    }
    else {
        // The request is not for subscription validation, so it's for an event.
        // CloudEvents schema delivers one event at a time.
        var event = JSON.parse(message);
        context.log('Source: ' + event.source);
        context.log('Time: ' + event.eventTime);
        context.log('Data: ' + JSON.stringify(event.data));
    }
    context.done();
};
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További információ a Event Grid](../event-grid/overview.md)
