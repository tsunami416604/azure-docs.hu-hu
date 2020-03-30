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
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131099"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Új és módosított fájlok fokozatos másolása a LastModifiedDate alapján az Adatok másolása eszközzel

Ebben az oktatóanyagban az Azure Portal használatával hozzon létre egy adat-előállító. Ezután az Adatok másolása eszközzel hozzon létre egy folyamatot, amely csak az új és a módosított fájlokat másolja, az Azure Blob storage-ból az Azure Blob storage-ból az Azure Blob storage-ba történő **LastModifiedDate** alapján.

Ezzel az ADF átkezi a forrástárolóösszes fájlját, alkalmazza a fájlszűrőt a LastModifiedDate segítségével, és az új és frissített fájlt csak a legutóbbi alkalom óta másolja a céltárolóba.  Kérjük, vegye figyelembe, hogy ha hagyja, hogy az ADF hatalmas mennyiségű fájlt szítson, de csak néhány fájlt másoljon a rendeltetési helyre, akkor is elvárná, hogy a fájlvizsgálat miatt a hosszú időtartam is időigényes.   

> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Folyamat létrehozása az Adatok másolása eszközzel.
> * A folyamat és a tevékenységek futásának monitorozása

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**: Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* **Azure storage-fiók:** Használja blob storage _forrásként_ és _fogadó_ adattárként. Ha még nem rendelkezik Azure Storage-fiókkal, a szükséges utasításokat a [Storage-fiók létrehozását](../storage/common/storage-account-create.md) ismertető cikkben találja.

### <a name="create-two-containers-in-blob-storage"></a>Hozzon létre két tárolót a Blob storage-ban

Készítse elő a Blob storage az oktatóanyag ezekkel a lépésekkel.

1. Hozzon létre egy **forrás**nevű tárolót. A feladat végrehajtásához különböző eszközöket használhat, például [az Azure Storage Explorert.](https://storageexplorer.com/)

2. **Hozzon**létre egy cél nevű tárolót.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. A bal oldali menüben válassza az > **Erőforrás-adatok létrehozása + Analytics** > **Data Factory** **lehetőséget:**

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az adat-előállító nevének _globálisan egyedinek_ kell lennie. A következő hibaüzenet jelenhet meg:

   ![Új adat-előállító hibaüzenete](./media/doc-common-process/name-not-available-error.png)

   Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. Például: _**sajátneve**_**ADFTutorialDataFactory**. A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
3. Válassza ki azt az **Azure-előfizetést,** amelyben létre fogja hozni az új adatgyárat.
4. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    * Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot.

    * Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket.

5. A **verzió**csoportban válassza a **V2**lehetőséget.
6. A **Hely** alatt válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Az adattárak (például az Azure Storage és az SQL Database) és a számítási (például az Azure HDInsight), hogy az adat-gyári felhasználású lehet más helyeken és régiókban.
8. Kattintson a **Létrehozás** gombra.
9. A létrehozás befejezése után a **Data Factory** kezdőlapja jelenik meg.
10. Ha az Azure Data Factory felhasználói felületét (UI) egy külön lapon szeretné megnyitni, válassza a **Szerzői & figyelés** csempét.

    ![Data factory kezdőlap](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Folyamat létrehozása az Adatok másolása eszközzel

1. A **Let's get started** (Az adatok másolása) lapon jelölje be az **Adatok másolása** címet az Adatok másolása eszköz megnyitásához.

   ![Az Adatok másolása eszköz csempéje](./media/doc-common-process/get-started-page.png)

2. A **Tulajdonságok** lapon tegye a következő lépéseket:

    a. A **Feladat neve mezőbe**írja be a **DeltaCopyFromBlobPipeline parancsot.**

    b. A **Tevékenység lépésszám** vagy **a Tevékenység ütemezése csoportban**válassza **a Futtatás rendszeresen ütemezés szerint**lehetőséget.

    c. Az **Eseményindító típusa csoportban**válassza **a Bukdácsoló ablak lehetőséget.**

    d. Az **Ismétlődés**mezőbe írja be **a 15 perc(ek)** értéket.

    e. Válassza a **Tovább lehetőséget.**

    A Data Factory felhasználói felülete létrehoz egy folyamatot a megadott feladatnéven.

    ![Tulajdonságok lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. A **Forrásadattár** oldalon hajtsa végre az alábbi lépéseket:

    a. Kapcsolat hozzáadásához válassza **a + Új kapcsolat létrehozása**lehetőséget.

    b. Válassza az **Azure Blob Storage** elemet a gyűjteményből, majd kattintson a Folytatás **gombra.**

    ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Az **új csatolt szolgáltatás (Azure Blob Storage)** lapon válassza ki a tárfiókot a **Storage-fiók** névlistájából. Tesztelje a kapcsolatot, és válassza a **Létrehozás lehetőséget.**

    d. Jelölje ki az újonnan létrehozott csatolt szolgáltatást, majd kattintson a **Tovább gombra.**

   ![Forrásadattár lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. **A bemeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. Tallózással **source** és jelölje ki a forrásmappát, majd válassza **a Választás gombot.**

    ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. A **Fájlbetöltési viselkedés csoportban**válassza a **Növekményes terhelés: LastModifiedDate**lehetőséget.

    c. Jelölje be **a Bináris másolás jelölőnégyzetet,** és válassza a **Tovább**gombot.

     ![A bemeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. A **Cél adattár** lapon válassza ki a létrehozott **AzureBlobStorage.On** the Destination data storage page, select the AzureBlobStorage that you created. Ez ugyanaz a tárfiók, mint a forrás-adattár. Ezután válassza a **Tovább**gombot.

6. **A kimeneti fájl vagy mappa kiválasztása** lapon kövesse az alábbi lépéseket:

    a. Tallózással **destination** és kijelöléssel a célmappában, majd válassza **a Választás gombot.**

    ![Kimeneti fájl vagy mappa kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Válassza a **Tovább lehetőséget.**

7. A **Beállítások** lapon kattintson a **Tovább** gombra.

8. Az **Összegzés** lapon tekintse át a beállításokat, majd kattintson a **Tovább gombra.**

    ![Összefoglaló lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. A folyamat (feladat) figyeléséhez az **Üzembe helyezés** lapon kattintson a **Monitorozás** elemre.

    ![Üzembe helyezés lap](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Figyelje meg, hogy a bal oldalon található **Figyelés** lap automatikusan ki lesz választva. Az alkalmazás átvált a **Monitor** lapra. Láthatja a folyamat állapotát. A lista frissítéséhez kattintson a **Frissítés** gombra. Kattintson a **FOLYAMAT NEVE** csoporthivatkozásra a tevékenységfuttatás részleteinek megtekintéséhez vagy a folyamat újbóli futtatásához. 

    ![Lista frissítése és a Tevékenységfuttatások megtekintése jelölőnégyzet kiválasztása](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Csak egy tevékenység (a másolási tevékenység) van a folyamatban, így csak egy bejegyzés jelenik meg. A másolási művelettel kapcsolatos részletekért válassza a **Részletek** hivatkozást (szemüveg ikon) a **ACTIVITY NAME** oszlopban. A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

    ![A másolási tevékenység folyamatban van](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Mivel nincs fájl a **forrástárolóban** a Blob storage-fiókban, nem fog látni a blob storage-fiók **céltárolójába** másolt fájlokat.

    ![Nincs fájl a forrástárolóban vagy a céltárolóban](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Hozzon létre egy üres szövegfájlt, és nevezze el **fájl1.txt**. Töltse fel ezt **source** a szövegfájlt a tárfiók forrástárolójába. Ennek elvégzéséhez különböző eszközök állnak rendelkezésére, például az [Azure Storage Explorer](https://storageexplorer.com/).

    ![Fájl létrehozása1.txt fájl létrehozása és feltöltés a forrástárolóba](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Ha vissza szeretne lépni a **Folyamatfuttatások** nézetbe, válassza a **Minden folyamatfuttatás**lehetőséget, és várja meg, amíg ugyanaz a folyamat automatikusan újra aktiválódik.  

    ![Az összes folyamatfuttatás kijelölése](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Amikor a második folyamat futtatása befejeződik, kövesse a fent említett lépéseket a tevékenység futtatási részleteinek áttekintéséhez.  

    Látni fogja, hogy egy fájl (file1.txt) lett másolva a **forrástárolóból** a Blob storage-fiók **céltárolójába.**

    ![A fájl1.txt fájl átlett másolva a forrástárolóból a céltárolóba](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Hozzon létre egy másik üres szövegfájlt, és nevezze el **file2.txt**. Töltse fel ezt **source** a szövegfájlt a Blob storage-fiók forrástárolójába.

16. Ismételje meg a 13. Látni fogja, hogy csak az új fájl (file2.txt) lett másolva a **forrástárolóból** a tárfiók **céltárolójába** a következő folyamat futtatásakor.  

    Ezt is ellenőrizheti az [Azure Storage Explorer](https://storageexplorer.com/) használatával a fájlok beolvasása.

    ![Fájlok betárolása az Azure Storage Explorer használatával](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>További lépések
Az alábbi oktatóanyagra az Azure-beli Apache Spark-fürt használatával az adatok átalakításáról olvashat:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben egy Apache Spark-fürt használatával](tutorial-transform-data-spark-portal.md)
