---
title: Apache Hadoop-fürtök létrehozása webböngészővel, Azure HDInsight
description: Ismerje meg, hogyan hozhat létre Apache Hadoop, Apache HBase, Apache Storm vagy Apache Spark-fürtöket Linux for HDInsight-alapú webböngészőés az Azure Portal használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623183"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Linux alapú fürtök létrehozása a HDInsightban az Azure Portal használatával

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Az Azure Portal egy webalapú felügyeleti eszköz a Microsoft Azure-felhőben üzemeltetett szolgáltatások és erőforrások számára. Ebben a cikkben megtudhatja, hogyan hozhat létre Linux-alapú Azure HDInsight-fürtök et a portál használatával. További részletek a [HDInsight-fürtök létrehozása](./hdinsight-hadoop-provision-linux-clusters.md)szolgáltatásból származnak.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Az Azure Portal a fürt tulajdonságainak nagy részét elérhetővé teszi. Az Azure Resource Manager-sablonok használatával számos részletet elrejthet. További információ: [Apache Hadoop-fürtök létrehozása a HDInsightban erőforrás-kezelői sablonok használatával.](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="create-clusters"></a>Fürtök létrehozása

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. A felső menüben válassza a **+ Erőforrás létrehozása**lehetőséget.

    ![Új fürt létrehozása az Azure Portalon](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Új fürt létrehozása az Azure Portalon")

1. Válassza az **Analytics** > **Azure HDInsight** lehetőséget a **HDInsight-fürt létrehozása** lap megugrásához.

## <a name="basics"></a>Alapvető beállítások

![A HDInsight fürtök alapjainak létrehozása](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Új fürt létrehozása az Azure Portalon")

Az **Alapok** lapon adja meg a következő információkat:

|Tulajdonság |Leírás |
|---|---|
|Előfizetés|A legördülő listából válassza ki a fürthöz használt Azure-előfizetést.|
|Erőforráscsoport|A legördülő listában jelölje ki a meglévő erőforráscsoportot, vagy válassza **az Új létrehozása lehetőséget.**|
|Fürt neve|Adjon meg egy globálisan egyedi nevet.|
|Régió|A legördülő listából válassza ki azt a régiót, ahol a fürt létrejön.|
|Fürt típusa|Lista megnyitásához válassza **a Fürttípus kiválasztása** lehetőséget. A listából válassza ki a kívánt fürttípust. A HDInsight-fürtök számos típusban léteznek. Ezek megfelelnek a számítási feladatok vagy a technológia, amely a fürt van hangolva. Nincs támogatott módszer több típust egyesítő fürt létrehozására.|
|Verzió|A legördülő listából válasszon egy **verziót.** Használja az alapértelmezett verziót, ha nem tudja, mit válasszon. További tájékoztatás a [HDInsight cluster versions](hdinsight-component-versioning.md) (A HDInsight-fürtök verziói) című cikkben olvasható.|
|Fürt bejelentkezési felhasználóneve|Adja meg a felhasználónevet, az alapértelmezett **admin**.|
|Fürt bejelentkezési jelszava|Adja meg a jelszót.|
|Fürtbejelentkezési jelszó megerősítése|Írja be újra a jelszót|
|Secure Shell- (SSH-) felhasználónév|Adja meg a felhasználónevet, az alapértelmezett **sshuser**|
|Fürtbejelentkezési jelszó használata az SSH-hoz|Ha ugyanazt az SSH-jelszót szeretné használni, mint a korábban megadott rendszergazdai jelszó, jelölje be a **Fürt bejelentkezési jelszavának használata az SSH-hoz** jelölőnégyzetet. Ha nem, adjon meg **egy jelszót** vagy egy **nyilvános kulcsot** az SSH-felhasználó hitelesítéséhez. A nyilvános kulcs az általunk ajánlott megközelítés. A hitelesítő adatok konfigurációjának mentéséhez válassza az alul a **Kijelölés** lehetőséget.  További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](hdinsight-hadoop-linux-use-ssh-unix.md)|

Válassza a **Tovább: Storage >>** gombot a következő lapra való ugráshoz.

## <a name="storage"></a>Storage

![A HDInsight fürttárolót hoz létre](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Új fürt létrehozása az Azure Portalon – tárolás")

### <a name="primary-storage"></a>Elsődleges tárolás

Az **Elsődleges tárolótípus**legördülő listájából válassza ki az alapértelmezett tárolási típust. A következő kitölteni nek kitölteni a kiválasztott mezők a ttól függően változnak, hogy mi történt. **Azure Storage**esetén:

1. A **Kijelölés módszer listában**válassza a Kijelölés a **listából**vagy **a Hozzáférési kulcs használata lehetőséget.**
    * A **Kijelölés a listából listában**válassza ki az elsődleges **tárfiókot** a legördülő listából, vagy válassza az Új létrehozása **lehetőséget.**
    * A **Hozzáférési kulcs használata**esetén adja meg a **Tárfiók nevét.** Ezután adja meg az **Access kulcsot**.

1. A **Tároló mezőben**fogadja el az alapértelmezett értéket, vagy adjon meg egy újat.

### <a name="additional-azure-storage"></a>További Azure-tárhely

Nem kötelező: Válassza **az Azure Storage hozzáadása** további fürttároláshoz lehetőséget. A HDInsight-fürttől eltérő régióban egy további tárfiók használata nem támogatott.

### <a name="metastore-settings"></a>Metaáruház beállításai

Nem kötelező: Adjon meg egy meglévő SQL-adatbázist az Apache Hive, az Apache Oozie és/vagy az Apache Ambari metaadatok fürtön kívüli mentéséhez. A metastore-hoz használt Azure SQL-adatbázisnak engedélyeznie kell a kapcsolatot más Azure-szolgáltatásokkal, például az Azure HDInsight-mal. Metatároló létrehozásakor ne nevezzen el kötőjellel vagy kötőjellel rendelkező adatbázist. Ezek a karakterek a fürt létrehozási folyamatának sikertelensedését okozhatják.

Válassza a **Tovább: Biztonság + hálózati >>** lehetőséget a következő lapra való ugráshoz.

## <a name="security--networking"></a>Biztonság + hálózatépítés

![A HDInsight fürtbiztonsági hálózatok at hoz létre](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "A HDInsight fürtbiztonsági hálózatok at hoz létre")

A **Biztonság + hálózat** lapon adja meg a következő információkat:

|Tulajdonság |Leírás |
|---|---|
|Vállalati biztonsági csomag|Nem kötelező: Jelölje be a jelölőnégyzetet a **Vállalati biztonsági csomag**használatához. További információt a [HDInsight-fürt konfigurálása vállalati biztonsági csomaggal az Azure Active Directory tartományi szolgáltatások használatával című témakörben talál.](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)|
|TLS|Nem kötelező: Válasszon egy TLS-verziót a legördülő listából. További információ: [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security).|
|Virtuális hálózat|Nem kötelező: Válasszon ki egy meglévő virtuális hálózatot és alhálózatot a legördülő listából. További információt az [Azure HDInsight-fürtök virtuális hálózati üzembe helyezésének megtervezése című témakörben talál.](hdinsight-plan-virtual-network-deployment.md) A cikk a virtuális hálózat ra vonatkozó speciális konfigurációs követelményeket tartalmaz.|
|Lemeztitkosítási beállítások|Nem kötelező: Jelölje be a jelölőnégyzetet a titkosítás használatához. További információt az [Ügyfél által kezelt kulcslemez-titkosítás](./disk-encryption.md)című témakörben talál.|
|Kafka REST proxy|Ez a beállítás csak a Kafka fürttípusesetén érhető el. További információt a [REST-proxy használata](./kafka/rest-proxy.md)című témakörben talál.|
|Identitás|Nem kötelező: Válasszon ki egy meglévő, felhasználó által hozzárendelt szolgáltatásidentitást a legördülő listából. További információ: [Felügyelt identitások az Azure HDInsightban.](./hdinsight-managed-identities.md)|

Válassza a **Tovább: Konfiguráció + árképzés i >>** a következő lapra való továbbjutáshoz.

## <a name="configuration--pricing"></a>Konfiguráció + árképzés

![A HDInsight fürtkonfigurációt hoz létre](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Konfiguráció és árképzés lap")

A **Konfiguráció + árképzés** lapon adja meg a következő információkat:

|Tulajdonság |Leírás |
|---|---|
|+ Alkalmazás hozzáadása|Nem kötelező: Válassza ki a kívánt alkalmazásokat. Microsoft, független szoftverszállítók (ISV-k), vagy fejlesztheti ezeket az alkalmazásokat. További információt az [Alkalmazások telepítése a fürt létrehozása során](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)című témakörben talál.|
|Csomópont mérete|Nem kötelező: Válasszon másik méretű csomópontot.|
|Csomópontok száma|Nem kötelező: Adja meg a megadott csomóponttípus csomópontjainak számát. Ha 32-nél több munkavégző csomópontot tervez, válasszon ki egy legalább nyolc maggal és 14 GB RAM-mal rendelkező főcsomópontméretet. Tervezze meg a csomópontokat a fürt létrehozásakor vagy a fürt létrehozása utáni méretezésével.|
|Automatikus skálázás engedélyezése|Nem kötelező: A funkció engedélyezéséhez jelölje be a jelölőnégyzetet. További információt az [Azure HDInsight-fürtök automatikus méretezése című témakörben talál.](./hdinsight-autoscale-clusters.md)|
|+ Add script művelet|Nem kötelező: Ez a beállítás akkor működik, ha egyéni parancsfájlt szeretne használni a fürt testreszabásához, miközben a fürt jön létre. A parancsfájlműveletekről a [Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek használatával című](hdinsight-hadoop-customize-cluster-linux.md)témakörben talál további információt.|

Válassza **a Véleményezés + létrehozás >>** lehetőséget a fürtkonfiguráció érvényesítéséhez és a végső lapra való továbbjutáshoz.

## <a name="review--create"></a>Véleményezés + létrehozás

![A HDInsight fürtösszegzést hoz létre](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Fürtcsomópontok számának megadása")

Tekintse át a beállításokat. A fürt létrehozásához kattintson a **Létrehozás** gombra.

Némi időt vesz igénybe a fürt létrehozása, általában körülbelül 20 percet. **Értesítések** figyelése a kiépítési folyamat ellenőrzéséhez.

## <a name="post-creation"></a>Közzététel létrehozása

A létrehozási folyamat befejezése után válassza az Ugrás az **erőforrásra** lehetőséget a **telepítés sikeres értesítésében.** A fürtablak a következő információkat tartalmazza.

![HDI Azure Portal-fürt – áttekintés](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Fürt tulajdonságai")

Az ablakban lévő ikonok némelyike a következőképpen magyarázható:

|Tulajdonság | Leírás |
|---|---|
|Áttekintés|A fürttel kapcsolatos összes lényeges információt tartalmazza. Ilyen például a fürt irányítópultjának neve, erőforráscsoportja, a helye, az operációs rendszere és az URL-címe.|
|Fürtirányítópultjai|A fürthöz társított Ambari portálra irányítja.|
|SSH + Fürt bejelentkezés|A fürt SSH használatával történő eléréséhez szükséges információkat tartalmazza.|
|Törlés|Törli a HDInsight-fürtöt.|

## <a name="customize-clusters"></a>Fürtök testreszabása

* [A HDInsight-fürtök testreszabása a Bootstrap használatával](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Linux-alapú HDInsight-fürtök testreszabása parancsfájlműveletek használatával](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>A fürt törlése

Lásd: [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával.](./hdinsight-delete-cluster.md)

## <a name="troubleshoot"></a>Hibaelhárítás

Ha problémába ütközik a HDInsight-fürtök létrehozása során, tekintse meg [a hozzáférés-vezérlésre vonatkozó követelményeket](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>További lépések

Sikeresen létrehozott egy HDInsight-fürtöt. Most már megtanulják, hogyan kell dolgozni a fürt.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop-fürtök

* [Az Apache Hive használata a HDInsight segítségével](hadoop/hdinsight-use-hive.md)
* [A MapReduce használata a HDInsightsegítségével](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-fürtök

* [Ismerkedés az Apache HBase-lel a HDInsight-on](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java alkalmazások fejlesztése Apache HBase alkalmazáshoz a HDInsight-on](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm-fürtök

* [Java topológiák fejlesztése apache stormhoz a HDInsighton](storm/apache-storm-develop-java-topology.md)
* [Python-összetevők használata az Apache Storm ban a HDInsighton](storm/apache-storm-develop-python-topology.md)
* [Topológiák üzembe helyezése és figyelése az Apache Storm segítségével a HDInsighton](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark-fürtök

* [Önálló alkalmazás létrehozása a Scala használatával](spark/apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark bi-val: Interaktív adatelemzés végezhet a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](spark/apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](spark/apache-spark-machine-learning-mllib-ipython.md)
