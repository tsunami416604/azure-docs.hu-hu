---
title: Térinformatikai adatindexek indexelése Azure Cosmos DB
description: Térbeli adatainak indexelése Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: 2cf682a404154b9c1bb94680b3adb673892c1c72
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2020
ms.locfileid: "77566373"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Térinformatikai adatindexek indexelése Azure Cosmos DB

Azure Cosmos DB adatbázis-motorját úgy terveztük, hogy valóban séma-agnosztikus legyen, és első osztályú támogatást nyújtson a JSON-hoz. A Azure Cosmos DB írásra optimalizált adatbázismotor natív módon értelmezi a GeoJSON standardban ábrázolt térbeli adatokra vonatkozó információkat.

Dióhéjban a geometria a geodéziai Koordinátákból egy 2D-síkra van kialakítva, majd fokozatosan, egy **quadtree**használatával osztva a cellákba. Ezek a cellák az 1D-re vannak leképezve a cella helye alapján a Hilbert belül, amely megőrzi a pontok helyi **kitöltését**. Emellett, ha a helyadatok indexelve lettek, egy **mozaikként**ismert folyamaton halad át, azaz az adott helyet keresztező összes cella azonosítható és kulcsként tárolódik a Azure Cosmos db indexben. Lekérdezéskor például a pontok és poligonok argumentumok is Csempézett, bontsa ki a megfelelő azonosító cellatartományokról, akkor az indexből adatok lekérésére használt.

Ha olyan indexelési házirendet ad meg, amely tartalmazza a/* (az összes elérési út) térbeli indexét, akkor a tárolóban található összes adathalmaz a hatékony térbeli lekérdezésekhez van indexelve.

> [!NOTE]
> Azure Cosmos DB támogatja a pontok, Linestring, sokszögek és többsokszögek indexelését
>
>

## <a name="geography-data-indexing-examples"></a>Földrajzi adatindexelési példák

A következő JSON-kódrészlet egy indexelési házirendet mutat be, amelyen engedélyezve van a **földrajzi** adattípusú térbeli indexelés. A földrajzi adattípussal rendelkező térbeli értékek esetében érvényes, és indexeli az GeoJSON pontot, a sokszöget, a többsokszögű vagy a LineString dokumentumokat a térbeli lekérdezésekhez. Ha az indexelési házirendet a Azure Portal használatával módosítja, megadhatja a következő JSON-t az indexelési házirendhez, hogy engedélyezze a térbeli indexelést a tárolón:

**Tároló-indexelési házirend JSON földrajzi térbeli indexeléssel**

```json
    {
       "automatic":true,
       "indexingMode":"Consistent",
        "includedPaths": [
        {
            "path": "/*"
        }
        ],
        "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
       "excludedPaths":[]
    }
```

> [!NOTE]
> Ha a hely a dokumentum GeoJSON érték helytelen formátumú vagy érvénytelen, majd, nem get indexelve a térbeli lekérdezéséhez. Hely értékek ST_ISVALID és ST_ISVALIDDETAILED használatával ellenőrizheti.
>
>
>

Az [indexelési házirendet](how-to-manage-indexing-policy.md) az Azure CLI, a PowerShell vagy bármely SDK használatával is módosíthatja.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulhatta, hogyan kezdheti el a földrajzi támogatásával az Azure Cosmos DB, ezután is:

* További információ a [Azure Cosmos db lekérdezésről](sql-query-getting-started.md)
* További információ a [térbeli adatainak a Azure Cosmos db való lekérdezéséről](sql-query-geospatial-query.md)
* További információ a [térinformatikai és a GeoJSON-hely adatainak Azure Cosmos db](sql-query-geospatial-intro.md)