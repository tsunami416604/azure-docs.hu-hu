---
title: "Adatok másolása az Azure Adatok másolása eszközével | Microsoft Docs"
description: "Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy Azure Blob Storage-beli mappából egy másik mappába."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: c17e738e3db18503f7cdda24a5f01ceb78e4e6a3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="use-copy-data-tool-to-copy-data"></a>Adatok másolása az Adatok másolása eszközzel 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-copy-data-tool.md)

Ebben a rövid útmutatóban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol egy Azure Blob Storage-beli mappából egy másik mappába. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md). 
>
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél a következő hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory), és próbálja meg újból a létrehozást. A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listán csak a támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken/régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
   ![Data factory kezdőlap](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felületének (UI) külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére. 

## <a name="launch-copy-data-tool"></a>Az Adatok másolása eszköz elindítása

1. Az Adatok másolása eszköz elindításához az első lépéseket ismertető oldalon kattintson az **Adatok másolása** csempére. 

   ![Az Adatok másolása eszköz csempéje](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Az Adatok másolása eszköz **Tulajdonságok** lapján kattintson a **Tovább** gombra. Ezen az oldalon elnevezheti a folyamatot, és megadhatja a leírását. 

    ![Adatok másolása eszköz – Tulajdonságok lap](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon válassza ki az **Azure Blob Storage** elemet, majd kattintson a **Tovább** gombra.

    ![Forrásadattár lap](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. **Az Azure Blob Storage-fiók megadása** lapon válassza ki a **Storage-fiók nevét** a legördülő listából, és kattintson a Tovább gombra. 

    ![Blob Storage-fiók megadása](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    1. Navigáljon az **adftutorial/input** mappához. 
    2. Válassza ki az **emp.txt** fájlt.
    3. Kattintson a **Kiválasztás** elemre. Ezt a lépést átugorhatja, ha duplán kattint az **emp.txt** fájlra. 
    4. Kattintson a **Tovább** gombra. 

    ![Bemeneti fájl vagy mappa kiválasztása](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. A **Fájlformátum beállításai** lapon látható, hogy az eszköz automatikusan észleli az oszlopok és sorok határolókaraktereit. Ezután kattintson a **Tovább** gombra. Ezen a lapon emellett megtekintheti az adatok előnézetét, valamint a bemeneti adatok sémáját is. 

    ![Fájlformátum beállításai lap](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. A **Céladattár** lapon válassza ki az **Azure Blob Storage** csempét, majd kattintson a **Tovább** elemre. 

    ![Céladattár lap](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. **Az Azure Blob Storage-fiók megadása** lapon válassza ki az Azure Blob Storage-fiókot, és kattintson a **Tovább** gombra. 

    ![Fogadó adattár megadása lap](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. A **Kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket: 

    1. Adja meg az **adftutorial/output** értéket a **mappa elérési útjaként**.
    2. Adja meg az **emp.txt** nevet **fájlnévként**. 
    3. Kattintson a **Tovább** gombra. 

    ![Kimeneti fájl vagy mappa kiválasztása](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. A **Fájlformátum beállításai** lapon kattintson a **Tovább** elemre. 

    ![Fájlformátum beállításai lap](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. A **Beállítások** lapon kattintson a **Tovább** elemre. 

    ![Speciális beállítások lap](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Tekintse át a beállításokat az **Összegzés** lapon, majd kattintson a Tovább gombra. 

    ![Összefoglaló lap](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Az **Üzembe helyezés kész** lapon kattintson a **Monitorozás** lehetőségre a létrehozott folyamat monitorozásához. 

    ![Üzembe helyezés lap](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. Az alkalmazás ekkor átvált a **Monitorozás** lapra. Ezen a lapon látható a folyamat állapota. A lista frissítéséhez kattintson a **Frissítés** elemre. 
    
    ![Folyamatfuttatások monitorozása lap](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Kattintson a Műveletek oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. A folyamathoz egyetlen **Másolás** típusú tevékenység tartozik. 

    ![Tevékenységfuttatások lap](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

    ![A másolási művelet részletei](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Ellenőrizze, hogy az **emp.txt** fájl az **adftutorial** tároló **output** mappájában jött-e létre. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza. 
18. A kapcsolódó szolgáltatások, adatkészletek és folyamatok szerkesztéséhez lépjen a **Szerkesztés** lapra. Annak megismeréséhez, hogy ezek hogyan szerkeszthetőek a Data Factory felhasználói felületén, olvassa el az [Adat-előállító létrehozása az Azure Portal használatával](quickstart-create-data-factory-portal.md) című cikket.

    ![Szerkesztés lap](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 