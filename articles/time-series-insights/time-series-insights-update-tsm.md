---
title: Idősorozat-modell – Azure Time Series Insights | Microsoft Docs
description: Az idősorozat-modell megismerése Azure Time Series Insights előzetes verzióban.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/20/2019
ms.custom: seodec18
ms.openlocfilehash: 7d588e11525e5087f8667da4602797e5299c76f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75374717"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Idősorozat-modell Azure Time Series Insights előzetes verzióban

Ez a cikk az idősorozat-modellt, a képességeket, valamint a saját modelljeinek a Azure Time Series Insights előnézeti környezetben való létrehozásának és frissítésének megkezdését ismerteti.

> [!TIP]
>  * Nyissa meg a [contoso szélerőműpark bemutató](https://insights.timeseries.azure.com/preview/samples) környezetét egy élő idősorozat-modellre példaként.
> * Az idősorozat-modell felhasználói felületének megismeréséhez olvassa el a [Azure Time Series Insights Preview Explorer böngészőt](time-series-insights-update-explorer.md) .
> * Ismerje meg, [Hogyan dolgozhat az idősorozat-modellel](time-series-insights-update-how-to-tsm.md) a Time Series Insights webkezelő használatával.

## <a name="summary"></a>Összefoglalás

Hagyományosan a IoT-eszközökről összegyűjtött adatok nem rendelkeznek környezetfüggő információkkal, ami megnehezíti az érzékelők gyors megtalálását és elemzését. Az idősorozat-modell fő motivációja a IoT és az idősoros adatok megkeresésének és elemzésének egyszerűbbé tétele. Ezt a célt úgy éri el, hogy lehetővé teszi a Time Series-adatokat a kurátorok, a karbantartás és az idősorozat-adathalmazok gazdagítása érdekében, hogy előkészítse a felhasználók számára kész adatkészleteket

## <a name="scenario-contosos-new-smart-oven"></a>Forgatókönyv: contoso új intelligens kemence

**Vegye figyelembe a contoso intelligens kemence kitalált forgatókönyvét.** Ebben az esetben tegyük fel, hogy minden contoso intelligens kemence öt hőmérséklet-érzékelővel rendelkezik, egyet a négy legfelső szintű égőkhez, egyet pedig magához a kemencéhez. Egészen a közelmúltig minden contoso hőmérséklet-érzékelő elküldve, tárolva és vizualizációban tárolja az adattartalmakat. A konyhai berendezés monitorozásához a contoso az alapszintű diagramokra támaszkodik, amelyek mindegyike egy adott érzékelőhöz tartozik.

Habár a contoso elégedett volt a kezdeti és vizualizációs megoldással, számos korlátozás nyilvánvalóvá vált:

* Az ügyfelek szeretnék megismerni, hogy a teljes kemence milyen gyakran fog megjelenni, amikor a legtöbb legtöbbet a legjobb égők használták. A contoso a teljes kemence feltételeivel kapcsolatban több nehézséget okozott, és egységes választ mutat.
* A contoso-mérnökök szeretnék ellenőrizni, hogy a leggyakrabban futó égők nem eredményeznek-e nem hatékony energiaellátást. Nehézségekbe ütközik, hogy mely hőmérséklet-és feszültség-érzékelők voltak társítva egymáshoz, és hogyan lehet megkeresni őket az áruházban.
* A contoso minőségbiztosítási csapata a két érzékelő verziójának előzményeit szeretné naplózni és összehasonlítani. Nehéz volt megállapítani, hogy mely adatkészlethez tartozó az érzékelő verziója.

Az átfogó, intelligens kemence idősorozat-modell strukturálása, rendszerezése és definiálása nélkül minden hőmérséklet-érzékelő a kihelyezett, elkülönített és kevésbé informatív adatpontokat kezeli. Az adatpontok gyakorlatban alkalmazható információkhoz való bekapcsolása nehezebbé vált, mivel az egyes adatkészletek a többitől függetlenül éltek.

Ezek a korlátozások az intelligens adatösszesítési és vizualizációs eszközök fontosságát mutatták ki a contoso új kemencéhez való csatlakozáshoz:

* Az adatvizualizáció hasznosnak bizonyulhat, ha az adatgyűjtést és-összevonást egy kényelmes nézetben szeretné használni. Ilyenek például a feszültség érzékelők a hőmérséklet-érzékelőkkel együtt.
* A többdimenziós adatmennyiségek több entitáshoz való kezelése, valamint az összehasonlítás, a nagyítás és az időtartomány-funkcionalitás nehéz feladat lehet.

Az **idősorozat-modell kényelmes megoldást kínál** a kitalált példán belül előforduló számos forgatókönyv esetében:

[![Time Series-modell intelligens kemence ábrázolási példája](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Az idősorozat-modell fontos szerepet játszik a lekérdezésekben és a navigálásban, mivel az összehasonlításokat az időtartományok, valamint az érzékelők és az eszközök közötti contextualizes teszi lehetővé. (**A**) 
* Az adatok továbbra is környezetfüggők, mert a Time Series-modellben megőrzött adatok megőrzik a Time Series lekérdezési számításait változókként, és a lekérdezéskor újra felhasználják őket.
* Az idősorozat-modell rendszerezi és összesíti az adatokat a jobb vizualizációs és felügyeleti képességekhez. (**B**) 

### <a name="key-capabilities"></a>Főbb képességek

Azzal a céllal, hogy egyszerű és könnyedebb legyen a Time Series contextualization kezelése, az idősorozat-modell a következő képességeket teszi lehetővé Time Series Insights előzetes verzióban. A következőkkel segíti Önt:

* Létrehozhat és kezelhet számításokat vagy képleteket a skaláris függvények, az összesítési műveletek és így tovább.
* Szülő-gyermek kapcsolatok definiálása a Navigálás, a keresés és a hivatkozás engedélyezéséhez.
* Definiálja a példányokhoz társított tulajdonságokat, a *példány mezőinek*meghatározását, és használja őket a hierarchiák létrehozásához.

### <a name="components"></a>Összetevők

A Time Series-modell három alapvető összetevőből áll:

* [Idősorozat-modell példányai](#time-series-model-instances)
* [Idősorozat-modell hierarchiái](#time-series-model-hierarchies)
* [Idősorozat-modell típusai](#time-series-model-types)

Ezek az összetevők össze vannak egyesítve egy idősorozat-modell megadásához és a Azure Time Series Insights adatai rendszerezéséhez.

[![idősorozat-modell áttekintő diagramja](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Egy idősorozat-modell hozható létre és kezelhető a [Time Series Insights előnézet](time-series-insights-update-how-to-tsm.md) felületén keresztül. Az idősorozat-modell beállításai a [Model Settings API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)használatával kezelhetők.

## <a name="time-series-model-instances"></a>Idősorozat-modell példányai

Az idősorozat-modell *példányai* magukban foglalhatják magukban az idősorozatok virtuális ábrázolását.

A legtöbb esetben a példányokat az **deviceId** vagy a **assetid**egyedileg azonosítják, amelyek idősorozat-azonosítóként lesznek mentve.

A példányok a *példány tulajdonságaihoz*tartozó, például egy idősorozat-azonosító, típus, név, leírás, hierarchia és példány mezőkhöz rendelt leíró információkat rendelkeznek. A példányok minimális tulajdonságai között a hierarchia adatai szerepelnek.

A *példány mezői* olyan leíró információk gyűjteményei, amelyek a hierarchia szintjeire, valamint a gyártóra, a kezelőre és így továbbra is tartalmazhatnak értékeket.

Ha egy eseményforrás konfigurálva van a Time Series Insights-környezethez, a rendszer automatikusan felderíti és létrehozza a példányokat egy idősorozat-modellben. A példányok a Time Series Insights Explorerben hozhatók létre vagy frissíthetők az idősorozat-modell lekérdezéseinek használatával.

A [contoso szélerőműpark bemutatója](https://insights.timeseries.azure.com/preview/samples) több élő példányra vonatkozó példát is tartalmaz.

[Példa ![idősorozat-modell példányára](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Példány tulajdonságai

A példányokat a **timeSeriesId**, a **typeId**, a **Name**, a **description**, a **hierarchyIds**és a **instanceFields**határozza meg. Az egyes példányok csak egyetlen *típusra*és egy vagy több *hierarchiára*mutatnak.

| Tulajdonság | Leírás |
| --- | ---|
| timeSeriesId | Az idősorozathoz tartozó UUID, amelyhez a példány társítva van. |
| typeId | Az idősorozat-modell UUID-azonosítója, amelyhez a példány társítva van. Alapértelmezés szerint az összes felderített új példány egy alapértelmezett típushoz van társítva.
| név | A **Name** tulajdonság nem kötelező, és megkülönbözteti a kis-és nagybetűket. Ha a **név** nem érhető el, az alapértelmezett érték a **timeSeriesId**. Ha megadja a nevet, a **timeSeriesId** továbbra [is elérhető.](time-series-insights-update-explorer.md#4-time-series-well) |
| leírás | A példány szöveges leírása. |
| hierarchyIds | Meghatározza, hogy mely hierarchiák tartoznak a példányhoz. |
| instanceFields | Egy példány tulajdonságai és a példányt definiáló statikus adatmennyiség. A hierarchia vagy a hierarchián kívüli tulajdonságok értékeit határozzák meg, miközben a keresési műveletek végrehajtásához is támogatják az indexelést. |

> [!NOTE]
> A hierarchiák a példány mezőinek használatával készültek. További **instanceFields** adhatók hozzá további példány-tulajdonságok definíciói.

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
> A Time Series Insights-példány API-hoz és a létrehozásához, olvasásához, frissítéséhez és törléséhez (szifilisz) támogatásához tekintse meg az [Adatlekérdezési](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket és a [példány API Rest dokumentációját](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Idősorozat-modell hierarchiái

Az idősorozat-modell *hierarchiái* megszervezik a példányokat a tulajdonságok neveinek és kapcsolataiknak a megadásával.

Egy adott Time Series Insights környezetben több hierarchiát is beállíthat. Az idősorozat-modell példányai egyetlen hierarchia vagy több hierarchia (több a többhöz kapcsolat) számára képezhetők le.

A [contoso szélerőműpark bemutató](https://insights.timeseries.azure.com/preview/samples) ügyfél felülete szabványos példányt és típus-hierarchiát jelenít meg.

[Példa ![Time Series modell-hierarchia](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hierarchia definíciója

A hierarchiákat a hierarchia **azonosítója**, **neve**és **forrása**határozza meg.

| Tulajdonság | Leírás |
| ---| ---|
| id | A hierarchia egyedi azonosítója, amely például egy példány definiálásakor használatos. |
| név | A hierarchia nevének megadására szolgáló sztring. |
| source | Meghatározza a szervezeti hierarchiát vagy elérési utat, amely a felhasználók által létrehozandó hierarchia legfelső szintű szülő-gyermek sorrendje. A szülő-gyermek tulajdonságok hozzárendelésének mezői. |

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

Az előző JSON-példában:

* a `Location` szülő `states` és gyermek `cities`rendelkező hierarchiát határoz meg. Minden `location` több `states`is rendelkezhet, ami viszont több `cities`is rendelkezhet.
* a `ManufactureDate` szülő `year` és gyermek `month`rendelkező hierarchiát határoz meg. Minden `ManufactureDate` több `years`is rendelkezhet, ami viszont több `months`is rendelkezhet.

> [!TIP]
> A Time Series Insights-példány API-hoz és a szifilisz támogatásához tekintse meg az [Adatlekérdezési](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket és a [hierarchia API Rest dokumentációját](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Példa hierarchia

Vegyünk egy példát arra, hogy a **H1** hierarchia `building`, `floor`és `room` a **instanceFieldNames** definíciójának részeként:

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

Az előző definícióban és a több idősorozatban használt példány mezők esetében a hierarchia attribútumai és értékei az alábbi táblázatban látható módon jelennek meg:

| Idősorozat-azonosító | Példány mezői |
| --- | --- |
| ID1 | "Building" = "1000", "Floor" = "10", "szoba" = "55"  |
| ID2 | "Building" = "1000", "Room" = "55" |
| ID3 | "Floor" = "10" |
| ID4 | "Building" = "1000", "Floor" = "10"  |
| ID5 | A "Building", a "Floor" vagy a "Room" nincs beállítva. |

Az idősorozat **ID1** és **id4** a **H1** hierarchia részeként jelennek meg a [Azure Time Series Insights Explorerben](time-series-insights-update-explorer.md) , mert teljes mértékben definiálva és megfelelően rendezték a *Létrehozás*, a *padló*és a *szoba* paramétereit.

A többiek a nem *szülő példányok* alá vannak sorolva, mert nem felelnek meg a megadott adathierarchiának.

## <a name="time-series-model-types"></a>Idősorozat-modell típusai

Az idősorozat *-modellek segítségével* változók vagy képletek definiálhatók a számítások végrehajtásához. A típusok egy adott Time Series Insights-példánnyal vannak társítva.

Egy típus egy vagy több változóval is rendelkezhet. Egy idősorozat-modell példánya például lehet *hőmérséklet-érzékelő*, amely az *átlagos hőmérsékletet*, a *minimális hőmérsékletet*és a *maximális hőmérsékletet*tartalmazza.

A [contoso szélerőműpark bemutatója](https://insights.timeseries.azure.com/preview/samples) több idősorozat-modellt jelenít meg a saját példányaihoz társítva.

[![idősorozat-modell típusának példája](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Time Series Insights instance API és a szifilisz támogatásához tekintse meg az [Adatlekérdezési](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket és az [API Rest-dokumentációját](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Típus tulajdonságai

Az idősorozat-modell típusait **azonosító**, **név**, **Leírás**és **változók**határozzák meg.

| Tulajdonság | Leírás |
| ---| ---|
| id | A típus UUID azonosítója. |
| név | A típus nevének megadására szolgáló sztring. |
| leírás | A típushoz tartozó karakterlánc leírása. |
| változók | Adja meg a típushoz társított változókat. |

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

A Time Series Insights típusok számos változót tartalmazhatnak, amelyek a képleteket és számítási szabályokat határozzák meg az eseményeken.

Mindegyik változó a három *típus*egyike lehet: *numerikus*, *kategorikus*és *aggregált*.

* A **numerikus típusú számok** folytonos értékekkel működnek. 
* A **kategorikus** típusok a különálló értékek meghatározott készletével működnek.
* Az **összesített** értékek egyetlen típus több változóját kombinálják (vagy az összes numerikus vagy az összes kategorikus).

Az alábbi táblázat az egyes változókhoz tartozó tulajdonságokat mutatja be.

[![Time Series Model változó táblázat](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numerikus változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok, amelyek korlátozzák a számításba veendő sorok számát. |
| Változó értéke | Az eszközről vagy érzékelőkről származó számításhoz használt telemetria-értékek, vagy az idősorozat-kifejezések használatával alakíthatók át. A numerikus típusú változóknak *dupla*típusúnak kell lenniük.|
| Változó interpoláció | A interpoláció azt adja meg, hogyan lehet a jeleket újradefiniálni a meglévő adatforgalom használatával. A *Step* és a *lineáris* interpolációs beállítások a numerikus változókhoz érhetők el. |
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
| Változó értéke | Az eszközről vagy érzékelőkről származó számításhoz használt telemetria-értékek. A kategorikus típus változóinak vagy *hosszúnak* vagy *sztringnek*kell lenniük. |
| Változó interpoláció | A interpoláció azt adja meg, hogyan lehet a jeleket újradefiniálni a meglévő adatforgalom használatával. A *Step* interpolációs beállítás a kategorikus változóknál érhető el. |
| Változók kategóriái | A kategóriák az eszközről vagy érzékelőkről a címkére érkező értékek közötti leképezést hoznak létre. |
| Változó alapértelmezett kategóriája | Az alapértelmezett kategória minden olyan értékhez tartozik, amely nincs leképezve a "Categories" tulajdonságban. |

A változók megfelelnek a következő JSON-példának:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)" 
},
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
