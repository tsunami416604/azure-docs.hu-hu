---
title: Mobile Apps-kötések az Azure Functions szolgáltatáshoz
description: Megtudhatja, hogyan használhatja az Azure Mobile Apps-kötések az Azure Functions szolgáltatásban.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: d43032f854aa37f150945c25515c03ec97277b41
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44091305"
---
# <a name="mobile-apps-bindings-for-azure-functions"></a>Mobile Apps-kötések az Azure Functions szolgáltatáshoz 

Ez a cikk azt ismerteti, hogyan használható a [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) kötések az Azure Functions szolgáltatásban. Az Azure Functions támogatja a bemeneti és kimeneti kötések Mobile Apps-alkalmazáshoz.

A Mobile Apps-kötések lehetővé teszik, hogy olvassa és frissítse a mobilalkalmazásokban adattáblák.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Mobile Apps-kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet-csomag verziója 1.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.MobileApps/) GitHub-adattárban.

[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

Mobile Apps-kötések szerepelnek a [Microsoft.Azure.WebJobs.Extensions.MobileApps](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MobileApps) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/) GitHub-adattárban.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="input"></a>Input (Bemenet)

A Mobile Apps bemeneti kötést egy rekord tölt be egy mobil table-végpont, és továbbadja azt a függvényt. C# és az F #-függvények a rekord végzett módosítások automatikusan érkeznek vissza a táblából, a függvény sikeresen kilép.

## <a name="input---example"></a>Adjon meg – példa

Tekintse meg az adott nyelvű példa:

* [C# script (.csx)](#input---c-script-example)
* [JavaScript](#input---javascript-example)

### <a name="input---c-script-example"></a>Bemenet – C#-szkript példa

Az alábbi példa bemutatja a Mobile Apps bemeneti kötés egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. Egy rekord azonosítóval rendelkező üzenetsori üzenet által aktivált függvény. A függvény a megadott rekordot olvas, és módosítja a `Text` tulajdonság.

Itt van a kötési adatait a *function.json* fájlt:

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
A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

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

Az alábbi példa bemutatja a Mobile Apps bemeneti kötés egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. Egy rekord azonosítóval rendelkező üzenetsori üzenet által aktivált függvény. A függvény a megadott rekordot olvas, és módosítja a `Text` tulajdonság.

Itt van a kötési adatait a *function.json* fájlt:

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
A [konfigurációs](#input---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

## <a name="input---attributes"></a>Bemenet - attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútum.

Konfigurálható attribútum-tulajdonságokkal kapcsolatos információkért lásd: [az alábbi konfigurációs szakaszban](#input---configuration).

## <a name="input---configuration"></a>Bemenet - konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `MobileTable` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type**|| "MobileTable" értékre kell állítani|
| **direction**||"A" értékre kell állítani|
| **name**|| Podpis funkce a bemeneti paraméter neve.|
|**Táblanév** |**Táblanév**|A mobilalkalmazás adatok tábla neve|
| **id**| **Azonosító** | A beolvasandó rekord azonosítója. Lehet statikus vagy az eseményindító, amely meghívja a függvényt alapján. Például, ha használja egy üzenetsor eseményindító a függvény ezután `"id": "{queueTrigger}"` az üzenetsorban található üzenet karakterlánc értékét használja, mint a Rekordazonosító lekéréséhez.|
|**kapcsolat**|**kapcsolat**|A mobilalkalmazás URL-CÍMÉT tartalmazó alkalmazásbeállítás neve. A függvény létrehozásához szükséges REST-műveleteinek a mobilalkalmazás szemben az URL-címet használja. Hozzon létre egy alkalmazásbeállításhoz a függvényalkalmazásban, amely tartalmazza a mobilalkalmazás URL-címet, majd adja meg az Alkalmazásbeállítás nevét a `connection` a bemeneti kötésnek a tulajdonságot. Az URL-címe a következőhöz hasonló `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKey**|A mobilalkalmazás API-kulcsot tartalmazó alkalmazásbeállítás neve. Adja meg a fő API-t, ha Ön [API-kulcs megvalósítása a Node.js-mobilalkalmazásban](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [API-kulcs megvalósítása a .NET-mobilalkalmazásban](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Adja meg a kulcsot, hozzon létre egy alkalmazásbeállításhoz a függvényalkalmazásban, amely tartalmazza az API-kulcsot, majd adja hozzá a `apiKey` tulajdonság frissítése az Alkalmazásbeállítás nevét a bemeneti kötést. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg API-t a mobilalkalmazás-ügyfelekkel. Ez csak terjesztése biztonságosan a Szolgáltatásoldali ügyfelek, például az Azure Functions. Az Azure Functions tárolja a kapcsolati adatokat és az API-kulcsok alkalmazásbeállításokként, hogy azok nincs bejelölve, a verziókövetési tárházzal. Ez megvédi a bizalmas információk ellopására.

## <a name="input---usage"></a>Bemenet - használat

A C#-függvények, ha a rekord a megadott Azonosítóval rendelkező talál, átadva az elnevezett be [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) paraméter. Ha a rekord nem található, a paraméter értéke `null`. 

A JavaScript-függvények, a rekord átad a `context.bindings.<name>` objektum. Ha a rekord nem található, a paraméter értéke `null`. 

A C# és az F # függvény, a változtatások a bemeneti, ha a függvény sikeresen kilép a rendszer automatikusan elküldje rekordot (a bemeneti paraméter) térjen vissza a táblából. Egy rekordot a JavaScript-függvények nem módosítható.

## <a name="output"></a>Kimenet

A Mobile Apps kimeneti kötés egy új rekordot írni egy Mobile Apps-tábla használja.  

## <a name="output---example"></a>Kimenete – példa

Tekintse meg az adott nyelvű példa:

* [C#](#output---c-example)
* [C# script (.csx)](#output---c-script-example)
* [JavaScript](#output---javascript-example)

### <a name="output---c-example"></a>Kimenet – C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) , amely egy üzenetsor által aktivált, és létrehoz egy rekordot egy mobilalkalmazás-táblában.

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

### <a name="output---c-script-example"></a>Kimenet – C#-szkript példa

Az alábbi példa bemutatja egy kötelező a Mobile Apps-kimenet egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény egy üzenetsor által aktivált, és új rekordot hoz létre változtatható értékét a `Text` tulajdonság.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

Íme a C#-szkriptkódot:

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

### <a name="output---javascript-example"></a>Kimenet – JavaScript-példa

Az alábbi példa bemutatja egy kötelező a Mobile Apps-kimenet egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény egy üzenetsor által aktivált, és új rekordot hoz létre változtatható értékét a `Text` tulajdonság.

Itt van a kötési adatait a *function.json* fájlt:

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

A [konfigurációs](#output---configuration) szakasz mutatja be ezeket a tulajdonságokat.

A következő JavaScript-kódot:

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="output---attributes"></a>Kimenet – attribútumok

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [MobileTable](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) attribútum.

Konfigurálható attribútum-tulajdonságokkal kapcsolatos információkért lásd: [kimenete – konfigurációs](#output---configuration). Íme egy `MobileTable` attribútum példa egy podpis metody:

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

Egy teljes példa: [kimenet – C#-példa](#output---c-example).

## <a name="output---configuration"></a>Kimenete – konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `MobileTable` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
| **type**|| "MobileTable" értékre kell állítani|
| **direction**||"Ki" értékre kell állítani|
| **name**|| Podpis funkce a kimeneti paraméter neve.|
|**Táblanév** |**Táblanév**|A mobilalkalmazás adatok tábla neve|
|**kapcsolat**|**MobileAppUriSetting**|A mobilalkalmazás URL-CÍMÉT tartalmazó alkalmazásbeállítás neve. A függvény létrehozásához szükséges REST-műveleteinek a mobilalkalmazás szemben az URL-címet használja. Hozzon létre egy alkalmazásbeállításhoz a függvényalkalmazásban, amely tartalmazza a mobilalkalmazás URL-címet, majd adja meg az Alkalmazásbeállítás nevét a `connection` a bemeneti kötésnek a tulajdonságot. Az URL-címe a következőhöz hasonló `http://<appname>.azurewebsites.net`.
|**apiKey**|**ApiKeySetting**|A mobilalkalmazás API-kulcsot tartalmazó alkalmazásbeállítás neve. Adja meg a fő API-t, ha Ön [API-kulcs megvalósítása a Node.js-mobilalkalmazások számára az](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [API-kulcs megvalósítása a .NET mobile Apps-háttéralkalmazást](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Adja meg a kulcsot, hozzon létre egy alkalmazásbeállításhoz a függvényalkalmazásban, amely tartalmazza az API-kulcsot, majd adja hozzá a `apiKey` tulajdonság frissítése az Alkalmazásbeállítás nevét a bemeneti kötést. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> Ne ossza meg API-t a mobilalkalmazás-ügyfelekkel. Ez csak terjesztése biztonságosan a Szolgáltatásoldali ügyfelek, például az Azure Functions. Az Azure Functions tárolja a kapcsolati adatokat és az API-kulcsok alkalmazásbeállításokként, hogy azok nincs bejelölve, a verziókövetési tárházzal. Ez megvédi a bizalmas információk ellopására.

## <a name="output---usage"></a>Kimenet – használat

A parancsfájl C#-függvények, egy névvel ellátott kimeneti paraméter típusa használja `out object` elérni a kimeneti rekordot. A C#-osztálykódtárakat a `MobileTable` attribútum is használható a következő típusok:

* `ICollector<T>` vagy `IAsyncCollector<T>`, ahol `T` vagy `JObject` bármely típusú, vagy egy `public string Id` tulajdonság.
* `out JObject`
* `out T` vagy `out T[]`, ahol `T` rendelkező bármilyen olyan `public string Id` tulajdonság.

A Node.js-függvények, használjon `context.bindings.<name>` elérni a kimeneti rekordot.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
