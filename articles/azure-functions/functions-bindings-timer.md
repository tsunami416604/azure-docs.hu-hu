---
title: "Az Azure Functions időzítő indítófeltételt"
description: "Időzítő eseményindítók használata az Azure Functions ismertetése."
services: functions
documentationcenter: na
author: tdykstra
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
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: bd1a2643d9faf65d664c786169c38f01767fb7e5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="timer-trigger-for-azure-functions"></a>Az Azure Functions időzítő indítófeltételt 

Ez a cikk ismerteti, hogyan használható az Azure Functions időzítő eseményindítók. Egy időzítő indítófeltételt lehetővé teszi egy függvény fussanak ütemezés szerint. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages"></a>Csomagok

Az időzítő indítófeltételt megtalálható a [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-csomagot. A csomag forráskódja van a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-tárházban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Példa

Tekintse meg a nyelvspecifikus példát:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# – példa

Az alábbi példa mutatja egy [C# függvény](functions-dotnet-class-library.md) futó, ötpercenként:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# parancsfájl – példa

A következő példa bemutatja egy időzítő indítófeltételt kötelező egy *function.json* fájlt és egy [C# parancsfájl függvény](functions-reference-csharp.md) , amely a kötés használja. A függvény jelző a függvény hívása miatt a kihagyott ütemezés előfordulási a napló írása.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

A C# parancsfájl kód itt látható:

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

### <a name="f-example"></a>F # – példa

A következő példa bemutatja egy időzítő indítófeltételt kötelező egy *function.json* fájlt és egy [F # parancsfájl függvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény jelző a függvény hívása miatt a kihagyott ütemezés előfordulási a napló írása.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

A F # parancsfájl kód itt látható:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript – példa

A következő példa bemutatja egy időzítő indítófeltételt kötelező egy *function.json* fájlt és egy [JavaScript függvény](functions-reference-node.md) , amely a kötés használja. A függvény jelző a függvény hívása miatt a kihagyott ütemezés előfordulási a napló írása.

Itt az kötés adatai a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

A JavaScript parancsfájl kód itt látható:

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

## <a name="attributes"></a>Attribútumok

A [C# osztálykönyvtárakhoz](functions-dotnet-class-library.md), használja a [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Az attribútumok konstruktorában CRON-kifejezés, hajtja végre a következő példában látható módon:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

Megadhat egy `TimeSpan` egy CRON-kifejezés, ha a függvény-alkalmazás az App Service-csomag (nem a fogyasztás terv) fut. helyett.

Tekintse meg a teljes például [C# példa](#c-example).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `TimerTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**Típusa** | n/a | "TimerTrigger" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**direction** | n/a | "A" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** | n/a | A függvény a kódban időzítő az objektumot határozza meg a változó neve. | 
|**schedule**|**ScheduleExpression**|A felhasználási terv ütemezés a CRON-kifejezés lehet definiálni. Ha használ egy App Service-csomag, használhatja a `TimeSpan` karakterlánc. Az alábbi szakaszok ismertetik a CRON-kifejezést. Az ütemezés kifejezés be Alkalmazásbeállítás, és csomagolni értékre állítani ezt a tulajdonságot  **%**  jelentkezik, például: "% NameOfAppSettingWithCRONExpression %". |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>CRON formátumban 

A [CRON-kifejezés](http://en.wikipedia.org/wiki/Cron#CRON_expression) az Azure Functions időzítő eseményindító magában foglalja a hat mezők: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>A CRON-kifejezés talál online számos hagyja ki ezt a `{second}` mező. Ha másolása egyik, adja hozzá a hiányzó `{second}` mező.

### <a name="cron-time-zones"></a>CRON időzónák

Az alapértelmezett időzónát együtt a CRON-kifejezés az egyezményes világidő (UTC). A CRON-kifejezés alapján másik időzónában van, hozzon létre egy új alkalmazásbeállítást nevű függvény alkalmazás `WEBSITE_TIME_ZONE`. Adja meg a értéket a kívánt időzóna neve látható módon a [Microsoft időzóna Index](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Például *keleti téli idő* az UTC-05:00. Szeretné, hogy a időzítő tűz indítás: 10:00 de minden nap, használja a következő CRON-kifejezés, amely az UTC időzóna:

```json
"schedule": "0 0 15 * * *",
``` 

Azt is megteheti, hozzáadhatja egy új alkalmazásbeállítást a függvény nevű alkalmazást `WEBSITE_TIME_ZONE` és állítsa be az értéket **keleti téli idő**.  Ezután a következő CRON-kifejezés 10:00 de használható: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>CRON példák

Az alábbiakban néhány olyan CRON kifejezéseket is használhat az Azure Functions időzítő eseményindítót. 

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

## <a name="usage"></a>Használat

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

## <a name="scale-out"></a>Kiterjesztés

Az időzítő indítófeltételt többpéldányos kibővített támogatja. Egy adott időzítő egyetlen példányán fut minden példányára.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ugrás a egy időzítő indítófeltételt használó gyors üzembe helyezés](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
