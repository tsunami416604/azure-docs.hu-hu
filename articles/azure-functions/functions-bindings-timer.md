---
title: Az Azure Functions időzítő indítófeltételt
description: Időzítő eseményindítók használata az Azure Functions ismertetése.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, Funkciók, Eseményfeldolgozási, dinamikus számítási kiszolgáló nélküli architektúrája
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: 2bc2559dc1cf737e018895ffae61d0da0e56fc85
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
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
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
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

Az attribútumok konstruktorában CRON-kifejezés vesz igénybe, vagy egy `TimeSpan`. Használhat `TimeSpan` csak akkor, ha a függvény az alkalmazás fut. az App Service-csomag. A következő példa bemutatja egy CRON-kifejezés:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    if (myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat ismerteti a beállított kötés konfigurációs tulajdonságok a *function.json* fájl és a `TimerTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "TimerTrigger" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon.|
|**direction** | n/a | "A" értékre kell állítani. Ez a tulajdonság rendszer automatikusan beállítja az eseményindítót hoz létre az Azure portálon. |
|**name** | n/a | A függvény a kódban időzítő az objektumot határozza meg a változó neve. | 
|**schedule**|**ScheduleExpression**|A [CRON-kifejezés](#cron-expressions) vagy egy [TimeSpan](#timespan) érték. A `TimeSpan` csak egy függvény alkalmazást az App Service-csomag futó használható. Az ütemezés kifejezés be Alkalmazásbeállítás, és állítsa be ezt a tulajdonságot a beállítás neve a becsomagolt alkalmazás **%** jelentkezik, például: "% ScheduleAppSetting %". |
|**runOnStartup**|**RunOnStartup**|Ha `true`, a függvény meghívták a futtatókörnyezet indításakor. Például a futtatókörnyezet kezdődik, amikor a függvény app felébred üresjárati tétlenség miatt váltás után. Ha a függvény alkalmazás-újraindítások függvény-módosítások miatt, ha a függvény app méretezi ki. Ezért **runOnStartup** ritkán Ha valaha is meg `true`, mert a teszi, hogy kód magas előre nem látható időpontokban hajtható végre.|
|**useMonitor**|**UseMonitor**|Beállítása `true` vagy `false` annak jelzésére, hogy az ütemezés kell figyelni. Figyelési ütemezés fenntartása ütemezés előfordulási biztosítása az ütemezés karban kell akkor is, ha a függvény app példányok indítsa újra a segítése érdekében. Ha nem állítja be, az alapértelmezett érték `true` az ütemezést, amelyekben egy nagyobb, mint 1 perces ismétlődési időköz. Percenként egynél többször kiváltó ütemezéseket, az alapértelmezett érték `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Egy időzítő funkció meghívásakor a [objektum](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) átad a funkciót a. A következő JSON-ja az objektum egy példa ábrázolását. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00+00:00",
        "LastUpdated":"2016-10-04T10:16:00+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

A `IsPastDue` tulajdonság `true` későbbi, mint az ütemezett aktuális függvény hívása esetén. Újra kell indítani a függvény alkalmazást okozhatja például lehet kihagyni egy híváshoz.

## <a name="cron-expressions"></a>CRON-kifejezés 

A CRON-kifejezés az Azure Functions időzítő eseményindító hat mezőket tartalmazza: 

`{second} {minute} {hour} {day} {month} {day-of-week}`

Minden mező a következő típusú értékek egyike lehet:

|Típus  |Példa  |Elindítása  |
|---------|---------|---------|
|Egy adott érték |<nobr>"0 5 * * * *"</nobr>|hh:05:00, ahol az ÓÓ az óránként (óránként):|
|Összes értéket (`*`)|<nobr>"0 * 5 * * *"</nobr>|5:mm:: 00 naponta, ahol az mm percenként az óra (60 naponta alkalommal)|
|A tartomány (`-` operátor)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05, valamint hh:mm:06 és hh:mm:07 óó: pp esetén minden perce minden órában (3-szor perc)|  
|Az értékek egy halmazát (`,` operátor)|<nobr>"5,8,10 * * * * *"</nobr>|hh:mm:05, valamint hh:mm:08 és hh:mm:10 óó: pp esetén minden perce minden órában (3-szor perc)|
|Az intervallum értéke (`/` operátor)|<nobr>"0 */5 * * * *"</nobr>|hh:05:00, hh:10:00, hh:15:00, és így tovább keresztül hh:55:00, ahol az ÓÓ az óránként (12-szer egy óra)|

### <a name="cron-examples"></a>CRON példák

Az alábbiakban néhány olyan CRON kifejezéseket is használhat az Azure Functions időzítő eseményindítót.

|Példa|Elindítása  |
|---------|---------|
|"0 */5 * * * *"|5 percenként egyszer|
|"0 0 * * * *"|egyszer minden órában tetején|
|"0 0 */2 * * *"|két óránként|
|"0 0 9-17 * * *"|óránként egyszer a Reggel 9 a délután 5 óra|
|"0 30 9 * * *"|9:30 AM minden nap:|
|"0 30 9 * * 1-5"|9:30 AM minden hétköznap:|

>[!NOTE]   
>Online CRON-példák találhatók, de ezek hagyja ki ezt a `{second}` mező. Ha másolása egyik, adja hozzá a hiányzó `{second}` mező. Általában érdemes a mezőben egy csillag nem nulla.

### <a name="cron-time-zones"></a>CRON időzónák

A számok CRON-kifejezés egy napon és időpontban, nem az időtartama hivatkozik. Például egy 5 a `hour` mező 5:00-kor, nem minden 5 óra hivatkozik.

Az alapértelmezett időzónát együtt a CRON-kifejezés az egyezményes világidő (UTC). A CRON-kifejezés alapján másik időzónában van, hozzon létre az függvény alkalmazás nevű Alkalmazásbeállítás `WEBSITE_TIME_ZONE`. Adja meg a értéket a kívánt időzóna neve látható módon a [Microsoft időzóna Index](https://technet.microsoft.com/library/cc749073). 

Például *keleti téli idő* az UTC-05:00. Szeretné, hogy a időzítő tűz indítás: 10:00 de minden nap, használja a következő CRON-kifejezés, amely az UTC időzóna:

```json
"schedule": "0 0 15 * * *",
``` 

Vagy hozzon létre az függvény alkalmazás nevű Alkalmazásbeállítás `WEBSITE_TIME_ZONE` és állítsa be az értéket **keleti téli idő**.  Akkor használja a következő CRON-kifejezés: 

```json
"schedule": "0 0 10 * * *",
``` 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` csak egy függvény alkalmazást az App Service-csomag futó használható.

CRON-kifejezés, ellentétben a `TimeSpan` érték meghatározza, hogy a minden függvény meghívása között. Egy függvény futtatása hosszabb, mint a megadott időszak után befejezése után a időzítő azonnal hív meg, a függvény újra.

Egy karakterlánc kifejezett a `TimeSpan` formátuma `hh:mm:ss` amikor `hh` : kisebb, mint 24. Ha az első két szám 24 vagy nagyobb, a formátuma `dd:hh:mm`. Néhány példa:

|Példa |Elindítása  |
|---------|---------|
|"01:00:00" | Minden órában        |
|"00:01:00"|percenként         |
|"24:00:00" | 24 naponta        |

## <a name="scale-out"></a>Kiterjesztés

Egy függvény alkalmazást több példányára méretezze át, ha egy időzítő-eseményindítóval aktivált függvény csak egyetlen példányán fut minden példányára.

## <a name="function-apps-sharing-storage"></a>Függvény alkalmazások megosztása

Ha több függvény alkalmazások között megosztott tárfiókot, győződjön meg arról, hogy minden függvény alkalmazás rendelkezik egy másik `id` a *host.json*. Akkor kihagyhatja a `id` tulajdonság, vagy manuálisan állítsa be az egyes függvény alkalmazások `id` más értékre. Az időzítő indítófeltételt tárolási zárolást használatával gondoskodjon arról, hogy csak egy időzítő példány amikor egy függvény app méretezi ki több példányára. Ha két függvény alkalmazások azonos `id` és egy időzítő indítófeltételt használó minden egyes, csak egy időzítő fog futni.

## <a name="retry-behavior"></a>Újrapróbálási viselkedés

Várólista eseményindító eltérően az időzítő indítófeltételt nem után próbálja megismételni a parancs nem működik. Ha nem sikerül egy olyan függvényt, azt is't nevű újra, amíg az ütemezés.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ugrás a egy időzítő indítófeltételt használó gyors üzembe helyezés](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [További tudnivalók az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
