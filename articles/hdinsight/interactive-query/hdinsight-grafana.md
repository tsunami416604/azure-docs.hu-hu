---
title: A Grafana használata az Azure HDInsight
description: Ismerje meg, hogyan érheti el a Grafana-irányítópultot Apache Hadoop-fürtökkel az Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: cea0e9709afb65caa23d28be093c28498f2b82d0
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122981"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Grafana elérése az Azure HDInsight

A [Grafana](https://grafana.com/) egy népszerű, nyílt forráskódú gráf-és irányítópult-szerkesztő. A Grafana funkció gazdag; nem csupán azt teszi lehetővé, hogy a felhasználók testre szabható és megosztható irányítópultokat hozzanak létre, valamint a sablonban szereplő/megírt irányítópultokat, az LDAP-integrációt, több adatforrást és egyebeket is kínál.

Jelenleg az Azure HDInsight-ben a Grafana a Hbase és az interaktív lekérdezési fürt típusait támogatja.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Ebben a szakaszban egy interaktív lekérdezési fürtöt hoz létre a HDInsight egy Azure Resource Manager sablon használatával. Nem kell a Resource Manager-sablonok használatára vonatkozó tapasztalattal rendelkeznie a cikk lépéseinek követéséhez. 

1. Az alábbi **Üzembe helyezés az Azure-ban** gombra kattintva jelentkezzen be az Azure-ba, és nyissa meg a Resource Manager-sablont az Azure Portalon.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-interactive-hive%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-grafana/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Adja meg vagy válassza ki az értékeket az alábbi képernyőképen látható módon:

    > [!NOTE]  
    > A megadott értékeknek egyedinek kell lenniük, és követniük kell az elnevezési irányelveket. A sablon nem végez érvényességi ellenőrzéseket. Ha a megadott értékek már használatban vannak, vagy nem felelnek meg az irányelveknek, a sablon elküldését követően egy hibaüzenetet fog kapni.       
    > 
    >
    
    ![HDInsight Linux – a Resource Manager-sablon a Portálon történő használatának első lépései](./media/hdinsight-grafana/hdinsight-linux-get-started-arm-template-on-portal.png "HDInsight-alapú Hadoop-fürt üzembe helyezése az Azure Portal és egy erőforráscsoport-kezelő sablon használatával")

    Adja meg vagy válassza ki a következő értékeket:
    
    |Tulajdonság  |Leírás  |
    |---------|---------|
    |**Előfizetés**     |  Válassza ki az Azure-előfizetését. |
    |**Erőforráscsoport**     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |**Hely**     | Válassza ki, melyik Azure-helyen kívánja létrehozni a fürtöt.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |
    |**Fürt típusa**     | Válassza a **Hadoop** lehetőséget. |
    |**Fürt neve**     | Adja meg a Apache Hadoop-fürt nevét. Mivel a HDInsightban az összes fürt ugyanazt a DNS-névteret használja, a névnek egyedinek kell lennie. A név legfeljebb 59 karakter hosszúságú lehet, és csak betűket, számokat, illetve kötőjelet tartalmazhat. A név első és utolsó karaktere nem lehet kötőjel. |
    |**A fürt bejelentkezési neve és jelszava**     | Az alapértelmezett bejelentkezési név az **admin**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs, illetve egy nem alfanumerikus karaktert (ami nem lehet ' " ` \)). Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |**SSH-felhasználónév és jelszó**     | Az alapértelmezett felhasználónév az **sshuser**.  Az SSH-felhasználónevet át lehet nevezni.  Az SSH-felhasználói jelszóra ugyanazon követelmények vonatkoznak, mint a fürt bejelentkezési jelszavára.|

    Egyes tulajdonságok szoftveresen kötöttek a sablonban.  Ezeket az értéteket a sablonból konfigurálhatja. A tulajdonságok részletes ismertetését lásd: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](../hdinsight-hadoop-provision-linux-clusters.md).

3. Jelölje be az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd válassza a **Vásárlás** lehetőséget. A portál irányítópultján egy új csempe jelenik meg **Üzembe helyezés beküldése** címmel. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

    ![Azure-template Deployment előrehaladás](./media/hdinsight-grafana/deployment-progress-tile.png "Azure-template Deployment előrehaladás")

4. A fürt létrejötte után a csempe felirata átvált az Ön által megadott erőforráscsoport-névre. A csempe az erőforráscsoporton belül létrejött HDInsight-fürtöt is felsorolja.

    ![A HDInsight (Linux) használatának első lépései – erőforráscsoport](./media/hdinsight-grafana/hdinsight-linux-get-started-resource-group.png "Azure HDInsight-alapú fürt erőforráscsoportja")

5. A csempe a fürthöz tartozó alapértelmezett tárterületet is felsorolja. Minden egyes fürt egy [Azure Storage-fióktól](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake-fióktól](../hdinsight-hadoop-use-data-lake-store.md) függ. Ez az alapértelmezett tárfiók. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlésével a tárfiók nem törlődik.
    

> [!NOTE]  
> A további fürtök létrehozási módszereivel és a cikkben használt tulajdonságok megismerésével kapcsolatban lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="access-the-grafana-dashboard"></a>Hozzáférés a Grafana irányítópulthoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki a **HDInsight-fürtök**lehetőséget, majd válassza ki az utolsó szakaszban létrehozott fürtöt.

3. A **Gyorshivatkozások**területen kattintson a **fürt irányítópultja**elemre.

    ![HDInsight-fürt irányítópult-portál](./media/hdinsight-grafana/hdinsight-portal-cluster-dashboard.png "HDInsight-fürt irányítópultja a portálon")

4. Az irányítópulton kattintson a **Grafana** csempére. Másik lehetőségként keresse `/grafana/` meg a fürt URL-címének elérési útját. Például: `https://<clustername>.azurehdinsight.net/grafana/`.

5. Adja meg a Hadoop-fürt felhasználói hitelesítő adatait.

6. Megjelenik a Grafana irányítópultja, amely a következő példához hasonlóan néz ki:

    ![HDInsight Grafana webes irányítópult](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana-irányítópult")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A cikk befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. 

> [!NOTE]  
> Ha *azonnal* továbblép a következő oktatóanyagra, amelyben megismerheti, hogyan futtathat ETL-műveleteket a Hadoop használatával a HDInsighton, érdemes lehet futva hagyni a fürtöt. Ez azért van, mert az oktatóanyagban ismét létre kell hoznia egy Hadoop-fürtöt. Ha azonban nem azonnal folytatja a következő oktatóanyaggal, most törölnie kell a fürtöt.

**A fürt és/vagy az alapértelmezett tárfiók törlése**

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

    ![Azure Portal fürt törlése ikon](./media/hdinsight-grafana/hdinsight-delete-cluster.png "HDInsight-fürt törlése")

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan hozhat létre egy Linux-alapú HDInsight-fürtöt egy Resource Manager-sablonnal, és hogyan hajthat végre alapszintű Apache Hive lekérdezéseket. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az interaktív lekérdezés használatával a HDInsight-on](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)

Ha készen áll dolgozni a saját adataival, és szeretne többet megtudni a HDInsight adattárolási módszereiről, illetve arról, hogyan kerülnek az adatok a HDInsightba, tekintse meg a következő cikkeket:

* További információt az Azure Storage HDInsight általi használatáról [az Azure Storage és a HDInsight együttes használatát](../hdinsight-hadoop-use-blob-storage.md) ismertető cikkben talál.
* További információ a HDInsightba való adatfeltöltésről: [Adatok feltöltése a HDInsightba](../hdinsight-upload-data.md).

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* Ha többet szeretne megtudni a kaptár és a HDInsight használatával kapcsolatban, többek között a kaptár-lekérdezések Visual studióból történő végrehajtásával kapcsolatban, tekintse meg a [Apache Hive használata a HDInsight](../hdinsight-use-hive.md)szolgáltatással
* Ha szeretne többet megtudni a Pig-ről, az adatátalakításhoz használt nyelvet lásd: az [Apache Pig és a HDInsight használata](../hdinsight-use-pig.md).
* További információ a Hadoopon adatokat feldolgozó programok írására szolgáló MapReduce módszerről: [A MapReduce használata a HDInsighttal](../hdinsight-use-mapreduce.md).
* A HDInsight-adatok elemzésére szolgáló HDInsight Tools for Visual Studio szolgáltatással kapcsolatos további információkért lásd: [Get started using Visual Studio Hadoop tools for HDInsight](../hadoop/apache-hadoop-visual-studio-tools-get-started.md) (A HDInsight Visual Studio Hadoop-eszközeinek használatára vonatkozó első lépések).



A HDInsight-fürtök létréhozásával vagy kezelésével kapcsolatos további információkért lásd a következő cikkeket:

* Információk a Linux-alapú HDInsight-fürtök kezeléséhez: [Manage HDInsight clusters using Ambari](../hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével).
* További információ a HDInsight-fürtök létrehozásakor kiválasztható beállításokról: [Creating HDInsight on Linux using custom options](../hdinsight-hadoop-provision-linux-clusters.md) (HDInsight létrehozása Linux rendszeren egyéni beállításokkal).


[1]: ../HDInsight/apache-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
