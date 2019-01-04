---
title: Az Azure digitális Twins felhasználói függvények hibakeresése |} A Microsoft Docs
description: Az Azure digitális Twins felhasználói függvények hibakeresése kapcsolatos útmutató arra az esetre.
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: stefanmsft
ms.custom: seodec18
ms.openlocfilehash: e373e7c3ca83a0200cd1b6b945c5e4cb43b77a51
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974862"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Az Azure digitális Twins felhasználó által definiált függvények hibakeresése

Ez a cikk összefoglalja, hogyan diagnosztizálhatja a felhasználó által definiált függvények. Ezután azonosítja néhány a leggyakoribb forgatókönyvek tűnhetnek, amikor a velük végzett munkát.

>[!TIP]
> Olvasási [figyelés és naplózás konfigurálása](./how-to-configure-monitoring.md) tudhat meg többet a hibakeresést az Azure digitális Twins tevékenységeket tartalmazó naplók, a diagnosztikai naplók és az Azure Monitor használatával eszközök beállítása.

## <a name="debug-issues"></a>Hibakeresés

Hogyan diagnosztizálhatja a belül az Azure digitális Twins-példány felmerülő problémákat, hogy támogatási hatékonyan azonosíthatja a problémát, a probléma, és a egy megoldás okát.

### <a name="enable-log-analytics-for-your-instance"></a>A példány a log analytics engedélyezése

Naplók és mérőszámok az Azure digitális Twins-példány az Azure monitoron keresztül érhetők el. Az alábbi dokumentáció azt feltételezi, hogy létrehozott egy [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) munkaterületen a [az Azure Portal](../azure-monitor/learn/quick-create-workspace.md)segítségével, [Azure CLI-vel](../azure-monitor/learn/quick-create-workspace-cli.md), vagy a Kiszolgálókezelő [ PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> 5 perces késleltetés során az események küldése az Azure Log Analytics az első alkalommal tapasztalhat.

Figyelés és naplózás az Azure digitális Twins erőforrások konfigurálásához, olvassa el a [figyelés és naplózás konfigurálása](./how-to-configure-monitoring.md).

A cikk a [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../azure-monitor/platform/diagnostic-logs-overview.md) diagnosztikai napló beállításait az Azure digitális Twins-példány az Azure Portal, az Azure CLI vagy a Powershellen keresztül átfogó áttekintése.

>[!IMPORTANT]
> Ellenőrizze, hogy válassza ki az összes naplókategóriák, mérőszámok és az Azure Log Analytics-munkaterületet.

### <a name="trace-sensor-telemetry"></a>Mozgásérzékelő – híváslánc-telemetria

Győződjön meg arról, hogy diagnosztikai beállítások engedélyezve van az Azure digitális Twins-példányon, az összes napló kategória ki van jelölve, és, hogy a naplók az Azure Log Analytics kap.

Egyezik meg a megfelelő naplók érzékelő telemetriai üzenetet, adja meg a korrelációs Azonosítót meg az eseményadatokat küld a rendszer. Ehhez állítsa a `x-ms-client-request-id` tulajdonságát egy GUID Azonosítót.

Telemetria küldése után nyissa meg Azure Log Analytics használatával a naplókhoz lekérdezéshez korrelációs azonosító:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Érték lekérdezése | Csere erre |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | A korrelációs Azonosítót, amely az eseményadatok lett megadva |

Ha a felhasználó által definiált függvény jelentkezik, ezeket a naplókat fog megjelenni az Azure Log Analytics-példány kategóriával `UserDefinedFunction`. Kérheti le azokat, az Azure Log Analyticsben adja meg a következő lekérdezési feltétel:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Hatékony lekérdezési műveletekkel kapcsolatos további információkért olvassa el [Ismerkedés a lekérdezések](../azure-monitor/log-query/get-started-queries.md).

## <a name="identify-common-issues"></a>Gyakori problémák azonosítása

Diagnosztizálás és a gyakori problémák azonosításában is fontosak, a megoldás hibaelhárítása során. Több felhasználó által definiált függvények fejlesztése során gyakran felmerülő problémák alábbi foglalja össze.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="ensure-a-role-assignment-was-created"></a>Győződjön meg arról, szerepkör-hozzárendelés létrehozása

Nélkül szerepkör-hozzárendelés létrehozott felügyeleti API a felhasználó által definiált függvény nem fog tudni hozzáférni minden olyan műveleteket, például az értesítések küldését, metaadatok beolvasása a, és beállítás számított értékek a topológia belül.

Ellenőrizze, hogy a szerepkör-hozzárendelés a felügyeleti API-n keresztül a felhasználó által definiált függvény:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Paraméter értéke | Csere erre |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | Szerepkör-hozzárendeléseit a lekérdezni kívánt felhasználó által definiált függvény Azonosítóját|

Ismerje meg, [a felhasználó által definiált függvény szerepkör-hozzárendelés létrehozása](./how-to-user-defined-functions.md), ha szerepkör-hozzárendelések nem létezik.

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

Ebben a forgatókönyvben merül fel, mert a használt azonosító hivatkozik érzékelő, míg a topológia objektumtípus megadott `Space`.

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

Ismerje meg, hogyan engedélyezheti [figyelés és a naplók](../azure-monitor/platform/activity-logs-overview.md) az Azure digitális Twins.
