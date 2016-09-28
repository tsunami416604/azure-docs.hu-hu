<properties
    pageTitle="Spark-fürt létrehozása HDInsight Linux rendszerben, valamint Jupyterről származó Spark SQL használata interaktív elemzések elvégzéséhez | Microsoft Azure"
    description="Részletes útmutatók Apache Spark-fürt gyors létrehozásához a HDInsightban, majd a Jupyter notebookokból származó Spark SQL használata interaktív lekérdezések futtatására."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/25/2016"
    ms.author="nitinme"/>



# Első lépések: Apache Spark-fürt létrehozása HDInsight Linux rendszeren és interaktív lekérdezések futtatása Spark SQL használatával

Útmutató Apache Spark-fürt gyors létrehozásához a HDInsightban, majd [Jupyter](https://jupyter.org) notebook használatához Spark SQL interaktív lekérdezések futtatására a Spark-fürtön.

   ![Ismerkedés az Apache Spark szolgáltatással a HDInsightban](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Get started using Apache Spark in HDInsight tutorial. Steps illustrated: create a storage account; create a cluster; run Spark SQL statements")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**Előfeltételek:**

- **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Secure Shell- (SSH-) ügyfél**: A Linux, a Unix és az OS X rendszerek SSH-ügyfelet biztosítanak az `ssh` paranccsal. Windows rendszerek használata esetén a [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) használata javasolt.
    
- **Secure Shell- (SSH-) kulcsok (nem kötelező)**: A fürthöz való csatlakozáshoz használt SSH-fiókjának biztonságát jelszóval vagy nyilvános kulccsal biztosíthatja. Jelszó használatával gyorsan megkezdheti az alkalmazás használatát, fürt gyors létrehozásakor és tesztfeladatok futtatásakor válassza ezt a lehetőséget. A kulcs használata biztonságosabb, de további beállítást igényel. Ezt a lehetőséget éles fürt létrehozásakor érdemes használni. Ebben a cikkben a jelszavas módszert használjuk. SSH-kulcsok a HDInsighttal történő létrehozásához és használatához kapcsolódó utasításokat az alábbi cikkek tartalmazzák:

    -  Linux rendszerű számítógépről – [Az SSH és a Linux-alapú HDInsight (Hadoop) együttes használata Linux, Unix vagy OS X rendszerről](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  Windows rendszerű számítógépről – [Az SSH és a Linux-alapú HDInsight (Hadoop) együttes használata Windows rendszerről](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] Ez a cikk egy ARM-sablon segítségével hoz létre egy Spark-fürtöt, amely [Azure Storage-blobokat használ fürttárolóként](hdinsight-hadoop-use-blob-storage.md). Olyan Spark-fürt is létrehozható, amely az alapértelmezett tárolóként használt Azure Storage-blobok mellett az [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) szolgáltatást is használja további tárolóként. Útmutatás: [Create an HDInsight cluster with Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) (HDInsight-fürt létrehozása a Data Lake Store-ral).


## Spark-fürt létrehozása

Ebben a szakaszban 3.4-es verziójú (1.6.1-es Spark-verziójú) HDInsight-fürtöt hoz létre egy Azure ARM-sablon használatával. Információk a HDInsight-verziókról és azok SLA-iról: [HDInsight-összetevők verziószámozása](hdinsight-component-versioning.md). Egyéb fürtlétrehozási módszerek: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

1. Az alábbi képre kattintva megnyithat egy ARM-sablont az Azure portálon.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Az ARM-sablon a következő nyilvános blobtárolóban található: *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. A Parameters (Paraméterek) panelen adja meg a következőket:

    - **ClusterName**: Adjon nevet a létrehozandó Hadoop-fürtnek.
    - **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    - **SSH-felhasználónév és -jelszó**.
    
    Jegyezze fel ezeket az értékeket.  Ezekre az oktatóanyag későbbi részében szüksége lesz.

    > [AZURE.NOTE] Az SSH segítségével – parancssorból – távolról is hozzáférhet a HDInsight-fürthöz. Az itt használt felhasználónévvel és jelszóval csatlakozhat a fürthöz az SSH-n keresztül. Emellett az SSH-felhasználónévnek egyedinek kell lennie, mivel az hozza létre a felhasználói fiókokat az összes HDInsight-fürtcsomóponton. A következő felhasználóneveket a rendszer a fürt szolgáltatásai számára tartja fent, ezért SSH-felhasználónévként nem használhatók:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

    > Az SSH a HDInsighttal történő használatáról további információkat a következő cikkekben talál:

    > * [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Linux, Unix vagy OS X rendszerben](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Az SSH használata a HDInsight-ra épülő Linux-alapú Hadooppal Windows rendszerben](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. Kattintson az **OK** gombra a paraméterek mentéséhez.

4. A **Custom deployment** (Egyéni üzembe helyezés) panelen kattintson a **Resource group** (Erőforráscsoport) legördülő listára, majd a **New** (Új) lehetőségre egy új erőforráscsoport létrehozásához. Az erőforráscsoport egy olyan tároló, amely csoportosítja a fürtöt, a függő tárfiókot és egyéb kapcsolt erőforrásokat.

5. Kattintson a **Legal terms** (Jogi feltételek), majd a **Create** (Létrehozás) gombra.

6. Kattintson a ** Create** (Létrehozás) gombra. Ekkor egy új csempe jelenik meg Submitting deployment for Template deployment (Üzemelő példány elküldése sablon üzemelő példányhoz) A fürt és az SQL-adatbázis létrehozása nagyjából 20 percet vesz igénybe.



## Spark SQL-lekérdezések futtatása Jupyter notebook használatával

Ebben a szakaszban Jupyter notebookot fog használni Spark SQL-lekérdezések futtatásához a Spark-fürtön. A HDInsight Spark-fürtjei két kernelt biztosítanak a Jupyter notebookkal történő használathoz. Ezek a következők:

* **PySpark** (Pythonban írt alkalmazások esetén)
* **Spark** (Scalában írt alkalmazások esetén)

Ebben a cikkben a PySpark kernelt fogja használni. A PySpark kernel használatának előnyeiről részletesen [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) című cikkben olvashat. Az alábbiakban a PySpark kernel használatának néhány fontosabb előnye van kiemelve:

* Nem szükséges beállítani a Spark- és Hive-környezeteket. Ezek automatikusan be vannak állítva az Ön számára.
* Az SQL- vagy Hive-lekérdezések közvetlen, megelőző kódrészletek nélkül történő futtatásához olyan cellafunkciókat használhat mint például a `%%sql`.
* Az SQL- vagy Hive-lekérdezések kimenetének megjelenítése automatikusan történik.

### Jupyter notebook létrehozása PySpark kernellel 

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   

2. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    > [AZURE.NOTE] A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a __CLUSTERNAME__ elemet a fürt nevére:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Jupyter notebook létrehozása](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Create a new Jupyter notebook")

3. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.

    ![A notebook elnevezése](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")

4. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ennek az első lépése a jelen forgatókönyvhöz szükséges típusok importálása. Ehhez illessze be a következő kódrészletet a cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

        from pyspark.sql.types import *
        
    Minden alkalommal, amikor a Jupyterben feladatot futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezését követően ez a jel üres körre változik.

     ![A Jupyter notebook feladat állapota](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Status of a Jupyter notebook job")

4. Töltse be a mintaadatokat egy ideiglenes táblába. Spark-fürt HDInsightban történő létrehozásakor a **hvac.csv** mintaadatfájl a hozzá tartozó tárfiókba lesz átmásolva a következő helyre: **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Illessze be a következő kódpéldát egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. Ez a kódpélda az adatokat a **hvac** nevű ideiglenes táblába regisztrálja.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Mivel PySpark kernelt használ, most közvetlenül futtathat SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) című részben talál.
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. A feladat sikeres végrehajtását követően alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

    ![A lekérdezés eredményének táblázatos kimenete](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Table output of query result")

    Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

    ![A lekérdezés eredményének területgrafikonja](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Area graph of query result")


6. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.

##A fürt törlése

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## Lásd még:


* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](hdinsight-apache-spark-overview.md)

### Forgatókönyvek

* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](hdinsight-apache-spark-use-bi-tools.md)

* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](hdinsight-apache-spark-eventhub-streaming.md)

* [A webhelynapló elemzése a Spark on HDInsight használatával](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Az Application Insights telemetriai adatainak elemzése a Spark on HDInsight használatával](hdinsight-spark-analyze-application-insight-logs.md)

### Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](hdinsight-apache-spark-create-standalone-application.md)

* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](hdinsight-apache-spark-livy-rest-interface.md)

### Eszközök és bővítmények

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Zeppelin notebookok használata Spark-fürttel HDInsighton](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Külső csomagok használata Jupyter notebookokkal](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](hdinsight-apache-spark-resource-manager.md)

* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md



<!--HONumber=Sep16_HO4-->


