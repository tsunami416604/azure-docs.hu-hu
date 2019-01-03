---
title: Egy böngészőben – Azure HDInsight az Apache Hadoop-fürtök létrehozása
description: Ismerje meg, az Apache Hadoop, az Apache HBase, Apache Storm vagy az Apache Spark-fürtök létrehozása Linux rendszeren a HDInsight egy webböngészőt, és az Azure betekintő portál használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974267"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Linux-alapú fürtök létrehozása a HDInsight az Azure portal használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az Azure Portalon egy webalapú felügyeleti eszköz, szolgáltatások és erőforrások a Microsoft Azure-felhő üzemeltet. Ebben a cikkben megismerheti, hogyan hozhat létre Linux-alapú HDInsight-fürtök a portál használatával.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A modern webböngésző**. Az Azure Portalon használja a HTML5-alapú és a Javascript, és előfordulhat, hogy nem működik megfelelően régebbi böngészőkben.

## <a name="create-clusters"></a>Fürtök létrehozása
Az Azure Portalon a tulajdonságait a legtöbb tesz elérhetővé. Azure Resource Manager-sablonokkal, számos Részletek elrejtése. További információkért lásd: [Linux-alapú Apache Hadoop-fürtök a HDInsight az Azure Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **+ erőforrás létrehozása**.

1.  A **Azure Marketplace-en**válassza **Analytics**.

1.  A **kiemelt**válassza **HDInsight**.
   
    ![Új fürt létrehozása az Azure Portalon](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "új fürt létrehozása az Azure Portalon")

1. Az a **HDInsight** lapon jelölje be **egyéni (méret, beállítások, alkalmazások)**.

1. Válassza ki **1 alapjai**, majd adja meg a következő információkat:

    ![Új fürt létrehozása az Azure Portalon](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "új fürt létrehozása az Azure Portalon")

    * Adja meg **fürt neve**: A névnek globálisan egyedinek kell lennie.

    * Az a **előfizetés** legördülő menüben válassza ki az Azure-előfizetést, amely a fürt szolgál.

    * Válassza ki **fürt típusa**, majd válassza ki a létrehozni kívánt fürt (Hadoop, Spark, stb.). A **operációs rendszer** lesz **Linux**. Ezután válassza ki a fürt alkalmazástípus-verzió. Alapértelmezett verzióját használja, ha nem tudja, melyik a megfelelő. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.
     
        > [!IMPORTANT]  
        > HDInsight fürtök különböző típusú, amelyek megfelelnek a számítási feladatok vagy technológia, amely a fürt van hangolva származnak. Nincs támogatott módszer, amely ötvözi az több típus, például a Storm és a egy fürtön a HBase-fürt létrehozásához.
        
    * A **fürt bejelentkezési felhasználóneve** és **fürt bejelentkezési jelszavának**, adja meg a rendszergazdai felhasználó felhasználónevét és jelszavát.

    * Adjon meg egy **SSH-felhasználónév** , és ha szeretné engedélyezni az SSH-jelszó ugyanaz, mint a korábban megadott rendszergazdai jelszót, jelölje be a **használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzetet. Ha nem, adja meg, vagy egy **jelszó** vagy **nyilvános kulcs**, az SSH-felhasználó hitelesítéséhez használandó. A Microsoft a nyilvános kulcs használatát javasolja. A hitelesítő adatok konfigurációjának mentéséhez kattintson az oldal alján található **Kiválasztás** elemre.
   
        További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Az **Erőforráscsoport** alatt adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni.

    * Adjon meg egy adatközpont **hely** ahol a fürt létrejött.

    * Válassza ki **tovább** áthelyezése a következő lapra.

4. A **2 biztonság és a hálózatkezelés**, a megadott legördülő menüben virtuális hálózathoz is csatlakoztathatja a fürtjét. Válassza ki a Azure-beli virtuális hálózathoz és az alhálózatot, ha el szeretné helyezni a fürt egy virtuális hálózatban. HDInsight használatával egy virtuális hálózathoz, beleértve a virtuális hálózathoz megadott konfigurációs követelményekkel kapcsolatos információkat lásd: [HDInsight kiterjesztése képességek az Azure Virtual Network használatával](hdinsight-extend-hadoop-virtual-network.md). Ha használni szeretné a **vállalati biztonsági csomag**, is követheti az itt leírt utasításokat: [A HDInsight-fürt konfigurálása a vállalati biztonsági csomaggal az Azure Active Directory Domain Services használatával](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Válassza ki **tovább** áthelyezése a következő lapra.


5. A **3 tárolási**, adja meg, hogy az Azure Storage (WASB) vagy a Data Lake Storage az alapértelmezett tárolóként. Tekintse meg az alábbi táblázatban további információt.

     ![Új fürt létrehozása az Azure Portalon](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "új fürt létrehozása az Azure Portalon")

     | Storage                                      | Leírás |
     |----------------------------------------------|-------------|
     | **Az Azure Storage-Blobokkal, az alapértelmezett tároló**   | <ul><li>A **elsődleges tárolási típusok**válassza **Azure Storage**. Ezt követően a **kijelöléséről**, választhat **saját előfizetések** szeretné-e az Azure-előfizetés részét képező tárfiókot adjon meg, és jelölje ki a tárfiókot. Ellenkező esetben kattintson a **hívóbetű** , és adja meg az adatokat a tárfiók, válassza ki a kívánt kívül az Azure-előfizetés.</li><li>A **alapértelmezett tároló**, ha szeretné, lépjen a portál által javasolt alapértelmezett tároló nevét, vagy saját maga hozza létre.</li><li>Ha a WASB használja alapértelmezett tárolóként, (opcionálisan) kattintson **további Tárfiókok** társítása a fürt további tárfiókok megadására. A **Azure-Tárkulcsok**, kattintson a **tárkulcs hozzáadása**, és ezután megadhat egy storage-fiókot az Azure-előfizetésekre vagy más előfizetésekből származó (azáltal, hogy a tárfiók hozzáférési kulcsát).</li><li>Ha a WASB használja alapértelmezett tárolóként, (opcionálisan) kattintson **Data Lake Storage access** , adja meg az Azure Data Lake Storage kiegészítő tárolóként. További információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Az Azure Data Lake Storage alapértelmezett tárolóként** | A **elsődleges tárolási típusok**válassza **Azure Data Lake Storage Gen1** vagy **Azure Data Lake Storage Gen2** , majd a cikk [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) útmutatást. |
     | **Külső metaadattárak**                      | Szükség esetén az SQL-adatbázis, a fürthöz társított Hive-val és az Oozie-metaadatok mentése is megadhat. A **SQL-adatbázis kiválasztása a Hive** SQL-adatbázis kiválasztása, és adja meg a felhasználónév/jelszó az adatbázishoz. Ismételje meg ezeket a lépéseket Oozie-metaadatok.<br><br>Néhány szempontot metaadattárakat az Azure SQL database használatakor. <ul><li>Az Azure SQL database, a metaadattár használt más Azure-szolgáltatásokkal, mint az Azure HDInsight való csatlakozást engedélyezhetik. Az Azure SQL database irányítópulton a jobb oldalon kattintson a kiszolgálónévre. Ez az a kiszolgáló, amelyen az SQL database-példány fut-e. Miután a nézetet, kattintson a **konfigurálása**, majd **Azure-szolgáltatások**, kattintson a **Igen**, és kattintson a **mentése**.</li><li>Amikor metaadattárának, ne használja az adatbázis nevét, amely tartalmazza a kötőjelet vagy kötőjelet tartalmazhat, ez okozhat a Fürtlétrehozási folyamat sikertelen lesz.</li></ul> |

     > [!WARNING]  
     > A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

     Válassza ki **tovább** áthelyezése a következő lapra.


6. A **4 alkalmazások (opcionális)**, válassza ki bármelyik kívánt alkalmazásokat.  Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései. További információkért lásd: [telepítése HDInsight-alkalmazások](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Válassza ki **tovább** áthelyezése a következő lapra.


6. **5 fürtméret** a csomópontok, a fürt által használt információkat jelenít meg. Állítsa be, amelyekre szüksége van a fürt munkavégző csomópontok számát. A fürt futtatása becsült költsége is látható.
   
    ![Csomópont árképzési szintjei](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "adja meg a fürt csomópontok száma")
   
   > [!IMPORTANT]  
   > Ha azt tervezi, hogy több mint 32 feldolgozó csomópontokat, a fürt létrehozáskor vagy a fürt létrehozása után felskálázásával majd választania kell egy fő csomópont mérete legalább 8 maggal és 14 GB RAM-MAL rendelkező.
   > 
   > További információ a csomópontméretekről és a velük járó költségekről: [A HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Válassza ki **tovább** áthelyezése a következő lapra.

8. A **6 szkriptműveletek**, testre szabhatja a fürt egyéni összetevők telepítéséhez.  Használja ezt a beállítást, ha azt szeretné, egyéni parancsfájl használata a fürt testreszabása, a fürt létre lesz hozva. A szkriptműveletek kapcsolatos további információkért lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

   Válassza ki **tovább** áthelyezése a következő lapra.

9. A **7 összefoglalás**, ellenőrizze a korábban megadott adatokat, majd **létrehozás**.

     ![Csomópont árképzési szintjei](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "adja meg a fürt csomópontok száma")
    
    > [!NOTE]  
    > Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. A figyelő **értesítések** ellenőrizze a kiépítési folyamat.

10. A létrehozási folyamat befejezése után jelölje ki a **erőforrás megnyitása** származó a **üzembe helyezés sikeres** értesítést. A fürt ablakban a következő adatokat tartalmazza.
    
    ![Fürt felület](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "fürt tulajdonságai")
    
    A következő segítségével megismerheti az ikonok tetején.
    
    * **Áttekintés** lapon például a nevét, az erőforráscsoportot, amelyhez tartozik, a helyet, az operációs rendszer, a fürt irányítópultja stb URL-CÍMÉT a fürt összes nélkülözhetetlen információkat nyújt.
    * **Irányítópult** a fürthöz társított Ambari portálra irányítja.
    * **Secure Shell**: A fürthöz SSH használatával eléréséhez szükséges információkat.
    * **Fürt méretezése** lehetővé teszi, hogy a fürthöz társított munkavégző csomópontok számának növelése.
      * **Törlés**: Törli a HDInsight-fürtöt.
    

## <a name="customize-clusters"></a>Fürtök személyre szabása
* Lásd: [Bootstrap használatával testre szabhatja a HDInsight-fürtök](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Lásd: [testreszabása HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>A fürt törlése
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>További lépések
Most, hogy sikeresen létrehozott egy HDInsight-fürtöt, megtudhatja, hogyan használható a fürt a következő használatával:

### <a name="apache-hadoop-clusters"></a>Az Apache Hadoop-fürtök
* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [A HDInsight UseApache Pig](hadoop/hdinsight-use-pig.md)
* [A MapReduce használata a HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Az Apache HBase-fürtök
* [A HDInsight Apache HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Az Apache HBase on HDInsight Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök
* [Java-topológiák fejlesztése a HDInsight az Apache stormmal](storm/apache-storm-develop-java-topology.md)
* [Az Apache Storm on HDInsight használata a Python-összetevők](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight Apache Storm-topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Az Apache Spark-fürtök
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](spark/apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)

