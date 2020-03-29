---
title: UdF-ek hibakeresése - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg a felhasználó által definiált függvények hibakeresésének ajánlott megközelítéseit az Azure Digital Twins szolgáltatásban.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 518383488aa878dab75aec7ad5da664332b62ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76511637"
---
# <a name="how-to-debug-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények hibakeresése az Azure Digital Twins szolgáltatásban

Ez a cikk összefoglalja, hogyan diagnosztizálhatja és debug felhasználó által definiált függvények az Azure Digital Twins. Ezután azonosítja a hibakeresés során talált leggyakoribb forgatókönyveket.

>[!TIP]
> Olvassa [el A figyelés és a naplózás konfigurálása című,](./how-to-configure-monitoring.md) a hibakeresési eszközök beállításáról az Azure digitális twins-ben a tevékenységnaplók, a diagnosztikai naplók és az Azure Monitor használatával.

## <a name="debug-issues"></a>Hibakeresési problémák

Az Azure Digital Twins-en belüli problémák diagnosztizálásának megismerése lehetővé teszi a problémák hatékony elemzését, a problémák okainak azonosítását és a megfelelő megoldások biztosítását.

Erre a célból számos naplózási, elemzési és diagnosztikai eszköz áll rendelkezésre.

### <a name="enable-logging-for-your-instance"></a>Naplózás engedélyezése a példányhoz

Az Azure Digital Twins támogatja a robusztus naplózást, figyelést és elemzést. A megoldások fejlesztői az Azure Monitor-naplók, diagnosztikai naplók, tevékenységnaplók és egyéb szolgáltatások segítségével támogathatják az IoT-alkalmazások összetett figyelési igényeit. A naplózási beállítások kombinálhatók a rekordok lekérdezéséhez vagy megjelenítéséhez számos szolgáltatásban, és részletes naplózási lefedettséget biztosíthatszámos szolgáltatás számára.

* Az Azure Digital Twins-re jellemző konfiguráció naplózásáról olvassa el [a Figyelés és naplózás konfigurálása](./how-to-configure-monitoring.md)című.
* Tekintse meg az [Azure Monitor](../azure-monitor/overview.md) áttekintését az Azure Monitoron keresztül engedélyezett hatékony naplóbeállításokról.
* Tekintse át a cikk [naplózási adatok gyűjtése és felhasználása az Azure-erőforrások](../azure-monitor/platform/platform-logs-overview.md) diagnosztikai naplóbeállítások konfigurálása az Azure Digital Twins az Azure Portalon keresztül, az Azure CLI vagy a PowerShell.

Konfigurálás után kiválaszthatja az összes naplókategóriát, metrikát, és hatékony Azure Monitor-naplóelemzési munkaterületeket használhat a hibakeresési erőfeszítések támogatásához.

### <a name="trace-sensor-telemetry"></a>Nyomkövetési érzékelő telemetriai adatai

Az érzékelő telemetriai adatainak nyomon követéséhez ellenőrizze, hogy engedélyezve vannak-e a diagnosztikai beállítások az Azure Digital Twins-példányhoz. Ezután győződjön meg arról, hogy az összes kívánt naplókategória ki van jelölve. Végül ellenőrizze, hogy a kívánt naplók küldése az Azure Monitor naplók.

Ha egy érzékelő telemetriai üzenetet a megfelelő naplók, megadhatja a korrelációs azonosítót az elküldött esemény adatokat. Ehhez állítsa a `x-ms-client-request-id` tulajdonságot GUID azonosítóra.

Telemetriai adatok elküldése után nyissa meg az Azure Monitor naplóelemzési adatait a naplók lekérdezéséhez a korrelációs azonosító használatával:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Lekérdezési érték | Csere erre |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | Az eseményadatokban megadott korrelációs azonosító |

Az összes legutóbbi telemetriai napló lekérdezésének olvasása:

```Kusto
AzureDiagnostics
| order by CorrelationId desc
```

Ha engedélyezi a naplózást a felhasználó által definiált függvényhez, ezek `UserDefinedFunction`a naplók megjelennek a naplóelemzési példányban a kategóriával. A beolvasásukhoz írja be a következő lekérdezési feltételt a naplóelemzésben:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

A hatékony lekérdezési műveletekről további információt az [Első lépések a lekérdezésekkel című szövegben](../azure-monitor/log-query/get-started-queries.md)talál.

## <a name="identify-common-issues"></a>Azonosítsa a gyakori problémákat

A megoldás diagnosztizálása és azonosítása egyaránt fontos a gyakori problémák diagnosztizálása és azonosítása. A felhasználó által definiált függvények fejlesztése során gyakran előforduló problémákat a következő alszakaszok foglaljuk össze.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="check-if-a-role-assignment-was-created"></a>Annak ellenőrzése, hogy létrejött-e szerepkör-hozzárendelés

A Felügyeleti API-n belül létrehozott szerepkör-hozzárendelés nélkül a felhasználó által definiált függvény nem rendelkezik hozzáféréssel olyan műveletek végrehajtásához, mint például az értesítések küldése, a metaadatok beolvasása és a számított értékek beállítása a topológián belül.

Ellenőrizze, hogy létezik-e szerepkör-hozzárendelés a felhasználó által definiált függvényhez a Felügyeleti API-n keresztül:

```URL
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Paraméter értéke | Csere erre |
| --- | --- |
| YOUR_USER_DEFINED_FUNCTION_ID | A felhasználó által definiált függvény azonosítója a szerepkör-hozzárendelések lekéréséhez|

[További információ: Szerepkör-hozzárendelés létrehozása a felhasználó által definiált függvényhez,](./how-to-user-defined-functions.md)ha nincsenek szerepkör-hozzárendelések.

### <a name="check-if-the-matcher-works-for-a-sensors-telemetry"></a>Annak ellenőrzése, hogy a gyufás működik-e az érzékelő telemetriai adataihoz

A következő hívás az Azure Digital Twins-példányok felügyeleti API-ja ellen, meg tudja határozni, hogy egy adott egyező vonatkozik-e az adott érzékelőre.

```URL
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A kiértékelni kívánt egyező azonosítója |
| *YOUR_SENSOR_IDENTIFIER* | A értékelni kívánt érzékelő azonosítója |

Válasz:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-triggers"></a>Ellenőrizze, hogy egy érzékelő mit indít el

Az Azure Digital Twins Management API-k elleni következő hívással meghatározhatja az adott érzékelő bejövő telemetriai adatai által aktivált, felhasználó által definiált függvények azonosítóit:

```URL
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | A telemetriai adatok küldéséhez a szenzor azonosítója |

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

### <a name="issue-with-receiving-notifications"></a>Probléma az értesítések fogadásával

Ha nem kap értesítéseket az aktivált felhasználó által definiált függvénytől, ellenőrizze, hogy a topológiaobjektum-típus paramétere megegyezik-e a használt azonosítótípussal.

**Helytelen** Példa:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Ez a forgatókönyv azért merül fel, mert a használt azonosító egy `Space`érzékelőre hivatkozik, míg a megadott topológiaobjektum-típus .

**Helyes** Példa:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A probléma nem fursálásának legegyszerűbb `Notify` módja a metaadat-objektumon lévő módszer használata.

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

Ha engedélyezi a diagnosztikai beállításokat, a következő gyakori kivételekkel találkozhat:

1. **Szabályozás:** ha a felhasználó által definiált függvény meghaladja a [Service Limits](./concepts-service-limits.md) cikkben ismertetett végrehajtási sebességkorlátokat, a rendszer szabályozza. A további műveletek végrehajtása nem történik meg, amíg a szabályozási korlátok le nem járnak.

1. **Nem található adatok:** ha a felhasználó által definiált függvény nem létező metaadatokhoz próbál hozzáférni, a művelet sikertelen lesz.

1. **Nincs engedélyezve:** Ha a felhasználó által definiált függvény nem rendelkezik szerepkör-hozzárendelési készlettel, vagy nem rendelkezik elegendő engedéllyel bizonyos metaadatok eléréséhez a topológiából, a művelet sikertelen lesz.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan engedélyezheti [a figyelést és](./how-to-configure-monitoring.md) a naplókat az Azure Digital Twins-ben.

- További Azure-naplózási beállításokért olvassa el [az Azure-tevékenységnapló áttekintése](../azure-monitor/platform/platform-logs-overview.md) című cikket.
