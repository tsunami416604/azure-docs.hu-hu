---
title: 'Rövid útmutató: Apache Hadoop, Apache Hive & Azure HDInsight portál'
description: Ebben a rövid útmutatóban az Azure Portal segítségével hozhat létre egy HDInsight Hadoop-fürtöt
keywords: hadoop első lépések, hadoop linux, hadoop rövid útmutató, hive első lépések, hive rövid útmutató
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 8939d9b342094342d576c00cf02e622286c8fc0f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130570"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rövid útmutató: Apache Hadoop-fürt létrehozása az Azure HDInsightban az Azure Portal használatával

Ebben a cikkben megtudhatja, hogyan hozhat létre Apache Hadoop-fürtöket a HDInsightban az Azure Portalhasználatával, majd futtathat Apache Hive-feladatokat a HDInsightban. A legtöbb Hadoop-feladat kötegelt feladat. Létrehoz fog hozni egy fürtöt, futtat néhány feladatot, majd törölni fogja a fürtöt. Ebben a cikkben mind a három feladatot elvégzi. Az elérhető konfigurációk részletes magyarázata a [Fürtök beállítása a HDInsightban](../hdinsight-hadoop-provision-linux-clusters.md). A portál fürtök létrehozásához való használatáról a [Fürtök létrehozása a portálon](../hdinsight-hadoop-create-linux-clusters-portal.md)című témakörben talál további információt.

Ebben a rövid útmutatóban egy HDInsight Hadoop-fürtöt hoz létre az Azure Portal használatával. Az [Azure Resource Manager-sablonok](apache-hadoop-linux-tutorial-get-started.md) használatával is létrehozhat fürtöket.

Jelenleg a HDInsight [hét különböző fürttípussal](../hdinsight-overview.md#cluster-types-in-hdinsight)érkezik. Minden egyes fürttípus más és más összetevőket támogat. A Hive-ot minden fürttípus támogatja. A HDInsight támogatott összetevőinek listáját [a HDInsight által biztosított Apache Hadoop fürtverziók újdonságai című témakörben tartalmazza?](../hdinsight-component-versioning.md)  

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Ebben a szakaszban egy Hadoop-fürtöt hozhat létre a HDInsightban az Azure Portal használatával.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A felső menüben válassza a **+ Erőforrás létrehozása**lehetőséget.

    ![Erőforrás HDInsight-fürt létrehozása](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Erőforrás HDInsight-fürt létrehozása")

1. Válassza az **Analytics** > **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megugrásához.

1. Az **Alapok** lapon adja meg a következő információkat:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Előfizetés    |  A legördülő listából válassza ki a fürthöz használt Azure-előfizetést. |
    |Erőforráscsoport     | A legördülő listában jelölje ki a meglévő erőforráscsoportot, vagy válassza **az Új létrehozása lehetőséget.**|
    |Fürt neve   | Adjon meg egy globálisan egyedi nevet. A név legfeljebb 59 karakterből állhat, beleértve a betűket, számokat és kötőjeleket. A név első és utolsó karaktere nem lehet kötőjel. |
    |Régió    | A legördülő listából válassza ki azt a régiót, ahol a fürt létrejön.  A legjobb teljesítmény érdekében válassza az Önhöz legközelebb eső helyet. |
    |Fürt típusa| Válassza **a Fürttípus kiválasztása**lehetőséget. Ezután válassza **a Hadoop** lehetőséget fürttípusként.|
    |Verzió|A legördülő listából válasszon egy **verziót.** Használja az alapértelmezett verziót, ha nem tudja, mit válasszon.|
    |A fürt bejelentkezési felhasználóneve és jelszava    | Az alapértelmezett bejelentkezési név **admin**. A jelszónak legalább 10 karakter hosszúságúnak kell lennie, és legalább egy számjegyet, egy nagybetűt és egy kisbetűt, egy nem alfanumerikus karaktert (kivéve a " ' karaktereket . \) Győződjön meg róla, hogy **ne adjon meg** gyakori jelszót, mint például a következő: Pass@word1.|
    |Secure Shell- (SSH-) felhasználónév | Az alapértelmezett felhasználónév az **sshuser**.  SSH-felhasználónévként más nevet is megadhat. |
    |Fürtbejelentkezési jelszó használata az SSH-hoz| Jelölje be ezt a jelölőnégyzetet, ha ugyanazt a jelszót szeretné használni az SSH-felhasználó számára, mint amelyet a fürtbejelentkezési felhasználó számára megadott.|

    ![HDInsight Linux első lépések fürtalapvető értékeket biztosítanak](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "HDInsight-fürt létrehozásának alapvető értékeinek biztosítása")

    Válassza a **Tovább: Storage >>** a tárolási beállításokhoz való előrelépéshez.

1. A **Tárolás** lapon adja meg a következő értékeket:

    |Tulajdonság  |Leírás  |
    |---------|---------|
    |Elsődleges tároló típusa|Használja az alapértelmezett **Azure Storage**értéket.|
    |Kiválasztási módszer|Használja az alapértelmezett : **Válassza ki a listából lehetőséget.**|
    |Az elsődleges tárfiók|A legördülő lista segítségével válasszon ki egy meglévő tárfiókot, vagy válassza **az Új létrehozása lehetőséget.** Ha új fiókot hoz létre, a név nek 3 és 24 karakter közötti hosszúságúnak kell lennie, és csak számokat és kisbetűket tartalmazhat.|
    |Tároló|Használja az automatikusan feltöltött értéket.|

    ![HDInsight Linux – első lépések fürttárolási értékek biztosítása](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "HDInsight-fürt létrehozásához tárolási értékek biztosítása")

    Minden egyes fürt egy [Azure Storage-fióktól](../hdinsight-hadoop-use-blob-storage.md) vagy egy [Azure Data Lake-fióktól](../hdinsight-hadoop-use-data-lake-store.md) függ. Ez a továbbiakban az alapértelmezett tárfiók. A HDInsight-fürtnek és alapértelmezett tárfiókjának ugyanabban az Azure-régióban kell elhelyezni. Fürtök törlése nem törli a tárfiókot.

    Válassza a **Véleményezés + létrehozás** lapot.

1. A **Véleményezés + létrehozás** lapon ellenőrizze a korábbi lépésekben kiválasztott értékeket.

    ![HDInsight Linux első lépések fürtösszefoglaló](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux első lépések fürtösszefoglaló")

1. Kattintson a **Létrehozás** gombra. Egy fürt létrehozása nagyjából 20 percet vesz igénybe.

    A fürt létrehozása után megjelenik a fürt áttekintési oldala az Azure Portalon.

    ![HDInsight Linux első lépései – fürtbeállítások](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "HDInsight-fürt tulajdonságai")

## <a name="run-apache-hive-queries"></a>Apache Hive-lekérdezések futtatása

Az [Apache Hive](hdinsight-use-hive.md) a HDInsight legnépszerűbb összetevője. Számos módon futtathat Hive-feladatokat a HDInsightban. Ebben a rövid útmutatóban az Ambari Hive nézetet használja a portálról. A Hive-feladatok egyéb küldési módjaiért lásd: [Use Hive in HDInsight](hdinsight-use-hive.md) (A Hive használata a HDInsightban).

> [!NOTE]
> Az Apache Hive View nem érhető el a HDInsight 4.0-s verzióban.

1. Az Ambari megnyitásához az előző képernyőkép szerint válassza a **Fürt irányítópultja** elemet.  Azt is tallózhatja, hogy `https://ClusterName.azurehdinsight.net` hol `ClusterName` van az előző szakaszban létrehozott fürt.

    ![HDInsight Linux első lépések fürtirányítópult](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux első lépések fürtirányítópult")

2. Adja meg a fürt létrehozásakor megadott Hadoop-felhasználónevet és -jelszót. Az alapértelmezett felhasználónév az **admin**.

3. Nyissa meg a **Hive View** nézetet az alábbi képernyőfelvételen látható módon:

    ![Hive nézet kiválasztása az Ambariból](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "A HDInsight Hive megjelenítő menüje")

4. A **QUERY** (Lekérdezés) lapon másolja be a következő HiveQL-kifejezést a munkalapra:

    ```sql
    SHOW TABLES;
    ```

    ![HDInsight Hive nézet lekérdezésszerkesztője](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "HDInsight Hive nézet lekérdezésszerkesztője")

5. Válassza a **Végrehajtás** lehetőséget. A **QUERY** (Lekérdezés) lap alatt megjelenik a **RESULTS** (Eredmények) lap, amelyen a feladat információi láthatók. 

    Miután a lekérdezés befejeződött, a **LEKÉRDEZÉS** lap megjeleníti a művelet eredményét. Látni fog egy **hivesampletable** nevű táblát. Ezzel a Hive mintatáblával az összes HDInsight-fürt rendelkezik.

    ![HDInsight Apache Hive nézet eredményei](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive nézet eredményei")

6. Ismételje meg a 4. és az 5. lépést az alábbi lekérdezés futtatásához:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. A lekérdezés eredményeit mentheti is. Válassza a jobb oldalon lévő menügombot, és adja meg, hogy letölti az eredményeket CSV-fájlként, vagy a fürthöz társított tárfiókban szeretné tárolni őket.

    ![Apache Hive-lekérdezés eredményének mentése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Apache Hive-lekérdezés eredményének mentése")

Miután elvégezte a Hive-feladatot, [exportálhatja az eredményeket az Azure SQL Database vagy az SQL Server adatbázisába,](apache-hadoop-use-sqoop-mac-linux.md)az excel segítségével is [megjelenítheti az eredményeket.](apache-hadoop-connect-excel-power-query.md) A Hive HDInsightban való használatáról az [Apache Hive és a HiveQL használata az Apache Hadoop használatával a HDInsightban című témakörben talál további információt az Apache log4j-fájl minta elemzéséhez.](hdinsight-use-hive.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

> [!NOTE]  
> Ha *azonnal* továbblép a következő cikkre, amely ből megtudhatja, hogyan futtathatja az ETL-műveleteket a Hadoop használatával a HDInsight-on, érdemes lehet a fürt futását. Ez azért van, mert a bemutató ban újra létre kell hoznia egy Hadoop-fürtöt. Ha azonban nem megy keresztül azonnal a következő cikken, azonnal törölnie kell a fürtöt.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>A fürt és/vagy az alapértelmezett tárfiók törlése

1. Térjen vissza ahhoz a böngészőlaphoz, amelyen meg van nyitva az Azure Portal. A portálon a fürt áttekintési lapja lesz látható. Ha csak a fürtöt szeretné törölni, de meg szeretné tartani az alapértelmezett tárfiókot, válassza a **Törlés** lehetőséget.

    ![Az Azure HDInsight fürttörlése](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Az Azure HDInsight-fürt törlése")

2. Ha a fürtöt és az alapértelmezett tárfiókot is törölni kívánja, válassza ki az erőforráscsoport nevét (amely az előző képernyőképen ki van emelve) az erőforráscsoport lapjának megnyitásához.

3. Válassza az **Erőforráscsoport törlése** lehetőséget a fürtöt és az alapértelmezett tárfiókot tartalmazó erőforráscsoport törléséhez. Vegye figyelembe, hogy az erőforráscsoport törlése a tárfiókot is törli. Ha szeretné megtartani a tárfiókot, csak a fürtöt törölje.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre Linux-alapú HDInsight-fürtet egy Erőforrás-kezelő sablon használatával, és hogyan hajthatja végre az alapvető Hive-lekérdezéseket. A következő cikkben megtudhatja, hogyan végezheti el az adatok kinyerési, átalakítási és betöltési (ETL) műveleteit a Hadoop használatával a HDInsighton.

> [!div class="nextstepaction"]
> [Adatok kinyerése, átalakítása és betöltése az Interaktív lekérdezés használatával a HDInsighton](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
