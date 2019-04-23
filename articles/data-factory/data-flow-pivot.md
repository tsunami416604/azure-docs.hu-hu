---
title: Az Azure Data Factory-folyamat Pivot adatátalakítás leképezése
description: A kimutatás adatainak sorok oszlopok használata Azure Data Factory leképezési Flow Pivot adatátalakítás
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e16cac281b77f3ca93d9ef358ae806203bc8b663
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794363"
---
# <a name="azure-data-factory-pivot-transformation"></a>Az Azure data factory pivot átalakítása
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Használható Pivot ADF adatfolyam összegzésként ahol egy vagy több csoportosítási oszlopok rendelkezik-e az egyes oszlopokra alakítja át különböző sor értékeit. Alapvetően Sorértékek is forgáspont (alakítsa adatait metaadatai) új oszlopba.

![Forgáspont beállítások](media/data-flow/pivot1.png "forgáspont 1")

## <a name="group-by"></a>Csoportosítási szempont

![Forgáspont beállítások](media/data-flow/pivot2.png "forgáspont 2")

Először állítsa be a pivot összesítés szerint csoportosítani kívánt oszlopokat. Az 1-nél több oszlop itt is megadhatja a + bejelentkezés mellett az oszloplistán.

## <a name="pivot-key"></a>Pivot kulcs

![Forgáspont beállítások](media/data-flow/pivot3.png "forgáspont 3")

A Pivot kulcsa az oszlopot, amely az ADF sor lesz kimutatás az oszlopot. Alapértelmezés szerint minden egyes egyedi érték, a mező adatkészlet lesz forgáspont tartalmazó oszlop. Ugyanakkor igény szerint értékeket adhat meg a kimutatás az oszlopértékeket kívánt az adatkészletből. Ez az az oszlop, amelyek meghatározzák a létrehozandó új oszlopokat.

## <a name="pivoted-columns"></a>Elforgatott oszlopok

![Forgáspont beállítások](media/data-flow/pivot4.png "forgáspont 4")

Végül az összesítést, az elforgatott értékeket, és hogyan szeretne a kívánt oszlopok jeleníthető meg az új kimeneti leképezése az átalakítást a használni kívánt fog választani.

(Nem kötelező) Beállíthat egy elnevezési mintát egy előtagot, középső és utótagot hozzá kell minden egyes új oszlop neve, a sorok értékei.

Például "Értékesítés" frissítésének "Régió" eredményezne új oszlop értékeit az egyes értékesítési értékét, azaz "25", "50", "1000", stb. Azonban ha egy előtag-érték a "Sales-", minden oszlop értéke lenne hozzáadása "Sales-" az érték elejéhez.

![Forgáspont beállítások](media/data-flow/pivot5.png "forgáspont 5")

A "Normál" oszlop elrendezésének beállítása fog egy csoportba összes elforgatott oszlopot az összesített értékekre. "Oldalirányú" oszlopot a megállapodás beállítását fog alternatív oszlophoz és értékhez között.

### <a name="aggregation"></a>Összesítés

Ilyenkor az összesítést, inkább a pivot értékek beállításához kattintson a mezőre a Problémakörrel oszlopok panel alján. Az ADF adatfolyam Kifejezésszerkesztő, amelyen egy összesítő kifejezés összeállítása és leíró alias nevezze el az új összesített értékeket kell megadnia.

Az ADF Data Flow kifejezés nyelve segítségével ismertetik a Kifejezésszerkesztőben oszlop elforgatva átalakítások: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Pivot metaadatok

A Pivot átalakítás új oszlopnevek, amelyek dinamikus a beérkező adatok alapján állítja elő. A Pivot kulcsot hoz létre minden egyes új oszlop neve értékeit. Ha nem adja meg az egyes értékeket, és nem kíván létrehozni minden egyedi értékére vonatkozóan dinamikus oszlopnevek a Pivot kulcs, a felhasználói felületen nem jelennek meg a metaadatok a vizsgálat, és nem lesznek nincs oszlop-propagálás, a fogadó átalakításában. A Pivot kulcs értéket állítja be, majd az ADF megadhatja, hogy az új oszlopnevek, és oszlop nevére lesz a vizsgálat az Ön számára, és a hozzárendelés fogadó.

### <a name="landing-new-columns-in-sink"></a>Új oszlopok üzenetsorokra a fogadó

Még a Pivot dinamikus oszlopnevek meg is továbbra is fogadó az új oszlopnevek és értékeket a cél-tárolóhoz. Csak "A séma eltéréseket engedélyezése" értékre állítva a fogadó között. Az oszlop metaadatai nem látják az új dinamikus nevek, de azt a sémabeállítást eltéréseket lehetővé teszi, hogy megnyitja az adatokat.

### <a name="view-metadata-in-design-mode"></a>Tervező módban metaadatainak megtekintése

Ha meg szeretné tekinteni az új oszlopnevek vizsgálat a metaadatokat, és szeretne látni a oszlopok explicit módon a fogadó átalakítási való propagálása, majd állítsa be explicit érték a Pivot kulcs fülre.

### <a name="how-to-rejoin-original-fields"></a>Hogyan kell csatlakozni az eredeti mezők
A Pivot átalakítási csak fog projekt a összesítését, a csoportosítás és a pivot műveletet a használt oszlopok. Kíván foglalni a többi oszlopot az előző lépésben a folyamatban, ha az előző lépésből származó új ágat használja, és a folyamat összekapcsolása metaadatait az önillesztés minta használatával.

## <a name="next-steps"></a>További lépések

Próbálja ki a [elemi értékekre bontása átalakítási](data-flow-unpivot.md) kapcsolja be az oszlopértékeket sor értékekké. 
