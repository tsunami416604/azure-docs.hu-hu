---
title: "A Microsoft Azure StorSimple adatokat kezelő felhasználói felületén |} Microsoft Docs"
description: "StorSimple adatok Manager szolgáltatással felhasználói felület használata"
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: d704cf8e6840c6a7b0a637c404d421f9f1497c46
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>A StorSimple adatkezelő szolgáltatás Azure-portálon kezelése

Ez a cikk ismerteti, hogyan használhatja a StorSimple adatokat kezelő felhasználói felületén a StorSimple 8000 sorozat eszközeire levő adatok átalakítását. Az átalakított adatok majd más Azure-szolgáltatások például az Azure Media Services, az Azure HDInsight, az Azure Machine Learning és az Azure Search által igénybe vehető.


## <a name="use-storsimple-data-transformation"></a>StorSimple adatok átalakítással

A StorSimple-kezelő a belül mely adatok átalakítása példányként létrehozott erőforrás. A Data Transformation szolgáltatás lehetővé teszi az adatok a StorSimple formátumból blobok vagy az Azure-fájlok natív formátumba. A StorSimple natív formátum adatok átalakítására, meg kell adnia a StorSimple 8000 series eszköz és az átalakítási kívánt érdeklő adatok részleteit.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple adatkezelő szolgáltatás létrehozása

A következő lépésekkel StorSimple adatkezelő szolgáltatás létrehozása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a Microsoft-fiók hitelesítő adataival.

2. Kattintson a **+ hozzon létre egy erőforrást** , és keresse a StorSimple Data Manager.

    ![1 StorSimple adatkezelő szolgáltatás létrehozása](./media/storsimple-data-manager-ui/create-service-1.png)

3. Kattintson a StorSimple Manager-adatokat, majd **létrehozása**.
    
    ![2 StorSimple adatkezelő szolgáltatás létrehozása](./media/storsimple-data-manager-ui/create-service-3.png)

3. Az új szolgáltatás adja meg az alábbiakat:

    1. Adjon meg egy egyedi **szolgáltatásnév** a StorSimple Data Manager. Ez a szolgáltatás azonosítására szolgáló rövid név. A neve, amely betűket, számokat és kötőjeleket tartalmazhat, 3 – 24 karakter között lehet. A névnek betűvel vagy számmal kell kezdődnie és végződnie.

    2. Válasszon egy **előfizetés** a legördülő listából. Az előfizetés az Ön számlázási fiókjához lesz társítva. Ez a mező automatikusan ki van töltve (és nem választható). Ha csak egyetlen előfizetéssel rendelkezik.

    3. Válasszon ki egy meglévő erőforráscsoportot, vagy hozzon létre egy új csoportot. További információk: [Azure-erőforráscsoportok](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/).

    4. Adja meg a **hely** a szolgáltatás, amely a storage-fiókok és a StorSimple adatok Manager szolgáltatást. A StorSimple Device Manager, kezelő szolgáltatás, és a kapcsolódó tárfiók kell lenniük a támogatott régiók.
    
    5. Ahhoz, hogy ez a szolgáltatás egy hivatkozást az irányítópulton, válassza ki a **rögzítés az irányítópulton**.
    
    6. Kattintson a **Create** (Létrehozás) gombra.

    ![3 StorSimple adatkezelő szolgáltatás létrehozása](./media/storsimple-data-manager-ui/create-service-4.png)

A szolgáltatás létrehozása eltarthat néhány percig. Megjelenik egy értesítés, miután a szolgáltatás sikeresen létrehozta, és az új szolgáltatás jelenik meg.

### <a name="create-a-data-transformation-job-definition"></a>A data transformation feladatdefiníció létrehozása

A StorSimple adatkezelő szolgáltatásokon belüli létrehozásához szükséges egy adatok átalakítási feladat definíciójához. Olyan feladatdefinícióban határozza meg, hogy érdekli helyezi át a natív formátumban a tárfiók StorSimple adatok részleteit. Miután létrehozott egy olyan feladatdefinícióban, majd a feladat ismét futtathatja eltérő futásidejű beállításokkal.

Hajtsa végre a következő lépésekkel hozza létre a feladat definíciójához.

1. Nyissa meg a létrehozott szolgáltatásra. Ugrás a **felügyeleti > Feladatdefiníciók**.

2. Kattintson a **+ definition feladat**.

    ![Kattintson a kívánt feladat definíciója](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Adjon meg egy nevet a feladat definíciójához. A név hosszúsága 3 és 63 karakter közötti lehet. A név kis- és nagybetűket betűket, számokat és kötőjeleket tartalmazhat.

4. Adjon meg egy helyet, ahol a feladat fut-e. Ezen a helyen lehet különböző, mint a helyen, a szolgáltatás telepítési helyét.

5. Kattintson a **forrás** adhatja meg a adatok tárházban.

    ![Forrás adatok tárház konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Mivel ez egy új adatokat kezelő szolgáltatás, nem adattárolók vannak konfigurálva. A **konfigurálása adatforrás**, adja meg a StorSimple 8000 series eszköz részleteit és érdeklő adatok.

   A StorSimple Device Manager hozzáadni a rendszert, kattintson a **új hozzáadása** az adatok tárház legördülő majd **adattárház hozzáadása**.

    ![Új adatok tárház hozzáadása](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
    1. Válasszon **StorSimple 8000 series Manager** tárház adattípusú értékként.
    
    2. Adjon meg egy rövid nevet a adatok tárházzal.
    
    3. A legördülő listából válassza ki a StorSimple Device Manager szolgáltatáshoz tartozó előfizetés.
    
    4. Adja meg a nevét, a StorSimple Device Manager számára a **erőforrás**.

    5. Adja meg a **szolgáltatásadat-titkosítási** kulcs a StorSimple eszköz Manager szolgáltatáshoz. 

    ![Forrás adatok tárház 1 konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-4.png)

    Kattintson a **OK** végzett. Ez menti a rendszert. Ezek a paraméterek ismételt beírása nélkül használja fel a StorSimple Device Manager lévő más definíciók. Kattintás után néhány másodpercet vesz igénybe **OK** az újonnan létrehozott forrás adatok tárház megjelennek a legördülő listában.

7. A legördülő listából a **adattárház**, válassza ki a létrehozott adatok tárházba. 

    1. Adja meg a StorSimple 8000 series eszköz egyik fontos adatokat tartalmazó nevét.

    2. Adja meg a kötet levő egyik fontos az adatok a StorSimple-eszköz nevét.

    3. Az a **szűrő** alszakasz, adja meg a gyökérkönyvtár, a fontos adatokat tartalmazó _\MyRootDirectory\Data_ formátumban. Meghajtó-betűjelek, például _\C:\Data_ nem támogatottak. Bármely fájlszűrők is hozzáadhat.

    4. A data transformation szolgáltatás működik-e az adatok az Azure-bA leküldött, a pillanatképek keresztül. Ez a feladat futtatásakor is választja, a biztonsági mentés minden futtatásakor ez a feladat (csak ezen legfrissebb adatokat), vagy használjon létező utolsó biztonsági mentés a felhőben (ha az egyes archivált adatokat).

    5. Kattintson az **OK** gombra.

    ![Forrás adatok tárház 2 konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Ezután a cél adattárház kell megadni. Válassza ki a fájlok kerüljenek a fiókhoz tartozó BLOB storage-fiókok. A legördülő listában jelölje ki **új hozzáadása** , majd **beállításainak**.

9. Válassza ki a hozzáadni kívánt cél tárház és a tárház társított a többi paraméter típusát.

    Ha a tárolási fiók céljának választja, megadhatja egy rövid nevet, az előfizetés (válasszon ugyanaz, mint amellyel a szolgáltatás, vagy más), és a storage-fiók.
        ![Cél adatok tárház 1 konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    A tároló várólista jön létre, a feladat futtatásakor. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével.
    
10. Miután hozzáadta az adattárház, várjon néhány percig.
    
    1. Válassza ki a létrehozott tárházba a legördülő listából a célként a **célfiók neve**.

    2. Válassza ki a tárolási BLOB vagy fájlokat. Adja meg az átalakított adatokat tartalmazó tároló nevét. Kattintson az **OK** gombra.

        ![Cél adatok tárház tárfiók konfigurálása](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Ellenőrizheti a feladat időtartama a feladat futtatása előtt becslést jelentenek lehetőséget is. Kattintson a **OK** létrehozni a feladat definíciójához. A feladat definíciójához befejeződött. A feladat definíciójához a felhasználói felületen keresztül többször használható különböző futásidejű beállításait.

    ![Teljes feladatdefiníció](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Az újonnan létrehozott feladatdefiníció kerül a szolgáltatás definíciók listája.

### <a name="run-the-job-definition"></a>A feladat definíciójához futtatása

Adatok áthelyezése StorSimple feladatdefinícióban megadott tárfiók van szüksége, akkor kell futtatnia. Alkalmazása futásidőben egyes paraméterek másképp adható meg. A lépések a következők:

1. Válassza ki a StorSimple adatkezelő szolgáltatást, és navigáljon **felügyeleti > Feladatdefiníciók**. Jelölje ki, majd kattintson a futtatni kívánt feladat definíciójához.
     
     ![Futtatás 1 feladat indítása](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Kattintson a **futtatása most**.
     
     ![Indítsa el a feladat futtatásához a 2. régiója](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Kattintson a **futtatási beállítások** módosítani a beállításait, előfordulhat, hogy módosítani szeretné a feladat futtatásához. Kattintson a **OK** majd **futtatása** elindítani a feladatot.

    ![Indítsa el a feladat futtatása 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Ez a feladat figyeléséhez, nyissa meg a **feladatok** a StorSimple adatkezelő. A figyelés mellett a **feladatok** panelen, akkor is figyelheti a a tároló várólista, ahol egy üzenet jelenik meg minden alkalommal, amikor egy fájl átkerül a StorSimple a tárfiók.

    ![Indítsa el a feladat futtatása 4](./media/storsimple-data-manager-ui/start-job-run4.png)


## <a name="next-steps"></a>További lépések

[.NET SDK használatával indítsa el a StorSimple adatkezelő feladatok](storsimple-data-manager-dotnet-jobs.md).