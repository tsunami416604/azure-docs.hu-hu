---
title: Felhasználó által definiált sémák használata
description: Tippek a T-SQL felhasználó által definiált sémák használatához az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.
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
ms.openlocfilehash: a9ed4f01aae6ace1af6c1652fe3c5ecfe14dc6bf
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351548"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Felhasználó által definiált sémák használata az SQL Data Warehouse-ban
Tippek a T-SQL felhasználó által definiált sémák használatához az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.

## <a name="schemas-for-application-boundaries"></a>Sémák az alkalmazáshatárokhoz

A hagyományos adatraktárak gyakran külön adatbázisokat használnak az alkalmazáshatárok létrehozásához a munkaterhelés, a tartomány vagy a biztonság alapján. Egy hagyományos SQL Server adattárház például tartalmazhat egy átmeneti adatbázist, egy adattárház-adatbázist és néhány adattárház-adatbázist. Ebben a topológiában minden adatbázis számítási feladatként és biztonsági határként működik az architektúrában.

Ezzel szemben az SQL Data Warehouse egy adatbázison belül futtatja a teljes adattárház-munkaterhelést. A keresztadatbázis-illesztések nem engedélyezettek. Ezért az SQL Data Warehouse elvárja, hogy a raktár által használt összes tábla egy adatbázisban tároljon.

> [!NOTE]
> Az SQL Data Warehouse nem támogatja az adatbázisközi lekérdezéseket. Következésképpen az adattárház-implementációk, amelyek ezt a mintát kihasználják, felül kell vizsgálni.
> 
> 

## <a name="recommendations"></a>Javaslatok
Ezek a javaslatok a számítási feladatok, a biztonság, a tartomány és a funkcionális határok felhasználói definiált sémák használatával történő konszolidálására szolgálnak.

1. Egyetlen SQL Data Warehouse-adatbázis használata a teljes adattárház-munkaterhelés futtatásához
2. A meglévő adattárház-környezet összesítése egyetlen SQL Data Warehouse-adatbázis használatához
3. Használja ki a **felhasználó által definiált sémákat,** hogy a határ korábban adatbázisok használatával megvalósított.

Ha a felhasználó által definiált sémák at korábban nem használták, akkor tiszta lappal rendelkezik. Egyszerűen használja a régi adatbázis nevét, mint a felhasználó által definiált sémák alapja az SQL Data Warehouse adatbázisban.

Ha a sémákmár használatosak, akkor van néhány lehetőség:

1. Az örökölt sémanevek eltávolítása és újrakezdés
2. Az örökölt sémanevek megőrzése az örökölt séma nevének a táblanévhez való előzetes függőben
3. Az örökölt sémanevek megtartása a tábla nézetei vel egy további sémában implementálva a régi sémastruktúra újbóli létrehozásához.

> [!NOTE]
> Az első ellenőrzési lehetőség 3 tűnhet, mint a legvonzóbb lehetőség. Azonban az ördög a részletekben rejlik. A nézetek csak az SQL Data Warehouse-ban olvashatók. Minden adatot vagy táblamódosítást az alaptáblán kell végrehajtani. 3. lehetőség is bevezet egy réteg nézetek a rendszerbe. Érdemes lehet, hogy ez néhány további gondolat, ha a nézetek használata az architektúra már.
> 
> 

### <a name="examples"></a>Példák:
Felhasználó által definiált sémák megvalósítása adatbázisnevek alapján

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

Az örökölt sémanevek megőrzése a tábla nevéhez előre függőben lévő konklúdia szerint. Használjon sémákat a munkaterhelés határához.

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

Örökölt sémanevek megtartása nézetek használatával

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
> A sémastratégia bármilyen változását felül kell vizsgálni az adatbázis biztonsági modelljével. Sok esetben egyszerűsítheti a biztonsági modellt, ha a séma szintjén rendel engedélyeket. Ha részletesebb engedélyekre van szükség, akkor használhatja az adatbázis-szerepköröket.
> 
> 

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

