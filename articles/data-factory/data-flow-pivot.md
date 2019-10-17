---
title: Azure Data Factory leképezési adatforgalom kimutatásának átalakítása
description: Sorokból oszlopokból származó adatok kimutatása Azure Data Factory leképezési adatforgalom pivot-transzformációjának használatával
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: e59f0623b898fedada63e51fabbaf88d8b17f59d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387796"
---
# <a name="azure-data-factory-pivot-transformation"></a>Az Azure-beli adatgyári kimutatás átalakítása


Az ADF-adatfolyamban az adatforgalom olyan összesítésként használható, amelyben egy vagy több csoportosítási oszlop különálló oszlopokra alakítja át a különböző sorok értékeit. Lényegében a sorok értékeit az új oszlopokra is felhasználhatja (az adatok metaadatokba való bekapcsolásával).

![Kimutatás beállításai](media/data-flow/pivot1.png "1\. kimutatás")

## <a name="group-by"></a>Csoportosítási alapja

![Kimutatás beállításai](media/data-flow/pivot2.png "2\. pivot")

Először állítsa be azokat az oszlopokat, amelyeket a kimutatás összesítéséhez szeretne csoportosítani. Itt több, mint 1 oszlopot is beállíthat az oszlopok listája melletti + jelre.

## <a name="pivot-key"></a>Pivot kulcsa

![Kimutatás beállításai](media/data-flow/pivot3.png "3\. kimutatás")

A pivot kulcs az az oszlop, amelyet az ADF a sor és az oszlop között fog kimutatni. Alapértelmezés szerint a mezőhöz tartozó adatkészlet minden egyedi értéke egy oszlopra mutat. Azonban megadhatja az adatkészletből azokat az értékeket, amelyeket meg szeretne adni az oszlop értékeinek. Ez az az oszlop, amely a létrehozandó új oszlopokat fogja meghatározni.

## <a name="pivoted-columns"></a>Elforgatott oszlopok

![Kimutatás beállításai](media/data-flow/pivot4.png "4\. kimutatás")

Végül válassza ki a tagolt értékekhez használni kívánt összesítést, és azt, hogy az oszlopok hogyan jelenjenek meg az átalakítás új kimeneti vetületében.

Választható Megadhat egy elnevezési mintát egy előtaggal, egy középtel és egy utótaggal, amelyet a rendszer minden új oszlopnév számára hozzáadhat a sorok értékeiből.

Például a "Sales" a "Region" kifejezéssel való elforgatása új oszlop értékeket eredményez az egyes értékesítési értékektől, azaz a "25", "50", "1000" stb. Ha azonban "Sales-" előtagot állít be, akkor minden oszlop értéke "Sales" (értékesítés) értéket ad az érték elejéhez.

![Kimutatás beállításai](media/data-flow/pivot5.png "5\. pivot")

Ha úgy állítja be az oszlop elrendezését a "NORMAL" értékre, hogy az összes elforgatott oszlopot összesítse. Ha az oszlopok elrendezése az "oldalirányú" értékre van állítva, akkor az oszlop és az érték között váltakozik.

### <a name="aggregation"></a>Összesítés

A kimutatás értékeihez használni kívánt összesítés beállításához kattintson a kitűzött oszlopok ablaktábla alján található mezőre. Itt adhatja meg az ADF adatáramlás-kifejezés-szerkesztőt, ahol összeállíthat egy összesítési kifejezést, és adjon meg egy leíró alias nevet az új összesített értékekhez.

Az ADF-adatfolyam kifejezésének nyelvével írja le a tagolt oszlopok átalakításait a Kifejezésszerkesztő: https://aka.ms/dataflowexpressions.

## <a name="pivot-metadata"></a>Kimutatás metaadatainak

A pivot-átalakítás a bejövő adatain alapuló új oszlopnevek létrehozására szolgál. A pivot kulcs az egyes új oszlopnevek értékeit állítja elő. Ha nem ad meg egyedi értékeket, és dinamikus oszlopnevet kíván létrehozni minden egyes egyedi értékhez a pivot kulcsban, akkor a felhasználói felület nem jeleníti meg a vizsgálat során a metaadatokat, és nem lesz a fogadó átalakítására szolgáló oszlop. Ha a pivot kulcs értékeit állítja be, az ADF meghatározhatja az új oszlopnevek, és ezek az oszlopnevek elérhetők lesznek a vizsgálat és a fogadó leképezésében.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Új modell létrehozása dinamikus oszlopokból

A pivot a sorok értékei alapján dinamikusan generálja az új oszlopnevek nevét. Ezeket az új oszlopokat olyan metaadatokba állíthatja be, amelyek később is hivatkozhatnak az adatfolyamban. Ehhez kattintson az adatelőnézet fülre. A pivot-átalakítás által létrehozott összes új oszlop "Drift" ikonnal jelenik meg a tábla fejlécében. Kattintson a "Térkép felsodródva" gombra az új oszlopok metaadatokba való bekapcsolásához, így az adatfolyamat modelljének részét képezi.

![Kimutatás oszlopai](media/data-flow/newpivot1.png "Elsodródott pivot oszlopok leképezése")

### <a name="landing-new-columns-in-sink"></a>Új oszlopok leszállása a fogadóban

Még a kimutatásban a dinamikus oszlopnevek is megmaradnak az új oszlopnevek és értékek a célhelyen való tárolásához. Csak állítsa be a "séma-eltolódás engedélyezése" beállítást a fogadó beállításaiban. Az új dinamikus nevek nem jelennek meg az oszlop metaadataiban, de a séma drift beállítás lehetővé teszi az adatok kirakodását.

### <a name="view-metadata-in-design-mode"></a>Metaadatok megtekintése tervezési módban

Ha szeretné megtekinteni az új oszlopnevek metaadatokként való megtekintését, és látni szeretné, hogy az oszlopok explicit módon legyenek propagálva a fogadó átalakítására, akkor a pivot kulcs lapon adja meg a explicit értékeket.

### <a name="how-to-rejoin-original-fields"></a>Az eredeti mezők újracsatlakoztatása
A pivot-átalakítás csak az összesítés, csoportosítás és kimutatás műveletben használt oszlopokat fogja feltervezni. Ha a folyamat előző lépésében lévő többi oszlopot is fel szeretné venni, használjon egy új ágat az előző lépésből, és használja az önillesztési mintát a folyamat az eredeti metaadatokkal való összekapcsolásához.

## <a name="next-steps"></a>Következő lépések

Az oszlopok értékeinek sorokba való bekapcsolásához próbálja meg az [unpivot transzformációt](data-flow-unpivot.md) . 
