---
title: Time Series modellben |} A Microsoft Docs
description: Ismertetése Time Series-modell
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 4cb83b61068922002c0c308f4d129a2e069beadd
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856448"
---
# <a name="time-series-model"></a>Idősorozat-modell

Ez a dokumentum ismerteti a **Idősorozat-modell** (TSM) rész az Azure Time Series Insights (TSI) frissítése. Modellelemek a modellben, annak képességeit és hogyan kezdheti el létrehozását és a saját modellek ismerteti.

Az IoT-eszközökről gyűjtött adatok hagyományosan, megnehezítve a keresése, és gyorsan elemezheti az érzékelők környezeti információkat nem rendelkezik. A fő motiváció TSM számára, hogy egyszerűbb keresése és elemzése IoT-adatot válogatott, a karbantartással és a felderítési bővítést az idősorozat-adatok épülő architektúratervezési foglakozásokon fogyasztói használatra kész adatkészleteket engedélyezésével. **Time Series modellek** a lekérdezésekben és navigációs létfontosságú szerepet játszik, mivel azok contextualize eszköz és az eszközre irányuló entitásokat. Adatait a bennük tárolt képletek kihasználva TSM powers time series lekérdezések számításokban megőrzi.

![TSM][1]

## <a name="key-capabilities"></a>Főbb képességek

. A cél legyen a egyszerű és zökkenőmentes time series contextualization kezeléséhez TSM lehetővé teszi, hogy az Azure Time Series Insights (előzetes verzió) található a következő képességekkel:

* Lehetővé teszi az hozhat létre és kezelheti a számítások vagy a képleteket, átalakítja az adatokat kihasználva skaláris függvények, összesített műveletek, stb.
* Szülő-gyermek típusú kapcsolatokat navigációs és referencia kontextusának biztosításához a time series telemetria engedélyezése meghatározása.
* Ezek segítségével hierarchiák létrehozása és a szolgáltatáspéldány-mezők példányok részét társított tulajdonságok megadása.

## <a name="times-series-model-key-components"></a>Túllépi az adatsorozat modell legfontosabb összetevők

Nincsenek TSM három fő összetevői:

* **Time Series modell** *típusok*
* **Time Series modell** *hierarchiák*
* **Time Series modell** *példányok*

## <a name="time-series-model-types"></a>Time Series adatmodell-típusokat

**Time Series modell** *típusok* meghatározó változókat és adatelemzésre számítások képletek engedélyezése és a egy adott TSI-példányhoz társított. A típus lehet egy vagy több változót. Például a TSI-példány típusú lehet **hőmérséklet-érzékelő**, amely a következőkből áll a változók: *átlagos hőmérsékletét*, *min hőmérséklet*, és *maximális hőmérséklet*. Alapértelmezett típussal hozunk létre, az adatok indításakor TSI beérkeznek. Azt is olvassa és beállítások frissítve lett. Alapértelmezett típusok fog rendelkezni, amely az események száma.

## <a name="time-series-model-type-json-request-and-response-example"></a>Idő tanulása típusú kérések és válaszok példa JSON

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/types/$batch?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

A következő JSON szervezet és a változó attribútumokkal:

| Attribútum | Kötelező vagy választható |
| --- | --- |
| **típusa**  |  Szükséges  |
| **filter**  |  Optional |
| **value**  | NULL értékű vagy nincs megadva  |
| **interpolációs**  |  Optional |
| **Összesítés**  |  Szükséges |

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleType",
            "description": "This is type 2",
            "variables": {
                "Avg Temperature": {
                    "kind": "numeric",
                    "filter": null,
                    "value": { "tsx": "$event.temperature.Double" },
                    "interpolation": "None",
                    "aggregation": {"tsx": "avg($value)"}
                },
                "Count Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "count()"}
                },
                "Min Temperature": {
                    "kind": "aggregate",
                    "filter": null,
                    "value": null,
                    "interpolation": "None",
                    "aggregation": {"tsx": "min($event.temperature)"}
                },
            }
        }
    ]
}
```

Válasz:

```JSON
{
    "get": null,
    "put": [
        {
            "timeSeriesType": {
                "id": "fc4f526c-da6e-4b85-87f7-16f6cf9b69be",
                "name": "type2",
                "description": "This is type 2",
                "variables": {
                    "Avg Temperature": {
                        "kind": "numeric",
                        "filter": null,
                        "value": { "tsx": "$event.temperature.Double" },
                        "interpolation": "None",
                        "aggregation": {"tsx": "avg($value)"}
                    },
                    "Count Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "count()"}
                    },
                    "Min Temperature": {
                        "kind": "aggregate",
                        "filter": null,
                        "value": null,
                        "interpolation": "None",
                        "aggregation": {"tsx": "min($event.temperature)"}
                    }
                }
            },
            "error": null
        }
    ]
}
``````

A **alapértelmezett** *típus* JSON-választ:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

## <a name="variables"></a>Változók

Az Azure TSI-típusok változók, a keresztül az események értékeinek ezek nevesített számítások. A TSI változó definíciók olyan képletet, és a számítási szabályokat tartalmaz. Változó-definícióhoz tartozik típusa, érték, szűrő, csökkentésére és határok. Változók TSM definíciójában vannak tárolva, és a tárolt definíció felülbírálása a lekérdezési API-kon keresztül beágyazott adható meg.

Az alábbi mátrix változó definíciók jelmagyarázat módon működik:

![tábla][2]

### <a name="variable-kind"></a>Változó típusa

A következő változó jellegű támogatottak:

* Numerikus – folyamatos

### <a name="variable-filter"></a>Változó szűrő

Változó szűrők adjon meg egy nem kötelező szűrőfeltételt, törölje a számításhoz feltételek alapján minősül sorok számának korlátozására.

### <a name="variable-value"></a>Változó értéke

Változó és számítási célszerű használni. Ez az az oszlop az eseményeket, hogy kell hivatkoznia.

### <a name="variable-interpolation"></a>Változó interpolációs

A folyamat értékek egy halmazát egy időszakonként érték átalakítása változó csökkentését nevezzük. Változó csökkentése lehet összesített rögzített adatokat a forrás- vagy rekonstruált interpolációs és összesítése, vagy a rekonstruált jelek interpolációs használatával, és a mintavételi jelek. Változó határok interpolációs lehet hozzáadni, ezek lehetővé teszik többek között a keresés kívüli események számításokat.

Az Azure TSI (előzetes verzió) támogatja a következő változó interpolációs: `linear`, `stepright`, és `none`.

### <a name="variable-aggregation"></a>Változó összesítés

Az aggregate függvényt a változó lehetővé teszi a számítási része. Ha a változó interpolációs `null` vagy `none`, majd TSI támogatni fogja a szokásos összesítések (nevezetesen, **min**, **maximális**, **átlagos**, **összeg** , és **száma**). Ha a változó interpolációs `stepright` vagy `linear`, majd fogja támogatni a TSI **twmin**, **twmax**, **twavg**, és **twsum**. Interpolációs száma nem adható meg.

## <a name="time-series-model-hierarchies"></a>Time Series modell hierarchiák

Hierarchiák rendszerezéséhez példányok és azok a tulajdonságnevek megadásával. Előfordulhat, hogy egy hierarchián vagy több hierarchiát. Ezenkívül nem szükséges, ezek az adatok aktuális része, de minden példány egy hierarchia leképezhető. Egy TSM példány leképezhet egy hierarchián vagy több hierarchiát.

Hierarchiák által meghatározott **Hierarchiaazonosító**, **neve**, és **forrás**. Hierarchiák elérési utak rendelkezik, egy elérési utat a felhasználó szeretne létrehozni a hierarchiában felülről lefelé szülő-gyermek sorrendjét. A szülő/gyermek tulajdonságok szolgáltatáspéldány-mezők leképezése.

### <a name="time-series-model-hierarchy-json-request-and-response-example"></a>Idő tanulása hierarchia kérések és válaszok példa JSON

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/hierarchies/$batch?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

A JSON-törzse:

```JSON
{
    "get": null,
    "put": [
        {
            "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
            "name": "Location",
            "source": {
                "instanceFieldNames": [
                    "state",
                    "city"
                ]
            }
        }
    ]
}
```

Válasz:

```JSON
{
    "get": null,
    "put": [
        {
            "hierarchy": {
                "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
                "name": "Location",
                "source": {
                    "instanceFieldNames": [
                        "state",
                        "city"
                    ]
                }
            },
            "error": null
        }
    ]
}
```

### <a name="hierarchy-definition-behavior"></a>Hierarchia definíció viselkedése

Vegye figyelembe az alábbi példában, amelyben hierarchia H1 rendelkezik "elkészítése", "floor" és "hely" definíciójához részeként:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

A szolgáltatáspéldány-mezők attól függően, a hierarchia attribútumai és értékei fog megjelenni jelennek meg: 

| Time Series-azonosító | Szolgáltatáspéldány-mezők |
| --- | --- |
| ID1 | "elkészítése" = "1000", "emelet" = "10", "hely" = "55."  |
| ID2 | "elkészítése" = "1000", "hely" = "55." |
| ID3 |  "floor" = "10" |
| MINT AZ ID4 ESETÉBEN | "elkészítése" = "1000", "emelet" = "10"  |
| ID5 | |

A fenti példában ID1 jeleníti meg a felhasználói felület és felhasználói H1 hierarchia részeként többi közben besorolt alatt `Unparented Instances` óta nem felelnek meg a megadott adatok hierarchiába.

## <a name="time-series-model-instances"></a>Time Series modell-példány

Az idősor magukat a példányai. A legtöbb esetben ez lesz a *deviceId* vagy *assetId* , hogy a környezetben az eszköz egyedi azonosítója. A példányok rendelkeznek leíró információkat a hozzájuk társított példány tulajdonságainak neve. Minimális példány tulajdonságai közé tartozik a hierarchiára vonatkozó információk. Akkor hasznos, leíró adatok, például a gyártó, operátor vagy a legutóbbi szolgáltatás dátuma is tartalmazhatnak.

Példányok által meghatározott *timeSeriesId*, *typeId*, *hierarchyId*, és *instanceFields*. Csak egy rendeli minden példány *típus*, és a egy vagy több hierarchiák. Példányok összes tulajdonságok öröklése a hierarchiák, míg további *instanceFields* adhat hozzá további példányt erőforrástulajdonság-definíciót.

*instanceFields* példányát és a statikus adatokat, amely meghatározza egy példányt, tulajdonság. Miközben is támogatja a keresési műveletek végrehajtása az indexelés meghatározzák a hierarchiában, vagy nem hierarchia tulajdonságának értékét.

## <a name="time-series-model-instance-json-request-and-response-example"></a>Idő tanulása példány kérések és válaszok példa JSON

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/instances/$batch?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

A JSON-törzse:

```JSON
{
    "get": null,
    "put": [
        {
            "name": "SampleName",
            "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
            "timeSeriesId": [
                "samplePartitionKeyValueOne"
            ],
            "description": "floor 100",
            "hierarchyIds": [
                "e37a4666-9650-42e6-a6d2-788f12d11158"
            ],
            "instanceFields": {
                "state": "California",
                "city": "Los Angeles"
            }
        }
    ]
}
```

Válasz:

```JSON
{
    "get": null,
    "put": [
        {
            "instance": null,
            "error": null
        },
        {
            "instance": null,
            "error": null
        }
    ]
}
```

## <a name="time-series-model-settings-example"></a>Idő tanulása beállítások példa

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/modelSettings?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

Válasz:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "someType1",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

## <a name="time-series-model-limits"></a>Adatsorozat modell határidők

| Paraméter |   Korlátok |
| --- | --- |
| Adatmodell-entitásokat (típusok, hierarchiákat és példányok) objektum mérete|  32 KB-ot tartalmaz tulajdonságokat |
| Az Azure Portalon konfigurált TSID-tulajdonságként engedélyezett kulcsok |   Max. 3 |
| A környezet típusok maximális száma |    1000|
| V typu változók maximális száma |    50|
| A környezet hierarchiák maximális száma|   32|
| Maximális hierarchia mélysége | 32|
| 1-példányhoz társított hierarchiák maximális száma   |21|
| A környezet a példányok maximális száma |    500,000|
| Szolgáltatáspéldány-mezők példányonként maximális száma |   50|
| A modellekre objektumok upsert/frissítése/törlése művelet / másodperc   |100 / másodperc|
| Idősorozat-modell API-kérelem mérete: kötegelt |  8 MB vagy 1000 adatmodell-objektumokat (amelyik hamarabb következik be)|
| Time Series modell kérés mérete: keresési és javaslat   | 32 KB|
| Idősorozat-modell API-kérelem mérete: kötegelt    | 32 MB|
| Keresés/javaslat 32 MB|  32 MB|

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Olvassa el az új [Idősorozat-modell](./time-series-insights-update-tsm.md).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
