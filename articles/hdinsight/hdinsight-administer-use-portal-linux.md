---
title: Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portalon
description: Ismerje meg, hogyan hozhat létre és kezelhet Azure HDInsight-fürtöket az Azure Portalon.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/12/2020
ms.openlocfilehash: b9d923b3272f9d8b3da39d7cdb771a766eee4eab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272733"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Apache Hadoop-fürtök kezelése a HDInsightban az Azure Portal használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Az [Azure Portal használatával](https://portal.azure.com)kezelheti az [Apache Hadoop-fürtöket](https://hadoop.apache.org/) az Azure HDInsightban. A fenti lapválasztóval a Hadoop-fürtök HDInsight-ban más eszközökkel történő kezeléséről nyújt tájékoztatást.

## <a name="prerequisites"></a>Előfeltételek

Meglévő Apache Hadoop-fürt a HDInsightban.  Lásd: [Linux-alapú fürtök létrehozása a HDInsightban az Azure Portalon](hdinsight-hadoop-create-linux-clusters-portal.md)keresztül.

## <a name="getting-started"></a>Első lépések

Jelentkezzen be [https://portal.azure.com](https://portal.azure.com)a ikonra.

## <a name="list-and-show-clusters"></a><a name="showClusters"></a>Fürtök listázása és megjelenítése

A **HDInsight-fürtök** lap felsorolja a meglévő fürtöket.  A portálról:
1. Válassza a bal oldali menü **Minden szolgáltatás lehetőséget.**
2. Az **ANALYTICS**csoportban válassza **a HDInsight-fürtöket.**

## <a name="cluster-home-page"></a><a name="homePage"></a>Fürt kezdőlapja

Válassza ki a fürt nevét a [**HDInsight-fürtök**](#showClusters) lapon.  Ez megnyitja az **Áttekintő** nézetet, amely az alábbi képhez hasonlóan néz ki:

![Az Azure Portal HDInsight-fürt alapvető fontosságú](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Felső menü:**  

| Elem| Leírás |
|---|---|
|Áthelyezés|Áthelyezi a fürtöt egy másik erőforráscsoportba vagy egy másik előfizetésbe.|
|Törlés|Törli a fürtöt. |
|Frissítés|Frissíti a nézetet.|

**Bal oldali menü:**  

  - **Bal felső menü**

    | Elem| Leírás |
    |---|---|
    |Áttekintés|Általános információkat tartalmaz a fürthöz.|
    |Tevékenységnapló|Tevékenységnaplók megjelenítése és lekérdezése.|
    |Hozzáférés-vezérlés (IAM)|Szerepkör-hozzárendelések használata.  Lásd: [Szerepkör-hozzárendelések használata az Azure-előfizetési erőforrásokhoz való hozzáférés kezeléséhez.](../role-based-access-control/role-assignments-portal.md)|
    |Címkék|Lehetővé teszi kulcs-/értékpárok beállítását a felhőszolgáltatások egyéni taxonómia meghatározásához. Létrehozhat például egy **projekt**nevű kulcsot, majd egy közös értéket használhat egy adott projekthez társított összes szolgáltatáshoz.|
    |Problémák diagnosztizálása és megoldása|Hibaelhárítási információk megjelenítése.|
    |Első lépések|Olyan információkat jelenít meg, amelyek segítenek a HDInsight használatának megkezdésében.|
    |Eszközök|Súgóinformációk a HDInsight-val kapcsolatos eszközökhöz.|

  - **Beállítások menü**  

    | Elem| Leírás |
    |---|---|
    |Fürt mérete|Ellenőrizze, növelje és csökkentse a fürt munkavégző csomópontjainak számát. Lásd: [Fürtök méretezése](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kvótakorlátok|Az előfizetéshez használt és rendelkezésre álló magok megjelenítése.|
    |SSH + Fürt bejelentkezés|A fürthöz secure shell (SSH) kapcsolaton keresztül történő csatlakozásra vonatkozó utasításokat jeleníti meg. További információ: [SSH használata a HDInsight segítségével.](hdinsight-hadoop-linux-use-ssh-unix.md)|
    |1. generációs Data Lake Storage|Konfigurálja a data lake storage gen1-et.  Lásd: [Gyorsútmutató: Fürtök beállítása a HDInsightban.](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)|
    |Tárfiókok|Tekintse meg a tárfiókokat és a kulcsokat. A tárfiókok a fürt létrehozási folyamata során vannak konfigurálva.|
    |Alkalmazások|HDInsight-alkalmazások hozzáadása/eltávolítása.  Lásd: [Egyéni HDInsight-alkalmazások telepítése.](hdinsight-apps-install-custom-applications.md)|
    |Parancsfájl-műveletek|Futtassa a Bash parancsfájlokat a fürtön. Lásd: [Linux-alapú HDInsight-fürtök testreszabása parancsfájlművelettel.](hdinsight-hadoop-customize-cluster-linux.md)|
    |Külső metaboltok|Tekintse meg az [Apache Hive](https://hive.apache.org/) és [az Apache Oozie](https://oozie.apache.org/) metaáruházait. A metaboltok csak a fürt létrehozási folyamata során konfigurálhatók.|
    |HDInsight-partner|Adja hozzá/távolítsa el az aktuális HDInsight-partnert.|
    |Tulajdonságok|A [fürt tulajdonságainak](#properties)megtekintése .|
    |Zárolások|A fürt módosításának vagy törlésének megakadályozása érdekében adjon hozzá zárolást.|
    |Sablon exportálása|Az Azure Resource Manager sablon megjelenítése és exportálása a fürthöz. Jelenleg csak a függő Azure storage-fiók exportálható. Lásd: [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsightban az Azure Resource Manager-sablonok használatával.](hdinsight-hadoop-create-linux-clusters-arm-templates.md)|

  - **Figyelés menü**

    | Elem| Leírás |
    |---|---|
    |Riasztások|A riasztások és műveletek kezelése.|
    |Mérőszámok|Figyelje a fürt metrikák az Azure Monitor naplók.|
    |Diagnosztikai beállítások|Beállítások a diagnosztikai mutatók tárolására.|
    |Azure Monitor|A fürt figyelése az Azure Monitorban.|

  - **Támogatás + hibaelhárítás menü**

    | Elem| Leírás |
    |---|---|
    |Erőforrás állapota|Tekintse meg [az Azure-erőforrások állapotának áttekintését.](../service-health/resource-health-overview.md)|
    |Új támogatási kérelem|Lehetővé teszi támogatási jegy létrehozását a Microsoft támogatásával.|

## <a name="cluster-properties"></a><a name="properties"></a>Fürt tulajdonságai

A [fürt kezdőlapján](#homePage)a **Beállítások** csoportban válassza a **Tulajdonságok lehetőséget.**

|Elem | Leírás |
|---|---|
|Hostname|Fürt neve.|
|FÜRT URL-CÍME|Az Ambari webes felület URL-címe.|
|Privát végpont|A fürt privát végpontja.|
|Biztonságos héj (SSH)|A fürt SSH-n keresztüli eléréséhez használandó felhasználónév és állomásnév.|
|ÁLLAPOT|Az egyik: Megszakítva, Elfogadva, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, Működési, Futás, Hiba, Törlés, Törölt, Időhelyes bírás, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ÁtméretezésVárólistába vagy ClusterCustomization.|
|RÉGIÓ|Az Azure helye. A támogatott Azure-helyek listáját a [HDInsight-díjszabás](https://azure.microsoft.com/pricing/details/hdinsight/) **Régió** legördülő listájában láthatja.|
|LÉTREHOZÁS DÁTUMA|A fürt üzembe helyezésének dátuma.|
|OPERÁCIÓS RENDSZER|Vagy **Windows** vagy **Linux**.|
|TÍPUS|Hadoop, HBase, Vihar, Szikra.|
|Verzió|Lásd [HDInsight verziók](hdinsight-component-versioning.md).|
|Minimális TLS-verzió|A TLS verzió.|
|ELŐFIZETÉS|Előfizetés neve.|
|ALAPÉRTELMEZETT ADATFORRÁS|Az alapértelmezett fürtfájlrendszer.|
|Dolgozói csomópontok méretei|A munkavégző csomópontok kijelölt virtuálisgép-mérete.|
|Átjárócsomópont mérete|A fejcsomópontok kijelölt virtuális gépmérete.|
|Virtuális hálózat|A fürt által üzembe helyezett virtuális hálózat neve, ha a központi telepítéskor ki lett választva.|

## <a name="move-clusters"></a>Fürtök áthelyezése

Áthelyezhet egy HDInsight-fürtöt egy másik Azure-erőforráscsoportba vagy egy másik előfizetésbe.

A [fürt kezdőlapjáról:](#homePage)

1. Válassza a felső menü **Mozgás** parancsát.
2. Válassza **az Áthelyezés másik erőforráscsoportba** vagy az Áthelyezés másik **előfizetésre**lehetőséget.
3. Kövesse az új oldal utasításait.

## <a name="delete-clusters"></a>Fürtök törlése

A fürt törlése nem törli az alapértelmezett tárfiókot, sem a kapcsolódó tárfiókokat. A fürt újra létrehozható az azonos tárfiókok és ugyanazokat a metaáruházakat használva. Javasoljuk, hogy a fürt újbóli létrehozásakor használjon egy új alapértelmezett Blob-tárolót.

A [fürt kezdőlapjáról:](#homePage)

1. Válassza a felső menü **Törlés parancsát.**
2. Kövesse az új oldal utasításait.

Lásd még: [Fürtök szüneteltetése/leállítása](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

A fürt létrehozása után további Azure Storage-fiókokat és Azure Data Lake Storage-fiókokat adhat hozzá. További információkért lásd: [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="scale-clusters"></a>Fürtök méretezése

A fürtméretezési szolgáltatás lehetővé teszi az Azure HDInsight-fürt által használt munkavégző csomópontok számának módosítását anélkül, hogy újra létre kellene hoznia a fürtöt.

A [HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md) című témakörben talál teljes körű tájékoztatást.

## <a name="pauseshut-down-clusters"></a>Fürtök felfüggesztése/leállítása

A Hadoop-feladatok többsége olyan kötegelt feladat, amely csak alkalmanként fut. A legtöbb Hadoop-fürt esetében nagy idő alatt nem használja a fürt feldolgozásra. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban.
A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

A folyamatot számos módon programozhatja:

- Felhasználói Azure Data Factory. Lásd: [Igény szerinti Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsightban az Azure Data Factory használatával](hdinsight-hadoop-create-linux-clusters-adf.md) igény szerinti HDInsight-alapú szolgáltatások létrehozásához.
- Használja az Azure PowerShellt.  Lásd: [A járat késési adatainak elemzése](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
- Használja az Azure CLI-t. Lásd: [Azure HDInsight-fürtök kezelése az Azure CLI használatával.](hdinsight-administer-use-command-line.md)
- Használja a HDInsight .NET SDK-t. Lásd: [Apache Hadoop-feladatok küldése.](hadoop/submit-apache-hadoop-jobs-programmatically.md)

Az árképzési információkért lásd: [HDInsight-díjszabás.](https://azure.microsoft.com/pricing/details/hdinsight/) Fürt törlése a portálról: [Fürtök törlése](#delete-clusters)

## <a name="upgrade-clusters"></a>Fürtök frissítése

Lásd: [HDInsight-fürt frissítése újabb verzióra.](./hdinsight-upgrade-cluster.md)

## <a name="open-the-apache-ambari-web-ui"></a>Az Apache Ambari webes felhasználói felületének megnyitása

Az Ambari intuitív, könnyen használható Hadoop felügyeleti webes felhasználói felületet biztosít, amelyet a RESTful API-k támogatnak. Az Ambari lehetővé teszi a rendszergazdák számára a Hadoop-fürtök kezelését és figyelését.

A [fürt kezdőlapjáról:](#homePage)

1. Válassza **a Fürt-irányítópultok lehetőséget.**

    ![A HDInsight Apache Hadoop fürtmenüje](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Válassza az **Ambari kezdőlapját** az új lapon.
1. Adja meg a fürt felhasználónevét és jelszavát.  A fürt alapértelmezett felhasználóneve _a rendszergazda_.

További információt a [HDInsight-fürtök kezelése az Apache Ambari Web felhasználói felületének használatával](hdinsight-hadoop-manage-ambari.md)című témakörben talál.

## <a name="change-passwords"></a>Jelszavak módosítása

Egy HDInsight-fürt nek két felhasználói fiókja lehet. A HDInsight fürt felhasználói fiókja (HTTP felhasználói fiók) és az SSH felhasználói fiók a létrehozási folyamat során jön létre. A portál segítségével módosíthatja a fürt felhasználói fiók jelszavát, és parancsfájlműveleteket az SSH felhasználói fiók módosításához.

### <a name="change-the-cluster-user-password"></a>A fürt felhasználói jelszavának módosítása

> [!NOTE]  
> A fürt felhasználói (rendszergazdai) jelszavának módosítása a fürtön futó parancsfájlműveletek sikertelensedést okozhat. Ha olyan megőrzött parancsfájlműveleteket hajt végre, amelyek feldolgozócsomópontokat céloznak meg, ezek a parancsfájlok sikertelenek lehetnek, ha csomópontokat ad hozzá a fürthöz átméretezési műveletek en keresztül. A parancsfájlműveletekről a [HDInsight-fürtök testreszabása parancsfájlműveletek használatával című](hdinsight-hadoop-customize-cluster-linux.md)témakörben talál további információt.

A [fürt kezdőlapjáról:](#homePage)
1. Válassza az **SSH + Cluster bejelentkezés lehetőséget** a Beállítások **területen.**
2. Válassza **a Hitelesítő adatok visszaállítása lehetőséget.**
3. Írja be és erősítse meg az új jelszót a szövegmezőkben.
4. Válassza **az OK gombot.**

A jelszó a fürt összes csomópontján módosul.

### <a name="change-the-ssh-user-password"></a>Az SSH felhasználói jelszavának módosítása

1. Szövegszerkesztő használatával mentse a következő szöveget **changepassword.sh**nevű fájlként.

    > [!IMPORTANT]  
    > Olyan szerkesztőt kell használnia, amely az LF-et használja sorvégződésként. Ha a szerkesztő CRLF-et használ, akkor a szkript nem működik.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Töltse fel a fájlt egy tárolóhelyre, amely a HDInsightból HTTP- vagy HTTPS-cím használatával érhető el. Például egy nyilvános fájltároló, például a OneDrive vagy az Azure Blob storage. Mentse az URI-t (HTTP vagy HTTPS-cím) a fájlba, mivel erre az URI-ra a következő lépésben van szükség.
3. A [fürt kezdőlapján](#homePage)válassza a **Parancsfájlműveletek** lehetőséget a **Beállítások csoportban.**
4. A **Parancsfájl-műveletek** lapon válassza az **Új küldése lehetőséget.**
5. A **Parancsfájl küldése műveletlapon** adja meg a következő adatokat:

   | Mező | Érték |
   | --- | --- |
   | Parancsfájl típusa | Válassza ki **a – Egyéni** elemet a legördülő listából.|
   | Név |"Ssh jelszó módosítása" |
   | Bash parancsfájl URI |A changepassword.sh fájl URI-ja |
   | Csomóponttípus(ok): (fej, dolgozó, Nimbus, felügyelő vagy Zookeeper.) |✓ az összes felsorolt csomóponttípushoz |
   | Paraméterek |Adja meg az SSH felhasználónevét, majd az új jelszót. A felhasználónév és a jelszó között egy szóköznek kell lennie. |
   | Őrizd meg ezt a parancsfájlműveletet ... |Hagyja bejelölve ezt a mezőt. |

6. A parancsfájl alkalmazásához válassza a **Létrehozás** gombot. Miután a parancsfájl befejeződött, ssh használatával csatlakozhat a fürthöz az új jelszóval.

## <a name="find-the-subscription-id"></a>Az előfizetés-azonosító megkeresése

Minden fürt egy Azure-előfizetéshez van kötve.  Az Azure-előfizetés-azonosító a [fürt kezdőlapjáról](#homePage)látható.

## <a name="find-the-resource-group"></a>Az erőforráscsoport megkeresése

Az Azure Resource Manager módban minden HDInsight-fürt egy Azure Resource Manager-csoporttal jön létre. Az Erőforrás-kezelő csoport a [fürt kezdőlapjáról](#homePage)látható.

## <a name="find-the-storage-accounts"></a>A tárfiókok megkeresése

A HDInsight-fürtök azure storage-fiókot vagy Azure Data Lake Storage-t használnak az adatok tárolásához. Minden HDInsight-fürt rendelkezhet egy alapértelmezett tárfiókkal és számos csatolt tárfiókkal. A tárfiókok listázásához a [fürt kezdőlapján,](#homePage) a **Beállítások**csoportban válassza a **Tárfiókok lehetőséget.**

## <a name="monitor-jobs"></a>Feladatok figyelése

Lásd: [HDInsight-fürtök kezelése az Apache Ambari Web felhasználói felületének használatával.](hdinsight-hadoop-manage-ambari.md#monitoring)

## <a name="cluster-size"></a>Fürt mérete

A [fürt kezdőlapjáról](#homePage) származó **fürtméret** csempe megjeleníti a fürthöz lefoglalt magok számát és a fürt önkiszolgáló csomópontjaiszámára való lefoglalásmódját.

> [!IMPORTANT]  
> A HDInsight-fürt által nyújtott szolgáltatások figyeléséhez az Ambari Web vagy az Ambari REST API-t kell használnia. Az Ambari használatáról további információt a [HDInsight-fürtök kezelése az Apache Ambari használatával című](hdinsight-hadoop-manage-ambari.md) témakörben talál.

## <a name="connect-to-a-cluster"></a>Csatlakozás fürthöz

- [Az Apache Hive használata a HDInsight segítségével](hadoop/apache-hadoop-use-hive-ambari-view.md)
- [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>További lépések

Ebben a cikkben néhány alapvető felügyeleti funkciót ismertet. További információ: a következő cikkek:

- [HDInsight felügyelete az Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
- [HDInsight felügyelete az Azure CLI használatával](hdinsight-administer-use-command-line.md)
- [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
- [Az Apache Ambari REST API használatának részletei](hdinsight-hadoop-manage-ambari-rest-api.md)
- [Az Apache Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
- [Az Apache Sqoop használata a HDInsightban](hadoop/hdinsight-use-sqoop.md)
- [Python-felhasználó által definiált függvények (UDF) használata Apache Hive-val és Apache Pig-rel a HDInsightban](hadoop/python-udf-hdinsight.md)
- [Az Apache Hadoop melyik verziója található az Azure HDInsightban?](hdinsight-component-versioning.md)
