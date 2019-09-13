---
title: 'Gyors útmutató: Apache Hadoop és Apache Hive a HDInsight a Azure Portal használatával'
description: Ebben a rövid útmutatóban egy HDInsight Hadoop-fürt létrehozásához használja a Azure Portal
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: hrasheed
ms.openlocfilehash: be7ff67f07ed8eaeb3f04a15c6185191bf107cf2
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918511"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Gyors útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsight Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre [Apache Hadoop](https://hadoop.apache.org/) -fürtöket a HDInsight-ben Azure Portal használatával, majd hogyan futtathat Apache Hive feladatokat a HDInsight. A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a cikkben mind a három feladatot elvégzi.

Ebben a rövid útmutatóban egy HDInsight Hadoop-fürtöt hoz létre az Azure Portal használatával. Az [Azure Resource Manager-sablonok](apache-hadoop-linux-tutorial-get-started.md) használatával is létrehozhat fürtöket.

A HDInsight jelenleg [7 különböző fürttípussal érhető el](../hdinsight-overview.md#cluster-types-in-hdinsight). Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsight által támogatott összetevők listáját a következő témakörben tekintheti [meg: Újdonságok a Apache Hadoop-fürt HDInsight által biztosított verzióiban?](../hdinsight-component-versioning.md)  

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban az Azure Portal használatával.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre.

1. A Azure Portal lépjen az **erőforrás** > -**elemzési** > **HDInsight**létrehozása elemre.

    ![Databricks az Azure Portalon](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Databricks az Azure Portalon")

1. Az **HDInsight** > **gyors létrehozása** > **alapismeretek**területen adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Fürt neve   | Adja meg a Hadoop-fürt nevét. Mivel a HDInsightban az összes fürt ugyanazt a DNS-névteret használja, a névnek egyedinek kell lennie. A név legfeljebb 59 karaktert tartalmazhat, beleértve a betűket, számokat és kötőjeleket. A név első és utolsó karaktere nem lehet kötőjel. |
    |Subscription    |  Válassza ki az Azure-előfizetését. |
    |Fürt típusa     | Ezt egyelőre hagyja ki. Ezt a bemenetet az eljárást következő lépésében adja meg.|
    |Fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név az **admin**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs, illetve egy nem alfanumerikus karaktert (ami nem lehet ' " ` \)). Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
    |Fürt bejelentkezési jelszavának használata SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót szeretné használni az SSH-felhasználó számára, mint a fürt bejelentkezési felhasználójának.|
    |Resource group     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |Location    | Válassza ki, melyik Azure-helyen kívánja létrehozni a fürtöt.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |

    ![HDInsight – Linux – első lépések – fürt alapvető beállításainak megadása](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics1.png "Alapvető beállítások megadása HDInsight-fürt létrehozásához")

1. Válassza ki a **fürt típusát** a **fürtkonfiguráció** lap megnyitásához, majd adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Fürt típusa     | Válassza a **Hadoop** lehetőséget. |
    |Version     | Válassza a **Hadoop 2.7.3 (HDI 3.6)** lehetőséget.|

    ![HDInsight Linux – első lépések – fürtkonfiguráció](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "HDInsight Linux – első lépések – fürtkonfiguráció")

    Válassza a **kiválasztás** lehetőséget, majd kattintson a **tovább** gombra a tárolási beállításokhoz való továbblépés előtt.

1. A **Storage (tárolás** ) lapon adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tárolási típusok    | Ehhez a cikkhez válassza az Azure Storage lehetőséget, hogy az alapértelmezett Storage-fiókként Azure Storage Blob használni. Az Azure Data Lake Storage-ot is használhatja alapértelmezett tárolóként. |
    |Kiválasztási módszer     |  Ehhez a cikkhez válassza a **Saját előfizetések** lehetőséget az Azure-előfizetésében lévő egyik tárfiók használatához. Ha más előfizetések tárfiókját szeretné használni, válassza a **Hozzáférési kulcs** lehetőséget, majd adja meg a fiók hozzáférési kulcsát. |
    |Tárfiók kiválasztása   | Válassza ki **a Storage-fiók** kiválasztása lehetőséget egy meglévő Storage-fiók kiválasztásához, vagy válassza az **új létrehozása**lehetőséget. Új fiók létrehozásakor a névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.|

    Fogadja el az összes többi alapértelmezett értéket, majd kattintson a **tovább** gombra az Összefoglalás lapra való továbblépés előtt.

    ![HDInsight – Linux – első lépések – fürt tárolóértékeinek megadása](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Tárolóértékek megadása HDInsight-fürt létrehozásához")

1. Az **Összefoglalás** lapon ellenőrizze a korábbi lépésekben kiválasztott értékeket.

    ![HDInsight – Linux – első lépések összegzése](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "HDInsight – Linux – első lépések – fürtösszegzés")

1. Kattintson a **Létrehozás** gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

1. A fürt létrehozása után megjelenik a fürt áttekintési oldala az Azure Portalon.

    ![fürtbeállítások](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "HDInsight-fürttulajdonságok")    

    Minden egyes fürt egy [Azure Storage-fióktól](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake-fióktól](../hdinsight-hadoop-use-data-lake-store.md) függ. Ez az alapértelmezett tárfiók. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlésével a tárfiók nem törlődik.

    > [!NOTE]  
    > A további fürtök létrehozási módszereivel és az ebben a rövid útmutatóban használt tulajdonságok megismerésével kapcsolatban lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Apache Hive lekérdezések futtatása

Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben a rövid útmutatóban a Ambari struktúra nézetet használja a portálon. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

1. Az Ambari megnyitásához az előző képernyőkép szerint válassza a **Fürt irányítópultja** elemet.  Tallózással is megkeresheti `https://ClusterName.azurehdinsight.net`, `ClusterName` ahol az az előző szakaszban létrehozott fürtöt.

    ![HDInsight – Linux – első lépések – irányítópult](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight – Linux – első lépések – fürt – irányítópult")

2. Adja meg a fürt létrehozásakor megadott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.

3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:

    ![Az Ambari-nézetek kiválasztása](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "HDInsight Hive Viewer menü")

4. A **QUERY** (Lekérdezés) lapon másolja be a következő HiveQL-kifejezést a munkalapra:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight Hive-nézetek](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HDInsight Hive View lekérdezésszerkesztő")

5. Válassza a **Végrehajtás** lehetőséget. A **QUERY** (Lekérdezés) lap alatt megjelenik a **RESULTS** (Eredmények) lap, amelyen a feladat információi láthatók. 

    A lekérdezés befejeződése után a **lekérdezés** lap a művelet eredményét jeleníti meg. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.

    ![HDInsight-struktúra nézet eredményei](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight-struktúra nézet eredményei")

6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. A lekérdezés eredményeit mentheti is. Válassza a jobb oldalon lévő menügombot, és adja meg, hogy letölti az eredményeket CSV-fájlként, vagy a fürthöz társított tárfiókban szeretné tárolni őket.

    ![Hive-lekérdezés eredményének mentése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Hive-lekérdezés eredményének mentése")

Egy Hive-feladat befejezése után [exportálhatja az eredményeket az Azure SQL-adatbázisba vagy az SQL Server-adatbázisba](apache-hadoop-use-sqoop-mac-linux.md), valamint az [Excel segítségével meg is jelenítheti az eredményeket](apache-hadoop-connect-excel-power-query.md). További információ a kaptár HDInsight-ben való használatáról: a [Apache Hive és a HiveQL használata Apache Hadoop a HDInsight-ben egy minta Apache log4j-fájl elemzéséhez](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

> [!NOTE]  
> Ha *azonnal* továbblép a következő cikkre, amelyből megtudhatja, hogyan futtathat ETL-műveleteket a HDInsight Hadoop használatával, érdemes megtartania a fürtöt. Ennek az az oka, hogy az oktatóanyagban újra létre kell hoznia egy Hadoop-fürtöt. Ha azonban a következő cikk azonnal nem érhető el, azonnal törölnie kell a fürtöt.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>A fürt és/vagy az alapértelmezett tárfiók törlése

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

    ![HDInsight – fürt törlése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "HDInsight-fürt törlése")

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Linux-alapú HDInsight-fürtöt egy Resource Manager-sablonnal, és hogyan hajthat végre alapszintű kaptár-lekérdezéseket. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
>[Adatok kinyerése, átalakítása és betöltése az interaktív lekérdezés használatával a HDInsight-on](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
