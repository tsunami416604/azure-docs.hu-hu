---
title: "A Microsoft Azure StorSimple adatokat kezelő felhasználói felületén |} Microsoft Docs"
description: "StorSimple adatok Manager szolgáltatással (magán előnézetben) felhasználói felület használata"
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
ms.openlocfilehash: 53a8599df2c647613122cd791b680e2e658586b0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-using-the-storsimple-data-manager-service-ui-private-preview"></a>A StorSimple adatokat kezelő szolgáltatással felhasználói felület (magán előnézetben) kezelése

Ez a cikk ismerteti, hogyan használhatja a StorSimple adatokat kezelő felhasználói felületén a StorSimple 8000 sorozat eszközeire levő adatok a data transformation végrehajtásához. Az átalakított adatok majd más Azure-szolgáltatások például az Azure Media Services, az Azure HDInsight, az Azure Machine Learning és az Azure Search által igénybe vehető. 


## <a name="use-storsimple-data-transformation"></a>StorSimple adatok átalakítással

A StorSimple-kezelő a Data Transformation példányosítható erőforrás. A Data Transformation szolgáltatás lehetővé teszi az eszközét a helyszíni adatok áthelyezése az Azure-tárfiókba blobok. Emiatt a munkafolyamat meg kell adnia a StorSimple eszköz és a storage-fiók áthelyezése kívánt érdeklő adatok részleteit.

### <a name="create-a-storsimple-data-manager-service"></a>StorSimple adatkezelő szolgáltatás létrehozása

A következő lépésekkel StorSimple adatkezelő szolgáltatás létrehozása.

1. Hozhatja létre a StorSimple adatok Manager szolgáltatást, [https://aka.ms/HybridDataManager](https://aka.ms/HybridDataManager)

2. Kattintson a  **+**  ikonra, és keresse a StorSimple Data Manager. Kattintson a StorSimple adatok Manager szolgáltatást, majd **létrehozása**.

3. Ha az előfizetés engedélyezve van ez a szolgáltatás létrehozása, a következő panelen láthatja.

    ![StorSimple adatok kezelők erőforrás létrehozása](./media/storsimple-data-manager-ui/create-new-data-manager-service.png)

4. Adja meg az adatokat, és kattintson a **létrehozása**. A megadott helyen az legyen, amely a storage-fiókok és a StorSimple Manager szolgáltatás. Jelenleg csak az USA nyugati régiója és Nyugat-Európában régiók támogatottak. Emiatt a StorSimple Manager szolgáltatás, kezelő szolgáltatás és a kapcsolódó tárfiók kell lenniük az előző támogatott régióban. A szolgáltatás létrehozásához körülbelül egy perce szükséges.

### <a name="create-a-data-transformation-job-definition"></a>A data transformation feladatdefiníció létrehozása

A StorSimple adatkezelő szolgáltatásokon belüli létrehozásához szükséges egy adatok átalakítási feladat definíciójához. Olyan feladatdefinícióban határozza meg, hogy érdekli helyezi át a natív formátumban a tárfiók adatok részleteit. 

A következő lépésekkel hozzon létre egy új adatok átalakítási feladat definíciójához.

1.  Nyissa meg a létrehozott szolgáltatásra. Kattintson a **+ Definition feladat**.

    ![Kattintson a kívánt feladat definíciója](./media/storsimple-data-manager-ui/click-add-job-definition.png)

2. Az új feladat definition panel is megnyílik. Nevezze el a feladat definíciójához, és kattintson a **forrás**. Az a **konfigurálása adatforrás** panelen adja meg a StorSimple eszköz részleteit és érdeklő adatok.

    ![Feladat-definíció létrehozása](./media/storsimple-data-manager-ui//create-new-job-deifnition.png)

3. Mivel ez egy új adatokat kezelő szolgáltatás, nem adattárolók vannak konfigurálva. A StorSimple Manager hozzáadni a rendszert, kattintson a **új hozzáadása** az adatok tárház legördülő majd **adattárház hozzáadása**.

4. Válasszon **StorSimple 8000 series Manager** tárházaként írja be, és adja meg a tulajdonságait a **StorSimple Manager**. Az a **erőforrás-azonosító** mezőben meg kell adnia a száma, mielőtt a **:** a regisztrációs kulcs a StorSimple Manager.

    ![Adatforrás létrehozása](./media/storsimple-data-manager-ui/create-new-data-source.png)

5.  Kattintson a **OK** végzett. Ez az adattárház menti, és a StorSimple Manager ezek a paraméterek ismételt beírása nélkül is újrahasznosíthatók lévő más definíciók. Kattintás után néhány másodpercet vesz igénybe **OK** a StorSimple Manager megjeleníti őket a legördülő listában.

6.  Az a **konfigurálása adatforrás** panelen adja meg az eszköz nevét és a kötet neve, amely a fontos adatokat tartalmaz.

7.  Az a **szűrő** alszakasz, adja meg a gyökérkönyvtár egyik fontos adatokat tartalmazó (ebben a mezőben kell kezdődnie, egy `\`). Bármely fájlszűrők is hozzáadhat.

8.  A data transformation szolgáltatás működik-e az adatok az Azure-bA leküldött, a pillanatképek keresztül. Ez a feladat futtatásakor is választja, a biztonsági mentés minden futtatásakor ez a feladat (csak ezen legfrissebb adatokat), vagy ha a legutóbbi meglévő biztonsági mentés a felhőben (ha az egyes archivált adatokat).

    ![Új adatforrás részletei](./media/storsimple-data-manager-ui/new-data-source-details.png)

9. Ezután a tárolóbeállítások kell konfigurálni. Támogatott célok – Azure Storage-fiókok és az Azure Media Services-fiókok 2 típusa van. Válassza ki a fájlok kerüljenek a fiókhoz tartozó BLOB storage-fiókok. Válassza ki a media services-fiók fájlok kerüljenek a fiókhoz tartozó eszközök számára. Ebben az esetben kell vegyen fel egy tárházat. A legördülő listában jelölje ki **új hozzáadása** , majd **beállításainak**.

    ![Adatokat fogadó létrehozása](./media/storsimple-data-manager-ui/create-new-data-sink.png)

10. Itt is válassza ki a hozzáadni kívánt tárház és a tárház társított a többi paraméter típusát. Mindkét esetben a tároló várólista létrejön, a feladat futtatásakor. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével. Ha **Media Services** tárház típusként, majd is megadhat tárfiók hitelesítő adatainak ahol a várólista létrejön.

    ![Új adatokat fogadó részletei](./media/storsimple-data-manager-ui/new-data-sink-details.png)

11. Miután hozzáadta az adattárház (amely néhány másodpercet vesz igénybe), megtalálja a tárházban meg a legördülő listában a a **célfiók neve**.  Válassza ki a cél, amelyekre szüksége van.

12. Kattintson a **OK** létrehozni a feladat definíciójához. A feladat definíciójához most már be van állítva. A feladat definíciójához a felhasználói felületen keresztül többször is használhatja.

    ![Új projekt hozzáadása](./media/storsimple-data-manager-ui/add-new-job-definition.png)

### <a name="run-the-job-definition"></a>A feladat definíciójához futtatása

Adatok áthelyezése StorSimple feladatdefinícióban megadott tárfiók van szüksége, szüksége lesz indítsa el. Rugalmas szerepel a paraméterek módosítása, minden alkalommal, amikor a feladat indításakor. A lépések a következők:

1. Válassza ki a StorSimple adatkezelő szolgáltatást, és navigáljon **figyelés**. Kattintson a **futtatása most**.

    ![Eseményindító feladatdefiníció](./media/storsimple-data-manager-ui/run-now.png)

2. Válassza ki a futtatni kívánt feladat definíciójához. Kattintson a **futtatási beállítások** módosítani a beállításait, előfordulhat, hogy módosítani szeretné a feladat futtatásához.

    ![Feladatbeállítások futtatása](./media/storsimple-data-manager-ui/run-settings.png)

3. Kattintson a **OK** majd **futtatása** elindítani a feladatot. Ez a feladat figyeléséhez, keresse fel a **feladatok** a StorSimple adatkezelő lap.

    ![Feladatok listája és állapota](./media/storsimple-data-manager-ui/jobs-list-and-status.png)

4. A figyelés mellett a **feladatok** panelen, akkor is figyelheti a a tároló várólista, ahol egy üzenet jelenik meg minden alkalommal, amikor egy fájl átkerül a StorSimple a tárfiók.


## <a name="next-steps"></a>Következő lépések

[.NET SDK használatával indítsa el a StorSimple adatkezelő feladatok](storsimple-data-manager-dotnet-jobs.md).