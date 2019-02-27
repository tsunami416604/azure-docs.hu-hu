---
title: Az Azure portal használatával HDInsight az Apache Hadoop-fürtök kezelése
description: Megtudhatja, hogyan hozhat létre és kezelhet a HDInsight-fürtök az Azure portal használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: hrasheed
ms.openlocfilehash: 8fccf82371225acd5839537f7629ffe7df138d43
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56888151"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>A HDInsight Apache Hadoop-fürtök kezelése az Azure portal használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Használatával a [az Azure portal][azure-portal], kezelheti [Apache Hadoop](https://hadoop.apache.org/) Azure HDInsight-fürtök. Információ más eszközök használatával HDInsight Hadoop-fürtök kezelése a fenti lapon választómezőt használja.

**Előfeltételek**
- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Meglévő Apache Hadoop-fürt a HDInsight.  Lásd: [a HDInsight az Azure portal használatával Linux-alapú fürtök](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Első lépések
Jelentkezzen be itt: [https://portal.azure.com](https://portal.azure.com).


## <a name="showClusters"></a> Fürtök listázása és megjelenítése
A **HDInsight-fürtök** lapon megjelennek a meglévő fürtök.  A portálról:
1. Válassza ki **minden szolgáltatás** a bal oldali menüből.
2. Válassza ki **HDInsight-fürtök** alatt **ANALYTICS**.

## <a name="homePage"></a> Fürt kezdőlapja 
Válassza ki a fürt nevét, a [ **HDInsight-fürtök** oldal](#showClusters).  Ekkor megnyílik a **áttekintése** nézetet, amely a következő képhez hasonlóan néz ki:

![Az Azure portal HDInsight fürt alapjai](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Felső menüben:**  
- **Helyezze át**: A fürt helyez, másik erőforráscsoportba, illetve egy másik előfizetésbe.  
- **Törlés**: Törli a fürtöt.  
- **Frissítés**:  A nézet frissítése.

**Bal oldali menüben:**  
 - **Bal felső menü**  
    - **Áttekintés**:  Általános információk a fürt számára.
    -  **Tevékenységnapló**: Tevékenységnaplók lekérdezése és megjelenítése.
    - **Hozzáférés-vezérlés (IAM)**: Szerepkör-hozzárendelésekkel.  Lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése a szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md).
    - **A címkék**: Lehetővé teszi kulcs-érték párok meghatározásához egy egyéni besorolás, a cloud Services szolgáltatások beállítását. Például előfordulhat, hogy létre nevű kulcs **projekt**, majd használja az adott projekthez tartozó összes szolgáltatás közös értéket.
    - **Problémák diagnosztizálása és megoldása**: Hibaelhárítási információk megjelenítése.
    - **Gyors üzembe helyezési**:  Információit jeleníti meg, amely segítséget nyújt a HDInsight használatának megkezdéséhez.
    - **Eszközök**: Információk a HDInsight kapcsolódó eszközök.

- **Beállítások menü**  
  - **Fürt mérete**: Ellenőrizze, növelheti és csökkentheti a fürt munkavégző csomópontok számát. Lásd: [fürtök méretezése](hdinsight-administer-use-management-portal.md#scale-clusters).
  - **Magkvóta korlátozásának**: A használt és elérhető az előfizetéshez tartozó magok jelennek meg.
  - **SSH + fürtbe való bejelentkezésekor**: Az utasításokat követve csatlakozhat a fürthöz Secure Shell (SSH)-kapcsolat használatával jeleníti meg. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).
  - **Data Lake Storage Gen1**: Data Lake Storage Gen1 hozzáférés konfigurálásához.  Lásd: [a rövid útmutató: A HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
  - **Storage-fiókok**: Tekintse meg a storage-fiókok és a kulcsokat. A storage-fiókok vannak konfigurálva, a fürt létrehozása során.
  - **Alkalmazások**: HDInsight-alkalmazások hozzáadása és eltávolítása.  Lásd: [egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md).
  - **Szkriptműveletek**: Bash parancsfájlok futtathatók a fürtön. Lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
  - **Külső metaadattárak**: Nézet a [Apache Hive](https://hive.apache.org/) és [Apache Oozie](https://oozie.apache.org/) metaadattárak. A metaadattárakat csak a fürt létrehozása során konfigurálható.
  - **HDInsight-partner**: A jelenlegi HDInsight-Partner felvétele/eltávolítása.
  - **Tulajdonságok**: Nézet a [fürt tulajdonságai](#properties).
  - **Zárolja**: Vegye fel a zárolást, hogy a fürt éppen módosított vagy törölt.
  -  **Automation-szkript**: Megjelenítés és a fürt az Azure Resource Manager-sablon exportálása. Jelenleg csak exportálhatja a függő Azure storage-fiókban. Lásd: [Linux-alapú Apache Hadoop-fürtök a HDInsight az Azure Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

- **Figyelési menü**
  - **Riasztások**: Kezelheti a riasztásokat és műveleteket.
  - **Metrikák**: Az Azure Monitor naplóira a fürt metrikák figyelése.
  - **Diagnosztikai beállítások**: A diagnosztikai metrikák tárolására beállításait.
  - **Az Operations Management Suite**:  A naplók az Azure Operations Management Suite (OMS) és az Azure Monitor-fürt monitorozásához.

- **Támogatás + hibaelhárítás menüjében**
  - **A Resource health**: Lásd: [az Azure resource health áttekintése](../service-health/resource-health-overview.md).
  - **Új támogatási kérelem**: Lehetővé teszi, hogy hozzon létre egy támogatási jegyet a Microsoft ügyfélszolgálatához.
    
## <a name="properties"></a> Fürt tulajdonságai
Az a [fürt kezdőlap](#homePage)alatt **beállítások** válassza **tulajdonságok**.
* **Állomásnév**: Fürt neve.
* **A fürt URL-cím**: Az Ambari webes felület URL-CÍMÉT.
* **Secure shell (SSH)**: A felhasználónév és a gazdagépcsoport nevét, a fürt SSH-n keresztüli eléréséhez.
* **Állapot**: Egyike: Megszakítva fogadják el, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, működési, fut, a hiba, törlése, a törlés időtúllépés miatt megszakadt, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued, vagy ClusterCustomization.
* **Régió**: Azure-helyen. Az Azure a támogatott helyek listáját lásd: a **régió** legördülő lista [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Létrehozás dátuma**: A dátum a fürtöt helyezett üzembe.
* **Operációs rendszer**: Mindkét **Windows** vagy **Linux**.
* **Típus**: Hadoop, HBase, Storm, Spark.
* **Verzió**. Lásd: [HDInsight-verziók](hdinsight-component-versioning.md).
* **Előfizetés**: Az előfizetés neve.
* **Alapértelmezett adatforrás**: Az alapértelmezett fürt fájlrendszer.
* **Munkavégző csomópontok mérete**: A kiválasztott virtuális gép mérete a feldolgozó csomópontok.
* **Átjárócsomópont csomópontméret**: A kiválasztott virtuális gép mérete az átjárócsomópontokat.
* **Virtuális hálózat**: Neve a virtuális hálózat, amely a fürt üzemel, ha egy központi telepítéskor lett kiválasztva.

## <a name="move-clusters"></a>Helyezze át a fürtök

Egy HDInsight-fürt áthelyezheti egy másik Azure-erőforráscsoportot, vagy egy másik előfizetésbe. 

Az a [fürt kezdőlap](#homePage):

1. Válassza ki **áthelyezése** a felső menüben.
2. Válassza ki **áthelyezése másik erőforráscsoportba** vagy **Áttérés másik előfizetésre**.
3. Kövesse az utasításokat az új lapon.

## <a name="delete-clusters"></a>Fürtök törlése
Fürt törlése nem törli az alapértelmezett tárfiókot, sem az összes kapcsolt tárfiókot. Ugyanazt a tárfiókot és az azonos metaadattárakat használatával újra létrehozhatja a fürtöt. Azt javasoljuk, ha újra létrehozza a fürtöt használ, egy új alapértelmezett blobtárolóval.

Az a [fürt kezdőlap](#homePage):

1. Válassza ki **törlése** a felső menüben.
2. Kövesse az utasításokat az új lapon.

Lásd még: [fürtök Szüneteltetés és leállítás](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

A fürt létrehozása után hozzáadhat további Azure Storage-fiókok és az Azure Data Lake-tárfiókokat. További információkért lásd: [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="scale-clusters"></a>Fürtök méretezése
A fürtméretezés egy funkció lehetővé teszi a fürt újbóli létrehozása nélkül egy Azure HDInsight-fürt által használt munkavégző csomópontok számának módosításához.

> [!NOTE]  
> Csak 3.1.3 verziójú HDInsight-fürtök vagy újabb verziója támogatott. Ha biztos benne, hogy a fürt verziója, a Tulajdonságok lapon ellenőrizheti.  Tekintse meg a listában, és bemutatják a fürtök.

Az a [fürt kezdőlap](#homePage):

1. A **beállítások**válassza **fürtméret**.
2. Adja meg **száma a munkavégző csomópontok** a numerikus szövegmezőben. A fürtcsomópontok számának korlátja az Azure-előfizetések közé esik. Növelje a számlázási támogatással fordulnia.  A költségadatokat a módosítások a csomópontok számát jeleníti meg.
3. Kattintson a **Mentés** gombra.

    ![HDInsight hadoop hbase storm spark skála](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster2.png)

Az adatcsomópontok száma módosításának hatása az egyes támogatott a HDInsight-fürt eltérő:

* Apache Hadoop

    Zökkenőmentesen lehet növelni egy Hadoop-fürtöt, amely a folyamatban lévő vagy futó feladatok befolyásolása nélkül fut-e a munkavégző csomópontok számát. Új feladatok is lehet beküldeni, amíg a művelet folyamatban van. A skálázási művelet hibák szabályosan kezeli, úgy, hogy a fürt minden esetben működőképes állapotban marad.

    Ha egy Hadoop-fürtöt az adatcsomópontok száma csökkentésével vertikálisan leskálázni, a fürtben a szolgáltatások újra lesz indítva. Ez a viselkedés hatására összes futó és a függőben lévő feladatok meghiúsulhatnak, a skálázási művelet befejezése után. A feladatok újból elküldheti, azonban a művelet befejeződése után.
* Apache HBase

    Zökkenőmentesen adja hozzá vagy távolíthat el csomópontokat a HBase-fürt futás közben. Regionális kiszolgáló automatikusan kiegyensúlyozott vannak a skálázási művelet befejezése néhány percen belül. Azonban, manuálisan is eloszthatja a regionális kiszolgálók jelentkezik be a fürt átjárócsomópontjával, és a egy parancssori ablakot a következő parancsokat futtatni:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    A HBase-rendszerhéj használata további információkért lásd: [Ismerkedés a HDInsight Apache HBase-példát](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Zökkenőmentesen adja hozzá vagy távolít el a Storm-fürt adatcsomópontok futás közben. Azonban a skálázási művelet a sikeres telepítést követően szüksége lesz a topológia újraegyensúlyozására.

    Az újraegyensúlyozás két módon is elvégezhető:

  * A Storm webes felhasználói felületen
  * Parancssori felület (CLI) eszköz

    Tekintse meg a [Apache Storm-dokumentáció](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) további részletekért.

    A Storm webes felhasználói felületen a HDInsight-fürtön érhető el:

    ![HDInsight Storm méretezési újraegyensúlyozási](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Íme egy példa a Storm-topológia újraegyensúlyozására CLI-parancsot:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```


## <a name="pauseshut-down-clusters"></a>Fürtök Szüneteltetés és leállítás

Hadoop-feladatok többsége kötegelt feladatok, amelyek csak esetenként futnak. A legtöbb Hadoop-fürtök nincsenek nagy időtartamra, amely a fürt nem használja a feldolgozáshoz. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban.
Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Számos módon meg a program a folyamatot:

* Az Azure Data Factory felhasználói. Lásd: [létrehozása igény szerinti Linux-alapú Apache Hadoop-fürtök az Azure Data Factory használatával HDInsight](hdinsight-hadoop-create-linux-clusters-adf.md) létrehozásához igény szerinti HDInsight társított szolgáltatás.
* Az Azure PowerShell-lel.  Lásd: [repülőjáratok késési adatainak elemzése](hdinsight-analyze-flight-delay-data.md).
* Klasszikus Azure CLI használatával. Lásd: [kezelése a HDInsight-fürtök használatával a klasszikus Azure CLI](hdinsight-administer-use-command-line.md).
* HDInsight .NET SDK használata. Lásd: [küldje el az Apache Hadoop-feladatok](hadoop/submit-apache-hadoop-jobs-programmatically.md).

Díjszabási információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/). Tekintse meg a fürt törlésének a portálról, [fürtök törlése](#delete-clusters)



## <a name="upgrade-clusters"></a>Fürtök frissítése

Lásd: [újabb verzióra való frissítése HDInsight-fürt](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Nyissa meg az Apache Ambari webes Felülettel

Az Ambari kínál egy intuitív, egyszerűen használható Hadoop felügyeleti webes felhasználói felületen a RESTful API-k által támogatott. Az Ambari lehetővé teszi a rendszergazdák kezelni és megfigyelni a Hadoop-fürtöket.

Az a [fürt kezdőlap](#homePage):

1. Válassza ki **fürt irányítópultjai**.

    ![HDInsight Hadoop-fürt menü](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Válassza ki **otthoni Ambari** az új lapon.
2. Adja meg a fürt felhasználónevet és jelszót.  A fürt alapértelmezett felhasználónév az _rendszergazdai_. Az Ambari webes felhasználói felület hasonlóan néz ki:

További információkért lásd: [kezelése a HDInsight-fürtök az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Jelszó módosítása
Egy HDInsight-fürt két felhasználói fiókot lehet. A HDInsight-fürt felhasználói fiók (más néven) HTTP-felhasználói fiókot) és a létrehozási folyamat során jönnek létre az SSH-felhasználói fiókot. A portál használatával módosítsa a fürt felhasználói fiók jelszavát, és a Parancsfájlműveletek az SSH-felhasználói fiók módosítása.

### <a name="change-the-cluster-user-password"></a>A fürt felhasználói jelszó módosítása

> [!NOTE]  
> A fürt (rendszergazdai) felhasználó jelszavának módosítása, előfordulhat, hogy parancsfájlműveletekkel futtatásához a fürtön, sikertelen lesz. Ha bármely megőrzött Parancsfájlműveletek adott cél feldolgozó csomóponttal rendelkezik, ezek a szkriptek sikertelen lehet csomópontokat a fürt átméretezése műveletek hozzáadásakor. Parancsfájlműveletekkel további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

Az a [fürt kezdőlap](#homePage):
1. Válassza ki **SSH + fürtbe bejelentkezési** alatt **beállítások**.
2. Válassza ki **hitelesítő adatok alaphelyzetbe állítása**.
3. Adja meg, majd új jelszót a mezőben.
4. Kattintson az **OK** gombra.

A jelszó módosítása a fürt minden csomópontján.

### <a name="change-the-ssh-user-password"></a>Az SSH-felhasználói jelszó módosítása
1. Egy szövegszerkesztővel, mentse az alábbi szöveget nevű fájlként **changepassword.sh**.

    > [!IMPORTANT]  
    > A sor vége LF használó szerkesztővé kell használnia. Ha a szerkesztő CRLF használ, majd a parancsfájl nem működik.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Töltse fel a fájlt egy tárolási helye, amely a HDInsight egy HTTP vagy HTTPS-cím használatával érhető el. Például egy nyilvános fájlt például a onedrive-on vagy az Azure Blob storage tárolja. Mentse a fájlt, az URI-t (HTTP vagy HTTPS-cím), ezt az URI a következő lépésben van szükség szerint.
3. Az a [fürt kezdőlap](#homePage) kiválasztása **Szkriptműveletek** alatt **beállítások**.
4. Az a **Parancsfájlműveletek** panelen válassza ki **új küldése**. 
5. Az a **Szkriptművelet** panelen adja meg a következőket:

   | Mező | Érték |
   | --- | --- |
   | Szkripttípus | Válassza ki **– az egyéni** a legördülő listából.|
   | Name (Név) |"Ssh jelszó módosítása" |
   | Bash parancsfájl URI azonosítója |Az URI-t a changepassword.sh fájl |
   | Csomóponttípus(ok): (A fő, feldolgozói, Nimbus, felügyelő, Zookeeper, stb.) |Minden csomópont esetében felsorolt ✓ |
   | Paraméterek |Adja meg az SSH-felhasználónevet és az új jelszót. Egy tárolóhely közötti a felhasználónevet és jelszót kell lennie. |
   | Parancsfájlműveletet... |Ne jelölje be ezt a mezőt. |
5. Válassza ki **létrehozás** a alkalmazni a parancsfájlt. A szkript befejezése után Ön tud csatlakozni a fürthöz SSH használatával az új jelszóval.

## <a name="grantrevoke-access"></a>GRANT/revoke-access
HDInsight-fürtök a következő HTTP webes szolgáltatások (ezen szolgáltatások mindegyikéhez kell RESTful végpontokon) rendelkezik:

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton eszközön keresztül végzett

Alapértelmezés szerint ezek a szolgáltatások hozzáférés kapnak. Akkor is visszavonása/biztosítása a hozzáférés használatával [Azure klasszikus parancssori felület](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) és [Azure PowerShell-lel](hdinsight-administer-use-powershell.md#grantrevoke-access).

## <a name="find-the-subscription-id"></a>Keresse meg az előfizetés-azonosító
Minden egyes fürt az Azure-előfizetés van kötve.  Az Azure-előfizetés azonosítója jelenik meg, a [fürt kezdőlap](#homePage).

## <a name="find-the-resource-group"></a>Keresse meg az erőforráscsoport
Az Azure Resource Manager módban minden HDInsight-fürt jön létre egy Azure Resource Manager-csoporttal. A Resource Manager-csoport nem láthatók a a [fürt kezdőlap](#homePage).

## <a name="find-the-storage-accounts"></a>Keresse meg a storage-fiókok
HDInsight-fürtök vagy egy Azure Storage-fiókot, vagy az Azure Data Lake Storage segítségével tárolja az adatokat. Minden egyes HDInsight-fürt rendelkezhet egy alapértelmezett tárfiókot és egy társított storage-fiókok számát. A tárfiókok listázásához a [fürt kezdőlap](#homePage) alatt **beállítások**, jelölje be **tárfiókok**.


## <a name="monitor-jobs"></a>Feladatok figyelése
Lásd: [kezelése a HDInsight-fürtök az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md#monitoring).


## <a name="monitor-cluster-usage"></a>Fürt használat monitorozása
A **használati** szakasz a HDInsight-fürt panelén, a HDInsight segítségével az előfizetéséhez elérhető magok száma, valamint az ehhez a fürthöz, és hogyan vannak lefoglalva számára lefoglalt magok száma információkat jelenít meg a csomópontok a fürtön belül. Tekintse meg a listában, és bemutatják a fürtök.

> [!IMPORTANT]  
> A HDInsight-fürt által nyújtott szolgáltatások figyeléséhez, Ambari Web vagy az Ambari REST API-t kell használnia. Az Ambari használatával kapcsolatos további információkért lásd: [kezelése a HDInsight-fürtök az Apache Ambari](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Csatlakozás fürthöz

* [Az Apache Hive használata a HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett néhány alapvető felügyeleti funkciókat. További tudnivalókért tekintse meg a következő cikkeket:

* [Az Azure PowerShell használatával HDInsight felügyelete](hdinsight-administer-use-powershell.md)
* [Felügyelheti a HDInsight az Azure klasszikus parancssori felület használatával](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [További információ az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md)
* [Az Apache Ambari REST API használatával](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Az Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight](hadoop/hdinsight-use-pig.md)
* [A HDInsight Apache Sqoop használata](hadoop/hdinsight-use-sqoop.md)
* [Az Azure HDInsight – első lépések](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Az Azure HDInsight az Apache Hadoop melyik verzióját van?](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop parancssor"
