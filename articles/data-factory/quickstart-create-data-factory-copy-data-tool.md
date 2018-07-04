---
title: Adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs
description: Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy Azure Blob Storage-beli helyről egy másik helyre.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 4df392ec7e100ef0efcbb3876079710a6b9ca4fb
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048026"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Adatok másolása az Adatok másolása eszközzel 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-copy-data-tool.md)

Ebben a rövid útmutatóban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol egy Azure Blob Storage-beli mappából egy másik mappába. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson az **Új** elemre a bal oldali menüben, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Az **Új data factory** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
      
   ![„Új data factory” lap](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Az Azure data factory nevének *globálisan egyedinek* kell lennie. Ha a következő hibaüzenetet kapja, módosítsa az adat-előállító nevét (például a **&lt;sajátneve&gt;ADFTutorialDataFactory** értékre), majd próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet, ha egy név nem érhető el](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd válasszon egy meglévő erőforráscsoportot a listából. 
   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. **Hely:** válassza ki az adat-előállító helyét. 

   Csak a támogatott helyek jelennek meg a listában. A Data Factory által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (Azure HDInsight stb.) más helyeken/régiókban is lehetnek.

6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Létrehozás** gombra.
8. Az irányítópulton megjelenő csempén a **Adat-előállító üzembe helyezése** állapotleírás látható: 

    ![„Adat-előállító üzembe helyezése” csempe](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. A létrehozás befejezése után megjelenik a **Data Factory** lap. Az Azure Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.
   
   ![Az adat-előállító kezdőlapja a „Létrehozás és monitorozás” csempével](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Az Adatok másolása eszköz elindítása

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére. 

   ![„Adatok másolása” csempe](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)

2. Az Adatok másolása eszköz **Tulajdonságok** lapján megadhatja a folyamat nevét és leírását, majd kattintson a **Tovább** lehetőségre. 

   ![„Tulajdonságok” lap](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** lehetőségre.

    ![„Forrásadattár” lap](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    b. Válassza a katalógusból az **Azure Blob Storage** elemet, majd válassza a **Tovább** lehetőséget.

    ![A Blob Storage kiválasztása a katalógusból](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Az **Azure Blob Storage-fiók megadása** lapon válassza ki tárfiókját a **Tárfiók neve** listából, és kattintson a **Befejezés** gombra. 

   ![Az Azure Blob Storage-fiók konfigurálása](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Forrásnak válassza ki az újonnan létrehozott társított szolgáltatást, majd kattintson a **Tovább** gombra.

   ![Forráshoz társított szolgáltatás kiválasztása](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


4. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

   a. A **Tallózás** elemre kattintva lépjen az **adftutorial/input** mappához, válassza az **emp.txt** fájlt, majd kattintson a **Kiválasztás** lehetőségre. 

   ![„A bemeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. A fájl változtatás nélküli másolásához jelölje be a **Bináris másolat** lehetőséget, majd válassza a **Tovább** lehetőséget. 

   ![„A bemeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


5. A **Céladattár** lapon válassza ki az imént létrehozott **Azure Blob Storage** társított szolgáltatást, majd válassza a **Tovább** gombot. 

   ![„Céladattár” lap](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

6. **A kimeneti fájl vagy mappa kiválasztása** lapon írja be az **adftutorial/output** karakterláncot mappaútvonalnak, majd válassza a **Tovább** lehetőséget. 

   ![„A kimeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

7. A **Beállítások** lapon kattintson a **Tovább** gombra az alapértelmezett konfigurációk használatához. 

8. Tekintse át a beállításokat az **Összegzés** lapon, majd válassza a **Tovább** gombot. 

    ![„Összegzés” lap](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

9. Az **Üzembe helyezés kész** lapon kattintson a **Monitorozás** elemre a létrehozott folyamat monitorozásához. 

    ![„Üzembe helyezés kész” lap](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

10. Az alkalmazás ekkor átvált a **Monitorozás** lapra. Ezen a lapon látható a folyamat állapota. A lista frissítéséhez kattintson a **Frissítés** gombra. 
    
    ![Folyamat futtatásának figyelése](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

11. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. A folyamathoz egyetlen **Másolás** típusú tevékenység tartozik. 

    ![Tevékenységfuttatás monitorozása](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
12. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md).

    ![A másolási művelet részletei](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

13. Ellenőrizze, hogy az **emp.txt** fájl az **adftutorial** tároló **output** mappájában jött-e létre. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza. 

14. A kapcsolódó szolgáltatások, adatkészletek és folyamatok szerkesztéséhez lépjen a bal oldali panelen a **Monitorozás** lap fölötti **Szerző** lapra. Annak megismeréséhez, hogy ezek hogyan szerkeszthetőek a Data Factory felhasználói felületén, olvassa el az [adat-előállító az Azure Portal használatával történő létrehozását](quickstart-create-data-factory-portal.md) ismertető cikket.

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra az Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 