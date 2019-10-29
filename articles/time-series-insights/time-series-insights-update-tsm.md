---
title: Idősorozat-modell a Azure Time Series Insights Preview-ban | Microsoft Docs
description: Az Azure Time Series Insights Time Series-modell ismertetése.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 802fd444c953c69dfa99fbd49fdf9541cda372ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989794"
---
# <a name="time-series-model"></a>Time Series-modell

Ez a dokumentum leírja a Time Series-modellt, azok képességeit, valamint azt, hogy miként hozhat létre és frissíthet saját Time Series Insights környezetben.

> [!TIP]
>  * Navigáljon a [contoso szélerőműpark bemutató](https://insights.timeseries.azure.com/preview/samples) környezetéhez egy élő idősorozat-modell példája alapján.
> * Az idősorozat-modell felhasználói felületének megismeréséhez olvassa el a [Time Series Insights Preview Explorer böngészőt](time-series-insights-update-explorer.md) .

## <a name="summary"></a>Összefoglalás

Hagyományosan a IoT-eszközökről összegyűjtött adatok nem rendelkeznek környezetfüggő információkkal, ami megnehezíti az érzékelők gyors megtalálását és elemzését. A Time Series-modell fő motivációja a IoT-/idősorozat-adatok megkeresésének és elemzésének egyszerűsítése. Ezt a célt úgy éri el, hogy lehetővé teszi a Time Series-adatokat a kurátorok, a karbantartás és az idősorozat-adathalmazok gazdagítása érdekében, hogy előkészítse a felhasználók számára kész adatkészleteket

## <a name="scenario-contosos-new-smart-oven"></a>Forgatókönyv: contoso új intelligens kemence

**Vegye fontolóra egy új contoso intelligens kemence kitalált forgatókönyvét.** Ebben az esetben tegyük fel, hogy minden contoso intelligens kemence öt hőmérséklet-érzékelővel rendelkezik, egyet a négy legfelső szintű égőkhez, egyet pedig magához a kemencéhez. Egészen a közelmúltig minden contoso hőmérséklet-érzékelő elküldve, tárolva és vizualizációban tárolja az adattartalmakat. A konyhai berendezés monitorozásához a contoso az alapszintű diagramokra támaszkodik, amelyek mindegyike egy adott érzékelőhöz tartozik.

Habár a contoso elégedett volt a kezdeti és a vizualizációs megoldással, számos korlátozás nyilvánvalóvá vált:

* Az ügyfelek szeretnék megismerni, hogy a teljes kemence milyen meleget fog kapni, amikor a legtöbb legfelső szintű égők bekerültek, és a contoso még nem tudta elemezni és bemutatni a teljes kemence feltételeivel kapcsolatos egységes választ.
* A contoso-mérnökök szeretnék ellenőrizni, hogy a leggyakrabban futó égők nem eredményeznek-e nem hatékony energiaellátást, és nem volt olyan nehézség, amely a hőmérséklet-és feszültség-érzékelők egymáshoz való kapcsolásával, valamint a hely megtalálásával kapcsolatos az áruházban.
* A contoso minőségbiztosítási csapata szeretné naplózni és összehasonlítani a két érzékelő verziójának előzményeit, és nem volt nehéz meghatározni, hogy az adott érzékelő melyik adataihoz tartoztak.

Az átfogó, intelligens kemence idősorozat-modell strukturálása, rendszerezése és definiálása nélkül minden hőmérséklet-érzékelő a kihelyezett, elkülönített és kevésbé informatív adatpontokat kezeli. Az adatpontok gyakorlatban hasznosítható elemzésekre való bekapcsolása nehezebbé vált, mivel minden adathalmaz a többitől függetlenül élt.

Ezek a korlátozások az intelligens adatösszesítési és vizualizációs eszközök fontosságát mutatták ki a contoso új kemencéhez való csatlakozáshoz:

* Az adatvizualizáció hasznosnak bizonyulhat, ha az adatgyűjtést és-összevonást egy kényelmes nézetben szeretné használni. Például a feszültség érzékelők, valamint a hőmérséklet-érzékelők láthatók.
* A többdimenziós adatmennyiség több entitáshoz való kezelése, valamint az összehasonlítás, a nagyítás és az időtartomány-funkciók megnehezítik.

A **Time Series-modell kényelmes megoldást kínál** a fenti fiktív példában szereplő számos forgatókönyvhöz:

[idősorozat-modell diagram![](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* Az idősorozat-modell fontos szerepet játszik a lekérdezésekben és a navigálásban, mivel az összehasonlításokat az időtartományok, valamint az érzékelők és az eszközök közötti contextualizes teszi lehetővé.
* Az adatok továbbra is kontextusba kerülnek, mert egy idősorozat-modellben megőrzött adatok megőrzik a Time Series lekérdezési számításait változókként, és ezeket a lekérdezési időpontokban használják.
* Az idősorozat-modell rendszerezi és összesíti az adatokat a jobb vizualizációs és felügyeleti funkciókhoz.

### <a name="key-capabilities"></a>Főbb képességek

Azzal a céllal, hogy egyszerű és könnyedebb legyen a Time Series contextualization kezelése, az idősorozat-modell a következő képességeket teszi lehetővé Time Series Insights előzetes verzióban. A következőkkel segíti Önt:

* Létrehozhat és kezelhet számításokat vagy képleteket a skaláris függvények, az összesítési műveletek és így tovább.
* Szülő-gyermek kapcsolatok definiálása a Navigálás, a keresés és a hivatkozás engedélyezéséhez.
* Definiálja a példányokhoz társított tulajdonságokat, a *példány mezőiben* definiált értéket, és használja őket a hierarchiák létrehozásához.

### <a name="components"></a>Összetevők

A Time Series-modell három alapvető összetevővel rendelkezik:

* [Idősorozat-modell példányai](#time-series-model-instances)
* [Idősorozat-modell hierarchiái](#time-series-model-hierarchies)
* [Idősorozat-modell típusai](#time-series-model-types)

Ezek az összetevők össze vannak egyesítve egy idősorozat-modell megadásához és a Azure Time Series Insights adatai rendszerezéséhez.

[![Time Series-modell áttekintése](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

Az idősorozat-modell hozható létre és kezelhető a [Time Series Insights előzetes](time-series-insights-update-how-to-tsm.md) verziójának felületén keresztül. Az idősorozat-modell beállításai a [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)használatával kezelhetők.

## <a name="time-series-model-instances"></a>Idősorozat-modell példányai

Az idősorozat-modell *példányai* magukban foglalhatják magukban az idősorozatok virtuális ábrázolását.

A legtöbb esetben az **deviceId** vagy a **assetid**egyedileg azonosítja a példányokat, ezeket a rendszer idősorozat-azonosítóként menti.

A példányok egy *példány tulajdonságainak* , például egy idősorozat-azonosító, a típus, a név, a leírás, a hierarchiák és a példány mezőkhöz tartozó leíró információkkal rendelkeznek. A példányok minimális tulajdonságai között a hierarchia adatai szerepelnek.

A példány mezői olyan leíró információk gyűjteményei, amelyek a hierarchia szintjeire, valamint a gyártóra, az operátorra stb. vonatkozó értékeket is tartalmazhatnak.

Ha egy eseményforrás konfigurálva van a Time Series Insights-környezethez, a rendszer automatikusan felderíti és létrehozta a példányokat az idősorozat-modellben. A példányok Time Series Insights Explorerben hozhatók létre vagy frissíthetők a Time Series Model – lekérdezések használatával.

A [contoso szélerőműpark bemutatója](https://insights.timeseries.azure.com/preview/samples) több élő példányra vonatkozó példát is tartalmaz.

[![idősorozat-modell példányai](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Példány tulajdonságai

A példányokat a **timeSeriesId**, a **typeId**, a **Name**, a **description**, a **hierarchyIds**és a **instanceFields**határozza meg. Az egyes példányok csak egyetlen *típusra*és egy vagy több *hierarchiára*mutatnak.

| Tulajdonság | Leírás |
| --- | ---|
| timeSeriesId | Az idősorozathoz tartozó UUID, amelyhez a példány társítva van. |
| typeId | Az idősorozat-modell UUID-azonosítója, amelyhez a példány társítva van. Alapértelmezés szerint az összes felderített új példány egy alapértelmezett típushoz van társítva|
| név | A *Name* tulajdonság nem kötelező, és megkülönbözteti a kis-és nagybetűket. Ha a *név* nem érhető el, az alapértelmezés szerint a *timeSeriesId*lesz. Ha megadja a *nevet* , a *timeSeriesId* továbbra [is elérhető lesz.](time-series-insights-update-explorer.md#preview-well) |
| leírás | A példány szöveges leírása. |
| hierarchyIds | Meghatározza, hogy mely hierarchiák tartoznak a példányhoz. |
| instanceFields | a *instanceFields* egy példány tulajdonságai, valamint a példányt definiáló statikus adatmennyiségek. A hierarchia vagy a hierarchián kívüli tulajdonságok értékeit határozzák meg, miközben a keresési műveletek végrehajtásához is támogatják az indexelést. |

> [!NOTE]
> A hierarchiák **példányok**használatával készültek, és további **instanceFields** adhatók hozzá a további példányok tulajdonságainak meghatározásához.

A példányok a következő JSON-ábrázolással rendelkeznek:

```JSON
{
    "timeSeriesId": ["PU2"],
    "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
    "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
    "description": "Pump #2",
    "instanceFields": {
        "Location": "Redmond",
        "Fleet": "Fleet 5",
        "Unit": "Pump Unit 3",
        "Manufacturer": "Contoso",
        "ScalePres": "0.54",
        "scaleTemp": "0.54"
    }
}
```

> [!TIP]
> Time Series Insights instance API és a szifilisz támogatásához olvassa el az [Adatlekérdezési](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket és a [példány API Rest dokumentációját](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Idősorozat-modell hierarchiái

Az idősorozat-modell *hierarchiái* megszervezik a példányokat a tulajdonságok neveinek és kapcsolataiknak a megadásával.

Egy adott Time Series Insights környezetben több hierarchiát is beállíthat. Az idősorozat-modell példányai egyetlen hierarchia vagy több hierarchia (több a többhöz kapcsolat) számára képezhetők le.

A [contoso szélerőműpark bemutató](https://insights.timeseries.azure.com/preview/samples) ügyfél felülete szabványos példányt és típus-hierarchiát jelenít meg.

[idősorozat-modell hierarchiáinak![](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Hierarchia definíciója

A hierarchiákat a hierarchia **azonosítója**, **neve**és **forrása**határozza meg.

| Tulajdonság | Leírás |
| ---| ---|
| id | A hierarchia egyedi azonosítója, amelyet például egy példány definiálásakor használtak. |
| név | A hierarchia nevének megadására szolgáló sztring. |
| source | Meghatározza a szervezeti hierarchiát vagy elérési utat, amely a felhasználók által létrehozandó hierarchia legfelső szintű szülő-gyermek sorrendje. A szülő-gyermek tulajdonságok hozzárendelésének *mezői*. |

A hierarchia a következőképpen jelenik meg a JSON-ben:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

Fenti

* a `Location` szülő `states` és gyermek `cities`rendelkező hierarchiát határoz meg. Minden `location` több `states` is rendelkezhet, amelyek viszont több `cities`is rendelkezhetnek.
* a `ManufactureDate` szülő `year` és gyermek `month`rendelkező hierarchiát határoz meg. Minden `ManufactureDate` több `years` is rendelkezhet, amelyek viszont több `months`is rendelkezhetnek.

> [!TIP]
> Time Series Insights instance API és a szifilisz támogatásához olvassa el az [Adatlekérdezési](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket és a [hierarchia API Rest dokumentációját](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Példa hierarchia

Vegyünk például egy olyan példát, ahol a **H1** hierarchia a **instanceFieldNames** -definíciójának részeként *épületet*, *padlót*és *helyet* tartalmaz:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

A fenti definícióban és a több idősorozatban használt **példány mezők** esetében a hierarchia attribútumai és értékei az alábbi táblázatban látható módon jelennek meg:

| Idősorozat-azonosító | Példány mezői |
| --- | --- |
| ID1 | "Building" = "1000", "Floor" = "10", "szoba" = "55"  |
| ID2 | "Building" = "1000", "Room" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "Building" = "1000", "Floor" = "10"  |
| ID5 | A "Building", a "Floor" vagy a "Room" nincs beállítva |

Az idősorozat **ID1** és **id4** a **H1** hierarchia részeként jelenik meg a [Azure Time Series Insights Explorerben](time-series-insights-update-explorer.md) , mivel teljes mértékben definiálva és megfelelően rendezett *kialakítással*, *padlóval*és *helyiséggel* rendelkeznek. paraméterek.

A többiek a nem *szülő példányok* alá lesznek sorolva, mivel nem felelnek meg a megadott adathierarchiának.

## <a name="time-series-model-types"></a>Idősorozat-modell típusai

Az idősorozat *-modellek segítségével* változók vagy képletek definiálhatók a számítások végrehajtásához. A típusok egy adott Time Series Insights-példánnyal vannak társítva.

Egy típus egy vagy több változóval is rendelkezhet. Egy idősorozat-modell példánya például lehet *hőmérséklet-érzékelő*, amely az *átlagos hőmérsékletet*, a *minimális hőmérsékletet*és a *maximális hőmérsékletet*tartalmazza.

A [contoso szélerőműpark bemutatója](https://insights.timeseries.azure.com/preview/samples) több idősorozat-modellt jelenít meg a saját példányaihoz társítva.

[![idősorozat-modell típusai](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Time Series Insights-példány API-hoz és a szifilisz támogatásához olvassa el az [Adatlekérdezési](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket és az [API Rest-dokumentációját](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Típus tulajdonságai

Az idősorozat-modell típusait **azonosító**, **név**, **Leírás**és **változók**határozzák meg.

| Tulajdonság | Leírás |
| ---| ---|
| id | A típus UUID azonosítója. |
| név | A típus nevének megadására szolgáló sztring. |
| leírás | A típushoz tartozó karakterlánc leírása. |
| Változók | Adja meg a típushoz társított változókat. |

A típusok megfelelnek a következő JSON-példának:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Változók

Time Series Insights típusok több változót is tartalmazhatnak, amelyek a képleteket és számítási szabályokat határozzák meg az eseményeken.

Mindegyik változó a három *típus*egyike lehet: *numerikus*, *kategorikus*és *aggregált*.

* A *numerikus típusú számok* folytonos értékekkel működnek. 
* A *kategorikus* típusok a különálló értékek meghatározott készletével működnek.
* Az *összesített* értékek egyetlen típusú változót egyesítenek (vagy az összes *numerikus* vagy az összes *kategorikus*).

Az alábbi táblázat megjeleníti, hogy az egyes változók milyen tulajdonságokkal rendelkeznek.

[![idősorozat-modell típusai](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numerikus változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó értéke | Telemetria az eszközről/érzékelőkről érkező számításokhoz vagy az idősorozat-kifejezések használatával átalakított értékekhez. A numerikus típusok változóinak *dupla*típusúnak kell lenniük.|
| Változó interpoláció | A interpoláció azt adja meg, hogyan lehet a jeleket újralétrehozni meglévő adataival. A *Step* és a *lineáris* interpolációs beállítások a numerikus változókhoz érhetők el. |
| Változó összesítése | A kiszámítások támogatása az *AVG*, a *min*, a *Max*, a *Sum*, a *Count*, a *First*, a *Last* és az Time-súlyozott (*AVG*, *min*, *Max*, *Sum*, *Left*) operátorral. |

A változók megfelelnek a következő JSON-példának:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Kategorikus változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó értéke | Az eszközből/érzékelőkből származó számításhoz használt telemetria-értékek. A kategorikus típus változóinak vagy *hosszúnak* vagy *sztringnek*kell lenniük. |
| Változó interpoláció | A interpoláció azt adja meg, hogyan lehet a jeleket újralétrehozni meglévő adataival. A *lépésenkénti* interpolációs beállítás a kategorikus változók esetében érhető el. |
| Változók kategóriái | A kategóriák az eszközről/érzékelőkről a címkére érkező értékek közötti leképezést hoznak létre. |
| Változó alapértelmezett kategóriája | Az alapértelmezett kategória minden olyan értékhez tartozik, amely nincs leképezve a "Categories" tulajdonságban. |

A változók megfelelnek a következő JSON-példának:

```JSON
"Status": {
  "kind": "categorical",
  "value": "toLong($event.[Status].Double)",
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Összesített változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó összesítése | A számítások támogatása az *AVG*, a *min*, a *Max*, a *Sum*, a *Count*, a *First*és a *Last*értékkel. |

A változók megfelelnek a következő JSON-példának:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

A változók egy idősorozat-modell definíciójában tárolódnak, és a [lekérdezési API](time-series-insights-update-tsq.md) -kon keresztül is elérhetők a tárolt definíció felülbírálásához.

## <a name="next-steps"></a>Következő lépések

- Lásd: [Azure Time Series Insights előnézeti tároló és bejövő](./time-series-insights-update-storage-ingress.md)forgalom.

- Ismerkedjen meg a gyakori idősorozat-modellezési műveletekkel [Azure Time Series Insights előzetes](./time-series-insights-update-how-to-tsm.md) verzióban

- Olvassa el az új [Idősorozat-modell](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) dokumentációját.
