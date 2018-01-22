---
title: "Adatok átalakítása a Spark segítségével az Azure Data Factoryban | Microsoft Docs"
description: "Ez az oktatóanyag lépésenkénti utasításokat biztosít az adatok átalakításához egy Spark-tevékenység az Azure Data Factoryban való használatával."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/10/2018
ms.author: shengc
ms.openlocfilehash: 8bd9382ed5a855368533c6bf2305682861c109c0
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Adatátalakítás a felhőben egy Spark-tevékenység az Azure Data Factoryban való használatával
Ebben az oktatóanyagban az Azure Portal segítségével hozhat létre egy Data Factory-folyamatot, amely egy Spark-tevékenységgel és egy igény szerinti HDInsight társított szolgáltatással alakítja át az adatokat. Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Folyamat létrehozása egy Spark-tevékenységgel
> * Folyamat futtatásának aktiválása
> * A folyamat futásának monitorozása

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure Storage-fiók** Létrehoz egy Python-szkriptet és egy bemeneti fájlt, és feltölti őket az Azure Storage-ba. A Spark-program kimenetét ebben a tárfiókban tárolja a rendszer. Az igény szerinti Spark-fürt ugyanezt a tárfiókot használja elsődleges tárterületként.  
* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat.


### <a name="upload-python-script-to-your-blob-storage-account"></a>Python-szkript feltöltése a Blob Storage-fiókba
1. Hozzon létre egy **WordCount_Spark.py** nevű Python-fájlt az alábbi tartalommal: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Cserélje a **&lt;storageAccountName&gt;** kifejezést Azure Storage-fiókja nevére. Ezután mentse a fájlt. 
3. Az Azure Blob Storage-ban hozzon létre egy **adftutorial** nevű tárolót, ha még nem létezik. 
4. Hozzon létre egy **spark** mappát.
5. Hozzon létre egy **szkript** almappát a **spark** mappában. 
6. Töltse fel a **WordCount_Spark.py** fájlt a **szkript** almappába. 


### <a name="upload-the-input-file"></a>A bemeneti fájl feltöltése
1. Hozzon létre egy **minecraftstory.txt** nevű fájlt némi szöveges tartalommal. A Spark-program megszámolja a szavak számát ebben a szövegben. 
2. Hozzon létre egy `inputfiles` nevű almappát a `spark` mappában. 
3. Töltse fel a `minecraftstory.txt` fájlt az `inputfiles` almappába. 

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
      A rövid útmutató egyes lépései azt feltételezik, hogy az **ADFTutorialResourceGroup** nevet adta az erőforráscsoportnak. Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//tutorial-transform-data-spark-portal/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)
10. A Data Factory felhasználóifelület-alkalmazás külön lapon való elindításához kattintson a **Létrehozás és figyelés** csempére.

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Ebben a szakaszban két társított szolgáltatást hoz létre: 
    
- Egy **Azure Storage-beli társított szolgáltatást**, amely egy Azure Storage-fiókot társít az adat-előállítóhoz. Ezt a tárterületet csak az igény szerinti HDInsight-fürt használja. Ez tartalmazza a végrehajtani kívánt Spark-szkriptet is. 
- Egy **igény szerinti HDInsight társított szolgáltatást**. Az Azure Data Factory automatikusan létrehoz egy HDInsight-fürtöt, futtatja a Spark-programot, majd törli a HDInsight-fürtöt, ha az egy előre megadott időtartamon keresztül inaktív. 

### <a name="create-an-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása

1. Az **első lépéseket bemutató** lapon váltson a **Szerkesztés** lapra a bal oldali panelen, ahogy az az alábbi képen látható: 

    ![Folyamat létrehozása csempe](./media/tutorial-transform-data-spark-portal/get-started-page.png)

2. Kattintson az ablak alján látható **Connections** (Kapcsolatok), majd a **+ New** (Új) elemre. 

    ![Új kapcsolat gomb](./media/tutorial-transform-data-spark-portal/new-connection.png)
3. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre. 

    ![Select Azure Blob Storage](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
4. Válassza ki **Azure Storage-fiókjának nevét**, és kattintson a **Save** (Mentés) gombra. 

    ![Blob Storage-fiók megadása](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Igény szerinti HDInsight társított szolgáltatás létrehozása

1. Kattintson ismét a **+ New** gombra egy további társított szolgáltatás létrehozásához. 
2. A **New Linked Service** (Új társított szolgáltatás) ablakban válassza az **Azure HDInsight** lehetőséget, majd kattintson a **Continue** (Folytatás) elemre. 

    ![Az Azure HDInsight kiválasztása](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
2. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket: 

    1. A **Name** (Név) mezőben adja meg a következőt: **AzureHDInsightLinkedService**.
    2. Győződjön meg arról, hogy az **On-demand HDInsight** (Igény szerinti HDInsight) van kiválasztva a **Type** (Típus) elemnél.
    3. Az **Azure Storage Linked Service** (Azure Storage társított szolgáltatás) mezőnél válassza az **AzureStorage1** lehetőséget. Ezt a társított szolgáltatást korábban hozta létre. Ha másik nevet használt, a mezőben azt adja meg. 
    4. Válasza a **spark** **fürttípust**.
    5. Adja meg az **annak az egyszerű szolgáltatásnak az azonosítóját**, amely rendelkezik a HDInsight-fürt létrehozásához szükséges engedéllyel. A szolgáltatásnévnek az előfizetés vagy a létrejövő fürtnek helyet adó erőforráscsoport Közreműködő szerepkörének tagjának kell lennie. Részletek: [Azure Active Directory-alkalmazás és -szolgáltatásnév létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md).
    6. Adja meg a **szolgáltatásnév kulcsát**. 
    7. A **Resource group** (Erőforráscsoport) beállításnál válassza ki az adat-előállító létrehozásához használt erőforráscsoportot. Ekkor a Spark-fürt létrejön ebben az erőforráscsoportban. 
    8. Bontsa ki az **OS type** (Operációs rendszer típusa) elemet.
    9. Adjon egy **nevet** a fürt **felhasználójának**. 
    10. Adja meg a felhasználónévhez tartozó **jelszót**. 
    11. Kattintson a **Save** (Mentés) gombra. 

        ![HDInsight társított szolgáltatás beállításai](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Az Azure HDInsightban korlátozott azon magok száma, amelyek az egyes támogatott Azure-régiókban felhasználhatók. Igény szerinti HDInsight társított szolgáltatás esetében a HDInsight-fürt ugyanazon a helyen jön létre, mint amit az Azure Storage elsődleges tárterületként használ. Ellenőrizze, hogy a magkvótája elegendő-e a fürt sikeres létrehozásához. További információk: [Fürtök beállítása a HDInsightban Hadoop, Spark, Kafka stb. használatával](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Folyamat létrehozása

2. Kattintson a + (plusz) jelre, majd a menüben a **Pipeline** (Folyamat) elemre.

    ![Új folyamat menü](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
3. Az **Activities** (Tevékenységek) eszközkészletben bontsa ki a **HDInsight** elemet, majd húzza a **Spark** tevékenységet az **Activities** eszközkészletből a folyamat tervezőfelületére. 

    ![A Spark-tevékenység áthúzása](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
4. A **Spark** tevékenység tulajdonságok ablakában végezze el az alábbi lépéseket: 

    1. Váltson a **HDI Cluster** (HDI-fürt) lapra.
    2. Válassza ki az előző lépésben létrehozott **AzureHDInsightLinkedService** elemet. 
        
    ![HDInsight társított szolgáltatás megadása](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
5. Váltson a **Script/Jar** (Szkript/Jar) lapra, és végezze el az alábbi lépéseket: 

    1. A **Job Linked Service** (Feladathoz társított szolgáltatás) mezőnél válassza az **AzureStorage1** lehetőséget.
    2. Kattintson a **Browse Storage** (Tallózás a tárolóban) elemre. 
    3. Keresse meg az **adftutorial/spark/script mappát**, válassza ki a **WordCount_Spark.py** fájlt, majd kattintson a **Finish** (Befejezés) gombra.      

    ![Spark-szkript megadása](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
6. A folyamat érvényesítéséhez kattintson a **Validation** (Érvényesítés) gombra az eszköztáron. Az érvényesítési ablak bezárásához kattintson a **jobbra mutató nyíl** (>>) gombra. 
    
    ![Érvényesítés gomb](./media/tutorial-transform-data-spark-portal/validate-button.png)
7. Kattintson a **Publish** (Közzététel) gombra. A Data Factory felhasználói felülete entitásokat (társított szolgáltatásokat és folyamatot) tesz közzé az Azure Data Factory szolgáltatásban. 

## <a name="trigger-a-pipeline-run"></a>Folyamat futtatásának aktiválása
Kattintson a **Trigger** (Aktiválás) gombra az eszköztáron, majd a **Trigger Now** (Aktiválás most) elemre. 

![Aktiválás most](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. Váltson a **Monitorozás** lapra. Ellenőrizze, hogy megjelenik-e a futó folyamat. Egy Spark-fürt létrehozása nagyjából 20 percet vesz igénybe. 

    ![Folyamatfuttatások monitorozása](./media/tutorial-transform-data-spark-portal/monitor-tab.png)
2. Rendszeres időközönként kattintson a **Refresh** (Frissítés) gombra a folyamat futási állapotának ellenőrzéséhez. 

    ![Folyamat futási állapota](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png)
1. A folyamat futásához kapcsolódó tevékenységfuttatások megtekintéséhez kattintson a **View Activity Runs** (Tevékenységfuttatások megtekintése) műveletre az Actions (Műveletek) oszlopban. A fenti **Pipelines** (Folyamatok) elemre kattintva visszaválthat a folyamatfuttatások nézetre.

    ![Tevékenységfuttatások nézet](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Kimenet ellenőrzése
Ellenőrizze, hogy a kimeneti fájl a spark/outputfiles/wordcount mappában jött-e létre. 

![Kimenet ellenőrzése](./media/tutorial-transform-data-spark-portal/verity-output.png)

A fájlban a bemeneti szövegfájl összes szavának szerepelnie kell, valamint annak is, hogy az adott szó hányszor szerepel a fájlban. Például: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>További lépések
A mintában szereplő folyamat egy Spark-tevékenységgel és egy igény szerinti HDInsight társított szolgáltatással alakítja át az adatokat. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adat-előállító létrehozása 
> * Folyamat létrehozása egy Spark-tevékenységgel
> * Folyamat futtatásának aktiválása
> * A folyamat futásának monitorozása.

A következő oktatóanyagra lépve megtudhatja, hogyan alakíthat át adatokat egy Hive szkriptnek a virtuális hálózatban lévő Azure HDInsight-fürtön való futtatásával. 

> [!div class="nextstepaction"]
> [Oktatóanyag: adatok átalakítása a Hive használatával egy Azure virtuális hálózatban](tutorial-transform-data-hive-virtual-network-portal.md).





