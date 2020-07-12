---
title: 'Gyors útmutató: igény szerinti SQL használata'
description: Ebből a rövid útmutatóból megtudhatja, hogy milyen egyszerű a különböző típusú fájlok lekérdezése az SQL on-demand (előzetes verzió) használatával.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fe07192b0077518cdd73092f53342c298034cfa8
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274169"
---
# <a name="quickstart-use-sql-on-demand"></a>Gyors útmutató: igény szerinti SQL használata

A szinapszis SQL on-demand (előzetes verzió) egy kiszolgáló nélküli lekérdezési szolgáltatás, amely lehetővé teszi, hogy SQL-lekérdezéseket futtasson az Azure Storage-ban elhelyezett fájlokon. Ebből a rövid útmutatóból megtudhatja, hogyan kérdezheti le a különböző típusú fájlokat az SQL igény szerinti használatával. A támogatott formátumok a [OpenRowset](sql/develop-openrowset.md)-ben vannak felsorolva.

Ez a rövid útmutató a következő lekérdezéseket mutatja be: CSV, Apache Parquet és JSON-fájlok.

## <a name="prerequisites"></a>Előfeltételek

Válasszon ki egy SQL-ügyfelet a lekérdezések kibocsátásához:

- Az [Azure szinapszis Studio](quickstart-synapse-studio.md) egy webes eszköz, amellyel böngészheti a fájlokat a tárolóban, és SQL-lekérdezéseket hozhat létre.
- A [Azure Data Studio](sql/get-started-azure-data-studio.md) egy ügyfél-eszköz, amely lehetővé teszi, hogy SQL-lekérdezéseket és jegyzetfüzeteket futtasson az igény szerinti adatbázison.
- A [SQL Server Management Studio](sql/get-started-ssms.md) egy ügyfél-eszköz, amely lehetővé teszi, hogy SQL-lekérdezéseket futtasson az igény szerinti adatbázison.

A rövid útmutató paraméterei:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Igény szerinti SQL-szolgáltatás végpontjának címe    | Kiszolgáló neveként használatos                                   |
| Igény szerinti SQL-szolgáltatás végpontjának régiója     | Annak meghatározására szolgál, hogy milyen tárterületet fogunk használni a mintákban |
| A végpontok hozzáférésének felhasználóneve és jelszava | Végponthoz való hozzáféréshez használatos                               |
| A nézetek létrehozásához használt adatbázis         | A mintákban kezdőpontként használt adatbázis       |

## <a name="first-time-setup"></a>Első beállítás

A minták használata előtt:

- Adatbázis létrehozása a nézetekhez (ha a nézeteket szeretné használni)
- A Storage-ban tárolt fájlok eléréséhez igény szerint használandó hitelesítő adatok létrehozása

### <a name="create-database"></a>Adatbázis létrehozása

Saját adatbázis létrehozása bemutató céljára. Ezt az adatbázist fogja használni a nézetek létrehozásához és a minta lekérdezésekhez ebben a cikkben.

> [!NOTE]
> Az adatbázisokat csak a metaadatok megtekintésére használják, a tényleges adatok esetében nem.
>Jegyezze fel az adatbázis nevét, amelyet később a gyors útmutatóban használhat.

Használja a következő lekérdezést, amely a `mydbname` választott névre változik:

```sql
CREATE DATABASE mydbname
```

### <a name="create-data-source"></a>Adatforrás létrehozása

Ha SQL igény szerint szeretné futtatni a lekérdezéseket, hozzon létre olyan adatforrást, amelyet az SQL igény szerint használhat a tárolóban lévő fájlok eléréséhez.
A következő kódrészlet végrehajtásával hozza létre az ebben a szakaszban található mintákban használt adatforrást:

```sql
-- create master key that will protect the credentials:
CREATE MASTER KEY ENCRYPTION BY PASSWORD = <enter very strong password here>

-- create credentials for containers in our demo storage account
CREATE DATABASE SCOPED CREDENTIAL sqlondemand
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
GO
CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
    CREDENTIAL = sqlondemand
);
```

## <a name="query-csv-files"></a>CSV-fájlok lekérdezése

A következő kép a lekérdezni kívánt fájl előnézete:

![A CSV-fájl első 10 sora fejléc nélkül, Windows stílusú új sor.](./sql/media/query-single-csv-file/population.png)

A következő lekérdezés azt mutatja be, hogyan lehet beolvasni egy olyan CSV-fájlt, amely nem tartalmaz fejlécsort, Windows-stílusú új sorral és vesszővel tagolt oszlopokkal:

```sql
SELECT TOP 10 *
FROM OPENROWSET
  (
      BULK 'csv/population/*.csv',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT = 'CSV', PARSER_VERSION = '2.0'
  )
WITH
  (
      country_code VARCHAR (5)
    , country_name VARCHAR (100)
    , year smallint
    , population bigint
  ) AS r
WHERE
  country_name = 'Luxembourg' AND year = 2017
```

A sémát megadhatja a lekérdezés fordítási idején.
További példákat a [CSV-fájl lekérdezése](sql/query-single-csv-file.md)című témakörben talál.

## <a name="query-parquet-files"></a>Parquet-fájlok lekérdezése

Az alábbi példa a Parquet-fájlok lekérdezésének automatikus séma-következtetéseit mutatja be. A 2017 szeptemberében visszaadja a sorok számát a séma meghatározása nélkül.

> [!NOTE]
> A Parquet-fájlok olvasásakor nem kell megadnia az oszlopok a `OPENROWSET WITH` záradékban. Ebben az esetben az SQL on-demand metaadatokat használ a Parquet fájlban, és az oszlopokat név szerint köti össze.

```sql
SELECT COUNT_BIG(*)
FROM OPENROWSET
  (
      BULK 'parquet/taxi/year=2017/month=9/*.parquet',
      DATA_SOURCE = 'SqlOnDemandDemo',
      FORMAT='PARQUET'
  ) AS nyc
```

További információ a [Parquet-fájlok lekérdezéséről](sql/query-parquet-files.md).

## <a name="query-json-files"></a>JSON-fájlok lekérdezése

### <a name="json-sample-file"></a>JSON-minta fájl

A fájlok *JSON* -tárolóban, *címjegyzékekben*tárolódnak, és az alábbi struktúrával rendelkező egykönyves bejegyzéseket tartalmaznak:

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

### <a name="query-json-files"></a>JSON-fájlok lekérdezése

Az alábbi lekérdezés azt mutatja be, hogyan használhatók a [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) a skaláris értékek (title, kiadó) egy könyvből való lekéréséhez a *Cryptology-ben a valószínűséggel és statisztikai metódusokkal, a kiválasztott cikkek bevezetésével*:

```sql
SELECT
    JSON_VALUE(jsonContent, '$.title') AS title
  , JSON_VALUE(jsonContent, '$.publisher') as publisher
  , jsonContent
FROM OPENROWSET
  (
      BULK 'json/books/*.json',
      DATA_SOURCE = 'SqlOnDemandDemo'
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
> A teljes JSON-fájlt egysoros/oszlopként olvasjuk. Így a FIELDTERMINATOR, a FIELDQUOTE és a ROWTERMINATOR a 0x0b értékre van állítva, mert nem várjuk, hogy megkeresjük a fájlban.

## <a name="next-steps"></a>Következő lépések

Most már készen áll a folytatásra a következő cikkekkel:

- [Egyetlen CSV-fájl lekérdezése](sql/query-single-csv-file.md)
- [Mappák és több CSV-fájl lekérdezése](sql/query-folders-multiple-csv-files.md)
- [Adott fájlok lekérdezése](sql/query-specific-files.md)
- [Parquet-fájlok lekérdezése](sql/query-parquet-files.md)
- [A Parquet beágyazott típusainak lekérdezése](sql/query-parquet-nested-types.md)
- [JSON-fájlok lekérdezése](sql/query-json-files.md)
- [Nézetek létrehozása és használata](sql/create-use-views.md)
- [Külső táblák létrehozása és használata](sql/create-use-external-tables.md)
- [Lekérdezési eredmények megőrzése az Azure Storage-ban](sql/create-external-table-as-select.md)
- [Egyetlen CSV-fájl lekérdezése](sql/query-single-csv-file.md)
