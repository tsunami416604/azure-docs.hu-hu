---
title: Az Azure Event Grid eseményindítója az Azure Functionshez
description: Ismerje meg, hogy kódot futtatni, amikor Event Grid események az Azure Functions küldik.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 2027629e1e9e297c97cbf40485ebe7dc2e3e6c0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277725"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Az Azure Event Grid eseményindítója az Azure Functionshez

A függvényesemény-eseményindító val válaszolhat egy Eseményrács-témakörbe küldött eseményre.

A beállítással és a konfigurációval kapcsolatos részletekről az [áttekintésben](./functions-bindings-event-grid.md)olvashat.

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

Http-eseményindító példát [lásd: Események fogadása HTTP-végpontra című témakörben.](../event-grid/receive-events.md)

### <a name="c-2x-and-higher"></a>C# (2.x és újabb)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely a következőkhöz `EventGridEvent`kötődik:

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

További információ: Csomagok, [Attribútumok,](#attributes-and-annotations) [Konfiguráció](#configuration)és [Használat](#usage).

### <a name="version-1x"></a>1.x verzió

A következő példa egy Functions 1.x [C# függvényt](functions-dotnet-class-library.md) mutat be, amely a következőkhöz `JObject`kötődik:

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy trigger kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja.

A *function.json* fájlban a kötési adatok:

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

### <a name="version-2x-and-higher"></a>2.x-es vagy újabb verzió

Íme egy példa, amely `EventGridEvent`kötődik:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

További információ: Csomagok, [Attribútumok,](#attributes-and-annotations) [Konfiguráció](#configuration)és [Használat](#usage).

### <a name="version-1x"></a>1.x verzió

Itt funkciók 1.x C # script kódot, amely kötődik: `JObject`

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy *function.json* fájlban lévő eseményindító-kötést és egy [JavaScript-függvényt](functions-reference-node.md) mutat be, amely a kötést használja.

A *function.json* fájlban a kötési adatok:

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

Itt a JavaScript-kód:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

A következő példa egy trigger kötést mutat be egy *function.json* fájlban és egy [Python-függvényt,](functions-reference-python.md) amely a kötést használja.

A *function.json* fájlban a kötési adatok:

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

Itt a Python kód:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [Eseményrács eseményindítója, Karakterlánc-paraméter](#event-grid-trigger-string-parameter)
* [Eseményrács eseményindítója, POJO paraméter](#event-grid-trigger-pojo-parameter)

A következő példák bemutatják trigger kötés [Java,](functions-reference-java.md) amelyek a kötést, és nyomtassa ki az eseményt, először az esemény fogadása, `String` mint a POJO.

### <a name="event-grid-trigger-string-parameter"></a>Eseményrács eseményindítója, Karakterlánc-paraméter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Eseményrács eseményindítója, POJO paraméter

Ez a példa a következő POJO-t használja, amely egy Event Grid-esemény legfelső szintű tulajdonságait jelöli:

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

Érkezéskor az esemény JSON-hasznos adata a ```EventSchema``` POJO-ban a függvény általi használatra leépül. Ez a folyamat lehetővé teszi, hogy a függvény objektumorientált módon férjen hozzá az esemény tulajdonságaihoz.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

A [Java függvények futásidejű függvénytárában](/java/api/overview/azure/functions/runtime)használja a jegyzetet azon `EventGridTrigger` paramétereken, amelyek értéke az EventGridből származna. Az ezekkel a jegyzetekkel rendelkező paraméterek hatására a függvény akkor fog futni, amikor egy esemény megérkezik.  Ez a jegyzet használható natív Java-típusok, POJOs vagy `Optional<T>`nullable értékek segítségével.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja az [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs) attribútumot.

A metódusaláírás `EventGridTrigger` egyik attribútuma:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Egy teljes példa, lásd: C# példa.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

Az [EventGridTrigger](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) jegyzet lehetővé teszi az Event Grid-kötés deklaratív konfigurálását konfigurációs értékek megadásával. További részletekért tekintse meg a [példát](#example) és a [konfigurációs](#configuration) szakaszokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function.json* fájlban beállított kötési konfigurációs tulajdonságokat ismerteti. Nincsenek konstruktorparaméterek vagy `EventGridTrigger` tulajdonságok, amelyeket az attribútumban be kell állítani.

|function.json tulajdonság |Leírás|
|---------|---------|
| **Típus** | Kötelező - a `eventGridTrigger`beállításnak a beállítására kell. |
| **direction** | Kötelező - a `in`beállításnak a beállítására kell. |
| **név** | Kötelező – az eseményadatokat fogadó paraméter függvénykódjában használt változónév. |

## <a name="usage"></a>Használat

# <a name="c"></a>[C #](#tab/csharp)

Az Azure Functions 1.x-ben a következő paramétertípusokat használhatja az Event Grid eseményindítóhoz:

* `JObject`
* `string`

Az Azure Functions 2.x-ben és újabb verzióban a következő paramétertípust is használhatja az Event Grid eseményindítóhoz:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Az összes eseménytípusközös mezők tulajdonságait határozza meg.

> [!NOTE]
> A Functions v1 `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`játékban, ha a hozzá szeretne kötni, a fordító egy "elavult" üzenetet jelenít meg, és azt javasolja, hogy helyette használja. `Microsoft.Azure.EventGrid.Models.EventGridEvent` Az újabb típus használatához hivatkozzon a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet `EventGridEvent` csomagra, és `Microsoft.Azure.EventGrid.Models`teljes mértékben minősítse a típusnevet a alkalmazással.

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az Azure Functions 1.x-ben a következő paramétertípusokat használhatja az Event Grid eseményindítóhoz:

* `JObject`
* `string`

Az Azure Functions 2.x-ben és újabb verzióban a következő paramétertípust is használhatja az Event Grid eseményindítóhoz:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Az összes eseménytípusközös mezők tulajdonságait határozza meg.

> [!NOTE]
> A Functions v1 `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`játékban, ha a hozzá szeretne kötni, a fordító egy "elavult" üzenetet jelenít meg, és azt javasolja, hogy helyette használja. `Microsoft.Azure.EventGrid.Models.EventGridEvent` Az újabb típus használatához hivatkozzon a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet `EventGridEvent` csomagra, és `Microsoft.Azure.EventGrid.Models`teljes mértékben minősítse a típusnevet a alkalmazással. A NuGet csomagok C# parancsfájlfüggvényben való hivatkozásáról a [NuGet-csomagok használata című](functions-reference-csharp.md#using-nuget-packages) témakörben talál további információt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Az Event Grid-példány a *function.json* fájl `name` tulajdonságában konfigurált paraméteren keresztül érhető el.

# <a name="python"></a>[Python](#tab/python)

Az Event Grid példány a *function.json* fájl `name` tulajdonságában konfigurált paraméteren keresztül érhető el, amelyet a `func.EventGridEvent`.

# <a name="java"></a>[Java](#tab/java)

Az Event Grid eseménypéldány az `EventGridTrigger` attribútumhoz társított paraméteren keresztül `EventSchema`érhető el, amelyet egy . Lásd a [példát](#example) további részletekért.

---

## <a name="event-schema"></a>Eseményséma

Az Event Grid-események adatai JSON-objektumként érkeznek egy HTTP-kérelem törzsébe. A JSON a következő példához hasonlóan néz ki:

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

A bemutatott példa egy elem tömbje. Az Event Grid mindig egy tömböt küld, és egynél több eseményt is küldhet a tömbben. A futásidejű minden tömbelemhez egyszer hívja meg a függvényt.

A legfelső szintű tulajdonságok abban az esetben, ha a JSON-adatok `data` megegyeznek az összes eseménytípus között, míg a tulajdonság tartalma az egyes eseménytípusokra jellemző. A példa látható egy blob tárolási esemény.

A gyakori és eseményspecifikus tulajdonságok magyarázatát az [Eseményrács dokumentációjának Eseménytulajdonságai](../event-grid/event-schema.md#event-properties) című témakörben találja.

A `EventGridEvent` típus csak a legfelső szintű tulajdonságokat határozza meg; az `Data` ingatlan `JObject`egy .

## <a name="create-a-subscription"></a>Előfizetés létrehozása

Event Grid HTTP-kérelmek fogadásának megkezdéséhez hozzon létre egy Event Grid-előfizetést, amely megadja a függvényt meghívó végpont URL-címét.

### <a name="azure-portal"></a>Azure portál

Az Azure Portalon az Eseményrács eseményindítójával kifejlesztett függvények esetén válassza az **Event Grid-előfizetés hozzáadása**lehetőséget.

![Előfizetés létrehozása a portálon](media/functions-bindings-event-grid/portal-sub-create.png)

Ha ezt a hivatkozást választja, a portál megnyitja az **Esemény-előfizetés létrehozása** lapot, amelynek végponti URL-címe előre ki van töltve.

![A végpont URL-címe előre kivan töltve](media/functions-bindings-event-grid/endpoint-url.png)

Az Azure Portal használatával előfizetések létrehozásáról az [Egyéni esemény – Azure portal létrehozása](../event-grid/custom-event-quickstart-portal.md) az Event Grid dokumentációjában című témakörben olvashat bővebben.

### <a name="azure-cli"></a>Azure CLI

Előfizetés létrehozása az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)használatával használja az [az eventgrid event-subscription create](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) parancsot.

A parancs megköveteli a végpont URL-címét, amely meghívja a függvényt. A következő példa a verzióspecifikus URL-mintát mutatja be:

#### <a name="version-2x-and-higher-runtime"></a>2.x-es (és újabb) verziójú futásidejű

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>1.x-es verziófutási idő

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A rendszerkulcs egy olyan engedélyezési kulcs, amelyet az Eseményrács eseményindítójának végponti URL-címében kell szerepeltetni. A következő szakasz bemutatja, hogyan szerezheti be a rendszerkulcsot.

Íme egy példa, amely előfizet egy blob tárfiók (a helyőrző a rendszerkulcs):

#### <a name="version-2x-and-higher-runtime"></a>2.x-es (és újabb) verziójú futásidejű

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>1.x-es verziófutási idő

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Az előfizetések létrehozásáról további információt [a blob storage rövid útmutatójában](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) vagy az Event Grid többi rövid útmutatójában talál.

### <a name="get-the-system-key"></a>A rendszerkulcs beszerezése

A rendszerkulcsot a következő API (HTTP GET) használatával szerezheti be:

#### <a name="version-2x-and-higher-runtime"></a>2.x-es (és újabb) verziójú futásidejű

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>1.x-es verziófutási idő

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Ez egy rendszergazdai API, ezért a függvényalkalmazás [főkulcsát](functions-bindings-http-webhook-trigger.md#authorization-keys)igényli. Ne keverje össze a rendszerkulcsot (az Event Grid eseményindító függvény meghívásához) a főkulival (felügyeleti feladatok végrehajtásához a függvényalkalmazásban). Amikor feliratkozik egy Event Grid-témakörre, mindenképpen használja a rendszerkulcsot.

Íme egy példa a rendszerkulcsot megadó válaszra:

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

A függvényalkalmazás főkulcsát a **függvényalkalmazás beállításai** lapon szerezheti be a portálon.

> [!IMPORTANT]
> A főkulcs rendszergazdai hozzáférést biztosít a függvényalkalmazáshoz. Ne ossza meg ezt a kulcsot harmadik felekkel, és ne terjessze natív ügyfélalkalmazásokban.

További információ: [Engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys) a HTTP-eseményindító referenciacikkben.

Másik lehetőségként elküldheti a HTTP PUT-t a kulcsérték saját személyének megadásához.

## <a name="local-testing-with-viewer-web-app"></a>Helyi tesztelés a megjelenítő webalkalmazásával

Az Event Grid-eseményindító helyi teszteléséhez az Event Grid HTTP-kérelmeit a felhőből a helyi gépre kell leadnia. Ennek egyik módja a kérelmek online rögzítése és a helyi számítógépen történő manuális újraküldése:

1. [Hozzon létre egy megjelenítő webalkalmazást,](#create-a-viewer-web-app) amely rögzíti az eseményüzeneteket.
1. [Hozzon létre egy Event Grid-előfizetést,](#create-an-event-grid-subscription) amely eseményeket küld a megjelenítő alkalmazásnak.
1. [Hozzon létre egy kérelmet,](#generate-a-request) és másolja a kérelem törzsét a megjelenítő alkalmazásból.
1. [Manuálisan tegye fel a kérelmet](#manually-post-the-request) az Event Grid eseményindító funkciójának localhost URL-címére.

Ha befejezte a tesztelést, használhatja ugyanazt az előfizetést éles környezetben a végpont frissítésével. Használja az [az eventgrid esemény-előfizetés frissítés](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI parancsot.

### <a name="create-a-viewer-web-app"></a>Megjelenítő webalkalmazás létrehozása

Az eseményüzenetek rögzítésének egyszerűsítése érdekében telepíthet egy [előre elkészített webalkalmazást,](https://github.com/Azure-Samples/azure-event-grid-viewer) amely megjeleníti az eseményüzeneteket. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A hely látható, de még nem lett közzétéve esemény.

![Új hely megtekintése](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre egy eseményrács-előfizetést a tesztelni kívánt típussal, és adja meg neki a webalkalmazás URL-címét az eseményértesítés végpontjaként. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia. Tehát a teljes URL`https://<your-site-name>.azurewebsites.net/api/updates`

Az Azure Portal használatával előfizetések létrehozásáról az [Egyéni esemény – Azure portal létrehozása](../event-grid/custom-event-quickstart-portal.md) az Event Grid dokumentációjában című témakörben olvashat.

### <a name="generate-a-request"></a>Kérelem létrehozása

Olyan eseményt indít el, amely HTTP-forgalmat generál a webalkalmazás végpontjára.  Ha például létrehozott egy blob storage-előfizetést, töltsön fel vagy töröljön egy blobot. Amikor egy kérelem megjelenik a webalkalmazásban, másolja a kérelem törzsét.

Először az előfizetés-érvényesítési kérelem érkezik meg; figyelmen kívül hagyja az érvényesítési kérelmeket, és másolja az eseménykérelmet.

![Kérelemtörzs másolása a webalkalmazásból](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>A kérelem manuális feladása

Futtassa az Event Grid funkciót helyileg.

Http POST-kérelem létrehozásához használjon egy eszközt, például [a Postman](https://www.getpostman.com/) t vagy a [Curl-t:](https://curl.haxx.se/docs/httpscripting.html)

* Fej `Content-Type: application/json` beállítása.
* Fej `aeg-event-type: Notification` beállítása.
* Illessze be a RequestBin-adatokat a kérelem törzsébe.
* Tegye közzé az Eseményrács eseményindító funkciójának URL-címét.
  * 2.x és magasabb esetén használja a következő mintát:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * 1.x alkalmazás esetén:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

A `functionName` paraméternek az `FunctionName` attribútumban megadott névnek kell lennie.

A következő képernyőképek a postás fejléceit és a kérelemtörzset mutatják be:

![Fejlécek a postásban](media/functions-bindings-event-grid/postman2.png)

![Kérelem szervezet postás](media/functions-bindings-event-grid/postman.png)

Az Event Grid eseményindító függvény a következő példához hasonló naplókat hajt végre és jelenít meg:

![Mintaeseményrács eseményindító függvénynaplói](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>További lépések

* [Eseményrács-esemény feladása](./functions-bindings-event-grid-trigger.md)
