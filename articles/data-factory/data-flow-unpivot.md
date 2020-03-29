---
title: Adatfolyam leképezése Kimutatás-transzformáció feloldása
description: Azure Data Factory leképezési adatfolyam kimutatási transzformációja
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: b207012335e68d389a07b54408e840dbb305a30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930140"
---
# <a name="azure-data-factory-unpivot-transformation"></a>Az Azure Data Factory kimutatásfeloldási átalakítása



Az ADF-hozzárendelési adatfolyamban a Kimutatás feloldása segítségével normalizáltabb verzióvá alakíthatja a nem normalizált adatkészletet azáltal, hogy egyetlen rekord több oszlopából származó értékeket több rekordra bontja ki, és ugyanazt az értéket egy oszlopban.

![Transzformformának feloldása](media/data-flow/unpivot1.png "A kimutatási beállítások 1.")

## <a name="ungroup-by"></a>Csoportbontás

![Transzformformának feloldása](media/data-flow/unpivot5.png "A kimutatás idiklékének feloldása 2")

Először állítsa be azoszlopokat, amelyek szerint a pivot összesítéséhez csoportosítani szeretné. Állítson be egy vagy több oszlopot a csoportosítás bontásához úgy, hogy az oszloplista mellett a + jel jelenjen meg.

## <a name="unpivot-key"></a>Kulcs kioldásának feloldása

![Transzformformának feloldása](media/data-flow/unpivot6.png "A kimutatási beállítások feloldása 3")

A kimutatáskulcs az az oszlop, amelyet az ADF sorról oszlopra fordít. Alapértelmezés szerint a mező adatkészletének minden egyedi értéke oszlopra kerül. Tetszés szerint azonban megadhatja az oszlopértékekre fordítani kívánt adatkészlet értékeit.

## <a name="unpivoted-columns"></a>Elnemti oszlopok kinemírt oszlopai

![Transzformformának feloldása](media/data-flow//unpivot7.png "4. beállítás feloldási beállításai")

Végül válassza ki azt az összesítést, amelyet a kimutatásértékekhez kíván használni, és azt, hogy miként szeretné megjeleníteni az oszlopokat az átalakítás új kimeneti vetületében.

(Nem kötelező) Beállíthat egy elnevezési mintát előtaggal, középső vel és utótaggal, amelyet a sorértékekből minden új oszlopnévhez hozzá kell adni.

Például az "Értékesítés" a "Régió" szerint elforgatással egyszerűen új oszlopértékeket adna az egyes értékesítési értékekből. Például: "25", "50", "1000", ... Ha azonban "Értékesítés" előtagértéket állít be, akkor az "Értékesítés" előtag az értékekhez lesz rögzítve.

<img src="media/data-flow/unpivot3.png" width="400">

Ha az Oszlopelrendezést "Normál" értékre állítja, az összes elforgatott oszlopot csoportosítja az összesített értékekkel. Az oszlopok elrendezésének "Oldalirányú" értékre állítása az oszlop és az érték között váltakozik.

![Transzformformának feloldása](media/data-flow//unpivot7.png "A kimutatás idiklékének feloldása 5")

A végleges kibontatlan adathalmaz az oszlopok összesítéseit mutatja, amelyek most már külön sorértékekre vannak átbontva.

## <a name="next-steps"></a>További lépések

A [Pivot átalakítássegítségével](data-flow-pivot.md) a sorokat oszlopokba forgatja.
