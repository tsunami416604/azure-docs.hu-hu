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
ms.date: 12/04/2018
ms.openlocfilehash: 5d5f94aebcd55474385e903246ce7945586456dd
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890415"
---
# <a name="time-series-model"></a>Time Series-modell

Ez a dokumentum ismerteti a **Idősorozat-modell** (TSM) rész az Azure Time Series Insights (TSI) frissítése. Modellelemek a modellben, annak képességeit és hogyan kezdheti el létrehozását és a saját modellek ismerteti.

Az IoT-eszközökről gyűjtött adatok hagyományosan, megnehezítve a keresése, és gyorsan elemezheti az érzékelők környezeti információkat nem rendelkezik. A fő motiváció TSM számára, hogy egyszerűbb keresése és elemzése IoT-adatot válogatott, a karbantartással és a felderítési bővítést az idősorozat-adatok épülő architektúratervezési foglakozásokon fogyasztói használatra kész adatkészleteket engedélyezésével. TSMs a lekérdezésekben és navigációs létfontosságú szerepet játszik, mivel azok contextualize eszköz és az eszközre irányuló entitásokat. Adatait a bennük tárolt képletek kihasználva TSM powers time series lekérdezések számításokban megőrzi.

![TSM][1]

## <a name="key-capabilities"></a>Főbb képességek

. A cél legyen a egyszerű és zökkenőmentes kezelheti a time series contextualization TSM lehetővé teszi a az Azure TSI-ben (előzetes verzió) a következő képességekkel:

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

## <a name="time-series-model-type-json-example"></a>Time Series modell típusa példa JSON

Minta:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
``````

További információk a származó Idősorozat-modell típust a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Változók

Az Azure TSI-típusok változók, nevesített számítások keresztül az események értékeinek rendelkezik. A TSI változó definíciók olyan képletet, és a számítási szabályokat tartalmaz. Változó-definícióhoz tartozik típusa, érték, szűrő, csökkentésére és határok. Változók TSM definíciójában vannak tárolva, és a tárolt definíció felülbírálása a lekérdezési API-kon keresztül beágyazott adható meg.

Az alábbi mátrix változó definíciók jelmagyarázat módon működik:

![tábla][2]

### <a name="variable-kind"></a>Változó típusa

A következő változó jellegű támogatottak:

* Numerikus
* Összesítés

### <a name="variable-filter"></a>Változó szűrő

Változó szűrők adjon meg egy nem kötelező szűrőfeltételt, törölje a számításhoz feltételek alapján minősül sorok számának korlátozására.

### <a name="variable-value"></a>Változó értéke

Változó és számítási célszerű használni. Ez az az oszlop az eseményeket, hogy kell hivatkoznia.

### <a name="variable-aggregation"></a>Változó összesítés

Az aggregate függvényt a változó lehetővé teszi a számítási része. A TSI támogatni fogja a szokásos összesítések (nevezetesen, **min**, **maximális**, **átlagos**, **sum**, és **száma**).

## <a name="time-series-model-hierarchies"></a>Time Series modell hierarchiák

Hierarchiák rendszerezéséhez példányok és azok a tulajdonságnevek megadásával. Előfordulhat, hogy egy hierarchián vagy több hierarchiát. Ezenkívül nem szükséges, ezek az adatok aktuális része, de minden példány egy hierarchia leképezhető. Egy TSM példány leképezhet egy hierarchián vagy több hierarchiát.

Hierarchiák által meghatározott **Hierarchiaazonosító**, **neve**, és **forrás**. Hierarchiák elérési utak rendelkezik, egy elérési utat a felhasználó szeretne létrehozni a hierarchiában felülről lefelé szülő-gyermek sorrendjét. A szülő/gyermek tulajdonságok szolgáltatáspéldány-mezők leképezése.

### <a name="time-series-model-hierarchy-json-example"></a>Time Series modell hierarchia példa JSON

Minta:

```JSON
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
```

További információt az Idősorozat-modell hierarchiát a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

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

Az idősor magukat a példányai. A legtöbb esetben a *deviceId* vagy *assetId* lesz a környezetben az eszköz egyedi azonosítója. A példányok rendelkeznek leíró információkat a hozzájuk társított példány tulajdonságainak neve. Minimális példány tulajdonságai közé tartozik a hierarchiára vonatkozó információk. Akkor hasznos, leíró adatok, például a gyártó, operátor vagy a legutóbbi szolgáltatás dátuma is tartalmazhatnak.

Példányok által meghatározott *timeSeriesId*, *typeId*, *hierarchyId*, és *instanceFields*. Csak egy rendeli minden példány *típus*, és a egy vagy több hierarchiák. Példányok összes tulajdonságok öröklése a hierarchiák, míg további *instanceFields* adhat hozzá további példányt erőforrástulajdonság-definíciót.

*instanceFields* példányát és a statikus adatokat, amely meghatározza egy példányt, tulajdonság. Miközben is támogatja a keresési műveletek végrehajtása az indexelés meghatározzák a hierarchiában, vagy nem hierarchia tulajdonságának értékét.

## <a name="time-series-model-instance-json-example"></a>Time Series modell példány példa JSON

Minta:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

További információt az Idősorozat-modell példányok a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-settings-example"></a>Idő tanulása beállítások példa

Minta:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Az Idősorozat-modell beállításokkal kapcsolatos további információkért olvassa el a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Olvassa el az új [idősorozat-modell](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
