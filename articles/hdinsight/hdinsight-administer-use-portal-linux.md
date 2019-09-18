---
title: Apache Hadoop-fürtök kezelése a HDInsight-ben Azure Portal használatával
description: Ismerje meg, hogyan hozhat létre és kezelhet Azure HDInsight-fürtöket a Azure Portal használatával.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: dfe531f8b298be1d90df45f57918f87744912949
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077058"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Apache Hadoop-fürtök kezelése a HDInsight-ben a Azure Portal használatával

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

A [Azure Portal](https://portal.azure.com)használatával [Apache Hadoop](https://hadoop.apache.org/) -fürtöket kezelhet az Azure HDInsight-ben. A fenti Tab választóval további információkat olvashat a Hadoop-fürtök HDInsight történő kezeléséről más eszközök használatával.

## <a name="prerequisites"></a>Előfeltételek

Meglévő Apache Hadoop-fürt a HDInsight-ben.  Lásd: [Linux-alapú fürtök létrehozása a HDInsight-ben a Azure Portal használatával](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="getting-started"></a>Első lépések
Jelentkezzen be itt: [https://portal.azure.com](https://portal.azure.com).

## <a name="showClusters"></a>Fürtök listázása és megjelenítése
A **HDInsight-fürtök** lapon megjelenik a meglévő fürtök listája.  A portálról:
1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben.
2. Válassza ki a **HDInsight-fürtöket** az **elemzés**alatt.

## <a name="homePage"></a>Fürt kezdőlapja 
Válassza ki a fürt nevét a [**HDInsight-fürtök**](#showClusters) lapon.  Ekkor megnyílik az **Áttekintés** nézet, amely az alábbi képhez hasonlóan néz ki:

![Azure Portal HDInsight-fürt Essentials](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**Felső menü:**  

| Elem| Leírás |
|---|---|
|Áthelyezés|Áthelyezi a fürtöt egy másik erőforráscsoporthoz vagy egy másik előfizetésbe.|
|Törlés|Törli a fürtöt. |
|Frissítés|Frissíti a nézetet.|

**Bal oldali menü:**  
  - **Bal felső menü**

    | Elem| Leírás |
    |---|---|
    |Áttekintés|A fürt általános információit tartalmazza.|
    |Tevékenységnapló|Tevékenységek naplóinak megjelenítése és lekérdezése.|
    |Hozzáférés-vezérlés (IAM)|Szerepkör-hozzárendelések használata.  Lásd: [szerepkör-hozzárendelések használata az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](../role-based-access-control/role-assignments-portal.md).|
    |Tags|Lehetővé teszi a kulcs/érték párok beállítását a Cloud Services egyéni besorolásának meghatározásához. Létrehozhat például egy **Project**nevű kulcsot, majd közös értéket használhat egy adott projekthez társított összes szolgáltatáshoz.|
    |Problémák diagnosztizálása és megoldása|Hibaelhárítási információk megjelenítése.|
    |Gyors üzembe helyezés|Megjeleníti a HDInsight használatának megkezdéséhez segítséget biztosító információkat.|
    |Eszközök|Súgó a HDInsight-hez kapcsolódó eszközökről.|

  - **Beállítások menü**  

    | Elem| Leírás |
    |---|---|
    |Fürt mérete|A fürt munkavégző csomópontjainak megkeresése, növelése és csökkentése. Lásd: [fürtök méretezése](hdinsight-administer-use-portal-linux.md#scale-clusters).|
    |Kvótahatárok|Az előfizetéshez használt és elérhető magok megjelenítése.|
    |SSH + fürt bejelentkezés|A fürthöz a Secure Shell (SSH) kapcsolat használatával történő kapcsolódáshoz szükséges utasításokat jeleníti meg. További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |1\. generációs Data Lake Storage|Hozzáférési Data Lake Storage Gen1 konfigurálása.  Lásd [: gyors útmutató: Fürtök beállítása a HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)-ben.|
    |Tárfiókok|Tekintse meg a Storage-fiókokat és a kulcsokat. A Storage-fiókok konfigurálása a fürt létrehozási folyamata során történik.|
    |Alkalmazások|HDInsight-alkalmazások hozzáadása/eltávolítása.  Lásd: [Egyéni HDInsight-alkalmazások telepítése](hdinsight-apps-install-custom-applications.md).|
    |Szkriptműveletek|Futtasson bash-parancsfájlokat a fürtön. Lásd: [a Linux-alapú HDInsight-fürtök testreszabása parancsfájl-művelettel](hdinsight-hadoop-customize-cluster-linux.md).|
    |Külső metaadattárak|Megtekintheti a [Apache Hive](https://hive.apache.org/) és az [Apache Oozie](https://oozie.apache.org/) metaadattárak. A metaadattárak csak a fürt létrehozási folyamata során lehet konfigurálni.|
    |HDInsight-partner|Az aktuális HDInsight-partner hozzáadása/eltávolítása.|
    |properties|A [fürt tulajdonságainak](#properties)megtekintése.|
    |Zárolások|Egy zárolás hozzáadásával megakadályozhatja a fürt módosítását vagy törlését.|
    |Sablon exportálása|A fürt Azure Resource Manager-sablonjának megjelenítése és exportálása. Jelenleg csak a függő Azure Storage-fiók exportálása végezhető el. Lásd: [Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight-ben Azure Resource Manager sablonok használatával](hdinsight-hadoop-create-linux-clusters-arm-templates.md).|

  - **Figyelés menü**

    | Elem| Leírás |
    |---|---|
    |Riasztások|A riasztások és műveletek kezelése.|
    |Mérőszámok|A fürt metrikáinak figyelése Azure Monitor naplókban.|
    |Diagnosztikai beállítások|A diagnosztikai metrikák tárolására szolgáló beállítások.|
    |Operations Management Suite|A fürt figyelése az Azure Operations Management Suite-ban (OMS) és Azure Monitor-naplókban.|

  - **Támogatás + hibaelhárítás menü**

    | Elem| Leírás |
    |---|---|
    |Erőforrás-állapot|Lásd: az [Azure Resource Health áttekintése](../service-health/resource-health-overview.md).|
    |Új támogatási kérelem|Lehetővé teszi, hogy támogatási jegyet hozzon létre a Microsoft támogatási szolgálatával.|

## <a name="properties"></a>Fürt tulajdonságai

A [fürt kezdőlapjának](#homePage) **Beállítások** területén válassza a **Tulajdonságok**lehetőséget.

|Elem | Leírás |
|---|---|
|ÁLLOMÁSNÉV|Fürt neve.|
|FÜRT URL-CÍME|A Ambari webes felületének URL-címe.|
|Privát végpont|A fürt magánhálózati végpontja.|
|Secure Shell (SSH)|A fürt SSH-n keresztüli eléréséhez használandó Felhasználónév és állomásnév.|
|ÁLLAPOT|Az egyik: Megszakítva, elfogadva, ClusterStorageProvisioned, AzureVMConfiguration, HDInsightConfiguration, működési, Futtatás, hiba, törlés, törlés, időtúllépés, DeleteQueued, DeleteTimedout, DeleteError, PatchQueued, CertRolloverQueued, ResizeQueued vagy ClusterCustomization.|
|RÉGIÓ|Azure-beli hely. A támogatott Azure-helyszínek listáját a [HDInsight díjszabásának](https://azure.microsoft.com/pricing/details/hdinsight/) **régió** legördülő listájában tekintheti meg.|
|LÉTREHOZÁS DÁTUMA|A fürt üzembe helyezésének dátuma.|
|OPERÁCIÓS RENDSZER|**Windows** vagy **Linux**.|
|TÍPUS|Hadoop, HBase, Storm, Spark.|
|Version|Lásd: [HDInsight-verziók](hdinsight-component-versioning.md).|
|ELŐFIZETÉS|Előfizetés neve.|
|ALAPÉRTELMEZETT ADATFORRÁS|Az alapértelmezett fürt fájlrendszere.|
|Munkavégző csomópontok mérete|A munkavégző csomópontok kiválasztott virtuálisgép-mérete.|
|Fő csomópont mérete|A fő csomópontok kiválasztott virtuálisgép-mérete.|
|Virtuális hálózat|Annak a Virtual Networknak a neve, amelybe a fürt telepítve van, ha a központi telepítési időpontban lett kiválasztva.|

## <a name="move-clusters"></a>Fürtök áthelyezése

Áthelyezhet egy HDInsight-fürtöt egy másik Azure-erőforráscsoporthoz vagy egy másik előfizetésbe.

A [fürt kezdőlapján](#homePage):

1. Válassza az **Áthelyezés** lehetőséget a felső menüben.
2. Válassza **az áthelyezés másik erőforráscsoporthoz** lehetőséget, vagy **váltson másik előfizetésre**.
3. Kövesse az új oldal utasításait.

## <a name="delete-clusters"></a>Fürtök törlése
A fürt törlése nem törli az alapértelmezett Storage-fiókot és a társított Storage-fiókokat. Újra létrehozhatja a fürtöt ugyanazzal a Storage-fiókkal és ugyanazzal a metaadattárak. Azt javasoljuk, hogy a fürt újbóli létrehozásakor új alapértelmezett BLOB-tárolót használjon.

A [fürt kezdőlapján](#homePage):

1. A felső menüben válassza a **Törlés** lehetőséget.
2. Kövesse az új oldal utasításait.

Lásd még: [fürtök szüneteltetése/leállítása](#pauseshut-down-clusters).

## <a name="add-additional-storage-accounts"></a>További tárfiókok hozzáadása

A fürt létrehozása után további Azure Storage-fiókokat és Azure Data Lake Storage fiókokat is hozzáadhat. További információkért lásd: [Add additional storage accounts to HDInsight](./hdinsight-hadoop-add-storage.md) (További tárfiókok hozzáadása a HDInsighthoz).

## <a name="scale-clusters"></a>Fürtök méretezése

A fürt skálázási funkciója lehetővé teszi az Azure HDInsight-fürt által használt munkavégző csomópontok számának módosítását anélkül, hogy újra létre kellene hozni a fürtöt.

A teljes információkért lásd: [HDInsight-fürtök méretezése](./hdinsight-scaling-best-practices.md) .

## <a name="pauseshut-down-clusters"></a>Fürtök szüneteltetése/leállítása

A legtöbb Hadoop feladat olyan batch-feladat, amely csak alkalmanként fut. A legtöbb Hadoop-fürt esetében nagy idő van, amíg a fürtöt nem használják feldolgozásra. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban.
Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

A folyamat többféleképpen is elvégezhető:

* Felhasználói Azure Data Factory. Lásd: [igény szerinti Linux-alapú Apache Hadoop-fürtök létrehozása a HDInsight-ben az](hdinsight-hadoop-create-linux-clusters-adf.md) igény szerinti HDInsight társított szolgáltatások létrehozásához Azure Data Factory használatával.
* Azure PowerShell használata.  Lásd: [repülési késleltetési információk elemzése](./interactive-query/interactive-query-tutorial-analyze-flight-data.md).
* Használja az Azure CLI-t. Lásd: [Azure HDInsight-fürtök kezelése az Azure CLI](hdinsight-administer-use-command-line.md)-vel.
* Használja a HDInsight .NET SDK-t. Lásd: [Apache Hadoop feladatok elküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md).

A díjszabással kapcsolatos információkért tekintse meg a [HDInsight díjszabását](https://azure.microsoft.com/pricing/details/hdinsight/). Ha törölni szeretne egy fürtöt a portálról, tekintse meg a [fürtök törlése](#delete-clusters) című témakört.

## <a name="upgrade-clusters"></a>Fürtök frissítése

Lásd: a [HDInsight-fürt frissítése újabb verzióra](./hdinsight-upgrade-cluster.md).

## <a name="open-the-apache-ambari-web-ui"></a>Az Apache Ambari webes FELÜLETének megnyitása

A Ambari a REST API-kkal támogatott, intuitív és könnyen használható Hadoop-felügyeleti webes FELÜLETET biztosít. A Ambari lehetővé teszi a rendszergazdák számára a Hadoop-fürtök felügyeletét és figyelését.

A [fürt kezdőlapján](#homePage):

1. Válassza a **fürt irányítópultok**lehetőséget.

    ![HDInsight Apache Hadoop-fürt menü](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. Válassza a **Ambari Kezdőlap** lehetőséget az új lapon.
1. Adja meg a fürt felhasználónevét és jelszavát.  Az alapértelmezett fürt felhasználóneve a _rendszergazda_.

További információ: HDInsight- [fürtök kezelése az Apache Ambari webes felhasználói felületén keresztül](hdinsight-hadoop-manage-ambari.md).

## <a name="change-passwords"></a>Jelszavak módosítása
An méretű HDInsight fürt két felhasználói fiókkal rendelkezhet. A HDInsight-fürt felhasználói fiókja (HTTP-felhasználói fiók) és az SSH-felhasználói fiók létrehozása a létrehozási folyamat során történik. A portálon módosíthatja a fürt felhasználói fiókjának jelszavát, valamint parancsfájl-műveleteket is megváltoztathatja az SSH-felhasználói fiókot.

### <a name="change-the-cluster-user-password"></a>A fürt felhasználói jelszavának módosítása

> [!NOTE]  
> Előfordulhat, hogy a fürt felhasználói (rendszergazdai) jelszavának módosítása a fürtön futó parancsfájl-műveletek meghibásodását okozhatja. Ha olyan megőrzött parancsfájl-művelettel rendelkezik, amely a munkavégző csomópontokat célozza meg, ezek a parancsfájlok sikertelenek lehetnek, ha csomópontokat ad hozzá a fürthöz az átméretezési műveletek használatával A parancsfájl-műveletekkel kapcsolatos további információkért lásd: [HDInsight-fürtök testreszabása parancsfájl-műveletek használatával](hdinsight-hadoop-customize-cluster-linux.md).

A [fürt kezdőlapján](#homePage):
1. Válassza az **SSH + fürt bejelentkezés** lehetőséget a **Beállítások**területen.
2. Válassza a **hitelesítő adatok visszaállítása**lehetőséget.
3. Adja meg és erősítse meg az új jelszót a szövegmezőben.
4. Kattintson az **OK** gombra.

A jelszó a fürt összes csomópontján módosul.

### <a name="change-the-ssh-user-password"></a>Az SSH-felhasználó jelszavának módosítása
1. Egy szövegszerkesztővel mentse az alábbi szöveget egy **ChangePassword.sh**nevű fájlba.

    > [!IMPORTANT]  
    > Olyan szerkesztőt kell használnia, amely a TT-t használja a vonal végeként. Ha a szerkesztő CRLF használ, akkor a parancsfájl nem működik.

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. Töltse fel a fájlt egy olyan tárolási helyre, amely HTTP-vagy HTTPS-címen keresztül érhető el a HDInsight-ből. Például egy nyilvános fájl tárolója, például a OneDrive vagy az Azure Blob Storage. Mentse az URI-t (HTTP vagy HTTPS-címeket) a fájlba, mivel ez az URI szükséges a következő lépésben.
3. A [fürt kezdőlapján](#homePage)válassza a **parancsfájlok műveletek** elemet a **Beállítások**területen.
4. A **parancsfájl-műveletek** lapon válassza az **új küldése**lehetőséget.
5. A **parancsfájl elküldése művelet** lapon adja meg a következő adatokat:

   | Mező | Value |
   | --- | --- |
   | Szkripttípus | Válassza a **-Custom** elemet a legördülő listából.|
   | Name (Név) |"SSH-jelszó módosítása" |
   | Bash parancsfájl URI azonosítója |A changepassword.sh-fájl URI-ja |
   | Csomópont típusa (i): (Head, Worker, Nimbus, témavezető, Zookeeper stb.) |✓ Minden felsorolt csomópont-típushoz |
   | Paraméterek |Adja meg az SSH-felhasználónevet, majd az új jelszót. A Felhasználónév és a jelszó között egy szóköznek kell lennie. |
   | A parancsfájl megtartása művelet... |Hagyja üresen ezt a mezőt. |

6. Válassza a **Létrehozás** elemet a parancsfájl alkalmazásához. A szkript befejeződése után az SSH használatával csatlakozhat a fürthöz az új jelszóval.

## <a name="grantrevoke-access"></a>Hozzáférés engedélyezése/visszavonása
A HDInsight-fürtök a következő HTTP-webszolgáltatásokkal rendelkeznek (az összes szolgáltatás REST-végpontokkal rendelkezik):

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

Alapértelmezés szerint ezek a szolgáltatások hozzáférést kapnak. [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access)használatával visszavonhatja vagy engedélyezheti a hozzáférést.

## <a name="find-the-subscription-id"></a>Az előfizetés AZONOSÍTÓjának megkeresése
Minden fürt egy Azure-előfizetéshez van kötve.  Az Azure-előfizetés azonosítója a [fürt kezdőlapján](#homePage)látható.

## <a name="find-the-resource-group"></a>Az erőforráscsoport megkeresése
A Azure Resource Manager módban minden HDInsight-fürt egy Azure Resource Manager csoporttal jön létre. A Resource Manager-csoport a [fürt kezdőlapján](#homePage)látható.

## <a name="find-the-storage-accounts"></a>A Storage-fiókok keresése
A HDInsight-fürtök vagy egy Azure Storage-fiókot vagy Azure Data Lake Storage használnak az adattároláshoz. Minden HDInsight-fürt rendelkezhet egyetlen alapértelmezett Storage-fiókkal és egy több társított Storage-fiókkal is. A Storage-fiókok listázásához a [fürt kezdőlapjának](#homePage) **Beállítások**területén válassza a **Storage-fiókok**lehetőséget.

## <a name="monitor-jobs"></a>Feladatok figyelése
Lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md#monitoring).

## <a name="cluster-size"></a>Fürt mérete

A fürt [kezdőlapjának](#homePage) **fürt mérete** csempéje megjeleníti a fürthöz lefoglalt magok számát, valamint azt, hogy ezek hogyan vannak lefoglalva a fürt csomópontjain.

> [!IMPORTANT]  
> A HDInsight-fürt által biztosított szolgáltatások figyeléséhez a Ambari web vagy a Ambari REST APIt kell használnia. További információ a Ambari használatáról: [HDInsight-fürtök kezelése Apache Ambari használatával](hdinsight-hadoop-manage-ambari.md)

## <a name="connect-to-a-cluster"></a>Csatlakozás fürthöz

* [Apache Hive használata a HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [SSH használata a HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanult néhány alapvető felügyeleti funkciót. További információt a következő cikkekben talál:

* [HDInsight felügyelete a Azure PowerShell használatával](hdinsight-administer-use-powershell.md)
* [HDInsight felügyelete az Azure CLI-vel](hdinsight-administer-use-command-line.md)
* [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Az Apache Ambari REST API használatának részletei](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Sqoop használata a HDInsight](hadoop/hdinsight-use-sqoop.md)
* [Python-felhasználó által definiált függvények (UDF) használata Apache Hive és Apache Pig használatával a HDInsight-ben](hadoop/python-udf-hdinsight.md)
* [Apache Hadoop verziója az Azure HDInsight?](hdinsight-component-versioning.md)