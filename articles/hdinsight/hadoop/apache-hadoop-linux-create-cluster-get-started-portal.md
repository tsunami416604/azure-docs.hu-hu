---
title: 'Gyors útmutató: Ismerkedés az Apache Hadoop- és Apache Hive, az Azure portal – Azure HDInsight használatával'
description: Ebben a rövid útmutatóban egy HDInsight Hadoop-fürt létrehozásához használhatja az Azure Portalon
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: hrasheed
ms.openlocfilehash: f92cb247afb25562a96373c28534549a2f16d8c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805599"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Gyors útmutató: Az Apache Hadoop-fürt létrehozása az Azure HDInsight az Azure portal használatával

Ebből a cikkből megismerheti, hogyan hozhat létre [Apache Hadoop](https://hadoop.apache.org/) a HDInsight az Azure portal használatával, és futtassa az Apache Hive-feladatok a HDInsight-fürtök. A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a cikkben mind a három feladatot elvégzi.

Ebben a rövid útmutatóban egy HDInsight Hadoop-fürtöt hoz létre az Azure Portal használatával. Az [Azure Resource Manager-sablonok](apache-hadoop-linux-tutorial-get-started.md) használatával is létrehozhat fürtöket.

A HDInsight jelenleg [7 különböző fürttípussal érhető el](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsight támogatott összetevők listáját lásd: [a HDInsight által biztosított az Apache Hadoop-fürtverziók újdonságai?](../hdinsight-component-versioning.md)  

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban az Azure Portal használatával.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre.

1. Az Azure Portalról nyissa meg **erőforrás létrehozása** > **Analytics** > **HDInsight**.

    ![Databricks az Azure Portalon](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight.png "Databricks az Azure Portalon")

1. A **HDInsight** > **Gyorslétrehozás** > **alapjai**, adja meg vagy válassza ki a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Fürt neve   | Adja meg a Hadoop-fürt nevét. Mivel a HDInsightban az összes fürt ugyanazt a DNS-névteret használja, a névnek egyedinek kell lennie. A név betűket, számokat és kötőjeleket tartalmazhat például legfeljebb 59 karakterből állhat. A név első és utolsó karaktere nem lehet kötőjel. |
    |Subscription    |  Válassza ki az Azure-előfizetését. |
    |Fürttípus     | Ezt egyelőre hagyja ki. Ezt a bemenetet az eljárást következő lépésében adja meg.|
    |Fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név az **admin**. A jelszónak legalább 10 karakterből kell állnia, és tartalmaznia kell legalább egy számot, egy nagybetűs és egy kisbetűs, illetve egy nem alfanumerikus karaktert (ami nem lehet ' " ` \)). Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
    |Fürt bejelentkezési jelszavának használata az SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót kívánja használni az SSH-felhasználóhoz, mint amelyet a fürt bejelentkezési fiókjához adott meg.|
    |Resource group     | Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy már meglévőt.  Az erőforráscsoport az Azure összetevőit tartalmazó tároló.  Ebben az esetben az erőforráscsoport a HDInsight-fürtöt és a függő Azure Storage-fiókot tartalmazza. |
    |Location    | Válassza ki, melyik Azure-helyen kívánja létrehozni a fürtöt.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |

    ![HDInsight – Linux – első lépések – fürt alapvető beállításainak megadása](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics.png "Alapvető beállítások megadása HDInsight-fürt létrehozásához")

1. Válassza ki **fürt típusa** megnyitásához a **fürtkonfiguráció** lapon, és adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Fürttípus     | Válassza a **Hadoop** lehetőséget. |
    |Version     | Válassza a **Hadoop 2.7.3 (HDI 3.6)** lehetőséget.|

    ![HDInsight – Linux – első lépések – fürt alapvető beállításainak megadása](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "Alapvető beállítások megadása HDInsight-fürt létrehozásához")

    Válassza ki **kiválasztása** majd **tovább** eljuthat a tárolási beállítások.

1. Az a **tárolási** lapra, adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tárolási típusok    | Ebben a cikkben az Azure storage használata az Azure Storage-Blobból az alapértelmezett tárfiók kiválasztása. Az Azure Data Lake Storage-ot is használhatja alapértelmezett tárolóként. |
    |Kiválasztási módszer     |  Ehhez a cikkhez válassza a **Saját előfizetések** lehetőséget az Azure-előfizetésében lévő egyik tárfiók használatához. Ha más előfizetések tárfiókját szeretné használni, válassza a **Hozzáférési kulcs** lehetőséget, majd adja meg a fiók hozzáférési kulcsát. |
    |Tárfiók kiválasztása   | Válassza ki **válassza ki a tárfiókot** válasszon ki egy meglévő tárfiókot, vagy válasszon **új létrehozása**. Hoz létre egy új fiókot, ha a név 3 – 24 karakter hosszúságúnak kell lennie, és számok és csak kisbetűket tartalmazhatnak.|

    Fogadja el az összes többi alapértelmezett értéket, majd **tovább** kattintva az Összegzés lapon lépjen.

    ![HDInsight – Linux – első lépések – fürt tárolóértékeinek megadása](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Tárolóértékek megadása HDInsight-fürt létrehozásához")

1. Az a **összefoglalás** lapot, ellenőrizze az értékeket az előző lépésben kiválasztott.

    ![HDInsight – Linux – első lépések összegzése](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "HDInsight – Linux – első lépések – fürtösszegzés")

1. Kattintson a **Létrehozás** gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

1. A fürt létrehozása után megjelenik a fürt áttekintési oldala az Azure Portalon.

    ![fürtbeállítások](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-overview.png "HDInsight-fürttulajdonságok")    

    Minden egyes fürt egy [Azure Storage-fióktól](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake-fióktól](../hdinsight-hadoop-use-data-lake-store.md) függ. Ez az alapértelmezett tárfiók. HDInsight-fürt és az alapértelmezett tárfióknak ugyanazon Azure-régióban kell felhőátjárónak. A fürtök törlésével a tárfiók nem törlődik.

    > [!NOTE]  
    > Egyéb Fürtlétrehozási módszerekhez és ebben a rövid útmutatóban használt tulajdonságok megértéséhez, lásd: [létre HDInsight-fürtök](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Az Apache Hive-lekérdezések futtatása

Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben a rövid útmutatóban használhatja a a portál Ambari Hive nézete. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

1. Az Ambari megnyitásához az előző képernyőkép szerint válassza a **Fürt irányítópultja** elemet.  Keresse a `https://ClusterName.azurehdinsight.net`, ahol `ClusterName` van az előző szakaszban létrehozott fürtöt.

    ![HDInsight – Linux – első lépések – irányítópult](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight – Linux – első lépések – fürt – irányítópult")

2. Adja meg a fürt létrehozásakor megadott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.

3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:

    ![Az Ambari-nézetek kiválasztása](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "HDInsight Hive Viewer menü")

4. A **QUERY** (Lekérdezés) lapon másolja be a következő HiveQL-kifejezést a munkalapra:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight Hive-nézetek](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "HDInsight Hive View lekérdezésszerkesztő")

5. Válassza a **Végrehajtás** lehetőséget. A **QUERY** (Lekérdezés) lap alatt megjelenik a **RESULTS** (Eredmények) lap, amelyen a feladat információi láthatók. 

    A lekérdezés befejeztével a **lekérdezés** lap megjeleníti a művelet eredményei. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.

    ![HDInsight Hive-nézetek](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "HDInsight Hive View lekérdezésszerkesztő")

6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. A lekérdezés eredményeit mentheti is. Válassza a jobb oldalon lévő menügombot, és adja meg, hogy letölti az eredményeket CSV-fájlként, vagy a fürthöz társított tárfiókban szeretné tárolni őket.

    ![Hive-lekérdezés eredményének mentése](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Hive-lekérdezés eredményének mentése")

Egy Hive-feladat befejezése után [exportálhatja az eredményeket az Azure SQL-adatbázisba vagy az SQL Server-adatbázisba](apache-hadoop-use-sqoop-mac-linux.md), valamint az [Excel segítségével meg is jelenítheti az eredményeket](apache-hadoop-connect-excel-power-query.md). Hive HDInsight használatával kapcsolatos további információkért lásd: [használata az Apache Hive és a HiveQL Apache hadooppal a HDInsight Apache log4j mintafájl elemzéséhez](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Miután végzett a gyors üzembe helyezéssel, érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

> [!NOTE]  
> Ha Ön *azonnal* továbblépés megtudhatja, hogyan futtathat a HDInsight hadooppal ETL-műveletek a következő cikkre, érdemes megtartani a rendszert futtató fürtre. Ennek az oka az oktatóanyagban kell újra létrehozni egy Hadoop-fürtöt. Azonban ha nem tervezi a következő cikk azonnal, törölnie kell a fürt most.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>A fürt és/vagy az alapértelmezett tárfiók törlése

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

    ![HDInsight – fürt törlése](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "HDInsight-fürt törlése")

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy Linux-alapú HDInsight-fürt Resource Manager-sablonnal, és hogyan hajthat végre alapszintű Hive-lekérdezéseket. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
>[A kinyerési, átalakítási és a HDInsight interaktív lekérdezés segítségével adatok betöltése](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
