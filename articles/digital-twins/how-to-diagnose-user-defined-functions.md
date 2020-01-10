---
title: UDF hibakeresése – Azure digitális Twins | Microsoft Docs
description: További információ a felhasználó által definiált függvények Azure digitális Ikrekben való hibakereséséhez ajánlott módszerekről.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: f963885f60116bf23087fceb6a92d18e71ac7f24
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745335"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények hibakeresése az Azure Digital Ikrekben

Ez a cikk összefoglalja, hogyan diagnosztizálhatja és hibakeresést végezhet a felhasználó által definiált függvények között az Azure digitális Twins-ban. Ezt követően azonosítja a leggyakoribb forgatókönyvek némelyikét a hibakeresés során.

>[!TIP]
> Olvassa el, [hogyan konfigurálhatja a figyelést és a naplózást](./how-to-configure-monitoring.md) , ha többet szeretne megtudni a hibakeresési eszközök Azure digitális Twins-beli beállításáról a tevékenységek naplói, a diagnosztikai naplók és a Azure monitor használatával.

## <a name="debug-issues"></a>Hibakeresési problémák

Az Azure Digital Twins-beli problémák diagnosztizálásának ismerete lehetővé teszi a problémák hatékony elemzését, a problémák okának azonosítását, valamint a megfelelő megoldások megadását.

Erre a célból számos naplózási, elemzési és diagnosztikai eszközt biztosítunk.

### <a name="enable-logging-for-your-instance"></a>A példány naplózásának engedélyezése

Az Azure digitális Twins támogatja a robusztus naplózást, monitorozást és elemzést. A megoldások fejlesztői Azure Monitor naplókat, diagnosztikai naplókat, tevékenységi naplókat és más szolgáltatásokat használhatnak a IoT-alkalmazások összetett figyelési igényeinek támogatásához. A naplózási lehetőségek kombinálhatók több szolgáltatás rekordjainak lekérdezéséhez és megjelenítéséhez, valamint számos szolgáltatás részletes naplózási lefedettségének biztosításához.

* Az Azure Digital Twins-specifikus naplózási konfigurációhoz olvassa el a [figyelés és naplózás konfigurálását ismertető témakört](./how-to-configure-monitoring.md).
* Tekintse át a [Azure monitor](../azure-monitor/overview.md) áttekintését, és ismerkedjen meg a Azure monitor használatával engedélyezett hatékony naplózási beállításokkal.
* Tekintse át az Azure- [erőforrások naplózási adatainak gyűjtésére és felhasználására szolgáló adatokat](../azure-monitor/platform/platform-logs-overview.md) az Azure Digital Twins diagnosztikai napló beállításainak konfigurálásához a Azure Portal, az Azure CLI vagy a PowerShell használatával.

A konfigurálást követően kiválaszthatja az összes naplózási kategóriát, metrikákat, és hatékony Azure Monitor log Analytics-munkaterületeket használhat a hibakeresési erőfeszítések támogatásához.

### <a name="trace-sensor-telemetry"></a>Nyomkövetési érzékelő telemetria

Az érzékelő telemetria nyomon követéséhez ellenőrizze, hogy a diagnosztikai beállítások engedélyezve vannak-e az Azure Digital Twins-példányhoz. Ezután győződjön meg arról, hogy az összes kívánt naplózási kategória ki van választva. Végül ellenőrizze, hogy a rendszer elküldje-e a kívánt naplókat Azure Monitor naplókba.

Ahhoz, hogy az érzékelő telemetria az adott naplóba, megadhat egy korrelációs azonosítót az elküldött esemény adataihoz. Ehhez állítsa a `x-ms-client-request-id` tulajdonságot egy GUID azonosítóra.

A telemetria elküldése után nyissa meg Azure Monitor log Analytics szolgáltatást a naplók lekérdezéséhez a korrelációs azonosító beállításával:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Lekérdezés értéke | Csere erre |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Az esemény-adathalmazban megadott korrelációs azonosító |

Az összes legutóbbi telemetria-napló lekérdezésének megtekintéséhez:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Ha engedélyezi a naplózást a felhasználó által definiált függvény számára, ezek a naplók a log Analytics-példányban jelennek meg a `UserDefinedFunction`kategóriával. A beolvasáshoz adja meg a következő lekérdezési feltételt a log Analyticsben:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

A hatékony lekérdezési műveletekkel kapcsolatos további információkért olvassa el a [lekérdezések első lépéseivel foglalkozó](../azure-monitor/log-query/get-started-queries.md)témakört.

## <a name="identify-common-issues"></a>Gyakori problémák azonosítása

A megoldás hibaelhárításakor a gyakori problémák diagnosztizálása és azonosítása is fontos. A felhasználó által definiált függvények fejlesztésekor gyakran előforduló problémák a következő alszakaszokban vannak összegezve.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Szerepkör-hozzárendelés létrehozásának engedélyezése

A felügyeleti API-n belül létrehozott szerepkör-hozzárendelés nélkül a felhasználó által definiált függvény nem rendelkezik hozzáféréssel olyan műveletek elvégzéséhez, mint például az értesítések küldése, a metaadatok beolvasása és a számított értékek beállítása a topológián belül.

Ellenőrizze, hogy létezik-e szerepkör-hozzárendelés a felhasználó által definiált függvényhez a felügyeleti API-n keresztül:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Paraméter értéke | Csere erre |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | A felhasználó által definiált függvény azonosítója a szerepkör-hozzárendelések lekéréséhez a következőhöz:|

Megtudhatja, [hogyan hozhat létre szerepkör-hozzárendelést a felhasználó által definiált függvényhez](./how-to-user-defined-functions.md), ha nem létezik szerepkör-hozzárendelés.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Ellenőrizze, hogy az Matcher működik-e az érzékelő telemetria

A következő hívással az Azure digitális Twins-példányok felügyeleti API-jával meghatározhatja, hogy egy adott Matcher vonatkozik-e az adott érzékelőre.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A kiértékelni kívánt Matcher azonosítója |
| *YOUR_SENSOR_IDENTIFIER* | A kiértékelni kívánt érzékelő azonosítója |

Válasz:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Az érzékelő eseményindítóinak megkeresése

A következő hívással az Azure digitális Twins felügyeleti API-jai segítségével meghatározhatja az adott érzékelő bejövő telemetria által aktivált, felhasználó által definiált függvények azonosítóit:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | Az telemetria elküldeni kívánt érzékelő azonosítója |

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

### <a name="issue-with-receiving-notifications"></a>Értesítés fogadásával kapcsolatos probléma

Ha nem kap értesítéseket az aktivált felhasználó által definiált függvényből, ellenőrizze, hogy a topológia objektumtípus-paramétere megegyezik-e a használt azonosító típusával.

**Helytelen** Például

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Ez a forgatókönyv azért fordul elő, mert a használt azonosító egy érzékelőre hivatkozik, amikor a megadott topológiai objektumtípus `Space`.

**Helyes** Például

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A probléma legegyszerűbb módja, ha a metaadat-objektumon lévő `Notify` metódust használja.

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

## <a name="common-diagnostic-exceptions"></a>Gyakori diagnosztikai kivételek

Ha engedélyezi a diagnosztikai beállításokat, a következő gyakori kivételek merülhetnek fel:

1. **Szabályozás**: Ha a felhasználó által definiált függvény meghaladja a [szolgáltatási korlátok](./concepts-service-limits.md) című cikkben ismertetett végrehajtási arányt, a rendszer szabályozza a szabályozást. A szabályozási korlátok lejárta előtt nem sikerült végrehajtani a további műveleteket.

1. **Nem találhatók adatok**: Ha a felhasználó által definiált függvény megpróbál hozzáférni a nem létező metaadatokhoz, a művelet sikertelen lesz.

1. **Nem engedélyezett**: Ha a felhasználó által definiált függvény nem rendelkezik szerepkör-hozzárendelési készlettel, vagy nem áll rendelkezésre elegendő engedélye bizonyos metaadatok elérésére a topológiából, a művelet meghiúsul.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan engedélyezheti a [monitorozást és a naplókat](./how-to-configure-monitoring.md) az Azure digitális Twins szolgáltatásban.

- További Azure-naplózási lehetőségekért olvassa el az [Azure-tevékenység naplójának áttekintését ismertető](../azure-monitor/platform/platform-logs-overview.md) cikket.
