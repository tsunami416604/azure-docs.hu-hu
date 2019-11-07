---
title: A Azure Data Factory használata az új fájlok növekményes másolására csak az idő particionált fájlneve alapján
description: Hozzon létre egy Azure-beli adatelőállítót, majd a Adatok másolása eszközzel fokozatosan töltse be az új fájlokat csak az idő particionált fájlnév alapján.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 1/24/2019
ms.openlocfilehash: 273aaaa2ac51f75edfad6da03d6720f58b7c3c47
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683448"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Az új fájlok növekményes másolása a Adatok másolása eszköz használatával, az időparticionált fájl neve alapján

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezt követően a Adatok másolása eszközzel létrehozhat egy folyamatot, amely Növekményesen átmásolja az új fájlokat az Azure Blob Storage-ból az Azure Blob Storage-ba történő időpartíciós fájlnév alapján. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage** _-_ fiók: a blob Storage használata _forrásként_ és fogadó adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Két tároló létrehozása a blob Storage-ban

Készítse elő a blob Storage-t az oktatóanyaghoz az alábbi lépések végrehajtásával.

1. Hozzon létre egy **forrás**nevű tárolót.  Hozzon létre egy mappa elérési útját **2019/02/26/14** -ként a tárolóban. Hozzon létre egy üres szövegfájlt, és nevezze el **file1. txt**néven. Töltse fel a file1. txt fájlt a Storage-fiókjában lévő **forrás/2019/02/26/14** mappa elérési útjára.  Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).
    
    ![fájlok feltöltése](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Állítsa be a mappa nevét az UTC időpontra.  Ha például az aktuális UTC-idő 2:03 PM, 2019, a mappa elérési útja **forrás/2019/02/26/14/** a **forrás/{év}/{hónap}/{Day}/{Hour}/** értékkel hozható létre.

2. Hozzon létre egy **célhely**nevű tárolót. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása** > **adatok és Analitika** > **Data Factory**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
    
    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:
   
   ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)
   
   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_ **ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki az Azure-**előfizetést** az új adat-előállító létrehozásához. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    a. Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    b. Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.

5. A **Verzió** résznél válassza a **V2** értéket.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken/régiókban is lehetnek.
7. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
8. Kattintson a **Létrehozás** gombra.
9. Az irányítópulton a folyamat állapotát az **Adat-előállító üzembe helyezése** csempe jelzi.

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
   
    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
11. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az **első lépések** lapon válassza ki a **Adatok másolása** címet a adatok másolása eszköz elindításához. 

   ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)
   
2. A **Tulajdonságok** oldalon hajtsa végre a következő lépéseket:

    a. A **feladat neve**alatt adja meg a **DeltaCopyFromBlobPipeline**.

    b. A **feladat lépésszám vagy a feladat ütemezés**alatt válassza **a rendszeres Futtatás ütemezés**szerint lehetőséget.

    c. Az **trigger típusa**területen válassza a **kiesési ablak**lehetőséget.
    
    d. Az **Ismétlődés**alatt adja meg az **1 óra**értéket. 
    
    e. Kattintson a **Tovább** gombra. 
    
    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. A kapcsolatok hozzáadásához kattintson az **+ új kapcsolatok létrehozása**lehetőségre.

    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Válassza ki az **Azure Blob Storage** elemet a katalógusból, majd kattintson a **Folytatás**gombra.

    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Az **új társított szolgáltatás** lapon válassza ki a Storage-fiók nevét a **Storage-fiók neve** listáról, majd kattintson a **Befejezés**gombra.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **tovább**gombra. 
    
   ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. **A bemeneti fájl vagy mappa kiválasztása** oldalon kövesse az alábbi lépéseket:
    
    a. Tallózással keresse meg és válassza ki a **forrás** tárolót, majd válassza **a kiválasztás lehetőséget.**
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. A **fájl betöltése viselkedés**területen válassza a **növekményes betöltés: idő particionált mappák/fájlnevek**lehetőséget.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Írja be a dinamikus mappa elérési útját **forrás/{év}/{hónap}/{nap}/{Hour}/** , és módosítsa a formátumot a következőképpen:
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Győződjön meg a **bináris másolásról** , majd kattintson a **tovább**gombra.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. A **cél adattár** lapon válassza ki a **AzureBlobStorage**, amely ugyanaz a Storage-fiók, mint az adatforrás-tároló, majd kattintson a **tovább**gombra.

    ![Céladattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. A **kimeneti fájl vagy mappa kiválasztása** lapon hajtsa végre a következő lépéseket:
    
    a. Tallózással keresse meg és jelölje ki a **célmappát** , majd kattintson a **kiválasztás**elemre.
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Írja be a dinamikus mappa elérési útját **forrás/{év}/{hónap}/{nap}/{Hour}/** , és módosítsa a formátumot a következőképpen:
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Kattintson a **Tovább** gombra.
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.
    ![üzembe helyezési oldal](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva.  Várnia kell, amíg a folyamat automatikusan elindul (körülbelül egy óra elteltével).  A művelet futtatásakor a **műveletek** oszlop hivatkozásokat tartalmaz a tevékenység futtatási részleteinek megtekintéséhez és a folyamat újrafuttatásához. A lista frissítéséhez kattintson a **frissítés** gombra, majd a **műveletek** oszlopban válassza a **tevékenység futtatása hivatkozás megjelenítése** hivatkozást. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Láthatja, hogy a forrásfájl (file1. txt) át lett másolva a **forrás/2019/02/26/14/** **cél/2019/02/26/14** /között ugyanazzal a fájlnévvel.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Ugyanezt a Azure Storage Explorer használatával is ellenőrizheti (https://storageexplorer.com/) a fájlok vizsgálatához.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Hozzon létre egy másik üres szövegfájlt az új névvel a **fájl2. txt**néven. Töltse fel a fájl2. txt fájlt a Storage-fiókjában lévő **forrás/2019/02/26/15** mappa elérési útjára.   Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).   
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Előfordulhat, hogy az új mappa elérési útjának létrehozására van szükség. Állítsa be a mappa nevét az UTC időpontra.  Ha például az aktuális UTC-idő 3:20 PM, 2019. február 26-án a mappa elérési útja a következőképpen hozható létre: **forrás/2019/02/26/15/** a (z) **{Year}/{month}/{Day}/{Hour}/** szabálya.
    
13. Ha vissza szeretne térni a **folyamat futási** nézetéhez, válassza a **minden folyamat fut**lehetőséget, és várjon, amíg egy másik óra elteltével a folyamat automatikusan újraindul.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. A második folyamat futásakor válassza a **Megtekintés tevékenység futtatása** lehetőséget, és végezze el a részletek áttekintését.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Láthatja, hogy a forrásfájl (fájl2. txt) át lett másolva a **forrás/2019/02/26/15/** **cél/2019/02/** 26/15/között, ugyanazzal a fájlnévvel.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Ugyanezt a Azure Storage Explorer használatával is ellenőrizheti (https://storageexplorer.com/) **a tárolóban** lévő fájlok vizsgálatához.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben a Spark használatával](tutorial-transform-data-spark-portal.md)