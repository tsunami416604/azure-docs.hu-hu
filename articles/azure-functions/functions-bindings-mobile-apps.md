---
title: Azure Functions-kötések Mobile Apps
description: Ismerje meg, hogyan használhatók az Azure Mobile Apps-kötések a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 952a94797e01a3931fdd151461250af0c2590c11
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76120541"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Azure Functions-kötések Mobile Apps 

> [!NOTE]
> Az Azure Mobile Apps-kötések csak az 1. x Azure Functions számára érhetők el. A Azure Functions 2. x vagy újabb verziókban nem támogatottak.

Ez a cikk azt ismerteti, hogyan használhatók az [Azure Mobile apps](../app-service-mobile/app-service-mobile-value-prop.md) -kötések a Azure Functionsban. A Azure Functions a Mobile Appshoz tartozó bemeneti és kimeneti kötéseket támogatja.

A Mobile Apps kötések lehetővé teszik adattáblák olvasását és frissítését a Mobile apps szolgáltatásban.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

Mobile Apps kötések a [Microsoft. Azure. webjobs. Extensions. MobileApps](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet csomagban találhatók, 1. x verzióban. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) tárházban található.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Input (Bemenet)

A Mobile Apps bemeneti kötés egy rekordot tölt be egy Mobile Table végpontból, és átadja a függvénynek. A C# és F# a függvények esetében a rekordon végrehajtott módosítások automatikusan vissza lesznek küldve a táblába, amikor a függvény sikeresen kilép.

## <a name="input---example"></a>Bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#parancsfájl (. CSX)](#input---c-script-example)
* JavaScript

### <a name="input---c-script-example"></a>Bemeneti – C# parancsfájl – példa

Az alábbi példa egy Mobile apps bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvényt egy, a rekord azonosítóját tartalmazó üzenetsor-üzenet indítja el. A függvény beolvassa a megadott rekordot, és módosítja a `Text` tulajdonságát.

Itt található a *function. JSON* fájlban található kötési adat:

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
A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

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

### <a name="input---javascript"></a>Bemenet – JavaScript

Az alábbi példa egy Mobile Apps bemeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvényt egy, a rekord azonosítóját tartalmazó üzenetsor-üzenet indítja el. A függvény beolvassa a megadott rekordot, és módosítja a `Text` tulajdonságát.

Itt található a *function. JSON* fájlban található kötési adat:

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
A [konfigurációs](#input---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Input-attributes

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútumot.

További információ a konfigurálható attribútumok tulajdonságairól: [a következő konfigurációs szakasz](#input---configuration).

## <a name="input---configuration"></a>Bemenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `MobileTable` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type**| – | "MobileTable" értékre kell állítani|
| **direction**| – |"In" értékre kell állítani|
| **név**| – | A függvény aláírásában szereplő bemeneti paraméter neve.|
|**Táblanév** |**Táblanév**|A Mobile alkalmazás adattáblájának neve|
| **id**| **Azonosító** | A beolvasandó rekord azonosítója. Lehet statikus vagy a függvényt meghívó trigger alapján. Ha például üzenetsor-triggert használ a függvényhez, akkor `"id": "{queueTrigger}"` a lekérdezni kívánt rekordazonosítóként használja az üzenetsor-azonosító karakterlánc értékét.|
|**kapcsolat**|**Kapcsolat**|A Mobile App URL-címét tartalmazó Alkalmazásbeállítás neve. A függvény ezt az URL-címet használja a szükséges REST-műveletek létrehozásához a mobil alkalmazáson. Hozzon létre egy alkalmazás-beállítást a Function alkalmazásban, amely tartalmazza a Mobile App URL-címét, majd adja meg az Alkalmazásbeállítás nevét a bemeneti kötés `connection` tulajdonságában. Az URL-cím úgy néz ki, mint `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|A Mobile App API-kulcsát tartalmazó Alkalmazásbeállítás neve. Adja meg az API-kulcsot, ha [implementál egy API-kulcsot a Node. js Mobile alkalmazásban](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [implementál egy API-kulcsot a .net Mobile-alkalmazásban](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). A kulcs megadásához hozzon létre egy alkalmazást a Function alkalmazásban, amely tartalmazza az API-kulcsot, majd adja hozzá az `apiKey` tulajdonságot a bemeneti kötésben az Alkalmazásbeállítás nevével. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg az API-kulcsot a Mobile App-ügyfelekkel. Ezt a szolgáltatást csak biztonságosan kell kiosztani a szolgáltatás-oldali ügyfelekhez, például a Azure Functionshoz. Azure Functions a rendszer az alkalmazás beállításainak megfelelően tárolja a kapcsolatok adatait és az API-kulcsokat, hogy azok ne legyenek bejelölve a verziókövetés adattárában. Ez védi a bizalmas adatokat.

## <a name="input---usage"></a>Bemenet – használat

A C# functions szolgáltatásban, ha a megadott azonosítójú rekord található, a rendszer átadja a nevet a [JObject](https://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) paraméternek. Ha a rekord nem található, a paraméter értéke `null`. 

JavaScript-függvényekben a rekord átkerül a `context.bindings.<name>` objektumba. Ha a rekord nem található, a paraméter értéke `null`. 

A C# és F# a függvényekben a bemeneti rekordon végrehajtott módosításokat (bemeneti paraméter) a rendszer automatikusan visszaküldi a táblába, amikor a függvény sikeresen kilép. JavaScript-függvényekben nem módosítható rekord.

## <a name="output"></a>Kimenet

A Mobile Apps kimeneti kötés használatával új rekordot írhat egy Mobile Apps táblába.  

## <a name="output---example"></a>Kimenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C#parancsfájl (. CSX)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimenet – C# példa

Az alábbi példa egy üzenetsor-üzenet által aktivált [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely egy rekordot hoz létre egy Mobile App-táblában.

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

### <a name="output---c-script-example"></a>Kimenet – C# parancsfájl – példa

Az alábbi példa egy Mobile apps kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény üzenetsor-üzenettel aktiválódik, és egy új rekordot hoz létre, amely rögzített értékkel rendelkezik a `Text` tulajdonsághoz.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa egy Mobile Apps kimeneti kötést mutat be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény üzenetsor-üzenettel aktiválódik, és egy új rekordot hoz létre, amely rögzített értékkel rendelkezik a `Text` tulajdonsághoz.

Itt található a *function. JSON* fájlban található kötési adat:

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

A [konfigurációs](#output---configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Kimenet – attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútumot.

További információ a konfigurálható attribútumok tulajdonságairól: [kimeneti konfiguráció](#output---configuration). Példa `MobileTable` attribútumra a metódus-aláírásban:

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

Teljes példa: [kimenet – C# példa](#output---c-example).

## <a name="output---configuration"></a>Kimenet – konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `MobileTable` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type**| – | "MobileTable" értékre kell állítani|
| **direction**| – |"Out" értékre kell állítani|
| **név**| – | A függvény aláírásában szereplő kimeneti paraméter neve.|
|**Táblanév** |**Táblanév**|A Mobile alkalmazás adattáblájának neve|
|**kapcsolat**|**MobileAppUriSetting**|A Mobile App URL-címét tartalmazó Alkalmazásbeállítás neve. A függvény ezt az URL-címet használja a szükséges REST-műveletek létrehozásához a mobil alkalmazáson. Hozzon létre egy alkalmazás-beállítást a Function alkalmazásban, amely tartalmazza a Mobile App URL-címét, majd adja meg az Alkalmazásbeállítás nevét a bemeneti kötés `connection` tulajdonságában. Az URL-cím úgy néz ki, mint `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|A Mobile App API-kulcsát tartalmazó Alkalmazásbeállítás neve. Adja meg az API-kulcsot, ha [implementál egy API-kulcsot a Node. js-alapú alkalmazási háttérrendszer](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)-felületén, vagy [egy API-kulcsot implementál a .net Mobile apps-háttérbe](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). A kulcs megadásához hozzon létre egy alkalmazást a Function alkalmazásban, amely tartalmazza az API-kulcsot, majd adja hozzá az `apiKey` tulajdonságot a bemeneti kötésben az Alkalmazásbeállítás nevével. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg az API-kulcsot a Mobile App-ügyfelekkel. Ezt a szolgáltatást csak biztonságosan kell kiosztani a szolgáltatás-oldali ügyfelekhez, például a Azure Functionshoz. Azure Functions a rendszer az alkalmazás beállításainak megfelelően tárolja a kapcsolatok adatait és az API-kulcsokat, hogy azok ne legyenek bejelölve a verziókövetés adattárában. Ez védi a bizalmas adatokat.

## <a name="output---usage"></a>Kimenet – használat

A C# script functions-ben használjon `out object` típusú kimeneti paramétert a kimeneti rekord eléréséhez. Az C# osztályok könyvtáraiban a `MobileTable` attribútum a következő típusok bármelyikével használható:

* `ICollector<T>` vagy `IAsyncCollector<T>`, ahol a `T` `JObject`, vagy bármilyen típusú `public string Id` tulajdonsággal.
* `out JObject`
* `out T` vagy `out T[]`, ahol a `T` típusa `public string Id` tulajdonságú.

A Node. js függvényekben `context.bindings.<name>` használatával férhet hozzá a kimeneti rekordhoz.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
