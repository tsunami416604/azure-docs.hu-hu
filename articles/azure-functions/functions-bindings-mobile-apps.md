---
title: "Az Azure Mobile Apps funkciók kötések |} Microsoft Docs"
description: "Azure Mobile Apps kötések az Azure Functions használatának megismerése."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: faad1263-0fa5-41a9-964f-aecbc0be706a
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/31/2016
ms.author: glenga
ms.openlocfilehash: d2c0e4e233761584bad2df05a8e702e4fc77e84f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-mobile-apps-bindings"></a>Az Azure Mobile Apps funkciók kötések
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk ismerteti, hogyan lehet konfigurálni és kód [Azure Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) Azure Functions kötések. Azure Functions támogatja bemeneti és kimeneti Mobile Apps kötéseit.

A Mobile Apps bemeneti és kimeneti kötések lehetővé teszik, hogy [az olvasási és írási adattáblák](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations) a mobilalkalmazásban.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="mobile-apps-input-binding"></a>Mobile Apps bemeneti kötése
A Mobile Apps bemeneti kötése tölt be egy olyan rekordot egy mobil table végpont a, és továbbadja azt a függvényt. A C# és F # függvényekben a rekord módosításai rendszer automatikusan küldi vissza a tábla amikor sikeresen kilép, a függvény.

A Mobile Apps bemenete egy olyan függvényt használja a következő JSON-objektum a `bindings` function.json tömbje:

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "id" : "<Id of the record to retrieve - see below>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "in"
}
```

Vegye figyelembe a következőket:

* `id`lehet, hogy statikus, vagy az eseményindító, amely meghívja a függvényt alapulhat. Például, ha egy [várólista eseményindító]() a függvénynél, majd `"id": "{queueTrigger}"` az üzenetsorban lévő üzenetet karakterlánc értékét használja a rekord azonosító beolvasása.
* `connection`a függvény alkalmazásban, viszont a mobilalkalmazás URL-CÍMÉT tartalmazó Alkalmazásbeállítás nevét kell tartalmaznia. A funkció URL-címet a mobilalkalmazás elleni szükséges REST-műveletek összeállításához. Akkor [alkalmazásbeállítás létrehozása az függvény alkalmazásban]() a mobilalkalmazás URL-címet tartalmaz, amely (mely tűnik `http://<appname>.azurewebsites.net`), majd adja meg az Alkalmazásbeállítás a nevét a `connection` tulajdonságot a bemeneti kötése. 
* Meg kell adnia `apiKey` Ha Ön [valósít meg API-kulcs a Node.js mobile Apps-háttéralkalmazás](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [valósít meg API-kulcs a .NET-mobil háttéralkalmazás](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Ehhez meg [alkalmazásbeállítás létrehozása az függvény alkalmazásban]() , amely tartalmazza az API-kulcsot, majd adja hozzá a `apiKey` tulajdonságot a bemeneti kötése az Alkalmazásbeállítás nevét. 
  
  > [!IMPORTANT]
  > Az API-kulcs nem kell osztani a mobilalkalmazás-ügyfelekkel. Ez csak kell elosztani biztonságosan Szolgáltatásoldali ügyfelek, például az Azure Functions. 
  > 
  > [!NOTE]
  > Az Azure Functions tárolja a kapcsolati adatokat és API-kulcsokat beállításait, hogy nem ellenőrzi azokat a verziókövetési tárházzal. Ez megvédi a bizalmas adatokat.
  > 
  > 

<a name="inputusage"></a>

## <a name="input-usage"></a>Bemeneti kihasználtsága
Ez a szakasz bemutatja, hogyan használható a Mobile Apps bemeneti kötése a funkciókódot. 

A megadott tábla és a rekord Azonosítójú rekord található, amikor átadva azokat a megnevezett [JObject](http://www.newtonsoft.com/json/help/html/t_newtonsoft_json_linq_jobject.htm) paraméter (vagy a node.js, akkor átad a a `context.bindings.<name>` objektum). Ha a rekord nem található, a paraméter nem `null`. 

A C# és F # funkciók, a változtatások a bemeneti rekord (bemeneti paraméter) automatikusan küld vissza a Mobile Apps tábla amikor sikeresen kilép, a függvény. A Node.js funkciók használata `context.bindings.<name>` a bemeneti rekord eléréséhez. Node.js rekord nem módosítható.

<a name="inputsample"></a>

## <a name="input-sample"></a>A minta bemeneti
Tegyük fel, amely lekéri a Mobile Apps tábla egyik rekordja, azonosító: a várólista eseményindító üzenet a következő function.json:

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

Tekintse meg a nyelvspecifikus mintát, amely a bemeneti rekord a kötés használja. A C# és F # minták is módosíthatja a rekord `text` tulajdonság.

* [C#](#inputcsharp)
* [Node.js](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>A C# bemeneti minta #

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

<!--
<a name="inputfsharp"></a>
### Input sample in F# ## 

```fsharp
#r "Newtonsoft.Json"    
open Newtonsoft.Json.Linq
let Run(myQueueItem: string, record: JObject) =
  inputDocument?text <- "This has changed."
```
-->

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>A node.js bemeneti minta

```javascript
module.exports = function (context, myQueueItem) {    
    context.log(context.bindings.record);
    context.done();
};
```

<a name="output"></a>

## <a name="mobile-apps-output-binding"></a>Mobile Apps kimeneti kötése
Használja a Mobile Apps kimenete egy új rekordot írni a Mobile Apps tábla végpont kötése.  

A Mobile Apps a függvényt használja a következő JSON-objektumot a kimeneti a `bindings` function.json tömbje:

```json
{
    "name": "<Name of output parameter in function signature>",
    "type": "mobileTable",
    "tableName": "<Name of your mobile app's data table>",
    "connection": "<Name of app setting that has your mobile app's URL - see below>",
    "apiKey": "<Name of app setting that has your mobile app's API key - see below>",
    "direction": "out"
}
```

Vegye figyelembe a következőket:

* `connection`a függvény alkalmazásban, viszont a mobilalkalmazás URL-CÍMÉT tartalmazó Alkalmazásbeállítás nevét kell tartalmaznia. A funkció URL-címet a mobilalkalmazás elleni szükséges REST-műveletek összeállításához. Akkor [alkalmazásbeállítás létrehozása az függvény alkalmazásban]() a mobilalkalmazás URL-címet tartalmaz, amely (mely tűnik `http://<appname>.azurewebsites.net`), majd adja meg az Alkalmazásbeállítás a nevét a `connection` tulajdonságot a bemeneti kötése. 
* Meg kell adnia `apiKey` Ha Ön [valósít meg API-kulcs a Node.js mobile Apps-háttéralkalmazás](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key), vagy [valósít meg API-kulcs a .NET-mobil háttéralkalmazás](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key). Ehhez meg [alkalmazásbeállítás létrehozása az függvény alkalmazásban]() , amely tartalmazza az API-kulcsot, majd adja hozzá a `apiKey` tulajdonságot a bemeneti kötése az Alkalmazásbeállítás nevét. 
  
  > [!IMPORTANT]
  > Az API-kulcs nem kell osztani a mobilalkalmazás-ügyfelekkel. Ez csak kell elosztani biztonságosan Szolgáltatásoldali ügyfelek, például az Azure Functions. 
  > 
  > [!NOTE]
  > Az Azure Functions tárolja a kapcsolati adatokat és API-kulcsokat beállításait, hogy nem ellenőrzi azokat a verziókövetési tárházzal. Ez megvédi a bizalmas adatokat.
  > 
  > 

<a name="outputusage"></a>

## <a name="output-usage"></a>Kimeneti használata
Ez a szakasz bemutatja, hogyan használhatja a Mobile Apps kimeneti kötelező a funkciókódot. 

A C# funkciók, használja a megnevezett kimeneti paramétert `out object` a kimeneti rekord eléréséhez. A Node.js funkciók használata `context.bindings.<name>` a kimeneti rekord eléréséhez.

<a name="outputsample"></a>

## <a name="output-sample"></a>Minta kimenet
Tegyük fel, a következő function.json, amely meghatározza a várólista eseményindító és a Mobile Apps kimeneti:

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

Tekintse meg a nyelvspecifikus mintát, amely létrehoz egy rekordot a Mobile Apps tábla végpont az üzenetsorban lévő üzenetet tartalmát.

* [C#](#outcsharp)
* [Node.js](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>A C# kimeneti minta #

```cs
public static void Run(string myQueueItem, out object record)
{
    record = new {
        Text = $"I'm running in a C# function! {myQueueItem}"
    };
}
```

<!--
<a name="outfsharp"></a>
### Output sample in F# ## 
```fsharp

```
-->
<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Kimeneti minta node.js

```javascript
module.exports = function (context, myQueueItem) {

    context.bindings.record = {
        text : "I'm running in a Node function! Data: '" + myQueueItem + "'"
    }   

    context.done();
};
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

