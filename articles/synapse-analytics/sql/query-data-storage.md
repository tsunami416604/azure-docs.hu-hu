---
title: Áttekintés – Adatok lekérdezése a tárolóban az SQL igény szerinti használatával (előzetes verzió)
description: Ez a szakasz mintalekérdezéseket tartalmaz, amelyek segítségével kipróbálhatja az SQL igény szerinti (előzetes verzió) erőforrást az Azure Synapse Analytics-en belül.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: cdad95b1a910a45629e85bcc716218b272afd9de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424901"
---
# <a name="overview-query-data-in-storage"></a>Áttekintés: Lekérdezési adatok a tárolóban

Ez a szakasz mintalekérdezéseket tartalmaz, amelyek segítségével kipróbálhatja az SQL igény szerinti (előzetes verzió) erőforrást az Azure Synapse Analytics-en belül.
Jelenleg támogatott fájlok a következők: 
- CSV
- Parketta
- JSON

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések kiadásához szükséges eszközök:

- SQL kliens az Ön által választott:
    - Azure Synapse Studio (előzetes verzió)
    - Azure Data Studio
    - SQL Server Management Studio

Ezenkívül a paraméterek a következők:

| Paraméter                                 | Leírás                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| SQL igény szerinti szolgáltatás végpontcíme    | A rendszer a kiszolgáló neveként fogja használni.                                   |
| SQL igény szerinti szolgáltatásvégpont-régió     | A mintákban használt tároló meghatározásához lesz használva. |
| Felhasználónév és jelszó a végpontokhoz való hozzáféréshez | A végpont eléréséhez lesz használva.                               |
| A nézetek létrehozásához használt adatbázis     | Ez az adatbázis lesz a minták kiindulópontja.       |

## <a name="first-time-setup"></a>Első beállítás

A cikk későbbi részében ismertetett minták használata előtt két lépésből áll:

- Adatbázis létrehozása a nézetekhez (ha nézeteket szeretne használni)
- Az SQL igény szerinti hitelesítő adatainak létrehozása a tárolóban lévő fájlok eléréséhez

### <a name="create-database"></a>Adatbázis létrehozása

A nézetek létrehozásához adatbázisra van szükség. Ezt az adatbázist fogja használni a dokumentációban szereplő egyes mintalekérdezésekhez.

> [!NOTE]
> Az adatbázisok csak metaadatok megtekintésére szolgálnak, a tényleges adatokra nem.  Írja le a használt adatbázisnevet, később szüksége lesz rá.

```sql
CREATE DATABASE mydbname;
```

### <a name="create-credentials"></a>Hitelesítő adatok létrehozása

A lekérdezések futtatása előtt létre kell hoznia a hitelesítő adatokat. Ezt a hitelesítő adatot az SQL igény szerinti szolgáltatás fogja használni a tárolóban lévő fájlok eléréséhez.

> [!NOTE]
> A Hogyan kell ebben a szakaszban sikeresen futtatni a Hogyan kell használni a SAS-jogkivonatot.
>
> A SAS-tokenek használatának megkezdéséhez el kell dobnia a UserIdentity azonosítót, amelyet a következő [cikk ismertet.](develop-storage-files-storage-access-control.md#disable-forcing-azure-ad-pass-through)
>
> Az IGÉNY szerinti SQL alapértelmezés szerint mindig AAD-átmenő értéket használ.

A tárolóhozzáférés-vezérlés kezelésével kapcsolatos további információkért tekintse meg ezt a [hivatkozást.](develop-storage-files-storage-access-control.md)

> [!WARNING]
> Létre kell hoznia egy hitelesítő adatokat egy tárfiók, amely a végpontrégióban található. Bár az SQL igény szerinti hozzáférés különböző régiókból származó tárolókhoz, az ugyanabban a régióban található tárolás és végpont jobb teljesítményélményt nyújt.

A CSV-, JSON- és Parkettatárolók hitelesítő adatainak létrehozásához futtassa az alábbi kódot:

```sql
-- create credentials for CSV container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/csv')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/csv]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for JSON container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/json')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/json]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO

-- create credentials for PARQUET container in our demo storage account
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://sqlondemandstorage.blob.core.windows.net/parquet')
DROP CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet];
GO

CREATE CREDENTIAL [https://sqlondemandstorage.blob.core.windows.net/parquet]
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D';
GO
```

## <a name="provided-demo-data"></a>Megadott bemutató adatok

A bemutató adatok a következő adatkészleteket tartalmazzák:

- NYC Taxi - Yellow Taxi Trip Records - része a nyilvános NYC adatkészlet
  - CSV formátum
  - Parquet formátum
- Népesség-adatkészlet
  - CSV formátum
- Minta parkettafájlok beágyazott oszlopokkal
  - Parquet formátum
- Könyvek JSON
  - JSON formátum

| Mappa elérési útja                                                  | Leírás                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| /csv/                                                        | Szülőmappa CSV formátumú adatokhoz                         |
| /csv/populáció/<br />/csv/population-unix/<br />/csv/populáció-unix-hdr/<br />/csv/population-unix-hdr-escape<br />/csv/population-unix-hdr-idézett | Különböző CSV-formátumú Population adatfájlokkal rendelkező mappák. |
| /csv/taxi/                                                   | Mappa NYC nyilvános adatfájlok CSV formátumban              |
| /parketta/                                                    | Szülőmappa a Parketta formátumú adatokhoz                     |
| /parketta/taxi                                                | NYC nyilvános adatfájlok Parketta formátumban, particionált év, és hónap segítségével Hive / Hadoop particionálási séma. |
| /parketta/beágyazott/                                             | Minta parkettafájlok beágyazott oszlopokkal                     |
| /json/                                                       | Szülőmappa JSON formátumú adatokhoz                        |
| /json/könyvek/                                                 | JSON fájlok könyvadatokkal                                   |

## <a name="validation"></a>Ellenőrzés

Hajtsa végre a következő három lekérdezést, és ellenőrizze, hogy a hitelesítő adatok megfelelően vannak-e létrehozva.

> [!NOTE]
> A mintalekérdezésekben szereplő összes URI-t az Észak-európai Azure-régióban található tárfiókot használ. Győződjön meg arról, hogy létrehozta a megfelelő hitelesítő adatokat. Futtassa az alábbi lekérdezést, és győződjön meg arról, hogy a tárfiók szerepel a listában.

```sql
SELECT name
FROM sys.credentials
WHERE
     name IN ( 'https://sqlondemandstorage.blob.core.windows.net/csv',
     'https://sqlondemandstorage.blob.core.windows.net/parquet',
     'https://sqlondemandstorage.blob.core.windows.net/json');
```

Ha nem találja a megfelelő hitelesítő adatokat, ellenőrizze [az első beállítás i.](#first-time-setup)

### <a name="sample-query"></a>Mintalekérdezés

Az ellenőrzés utolsó lépése a következő lekérdezés végrehajtása:

```sql
SELECT
    COUNT_BIG(*)
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

A fenti lekérdezésnek ezt a számot kell visszaadnia: **8945574**.

## <a name="next-steps"></a>További lépések

Most már készen áll a következő Cikkek folytatására:

- [Egyetlen CSV-fájl lekérdezése](query-single-csv-file.md)

- [Mappák és több CSV-fájl lekérdezése](query-folders-multiple-csv-files.md)

- [Adott fájlok lekérdezése](query-specific-files.md)

- [Parquet-fájlok lekérdezése](query-parquet-files.md)

- [Parketta beágyazott típusai lekérdezése](query-parquet-nested-types.md)

- [JSON-fájlok lekérdezése](query-json-files.md)

- [Nézetek létrehozása és használata](create-use-views.md)
