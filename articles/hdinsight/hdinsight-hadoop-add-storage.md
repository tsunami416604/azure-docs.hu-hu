---
title: További Azure Storage-fiókok hozzáadása a HDInsighthoz
description: Ismerje meg, hogyan adhat hozzá további Azure Storage-fiókokat egy meglévő HDInsight-fürthöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206707"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>További tárfiókok hozzáadása a HDInsighthoz

Ismerje meg, hogyan használhatja a parancsfájlműveleteket további Azure *Storage-fiókok* hozzáadása a HDInsighthoz. A jelen dokumentum lépései hozzáadnak egy *tárfiókot* egy meglévő HDInsight-fürthöz. Ez a cikk a *tárfiókokra* (nem az alapértelmezett fürtalapú tárfiókra) vonatkozik, és nem további tárhelyre, például [az Azure Data Lake Storage Gen1-re](hdinsight-hadoop-use-data-lake-store.md) és az Azure Data Lake Storage [Gen2-re.](hdinsight-hadoop-use-data-lake-storage-gen2.md)

> [!IMPORTANT]  
> A dokumentumban szereplő információk arról szólnak, hogy további tárfiók(oka)t adnak hozzá a fürthöz a létrehozásután. A fürt létrehozása során a tárolófiókok hozzáadásáról a [Fürtök beállítása a HDInsightban az Apache Hadoop, az Apache Spark, az Apache Kafka és egyebek](hdinsight-hadoop-provision-linux-clusters.md)című témakörben talál.

## <a name="prerequisites"></a>Előfeltételek

* Egy Hadoop-fürt a HDInsighton. Lásd: [Első lépések a HDInsight linuxos alkalmazásával.](./hadoop/apache-hadoop-linux-tutorial-get-started.md)
* Tárfiók neve és kulcsa. Lásd: [Tárfiók hozzáférési kulcsainak kezelése.](../storage/common/storage-account-keys-manage.md)
* PowerShell használata esetén szüksége lesz az AZ modulra.  Tekintse [meg az Azure PowerShell áttekintése című témakört.](https://docs.microsoft.com/powershell/azure/overview)

## <a name="how-it-works"></a>Működés

A feldolgozás során a parancsfájl a következő műveleteket hajtja végre:

* Ha a tárfiók már létezik a fürt core-site.xml konfigurációjában, a parancsfájl kilép, és nem hajt végre további műveleteket.

* Ellenőrzi, hogy a tárfiók létezik-e, és elérhető-e a kulccsal.

* A fürt hitelesítő adataival titkosítja a kulcsot.

* Hozzáadja a tárfiókot a core-site.xml fájlhoz.

* Leállítja és újraindítja az [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)és [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) szolgáltatásokat. Ezek a szolgáltatások leállítása és indítása lehetővé teszi számukra, hogy az új tárfiókot.

> [!WARNING]  
> A HDInsight-fürttől eltérő helyen lévő tárfiók használata nem támogatott.

## <a name="add-storage-account"></a>Tárfiók hozzáadása

A [Parancsfájl-művelet segítségével](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) alkalmazza a módosításokat a következő szempontokat:

|Tulajdonság | Érték |
|---|---|
|Bash parancsfájl URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Csomóponttípus(ok)|Head|
|Paraméterek|`ACCOUNTNAME``ACCOUNTKEY` `-p` kötelező)|

* `ACCOUNTNAME`A HDInsight-fürthöz hozzáadni szükséges tárfiók neve.
* `ACCOUNTKEY`a hozzáférési kulcs `ACCOUNTNAME`a.
* A(z) `-p` nem kötelező. Ha meg van adva, a kulcs nem lesz titkosítva, és a core-site.xml fájlban egyszerű szövegként tárolódik.

## <a name="verification"></a>Ellenőrzés

A HDInsight-fürt az Azure Portalon, a __Storage-fiókok__ bejegyzés __kiválasztása a Tulajdonságok__ csoportban nem jeleníti meg a parancsfájl-műveleten keresztül hozzáadott tárfiókokat. Az Azure PowerShell és az Azure CLI sem jeleníti meg a további tárfiókot. A tárolási adatok nem jelennek meg, mert `core-site.xml` a parancsfájl csak a fürt konfigurációját módosítja. Ez az információ nem használható a fürtadatok Azure felügyeleti API-k használatával történő beolvasásakor.

A további tárhely ellenőrzéséhez használja az alábbi módszerek egyikét:

### <a name="powershell"></a>PowerShell

A parancsfájl az adott fürthöz társított tárfiók nevét adja vissza. Cserélje `CLUSTERNAME` le a tényleges fürtnévre, majd futtassa a parancsfájlt.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Az Ambari

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Nyissa meg a **HDFS** > **Configs** > **Advanced** > Custom**core-site webhelyet.**

1. Figyelje meg a `fs.azure.account.key`. A fióknév a kulcs része lesz, amintaképen látható:

   ![ellenőrzés az Apache Ambari-n keresztül](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Tárfiók eltávolítása

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net`meg `CLUSTERNAME` a , ahol a fürt neve.

1. Nyissa meg a **HDFS** > **Configs** > **Advanced** > Custom**core-site webhelyet.**

1. Távolítsa el a következő billentyűket:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

A kulcsok eltávolítása és a konfiguráció mentése után egyenként újra kell indítania az Oozie, a Yarn, a MapReduce2, a HDFS és a Hive rendszert.

## <a name="known-issues"></a>Ismert problémák

### <a name="storage-firewall"></a>Tároló tűzfala

Ha úgy dönt, hogy a tárfiókot a **tűzfalak és** a virtuális hálózatok korlátozások **a kiválasztott hálózatokon,** győződjön meg róla, hogy a kivétel engedélyezése megbízható **Microsoft-szolgáltatások ,** hogy a HDInsight hozzáférhet a tárfiókhoz.

### <a name="unable-to-access-storage-after-changing-key"></a>Nem lehet hozzáférni a tárolóhoz a kulcs módosítása után

Ha módosítja a kulcs egy tárfiók, HDInsight már nem tud hozzáférni a tárfiókhoz. A HDInsight a fürt core-site.xml fájljában lévő kulcs gyorsítótárazott másolatát használja. Ezt a gyorsítótárazott példányt frissíteni kell, hogy megfeleljen az új kulcsnak.

A parancsfájlművelet ismételt futtatása __nem__ frissíti a kulcsot, mivel a parancsfájl ellenőrzi, hogy létezik-e már bejegyzés a tárfiókhoz. Ha egy bejegyzés már létezik, az nem hajt végre módosításokat.

A probléma kerülő megoldása:  
1. Távolítsa el a tárfiókot.
1. Adja hozzá a tárfiókot.

> [!IMPORTANT]  
> A fürthöz csatlakoztatott elsődleges tárfiók tárolókulcsának elforgatása nem támogatott.

### <a name="poor-performance"></a>Gyenge teljesítmény

Ha a tárfiók a HDInsight-fürthöz eltérő régióban található, gyenge teljesítményt tapasztalhat. Az adatok elérése egy másik régióban hálózati forgalmat küld a regionális Azure-adatközponton kívül és a nyilvános interneten keresztül, amely késést okozhat.

### <a name="additional-charges"></a>További költségek

Ha a tárfiók egy másik régióban, mint a HDInsight-fürt, észreveheti, további kimenő díjak az Azure-számlázási. A kimenő forgalom díja akkor kerül alkalmazásra, amikor az adatok kilépnek egy regionális adatközpontból. Ez a díj akkor is érvényes, ha a forgalom egy másik Azure-adatközpontba kerül egy másik régióban.

## <a name="next-steps"></a>További lépések

Megtanulta, hogyan adhat hozzá további tárfiókokat egy meglévő HDInsight-fürthöz. A parancsfájlműveletekről a [Linux-alapú HDInsight-fürtök testreszabása parancsfájlművelettel című](hdinsight-hadoop-customize-cluster-linux.md) témakörben talál további információt.
