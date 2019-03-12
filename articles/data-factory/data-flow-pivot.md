---
title: Az Azure Data Factory-folyamat Pivot adatátalakítás leképezése
description: Az Azure Data Factory-folyamat Pivot adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 5548a62218aaac2e4da3853e8e5d43a584922bc0
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57569892"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Az Azure Data Factory-folyamat Pivot adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Használható Pivot ADF adatfolyam összegzésként ahol egy vagy több csoportosítási oszlopok rendelkezik-e az egyes oszlopokra alakítja át különböző sor értékeit. Alapvetően Sorértékek is forgáspont (alakítsa adatait metaadatai) új oszlopba.

![Forgáspont beállítások](media/data-flow/pivot1.png "forgáspont 1")

## <a name="group-by"></a>Csoportosítási szempont

![Forgáspont beállítások](media/data-flow/pivot2.png "forgáspont 2")

Először állítsa be a pivot összesítés szerint csoportosítani kívánt oszlopokat. Az 1-nél több oszlop itt is megadhatja a + bejelentkezés mellett az oszloplistán.

## <a name="pivot-key"></a>Pivot kulcs

![Forgáspont beállítások](media/data-flow/pivot3.png "forgáspont 3")

A Pivot kulcsa az oszlopot, amely az ADF sor lesz kimutatás az oszlopot. Alapértelmezés szerint minden egyes egyedi érték, a mező adatkészlet lesz forgáspont tartalmazó oszlop. Ugyanakkor igény szerint értékeket adhat meg a kimutatás az oszlopértékeket kívánt az adatkészletből.

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

### <a name="how-to-rejoin-original-fields"></a>Hogyan kell csatlakozni az eredeti mezők
> [!NOTE]
> A Pivot átalakítási csak fog projekt a összesítését, a csoportosítás és a pivot műveletet a használt oszlopok. Kíván foglalni a többi oszlopot az előző lépésben a folyamatban, ha az előző lépésből származó új ágat használja, és a folyamat összekapcsolása metaadatait az önillesztés minta használatával.

## <a name="next-steps"></a>További lépések

Próbálja ki a [elemi értékekre bontása átalakítási](data-flow-unpivot.md) kapcsolja be az oszlopértékeket sor értékekké. 
