---
title: Áttekintés – adatlekérdezés a Storage-ban az SQL on-demand használatával (előzetes verzió)
description: Ez a szakasz azokat a lekérdezéseket tartalmazza, amelyekkel kipróbálhatja az Azure-beli SQL on-demand (előzetes verzió) erőforrást az Azure szinapszis Analyticsen belül.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8501f9d07ffa2d04915d4d1a351317cc145f9844
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118267"
---
# <a name="overview-query-data-in-storage"></a>Áttekintés: adatlekérdezés a Storage szolgáltatásban

Ez a szakasz azokat a lekérdezéseket tartalmazza, amelyekkel kipróbálhatja az Azure-beli SQL on-demand (előzetes verzió) erőforrást az Azure szinapszis Analyticsen belül.
A jelenleg támogatott formátumok a következők:  
- CSV
- Parquet
- JSON

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések kibocsátásához szükséges eszközök:

- Tetszőleges SQL-ügyfél:
    - Azure szinapszis Studio (előzetes verzió)
    - Azure Data Studio
    - SQL Server Management Studio

Emellett a paraméterek a következők:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Igény szerinti SQL-szolgáltatás végpontjának címe    | A kiszolgáló neveként lesz használva.                                   |
| Igény szerinti SQL-szolgáltatás végpontjának régiója     | A rendszer a mintákban használt tárterület meghatározására szolgál. |
| A végpontok hozzáférésének felhasználóneve és jelszava | A végpont eléréséhez lesz használva.                               |
| A nézetek létrehozásához használni kívánt adatbázis     | Ezt az adatbázist a rendszer kiindulási pontként fogja használni a mintákhoz.       |

## <a name="first-time-setup"></a>Első beállítás

Első lépésként létre kell **hoznia egy adatbázist** , amelyen végre fogja hajtani a lekérdezéseket. Ezután inicializálja az objektumokat a [telepítési parancsfájl](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) végrehajtásával az adatbázison. Ez a telepítési parancsfájl létrehozza az adatforrásokat, az adatbázis-hatókörrel rendelkező hitelesítő adatokat, valamint az ezekben a mintákban lévő adatok olvasásához használt külső fájlformátumokat.

> [!NOTE]
> Az adatbázisokat csak a metaadatok megtekintésére használják, nem a tényleges adatokhoz.  Jegyezze fel a használni kívánt adatbázis nevét, később szüksége lesz rá.

```sql
CREATE DATABASE mydbname;
```

## <a name="provided-demo-data"></a>Megadott bemutatói információk

A demo-adat a következő adatkészleteket tartalmazza:

- New York-i taxi – Yellow taxi Trip Records – a Public NYC adathalmazának része
  - CSV-formátum
  - Parquet formátum
- Populációs adatkészlet
  - CSV-formátum
- Parketta-fájlok beágyazott oszlopokkal
  - Parquet formátum
- Könyvek JSON
  - JSON formátum

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| CSV                                                        | CSV formátumú adatszülő mappa                         |
| /csv/population/<br />/csv/population-unix/<br />/csv/population-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-quoted | Különböző CSV formátumú adatfájlokkal rendelkező mappák. |
| /csv/taxi/                                                   | A New York-i nyilvános adatfájlok mappája CSV formátumban              |
| Parquet                                                    | Az adatszülő mappa a parketta formátumban                     |
| /parquet/taxi                                                | A New York-i nyilvános adatfájlok parkettás formátumban, évről évre, a kaptár/Hadoop particionálási sémával particionálva. |
| /parquet/nested/                                             | Parketta-fájlok beágyazott oszlopokkal                     |
| JSON                                                       | JSON formátumú adatszülő mappa                        |
| /json/books/                                                 | Könyvekből származó JSON-fájlok                                   |

### <a name="sample-query"></a>Mintalekérdezés

Az érvényesítés utolsó lépése a következő lekérdezés végrehajtása:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc;
```

A fenti lekérdezésnek a következő számot kell visszaadnia: **8945574**.

## <a name="next-steps"></a>További lépések

Most már készen áll a folytatásra a következő cikkekkel:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [A Parquet beágyazott típusainak lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)
