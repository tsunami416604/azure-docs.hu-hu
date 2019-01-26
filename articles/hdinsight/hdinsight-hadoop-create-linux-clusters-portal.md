---
title: Az Apache Hadoop-fürtök létrehozása a webböngésző, az Azure HDInsight használatával
description: Ismerje meg, hogyan hozhat létre Apache Hadoop, az Apache HBase, Apache Storm vagy az Apache Spark-fürtök Linux rendszeren a HDInsight egy webböngészőt, és az Azure betekintő portál használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: 13d053c14b083390bfdd28fdad616caf13e7e4e0
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911557"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>HDInsight Linux-alapú fürtök létrehozása az Azure portal használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az Azure Portalon egy webalapú felügyeleti eszköz, szolgáltatások és erőforrások a Microsoft Azure-felhő üzemeltet. Ebből a cikkből megismerheti, hogyan hozhat létre Linux-alapú Azure HDInsight-fürtök a portál használatával.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [tesztelési a Hadoop HDInsight az Azure ingyenes próbaverzió beszerzése](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A modern webböngésző**. Az Azure Portalon a HTML5- és JavaScript használ. Ez nem működik megfelelően régebbi böngészőkben.

## <a name="create-clusters"></a>Fürtök létrehozása
Az Azure Portalon a tulajdonságait a legtöbb tesz elérhetővé. Az Azure Resource Manager-sablonok használatával elrejtheti számos részletet. További információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight használatával a Resource Manager-sablonok](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza ki a **+ erőforrás létrehozása**.

1.  A **Azure Marketplace-en**válassza **Analytics**.

1.  A **kiemelt**válassza **HDInsight**.
   
    ![Hozzon létre egy új fürtöt az Azure Portalon](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "új fürt létrehozása az Azure Portalon")

1. Az a **HDInsight** lapon jelölje be **egyéni (méret, beállítások, alkalmazások)**.

1. Válassza ki **1 alapjai**. Majd adja meg a következő információkat.

    ![Konfigurálja az alapbeállításokat](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "új fürt létrehozása az Azure Portalon")

    * Adja meg a **fürt neve**. A névnek globálisan egyedinek kell lennie.

    * Az a **előfizetés** legördülő listára, válassza ki az Azure-előfizetést, amely a fürt szolgál.

    * Válassza ki **fürt típusa**. Ezután válassza ki a létrehozni kívánt fürt típusát. Példák az Apache Spark és Hadoop. A **operációs rendszer** lesz **Linux**. Ezután válassza ki a fürt alkalmazástípus-verzió. Alapértelmezett verzióját használja, ha nem tudja, melyik a megfelelő. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.
     
        > [!IMPORTANT]  
        > HDInsight fürtöket a többféle típusú származnak. A számítási feladatok vagy technológia, amely a fürt van hangolva megfelelnek. Nincs támogatott módszer, amely ötvözi az több típusok fürtöt létrehozni. Példák a Storm és HBase egy fürtön is.
        
    * A **fürt bejelentkezési felhasználóneve** és **fürt bejelentkezési jelszavának**, adja meg a rendszergazdai felhasználó felhasználónevét és jelszavát.

    * Adjon meg egy **SSH-felhasználónév**. Ha azt szeretné, hogy az azonos SSH-jelszó korábban megadott rendszergazdai jelszót, válassza ki a **használja ugyanazt a jelszót, mint a fürtbe való bejelentkezésekor** jelölőnégyzetet. Ha nem, adja meg, vagy egy **jelszó** vagy **nyilvános kulcs** az SSH-felhasználó hitelesítéséhez. A nyilvános kulcs az ajánlott módszer. Válasszon **kiválasztása** alján, a hitelesítő adatok konfigurációjának mentéséhez.
   
        További információkért lásd: [HDInsight (az Apache Hadoop) SSH használatával csatlakozhat](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Az **Erőforráscsoport** alatt adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni.

    * Adja meg az egy kínai adatközpont **hely** ahol a fürt létrejött.

    * Válassza ki **tovább** áthelyezése a következő lapra.

4. A **2 biztonság és a hálózatkezelés**, csatlakozhat a fürthöz egy virtuális hálózatot a megadott legördülő menü használatával. Válassza ki a Azure-beli virtuális hálózathoz és az alhálózatot, ha el szeretné helyezni a fürt egy virtuális hálózatban. Információk a HDInsight használata a virtuális hálózaton: [HDInsight kiterjesztése képességek az Azure Virtual Network használatával](hdinsight-extend-hadoop-virtual-network.md). A cikk tartalmazza a virtuális hálózathoz megadott konfigurációs követelményekkel. 

    Ha használni szeretné a **vállalati biztonsági csomag**, kövesse az alábbi utasításokat: [A HDInsight-fürt konfigurálása a vállalati biztonsági csomaggal az Azure Active Directory Domain Services használatával](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Válassza ki **tovább** áthelyezése a következő lapra.


5. A **3 tárolási**, adja meg, hogy az Azure Storage vagy az Azure Data Lake Storage az alapértelmezett tárolóként. További információkért lásd az alábbi táblázatot.

     ![Tárolási beállítások megadása](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "új fürt létrehozása az Azure Portalon")

     | Storage                                      | Leírás |
     |----------------------------------------------|-------------|
     | **Az Azure Storage-blobokkal, az alapértelmezett tárolóként**   | <ul><li>A **elsődleges tárolási típusok**válassza **Azure Storage**. A **kijelöléséről**, válassza a **saját előfizetések** Ha szeretné az Azure-előfizetés részét képező tárfiókot adjon meg. Ezután válassza ki a tárfiókot. Ellenkező esetben válassza **hívóbetű**. Ezután adja meg a tárfiók kívül az Azure-előfizetés válassza ki a kívánt adatokat.</li><li>A **alapértelmezett tároló**, válassza ki a portál által javasolt alapértelmezett tároló nevét, vagy saját maga hozza létre.</li><li>Az alapértelmezett tároló Azure Blob storage esetén is kiválaszthatja **további Tárfiókok** társítása a fürt további tárfiókok megadására. A **Azure-Tárkulcsok**válassza **tárkulcs hozzáadása**. Ezután megadhat egy storage-fiókot az Azure-előfizetésekre vagy más előfizetésekből származó. Adja meg a tárfiók hozzáférési kulcsát.</li><li>Ha az alapértelmezett tároló, Blob storage lehetőség kiválasztásával **Data Lake Storage access** , adja meg az Azure Data Lake Storage kiegészítő tárolóként. További információkért lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Az Azure Data Lake Storage az alapértelmezett tárolóként** | A **elsődleges tárolási típusok**válassza **Azure Data Lake Storage Gen1** vagy **Azure Data Lake Storage Gen2**. Ezután tekintse meg a cikk [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) útmutatást. |
     | **Külső metaadattárak**                      | Szükség esetén adja meg az SQL-adatbázis, a fürthöz társított az Apache Hive- és Apache Oozie-metaadatok mentése. A **SQL-adatbázis kiválasztása a Hive**, válasszon ki egy SQL-adatbázist. Adja meg felhasználónevét és jelszavát az adatbázisban. Ismételje meg ezeket a lépéseket Oozie-metaadatok.<br><br>Azure SQL database segítségével metaadattárakat kapcsolatos szempontokat a következők: <ul><li>Az Azure SQL database a metaadattár használt más Azure-szolgáltatásokkal, mint az Azure HDInsight való csatlakozást engedélyezhetik a. Az Azure SQL database irányítópult jobb oldalán válassza ki a kiszolgáló nevét. Ezen a kiszolgálón, amely futtatja az SQL database-példány. Kiszolgáló megtekintése közben, válassza ki **konfigurálása**. Ezután a **Azure-szolgáltatások**válassza **Igen**. Ezután válassza a **Save** (Mentés) lehetőséget.</li><li>A metaadattár létrehozásakor ne nevezze egy adatbázis kötőjeleket vagy kötőjelet tartalmazhat. Ezek a karakterek okozhat a Fürtlétrehozási folyamat sikertelen lesz.</li></ul> |

     > [!WARNING]  
     > További storage-fiók használatával, mint a HDInsight-fürt egy másik helyen található nem támogatott.

     Válassza ki **tovább** áthelyezése a következő lapra.


6. A **4 alkalmazások (opcionális)**, válassza ki a kívánt alkalmazásokat. A Microsoft, független szoftverszállítók (ISV), vagy ezeket az alkalmazásokat fejleszthet. További információkért lásd: [alkalmazások telepítése fürtlétrehozás során](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Válassza ki **tovább** áthelyezése a következő lapra.


6. **5 fürtméret** a csomópontok, a fürt által használt információkat jelenít meg. Állítsa be, amelyekre szüksége van a fürt munkavégző csomópontok számát. A fürt futtatása becsült költsége is látható.
   
    ![Adja meg a csomópont árképzési szintjei](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "adja meg a fürt csomópontok száma")
   
   > [!IMPORTANT]  
   > Ha több mint 32 munkavégző csomópontokhoz szeretne, válassza ki a fő csomópont méretének legalább 8 maggal és 14 GB RAM-MAL rendelkező. Tervezze meg a fürt létrehozáskor vagy a fürt létrehozása után felskálázásával csomópontok. 
   > 
   > A csomópontok méretét és a kapcsolódó költségek további információkért lásd: [Azure HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Válassza ki **tovább** áthelyezése a következő lapra.

8. A **6 szkriptműveletek**, testre szabhatja a fürt egyéni összetevők telepítéséhez. Ez a beállítás akkor működik, ha szeretne egyéni parancsfájl használata a fürt testreszabása, a fürt létre lesz hozva. A szkriptműveletek kapcsolatos további információkért lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

   Válassza ki **tovább** áthelyezése a következő lapra.

9. A **7 összefoglalás**, ellenőrizze a korábban megadott adatokat. Ezután kattintson a **Létrehozás** elemre.

     ![Konfigurációk megerősítése](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "adja meg a fürt csomópontok száma")
    
    > [!NOTE]  
    > Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. A figyelő **értesítések** ellenőrizze a kiépítési folyamat.

10. A létrehozási folyamat befejezése után jelölje ki a **erőforrás megnyitása** származó a **üzembe helyezés sikeres** értesítést. A fürt ablakban a következő adatokat tartalmazza.
    
    ![Fürt felület](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "fürt tulajdonságai")
    
    Az ikonok az ablakban vannak az alábbiak:
    
    * A **áttekintése** lap a fürttel kapcsolatos alapvető adatokat tartalmaz. Példák a nevét, az erőforráscsoportot, amelyhez tartozik, a hely, az operációs rendszer és a fürt irányítópultja URL-CÍMÉT.
    * **Irányítópult** a fürthöz társított Ambari portálra irányítja.
    * **Secure Shell** SSH használatával a fürt eléréséhez szükséges információkat biztosít.
    * Használatával **fürt méretezése**, növelheti a fürthöz társított munkavégző csomópontok számát.
    * **Törlés** törli a HDInsight-fürtöt.
    

## <a name="customize-clusters"></a>Fürtök személyre szabása
* [HDInsight-fürtök testreszabása a Bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Linux-alapú HDInsight-fürtök testreszabása szkriptműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>A fürt törlése
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>További lépések
Sikeresen létrehozott egy HDInsight-fürtön. Most megtudhatja, hogyan működik a fürttel.

### <a name="apache-hadoop-clusters"></a>Az Apache Hadoop-fürtök
* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
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
* [Feladatok távoli futtatása az Apache Spark-fürt Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Az Apache Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)

