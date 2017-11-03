---
title: "Az Azure Functions időzítő indítófeltételt |} Microsoft Docs"
description: "Időzítő eseményindítók használata az Azure Functions ismertetése."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 12beb090a95a31c7e83ae03a920016bdfbf474e3
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="azure-functions-timer-trigger"></a>Az Azure Functions időzítő indítófeltételt

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Ez a cikk azt ismerteti, konfigurálása és az Azure Functions kód időzítő eseményindítók. Az Azure Functions lehetővé teszi, hogy a kódra a függvény a meghatározott ütemezés szerint időzítő eseményindító-kötéssel rendelkezik. 

Az időzítő indítófeltételt többpéldányos kibővített támogatja. Egy adott időzítő egyetlen példányán fut minden példányára.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>Időzítő eseményindító
Az időzítő indítófeltételt egy függvénynek, használja a következő JSON-objektum a `bindings` function.json tömbje:

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

Értékének `schedule` van egy [CRON-kifejezés](http://en.wikipedia.org/wiki/Cron#CRON_expression) , amely tartalmazza a hat mezők: 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>A cron-kifejezés talál online számos hagyja ki ezt a `{second}` mező. Másolása egyik, ha úgy, hogy az extra kell `{second}` mező. Adott, tekintse meg a [példák ütemezése](#examples) alatt.

Az alapértelmezett időzónát együtt a CRON-kifejezés az egyezményes világidő (UTC). A CRON-kifejezés alapján másik időzónában van, hozzon létre egy új alkalmazásbeállítást nevű függvény alkalmazás `WEBSITE_TIME_ZONE`. Adja meg a értéket a kívánt időzóna neve látható módon a [Microsoft időzóna Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Például *keleti téli idő* az UTC-05:00. Szeretné, hogy a időzítő tűz indítás: 10:00 de minden nap, használja a következő CRON-kifejezés, amely az UTC időzóna:

```json
"schedule": "0 0 15 * * *",
``` 

Azt is megteheti, hozzáadhatja egy új alkalmazásbeállítást a függvény nevű alkalmazást `WEBSITE_TIME_ZONE` és állítsa be az értéket **keleti téli idő**.  Ezután a következő CRON-kifejezés 10:00 de használható: 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>Ütemezés példák
Az alábbiakban néhány CRON kifejezésekre is használhatja a mintákat a `schedule` tulajdonság. 

5 percenként egyszer indításához:

```json
"schedule": "0 */5 * * * *"
```

Az Indítás egyszer minden órában tetején:

```json
"schedule": "0 0 * * * *",
```

Két óránként indításához:

```json
"schedule": "0 0 */2 * * *",
```

Óránként egyszer a Reggel 9 délután 5 óra történő indításához:

```json
"schedule": "0 0 9-17 * * *",
```

Indításához: 9:30 AM minden nap:

```json
"schedule": "0 30 9 * * *",
```

Indításához: 9:30 AM minden hétköznap:

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>Eseményindító kihasználtsága
Egy időzítő funkció meghívásakor a [objektum](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) átad a funkciót a. A következő JSON-ja az objektum egy példa ábrázolását. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>Eseményindító minta
Tegyük fel, hogy rendelkezik a következő időzítő indítófeltételt a `bindings` function.json tömbje:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Tekintse meg a nyelvspecifikus mintát, hogy az olvasások az objektum, hogy késői fut-e.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.js](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>A C# eseményindító minta #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>Az F # eseményindító minta #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>A node.js eseményindító minta
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>Következő lépések
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

