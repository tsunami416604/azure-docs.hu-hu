---
title: Az SQL igény szerinti használata (előzetes verzió)
description: Ebben a rövid útmutatóban megtudhatja, hogy milyen egyszerű lekérdezni a különböző típusú fájlokat az SQL on-demand (előzetes verzió) használatával.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 7ab61b6b0243abea0f5e618c9e83d770f7eaeac7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424383"
---
# <a name="quickstart-using-sql-on-demand"></a>Rövid útmutató: SQL igény szerinti használata

A Synapse SQL igény szerinti (előzetes verzió) egy kiszolgáló nélküli lekérdezési szolgáltatás, amely lehetővé teszi az Sql-lekérdezések futtatását az Azure Storage-ban elhelyezett fájlokon. Ebben a rövid útmutatóban megtudhatja, hogyan lehet lekérdezni a különböző típusú fájlokat az SQL igény szerinti használatával.

A következő fájltípusok támogatottak: JSON, CSV, Apache Parquet

## <a name="prerequisites"></a>Előfeltételek

Válasszon egy SQL-ügyfelet a lekérdezések kérdéséhez:

- [Az Azure Synapse Studio](quickstart-synapse-studio.md) egy webes eszköz, amellyel tallózhat a tárolóban lévő fájlok között, és SQL-lekérdezést hozhat létre.
- [Az Azure Data Studio](sql/get-started-azure-data-studio.md) egy olyan ügyféleszköz, amely lehetővé teszi az SQL-lekérdezések és notebookok futtatását az igény szerinti adatbázisban.
- [Az SQL Server Management Studio](sql/get-started-ssms.md) egy olyan ügyféleszköz, amely lehetővé teszi az SQL-lekérdezések futtatását az igény szerinti adatbázisban.

A rövid útmutató paraméterei:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL igény szerinti szolgáltatás végpontcíme    | Kiszolgálónévként használva                                   |
| SQL igény szerinti szolgáltatásvégpont-régió     | Annak meghatározására szolgál, hogy milyen tárolót fogunk használni a mintákban |
| Felhasználónév és jelszó a végpontokhoz való hozzáféréshez | Végpont eléréséhez használva                               |
| Nézetek létrehozásához használt adatbázis         | A minták kiindulási pontjaként használt adatbázis       |

## <a name="first-time-setup"></a>Első beállítás

Minták használata előtt:

- Adatbázis létrehozása a nézetekhez (ha nézeteket szeretne használni)
- Az SQL igény szerinti hozzáférési adatainak létrehozása a tárolóban lévő fájlok eléréséhez

### <a name="create-database"></a>Adatbázis létrehozása

Hozzon létre saját adatbázist demo célokra. Ebben az adatbázisban hozhatja létre a nézeteket. Használja ezt az adatbázist a cikkben található mintalekérdezésekben.

> [!NOTE]
> Az adatbázisok csak a metaadatok megtekintésére szolgálnak, a tényleges adatokra nem.
>
> Írja le a rövid útmutató későbbi részében használható adatbázisnevet.

Használja a következő `mydbname` lekérdezést, és váltson egy választott névre:

```sql
CREATE DATABASE mydbname
```

### <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lekérdezések futtatása sql igény szerint, hozzon létre hitelesítő adatokat az SQL igény szerint a tárolóban lévő fájlok eléréséhez.

> [!NOTE]
> Vegye figyelembe, hogy létre kell hoznia a tárfiókhoz való hozzáféréshez szükséges hitelesítő adatokat. Bár az SQL igény szerinti hozzáférés különböző régiókból származó tárolókhoz férhet hozzá, a storage és az Azure Synapse munkaterület ugyanabban a régióban jobb teljesítményélményt nyújt.

Módosítsa a következő kódrészletet a CSV-, JSON- és parkettatárolók hitelesítő adatainak létrehozásához:

```sql
-- create credentials for containers in our demo storage account
IF EXISTS
   (SELECT * FROM sys.credentials
   WHERE name = 'https://sqlondemandstorage.blob.core.windows.net')
   DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
```

## <a name="querying-csv-files"></a>CSV-fájlok lekérdezése

A következő kép a lekérdezendő fájl előnézete:

![A CSV fájl első 10 sora fejléc nélkül, a Windows új sort stílusa.](./sql/media/query-single-csv-file/population.png)

A következő lekérdezés bemutatja, hogyan lehet olvasni egy olyan CSV-fájlt, amely nem tartalmaz fejlécsort, Windows stílusú új sorés vesszővel tagolt oszlopokkal:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/*.csv'
    , FORMAT = 'CSV'
    , FIELDTERMINATOR =','
    , ROWTERMINATOR = '\n'
  )
WITH
  (
      [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2
    , [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2
    , [year] smallint
    , [population] bigint
  ) AS [r]
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

A sémát a lekérdezés fordítási idejében adhatja meg.
További példákat a [CSV-fájl lekérdezése talál.](sql/query-single-csv-file.md)

## <a name="querying-parquet-files"></a>Parkettafájlok lekérdezése

Az alábbi minta a Parquet-fájlok lekérdezéséhez szükséges automatikus sémakövetkeztetési képességeket mutatja be. A sorok számát adja vissza 2017 szeptemberében séma megadása nélkül.

> [!NOTE]
> A Parquet fájlok `OPENROWSET WITH` olvasásakor nem kell oszlopokat megadnia a záradékban. Ebben az esetben az SQL igény szerinti metaadatokat használ a Parquet fájlban, és név szerint köti az oszlopokat.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet'
    , FORMAT='PARQUET'
  ) AS nyc
```

További információ [a parkettafájlok lekérdezéséről](sql/query-parquet-files.md)].

## <a name="querying-json-files"></a>JSON-fájlok lekérdezése

### <a name="json-sample-file"></a>JSON mintafájl

A fájlok *json* tárolóban, *mappakönyvekben*tárolódnak, és egykönyves bejegyzést tartalmaznak, amelynek szerkezete a következő:

```json
{  
   "_id":"ahokw88",
   "type":"Book",
   "title":"The AWK Programming Language",
   "year":"1988",
   "publisher":"Addison-Wesley",
   "authors":[  
      "Alfred V. Aho",
      "Brian W. Kernighan",
      "Peter J. Weinberger"
   ],
   "source":"DBLP"
}
```

### <a name="querying-json-files"></a>JSON-fájlok lekérdezése

A következő lekérdezés bemutatja, hogyan lehet [használni JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) lekérni skaláris értékek (cím, kiadó) egy könyv címe *valószínűségi és statisztikai módszerek cryptology, Bevezetés a kiválasztott cikkek:*

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'https://sqlondemandstorage.blob.core.windows.net/json/books/*.json'
    , FORMAT='CSV'
    , FIELDTERMINATOR ='0x0b'
    , FIELDQUOTE = '0x0b'
    , ROWTERMINATOR = '0x0b'
  )
WITH
  ( jsonContent varchar(8000) ) AS [r]
WHERE
  JSON_VALUE(jsonContent, '$.title') = 'Probabilistic and Statistical Methods in Cryptology, An Introduction by Selected Topics'
```

> [!IMPORTANT]
> A teljes JSON fájlt egy soros/oszlopként olvassuk, így a FIELDTERMINATOR, a FIELDQUOTEOR és a ROWTERMINATOR 0x0b-re van állítva, mert nem várjuk, hogy megtaláljuk a fájlban.

## <a name="next-steps"></a>További lépések

Most már készen áll a következő rövid útmutató cikkek:

- [Egyetlen CSV-fájl lekérdezése](sql/query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](sql/query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](sql/query-specific-files.md)
- [Parquet-fájlok lekérdezése](sql/query-parquet-files.md)
- [Parketta beágyazott típusai lekérdezése](sql/query-parquet-nested-types.md)
- [JSON-fájlok lekérdezése](sql/query-json-files.md)
- [Nézetek létrehozása és használata](sql/create-use-views.md)
- [Külső táblák létrehozása és használata](sql/create-use-external-tables.md)
- [Lekérdezéseredmény megőrzése az Azure storage-ban](sql/create-external-table-as-select.md)

A következő cikkből megtudhatja, hogyan kérdezhet le egyetlen CSV-fájlt.
> [!div class="nextstepaction"]
> [Egyetlen CSV-fájl lekérdezése](sql/query-single-csv-file.md)
