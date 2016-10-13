<properties
    pageTitle="Linux oktatóanyag: Ismerkedés a Hadoop és a Hive használatával | Microsoft Azure"
    description="Ez az oktatóanyag segítséget nyújt a Hadoop használatának megkezdésében a HDInsight rendszerében. Megtudhatja, hogyan építhet ki Linux-fürtöket, és hogyan kérdezhet le adatokat a Hive segítségével."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# Hadoop oktatóanyag: A Linux-alapú Hadoop használatának első lépései a HDInsightban

> [AZURE.SELECTOR]
- [Linux-alapú](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Windows-alapú](hdinsight-hadoop-tutorial-get-started-windows.md)

Megtanulhatja, hogyan hozhat létre Linux-alapú [Hadoop](http://hadoop.apache.org/)-fürtöket, és hogyan futtathat Hive-feladatokat a HDInsightban. Az [Apache Hive](https://hive.apache.org/) a Hadoop ökoszisztémájának legnépszerűbb összetevője. Jelenleg a HDInsight 4 különböző fürttípussal érhető el: [Hadoop](hdinsight-hadoop-introduction.md), [Spark](hdinsight-apache-spark-overview.md), [HBase](hdinsight-hbase-overview.md) és [Storm](hdinsight-storm-overview.md).  Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot mind a 4 fürttípus támogatja. A HDInsightban támogatott összetevők listáját lásd: [What's new in the Hadoop cluster versions provided by HDInsight?](hdinsight-component-versioning.md) (A HDInsight által biztosított Hadoop-fürtverziók újdonságai).  

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Előfeltételek

Az oktatóanyag elindításának feltétele:

- **Azure-előfizetés**: egy ingyenes egy hónapos próbafiók létrehozásához keresse fel az [azure.microsoft.com/free](https://azure.microsoft.com/free) webhelyet.

### A hozzáférés-vezérlésre vonatkozó követelmények

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## Fürt létrehozása

A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a szakaszban egy Linux-alapú Hadoop-fürtöt fog létrehozni a HDInsightban az [Azure Resource Manager-sablon](../resource-group-template-deploy.md) segítségével. A Resource Manager-sablon teljes mértékben testreszabható, így könnyen létrehozhatók vele olyan Azure-erőforrások, mint például a HDInsight. Nem kell a Resource Manager-sablonok használatára vonatkozó tapasztalattal rendelkeznie az oktatóanyag követéséhez. Egyéb fürtlétrehozási módszerekhez és az oktatóanyagban használt tulajdonságok megértéséhez tekintse meg a [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight-fürtök létrehozása) című témakört. A jelen oktatóanyagban használt Resource Manager-sablon a nyilvános blobtárolóban található: [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json). 

1. Az alábbi képre kattintva beléphet az Azure szolgáltatásba, és megnyithatja a Resource Manager-sablont az Azure Portalon. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. A **Parameters** (Paraméterek) panelen adja meg a következőket:

    ![HDInsight Linux első lépések – Resource Manager-sablon a portálon](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-arm-template-on-portal.png).

    - **ClusterName**: Adjon nevet a létrehozandó Hadoop-fürtnek.
    - **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az **admin**.
    - **SSH-felhasználónév és jelszó**: Az alapértelmezett felhasználónév az **sshuser**.  Ezt át lehet nevezni. 
    
    Más paramétereket az oktatóanyag folytatásához nem kötelező megadni. Hagyhatja őket úgy, ahogy vannak. 
    
    Minden egyes fürt az Azure Blob Storage-fióktól függ. Általában ez az alapértelmezett tárfiók. A HDInsight-fürtnek és az alapértelmezett tárfióknak ugyanabban az Azure-régióban kell lennie. A fürtök törlésével a tárfiók nem törlődik. A sablonban az alapértelmezett tárfiók neveként a fürt neve van megadva a „store” kifejezéssel kiegészítve. 
    
3. Kattintson az **OK** gombra a paraméterek mentéséhez.
4. A **Custom deployment** (Egyéni üzembe helyezés) panelen az **Új erőforráscsoport neve** megadásával hozzon létre egy új erőforráscsoportot.  Az erőforráscsoport egy olyan tároló, amely csoportosítja a fürtöt, a függő tárfiókot és egyéb elemeket. Az erőforráscsoport helye eltérhet a fürt helyétől.
5. Kattintson a **Legal terms** (Jogi feltételek), majd a **Create** (Létrehozás) gombra.
6. Ellenőrizze, hogy a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzet be van-e jelölve, majd kattintson a **Create** (Létrehozás) gombra. Egy új csempe jelenik meg **Deploying Template deployment** (Üzembe helyezés – Sablon telepítése) címmel. Egy fürt létrehozása nagyjából 20 percet vesz igénybe. 
7.  A fürt létrejötte után a csempe felirata átvált az Ön által megadott erőforráscsoport-névre. A portál automatikusan két külön panelben jeleníti meg a fürtöt és a fürt beállításait. 

    ![HDInsight Linux első lépései – fürtbeállítások](./media/hdinsight-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-cluster-settings.png).

    A felsorolásban két erőforrás szerepel, a fürt és az alapértelmezett tárfiók.

##Hive-lekérdezések futtatása

Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben az oktatóanyagban a portál Ambari Hive nézete segítségével fog Hive-feladatokat futtatni. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

1. Keresse fel a **https://&lt;ClusterName>.azurehdinsight.net** webhelyet, ahol a &lt;ClusterName> az a fürt, amelyet az előző szakaszban az Ambari megnyitásához hozott létre.
2. Adja meg az előző szakaszban létrehozott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.
3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:

    ![Az Ambari nézetek kiválasztása](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. A lap __Query Editor__ (Lekérdezésszerkesztő) szakaszában másolja be a következő HiveQL-kifejezést a munkalapra:

        SHOW TABLES;

    >[AZURE.NOTE] A pontosvessző megadása a Hive-nál kötelező.       
        
5. Kattintson az __Execute__ (Végrehajtás) parancsra. A lekérdezésszerkesztő alatt egy __Query Process Results__ (Lekérdezési folyamat eredményei) című szakasznak kell megjelennie, amelyben a feladat információi láthatók. 

    A lekérdezés befejeztével a __Query Process Results__ (Lekérdezési folyamat eredményei) szakaszban a művelet eredményei jelennek meg. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.

    ![HDInsight Hive-nézetek](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png).

6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:

        SELECT * FROM hivesampletable;

    > [AZURE.TIP] Tekintse meg az __Save results__ (Eredmények mentése) legördülő menüt a __Query Process Results__ (Lekérdezési folyamat eredményei) szakaszban, melynek segítségével az eredményeket letöltheti vagy CSV-fájlként mentheti a HDInsight-tárolóba.

7. A **History** (Előzmények) lehetőségre kattintva megkapja a feladatok listáját.

Egy Hive-feladat befejezése után [exportálhatja az eredményeket az Azure SQL Database adatbázisba vagy az SQL Server-adatbázisba](hdinsight-use-sqoop-mac-linux.md), valamint az [Excel segítségével meg is jelenítheti az eredményeket](hdinsight-connect-excel-power-query.md). A Hive HDInsight rendszeren belüli használatával kapcsolatos további információkért lásd: [Use Hive and HiveQL with Hadoop in HDInsight to analyze a sample Apache log4j file](hdinsight-use-hive.md) (A Hive és a HiveQL együttes használata a HDInsight Hadoop eszközével egy Apache log4j mintafájl elemzéséhez).

##Az oktatóanyag tartalmának törlése

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. 

>[AZURE.NOTE] Az [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) segítségével igény szerint hozhat létre HDInsight-fürtöket, és konfigurálhat egy TimeToLive-beállítást a fürtök automatikus törléséhez. 

**A fürt és/vagy az alapértelmezett tárfiók törlése**

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. A portál irányítópultján kattintson arra a csempére, amelynek a neve megegyezik a fürt létrehozásakor használt erőforráscsoport-névvel.
3. Kattintson az erőforrás panel **Törlés** gombjára a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez, vagy kattintson a fürt nevére az **Erőforrások** csempén, majd a **Törlés** gombra a fürt paneljén. Vegye figyelembe, hogy az erőforráscsoport törlése esetén a tárfiók is törlődik. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## Következő lépések

Ebben az oktatóprogramban megtanulhatta, hogyan hozhat létre Linux-alapú HDInsight-fürtöt egy Resource Manager-sablonnal, és hogyan hajthat végre alapszintű Hive-lekérdezéseket.

A HDInsighttal végzett adatelemzéssel kapcsolatos további információkért tekintse meg a következőket:

- A Hive és a HDInsight együttes használatával, például a Hive-lekérdezések Visual Studióból történő végrehajtásával kapcsolatos további információkért lásd: [Use Hive with HDInsight][hdinsight-use-hive] (A Hive használata a HDInsightban).

- Az adatok átalakítására szolgáló Pig nyelvvel kapcsolatos további információkért lásd: [Use Pig with HDInsight][hdinsight-use-pig] (A Pig használata a HDInsightban).

- A Hadoopon adatokat feldolgozó programok írására szolgáló MapReduce módszerrel kapcsolatos további információkért lásd: [Use MapReduce with HDInsight][hdinsight-use-mapreduce] (A MapReduce használata a HDInsightban).

- A HDInsight-adatok elemzésére szolgáló HDInsight Tools for Visual Studio szolgáltatással kapcsolatos további információkért lásd: [Get started using Visual Studio Hadoop tools for HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md) (A HDInsight Visual Studio Hadoop-eszközeinek használatára vonatkozó első lépések).

Ha készen áll dolgozni a saját adataival, és szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következőket:

- További információ az Azure Blob Storage HDInsight-általi használatáról: [Use Azure Blob storage with HDInsight](hdinsight-hadoop-use-blob-storage.md) (Az Azure Blob Storage használata a HDInsightban).

- Információk arról, hogyan tölthet fel adatokat a HDInsightba: [Upload data to HDInsight][hdinsight-upload-data] (Adatok feltöltése a HDInsightba).

A HDInsight-fürtök létréhozásával vagy kezelésével kapcsolatos további információkért lásd:

- Információk a Linux-alapú HDInsight-fürtök kezeléséhez: [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével).

- További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).

- Ha már ismeri a Linux és a Hadoop használatát, de szeretne konkrét részleteket megtudni a HDInsight-alapú Hadoopról, akkor tekintse meg a következő témakört: [Working with HDInsight on Linux](hdinsight-hadoop-linux-information.md) (A HDInsight használata Linux rendszeren). Ez többek között a következő információkat biztosítja:

    * a fürtön tárolt szolgáltatások, például az Ambari és a WebHCat URL-címét;
    * a Hadoop-fájlok és- példák helyét a helyi fájlrendszerben;
    * az Azure Storage (WASB) alapértelmezett adattárként történő használatát a HDFS helyett.


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png



<!--HONumber=Sep16_HO5-->


