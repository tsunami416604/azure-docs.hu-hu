---
title: Az Azure Time Series Insights - Time Series modellben |} A Microsoft Docs
description: Ismertetése az Azure Time Series Insights Time Series modellben.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2ead7a9a71c0afe72736bef8796107cae42009f1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278185"
---
# <a name="time-series-model"></a>Time Series-modell

Ez a cikk ismerteti az Azure Time Series Insights – előzetes Idősorozat-modell részét. Modellelemek a modellben, annak képességeit és hogyan kezdheti el létrehozását és a saját modellek tárgyalja.

Az IoT-eszközökről gyűjtött adatok hagyományosan környezeti információkat, ami megnehezíti a keresése, és gyorsan elemezheti az érzékelő nem rendelkezik. A fő motiváció Idősorozat-modell számára, hogy egyszerűbb keresése és elemzése IoT-adatot. Ez a cél a válogatott, a karbantartással és a felderítési bővítést az idősorozat-adatok épülő architektúratervezési foglakozásokon fogyasztói használatra kész adatkészleteket engedélyezésével éri el. 

Time Series modellek a lekérdezésekben és navigációs létfontosságú szerepet játszanak, mert azok az eszköz és az eszközre irányuló entitások contextualize. Idősorozat-modellben levő rendelkezik megőrzött adatok idősorozat-lekérdezés számítások működteti a bennük tárolt képletek kihasználásával.

![TSM][1]

## <a name="key-capabilities"></a>Főbb képességek

Idősorozat-modell a cél legyen a egyszerű és zökkenőmentes time series contextualization kezelése, lehetővé teszi a Time Series Insights előzetes verzióban érhető el az alábbi képességeket. Ez segít:

* Hozhat létre és számítások vagy képletek kezelése, skaláris függvények felhasználásával adatok átalakítása műveletek összesített, és így tovább.

* Adja meg a navigációs és hivatkozás engedélyezése és a time series telemetriai kontextusba szülő-gyermek típusú kapcsolatokat.

* Adja meg a példányok részét társított tulajdonságok *mezők példány* és hozhatók létre hierarchiák.

## <a name="times-series-model-key-components"></a>Túllépi az adatsorozat modell legfontosabb összetevők

Idősorozat-modell három fő részből áll:

* Time Series modell *típusok*
* Time Series modell *hierarchiák*
* Time Series modell *példányok*

## <a name="time-series-model-types"></a>Time Series adatmodell-típusokat

Time Series modell *típusok* segítségével meghatározhatja a változók vagy számítások adatelemzésre képleteket. A típusok társítva egy adott Time Series Insights-példányt. A típus lehet egy vagy több változót. Például egy Time Series Insights-példány típusú lehet *hőmérséklet-érzékelő*, amely a következőkből áll a változók *átlagos hőmérsékletét*, *min hőmérséklet*, és *hőmérséklet maximális*. Alapértelmezett típussal hozunk létre, amikor az adatok elindul a tárfiókba kerülnek a Time Series Insights. Az alapértelmezett típus is lekérje és a modell beállítások frissítése. Alapértelmezett típusok rendelkezik, amely az események száma.

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

Idősorozat-modell típusaival kapcsolatos további információkért lásd: a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

## <a name="variables"></a>Változók

Time Series Insights típusok változók, nevesített számítások keresztül az események értékeinek rendelkezik. Time Series Insights változó definíciók olyan képletet, és a számítás szabályokat tartalmaz. Változó-definícióhoz tartozik *kind*, *érték*, *szűrő*, *csökkentési*, és *határok*. Változók vannak tárolva a definíció az Idősorozat-modell, és a tárolt definíció felülbírálása a lekérdezési API-kon keresztül beágyazott adható meg.

A következő mátrix változó definíciók jelmagyarázat módon működik:

![tábla][2]

### <a name="variable-kind"></a>Változó típusa

A következő változó jellegű támogatottak:

* *numerikus*
* *Összesítés*

### <a name="variable-filter"></a>Változó szűrő

Változó szűrők adjon meg egy nem kötelező szűrőfeltételt, törölje a számításhoz feltételek alapján minősül sorok számának korlátozására.

### <a name="variable-value"></a>Változó értéke

Változó és számítási célszerű használni. Ez az az oszlop az eseményeket, hogy kell hivatkoznia.

### <a name="variable-aggregation"></a>Változó összesítés

A változó az aggregate függvényt lehetővé teszi a számítási része. A Time Series Insights támogatja reguláris összesítések (nevezetesen *min*, *maximális*, *átlagos*, *sum*, és *száma*).

## <a name="time-series-model-hierarchies"></a>Time Series modell hierarchiák

Hierarchiák rendszerezéséhez példányok és azok a tulajdonságnevek megadásával. Előfordulhat, hogy egy hierarchián vagy több hierarchiát. Nem szükséges az adatok aktuális része, de minden példány egy hierarchia leképezhető. Egy Idősorozat-modell-példány leképezhet egy hierarchián vagy több hierarchiát.

Hierarchiák által meghatározott *Hierarchiaazonosító*, *neve*, és *forrás*. Hierarchiák rendelkezik egy elérési utat, amely a felhasználók létre szeretné hozni a hierarchiában felülről lefelé szülő-gyermek sorrendjét. A szülő-gyermek típusú tulajdonságok térkép *mezők példány*.

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

Idősorozat-modell hierarchiák kapcsolatos további információkért lásd: a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-definition-behavior"></a>Hierarchia definíció viselkedése

Vegye figyelembe az alábbi példában, ahol a H1 hierarchia már *létrehozásához*, *emelet*, és *szoba* definíciójához részeként:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Attól függően, a *mezők példány*, a hierarchia attribútumai és értékei jelennek meg az alábbi táblázatban látható módon: 

| Idősorozat azonosítója | Szolgáltatáspéldány-mezők |
| --- | --- |
| ID1 | "elkészítése" = "1000", "emelet" = "10", "hely" = "55."  |
| ID2 | "elkészítése" = "1000", "hely" = "55." |
| ID3 | "floor" = "10" |
| MINT AZ ID4 ESETÉBEN | "elkészítése" = "1000", "emelet" = "10"  |
| ID5 | |

Az előző példában ID1 megjeleníti a hierarchiában H1 felhasználói felület és felhasználói részeként, és a többi szerint besorolt *fölérendelt objektum nélküli példányok* mivel azok nem megfelelnek a megadott hierarchiát.

## <a name="time-series-model-instances"></a>Time Series modell-példány

Az idősor magukat a példányai. A legtöbb esetben a *deviceId* vagy *assetId* a környezetben az eszköz egyedi azonosítója. A példányok rendelkeznek leíró információkat a hozzájuk társított példány tulajdonságainak neve. Minimális példány tulajdonságai közé tartozik a hierarchiára vonatkozó információk. Akkor hasznos, leíró adatok, például a gyártó, operátor vagy a legutóbbi szolgáltatás dátuma is tartalmazhatnak.

Példányok által meghatározott *timeSeriesId*, *typeId*, *hierarchyId*, és *instanceFields*. Csak egy rendeli minden példány *típus*, és a egy vagy több hierarchiák. Hierarchiák, és további példányok öröklik az összes tulajdonság *instanceFields* adhat hozzá további példányt erőforrástulajdonság-definíciót.

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

Idősorozat-modell példányok kapcsolatos további információkért lásd: a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

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

Idősorozat-modell beállításaival kapcsolatos további információkért lásd: a [Referenciadokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>További lépések

Lásd: [storage Azure Time Series Insights előzetes verziója a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Megjelenik az új [Idősorozat-modell](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).

<!-- Images -->
[1]: media/v2-update-tsm/tsm.png
[2]: media/v2-update-tsm/table.png
