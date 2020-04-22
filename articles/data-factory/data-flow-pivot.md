---
title: Kimutatás átalakítása az adatfolyam leképezésében
description: Az Azure Data Factory adatfolyamat-transzformációjának sorokról oszlopokra történő kimutatása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686435"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Kimutatás átalakítása az adatfolyam leképezésében


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A kimutatás átalakításával több oszlopot hozhat létre egyetlen oszlop egyedi sorértékeiből. A pivot egy összesítési transzformáció, ahol oszlopok szerint csoportosítva választhatja ki a pivot oszlopokat, és [összesítő függvények](data-flow-expression-functions.md#aggregate-functions)segítségével hozhat létre pivot oszlopokat.

## <a name="configuration"></a>Konfiguráció

A pivot átalakításhoz három különböző bemenetre van szükség: oszlopok csoportja, a kimutatásbillentyű és a forgó oszlopok létrehozásának módjára

### <a name="group-by"></a>Csoportosítási szempont

![Csoportosítás beállítások szerint](media/data-flow/pivot2.png "[Csoportosítás beállítások szerint")

Válassza ki, hogy mely oszlopokra szeretné összesíteni a forgó oszlopokat. A kimeneti adatok az azonos csoportba sorba sorba csoportosítják az azonos csoportba sorokat egy sorba. A pivoted oszlopban végzett összesítés minden csoportfelett megtörténik.

Ez a szakasz nem kötelező. Ha nincs kijelölve oszloponkénti csoport, a program összesíti a teljes adatfolyamot, és csak egy sort továbbít.

### <a name="pivot-key"></a>Kimutatás billentyű

![Kimutatás billentyű](media/data-flow/pivot3.png "Kimutatás billentyű")

A kimutatáskulcs az az oszlop, amelynek sorértékei új oszlopokba kerülnek. Alapértelmezés szerint a kimutatás átalakítása új oszlopot hoz létre minden egyes egyedi sorértékhez.

**Az Érték**feliratú szakaszban megadhatja a fordítandó sorok at. Csak az ebben a szakaszban megadott sorértékek lesznek elforgatva. A **Null érték** engedélyezése elforgatott oszlopot hoz létre az oszlopban lévő null értékekhez.

### <a name="pivoted-columns"></a>Elforgatott oszlopok

![Elforgatott oszlopok](media/data-flow/pivot4.png "Elforgatott oszlopok")

Minden egyes egyedi kimutatáskulcs-értékhez, amely oszloprá válik, hozzon létre egy összesített sorértéket az egyes csoportokhoz. Kimutatásbillentyűnként több oszlopot is létrehozhat. Minden pivot oszlopnak legalább egy [összesítő függvényt kell tartalmaznia.](data-flow-expression-functions.md#aggregate-functions)

**Oszlopnév-minta:** Adja meg, hogyan szeretné formázni az egyes pivot oszlopok oszlopnevét. A kimenetelt oszlopnév a pivot kulcs értékének, az oszlopelőtagnak és a választható előtagnak a kombinációja lesz, elegendő, középső karakter. 

**Oszlop elrendezése:** Ha pivotbillentyűnként egynél több pivot oszlopot hoz létre, adja meg, hogyan szeretné megrendezni az oszlopokat. 

**Oszlopelőtag:** Ha pivotbillentyűnként egynél több pivot oszlopot hoz létre, minden oszlophoz adjon meg egy oszlopelőtagot. Ez a beállítás nem kötelező, ha csak egy elforgatott oszlopa van.

## <a name="help-graphic"></a>Súgókép

Az alábbi súgókép bemutatja, hogy a különböző pivot komponensek hogyan hatnak egymásra

![A kimutatás súgógrafikái](media/data-flow/pivot5.png "Kimutatás súgógrafikája")

## <a name="pivot-metadata"></a>Kimutatás metaadatai

Ha a pivot kulcs konfigurációjában nincs megadva érték, a forgó oszlopok futási időben dinamikusan jönnek létre. A forgó oszlopok száma megegyezik az egyedi pivot kulcsértékek számának és a pivot oszlopok számának szorzatával. Mivel ez lehet egy változó szám, a felhasználói felület nem jeleníti meg az oszlop metaadatait a **Vizsgálat** lapon, és nem lesz oszloppropagálás. Az oszlopok átalakításához használja az adatfolyam leképezésének [oszlopmintázati](concepts-data-flow-column-pattern.md) képességeit. 

Ha meghatározott kimutatáskulcsértékek vannak beállítva, a kimutatásoszlopok megjelennek a metaadatban.e az oszlopnevek elérhetők lesznek az Ellenőrzés és a Fogadó leképezésben.

### <a name="generate-metadata-from-drifted-columns"></a>Metaadatok létrehozása sodródott oszlopokból

A Pivot dinamikusan hozza létre az új oszlopneveket a sorértékek alapján. Ezeket az új oszlopokat hozzáadhatja a metaadatokhoz, amelyekre később az adatfolyamban hivatkozni lehet. Ehhez használja a [térkép sodródott](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) gyorsművelet az adatok előnézetében. 

![Oszlopok forgatása](media/data-flow/newpivot1.png "Sodródott kimutatásoszlopok leképezése")

### <a name="sinking-pivoted-columns"></a>Elforgatási elforgatott oszlopok

Bár a forgó oszlopok dinamikusak, továbbra is beírhatók a céladattárba. **Engedélyezze a sémaeltolódás engedélyezése** a fogadó beállításaiban. Ez lehetővé teszi olyan oszlopok írását, amelyek nem szerepelnek a metaadatokban. az oszlop metaadatai, de a sémaeltolódás opció lehetővé teszi az adatok leválasztását.

### <a name="rejoin-original-fields"></a>Eredeti mezők újraegyesítése

A kimutatás átalakítása csak a csoportot vetíti ki a kimutatásoszlopok szerint. Ha azt szeretné, hogy a kimeneti adatok más bemeneti oszlopokat is tartalmazzanak, használjon [önillesztési](data-flow-join.md#self-join) mintát.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Példa

A konfigurációs szakaszban látható képernyők a következő adatfolyam-parancsfájllal rendelkeznek:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>További lépések

Próbálja meg a [visszaforgatási transzformációt](data-flow-unpivot.md) az oszlopértékek sorértékekké alakításához. 
