---
title: Time Series lekérdezés |} A Microsoft Docs
description: Axure Time Series Insights (előzetes verzió) Time Series lekérdezés
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: f5370d354833e9507d0794d7bc407a7ea2294e1a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856472"
---
# <a name="time-series-query"></a>Idősorozat-lekérdezés

**Time Series lekérdezés** (TSQ), megkönnyíti a különböző számítási, és a Time Series Insights (TSI) tárolt ipari méretekben Idősorozat-adatok beolvasására. TSQ átalakításához, és adatokat lekérni a tárolási számítási definíciókat használja. Ezt úgy valósítja meg a **Time Series modellek** (TSM) vagy a beágyazott változó definíciók keresztül.

TSQ adatokat két különböző módon kérdezi le. TSQ is képesek adatokat beolvasni, ahogyan azt az adatforrás-szolgáltatója, és csökkenthető az adatmennyiség, vagy a jeleket, kihasználva a megadott metódust teszi lehetővé az ügyfelek műveleteket átalakításához Visszaépítés egyesítheti, és számításokat hajthat végre idősorozat-adatok.

Time Series Insights-adatok a natív TSI Explorer frissítés vagy a nyilvános API felületen keresztül érhetők el. TSQ egy kifejezésnyelv is kínál, így hozhat létre, saját speciális TSI-lekérdezéseket. Az alábbiakban a TSQ kapcsolatos céljaink:

![Cél][1]

## <a name="core-apis"></a>Core API-k

Az alábbiakban alapvető fontosságú API-k is nyújtunk támogatást.

![tsq][2]

### <a name="getmodelsettings-api"></a>getModelSettings API

A getModelSettings API segítségével a környezetek partíciókulcsot az automatikusan létrehozott modellt adja vissza.

A getModelSettings API a következő paramétereket fogadja:

* *név*: a modell lekérni kívánt nevét.
* *timeSeriesIdProperties*

  * *name*
  * *type*

* *defaultTypeID*

#### <a name="getmodelsettings-json-request-and-response-example"></a>getModelSettings kérések és válaszok példa JSON

Egy GET HTTP kérést kap:

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

### <a name="gettypes-api"></a>getTypes API

A getTypes API-t adja vissza a Time Series-típusok és azok kapcsolódó változók a modellhez.

A getTypes API a következő paramétereket fogadja:

* *typeId*: a típusa nem módosítható egyedi azonosítója.
* *timeSeriesId*: A **Time Series azonosító** az eseménystream, és a modellt az adatok egyedi kulcsa. Ezt a kulcsot nem használja a TSI az adatok particionálásához.
* *Leírás*: típusának leírása.
* *hierarchyIds*: a típushoz társított hierarchyIds tömbjét.
* *instanceFields*:
  * *állapot*
  * *Város*

#### <a name="gettypes-json-request-and-response-example"></a>getTypes kérések és válaszok példa JSON

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
```

### <a name="gethierarchies-api"></a>getHierarchies API

Az API getHierarchies összes a Time Series hierarchiák és az összes kapcsolódó mező elérési adja vissza.

A getHierarchies API a következő paramétereket fogadja:

* *ID*: az a hierarchia egyedileg azonosító kulcsa.
* *név*: a hierarchia neve
* *source*
* *instanceFields*
  * *Év*
  * *Hónap*

#### <a name="gethierarchies-json-request-and-response-example"></a>getHierarchies kérések és válaszok példa JSON

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

### <a name="getinstances-api"></a>getInstances API

A getInstances API segítségével a Time Series példányok és azok kapcsolódó szolgáltatáspéldány-mezők adja vissza.

A getInstances API a következő paramétereket fogadja:

* *név*: társított lekérdezése, valamint a helyettesítve a UX használt a példány neve
* *typeId*: a típus egyedileg azonosító kulcsa.
* *timeSeriesId*: A **Time Series azonosító** az eseménystream, és a modellt az adatok egyedi kulcsa. Ezt a kulcsot nem használja a TSI az adatok particionálásához.
* *Leírás*: a példány leírása.
* *hierarchyIds*: egy vagy több hierarchyIds, minden hierarchiát egyedien meghatározó tömbjét.
* *instanceFields*:
  * *állapot*
  * *Város*

#### <a name="getinstances-json-request-and-response-example"></a>getInstances kérések és válaszok példa JSON

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

### <a name="aggregateseries-api"></a>aggregateSeries API

A aggregateSeries API lehetővé teszi a lekérdezés és a rögzített események TSI-adatainak lekéréséhez mintavételi és összesíti az összesített vagy mintát funkciók használata az adatok rögzíti.

A aggregateSeries API a következő paramétereket fogadja:

* *timeSeriesId*: A **Time Series azonosító** az eseménystream, és a modellt az adatok egyedi kulcsa. Ezt a kulcsot nem használja a TSI az adatok particionálásához.
* *searchSpan*: az időtartam és a gyűjtő méretét a összesítő kifejezésben.
* *Szűrő*: nem kötelező predikátum záradékban.
* *Intervallum*: milyen adatokat kell kiszámított időköz.
* *ProjectedVariables*: változókat, amelyek terjed ki.
* *InlineVariables(optional)*: változók definíciók, amelyet meg szeretnénk termékeken bírálja felül az adott TSM vagy törölje a számításhoz megadott beágyazott keresztül érkező.

A getSeries API minden egyes időtartam alatt, a megadott alapján ad vissza egy TSV minden egyes változójánál **Time Series azonosító** , valamint a megadott változókat. Az API getSeries csökkentése TSM tárolt vagy beágyazott összesítés vagy mintát adatokhoz megadott változók használatával éri el.

> [!NOTE]
> Változó interpolációs jelenleg nem támogatott.

Összesített típusok támogatottak:

* `Min`
* `Max`
* `Sum`
* `Count`
* `Average`

#### <a name="aggregateseries-json-request-and-response-example"></a>aggregateSeries kérések és válaszok példa JSON

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

A JSON-törzse:

```JSON
{
    "aggregateSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        //Optional
        //If provided, Reduction should be specified in Variable Definition
        "interval": "PT1M",
        "filter": null, //Optional
        "projectedVariables": [
            "Count",
            "Average Temperature",
            "Min Temperature"
        ],
        "inlineVariables": {
            "Average Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None",
                "aggregation": {"tsx": "avg($value)"}
            },
            //Default Type Count Experience
            "Count": {
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
}
```

Válasz:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Average Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316]
        },
        {
            "name": "Count Temperature",
            "type": "Double",
            "values": [ 60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0,60.0 ]
        },
        {
            "name": "Min Temperature",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getseries-api"></a>getSeries API

Az API lehetővé teszi a lekérdezés és a rögzített események TSI-adatainak lekéréséhez a változók használata a vezetéken rögzített adatok nyújtotta előnyöket kihasználva getSeries beágyazott megadott, vagy a modell definiálása.

> [!Note]
> Ha interpolációs és összesítési záradék van megadva, a változó, illetve az intervallum meg van adva, akkor figyelmen kívül.

A getSeries API a következő paramétereket fogadja:

* *timeSeriesId*: A **Time Series azonosító** az eseménystream, és a modellt az adatok egyedi kulcsa. Ezt a kulcsot nem használja a TSI az adatok particionálásához.
* *searchSpan*: az időtartam és a gyűjtő méretét a összesítő kifejezésben.
* *Szűrő*: nem kötelező predikátum záradékban.
* *ProjectedVariables*: változókat, amelyek terjed ki.
* *InlineVariables(optional)*: változók definíciók, amelyet meg szeretnénk termékeken bírálja felül az adott TSM vagy törölje a számításhoz megadott beágyazott keresztül érkező.

A getSeries API minden egyes változójánál egy TSV adja vissza a megadott alapján **Time Series azonosító** , valamint a megadott változókat. Az API-t biztosít getSeries időközök vagy változó csökkentési/interpolációs nepodporuje.  

#### <a name="getseries-json-request-and-response-example"></a>getSeries kérések és válaszok példa JSON

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

A JSON-törzse:

```JSON
{
    "getSeries": {
        "timeSeriesId": ["da2754af-cc51-46b3-b18f-6231f8781a12","PU.98"],
        "searchSpan": {
            "from": {"dateTime": "2016-08-01T00:00:00Z"},
            "to": {"dateTime": "2016-08-01T00:10:00Z"}
        },
        "interval": null, //Null or not specified
        "filter": null, //Optional
        "projectedVariables": [
            "Temperature",
            "Pressure",
        ],
        "inlineVariables": {
            "Temperature": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.temperature.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            },
            "Pressure": {
                "kind": "numeric",
                "filter": null,
                "value": { "tsx": "$event.pressure.Double" },
                "interpolation": "None", // null, not specified or ignored if specified
                "aggregation": {"tsx": "avg($value)"} //null, not specified or ignored if specified
            }
        }
    }
}
```

Válasz:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:00Z","2016-08-01T00:02:00Z","2016-08-01T00:03:00Z","2016-08-01T00:04:00Z",
        "2016-08-01T00:05:00Z","2016-08-01T00:06:00Z","2016-08-01T00:07:00Z","2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Temperature",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        },
        {
            "name": "Pressure",
            "type": "Double",
            "values": [ 68.645954260192127,68.790954260192578,68.990954260193249,69.190954260193919,69.39095426019459,69.590954260195261,69.790954260195932,69.9909542601966,70.190954260197273,70.390954260197944 ]
        }
    ]
}
```

### <a name="getevents-api"></a>getEvents API

A getEvents API lehetővé teszi, hogy lekérdezés és a TSI-események adatainak lekéréséhez a Time Series Insightsban az adatforrás-szolgáltatója a rögzített.

A getEvents API a következő paramétereket fogadja:

* *timeSeriesId*: A **Time Series azonosító** az eseménystream, és a modellt az adatok egyedi kulcsa. Ezt a kulcsot nem használja a TSI az adatok particionálásához.
* *searchSpan*: az időtartam és a gyűjtő méretét a összesítő kifejezésben.
* *szűrő*: lehetővé teszi, hogy a lekérdezések szűréséhez, azonban a felhasználók által igényelt predikátum értékeket határozhat meg.
* *(nem kötelező) projectedProperties*: lehetővé teszi, hogy oszloptulajdonság/szűrés.

Az API-t adja vissza a nyers getEvents a TSI tárolt értékek a rögzített események egy adott **Time Series azonosító** és időtartományra. Nem szükséges változó definíciók (TSM ANI változó definíciók használnak).

### <a name="getevents-json-request-and-response-example"></a>getEvents kérések és válaszok példa JSON

Egy HTTP POST-kérést kap:

```plaintext
https://YOUR_ENVIROMENT.env.timeseries.azure.com/timeseries/query?api-version=API_VERSION
```

| Name (Név) | Leírás | Példa |
| --- | --- | --- |
| YOUR_ENVIRONMENT  |  A környezet neve  | `environment123` |
| API_VERSION  |  Az API-specifikáció | `2018-11-01-preview` |

A JSON-törzse:

```JSON
{
    "getEvents": {
        // Required and Supports 3 Key as tsId
        "timeSeriesId": [
            "PU.123","W00158","ABN.9890"
        ],
        // Required.
        "searchSpan": {
            "from": "2018-08-01T17:01:00Z",
            "to": "2018-08-20T17:01:00Z",
        },
        // Optional.
        "filter": {
            "tsx": "($event.Value != null) OR ($event.Status.String == 'Good')"
        },
        // Optional – array of simple TSXs that refer exactly one property
        // If not specified, we will return all the properties.
        "projectedProperties": [ 
            {"propertyName" : "Volts", "type": "double" } 
        ]
    }
}
```

Válasz:

```JSON
{
    "timestamps": [ "2016-08-01T00:00:00Z","2016-08-01T00:01:03Z","2016-08-01T00:02:05Z","2016-08-01T00:03:00Z",
        "2016-08-01T00:04:00Z","2016-08-01T00:05:05Z","2016-08-01T00:06:00Z","2016-08-01T00:07:08Z",
        "2016-08-01T00:08:00Z","2016-08-01T00:09:00Z" ],
    "properties": [
        {
            "name": "Volts",
            "type": "Double",
            "values": [ 68.699982037970059,68.889287593526234,69.089287593526919,69.28928759352759,69.489287593528246,69.689287593528917,69.8892875935296,70.089287593530273,70.289287593530929,70.4892875935316 ]
        }
    ]
}
```

### <a name="supported-query-operators"></a>Támogatott lekérdezési operátorokkal

Szűrés:

* `HAS`
* `IN`
* `AND`
* `AND NOT`
* `OR`
* `>`
* `<`
* `<=`
* `>=`
* `!=`
* `<>`
* `/`
* `*`

A historikus:

* `From`
* `To`
* `First/Last` (Csak az API-t)

Aggregációs/átalakítás:

* `MEASURE`
* `SUM` (a mérték)
* `COUNT` (események)
* `AVG` (a mérték)
* `MIN` (a mérték)
* `MAX` (a mérték) "
* `GROUP BY` (a kategorikus oszlop)
* `ORDER BY ASC, DSC` (a skálához képest időbélyeg)
* `DateHistogram` (gyűjtő mérete)

## <a name="tsq-api-limits"></a>TSQ API-korlátok

> [!NOTE]
> Az alábbi táblázat megadja a korlátok a **11/20/18**.

| Paraméter | Korlátok |
| --- | --- |
| TSQ kérés mérete | 32 KB |
| TSQ válasz korlátja | 16 MB |
| TSQ párhuzamos lekérdezés-korlát | környezetenként 20 |
| Események beolvasása | 16 MB-os méret vagy 30s idő |
| Jogkivonat érvényességi | 1 óra |
| Adatsorozat beolvasása | 500 000 időközök vagy időbélyegeket vagy 16 MB-os méret |
| Min | Intervallum korlát 1 ms |
| Max | 50 tervezett változók |
| Összesített sorozat | 500 000 időközök vagy időbélyegeket vagy 16 MB-os méret |
| Min | Intervallum korlát 1 ms |
| Max | 50 tervezett változók |

## <a name="time-series-query-tutorial"></a>Time Series lekérdezés – oktatóanyag

Teljes TSQ oktatóanyagnak a későbbi biztosítjuk.

[!INCLUDE [tsi-update-docs](../../includes/time-series-insights-update-documents.md)]

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

Olvassa el az új [Idősorozat-modell](./time-series-insights-update-tsm.md).

További információ [ajánlott eljárásokat, amikor kiválasztja a Time Series ID](./time-series-insights-update-how-to-id.md).

<!-- Images -->
[1]: media/v2-update-tsq/goal.png
[2]: media/v2-update-tsq/tsq.png
