---
title: "Az SQL Data Warehouse felhasználói sémákkal |} Microsoft Docs"
description: "Tippek a Transact-SQL-sémák használata az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 52af5bd5-d5d3-4f9b-8704-06829fb924e3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: dfb58956ad6637cf0f50b4c052ab98fb7c26139d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Az SQL Data Warehouse felhasználói sémák
Hagyományos adatraktárak gyakran segítségével külön adatbázisban munkaterhelés, a tartomány vagy a biztonsági alapján alkalmazás határokat hozzon létre. Egy hagyományos SQL Server data warehouse tartalmazhat például egy átmeneti adatbázis, adatraktár-adatbázis és néhány adat adatközpont-adatbázis. Ebben a topológiában az egyes adatbázisok működik, a munkaterhelés és az architektúra biztonsági határ.

Az SQL Data Warehouse ellentétben a teljes adatraktározás számítási feladatáról egy adatbázison belül futtatja. Adatbázis közötti illesztések nem engedélyezettek. Ezért az SQL Data Warehouse egy adatbázis tárolja az adatraktár által használt összes táblának vár.

> [!NOTE]
> Az SQL Data Warehouse nem támogatja az alhálózatok közötti adatbázis-lekérdezések bármilyen típusú. Következésképpen adatok adatraktár megvalósításokhoz, amely ezt a mintát használja ki kell vizsgálni.
> 
> 

## <a name="recommendations"></a>Javaslatok
Ezek a javaslatok munkaterhelések, a biztonság, a tartomány és a működési határok konszolidálása felhasználó által definiált sémák használatával

1. A teljes adatraktár-számítási feladat futtatása egy SQL Data Warehouse-adatbázis segítségével
2. A meglévő adatok adatraktár környezetet egy SQL Data Warehouse-adatbázis használata a összesítése
3. Használja ki az **felhasználói sémákkal** a korábban az adatbázisok készletével megvalósított határ biztosításához.

Ha a felhasználó által definiált sémák nem korábban használt kell végrehajtania egy tiszta lappal. Egyszerűen a régi adatbázisnév alapjaként használni a a felhasználó által definiált lehetséges az SQL Data Warehouse-adatbázis.

Ha a séma már használták majd közül néhány:

1. Távolítsa el az örökölt séma nevét, és indítsa el a friss
2. Az örökölt séma nevek megőrzi által előre függőben lévő örökölt séma nevét a tábla neve
3. Továbbra is a hagyományos séma nevek nézetek implementálásával egy extra séma újra létre kell hoznia a régi séma struktúrát a tábla keresztül.

> [!NOTE]
> Az első ellenőrzés 3. lehetőség tűnhet a legtöbb tetszetős lehetőséget. Azonban a ördögöt a részletesen is. Nézetek az SQL Data Warehouse csak olvasható. További adatok vagy a tábla változtatások kell az alaptábla hajt végre. 3. lehetőség nézetek réteget is bevezeti a rendszerbe. Előfordulhat, hogy hozzárendelni kívánt Ez néhány további gondolat használata nézetek a architektúrában már.
> 
> 

### <a name="examples"></a>Példák:
Valósítja meg a felhasználó által definiált sémák alapján az adatbázis neve

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

Őriznie örökölt séma nevek előre függőben lévő őket a tábla neve. A munkaterhelés határ sémák használja.

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

A hagyományos séma nevek nézetek használatával

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
> A séma stratégia változásait a biztonsági modell áttekintése az adatbázis szüksége van. Sok esetben előfordulhat egyszerűsítése érdekében a biztonsági modell a séma szintű engedélyek meghatározásával. Ha részletesebb engedélyek is szükségesek, majd az adatbázis-szerepkörök is használhatja.
> 
> 

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
