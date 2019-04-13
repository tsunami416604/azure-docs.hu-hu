---
title: Növekményes másolása az adatok másolása eszköz használatával LastModifiedDate alapján új és módosított fájlok |} A Microsoft Docs
description: Hozzon létre egy Azure-beli adat-előállítót, majd az adatok másolása eszközzel történő növekményes betöltése az új fájlok LastModifiedDate alapján.
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
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544483"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Növekményes másolása az adatok másolása eszköz használatával LastModifiedDate alapján új és módosított fájlok

Ebben az oktatóanyagban az Azure Portalon való egy adat-előállító létrehozásához használni kívánt. Ezt követően használni az adatok másolása eszközzel létrehoz egy folyamatot, amely alapján növekményes másol csak, új és módosított fájlok azok **LastModifiedDate** Azure Blob storage Azure Blob Storage-ból.

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban végre fogja hajtani a következő feladatokat:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Az Azure storage-fiók**: Blob storage-ot használja a _forrás_ és _fogadó_ adattár. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Hozzon létre két tárolót a Blob storage-ban

A Blob storage előkészítése az oktatóanyaghoz elvégezhesse ezeket a lépéseket.

1. Hozzon létre egy tárolót **forrás**. Ez a feladat végrehajtásához használhatja a különböző eszközök [Azure Storage Explorer](https://storageexplorer.com/).

2. Hozzon létre egy tárolót **cél**. 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza ki a **erőforrás létrehozása** > **adatok + analitika** > **adat-előállító**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
     ![Új adat-előállító](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:
   
   ![Új adat-előállító hibaüzenete](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki az Azure **előfizetés** található, amely fog létrehozni az új adat-előállító. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    * Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    * Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.

5. A **verzió**válassza **V2**.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Amely az adat-előállító használ adattárak (például Azure Storage és SQL Database) és számítási erőforrások (például Azure HDInsight) más helyeken / régiókban is lehet.
7. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
8. Kattintson a **Létrehozás** gombra.
9. Az irányítópulton, tekintse meg a **adat-előállító üzembe helyezése** csempére kattintva megtekintheti a folyamat állapotát.

    ![Adat-előállító üzembe helyezése csempe](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Az Azure Data Factory felhasználói felületének (UI) külön lapon megnyitásához válassza a **létrehozás és Monitorozás** csempére. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az a **első lépések** lapon válassza ki a **adatok másolása** címére kattintva nyissa meg az adatok másolása eszközzel. 

   ![Az Adatok másolása eszköz csempéje](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Az a **tulajdonságok** lapon, tegye a következőket:

    a. A **feladatnév**, adja meg **DeltaCopyFromBlobPipeline**.

    b. A **feladat kiadása ütemben történik** vagy **ütemezett feladat**válassza **rendszeres ütemezés szerint fut**.

    c. A **típusú Trigger**válassza **Átfedésmentes ablak**.
    
    d. A **ismétlődési**, adja meg **15 percen belül**. 
    
    e. Kattintson a **Tovább** gombra. 
    
    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Válassza ki **+ új kapcsolat létrehozása**, kapcsolatot szeretne hozzáadni.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Válassza ki **Azure Blob Storage** a katalógusban, és válassza ki a **Folytatás**.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Az a **új társított szolgáltatás** lapon, válassza ki a tárfiókját a **Tárfiók neve** listában, majd **Befejezés**.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Válassza ki az újonnan létrehozott társított szolgáltatást, majd **tovább**. 
    
   ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:
    
    a. Keresse meg és válassza a **forrás** mappát, és válassza ki **válasszon**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. A **viselkedését fájl**válassza **növekményes betöltés: LastModifiedDate**.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Ellenőrizze **bináris másolat** válassza **tovább**.
    
     ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Az a **célként megadott adattárba** lapon jelölje be **Azure BLOB Storage szolgáltatásról**. Ez a ugyanazt a tárfiókot, a forrásadattár. Ezután kattintson a **Tovább** gombra.

    ![Céladattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. **A kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:
    
    a. Keresse meg és válassza a **cél** mappát, és válassza ki **válasszon**.
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Kattintson a **Tovább** gombra.
    
     ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Az a **összefoglalás** lapon. tekintse át a beállításokat, majd válassza ki **tovább**.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók. Válassza ki **frissítése** frissítse a listát, és válassza ki a **Tevékenységfuttatások megtekintése** hivatkozásra a **műveletek** oszlop. 

    ![Frissítse a listát, és válassza ki a Tevékenységfuttatások megtekintése](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Nincs (a másolási tevékenység) csak egyetlen tevékenység található a folyamatban, ezért csak egy bejegyzést. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). 

    ![A másolási tevékenység van folyamatban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Mivel nem áll fájl a a **forrás** tárolót a Blob storage-fiókban, nem jelenik meg minden olyan fájlt, másolja a **cél** tárolót a Blob storage-fiókban.
    
    ![A forrás-tároló vagy a cél tároló nem fájl](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Hozzon létre egy üres szövegfájlba **file1.txt**. Töltse fel a szöveges fájlt a **forrás** a storage-fiókban lévő tárolóba. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).   

    ![File1.txt létrehozása és feltöltése az forrástároló](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Vissza a **Folyamatfuttatások** nézetben válassza **összes Folyamatfuttatást**, és várjon, amíg az azonos folyamat ismét automatikusan aktiválódik.  

    ![Válassza ki az összes Folyamatfuttatások](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Válassza ki **nézet Tevékenységfuttatás** futtatható, ha a második folyamat megjelenik. Tekintse át a részleteket az első folyamat futásának hasonló megegyező módon.  

    ![Válassza ki a nézet Tevékenységfuttatás és részletek áttekintése](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Lásd: az egyik fogja (file1.txt) fájlt átmásolja a **forrás** tároló a **cél** tároló, Blob storage-fiókjában.
    
    ![Céltároló file1.txt másolta a forrás-tárolóból](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Hozzon létre egy másik üres szövegfájlt, és adja neki **fájl2.ref fájllal**. Töltse fel a szöveges fájlt a **forrás** tárolót a Blob storage-fiókban.   
    
16. A második szöveges fájlt ismételje meg az 13 és 14. Látni fogja, hogy csak az új fájl (fájl2.ref fájllal) a program átmásolta a **forrás** tárolót, hogy a **cél** tárolót a storage-fiók található a következő folyamatfuttatáshoz.  
    
    ![Céltároló fájl2.ref fájllal másolta a forrás-tárolóból](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Ezt is ellenőrizheti a [Azure Storage Explorer](https://storageexplorer.com/) megvizsgálja a fájlokat.
    
    ![Azure Storage Explorer használatával fájlok vizsgálata](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal, az adatok átalakítása az Azure-on Apache Spark-fürt használatával:

> [!div class="nextstepaction"]
>[Adatátalakítás a felhőben egy Apache Spark-fürt használatával](tutorial-transform-data-spark-portal.md)