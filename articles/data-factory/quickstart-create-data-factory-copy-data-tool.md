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
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: aea76544f244adba8368a1d8fbe268746060b5a6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
ms.locfileid: "30169825"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Adatok másolása az Adatok másolása eszközzel 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-copy-data-tool.md)

Ebben a rövid útmutatóban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol egy Azure Blob Storage-beli mappából egy másik mappába. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md). 
>
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a szolgáltatás általánosan elérhető 1. verzióját használja, olvassa el a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


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
4. **Verzió:** válassza a **V2 (előzetes verzió)** értéket.
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
2. Az Adatok másolása eszköz **Tulajdonságok** lapján kattintson a **Tovább** gombra. Ezen a lapon elnevezheti a folyamatot, és megadhatja a leírását. 

   ![„Tulajdonságok” lap](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. A **Forrásadattár** lapon válassza ki az **Azure Blob Storage** elemet, majd kattintson a **Tovább** gombra.

   ![„Forrásadattár” lap](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Az **Azure Blob Storage-fiók megadása** lapon válassza ki tárfiókját a **Storage-fiók neve** listából, és kattintson a **Tovább** gombra. 

   ![„Azure Blob Storage-fiók megadása” lap](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

   a. Navigáljon az **adftutorial/input** mappához.

   b. Válassza ki az **emp.txt** fájlt.

   c. Kattintson a **Kiválasztás** elemre. Ezt a lépést átugorhatja, ha duplán kattint az **emp.txt** fájlra.

   d. Kattintson a **Tovább** gombra. 

   ![„A bemeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. A **Fájlformátum beállításai** lapon látható, hogy az eszköz automatikusan észleli az oszlopok és sorok határolókaraktereit. Ezután kattintson a **Tovább** gombra. Ezen a lapon emellett megtekintheti az adatok előnézetét, valamint a bemeneti adatok sémáit is. 

   ![„Fájlformátum beállításai” lap](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. A **Céladattár** lapon válassza ki az **Azure Blob Storage** elemet, majd kattintson a **Tovább** gombra. 

   ![„Céladattár” lap](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Az **Azure Blob Storage-fiók megadása** lapon válassza ki az Azure Blob Storage-fiókot, és kattintson a **Tovább** gombra. 

   ![„Azure Blob Storage-fiók megadása” lap](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. **A kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket: 

   a. Adja meg az **adftutorial/output** értéket a mappa elérési útjaként.

   b. Adja meg az **emp.txt** nevet fájlnévként.

   c. Kattintson a **Tovább** gombra. 

   ![„A kimeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. A **Fájlformátum beállításai** lapon kattintson a **Tovább** gombra. 

    ![„Fájlformátum beállításai” lap](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. A **Beállítások** lapon kattintson a **Tovább** gombra. 

    ![„Beállítások” lap](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Tekintse át a beállításokat az **Összegzés** lapon, majd kattintson a **Tovább** gombra. 

    ![„Összegzés” lap](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Az **Üzembe helyezés kész** lapon kattintson a **Monitorozás** elemre a létrehozott folyamat monitorozásához. 

    ![„Üzembe helyezés kész” lap](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. Az alkalmazás ekkor átvált a **Monitorozás** lapra. Ezen a lapon látható a folyamat állapota. A lista frissítéséhez kattintson a **Frissítés** gombra. 
    
    ![Folyamatok monitorozására szolgáló lap, „Frissítés” gombbal](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Kattintson a **Műveletek** oszlopban található **Tevékenységfuttatások megtekintése** hivatkozásra. A folyamathoz egyetlen **Másolás** típusú tevékenység tartozik. 

    ![Tevékenységfuttatások listája](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

    ![A másolási művelet részletei](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Ellenőrizze, hogy az **emp.txt** fájl az **adftutorial** tároló **output** mappájában jött-e létre. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza. 
18. A kapcsolódó szolgáltatások, adatkészletek és folyamatok szerkesztéséhez lépjen a **Szerkesztés** lapra. Annak megismeréséhez, hogy ezek hogyan szerkeszthetőek a Data Factory felhasználói felületén, olvassa el az [adat-előállító az Azure Portal használatával történő létrehozását](quickstart-create-data-factory-portal.md) ismertető cikket.

    ![Szerkesztés lap](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra az Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 