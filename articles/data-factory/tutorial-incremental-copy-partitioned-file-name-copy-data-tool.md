---
title: Azure Data Factory használatával történő növekményes másolása az új fájlok csak alapján particionált fájlnév idő |} A Microsoft Docs
description: Hozzon létre egy Azure-beli adat-előállítót, majd az adatok másolása eszközzel történő növekményes betöltése az új fájlok csak az idő particionált fájl neve alapján.
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
ms.openlocfilehash: 04707a747edb7cf26b4e7f4e3251b958f6f11f38
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56962295"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Növekményes másolása az új fájlok particionált fájlnév idő alapján az adatok másolása eszközzel

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezt követően használhatja az adatok másolása eszközzel létrehoz egy folyamatot, amely a növekményes átmásolja az új fájlokat idő particionált fájlnév alapján az Azure Blob storage-ból az Azure Blob storage. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Az Azure storage-fiók**: Blob storage-ot használja a _forrás_ és _fogadó_ adattár. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Hozzon létre két tárolót a Blob storage-ban

A Blob storage előkészítése az oktatóanyaghoz elvégezhesse ezeket a lépéseket.

1. Hozzon létre egy tárolót **forrás**.  Hozzon létre egy mappa elérési útját, **2019/02/26/14** a tárolóban. Hozzon létre egy üres szövegfájlba, és adja neki **file1.txt**. Töltse fel a mappa elérési útját a file1.txt **forrás/2019/02/26/14** a storage-fiókban.  Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](http://storageexplorer.com/).
    
    ![Fájlok feltöltése](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Módosítsa az UTC-idő a mappa nevét.  Például, ha az aktuális UTC időpontig 2:03 PM 2019. február 26., a hozhat létre a mappa elérési útját, **forrás/2019/02/26/14/** , a szabály által **forrás / {Year} / {Month} / {Day} / {Hour} /**.

2. Hozzon létre egy tárolót **cél**. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](http://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben kattintson az **+ Új** > **Adatok és analitika** > **Data Factory** elemre: 
   
   ![Új adat-előállító létrehozása](./media/tutorial-copy-data-tool/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
    ![Új adat-előállító](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
    
    Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:
   
   ![Új adat-előállító hibaüzenete](./media/tutorial-copy-data-tool/name-not-available-error.png)
   
   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
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
   
    ![Data factory kezdőlap](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő megnyitásához kattintson a **Tartalomkészítés és monitorozás** csempére. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az a **első lépések** lapon válassza ki a **adatok másolása** címére, ha az adatok másolása eszköz elindításához. 

   ![Az Adatok másolása eszköz csempéje](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Az a **tulajdonságok** lapon, tegye a következőket:

    a. A **feladatnév**, adja meg **DeltaCopyFromBlobPipeline**.

    b. A **feladat kiadása ütemben történik, vagy a feladat ütemezés**válassza **rendszeres ütemezés szerint fut**.

    c. A **típusú Trigger**válassza **Átfedésmentes ablak**.
    
    d. A **ismétlődési**, adja meg **1 óra**. 
    
    e. Kattintson a **Tovább** gombra. 
    
    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kattintson a **+ új kapcsolat létrehozása**, kapcsolatot szeretne hozzáadni.

    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Válassza ki **Azure Blob Storage** a katalógusban, és kattintson a **Folytatás**.

    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Az a **új társított szolgáltatás** lapon, válassza ki a storage-fiókjában az **Tárfiók neve** listában, és kattintson a **Befejezés**.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **tovább**. 
    
   ![Forrásadattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. **A bemeneti fájl vagy mappa kiválasztása** oldalon kövesse az alábbi lépéseket:
    
    a. Keresse meg és válassza a **forrás** tárolót, majd válassza ki **válasszon**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Alatt **viselkedését fájl**válassza **növekményes betöltés: idő particionált fájl/mappa nevének**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. A dinamikus mappa elérési útját, írási **forrás / {year} / {month} / {day} / {hour} /**, és módosítsa a formátumot, a következőket:
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Ellenőrizze **bináris másolat** kattintson **tovább**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Az a **célként megadott adattárba** lapon válassza ki a **Azure BLOB Storage szolgáltatásról**, amely ugyanazon a tárterületen forrás adattárként fiókra, és kattintson **tovább**.

    ![Céladattár lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Az a **a kimeneti fájl vagy mappa kiválasztása** lapon, tegye a következőket:
    
    a. Keresse meg és válassza a **cél** mappát, majd kattintson a **válasszon**.
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. A dinamikus mappa elérési útját, írási **forrás / {year} / {month} / {day} / {hour} /**, és módosítsa a formátumot, a következőket:
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Kattintson a **tovább**.
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.
    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva.  Várja meg, automatikusan aktivált folyamatfuttatást van szüksége (kapcsolatos egy óra múlva).  Ha fut, a **műveletek** oszlop a tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozásokat tartalmaz. Válassza ki **frissítése** frissítse a listát, és válassza ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. Láthatja, hogy a forrásfájl (file1.txt) lett átmásolva **forrás/2019/02/26/14/** való **cél/2019/02/26/14/** azonos nevű fájl.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Is ellenőrizheti az azonos Azure Storage Explorer használatával (http://storageexplorer.com/) megvizsgálja a fájlokat.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Hozzon létre egy másik üres szöveges fájlt új néven **fájl2.ref fájllal**. Töltse fel a mappa elérési útját a Fájl2.ref fájllal fájlt **forrás/2019/02/26 vagy 15** a storage-fiókban.   Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](http://storageexplorer.com/).    
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Előfordulhat, hogy vegye figyelembe, hogy egy új mappa elérési útját kötelező-e létrehozni. Módosítsa az UTC-idő a mappa nevét.  Például, ha az aktuális UTC időpontig 3:20-kor 2019. február 26., hozhat létre a mappa elérési útját, **forrás/2019/02/26/15/** , a szabály által **{Year} / {Month} / {Day} / {Hour} /**.
    
13. Vissza a **Folyamatfuttatások** nézetben válassza **minden folyamatok Futtatás**, és várjon, amíg a azonos folyamat aktiválása újra egy másik egy óra múlva automatikusan.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Válassza ki **nézet Tevékenységfuttatás** futtatható, ha a második folyamat származnak, és tegye meg ugyanezt részleteihez.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Láthatja, hogy a forrásfájl (fájl2.ref fájllal) lett átmásolva **forrás/2019/02/26/15/** való **cél/2019/02/26/15/** azonos nevű fájl.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Is ellenőrizheti az azonos Azure Storage Explorer használatával (http://storageexplorer.com/) megvizsgálja a fájlokat a **cél** tároló
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben a Spark használatával](tutorial-transform-data-spark-portal.md)