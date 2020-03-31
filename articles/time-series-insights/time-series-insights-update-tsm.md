---
title: Idősorozat-modell – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg a Time Series Model az Azure Time Series Insights előzetes verziójában.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476654"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Idősorozat-modell az Azure Time Series Insights előzetes verziójában

Ez a cikk ismerteti a Time Series Model, a képességek, és hogyan lehet elkezdeni a saját modellek az Azure Time Series Insights előzetes környezetben.

> [!TIP]
>  * Lépjen a [Contoso Wind Farm bemutató](https://insights.timeseries.azure.com/preview/samples) környezetegy élő Idősorozat modell példa.
> * Az [Azure Time Series Insights előzetes verziójával kapcsolatos explorer](time-series-insights-update-explorer.md) ből megtudhatja, hogyan navigálhat a Time Series Model felhasználói felületén.
> * Ismerje [meg, hogyan dolgozhat a Time Series Model használatával](time-series-insights-update-how-to-tsm.md) a Time Series Insights webexplorer használatával.

## <a name="summary"></a>Összefoglalás

Hagyományosan az IoT-eszközökről gyűjtött adatok nem rendelkeznek kontextuális információkkal, ami megnehezíti az érzékelők gyors megtalálását és elemzését. A Time Series Model fő motivációja az IoT- vagy idősorozat-adatok keresésének és elemzésének egyszerűsítése. Ezt a célt úgy éri el, hogy lehetővé teszi az idősorozat-adatok kiírását, karbantartását és bővítését, hogy segítsen előkészíteni a fogyasztóra kész adatkészleteket az elemzéshez.

## <a name="scenario-contosos-new-smart-oven"></a>Forgatókönyv: Contoso új intelligens sütő

**Fontolja meg a Contoso intelligens sütő fiktív forgatókönyvét.** Ebben a forgatókönyvben tegyük fel, hogy minden Contoso intelligens sütő öt hőmérséklet-érzékelővel rendelkezik, amelyek mindegyike négy felső égőhöz és egy-egy a sütőhöz tartozik. Egészen a közelmúltig minden Contoso hőmérséklet-érzékelő külön-külön elküldte, tárolta és vizualizálta az adatokat. A konyhai berendezések felügyeletéhez a Contoso alaptérképekre támaszkodott, amelyek mindegyike egyedi érzékelőhöz tartozik.

Bár a Contoso elégedett volt a kezdeti adat- és vizualizációs megoldással, számos korlátozás vált nyilvánvalóvá:

* Az ügyfelek azt akarták tudni, hogy milyen meleg a teljes sütő kap, ha a legtöbb felső égők voltak. A Contoso-nak nehezebb volt elemeznie és egységes választ adnia a teljes sütő feltételeiről.
* A Contoso mérnökei ellenőrizni akarták, hogy az egyidejűleg futtatott felső égők nem eredményeznek-e nem hatékony energialekötést. Nehéz volt összevetnem, hogy mely hőmérséklet- és feszültségérzékelők kapcsolódnak egymáshoz, és hogyan lehet megtalálni őket a boltban.
* A Contoso minőségbiztosítási csapata két érzékelőverzió naplózását és összehasonlítását szerette volna. Nehéz volt meghatározni, hogy mely adatok melyik érzékelőverzióhoz tartoztak.

Az átfogó intelligens sütő idősorozat-modell strukturálása, rendszerezése és meghatározása nélkül minden hőmérséklet-érzékelő kificamodott, elszigetelt és kevésbé informatív adatpontokat tartott fenn. Ezek az adatpontok működésbe lépő elemzési adatok nehezebb volt, mivel minden adatkészlet a többitől függetlenül élt.

Ezek a korlátozások rávilágítottak az intelligens adatösszesítési és vizualizációs eszközök fontosságára a Contoso új sütőjének kísérő számára:

* Az adatvizualizáció akkor bizonyul hasznosnak, ha az adatokat kényelmes nézetbe tudja társítani és összekapcsolni. Egy példa a feszültségérzékelők és a hőmérséklet-érzékelők megjelenítése.
* Többdimenziós adatok kezelése több entitáshoz az összehasonlítási, nagyítási és időtartomány-funkciókkal együtt nehéz lehet megvalósítani.

**A Time Series Model kényelmes megoldást kínál** számos, ebben a fiktív példában tapasztalt forgatókönyvhöz:

[![Példa a Time Series Model intelligens sütődiagramra](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* A Time Series Model létfontosságú szerepet játszik a lekérdezésekben és a navigációban, mivel az adatokat úgy teszi lehetővé, hogy az időtartományok, valamint az érzékelő és az eszköz típusa közötti összehasonlításokat lehessen készíteni. **( a)** 
* Az adatok további kontextualizáltak, mivel a Time Series Model ben megőrzött adatok megőrzik az idősorozat-lekérdezési számításokat változóként, és újra felhasználja azokat a lekérdezés idáig.
* A Time Series Model rendszerezi és összesíti az adatokat a jobb vizualizációs és felügyeleti képességek érdekében. **(B)** 

### <a name="key-capabilities"></a>Főbb képességek

Azzal a céllal, hogy egyszerűvé és egyszerűvé tegye az idősorozat-környezet-modellezés kezelését, a Time Series Model a következő képességeket teszi lehetővé a Time Series Insights előzetes verziójában. Ez segít:

* Számítások at vagy képleteket dolgozki és kezelhet skaláris függvények, összesítő műveletek és így tovább.
* Szülő-gyermek kapcsolatok definiálása a navigáció, a keresés és a hivatkozás engedélyezéséhez.
* Definiálja a példányokkal társított, *példánymezőkként*definiált tulajdonságokat, és használja őket hierarchiák létrehozásához.

### <a name="components"></a>Összetevők

A Time Series Model három alapvető összetevőből áll:

* [Idősorozat-modell példányai](#time-series-model-instances)
* [Idősorozat-modell hierarchiái](#time-series-model-hierarchies)
* [Idősorozat-modelltípusok](#time-series-model-types)

Ezek az összetevők kombinálva egy idősorozat-modell megadásához és az Azure Time Series Insights-adatok rendszerezéséhez.

[![Idősorozat-modell – áttekintő diagram](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Az idősorozat-modell a Time [Series Insights előzetes](time-series-insights-update-how-to-tsm.md) felületén keresztül hozható létre és kezelhető. A Time Series Model beállításai a [Modellbeállítások API-n](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)keresztül kezelhetők.

## <a name="time-series-model-instances"></a>Idősorozat-modell példányai

A Time Series Model *példányok* maguk az idősorozat okainak virtuális ábrázolásai.

A legtöbb esetben a példányokat egyedileg azonosítja **az eszközazonosító** vagy **az eszközazonosító,** amely idősorozat-azonosítóként kerül mentésre.

A példányok hoznak létre leíró információkat *a példánytulajdonságoknak*, például idősorozat-azonosítónak, típusnak, névnek, leírásnak, hierarchiáknak és példánymezőknek. A példánytulajdonságok legalább hierarchiaadatokat tartalmaznak.

*A példánymezők* olyan leíró információk gyűjteményei, amelyek tartalmazhatják a hierarchiaszintek, valamint a gyártó, az üzemeltető és így tovább értékeket.

Miután egy eseményforrás konfigurálva van a Time Series Insights környezetben, a példányok automatikusan felderítve, és egy idősorozat-modellben jön létre. A példányok a Time Series Insights-kezelőn keresztül hozhatók létre vagy frissíthetők a Time Series Model lekérdezések használatával.

A [Contoso Wind Farm bemutató](https://insights.timeseries.azure.com/preview/samples) számos élő példány példát tartalmaz.

[![Példa a Time Series Model példányára](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Példány tulajdonságai

A példányokat **a timeSeriesId**, **typeId**, **name,** **description**, **hierarchyIds**és **instanceFields határozza**meg. Minden példány csak egy *típusra*és egy vagy több *hierarchiára van leképezve*.

| Tulajdonság | Leírás |
| --- | ---|
| idősorozat-azonosító | A példányhoz társított idősorozat UUID azonosítója. |
| gépazonosító | A példány hoz tartozó Time Series Model típus UUID azonosítója. Alapértelmezés szerint az összes felderített új példány egy alapértelmezett típushoz lesz társítva.
| név | A **name** tulajdonság nem kötelező, és a kis- és nagybetűket nem. Ha **a név** nem érhető el, akkor alapértelmezés szerint **timeSeriesId**lesz. Ha meg ad egy nevet, a **timeSeriesId** továbbra is elérhető a [kútban.](time-series-insights-update-explorer.md#4-time-series-well) |
| leírás | A példány szöveges leírása. |
| hierarchiaAzonosítók | Azt határozza meg, hogy a példány mely hierarchiákhoz tartozik. |
| instanceFields (példánymezők) | Egy példány tulajdonságai és a példányt meghatározó statikus adatok. A hierarchia vagy a nem hierarchia tulajdonságainak értékeit határozzák meg, miközben támogatják az indexelést a keresési műveletek végrehajtásához. |

> [!NOTE]
> A hierarchiák példánymezők használatával épülnek fel. További **instanceFields** további példánytulajdonság-definíciókhoz adható hozzá.

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
> A Time Series Insights Instance API-t, valamint a létrehozása, olvasása, frissítése és törlése (CRUD) támogatása, olvassa el az [adatok lekérdezése](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket, és a [instance API REST dokumentációt.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)

## <a name="time-series-model-hierarchies"></a>Idősorozat-modell hierarchiái

A Time Series Model *hierarchiái* a példányokat tulajdonságnevek és kapcsolataik megadásával rendezik.

Egy adott Time Series Insights környezetben több hierarchiát is konfigurálhat. A Time Series Model példányok egyetlen hierarchiához vagy több hierarchiához (több-a-többhöz kapcsolat) is leképezhetők.

A [Contoso Wind Farm bemutató](https://insights.timeseries.azure.com/preview/samples) ügyfélfelülete egy szabványos példány- és típushierarchiát jelenít meg.

[![Példa a Time Series Model hierarchia](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hierarchia definíciója

A hierarchiákat a hierarchia **azonosítója**, **a neve**és **a forrása**határozza meg.

| Tulajdonság | Leírás |
| ---| ---|
| id | A hierarchia egyedi azonosítója, amelyet például egy példány definiálásakor használ. |
| név | A hierarchia nevének megadására használt karakterlánc. |
| source | Megadja a szervezeti hierarchiát vagy elérési utat, amely a felhasználók által létrehozni kívánt hierarchia felülről lefelé irányuló szülő-gyermek sorrendje. A szülő-gyermek tulajdonságok leképezési példánymezőket. |

A hierarchiák a JSON-ban a következőképpen jelennek meg:

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

* `Location`meghatározza a hierarchiát a szülővel `states` és a gyermekkel. `cities` Mindegyiknek `location` több `states`is lehet , `cities`ami viszont több is lehet .
* `ManufactureDate`meghatározza a hierarchiát a szülővel `year` és a gyermekkel. `month` Mindegyiknek `ManufactureDate` több `years`is lehet , `months`ami viszont több is lehet .

> [!TIP]
> A Time Series Insights Instance API és a CRUD támogatás, olvassa el az [adatok lekérdezése](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket, és a [Hierarchia API REST dokumentációt.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)

### <a name="hierarchy-example"></a>Példa hierarchia

Vegyünk egy példát, `building` `floor`ahol `room` a **H1** hierarchia rendelkezik , és a **instanceFieldNames** definíció részeként:

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

Az előző definícióban és több idősorozatban használt példánymezők alapján a hierarchia attribútumai és értékei az alábbi táblázatban látható módon jelennek meg:

| Idősorozat-azonosító | Példánymezők |
| --- | --- |
| ID1 | "épület" = "1000", "padló" = "10", "szoba" = "55"  |
| ID2 | "épület" = "1000", "szoba" = "55" |
| ID3 | "padló" = "10" |
| ID4 | "épület" = "1000", "padló" = "10"  |
| ID5 | Egyik "épület", "padló", vagy "szoba" sincs beállítva. |

A Time Series **ID1** és **az ID4** a **H1** hierarchia részeként jelenik meg az [Azure Time Series Insights explorerben,](time-series-insights-update-explorer.md) mert teljesen meghatározott és helyesen rendezett *épület,* *padló*és *szoba* paraméterekkel rendelkeznek.

A többi példány a *fölénnem szülő példányok* közé van besorolva, mert nem felelnek meg a megadott adathierarchiának.

## <a name="time-series-model-types"></a>Idősorozat-modelltípusok

Az Idősorozat-modell *típusok* segítségével változókat vagy képleteket definiálhatsz a számításokhoz. A típusok egy adott Time Series Insights-példányhoz vannak társítva.

Egy típusnak lehet egy vagy több változója. Például egy Idősorozat-modell példány *hőmérséklet-érzékelő*típusú lehet, amely az *átlagos hőmérséklet*, *a min hőmérséklet*és a maximális *hőmérséklet*változókból áll.

A [Contoso Wind Farm bemutató](https://insights.timeseries.azure.com/preview/samples) több, a megfelelő példányokkal társított Idősorozat-modelltípust jelenít meg.

[![Példa a Time Series Model típusra](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> A Time Series Insights Instance API és a CRUD támogatás, olvassa el az [adatok lekérdezése](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) cikket, és a [Type API REST dokumentációt.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)

### <a name="type-properties"></a>Típus tulajdonságai

Az idősorozat-modell típusokat **az id**, **név**, **leírás**és **változók határozzák**meg .

| Tulajdonság | Leírás |
| ---| ---|
| id | A típus UUID azonosítója. |
| név | A típus nevének megadására használt karakterlánc. |
| leírás | A típus karakterlánc-leírása. |
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

A Time Series Insights-típusok számos változóval rendelkezhetnek, amelyek képlet- és számítási szabályokat határoznak meg az eseményekre vonatkozóan.

Minden változó *háromféle*lehet: *numerikus*, *kategorikus*és *összesítő*.

* **A numerikus** fajták folyamatos értékekkel dolgoznak. 
* **A kategorikus** fajták különálló értékek meghatározott készletével dolgoznak.
* **Az összesítő** értékek több, egyetlen típusú változót egyesítenek (vagy az összes numerikus vagy az összes kategorikus).

Az alábbi táblázat bemutatja, hogy mely tulajdonságok relevánsak az egyes változók esetében.

[![Idősorozat modell változótáblája](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numerikus változók

| Változó tulajdonság | Leírás |
| --- | ---|
| Változó szűrő | A szűrők nem kötelező feltételes záradékok a számításhoz figyelembe vett sorok számának korlátozására. |
| Változó érték | Az eszközről vagy érzékelőkről származó vagy a Time Series-kifejezések használatával átalakított számítási telemetriai értékek. A numerikus típusváltozóknak *Dupla*típusúaknak kell lenniük.|
| Változó interpoláció | Az interpoláció azt határozza meg, hogyan lehet rekonstruálni egy jelet a meglévő adatok felhasználásával. *Numerikus* változókhoz *lépés-* és lineáris interpolációs beállítások állnak rendelkezésre. |
| Változó összesítése | Az átlagos, a *min,* *a* *max,* *az szum,* *a darabszám*, az *utolsó,* az *utolsó* és az idővel súlyozott (*átlag,* *min,* *max,* *összeg*, *bal*) operátorokon keresztül történő számítás támogatása. |

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
| Változó szűrő | A szűrők nem kötelező feltételes záradékok a számításhoz figyelembe vett sorok számának korlátozására. |
| Változó érték | Az eszközről vagy érzékelőkből származó számítási telemetriai értékek. A kategorikus típusú változóknak *hosszúnak* vagy *karakterláncnak*kell lenniük. |
| Változó interpoláció | Az interpoláció azt határozza meg, hogyan lehet rekonstruálni egy jelet a meglévő adatok felhasználásával. A *Lépés* interpolációs beállítás kategorikus változókhoz érhető el. |
| Változó kategóriák | A kategóriák leképezést hoznak létre az eszközről vagy az érzékelőkről egy címkére érkező értékek között. |
| Változó alapértelmezett kategóriája | Az alapértelmezett kategória minden olyan értékre vonatkozik, amely nincs leképezve a "kategóriák" tulajdonságban. |

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
      "values": [0, 1, 2],
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
| Változó szűrő | A szűrők nem kötelező feltételes záradékok a számításhoz figyelembe vett sorok számának korlátozására. |
| Változó összesítése | Az átlag *,* *a maximum*, *a max*, *az összeg*, a *darabszám*, *az első*, *az utolsó*. |

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

A változók egy idősorozat-modell típusdefiníciójában tárolódnak, és a tárolt definíció felülbírálásához [a lekérdezési API-kon](time-series-insights-update-tsq.md) keresztül inline-t is megadhatók.

## <a name="next-steps"></a>További lépések

- Olvassa el [az Azure Time Series Insights előzetes tártárházának és be- és be- és információinak megtekintését.](./time-series-insights-update-storage-ingress.md)

- Ismerje meg a Time Series Model gyakori műveleteit az [Azure Time Series Insights előzetes verziójában az adatmodellezésben](./time-series-insights-update-how-to-tsm.md)

- Olvassa el az új [Time Series Model](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) referenciadokumentációt.
