---
title: Időzítő-eseményindító az Azure Functionshez
description: Ismerje meg, hogyan használhatja az időzítő-eseményindítókat az Azure Functionsben.
author: craigshoemaker
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.topic: reference
ms.date: 09/08/2018
ms.author: cshoe
ms.custom: ''
ms.openlocfilehash: 566d6ccf43024692e19bcd6639fe5cfbbba0660d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056408"
---
# <a name="timer-trigger-for-azure-functions"></a>Időzítő-eseményindító az Azure Functionshez 

Ez a cikk bemutatja, hogyan dolgozhat időzítő eseményindítók az Azure Functionsben. Az időzítő eseményindítólehetővé teszi egy függvény ütemezés szerinti futtatását. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Csomagok - 1.x függvények

Az időzítő eseményindítója a [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomag 2.x-es verziójában található. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Timers/) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x-and-higher"></a>Csomagok - 2.x vagy újabb funkciók

Az időzítő eseményindítója a [Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet csomag 3.x-es verziójában található. A csomag forráskódja az [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/) GitHub-tárházban található.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="example"></a>Példa

# <a name="c"></a>[C #](#tab/csharp)

A következő példa egy [C# függvényt](functions-dotnet-class-library.md) mutat be, amely minden alkalommal végrehajtásra kerül, amikor a percek értéke ötel osztható (például ha a függvény 18:57:00-kor kezdődik, a következő teljesítmény 19:00:00-kor lesz). Az [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektum átkerül a függvénybe.

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

A következő példa egy időzítő eseményindító kötést mutat be egy *function.json* fájlban, és egy [C# parancsfájl függvényt,](functions-reference-csharp.md) amely a kötést használja. A függvény naplót ír, amely jelzi, hogy a függvény meghívása egy kihagyott ütemezési esemény miatt történt-e. Az [`TimerInfo`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) objektum átkerül a függvénybe.

A *function.json* fájlban a kötési adatok:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Itt a C# script kód:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

A következő példa egy időzítő eseményindító kötést mutat be egy *function.json* fájlban, és egy [JavaScript függvényt,](functions-reference-node.md) amely a kötést használja. A függvény naplót ír, amely jelzi, hogy a függvény meghívása egy kihagyott ütemezési esemény miatt történt-e. A függvény egy [időzítőobjektumot](#usage) ad át.

A *function.json* fájlban a kötési adatok:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Itt a JavaScript-kód:

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

# <a name="python"></a>[Python](#tab/python)

A következő példa egy időzítő eseményindító-kötést használ, amelynek konfigurációja a *function.json* fájlban van leírva. A kötést használó tényleges [Python-függvény](functions-reference-python.md) leírása az * __init__.py* fájlban található. A függvénybe átadott objektum [azure.functions.TimerRequest objektum típusú.](/python/api/azure-functions/azure.functions.timerrequest) A függvénylogika írja a naplókat, jelezve, hogy az aktuális meghívás oka egy kihagyott ütemezés előfordulása. 

A *function.json* fájlban a kötési adatok:

```json
{
    "name": "mytimer",
    "type": "timerTrigger",
    "direction": "in",
    "schedule": "0 */5 * * * *"
}
```

Itt a Python kód:

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

# <a name="java"></a>[Java](#tab/java)

A következő példa függvény ötpercenként aktiválódik és hajt végre. A `@TimerTrigger` függvény jegyzetelése az ütemezést a [CRON-kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező karakterláncformátummal határozza meg.

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

# <a name="c"></a>[C #](#tab/csharp)

A [C# osztálytárakban](functions-dotnet-class-library.md)használja a [TimerTriggerAttribute attribútumot.](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)

Az attribútum konstruktora CRON-kifejezést vagy . `TimeSpan` Csak akkor `TimeSpan` használható, ha a függvényalkalmazás egy App Service-csomagon fut. `TimeSpan`nem támogatott a fogyasztás i vagy rugalmas prémium funkciók.

A következő példa egy CRON kifejezést mutat be:

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

# <a name="c-script"></a>[C# parancsfájl](#tab/csharp-script)

Az attribútumokat a C# script nem támogatja.

# <a name="javascript"></a>[Javascript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

Az attribútumokat a Python nem támogatja.

# <a name="java"></a>[Java](#tab/java)

A `@TimerTrigger` függvény jegyzetelése az ütemezést a [CRON-kifejezésekkel](https://en.wikipedia.org/wiki/Cron#CRON_expression)megegyező karakterláncformátummal határozza meg.

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

Az alábbi táblázat a *function.json* fájlban és az `TimerTrigger` attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti.

|function.json tulajdonság | Attribútum tulajdonság |Leírás|
|---------|---------|----------------------|
|**Típus** | n/a | Be kell állítani a "timerTrigger". Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon.|
|**direction** | n/a | Be kell állítani, hogy "in". Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az eseményindítót az Azure Portalon. |
|**név** | n/a | Az időzítőobjektumot a függvénykódban jelölő változó neve. | 
|**Ütemezése**|**ScheduleExpression kifejezés**|[CRON-kifejezés](#ncrontab-expressions) vagy [TimeSpan](#timespan) érték. A `TimeSpan` csak egy app szolgáltatási csomagon futó függvényalkalmazáshoz használható. Az ütemezési kifejezést egy alkalmazásbeállításba helyezheti, és beállíthatja, hogy ez a tulajdonság az alkalmazásbeállítás nevét jelekbe **%** csomagolva állítsa be, ahogy ebben a példában a következő példában: "%ScheduleAppSetting%". |
|**runOnStartup**|**RunonStartup**|Ha `true`a függvény meghívása a futásidő indításakor történik. Például a futásidejű akkor kezdődik, amikor a függvényalkalmazás felébred, miután tétlen inaktív miatt tétlen. amikor a függvényalkalmazás a funkció változások miatt újraindul, és amikor a függvényalkalmazás horizontálisan kinagyul. Tehát **runOnStartup** ritkán kell beállítani, különösen `true`a termelés. |
|**useMonitor**|**UseMonitor**|`true` Beállíthatja, `false` vagy jelezze, hogy az ütemezést ellenőrizni kell-e. Ütemezésfigyelés továbbra is ütemezése előfordulások támogatás biztosítása érdekében az ütemezés megfelelően karbanmaradnak, még akkor is, ha a függvény alkalmazáspéldányok újraindítása. Ha nincs beállítva explicit `true` módon, az alapértelmezett az ütemezések, amelyek ismétlődési időköze nagyobb vagy egyenlő, mint 1 perc. A percenként többször aktivált ütemezések esetében az `false`alapértelmezett érték a .

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!CAUTION]
> Azt javasoljuk, hogy ne `true` tegye a **runOnStartup** beállítást éles környezetben. Ezzel a beállítással a kód végrehajtása rendkívül kiszámíthatatlan időpontokban. Bizonyos éles környezetben ezek az extra végrehajtások jelentősen magasabb költségeket eredményezhetnek a használati csomagokban üzemeltetett alkalmazások esetében. Például **a runOnStartup** engedélyezve van az eseményindító meghívása a függvényalkalmazás méretezésekekekekekekkor. Győződjön meg arról, hogy teljes mértékben ismeri a függvények éles viselkedését, mielőtt engedélyezné **a runOnStartup** éles környezetben.   

## <a name="usage"></a>Használat

Időzítő eseményindító függvény meghívásakor egy időzítőobjektum kerül át a függvénybe. A következő JSON egy példa az időzítő objektum.

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

A `IsPastDue` tulajdonság `true` az, amikor az aktuális függvény meghívása az ütemezettnél későbbi. Egy függvényalkalmazás újraindítása például a meghívás kihagyását okozhatja.

## <a name="ncrontab-expressions"></a>NCRONTAB kifejezések 

Az Azure Functions az [NCronTab függvénytárat](https://github.com/atifaziz/NCrontab) használja az NCRONTAB-kifejezések értelmezéséhez. Az NCRONTAB kifejezés hasonlít a CRON-kifejezéshez, azzal a különbséggel, hogy az elején egy további hatodik mezőt tartalmaz, amelyet másodpercben használnak az időpontossághoz:

`{second} {minute} {hour} {day} {month} {day-of-week}`

Minden mező a következő típusú értékek egyikével rendelkezhet:

|Típus  |Példa  |Aktiváláskor  |
|---------|---------|---------|
|Egy adott érték |<nobr>"0 5 * * * *"</nobr>|a hh:05:00-kor, ahol hh óránként (óránként egyszer)|
|Minden érték`*`( )|<nobr>"0 * 5 * * *"</nobr>|5:mm:00 minden nap, ahol mm minden percében az óra (60-szor egy nap)|
|A tartomány`-` ( operátor)|<nobr>"5-7 * * * * *"</nobr>|a(z) hh:05,hh:pp:06 és a hh:mm:07 időpontban, ahol a hh:mm minden perc óránként (percenként 3 alkalommal)|
|Értékhalmaz (`,` operátor)|<nobr>"5,8,10 * * * * *"</nobr>|a(z) hh:mm:05,hh:pp:08 és a hh:mm:10 időpontban, ahol a hh:mm minden perc óránként (percenként 3 alkalommal)|
|Intervallumérték (`/` operátor)|<nobr>"0 */5 * * * *"</nobr>|a hh:00:00, hh:05:00, hh:10:00, és így tovább keresztül hh:55:00, ahol hh van óránként (12-szer egy óra)|

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="ncrontab-examples"></a>Példák NCRONTAB

Íme néhány példa az NCRONTAB-kifejezésekre, amelyeket az Azure Functions időzítőeseményindítójával használhat.

|Példa|Aktiváláskor  |
|---------|---------|
|`"0 */5 * * * *"`|ötpercenként egyszer|
|`"0 0 * * * *"`|óránként egyszer|
|`"0 0 */2 * * *"`|kétóránként egyszer|
|`"0 0 9-17 * * *"`|óránként egyszer 9:00 és 17:00 óra között|
|`"0 30 9 * * *"`|minden nap 9:30-kor|
|`"0 30 9 * * 1-5"`|hétköznap 9:30-kor|
|`"0 30 9 * Jan Mon"`|január ban minden hétfőn 9:30-kor|


### <a name="ncrontab-time-zones"></a>NCRONTAB időzónák

A CRON-kifejezésben lévő számok időre és dátumra utalnak, nem pedig időtartományra. A `hour` mezőben lévő 5 például 5:00 órára utal, nem 5 óránként.

A CRON-kifejezésekalapértelmezett időzónája az egyezményes világidő (UTC). Ha azt szeretné, hogy a CRON-kifejezés egy másik időzónán alapuljon, hozzon létre egy alkalmazásbeállítást a függvényalkalmazáshoz, melynek neve `WEBSITE_TIME_ZONE`. Állítsa be az értéket a kívánt időzóna nevére a [Microsoft Időzóna-indexben](https://technet.microsoft.com/library/cc749073)látható módon.

  > [!NOTE]
  > `WEBSITE_TIME_ZONE`jelenleg nem támogatott a Linux-felhasználási tervben.

A *keleti téli idő például* UTC-05:00. Ha azt szeretné, hogy az időzítő minden nap 10:00-kor aktiválódjon, használja a következő NCRONTAB kifejezést, amely az UTC időzónáját használja:

```
"0 0 15 * * *"
``` 

Vagy hozzon létre egy alkalmazásbeállítást a függvényalkalmazás nevű, `WEBSITE_TIME_ZONE` és állítsa be az értéket keleti téli **idő.**  Ezután a következő NCRONTAB kifejezést használja: 

```
"0 0 10 * * *"
``` 

A használatakor `WEBSITE_TIME_ZONE`az idő módosul az adott időzónában bekövetkező időváltozásokhoz, például a nyári időszámításhoz. 

## <a name="timespan"></a>időtartam

 A `TimeSpan` csak egy app szolgáltatási csomagon futó függvényalkalmazáshoz használható.

A CRON-kifejezéssel `TimeSpan` ellentétben az érték az egyes függvények meghívása közötti időintervallumot adja meg. Ha egy függvény a megadott időköznél hosszabb futás után fejeződik be, az időzítő azonnal újra meghívja a függvényt.

Karakterláncként kifejezve `TimeSpan` a `hh:mm:ss` formátum `hh` akkor van, amikor kevesebb, mint 24. Ha az első két számjegy legalább 24, a formátum `dd:hh:mm`a . Néhány példa:

|Példa |Aktiváláskor  |
|---------|---------|
|"01:00:00" | óránként        |
|"00:01:00"|percenként         |
|"24:00:00" | 24 naponta        |
|"1.00:00:00" | minden nap        |

## <a name="scale-out"></a>Bővítés

Ha egy függvényalkalmazás több példányra skálázódik, az időzítő által aktivált függvénynek csak egy példánya fut az összes példányon.

## <a name="function-apps-sharing-storage"></a>Funkciós alkalmazások megosztása a tárolóban

Ha olyan függvényalkalmazások között oszt meg tárfiókokat, amelyek nincsenek telepítve az alkalmazásszolgáltatásba, előfordulhat, hogy minden alkalmazáshoz explicit módon hozzá kell rendelnie a gazdagép-azonosítót.

| Függvények verziója | Beállítás                                              |
| ----------------- | ---------------------------------------------------- |
| 2.x (és magasabb)  | `AzureFunctionsWebHost__hostid`környezeti változó |
| 1,x               | `id`a *host.json*                                  |

Kihagyhatja az azonosító értéket, vagy manuálisan beállíthatja az egyes függvényalkalmazások azonosító konfigurációját egy másik értékre.

Az időzítő eseményindító egy tárolózárat használ annak biztosítására, hogy csak egy időzítőpéldány legyen, amikor egy függvényalkalmazás több példányra skálázódik. Ha két függvényalkalmazás azonos azonosító konfigurációval rendelkezik, és mindegyik időzítő-eseményindítót használ, csak egy időzítő fut.

## <a name="retry-behavior"></a>Újrapróbálkozási viselkedés

A várólista-eseményindítóval ellentétben az időzítő eseményindítója nem indul újra, miután egy függvény meghibásodik. Ha egy függvény meghibásodik, a rendszer csak az ütemezés következő időpontjában kéri meg újra.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha többet szeretne tudni arról, hogy mi a teendő, ha az időzítő eseményindítója nem a várt módon működik, [olvassa el a Nem tüzelt időzítővel kapcsolatos problémák vizsgálata és jelentése](https://github.com/Azure/azure-functions-host/wiki/Investigating-and-reporting-issues-with-timer-triggered-functions-not-firing)című témakört.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Ugrás időzítő-eseményindítót használó rövid útmutatóra](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)
