---
title: Felhasználó által definiált sémák használata
description: Tippek a T-SQL felhasználó által definiált sémák használatával megoldások fejlesztéséhez a Synapse SQL-készletben.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7144fa75d156ca7aed9d8215592f89c167cfb221
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633456"
---
# <a name="user-defined-schemas-in-synapse-sql-pool"></a>Felhasználó által definiált sémák a szinapszis SQL-készletben
Ez a cikk a T-SQL-felhasználó által definiált sémák használatával kapcsolatos számos tippet nyújt a Synapse SQL-készletben lévő megoldások fejlesztéséhez.

## <a name="schemas-for-application-boundaries"></a>Sémák az alkalmazáshatárokhoz

A hagyományos adatraktárak gyakran külön adatbázisokat használnak az alkalmazáshatárok létrehozásához a munkaterhelés, a tartomány vagy a biztonság alapján. 

Például egy hagyományos SQL Server adattárház tartalmazhat egy átmeneti adatbázist, egy adattárház-adatbázist és néhány adattárház-adatbázist. Ebben a topológiában minden adatbázis számítási feladatok és biztonsági határként működik az architektúrában.

Ezzel szemben az SQL-készlet egy adatbázison belül futtatja a teljes adattárház-munkaterhelést. A keresztadatbázis-illesztések nem engedélyezettek. Az SQL-készlet várakozásai szerint a raktár által használt összes táblát egy adatbázisban kell tárolni.

> [!NOTE]
> Az SQL-készlet semmilyen adatbázisközi lekérdezést nem támogat. Következésképpen az adattárház-implementációk, amelyek ezt a mintát kihasználják, felül kell vizsgálni.
> 
> 

## <a name="recommendations"></a>Javaslatok
A következőkben a számítási feladatok, a biztonság, a tartomány és a funkcionális határok felhasználói alapú sémák használatával történő konszolidálására vonatkozó javaslatok találhatók:

- Egyetlen SQL-készletadatbázis segítségével futtassa a teljes adattárház-munkaterhelést.
- Konszolidálja a meglévő adattárház-környezetet egy SQL-készletadatbázis használatához.
- Használja ki a **felhasználó által definiált sémákat,** hogy a határ korábban adatbázisok használatával megvalósított.

Ha a felhasználó által definiált sémák at korábban nem használták, akkor tiszta lappal rendelkezik. Használja a régi adatbázis nevét a felhasználó által definiált sémák alapjaként az SQL-készlet adatbázisában.

Ha a sémákmár használatosak, akkor van néhány lehetőség:

- Távolítsa el az örökölt sémaneveket, és kezdje újra.
- Őrizze meg az örökölt sémaneveket úgy, hogy az örökölt séma nevét előre meg kell őrizni a táblanévhez.
- Az örökölt sémanevek megtartása a tábla nézetei vel egy további sémában implementálva a régi sémastruktúra újbóli létrehozásához.

> [!NOTE]
> Az első ellenőrzési lehetőség 3 tűnhet, mint a legvonzóbb lehetőség. Azonban az ördög a részletekben rejlik. A nézetek csak az SQL-készletben olvashatók. Minden adatot vagy táblamódosítást az alaptáblán kell végrehajtani. 3. lehetőség is bevezet egy réteg nézetek a rendszerbe. Érdemes lehet, hogy ez néhány további gondolat, ha a nézetek használata az architektúra már.
> 
> 

### <a name="examples"></a>Példák:
Felhasználó által definiált sémák megvalósítása adatbázisnevek alapján:

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

Őrizheti meg az örökölt sémaneveket az előre függőben lévő kontrától a táblanévig. Sémák használata a munkaterhelés határához:

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

Örökölt sémanevek megtartása nézetek használatával:

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
> A sémastratégia bármilyen változását felül kell vizsgálni az adatbázis biztonsági modelljével. Sok esetben a séma szintjén engedélyek hozzárendelésével egyszerűsítheti a biztonsági modellt. Ha részletesebb engedélyekre van szükség, akkor adatbázis-szerepköröket is használhat.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

