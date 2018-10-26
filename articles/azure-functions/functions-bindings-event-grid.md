---
title: Event Grid-trigger az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan kezelje az Event Grid-események az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 4bfc1fa167185023b71566bde6e6199bdb18849b
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50087644"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Event Grid-trigger az Azure Functions szolgáltatáshoz

Ez a cikk azt ismerteti, hogyan legyen kezelve [Event Grid](../event-grid/overview.md) események az Azure Functions szolgáltatásban.

Event Grid egy Azure-szolgáltatás, amely HTTP-kéréseinek bekövetkező eseményeket kaphat értesítést küld *közzétevők*. A közzétevő, a szolgáltatás vagy erőforrás, amely az esemény származik. Például egy Azure blob storage-fiók-e a közzétevő és [egy blob feltöltése vagy törlésre egy esemény](../storage/blobs/storage-blob-event-overview.md). Néhány [Azure-szolgáltatás tartalmaz beépített támogatást az események Event Grid való közzétételéhez](../event-grid/overview.md#event-sources). 

Esemény *kezelők* fogadni és feldolgozni az eseményeket. Az Azure Functions az alábbiak egyike több [tartalmaz beépített támogatást az Event Grid-események kezelése Azure-szolgáltatások](../event-grid/overview.md#event-handlers). Ebben a cikkben megismerheti, hogyan használható egy Event Grid eseményindító függvény meghívásához, ha egy esemény fogadását az Event.

Igény szerint, HTTP-trigger használatával kezeli az Event Grid-események; Lásd: [HTTP-trigger használja, mint az Event Grid-trigger](#use-an-http-trigger-as-an-event-grid-trigger) a cikk későbbi részében. Jelenleg nem használható egy Event Grid-trigger egy Azure Functions-alkalmazás, amikor az esemény továbbítsa a [CloudEvents-séma](../event-grid/cloudevents-schema.md). Ehelyett használja a HTTP-trigger.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Az Event Grid eseményindító van megadva a [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-csomag verziója 1.x. A csomag forráskódja a [azure-functions-eventgrid-bővítmény](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub-adattárban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

Az Event Grid eseményindító van megadva a [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-functions-eventgrid-bővítmény](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

Tekintse meg a nyelvspecifikus példát egy Event Grid eseményindító:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [JavaScript](#javascript-example)
* [Java](#trigger---java-example)

HTTP eseményindító példa: [használata HTTP-eseményindító](#use-an-http-trigger-as-an-event-grid-trigger) a cikk későbbi részében.

### <a name="c-version-1x"></a>C#(Verzió 1.x)

Az alábbi példa bemutatja a Functions 1.x [C#-függvény](functions-dotnet-class-library.md) , amely összekapcsolja `JObject`:

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

### <a name="c-2x"></a>C#(2.x)

Az alábbi példa bemutatja a Functions 2.x [C#-függvény](functions-dotnet-class-library.md) , amely összekapcsolja `EventGridEvent`:

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

További információkért lásd: [csomagok](#packages), [attribútumok](#attributes), [konfigurációs](#configuration), és [használati](#usage).

### <a name="c-script-example"></a>C#-szkript példa

Az alábbi példa bemutatja a trigger kötés egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja.

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

#### <a name="c-script-version-1x"></a>C#parancsprogram (verzió 1.x)

Függvények 1.x C# szkriptet kódja kötődő `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

#### <a name="c-script-version-2x"></a>C#parancsprogram (verzió 2.x)

Függvények 2.x C# szkriptet kódja kötődő `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

További információkért lásd: [csomagok](#packages), [attribútumok](#attributes), [konfigurációs](#configuration), és [használati](#usage).

### <a name="javascript-example"></a>JavaScript-példa

Az alábbi példa bemutatja a trigger kötés egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja.

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

### <a name="trigger---java-example"></a>Eseményindító - Java-példában

Az alábbi példa bemutatja a trigger kötés egy *function.json* fájl és a egy [Java függvény](functions-reference-java.md) , amely a kötés használja, és kiírja ezt egy eseményt.

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

A Java-kód itt látható:

```java
@FunctionName("eventGridMonitor")
  public void logEvent(
     @EventGridTrigger(name = "event") String content,
      final ExecutionContext context
  ) { 
      context.getLogger().info(content);
    }
```

Az a [Java-függvények futásidejű kódtár](/java/api/overview/azure/functions/runtime), használja a `EventGridTrigger` jegyzet paraméterekkel, amelynek az értéke EventGrid kellene származnia. Ezek a jegyzetek a paraméterek miatt a funkció futtatását, amikor az esemény érkezik.  A jegyzet használható natív Java-típusokat, POJOs vagy nullázható értékek használatával `Optional<T>`. 

## <a name="attributes"></a>Attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) attribútum.

Íme egy `EventGridTrigger` attribútumot a podpis metody:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Egy teljes példa: [C#-példa](#c-example).

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt. Nem konstruktor paraméterek vagy a tulajdonságok beállításához a `EventGridTrigger` attribútum.

|Function.JSON tulajdonság |Leírás|
|---------|---------|----------------------|
| **type** | Kötelező – kell állítani `eventGridTrigger`. |
| **direction** | Kötelező – kell állítani `in`. |
| **name** | Kötelező – a függvénykódot az a paraméter, amely megkapja az eseményadatokat használt változó neve. |

## <a name="usage"></a>Használat

C# és az F # az Azure Functions függvények 1.x, az Event Grid eseményindító is használhatja a következő paraméter típusa:

* `JObject`
* `string`

Az Azure Functions C# és az F # Functions 2.x lehetősége is van az Event Grid eseményindító használata a a következő paraméter típusa:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Határozza meg az összes eseménytípusra az általános mezők tulajdonságait.

> [!NOTE]
> Ha megpróbál kötést létrehozni a Functions v1-ben `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, a fordító fog "elavult" üzenet megjelenítése, és javasolja, hogy használja `Microsoft.Azure.EventGrid.Models.EventGridEvent` helyette. Az újabb típusú használatához hivatkozhat a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet csomagot, és teljes minősítéséhez a `EventGridEvent` típusnév előtaggal rendelkező `Microsoft.Azure.EventGrid.Models`. NuGet-csomagok egy C#-szkriptfüggvény hivatkozhat kapcsolatos információkért lásd: [használatával NuGet-csomagok](functions-reference-csharp.md#using-nuget-packages)

A JavaScript-függvények, a paraméter által megnevezett a *function.json* `name` tulajdonság az eseményobjektum vonatkozó hivatkozás van.

## <a name="event-schema"></a>Eseményséma

Adatokat az Event Grid-esemény érkezik, JSON-objektumként egy HTTP-kérelem törzse. A JSON a következő példához hasonlóan néz ki:

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

A példában látható egy elemet egy tömbjét. Event Grid mindig egy tömböt küld, és több eseményt küldhet a tömbben. A futtatókörnyezet meghívja a függvényt az egyes tömbelemeken.

A legfelső szintű tulajdonságok az eseményhez tartozó JSON-adatok ugyanazok, többek között az összes eseménytípust, miközben a tartalmát a `data` tulajdonság csak az adott minden egyes esemény típusa. A bemutatott példában egy blob storage esemény van.

Az egyes a közös és az esemény-specifikus tulajdonságokat, lásd: [esemény tulajdonságai](../event-grid/event-schema.md#event-properties) az Event Grid dokumentációjában.

A `EventGridEvent` típusa csak a legfelső szintű tulajdonságait határozza meg; a `Data` tulajdonság egy `JObject`. 

## <a name="create-a-subscription"></a>Előfizetés létrehozása

Event Grid HTTP-kérések fogadása indításához hozzon létre egy Event Grid-előfizetést, amely meghatározza a végponti URL-cím, amely meghívja a függvényt.

### <a name="azure-portal"></a>Azure Portal

Az funkciók, amelyek az Azure Portalon, az Event Grid eseményindító fejleszt, válassza az **hozzáadása Event Grid-előfizetés**.

![Előfizetés létrehozása a portálon](media/functions-bindings-event-grid/portal-sub-create.png)

Amikor kiválasztja ezt a hivatkozást, a portál megnyitja az **esemény-előfizetés létrehozása** előre kitöltött lapja, a végpont URL-címe.

![Végponti URL-cím előre kitöltött](media/functions-bindings-event-grid/endpoint-url.png)

Előfizetések létrehozása az Azure portal használatával kapcsolatos további információkért lásd: [egyéni esemény létrehozása – Azure portal](../event-grid/custom-event-quickstart-portal.md) az Event Grid dokumentációjában.

### <a name="azure-cli"></a>Azure CLI

Előfizetés létrehozása használatával [az Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), használja a [az eventgrid esemény-előfizetés létrehozása](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-create) parancsot.

A parancs használatához a végponti URL-cím, amely meghívja a függvényt. Az alábbi példa bemutatja a verzió-specifikus URL-minta:

#### <a name="version-2x-runtime"></a>2.x verziójú futtatókörnyezet verziója

    https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}

#### <a name="version-1x-runtime"></a>1.x futtatókörnyezet verziója

    https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}

A rendszer kulcs egy engedélyezési kulcsot, amely egy Event Grid-triggert a végponti URL-Címének szerepelnie kell. A következő szakasz azt ismerteti, hogyan a rendszer kulcs beszerzése.

Íme egy példa, amely feliratkozik egy blob storage-fiók (az a rendszer kulcs helyőrzője):

#### <a name="version-2x-runtime"></a>2.x verziójú futtatókörnyezet verziója

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

#### <a name="version-1x-runtime"></a>1.x futtatókörnyezet verziója

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name myblobstorage12345 --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

Előfizetés létrehozásával kapcsolatos további információkért lásd: [a blob storage rövid útmutatójával](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) vagy az Event Grid rövid útmutatók.

### <a name="get-the-system-key"></a>A rendszer kulcs lekérése

A rendszer kulcs kaphat (HTTP GET) a következő API-val:

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Erre azért egy felügyeleti API-t, hogy a függvényalkalmazás igényel [főkulcs](functions-bindings-http-webhook.md#authorization-keys). Ne tévessze össze a fájlrendszer kulcsa (az Event Grid eseményindító függvényének meghívása) főkulccsal (a felügyeleti feladatok végrehajtása a függvényalkalmazás). Amikor előfizet egy Event Grid-témakört, ügyeljen arra, használja a fájlrendszer kulcsa.

Íme egy példa a válasz a rendszer kulcsot biztosító:

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

Megtekintheti a főkulcs a függvényalkalmazás a a **Alkalmazásbeállítások függvény** lapot a portálon.

> [!IMPORTANT]
> A főkulcs a függvényalkalmazás rendszergazdai hozzáférést biztosít. Ne ossza meg ezt a kulcsot harmadik felekkel, vagy osztja el a natív ügyfélalkalmazások.

További információkért lásd: [engedélyezési kulcsok](functions-bindings-http-webhook.md#authorization-keys) a HTTP-eseményindító áttekintésével foglalkozó cikkben található.

Azt is megteheti küldhet egy HTTP PUT, a kulcs értékét saját magát adja meg.

## <a name="local-testing-with-viewer-web-app"></a>A jelentésmegjelenítő webes alkalmazás helyi tesztelése

Egy Event Grid-trigger tesztelése helyileg, akkor a helyi gépen a forrásból a felhőben elérhető Event Grid HTTP-kérések lekérése. Az egyik lehetőség, amely a kérések online, és manuálisan újra elküldeni őket a helyi gépen rögzítésével van:

2. [A jelentésmegjelenítő webes alkalmazás létrehozása](#create-a-viewer-web-app) , amely eseményt üzenetek rögzíti.
3. [Hozzon létre egy Event Grid-előfizetés](#create-an-event-grid-subscription) , amely elküldi az eseményeket a megjelenítő alkalmazást.
4. [Hozzon létre egy kérelem](#generate-a-request) , és másolja a kérelem törzsében a megjelenítő alkalmazást.
5. [A kérelem fel kézzel](#manually-post-the-request) localhost URL-címét az Event Grid aktiválja a függvényt.

Ha végzett tesztelése, használhatja az ugyanahhoz az előfizetéshez éles környezetben a végpont frissítése. Használja a [az eventgrid esemény-előfizetés frissítése](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI-parancsot.

### <a name="create-a-viewer-web-app"></a>A jelentésmegjelenítő webes alkalmazás létrehozása

Rögzítését eseményüzenetek leegyszerűsítése telepíthet egy [előre elkészített webalkalmazás](https://github.com/Azure-Samples/azure-event-grid-viewer) , amely az esemény üzeneteket jelenít meg. Az üzembe helyezett megoldás egy App Service-csomagot, egy App Service-webalkalmazást és egy, a GitHubról származó forráskódot tartalmaz.

A megoldásnak az előfizetésébe való telepítéséhez válassza az **Üzembe helyezés az Azure-ban** lehetőséget. Az Azure Portalon adjon meg értékeket a paraméterekhez.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Az üzembe helyezés befejezése eltarthat néhány percig. A sikeres üzembe helyezést követően tekintse meg a webalkalmazást, hogy meggyőződjön annak működéséről. Egy webböngészőben navigáljon a következő helyre: `https://<your-site-name>.azurewebsites.net`.

A hely látható, de még nem lett közzétéve esemény.

![Új hely megtekintése](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Event Grid-előfizetés létrehozása

Hozzon létre egy Event Grid-előfizetést a vizsgálni kívánt típusú, és adjon meg az URL-címet a webalkalmazás és a végpontnak eseményértesítés. A webalkalmazás végpontjának az `/api/updates/` utótagot kell tartalmaznia. Így a teljes URL-cím van-e `https://<your-site-name>.azurewebsites.net/api/updates`

Előfizetések létrehozása az Azure portal használatával kapcsolatos információkért lásd: [egyéni esemény létrehozása – Azure portal](../event-grid/custom-event-quickstart-portal.md) az Event Grid dokumentációjában.

### <a name="generate-a-request"></a>Kérés létrehozása

A web app-végpontra HTTP-forgalmat generáló esemény aktiválása.  Például egy blob storage-előfizetést létrehozta, töltse fel, vagy egy blob törlése. Ha a kérelem megjelenik-e a webalkalmazást, másolja a kérelem törzsében.

Az előfizetés érvényesítési kérelmet fog kapni, először; ellenőrzési kérések mellőzése, és másolja az esemény kérelmet.

![Másolja ki a kérés törzse webalkalmazásból](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>A kérelem manuális közzététele

Az Event Grid függvény helyi futtatása.

Például egy eszközzel [Postman](https://www.getpostman.com/) vagy [curl](https://curl.haxx.se/docs/httpscripting.html) HTTP POST-kérelmet létrehozni:

* Állítsa be a `Content-Type: application/json` fejléc.
* Állítsa be egy `aeg-event-type: Notification` fejléc.
* A RequestBin adatok illessze be a kérelem törzsében. 
* Közzététel URL-címét az Event Grid eseményindító függvény használatával a következő mintának:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
```

A `functionName` paraméternek kell lennie a megadott név a `FunctionName` attribútum.

Az alábbi képernyőfelvételnek megfelelően megjelenítése a fejlécek, és a kérés törzsének a Postmanben:

![A Postmanben fejlécek](media/functions-bindings-event-grid/postman2.png)

![A Postmanben a kérelem törzse](media/functions-bindings-event-grid/postman.png)

Az Event Grid eseményindító függvény végrehajtása, és a naplók az alábbi példához hasonló jelenít meg:

![Minta Event Grid eseményindító függvény naplóihoz](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Helyi ngrok tesztelésével

Egy Event Grid eseményindító helyi tesztelése egy másik úgy, hogy automatizálja a HTTP-kapcsolat az Internet és a fejlesztési számítógép között. Megteheti, hogy egy nyílt forráskódú eszköz nevű [ngrok](https://ngrok.com/):

3. [Hozzon létre egy ngrok végpontot](#create-an-ngrok-endpoint).
4. [Az Event Grid eseményindító függvény futtatása](#run-the-event-grid-trigger-function).
5. [Hozzon létre egy Event Grid-előfizetés](#create-a-subscription) , amely elküldi az eseményeket a ngrok végpontot.
6. [Esemény aktiválása](#trigger-an-event).

Ha végzett tesztelése, használhatja az ugyanahhoz az előfizetéshez éles környezetben a végpont frissítése. Használja a [az eventgrid esemény-előfizetés frissítése](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-update) Azure CLI-parancsot.

### <a name="create-an-ngrok-endpoint"></a>Ngrok végpont létrehozása

Töltse le *ngrok.exe* a [ngrok](https://ngrok.com/), és futtassa a következő paranccsal:

```
ngrok http -host-header=localhost 7071
```

A - gazdagép-fejléc paraméter van szükség, mert a functions futtatókörnyezete vár a localhost érkező kérelmeket, ha futtat a localhoston. 7071 az alapértelmezett portszám esetén a futtatókörnyezet helyileg futtatja.

A parancs kimenete az alábbihoz hasonló hoz létre:

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

Szeretné használni a `https://{subdomain}.ngrok.io` az Event Grid-előfizetés URL-CÍMÉT.

### <a name="run-the-event-grid-trigger-function"></a>Az Event Grid eseményindító függvény futtatása

Ngrok URL-címe nem kaphat Event GRID, különleges kezelést, így a függvény helyileg kell futnia az előfizetés létrehozásakor. Ha nem, az érvényesítési válaszhoz nem küldi el a rendszer, és az előfizetés létrehozása sikertelen lesz.

### <a name="create-a-subscription"></a>Előfizetés létrehozása

Hozzon létre egy Event Grid-előfizetést a vizsgálni kívánt típusú, és adjon neki a ngrok végponthoz.

Ez a végpont-minta használata funkciók 1.x:
```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
```
Ez a végpont-minta használata funkciók 2.x:
```
https://{subdomain}.ngrok.io/runtime/webhooks/eventgrid?functionName={functionName}
```
A `functionName` paraméternek kell lennie a megadott név a `FunctionName` attribútum.

Íme egy példa az Azure CLI használatával:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Előfizetés létrehozásával kapcsolatos további információkért lásd: [hozzon létre egy előfizetést](#create-a-subscription) a cikk elején.

### <a name="trigger-an-event"></a>Események aktiválása

A ngrok végpontra HTTP-forgalmat generáló esemény aktiválása.  Például egy blob storage-előfizetést létrehozta, töltse fel, vagy egy blob törlése.

Az Event Grid eseményindító függvény végrehajtása, és a naplók az alábbi példához hasonló jelenít meg:

![Minta Event Grid eseményindító függvény naplóihoz](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>HTTP-trigger használja, mint egy Event Grid-triggert

Event Grid-események fogadásának HTTP-kérések, így az események HTTP-trigger helyett egy Event Grid-trigger használatával kezelheti. Egy lehetséges oka, hogy, hogy jobban szabályozhatja a végponti URL-cím, amely meghívja a függvényt kap. Ha az eseményeket fogadni szeretné a [CloudEvents-séma](../event-grid/cloudevents-schema.md). Jelenleg az Event Grid-trigger nem támogatja a CloudEvents-séma. Ebben a szakaszban szereplő példák az Event Grid-séma- és CloudEvents-séma megoldások megjelenítése.

HTTP-trigger használatakor kódot írni az Event Grid eseményindító funkciója automatikusan a rendelkezésére:

* Érvényesítési választ küld egy [előfizetés érvényesítési kérés](../event-grid/security-authentication.md#webhook-event-delivery).
* Meghívja a függvényt, az esemény-tömb, a kérelem törzsében szereplő elem esetében egyszer.

Az URL-címet ad meg a függvény helyben, vagy ha Azure-ban futó használandó kapcsolatos információkért tekintse meg a [HTTP trigger kötés dokumentációja](functions-bindings-http-webhook.md)

### <a name="event-grid-schema"></a>Event Grid-séma

Az alábbi C# mintakód a HTTP-trigger Event Grid eseményindító viselkedés szimulálja. Ebben a példában használja az rácsos sémájának kézbesített eseményekre.

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

A következő példa JavaScript-kódot a HTTP-trigger szimulálja az Event Grid eseményindító viselkedését. Ebben a példában használja az rácsos sémájának kézbesített eseményekre.

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

Az esemény-kezelő kód belül a hurok keresztül halad a `messages` tömb.

### <a name="cloudevents-schema"></a>CloudEvents-séma

Az alábbi C# mintakód a HTTP-trigger Event Grid eseményindító viselkedés szimulálja.  Ebben a példában használja a CloudEvents-séma kézbesített eseményekre.

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

A következő példa JavaScript-kódot a HTTP-trigger szimulálja az Event Grid eseményindító viselkedését. Ebben a példában használja a CloudEvents-séma kézbesített eseményekre.

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
> [További információ az Event Grid](../event-grid/overview.md)
