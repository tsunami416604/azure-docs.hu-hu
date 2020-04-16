---
title: Adateszköz az új és frissített fájlok növekményes másolásához
description: Hozzon létre egy Azure-adat-előállítót, majd az Adatok másolása eszközzel növekményesen töltse be az új fájlokat a LastModifiedDate alapján.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399490"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Új és módosított fájlok fokozatos másolása a LastModifiedDate alapján az Adatok másolása eszközzel

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben az oktatóanyagban az Azure Portal használatával hozzon létre egy adat-előállító. Ezután az Adatok másolása eszközzel hozzon létre egy folyamatot, amely csak az új és a módosított fájlokat másolja az Azure Blob storage-ból az Azure Blob storage-ba. A `LastModifiedDate` másoláshoz használt fájlok at.

Miután itt elvégezte a lépéseket, az Azure Data Factory megvizsgálja a forrástárolóban lévő összes fájlt, alkalmazza a fájlszűrőt `LastModifiedDate`, és csak a céltárolóba csak azokat a fájlokat másolja, amelyek újak, vagy a legutóbbi időpont óta frissültek. Vegye figyelembe, hogy ha a Data Factory nagy számú fájlt vizsgál, akkor is hosszú időtartamokra számíthat. A fájlbeolvasás időigényes, még akkor is, ha a másolt adatok mennyisége csökken.

> [!NOTE]
> Ha még csak ismerkedik a Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban az alábbi feladatokat hajthatja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure Storage-fiók:** Blob storage használata a forrás- és a fogadó adattárak. Ha nem rendelkezik Azure Storage-fiókkal, kövesse a Tárfiók létrehozása című útmutató [utasításait.](../storage/common/storage-account-create.md)

## <a name="create-two-containers-in-blob-storage"></a>Hozzon létre két tárolót a Blob storage-ban

Készítse elő a Blob-tárolót az oktatóanyagra az alábbi lépések végrehajtásával:

1. Hozzon létre egy **forrás**nevű tárolót. A feladat végrehajtásához különböző eszközöket használhat, például [az Azure Storage Explorert.](https://storageexplorer.com/)

2. **Hozzon**létre egy cél nevű tárolót.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali panelen válassza az **Erőforrás létrehozása** elemet. **Válassza az Analytics** > **Data Factory lehetőséget:**

   ![Adatgyár kiválasztása](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének globálisan egyedinek kell lennie. A következő hibaüzenet jelenhet meg:

   ![A név nem érhető el hibaüzenet](./media/doc-common-process/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Az **Előfizetés**csoportban válassza ki azt az Azure-előfizetést, amelyben létre fogja hozni az új adatgyárat.
4. Az **Erőforráscsoport**csoport csoportban tegye az alábbi lépések egyikét:

    * Válassza **a Meglévő használata lehetőséget,** majd jelöljön ki egy meglévő erőforráscsoportot a listában.

    * Válassza **az Új létrehozása lehetőséget,** majd adja meg az erőforráscsoport nevét.
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

5. A **Verzió** résznél válassza a **V2** értéket.
6. A **Hely csoportban**válassza ki az adat-előállító helyét. Csak a támogatott helyek jelennek meg a listában. Az adattárak (például az Azure Storage és az Azure SQL Database) és a számítási (például Az Azure HDInsight), hogy az adat-gyári felhasználású lehet más helyeken és régiókban.
8. Kattintson a **Létrehozás** gombra.
9. Az adat-előállító létrehozása után megjelenik az adatgyár kezdőlapja.
10. Ha az Azure Data Factory felhasználói felületét (UI) egy külön lapon szeretné megnyitni, válassza a **Szerzői & figyelés** csempét:

    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. Az Adatok másolása eszköz megnyitásához válassza az **Adatok másolása** csempét a **Let's started** (Az első lépések) lapon:

   ![Adatok másolása csempe](./media/doc-common-process/get-started-page.png)

2. A **Tulajdonságok** lapon tegye a következő lépéseket:

    a. A **Feladat neve mezőbe**írja be a **DeltaCopyFromBlobPipeline parancsot.**

    b. A **Tevékenység lépésszám vagy a Tevékenység ütemezése csoportban**válassza **a Futtatás rendszeresen ütemezés szerint**lehetőséget.

    c. Az **Eseményindító típusa csoportban**válassza a **Tumbling ablak lehetőséget.**

    d. Az **Ismétlődés**mezőbe írja be **a 15 perc(ek)** értéket.

    e. Kattintson a **Tovább** gombra.

    A Data Factory létrehoz egy folyamatot a megadott feladatnévvel.

    ![Adatok tulajdonságainak másolása lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. A **Forrás adattár** lapján hajtsa végre az alábbi lépéseket:

    a. Kapcsolat **hozzáadásához** válassza az Új kapcsolat létrehozása lehetőséget.

    b. Válassza az **Azure Blob Storage elemet** a gyűjteményből, majd válassza a **Folytatás**lehetőséget:

    ![Az Azure Blog storage kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Az **új csatolt szolgáltatás (Azure Blob Storage)** lapon válassza ki a tárfiókot a **Storage-fiók** névlistájából. Tesztelje a kapcsolatot, majd válassza a **Létrehozás lehetőséget.**

    d. Jelölje ki az új csatolt szolgáltatást, majd válassza a **Tovább**gombot:

   ![Az új csatolt szolgáltatás kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. Tallózással **source** keresse meg és jelölje ki a forrásmappát, majd válassza a **Választás gombot.**

    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. A **Fájlbetöltési viselkedés csoportban**válassza a **Növekményes terhelés: LastModifiedDate**lehetőséget.

    c. Válassza **a Bináris másolás,** majd a **Tovább**lehetőséget:

     ![A bemeneti fájl vagy mappalap kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. A **Cél adattár** lapon válassza ki a létrehozott **AzureBlobStorage-szolgáltatást.** Ez ugyanaz a tárfiók, mint a forrás-adattár. Ezután válassza a **Tovább**gombot.

6. **A kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. Tallózással **destination** keresse meg és jelölje ki a célmappát, majd válassza **a Választás lehetőséget:**

    ![A kimeneti fájl vagy mappalap kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Kattintson a **Tovább** gombra.

7. A **Beállítások** lapon kattintson a **Tovább** gombra.

8. Az **Összegzés** lapon tekintse át a beállításokat, majd kattintson a **Tovább gombra.**

    ![Összefoglaló lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. Az alkalmazás átvált a **Monitor** lapra. Láthatja a folyamat állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra. Válassza ki a kapcsolatot a **FOLYAMAT NEVE** csoportban a tevékenységfuttatás részleteinek megtekintéséhez vagy a folyamat ismételt futtatásához.

    ![A lista frissítése és a tevékenységfuttatás részleteinek megtekintése](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Csak egy tevékenység (a másolási tevékenység) van a folyamatban, így csak egy bejegyzés jelenik meg. A másolási művelettel kapcsolatos részletekért válassza a **Részletek** hivatkozást (a szemüveg ikont) a **ACTIVITY NAME** oszlopban. A tulajdonságokkal kapcsolatos részletekért olvassa el a [Tevékenység másolása – áttekintés című témakört.](copy-activity-overview.md)

    ![Tevékenység másolása a folyamatban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Mivel a Blob storage-fiókban nincsenek fájlok a forrástárolóban, a fiókban nem fog látni a céltárolóba másolt fájlokat:

    ![Nincs fájl a forrástárolóban vagy a céltárolóban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Hozzon létre egy üres szövegfájlt, és nevezze el **fájl1.txt**. Töltse fel ezt a szövegfájlt a tárfiók forrástárolójába. A feladatok, például az [Azure Storage Explorer](https://storageexplorer.com/)különböző eszközökkel hajthatja végre a feladatokat.

    ![Fájl létrehozása1.txt fájl létrehozása és feltöltése a forrástárolóba](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Ha vissza szeretne lépni a **folyamat futtatási** nézetéhez, válassza a **Minden folyamatfuttatás**lehetőséget, és várja meg, amíg ugyanaz a folyamat automatikusan újra aktiválódik.  

    ![Az összes folyamatfuttatás kijelölése](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Amikor a második folyamat futtatása befejeződik, kövesse a korábban említett lépéseket a tevékenység futtatási részleteinek áttekintéséhez.  

    Látni fogja, hogy egy fájl (file1.txt) lett másolva a forrástárolóból a Blob storage-fiók céltárolójába:

    ![a fájl1.txt fájl a forrástárolóból a céltárolóba lett másolva](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Hozzon létre egy másik üres szövegfájlt, és nevezze el **file2.txt**. Töltse fel ezt a szövegfájlt a Blob storage-fiók forrástárolójába.

16. Ismételje meg a 13. Látni fogja, hogy csak az új fájlt (file2.txt) másolta a forrástárolóból a tárfiók céltárolójába a folyamat futtatása során.  

    Azt is ellenőrizheti, hogy csak egy fájlt másolt-e az [Azure Storage Explorer](https://storageexplorer.com/) segítségével a fájlok beolvasása:

    ![Fájlok bekése az Azure Storage Explorer használatával](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>További lépések
Az alábbi oktatóanyagból megtudhatja, hogyan alakíthatja át az adatokat egy Apache Spark-fürt használatával az Azure-ban:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben egy Apache Spark-fürt használatával](tutorial-transform-data-spark-portal.md)
