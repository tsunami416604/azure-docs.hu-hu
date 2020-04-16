---
title: Adatfolyam-kimutatás transzformációjának leképezése
description: Az Azure Data Factory adatfolyamat-transzformációjának sorokról oszlopokra történő kimutatása
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a2276f5714db427586dbd56027e51c167b8c604f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413597"
---
# <a name="azure-data-factory-pivot-transformation"></a>Az Azure-adatgyár immára vonatkozó forint átalakítása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Pivot az ADF-adatfolyamban olyan összesítésként használja, ahol egy vagy több csoportosítási oszlop különböző sorértékeit egyedi oszlopokká alakítják át. Lényegében a sorértékeket új oszlopokká alakíthatja (az adatokat metaadatokká alakíthatja).

![Kimutatás beállításai](media/data-flow/pivot1.png "1. elforgatás")

## <a name="group-by"></a>Csoportosítási szempont

![Kimutatás beállításai](media/data-flow/pivot2.png "2. elforgatás")

Először állítsa be azoszlopokat, amelyek szerint a pivot összesítéséhez csoportosítani szeretné. Itt több mint 1 oszlopot állíthat be az oszloplista melletti + jellel.

## <a name="pivot-key"></a>Kimutatás billentyű

![Kimutatás beállításai](media/data-flow/pivot3.png "3. elforgatás")

A kimutatáskulcs az az oszlop, amelyet az ADF sorról oszlopra fordít. Alapértelmezés szerint a mező adatkészletének minden egyedi értéke oszlopra kerül. Tetszés szerint azonban megadhatja az oszlopértékekre fordítani kívánt adatkészlet értékeit. Ez az oszlop határozza meg a létrehozandó új oszlopokat.

## <a name="pivoted-columns"></a>Elforgatott oszlopok

![Kimutatás beállításai](media/data-flow/pivot4.png "4.")

Végül kiválaszthatja azt az összesítést, amelyet a kimutatásértékekhez kíván használni, és azt, hogy miként szeretné megjeleníteni az oszlopokat az átalakítás új kimeneti vetületében.

(Nem kötelező) Beállíthat egy elnevezési mintát előtaggal, középső vel és utótaggal, amelyet a sorértékekből minden új oszlopnévhez hozzá kell adni.

Például az "Értékesítés" "Régió" szerint történő elforgatás új oszlopértékeket eredményezne minden egyes eladási értékből, azaz "25", "50", "1000" stb. Ha azonban "Sales-" előtagértéket állít be, minden oszlopérték hozzáadja az "Értékesítés-" értéket az érték elejéhez.

![Kimutatás beállításai](media/data-flow/pivot5.png "5. forduló")

Ha az Oszlopelrendezést "Normál" értékre állítja, az összes elforgatott oszlopot csoportosítja az összesített értékekkel. Az oszlopok elrendezésének "Oldalirányú" értékre állítása az oszlop és az érték között váltakozik.

### <a name="aggregation"></a>Összesítés

A kimutatásértékekhez használni kívánt összesítés beállításához kattintson a Kimutatás oszlopok ablaktábla alján található mezőre. Beírhatja az ADF adatfolyam-szerkesztőjét, ahol összesítési kifejezést hozhat létre, és az új összesített értékekhez leíró aliasnevet adhat meg.

Az ADF adatfolyam-kifejezésnyelvével írja le a pivoted https://aka.ms/dataflowexpressionsoszlop átalakításokat a Kifejezésszerkesztőben: .

## <a name="pivot-metadata"></a>Kimutatás metaadatai

A Kimutatás átalakítása új oszlopneveket hoz létre, amelyek dinamikusak a bejövő adatok alapján. A kimutatásbillentyű minden új oszlopnév értékét létrehozza. Ha nem ad meg egyedi értékeket, és dinamikus oszlopneveket szeretne létrehozni a kimutatáskulcs minden egyes egyedi értékéhez, akkor a felhasználói felület nem jeleníti meg a metaadatokat az Ellenőrzés alkalmazásban, és nem lesz oszloppropagálás a Fogadó átalakításhoz. Ha értékeket állít be a kimutatáskulcshoz, akkor az ADF meghatározhatja az új oszlopneveket, és ezek az oszlopnevek elérhetők lesznek a Vizsgálat és a Fogadó leképezésben.

### <a name="generate-a-new-model-from-dynamic-columns"></a>Új modell létrehozása dinamikus oszlopokból

A Pivot dinamikusan hozza létre az új oszlopneveket a sorértékek alapján. Ezeket az új oszlopokat metaadatokká alakíthatja, amelyekre később az adatfolyamban hivatkozni lehet. Ehhez kattintson az Adatelőnézet fülre. A kimutatás átalakítása által létrehozott összes új oszlop egy "sodródott" ikonnal jelenik meg a táblázat fejlécében. Kattintson a "Térkép sodródott" gombra, hogy ezeket az új oszlopokat metaadatokká alakítsa, így azok az adatfolyam modelljének részét képezik.

![Oszlopok forgatása](media/data-flow/newpivot1.png "Sodródott kimutatásoszlopok leképezése")

### <a name="landing-new-columns-in-sink"></a>Új oszlopok leszállása a Mosogatóban

Még a Pivot dinamikus oszlopneveivel is besüllyesztheti az új oszlopneveket és értékeket a céltárolóba. Csak állítsa be a "Séma-drift" beállítást a Mosogató beállításaiban. Az új dinamikus nevek nem jelennek meg az oszlop metaadataiban, de a sémaeltolódási beállítás lehetővé teszi az adatok leválasztását.

### <a name="view-metadata-in-design-mode"></a>Metaadatok megtekintése tervező módban

Ha az új oszlopneveket metaadatként szeretné megtekinteni az Ellenőrzés alkalmazásban, és azt szeretné, hogy az oszlopok kifejezetten a Fogadó átalakításra propagáljanak, akkor állítson be explicit értékeket a Kimutatáskulcs lapon.

### <a name="how-to-rejoin-original-fields"></a>Az eredeti mezők újbóli egyesítése
A kimutatás átalakítása csak az összesítésben, csoportosításban és kimutatásműveletben használt oszlopokat vetíti ki. Ha az előző lépés többi oszlopát is bele szeretné foglalni a folyamatba, használjon az előző lépés új ága, és az önillesztési mintával kapcsolja össze a folyamatot az eredeti metaadatokkal.

## <a name="next-steps"></a>További lépések

Próbálja meg a [visszaforgatási transzformációt](data-flow-unpivot.md) az oszlopértékek sorértékekké alakításához. 
