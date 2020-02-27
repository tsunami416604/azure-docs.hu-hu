---
title: Apache Hadoop-fürtök létrehozása webböngésző használatával, Azure HDInsight
description: Megtudhatja, hogyan hozhat létre Apache Hadoop-, Apache HBase-, Apache Storm-vagy Apache Spark-fürtöket Linux rendszeren a HDInsight webböngészővel és a Azure Portal használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623183"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

A Azure Portal egy webalapú felügyeleti eszköz a Microsoft Azure felhőben üzemeltetett szolgáltatásokhoz és erőforrásokhoz. Ebből a cikkből megtudhatja, hogyan hozhat létre Linux-alapú Azure HDInsight-fürtöket a portál használatával. További részletek állnak rendelkezésre a [HDInsight-fürtök létrehozásával](./hdinsight-hadoop-provision-linux-clusters.md)kapcsolatban.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

A Azure Portal a fürt legtöbb tulajdonságát elérhetővé teszi. Azure Resource Manager sablonok használatával számos részletet elolvashat. További információ: [Apache Hadoop-fürtök létrehozása a HDInsight-ben Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-clusters"></a>Fürtök létrehozása

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).

1. A felső menüben válassza az **+ erőforrás létrehozása**lehetőséget.

    ![Új fürt létrehozása a Azure Portalban](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Új fürt létrehozása a Azure Portalban")

1. Válassza az **elemzési** > **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megjelenítéséhez.

## <a name="basics"></a>Alapvető beállítások

![HDInsight létrehozása a fürt alapjaival](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Új fürt létrehozása a Azure Portalban")

Az **alapok** lapon adja meg a következő információkat:

|Tulajdonság |Leírás |
|---|---|
|Előfizetést|A legördülő listában válassza ki a fürthöz használt Azure-előfizetést.|
|Erőforráscsoport|A legördülő listából válassza ki a meglévő erőforráscsoportot, vagy válassza az **új létrehozása**lehetőséget.|
|Fürt neve|Adjon meg egy globálisan egyedi nevet.|
|Régió|A legördülő listából válassza ki azt a régiót, ahol a fürtöt létrehozták.|
|Fürt típusa|A lista megnyitásához válassza a **fürt típusának kiválasztása** lehetőséget. A listából válassza ki a kívánt típusú fürtöt. A HDInsight-fürtök különféle típusúak. Azok a munkaterhelésnek vagy technológiának felelnek meg, amelyhez a fürt be van hangolva. Nincs támogatott módszer a több típust egyesítő fürt létrehozásához.|
|Verzió|A legördülő listából válassza ki a kívánt **verziót**. Ha nem tudja, mit kell választania, használja az alapértelmezett verziót. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.|
|Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett érték a **rendszergazda**.|
|Fürt bejelentkezési jelszava|Adja meg a jelszót.|
|Fürt bejelentkezési jelszavának megerősítése|Írja be újra a jelszót|
|Secure Shell- (SSH-) felhasználónév|Adja meg a felhasználónevet, az alapértelmezett érték a **sshuser** .|
|Fürt bejelentkezési jelszavának használata SSH-hoz|Ha ugyanazt az SSH-jelszót szeretné használni, mint a korábban megadott rendszergazdai jelszó, jelölje be a **fürt bejelentkezési jelszavának használata SSH** -hoz jelölőnégyzetet. Ha nem, adjon meg egy **jelszót** vagy egy **nyilvános kulcsot** az ssh-felhasználó hitelesítéséhez. A javasolt módszer a nyilvános kulcs. A hitelesítő adatok konfigurációjának mentéséhez kattintson az alján található **kiválasztás** gombra.  További információ: [Kapcsolódás HDInsight (Apache Hadoop) SSH használatával](hdinsight-hadoop-linux-use-ssh-unix.md).|

Válassza a **Tovább: Storage > >** a következő lapra való továbblépés előtt.

## <a name="storage"></a>Tárterület

![HDInsight létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Új fürt létrehozása a Azure Portal-Storage-ban")

### <a name="primary-storage"></a>Elsődleges tárterület

Az **elsődleges tároló típusa**legördülő listából válassza ki az alapértelmezett tárolási típust. A következő mezők a kiválasztástól függően változhatnak. **Azure Storage**esetén:

1. A **kiválasztási módszer**esetében válassza **a kiválasztás listából lehetőséget**, vagy **használja a hozzáférési kulcsot**.
    * Válassza ki a **listából**az **elsődleges Storage-fiókot** a legördülő listából, vagy válassza az **új létrehozása**lehetőséget.
    * A **hozzáférési kulcs használatához**adja meg a **Storage-fiók nevét**. Ezután adja meg a **hozzáférési kulcsot**.

1. A **tároló**mezőben fogadja el az alapértelmezett értéket, vagy adjon meg egy újat.

### <a name="additional-azure-storage"></a>További Azure Storage

Nem kötelező: válassza az **Azure Storage hozzáadása** lehetőséget a fürt további tárhelyéhez. A HDInsight-fürttől eltérő régióban található további Storage-fiók használata nem támogatott.

### <a name="metastore-settings"></a>Metaadattár-beállítások

Nem kötelező: egy meglévő SQL Database megadásával mentheti Apache Hive, Apache Oozie és/vagy Apache Ambari metaadatokat a fürtön kívül. A metaadattár használt Azure SQL Databasenak engedélyeznie kell más Azure-szolgáltatásokhoz való kapcsolódást, beleértve az Azure HDInsight-t is. Metaadattár létrehozásakor ne adjon meg kötőjeleket vagy kötőjeleket tartalmazó adatbázist. Ezek a karakterek a fürt létrehozási folyamatának sikertelenségét okozhatják.

Válassza a **Tovább: biztonság és hálózatkezelés > >** a következő lapra való továbblépés előtt.

## <a name="security--networking"></a>Biztonság és hálózatkezelés

![HDInsight-fürt biztonsági hálózatkezelésének létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight-fürt biztonsági hálózatkezelésének létrehozása")

A **Biztonság és hálózat** lapon adja meg a következő információkat:

|Tulajdonság |Leírás |
|---|---|
|Vállalati biztonsági csomag|Nem kötelező: jelölje be a jelölőnégyzetet a **Enterprise Security Package**használatához. További információ: HDInsight- [fürt konfigurálása Enterprise Security Package használatával Azure Active Directory Domain Services használatával](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Nem kötelező: válasszon ki egy TLS-verziót a legördülő listából. További információ: [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Virtuális hálózat|Nem kötelező: válasszon ki egy meglévő virtuális hálózatot és alhálózatot a legördülő listából. További információ: [virtuális hálózat központi telepítésének megtervezése az Azure HDInsight-fürtökhöz](hdinsight-plan-virtual-network-deployment.md). A cikk a virtuális hálózat konkrét konfigurációs követelményeit tartalmazza.|
|Lemez titkosítási beállításai|Nem kötelező: jelölje be a jelölőnégyzetet a titkosítás használatához. További információ: [ügyfél által felügyelt kulcs lemezének titkosítása](./disk-encryption.md).|
|Kafka REST proxy|Ez a beállítás csak a Kafka típusú fürtök esetében érhető el. További információ: [Rest proxy használata](./kafka/rest-proxy.md).|
|Identitás|Nem kötelező: válasszon ki egy meglévő felhasználó által hozzárendelt szolgáltatás-identitást a legördülő listából. További információ: [felügyelt identitások az Azure HDInsight-ben](./hdinsight-managed-identities.md).|

Válassza a **Next (tovább): Configuration + díjszabás > >** a következő lapra való továbblépés előtt.

## <a name="configuration--pricing"></a>Konfiguráció és díjszabás

![HDInsight létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Konfiguráció és díjszabás lap")

A **konfiguráció + díjszabás** lapon adja meg a következő információkat:

|Tulajdonság |Leírás |
|---|---|
|+ Alkalmazás hozzáadása|Nem kötelező: válassza ki a kívánt alkalmazásokat. A Microsoft, a független szoftvergyártók (ISV-ket), vagy fejlesztheti ezeket az alkalmazásokat. További információ: [alkalmazások telepítése a fürt létrehozása során](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Csomópont mérete|Nem kötelező: válasszon másik méretű csomópontot.|
|Csomópontok száma|Nem kötelező: adja meg a csomópontok számát a megadott csomópont-típushoz. Ha több mint 32 feldolgozó csomópontot tervez, válasszon ki egy fő csomópont-méretet legalább nyolc maggal és 14 GB RAM-mal. Tervezze meg a csomópontokat a fürt létrehozásakor vagy a fürt méretezésével a létrehozás után.|
|Az autoskálázás engedélyezése|Nem kötelező: jelölje be a jelölőnégyzetet a funkció engedélyezéséhez. További információ: az [Azure HDInsight-fürtök automatikus méretezése](./hdinsight-autoscale-clusters.md).|
|+ Parancsfájl hozzáadása művelet|Nem kötelező: Ez a beállítás akkor működik, ha egyéni parancsfájllal szeretné testre szabni a fürtöt, mivel a fürt létrehozása folyamatban van. További információ a parancsfájl-műveletekről: [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).|

A fürtkonfiguráció ellenőrzéséhez és a végső lapra való továbblépés előtt válassza a **felülvizsgálat + > > létrehozása** lehetőséget.

## <a name="review--create"></a>Felülvizsgálat + létrehozás

![HDInsight-fürt összesítésének létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Fürtcsomópontok számának meghatározása")

Tekintse át a beállításokat. A fürt létrehozásához kattintson a **Létrehozás** gombra.

Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. Figyelje az **értesítéseket** a kiépítési folyamat ellenőrzéséhez.

## <a name="post-creation"></a>Létrehozás utáni

A létrehozási folyamat befejeződése után válassza az **Ugrás az erőforráshoz** lehetőséget az **üzembe helyezés sikeres** értesítése elemnél. A fürt ablak a következő információkat tartalmazza.

![A HDI Azure Portal-fürt áttekintése](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Fürt tulajdonságai")

Az ablakban az alábbi ikonokat kell megmagyarázni:

|Tulajdonság | Leírás |
|---|---|
|Áttekintés|A fürttel kapcsolatos összes lényeges információt tartalmazza. Ilyenek például a név, az erőforráscsoport, a hely, az operációs rendszer és a fürt irányítópultjának URL-címe.|
|Fürt irányítópultjai|Irányítja a fürthöz társított Ambari-portálra.|
|SSH + fürt bejelentkezés|A fürt SSH-kapcsolaton keresztüli eléréséhez szükséges információkat tartalmazza.|
|Törlés|Törli a HDInsight-fürtöt.|

## <a name="customize-clusters"></a>Fürtök testreszabása

* [HDInsight-fürtök testreszabása a bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Linux-alapú HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>A fürt törlése

Lásd: [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

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
