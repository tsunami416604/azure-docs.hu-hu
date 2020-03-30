---
title: Sorátalakítás módosítása az adatfolyam leképezésében
description: Az adatbázis-cél frissítése az alteregósor-átalakítással az adatfolyam leképezésében
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/08/2020
ms.openlocfilehash: 0798a3f9ab45ce68086681e7aea96deeb9639f94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834542"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Sorátalakítás módosítása az adatfolyam leképezésében

A sorok beszúrási, törlési, frissítési és upsert házirendjeinek beállításához használja a Sor módosítása átalakítót. Kifejezésként egy-a-többhöz feltételeket adhat hozzá. Ezeket a feltételeket prioritási sorrendben kell megadni, mivel minden sor az első egyezési kifejezésnek megfelelő házirenddel lesz megjelölve. Ezen feltételek mindegyike egy sor (vagy sor) beszúrását, frissítését, törlését vagy upserted beszúrását eredményezheti. Az Alter Sor ddl-& DML-műveleteket is képes létrehozni az adatbázison.

![Sorbeállítások módosítása](media/data-flow/alter-row1.png "Sorbeállítások módosítása")

Alter Sor átalakítások csak akkor működik, az adatbázis vagy cosmosDB elsüllyeszti az adatfolyamban. A sorokhoz rendelt műveletek (beszúrás, frissítés, törlés, upsert) nem történnek a hibakeresési munkamenetek során. Futtasson egy adatfolyam-tevékenységet egy folyamatban az adatbázistáblák altersorházirendjeinek életbe léptetéséhez.

## <a name="specify-a-default-row-policy"></a>Alapértelmezett sorházirend megadása

Hozzon létre egy Alter Row átalakítást, `true()`és adjon meg egy sorházirendet a feltételével. Minden olyan sor, amely nem felel meg a korábban definiált kifejezések egyikének sem, meg lesz jelölve a megadott sorházirendhez. Alapértelmezés szerint minden olyan sor, amely nem felel `Insert`meg egyetlen feltételes kifejezésnek sem, meg lesz jelölve a számára.

![Sorházirend módosítása](media/data-flow/alter-row4.png "Sorházirend módosítása")

> [!NOTE]
> Ha az összes sort egy házirenddel szeretné megjelölni, létrehozhat `true()`egy feltételt a házirendhez, és megadhatja a feltételt .

## <a name="view-policies-in-data-preview"></a>Házirendek megtekintése az adatelőnézetben

A [hibakeresési mód segítségével](concepts-data-flow-debug-mode.md) megtekintheti az alteregósor-házirendek eredményeit az adatok betekintő ablaktáblájában. Egy alteregósor-átalakítás adatelőnézete nem eredményez DDL- vagy DML-műveleteket a célhoz képest.

![Sorházirendek módosítása](media/data-flow/alter-row3.png "Sorházirendek módosítása")

Minden alter sorházirendet egy ikon jelöl, amely jelzi, hogy beszúrási, frissítési, upsert vagy törölt művelet történik-e. A felső fejléc azt mutatja, hogy az előnézetben hány sort érint az egyes házirendek.

## <a name="allow-alter-row-policies-in-sink"></a>Sorházirendek módosításának engedélyezése a fogadóban

A változássor-házirendek működéséhez az adatfolyamnak egy adatbázisba vagy a Cosmos-fogadóba kell írnia. A **fogadó Beállítások** lapján engedélyezze, hogy az adott fogadóhoz mely sorházirendek módosíthatók.

![Sorelesső módosítása](media/data-flow/alter-row2.png "Sormosogató módosítása")

 Az alapértelmezett viselkedés az, hogy csak a beszúrások engedélyezése. A frissítések, upserts vagy törlések engedélyezéséhez jelölje be az adott feltételnek megfelelő fogadóban lévő jelölőnégyzetet. Ha a frissítések, a upserts vagy a deleteek engedélyezve vannak, meg kell adnia, hogy a fogadóban mely kulcsoszlopoknak kell megfelelnie.

> [!NOTE]
> Ha a beszúrások, frissítések vagy upserts módosítja a sémát a céltábla a fogadóban, az adatfolyam sikertelen lesz. Az adatbázis célsémájának módosításához válassza a **Tábla újbóli létrehozása** táblaműveletként lehetőséget. Ezzel eldobja és újra létrehozza a táblát az új sémadefinícióval.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy módosított `CleanData` sorátalakítás, amely `SpecifyUpsertConditions` egy bejövő adatfolyamot vesz fel, és három altersorfeltételt hoz létre. Az előző átalakítássorán a `alterRowCondition` program kiszámítja az elnevezett oszlopot, amely meghatározza, hogy egy sor beszúrva, frissítve vagy törölve van-e az adatbázisban. Ha az oszlop értéke olyan karakterlánc-értékkel rendelkezik, amely megegyezik a módosító sorszabályával, akkor az adott házirendhöz van rendelve.

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Például sor módosítása](media/data-flow/alter-row4.png "Például sor módosítása")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>További lépések

Az Alter Sor átalakítás után érdemes lehet [az adatokat a céladattárba süllyeszteni.](data-flow-sink.md)
