---
title: Az Azure portal használatával HDInsight az Apache Hadoop-fürtök kezelése
description: Megtudhatja, hogyan hozhat létre és kezelhet a HDInsight-fürtök az Azure portal használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: hrasheed
ms.openlocfilehash: c615188e5f198cb8707b12643e0942e8d8821983
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725182"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>A HDInsight Apache Hadoop-fürtök kezelése az Azure portal használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Használatával a [az Azure portal][azure-portal], kezelheti [Apache Hadoop](https://hadoop.apache.org/) Azure HDInsight-fürtök. Információ más eszközök használatával HDInsight Hadoop-fürtök kezelése a fenti lapon választómezőt használja.

**Előfeltétel**

Kövesse az ebben a cikkben, szüksége lesz egy **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="open-the-azure-portal"></a>Nyissa meg az Azure Portalon
1. Jelentkezzen be itt: [https://portal.azure.com](https://portal.azure.com).
2. Miután megnyitotta a portálon, teszi lehetővé:

   * Kattintson a **erőforrás létrehozása** hozzon létre egy új fürtöt, a bal oldali menüből:

       ![új HDInsight-fürt gomb](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)

       Adja meg **HDInsight** a **keresés a piactéren**, kattintson a **HDInsight**, és kattintson a **létrehozás**.

   * Kattintson a **HDInsight-fürtök** , a meglévő fürtök listája a bal oldali menüből:

       ![Az Azure portal HDInsight fürt gomb](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       Ha nem látja a **HDInsight-fürtök** gombra, majd **HDInsight-fürtök** alatt a **intelligencia és elemzés** szakaszban.


## <a name="create-clusters"></a>Fürtök létrehozása
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight együttműködik egy széles számos Hadoop-összetevők. Az ellenőrzött és a támogatott összetevők listáját lásd: [Azure HDInsight az Apache Hadoop verziójának van?](hdinsight-component-versioning.md) Általános fürt létrehozásának információkért lásd: [Apache Hadoop-fürtök létrehozása a HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények

Egy HDInsight-fürt létrehozásakor meg kell adnia egy Azure-előfizetést. A fürt vagy egy új Azure-erőforráscsoportot, vagy egy meglévő erőforráscsoportban hozható létre. A következő lépések segítségével ellenőrizze az engedélyeit a HDInsight-fürtök létrehozására:

- Új erőforráscsoport létrehozása:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. Kattintson a **előfizetés** a bal oldali menüből. Rendelkezik egy sárga kulcs ikon. Az előfizetések listája gondoskodnak.
    3. Kattintson az előfizetést, amellyel létrehozhat fürtöket. 
    4. Kattintson a **saját engedélyek**.  Megjeleníti a [szerepkör](../role-based-access-control/built-in-roles.md) az előfizetésben. Legalább szükséges közreműködői hozzáférés HDInsight-fürt létrehozásához.

- Meglévő erőforráscsoport használata:

    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    2. Kattintson a **erőforráscsoportok** a bal oldali menüben az erőforráscsoportok listázásához.
    3. Az erőforráscsoport, a HDInsight-fürt létrehozásához használni kívánt gombra.
    4. Kattintson a **hozzáférés-vezérlés (IAM)** és **szerepkör-hozzárendelések**, és ellenőrizze, hogy Ön (vagy egy csoport, amelynek tagja) legalább az erőforráscsoport közreműködői hozzáféréssel rendelkeznek.

Ha a NoRegisteredProviderFound hiba vagy a MissingSubscriptionRegistration hibaüzenetet kap, tekintse meg a [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md).

## <a name="list-and-show-clusters"></a>Fürtök listázása és megjelenítése
1. Jelentkezzen be itt: [https://portal.azure.com](https://portal.azure.com).
2. Kattintson a **HDInsight-fürtök** , a meglévő fürtök listája a bal oldali menüből. Ha nem lát **HDInsight-fürtök**, kattintson a **minden szolgáltatás** első.
3. Kattintson a fürt nevére. Ha a fürt lista hosszú, az oldal tetején a szűrő is használhatja.
4. Kattintson egy fürtöt a listából, az Áttekintés oldal jelenik meg:

    ![Az Azure portal HDInsight fürt essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png) **áttekintés menüben:**
    * **Irányítópult**: az Ambari webes felhasználói felület a fürt megnyílik.
    * **Secure Shell**: jeleníti meg az utasításokat követve csatlakozhat a fürthöz Secure Shell (SSH) kapcsolaton keresztül.
    * **Fürt méretezése**: lehetővé teszi, hogy a fürt munkavégző csomópontok számának módosítását.
    * **Helyezze át**: a fürt áthelyezi egy másik erőforráscsoportba, illetve egy másik előfizetésbe.
    * **Törlés**: törli a fürtöt.

**Bal oldali menüben:**
    * **A Tevékenységnaplók**: megjelenítése és a lekérdezés tevékenységeket tartalmazó naplók.
    * **Hozzáférés-vezérlés (IAM)**: szerepkör-hozzárendelésekkel.  Lásd: [az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése a szerepkör-hozzárendelésekkel](../role-based-access-control/role-assignments-portal.md).
    * **A címkék**: lehetővé teszi, hogy állítsa be a kulcs/érték párok meghatározásához egy egyéni besorolás, a felhőszolgáltatásban. Például előfordulhat, hogy létre nevű kulcs **projekt**, majd használja az adott projekthez tartozó összes szolgáltatás közös értéket.
* **Problémák diagnosztizálása és megoldása**: hibaelhárítási információk megjelenítése.
    * **Gyors üzembe helyezési**: információit jeleníti meg, amely segítséget nyújt a HDInsight használatának megkezdéséhez.
    * **A HDInsight Tools**: segítő információk a HDInsight kapcsolódó eszközök.
**Beállítások**
* **Fürt mérete**: Ellenőrizze, növelése és csökkentése a fürt munkavégző csomópontok számát. Lásd: [fürtök méretezése](hdinsight-administer-use-management-portal.md#scale-clusters).
    * **Magkvóta korlátozásának**: az előfizetésében használt és elérhető maggal megjelenítéséhez.
    * **SSH + fürtbe való bejelentkezésekor**: jeleníti meg az utasításokat követve csatlakozhat a fürthöz Secure Shell (SSH) kapcsolaton keresztül. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).
* **Data Lake Store Gen1**: konfigurálása Data Lake Store Gen1 eléréséhez.  Lásd: [a rövid útmutató: a HDInsight-fürtök beállítása](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).
    * **Storage-fiókok**: a storage-fiókok és kulcsok megtekintéséhez. A storage-fiókok vannak konfigurálva, a fürt létrehozása során.
    * **Alkalmazások**: hozzáadása/eltávolítása HDInsight-alkalmazások.  Lásd: [egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md).
    * **Szkriptműveletek**: futtassa a Bash-szkriptek a fürt. Lásd: [testreszabása Linux-alapú HDInsight-fürtök szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
    * **HDInsight-Partner**: a jelenlegi HDInsight-Partner felvétele/eltávolítása.
    * **Tulajdonságok**: megtekintheti a tulajdonságait.
* **Zárolja**: módosított vagy törölt, adja hozzá a zárolást, hogy a fürt folyamatban van.
    * **Automation-szkript**: megjelenítési és exportálása az Azure Resource Manager-sablon a fürt számára. Jelenleg csak exportálhatja a függő Azure storage-fiókban. Lásd: [Linux-alapú Apache Hadoop-fürtök a HDInsight az Azure Resource Manager-sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).
**Monitorozás**
* **Alters**: a riasztások és műveletek kezelése.
    * **Metrikák**: nyomon követheti a fürt metrikákat az Azure Log Analyticsben.
* **Diagnosztikai beállítások**: beállításainak tárolására, a diagnosztikai metrikák **támogatás + hibaelhárítás**
    * **A Resource health**: lásd: [az Azure resource health áttekintése](../service-health/resource-health-overview.md).
    * **Új támogatási kérelem**: lehetővé teszi, hogy hozzon létre egy támogatási jegyet a Microsoft ügyfélszolgálatához.
    
6. Kattintson a **tulajdonságok**:

    A tulajdonságok a következők:

   * **Állomásnév**: fürt neve.
   * **A fürt URL-cím**: az Ambari webes felület URL-címe.
   * **Secure shell (SSH)**: a fürt SSH-n keresztüli eléréséhez használandó felhasználónevet és a gazdagépcsoport nevét.
   * **Állapot**: egyikét: megszakítva fogadják el, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, működési, fut, a hiba, törlése, a törlés időtúllépés miatt megszakadt, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued vagy ClusterCustomization.
   * **Régió**: az Azure-helyen. Az Azure a támogatott helyek listáját lásd: a **régió** legördülő lista [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).
   * **Létrehozás dátuma**: A dátum a fürtöt helyezett üzembe.
   * **Operációs rendszer**: vagy **Windows** vagy **Linux**.
   * **Típus**: Hadoop, HBase, Storm, Spark.
   * **Verzió**. Lásd: [HDInsight-verziók](hdinsight-component-versioning.md).
   * **Előfizetés**: előfizetés nevét.
   * **Alapértelmezett adatforrás**: fürt alapértelmezett fájlrendszerének.
   * **Munkavégző csomópontok mérete**: a feldolgozó csomópontok a kiválasztott Virtuálisgép-méretet.
   * **Átjárócsomópont csomópontméret**: A kiválasztott Virtuálisgép-méretet az átjárócsomópontokat.
   * **Virtuális hálózat**: A neve, a virtuális hálózat, amely a fürt üzemel, ha egy központi telepítéskor lett kiválasztva.

## <a name="delete-clusters"></a>Fürtök törlése
Fürt törlése nem törli az alapértelmezett tárfiókot, sem az összes kapcsolt tárfiókot. Ugyanazt a tárfiókot és az azonos metaadattárakat használatával újra létrehozhatja a fürtöt. Azt javasoljuk, ha újra létrehozza a fürtöt használ, egy új alapértelmezett blobtárolóval.

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **HDInsight-fürtök** a bal oldali menüből. Ha nem lát **HDInsight-fürtök**, kattintson a **minden szolgáltatás** első.
3. Kattintson a törölni kívánt fürtöt.
4. Kattintson a **törlése** a felső menüben, majd kövesse az utasításokat.

Lásd még: [fürtök Szüneteltetés és leállítás](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

A fürt létrehozása után hozzáadhat további Azure Storage-fiókok és az Azure Data Lake Store-fiókok. További információkért lásd: [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="scale-clusters"></a>Fürtök méretezése
A fürtméretezés egy funkció lehetővé teszi a fürt újbóli létrehozása nélkül egy Azure HDInsight-fürt által használt munkavégző csomópontok számának módosításához.

> [!NOTE]
> Csak 3.1.3 verziójú HDInsight-fürtök vagy újabb verziója támogatott. Ha biztos benne, hogy a fürt verziója, a Tulajdonságok lapon ellenőrizheti.  Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).
>
>
**Fürtök méretezése**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **HDInsight-fürtök** a bal oldali menüből.
3. Kattintson a fürt méretezésére.
3. Kattintson a **fürt méretezése**.
4. Adja meg **száma a munkavégző csomópontok**. A fürtcsomópontok számának korlátja az Azure-előfizetések közé esik. Növelje a számlázási támogatással fordulnia.  A költségadatokat a módosítások a csomópontok számát jeleníti meg.

    ![HDInsight hadoop hbase storm spark skála](./media/hdinsight-administer-use-portal-linux/hdinsight-portal-scale-cluster.png)

Az adatcsomópontok száma módosításának hatása az egyes támogatott a HDInsight-fürt eltérő:

* Apache Hadoop

    Zökkenőmentesen lehet növelni egy Hadoop-fürtöt, amely a folyamatban lévő vagy futó feladatok befolyásolása nélkül fut-e a munkavégző csomópontok számát. Új feladatok is lehet beküldeni, amíg a művelet folyamatban van. A skálázási művelet hibák szabályosan kezeli, úgy, hogy a fürt minden esetben működőképes állapotban marad.

    Ha egy Hadoop-fürtöt az adatcsomópontok száma csökkentésével vertikálisan leskálázni, a fürtben a szolgáltatások újra lesz indítva. Ez a viselkedés hatására összes futó és a függőben lévő feladatok meghiúsulhatnak, a skálázási művelet befejezése után. A feladatok újból elküldheti, azonban a művelet befejeződése után.
* Apache HBase

    Zökkenőmentesen adja hozzá vagy távolíthat el csomópontokat a HBase-fürt futás közben. Regionális kiszolgáló automatikusan kiegyensúlyozott vannak a skálázási művelet befejezése néhány percen belül. Azonban, manuálisan is eloszthatja a regionális kiszolgálók jelentkezik be a fürt átjárócsomópontjával, és a egy parancssori ablakot a következő parancsokat futtatni:

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

    A HBase-rendszerhéj használata további információkért lásd: [Ismerkedés a HDInsight Apache HBase-példát](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Zökkenőmentesen adja hozzá vagy távolít el a Storm-fürt adatcsomópontok futás közben. Azonban a skálázási művelet a sikeres telepítést követően szüksége lesz a topológia újraegyensúlyozására.

    Az újraegyensúlyozás két módon is elvégezhető:

  * A Storm webes felhasználói felületen
  * Parancssori felület (CLI) eszköz

    Tekintse meg a [Apache Storm-dokumentáció](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) további részletekért.

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

## <a name="move-cluster"></a>Helyezze át a fürt

Egy HDInsight-fürt áthelyezheti egy másik Azure-erőforráscsoportot, vagy egy másik előfizetésbe.  Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

## <a name="upgrade-clusters"></a>Fürtök frissítése

Lásd: [újabb verzióra való frissítése HDInsight-fürt](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Nyissa meg az Apache Ambari webes Felülettel

Az Ambari kínál egy intuitív, egyszerűen használható Hadoop felügyeleti webes felhasználói felületen a RESTful API-k által támogatott. Az Ambari lehetővé teszi a rendszergazdák kezelni és megfigyelni a Hadoop-fürtöket.

1. Hozzon létre egy HDInsight-fürtön az Azure Portalon.  Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).
2. Kattintson a **Cluster Dashboard**.

    ![HDInsight Hadoop-fürt menü](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu.png)

1. Adja meg a fürt felhasználónevet és jelszót.  A fürt alapértelmezett felhasználónév az _rendszergazdai_. Az Ambari webes felhasználói felület hasonlóan néz ki:

    ![HDInsight Hadoop Ambari webes felhasználói felület](./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-ambari-web-ui.png)

További információkért lásd: [kezelése a HDInsight-fürtök az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Jelszó módosítása
Egy HDInsight-fürt két felhasználói fiókot lehet. A HDInsight-fürt felhasználói fiók (más néven) HTTP-felhasználói fiókot) és a létrehozási folyamat során jönnek létre az SSH-felhasználói fiókot. Az Ambari webes felhasználói felület segítségével módosítsa a fürt felhasználói fiókhoz tartozó felhasználónév és jelszó és Parancsfájlműveletek az SSH-felhasználói fiók módosítása

### <a name="change-the-cluster-user-password"></a>A fürt felhasználói jelszó módosítása
Az Ambari webes felhasználói Felületet használhatja a fürt felhasználói jelszó módosítására. Jelentkezzen be az Ambari, a meglévő fürt felhasználónevet és jelszót kell használnia.

> [!NOTE]
> A fürt (rendszergazdai) felhasználó jelszavának módosítása, előfordulhat, hogy parancsfájlműveletekkel futtatásához a fürtön, sikertelen lesz. Ha bármely megőrzött Parancsfájlműveletek adott cél feldolgozó csomóponttal rendelkezik, ezek a szkriptek sikertelen lehet csomópontokat a fürt átméretezése műveletek hozzáadásakor. Parancsfájlműveletekkel további információkért lásd: [testreszabása HDInsight-fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Jelentkezzen be az Ambari webes felhasználói Felületet a HDInsight-fürt felhasználói hitelesítő adatokkal. Az alapértelmezett felhasználónév az **admin**. Az URL-cím **https://&lt;HDInsight-fürt neve > azurehdinsight.net**.
2. Kattintson a **rendszergazdai** a felső menüben, majd kattintson a "Kezelése az Ambari".
3. A bal oldali menüben kattintson a **felhasználók**.
4. Kattintson a **rendszergazdai**.
5. Kattintson a **jelszómódosítás**.

Az Ambari ezután módosítja a jelszavát, a fürt minden csomópontján.

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
3. Az Azure Portalon kattintson **HDInsight-fürtök**.
4. Kattintson a HDInsight-fürt.
4. Kattintson a **Szkriptműveletek**.
4. Az a **Parancsfájlműveletek** panelen válassza ki **új küldése**. Ha a **Szkriptművelet** panel jelenik meg, adja meg a következőket:

   | Mező | Érték |
   | --- | --- |
   | Name (Név) |Ssh jelszó módosítása |
   | Bash parancsfájl URI azonosítója |Az URI-t a changepassword.sh fájl |
   | Csomópontok (fő, feldolgozói, Nimbus, felügyelő, Zookeeper, stb.) |Minden csomópont esetében felsorolt ✓ |
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

**Az Azure-előfizetés azonosítókat keresése**

1. Jelentkezzen be a [portál][azure-portal].
2. Kattintson a **előfizetések**. Minden előfizetési csomaghoz tartozik egy nevet és a egy.

Minden egyes fürt az Azure-előfizetés van kötve. Az előfizetés-azonosító jelenik meg a fürt **alapvető** csempére. Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

## <a name="find-the-resource-group"></a>Keresse meg az erőforráscsoport
Az Azure Resource Manager módban minden HDInsight-fürt jön létre egy Azure Resource Manager-csoporttal. A Resource Manager-csoportot, amely egy fürt tagja jelenik meg:

* A fürt listája tartalmaz egy **erőforráscsoport** oszlop.
* Fürt **alapvető** csempére.  

Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

## <a name="find-the-storage-accounts"></a>Keresse meg a storage-fiókok

HDInsight-fürtök vagy egy Azure Storage-fiókot, vagy egy Azure Data Lake Store használata adatok tárolására. Minden egyes HDInsight-fürt rendelkezhet egy alapértelmezett tárfiókot és egy társított storage-fiókok számát. A tárfiókok listázásához először nyissa meg a fürtöt a portálról, és kattintson **tárfiókok**:

![HDInsight-fürt storage-fiókok](./media/hdinsight-administer-use-portal-linux/hdinsight-storage-accounts.png)

Az előző képernyőkép szerint van egy __alapértelmezett__ oszlop jelzi, hogy a fióknak-e az alapértelmezett tárfiók.

A Data Lake Store-fiókok listájában, kattintson a **Data Lake Store-hozzáférés** az előző képernyőképen látható.

## <a name="run-apache-hive-queries"></a>Az Apache Hive-lekérdezések futtatása
Hive-feladat nem futtatható közvetlenül az Azure Portalról, de használhatja a Hive-nézet az Ambari webes felhasználói Felületet.

**Ambari, Hive-nézet használata a Hive-lekérdezések futtatásához**

1. Jelentkezzen be az Ambari webes felhasználói Felületet a HDInsight-fürt felhasználói hitelesítő adatokkal. Az alapértelmezett felhasználónév az **admin**. Az URL-cím **https://&lt;HDInsight-fürt neve > azurehdinsight.net**.
2. Nyissa meg a Hive-nézet az alábbi képernyőképen látható módon:  

    ![HDInsight hive-nézet](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)

3. Kattintson a **lekérdezés** a felső menüben.
4. Adja meg a Hive-lekérdezést **Lekérdezésszerkesztő**, és kattintson a **Execute**.

## <a name="monitor-jobs"></a>Feladatok figyelése
Lásd: [kezelése a HDInsight-fürtök az Apache Ambari webes felhasználói felület használatával](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="browse-files"></a>Fájlok tallózása
Az Azure portal használatával megkeresheti az alapértelmezett tároló tartalmát.

1. Jelentkezzen be itt: [https://portal.azure.com](https://portal.azure.com).
2. Kattintson a **HDInsight-fürtök** , a meglévő fürtök listája a bal oldali menüből.
3. Kattintson a fürt nevére. Ha a fürt lista hosszú, az oldal tetején a szűrő is használhatja.
4. Kattintson a **Tárfiókok** a fürt bal oldali menüből.
5. Kattintson a Storage-fiók.
7. Kattintson a **Blobok** csempére.
8. Kattintson az alapértelmezett tároló nevére.

## <a name="monitor-cluster-usage"></a>Fürt használat monitorozása
A **használati** szakasz a HDInsight-fürt panelén, a HDInsight segítségével az előfizetéséhez elérhető magok száma, valamint az ehhez a fürthöz, és hogyan vannak lefoglalva számára lefoglalt magok száma információkat jelenít meg a csomópontok a fürtön belül. Lásd: [fürtök listázása és megjelenítése](#list-and-show-clusters).

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
