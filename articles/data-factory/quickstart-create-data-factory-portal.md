---
title: "Azure-beli adat-előállító létrehozása az Azure Data Factory felhasználói felületén | Microsoft Docs"
description: "Ez az oktatóanyag ismerteti, hogyan hozhat létre adat-előállítókat olyan folyamatokkal, amelyek adatokat másolnak az egyik mappából a másikba az Azure Blob Storage-ban."
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
ms.openlocfilehash: ebce4e0d137d2e56cc914efad357593af7abb255
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-data-factory-using-the-azure-data-factory-ui"></a>Adat-előállító létrehozása az Azure Data Factory felhasználói felületén
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [2. verzió – Előzetes verzió](quickstart-create-data-factory-portal.md)

Ez a rövid útmutató ismerteti, hogyan használható az Azure Data Factory felhasználói felülete egy adat-előállító létrehozásához és monitorozásához. Az adat-előállítóban létrehozott folyamat adatokat **másol** egy Azure-blobtároló egyik mappájából egy másikba. Az adatok Azure Data Factoryval történő **átalakításának** útmutatásáért olvassa el az [az adatok Spark segítségével történő átalakítását ismertető oktatóanyagot](tutorial-transform-data-spark-portal.md). 


> [!NOTE]
> Ha még csak ismerkedik az Azure Data Factory szolgáltatással, a gyors útmutató elvégzése előtt tekintse meg a következő cikket: [Bevezetés az Azure Data Factory használatába](data-factory-introduction.md). 
>
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory általánosan elérhető 1. verzióját használja, lásd: [A Data Factory 1. verziójának oktatóanyaga](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Videó 
A következő videó megtekintése segíthet az Azure Data Factory felhasználói felületének megismerésében: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Lépjen az [Azure Portalra](https://portal.azure.com). 
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/quickstart-create-data-factory-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//quickstart-create-data-factory-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/quickstart-create-data-factory-portal/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felület (UI) alkalmazás külön lapon történő elindításához kattintson az **Létrehozás és monitorozás** csempére. 
11. Az első lépéseket bemutató lapon váltson az **Szerkesztés** lapra a bal oldali panelen, ahogy az alábbi képen látható: 

    ![Első lépések lap](./media/quickstart-create-data-factory-portal/get-started-page.png)

## <a name="create-azure-storage-linked-service"></a>Azure Storage társított szolgáltatás létrehozása
Ebben a lépésben létrehoz egy társított szolgáltatást, hogy az Azure Storage-fiókot az adat-előállítóhoz kapcsolja. A társított szolgáltatás azon kapcsolatadatokkal rendelkezik, amelyeket a Data Factory szolgáltatás használ futtatáskor a hozzá való kapcsolódáshoz.

2. Kattintson a **Kapcsolatok** elemre, majd az eszköztáron kattintson az **Új** elemre. 

    ![Új kapcsolat](./media/quickstart-create-data-factory-portal/new-connection-button.png)    
3. Az **Új társított szolgáltatás** oldalon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Folytatás** gombra. 

    ![Az Azure Storage kiválasztása](./media/quickstart-create-data-factory-portal/select-azure-storage.png)
4. Az **Új társított szolgáltatás** oldalon végezze el az alábbi lépéseket: 

    1. A **Név** mezőbe írja az **AzureStorageLinkedService** nevet.
    2. A **Storage-fiók neve** mezőben válassza ki saját Azure Storage-fiókját.
    3. Kattintson a **Kapcsolat tesztelése** elemre annak ellenőrzéséhez, hogy a Data Factory szolgáltatás kapcsolódik-e a tárfiókhoz. 
    4. A társított szolgáltatás mentéséhez kattintson a **Mentés** gombra. 

        ![Az Azure Storage társított szolgáltatásának beállításai](./media/quickstart-create-data-factory-portal/azure-storage-linked-service.png) 
5. Győződjön meg róla, hogy az **AzureStorageLinkedService** szerepel-e a társított szolgáltatások listájában. 

    ![Az Azure Storage társított szolgáltatása a listában](./media/quickstart-create-data-factory-portal/azure-storage-linked-service-in-list.png)

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben két adatkészletet hoz létre, az **InputDataset** és az **OutputDataset** adatkészletet. Ezek az adatkészletek **AzureBlob** típusúak. Ezek az adatkészletek az előző lépésben létrehozott **Azure Storage-beli társított szolgáltatásra** vonatkoznak. 

A bemeneti adatkészlet a bemeneti mappában lévő forrásadatokat jelenti. A bemeneti adatkészlet definíciójában adhatja meg a forrásadatokat tartalmazó blobtároló (**adftutorial**), mappa (**input**) és fájl (**emp.txt**) nevét. 

A kimeneti adatkészlet a célhelyre másolt adatokat jelenti. A kimeneti adatkészlet definíciójában adhatja meg annak a blobtárolónak (**adftutorial**), mappának (**output**) és fájlnak a nevét, amelybe az adatok át lesznek másolva. Minden egyes folyamathoz egyedi azonosító tartozik, amely a **RunId** rendszerváltozó használatával érhető el. A rendszer a folyamatfuttatási azonosító alapján dinamikusan kiértékeli a kimeneti fájl nevét.   

A társított szolgáltatás beállításainál megadta a forrásadatokat tartalmazó Azure Storage-fiókot. A forrásadatkészlet beállításainál megadja a forrásadatok pontos helyét (blobtároló, mappa és fájl). A fogadó adatkészlet beállításainál megadja az adatok másolásának célhelyét (blobtároló, mappa és fájl). 
 
1. Kattintson a **+ (plusz)** gombra, és válassza az **Adatkészlet** lehetőséget.

    ![Új adatkészlet menü](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)
2. Az **Új adatkészlet** oldalon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Befejezés** gombra. 

    ![Select Azure Blob Storage](./media/quickstart-create-data-factory-portal/select-azure-blob-storage.png)
3. Az adatkészlet **Tulajdonságok** ablakában adja meg az **InputDataset** értéket a **Név** mezőben. 

    ![Adatkészlet általános beállításai](./media/quickstart-create-data-factory-portal/dataset-general-page.png)
4. Váltson a **Kapcsolat** lapra, és végezze el az alábbi lépéseket: 

    1. Válassza az **AzureStorageLinkedService** elemet a társított szolgáltatáshoz. 
    2. Kattintson a **Fájl elérési útja** mező mellett a **Tallózás** gombra. 
        ![A bemeneti fájl megkeresése](./media/quickstart-create-data-factory-portal/file-path-browse-button.png)
    3. A **Fájl vagy mappa kiválasztása** ablakban keresse meg az **input** mappát az **adftutorial** tárolóban, válassza ki az **emp.txt** fájlt, és kattintson a **Befejezés** elemre.

        ![A bemeneti fájl megkeresése](./media/quickstart-create-data-factory-portal/choose-file-folder.png)
    4. (nem kötelező) Az emp.txt fájlban lévő adatok előnézetének megtekintéséhez kattintson az **Adatok villámnézete** elemre.     
5. A kimeneti adatkészlet létrehozásához ismételje meg ezeket a lépéseket.  

    1. Kattintson a **+ (plusz)** gombra a bal oldali panelen, és válassza a **Adatkészlet** lehetőséget.
    2. Az **Új adatkészlet** oldalon válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Befejezés** gombra.
    3. Adja meg az **OutputDataset** nevet.
    4. Adja meg az **adftutorial/output** mappát. A másolási tevékenység létrehozza a kimeneti mappát, ha még nem létezik. 
    5. Adja meg a következő fájlnevet: `@CONCAT(pipeline().RunId, '.txt')`. Minden folyamatfuttatáshoz egyedi azonosító tartozik. Ez a kifejezés összefűzi a folyamat azonosítóját a **.txt** kiterjesztéssel a kimeneti fájl nevének kiértékeléséhez. A támogatott rendszerváltozók és kifejezések listáját lásd a [rendszerváltozókat](control-flow-system-variables.md) és a [kifejezésnyelveket](control-flow-expression-language-functions.md) ismertető cikkekben.

        ![Kimeneti adatkészlet beállításai](./media/quickstart-create-data-factory-portal/output-dataset-settings.png)

## <a name="create-a-pipeline"></a>Folyamat létrehozása 
Ebben a lépésben létrehozza és megerősíti azt a **másolási** tevékenységgel rendelkező folyamatot, amely a bemeneti és a kimeneti adatkészleteket használja. A másolási tevékenység adatokat másol a bemeneti adatkészlet beállításaiban megadott fájlból a kimeneti adatkészlet beállításaiban megadott fájlba. Ha a bemeneti adatkészletben csak egy mappát ad meg (de fájlnevet nem), a másolási tevékenység a forrásmappában található összes fájlt átmásolja a célhelyre. 

1. Kattintson a **+ (plusz)** gombra, és válassza a **Folyamat** lehetőséget. 

    ![Új folyamat menü](./media/quickstart-create-data-factory-portal/new-pipeline-menu.png)
2. A **Tulajdonságok** ablakban a **CopyPipeline** értéket adja meg a **Név** mezőben. 

    ![Folyamat általános beállításai](./media/quickstart-create-data-factory-portal/pipeline-general-settings.png)
3. A **Tevékenységek** eszközkészletben bontsa ki az **Adatfolyam** elemet, és húzza át a **Másolás** tevékenységet a **Tevékenységek** eszközkészletből a folyamat tervezőfelületére. A **Tevékenységek** eszközkészletben kereshet is az egyes tevékenységek között. Adja meg a **CopyFromBlobToBlob** **nevet**.

    ![Másolási tevékenység általános beállításai](./media/quickstart-create-data-factory-portal/copy-activity-general-settings.png)
4. Váltson a **Forrás** lapra a másolási tevékenység beállításainál, és válassza az **InputDataset** lehetőséget a **Forrás adatkészletnél**.

    ![Másolási tevékenység – forrásbeállítások](./media/quickstart-create-data-factory-portal/copy-activity-source-settings.png)    
5. Váltson a **Fogadó** lapra a másolási tevékenység beállításainál, és válassza az **OutputDataset** lehetőséget a **Fogadó adatkészlet** mezőben.

    ![Másolási tevékenység – fogadóbeállítások](./media/quickstart-create-data-factory-portal/copy-activity-sink-settings.png)    
7. A folyamatbeállítások érvényesítéséhez kattintson az **Érvényesítés** elemre. Győződjön meg róla, hogy a folyamat érvényesítése sikerült. Az érvényesítés kimenetének bezárásához kattintson a **jobbra mutató nyíl** (>>) gombra. 

    ![A folyamat érvényesítése](./media/quickstart-create-data-factory-portal/pipeline-validate-button.png)

## <a name="test-run-the-pipeline"></a>A folyamat próbafuttatása
Ebben a lépésben elvégzi a folyamat tesztfuttatását a Data Factoryban történő üzembe helyezés előtt. 

1. A folyamat eszköztárán kattintson a **Tesztfuttatás** elemre. 
    
    ![Folyamat tesztfuttatásai](./media/quickstart-create-data-factory-portal/pipeline-test-run.png)
2. Győződjön meg róla, hogy látja a folyamatfuttatás állapotát a folyamat beállításainak **Kimenet** lapján. 

    ![A tesztfuttatás kimenete](./media/quickstart-create-data-factory-portal/test-run-output.png)    
3. Győződjön meg róla, hogy a kimeneti fájl látható az **adftutorial** tároló **output** mappájában. Ha a kimeneti mappa nem létezik, a Data Factory szolgáltatás automatikusan létrehozza. 
    
    ![Kimenet ellenőrzése](./media/quickstart-create-data-factory-portal/verify-output.png)


## <a name="trigger-the-pipeline-manually"></a>A folyamat manuális aktiválása
Ebben a lépésben entitásokat (társított szolgáltatásokat, adatkészleteket és folyamatokat) helyez üzembe az Azure Data Factoryban. Ezután manuálisan fogja aktiválni a folyamat futtatását. Egy [másik oktatóanyagban](tutorial-copy-data-portal.md?#configure-code-repository) leírt módon közzé is teheti ezeket az entitásokat saját VSTS GIT-adattárában.

1. A folyamat aktiválása előtt közzé kell tennie az entitásokat a Data Factory számára. A közzétételhez a bal oldali panelen kattintson a **Közzététel** elemre. 

    ![Közzététel gomb](./media/quickstart-create-data-factory-portal/publish-button.png)
2. A folyamat manuális aktiválásához kattintson az **Aktiválás** elemre az eszköztáron, majd válassza az **Aktiválás most** lehetőséget. 
    
    ![Aktiválás most](./media/quickstart-create-data-factory-portal/pipeline-trigger-now.png)

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre.

    ![A folyamat figyelése](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
2. Kattintson az **Műveletek** területen található **Tevékenységfuttatások megtekintése** hivatkozásra. Ezen az oldalon látható a másolási tevékenység futtatási állapota. 

    ![Folyamattevékenység-futtatások](./media/quickstart-create-data-factory-portal/pipeline-activity-runs.png)
3. A másolási művelet részleteinek megtekintéséhez kattintson a **Műveletek** oszlop **Részletek** hivatkozására (szemüveg ikon). A tulajdonságokkal kapcsolatos részletekért tekintse meg a [másolási tevékenység áttekintését](copy-activity-overview.md). 

    ![A másolási művelet részletei](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
4. Győződjön meg arról, hogy megjelenik egy új fájl az **output** mappában. 
5. A **Folyamatok** hivatkozásra kattintva visszaválthat a **Tevékenységfuttatások** nézetről a **Folyamatfuttatások** nézetre. 

## <a name="trigger-the-pipeline-on-a-schedule"></a>A folyamat aktiválása ütemezés szerint
Az oktatóanyagnak ez a lépése nem kötelező. A folyamat rendszeres időközönként (például óránként, naponta) történő futtatásához létrehozhat egy **ütemező eseményindítót**. Ebben a lépésben egy olyan eseményindítót fog létrehozni, amely a datetime formátumban megadott záró dátumig percenként fut. 

1. Váltson a **Szerkesztés** lapra. 

    ![Váltás a Szerkesztés lapra](./media/quickstart-create-data-factory-portal/switch-edit-tab.png)
1. Kattintson a menü **Aktiválás** pontjára, majd az **Új/Szerkesztés** elemre. 

    ![Új eseményindító menü](./media/quickstart-create-data-factory-portal/new-trigger-menu.png)
2. Az **Eseményindítók hozzáadása** oldalon kattintson az **Eseményindító kiválasztása**, majd pedig az **Új** elemre. 

    ![Eseményindítók hozzáadása – új eseményindító](./media/quickstart-create-data-factory-portal/add-trigger-new-button.png)
3. Az **Új eseményindító** oldal **Befejezés** mezőjében válassza a **Dátumon** lehetőséget, állítsa be a befejezés időpontját néhány perccel az aktuális időpont utánra, majd kattintson az **Alkalmaz** elemre. Minden egyes folyamatfuttatásnak van bizonyos költségvonzata, ezért a befejezés időpontját csak néhány perccel a kezdés időpontja utánra állítsa be. Győződjön meg arról, hogy a két időponthoz tartozó dátum megegyezik. Arról is győződjön meg, hogy elegendő idő áll rendelkezésre a folyamat futtatásához a közzététel időpontja és a befejezés időpontja között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem. 

    ![Eseményindító-beállítások](./media/quickstart-create-data-factory-portal/trigger-settings.png)
4. Jelölje be az **Aktiválva** beállítást a **Új eseményindító** oldalon, majd kattintson a **Tovább** gombra 

    ![Eseményindító-beállítások – Tovább gomb](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
5. Az **Új eseményindító** oldalon tekintse át a figyelmeztető üzenetet, majd kattintson a **Befejezés** gombra.

    ![Eseményindító-beállítások – Befejezés gomb](./media/quickstart-create-data-factory-portal/new-trigger-finish.png)
6. Kattintson a **Közzététel** elemre a módosítások Data Factoryban való közzétételéhez. 

    ![Közzététel gomb](./media/quickstart-create-data-factory-portal/publish-2.png)
8. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre. Láthatja, hogy a folyamat percenként fut a közzététel időpontja és a befejezés időpontja között. Figyelje meg az **Aktiválva a következő alapján** oszlop értékeit. A manuális eseményindító-futtatás egy korábban elvégzett lépésből (**Aktiválás most**) származik. 

    ![Aktivált futtatások monitorozása](./media/quickstart-create-data-factory-portal/monitor-triggered-runs.png)
9. Kattintson a **Folyamatfuttatások** melletti lefelé mutató nyílra a **Eseményindító-futtatások** nézetre való átváltáshoz. 

    ![Eseményindító-futtatások monitorozása](./media/quickstart-create-data-factory-portal/monitor-trigger-runs.png)    
10. Győződjön meg arról, hogy a datetime formátumban megadott befejezési időpontig futtatott minden folyamatfuttatáshoz létrejött egy **kimeneti fájl** az **output** mappában. 

## <a name="next-steps"></a>További lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-portal.md). 