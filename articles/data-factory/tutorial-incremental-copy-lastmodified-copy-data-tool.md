---
title: Új és módosított fájlok növekményes másolása LastModifiedDate alapján a Adatok másolása eszköz használatával
description: Hozzon létre egy Azure-beli adatelőállítót, majd használja az Adatok másolása eszközt az új fájlok LastModifiedDate-alapú növekményes betöltéséhez.
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
ms.openlocfilehash: 09a9fa4515913470c86bbafe293add007a3117ea
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683461"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Új és módosított fájlok növekményes másolása LastModifiedDate alapján a Adatok másolása eszköz használatával

Ebben az oktatóanyagban a Azure Portal használatával hozzon létre egy adatelőállítót. Ezt követően a Adatok másolása eszköz használatával létrehozhat egy folyamatot, amely fokozatosan átmásolja az új és módosított fájlokat, az Azure Blob Storage-ból az Azure Blob Storage-ba való **LastModifiedDate** függően.

Ezzel az ADF megvizsgálja az összes fájlt a forrás-áruházból, alkalmazza a LastModifiedDate, és csak az utolsó időpont óta másolja az új és frissített fájlt.  Vegye figyelembe, hogy ha lehetővé teszi, hogy az ADF nagy mennyiségű fájlt helyezzen el, de csak néhány fájl másolását másolja a célhelyre, akkor a fájl-ellenőrzés miatt is hosszú időt kell várnia.   

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban a következő feladatokat fogja elvégezni:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage** _-_ fiók: a blob Storage használata _forrásként_ és fogadó adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-quickstart-create-account.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Két tároló létrehozása a blob Storage-ban

Készítse elő a blob Storage-t az oktatóanyaghoz az alábbi lépések végrehajtásával.

1. Hozzon létre egy **forrás**nevű tárolót. A feladat elvégzéséhez különböző eszközöket használhat, például [Azure Storage Explorer](https://storageexplorer.com/).

2. Hozzon létre egy **célhely**nevű tárolót. 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az **erőforrás létrehozása** > **adatok és Analitika** > **Data Factory**: 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
 
   Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:
   
   ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_ **ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki azt az Azure- **előfizetést** , amelyben létre kívánja hozni az új adatgyárat. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
    * Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    * Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/resource-group-overview.md) ismertető cikket.

5. A **verzió**alatt válassza a **v2**elemet.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattárolók (például az Azure Storage és a SQL Database) és a számítási erőforrások (például az Azure HDInsight) más helyszíneken és régiókban is lehetnek.
7. Válassza a **Rögzítés az irányítópulton** lehetőséget. 
8. Kattintson a **Létrehozás** gombra.
9. Az irányítópulton tekintse meg az **üzembe helyezés Data Factory** csempét a folyamat állapotának megtekintéséhez.

    ![Data Factory csempe üzembe helyezése](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
   
    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)
11. A Azure Data Factory felhasználói felületének (UI) külön lapon való megnyitásához válassza a **szerző & monitorozás** csempét. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az **első lépések** lapon válassza ki a **Adatok másolása** címet a adatok másolása eszköz megnyitásához. 

   ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)
   
2. A **Tulajdonságok** oldalon hajtsa végre a következő lépéseket:

    a. A **feladat neve**alatt adja meg a **DeltaCopyFromBlobPipeline**.

    b. A **feladat lépésszám** vagy a **feladat ütemezés**alatt válassza **a rendszeres Futtatás ütemezés**szerint lehetőséget.

    c. Az **trigger típusa**területen válassza a **kiesési ablak**lehetőséget.
    
    d. Az **Ismétlődés**alatt adja meg a **15 percet**. 
    
    e. Kattintson a **Tovább** gombra. 
    
    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven. 

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. A kapcsolatok hozzáadásához válassza az **+ új kapcsolatok létrehozása**lehetőséget.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Válassza ki az **Azure Blob Storage** elemet a katalógusból, majd válassza a **Folytatás**lehetőséget.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Az **új társított szolgáltatás** lapon válassza ki a Storage-fiók nevét a **Storage-fiók neve** listából, majd válassza a **Befejezés**lehetőséget.
    
    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **tovább**gombra. 
    
   ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:
    
    a. Tallózással keresse meg és válassza ki a **forrás** mappát, majd válassza **a kiválasztás lehetőséget.**
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. A **fájl betöltése viselkedés**területen válassza a **növekményes betöltés: LastModifiedDate**lehetőséget.
    
    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Győződjön meg a **bináris másolásról** , majd kattintson a **Tovább gombra**.
    
     ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. A **cél adattár** lapon válassza a **AzureBlobStorage**lehetőséget. Ez ugyanaz a Storage-fiók, mint a forrás-adattár. Ezután kattintson a **Tovább** gombra.

    ![Céladattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. **A kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:
    
    a. Tallózással keresse meg és jelölje ki a **célmappát** , majd válassza **a kiválasztás lehetőséget.**
    
    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Kattintson a **Tovább** gombra.
    
     ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![Beállítások lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Az **Összefoglalás** lapon tekintse át a beállításokat, majd kattintson a **tovább**gombra.

    ![Összefoglaló lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. A tevékenységfuttatási részletek megtekintéséhez és a folyamat ismételt futtatásához használható hivatkozások a **Műveletek** oszlopban találhatók. A lista frissítéséhez kattintson a **frissítés** gombra, majd a **műveletek** oszlopban válassza a **tevékenység futtatása hivatkozás megjelenítése** hivatkozást. 

    ![Lista frissítése és a tevékenységek megtekintésének kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Csak egy tevékenység (másolási tevékenység) található a folyamatban, így csak egy bejegyzés jelenik meg. A másolási művelet részleteinek megtekintéséhez válassza a **Műveletek** oszlop **Részletek** hivatkozását (szemüveg ikon). 

    ![A másolási tevékenység folyamatban van](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Mivel a blob Storage-fiókban nincs fájl a **forrás** tárolóban, nem jelenik meg a blob Storage-fiókban a **cél** tárolóba másolt fájl.
    
    ![Nincs fájl a forrás tárolóban vagy a tárolóban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Hozzon létre egy üres szövegfájlt, és nevezze el a **file1. txt**fájlba. Töltse fel ezt a szövegfájlt a Storage-fiókban található **forrás** tárolóba. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Hozzon létre file1. txt fájlt, és töltse fel a forrás tárolóba](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Ha vissza szeretne térni a **folyamat futási** nézetéhez, válassza a **minden folyamat futtatása**lehetőséget, és várjon, amíg a folyamat automatikusan elindul.  

    ![Az összes folyamat futtatásának kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. A második folyamat futtatásához válassza a **Megtekintés tevékenység futtatása** lehetőséget, amikor megjelenik. Ezután tekintse át a részleteket ugyanúgy, ahogy az első folyamat futtatásához.  

    ![Válassza ki a tevékenység futtatásának megtekintése és a részletek áttekintése elemet.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Ekkor megjelenik egy fájl (file1. txt) a **forrás** -tárolóból a blob Storage-fiókhoz tartozó **cél** tárolóba.
    
    ![A file1. txt fájl a forrás tárolóból a célhelyre lett másolva.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Hozzon létre egy másik üres szövegfájlt, és nevezze el a **fájl2. txt**fájlba. Töltse fel ezt a szövegfájlt a blob Storage-fiókban található **forrás** tárolóba.   
    
16. Ismételje meg a 13. és a 14. lépést a második szövegfájlban. Látni fogja, hogy csak az új fájl (fájl2. txt) lett átmásolva a **forrás** tárolóból a Storage-fiók **célhelyére** a következő folyamat futtatásakor.  
    
    ![A fájl2. txt fájl a forrás tárolóból a célhelyre lett másolva.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Ezt a [Azure Storage Explorer](https://storageexplorer.com/) használatával is ellenőrizheti a fájlok vizsgálatához.
    
    ![Fájlok vizsgálata Azure Storage Explorer használatával](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>További lépések
Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan alakíthatja át az adatátalakítást egy Apache Spark-fürt használatával az Azure-ban:

> [!div class="nextstepaction"]
>[A felhőben tárolt adatátalakítás Apache Spark fürt használatával](tutorial-transform-data-spark-portal.md)