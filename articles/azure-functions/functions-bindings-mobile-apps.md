---
title: "Az Azure Functions Mobile Apps kötései"
description: "Azure Mobile Apps kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: c5fb7bdd88691c9aeab6b348507901c34502b28b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Az Azure Functions Mobile Apps kötései 

Ez a cikk azt ismerteti, hogyan használható [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) Azure Functions kötések. Azure Functions támogatja bemeneti és kimeneti Mobile Apps kötéseit.

A Mobile Apps kötések lehetővé teszik, hogy olvassa és frissítse a mobilalkalmazásokban adattáblák.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Csomagok

A Mobile Apps kötések találhatók a [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet-csomagot. A csomag forráskódja van a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/) GitHub-tárházban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="input"></a>Input (Bemenet)

A Mobile Apps bemeneti kötése tölt be egy olyan rekordot egy mobil table végpont a, és továbbadja azt a függvényt. A C# és F # függvényekben a rekord módosításai rendszer automatikusan küldi vissza a tábla amikor sikeresen kilép, a függvény.

## <a name="input---example"></a>Bemenet – példa

Tekintse meg a nyelvspecifikus példát:

* [C# script (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Bemenet – C# parancsfájl – példa

A következő példa bemutatja a Mobile Apps bemeneti kötés egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy üzenetsor-üzenetet, amely rendelkezik egy rekord azonosítóval váltja ki. A függvény a megadott rekord olvas, és módosítja a `Text` tulajdonság.

Itt az kötés adatai a *function.json* fájlt:

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
],
"disabled": false
}
```
A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

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

A következő példa bemutatja a Mobile Apps bemeneti kötés egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy üzenetsor-üzenetet, amely rendelkezik egy rekord azonosítóval váltja ki. A függvény a megadott rekord olvas, és módosítja a `Text` tulajdonság.

Itt az kötés adatai a *function.json* fájlt:

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
],
"disabled": false
}
```
A [konfigurációs](#input---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Bemenet – attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútum.

Konfigurálható attribútumtulajdonságok kapcsolatos információkért lásd: [a következő konfigurációs szakasz](#input---configuration).

## <a name="input---configuration"></a>Adjon meg - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `MobileTable` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **Típusa**|| "MobileTable" értékre kell állítani|
| **direction**||"A" értékre kell állítani|
| **name**|| Függvényaláíráshoz a bemeneti paraméter neve.|
|**Táblanév** |**TableName**|A mobilalkalmazás adatok tábla neve|
| **id**| **Azonosító** | A beolvasandó bejegyzés azonosítója. Lehetnek statikusak vagy az eseményindító, amely hívja meg a függvény alapján. Például, ha használja a várólista eseményindító a függvény, majd `"id": "{queueTrigger}"` az üzenetsorban lévő üzenetet karakterlánc értékét használja a rekord azonosító beolvasása.|
|**connection**|**Kapcsolat**|A mobilalkalmazás URL-címet tartalmazó alkalmazásbeállítás neve. A funkció URL-címet a mobilalkalmazás elleni szükséges REST-műveletek összeállításához. Hozzon létre egy Alkalmazásbeállítás a függvény alkalmazásban, amelyet a mobilalkalmazás URL-címet tartalmaz, majd adja meg az Alkalmazásbeállítás a nevét a `connection` tulajdonságot a bemeneti kötése. Az URL-cím a következőképpen néz `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|A mobilalkalmazás API-kulcs alkalmazásbeállítás neve. Adja meg az API-kulcs Ha Ön [valósítja meg az API-kulcs a Node.js mobilalkalmazásban](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [valósítja meg az API-kulcs a .NET mobilalkalmazásban](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Adja meg a kulcsot, hozzon létre egy Alkalmazásbeállítás az API-kulcsot tartalmazó függvény alkalmazásban, és vegye fel a `apiKey` tulajdonságot a bemeneti kötése az Alkalmazásbeállítás nevét. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg az API-kulcsot a mobilalkalmazás-ügyfelekkel. Ez csak kell elosztani biztonságosan Szolgáltatásoldali ügyfelek, például az Azure Functions. Az Azure Functions tárolja a kapcsolati adatokat és API-kulcsokat beállításait, hogy nem ellenőrzi azokat a verziókövetési tárházzal. Ez megvédi a bizalmas adatokat.

## <a name="input---usage"></a>Bemenet - használat

A C# függvények, ha a megadott azonosítójú rekord található, átadva azokat a megnevezett [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) paraméter. Ha a rekord nem található, a paraméter értéke `null`. 

A JavaScript-funkcióként, a rekord át a `context.bindings.<name>` objektum. Ha a rekord nem található, a paraméter értéke `null`. 

A C# és F # funkciók, a változtatások a bemeneti rekord (bemeneti paraméter) automatikusan küldött vissza a tábla amikor sikeresen kilép, a függvény. JavaScript-funkcióként rekord nem módosítható.

## <a name="output"></a>Kimenet

A Mobile Apps kimeneti kötése egy új rekordot írni a Mobile Apps tábla használja.  

## <a name="output---example"></a>Kimeneti – példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimeneti - C# – példa

A következő példa egy [C# függvény](functions-dotnet-class-library.md) , amely váltja ki egy üzenetsor-üzenetet, és létrehoz egy rekordot egy mobilalkalmazás táblában.

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

### <a name="output---c-script-example"></a>Kimeneti - C# parancsfájl – példa

A következő példa bemutatja a Mobile Apps kimeneti kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy üzenetsor váltja ki, és létrehoz egy új rekordot kódolt értékkel a `Text` tulajdonság.

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A C# parancsfájl kód itt látható:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Kimeneti - JavaScript – példa

A következő példa bemutatja a Mobile Apps kimeneti kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy üzenetsor váltja ki, és létrehoz egy új rekordot kódolt értékkel a `Text` tulajdonság.

Itt az kötés adatai a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) a szakasz ismerteti ezeket a tulajdonságokat.

A JavaScript-kód itt látható:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Kimeneti - attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútum.

Konfigurálható attribútumtulajdonságok kapcsolatos információkért lásd: [kimeneti - konfigurációs](#output---configuration). Íme egy `MobileTable` metódus-aláírás attribútum példát:

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

Tekintse meg a teljes például [kimeneti - C# példa](#output---c-example).

## <a name="output---configuration"></a>Kimeneti - konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `MobileTable` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **Típusa**|| "MobileTable" értékre kell állítani|
| **direction**||"Ki" értékre kell állítani|
| **name**|| A kimeneti paraméter függvényaláíráshoz a neve.|
|**Táblanév** |**TableName**|A mobilalkalmazás adatok tábla neve|
|**connection**|**MobileAppUriSetting**|A mobilalkalmazás URL-címet tartalmazó alkalmazásbeállítás neve. A funkció URL-címet a mobilalkalmazás elleni szükséges REST-műveletek összeállításához. Hozzon létre egy Alkalmazásbeállítás a függvény alkalmazásban, amelyet a mobilalkalmazás URL-címet tartalmaz, majd adja meg az Alkalmazásbeállítás a nevét a `connection` tulajdonságot a bemeneti kötése. Az URL-cím a következőképpen néz `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|A mobilalkalmazás API-kulcs alkalmazásbeállítás neve. Adja meg az API-kulcs Ha Ön [valósít meg API-kulcs a Node.js mobile Apps-háttéralkalmazás](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [valósít meg API-kulcs a .NET-mobil háttéralkalmazás](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Adja meg a kulcsot, hozzon létre egy Alkalmazásbeállítás az API-kulcsot tartalmazó függvény alkalmazásban, és vegye fel a `apiKey` tulajdonságot a bemeneti kötése az Alkalmazásbeállítás nevét. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg az API-kulcsot a mobilalkalmazás-ügyfelekkel. Ez csak kell elosztani biztonságosan Szolgáltatásoldali ügyfelek, például az Azure Functions. Az Azure Functions tárolja a kapcsolati adatokat és API-kulcsokat beállításait, hogy nem ellenőrzi azokat a verziókövetési tárházzal. Ez megvédi a bizalmas adatokat.

## <a name="output---usage"></a>Kimeneti - használat

A C# parancsfájl funkciók, használja a megnevezett kimeneti paramétert `out object` a kimeneti rekord eléréséhez. A C# osztálykönyvtárakhoz a `MobileTable` attribútum is használható a következő típusok:

* `ICollector<T>` vagy `IAsyncCollector<T>`, ahol `T` vagy `JObject` vagy rendelkező bármilyen olyan `public string Id` tulajdonság.
* `out JObject`
* `out T` vagy `out T[]`, ahol `T` rendelkező bármilyen olyan `public string Id` tulajdonság.

A Node.js funkciók használata `context.bindings.<name>` a kimeneti rekord eléréséhez.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
