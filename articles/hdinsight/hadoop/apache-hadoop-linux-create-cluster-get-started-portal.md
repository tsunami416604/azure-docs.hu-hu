---
title: 'Gyors útmutató: Apache Hadoop, Apache Hive & Azure HDInsight-portál'
description: Ebben a rövid útmutatóban egy HDInsight Hadoop-fürt létrehozásához használja a Azure Portal
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 35dc4938760ca83a6781d5791c746ee2f74310ab
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031586"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsight Azure Portal használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre [Apache Hadoop](https://hadoop.apache.org/) -fürtöket a HDInsight-ben Azure Portal használatával, majd hogyan futtathat Apache Hive feladatokat a HDInsight. A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a cikkben mind a három feladatot elvégzi.

Ebben a rövid útmutatóban egy HDInsight Hadoop-fürtöt hoz létre az Azure Portal használatával. Az [Azure Resource Manager-sablonok](apache-hadoop-linux-tutorial-get-started.md) használatával is létrehozhat fürtöket.

A HDInsight jelenleg [7 különböző fürttípussal érhető el](../hdinsight-overview.md#cluster-types-in-hdinsight). Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsight által támogatott összetevők listáját a következő témakörben tekintheti [meg: Újdonságok a Apache Hadoop-fürt HDInsight által biztosított verzióiban?](../hdinsight-component-versioning.md)  

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban az Azure Portal használatával.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre.

1. A Azure Portal válassza az **erőforrás létrehozása** > **Analytics** > **HDInsight**elemet.

    ![Erőforrás-HDInsight fürt létrehozása](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Erőforrás-HDInsight fürt létrehozása")

1. Az **alapbeállítások**területen adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetést    |  Válassza ki az Azure-előfizetését. |
    |Erőforráscsoport     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |Fürt neve   | Adja meg a Hadoop-fürt nevét. Mivel a HDInsightban az összes fürt ugyanazt a DNS-névteret használja, a névnek egyedinek kell lennie. A név legfeljebb 59 karaktert tartalmazhat, beleértve a betűket, számokat és kötőjeleket. A név első és utolsó karaktere nem lehet kötőjel. |
    |Hely    | Válassza ki, melyik Azure-helyen kívánja létrehozni a fürtöt.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |
    |Fürt típusa| Válassza a **fürt típusának kiválasztása**lehetőséget. Ezután válassza a **Hadoop** lehetőséget.|
    |Verzió|A fürt típusának alapértelmezett verziója lesz megadva. Ha más verziót szeretne megadni, válasszon a legördülő listából.|
    |Fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név a **rendszergazda**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs betűt, egy nem alfanumerikus karaktert (kivéve a "" "karaktert \). Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
    |Fürt bejelentkezési jelszavának használata SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót szeretné használni az SSH-felhasználó számára, mint a fürt bejelentkezési felhasználójának.|

    ![A HDInsight Linux első lépései a fürt alapértékeit biztosítják](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Alapvető értékek megadása HDInsight-fürt létrehozásához")

    Válassza ki a **következőt: storage > >** a tárolási beállításokra való továbblépés érdekében.

1. A **Storage (tárolás** ) lapon adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett értéket az **Azure Storage**-ban.|
    |Kiválasztási módszer|Használja az alapértelmezett értéket a **listából**.|
    |Az elsődleges tárfiók|A legördülő listából válasszon ki egy meglévő Storage-fiókot, vagy válassza az **új létrehozása**lehetőséget. Új fiók létrehozásakor a névnek 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![HDInsight Linux – első lépések – a fürt tárolási értékeinek megadása](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Tárolási értékek megadása HDInsight-fürt létrehozásához")

    Válassza a **felülvizsgálat + létrehozás** lapot.

1. A **felülvizsgálat + létrehozás** lapon ellenőrizze a korábbi lépésekben kiválasztott értékeket.

    ![HDInsight Linux – első lépések – fürt összefoglalása](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux – első lépések – fürt összefoglalása")

1. Kattintson a **Létrehozás** gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

A fürt létrehozása után megjelenik a fürt áttekintési oldala az Azure Portalon.

![HDInsight Linux – első lépések – fürt beállításai](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "HDInsight-fürt tulajdonságai")

Minden egyes fürt egy [Azure Storage-fióktól](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake-fióktól](../hdinsight-hadoop-use-data-lake-store.md) függ. Ez az alapértelmezett tárfiók. A HDInsight-fürtnek és az alapértelmezett Storage-fióknak ugyanabban az Azure-régióban kell elhelyezkednie. A fürtök törlésével a tárfiók nem törlődik.

> [!NOTE]  
> A további fürtök létrehozási módszereivel és az ebben a rövid útmutatóban használt tulajdonságok megismerésével kapcsolatban lásd: [HDInsight-fürtök létrehozása](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Apache Hive lekérdezések futtatása

Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben a rövid útmutatóban a Ambari struktúra nézetet használja a portálon. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

1. Az Ambari megnyitásához az előző képernyőkép szerint válassza a **Fürt irányítópultja** elemet.  Tallózással is megkeresheti `https://ClusterName.azurehdinsight.net`, ahol `ClusterName` az előző szakaszban létrehozott fürt.

    ![HDInsight Linux – első lépések fürt irányítópultja](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux – első lépések fürt irányítópultja")

2. Adja meg a fürt létrehozásakor megadott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.

3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:

    ![A kaptár nézet kiválasztása a Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "HDInsight-struktúra megjelenítői menü")

4. A **QUERY** (Lekérdezés) lapon másolja be a következő HiveQL-kifejezést a munkalapra:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight-struktúra nézet lekérdezés-szerkesztője](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HDInsight-struktúra nézet lekérdezés-szerkesztője")

5. Válassza a **Végrehajtás** lehetőséget. A **QUERY** (Lekérdezés) lap alatt megjelenik a **RESULTS** (Eredmények) lap, amelyen a feladat információi láthatók. 

    A lekérdezés befejeződése után a **lekérdezés** lap a művelet eredményét jeleníti meg. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.

    ![HDInsight Apache Hive eredményeinek megtekintése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive eredményeinek megtekintése")

6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. A lekérdezés eredményeit mentheti is. Válassza a jobb oldalon lévő menügombot, és adja meg, hogy letölti az eredményeket CSV-fájlként, vagy a fürthöz társított tárfiókban szeretné tárolni őket.

    ![Apache Hive lekérdezés eredményének mentése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Apache Hive lekérdezés eredményének mentése")

Egy Hive-feladat befejezése után [exportálhatja az eredményeket az Azure SQL-adatbázisba vagy az SQL Server-adatbázisba](apache-hadoop-use-sqoop-mac-linux.md), valamint az [Excel segítségével meg is jelenítheti az eredményeket](apache-hadoop-connect-excel-power-query.md). További információ a kaptár HDInsight-ben való használatáról: a [Apache Hive és a HiveQL használata Apache Hadoop a HDInsight-ben egy minta Apache log4j-fájl elemzéséhez](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

> [!NOTE]  
> Ha *azonnal* továbblép a következő cikkre, amelyből megtudhatja, hogyan futtathat ETL-műveleteket a HDInsight Hadoop használatával, érdemes megtartania a fürtöt. Ennek az az oka, hogy az oktatóanyagban újra létre kell hoznia egy Hadoop-fürtöt. Ha azonban a következő cikk azonnal nem érhető el, azonnal törölnie kell a fürtöt.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>A fürt és/vagy az alapértelmezett tárfiók törlése

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

    ![Azure HDInsight-fürt törlése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Azure HDInsight-fürt törlése")

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Linux-alapú HDInsight-fürtöt egy Resource Manager-sablonnal, és hogyan hajthat végre alapszintű kaptár-lekérdezéseket. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az interaktív lekérdezés használatával a HDInsight-on](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
