---
title: Adatok másolása az Azure Copy Data eszközzel
description: Létrehozhat egy Azure-beli adat-előállítót, majd az Adatok másolása eszközzel másolhat adatokat egy Azure Blob Storage-beli helyről egy másik helyre.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 03/18/2020
ms.openlocfilehash: 26169755fbe252a4be2626dae50d40c005c7c6db
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130927"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Rövid útmutató: Adatok másolása az Adatok másolása eszközzel

> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-copy-data-tool.md)

Ebben a rövid útmutatóban egy adat-előállítót hoz létre az Azure Portal használatával. Ezután az Adatok másolása eszközzel létrehoz egy folyamatot, amely adatokat másol egy Azure Blob Storage-beli mappából egy másik mappába. 

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
1. Nyissa meg az [Azure Portalt.](https://portal.azure.com) 
1. Az Azure Portal menüben válassza az > **Erőforrás-elemzési** > **adatgyár** **létrehozása lehetőséget:**

    ![Új adat-előállító létrehozása](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Az **Új data factory** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket. 
 
   Az Azure-adat-előállító nevének *globálisan egyedinek*kell lennie. Ha a következő hiba jelenik meg, módosítsa az adatgyár nevét (például ** &lt;az Önnevét&gt;ADFTutorialDataFactory),** és próbálja meg újra létrehozni. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
   ![Hibaüzenet, ha egy név nem érhető el](./media/doc-common-process/name-not-available-error.png)
1. **Előfizetés:** válassza ki azt az Azure-előfizetést, amelyben az adat-előállítót létre szeretné hozni. 
1. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd válasszon egy meglévő erőforráscsoportot a listából. 
   - Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét.   
         
   Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
1. A **Verzió** résznél válassza a **V2** értéket.
1. **Hely:** válassza ki az adat-előállító helyét.

   A listában csak a Data Factory által támogatott helyek és az Azure Data Factory-metaadatok tárolási helye jelenik meg. A kapcsolódó adattárak (például az Azure Storage és az Azure SQL Database) és a számítások (például az Azure HDInsight), hogy a Data Factory által használt futtatható más régiókban.

1. Kattintson a **Létrehozás** gombra.

1. A létrehozás befejezése után megjelenik a **Data Factory** oldal. Az Azure Data Factory felhasználói felületi (UI) alkalmazás külön lapon történő elindításához kattintson a **Létrehozás és monitorozás** csempére.
   
   ![Az adat-előállító kezdőlapja a „Létrehozás és monitorozás” csempével](./media/doc-common-process/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Az Adatok másolása eszköz elindítása

1. Az Adatok másolása eszköz elindításához az **első lépéseket** ismertető oldalon kattintson az **Adatok másolása** csempére. 

   ![„Adatok másolása” csempe](./media/doc-common-process/get-started-page.png)

1. Az Adatok másolása eszköz **Tulajdonságok** lapján megadhatja a folyamat nevét és leírását, majd kattintson a **Tovább** lehetőségre. 

   ![„Tulajdonságok” lap](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához kattintson a **+ Új kapcsolat létrehozása** gombra.

    b. Válassza ki a forráskapcsolathoz létrehozni kívánt csatolt szolgáltatástípust. Ebben az oktatóanyagban az **Azure Blob Storage-ot használjuk.** Jelölje ki a gyűjteményből, majd válassza a **Folytatás gombot.**
    
    ![Blob kiválasztása](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Az **új csatolt szolgáltatás (Azure Blob Storage)** lapon adja meg a csatolt szolgáltatás nevét. Válassza ki a tárfiókot a **Tárfiók** névlistájából, tesztelje a kapcsolatot, majd válassza a **Létrehozás lehetőséget.** 

    ![Az Azure Blob Storage-fiók konfigurálása](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Jelölje ki az újonnan létrehozott csatolt szolgáltatást forrásként, majd kattintson a **Tovább**gombra.


1. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

   a. A **Tallózás gombra** kattintva keresse meg az **adftutorial/input** mappát, jelölje ki az **emp.txt** fájlt, majd kattintson a **Választás gombra.** 

   d. Jelölje be a **Bináris másolás** jelölőnégyzetet a fájl másként való másolásához, majd a **Tovább**gombra. 

   ![„A bemeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. A **Cél adattár** lapon válassza ki a létrehozott **Azure Blob Storage-kapcsolt** szolgáltatást, majd kattintson a **Tovább**gombra. 

1. A **Kimeneti fájl vagy mappa kiválasztása** lapon adja meg az **adftutorial/output** parancsot a mappa elérési útjához, majd kattintson a **Tovább**gombra. 

   ![„A kimeneti fájl vagy mappa kiválasztása” lap](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. A **Beállítások** lapon kattintson a **Tovább** gombra az alapértelmezett konfigurációk használatához. 

1. Tekintse át a beállításokat az **Összegzés** lapon, majd válassza a **Tovább** gombot. 

1. Az **Üzembe helyezés kész** lapon kattintson a **Monitorozás** elemre a létrehozott folyamat monitorozásához. 

    ![„Üzembe helyezés kész” lap](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Az alkalmazás átvált a **Monitor** lapra. Ezen a lapon láthatja a folyamat **Refresh** állapotát. Kattintson a **FOLYAMAT NEVE** csoporthivatkozásra a tevékenységfuttatás részleteinek megtekintéséhez vagy a folyamat újbóli futtatásához. 
   
    ![Folyamat frissítése](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. A Tevékenység futtatása lapon válassza a **Részletek** hivatkozást (szemüveg ikon) a **TEVÉKENYSÉG NEVE** oszlopban a másolási művelettel kapcsolatos további részletekért. A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

1. Ha vissza szeretne lépni a Folyamatfuttatások nézetbe, válassza az **ALL folyamat futtatása** hivatkozást a navigációs menüben. A nézet frissítéséhez válassza a **Frissítés** parancsot. 

1. Ellenőrizze, hogy az **emp.txt** fájl az **adftutorial** tároló **output** mappájában jött-e létre. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza azt. 

1. A kapcsolódó szolgáltatások, adatkészletek és folyamatok szerkesztéséhez lépjen a bal oldali panelen a **Monitorozás** lap fölötti **Szerző** lapra. Annak megismeréséhez, hogy ezek hogyan szerkeszthetőek a Data Factory felhasználói felületén, olvassa el az [adat-előállító az Azure Portal használatával történő létrehozását](quickstart-create-data-factory-portal.md) ismertető cikket.

    ![Szerző kiválasztása lap](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra az Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 
