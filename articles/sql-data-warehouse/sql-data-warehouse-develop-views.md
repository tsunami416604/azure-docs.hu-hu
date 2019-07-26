---
title: T-SQL-nézetek használata a Azure SQL Data Warehouseban | Microsoft Docs
description: Tippek a Azure SQL Data Warehouse T-SQL-nézeteinek használatához a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 8a770e66120e69271744942899186ece39b2a3c3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479521"
---
# <a name="views-in-azure-sql-data-warehouse"></a>A Azure SQL Data Warehouse nézetei
Tippek a Azure SQL Data Warehouse T-SQL-nézeteinek használatához a megoldások fejlesztéséhez. 

## <a name="why-use-views"></a>Miért érdemes a nézeteket használni?
A nézetek számos különböző módon használhatók a megoldás minőségének javítására.  Ez a cikk néhány példát mutat be arra, hogyan gazdagíthatja a megoldásait a nézetekkel, valamint a szükséges korlátozásokat.


> [!IMPORTANT]
> Tekintse meg az új, az anyagra vonatkozó nézet szintaxisát a kiválasztható [anyag létrehozása nézetben](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest).  További információ: [kibocsátási megjegyzések](/azure/sql-data-warehouse/release-notes-10-0-10106-0).
>


> [!NOTE]
> Ez a cikk nem tárgyalja a létrehozás nézet szintaxisát. További információ: [create View](/sql/t-sql/statements/create-view-transact-sql) dokumentáció.
> 

## <a name="architectural-abstraction"></a>Építészeti absztrakció

Gyakori alkalmazási minta a táblázatok újbóli létrehozása a CREATE TABLE AS SELECT (CTAS) használatával, amelyet egy objektum átnevezési mintája követ, az adatbetöltések során.

A következő példa új dátum rekordokat vesz fel egy dátum dimenzióba. Figyelje meg, hogy a rendszer először hozza létre az új táblát, a DimDate_New, majd átnevezi a tábla eredeti verziójának helyére.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Ez a megközelítés azonban azt eredményezheti, hogy a táblák megjelenhetnek, és nem jelennek meg a felhasználó nézetében, valamint a "táblázat nem létezik" hibaüzenetek. A nézetek segítségével konzisztens megjelenítési réteget biztosíthat a felhasználók számára, miközben az alapul szolgáló objektumokat átnevezi. Azáltal, hogy a nézetekben hozzáférést biztosít az adathozzáféréshez, a felhasználóknak nincs szükségük a mögöttes táblák láthatóságára. Ez a réteg egységes felhasználói élményt nyújt, és gondoskodik arról, hogy az adatraktár-tervezők is fejlődjön az adatmodellben. Az alapul szolgáló táblák fejlesztése azt jelenti, hogy a tervezők a CTAS használatával maximalizálják a teljesítményt az betöltési folyamat során.   

## <a name="performance-optimization"></a>Teljesítmény optimalizálása
A nézetek a táblázatok közötti, teljesítményre optimalizált illesztések betartatására is használhatók. Egy nézet például belefoglalhatja a redundáns terjesztési kulcsot a csatlakozás feltételeinek részeként az adatáthelyezés csökkentése érdekében. A nézetek egy másik előnye, hogy egy adott lekérdezést kényszerítenek, vagy a tipphez csatlakoznak. A nézetek ily módon történő használata garantálja, hogy az összekapcsolások mindig optimális módon történnek, elkerülve a felhasználóknak az illesztések helyes szerkezetének megemlékezését.

## <a name="limitations"></a>Korlátozások
A SQL Data Warehouse nézetei csak metaadatokként vannak tárolva. Ennek következtében a következő beállítások nem érhetők el:

* Nincs séma-kötési beállítás
* Az alaptáblák nem frissíthetők a nézeten keresztül
* Ideiglenes táblákon nem hozhatók létre nézetek
* A kibontási/kibontási útmutatók nem támogatottak
* Nincsenek indexelt nézetek a SQL Data Warehouse

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [SQL Data Warehouse fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).


