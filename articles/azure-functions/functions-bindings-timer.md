---
title: Az Azure Functions időzítő eseményindító
description: Az Azure Functions időzített eseményindítók használatának elsajátítását.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: ''
ms.openlocfilehash: eead96a2db9d0038ab27de747f2d9f486a7284a5
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284289"
---
# <a name="timer-trigger-for-azure-functions"></a>Az Azure Functions időzítő eseményindító 

Ez a cikk azt ismerteti, hogyan használható az Azure Functions időzített eseményindítók a. Időzítő eseményindító függvény futtatása ütemezés szerint teszi lehetővé. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Az időzítő eseményindító van megadva a [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-csomag verziója 2.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

Az időzítő eseményindító van megadva a [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet-csomag verziója 3.x. A csomag forráskódja a [azure-webjobs-sdk-bővítmények](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-adattárban.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Példa

Tekintse meg az adott nyelvű példa:

* [C#](#trigger---c-example)
* [C# script (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C#-példa

A következő példa bemutatja egy [C#-függvény](functions-dotnet-class-library.md) öt percenként futó:

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

### <a name="c-script-example"></a>C#-szkript példa

Az alábbi példa bemutatja egy időzítő indítófeltételt kötelező egy *function.json* fájl és a egy [C#-szkriptfüggvény](functions-reference-csharp.md) , amely a kötés használja. A függvény a napló-e a függvény meghívási okozza-e a kihagyott ütemezés előfordulási számainak jelző ír.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Íme a C#-szkriptkódot:

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

### <a name="f-example"></a>F #-példa

Az alábbi példa bemutatja egy időzítő indítófeltételt kötelező egy *function.json* fájl és a egy [F #-szkriptfüggvény](functions-reference-fsharp.md) , amely a kötés használja. A függvény a napló-e a függvény meghívási okozza-e a kihagyott ütemezés előfordulási számainak jelző ír.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Az F #-szkriptkódot itt látható:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript-példa

Az alábbi példa bemutatja egy időzítő indítófeltételt kötelező egy *function.json* fájl és a egy [JavaScript-függvény](functions-reference-node.md) , amely a kötés használja. A függvény a napló-e a függvény meghívási okozza-e a kihagyott ütemezés előfordulási számainak jelző ír.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Íme a szkript JavaScript-kódot:

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

A [C#-osztálykódtárakat](functions-dotnet-class-library.md), használja a [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Az attribútum konstruktorának paramétereként egy CRON-kifejezést vagy egy `TimeSpan`. Használhat `TimeSpan` csak akkor, ha a függvény alkalmazás fut, az App Service-csomag. Az alábbi példa bemutatja egy CRON-kifejezést:

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

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `TimerTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | Állítsa "timerTrigger". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell "a". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | A függvénykódot az időzítő objektumot képviselő változó neve. | 
|**schedule**|**ScheduleExpression**|A [CRON-kifejezés](#cron-expressions) vagy egy [TimeSpan](#timespan) értéket. A `TimeSpan` csak egy függvényalkalmazást, amely egy App Service-csomag futtat használható. Az ütemezés kifejezés helyezni egy alkalmazásbeállításhoz, és ezzel a tulajdonsággal, a beállítás neve a beburkolt alkalmazás **%** jelentkezik, mint ebben a példában: "% ScheduleAppSetting %". |
|**runOnStartup**|**RunOnStartup**|Ha `true`, a függvény meghívása a futtatókörnyezet indításakor. A modul például akkor kezdődik, amikor a függvényalkalmazás felébred végeztével üresjárati inaktivitás miatt. Amikor a függvényalkalmazás újraindítja a függvény változtatások miatt, és amikor a függvényalkalmazás elvégzi a horizontális felskálázást. Ezért **runOnStartup** ritkán, ha minden eddiginél meg `true`szerint fog létrehozni, akkor kód magas kiszámíthatatlan időpontokban hajtható végre.|
|**useMonitor**|**UseMonitor**|Állítsa be `true` vagy `false` jelzi, hogy az ütemezés kell figyelni a. Figyelési ütemezés továbbra is fennáll, ezzel elősegítve annak biztosítása, az ütemezés megfelelően, kezelik, akkor is, ha a függvény alkalmazáspéldány indítsa újra az ütemezés előfordulási. Ha nincs explicit módon beállítva, az alapértelmezett érték `true` , amelyek rendelkeznek a nagyobb, mint 1 perces ismétlődési időköz ütemezések esetében. Percenként egynél többször kiváltó ütemezések esetében az alapértelmezett érték `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

Amikor egy időzítő által aktivált függvény meghívása a [időzítő objektum](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) átad a függvény. A következő JSON-példa reprezentációját az időzítő objektum. 

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

A `IsPastDue` tulajdonság `true` újabb, mint az ütemezett az aktuális függvény meghívási esetén. Például egy függvény alkalmazás újraindítása nem észlelnének. a hívás okozhatja.

## <a name="cron-expressions"></a>CRON-kifejezések 

Azure Functions az a [NCronTab](https://github.com/atifaziz/NCrontab) könyvtár értelmezése CRON-kifejezéseket. A CRON-kifejezés hat mezőt tartalmazza:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Minden mező a következő típusú értékek egyike lehet:

|Típus  |Példa  |Adatvezérelt  |
|---------|---------|---------|
|Egy adott érték |<nobr>"0 5 * * * *"</nobr>|hh:05:00, ahol az ÓÓ az óránként (óránként):|
|Minden érték (`*`)|<nobr>"0 * 5 * * *"</nobr>|címen 5:mm: 00 minden nap, ahol az mm percenként az óra (60 naponta alkalommal)|
|A tartomány (`-` operátor)|<nobr>"5-7 * * * * *"</nobr>|hh:mm:05, valamint hh:mm:06 és hh:mm:07 óó: pp esetén percenként, óránként (3-szor perc)|  
|Értékek egy halmazát (`,` operátor)|<nobr>"5,8,10 x *"</nobr>|hh:mm:05, valamint hh:mm:08 és hh:mm:10 óó: pp esetén percenként, óránként (3-szor perc)|
|Az intervallum értéke (`/` operátor)|<nobr>"0 */5 * * * *"</nobr>|hh:05:00, hh:10:00, hh:15:00, és így tovább – hh:55:00, ahol az ÓÓ az minden óra (12-szer egy óra)|

Adja meg a hónap és nap számértékeket, nevek vagy rövidítések neveket is használhatja:

* Napi numerikus értékek 0 és 6 ahol 0 a vasárnap karakterlánccal kezdődik.
* Nevek angolul jelennek meg. Például: `Monday`, `January`.
* Nevek és nagybetűk nincsenek megkülönböztetve.
* Nevek rövidíthető. Három betű alapvetően ajánlott kifejezés rövidítését jelöli.  Például: `Mon`, `Jan`. 

### <a name="cron-examples"></a>CRON-példák

Az alábbiakban néhány példa a CRON-kifejezéseket is használhat az Azure Functions időzítő eseményindító.

|Példa|Adatvezérelt  |
|---------|---------|
|`"0 */5 * * * *"`|öt percenként|
|`"0 0 * * * *"`|egyszer minden órában tetején|
|`"0 0 */2 * * *"`|két óránként egyszer|
|`"0 0 9-17 * * *"`|óránként egyszer a 9-kor, 17: 00|
|`"0 30 9 * * *"`|9-kor: 30 nap|
|`"0 30 9 * * 1-5"`|a 9:30-kor minden hétköznap|
|`"0 30 9 * Jan Mon"`|a 9:30-kor januárban minden hétfőn|
>[!NOTE]   
>CRON kifejezés online példákat talál, de ezek közül számos hagyja ki a `{second}` mező. Ha az egyik másolja, adja hozzá a hiányzó `{second}` mező. Általában érdemes a mezőben egy csillag nem nulla.

### <a name="cron-time-zones"></a>CRON időzónák

A CRON-kifejezés szereplő számok időpontját és dátumát, nem egy időtartam vonatkoznak. Ha például egy 5 a `hour` mező hivatkozik, 05:00-kor, nem minden 5 óra.

A CRON-kifejezésekkel használt alapértelmezett időzónája az egyezményes világidő (UTC). A CRON-kifejezés alapján egy másik időzónában van, hozzon létre a függvényalkalmazás nevű Alkalmazásbeállítás `WEBSITE_TIME_ZONE`. Állítsa az értékét a kívánt időzóna neve, ahogyan a [időzóna termékjegyzék](https://technet.microsoft.com/library/cc749073). 

Ha például *keleti téli idő* az UTC-05:00. Az időzítő fire aktiválhat, 10:00 keleti téli idő minden nap, használja a következő CRON-kifejezés, amely az UTC időzóna szerint kell:

```json
"schedule": "0 0 15 * * *"
``` 

Vagy hozzon létre a függvényalkalmazás nevű Alkalmazásbeállítás `WEBSITE_TIME_ZONE` , és állítsa az értékét **keleti téli idő**.  Ezután használja a következő CRON-kifejezést: 

```json
"schedule": "0 0 10 * * *"
``` 

## <a name="timespan"></a>Időtartam

 A `TimeSpan` csak egy függvényalkalmazást, amely egy App Service-csomag futtat használható.

CRON-kifejezés, ellentétben a `TimeSpan` az érték határozza meg az egyes függvény meghívási között eltelt időt. Egy függvény futtatása hosszabb, mint a megadott időszak után befejeződése után az időzítő azonnal meghívja a függvényt újra.

Egy karakterlánc kifejezett a `TimeSpan` formátuma `hh:mm:ss` amikor `hh` : kisebb, mint 24. Ha az első két szám 24, vagy nagyobb, a formátum a következő `dd:hh:mm`. Néhány példa:

|Példa |Adatvezérelt  |
|---------|---------|
|"01:00:00" | óránként        |
|"00:01:00"|percenként         |
|"24:00:00" | 24 naponta        |

## <a name="scale-out"></a>Kiterjesztés

Függvényalkalmazás elvégzi a horizontális felskálázást több példányra, ha csak egy példány lehet egy időzítő által aktivált függvény összes példányán fut le.

## <a name="function-apps-sharing-storage"></a>Megosztás tárolási függvényalkalmazások

Ha több függvényalkalmazás között megosztott tárfiókot, győződjön meg róla, hogy rendelkezik-e egy másik minden függvényalkalmazáshoz `id` a *host.json*. Kihagyhatja a `id` tulajdonságot vagy állítsa be kézzel a minden függvényalkalmazáshoz `id` más értékre. Az időzítő eseményindító egy tárolási zár segítségével győződjön meg arról, hogy lesz időzítő csak egy példánnyal, amikor a függvényalkalmazás elvégzi a horizontális felskálázást több példányra. Ha két függvényalkalmazások azonos `id` és minden egyes egy időzítő indítófeltételt használ, csak egy időzítő fog futni.

## <a name="retry-behavior"></a>Újrapróbálkozási viselkedés

Az üzenetsor eseményindító eltérően az időzítő eseményindító függvény sikertelen kísérlet után nem újra. Függvény sikertelen, azt nem hívják újra addig, amíg az ütemezés szerint.

## <a name="troubleshooting"></a>Hibaelhárítás

További információ, mi a teendő, amikor az időzítő eseményindító nem a várt módon működik: [Investigating és jelentéskészítési problémái az időzítő által aktivált függvények nem aktiválja](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Nyissa meg a rövid útmutató egy időzítő indítófeltételt használó](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
