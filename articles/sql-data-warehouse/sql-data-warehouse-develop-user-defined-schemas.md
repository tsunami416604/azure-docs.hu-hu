---
title: Felhasználó által definiált sémák használata
description: Tippek a T-SQL felhasználó által definiált sémák használatához Azure SQL Data Warehouse a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 697bffa36e9b208c1a027654df81fb356ddfc8ed
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685815"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Felhasználó által definiált sémák használata a SQL Data Warehouseban
Tippek a T-SQL felhasználó által definiált sémák használatához Azure SQL Data Warehouse a megoldások fejlesztéséhez.

## <a name="schemas-for-application-boundaries"></a>Az alkalmazás határaihoz tartozó sémák

A hagyományos adattárházak gyakran külön adatbázisokat használnak az alkalmazások határainak a munkaterhelés, a tartomány vagy a biztonság alapján történő létrehozásához. Előfordulhat például, hogy egy hagyományos SQL Server adattárház tartalmaz egy átmeneti adatbázist, egy adattárház-adatbázist és néhány data mart-adatbázist. Ebben a topológiában az egyes adatbázisok munkaterhelésként és biztonsági határként működnek az architektúrában.

Ezzel szemben a SQL Data Warehouse egy adatbázison belül futtatja a teljes adattárház-munkafolyamatot. Az adatbázisok közötti illesztések nem engedélyezettek. Ezért SQL Data Warehouse elvárja, hogy a raktár által használt összes táblát az egyetlen adatbázisban tárolja.

> [!NOTE]
> A SQL Data Warehouse nem támogatja a bármilyen típusú adatbázisok lekérdezését. Ennek következtében az ezt a mintát használó adatraktár-implementációkat felül kell vizsgálni.
> 
> 

## <a name="recommendations"></a>Javaslatok
Ezek a számítási feladatok, a biztonság, a tartomány és a funkcionális határok konszolidálása a felhasználó által definiált sémák használatával

1. Az adatraktár teljes számítási feladatának futtatásához használjon egy SQL Data Warehouse adatbázist
2. A meglévő adattárház-környezet konszolidálása egy SQL Data Warehouse adatbázis használatára
3. **Felhasználó által definiált sémák** használata a korábban adatbázisokkal megvalósított határ biztosításához.

Ha a felhasználó által definiált sémákat korábban nem használták, akkor tiszta lappal rendelkezik. Egyszerűen használja a régi adatbázis nevét a felhasználó által definiált sémák alapjául a SQL Data Warehouse adatbázisban.

Ha a sémák már használatban vannak, néhány lehetőség közül választhat:

1. Távolítsa el a régi sémák nevét, és kezdje el a frissen
2. A régi séma nevének megtartása előre függőben a régi séma nevének a tábla nevére
3. Tartsa meg a régi sémák nevét úgy, hogy egy további sémában lévő nézeteket implementál, hogy újra létrehozza a régi séma-struktúrát.

> [!NOTE]
> Előfordulhat, hogy a 3. első ellenőrzési lehetőség a leginkább vonzó megoldásnak tűnik. Az ördög azonban részletesen szerepel. A nézetek csak SQL Data Warehouse olvashatók. Az alaptáblán minden adat-vagy tábla-módosítást el kell végezni. A 3. lehetőség is bevezeti a nézet rétegét a rendszerbe. Érdemes lehet ezt néhány további gondolatot használni, ha már használja a nézeteket az architektúrában.
> 
> 

### <a name="examples"></a>Példák:
Felhasználó által definiált sémák implementálása az adatbázisok nevei alapján

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

A régi sémák nevét megtarthatja a tábla nevére való előre függőben. Sémák használata a munkaterhelés határához.

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

Örökölt sémák nevének megőrzése nézetek használatával

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
> A séma-stratégiában bekövetkező változásoknak át kell tekinteniük az adatbázis biztonsági modelljét. Sok esetben a séma szintjén rendelheti hozzá az engedélyeket a biztonsági modell egyszerűsítéséhez. Ha további részletességi engedélyekre van szükség, akkor használhatja az adatbázis-szerepköröket.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

