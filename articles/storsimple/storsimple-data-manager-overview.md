---
title: Microsoft Azure StorSimple Data Manager – áttekintés | Microsoft dokumentumok
description: Áttekintést nyújt a StorSimple Data Manager szolgáltatásról
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
ms.author: alkohli
ms.openlocfilehash: 2ffe17bf7ef4f01c18d2c26f4a045add7302272d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876107"
---
# <a name="storsimple-data-manager-solution-overview"></a>StorSimple Data Manager megoldás – áttekintés

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple a helyszíni megoldás bővítményeként használja a felhőalapú tárhelyet, és automatikusan rétegezi az adatokat a helyszíni és a felhőbeli tárhelyen. Az adatok tárolása a felhőben egy deduped és tömörített formátumban a maximális hatékonyság és a költségek csökkentése érdekében. Mivel az adatok StorSimple formátumban vannak tárolva, más felhőalapú alkalmazások nem fogyaszthatják el, amelyeket érdemes lehet használni.

A StorSimple Adatkezelő lehetővé teszi, hogy zökkenőmentesen hozzáférjen és használja a StorSimple formátumú adatokat a felhőben. Ezt úgy éri el, hogy a StorSimple formátumot natív blobokká és fájlokká alakítja, amelyeket más szolgáltatásokkal, például az Azure Media Services, az Azure HDInsights és az Azure Machine Learning használatával is használhat.

Ez a cikk áttekintést nyújt a StorSimple Data Manager megoldás. Azt is bemutatja, hogyan használhatja ezt a szolgáltatást a StorSimple-adatokat és más Azure-szolgáltatásokat a felhőben használó alkalmazások írásához.

## <a name="how-it-works"></a>Hogy működik?

A StorSimple Data Manager szolgáltatás egy StorSimple 8000-es sorozatú helyszíni eszközről azonosítja a StorSimple-adatokat a felhőben. A StorSimple adatok a felhőben van deduped, tömörített StorSimple formátumban. A Data Manager szolgáltatás API-kat biztosít a StorSimple formátumadatok kibontásához és más formátumokba, például az Azure blobok és az Azure Files átalakításához. Ezt az átalakított adatokat ezután az Azure HDInsight és az Azure Media-szolgáltatások könnyen felhasználják. Az adatátalakítás így lehetővé teszi, hogy ezek a szolgáltatások a StorSimple 8000 sorozatú helyszíni eszköz ről származó átalakított StorSimple-adatokon működjenek. Ezt a folyamatot az alábbi ábra szemlélteti.

![Magas szintű diagram](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Az Adatkezelő használati esetei

Az Azure Functions, az Azure Automation és az Azure Data Factory adatkezelővel munkafolyamatokat futtathat az adatokon, ahogy azok a StorSimple-be kerülnek. Előfordulhat, hogy szeretné feldolgozni a StorSimple az Azure Media Services tárolja, vagy futtatni egy Machine Learning-algoritmusaz adatokon, vagy hozzon létre egy Hadoop-fürt a StorSimple tárolt adatok elemzéséhez. Az Azure-ban elérhető szolgáltatások széles skálájával és a StorSimple-adatokkal kombinálva kiaknázhatja az adatok erejét.


## <a name="region-availability"></a>Régiónkénti elérhetőség

A StorSimple Adatkezelő a következő 7 régióban érhető el:

 - Délkelet-Ázsia
 - USA keleti régiója
 - USA nyugati régiója
 - USA nyugati régiója, 2.
 - USA nyugati középső régiója
 - Észak-Európa
 - Nyugat-Európa

A StorSimple-adatkezelő azonban a következő régiókban lévő adatok átalakítására használható. 

![Az adatok számára rendelkezésre álló régiók](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Ez a készlet nagyobb, mert az erőforrás-telepítés a fenti régiók bármelyikében képes az átalakítási folyamat az alábbi régiókban. Így mindaddig, amíg az adatok a 19 régió bármelyikében találhatók, átalakíthatja az adatokat ezzel a szolgáltatással.


## <a name="choosing-a-region"></a>Régió kiválasztása

A következő megoldást javasoljuk:
 - A forrástár-fiók (a StorSimple-eszközhöz társított) és a céltárfiók (ahol az adatokat natív formátumban szeretné) ugyanabban az Azure-régióban legyenek.
 - Az Adatkezelőt és a feladatdefiníciót a StorSimple tárfiókot tartalmazó régióban hozza létre. Ha ez nem lehetséges, hozza létre az adatkezelőt a legközelebbi Azure-régióban, majd hozza létre a feladatdefiníciót ugyanabban a régióban, mint a StorSimple tárfiók. 

    Ha a StorSimple tárfiók nem a 26 régióban, amelyek támogatják a feladatdefiníció létrehozását, azt javasoljuk, hogy ne futtassa a StorSimple Data Manager, ahogy a hosszú késések és a potenciális kimenő díjak.
    
A Microsoft törekszik annak biztosítására, hogy az Azure-szolgáltatások mindig elérhetők legyenek minden régióban. Egy adott régióban azonban rövid ideig nem tervezett szolgáltatáskimaradások fordulhatnak elő. Ilyen esetekben létrehozhat egy Data Manager és a feladatdefiníciót egy olyan régióban, amelyet nem érint a kimaradás, és futtathatja az átalakítási feladatot. Előfordulhat, hogy egy ilyen forgatókönyv ben további késés, de ez lehet a helyreállítási stratégia a regionális leállás ritka esetén.

## <a name="security-considerations"></a>Biztonsági szempontok

A StorSimple Data Manager szüksége van a szolgáltatás adattitkosítási kulcs átalakulni StorSimple formátumnatív formátumban. A szolgáltatás adattitkosítási kulcs akkor jön létre, amikor az első eszköz regisztrál a StorSimple szolgáltatás. A kulccsal kapcsolatos további információkért látogasson el a [StorSimple biztonsága oldalra.](storsimple-8000-security.md)

A szolgáltatás adattitkosítási kulcs bemenetként biztosított tárolja a key vault, amely az Adatkezelő létrehozásakor jön létre. A tároló ugyanabban az Azure-régióban található, mint a StorSimple Data Manager. Ez a kulcs törlődik az Adatkezelő szolgáltatás törlésekor.

Ezt a kulcsot a számítási erőforrások használják az átalakítás végrehajtásához. Ezek a számítási erőforrások ugyanabban az Azure-régióban találhatók, mint a feladatdefiníció. Ez a régió lehet, vagy nem ugyanaz, mint az a régió, ahol az Adatkezelőt hozza létre.

Ha az Adatkezelő régiója eltér a feladatdefiníciós régiótól, fontos, hogy tisztában legyen azzal, hogy milyen adatok/metaadatok találhatók az egyes régiókban. Az alábbi ábra bemutatja a különböző régiók adatkezelőés feladatdefiníció hatását.

![Szolgáltatás- és feladatmeghatározás a különböző régiókban](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Személyes adatok kezelése

A StorSimple Adatkezelő nem gyűjt és nem jelenít meg személyes adatokat. További információkért lásd a Microsoft szabályzatát a [biztonsági és adatkezelési központban](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Ismert korlátozások

A szolgáltatás jelenleg a következő korlátozásokkal rendelkezik:
- A StorSimple adatkezelő jelenleg nem működik a bitlocker titkosított kötetekkel. Ha titkosított meghajtóval próbálja futtatni a szolgáltatást, akkor feladathibák lépnek fel.
- A fájlok egyes metaadatai (beleértve az AC-ket is) nem maradnak meg az átalakított adatokban.
- Ez a szolgáltatás csak NTFS-kötetekkel működik.
- A fájl elérési útjának hosszának 256 karakternél kisebbnek kell lennie, különben a feladat sikertelen lesz.

## <a name="next-steps"></a>További lépések

[Az adatok átalakításához használja a StorSimple Data Manager felhasználói felületét.](storsimple-data-manager-ui.md)
