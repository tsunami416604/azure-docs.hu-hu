---
title: Idősorozat-modell a Azure Time Series Insights Preview-ban | Microsoft Docs
description: Az Azure Time Series Insights Time Series-modell ismertetése.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 2a740d8ee0eb50cfa01f36bd8f5590a58e1e6627
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931928"
---
# <a name="time-series-model"></a>Time Series-modell

Ez a cikk a Azure Time Series Insights előzetes verziójának idősorozat-modell részét ismerteti. Maga a modell, a képességei, valamint a saját modellek létrehozásának és frissítésének megkezdése.

Hagyományosan a IoT-eszközökről összegyűjtött adatok nem rendelkeznek környezetfüggő információkkal, ami megnehezíti az érzékelők gyors megtalálását és elemzését. A Time Series-modell fő motivációja a IoT-adatok keresésének és elemzésének egyszerűsítése. Ezt a célt úgy éri el, hogy lehetővé teszi a Time Series-adatokat a kurátorok, a karbantartás és az idősorozat-adathalmazok gazdagítása érdekében.

Az idősorozat-modellek fontos szerepet játszanak a lekérdezésekben és a navigációban, mivel azok contextualize az eszköz-és a nem eszköz entitásokat. A idősorozat-modellben megőrzött adatok az idősorozat lekérdezési számítási feltételeit a bennük tárolt képletek kihasználásával.

[![Idősorozat-modell áttekintése](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Főbb képességek

Azzal a céllal, hogy egyszerű és könnyedebb legyen a Time Series contextualization kezelése, az idősorozat-modell a következő képességeket teszi lehetővé Time Series Insights előzetes verzióban. A következőkkel segíti Önt:

* Számítások vagy képletek létrehozása és kezelése, az adatok átalakítása skaláris függvények, összesítő műveletek és így tovább.
* Adja meg a szülő-gyermek kapcsolatokat a Navigálás és a hivatkozás engedélyezéséhez, valamint az idősorozat-telemetria környezetének biztosításához.
* Adja meg a példányok példányaihoz tartozó példányok részét képező tulajdonságokat, és használja őket a hierarchiák létrehozásához.

## <a name="entity-components"></a>Entitás-összetevők

Az idősorozat-modellek három alapvető összetevővel rendelkeznek:

* <a href="#time-series-model-types">Idősorozat-modell típusai</a>
* <a href="#time-series-model-hierarchies">Idősorozat-modell hierarchiái</a>
* <a href="#time-series-model-instances">Idősorozat-modell példányai</a>

Ezek az összetevők össze vannak egyesítve egy idősorozat-modell megadásához és a Azure Time Series Insights adatai rendszerezéséhez.

## <a name="time-series-model-types"></a>Idősorozat-modell típusai

Az idősorozat -modellek segítségével változók vagy képletek definiálhatók a számítások végrehajtásához. A típusok egy adott Time Series Insights-példánnyal vannak társítva. A típus lehet egy vagy több változót. Egy Time Series Insights példány lehet például egy *hőmérséklet-érzékelő*, amely az *átlagos hőmérséklet*, a *minimális hőmérséklet*és a *maximális hőmérséklet*értéket tartalmazza. Egy alapértelmezett típust hozunk létre, amikor az adatforgalom bekerül Time Series Insightsba. Az alapértelmezett típus a modell beállításaiból kérhető le és frissíthető. Az alapértelmezett típusok olyan változóval rendelkeznek, amely megszámolja az események számát.

### <a name="time-series-model-type-json-example"></a>Idősorozat-modell típusú JSON-példa

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
```

Az idősorozat-modell típusaival kapcsolatos további információkért tekintse meg a [dokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="variables"></a>Változók

Time Series Insights típusok változókkal rendelkeznek, amelyek az események értékei alapján számítják ki a számításokat. Time Series Insights a változók képleteket és számítási szabályokat tartalmaznak. A változók definíciói a következők lehetnek: *fajta*, *érték*, *szűrő*, *csökkentés*és *határvonalak*. A változók tárolása a Time Series-modell definíciójában történik, és a lekérdezési API-kon keresztül is biztosítható a tárolt definíció felülbírálásához.

A következő mátrix jelmagyarázatként működik a változók definíciói esetében:

[![Változó definíciós táblázatának beírása](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Meghatározás | Leírás |
| --- | ---|
| Változó típusa |  A *numerikus* és az *aggregált* típus támogatott |
| Változó szűrő | A változók szűrői nem kötelező szűrési záradékot határoznak meg, amely korlátozza a számítási feltételek alapján számításba veendő sorok számát. |
| Változó értéke | A változó értékek a számításokban használatosak. A szóban forgó adatpontra hivatkozó megfelelő mező. |
| Változó összesítése | A változó összesítő funkciója lehetővé teszi a számítás részét. Time Series Insights támogatja a rendszeres összesítéseket (azaz a *min*, a *Max*, az *AVG*, a *Sum*és a *Count*). |

## <a name="time-series-model-hierarchies"></a>Idősorozat-modell hierarchiái

A hierarchiák a tulajdonságok nevét és a kapcsolataik megadásával rendezik a példányokat. Lehet, hogy egyetlen hierarchiával vagy több hierarchiával rendelkezik. Nem kell az adatainak aktuális részét képezniük, de minden példánynak egy hierarchiához kell tartoznia. Az idősorozat-modell példányai egyetlen hierarchiához vagy több hierarchiához képezhetők le.

A hierarchiákat a *hierarchia azonosítója*, *neve*és *forrása*határozza meg. A hierarchiák elérési útja, amely a felhasználók által létrehozni kívánt hierarchia legfelső szintű szülő-gyermek sorrendje. A szülő-gyermek tulajdonságok hozzárendelésének *mezői*.

### <a name="time-series-model-hierarchy-json-example"></a>Idősorozat-modell-hierarchia JSON-példája

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

Az idősorozat-modell hierarchiákkal kapcsolatos további információkért tekintse [](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)meg a dokumentációt.

### <a name="hierarchy-definition-behavior"></a>Hierarchia definíciójának viselkedése

Vegye figyelembe a következő példát, ahol aH1 hierarchia a definíciójának részeként épületet, *padlót*és *helyet* tartalmaz:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

A *példány mezőitől*függően a hierarchia attribútumai és értékei az alábbi táblázatban látható módon jelennek meg:

| Idősorozat azonosítója | Példány mezői |
| --- | --- |
| ID1 | "Building" = "1000", "Floor" = "10", "szoba" = "55"  |
| ID2 | "Building" = "1000", "Room" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "Building" = "1000", "Floor" = "10"  |
| ID5 | A "Building", a "Floor" vagy a "Room" nincs beállítva |

Az előző példában a **ID1** és a **id4** a H1 hierarchia részeként jelenik meg a Azure Time Series Insights Explorerben, a többi pedig nem *szülő példányba* van besorolva, mert nem felelnek meg a megadott adathierarchiának.

## <a name="time-series-model-instances"></a>Idősorozat-modell példányai

A példányok maguk az idősorozatok. A legtöbb esetben a *deviceId* vagy a *assetid* az eszköz egyedi azonosítója a környezetben. A példányokhoz a példány tulajdonságainak nevezett leíró információk tartoznak. A példányok minimális tulajdonságai között a hierarchia adatai szerepelnek. Olyan hasznos, leíró jellegű adatok is lehetnek, mint például a gyártó, az operátor vagy az utolsó szolgáltatás dátuma.

A példányokat a *typeId*, a *timeSeriesId*, a *Name*, a Description, a *hierarchyIds*és a *instanceFields*határozza meg. Az egyes példányok csak egyetlen *típusra*és egy vagy több hierarchiára mutatnak. A példányok öröklik az összes tulajdonságot a hierarchiából, és további *instanceFields* adhatók hozzá a további példányok tulajdonságainak meghatározásához.

a *instanceFields* egy példány tulajdonságai, valamint a példányt definiáló statikus adatmennyiségek. A hierarchia vagy a hierarchián kívüli tulajdonságok értékeit határozzák meg, miközben a keresési műveletek végrehajtásához is támogatják az indexelést.

A *Name* tulajdonság nem kötelező, és megkülönbözteti a kis-és nagybetűket. Ha a *név* nem érhető el, a rendszer alapértelmezés szerint az idősorozat-azonosítót adja meg. Ha meg van adni a *név* , az idősorozat-azonosító továbbra is elérhető lesz a (z)-ben a diagram alatt a rácsban.

### <a name="time-series-model-instance-json-example"></a>Idősorozat-modell példány JSON-példája

Minta:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
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

Az idősorozat-modell példányaival kapcsolatos további információkért tekintse meg a [dokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

### <a name="time-series-model-settings-example"></a>Példa az idősorozat-modell beállításaira

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

Az idősorozat-modell beállításaival kapcsolatos további információkért tekintse meg a [dokumentációt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="next-steps"></a>További lépések

- Lásd: [Azure Time Series Insights előnézeti tároló és bejövő](./time-series-insights-update-storage-ingress.md)forgalom.

- Tekintse meg az új idősorozat- [modellt](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).