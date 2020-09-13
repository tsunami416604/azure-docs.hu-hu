---
title: T-SQL-nézetek a szinapszis SQL használatával
description: Tippek a T-SQL-nézetek használatához és megoldások a szinapszis SQL-sel való fejlesztéséhez.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: fafa0c2e1b02cc49bfb852ed7770b0927b0e9334
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032724"
---
# <a name="t-sql-views-using-synapse-sql"></a>T-SQL-nézetek a szinapszis SQL használatával
Ebből a cikkből megtudhatja, hogyan használhatja a T-SQL nézeteket, és hogyan fejleszthet megoldásokat a szinapszis SQL használatával. 

## <a name="why-use-views"></a>A nézetek használata

A nézetek számos különböző módon használhatók a megoldás minőségének javítására.  Ez a cikk néhány példát mutat be arra, hogyan gazdagíthatja a megoldásait nézetekkel, és tartalmazza a megfontolandó korlátozásokat.

### <a name="sql-pool---create-view"></a>SQL-készlet – nézet létrehozása

> [!NOTE]
> **SQL-készlet**: a létrehozás nézet szintaxisa nem szerepel ebben a cikkben. További információ: [create View](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dokumentáció.

### <a name="sql-on-demand-preview---create-view"></a>Igény szerinti SQL-(előzetes verzió) – nézet létrehozása

> [!NOTE]
> **SQL on-demand**: a létrehozás nézet szintaxisa nem szerepel ebben a cikkben. További információ: [create View](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) dokumentáció.

## <a name="architectural-abstraction"></a>Építészeti absztrakció

Gyakori alkalmazási minta a táblák újbóli létrehozása a [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) használatával, amelyet egy objektum átnevezési mintája követ az adatbetöltése során.

A következő példa új dátum rekordokat vesz fel egy dátum dimenzióba. Vegye figyelembe, hogy egy új tábla, DimDate_New, először jön létre, majd átnevezve lett a tábla eredeti verziójának helyére.

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

Ne feledje, hogy ez a megközelítés azt eredményezheti, hogy a táblák megjelenhetnek és eltűnnek a felhasználó nézetében, és a "táblázat nem létezik" hibaüzenetek jelennek meg. A nézetek segítségével konzisztens megjelenítési réteget biztosíthat a felhasználók számára, miközben az alapul szolgáló objektumok átnevezve lesznek.

Azáltal, hogy a nézetekben hozzáférést biztosít az adathozzáféréshez, a felhasználóknak nincs szükségük a mögöttes táblák láthatóságára. Az egységes felhasználói élmény mellett ez a réteg biztosítja, hogy az elemzési tervezők is fejlődjön az adatmodellben. Az alapul szolgáló táblák fejlesztése azt jelenti, hogy a tervezők a CTAS használatával maximalizálják a teljesítményt az betöltési folyamat során.

## <a name="performance-optimization"></a>Teljesítményoptimalizálás

A nézetek a táblázatok közötti teljesítményre optimalizált illesztések betartatására is használhatók. Egy nézet például belefoglalhatja a redundáns terjesztési kulcsot a csatlakozás feltételeinek részeként az adatáthelyezés csökkentése érdekében.

Egy adott lekérdezés kényszerítése vagy a tipphez való csatlakozás másik előnye a T-SQL-nézetek használata. Így a nézetek funkció biztosítja, hogy az illesztések mindig optimális módon legyenek elvégezve. Ne feledje, hogy a felhasználóknak emlékezniük kell a megfelelő szerkezetre az illesztésekhez.

## <a name="limitations"></a>Korlátozások

A szinapszis SQL nézetei csak metaadatokként vannak tárolva. Ennek következtében a következő beállítások nem érhetők el:

* Nincs séma-kötési beállítás
* Az alaptáblák nem frissíthetők a nézeten keresztül
* Ideiglenes táblákon nem hozhatók létre nézetek
* A kibontási/kibontási útmutatók nem támogatottak
* Nincsenek indexelt nézetek a szinapszis SQL-ben

## <a name="next-steps"></a>Következő lépések

További fejlesztési tippek: a [SZINAPSZIS SQL-fejlesztés áttekintése](develop-overview.md).



