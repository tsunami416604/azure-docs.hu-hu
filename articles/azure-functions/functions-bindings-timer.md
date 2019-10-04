---
title: Időzítő trigger a Azure Functionshoz
description: Megtudhatja, hogyan használhatja az időzítő eseményindítókat a Azure Functionsban.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: az Azure functions, függvények, eseményfeldolgozás, dinamikus számítás, kiszolgáló nélküli architektúra
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: azure-functions
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 57b4f018cd044b4f516266dcf9776e82252f7f22
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937113"
---
# <a name="timer-trigger-for-azure-functions"></a>Időzítő trigger a Azure Functionshoz 

Ez a cikk azt ismerteti, hogyan használhatók időzítő eseményindítók a Azure Functionsban. Az időzítő-trigger lehetővé teszi, hogy ütemezés szerint futtasson egy függvényt. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1.x függvények

Az időzítő triggert a [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomagban, 2. x verzióban kell megadnia. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Csomagok – 2.x függvények

Az időzítő triggert a [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomagban, 3. x verzióban kell megadnia. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Példa

Tekintse meg az adott nyelvű példa:

* [C#](#c-example)
* [C# script (.csx)](#c-script-example)
* [F#](#f-example)
* [Java](#java-example)
* [JavaScript](#javascript-example)
* [Python](#python-example)

### <a name="c-example"></a>C#például

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely minden alkalommal végrehajtja a percet, ha a percek értéke öt (például ha a függvény 18:57:00-kor kezdődik), a következő teljesítmény a 19:00:00-nél lesz. A függvény a [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot adja át.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C#parancsfájl – példa

A következő példa egy időzítő trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A függvény a [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot adja át.

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
public static void Run(TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>F#például

A következő példa egy időzítő trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ F# parancsfájl-függvényt](functions-reference-fsharp.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A függvény a [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot adja át.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Íme a F# parancsfájl-kódot:

```fsharp
let Run(myTimer: TimerInfo, log: ILogger ) =
    if (myTimer.IsPastDue) then
        log.LogInformation("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.LogInformation(sprintf "F# function executed at %s!" now)
```

### <a name="java-example"></a>Java-példa

A következő példa függvény elindítja és végrehajtja az öt percenkénti műveletet. A függvény `@TimerTrigger` megjegyzése azt a karakterláncot adja meg, amely a cron- [kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező sztring formátumot használja.

```java
@FunctionName("keepAlive")
public void keepAlive(
  @TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
      ExecutionContext context
 ) {
     // timeInfo is a JSON string, you can deserialize it to an object using your favorite JSON library
     context.getLogger().info("Timer is triggered: " + timerInfo);
}
```

### <a name="javascript-example"></a>JavaScript-példa

A következő példa egy időzítő trigger kötését mutatja be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A függvény egy [időzítő objektumot](#usage) ad át.

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

A következő JavaScript-kódot:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if (myTimer.IsPastDue)
    {
        context.log('Node is running late!');
    }
    context.log('Node timer trigger function ran!', timeStamp);   

    context.done();
};
```

### <a name="python-example"></a>Python-példa

Az alábbi példa egy időzítő trigger kötést használ, amelynek konfigurációját a *function. JSON* fájl írja le. A kötést használó tényleges [Python-függvényt](functions-reference-python.md) az  *__init__.* rajzfájl fájl írja le. A függvénynek átadott objektum [Azure. functions. TimerRequest objektum](/python/api/azure-functions/azure.functions.timerrequest)típusú. A függvény logikája azt jelzi, hogy az aktuális hívás egy kimaradt ütemterv miatt következik-e be. 

Itt van a kötési adatait a *function.json* fájlt:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Itt látható a Python-kód:

```python
import datetime
import logging

import azure.functions as func


def main(mytimer: func.TimerRequest) -> None:
    utc_timestamp = datetime.datetime.utcnow().replace(
        tzinfo=datetime.timezone.utc).isoformat()

    if mytimer.past_due:
        logging.info('The timer is past due!')

    logging.info('Python timer trigger function ran at %s', utc_timestamp)
```

## <a name="attributes"></a>Attribútumok

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Az attribútum konstruktora egy CRON kifejezést vagy egy `TimeSpan` értéket használ. Csak akkor használhatja a `TimeSpan` értéket, ha a Function alkalmazás egy App Service csomagon fut. A következő példa egy CRON-kifejezést mutat be:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
{
    if (myTimer.IsPastDue)
    {
        log.LogInformation("Timer is running late!");
    }
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
}
 ```

## <a name="configuration"></a>Konfiguráció

A következő táblázat ismerteti a megadott kötés konfigurációs tulajdonságaiban a *function.json* fájlt, és a `TimerTrigger` attribútum.

|Function.JSON tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | n/a | "TimerTrigger" értékre kell állítani. Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon.|
|**direction** | n/a | Meg kell "a". Ez a tulajdonság beállítása automatikusan történik, ha az eseményindítót fog létrehozni az Azure Portalon. |
|**name** | n/a | Annak a változónak a neve, amely az időzítő objektumot jelöli a függvény kódjában. | 
|**schedule**|**ScheduleExpression**|Egy [cron kifejezés](#ncrontab-expressions) vagy egy [TimeSpan](#timespan) érték. A `TimeSpan` csak olyan Function alkalmazás esetében használható, amely egy App Service-csomagon fut. Az ütemezett kifejezést beállíthatja egy alkalmazás-beállításban, és ezt a tulajdonságot a **%** jelekbe becsomagolt Alkalmazásbeállítások neveként állíthatja be, az alábbi példában látható módon: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Ha @no__t – 0, a rendszer meghívja a függvényt a futtatókörnyezet indításakor. Például a futtatókörnyezet akkor indul el, amikor a Function alkalmazás felébred, miután inaktivitás miatt tétlen marad. Ha a Function alkalmazás újraindul a függvény változásai miatt, és a függvény alkalmazás skálázása. Így a **runOnStartup** ritkán kell lennie, ha minden eddiginél `true` értékre van állítva, különösen éles környezetben. |
|**useMonitor**|**UseMonitor**|Állítsa `true` vagy `false` értékre, hogy jelezze, az ütemtervet figyelni kell-e. Az ütemterv figyelése továbbra is fenntartja az ütemezett előfordulásokat, hogy a támogatás az ütemterv megfelelő karbantartása legyen, még akkor is, ha a Function app instances újraindul Ha nincs megadva explicit módon, az alapértelmezett érték `true` olyan ütemezések esetében, amelyeknél az ismétlődési időköz nagyobb vagy egyenlő, mint 1 perc. Az olyan ütemtervek esetében, amelyek percenként többször aktiválódnak, az alapértelmezett érték `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Azt javasoljuk, hogy a **runOnStartup** beállítása `true` éles környezetben. Ha ezt a beállítást használja, a kód nagy előre nem látható időpontokban lesz végrehajtva. Bizonyos éles beállításokban ezek az extra végrehajtások jelentős mértékben magasabb költségekkel járhatnak a használati tervekben üzemeltetett alkalmazások esetében. Ha például a **runOnStartup** engedélyezve van, akkor a rendszer meghívja az eseményindítót, amikor a Function alkalmazás skálázásra kerül. Győződjön meg arról, hogy teljesen tisztában van a függvények üzemi viselkedésével, mielőtt engedélyezi a **runOnStartup** az éles környezetben.   

## <a name="usage"></a>Használat

Időzítő eseményindító függvény meghívásakor a függvény egy időzítő objektumot ad át. A következő JSON példa az időzítő objektum ábrázolására.

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

A `IsPastDue` tulajdonság értéke `true`, ha az aktuális függvény meghívása az ütemezettnél későbbi. Előfordulhat például, hogy egy Function alkalmazás újraindítása miatt a hívás kimarad.

## <a name="ncrontab-expressions"></a>NCRONTAB kifejezések 

Azure Functions a [NCronTab](https://github.com/atifaziz/NCrontab) -függvénytárat használja a NCronTab kifejezések értelmezéséhez. Egy NCRONTAB-exppression hasonló egy CRON-kifejezéshez, azzal a különbséggel, hogy az elején egy további hatodik mezőt is tartalmaz, amelyet másodpercek alatt használhat az időpontossághoz:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Minden mezőhöz a következő típusú értékek tartozhatnak:

|Type  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>"0 5 * * * *"</nobr>|óó: 05:00, ahol hh óránként (óránként)|
|Minden érték (`*`)|<nobr>"0 * 5 * * *"</nobr>|minden nap 5: PP: 00, ahol a mm az óra minden percében (naponta 60 alkalommal)|
|Tartomány (`-` operátor)|<nobr>"5-7 * * * * *"</nobr>|óó: PP: 05, óó: PP: 06 és óó: PP: 07, ahol óó: PP percenként minden percben (percenként 3 alkalommal)|  
|Értékek halmaza (`,` operátor)|<nobr>"5, 8, 10 * * * * *"</nobr>|óó: PP: 05, óó: PP: 08 és óó: PP: 10, ahol óó: PP percenként minden percben (percenként 3 alkalommal)|
|Intervallum értéke (`/` operátor)|<nobr>"0 */5 * * * *"</nobr>|hh: 05:00, óó: 10:00, óó: 15:00, és így tovább hh: 55:00, ahol hh óránként (12 alkalommal óránként)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>NCRONTAB-példák

Íme néhány példa a Azure Functions időzítő-triggeréhez használható NCRONTAB-kifejezésekre.

|Példa|Aktiváláskor  |
|---------|---------|
|`"0 */5 * * * *"`|öt percenként|
|`"0 0 * * * *"`|egyszer minden óra elején|
|`"0 0 */2 * * *"`|két óránként egyszer|
|`"0 0 9-17 * * *"`|óránként, 9 órától 5 óráig|
|`"0 30 9 * * *"`|minden nap 9:30-kor|
|`"0 30 9 * * 1-5"`|minden hétköznap 9:30 órakor|
|`"0 30 9 * Jan Mon"`|Január 9:30 órakor|


### <a name="ncrontab-time-zones"></a>NCRONTAB időzónái

A CRON-kifejezésben szereplő számok egy időre és dátumra hivatkoznak, nem pedig időtartományra. Például a `hour` mezőben lévő 5 érték a 5:00, nem pedig 5 óra.

A CRON-kifejezésekkel használt alapértelmezett időzóna az egyezményes világidő (UTC) szerint van megadva. Ha egy másik időzónán alapuló CRON-kifejezést szeretne létrehozni, hozzon létre egy `WEBSITE_TIME_ZONE` nevű Function-alkalmazást. Állítsa az értéket a kívánt időzóna nevére a [Microsoft időzóna-indexben](https://technet.microsoft.com/library/cc749073)látható módon. 

A *keleti téli idő* például UTC-05:00. A következő NCRONTAB-kifejezéssel, amely az UTC-időzónához 10:00 tartozik:

```
"0 0 15 * * *"
``` 

Vagy hozzon létre egy alkalmazást a `WEBSITE_TIME_ZONE` nevű Function alkalmazáshoz, és állítsa be az értéket a **keleti téli időpontra**.  Ezután a következő NCRONTAB kifejezést használja: 

```
"0 0 10 * * *"
``` 

Ha `WEBSITE_TIME_ZONE` értéket használ, az idő az adott időzónában, például a nyári időszámítás időpontjára módosul. 

## <a name="timespan"></a>TimeSpan

 A `TimeSpan` csak olyan Function alkalmazás esetében használható, amely egy App Service-csomagon fut.

A CRON kifejezéstől eltérően a @no__t 0 érték határozza meg az egyes függvények meghívása közötti időtartamot. Ha egy függvény a megadott intervallumnál hosszabb ideig fut, az időzítő azonnal meghívja a függvényt.

Karakterláncként kifejezve a `TimeSpan` formátum `hh:mm:ss`, ha a `hh` értéke kisebb, mint 24. Ha az első két számjegy 24 vagy nagyobb, a formátum `dd:hh:mm`. Néhány példa:

|Példa |Aktiváláskor  |
|---------|---------|
|"01:00:00" | óránként        |
|"00:01:00"|percenként         |
|"24:00:00" | minden 24 nap        |
|"1.00:00:00" | naponta        |

## <a name="scale-out"></a>Kiterjesztés

Ha egy függvény alkalmazás több példányra is kiterjed, csak egy időzítő által aktivált függvény egyetlen példánya fut az összes példányon.

## <a name="function-apps-sharing-storage"></a>Function apps megosztása Storage

Ha több Function-alkalmazás között oszt meg egy Storage-fiókot, győződjön meg arról, hogy az egyes functions-alkalmazások eltérő `id` értékűek a *Host. JSON*fájlban. Kihagyhatja a `id` tulajdonságot, vagy manuálisan is beállíthatja `id` az egyes függvények alkalmazásait egy másik értékre. Az időzítő-trigger egy tárolási zárolást használ annak biztosítására, hogy csak egy időzítő példány legyen, ha egy függvény alkalmazás több példányra is méretezhető. Ha két Function-alkalmazás azonos `id` , és mindegyik időzítő-triggert használ, csak egy időzítő fog futni.

## <a name="retry-behavior"></a>Újrapróbálkozási viselkedés

A várólista-triggertől eltérően az időzítő trigger nem próbálkozik újra a függvény meghibásodása után. Ha egy függvény meghibásodik, azt a rendszer nem hívja újra az ütemezés következő időpontjáig.

## <a name="troubleshooting"></a>Hibaelhárítás

További információ arról, hogy mi a teendő, ha az időzítő trigger nem a várt módon működik, lásd: [kivizsgálás és jelentéskészítési hibák az időzítő által aktivált függvények nem égetéssel](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ugrás olyan rövid útmutatóra, amely időzítő-triggert használ](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)
