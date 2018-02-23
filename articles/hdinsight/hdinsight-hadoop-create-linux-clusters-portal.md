---
title: "Egy webböngészővel - Azure HDInsight Hadoop-fürtök létrehozása |} Microsoft Docs"
description: "Útmutató a Hadoop, HBase, Storm vagy Spark-fürtök létrehozása Linux rendszeren a HDInsight egy webes böngésző és az Azure betekintő portál használatával."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 697278cf-0032-4f7c-b9b2-a84c4347659e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 6601e93cf3ff3a97c5c77f5fdff712fe28aff795
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Linux-alapú fürtök létrehozása a Hdinsightban az Azure portál használatával
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az Azure portál egy olyan webalapú felügyeleti eszköz a Microsoft Azure felhőalapú erőforrások és szolgáltatások. Ebből a cikkből megismerheti a portál használata Linux-alapú HDInsight-fürtök létrehozása.

## <a name="prerequisites"></a>Előfeltételek
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Modern webböngésző**. Az Azure-portálon HTML5-ös és a Javascript használ, és esetleg nem működnek megfelelően korábbi verziójú böngészőt.

## <a name="create-clusters"></a>Fürtök létrehozása
Az Azure-portálon a legtöbb fürt tulajdonságait mutatja. Azure Resource Manager-sablonnal, számos részletet elrejtése. További információkért lásd: [létrehozása Linux-alapú Hadoop-fürtök használata Azure Resource Manager-sablonok hdinsight](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre.
2. Kattintson a  **+** , kattintson a **Eszközintelligencia + analitika**, és kattintson a **HDInsight**.
   
    ![Új fürt létrehozása az Azure portálon](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "új fürt létrehozása az Azure-portálon")

3. A a **HDInsight** panelen kattintson a **(mérete, beállítások, alkalmazásokat) egyéni**, kattintson a **alapjai**, és írja be a következő információkat.

    ![Új fürt létrehozása az Azure portálon](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "új fürt létrehozása az Azure-portálon")

    * A **Fürt neve** mezőben adja meg a fürt nevét, amelynek globálisan egyedinek kell lennie.

    * Az a **előfizetés** legördülő listából válassza ki az Azure-előfizetés, amely a fürt.

    * Kattintson a **típusú fürt**, majd válassza ki a típust (Hadoop, Spark, stb.) a fürt létrehozásához. A **operációs rendszer**, kattintson a **Linux** és majd a megfelelő verzió kiválasztása. Az alapértelmezett verzióját használja, ha nem tudja, mit válassza. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.

        A Hadoop, Spark és interaktív lekérdezés fürttípusok, igény szerint kiválaszthatja telepítéséhez a **vállalati biztonsági csomag**. A vállalati biztonsági csomag lehetővé teszi, hogy a fürtök például az Azure Active Directory-integrációval és Apache Pletyka biztonsági funkciói. További információkért lásd: [vállalati biztonsági csomag Azure hdinsight](./domain-joined/apache-domain-joined-introduction.md).

        ![Engedélyezi a vállalati biztonsági csomag](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-enable-enterprise-security-package.png "engedélyezése a vállalati biztonsági csomag")
     
        > [!IMPORTANT]
        > HDInsight fürtök típusokat, amelyek megfelelnek a munkaterhelés vagy technológia, amely a fürt úgy van beállítva, a különböző származnak. Nincs támogatott módszer, amely többféle, például a Storm és HBase egy fürtön fürt létrehozásához. 
        > 
        > 
        
    * A **fürt bejelentkezési felhasználónevének** és **fürt bejelentkezési jelszó**, adja meg a felhasználónevet és jelszót a rendszergazdai felhasználó.

    * Adjon meg egy **SSH felhasználónév** és, ha azt szeretné, hogy az SSH-jelszónak ugyanaz, mint a korábban megadott rendszergazdai jelszó, jelölje be a **ugyanazt a jelszót használják a fürt bejelentkezési** jelölőnégyzetet. Ha nem, adja meg, vagy egy **jelszó** vagy **nyilvános kulcs**, az SSH-felhasználó hitelesítéséhez használni. A Microsoft a nyilvános kulcs használatát javasolja. A hitelesítő adatok konfigurációjának mentéséhez kattintson az oldal alján található **Kiválasztás** elemre.
   
    További információk: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Az **Erőforráscsoport** alatt adja meg, hogy új erőforráscsoportot kíván-e létrehozni, vagy egy meglévőt szeretne használni.

    * Adjon meg egy adott adatközpont **hely** ahol a fürt létrehozását.

    * Kattintson a **Tovább** gombra.

4. A **tárolási**, adja meg, hogy Azure Storage (WASB) vagy a Data Lake Store az alapértelmezett tárolóként. Tekintse meg az alábbi táblázatban további információt.

    ![Új fürt létrehozása az Azure portálon](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "új fürt létrehozása az Azure-portálon")

    | Tárolás                                      | Leírás |
    |----------------------------------------------|-------------|
    | **Az Azure Storage Blobs alapértelmezett tárolóként**   | <ul><li>A **elsődleges tárolótípus**, jelölje be **Azure Storage**. Ezt követően a **kijelöléséről**, dönthet úgy **a saját előfizetések** Ha szeretné-e adjon meg egy tárfiókot, amely része az Azure-előfizetéshez, és válassza ki a tárfiók. Ellenkező esetben kattintson a **hozzáférési kulcs** és adja meg a tárfiók kívül az Azure-előfizetéshez válassza ki a kívánt információkat.</li><li>A **alapértelmezett tároló**, nyissa meg a portál által javasolt alapértelmezett tároló névvel, vagy adjon meg egy saját választhat.</li><li>Ha a WASB alapértelmezett tárolóként használ, (opcionális) kattintson **további Tárfiókok** adhatja meg a fürt rendelje hozzá a további tárfiókok. A **Azure Storage kulcsok**, kattintson a **tárolási kulcs hozzáadása**, és megadhatja a storage-fiókok Azure-előfizetését, illetve más előfizetések (azáltal, hogy a fiók tárelérési kulcs).</li><li>WASB alapértelmezett tárolóként használ, ha (opcionális) kattintson **Data Lake Store hozzáférés** való további tárhely az Azure Data Lake Store beállításához. További információkért lásd: [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).</li></ul> |
    | **Azure Data Lake Store alapértelmezett tárolóként** | A **elsődleges tárolótípus**, jelölje be **Data Lake Store** majd tekintse át a cikk [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) utasításokat. |
    | **Külső metaadattárakat**                      | Szükség esetén a fürt társítva struktúra és az Oozie-metaadatok mentése SQL-adatbázis is megadhat. A **jelöljön ki egy SQL-adatbázist a Hive** jelöljön ki egy SQL-adatbázist, és a felhasználónév/jelszó adja meg az adatbázisban. Ismételje ezeket a lépéseket az Oozie-metaadatok.<br><br>Azt is számításba kell metastores az Azure SQL-adatbázis használata során. <ul><li>Az Azure SQL-adatbázis a metaadattárhoz használt engedélyezniük kell az és más Azure-szolgáltatásokkal, beleértve az Azure HDInsight. Az Azure SQL adatbázis irányítópultra, a jobb oldalon kattintson a kiszolgáló nevére. Ez az a kiszolgáló, amelyen fut az SQL-adatbázispéldány. Ha a nézetet, kattintson **konfigurálása**, és majd a **Azure Services**, kattintson a **Igen**, és kattintson a **mentése**.</li><li>Egy metaadattárhoz létrehozásakor ne használja az adatbázis nevét, amely tartalmazza a kötőjelek és kötőjeleket tartalmazhat, mivel ez is okozhatja, hogy a Fürtlétrehozási folyamat sikertelen lesz.</li></ul> |

    Kattintson a **Tovább** gombra. 

    > [!WARNING]
    > A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

5. Ha úgy gondolja, a **alkalmazások** a HDInsight-fürtök együtt használható alkalmazások telepítéséhez. Ezek az alkalmazások lehetnek a Microsoft, független szoftvergyártók (ISV-k) vagy a felhasználók fejlesztései. További információkért lásd: [telepítése HDInsight-alkalmazások](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Kattintson a **a fürt méretét** a csomópontokat ehhez a fürthöz használt kapcsolatos információk megjelenítéséhez. Adjon meg, amelyekre szüksége van a fürt munkavégző csomópontokhoz. A becsült költség, a fürtön futó is látható.
   
    ![Tarifacsomagok csomópont](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "adja meg a fürtcsomópontok számára")
   
   > [!IMPORTANT]
   > Ha azt tervezi, több mint 32 munkavégző csomópont, vagy a fürt létrehozásakor, vagy a fürt létrehozása után skálázással majd ki kell választania egy átjárócsomóponttal mérete legalább 8 maggal és 14 GB RAM-MAL.
   > 
   > A csomópont-méretek és a társuló költségeket további információkért lásd: [HDInsight árképzési](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Kattintson a **tovább** a csomópont árképzési konfiguráció mentéséhez.

7. Kattintson a **speciális beállítások** egyéb választható beállításainak használatával például **Parancsfájlműveletek** testreszabása egyéni összetevők vagy az összekötő telepítése egy fürt egy **virtuális hálózati**. Tekintse meg az alábbi táblázatban további információt.

    ![Tarifacsomagok csomópont](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-advanced.png "adja meg a fürtcsomópontok számára")

    | Beállítás | Leírás |
    |--------|-------------|
    | **A Parancsfájlműveletek** | Használja ezt a beállítást, ha azt szeretné, hogy egy egyéni parancsfájl segítségével testre szabhatja a fürt a fürt létrehozását. A Parancsfájlműveletek kapcsolatos további információkért lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md). |
    | **Virtual Network** | Válassza ki a egy Azure virtuális hálózatra és az alhálózatot, ha el szeretné helyezni a fürt virtuális hálózatba. Információ a HDInsight használata a virtuális hálózaton, többek között a virtuális hálózat, megadott konfigurációs követelményekkel kapcsolatban: [kiterjesztése HDInsight képességek az Azure virtuális hálózat használatával](hdinsight-extend-hadoop-virtual-network.md). |

    Kattintson a **Tovább** gombra.

8. A **összegzés**, ellenőrizze a korábban megadott adatokat, és kattintson a **létrehozása**.

    ![Tarifacsomagok csomópont](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "adja meg a fürtcsomópontok számára")
    
    > [!NOTE]
    > Némi időbe, a fürt hozható létre, általában körülbelül 15 percet vesz igénybe. A csempe használja a kezdőpult alján, vagy a **értesítések** bal oldalán a lapot annak ellenőrzéséhez, a telepítési folyamatot a bejegyzést.
    > 
    > 
12. Miután a létrehozási folyamat befejeződött, kattintson a kezdőpultról a fürthöz tartozó csempére. A fürt ablakban a következő adatokat tartalmazza.
    
    ![Fürt felület](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "fürt tulajdonságai")
    
    A következő segítségével azonosíthatja a ikonok tetején.
    
    * **Áttekintés** lapon a nevét, az erőforráscsoport tartozik, a hely, az operációs rendszer, a fürt irányítópult stb URL-címe például a fürt összes alapvető információt nyújt.
    * **Irányítópult** irányítja az Ambari portal a fürthöz rendelt.
    * **Biztonságos rendszerhéj**: SSH használata a fürt eléréséhez szükséges információkat.
    * **Skála fürt** megadható, hogy növeli a társított a fürt feldolgozó csomópontjainak számát.
    * **Törlés**: a HDInsight-fürt törlése.
    

## <a name="customize-clusters"></a>Fürtök személyre szabása
* Lásd: [testreszabása HDInsight-fürtök használata rendszerindítási](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Lásd: [testreszabása HDInsight-fürtök használata parancsfájlművelet](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>A fürt törlése
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>További lépések
Most, hogy sikeresen létrehozott egy HDInsight-fürtre, használja a következő áttekintésével megismerheti, hogyan használható a fürt:

### <a name="hadoop-clusters"></a>Hadoop-fürtök
* [A Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
* [A Pig használata a HDInsightban](hadoop/hdinsight-use-pig.md)
* [Use MapReduce with HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase-fürtökkel
* [A HDInsighton futó HBase használatának első lépései](hbase/apache-hbase-tutorial-get-started-linux.md)
* [A HDInsight HBase Java-alkalmazások fejlesztése](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-fürtök
* [Java-topológiák fejlesztése hdinsight alatt futó Storm](storm/apache-storm-develop-java-topology.md)
* [A HDInsight alatt futó Storm Python-összetevőket használnak](storm/apache-storm-develop-python-topology.md)
* [Telepítheti és figyelheti a HDInsight alatt futó Storm topológiák](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>A Spark-fürtök
* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](spark/apache-spark-machine-learning-mllib-ipython.md)

