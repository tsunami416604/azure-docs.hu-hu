---
title: "A Microsoft Azure StorSimple adatkezelő áttekintése |} Microsoft Docs"
description: "A StorSimple adatok Manager szolgáltatással (magán előnézetben) áttekintése"
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
ms.date: 11/22/2016
ms.author: vidarmsft
ms.openlocfilehash: aedb44610fe57055851538b9dbdb810e66e58d73
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="storsimple-data-manager-overview-private-preview"></a>StorSimple adatkezelő áttekintése (magán előnézetben)

## <a name="overview"></a>Áttekintés

A Microsoft Azure StorSimple hibrid felhőalapú tárolási megoldás, amely a strukturálatlan adatok gyakran társított fájlmegosztások bonyolultságára. StorSimple felhőbeli tárhelyét használja a helyszíni megoldás, és automatikusan rétegek adatok kiterjesztése a helyszíni és felhőalapú tárolására között. Az adatvédelem integrálva van a helyi és felhőalapú pillanatfelvételek, nem kell az sprawling tároló-infrastruktúra. Archiválás és katasztrófa-helyreállítás is zökkenőmentes egy külső helyszín működött a felhővel.

Vezetjük be ebben a dokumentumban átalakítása adatszolgáltatás lehetővé teszi, hogy akadálytalanul hozzáférjenek a StorSimple-adatok a felhőben. Ez a szolgáltatás biztosítja az API-k adatok kinyerése a StorSimple és bevezetését, formátumokban más Azure-szolgáltatásokkal, amelyek könnyen képes használni. Az ebben az előzetes verzióban támogatott formátumok a következők: Azure-blobokat és az Azure Media Services eszközök. Ez a transzformáció könnyen hozzá kell fűznie szolgáltatások, például az Azure Media Services, Azure HDInsight, Azure Machine Learning és Azure Search működéséhez a StorSimple 8000 series a helyi eszközön adatok teszi lehetővé.

Ez ábrázoló diagram elérésű magas szintű alább láthatók.

![Magas szintű diagramját](./media//storsimple-data-manager-overview/high-level-diagram.png)

Ez a dokumentum azt ismerteti, hogyan regisztrálhat a egy, a szolgáltatás private Preview verziójára. Azt is bemutatja, hogyan használható ez a szolgáltatás írási StorSimple adatokat használó alkalmazások és más Azure-szolgáltatásokkal a felhőben.

## <a name="sign-up-for-data-manager-preview"></a>Az adatkezelő előzetes regisztráció
Az adatkezelő szolgáltatás regisztrálása előtt tekintse át a következő előfeltételek teljesülését.

### <a name="prerequisites"></a>Előfeltételek

A gyakorlat feltételezi, hogy rendelkezik
* Egy aktív Azure-előfizetéssel.
* a regisztrált StorSimple 8000 series eszköz a hozzáférést
* a StorSimple 8000 sorozat eszközhöz társított összes a kulcsokat.

### <a name="sign-up"></a>Regisztráció

A StorSimple-kezelő van private Preview verziójára. A következő lépésekkel regisztrálhat egy, a szolgáltatás private Preview verziójára:

1.  Jelentkezzen be az Azure portálon, a StorSimple adatkezelő kiterjesztésű: [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager). Az Azure hitelesítő adatok használatát a bejelentkezéshez.

2.  Kattintson a  **+**  ikonra kattintva hozzon létre egy szolgáltatást. Kattintson a **tárolási** , majd **tekintse meg az összes** a megnyíló paneljén.

    ![Keresési StorSimple adatkezelő ikon](./media/storsimple-data-manager-overview/search-data-manager-icon.png)

3. A StorSimple adatkezelő ikon jelenik meg.

    ![Válassza ki a StorSimple Manager ikon](./media/storsimple-data-manager-overview/select-data-manager-icon.png)

4. Kattintson a StorSimple adatkezelő ikonra, majd **létrehozása**. Válassza ki az előfizetés engedélyezése a private Preview verziójára, és kattintson a kívánt **bejelentkezés!**

    ![Bejelentkezés](./media/storsimple-data-manager-overview/sign-me-up.png)

5. Ez egy kérést küld a bevezetni meg. Azt is megjelenik majd, amint lehetséges. Az előfizetés engedélyezése után létrehozhat egy StorSimple adatkezelő szolgáltatás.

6. Egyszerűen hozzáférhetnek a StorSimple Data Manager szolgáltatáshoz, kattintson a csillag ikonra kattintva rögzítheti a Kedvencekhez.

    ![Hozzáférés a StorSimple adatok kezelők](./media/storsimple-data-manager-overview/access-data-managers.png)


## <a name="next-steps"></a>Következő lépések

[Használja a StorSimple adatokat kezelő felhasználói felületén, az adatok átalakítására](storsimple-data-manager-ui.md).