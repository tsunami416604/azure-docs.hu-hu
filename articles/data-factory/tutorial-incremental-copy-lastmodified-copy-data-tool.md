---
title: Azure Data Factory használatával történő növekményes másolása az új és módosított fájlok csak alapján LastModifiedDate |} A Microsoft Docs
description: Hozzon létre egy Azure-beli adat-előállítót, majd az adatok másolása eszközzel történő növekményes betöltése az új fájlok csak LastModifiedDate alapján.
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
ms.openlocfilehash: d79b44d0123d64d6280939767e5df7b5f64a5fcb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445953"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Növekményes másolása az adatok másolása eszköz használatával LastModifiedDate alapján új és módosított fájlok

Ebben az oktatóanyagban egy adat-előállítót hoz létre az Azure Portal használatával. Ezt követően használhatja az adatok másolása eszközzel létrehoz egy folyamatot, amely a növekményes átmásolja az új és módosított fájlok csak az Azure Blob storage Azure Blob storage-ból a "LastModifiedDate" alapján. 

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

1. Hozzon létre egy tárolót **forrás**. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

2. Hozzon létre egy tárolót **cél**. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **adatok + analitika** > **adat-előállító**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

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
    
    d. A **ismétlődési**, adja meg **15 percen belül**. 
    
    e. Kattintson a **Tovább** gombra. 
    
    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kattintson a **+ új kapcsolat létrehozása**, kapcsolatot szeretne hozzáadni.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Válassza ki **Azure Blob Storage** a katalógusban, és kattintson a **Folytatás**.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Az a **új társított szolgáltatás** lapon, válassza ki a storage-fiókjában az **Tárfiók neve** listában, és kattintson a **Befejezés**.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **tovább**. 
    
   ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **A bemeneti fájl vagy mappa kiválasztása** oldalon kövesse az alábbi lépéseket:
    
    a. Keresse meg és válassza a **forrás** mappát, majd kattintson a **válasszon**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. A **viselkedését fájl**válassza **növekményes betöltés: LastModifiedDate**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Ellenőrizze **bináris másolat** kattintson **tovább**.
    
     ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Az a **célként megadott adattárba** lapon válassza ki a **Azure BLOB Storage szolgáltatásról** Ez az ugyanazon a tárterületen forrás adattárként fiókra, és kattintson **tovább**.

    ![Céladattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Az a **a kimeneti fájl vagy mappa kiválasztása** lapon, tegye a következőket:
    
    a. Keresse meg és válassza a **cél** mappát, majd kattintson a **válasszon**.
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Kattintson a **tovább**.
    
     ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **Tovább** gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók. Válassza ki **frissítése** frissítse a listát, és válassza ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Csak egy (másolási) tevékenység található a folyamatban, ezért csak egy bejegyzést lát. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). 

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Tekintettel arra, hogy nem áll fájl a **forrás** a blob storage-fiókjában, ezért nem jelenik meg minden olyan fájlt, amely a rendszer átmásolt tárolót **cél** tárolót a blob storage-fiókban.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Hozzon létre egy üres szövegfájlba, és nevezze el másként file1.txt. Töltse fel a file1.txt fájlt a **forrás** a storage-fiókban lévő tárolóba. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Vissza a **Folyamatfuttatások** nézetben válassza **minden folyamatok Futtatás**, és várjon, amíg a azonos folyamat aktiválása automatikusan újra.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Válassza ki **nézet Tevékenységfuttatás** a második Futtatás folyamatot, ha a látható származnak, és tegye meg ugyanezt részleteihez.  

    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Látni fogja (file1.txt) egy fájlt átmásolja a **forrás** tárolót, hogy a **cél** tárolót a storage-fiók.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Hozzon létre egy másik üres szövegfájlt, és nevezze el másként fájl2.ref fájllal. Töltse fel a Fájl2.ref fájllal fájlt a **forrás** a storage-fiókban lévő tárolóba. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).  
    
16. Tegye meg ugyanezt 13 és 14. lépés, és láthatja, hogy csak az új fájl (fájl2.ref fájllal) másolta a **forrás** tárolót, hogy a **cél** tárolót a storage-fiók található a következő folyamatfuttatáshoz.  
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Is ellenőrizheti az azonos Azure Storage Explorer használatával (https://storageexplorer.com/) megvizsgálja a fájlokat.
    
    ![Folyamatfuttatások monitorozása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben a Spark használatával](tutorial-transform-data-spark-portal.md)