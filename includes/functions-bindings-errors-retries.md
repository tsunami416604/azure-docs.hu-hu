---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 285c3bf37e9d6de042cb028745fc8b094d34c3a1
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93284409"
---
Egy Azure Functionsban felmerülő hibák a következő eredetek bármelyike esetén származhatnak:

- Beépített Azure Functions [Eseményindítók és kötések](..\articles\azure-functions\functions-triggers-bindings.md) használata
- Az alapul szolgáló Azure-szolgáltatások API-jának meghívása
- REST-végpontokra irányuló hívások
- Ügyféloldali kódtárak, csomagok vagy külső API-k hívása

Az adatvesztés és a kihagyott üzenetek elvesztésének elkerülése érdekében fontos a megfelelő hibakezelés. Ajánlott hibakezelés a következő műveletekkel:

- [Az Application Insights engedélyezése](../articles/azure-functions/functions-monitoring.md)
- [Strukturált hibakezelés használata](#use-structured-error-handling)
- [Idempotencia tervezése](../articles/azure-functions/functions-idempotent.md)
- [Újrapróbálkozási szabályzatok implementálása](#retry-policies) (ha szükséges)

### <a name="use-structured-error-handling"></a>Strukturált hibakezelés használata

A rögzítési és naplózási hibák kritikus fontosságúak az alkalmazás állapotának monitorozásához. A függvények kódjának legfelső szintjén szerepelnie kell egy try/catch blokknak. A Catch blokkban a hibák rögzítése és naplózása végezhető el.

## <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

Az újrapróbálkozási szabályzat bármely függvényben definiálható a Function alkalmazás bármely trigger-típusához.  Az újrapróbálkozási szabályzat Újrafuttatja a függvényt, amíg a végrehajtás nem sikerült, vagy amíg az újrapróbálkozások maximális száma megtörténik.  Az újrapróbálkozási szabályzatok meghatározhatók az alkalmazás összes funkciója vagy az egyes függvények esetében.  Alapértelmezés szerint a Function alkalmazás nem próbálkozik újra az üzenetekkel (az [eseményindító-forrásra vonatkozó újrapróbálkozási szabályzattal rendelkező eseményindítók esetében](#trigger-specific-retry-support)).  Az újrapróbálkozási szabályzat kiértékelése akkor történik meg, amikor egy végrehajtás nem kezelt kivételt eredményez.  Ajánlott eljárásként az összes kivételt el kell fogni a kódban, és újra kell dobnia azokat a hibákat, amelyek újrapróbálkozást eredményeznek.  Event Hubs és Azure Cosmos DB ellenőrzőpontok nem írhatók, amíg a végrehajtás újrapróbálkozási házirendje be nem fejeződik, azaz a partíción lévő előrehaladás szünetel, amíg az aktuális köteg be nem fejeződik.

### <a name="retry-policy-options"></a>Újrapróbálkozási szabályzat beállításai

Az újrapróbálkozási szabályzat definiálásához a következő lehetőségek állnak rendelkezésre.

Az **újrapróbálkozások** maximális száma érték az a művelet, amelynek során a rendszer újból megkísérli a végrehajtást a végleges hiba előtt. Az érték `-1` azt jelenti, hogy a határozatlan idejű újrapróbálkozás. A rendszer a példány memóriájában tárolja a jelenlegi újrapróbálkozások számát. Lehetséges, hogy egy példány meghiúsul az újrapróbálkozási kísérletek között.  Ha egy példány meghiúsul az újrapróbálkozási házirend során, az újrapróbálkozások száma elvész.  A példányok meghibásodása esetén a triggerek, például a Event Hubs, a Azure Cosmos DB és a várólista-tárolás képes folytatni a feldolgozást, és újra próbálkozni a kötegtel egy új példányon, és az újrapróbálkozások száma nullára lesz állítva.  Más eseményindítók, például a HTTP és az időzítő, nem folytatnak új példányt.  Ez azt jelenti, hogy a maximális újrapróbálkozások száma a legjobb megoldás, és néhány ritka esetben a végrehajtás több, mint a maximumot, a HTTP-t és az időzítőt pedig a maximálisnál kisebb értékre lehet megkísérelni.

Az **újrapróbálkozási stratégia** szabályozza az újrapróbálkozások viselkedését.  A következő két támogatott újrapróbálkozási lehetőség közül választhat:

| Beállítás | Leírás|
|---|---|
|**`fixedDelay`**| Az egyes újrapróbálkozások között a megadott időtartam eltelt.|
| **`exponentialBackoff`**| Az első újrapróbálkozás megvárja a minimális késleltetést. A későbbi újrapróbálkozások során a rendszer exponenciálisan hozzáadja az időt az egyes újrapróbálkozások kezdeti időtartamához, amíg el nem éri a maximális késleltetést.  Az exponenciális visszatartással kis véletlenszerűség következik be, amely késlelteti az újrapróbálkozások nagy átviteli sebességű helyzetekben való késleltetését.|

#### <a name="app-level-configuration"></a>Alkalmazás szintű konfiguráció

Újrapróbálkozási szabályzat definiálható egy alkalmazás összes függvényéhez [a `host.json` fájl használatával](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Függvény szintű konfiguráció

Egy adott függvény újrapróbálkozási szabályzata is meghatározható.  A Function-specifikus konfiguráció elsőbbséget élvez az alkalmazás szintű konfigurációval szemben.

#### <a name="fixed-delay-retry"></a>Rögzített késleltetés – újrapróbálkozás

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Exponenciális leállítási újrapróbálkozás

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Itt látható az újrapróbálkozási szabályzat a fájl *function.jsjában* :

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.jsa tulajdonságon  |Attribútum tulajdonsága | Leírás |
|---------|---------|---------| 
|stratégia|n/a|Kötelező. A használt újrapróbálkozási stratégia. Az érvényes értékek a következők: `fixedDelay` vagy `exponentialBackoff` .|
|Maxretrycount csak|n/a|Kötelező. Az újrapróbálkozások maximális száma egy függvény végrehajtásakor. `-1` azt jelenti, hogy határozatlan ideig próbálkozik.|
|delayInterval|n/a|Az újrapróbálkozások között a stratégia használatakor használt késleltetés `fixedDelay` .|
|minimumInterval|n/a|A stratégia használatának minimális újrapróbálkozási késleltetése `exponentialBackoff` .|
|maximumInterval|n/a|A stratégia használatának maximális újrapróbálkozási késleltetése `exponentialBackoff` .| 

## <a name="trigger-specific-retry-support"></a>Trigger-specifikus újrapróbálkozás támogatása

Egyes eseményindítók az eseményindító forrásában újrapróbálkozásokat biztosítanak.  Ezek a trigger-újrapróbálkozások a Function app Host újrapróbálkozási házirendjének kiegészítéseként vagy helyettesítő módon is használhatók.  Ha meghatározott számú újrapróbálkozásra van szükség, a trigger-specifikus újrapróbálkozási szabályzatot kell használnia az általános gazdagép újrapróbálkozási házirendjében.  A következő eseményindítók támogatják az újrapróbálkozásokat az eseményindító forrásaként:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure üzenetsor-tároló](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (Üzenetsor/témakör)](../articles/azure-functions/functions-bindings-service-bus.md)

Alapértelmezés szerint ezek az eseményindítók legfeljebb ötször kérik újra a kérelmeket. Az ötödik újrapróbálkozás után az Azure üzenetsor-tároló és a Azure Service Bus trigger üzenetet ír a méreg- [várólistába](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages).
