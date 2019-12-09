---
title: Időzítő trigger a Azure Functionshoz
description: Megtudhatja, hogyan használhatja az időzítő eseményindítókat a Azure Functionsban.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: de36f760fb637ad02446265927e7df7aa91b2abf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928378"
---
# <a name="timer-trigger-for-azure-functions"></a>Időzítő trigger a Azure Functionshoz 

Ez a cikk azt ismerteti, hogyan használhatók időzítő eseményindítók a Azure Functionsban. Az időzítő-trigger lehetővé teszi, hogy ütemezés szerint futtasson egy függvényt. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok – 1. x függvények

Az időzítő triggert a [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomagban, 2. x verzióban kell megadnia. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok – 2. x és újabb függvények

Az időzítő triggert a [Microsoft. Azure. webjobs. Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomagban, 3. x verzióban kell megadnia. A csomag forráskódja az [Azure-webjobs-SDK-Extensions GitHub-](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Példa

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az alábbi példa egy [ C# függvényt](functions-dotnet-class-library.md) mutat be, amely minden alkalommal végrehajtja a percet, ha a percek értéke öt (például ha a függvény 18:57:00-kor kezdődik), a következő teljesítmény a 19:00:00-nél lesz. A rendszer átadja a függvénynek a [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot.

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

A következő példa egy időzítő trigger kötést mutat be egy *function. JSON* fájlban, valamint egy olyan [ C# parancsfájl-függvényt](functions-reference-csharp.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A rendszer átadja a függvénynek a [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektumot.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

A C# szkript kódja:

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

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A következő példa egy időzítő trigger kötését mutatja be egy *function. JSON* fájlban, valamint egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény egy naplót ír, amely azt jelzi, hogy a függvény meghívása egy kihagyott ütemterv miatt következik-e be. A függvény egy [időzítő objektumot](#usage) ad át.

Itt található a *function. JSON* fájlban található kötési adat:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Itt látható a JavaScript-kód:

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Az alábbi példa egy időzítő trigger kötést használ, amelynek konfigurációját a *function. JSON* fájl írja le. A kötést használó tényleges [Python-függvényt](functions-reference-python.md) az  *__init__.* rajzfájl fájl írja le. A függvénynek átadott objektum [Azure. functions. TimerRequest objektum](/python/api/azure-functions/azure.functions.timerrequest)típusú. A függvény logikája azt jelzi, hogy az aktuális hívás egy kimaradt ütemterv miatt következik-e be. 

Itt található a *function. JSON* fájlban található kötési adat:

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

# <a name="javatabjava"></a>[Java](#tab/java)

A következő példa függvény elindítja és végrehajtja az öt percenkénti műveletet. A függvény `@TimerTrigger` jegyzete az ütemtervet a [cron-kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező karakterlánc-formátum használatával határozza meg.

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

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

Az [ C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs).

Az attribútum konstruktora egy CRON kifejezést vagy egy `TimeSpan`t használ. `TimeSpan` csak akkor használható, ha a Function alkalmazás egy App Service csomagon fut. A következő példa egy CRON-kifejezést mutat be:

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

# <a name="c-scripttabcsharp-script"></a>[C#Parancsfájl](#tab/csharp-script)

Az C# attribútumokat a parancsfájl nem támogatja.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="javatabjava"></a>[Java](#tab/java)

A függvény `@TimerTrigger` jegyzete az ütemtervet a [cron-kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező karakterlánc-formátum használatával határozza meg.

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

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és a `TimerTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**type** | – | "TimerTrigger" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**direction** | – | "In" értékre kell állítani. Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. |
|**név** | – | Annak a változónak a neve, amely az időzítő objektumot jelöli a függvény kódjában. | 
|**schedule**|**ScheduleExpression**|Egy [cron kifejezés](#ncrontab-expressions) vagy egy [TimeSpan](#timespan) érték. `TimeSpan` csak egy App Service-csomagon futó Function alkalmazás esetében használható. Az ütemezett kifejezést beállíthatja egy alkalmazás-beállításban, és ezt a tulajdonságot becsomagolhatja **%** -jelekbe becsomagolt alkalmazás-beállítási névre, ahogy az a következő példában látható: "% ScheduleAppSetting%". |
|**runOnStartup**|**RunOnStartup**|Ha `true`, a rendszer meghívja a függvényt a futtatókörnyezet indításakor. Például a futtatókörnyezet akkor indul el, amikor a Function alkalmazás felébred, miután inaktivitás miatt tétlen marad. Ha a Function alkalmazás újraindul a függvény változásai miatt, és a függvény alkalmazás skálázása. Így a **runOnStartup** -nek ritkán kell lennie, ha minden eddiginél `true`re van állítva, különösen éles környezetben. |
|**useMonitor**|**UseMonitor**|Állítsa `true` vagy `false` értékre, hogy jelezze, az ütemtervet figyelni kell-e. Az ütemterv figyelése továbbra is fenntartja az ütemezett előfordulásokat, hogy a támogatás az ütemterv megfelelő karbantartása legyen, még akkor is, ha a Function app instances újraindul Ha nincs beállítva explicit módon, az alapértelmezett érték `true` az olyan ütemezések esetében, amelyek ismétlődési időköze 1 percnél nagyobb vagy azzal egyenlő. Az olyan ütemtervek esetében, amelyek percenként többször aktiválódnak, az alapértelmezett érték `false`.

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Javasoljuk, hogy az éles környezetben való `true` **runOnStartup** . Ha ezt a beállítást használja, a kód nagy előre nem látható időpontokban lesz végrehajtva. Bizonyos éles beállításokban ezek az extra végrehajtások jelentős mértékben magasabb költségekkel járhatnak a használati tervekben üzemeltetett alkalmazások esetében. Ha például a **runOnStartup** engedélyezve van, akkor a rendszer meghívja az eseményindítót, amikor a Function alkalmazás skálázásra kerül. Győződjön meg arról, hogy teljesen tisztában van a függvények üzemi viselkedésével, mielőtt engedélyezi a **runOnStartup** az éles környezetben.   

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

A `IsPastDue` tulajdonság akkor `true`, ha az aktuális függvény meghívása az ütemezettnél későbbi. Előfordulhat például, hogy egy Function alkalmazás újraindítása miatt a hívás kimarad.

## <a name="ncrontab-expressions"></a>NCRONTAB kifejezések 

Azure Functions a [NCronTab](https://github.com/atifaziz/NCrontab) -függvénytárat használja a NCronTab kifejezések értelmezéséhez. Egy NCRONTAB kifejezés hasonló egy CRON-kifejezéshez, kivéve, ha az elején további hatodik mező szerepel az idő pontosságához másodpercben:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Minden mezőhöz a következő típusú értékek tartozhatnak:

|Type (Típus)  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>"0 5 * * * *"</nobr>|óó: 05:00, ahol hh óránként (óránként)|
|Minden érték (`*`)|<nobr>"0 * 5 * * *"</nobr>|minden nap 5: PP: 00, ahol a mm az óra minden percében (naponta 60 alkalommal)|
|Tartomány (`-` operátor)|<nobr>"5-7 * * * * * *"</nobr>|óó: PP: 05, óó: PP: 06 és óó: PP: 07, ahol óó: PP percenként minden percben (percenként 3 alkalommal)|
|Értékek halmaza (`,` operátor)|<nobr>"5, 8, 10 * * * * * *"</nobr>|óó: PP: 05, óó: PP: 08 és óó: PP: 10, ahol óó: PP percenként minden percben (percenként 3 alkalommal)|
|Intervallum érték (`/` operátor)|<nobr>"0 */5 * * * *"</nobr>|hh: 05:00, óó: 10:00, óó: 15:00, és így tovább hh: 55:00, ahol hh óránként (12 alkalommal óránként)|

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

A CRON-kifejezésben szereplő számok egy időre és dátumra hivatkoznak, nem pedig időtartományra. Például a `hour` mezőben lévő 5 érték 5:00-re, és nem 5 óránként van megjelölve.

A CRON-kifejezésekkel használt alapértelmezett időzóna az egyezményes világidő (UTC) szerint van megadva. Ha egy másik időzóna alapján szeretné megkeresni a CRON-kifejezést, hozzon létre egy, az `WEBSITE_TIME_ZONE`nevű Function alkalmazáshoz tartozó Alkalmazásbeállítás-beállítást. Állítsa az értéket a kívánt időzóna nevére a [Microsoft időzóna-indexben](https://technet.microsoft.com/library/cc749073)látható módon.

  > [!NOTE]
  > a `WEBSITE_TIME_ZONE` jelenleg nem támogatott a Linux-használati tervben.

A *keleti téli idő* például UTC-05:00. A következő NCRONTAB-kifejezéssel, amely az UTC-időzónához 10:00 tartozik:

```
"0 0 15 * * *"
``` 

Vagy hozzon létre egy alkalmazást a `WEBSITE_TIME_ZONE` nevű Function alkalmazáshoz, és állítsa be az értéket **keleti téli időpontra**.  Ezután a következő NCRONTAB kifejezést használja: 

```
"0 0 10 * * *"
``` 

`WEBSITE_TIME_ZONE`használatakor az idő az adott időzónában, például a nyári időszámítás időpontjára módosul. 

## <a name="timespan"></a>időtartam

 `TimeSpan` csak egy App Service-csomagon futó Function alkalmazás esetében használható.

A CRON kifejezéstől eltérően a `TimeSpan` érték határozza meg az egyes függvények meghívása közötti időtartamot. Ha egy függvény a megadott intervallumnál hosszabb ideig fut, az időzítő azonnal meghívja a függvényt.

Karakterláncként kifejezve a `TimeSpan` formátum `hh:mm:ss`, ha a `hh` kevesebb, mint 24. Ha az első két számjegy 24 vagy nagyobb, a formátum `dd:hh:mm`. Néhány példa:

|Példa |Aktiváláskor  |
|---------|---------|
|"01:00:00" | óránként        |
|"00:01:00"|percenként         |
|"24:00:00" | 24 óránként        |
|"1.00:00:00" | minden nap        |

## <a name="scale-out"></a>Kiterjesztés

Ha egy függvény alkalmazás több példányra is kiterjed, csak egy időzítő által aktivált függvény egyetlen példánya fut az összes példányon.

## <a name="function-apps-sharing-storage"></a>Function apps megosztása Storage

Ha a Storage-fiókokat az App Service-be nem telepített functions-alkalmazások között osztja meg, előfordulhat, hogy explicit módon kell kiosztania a gazdagép AZONOSÍTÓját az egyes alkalmazásokhoz.

| Függvények verziója | Beállítás                                              |
| ----------------- | ---------------------------------------------------- |
| 2. x (és újabb)  | `AzureFunctionsWebHost__hostid` környezeti változó |
| 1. x               | `id` a *Host. JSON* fájlban                                  |

Kihagyhatja az azonosító értéket, vagy manuálisan állíthatja be az egyes functions-alkalmazások azonosítási konfigurációját egy másik értékre.

Az időzítő-trigger tároló-zárolással biztosítja, hogy csak egy időzítő példány legyen, ha egy függvény alkalmazás több példányra is méretezhető. Ha két Function-alkalmazás ugyanazt az azonosítási konfigurációt használja, és mindegyik időzítő-triggert használ, csak egy időzítő fut.

## <a name="retry-behavior"></a>Újrapróbálkozási viselkedés

A várólista-triggertől eltérően az időzítő trigger nem próbálkozik újra a függvény meghibásodása után. Ha egy függvény meghibásodik, azt a rendszer nem hívja újra az ütemezés következő időpontjáig.

## <a name="troubleshooting"></a>Hibakeresés

További információ arról, hogy mi a teendő, ha az időzítő trigger nem a várt módon működik, lásd: [kivizsgálás és jelentéskészítési hibák az időzítő által aktivált függvények nem égetéssel](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ugrás olyan rövid útmutatóra, amely időzítő-triggert használ](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
