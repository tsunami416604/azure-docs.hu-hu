---
title: Felhasználó által definiált sémák használata az SQL Data Warehouse |} A Microsoft Docs
description: Tippek a T-SQL-felhasználó által definiált sémák használata az Azure SQL Data Warehouse-megoldások fejlesztése.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: d46f41e75538fae230219068d3530b7181564ac0
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43302641"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Az SQL Data Warehouse használatával a felhasználó által definiált sémák
Tippek a T-SQL-felhasználó által definiált sémák használata az Azure SQL Data Warehouse-megoldások fejlesztése.

## <a name="schemas-for-application-boundaries"></a>A kérelem határok sémák

Hagyományos data warehouse-adattárházak a különálló adatbázisok gyakran használnak a számítási feladatok, a tartomány vagy a biztonsági alkalmazás határokat kíván létrehozni. Egy hagyományos SQL Server data warehouse tartalmazhat például egy átmeneti adatbázis, adatraktár-adatbázis és néhány adat adatközpont-adatbázis. Ebben a topológiában minden egyes adatbázis számítási feladatok és az architektúra biztonsági határon működik.

Ezzel szemben az SQL Data Warehouse fut, a teljes adatraktározás számítási feladatáról egy adatbázison belül. Adatbázis közötti illesztések nem engedélyezettek. Ezért az SQL Data Warehouse az egyetlen adatbázis tárolja az adatraktár által használt összes táblának vár.

> [!NOTE]
> Az SQL Data Warehouse nem támogatja semmiféle adatbázisközi lekérdezések. Ebből következően data warehouse megvalósításokhoz, az ezt a mintát kell vizsgálni.
> 
> 

## <a name="recommendations"></a>Javaslatok
Ezek a javaslatok számítási feladatok, a biztonság, a tartomány és a működési határok konszolidálása a felhasználó által definiált sémák

1. Egy SQL Data Warehouse-adatbázis használatával futtassa a teljes adatraktározás számítási feladatáról
2. Egyesíthetők a meglévő data warehouse környezet egy SQL Data Warehouse-adatbázis használatára
3. Használja ki **felhasználó által definiált sémák** a korábban az adatbázisok használatával implementált határ biztosításához.

Ha a felhasználó által definiált sémák nem korábban már használtak egy tiszta lappal rendelkezik majd. Egyszerűen használja a régi adatbázis neve alapján a felhasználó által definiált sémák, az SQL Data Warehouse-adatbázis.

Ha már használt sémák majd néhány lehetőségek állnak rendelkezésére:

1. Távolítsa el a régi sémanevek és kezdhet tiszta lappal
2. A tábla neve a régi sémanév őriznie a régi sémanevek a előre függőben
3. A régi sémanevek megőrzése a táblán egy extra sémában újra létre kell hoznia a régi séma struktúra nézetek alkalmazásával.

> [!NOTE]
> 3. lehetőség az első vizsgálat a legtöbb viszont lehetőség tűnhet. Azonban az ördögöt, a részleteket. Nézetek az SQL Data Warehouse csak olvasható. Bármilyen adatokat vagy a tábla módosítása kell az alaptábla végre a rendszer. 3. lehetőség nézetek réteget is bevezeti a rendszerbe. Ez néhány további gondolkodási biztosíthat használatakor nézetek az architektúrához már érdemes.
> 
> 

### <a name="examples"></a>Példák:
Felhasználó által definiált sémák alapján adatbázisnevek megvalósítása

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Megőrizni az örökölt sémanevek előre függőben lévő őket a tábla neve. A számítási feladatok határ sémák használja.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Nézetek használata az örökölt sémanevek megőrzése

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Séma stratégia bármilyen változás van szüksége az adatbázis a biztonsági modell áttekintése. Sok esetben előfordulhat egyszerűsítése érdekében a biztonsági modell által a séma szintű engedélyeket. Ha részletesebb engedélyek szükségesek, ezt követően használhatja az adatbázis-szerepkörökhöz.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

