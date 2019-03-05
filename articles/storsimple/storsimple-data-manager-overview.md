---
title: A Microsoft Azure StorSimple Data Manager áttekintése |} A Microsoft Docs
description: A StorSimple Data Manager szolgáltatás áttekintése
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: vidarmsft
ms.openlocfilehash: c5ffe3ec2ec3cb06297df6be4ba7021f692633bf
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312489"
---
# <a name="storsimple-data-manager-solution-overview"></a>A StorSimple Data Manager-megoldás áttekintése

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple felhőalapú tárolás kiterjesztése a helyszíni megoldást, és automatikusan rétegek adatokat, mint a helyszíni tárolók és a felhőben használja. Egy deduplikált és tömörített formátumban, a maximális hatékonyság és a költségek csökkentése a felhőben tárolt adatokat. Mivel az adatok StorSimple-formátumban tárolódik, akkor sem könnyen fogyaszthatóvá más felhőalapú alkalmazásokkal, amelyek igénybe szeretnék.

A StorSimple Data Manager lehetővé teszi, hogy zökkenőmentesen elérheti és használhatja az StorSimple formátumú adatokat a felhőben. Ezt nem átalakításával keletkező StorSimple a formátum natív blobok és-fájlokat, amelyek más szolgáltatásokkal, például az Azure Media Services, Azure Hdinsight és az Azure Machine Learning is használhatja.

Ez a cikk a StorSimple Data Manager-megoldás áttekintése. Azt is bemutatja, hogyan használható ez a szolgáltatás a StorSimple-adatokat használó alkalmazások írására, és más Azure-szolgáltatások a felhőben.

## <a name="how-it-works"></a>Hogyan működik?

A StorSimple Data Manager szolgáltatás a felhőben, helyszíni eszköz a StorSimple 8000 sorozat a StorSimple data azonosítja. A StorSimple a felhőbeli adatok deduplikált van, a StorSimple-formátumban tömörített. A Data Manager szolgáltatás API-k, bontsa ki a StorSimple-adatok formázása és átalakítása más formátumba való, mint például az Azure-blobok és az Azure Files biztosít. Ez az átalakított adatok ezután azonnal használja fel az Azure HDInsight és az Azure Media services. Az Adatátalakítási így lehetővé teszi ezeket a szolgáltatásokat szeretne végezni a StorSimple 8000 sorozat a helyszíni eszköz származó, átalakított StorSimple adatokat. Ez a folyamat a következő ábra mutatja be.

![Magas szintű diagramját](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Adatkezelő alkalmazási helyzetek

Az Azure Functions, az Azure Automation és az Azure Data Factory az adatkezelő segítségével munkafolyamatokat futtat az adatok StorSimple szolgáltatásba betölt, rendelkezik. Előfordulhat, hogy szeretne feldolgozni a médiatartalmak, hogy tárolja a StorSimple az Azure Media Services vagy egy gépi tanulási algoritmus futtatása az adatok, vagy viszi, megjelenik egy Hadoop-fürtöt, amely a StorSimple tárolja az adatok elemzéséhez. A szoftverleltáradatokkal együtt a StorSimple az Azure-on elérhető szolgáltatások hatalmas választékának, rejlő az adatokat.


## <a name="region-availability"></a>Régiónkénti elérhetőség

A StorSimple Data Manager a következő 7 régióban érhető el:

 - Délkelet-Ázsia
 - USA keleti régiója
 - USA nyugati régiója
 - USA nyugati régiója, 2.
 - USA nyugati középső régiója
 - Észak-Európa
 - Nyugat-Európa

A StorSimple Data Manager azonban az alábbi régiókban adatok átalakítására használható. 

![Az adatok régiók](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

A készlet, nagyobb, mert az erőforrások üzembe helyezésének, a fenti régiók egyikében képes használni az átalakítási folyamat az az alábbi régiókban. Így mindaddig, amíg az adatok a 19 régiók egyikében található, alakíthatja át az adatokat, ezt a szolgáltatást használja.


## <a name="choosing-a-region"></a>A régió kiválasztása

Azt javasoljuk, hogy:
 - A forrás tárfiókban (a StorSimple-eszköz társított egyet), és ugyanazon Azure-régióban kell céloldali tárfiók (Ha azt szeretné, az adatok natív formátumban).
 - A Data Manager és a feladat definíciója csatlakozva a régióban, amely a StorSimple tárfiókot tartalmaz. Ha ez nem lehetséges, léptetik be a Data Manager legközelebbi Azure-régióban, és majd a StorSimple tárfiók ugyanabban a régióban hozza létre a feladatdefiníciót. 

    Ha a StorSimple tárfiók nem támogatja a feladat definíciójának létrehozása 26 régióban, azt javasoljuk, hogy nem StorSimple Data Manager szerint hosszú késések és lehetséges kimenő forgalom költségeit.
    
A Microsoft nagy hangsúlyt fektet a győződjön meg arról, hogy Azure-szolgáltatások minden esetben érhető el minden régióban. Azonban nem tervezett szolgáltatáskimaradások fordulhat elő rövid időszakokra egy adott régióban. Ezekben az esetekben viszi, megjelenik egy Data Manager és a feladat definíciója egy régióban, amely a szolgáltatáskiesés megszüntetése után nem érinti, és az átalakítási feladat futtatása. Ilyen esetben némi késést találkozhat, de ez lehet a stratégia olyan regionális szolgáltatáskimaradás, a ritka esemény.

## <a name="security-considerations"></a>Biztonsági szempontok

A StorSimple Data Manager a szolgáltatásadat-titkosítási kulcs natív formátumban, a StorSimple a formátum átalakításához szükséges. A szolgáltatásadat-titkosítási kulcs jön létre, amikor az első eszköz regisztrálja magát a StorSimple szolgáltatással. Ezt a kulcsot a további információkért lépjen [StorSimple biztonsági](storsimple-8000-security.md).

A szolgáltatásadat-titkosítási kulcs egy bemenetként tárolja a kulcstartóhoz, amely egy Data Manager létrehozásakor jön létre. A tárolóban található azonos Azure-régióban, a StorSimple Data Manager. Ezt a kulcsot a Data Manager szolgáltatás törlésekor törlődik.

Ezt a kulcsot használják a számítási erőforrásokat az átalakítás végrehajtásához. Ezek a számítási erőforrások és a feladat definíciója Azure ugyanabban a régióban találhatók. Ebben a régióban is, vagy nem lehet ugyanaz, mint a régió, ahol mentése a Data Manager tenné.

Ha a Data Manager régió eltér a feladat definíciója régió, fontos, hogy megértette minden ezekben a régiókban található milyen adatokat vagy metaadatokat. A következő ábra szemlélteti a hatást, hogy a Data Manager és a feladat definíciója különböző régiókban.

![Különböző régiókban a szolgáltatás és a feladat definíciója](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A StorSimple Data Manager nem összegyűjtése és megjelenítése a személyes adatokat. További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Ismert korlátozások

A szolgáltatás jelenleg a következő korlátozások vonatkoznak:
- A StorSimple Data Manager jelenleg nem működik, amelyek a bitlocker-titkosítású kötetek. Látni fogja a sikertelen feladatok, ha megpróbálja futtatni a szolgáltatást egy titkosított meghajtó.
- Néhány metaadat-fájlok (beleértve az ACL-ek) az átalakított adatok nem lesznek megőrizve.
- Ez a szolgáltatás csak NTFS-kötetekről működik.
- Fájl elérési útja hosszúságok kell lennie 256 karakternél rövidebb más a feladat sikertelen lesz.

## <a name="next-steps"></a>További lépések

[Használja a StorSimple Data Manager felhasználói Felületét, hogy az adatok átalakítása](storsimple-data-manager-ui.md).
