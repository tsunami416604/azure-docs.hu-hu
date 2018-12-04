---
title: Az Azure digitális Twins felhasználói függvények hibakeresése |} A Microsoft Docs
description: Az Azure digitális Twins felhasználói függvények hibakeresése kapcsolatos útmutató
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: stefanmsft
ms.openlocfilehash: 559c737c4e8309527d588b59759b93750da33a9e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837715"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények használata az Azure digitális Twins problémák hibakeresése

Ez a cikk összefoglalja, hogyan diagnosztizálhatja a felhasználó által definiált függvények. Ezután azonosítja a leggyakoribb forgatókönyvek észlelt, amikor a velük végzett munkát némelyike.

## <a name="debug-issues"></a>Hibakeresés

Hogyan diagnosztizálhatja a belül az Azure digitális Twins-példány felmerülő problémákat, hogy támogatási hatékonyan azonosíthatja a problémát, a probléma, és a egy megoldás okát.

### <a name="enable-log-analytics-for-your-instance"></a>A példány a log analytics engedélyezése

Naplók és mérőszámok az Azure digitális Twins-példány az Azure monitoron keresztül érhetők el. Az alábbi dokumentáció azt feltételezi, hogy létrehozott egy [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) munkaterületen a [az Azure Portal](../log-analytics/log-analytics-quick-create-workspace.md)segítségével, [Azure CLI-vel](../log-analytics/log-analytics-quick-create-workspace-cli.md), vagy a Kiszolgálókezelő [ PowerShell](../log-analytics/log-analytics-quick-create-workspace-posh.md).

> [!NOTE]
> Események küldése során egy 5 perces késleltetés tapasztalhat **Log Analytics** először.

A cikk a ["Gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások"](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) az Azure digitális Twins-példány a Portal, az Azure CLI vagy a Powershellen keresztül a diagnosztikai beállítások engedélyezése. Ellenőrizze, hogy válassza ki az összes naplókategóriák, mérőszámok és az Azure Log Analytics-munkaterületet.

### <a name="trace-sensor-telemetry"></a>Mozgásérzékelő – híváslánc-telemetria

Győződjön meg arról, hogy diagnosztikai beállítások engedélyezve van az Azure digitális Twins-példányon, az összes napló kategória ki van jelölve, és, hogy a naplók az Azure Log Analytics kap.

Egyezik meg a megfelelő naplók érzékelő telemetriai üzenetet, adja meg a korrelációs Azonosítót meg az eseményadatokat küld a rendszer. Ehhez állítsa a `x-ms-client-request-id` tulajdonságát egy GUID Azonosítót.

Telemetria küldése után nyissa meg Azure Log Analytics használatával a naplókhoz lekérdezéshez korrelációs azonosító:

```Kusto
AzureDiagnostics
| where CorrelationId = 'YOUR_CORRELATION_IDENTIFIER'
```

| Érték lekérdezése | Csere erre |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | A korrelációs Azonosítót, amely az eseményadatok lett megadva |

Ha a felhasználó által definiált függvény jelentkezik, ezeket a naplókat fog megjelenni az Azure Log Analytics-példány kategóriával `UserDefinedFunction`. Kérheti le azokat, az Azure Log Analyticsben adja meg a következő lekérdezési feltétel:

```Kusto
AzureDiagnostics
| where Category = 'UserDefinedFunction'
```

Hatékony lekérdezési műveletekkel kapcsolatos további információkért lásd: [Ismerkedés a lekérdezések](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="identify-common-issues"></a>Gyakori problémák azonosítása

Diagnosztizálás és a gyakori problémák azonosításában is fontosak, a megoldás hibaelhárítása során. Számos gyakori problémát észlelt, amikor a felhasználó által definiált függvények fejlesztése alatti foglalja össze.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="ensure-a-role-assignment-was-created"></a>Győződjön meg arról, szerepkör-hozzárendelés létrehozása

Nélkül szerepkör-hozzárendelés létrehozott felügyeleti API a felhasználó által definiált függvény nem fog tudni hozzáférni minden olyan műveleteket, például az értesítések küldését, metaadatok beolvasása a, és beállítás számított értékek a topológia belül.

Ellenőrizze, hogy a szerepkör-hozzárendelés a felügyeleti API-n keresztül a felhasználó által definiált függvény:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_USER_DEFINED_FUNCTION_ID* | Szerepkör-hozzárendeléseit a lekérdezni kívánt felhasználó által definiált függvény Azonosítóját|

Ha nincsenek szerepkör-hozzárendelés beolvasott, végezze el a cikk [a felhasználó által definiált függvény szerepkör-hozzárendelés létrehozása](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Ha a megfeleltetőben megadott működni fog-e a telemetria-érzékelő

A következő hívást kell végrehajtanunk a digitális Twins Azure-példányok felügyeleti API-t akkor hamarosan meg tudja határozni, ha egy adott megfeleltetőben megadott érvényes az adott érzékelő.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | Az értékelni kívánt megfeleltetőben megadott azonosítója |
| *YOUR_SENSOR_IDENTIFIER* | Az értékelni kívánt érzékelő azonosítója |

Válasz:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>Ellenőrizze az érzékelő vált

Az Azure digitális Twins példányok felügyeleti API a következő hívással fogja meg tudja határozni a felhasználó által definiált függvények, amelyek a bejövő telemetriát az adott érzékelő által kiváltott azonosítók:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Az érzékelő, amely a telemetriát küldő azonosítója |

Válasz:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Az értesítések fogadásának kiadása

Amikor a felhasználó által definiált aktivált függvényen belül értesítései nem fogadó ügyeljen arra, hogy a topológia objektum típusú paraméter megfelelő használt azonosító.

**Helytelen** példa:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Ez a forgatókönyv akkor merül fel, mert a használt azonosító hivatkozik érzékelő, a megadott topológia adatobjektum-típus pedig "Hely".

**Megfelelő** példa:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

Futtassa ezt a hibát a legegyszerűbb módja az, hogy használja a `Notify` metódust a metaadat-objektum.

Példa:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Diagnosztikai gyakori kivételek

Diagnosztikai beállítások engedélyezése, ha ezek a gyakori kivételek merülhetnek fel:

1. **Szabályozás**: Ha a felhasználó által definiált függvény meghaladja-e a feldolgozási korlátok leírt sebességét a [Szolgáltatáskorlátok](./concepts-service-limits.md) a cikkben azt szabályozva lesz. Nincsenek további műveletek a korlátok lejártáig sikeres végrehajtása szabályozás jár.

1. **Nem találhatók adatok**: Ha a felhasználó által definiált függvény megpróbál hozzáférni a metaadatokat, amelyek nem létezik, a művelet sikertelen lesz.

1. **Nem jogosult**: Ha a felhasználó által definiált függvény nem rendelkezik a szerepkör-hozzárendelés beállítása, vagy nem rendelkezik megfelelő engedéllyel az egyes metaadatok elérését a topológia, a művelet sikertelen lesz.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan engedélyezheti [figyelés és a naplók](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) az Azure digitális Twins.
