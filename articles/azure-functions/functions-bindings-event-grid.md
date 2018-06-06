---
title: Az Azure Functions rács eseményindító
description: Megtudhatja, hogyan kezelhet az Azure Functions esemény rács eseményeket.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/26/2018
ms.author: tdykstra
ms.openlocfilehash: 7e0fb3cee8d4ec72e1ec44f7444264fabb1dd202
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724730"
---
# <a name="event-grid-trigger-for-azure-functions"></a>Az Azure Functions rács eseményindító

Ez a cikk azt ismerteti, hogyan legyen kezelve [esemény rács](../event-grid/overview.md) események az Azure Functions.

Esemény rács az Azure-szolgáltatások HTTP-kéréseket bekövetkező eseményekről kaphat értesítést küldő *közzétevők*. A gyártót, de a szolgáltatás erőforrása, amely az esemény származik. Például egy Azure blob storage-fiók-e a közzétevő és [egy blob feltöltése vagy törlésre esemény](../storage/blobs/storage-blob-event-overview.md). Néhány [Azure-szolgáltatásokkal rendelkezik beépített támogatása az események közzétételének esemény rácshoz](../event-grid/overview.md#event-sources). 

Esemény *kezelők* fogadni és feldolgozni az eseményeket. Az Azure Functions egyike több [Azure-szolgáltatásokat, amelyek esemény rács események kezelésére beépített támogatása](../event-grid/overview.md#event-handlers). Ebből a cikkből megismerheti, hogyan lehet meghívni a függvényt esemény rácsban fogadásakor. az esemény egy esemény rács eseményindító használandó.

Tetszés szerint, egy HTTP-eseményindítóval használatával kezeli a rács eseményeinek; Lásd: [egy HTTP-eseményindítóval használják az esemény rács eseményindító](#use-an-http-trigger-as-an-event-grid-trigger) című cikkben.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x működik

Az esemény rács eseményindító megtalálható a [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-csomag verziója 1.x. A csomag forráskódja van a [azure-funkciók-eventgrid-bővítmény](https://github.com/Azure/azure-functions-eventgrid-extension/tree/master) GitHub-tárházban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok - 2.x működik

Az esemény rács eseményindító megtalálható a [Microsoft.Azure.WebJobs.Extensions.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid) NuGet-csomag verziója 2.x. A csomag forráskódja van a [azure-funkciók-eventgrid-bővítmény](https://github.com/Azure/azure-functions-eventgrid-extension/tree/v2.x) GitHub-tárházban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="example"></a>Példa

Tekintse meg a nyelvspecifikus például egy esemény rács eseményindító:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [JavaScript](#javascript-example)

HTTP-eseményindító példáért lásd: [használata a HTTP-eseményindítóval](#use-an-http-trigger-as-an-event-grid-trigger) című cikkben.

### <a name="c-example"></a>C# – példa

A következő példa bemutatja a funkciók 1.x [C# függvény](functions-dotnet-class-library.md) , amely összekapcsolja `JObject`:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

A következő példa bemutatja a funkciók 2.x [C# függvény](functions-dotnet-class-library.md) , amely összekapcsolja `EventGridEvent`:

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, TraceWriter log)
        {
            log.Info(eventGridEvent.Data.ToString());
        }
    }
}
```

További információkért lásd: [csomagok](#packages), [attribútumok](#attributes), [konfigurációs](#configuration), és [használati](#usage).

### <a name="c-script-example"></a>C# parancsfájl – példa

A következő példa bemutatja az eseményindító kötés egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja.

Itt az kötés adatai a *function.json* fájlt:

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

Itt a funkciók 1.x C# parancsfájlkód, amelyhez van kötve van `JObject`:

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

Itt a funkciók 2.x C# parancsfájlkód, amelyhez van kötve van `EventGridEvent`:

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;

public static void Run(EventGridEvent eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.Data.ToString());
}
```

További információkért lásd: [csomagok](#packages), [attribútumok](#attributes), [konfigurációs](#configuration), és [használati](#usage).

### <a name="javascript-example"></a>JavaScript – példa

A következő példa bemutatja az eseményindító kötés egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja.

Itt az kötés adatai a *function.json* fájlt:

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

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```
     
## <a name="attributes"></a>Attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [EventGridTrigger](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/EventGridTriggerAttribute.cs) attribútum.

Íme egy `EventGridTrigger` metódus-aláírás attribútum:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, TraceWriter log)
{
    ...
}
 ```

Tekintse meg a teljes például [C# példa](#c-example).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájlt. Nincsenek konstruktorparaméterek és tulajdonságok a `EventGridTrigger` attribútum.

|Function.JSON tulajdonság |Leírás|
|---------|---------|----------------------|
| **type** | Szükséges – kell állítani `eventGridTrigger`. |
| **direction** | Szükséges – kell állítani `in`. |
| **name** | Kötelező – a változó nevét, amely megkapja a eseményadatok paramétereként függvény a kódban használt. |

## <a name="usage"></a>Használat

Az Azure-ban a C# és F # függvények 1.x működik, a következő paraméter típusok használhatók az esemény rács eseményindító:

* `JObject`
* `string`

Az Azure Functions a C# és F # funkciók 2.x, lehetősége is van a következő paraméter típusa használatára az esemény rács eseményindító:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`-Határozza meg a közös mezők való összes eseménytípust.

> [!NOTE]
> A funkciók v1 köthető jelszómódosítás `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent`, a fordító fog "elavult" üzenetet jelenít meg, és hogy használata `Microsoft.Azure.EventGrid.Models.EventGridEvent` helyette. Az újabb típust használatához hivatkozzon a [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) NuGet csomag és a teljes minősítéséhez a `EventGridEvent` pontosítani a típusnév `Microsoft.Azure.EventGrid.Models`. Hogyan hivatkozható a C# parancsfájl függvényben a NuGet-csomagok kapcsolatos információkért lásd: [használatával NuGet-csomagok](functions-reference-csharp.md#using-nuget-packages)

A JavaScript-funkcióként, a paraméter neve szerint a *function.json* `name` tulajdonságnak egy esemény objektum hivatkozását.

## <a name="event-schema"></a>Eseményséma

Egy esemény rács esemény adatainak érkezett egy JSON-objektum egy HTTP-kérés törzsében. A JSON az alábbihoz hasonlít:

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

A bemutatott példában egy elemű tömbnek. Esemény rács mindig elküldi a tömbben, és több esemény küldhet a tömbben. A futtatókörnyezet hív meg, a függvény minden tömbelem egyszer.

A legfelső szintű tulajdonságok az eseményben JSON-adatok: minden eseménytípushoz, miközben tartalma megegyezik a `data` tulajdonság csak az adott minden esemény típusa. A bemutatott példában egy blob storage esemény van.

Az egyes az általános és a vonatkozó esemény tulajdonságai, lásd: [esemény tulajdonságai](../event-grid/event-schema.md#event-properties) a rács esemény dokumentációjában.

A `EventGridEvent` típus csak a legfelső szintű tulajdonságainak meghatározása; a `Data` tulajdonság egy `JObject`. 

## <a name="create-a-subscription"></a>Előfizetés létrehozása

Esemény rács HTTP-kérelmek fogadását elindításához előfizetéssel, hozzon létre esemény rács, amely meghatározza a végpont URL-címet, amely meghívja a függvényt.

### <a name="azure-portal"></a>Azure Portal

Az Azure-portálon az esemény rács eseményindító fejlesztése függvények, válassza a **hozzáadása esemény rács előfizetés**.

![Előfizetés létrehozása a portálon](media/functions-bindings-event-grid/portal-sub-create.png)

Ez a hivatkozás kiválasztásakor a portál megnyitja a **esemény-előfizetés létrehozása** előre kitöltött lap a végpont URL-címet.

![Végponti URL-cím előre kitöltött](media/functions-bindings-event-grid/endpoint-url.png)

Előfizetések létrehozása az Azure portál használatával kapcsolatos további információkért lásd: [hozzon létre egyéni esemény - Azure-portálon](../event-grid/custom-event-quickstart-portal.md) a rács esemény dokumentációjában.

### <a name="azure-cli"></a>Azure CLI

Előfizetés létrehozása a használatával [az Azure parancssori felület](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest), használja a [az eventgrid esemény-előfizetés létrehozása](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) parancsot.

A parancs, amely meghívja a függvényt végpont URL-címet igényel. A következő példa bemutatja az URL-minta:

```
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

A rendszer, amely rendelkezik a végponti URL-cím egy esemény rács eseményindító szereplő engedélykulcs kulcsa. A következő szakasz azt ismerteti, hogy a rendszer kulcs beszerzése.

Íme egy példa, amely egy blob storage-fiókkal (és a rendszer kulcs helyőrzője) számítógépcsoportra fizetett elő:

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
--provider-namespace Microsoft.Storage --resource-type storageAccounts \
--resource-name glengablobstorage --name myFuncSub  \
--included-event-types Microsoft.Storage.BlobCreated \
--subject-begins-with /blobServices/default/containers/images/blobs/ \
--endpoint https://glengastorageevents.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=LUwlnhIsNtSiUjv/sNtSiUjvsNtSiUjvsNtSiUjvYb7XDonDUr/RUg==
```

Előfizetés létrehozásával kapcsolatos további információkért lásd: [a blob storage gyors üzembe helyezés](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) vagy a más esemény rács quickstarts.

### <a name="get-the-system-key"></a>A rendszer kulcs beszerzése

A rendszer kulcs kérhető le a következő API-val (HTTP GET):

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={adminkey}
```

Ez az API-t, hogy egy rendszergazda, ezért van szükség a [adminisztrációs kulcsot](functions-bindings-http-webhook.md#authorization-keys). Az adminisztrátori kulcsot (a rendszergazdai feladatok az függvény alkalmazás) rendelkező ne tévessze össze a rendszerkulcsát (egy esemény rács eseményindító függvény meghívása). Amikor előfizet egy esemény rács témakör, ügyeljen arra, hogy a rendszer-kulcsot használ.

Íme egy példa a választ, a rendszer-kulcsot biztosító:

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

További információkért lásd: [engedélyezési kulcsok](functions-bindings-http-webhook.md#authorization-keys) a HTTP-eseményindító áttekintésével foglalkozó cikkben található. 

Másik lehetőségként küldhet egy HTTP PUT saját maga a kulcs értékét meg.

## <a name="local-testing-with-requestbin"></a>Helyi RequestBin teszteléshez

> [!NOTE]
> A RequestBin hely jelenleg nem érhető el, de használhatja ezt a módszert a https://hookbin.com helyette. Ha a hely nem működik, használhatja [ngrok](#local-testing-with-ngrok).

Egy esemény rács eseményindító teszteléséhez helyileg, akkor a helyi gép a felhőben a forrásból kézbesíteni esemény rács HTTP-kérelmek get. Ehhez egy úgy, hogy a rögzítés kérelmek online, és manuálisan küldje el újra azokat a helyi számítógépen:

2. [Hozzon létre egy RequestBin végpontot](#create-a-RequestBin-endpoint).
3. [Esemény rács előfizetéssel, hozzon létre](#create-an-event-grid-subscription) a RequestBin végpont az eseményeket, amelyek küldi.
4. [A kérelem generálásához](#generate-a-request) és a kérés törzsében másolja a RequestBin webhely.
5. [A kérelem fel kézzel](#manually-post-the-request) localhost URL-címét az esemény rács indítás függvény.

Ha elkészült tesztelni, használhatja ugyanazt az előfizetést üzemi a végpont frissítése. Használja a [az eventgrid esemény-előfizetés frissítése](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI parancsot.

### <a name="create-a-requestbin-endpoint"></a>RequestBin-végpont létrehozása

RequestBin egy nyílt forráskódú eszköz, amely elfogadja a HTTP-kérelmekre, és megjeleníti a kérés törzsében. A http://requestb.in URL-cím beolvasása különleges kezelés Azure esemény rács által. Tesztelés megkönnyítéséhez esemény rács eseményeket küldi a RequestBin URL-cím anélkül, hogy az előfizetés érvényesítése kérelmek helyes választ. Egy vizsgálati eszköz kap azonos kezelésére: http://hookbin.com.

RequestBin nem célja a magas teljesítmény-használatról. Ha egyszerre több eseményt továbbít, lehetséges, hogy az eszközben nem fog megjelenni az összes esemény.

Hozzon létre egy végpontot.

![RequestBin-végpont létrehozása](media/functions-bindings-event-grid/create-requestbin.png)

A végpont URL-Címének másolása.

![Másolás RequestBin végpont](media/functions-bindings-event-grid/save-requestbin-url.png)

### <a name="create-an-event-grid-subscription"></a>Egy esemény rács előfizetés létrehozása

A vizsgálni kívánt típusú esemény rács előfizetéssel, hozzon létre, és adjon neki RequestBin végpontot. Előfizetés létrehozásával kapcsolatos további információkért lásd: [előfizetés létrehozása](#create-a-subscription) korábbi ebben a cikkben.

### <a name="generate-a-request"></a>A kérelem létrehozása

Indítás, egy eseményt, amely HTTP-forgalom a RequestBin végponthoz hoz létre.  Például ha létrehozott egy blob storage-előfizetés, töltse fel, vagy egy blobot törölni. Amikor egy kérelem megjelennek a RequestBin lap, másolja a kérés törzsében.

Az előfizetés-ellenőrzési kérelem fogadja először; figyelmen kívül hagyja a érvényesítési kéréseit, és másolja a esemény kérelmet.

![Kérés törzsében másolása RequestBin](media/functions-bindings-event-grid/copy-request-body.png)

### <a name="manually-post-the-request"></a>A kérelem manuálisan utáni

Futtassa helyben a esemény rácsos függvényt.

Használjon például egy eszköz [Postman](https://www.getpostman.com/) vagy [curl](https://curl.haxx.se/docs/httpscripting.html) HTTP POST-kérelmet létrehozni:

* Állítsa be a `Content-Type: application/json` fejléc.
* Állítsa be az `aeg-event-type: Notification` fejléc.
* Illessze be a RequestBin adatokat a kérés törzsében. 
* Küldje el az URL-CÍMÉT az esemény rács funkció, a következő minta használatával:

```
http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

A `functionName` paraméternek kell lennie a megadott név a `FunctionName` attribútum.

Az alábbi képek a fejléc megjelenítése, és a kérelem törzse a Postman:

![Fejlécek Postman](media/functions-bindings-event-grid/postman2.png)

![Postman a kérelem törzsében](media/functions-bindings-event-grid/postman.png)

Az esemény rács funkció hajt végre, és naplók az alábbi példához hasonló jeleníti meg:

![A minta esemény rács eseményindító függvény naplók](media/functions-bindings-event-grid/eg-output.png)

## <a name="local-testing-with-ngrok"></a>Helyi ngrok teszteléshez

Egy másik helyileg egy esemény rács eseményindító tesztelése módja a HTTP-kapcsolaton az internethez és a fejlesztési számítógép közötti automatizálását. Egy nevű nyílt forráskódú eszköz, amely teheti [ngrok](https://ngrok.com/):

3. [Hozzon létre egy ngrok végpontot](#create-an-ngrok-endpoint).
4. [Futtassa az esemény rács funkció](#run-the-event-grid-trigger-function).
5. [Esemény rács előfizetéssel, hozzon létre](#create-a-subscription) a ngrok végpont az eseményeket, amelyek küldi.
6. [Egy esemény következik](#trigger-an-event).

Ha elkészült tesztelni, használhatja ugyanazt az előfizetést üzemi a végpont frissítése. Használja a [az eventgrid esemény-előfizetés frissítése](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update) Azure CLI parancsot.

### <a name="create-an-ngrok-endpoint"></a>Hozzon létre egy ngrok végpontot

Töltse le *ngrok.exe* a [ngrok](https://ngrok.com/), és futtassa a következő paranccsal:

```
ngrok http -host-header=localhost 7071
```

A - host-fejléc paraméter van szükség, mert a functions futtatókörnyezete localhost kérelmeinek vár, a localhost futtatásakor. 7071 az alapértelmezett portszám esetén helyben fut a futtatókörnyezetben.

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

Az esemény rács előfizetéséhez a https://{subdomain}.ngrok.io URL-címet fogja használni.

### <a name="run-the-event-grid-trigger-function"></a>Az esemény rács funkció futtatása

Ngrok URL-CÍMÉT nem olvasható be a különleges kezelést esemény rács által, a függvény futnia kell az helyileg az előfizetés létrehozásakor. Ha nem, az érvényesítési válasz nem sikerül elküldeni, és az előfizetés létrehozása sikertelen.

### <a name="create-a-subscription"></a>Előfizetés létrehozása

A vizsgálni kívánt típusú esemény rács előfizetéssel, hozzon létre, és adjon neki a ngrok végpont a következő mintát:

```
https://{subdomain}.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName={functionname}
``` 

A `functionName` paraméternek kell lennie a megadott név a `FunctionName` attribútum.

Íme egy példa az Azure parancssori felület használatával:

```
az eventgrid event-subscription create --resource-id /subscriptions/aeb4b7cb-b7cb-b7cb-b7cb-b7cbb6607f30/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstor0122 --name egblobsub0126 --endpoint https://263db807.ngrok.io/admin/extensions/EventGridExtensionConfig?functionName=EventGridTrigger
```

Előfizetés létrehozásával kapcsolatos további információkért lásd: [előfizetés létrehozása](#create-a-subscription) korábbi ebben a cikkben.

### <a name="trigger-an-event"></a>Események aktiválása

Indítás, egy eseményt, amely HTTP-forgalom a ngrok végponthoz hoz létre.  Például ha létrehozott egy blob storage-előfizetés, töltse fel, vagy egy blobot törölni.

Az esemény rács funkció hajt végre, és naplók az alábbi példához hasonló jeleníti meg:

![A minta esemény rács eseményindító függvény naplók](media/functions-bindings-event-grid/eg-output.png)

## <a name="use-an-http-trigger-as-an-event-grid-trigger"></a>Egy esemény rács eseményindító egy HTTP-eseményindítóval használata

Rács eseményeinek fogadott HTTP-kérelmekre, így az események az esemény rács eseményindító helyett a HTTP-eseményindítóval használatával kezelheti. A, amely egyik lehetséges ok: a végpont URL-címet, amely meghívja a függvényt teljesebb körű vezérlése segítségével. 

Ha egy HTTP-eseményindítóval használatához írhat kódot a esemény rács eseményindító funkciója automatikusan van:

* Érvényesítési választ küld egy [előfizetés ellenőrzési kérelem](../event-grid/security-authentication.md#webhook-event-delivery).
* Meghívja a függvényt a kérelem törzsében lévő esemény tömb elemnek egy alkalommal.

Az alábbi C# mintakód egy HTTP-eseményindító szimulálja esemény rács indítási viselkedést:

```csharp
[FunctionName("HttpTrigger")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]HttpRequestMessage req,
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    var messages = await req.Content.ReadAsAsync<JArray>();

    // If the request is for subscription validation, send back the validation code.
    if (messages.Count > 0 && string.Equals((string)messages[0]["eventType"], 
        "Microsoft.EventGrid.SubscriptionValidationEvent", 
        System.StringComparison.OrdinalIgnoreCase))
    {
        log.Info("Validate request received");
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
        log.Info($"Subject: {eventGridEvent.Subject}");
        log.Info($"Time: {eventGridEvent.EventTime}");
        log.Info($"Event data: {eventGridEvent.Data.ToString()}");
    }

    return req.CreateResponse(HttpStatusCode.OK);
}
```

A következő minta JavaScript-kódot egy HTTP-eseményindító szimulálja esemény rács indítási viselkedést:

```javascript
module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    var messages = req.body;
    // If the request is for subscription validation, send back the validation code.
    if (messages.length > 0 && messages[0].eventType == "Microsoft.EventGrid.SubscriptionValidationEvent") {
        context.log('Validate request received');
        context.res = { status: 200, body: JSON.stringify({validationResponse: messages[0].data.validationCode}) }
    }
    else {
        // The request is not for subscription validation, so it's for one or more events.
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

A eseménykezelésnek kód belül a hurok keresztül kerül a `messages` tömb.

Meghívja a helyi vagy mikor fusson az Azure-ban használandó URL-címet kapcsolatos információkért lásd: a [HTTP eseményindító kötés referenciadokumentációt](functions-bindings-http-webhook.md) 

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [További tudnivalók az esemény rács](../event-grid/overview.md)
