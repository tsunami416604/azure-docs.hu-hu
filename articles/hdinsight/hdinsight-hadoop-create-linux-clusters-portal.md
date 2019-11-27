---
title: Apache Hadoop-fürtök létrehozása webböngésző használatával, Azure HDInsight
description: Megtudhatja, hogyan hozhat létre Apache Hadoop-, Apache HBase-, Apache Storm-vagy Apache Spark-fürtöket Linux rendszeren a HDInsight webböngészővel és a Azure Portal használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215917"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A Azure Portal egy webalapú felügyeleti eszköz a Microsoft Azure felhőben üzemeltetett szolgáltatásokhoz és erőforrásokhoz. Ebből a cikkből megtudhatja, hogyan hozhat létre Linux-alapú Azure HDInsight-fürtöket a portál használatával.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Tekintse meg, [hogyan szerezhet be ingyenes Azure-próbaverziót a HDInsight-Hadoop teszteléséhez](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Egy modern**böngésző. A Azure Portal HTML5-t és JavaScriptet használ. Előfordulhat, hogy nem működik megfelelően a régebbi böngészőkben.

## <a name="create-clusters"></a>Fürtök létrehozása

A Azure Portal a fürt legtöbb tulajdonságát elérhetővé teszi. Azure Resource Manager sablonok használatával számos részletet elolvashat. További információ: [Apache Hadoop-fürtök létrehozása a HDInsight-ben Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. A bal oldali menüben navigáljon a **+ erőforrás létrehozása** >  **Analytics** > **Azure HDInsight**elemre.

    ![Új fürt létrehozása a Azure Portalban](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Új fürt létrehozása a Azure Portalban")

1. A **HDInsight-fürt létrehozása** lapon válassza a **klasszikus létrehozási élmény**lehetőséget.

    ![Ugrás a klasszikus létrehozási élményre](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. A **HDInsight** lapon válassza az **Egyéni (méret, beállítások, alkalmazások)** lehetőséget.

1. Válasszon **1 alapismereteket**. Ezután adja meg a következő adatokat:

    |Tulajdonság |Leírás |
    |---|---|
    |Fürt neve|A névnek globálisan egyedinek kell lennie.|
    |Előfizetést|A legördülő listában válassza ki a fürthöz használt Azure-előfizetést.|
    |Fürttípus|Válassza ki a létrehozni kívánt fürt típusát. Ilyenek például a Hadoop és a Apache Spark. Az **operációs rendszer** **Linux**lesz. Ezután válassza ki a fürt típusának verzióját. Ha nem tudja, mit kell választania, használja az alapértelmezett verziót. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.|
    |Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett érték a **rendszergazda**.|
    |Fürt bejelentkezési jelszava|Adja meg a jelszót.|
    |Secure Shell- (SSH-) felhasználónév|Az alapértelmezett érték a **sshuser**. Ha ugyanazt az SSH-jelszót szeretné használni, mint a korábban megadott rendszergazdai jelszó, jelölje be a **fürt bejelentkezési jelszavának használata SSH** -hoz jelölőnégyzetet. Ha nem, adjon meg egy **jelszót** vagy egy **nyilvános kulcsot** az ssh-felhasználó hitelesítéséhez. A javasolt módszer a nyilvános kulcs. A hitelesítő adatok konfigurációjának mentéséhez kattintson az alján található **kiválasztás** gombra.  További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Erőforráscsoport|Adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni.|
    |Hely|Itt adhatja meg azt az adatközpontot, amelyben a fürt létrejött.|

    ![HDInsight létrehozása a fürt alapjaival](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Új fürt létrehozása a Azure Portalban")

    > [!IMPORTANT]  
    > A HDInsight-fürtök különféle típusúak. Azok a munkaterhelésnek vagy technológiának felelnek meg, amelyhez a fürt be van hangolva. Nincs támogatott módszer a több típust egyesítő fürt létrehozásához. Ilyenek például a Storm és a HBase egy fürtön.

    A **Tovább gombra kattintva lépjen a következő lapra** .

1. A **2. biztonsági és hálózati hálózatról**a megadott legördülő menü használatával a fürtöt a virtuális hálózathoz is összekapcsolhatja. Válasszon ki egy Azure-beli virtuális hálózatot és az alhálózatot, ha a fürtöt virtuális hálózatba kívánja helyezni. További információ a HDInsight virtuális hálózattal való használatáról: [virtuális hálózat központi telepítésének megtervezése Azure HDInsight-fürtökhöz](hdinsight-plan-virtual-network-deployment.md). A cikk a virtuális hálózat konkrét konfigurációs követelményeit tartalmazza.

    Ha a **Enterprise Security Packaget**szeretné használni, kövesse az alábbi utasításokat: [HDInsight-fürt konfigurálása Enterprise Security Packageekkel Azure Active Directory Domain Services használatával](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    A **Tovább gombra kattintva lépjen a következő lapra** .

1. **3 tárolóban**a **Storage-fiók beállításainál**adja meg, hogy az Azure Storage vagy a Azure Data Lake Storage alapértelmezett tárolóként kívánja-e használni. További információkért tekintse meg a következő táblázatot.

    | Elsődleges tároló típusa | Leírás |
    |------------------|-------------|
    | Azure Storage   |  * A **kiválasztási módszer**esetében válassza a **saját előfizetések** lehetőséget, ha olyan Storage-fiókot szeretne megadni, amely az Azure-előfizetéséhez tartozik. Ezután válassza ki a Storage-fiókot. Ellenkező esetben válassza a **hozzáférési kulcs**lehetőséget. Ezután adja meg az Azure-előfizetésen kívülről kiválasztani kívánt Storage-fiók adatait.</br></br> * Az **alapértelmezett tárolónál**válassza ki a portál által javasolt alapértelmezett nevet, vagy adja meg a sajátját.</br></br> * Ha az Azure Blob Storage az alapértelmezett tároló, a **további Storage-fiókok** lehetőség kiválasztásával további Storage-fiókokat adhat meg a fürthöz való hozzárendeléshez. Az **Azure Storage-kulcsok**esetében válassza **a Storage-kulcs hozzáadása**elemet. Ezután megadhat egy Storage-fiókot az Azure-előfizetésből vagy más előfizetésből. Adja meg a Storage-fiók elérési kulcsát.</br></br> * Ha a blob Storage az alapértelmezett tároló, akkor **Data Lake Storage hozzáférés** lehetőség kiválasztásával megadhatja a Azure Data Lake Storage további tárterületként. További információ [: gyors útmutató: fürtök beállítása a HDInsight-ben](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Azure Data Lake Storage | Válassza **Azure Data Lake Storage Gen1** vagy **Azure Data Lake Storage Gen2**lehetőséget. Ezután tekintse meg a következő cikket: rövid útmutató [: fürtök beállítása a HDInsight-ben](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) utasításokért. |

    **Metaadattár-beállítások (nem kötelező)**

    Lehetőségként megadhat egy SQL-adatbázist a fürthöz tartozó Apache Hive és Apache Oozie-metaadatok mentéséhez. **SQL-adatbázis kiválasztásához**válasszon ki egy SQL-adatbázist. Ezután adja meg az adatbázis felhasználónevét és jelszavát. Ismételje meg ezeket a lépéseket a Oozie-metaadatokhoz.

    Az Azure SQL Database metaadattárak való használatának néhány szempontja a következő:
    * A metaadattár használt Azure SQL Database-adatbázisnak engedélyeznie kell más Azure-szolgáltatásokhoz való kapcsolódást, beleértve az Azure HDInsight-t is. Az Azure SQL Database irányítópultjának jobb oldalán válassza ki a kiszolgáló nevét. Ez a kiszolgáló az a számítógép, amelyen az SQL Database-példány fut. A kiszolgáló nézetben válassza a **Konfigurálás**lehetőséget. Az **Azure-szolgáltatások**esetében válassza az **Igen**lehetőséget. Ezután válassza a **Save** (Mentés) lehetőséget.
    * Metaadattár létrehozásakor ne adjon meg kötőjeleket vagy kötőjeleket tartalmazó adatbázist. Ezek a karakterek a fürt létrehozási folyamatának sikertelenségét okozhatják.

    ![HDInsight létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Új fürt létrehozása a Azure Portalban")

    > [!WARNING]  
    > A HDInsight-fürttől eltérő helyen található további Storage-fiók használata nem támogatott.

    A **Tovább gombra kattintva lépjen a következő lapra** .

1. **4 alkalmazásból (nem kötelező)** válassza ki a kívánt alkalmazásokat. A Microsoft, a független szoftvergyártók (ISV-ket), vagy fejlesztheti ezeket az alkalmazásokat. További információ: [alkalmazások telepítése a fürt létrehozása során](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    A **Tovább gombra kattintva lépjen a következő lapra** .

1. **5 a fürt mérete** a fürthöz használt csomópontokkal kapcsolatos információkat jeleníti meg. Állítsa be a fürthöz szükséges munkavégző csomópontok számát. A fürt futtatásának becsült díja is látható.

    ![HDInsight létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Fürtcsomópontok számának meghatározása")

   > [!IMPORTANT]  
   > Ha több mint 32 feldolgozó csomópontot tervez, válasszon ki egy fő csomópont-méretet legalább nyolc maggal és 14 GB RAM-mal. Tervezze meg a csomópontokat a fürt létrehozásakor vagy a fürt méretezésével a létrehozás után.
   >
   > A csomópontok méretével és a kapcsolódó költségekkel kapcsolatos további információkért lásd az [Azure HDInsight díjszabását](https://azure.microsoft.com/pricing/details/hdinsight/).

    A **Tovább gombra kattintva lépjen a következő lapra** .

1. **6 parancsfájl-műveletből**testreszabhat egy fürtöt az egyéni összetevők telepítéséhez. Ez a beállítás akkor működik, ha egyéni parancsfájllal szeretné testre szabni a fürtöt, mivel a fürt létrehozása folyamatban van. További információ a parancsfájl-műveletekről: [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

   A **Tovább gombra kattintva lépjen a következő lapra** .

1. **7 összefoglalás**esetén ellenőrizze a korábban megadott adatokat. Ezután kattintson a **Létrehozás** elemre.

    ![HDInsight-fürt összesítésének létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Fürtcsomópontok számának meghatározása")

    > [!NOTE]  
    > Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. Figyelje az **értesítéseket** a kiépítési folyamat ellenőrzéséhez.

1. A létrehozási folyamat befejeződése után válassza az **Ugrás az erőforráshoz** lehetőséget az **üzembe helyezés sikeres** értesítése elemnél. A fürt ablak a következő információkat tartalmazza.

    ![A HDI Azure Portal-fürt áttekintése](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Fürt tulajdonságai")

    Az ablakban az alábbi ikonokat kell megmagyarázni:

    |Tulajdonság | Leírás |
    |---|---|
    |Áttekintés|A fürttel kapcsolatos összes lényeges információt tartalmazza. Ilyenek például a név, az erőforráscsoport, a hely, az operációs rendszer és a fürt irányítópultjának URL-címe.|
    |Fürt irányítópultjai|Irányítja a fürthöz társított Ambari-portálra.|
    |SSH + fürt bejelentkezés|A fürt SSH-kapcsolaton keresztüli eléréséhez szükséges információkat tartalmazza.|
    |Törlés|Törli a HDInsight-fürtöt.|

## <a name="customize-clusters"></a>Fürtök személyre szabása

* [HDInsight-fürtök testreszabása a bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>A fürt törlése

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Következő lépések

Sikeresen létrehozott egy HDInsight-fürtöt. Most megtudhatja, hogyan dolgozhat a fürttel.

### <a name="apache-hadoop-clusters"></a>Fürtök Apache Hadoop

* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase a HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-alkalmazások fejlesztése az Apache HBase a HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Fürtök Apache Storm

* [Java-topológiák fejlesztése a HDInsight Apache Storméhez](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata a HDInsight-ben Apache Storm](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése Apache Storm a HDInsight-on](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Fürtök Apache Spark

* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](spark/apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)
