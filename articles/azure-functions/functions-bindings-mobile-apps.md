---
title: Mobilalkalmazások kötései az Azure Functionshez
description: Ismerje meg, hogyan használhatja az Azure Mobile Apps-kötéseket az Azure Functionsben.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76120541"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobilalkalmazások kötései az Azure Functionshez 

> [!NOTE]
> Az Azure Mobile Apps-kötések csak az Azure Functions 1.x számára érhetők el. Az Azure Functions 2.x és újabb verziók nem támogatottak.

Ez a cikk bemutatja, hogyan működik együtt az [Azure Mobile Apps-kötések](../app-service-mobile/app-service-mobile-value-prop.md) az Azure Functionsben. Az Azure Functions támogatja a mobilalkalmazások bemeneti és kimeneti kötéseit.

A Mobilalkalmazások kötései lehetővé teszik az adattáblák olvasását és frissítését a mobilalkalmazásokban.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

A mobilalkalmazások kötéseit a [Microsoft.Azure.WebJobs.Extensions.MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet csomag 1.x-es verziója tartalmazza. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub-tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Input (Bemenet)

A Mobile Apps input kötés betölt egy rekordot egy mobil tábla végpontról, és továbbítja azt a funkcióba. A C# és Az F# függvényekben a rekordon végrehajtott módosítások automatikusan visszakerülnek a táblába, amikor a függvény sikeresen kilép.

## <a name="input---example"></a>Bemenet - példa

Lásd a nyelvspecifikus példát:

* [C#-szkript (.csx)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Input - C# parancsfájl példa

A következő példa egy Mobile Apps bemeneti kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvényt egy rekordazonosítóval rendelkező üzenetsor-üzenet váltja ki. A függvény beolvassa a megadott rekordot, és módosítja annak tulajdonságát. `Text`

A *function.json* fájlban a kötési adatok:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```cs
#r "Newtonsoft.Json"    
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, JObject record)
{
    if (record != null)
    {
        record["Text"] = "This has changed.";
    }    
}
```

### <a name="input---javascript"></a>Bemenet - JavaScript

A következő példa egy Mobile Apps bemeneti kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvényt egy rekordazonosítóval rendelkező üzenetsor-üzenet váltja ki. A függvény beolvassa a megadott rekordot, és módosítja annak tulajdonságát. `Text`

A *function.json* fájlban a kötési adatok:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
        "name": "record",
        "type": "mobileTable",
        "tableName": "MyTable",
        "id" : "{queueTrigger}",
        "connection": "My_MobileApp_Url",
        "apiKey": "My_MobileApp_Key",
        "direction": "in"
    }
]
}
```
A [konfigurációs](#input---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Bemenet - attribútumok

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútumot.

A konfigurálható attribútumtulajdonságokról [a következő konfigurációs szakaszban](#input---configuration)talál további információt.

## <a name="input---configuration"></a>Bemenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `MobileTable` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
| **Típus**| n/a | Be kell állítani a "mobileTable"|
| **direction**| n/a |Be kell állítani, hogy "in"|
| **név**| n/a | A bemeneti paraméter neve a függvényaláírásban.|
|**táblaneve** |**TableName**|A mobilalkalmazás adattáblájának neve|
| **id**| **Id** | A beolvasandó rekord azonosítója. Lehet statikus vagy a függvényt meghívó eseményindító alapján. Ha például egy várólista-eseményindítót `"id": "{queueTrigger}"` használ a függvényhez, akkor a várólista-üzenet karakterláncértékét használja rekordazonosítóként a beolvasáshoz.|
|**Kapcsolat**|**Kapcsolat**|A mobilalkalmazás URL-címét tartalmazó alkalmazásbeállítás neve. A függvény ezt az URL-címet használja a szükséges REST-műveletek mobilalkalmazáshoz való összeállításához. Hozzon létre egy alkalmazásbeállítást a függvényalkalmazásban, amely tartalmazza a mobilalkalmazás URL-címét, majd adja meg az alkalmazásbeállítás nevét a tulajdonságban a `connection` bemeneti kötésben. Az URL `http://<appname>.azurewebsites.net`így néz ki.
|**apiKey (apiKey)**|**ApiKey (ApiKey)**|A mobilalkalmazás API-kulcsával rendelkező alkalmazásbeállítás neve. Adja meg az API-kulcsot, ha [API-kulcsot valósít meg a Node.js mobilalkalmazásban,](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)vagy [valósít meg egy API-kulcsot a .NET mobilalkalmazásban.](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) A kulcs biztosításához hozzon létre egy alkalmazásbeállítást a függvényalkalmazásban, `apiKey` amely tartalmazza az API-kulcsot, majd adja hozzá a tulajdonságot a bemeneti kötéshez az alkalmazásbeállítás nevével. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg az API-kulcsot a mobilalkalmazás-ügyfelekkel. Csak biztonságosan kell elosztani a szolgáltatásoldali ügyfelek, például az Azure Functions. Az Azure Functions a kapcsolatadatait és az API-kulcsokat alkalmazásbeállításokként tárolja, hogy azok ne vesse be őket a forrásvezérlő tárházba. Ez védi a bizalmas információkat.

## <a name="input---usage"></a>Bemenet - használat

A C# függvényekben, amikor a megadott azonosítóval rendelkező rekord található, a rendszer átkerül a megnevezett [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) paraméterbe. Ha a rekord nem található, `null`a paraméter értéke . 

JavaScript-függvényekben a rekord átkerül az `context.bindings.<name>` objektumba. Ha a rekord nem található, `null`a paraméter értéke . 

A C# és Az F# függvényekben a bemeneti rekordon (bemeneti paraméter) végzett módosítások automatikusan visszakerülnek a táblába, amikor a függvény sikeresen kilép. A JavaScript-függvényekben nem lehet módosítani egy rekordot.

## <a name="output"></a>Kimenet

A Mobile Apps kimenetkötés sel új rekordot írhat egy Mobile Apps táblába.  

## <a name="output---example"></a>Kimenet - példa

Lásd a nyelvspecifikus példát:

* [C #](#output---c-example)
* [C#-szkript (.csx)](#output---c-script-example)
* [Javascript](#output---javascript-example)

### <a name="output---c-example"></a>Kimenet - C# példa

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amelyet egy várólista-üzenet indít el, és létrehoz egy rekordot egy mobilalkalmazás-táblában.

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    return new { Text = $"I'm running in a C# function! {myQueueItem}" };
}
```

### <a name="output---c-script-example"></a>Kimenet - C# parancsfájl példa

A következő példa egy Mobile Apps kimeneti kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvényt egy üzenetsor-üzenet váltja ki, és új `Text` rekordot hoz létre a tulajdonság nehezen kódolt értékével.

A *function.json* fájlban a kötési adatok:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
]
}
```

A [konfigurációs](#output---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a C# script kód:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Kimenet - JavaScript példa

A következő példa egy Mobile Apps kimeneti kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvényt egy üzenetsor-üzenet váltja ki, és új `Text` rekordot hoz létre a tulajdonság nehezen kódolt értékével.

A *function.json* fájlban a kötési adatok:

```json
{
"bindings": [
    {
    "name": "myQueueItem",
    "queueName": "myqueue-items",
    "connection":"",
    "type": "queueTrigger",
    "direction": "in"
    },
    {
    "name": "record",
    "type": "mobileTable",
    "tableName": "MyTable",
    "connection": "My_MobileApp_Url",
    "apiKey": "My_MobileApp_Key",
    "direction": "out"
    }
],
"disabled": false
}
```

A [konfigurációs](#output---configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Kimenet - attribútumok

A [C# osztálykönyvtárakban](functions-dotnet-class-library.md)használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútumot.

A konfigurálható attribútumtulajdonságokról a [Kimenet - konfiguráció](#output---configuration)című témakörben talál további információt. Íme egy `MobileTable` attribútum példa egy metódus aláírás:

```csharp
[FunctionName("MobileAppsOutput")]        
[return: MobileTable(ApiKeySetting = "MyMobileAppKey", TableName = "MyTable", MobileAppUriSetting = "MyMobileAppUri")]
public static object Run(
    [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
    TraceWriter log)
{
    ...
}
```

Egy teljes példa, lásd: [Kimenet - C# példa](#output---c-example).

## <a name="output---configuration"></a>Kimenet - konfiguráció

Az alábbi táblázat a *function.json* fájlban és az `MobileTable` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
| **Típus**| n/a | Be kell állítani a "mobileTable"|
| **direction**| n/a |Be kell állítani, hogy "ki"|
| **név**| n/a | A kimeneti paraméter neve a függvényaláírásban.|
|**táblaneve** |**TableName**|A mobilalkalmazás adattáblájának neve|
|**Kapcsolat**|**MobileAppUriSetting beállítás**|A mobilalkalmazás URL-címét tartalmazó alkalmazásbeállítás neve. A függvény ezt az URL-címet használja a szükséges REST-műveletek mobilalkalmazáshoz való összeállításához. Hozzon létre egy alkalmazásbeállítást a függvényalkalmazásban, amely tartalmazza a mobilalkalmazás URL-címét, majd adja meg az alkalmazásbeállítás nevét a tulajdonságban a `connection` bemeneti kötésben. Az URL `http://<appname>.azurewebsites.net`így néz ki.
|**apiKey (apiKey)**|**ApiKeySetting (ApiKeySetting) beállítás**|A mobilalkalmazás API-kulcsával rendelkező alkalmazásbeállítás neve. Adja meg az API-kulcsot, ha [api-kulcsot valósít meg a Node.js mobilalkalmazás-háttérben,](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)vagy [valósít meg egy API-kulcsot a .NET mobilalkalmazás-háttérben.](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key) A kulcs biztosításához hozzon létre egy alkalmazásbeállítást a függvényalkalmazásban, `apiKey` amely tartalmazza az API-kulcsot, majd adja hozzá a tulajdonságot a bemeneti kötéshez az alkalmazásbeállítás nevével. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg az API-kulcsot a mobilalkalmazás-ügyfelekkel. Csak biztonságosan kell elosztani a szolgáltatásoldali ügyfelek, például az Azure Functions. Az Azure Functions a kapcsolatadatait és az API-kulcsokat alkalmazásbeállításokként tárolja, hogy azok ne vesse be őket a forrásvezérlő tárházba. Ez védi a bizalmas információkat.

## <a name="output---usage"></a>Kimenet - használat

A C# parancsfájlfüggvényekben használjon egy `out object` elnevezett kimeneti paramétert a kimeneti rekord eléréséhez. A C# osztálytárakban az `MobileTable` attribútum a következő típusok bármelyikével használható:

* `ICollector<T>`vagy `IAsyncCollector<T>`, `T` ahol `JObject` vagy bármilyen `public string Id` típusú tulajdonsággal.
* `out JObject`
* `out T`vagy `out T[]`, `T` ahol van `public string Id` olyan típus, amelynek tulajdonsága van.

A Node.js függvényekben a kimeneti rekord eléréséhez használható. `context.bindings.<name>`

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)
