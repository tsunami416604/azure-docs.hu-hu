---
title: "A Microsoft Azure StorSimple adatkezelő áttekintése |} Microsoft Docs"
description: "A StorSimple adatok Manager szolgáltatással áttekintése"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/26/2018
ms.author: vidarmsft
ms.openlocfilehash: 4d27bc3660035ace25436a4f756c41e88200649d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple adatkezelő megoldási áttekintés

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple felhőbeli tárhelyét használja a helyszíni megoldás, és automatikusan rétegek adatok kiterjesztése a helyszíni tárolási és a felhő között. Adatok a felhőben a maximális hatékonyság és csökkenthetők a költségek deduplikált és tömörített formátumban tárolja. Az adatok tárolási StorSimple formátumban, mivel nincs könnyen felhasználhatóvá más felhőalapú alkalmazásokhoz is használható.

A StorSimple-kezelő lehetővé teszi, hogy zökkenőmentesen eléréséhez, és a StorSimple formátum adatok használhatók a felhőben. Ennek érdekében StorSimple formátum átalakítása natív blobok és a fájlokat, amelyek más szolgáltatásokon, például az Azure Media Services, Azure HDInsights és Azure Machine Learning használható.

Ez a cikk áttekintést a StorSimple adatkezelő megoldás. Azt is bemutatja, hogyan használható ez a szolgáltatás írási StorSimple adatokat használó alkalmazások és más Azure-szolgáltatásokkal a felhőben.

## <a name="how-it-works"></a>Hogyan működik?

A StorSimple adatkezelő szolgáltatás azonosítja a StorSimple-adatokat a StorSimple 8000 series helyszíni eszközről a felhőbe. A StorSimple-adatok a felhőben van deduplikált, tömörített StorSimple formátumban. Az adatkezelő service API-k bontsa ki a StorSimple formátumú adatokat, és átalakíthatja a más formátumba, például az Azure-blobok és Azure fájlok biztosít. Ezt átalakítják Azure HDInsight és az Azure Media Services majd könnyen használt adatok. A data transformation így lehetővé teszi, hogy a StorSimple 8000 series helyszíni eszköz StorSimple az átalakított adatok működéséhez ezeket a szolgáltatásokat. Ez a folyamat a következő ábrán látható.

![Magas szintű diagramját](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Adatkezelő használati esetek

Az Azure Functions, az Azure Automation és az Azure Data Factory az adatkezelő használatával vannak StorSimple be ismét az adatok futó munkafolyamatok. Előfordulhat, hogy fel szeretné dolgoztatni a médiatartalmak, hogy akkor tárolja a StorSimple az Azure Media Services, a gépi tanulási algoritmus futtatása az adatok vagy a Hadoop fürtök elindítani a StorSimple tárolt adatok elemzésére. Az Azure StorSimple levő adatokkal együtt az elérhető szolgáltatások túlnyomó tömbje az adatok power zárolásának feloldásához.


## <a name="region-availability"></a>Régiónkénti elérhetőség

A StorSimple-kezelő a következő 7 régiókban érhető el:

 - Délkelet-Ázsia
 - USA keleti régiója
 - USA nyugati régiója
 - USA nyugati régiója, 2.
 - USA nyugati középső régiója
 - Észak-Európa
 - Nyugat-Európa

Azonban a StorSimple-kezelő a következő régiókban adatok átalakítására használható. 

![Az adatok területek](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ebben a készletben nem nagyobb, mivel az erőforrások telepítése a fenti régiók valamelyikében képes az átalakítási folyamat Tulajdonságkészítő a régiók alatt. Igen mindaddig, amíg az adatok közül legalább 19 régióban található, átalakíthatja az adatokat, használhatja a szolgáltatást.


## <a name="choosing-a-region"></a>A régió kiválasztásával

Azt javasoljuk, hogy:
 - A forrás-storage-fiók (a StorSimple eszköz társított egyik) és a cél tárfiók (amelyen kívánja az adatokat a natív formátum) azonos Azure-régióban kell.
 - A kezelő és a feladat definíciójának elindítani, amely tartalmazza a StorSimple tárfiók a régióban. Ha ez nem lehetséges, kapcsolja be az adatkezelő legközelebbi Azure-régióban, és majd a StorSimple tárfiók ugyanabban a régióban hozza létre a feladat definíciójához. 

    A StorSimple tárfiók nincs 26 régiókban, amely támogatja a feladat definíciójának létrehozását, azt javasoljuk, hogy nem futtatja a StorSimple adatkezelő hosszú késések és potenciálisan magas kilépő díjakat látható módon.

## <a name="security-considerations"></a>Biztonsági szempontok

A StorSimple-kezelő a StorSimple formátum natív formátumra alakítja át a szolgáltatásadat-titkosítási kulcs szükséges. A szolgáltatásadat-titkosítási kulcs jön létre, amikor az első eszköz regisztrálása a StorSimple szolgáltatásban. Ez a kulcs további információkért látogasson el [StorSimple biztonsági](storsimple-8000-security.md).

A bemenetként megadott szolgáltatásadat-titkosítási kulcs egy adatkezelő létrehozásakor létrehozott egy kulcstartót tárolódik. A tárolónak ugyanabban a régióban Azure, a StorSimple adatkezelő helyezkedik el. Ezt a kulcsot a rendszer törli, a kezelő szolgáltatás törlésekor.

Ezt a kulcsot a számítási erőforrások használják az átalakítás végrehajtásához. Ezek a számítási erőforrások és a feladat definíciójához Azure ugyanabban a régióban találhatók. Ebben a régióban lehet, vagy nem lehet ugyanaz, mint a régió, ahol kapcsolná az adatkezelő fel.

Az adatkezelő régió eltér a definíció régiója, ha fontos, hogy tudomásul veszi a milyen adatok metaadatok e régió helyezkedik el. A következő ábra szemlélteti a hatást, hogy a különböző régiókban adatkezelő és a feladat definíciójához.

![Különböző régiókban szolgáltatás és a feladat meghatározása](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="next-steps"></a>További lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md).
