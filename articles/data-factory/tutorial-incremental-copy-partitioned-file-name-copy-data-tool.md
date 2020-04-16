---
title: Új fájlok növekményes másolása az idő particionált fájlneve alapján
description: Hozzon létre egy Azure-adat-előállítót, majd az Adatok másolása eszközzel csak az idő particionált fájlneve alapján tölthetbe be új fájlokat.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 6cc089a1efc3f5960a8bca8a36063bb1019bbcc6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409398"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Új fájlok növekményes másolása az idő particionált fájlneve alapján az Adatok másolása eszközzel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel hozzon létre egy folyamatot, amely növekményesen másolja az új fájlokat az Azure Blob storage-ból az Azure Blob storage-ból az Azure Blob storage-ba történő particionált fájlnév alapján.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure storage-fiók:** Használja blob storage _forrásként_ és _fogadó_ adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Hozzon létre két tárolót a Blob storage-ban

Készítse elő a Blob storage az oktatóanyag ezekkel a lépésekkel.

1. Hozzon létre egy **forrás**nevű tárolót.  Hozzon létre egy mappa elérési útját **2020/03/17/03** néven a tárolóban. Hozzon létre egy üres szövegfájlt, és nevezze el **fájlnak1.txt fájlként.** Töltse fel a file1.txt fájlt a tárfiókban lévő mappaelérési út **forrására/2020/03/17/03.**  Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

    ![fájlok feltöltése](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Állítsa be a mappa nevét az UTC-idővel.  Ha például az aktuális UTC-idő **source/2020/03/17/03/** 2020. **source/{Year}/{Month}/{Day}/{Hour}/**

2. **Hozzon**létre egy cél nevű tárolót. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az > **Erőforrás-adatok létrehozása + Analytics** > **Data Factory** **lehetőséget:**

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:

   ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához.
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

5. A **Verzió** résznél válassza a **V2** értéket.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
7. Kattintson a **Létrehozás** gombra.
8. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
9. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére.

    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. A **Let's get started (Az adatok másolása)** lapon válassza az **Adatok másolása** eszközt.

   ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)

2. A **Tulajdonságok** lapon tegye a következő lépéseket:

    a. A **Feladat neve mezőbe**írja be a **DeltaCopyFromBlobPipeline parancsot.**

    b. A **Tevékenység lépésszám vagy a Tevékenység ütemezése csoportban**válassza **a Futtatás rendszeresen ütemezés szerint**lehetőséget.

    c. Az **Eseményindító típusa csoportban**válassza **a Bukdácsoló ablak lehetőséget.**

    d. Az **Ismétlődés**csoportban adja meg **az 1 óra(ok)** értéket.

    e. Kattintson a **Tovább** gombra.

    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven.

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre
    
    b. Válassza ki az Azure Blob Storage a gyűjteményből, majd válassza a Folytatás lehetőséget.
    
    c. Az **új csatolt szolgáltatás (Azure Blob Storage)** lapon adja meg a csatolt szolgáltatás nevét. Válassza ki az Azure-előfizetést, és válassza ki a tárfiókot a **Storage-fiók** névlistájából. Tesztelje a kapcsolatot, és válassza a **Létrehozás lehetőséget.**

    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Jelölje ki az újonnan létrehozott csatolt szolgáltatást a **Forrás adattár** lapon, majd kattintson a **Tovább**gombra.

4. **A bemeneti fájl vagy mappa kiválasztása** oldalon kövesse az alábbi lépéseket:

    a. Tallózással **source** és válassza ki a forrástárolót, majd válassza **a Választás gombot.**

    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. A **Fájlbetöltési viselkedés**csoportban válassza **a Növekményes betöltés: időpartidott mappa-/fájlnevek**lehetőséget.

    c. Írja be a dinamikus mappa elérési útját **forrásként/{year}/{month}/{day}/{hour}/** és módosítsa a formátumot a következő képernyőképen látható módon. Jelölje be **a Bináris másolás jelölőnégyzetet,** és kattintson a **Tovább**gombra.

    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. A **Cél adattár** lapon válassza ki az **AzureBlobStorage**-, amely ugyanaz a tárfiók, mint az adatforrás-tároló, majd kattintson a **Tovább**gombra.

    ![Céladattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. A **Kimeneti fájl vagy mappa kiválasztása** lapon tegye a következő lépéseket:

    a. Tallózással **destination** és kijelöléssel, majd kattintson a **Választás gombra.**

    b. Írja be a dinamikus mappa elérési útját **célként/{year}/{month}/{day}/{hour}/** és módosítsa a formátumot a következők szerint:

    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Kattintson a **Tovább** gombra.

    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. A **Beállítások** lapon kattintson a **Tovább** gombra.

8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.
    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva.  Meg kell várnia a folyamat futtatását, amikor automatikusan aktiválódik (körülbelül egy óra elteltével). Futtatásakor kattintson a folyamat névkapcsolatára, a **DeltaCopyFromBlobPipeline-ra** a tevékenységfuttatásrészleteinek megtekintéséhez vagy a folyamat újbóli futtatásához. A lista frissítéséhez kattintson a **Frissítés** gombra.

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Állítsa be a **forrás-** és **céloszlopok** oszlopszélességét (ha szükséges), hogy további részleteket jelenítsen meg, láthatja, hogy a forrásfájlt (file1.txt) a *forrásból/2020/03/17/03/* a *cél/2020/03/17/03/* fájlnévvel másolta. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Ugyanezt az Azure Storage Explorer (ahttps://storageexplorer.com/) fájlok benézésével) segítségével is ellenőrizheti.

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Hozzon létre egy másik üres szövegfájlt, amelynek új neve **fájl2.txt**. Töltse fel a file2.txt fájlt a tárfiók **2020/03/17/04** mappaelérési útjára. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Előfordulhat, hogy tudnia kell, hogy új mappaelérési utat kell létrehozni. Állítsa be a mappa nevét az UTC-idővel.  Ha például az aktuális UTC-idő 2020. **source/2020/03/17/04/** **{Year}/{Month}/{Day}/{Hour}/**

13. Ha vissza szeretne lépni a **Folyamatfuttatások** nézetbe, válassza az **Összes folyamat futtatása**lehetőséget, és várja meg, amíg ugyanaz a folyamat automatikusan újra aktiválódik egy óra elteltével.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Válassza ki az új **DeltaCopyFromBlobPipeline** kapcsolatot a második folyamat futtatásához, amikor jön, és ugyanezt a részleteket. Látni fogja, hogy a forrásfájlt (file2.txt) a **forrás/2020/03/17/04/** forrásból a **cél/2020/03/17/04/** célhelyre másolta ugyanazzal a fájlnévvel. Ugyanezt az Azure Storage Explorer (ahttps://storageexplorer.com/) **céltárolóban** lévő fájlok beolvasásával is ellenőrizheti.


## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben a Spark használatával](tutorial-transform-data-spark-portal.md)
