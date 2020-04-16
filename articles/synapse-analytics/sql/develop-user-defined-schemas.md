---
title: Felhasználó által definiált sémák a szinapszis SQL-ben
description: Az alábbi szakaszokban különböző tippeket talál a T-SQL-felhasználó által definiált sémák használatához az Azure Synapse Analytics Synapse SQL-képességével rendelkező megoldások fejlesztéséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: ac4753da1405fe6b8cd209bb4899192e9f317aa1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428705"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Felhasználó által definiált sémák a szinapszis SQL-ben

Az alábbi szakaszokban különböző tippeket talál a T-SQL felhasználó által definiált sémák használatával a Synapse SQL-en belüli megoldások fejlesztéséhez.

## <a name="schemas-for-application-boundaries"></a>Sémák az alkalmazáshatárokhoz

A hagyományos elemzési architektúra gyakran külön adatbázisokat használ az alkalmazáshatárok létrehozásához a munkaterhelés, a tartomány vagy a biztonság alapján. Egy hagyományos SQL Server-elemzési infrastruktúra például tartalmazhat egy átmeneti adatbázist, egy elemzési adatbázist és egy adatpiac-adatbázisokat. Ebben a topológiában minden adatbázis számítási feladatok és biztonsági határként működik az architektúrában.

Ehelyett a Synapse SQL egy adatbázison belül futtatja a teljes elemzési számítási feladatot. A keresztadatbázis-illesztések nem engedélyezettek. A Szinapszis SQL elvárja, hogy a raktár által használt összes tábla egy adatbázisban tároljon.

> [!NOTE]
> Az SQL-készletek nem támogatják az adatbázisközi lekérdezéseket. Következésképpen a mintát kihasználó elemzési implementációkat felül kell vizsgálni. Az SQL igény szerinti (előzetes verzió) támogatja az adatbázisközi lekérdezéseket.

## <a name="user-defined-schema-recommendations"></a>Felhasználó által definiált sémajavaslatok

A számítási feladatok, a biztonság, a tartomány és a funkcionális határok felhasználói alapú sémák használatával történő konszolidálására vonatkozó javaslatok a következők:

- Egyetlen adatbázis segítségével futtassa a teljes elemzési számítási feladatot.
- Konszolidálja a meglévő elemzési környezetet egy adatbázis használatához.
- Használja ki a **felhasználó által definiált sémákat,** hogy a határ korábban adatbázisok használatával megvalósított.

Ha a felhasználó által definiált sémák at korábban még nem használták, akkor tiszta lappal rendelkezik. Használja a régi adatbázis nevét a synapse SQL-adatbázisban a felhasználó által definiált sémák alapjaként.

Ha a sémákmár használatosak, akkor van néhány lehetőség:

- Az örökölt sémanevek eltávolítása és újrakezdés
- Az örökölt sémanevek megtartása az örökölt séma nevének előzetes függőben tartása a táblanévhez
- Az örökölt sémanevek megtartása a tábla nézetei vel egy további sémában implementálva, amely újra létrehozza a régi sémastruktúrát.

> [!NOTE]
> Az első ellenőrzés, lehetőség 3 tűnhet, mint a legvonzóbb választás. A nézetek csak a Synapse SQL-ben olvashatók. Minden adatot vagy táblamódosítást az alaptáblán kell végrehajtani. 3. lehetőség is bevezet egy réteg nézetek a rendszerbe. Érdemes lehet, hogy ez néhány további gondolat, ha már használja a nézeteket az architektúra.
> 
> 

### <a name="examples"></a>Példák

Felhasználó által definiált sémák megvalósítása adatbázisnevek alapján.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Tartsa meg az örökölt séma neveket előre függőben lévő őket a tábla nevét. Használjon sémákat a munkaterhelés határához.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Az örökölt sémanevek megőrzése nézetek használatával.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> A sémastratégia bármilyen változása az adatbázis biztonsági modelljének felülvizsgálatát igényli. Sok esetben a séma szintjén engedélyek hozzárendelésével egyszerűsítheti a biztonsági modellt.

Ha részletesebb engedélyekre van szükség, használhatja az adatbázis-szerepköröket. Az adatbázis-szerepkörökről további információt az [Adatbázis-szerepkörök és felhasználók kezelése](../../analysis-services/analysis-services-database-users.md) című cikkben talál.

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [Synapse SQL-fejlesztés áttekintése című témakörben talál.](develop-overview.md)
